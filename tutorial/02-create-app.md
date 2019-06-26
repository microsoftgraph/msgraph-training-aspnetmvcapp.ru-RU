<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="49ba2-101">Откройте Visual Studio и выберите **файл > создать проект >**.</span><span class="sxs-lookup"><span data-stu-id="49ba2-101">Open Visual Studio, and select **File > New > Project**.</span></span> <span data-ttu-id="49ba2-102">В диалоговом окне **Новый проект** выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="49ba2-102">In the **New Project** dialog, do the following:</span></span>

1. <span data-ttu-id="49ba2-103">Выберите **шаблоны > Visual C# > веб-сайт**.</span><span class="sxs-lookup"><span data-stu-id="49ba2-103">Select **Templates > Visual C# > Web**.</span></span>
1. <span data-ttu-id="49ba2-104">Выберите **веб-приложение ASP.NET (.NET Framework)**.</span><span class="sxs-lookup"><span data-stu-id="49ba2-104">Select **ASP.NET Web Application (.NET Framework)**.</span></span>
1. <span data-ttu-id="49ba2-105">Введите **Graph — руководство** по имени проекта.</span><span class="sxs-lookup"><span data-stu-id="49ba2-105">Enter **graph-tutorial** for the Name of the project.</span></span>

    ![Visual Studio 2017 диалоговое окно создания нового проекта](./images/vs-new-project-01.png)

    > [!NOTE]
    > <span data-ttu-id="49ba2-107">Убедитесь, что вы вводите точно такое же имя для проекта Visual Studio, которое указано в этих инструкциях лаборатории.</span><span class="sxs-lookup"><span data-stu-id="49ba2-107">Ensure that you enter the exact same name for the Visual Studio Project that is specified in these lab instructions.</span></span> <span data-ttu-id="49ba2-108">Имя проекта Visual Studio становится частью пространства имен в коде.</span><span class="sxs-lookup"><span data-stu-id="49ba2-108">The Visual Studio Project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="49ba2-109">Код в этих инструкциях зависит от пространства имен, которое соответствует имени проекта Visual Studio, указанного в данных инструкциях.</span><span class="sxs-lookup"><span data-stu-id="49ba2-109">The code inside these instructions depends on the namespace matching the Visual Studio Project name specified in these instructions.</span></span> <span data-ttu-id="49ba2-110">Если вы используете другое имя проекта, код не будет компилироваться, если не настроить все пространства имен в качестве имени проекта Visual Studio, вводимого при создании проекта.</span><span class="sxs-lookup"><span data-stu-id="49ba2-110">If you use a different project name the code will not compile unless you adjust all the namespaces to match the Visual Studio Project name you enter when you create the project.</span></span>

1. <span data-ttu-id="49ba2-111">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="49ba2-111">Select **OK**.</span></span> <span data-ttu-id="49ba2-112">В диалоговом окне **Создание проекта веб-приложения ASP.NET** выберите элемент **MVC** (в разделе **ASP.NET 4.7.2 шаблоны**) и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="49ba2-112">In the **New ASP.NET Web Application Project** dialog, select **MVC** (under **ASP.NET 4.7.2 Templates**) and select **OK**.</span></span>

1. <span data-ttu-id="49ba2-113">Нажмите клавишу **F5** или выберите **Отладка > начать отладку**.</span><span class="sxs-lookup"><span data-stu-id="49ba2-113">Press **F5** or select **Debug > Start Debugging**.</span></span> <span data-ttu-id="49ba2-114">Если все работает, браузер по умолчанию должен открыть и отобразить страницу ASP.NET по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="49ba2-114">If everything is working, your default browser should open and display a default ASP.NET page.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="49ba2-115">Добавление пакетов NuGet</span><span class="sxs-lookup"><span data-stu-id="49ba2-115">Add NuGet packages</span></span>

