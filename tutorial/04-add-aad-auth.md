<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="b5bbf-101">В этом упражнении вы расширим приложение из предыдущего упражнения, чтобы поддерживать проверку подлинности с помощью Azure AD.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="b5bbf-102">Это необходимо для получения необходимого маркера доступа OAuth для вызова API Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph API.</span></span> <span data-ttu-id="b5bbf-103">На этом этапе вы интегрируете ПО по среднему окне OWIN и библиотеку библиотеки проверки подлинности [Майкрософт](https://www.nuget.org/packages/Microsoft.Identity.Client/) в приложение.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-103">In this step you will integrate the OWIN middleware and the [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client/) library into the application.</span></span>

1. <span data-ttu-id="b5bbf-104">Щелкните правой кнопкой мыши проект **учебника** по графику в обозревателе решений **и выберите "Добавить > Новый элемент..."** Choose **Web Configuration File,** name the file `PrivateSettings.config` and select **Add**.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-104">Right-click the **graph-tutorial** project in Solution Explorer and select **Add > New Item...**. Choose **Web Configuration File**, name the file `PrivateSettings.config` and select **Add**.</span></span> <span data-ttu-id="b5bbf-105">Замените все его содержимое указанным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-105">Replace its entire contents with the following code.</span></span>

    ```xml
    <appSettings>
        <add key="ida:AppID" value="YOUR APP ID" />
        <add key="ida:AppSecret" value="YOUR APP PASSWORD" />
        <add key="ida:RedirectUri" value="https://localhost:PORT/" />
        <add key="ida:AppScopes" value="User.Read Calendars.Read" />
    </appSettings>
    ```

    <span data-ttu-id="b5bbf-106">Замените ИД приложения на портале регистрации приложений и замените созданный секрет `YOUR_APP_ID_HERE` `YOUR_APP_PASSWORD_HERE` клиента.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-106">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_PASSWORD_HERE` with the client secret you generated.</span></span> <span data-ttu-id="b5bbf-107">Если секрет клиента содержит какие-либо амперанды ( ), обязательно замените их в `&` `&amp;` `PrivateSettings.config` .</span><span class="sxs-lookup"><span data-stu-id="b5bbf-107">If your client secret contains any ampersands (`&`), be sure to replace them with `&amp;` in `PrivateSettings.config`.</span></span> <span data-ttu-id="b5bbf-108">Кроме того, обязательно измените `PORT` значение для `ida:RedirectUri` URL-адреса приложения.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-108">Also be sure to modify the `PORT` value for the `ida:RedirectUri` to match your application's URL.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="b5bbf-109">Если вы используете управление исходным кодом, например git, пришло бы время исключить файл из системы управления источником, чтобы избежать случайной утечки ИД приложения и `PrivateSettings.config` пароля.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-109">If you're using source control such as git, now would be a good time to exclude the `PrivateSettings.config` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

1. <span data-ttu-id="b5bbf-110">`Web.config`Обновим, чтобы загрузить этот новый файл.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-110">Update `Web.config` to load this new file.</span></span> <span data-ttu-id="b5bbf-111">Замените `<appSettings>` строку (строка 7) на следующий:</span><span class="sxs-lookup"><span data-stu-id="b5bbf-111">Replace the `<appSettings>` (line 7) with the following</span></span>

    ```xml
    <appSettings file="PrivateSettings.config">
    ```

## <a name="implement-sign-in"></a><span data-ttu-id="b5bbf-112">Реализация входов</span><span class="sxs-lookup"><span data-stu-id="b5bbf-112">Implement sign-in</span></span>

<span data-ttu-id="b5bbf-113">Начните с инициализации ПО среднего по OWIN для использования проверки подлинности Azure AD для приложения.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-113">Start by initializing the OWIN middleware to use Azure AD authentication for the app.</span></span>

1. <span data-ttu-id="b5bbf-114">Щелкните правой **кнопкой мыши папку App_Start** в обозревателе решений и выберите **add > Class...**. Назовем файл `Startup.Auth.cs` и выберите **"Добавить".**</span><span class="sxs-lookup"><span data-stu-id="b5bbf-114">Right-click the **App_Start** folder in Solution Explorer and select **Add > Class...**. Name the file `Startup.Auth.cs` and select **Add**.</span></span> <span data-ttu-id="b5bbf-115">Замените все содержимое следующим кодом.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-115">Replace the entire contents with the following code.</span></span>

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
    > <span data-ttu-id="b5bbf-116">Этот код настраивает ПО по середине OWIN со значениями из и определяет два метода `PrivateSettings.config` вызова, `OnAuthenticationFailedAsync` а также `OnAuthorizationCodeReceivedAsync` .</span><span class="sxs-lookup"><span data-stu-id="b5bbf-116">This code configures the OWIN middleware with the values from `PrivateSettings.config` and defines two callback methods, `OnAuthenticationFailedAsync` and `OnAuthorizationCodeReceivedAsync`.</span></span> <span data-ttu-id="b5bbf-117">Эти методы обратного вызова будут вызываться, когда процесс вход в службу возвращается из Azure.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-117">These callback methods will be invoked when the sign-in process returns from Azure.</span></span>

1. <span data-ttu-id="b5bbf-118">Теперь обновим `Startup.cs` файл, чтобы вызвать `ConfigureAuth` метод.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-118">Now update the `Startup.cs` file to call the `ConfigureAuth` method.</span></span> <span data-ttu-id="b5bbf-119">Замените все содержимое на `Startup.cs` следующий код.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-119">Replace the entire contents of `Startup.cs` with the following code.</span></span>

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

1. <span data-ttu-id="b5bbf-120">Добавьте в `Error` класс действие для преобразования `HomeController` параметров `message` `debug` запроса и их преобразования в `Alert` объект.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-120">Add an `Error` action to the `HomeController` class to transform the `message` and `debug` query parameters into an `Alert` object.</span></span> <span data-ttu-id="b5bbf-121">Откройте `Controllers/HomeController.cs` и добавьте следующую функцию.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-121">Open `Controllers/HomeController.cs` and add the following function.</span></span>

    ```cs
    public ActionResult Error(string message, string debug)
    {
        Flash(message, debug);
        return RedirectToAction("Index");
    }
    ```

1. <span data-ttu-id="b5bbf-122">Добавление контроллера для обработки входов.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-122">Add a controller to handle sign-in.</span></span> <span data-ttu-id="b5bbf-123">Щелкните правой **кнопкой** мыши папку "Контроллеры" в обозревателе решений и выберите **"Добавить контроллер >"...** Выберите **контроллер MVC 5 — пустой и** выберите **"Добавить".**</span><span class="sxs-lookup"><span data-stu-id="b5bbf-123">Right-click the **Controllers** folder in Solution Explorer and select **Add > Controller...**. Choose **MVC 5 Controller - Empty** and select **Add**.</span></span> <span data-ttu-id="b5bbf-124">Назовем контроллер `AccountController` и выберите **"Добавить".**</span><span class="sxs-lookup"><span data-stu-id="b5bbf-124">Name the controller `AccountController` and select **Add**.</span></span> <span data-ttu-id="b5bbf-125">Замените все содержимое файла на следующий код.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-125">Replace the entire contents of the file with the following code.</span></span>

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

    <span data-ttu-id="b5bbf-126">Это определяет действие `SignIn` `SignOut` и действие.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-126">This defines a `SignIn` and `SignOut` action.</span></span> <span data-ttu-id="b5bbf-127">Это `SignIn` действие проверяет, был ли запрос уже аутентификацией.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-127">The `SignIn` action checks if the request is already authenticated.</span></span> <span data-ttu-id="b5bbf-128">В этом случае для проверки подлинности пользователя вызывается по середине OWIN.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-128">If not, it invokes the OWIN middleware to authenticate the user.</span></span> <span data-ttu-id="b5bbf-129">Это `SignOut` действие вызывает ПО по середине OWIN, чтобы выйти из нее.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-129">The `SignOut` action invokes the OWIN middleware to sign out.</span></span>

1. <span data-ttu-id="b5bbf-130">Сохраните изменения и запустите проект.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-130">Save your changes and start the project.</span></span> <span data-ttu-id="b5bbf-131">Нажмите кнопку "Вход" и перенаправите вас на `https://login.microsoftonline.com` .</span><span class="sxs-lookup"><span data-stu-id="b5bbf-131">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="b5bbf-132">Войдите в систему с помощью учетной записи Майкрософт и согласиться на запрашиваемую разрешения.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-132">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="b5bbf-133">Браузер перенаправляет пользователя в приложение с отображением маркера.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-133">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="b5bbf-134">Получить сведения о пользователе</span><span class="sxs-lookup"><span data-stu-id="b5bbf-134">Get user details</span></span>

<span data-ttu-id="b5bbf-135">После входа пользователя вы можете получить его сведения из Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-135">Once the user is logged in, you can get their information from Microsoft Graph.</span></span>

1. <span data-ttu-id="b5bbf-136">Щелкните правой кнопкой **мыши папку учебника** по графику в обозревателе решений и **выберите "Добавить > Новую папку".**</span><span class="sxs-lookup"><span data-stu-id="b5bbf-136">Right-click the **graph-tutorial** folder in Solution Explorer, and select **Add > New Folder**.</span></span> <span data-ttu-id="b5bbf-137">Назовем `Helpers` папку.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-137">Name the folder `Helpers`.</span></span>

1. <span data-ttu-id="b5bbf-138">Щелкните правой кнопкой мыши эту новую папку и **выберите "Добавить > Класс..."**. Назовем файл `GraphHelper.cs` и выберите **"Добавить".**</span><span class="sxs-lookup"><span data-stu-id="b5bbf-138">Right click this new folder and select **Add > Class...**. Name the file `GraphHelper.cs` and select **Add**.</span></span> <span data-ttu-id="b5bbf-139">Замените содержимое этого файла на следующий код.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-139">Replace the contents of this file with the following code.</span></span>

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

    <span data-ttu-id="b5bbf-140">При этом реализуется функция, которая использует SDK Microsoft Graph для вызова конечной точки `GetUserDetails` `/me` и возврата результата.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-140">This implements the `GetUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

1. <span data-ttu-id="b5bbf-141">`OnAuthorizationCodeReceivedAsync`Обновив метод `App_Start/Startup.Auth.cs` для вызова этой функции.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-141">Update the `OnAuthorizationCodeReceivedAsync` method in `App_Start/Startup.Auth.cs` to call this function.</span></span> <span data-ttu-id="b5bbf-142">Добавьте следующий `using` выписку в верхнюю часть файла.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-142">Add the following `using` statement to the top of the file.</span></span>

    ```cs
    using graph_tutorial.Helpers;
    ```

1. <span data-ttu-id="b5bbf-143">Замените `try` существующий блок на `OnAuthorizationCodeReceivedAsync` следующий код.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-143">Replace the existing `try` block in `OnAuthorizationCodeReceivedAsync` with the following code.</span></span>

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

1. <span data-ttu-id="b5bbf-144">Сохраните изменения и запустите приложение. После регистрации вместо маркера доступа вы увидите имя пользователя и адрес электронной почты.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-144">Save your changes and start the app, after sign-in you should see the user's name and email address instead of the access token.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="b5bbf-145">Хранение маркеров</span><span class="sxs-lookup"><span data-stu-id="b5bbf-145">Storing the tokens</span></span>

<span data-ttu-id="b5bbf-146">Теперь, когда вы можете получить маркеры, пора реализовать способ их хранения в приложении.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-146">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="b5bbf-147">Так как это пример приложения, вы будете использовать сеанс для хранения маркеров.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-147">Since this is a sample app, you will use the session to store the tokens.</span></span> <span data-ttu-id="b5bbf-148">В реальных приложениях используется более надежное решение для безопасного хранения данных, например база данных.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-148">A real-world app would use a more reliable secure storage solution, like a database.</span></span> <span data-ttu-id="b5bbf-149">В этом разделе вы сделаете следующее:</span><span class="sxs-lookup"><span data-stu-id="b5bbf-149">In this section, you will:</span></span>

- <span data-ttu-id="b5bbf-150">Реализуют класс магазина маркеров для сериализации и хранения кэша маркеров MSAL и сведений пользователя в сеансе пользователя.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-150">Implement a token store class to serialize and store the MSAL token cache and the user's details in the user session.</span></span>
- <span data-ttu-id="b5bbf-151">Обновим код проверки подлинности, чтобы использовать класс магазина маркеров.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-151">Update the authentication code to use the token store class.</span></span>
- <span data-ttu-id="b5bbf-152">Обновим базовый класс контроллера, чтобы показать сохраненные сведения о пользователе всем представлениям в приложении.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-152">Update the base controller class to expose the stored user details to all views in the application.</span></span>

1. <span data-ttu-id="b5bbf-153">Щелкните правой кнопкой **мыши папку учебника** по графику в обозревателе решений и **выберите "Добавить > Новую папку".**</span><span class="sxs-lookup"><span data-stu-id="b5bbf-153">Right-click the **graph-tutorial** folder in Solution Explorer, and select **Add > New Folder**.</span></span> <span data-ttu-id="b5bbf-154">Назовем `TokenStorage` папку.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-154">Name the folder `TokenStorage`.</span></span>

1. <span data-ttu-id="b5bbf-155">Щелкните правой кнопкой мыши эту новую папку и **выберите "Добавить > Класс..."**. Назовем файл `SessionTokenStore.cs` и выберите **"Добавить".**</span><span class="sxs-lookup"><span data-stu-id="b5bbf-155">Right click this new folder and select **Add > Class...**. Name the file `SessionTokenStore.cs` and select **Add**.</span></span> <span data-ttu-id="b5bbf-156">Замените содержимое этого файла на следующий код.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-156">Replace the contents of this file with the following code.</span></span>

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

1. <span data-ttu-id="b5bbf-157">Добавьте следующие `using` утверждения в верхнюю часть `App_Start/Startup.Auth.cs` файла.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-157">Add the following `using` statements to the top of the `App_Start/Startup.Auth.cs` file.</span></span>

    ```cs
    using graph_tutorial.TokenStorage;
    using System.Security.Claims;
    ```

1. <span data-ttu-id="b5bbf-158">Замените имеющуюся функцию `OnAuthorizationCodeReceivedAsync` указанным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-158">Replace the existing `OnAuthorizationCodeReceivedAsync` function with the following.</span></span>

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
    > <span data-ttu-id="b5bbf-159">Изменения в этой новой версии `OnAuthorizationCodeReceivedAsync` делают следующее:</span><span class="sxs-lookup"><span data-stu-id="b5bbf-159">The changes in this new version of `OnAuthorizationCodeReceivedAsync` do the following:</span></span>
    >
    > - <span data-ttu-id="b5bbf-160">Теперь код обтекает класс кэш маркеров пользователя по `ConfidentialClientApplication` `SessionTokenStore` умолчанию.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-160">The code now wraps the `ConfidentialClientApplication`'s default user token cache with the `SessionTokenStore` class.</span></span> <span data-ttu-id="b5bbf-161">Библиотека MSAL будет обрабатывать логику хранения маркеров и их обновления при необходимости.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-161">The MSAL library will handle the logic of storing the tokens and refreshing it when needed.</span></span>
    > - <span data-ttu-id="b5bbf-162">Теперь код передает сведения о пользователе, полученные из Microsoft Graph, в объект для `SessionTokenStore` хранения в сеансе.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-162">The code now passes the user details obtained from Microsoft Graph to the `SessionTokenStore` object to store in the session.</span></span>
    > - <span data-ttu-id="b5bbf-163">В успешном отправляемом коде перенаправление больше не происходит, он просто возвращается.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-163">On success, the code no longer redirects, it just returns.</span></span> <span data-ttu-id="b5bbf-164">Это позволяет ПО-посреднику OWIN завершить процесс проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-164">This allows the OWIN middleware to complete the authentication process.</span></span>

1. <span data-ttu-id="b5bbf-165">`SignOut`Обновите действие, чтобы очистить хранилище маркеров перед выходом из системы. Добавьте следующую `using` выписку в верхнюю часть `Controllers/AccountController.cs` .</span><span class="sxs-lookup"><span data-stu-id="b5bbf-165">Update the `SignOut` action to clear the token store before signing out. Add the following `using` statement to the top of `Controllers/AccountController.cs`.</span></span>

    ```cs
    using graph_tutorial.TokenStorage;
    ```

1. <span data-ttu-id="b5bbf-166">Замените имеющуюся функцию `SignOut` указанным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-166">Replace the existing `SignOut` function with the following.</span></span>

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

1. <span data-ttu-id="b5bbf-167">Откройте `Controllers/BaseController.cs` и добавьте следующие утверждения в `using` верхнюю часть файла.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-167">Open `Controllers/BaseController.cs` and add the following `using` statements to the top of the file.</span></span>

    ```cs
    using graph_tutorial.TokenStorage;
    using System.Security.Claims;
    using System.Web;
    using Microsoft.Owin.Security.Cookies;
    ```

1. <span data-ttu-id="b5bbf-168">Добавьте следующую функцию:</span><span class="sxs-lookup"><span data-stu-id="b5bbf-168">Add the following function.</span></span>

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

1. <span data-ttu-id="b5bbf-169">Запустите сервер и войдите в нее.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-169">Start the server and go through the sign-in process.</span></span> <span data-ttu-id="b5bbf-170">Вы должны вернуться на home-страницу, но пользовательский интерфейс должен измениться, чтобы указать, что вы вписались.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-170">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Снимок экрана с домашней страницей после входов](./images/add-aad-auth-01.png)

