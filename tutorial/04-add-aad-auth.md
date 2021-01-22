<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы расширим приложение из предыдущего упражнения, чтобы поддерживать проверку подлинности с помощью Azure AD. Это необходимо для получения необходимого маркера доступа OAuth для вызова API Microsoft Graph. На этом этапе вы интегрируете ПО по среднему окне OWIN и библиотеку библиотеки проверки подлинности [Майкрософт](https://www.nuget.org/packages/Microsoft.Identity.Client/) в приложение.

1. Щелкните правой кнопкой мыши проект **учебника** по графику в обозревателе решений **и выберите "Добавить > Новый элемент..."** Choose **Web Configuration File,** name the file `PrivateSettings.config` and select **Add**. Замените все его содержимое указанным ниже кодом.

    ```xml
    <appSettings>
        <add key="ida:AppID" value="YOUR APP ID" />
        <add key="ida:AppSecret" value="YOUR APP PASSWORD" />
        <add key="ida:RedirectUri" value="https://localhost:PORT/" />
        <add key="ida:AppScopes" value="User.Read Calendars.Read" />
    </appSettings>
    ```

    Замените ИД приложения на портале регистрации приложений и замените созданный секрет `YOUR_APP_ID_HERE` `YOUR_APP_PASSWORD_HERE` клиента. Если секрет клиента содержит какие-либо амперанды ( ), обязательно замените их в `&` `&amp;` `PrivateSettings.config` . Кроме того, обязательно измените `PORT` значение для `ida:RedirectUri` URL-адреса приложения.

    > [!IMPORTANT]
    > Если вы используете управление исходным кодом, например git, пришло бы время исключить файл из системы управления источником, чтобы избежать случайной утечки ИД приложения и `PrivateSettings.config` пароля.

1. `Web.config`Обновим, чтобы загрузить этот новый файл. Замените `<appSettings>` строку (строка 7) на следующий:

    ```xml
    <appSettings file="PrivateSettings.config">
    ```

## <a name="implement-sign-in"></a>Реализация входов

Начните с инициализации ПО среднего по OWIN для использования проверки подлинности Azure AD для приложения.

1. Щелкните правой **кнопкой мыши папку App_Start** в обозревателе решений и выберите **add > Class...**. Назовем файл `Startup.Auth.cs` и выберите **"Добавить".** Замените все содержимое следующим кодом.

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

    > [!NOTE]
    > Этот код настраивает ПО по середине OWIN со значениями из и определяет два метода `PrivateSettings.config` вызова, `OnAuthenticationFailedAsync` а также `OnAuthorizationCodeReceivedAsync` . Эти методы обратного вызова будут вызываться, когда процесс вход в службу возвращается из Azure.

1. Теперь обновим `Startup.cs` файл, чтобы вызвать `ConfigureAuth` метод. Замените все содержимое на `Startup.cs` следующий код.

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

1. Добавьте в `Error` класс действие для преобразования `HomeController` параметров `message` `debug` запроса и их преобразования в `Alert` объект. Откройте `Controllers/HomeController.cs` и добавьте следующую функцию.

    ```cs
    public ActionResult Error(string message, string debug)
    {
        Flash(message, debug);
        return RedirectToAction("Index");
    }
    ```

1. Добавление контроллера для обработки входов. Щелкните правой **кнопкой** мыши папку "Контроллеры" в обозревателе решений и выберите **"Добавить контроллер >"...** Выберите **контроллер MVC 5 — пустой и** выберите **"Добавить".** Назовем контроллер `AccountController` и выберите **"Добавить".** Замените все содержимое файла на следующий код.

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

    Это определяет действие `SignIn` `SignOut` и действие. Это `SignIn` действие проверяет, был ли запрос уже аутентификацией. В этом случае для проверки подлинности пользователя вызывается по середине OWIN. Это `SignOut` действие вызывает ПО по середине OWIN, чтобы выйти из нее.

1. Сохраните изменения и запустите проект. Нажмите кнопку "Вход" и перенаправите вас на `https://login.microsoftonline.com` . Войдите в систему с помощью учетной записи Майкрософт и согласиться на запрашиваемую разрешения. Браузер перенаправляет пользователя в приложение с отображением маркера.

### <a name="get-user-details"></a>Получить сведения о пользователе

После входа пользователя вы можете получить его сведения из Microsoft Graph.

1. Щелкните правой кнопкой **мыши папку учебника** по графику в обозревателе решений и **выберите "Добавить > Новую папку".** Назовем `Helpers` папку.

1. Щелкните правой кнопкой мыши эту новую папку и **выберите "Добавить > Класс..."**. Назовем файл `GraphHelper.cs` и выберите **"Добавить".** Замените содержимое этого файла на следующий код.

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
                        (requestMessage) =>
                        {
                            requestMessage.Headers.Authorization =
                                new AuthenticationHeaderValue("Bearer", accessToken);
                            return Task.FromResult(0);
                        }));

                return await graphClient.Me.Request().GetAsync();
            }
        }
    }
    ```

    При этом реализуется функция, которая использует SDK Microsoft Graph для вызова конечной точки `GetUserDetails` `/me` и возврата результата.

1. `OnAuthorizationCodeReceivedAsync`Обновив метод `App_Start/Startup.Auth.cs` для вызова этой функции. Добавьте следующий `using` выписку в верхнюю часть файла.

    ```cs
    using graph_tutorial.Helpers;
    ```

1. Замените `try` существующий блок на `OnAuthorizationCodeReceivedAsync` следующий код.

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

1. Сохраните изменения и запустите приложение. После регистрации вместо маркера доступа вы увидите имя пользователя и адрес электронной почты.

## <a name="storing-the-tokens"></a>Хранение маркеров

Теперь, когда вы можете получить маркеры, пора реализовать способ их хранения в приложении. Так как это пример приложения, вы будете использовать сеанс для хранения маркеров. В реальных приложениях используется более надежное решение для безопасного хранения данных, например база данных. В этом разделе вы сделаете следующее:

- Реализуют класс магазина маркеров для сериализации и хранения кэша маркеров MSAL и сведений пользователя в сеансе пользователя.
- Обновим код проверки подлинности, чтобы использовать класс магазина маркеров.
- Обновим базовый класс контроллера, чтобы показать сохраненные сведения о пользователе всем представлениям в приложении.

1. Щелкните правой кнопкой **мыши папку учебника** по графику в обозревателе решений и **выберите "Добавить > Новую папку".** Назовем `TokenStorage` папку.

1. Щелкните правой кнопкой мыши эту новую папку и **выберите "Добавить > Класс..."**. Назовем файл `SessionTokenStore.cs` и выберите **"Добавить".** Замените содержимое этого файла на следующий код.

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT license.

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

        public class SessionTokenStore
        {
            private static readonly ReaderWriterLockSlim sessionLock = new ReaderWriterLockSlim(LockRecursionPolicy.NoRecursion);

            private HttpContext httpContext = null;
            private string tokenCacheKey = string.Empty;
            private string userCacheKey = string.Empty;

            public SessionTokenStore(ITokenCache tokenCache, HttpContext context, ClaimsPrincipal user)
            {
                httpContext = context;

                if (tokenCache != null)
                {
                    tokenCache.SetBeforeAccess(BeforeAccessNotification);
                    tokenCache.SetAfterAccess(AfterAccessNotification);
                }

                var userId = GetUsersUniqueId(user);
                tokenCacheKey = $"{userId}_TokenCache";
                userCacheKey = $"{userId}_UserCache";
            }

            public bool HasData()
            {
                return (httpContext.Session[tokenCacheKey] != null &&
                    ((byte[])httpContext.Session[tokenCacheKey]).Length > 0);
            }

            public void Clear()
            {
                sessionLock.EnterWriteLock();

                try
                {
                    httpContext.Session.Remove(tokenCacheKey);
                }
                finally
                {
                    sessionLock.ExitWriteLock();
                }
            }

            private void BeforeAccessNotification(TokenCacheNotificationArgs args)
            {
                sessionLock.EnterReadLock();

                try
                {
                    // Load the cache from the session
                    args.TokenCache.DeserializeMsalV3((byte[])httpContext.Session[tokenCacheKey]);
                }
                finally
                {
                    sessionLock.ExitReadLock();
                }
            }

            private void AfterAccessNotification(TokenCacheNotificationArgs args)
            {
                if (args.HasStateChanged)
                {
                    sessionLock.EnterWriteLock();

                    try
                    {
                        // Store the serialized cache in the session
                        httpContext.Session[tokenCacheKey] = args.TokenCache.SerializeMsalV3();
                    }
                    finally
                    {
                        sessionLock.ExitWriteLock();
                    }
                }
            }

            public void SaveUserDetails(CachedUser user)
            {

                sessionLock.EnterWriteLock();
                httpContext.Session[userCacheKey] = JsonConvert.SerializeObject(user);
                sessionLock.ExitWriteLock();
            }

            public CachedUser GetUserDetails()
            {
                sessionLock.EnterReadLock();
                var cachedUser = JsonConvert.DeserializeObject<CachedUser>((string)httpContext.Session[userCacheKey]);
                sessionLock.ExitReadLock();
                return cachedUser;
            }

            private string GetUsersUniqueId(ClaimsPrincipal user)
            {
                // Combine the user's object ID with their tenant ID

                if (user != null)
                {
                    var userObjectId = user.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value ??
                        user.FindFirst("oid").Value;

                    var userTenantId = user.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value ??
                        user.FindFirst("tid").Value;

                    if (!string.IsNullOrEmpty(userObjectId) && !string.IsNullOrEmpty(userTenantId))
                    {
                        return $"{userObjectId}.{userTenantId}";
                    }
                }

                return null;
            }
        }
    }
    ```

