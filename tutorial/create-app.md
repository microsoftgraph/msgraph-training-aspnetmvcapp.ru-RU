<!-- markdownlint-disable MD002 MD041 -->

Откройте Visual Studio и выберите **файл _Гт_ создать проект _гт_**. В диалоговом окне **Новый проект** выполните следующие действия:

1. Выберите **шаблоны _Гт_ Visual C# _Гт_ Web**.
1. Выберите **веб-приложение ASP.NET (.NET Framework)**.
1. Введите **Graph — руководство** по имени проекта.

![Visual Studio 2017 диалоговое окно создания нового проекта](./images/vs-new-project-01.png)

> [!NOTE]
> Убедитесь, что вы вводите точно такое же имя для проекта Visual Studio, которое указано в этих инструкциях лаборатории. Имя проекта Visual Studio становится частью пространства имен в коде. Код в этих инструкциях зависит от пространства имен, которое соответствует имени проекта Visual Studio, указанного в данных инструкциях. Если вы используете другое имя проекта, код не будет компилироваться, если не настроить все пространства имен в качестве имени проекта Visual Studio, вводимого при создании проекта.

Нажмите кнопку **ОК**. В диалоговом окне **Создание проекта веб-приложения ASP.NET** выберите элемент **MVC** (в разделе **ASP.NET 4.7.2 шаблоны**) и нажмите кнопку **ОК**.

Нажмите клавишу **F5** или выберите **Отладка _гт_ начать отладку**. Если все работает, браузер по умолчанию должен открыть и отобразить страницу ASP.NET по умолчанию.

Перед перемещением обновите пакет `bootstrap` NuGet и установите некоторые дополнительные пакеты NuGet, которые будут использоваться позже.

- [Microsoft. Owin. host. системвеб](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb/) для включения интерфейсов [Owin](http://owin.org/) в приложении ASP.NET.
- [Microsoft. Owin. Security. опенидконнект](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/) для проверки подлинности OpenID Connect с помощью Azure.
- [Microsoft. Owin. Security. cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies/) для включения проверки подлинности на основе файлов cookie.
- [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) для запроса и управления маркерами доступа.
- [Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) для совершения вызовов в Microsoft Graph.

Выберите **инструменты _Гт_ диспетчера пакетов NuGet _Гт_ консоли диспетчера пакетов**. В консоли диспетчера пакетов введите указанные ниже команды.

```Powershell
Update-Package bootstrap
Install-Package Microsoft.Owin.Host.SystemWeb
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Identity.Client -Version 2.7.0
Install-Package Microsoft.Graph -Version 1.11.0
```

Создайте базовый класс запуска OWIN. Щелкните правой кнопкой мыши `graph-tutorial` папку в обозревателе решений и выберите команду **Добавить _гт_ новый элемент**. Выберите шаблон **стартового класса OWIN** , введите имя файла `Startup.cs`и нажмите кнопку **добавить**.

## <a name="design-the-app"></a>Проектирование приложения

Начните с создания простой модели для сообщения об ошибке. Эта модель используется для Flash-сообщений об ошибках в представлениях приложения.

Щелкните правой кнопкой мыши папку **модели** в обозревателе решений и выберите команду **Добавить класс _гт_..**.. Назовите класс `Alert` и нажмите кнопку **Добавить**. Добавьте следующий код в `Alert.cs`.

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

Теперь обновите глобальную структуру приложения. Откройте `./Views/Shared/_Layout.cshtml` файл и замените все его содержимое приведенным ниже кодом.

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

В этом коде [](https://getbootstrap.com/) добавляется начальная загрузка для простых стилей и [Шрифт Awesome](https://fontawesome.com/) для некоторых простых значков. Он также определяет глобальную структуру с помощью панели навигации и использует `Alert` класс для отображения оповещений.

Теперь откройте `Content/Site.css` и замените все содержимое приведенным ниже кодом.

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

Теперь обновите страницу по умолчанию. Откройте `Views/Home/index.cshtml` файл и замените его содержимое на приведенный ниже код.

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

Теперь добавьте вспомогательную функцию, чтобы создать `Alert` объект и передать его в представление. Чтобы сделать его легко доступным для всех создаваемых контроллеров, определите базовый класс контроллера.

Щелкните правой кнопкой мыши **** папку Controllers в обозревателе решений и выберите команду **Добавить контроллер _гт_..**.. Выберите **контроллер MVC 5 — пустой** и нажмите кнопку **Добавить**. Назовите контроллер `BaseController` и нажмите кнопку **Добавить**. Замените содержимое файла `BaseController.cs` на приведенный ниже код.

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

Любой контроллер может наследовать от этого базового класса контроллера, чтобы получить доступ `Flash` к функции. Обновите `HomeController` класс, чтобы он `BaseController`наследовал от. Откройте `Controllers/HomeController.cs` и измените `public class HomeController : Controller` строку следующим образом:

```cs
public class HomeController : BaseController
```

Сохраните все изменения и перезапустите сервер. Теперь приложение должно выглядеть по-другому.

![Снимок экрана с переработанной домашней страницей](./images/create-app-01.png)