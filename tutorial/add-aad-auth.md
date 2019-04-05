<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="b6eda-101">В этом упражнении вы будете расширяем приложение из предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD.</span><span class="sxs-lookup"><span data-stu-id="b6eda-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="b6eda-102">Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="b6eda-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="b6eda-103">На этом этапе выполняется интеграция промежуточного слоя OWIN и библиотеки [библиотеки проверки ПодлиннОсти Майкрософт](https://www.nuget.org/packages/Microsoft.Identity.Client/) в приложение.</span><span class="sxs-lookup"><span data-stu-id="b6eda-103">In this step you will integrate the OWIN middleware and the [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client/) library into the application.</span></span>

<span data-ttu-id="b6eda-104">Щелкните правой кнопкой мыши проект **Graph – Tutorial** в обозревателе решений и выберите команду **Добавить _гт_ новый элемент..**.. Выберите **файл веб-конфигурации**, назовите `PrivateSettings.config` файл и нажмите кнопку **добавить**.</span><span class="sxs-lookup"><span data-stu-id="b6eda-104">Right-click the **graph-tutorial** project in Solution Explorer and choose **Add > New Item...**. Choose **Web Configuration File**, name the file `PrivateSettings.config` and choose **Add**.</span></span> <span data-ttu-id="b6eda-105">Замените все его содержимое указанным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="b6eda-105">Replace its entire contents with the following code.</span></span>

```xml
<appSettings>
    <add key="ida:AppID" value="YOUR APP ID" />
    <add key="ida:AppSecret" value="YOUR APP PASSWORD" />
    <add key="ida:RedirectUri" value="http://localhost:PORT/" />
    <add key="ida:AppScopes" value="User.Read Calendars.Read" />
</appSettings>
```

<span data-ttu-id="b6eda-106">Замените `YOUR_APP_ID_HERE` идентификатором приложения на портале регистрации приложений и замените `YOUR_APP_PASSWORD_HERE` на созданный секрет клиента.</span><span class="sxs-lookup"><span data-stu-id="b6eda-106">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_PASSWORD_HERE` with the client secret you generated.</span></span> <span data-ttu-id="b6eda-107">Если ваш секрет клиента содержит амперсанд (`&`), не забудьте заменить их `&amp;` на в. `PrivateSettings.config`</span><span class="sxs-lookup"><span data-stu-id="b6eda-107">If your client secret contains any ampersands (`&`), be sure to replace them with `&amp;` in `PrivateSettings.config`.</span></span> <span data-ttu-id="b6eda-108">Кроме того, необходимо изменить `PORT` значение свойства в `ida:RedirectUri` соответствии с URL-адресом приложения.</span><span class="sxs-lookup"><span data-stu-id="b6eda-108">Also be sure to modify the `PORT` value for the `ida:RedirectUri` to match your application's URL.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b6eda-109">Если вы используете систему управления версиями (например, Git), то в дальнейшем будет полезно исключить `PrivateSettings.config` файл из системы управления версиями, чтобы избежать непреднамеренного утечки идентификатора и пароля приложения.</span><span class="sxs-lookup"><span data-stu-id="b6eda-109">If you're using source control such as git, now would be a good time to exclude the `PrivateSettings.config` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

<span data-ttu-id="b6eda-110">Обновление `Web.config` для загрузки нового файла.</span><span class="sxs-lookup"><span data-stu-id="b6eda-110">Update `Web.config` to load this new file.</span></span> <span data-ttu-id="b6eda-111">`<appSettings>` Замените строку 7 на приведенную ниже строку.</span><span class="sxs-lookup"><span data-stu-id="b6eda-111">Replace the `<appSettings>` (line 7) with the following</span></span>

```xml
<appSettings file="PrivateSettings.config">
```

## <a name="implement-sign-in"></a><span data-ttu-id="b6eda-112">Реализация входа</span><span class="sxs-lookup"><span data-stu-id="b6eda-112">Implement sign-in</span></span>

<span data-ttu-id="b6eda-113">Начните с инициализации промежуточного слоя OWIN для использования проверки подлинности Azure AD для приложения.</span><span class="sxs-lookup"><span data-stu-id="b6eda-113">Start by initializing the OWIN middleware to use Azure AD authentication for the app.</span></span> <span data-ttu-id="b6eda-114">Щелкните правой кнопкой мыши папку **апп_старт** в обозревателе решений и выберите команду **Добавить класс _гт_..**.. ПриСвойте файлу `Startup.Auth.cs` имя и нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="b6eda-114">Right-click the **App_Start** folder in Solution Explorer and choose **Add > Class...**. Name the file `Startup.Auth.cs` and choose **Add**.</span></span> <span data-ttu-id="b6eda-115">Замените все содержимое приведенным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="b6eda-115">Replace the entire contents with the following code.</span></span>

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
            var idClient = new ConfidentialClientApplication(
                appId, redirectUri, new ClientCredential(appSecret), null, null);

            string message;
            string debug;

            try
            {
                string[] scopes = graphScopes.Split(' ');

                var result = await idClient.AcquireTokenByAuthorizationCodeAsync(
                    notification.Code, scopes);

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

<span data-ttu-id="b6eda-116">Этот код настраивает промежуточный по OWIN со значениями из `PrivateSettings.config` и определяет два метода обратного `OnAuthenticationFailedAsync` вызова `OnAuthorizationCodeReceivedAsync`и.</span><span class="sxs-lookup"><span data-stu-id="b6eda-116">This code configures the OWIN middleware with the values from `PrivateSettings.config` and defines two callback methods, `OnAuthenticationFailedAsync` and `OnAuthorizationCodeReceivedAsync`.</span></span> <span data-ttu-id="b6eda-117">Эти методы обратного вызова будут вызываться при возвращении процесса входа из Azure.</span><span class="sxs-lookup"><span data-stu-id="b6eda-117">These callback methods will be invoked when the sign-in process returns from Azure.</span></span>

<span data-ttu-id="b6eda-118">Теперь обновите `Startup.cs` файл, чтобы вызвать `ConfigureAuth` метод.</span><span class="sxs-lookup"><span data-stu-id="b6eda-118">Now update the `Startup.cs` file to call the `ConfigureAuth` method.</span></span> <span data-ttu-id="b6eda-119">Замените все содержимое `Startup.cs` приведенным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="b6eda-119">Replace the entire contents of `Startup.cs` with the following code.</span></span>

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

<span data-ttu-id="b6eda-120">Добавьте в `Error` `HomeController` класс действие, чтобы преобразовать `message` параметры `debug` запроса в `Alert` объект.</span><span class="sxs-lookup"><span data-stu-id="b6eda-120">Add an `Error` action to the `HomeController` class to transform the `message` and `debug` query parameters into an `Alert` object.</span></span> <span data-ttu-id="b6eda-121">Откройте `Controllers/HomeController.cs` и добавьте указанную ниже функцию.</span><span class="sxs-lookup"><span data-stu-id="b6eda-121">Open `Controllers/HomeController.cs` and add the following function.</span></span>

```cs
public ActionResult Error(string message, string debug)
{
    Flash(message, debug);
    return RedirectToAction("Index");
}
```

<span data-ttu-id="b6eda-122">Добавление контроллера для обработки входа.</span><span class="sxs-lookup"><span data-stu-id="b6eda-122">Add a controller to handle sign-in.</span></span> <span data-ttu-id="b6eda-123">Щелкните правой кнопкой мыши \*\*\*\* папку Controllers в обозревателе решений и выберите команду **Добавить контроллер _гт_..**.. Выберите **контроллер MVC 5 — пустой** и нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="b6eda-123">Right-click the **Controllers** folder in Solution Explorer and choose **Add > Controller...**. Choose **MVC 5 Controller - Empty** and choose **Add**.</span></span> <span data-ttu-id="b6eda-124">НаЗовите контроллер `AccountController` и нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="b6eda-124">Name the controller `AccountController` and choose **Add**.</span></span> <span data-ttu-id="b6eda-125">Замените все содержимое файла приведенным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="b6eda-125">Replace the entire contents of the file with the following code.</span></span>

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

<span data-ttu-id="b6eda-126">Это определяет действие `SignIn` и `SignOut` действие.</span><span class="sxs-lookup"><span data-stu-id="b6eda-126">This defines a `SignIn` and `SignOut` action.</span></span> <span data-ttu-id="b6eda-127">`SignIn` Действие проверяет, был ли запрос уже прошел проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="b6eda-127">The `SignIn` action checks if the request is already authenticated.</span></span> <span data-ttu-id="b6eda-128">В противном случае он вызывает промежуточный по OWIN для проверки подлинности пользователя.</span><span class="sxs-lookup"><span data-stu-id="b6eda-128">If not, it invokes the OWIN middleware to authenticate the user.</span></span> <span data-ttu-id="b6eda-129">`SignOut` Действие вызывает промежуточный по OWIN для выхода.</span><span class="sxs-lookup"><span data-stu-id="b6eda-129">The `SignOut` action invokes the OWIN middleware to sign out.</span></span>

<span data-ttu-id="b6eda-130">Сохраните изменения и запустите проект.</span><span class="sxs-lookup"><span data-stu-id="b6eda-130">Save your changes and start the project.</span></span> <span data-ttu-id="b6eda-131">Нажмите кнопку входа, и вы будете перенаправлены на `https://login.microsoftonline.com`.</span><span class="sxs-lookup"><span data-stu-id="b6eda-131">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="b6eda-132">Войдите с помощью учетной записи Майкрософт и согласия с запрошенными разрешениями.</span><span class="sxs-lookup"><span data-stu-id="b6eda-132">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="b6eda-133">Браузер перенаправляется на приложение, отображая маркер.</span><span class="sxs-lookup"><span data-stu-id="b6eda-133">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="b6eda-134">Получение сведений о пользователе</span><span class="sxs-lookup"><span data-stu-id="b6eda-134">Get user details</span></span>

<span data-ttu-id="b6eda-135">Начните с создания нового файла, в котором будет храниться весь набор вызовов Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="b6eda-135">Start by creating a new file to hold all of your Microsoft Graph calls.</span></span> <span data-ttu-id="b6eda-136">Щелкните правой кнопкой мыши папку **Graph – Tutorial** в обозревателе решений и выберите команду **Добавить _гт_ новую папку**.</span><span class="sxs-lookup"><span data-stu-id="b6eda-136">Right-click the **graph-tutorial** folder in Solution Explorer, and choose **Add > New Folder**.</span></span> <span data-ttu-id="b6eda-137">НаЗовите папку `Helpers`.</span><span class="sxs-lookup"><span data-stu-id="b6eda-137">Name the folder `Helpers`.</span></span> <span data-ttu-id="b6eda-138">Щелкните новую папку правой кнопкой мыши и выберите команду **Добавить класс _гт_..**.. ПриСвойте файлу `GraphHelper.cs` имя и нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="b6eda-138">Right click this new folder and choose **Add > Class...**. Name the file `GraphHelper.cs` and choose **Add**.</span></span> <span data-ttu-id="b6eda-139">Замените содержимое этого файла приведенным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="b6eda-139">Replace the contents of this file with the following code.</span></span>

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

<span data-ttu-id="b6eda-140">При этом реализуется `GetUserDetails` функция, которая использует пакет SDK Microsoft Graph для вызова `/me` конечной точки и возврата результата.</span><span class="sxs-lookup"><span data-stu-id="b6eda-140">This implements the `GetUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

<span data-ttu-id="b6eda-141">Обновите `OnAuthorizationCodeReceivedAsync` метод в `App_Start/Startup.Auth.cs` методе, чтобы вызвать эту функцию.</span><span class="sxs-lookup"><span data-stu-id="b6eda-141">Update the `OnAuthorizationCodeReceivedAsync` method in `App_Start/Startup.Auth.cs` to call this function.</span></span> <span data-ttu-id="b6eda-142">Сначала добавьте следующий `using` оператор в начало файла.</span><span class="sxs-lookup"><span data-stu-id="b6eda-142">First, add the following `using` statement to the top of the file.</span></span>

```cs
using graph_tutorial.Helpers;
```

<span data-ttu-id="b6eda-143">Замените существующий `try` блок на `OnAuthorizationCodeReceivedAsync` приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="b6eda-143">Replace the existing `try` block in `OnAuthorizationCodeReceivedAsync` with the following code.</span></span>

```cs
try
{
    string[] scopes = graphScopes.Split(' ');

    var result = await idClient.AcquireTokenByAuthorizationCodeAsync(
        notification.Code, scopes);

    var userDetails = await GraphHelper.GetUserDetailsAsync(result.AccessToken);

    string email = string.IsNullOrEmpty(userDetails.Mail) ?
        userDetails.UserPrincipalName : userDetails.Mail;

    message = "User info retrieved.";
    debug = $"User: {userDetails.DisplayName}, Email: {email}";
}
```

<span data-ttu-id="b6eda-144">Теперь, если вы сохраните изменения и запустите приложение, после входа вы увидите имя пользователя и адрес электронной почты, а не маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="b6eda-144">Now if you save your changes and start the app, after sign-in you should see the user's name and email address instead of the access token.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="b6eda-145">Сохранение маркеров</span><span class="sxs-lookup"><span data-stu-id="b6eda-145">Storing the tokens</span></span>

<span data-ttu-id="b6eda-146">Теперь, когда вы можете получить маркеры, следует реализовать способ их хранения в приложении.</span><span class="sxs-lookup"><span data-stu-id="b6eda-146">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="b6eda-147">Так как это пример приложения, мы будем использовать сеанс для хранения маркеров.</span><span class="sxs-lookup"><span data-stu-id="b6eda-147">Since this is a sample app, we'll use the session to store the tokens.</span></span> <span data-ttu-id="b6eda-148">Реальное приложение использует более надежное решение для безопасного хранения, например базу данных.</span><span class="sxs-lookup"><span data-stu-id="b6eda-148">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

<span data-ttu-id="b6eda-149">Щелкните правой кнопкой мыши папку **Graph – Tutorial** в обозревателе решений и выберите команду **Добавить _гт_ новую папку**.</span><span class="sxs-lookup"><span data-stu-id="b6eda-149">Right-click the **graph-tutorial** folder in Solution Explorer, and choose **Add > New Folder**.</span></span> <span data-ttu-id="b6eda-150">НаЗовите папку `TokenStorage`.</span><span class="sxs-lookup"><span data-stu-id="b6eda-150">Name the folder `TokenStorage`.</span></span> <span data-ttu-id="b6eda-151">Щелкните новую папку правой кнопкой мыши и выберите команду **Добавить класс _гт_..**.. ПриСвойте файлу `SessionTokenStore.cs` имя и нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="b6eda-151">Right click this new folder and choose **Add > Class...**. Name the file `SessionTokenStore.cs` and choose **Add**.</span></span> <span data-ttu-id="b6eda-152">Замените содержимое этого файла приведенным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="b6eda-152">Replace the contents of this file with the following code.</span></span>

```cs
using Microsoft.Identity.Client;
using Newtonsoft.Json;
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
        private static ReaderWriterLockSlim sessionLock = new ReaderWriterLockSlim(LockRecursionPolicy.NoRecursion);
        private readonly string userId = string.Empty;
        private readonly string cacheId = string.Empty;
        private readonly string cachedUserId = string.Empty;
        private HttpContextBase httpContext = null;

        TokenCache tokenCache = new TokenCache();

        public SessionTokenStore(string userId, HttpContextBase httpContext)
        {
            this.userId = userId;
            cacheId = $"{userId}_TokenCache";
            cachedUserId = $"{userId}_UserCache";
            this.httpContext = httpContext;
            Load();
        }

        public TokenCache GetMsalCacheInstance()
        {
            tokenCache.SetBeforeAccess(BeforeAccessNotification);
            tokenCache.SetAfterAccess(AfterAccessNotification);
            Load();
            return tokenCache;
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
            tokenCache.Deserialize((byte[])httpContext.Session[cacheId]);
            sessionLock.ExitReadLock();
        }

        private void Persist()
        {
            sessionLock.EnterReadLock();
            httpContext.Session[cacheId] = tokenCache.Serialize();
            sessionLock.ExitReadLock();
        }

        // Triggered right before MSAL needs to access the cache.
        private void BeforeAccessNotification(TokenCacheNotificationArgs args)
        {
            // Reload the cache from the persistent store in case it changed since the last access.
            Load();
        }

        // Triggered right after MSAL accessed the cache.
        private void AfterAccessNotification(TokenCacheNotificationArgs args)
        {
            // if the access operation resulted in a cache update
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

<span data-ttu-id="b6eda-153">Этот код создает `SessionTokenStore` класс, работающий с `TokenCache` классом библиотеки MSAL.</span><span class="sxs-lookup"><span data-stu-id="b6eda-153">This code creates a `SessionTokenStore` class that works with the MSAL library's `TokenCache` class.</span></span> <span data-ttu-id="b6eda-154">В большинстве кода здесь `TokenCache` описывается сериализация и десериализация для сеанса.</span><span class="sxs-lookup"><span data-stu-id="b6eda-154">Most of the code here involves serializing and deserializing the `TokenCache` to the session.</span></span> <span data-ttu-id="b6eda-155">Кроме того, он предоставляет класс и методы для сериализации и десериализации сведений о пользователе в сеансе.</span><span class="sxs-lookup"><span data-stu-id="b6eda-155">It also provides a class and methods to serialize and deserialize the user's details to the session.</span></span>

<span data-ttu-id="b6eda-156">Теперь добавьте приведенный ниже `using` оператор в начало `App_Start/Startup.Auth.cs` файла.</span><span class="sxs-lookup"><span data-stu-id="b6eda-156">Now, add the following `using` statement to the top of the `App_Start/Startup.Auth.cs` file.</span></span>

```cs
using graph_tutorial.TokenStorage;
using System.IdentityModel.Claims;
```

<span data-ttu-id="b6eda-157">Теперь обновите `OnAuthorizationCodeReceivedAsync` функцию, чтобы создать экземпляр `SessionTokenStore` класса и предоставить конструктору для `ConfidentialClientApplication` объекта.</span><span class="sxs-lookup"><span data-stu-id="b6eda-157">Now update the `OnAuthorizationCodeReceivedAsync` function to create an instance of the `SessionTokenStore` class and provide that to the constructor for the `ConfidentialClientApplication` object.</span></span> <span data-ttu-id="b6eda-158">Это приведет к тому, что MSAL использует реализацию кэша для хранения маркеров.</span><span class="sxs-lookup"><span data-stu-id="b6eda-158">That will cause MSAL to use your cache implementation for storing tokens.</span></span> <span data-ttu-id="b6eda-159">Замените имеющуюся функцию `OnAuthorizationCodeReceivedAsync` указанным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="b6eda-159">Replace the existing `OnAuthorizationCodeReceivedAsync` function with the following.</span></span>

```cs
private async Task OnAuthorizationCodeReceivedAsync(AuthorizationCodeReceivedNotification notification)
{
    // Get the signed in user's id and create a token cache
    string signedInUserId = notification.AuthenticationTicket.Identity.FindFirst(ClaimTypes.NameIdentifier).Value;
    SessionTokenStore tokenStore = new SessionTokenStore(signedInUserId,
        notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase);

    var idClient = new ConfidentialClientApplication(
        appId, redirectUri, new ClientCredential(appSecret), tokenStore.GetMsalCacheInstance(), null);

    try
    {
        string[] scopes = graphScopes.Split(' ');

        var result = await idClient.AcquireTokenByAuthorizationCodeAsync(
            notification.Code, scopes);

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
    catch(Microsoft.Graph.ServiceException ex)
    {
        string message = "GetUserDetailsAsync threw an exception";
        notification.HandleResponse();
        notification.Response.Redirect($"/Home/Error?message={message}&debug={ex.Message}");
    }
}
```

<span data-ttu-id="b6eda-160">Чтобы обвести итог изменений:</span><span class="sxs-lookup"><span data-stu-id="b6eda-160">To summarize the changes:</span></span>

- <span data-ttu-id="b6eda-161">Теперь код передает `TokenCache` объект конструктору `ConfidentialClientApplication`.</span><span class="sxs-lookup"><span data-stu-id="b6eda-161">The code now passes a `TokenCache` object to the constructor for `ConfidentialClientApplication`.</span></span> <span data-ttu-id="b6eda-162">Библиотека MSAL будет обрабатывать логику хранения маркеров и обновлять их при необходимости.</span><span class="sxs-lookup"><span data-stu-id="b6eda-162">The MSAL library will handle the logic of storing the tokens and refreshing it when needed.</span></span>
- <span data-ttu-id="b6eda-163">Теперь код передает сведения о пользователях, полученные из Microsoft Graph, `SessionTokenStore` в объект, который будет храниться в сеансе.</span><span class="sxs-lookup"><span data-stu-id="b6eda-163">The code now passes the user details obtained from Microsoft Graph to the `SessionTokenStore` object to store in the session.</span></span>
- <span data-ttu-id="b6eda-164">При успешном выполнении код не переправляется, он просто возвращает.</span><span class="sxs-lookup"><span data-stu-id="b6eda-164">On success, the code no longer redirects, it just returns.</span></span> <span data-ttu-id="b6eda-165">Это позволяет промежуточному по OWIN выполнить процесс проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="b6eda-165">This allows the OWIN middleware to complete the authentication process.</span></span>

<span data-ttu-id="b6eda-166">Так как кэш маркера хранится в сеансе, обновите `SignOut` действие в `Controllers/AccountController.cs` , чтобы очистить хранилище маркеров перед выходом. Сначала добавьте следующий `using` оператор в начало файла.</span><span class="sxs-lookup"><span data-stu-id="b6eda-166">Since the token cache is stored in the session, update the `SignOut` action in `Controllers/AccountController.cs` to clear the token store before signing out. First, add the following `using` statement to the top of the file.</span></span>

```cs
using graph_tutorial.TokenStorage;
```

<span data-ttu-id="b6eda-167">Затем замените существующую `SignOut` функцию на приведенную ниже.</span><span class="sxs-lookup"><span data-stu-id="b6eda-167">Then, replace the existing `SignOut` function with the following.</span></span>

```cs
public ActionResult SignOut()
{
    if (Request.IsAuthenticated)
    {
        string signedInUserId = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier).Value;
        SessionTokenStore tokenStore = new SessionTokenStore(signedInUserId, HttpContext);

        tokenStore.Clear();

        Request.GetOwinContext().Authentication.SignOut(
            CookieAuthenticationDefaults.AuthenticationType);
    }

    return RedirectToAction("Index", "Home");
}
```

<span data-ttu-id="b6eda-168">Сведения о кэшированном пользователе — это то, что потребуется для каждого представления в приложении, поэтому `BaseController` обновите класс, чтобы загрузить эти сведения из сеанса.</span><span class="sxs-lookup"><span data-stu-id="b6eda-168">The cached user details are something that every view in the application will need, so update the `BaseController` class to load this information from the session.</span></span> <span data-ttu-id="b6eda-169">Откройте `Controllers/BaseController.cs` и добавьте приведенные `using` ниже операторы в начало файла.</span><span class="sxs-lookup"><span data-stu-id="b6eda-169">Open `Controllers/BaseController.cs` and add the following `using` statements to the top of the file.</span></span>

```cs
using graph_tutorial.TokenStorage;
using System.Security.Claims;
using System.Web;
using Microsoft.Owin.Security.Cookies;
```

<span data-ttu-id="b6eda-170">Затем добавьте указанную ниже функцию.</span><span class="sxs-lookup"><span data-stu-id="b6eda-170">Then add the following function.</span></span>

```cs
protected override void OnActionExecuting(ActionExecutingContext filterContext)
{
    if (Request.IsAuthenticated)
    {
        // Get the signed in user's id and create a token cache
        string signedInUserId = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier).Value;
        SessionTokenStore tokenStore = new SessionTokenStore(signedInUserId, HttpContext);

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

<span data-ttu-id="b6eda-171">Запустите сервер и пройдите процесс входа.</span><span class="sxs-lookup"><span data-stu-id="b6eda-171">Start the server and go through the sign-in process.</span></span> <span data-ttu-id="b6eda-172">Необходимо вернуться на домашнюю страницу, но пользовательский интерфейс должен измениться, чтобы показать, что вы вошли в систему.</span><span class="sxs-lookup"><span data-stu-id="b6eda-172">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

![Снимок экрана домашней страницы после входа](./images/add-aad-auth-01.png)

<span data-ttu-id="b6eda-174">Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к ссылке **выхода** .</span><span class="sxs-lookup"><span data-stu-id="b6eda-174">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="b6eda-175">При нажатии кнопки **выйти** сбрасывается сеанс и возвращается на домашнюю страницу.</span><span class="sxs-lookup"><span data-stu-id="b6eda-175">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

![Снимок экрана с раскрывающимся меню со ссылкой "выйти"](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="b6eda-177">Обновление маркеров</span><span class="sxs-lookup"><span data-stu-id="b6eda-177">Refreshing tokens</span></span>

<span data-ttu-id="b6eda-178">На этом шаге приложение имеет маркер доступа, который отправляется в `Authorization` заголовке вызовов API.</span><span class="sxs-lookup"><span data-stu-id="b6eda-178">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="b6eda-179">Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.</span><span class="sxs-lookup"><span data-stu-id="b6eda-179">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="b6eda-180">Однако этот маркер кратковременно используется.</span><span class="sxs-lookup"><span data-stu-id="b6eda-180">However, this token is short-lived.</span></span> <span data-ttu-id="b6eda-181">Срок действия маркера истечет через час после его выдачи.</span><span class="sxs-lookup"><span data-stu-id="b6eda-181">The token expires an hour after it is issued.</span></span> <span data-ttu-id="b6eda-182">В этом случае маркер обновления становится полезен.</span><span class="sxs-lookup"><span data-stu-id="b6eda-182">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="b6eda-183">Маркер обновления позволяет приложению запросить новый маркер доступа, не требуя от пользователя повторного входа.</span><span class="sxs-lookup"><span data-stu-id="b6eda-183">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="b6eda-184">Так как приложение использует библиотеку и `TokenCache` объект MSAL, нет необходимости реализовывать какую-либо логику обновления маркеров.</span><span class="sxs-lookup"><span data-stu-id="b6eda-184">Because the app is using the MSAL library and a `TokenCache` object, you do not have to implement any token refresh logic.</span></span> <span data-ttu-id="b6eda-185">`ConfidentialClientApplication.AcquireTokenSilentAsync` Метод выполняет всю логику.</span><span class="sxs-lookup"><span data-stu-id="b6eda-185">The `ConfidentialClientApplication.AcquireTokenSilentAsync` method does all of the logic for you.</span></span> <span data-ttu-id="b6eda-186">Сначала он проверяет кэшированный маркер и, если срок его действия не истек, он возвращает его.</span><span class="sxs-lookup"><span data-stu-id="b6eda-186">It first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="b6eda-187">Если срок действия истек, он использует кэшированный маркер обновления, чтобы получить новый.</span><span class="sxs-lookup"><span data-stu-id="b6eda-187">If it is expired, it uses the cached refresh token to obtain a new one.</span></span> <span data-ttu-id="b6eda-188">Этот метод будет использоваться в следующем модуле.</span><span class="sxs-lookup"><span data-stu-id="b6eda-188">You'll use this method in the following module.</span></span>