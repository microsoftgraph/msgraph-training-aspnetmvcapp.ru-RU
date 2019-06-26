<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="d578d-101">В этом руководстве рассказывается, как создать веб-приложение ASP.NET MVC, которое использует API Microsoft Graph для получения сведений о календаре для пользователя.</span><span class="sxs-lookup"><span data-stu-id="d578d-101">This tutorial teaches you how to build an ASP.NET MVC web app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="d578d-102">Если вы хотите просто скачать заполненный учебник, можно скачать его двумя способами.</span><span class="sxs-lookup"><span data-stu-id="d578d-102">If you prefer to just download the completed tutorial, you can download it in two ways.</span></span>
>
> - <span data-ttu-id="d578d-103">Скачайте [краткий старт ASP.NET](https://developer.microsoft.com/graph/quick-start?platform=option-dotnet) для получения рабочего кода в минутах.</span><span class="sxs-lookup"><span data-stu-id="d578d-103">Download the [ASP.NET quick start](https://developer.microsoft.com/graph/quick-start?platform=option-dotnet) to get working code in minutes.</span></span>
> - <span data-ttu-id="d578d-104">Скачайте или скопируйте [репозиторий GitHub](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp).</span><span class="sxs-lookup"><span data-stu-id="d578d-104">Download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d578d-105">Необходимые условия</span><span class="sxs-lookup"><span data-stu-id="d578d-105">Prerequisites</span></span>

<span data-ttu-id="d578d-106">Прежде чем приступить к работе с этим руководством, на компьютере для разработки должен быть установлен [Visual Studio](https://visualstudio.microsoft.com/vs/) .</span><span class="sxs-lookup"><span data-stu-id="d578d-106">Before you start this tutorial, you should have [Visual Studio](https://visualstudio.microsoft.com/vs/) installed on your development machine.</span></span> <span data-ttu-id="d578d-107">Если у вас нет Visual Studio, посетите предыдущую ссылку для получения вариантов загрузки.</span><span class="sxs-lookup"><span data-stu-id="d578d-107">If you do not have Visual Studio, visit the previous link for download options.</span></span>

> [!NOTE]
> <span data-ttu-id="d578d-108">Это руководство было написано с помощью Visual Studio 2017 версии 15,81.</span><span class="sxs-lookup"><span data-stu-id="d578d-108">This tutorial was written with Visual Studio 2017 version 15.81.</span></span> <span data-ttu-id="d578d-109">Действия, описанные в этом руководстве, могут работать с другими версиями, но не тестировались.</span><span class="sxs-lookup"><span data-stu-id="d578d-109">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="watch-the-tutorial"></a><span data-ttu-id="d578d-110">Просмотр руководства</span><span class="sxs-lookup"><span data-stu-id="d578d-110">Watch the tutorial</span></span>

<span data-ttu-id="d578d-111">Этот модуль записан и доступен в канале разработки Office на YouTube.</span><span class="sxs-lookup"><span data-stu-id="d578d-111">This module has been recorded and is available in the Office Development YouTube channel.</span></span>

<!-- markdownlint-disable MD033 MD034 -->
<br/>

> [!VIDEO https://www.youtube-nocookie.com/embed/a2teHZ5WuNc]
<!-- markdownlint-enable MD033 MD034 -->

## <a name="feedback"></a><span data-ttu-id="d578d-112">Обратная связь</span><span class="sxs-lookup"><span data-stu-id="d578d-112">Feedback</span></span>

<span data-ttu-id="d578d-113">Сообщите о нем в [репозиторий GitHub](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp).</span><span class="sxs-lookup"><span data-stu-id="d578d-113">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp).</span></span>
