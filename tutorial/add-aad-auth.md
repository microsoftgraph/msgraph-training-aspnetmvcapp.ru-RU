<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы будете расширяем приложение из предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD. Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph. На этом этапе выполняется интеграция промежуточного слоя OWIN и библиотеки [библиотеки проверки подлинности Майкрософт](https://www.nuget.org/packages/Microsoft.Identity.Client/) в приложение.

Щелкните правой кнопкой мыши проект **Graph – Tutorial** в обозревателе решений и выберите команду **Добавить > новый элемент..**.. Выберите **файл веб-конфигурации**, назовите `PrivateSettings.config` файл и нажмите кнопку **добавить**. Замените все его содержимое указанным ниже кодом.

```xml
<appSettings>
    <add key="ida:AppID" value="YOUR APP ID" />
    <add key="ida:AppSecret" value="YOUR APP PASSWORD" />
    <add key="ida:RedirectUri" value="http://localhost:PORT/" />
    <add key="ida:AppScopes" value="User.Read Calendars.Read" />
</appSettings>
```

Замените `YOUR_APP_ID_HERE` идентификатором приложения на портале регистрации приложений и замените `YOUR_APP_PASSWORD_HERE` на созданный секрет клиента. Если ваш секрет клиента содержит амперсанд (`&`), не забудьте заменить их `&amp;` на в. `PrivateSettings.config` Кроме того, необходимо изменить `PORT` значение свойства в `ida:RedirectUri` соответствии с URL-адресом приложения.

> [!IMPORTANT]
> Если вы используете систему управления версиями (например, Git), то в дальнейшем будет полезно исключить `PrivateSettings.config` файл из системы управления версиями, чтобы избежать непреднамеренного утечки идентификатора и пароля приложения.

Обновление `Web.config` для загрузки нового файла. `<appSettings>` Замените строку 7 на приведенную ниже строку.

```xml
<appSettings file="PrivateSettings.config">
```

## <a name="implement-sign-in"></a>Реализация входа

Начните с инициализации промежуточного слоя OWIN для использования проверки подлинности Azure AD для приложения. Щелкните правой кнопкой мыши папку **апп_старт** в обозревателе решений и выберите команду **Добавить класс >..**.. Присвойте файлу `Startup.Auth.cs` имя и нажмите кнопку **Добавить**. Замените все содержимое приведенным ниже кодом.

```cs
using Microsoft.Identity.Client;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;
using Microsoft.IdentityModel.Tokens;
using Microsoft.Owin.Security;
using Microsoft.Owin.Security.Cookies;
using Microsoft.Owin.Security.Notifications;
using Microsoft.Owin.Security.OpenIdConnect;
using Owin;
using System.Configuration;
using System.Threading.Tasks;
using System.Web;

namespace graph_tutorial
{
    public partial class Startup
    {
        // Load configuration settings from PrivateSettings.config
        private static string appId = ConfigurationManager.AppSettings["ida:AppId"];
        private static string appSecret = ConfigurationManager.AppSettings["ida:AppSecret"];
        private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];
        private static string graphScopes = ConfigurationManager.AppSettings["ida:AppScopes"];

        public void ConfigureAuth(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());

            app.UseOpenIdConnectAuthentication(
              new OpenIdConnectAuthenticationOptions
              {
                  ClientId = appId,
                  Authority = "https://login.microsoftonline.com/common/v2.0",
                  Scope = $"openid email profile offline_access {graphScopes}",
                  RedirectUri = redirectUri,
                  PostLogoutRedirectUri = redirectUri,
                  TokenValidationParameters = new TokenValidationParameters
                  {
                      // For demo purposes only, see below
                      ValidateIssuer = false

                      // In a real multi-tenant app, you would add logic to determine whether the
                      // issuer was from an authorized tenant
                      //ValidateIssuer = true,
                      //IssuerValidator = (issuer, token, tvp) =>
                      //{
                      //  if (MyCustomTenantValidation(issuer))
                      //  {
                      //    return issuer;
                      //  }
                      //  else
                      //  {
                      //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
                      //  }
                      //}
                  },
                  Notifications = new OpenIdConnectAuthenticationNotifications
                  {
                      AuthenticationFailed = OnAuthenticationFailedAsync,
                      AuthorizationCodeReceived = OnAuthorizationCodeReceivedAsync
                  }
              }
            );
        }

        private static Task OnAuthenticationFailedAsync(AuthenticationFailedNotification<OpenIdConnectMessage,
          OpenIdConnectAuthenticationOptions> notification)
        {
            notification.HandleResponse();
            string redirect = $"/Home/Error?message={notification.Exception.Message}";
            if (notification.ProtocolMessage != null && !string.IsNullOrEmpty(notification.ProtocolMessage.ErrorDescription))
            {
                redirect += $"&debug={notification.ProtocolMessage.ErrorDescription}";
            }
            notification.Response.Redirect(redirect);
            return Task.FromResult(0);
        }

        private async Task OnAuthorizationCodeReceivedAsync(AuthorizationCodeReceivedNotification notification)
        {
            var idClient = ConfidentialClientApplicationBuilder.Create(appId)
                .WithRedirectUri(redirectUri)
                .WithClientSecret(appSecret)
                .Build();

            string message;
            string debug;

            try
            {
                string[] scopes = graphScopes.Split(' ');

                var result = await idClient.AcquireTokenByAuthorizationCode(
                    scopes, notification.Code).ExecuteAsync();

                message = "Access token retrieved.";
                debug = result.AccessToken;
            }
            catch (MsalException ex)
            {
                message = "AcquireTokenByAuthorizationCodeAsync threw an exception";
                debug = ex.Message;
            }

            notification.HandleResponse();
            notification.Response.Redirect($"/Home/Error?message={message}&debug={debug}");
        }
    }
}
```

Этот код настраивает промежуточный по OWIN со значениями из `PrivateSettings.config` и определяет два метода обратного `OnAuthenticationFailedAsync` вызова `OnAuthorizationCodeReceivedAsync`и. Эти методы обратного вызова будут вызываться при возвращении процесса входа из Azure.

Теперь обновите `Startup.cs` файл, чтобы вызвать `ConfigureAuth` метод. Замените все содержимое `Startup.cs` приведенным ниже кодом.

```cs
using Microsoft.Owin;
using Owin;

[assembly: OwinStartup(typeof(graph_tutorial.Startup))]

namespace graph_tutorial
{
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
}
```

Добавьте в `Error` `HomeController` класс действие, чтобы преобразовать `message` параметры `debug` запроса в `Alert` объект. Откройте `Controllers/HomeController.cs` и добавьте указанную ниже функцию.

```cs
public ActionResult Error(string message, string debug)
{
    Flash(message, debug);
    return RedirectToAction("Index");
}
```

Добавление контроллера для обработки входа. Щелкните правой кнопкой мыши **** папку Controllers в обозревателе решений и выберите **Добавить контроллер >..**.. Выберите **контроллер MVC 5 — пустой** и нажмите кнопку **Добавить**. Назовите контроллер `AccountController` и нажмите кнопку **Добавить**. Замените все содержимое файла приведенным ниже кодом.

```cs
using Microsoft.Owin.Security;
using Microsoft.Owin.Security.Cookies;
using Microsoft.Owin.Security.OpenIdConnect;
using System.Security.Claims;
using System.Web;
using System.Web.Mvc;

namespace graph_tutorial.Controllers
{
    public class AccountController : Controller
    {
        public void SignIn()
        {
            if (!Request.IsAuthenticated)
            {
                // Signal OWIN to send an authorization request to Azure
                Request.GetOwinContext().Authentication.Challenge(
                    new AuthenticationProperties { RedirectUri = "/" },
                    OpenIdConnectAuthenticationDefaults.AuthenticationType);
            }
        }

        public ActionResult SignOut()
        {
            if (Request.IsAuthenticated)
            {
                Request.GetOwinContext().Authentication.SignOut(
                    CookieAuthenticationDefaults.AuthenticationType);
            }

            return RedirectToAction("Index", "Home");
        }
    }
}
```

Это определяет действие `SignIn` и `SignOut` действие. `SignIn` Действие проверяет, был ли запрос уже прошел проверку подлинности. В противном случае он вызывает промежуточный по OWIN для проверки подлинности пользователя. `SignOut` Действие вызывает промежуточный по OWIN для выхода.

Сохраните изменения и запустите проект. Нажмите кнопку входа, и вы будете перенаправлены на `https://login.microsoftonline.com`. Войдите с помощью учетной записи Майкрософт и согласия с запрошенными разрешениями. Браузер перенаправляется на приложение, отображая маркер.

### <a name="get-user-details"></a>Получение сведений о пользователе

Начните с создания нового файла, в котором будет храниться весь набор вызовов Microsoft Graph. Щелкните правой кнопкой мыши папку **Graph – Tutorial** в обозревателе решений и выберите команду **Добавить > создать папку**. Назовите папку `Helpers`. Щелкните новую папку правой кнопкой мыши и выберите команду **добавить > класс..**.. Присвойте файлу `GraphHelper.cs` имя и нажмите кнопку **Добавить**. Замените содержимое этого файла приведенным ниже кодом.

```cs
using Microsoft.Graph;
using System.Net.Http.Headers;
using System.Threading.Tasks;

namespace graph_tutorial.Helpers
{
    public static class GraphHelper
    {
        public static async Task<User> GetUserDetailsAsync(string accessToken)
        {
            var graphClient = new GraphServiceClient(
                new DelegateAuthenticationProvider(
                    async (requestMessage) =>
                    {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", accessToken);
                    }));

            return await graphClient.Me.Request().GetAsync();
        }
    }
}
```

При этом реализуется `GetUserDetails` функция, которая использует пакет SDK Microsoft Graph для вызова `/me` конечной точки и возврата результата.

Обновите `OnAuthorizationCodeReceivedAsync` метод в `App_Start/Startup.Auth.cs` методе, чтобы вызвать эту функцию. Сначала добавьте следующий `using` оператор в начало файла.

```cs
using graph_tutorial.Helpers;
```

Замените существующий `try` блок на `OnAuthorizationCodeReceivedAsync` приведенный ниже код.

```cs
try
{
    string[] scopes = graphScopes.Split(' ');

    var result = await idClient.AcquireTokenByAuthorizationCode(
        scopes, notification.Code).ExecuteAsync();

    var userDetails = await GraphHelper.GetUserDetailsAsync(result.AccessToken);

    string email = string.IsNullOrEmpty(userDetails.Mail) ?
        userDetails.UserPrincipalName : userDetails.Mail;

    message = "User info retrieved.";
    debug = $"User: {userDetails.DisplayName}, Email: {email}";
}
```

Теперь, если вы сохраните изменения и запустите приложение, после входа вы увидите имя пользователя и адрес электронной почты, а не маркер доступа.

## <a name="storing-the-tokens"></a>Сохранение маркеров

Теперь, когда вы можете получить маркеры, следует реализовать способ их хранения в приложении. Так как это пример приложения, мы будем использовать сеанс для хранения маркеров. Реальное приложение использует более надежное решение для безопасного хранения, например базу данных.

Щелкните правой кнопкой мыши папку **Graph – Tutorial** в обозревателе решений и выберите команду **Добавить > создать папку**. Назовите папку `TokenStorage`. Щелкните новую папку правой кнопкой мыши и выберите команду **добавить > класс..**.. Присвойте файлу `SessionTokenStore.cs` имя и нажмите кнопку **Добавить**. Замените содержимое этого файла приведенным ниже кодом.

```cs
using Microsoft.Identity.Client;
using Newtonsoft.Json;
using System.Security.Claims;
using System.Threading;
using System.Web;

namespace graph_tutorial.TokenStorage
{
    // Simple class to serialize into the session
    public class CachedUser
    {
        public string DisplayName { get; set; }
        public string Email { get; set; }
        public string Avatar { get; set; }
    }

    // Adapted from https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-v2
    public class SessionTokenStore
    {
        private static readonly ReaderWriterLockSlim sessionLock = new ReaderWriterLockSlim(LockRecursionPolicy.NoRecursion);

        private readonly string userId = string.Empty;
        private readonly string cacheId = string.Empty;
        private readonly string cachedUserId = string.Empty;
        private HttpContext httpContext = null;
        private ITokenCache tokenCache;

        public SessionTokenStore(string userId, HttpContext httpcontext)
        {
            this.userId = userId;
            this.cacheId = $"{userId}_TokenCache";
            this.cachedUserId = $"{userId}_UserCache";
            this.httpContext = httpcontext;
        }

        public void Initialize(ITokenCache tokenCache)
        {
            this.tokenCache = tokenCache;
            this.tokenCache.SetBeforeAccess(BeforeAccessNotification);
            this.tokenCache.SetAfterAccess(AfterAccessNotification);
            Load();
        }

        public bool HasData()
        {
            return (httpContext.Session[cacheId] != null && ((byte[])httpContext.Session[cacheId]).Length > 0);
        }

        public void Clear()
        {
            httpContext.Session.Remove(cacheId);
        }

        private void Load()
        {
            sessionLock.EnterReadLock();
            tokenCache.DeserializeMsalV3((byte[])httpContext.Session[cacheId]);
            sessionLock.ExitReadLock();
        }

        private void Persist()
        {
            sessionLock.EnterReadLock();
            httpContext.Session[cacheId] = tokenCache.SerializeMsalV3();
            sessionLock.ExitReadLock();
        }

        private void BeforeAccessNotification(TokenCacheNotificationArgs args)
        {
            Load();
        }

        private void AfterAccessNotification(TokenCacheNotificationArgs args)
        {
            if (args.HasStateChanged)
            {
                Persist();
            }
        }

        public void SaveUserDetails(CachedUser user)
        {
            sessionLock.EnterReadLock();
            httpContext.Session[cachedUserId] = JsonConvert.SerializeObject(user);
            sessionLock.ExitReadLock();
        }

        public CachedUser GetUserDetails()
        {
            sessionLock.EnterReadLock();
            var cachedUser = JsonConvert.DeserializeObject<CachedUser>((string)httpContext.Session[cachedUserId]);
            sessionLock.ExitReadLock();
            return cachedUser;
        }
    }
}
```

Этот код создает `SessionTokenStore` класс, работающий с `TokenCache` классом библиотеки MSAL. В большинстве кода здесь `TokenCache` описывается сериализация и десериализация для сеанса. Кроме того, он предоставляет класс и методы для сериализации и десериализации сведений о пользователе в сеансе.

Теперь добавьте приведенный ниже `using` оператор в начало `App_Start/Startup.Auth.cs` файла.

```cs
using graph_tutorial.TokenStorage;
using System.IdentityModel.Claims;
```

Теперь обновите `OnAuthorizationCodeReceivedAsync` функцию, чтобы создать экземпляр `SessionTokenStore` класса и предоставить конструктору для `ConfidentialClientApplication` объекта. Это приведет к тому, что MSAL использует реализацию кэша для хранения маркеров. Замените имеющуюся функцию `OnAuthorizationCodeReceivedAsync` указанным ниже кодом.

```cs
private async Task OnAuthorizationCodeReceivedAsync(AuthorizationCodeReceivedNotification notification)
{
    var idClient = ConfidentialClientApplicationBuilder.Create(appId)
        .WithRedirectUri(redirectUri)
        .WithClientSecret(appSecret)
        .Build();

    var signedInUserId = notification.AuthenticationTicket.Identity.FindFirst(ClaimTypes.NameIdentifier).Value;
    var tokenStore = new SessionTokenStore(signedInUserId, HttpContext.Current);
    tokenStore.Initialize(idClient.UserTokenCache);

    try
    {
        string[] scopes = graphScopes.Split(' ');

        var result = await idClient.AcquireTokenByAuthorizationCode(
            scopes, notification.Code).ExecuteAsync();

        var userDetails = await GraphHelper.GetUserDetailsAsync(result.AccessToken);

        var cachedUser = new CachedUser()
        {
            DisplayName = userDetails.DisplayName,
            Email = string.IsNullOrEmpty(userDetails.Mail) ?
            userDetails.UserPrincipalName : userDetails.Mail,
            Avatar = string.Empty
        };

        tokenStore.SaveUserDetails(cachedUser);
    }
    catch (MsalException ex)
    {
        string message = "AcquireTokenByAuthorizationCodeAsync threw an exception";
        notification.HandleResponse();
        notification.Response.Redirect($"/Home/Error?message={message}&debug={ex.Message}");
    }
    catch (Microsoft.Graph.ServiceException ex)
    {
        string message = "GetUserDetailsAsync threw an exception";
        notification.HandleResponse();
        notification.Response.Redirect($"/Home/Error?message={message}&debug={ex.Message}");
    }
}
```

Чтобы обвести итог изменений:

- Теперь код заменяет `ConfidentialClientApplication`кэш маркеров пользователя по умолчанию экземпляром `SessionTokenStore`. Библиотека MSAL будет обрабатывать логику хранения маркеров и обновлять их при необходимости.
- Теперь код передает сведения о пользователях, полученные из Microsoft Graph, `SessionTokenStore` в объект, который будет храниться в сеансе.
- При успешном выполнении код не переправляется, он просто возвращает. Это позволяет промежуточному по OWIN выполнить процесс проверки подлинности.

Так как кэш маркера хранится в сеансе, обновите `SignOut` действие в `Controllers/AccountController.cs` , чтобы очистить хранилище маркеров перед выходом. Сначала добавьте следующий `using` оператор в начало файла.

```cs
using graph_tutorial.TokenStorage;
```

Затем замените существующую `SignOut` функцию на приведенную ниже.

```cs
public ActionResult SignOut()
{
    if (Request.IsAuthenticated)
    {
        string signedInUserId = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier).Value;
        SessionTokenStore tokenStore = new SessionTokenStore(signedInUserId, System.Web.HttpContext.Current);

        tokenStore.Clear();

        Request.GetOwinContext().Authentication.SignOut(
            CookieAuthenticationDefaults.AuthenticationType);
    }

    return RedirectToAction("Index", "Home");
}
```

Сведения о кэшированном пользователе — это то, что потребуется для каждого представления в приложении, поэтому `BaseController` обновите класс, чтобы загрузить эти сведения из сеанса. Откройте `Controllers/BaseController.cs` и добавьте приведенные `using` ниже операторы в начало файла.

```cs
using graph_tutorial.TokenStorage;
using System.Security.Claims;
using System.Web;
using Microsoft.Owin.Security.Cookies;
```

Затем добавьте указанную ниже функцию.

```cs
protected override void OnActionExecuting(ActionExecutingContext filterContext)
{
    if (Request.IsAuthenticated)
    {
        // Get the signed in user's id and create a token cache
        string signedInUserId = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier).Value;
        SessionTokenStore tokenStore = new SessionTokenStore(signedInUserId, System.Web.HttpContext.Current);

        if (tokenStore.HasData())
        {
            // Add the user to the view bag
            ViewBag.User = tokenStore.GetUserDetails();
        }
        else
        {
            // The session has lost data. This happens often
            // when debugging. Log out so the user can log back in
            Request.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
            filterContext.Result = RedirectToAction("Index", "Home");
        }
    }

    base.OnActionExecuting(filterContext);
}
```

Запустите сервер и пройдите процесс входа. Необходимо вернуться на домашнюю страницу, но пользовательский интерфейс должен измениться, чтобы показать, что вы вошли в систему.

![Снимок экрана домашней страницы после входа](./images/add-aad-auth-01.png)

Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к ссылке **выхода** . При нажатии кнопки **выйти** сбрасывается сеанс и возвращается на домашнюю страницу.

![Снимок экрана с раскрывающимся меню со ссылкой "выйти"](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Обновление маркеров

На этом шаге приложение имеет маркер доступа, который отправляется в `Authorization` заголовке вызовов API. Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.

Однако этот маркер кратковременно используется. Срок действия маркера истечет через час после его выдачи. В этом случае маркер обновления становится полезен. Маркер обновления позволяет приложению запросить новый маркер доступа, не требуя от пользователя повторного входа.

Так как приложение использует библиотеку и `TokenCache` объект MSAL, нет необходимости реализовывать какую-либо логику обновления маркеров. `ConfidentialClientApplication.AcquireTokenSilentAsync` Метод выполняет всю логику. Сначала он проверяет кэшированный маркер и, если срок его действия не истек, он возвращает его. Если срок действия истек, он использует кэшированный маркер обновления, чтобы получить новый. Этот метод будет использоваться в следующем модуле.