1. Добавьте следующие `using` утверждения в верхнюю часть `App_Start/Startup.Auth.cs` файла.

    ```cs
    using graph_tutorial.TokenStorage;
    using System.Security.Claims;
    ```

1. Замените имеющуюся функцию `OnAuthorizationCodeReceivedAsync` указанным ниже кодом.

    ```cs
    private async Task OnAuthorizationCodeReceivedAsync(AuthorizationCodeReceivedNotification notification)
    {
        var idClient = ConfidentialClientApplicationBuilder.Create(appId)
            .WithRedirectUri(redirectUri)
            .WithClientSecret(appSecret)
            .Build();

        var signedInUser = new ClaimsPrincipal(notification.AuthenticationTicket.Identity);
        var tokenStore = new SessionTokenStore(idClient.UserTokenCache, HttpContext.Current, signedInUser);

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

    > [!NOTE]
    > Изменения в этой новой версии `OnAuthorizationCodeReceivedAsync` делают следующее:
    >
    > - Теперь код обтекает класс кэш маркеров пользователя по `ConfidentialClientApplication` `SessionTokenStore` умолчанию. Библиотека MSAL будет обрабатывать логику хранения маркеров и их обновления при необходимости.
    > - Теперь код передает сведения о пользователе, полученные из Microsoft Graph, в объект для `SessionTokenStore` хранения в сеансе.
    > - В успешном отправляемом коде перенаправление больше не происходит, он просто возвращается. Это позволяет ПО-посреднику OWIN завершить процесс проверки подлинности.

1. `SignOut`Обновите действие, чтобы очистить хранилище маркеров перед выходом из системы. Добавьте следующую `using` выписку в верхнюю часть `Controllers/AccountController.cs` .

    ```cs
    using graph_tutorial.TokenStorage;
    ```

1. Замените имеющуюся функцию `SignOut` указанным ниже кодом.

    ```cs
    public ActionResult SignOut()
    {
        if (Request.IsAuthenticated)
        {
            var tokenStore = new SessionTokenStore(null,
                System.Web.HttpContext.Current, ClaimsPrincipal.Current);

            tokenStore.Clear();

            Request.GetOwinContext().Authentication.SignOut(
                CookieAuthenticationDefaults.AuthenticationType);
        }

        return RedirectToAction("Index", "Home");
    }
    ```

1. Откройте `Controllers/BaseController.cs` и добавьте следующие утверждения в `using` верхнюю часть файла.

    ```cs
    using graph_tutorial.TokenStorage;
    using System.Security.Claims;
    using System.Web;
    using Microsoft.Owin.Security.Cookies;
    ```

1. Добавьте следующую функцию:

    ```cs
    protected override void OnActionExecuting(ActionExecutingContext filterContext)
    {
        if (Request.IsAuthenticated)
        {
            // Get the user's token cache
            var tokenStore = new SessionTokenStore(null,
                System.Web.HttpContext.Current, ClaimsPrincipal.Current);

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

1. Запустите сервер и войдите в нее. Вы должны вернуться на home-страницу, но пользовательский интерфейс должен измениться, чтобы указать, что вы вписались.

    ![Снимок экрана с домашней страницей после входов](./images/add-aad-auth-01.png)

1. Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к ссылке **"Выйти".** При **нажатии** кнопки "Выйти" сеанс сбрасывается и возвращается на домашней странице.

    ![Снимок экрана с выпадающим меню со ссылкой "Выйти"](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Обновление маркеров

На этом этапе приложение имеет маркер доступа, который отправляется в `Authorization` заголовке вызовов API. Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.

Однако этот маркер является кратковременной. Срок действия маркера истекает через час после его выпуска. В этом случае маркер обновления становится полезным. Маркер обновления позволяет приложению запрашивать новый маркер доступа, не требуя от пользователя повторного входить.

Так как приложение использует библиотеку MSAL и сериализует объект, вам не нужно реализовывать логику обновления `TokenCache` маркеров. Этот `ConfidentialClientApplication.AcquireTokenSilentAsync` метод делает всю логику за вас. Сначала он проверяет кэшный маркер и, если срок его действия еще не истек, возвращает его. Если срок действия истек, для получения нового маркера используется кэшный маркер обновления. Этот метод будет применяться в следующем модуле.
