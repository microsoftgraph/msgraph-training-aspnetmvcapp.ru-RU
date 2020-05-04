<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="00279-101">В этой демонстрации вы добавите Microsoft Graph в приложение.</span><span class="sxs-lookup"><span data-stu-id="00279-101">In this demo you will incorporate Microsoft Graph into the application.</span></span> <span data-ttu-id="00279-102">Для этого приложения вы будете использовать [клиентскую библиотеку Microsoft Graph для .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , чтобы совершать вызовы в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="00279-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="00279-103">Получение событий календаря из Outlook</span><span class="sxs-lookup"><span data-stu-id="00279-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="00279-104">Начните с расширения `GraphHelper` класса, созданного в последнем модуле.</span><span class="sxs-lookup"><span data-stu-id="00279-104">Start by extending the `GraphHelper` class you created in the last module.</span></span>

1. <span data-ttu-id="00279-105">Добавьте приведенные `using` ниже операторы в начало `Helpers/GraphHelper.cs` файла.</span><span class="sxs-lookup"><span data-stu-id="00279-105">Add the following `using` statements to the top of the `Helpers/GraphHelper.cs` file.</span></span>

    ```cs
    using graph_tutorial.TokenStorage;
    using Microsoft.Identity.Client;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using System.Security.Claims;
    using System.Web;
    ```

1. <span data-ttu-id="00279-106">Добавьте в `GraphHelper` класс приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="00279-106">Add the following code to the `GraphHelper` class.</span></span>

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
                    var idClient = ConfidentialClientApplicationBuilder.Create(appId)
                        .WithRedirectUri(redirectUri)
                        .WithClientSecret(appSecret)
                        .Build();

                    var tokenStore = new SessionTokenStore(idClient.UserTokenCache,
                            HttpContext.Current, ClaimsPrincipal.Current);

                    var accounts = await idClient.GetAccountsAsync();

                    // By calling this here, the token can be refreshed
                    // if it's expired right before the Graph call is made
                    var scopes = graphScopes.Split(' ');
                    var result = await idClient.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                        .ExecuteAsync();

                    requestMessage.Headers.Authorization =
                        new AuthenticationHeaderValue("Bearer", result.AccessToken);
                }));
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="00279-107">Рассмотрите, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="00279-107">Consider what this code is doing.</span></span>
    >
    > - <span data-ttu-id="00279-108">`GetAuthenticatedClient` Функция инициализирует объект `GraphServiceClient` с помощью поставщика проверки подлинности, который вызывает `AcquireTokenSilent`.</span><span class="sxs-lookup"><span data-stu-id="00279-108">The `GetAuthenticatedClient` function initializes a `GraphServiceClient` with an authentication provider that calls `AcquireTokenSilent`.</span></span>
    > - <span data-ttu-id="00279-109">В `GetEventsAsync` функции:</span><span class="sxs-lookup"><span data-stu-id="00279-109">In the `GetEventsAsync` function:</span></span>
    >   - <span data-ttu-id="00279-110">URL-адрес, который будет вызываться — это `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="00279-110">The URL that will be called is `/v1.0/me/events`.</span></span>
    >   - <span data-ttu-id="00279-111">`Select` Функция ограничит поля, возвращаемые для каждого события, только теми, которые будут реально использоваться в представлении.</span><span class="sxs-lookup"><span data-stu-id="00279-111">The `Select` function limits the fields returned for each events to just those the view will actually use.</span></span>
    >   - <span data-ttu-id="00279-112">`OrderBy` Функция сортирует результаты по дате и времени создания, начиная с самого последнего элемента.</span><span class="sxs-lookup"><span data-stu-id="00279-112">The `OrderBy` function sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="00279-113">Создайте контроллер для представлений календаря.</span><span class="sxs-lookup"><span data-stu-id="00279-113">Create a controller for the calendar views.</span></span> <span data-ttu-id="00279-114">Щелкните правой кнопкой мыши папку **Controllers** в обозревателе решений и выберите **Добавить контроллер >..**.. Выберите **контроллер MVC 5 — пустой** и нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="00279-114">Right-click the **Controllers** folder in Solution Explorer and select **Add > Controller...**. Choose **MVC 5 Controller - Empty** and select **Add**.</span></span> <span data-ttu-id="00279-115">Присвойте имя `CalendarController` контроллеру и нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="00279-115">Name the controller `CalendarController` and select **Add**.</span></span> <span data-ttu-id="00279-116">Замените все содержимое нового файла приведенным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="00279-116">Replace the entire contents of the new file with the following code.</span></span>

    ```cs
    using graph_tutorial.Helpers;
    using System;
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

                // Change start and end dates from UTC to local time
                foreach (var ev in events)
                {
                    ev.Start.DateTime = DateTime.Parse(ev.Start.DateTime).ToLocalTime().ToString();
                    ev.Start.TimeZone = TimeZoneInfo.Local.Id;
                    ev.End.DateTime = DateTime.Parse(ev.End.DateTime).ToLocalTime().ToString();
                    ev.End.TimeZone = TimeZoneInfo.Local.Id;
                }

                return Json(events, JsonRequestBehavior.AllowGet);
            }
        }
    }
    ```

1. <span data-ttu-id="00279-117">Запустите приложение и войдите в систему, а затем щелкните ссылку **Календарь** на панели навигации.</span><span class="sxs-lookup"><span data-stu-id="00279-117">Start the app, sign in, and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="00279-118">Если все работает, вы должны увидеть дамп событий JSON в календаре пользователя.</span><span class="sxs-lookup"><span data-stu-id="00279-118">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="00279-119">Отображение результатов</span><span class="sxs-lookup"><span data-stu-id="00279-119">Display the results</span></span>

<span data-ttu-id="00279-120">Теперь вы можете добавить представление для отображения результатов более удобным для пользователя способом.</span><span class="sxs-lookup"><span data-stu-id="00279-120">Now you can add a view to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="00279-121">В обозревателе решений щелкните правой кнопкой мыши папку **views/Calendar** и выберите команду **Добавить > представление..**.. Назовите представление `Index` и нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="00279-121">In Solution Explorer, right-click the **Views/Calendar** folder and select **Add > View...**. Name the view `Index` and select **Add**.</span></span> <span data-ttu-id="00279-122">Замените все содержимое нового файла приведенным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="00279-122">Replace the entire contents of the new file with the following code.</span></span>

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

    <span data-ttu-id="00279-123">Это приведет к перебору коллекции событий и добавлению строки таблицы для каждой из них.</span><span class="sxs-lookup"><span data-stu-id="00279-123">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="00279-124">Удалите `return Json(events, JsonRequestBehavior.AllowGet);` строку из `Index` функции `Controllers/CalendarController.cs`и замените ее на приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="00279-124">Remove the `return Json(events, JsonRequestBehavior.AllowGet);` line from the `Index` function in `Controllers/CalendarController.cs`, and replace it with the following code.</span></span>

    ```cs
    return View(events);
    ```

1. <span data-ttu-id="00279-125">Запустите приложение и войдите в систему, а затем щелкните ссылку " **Календарь** ".</span><span class="sxs-lookup"><span data-stu-id="00279-125">Start the app, sign in, and click the **Calendar** link.</span></span> <span data-ttu-id="00279-126">Теперь приложение должно отображать таблицу событий.</span><span class="sxs-lookup"><span data-stu-id="00279-126">The app should now render a table of events.</span></span>

    ![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)