<span data-ttu-id="49ba2-116">Перед перемещением обновите пакет `bootstrap` NuGet и установите некоторые дополнительные пакеты NuGet, которые будут использоваться позже.</span><span class="sxs-lookup"><span data-stu-id="49ba2-116">Before moving on, update the `bootstrap` NuGet package, and install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="49ba2-117">[Microsoft. Owin. host. системвеб](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb/) для включения интерфейсов [Owin](http://owin.org/) в приложении ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="49ba2-117">[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb/) to enable the [OWIN](http://owin.org/) interfaces in the ASP.NET application.</span></span>
- <span data-ttu-id="49ba2-118">[Microsoft. Owin. Security. опенидконнект](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/) для проверки подлинности OpenID Connect с помощью Azure.</span><span class="sxs-lookup"><span data-stu-id="49ba2-118">[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/) for doing OpenID Connect authentication with Azure.</span></span>
- <span data-ttu-id="49ba2-119">[Microsoft. Owin. Security. cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies/) для включения проверки подлинности на основе файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="49ba2-119">[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies/) to enable cookie-based authentication.</span></span>
- <span data-ttu-id="49ba2-120">[Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) для запроса и управления маркерами доступа.</span><span class="sxs-lookup"><span data-stu-id="49ba2-120">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) for requesting and managing access tokens.</span></span>
- <span data-ttu-id="49ba2-121">[Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) для совершения звонков в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="49ba2-121">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to Microsoft Graph.</span></span>

1. <span data-ttu-id="49ba2-122">Выберите **инструменты > диспетчер пакетов NuGet > консоли диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="49ba2-122">Select **Tools > NuGet Package Manager > Package Manager Console**.</span></span>
1. <span data-ttu-id="49ba2-123">В консоли диспетчера пакетов введите указанные ниже команды.</span><span class="sxs-lookup"><span data-stu-id="49ba2-123">In the Package Manager Console, enter the following commands.</span></span>

    ```Powershell
    Update-Package bootstrap
    Install-Package Microsoft.Owin.Host.SystemWeb
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Identity.Client -Version 3.0.8
    Install-Package Microsoft.Graph -Version 1.15.0
    ```

## <a name="design-the-app"></a><span data-ttu-id="49ba2-124">Проектирование приложения</span><span class="sxs-lookup"><span data-stu-id="49ba2-124">Design the app</span></span>

<span data-ttu-id="49ba2-125">В этом разделе вы создадите базовую структуру приложения.</span><span class="sxs-lookup"><span data-stu-id="49ba2-125">In this section you will create the basic structure of the application.</span></span>

1. <span data-ttu-id="49ba2-126">Создайте базовый класс запуска OWIN.</span><span class="sxs-lookup"><span data-stu-id="49ba2-126">Create a basic OWIN startup class.</span></span> <span data-ttu-id="49ba2-127">Щелкните правой кнопкой мыши `graph-tutorial` папку в обозревателе решений и выберите команду **Добавить > новый элемент**.</span><span class="sxs-lookup"><span data-stu-id="49ba2-127">Right-click the `graph-tutorial` folder in Solution Explorer and select **Add > New Item**.</span></span> <span data-ttu-id="49ba2-128">Выберите шаблон **стартового класса OWIN** , введите имя файла `Startup.cs`и нажмите кнопку **добавить**.</span><span class="sxs-lookup"><span data-stu-id="49ba2-128">Choose the **OWIN Startup Class** template, name the file `Startup.cs`, and select **Add**.</span></span>

1. <span data-ttu-id="49ba2-129">Щелкните правой кнопкой мыши папку **модели** в обозревателе решений и выберите команду **Добавить класс >..**.. Назовите класс `Alert` и нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="49ba2-129">Right-click the **Models** folder in Solution Explorer and select **Add > Class...**. Name the class `Alert` and select **Add**.</span></span> <span data-ttu-id="49ba2-130">Добавьте следующий код в `Alert.cs`.</span><span class="sxs-lookup"><span data-stu-id="49ba2-130">Add the following code in `Alert.cs`.</span></span> <span data-ttu-id="49ba2-131">Этот класс используется для Flash-сообщений об ошибках в представлениях приложения.</span><span class="sxs-lookup"><span data-stu-id="49ba2-131">You'll use this class to flash error messages in the app's views.</span></span>

    ```cs
    namespace graph_tutorial.Models
    {
        public class Alert
        {
            public const string AlertKey = "TempDataAlerts";
            public string Message { get; set; }
            public string Debug { get; set; }
        }
    }
    ```