1. <span data-ttu-id="b5bbf-172">Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к ссылке **"Выйти".**</span><span class="sxs-lookup"><span data-stu-id="b5bbf-172">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="b5bbf-173">При **нажатии** кнопки "Выйти" сеанс сбрасывается и возвращается на домашней странице.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-173">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Снимок экрана с выпадающим меню со ссылкой "Выйти"](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="b5bbf-175">Обновление маркеров</span><span class="sxs-lookup"><span data-stu-id="b5bbf-175">Refreshing tokens</span></span>

<span data-ttu-id="b5bbf-176">На этом этапе приложение имеет маркер доступа, который отправляется в `Authorization` заголовке вызовов API.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-176">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="b5bbf-177">Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-177">This is the token that allows the app to access Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="b5bbf-178">Однако этот маркер является кратковременной.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-178">However, this token is short-lived.</span></span> <span data-ttu-id="b5bbf-179">Срок действия маркера истекает через час после его выпуска.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-179">The token expires an hour after it is issued.</span></span> <span data-ttu-id="b5bbf-180">В этом случае маркер обновления становится полезным.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-180">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="b5bbf-181">Маркер обновления позволяет приложению запрашивать новый маркер доступа, не требуя от пользователя повторного входить.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-181">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="b5bbf-182">Так как приложение использует библиотеку MSAL и сериализует объект, вам не нужно реализовывать логику обновления `TokenCache` маркеров.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-182">Because the app is using the MSAL library and serializing the `TokenCache` object, you do not have to implement any token refresh logic.</span></span> <span data-ttu-id="b5bbf-183">Этот `ConfidentialClientApplication.AcquireTokenSilentAsync` метод делает всю логику за вас.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-183">The `ConfidentialClientApplication.AcquireTokenSilentAsync` method does all of the logic for you.</span></span> <span data-ttu-id="b5bbf-184">Сначала он проверяет кэшный маркер и, если срок его действия еще не истек, возвращает его.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-184">It first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="b5bbf-185">Если срок действия истек, для получения нового маркера используется кэшный маркер обновления.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-185">If it is expired, it uses the cached refresh token to obtain a new one.</span></span> <span data-ttu-id="b5bbf-186">Этот метод будет применяться в следующем модуле.</span><span class="sxs-lookup"><span data-stu-id="b5bbf-186">You'll use this method in the following module.</span></span>
