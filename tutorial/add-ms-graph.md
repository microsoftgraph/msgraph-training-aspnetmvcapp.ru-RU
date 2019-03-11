<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="479e9-101">В этой демонстрации вы добавите Microsoft Graph в приложение.</span><span class="sxs-lookup"><span data-stu-id="479e9-101">In this demo you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="479e9-102">Для этого приложения вы будете использовать клиентскую [библиотеку Microsoft Graph для .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , чтобы совершать вызовы в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="479e9-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="479e9-103">Получение событий календаря из Outlook</span><span class="sxs-lookup"><span data-stu-id="479e9-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="479e9-104">Начните с расширения `GraphHelper` класса, созданного в последнем модуле.</span><span class="sxs-lookup"><span data-stu-id="479e9-104">Start by extending the `GraphHelper` class you created in the last module.</span></span> <span data-ttu-id="479e9-105">Сначала добавьте приведенные ниже `using` операторы в начало `Helpers/GraphHelper.cs` файла.</span><span class="sxs-lookup"><span data-stu-id="479e9-105">First, add the following `using` statements to the top of the `Helpers/GraphHelper.cs` file.</span></span>

```cs
using graph_tutorial.TokenStorage;
using Microsoft.Identity.Client;
using System.Collections.Generic;
using System.Configuration;
using System.Linq;
using System.Security.Claims;
using System.Web;
```

<span data-ttu-id="479e9-106">Затем добавьте в `GraphHelper` класс приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="479e9-106">Then add the following code to the `GraphHelper` class.</span></span>

```cs
// Load configuration settings from PrivateSettings.config
private static string appId = ConfigurationManager.AppSettings["ida:AppId"];
private static string appSecret = ConfigurationManager.AppSettings["ida:AppSecret"];
private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];
private static string graphScopes = ConfigurationManager.AppSettings["ida:AppScopes"];

public static async Task<IEnumerable<Event>> GetEventsAsync()
{
    var graphClient = GetAuthenticatedClient();

    var events = await graphClient.Me.Events.Request()
        .Select("subject,organizer,start,end")
        .OrderBy("createdDateTime DESC")
        .GetAsync();

    return events.CurrentPage;
}

private static GraphServiceClient GetAuthenticatedClient()
{
    return new GraphServiceClient(
        new DelegateAuthenticationProvider(
            async (requestMessage) =>
            {
                // Get the signed in user's id and create a token cache
                string signedInUserId = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier).Value;
                SessionTokenStore tokenStore = new SessionTokenStore(signedInUserId,
                    new HttpContextWrapper(HttpContext.Current));

                var idClient = new ConfidentialClientApplication(
                    appId, redirectUri, new ClientCredential(appSecret),
                    tokenStore.GetMsalCacheInstance(), null);

                var accounts = await idClient.GetAccountsAsync();

                // By calling this here, the token can be refreshed
                // if it's expired right before the Graph call is made
                var result = await idClient.AcquireTokenSilentAsync(
                    graphScopes.Split(' '), accounts.FirstOrDefault());

                requestMessage.Headers.Authorization =
                    new AuthenticationHeaderValue("Bearer", result.AccessToken);
            }));
}
```

<span data-ttu-id="479e9-107">РасСмотрите, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="479e9-107">Consider what this code is doing.</span></span>

- <span data-ttu-id="479e9-108">`GetAuthenticatedClient` Функция инициализирует объект `GraphServiceClient` с помощью поставщика проверки подлинности, который вызывает `AcquireTokenSilentAsync`.</span><span class="sxs-lookup"><span data-stu-id="479e9-108">The `GetAuthenticatedClient` function initializes a `GraphServiceClient` with an authentication provider that calls `AcquireTokenSilentAsync`.</span></span>
- <span data-ttu-id="479e9-109">В `GetEventsAsync` функции:</span><span class="sxs-lookup"><span data-stu-id="479e9-109">In the `GetEventsAsync` function:</span></span>
  - <span data-ttu-id="479e9-110">URL-адрес, который будет вызываться — это `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="479e9-110">The URL that will be called is `/v1.0/me/events`.</span></span>
  - <span data-ttu-id="479e9-111">`Select` Функция ограничит поля, возвращаемые для каждого события, только теми, которые будут реально использоваться в представлении.</span><span class="sxs-lookup"><span data-stu-id="479e9-111">The `Select` function limits the fields returned for each events to just those the view will actually use.</span></span>
  - <span data-ttu-id="479e9-112">`OrderBy` Функция сортирует результаты по дате и времени создания, начиная с самого последнего элемента.</span><span class="sxs-lookup"><span data-stu-id="479e9-112">The `OrderBy` function sorts the results by the date and time they were created, with the most recent item being first.</span></span>

<span data-ttu-id="479e9-113">Теперь создайте контроллер для представлений календаря.</span><span class="sxs-lookup"><span data-stu-id="479e9-113">Now create a controller for the calendar views.</span></span> <span data-ttu-id="479e9-114">Щелкните правой кнопкой мыши \*\*\*\* папку Controllers в обозревателе решений и выберите команду **Добавить контроллер _гт_..**.. Выберите **контроллер MVC 5 — пустой** и нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="479e9-114">Right-click the **Controllers** folder in Solution Explorer and choose **Add > Controller...**. Choose **MVC 5 Controller - Empty** and choose **Add**.</span></span> <span data-ttu-id="479e9-115">НаЗовите контроллер `CalendarController` и нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="479e9-115">Name the controller `CalendarController` and choose **Add**.</span></span> <span data-ttu-id="479e9-116">Замените все содержимое нового файла приведенным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="479e9-116">Replace the entire contents of the new file with the following code.</span></span>

```cs
using graph_tutorial.Helpers;
using System.Threading.Tasks;
using System.Web.Mvc;

namespace graph_tutorial.Controllers
{
    public class CalendarController : BaseController
    {
        // GET: Calendar
        [Authorize]
        public async Task<ActionResult> Index()
        {
            var events = await GraphHelper.GetEventsAsync();
            return Json(events, JsonRequestBehavior.AllowGet);
        }
    }
}
```

<span data-ttu-id="479e9-117">Теперь вы можете протестировать это.</span><span class="sxs-lookup"><span data-stu-id="479e9-117">Now you can test this.</span></span> <span data-ttu-id="479e9-118">Запустите приложение и войдите в систему, а затем щелкните ссылку **Календарь** на панели навигации.</span><span class="sxs-lookup"><span data-stu-id="479e9-118">Start the app, sign in, and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="479e9-119">Если все работает, вы должны увидеть дамп событий JSON в календаре пользователя.</span><span class="sxs-lookup"><span data-stu-id="479e9-119">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="479e9-120">Отображение результатов</span><span class="sxs-lookup"><span data-stu-id="479e9-120">Display the results</span></span>

<span data-ttu-id="479e9-121">Теперь вы можете добавить представление для отображения результатов более удобным для пользователя способом.</span><span class="sxs-lookup"><span data-stu-id="479e9-121">Now you can add a view to display the results in a more user-friendly manner.</span></span> <span data-ttu-id="479e9-122">В обозревателе решений щелкните правой кнопкой мыши папку **views/Calendar** и выберите команду **Добавить представление _гт_..**.. НаЗовите представление `Index` и нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="479e9-122">In Solution Explorer, right-click the **Views/Calendar** folder and choose **Add > View...**. Name the view `Index` and choose **Add**.</span></span> <span data-ttu-id="479e9-123">Замените все содержимое нового файла приведенным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="479e9-123">Replace the entire contents of the new file with the following code.</span></span>

```html
@model IEnumerable<Microsoft.Graph.Event>

@{
    ViewBag.Current = "Calendar";
}

<h1>Calendar</h1>
<table class="table">
    <thead>
        <tr>
            <th scope="col">Organizer</th>
            <th scope="col">Subject</th>
            <th scope="col">Start</th>
            <th scope="col">End</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var item in Model)
        {
            <tr>
                <td>@item.Organizer.EmailAddress.Name</td>
                <td>@item.Subject</td>
                <td>@Convert.ToDateTime(item.Start.DateTime).ToString("M/d/yy h:mm tt")</td>
                <td>@Convert.ToDateTime(item.End.DateTime).ToString("M/d/yy h:mm tt")</td>
            </tr>
        }
    </tbody>
</table>
```

<span data-ttu-id="479e9-124">Это приведет к перебору коллекции событий и добавлению строки таблицы для каждой из них.</span><span class="sxs-lookup"><span data-stu-id="479e9-124">That will loop through a collection of events and add a table row for each one.</span></span> <span data-ttu-id="479e9-125">Удалите `return Json(events, JsonRequestBehavior.AllowGet);` строку из `Index` функции `Controllers/CalendarController.cs`и замените ее на приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="479e9-125">Remove the `return Json(events, JsonRequestBehavior.AllowGet);` line from the `Index` function in `Controllers/CalendarController.cs`, and replace it with the following code.</span></span>

```cs
return View(events);
```

<span data-ttu-id="479e9-126">Запустите приложение и войдите в систему, а затем щелкните ссылку " **Календарь** ".</span><span class="sxs-lookup"><span data-stu-id="479e9-126">Start the app, sign in, and click the **Calendar** link.</span></span> <span data-ttu-id="479e9-127">Теперь приложение должно отображать таблицу событий.</span><span class="sxs-lookup"><span data-stu-id="479e9-127">The app should now render a table of events.</span></span>

![Снимок экрана С таблицей событий](./images/add-msgraph-01.png)