1. <span data-ttu-id="49ba2-132">Откройте `./Views/Shared/_Layout.cshtml` файл и замените все его содержимое приведенным ниже кодом, чтобы обновить глобальную структуру приложения.</span><span class="sxs-lookup"><span data-stu-id="49ba2-132">Open the `./Views/Shared/_Layout.cshtml` file, and replace its entire contents with the following code to update the global layout of the app.</span></span>

    ```html
    @{
        var alerts = TempData.ContainsKey(graph_tutorial.Models.Alert.AlertKey) ?
            (List<graph_tutorial.Models.Alert>)TempData[graph_tutorial.Models.Alert.AlertKey] :
            new List<graph_tutorial.Models.Alert>();
    }

    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>ASP.NET Graph Tutorial</title>
        @Styles.Render("~/Content/css")
        @Scripts.Render("~/bundles/modernizr")

        <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.1.0/css/all.css"
              integrity="sha384-lKuwvrZot6UHsBSfcMvOkWwlCMgc0TaWr+30HWe3a4ltaBwTZhyTEggF5tJv8tbt"
              crossorigin="anonymous">
    </head>

    <body>
        <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
            <div class="container">
                @Html.ActionLink("ASP.NET Graph Tutorial", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
                <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarCollapse"
                        aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
                    <span class="navbar-toggler-icon"></span>
                </button>
                <div class="collapse navbar-collapse" id="navbarCollapse">
                    <ul class="navbar-nav mr-auto">
                        <li class="nav-item">
                            @Html.ActionLink("Home", "Index", "Home", new { area = "" },
                                new { @class = ViewBag.Current == "Home" ? "nav-link active" : "nav-link" })
                        </li>
                        @if (Request.IsAuthenticated)
                        {
                            <li class="nav-item" data-turbolinks="false">
                                @Html.ActionLink("Calendar", "Index", "Calendar", new { area = "" },
                                    new { @class = ViewBag.Current == "Calendar" ? "nav-link active" : "nav-link" })
                            </li>
                        }
                    </ul>
                    <ul class="navbar-nav justify-content-end">
                        <li class="nav-item">
                            <a class="nav-link" href="https://developer.microsoft.com/graph/docs/concepts/overview" target="_blank">
                                <i class="fas fa-external-link-alt mr-1"></i>Docs
                            </a>
                        </li>
                        @if (Request.IsAuthenticated)
                        {
                            <li class="nav-item dropdown">
                                <a class="nav-link dropdown-toggle" data-toggle="dropdown" href="#" role="button" aria-haspopup="true" aria-expanded="false">
                                    @if (!string.IsNullOrEmpty(ViewBag.User.Avatar))
                                    {
                                        <img src="@ViewBag.User.Avatar" class="rounded-circle align-self-center mr-2" style="width: 32px;">
                                    }
                                    else
                                    {
                                        <i class="far fa-user-circle fa-lg rounded-circle align-self-center mr-2" style="width: 32px;"></i>
                                    }
                                </a>
                                <div class="dropdown-menu dropdown-menu-right">
                                    <h5 class="dropdown-item-text mb-0">@ViewBag.User.DisplayName</h5>
                                    <p class="dropdown-item-text text-muted mb-0">@ViewBag.User.Email</p>
                                    <div class="dropdown-divider"></div>
                                    @Html.ActionLink("Sign Out", "SignOut", "Account", new { area = "" }, new { @class = "dropdown-item" })
                                </div>
                            </li>
                        }
                        else
                        {
                            <li class="nav-item">
                                @Html.ActionLink("Sign In", "SignIn", "Account", new { area = "" }, new { @class = "nav-link" })
                            </li>
                        }
                    </ul>
                </div>
            </div>
        </nav>
        <main role="main" class="container">
            @foreach (var alert in alerts)
            {
                <div class="alert alert-danger" role="alert">
                    <p class="mb-3">@alert.Message</p>
                    @if (!string.IsNullOrEmpty(alert.Debug))
                    {
                        <pre class="alert-pre border bg-light p-2"><code>@alert.Debug</code></pre>
                    }
                </div>
            }

            @RenderBody()
        </main>
        @Scripts.Render("~/bundles/jquery")
        @Scripts.Render("~/bundles/bootstrap")
        @RenderSection("scripts", required: false)
    </body>
    </html>
    ```

    <span data-ttu-id="49ba2-133">В этом коде [](https://getbootstrap.com/) добавляется начальная загрузка для простых стилей и [Шрифт Awesome](https://fontawesome.com/) для некоторых простых значков.</span><span class="sxs-lookup"><span data-stu-id="49ba2-133">This code adds [Bootstrap](https://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="49ba2-134">Он также определяет глобальную структуру с помощью панели навигации и использует `Alert` класс для отображения оповещений.</span><span class="sxs-lookup"><span data-stu-id="49ba2-134">It also defines a global layout with a nav bar, and uses the `Alert` class to display any alerts.</span></span>

1. <span data-ttu-id="49ba2-135">Откройте `Content/Site.css` и замените все содержимое приведенным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="49ba2-135">Open `Content/Site.css` and replace its entire contents with the following code.</span></span>

    ```css
    body {
      padding-top: 4.5rem;
    }

    .alert-pre {
      word-wrap: break-word;
      word-break: break-all;
      white-space: pre-wrap;
    }
    ```

1. <span data-ttu-id="49ba2-136">Откройте `Views/Home/index.cshtml` файл и замените его содержимое на приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="49ba2-136">Open the `Views/Home/index.cshtml` file and replace its contents with the following.</span></span>

    ```html
    @{
        ViewBag.Current = "Home";
    }

    <div class="jumbotron">
        <h1>ASP.NET Graph Tutorial</h1>
        <p class="lead">This sample app shows how to use the Microsoft Graph API to access Outlook and OneDrive data from ASP.NET</p>
        @if (Request.IsAuthenticated)
        {
            <h4>Welcome @ViewBag.User.DisplayName!</h4>
            <p>Use the navigation bar at the top of the page to get started.</p>
        }
        else
        {
            @Html.ActionLink("Click here to sign in", "SignIn", "Account", new { area = "" }, new { @class = "btn btn-primary btn-large" })
        }
    </div>
    ```

1. <span data-ttu-id="49ba2-137">Щелкните правой кнопкой мыши \*\*\*\* папку Controllers в обозревателе решений и выберите **Добавить контроллер >..**.. Выберите **контроллер MVC 5 — пустой** и нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="49ba2-137">Right-click the **Controllers** folder in Solution Explorer and select **Add > Controller...**. Choose **MVC 5 Controller - Empty** and select **Add**.</span></span> <span data-ttu-id="49ba2-138">Присвойте имя `BaseController` контроллеру и нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="49ba2-138">Name the controller `BaseController` and select **Add**.</span></span> <span data-ttu-id="49ba2-139">Замените содержимое файла `BaseController.cs` на приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="49ba2-139">Replace the contents of `BaseController.cs` with the following code.</span></span>

    ```cs
    using graph_tutorial.Models;
    using System.Collections.Generic;
    using System.Web.Mvc;

    namespace graph_tutorial.Controllers
    {
        public abstract class BaseController : Controller
        {
            protected void Flash(string message, string debug=null)
            {
                var alerts = TempData.ContainsKey(Alert.AlertKey) ?
                    (List<Alert>)TempData[Alert.AlertKey] :
                    new List<Alert>();

                alerts.Add(new Alert
                {
                    Message = message,
                    Debug = debug
                });

                TempData[Alert.AlertKey] = alerts;
            }
        }
    }
    ```

    <span data-ttu-id="49ba2-140">Любой контроллер может наследовать от этого базового класса контроллера, чтобы получить доступ `Flash` к функции.</span><span class="sxs-lookup"><span data-stu-id="49ba2-140">Any controller can inherit from this base controller class to gain access to the `Flash` function.</span></span> <span data-ttu-id="49ba2-141">Обновите `HomeController` класс, чтобы он `BaseController`наследовал от.</span><span class="sxs-lookup"><span data-stu-id="49ba2-141">Update the `HomeController` class to inherit from `BaseController`.</span></span>

1. <span data-ttu-id="49ba2-142">Откройте `Controllers/HomeController.cs` и измените `public class HomeController : Controller` строку следующим образом:</span><span class="sxs-lookup"><span data-stu-id="49ba2-142">Open `Controllers/HomeController.cs` and change the `public class HomeController : Controller` line to:</span></span>

    ```cs
    public class HomeController : BaseController
    ```

1. <span data-ttu-id="49ba2-143">Сохраните все изменения и перезапустите сервер.</span><span class="sxs-lookup"><span data-stu-id="49ba2-143">Save all of your changes and restart the server.</span></span> <span data-ttu-id="49ba2-144">Теперь приложение должно выглядеть по-другому.</span><span class="sxs-lookup"><span data-stu-id="49ba2-144">Now, the app should look very different.</span></span>

    ![Снимок экрана с переработанной домашней страницей](./images/create-app-01.png)
