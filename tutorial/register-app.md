<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="9c914-101">В этом упражнении вы создадите регистрацию нового веб-приложения Azure AD с помощью центра администрирования Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="9c914-101">In this exercise, you will create a new Azure AD web application registration using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="9c914-102">Определите URL-адрес приложения ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="9c914-102">Determine your ASP.NET app's URL.</span></span> <span data-ttu-id="9c914-103">В обозревателе решений Visual Studio выберите проект **Graph – Tutorial** .</span><span class="sxs-lookup"><span data-stu-id="9c914-103">In Visual Studio's Solution Explorer, select the **graph-tutorial** project.</span></span> <span data-ttu-id="9c914-104">В окне **Свойства** найдите значение **URL-адрес**.</span><span class="sxs-lookup"><span data-stu-id="9c914-104">In the **Properties** window, find the value of **URL**.</span></span> <span data-ttu-id="9c914-105">Скопируйте это значение.</span><span class="sxs-lookup"><span data-stu-id="9c914-105">Copy this value.</span></span>

    ![Снимок экрана: окно "Свойства" в Visual Studio](./images/vs-project-url.png)

1. <span data-ttu-id="9c914-107">Откройте браузер и перейдите в [центр администрирования Azure Active Directory](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="9c914-107">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="9c914-108">Вход с использованием **личной учетной записи** (с учетной записью Майкрософт) или **рабочей или учебНой учетной записи**.</span><span class="sxs-lookup"><span data-stu-id="9c914-108">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="9c914-109">Выберите **Azure Active Directory** в левой панели навигации, а затем выберите **Регистрация приложений (Предварительная версия)** в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="9c914-109">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations (Preview)** under **Manage**.</span></span>

    ![<span data-ttu-id="9c914-110">Снимок экрана с регистрациями приложений</span><span class="sxs-lookup"><span data-stu-id="9c914-110">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="9c914-111">Нажмите кнопку **создать регистрацию**.</span><span class="sxs-lookup"><span data-stu-id="9c914-111">Select **New registration**.</span></span> <span data-ttu-id="9c914-112">На странице **Регистрация приложения** задайте указанные ниже значения.</span><span class="sxs-lookup"><span data-stu-id="9c914-112">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="9c914-113">Задайте \*\*\*\* для `ASP.NET Graph Tutorial`параметра Name значение.</span><span class="sxs-lookup"><span data-stu-id="9c914-113">Set **Name** to `ASP.NET Graph Tutorial`.</span></span>
    - <span data-ttu-id="9c914-114">Установите **Поддерживаемые типы учетных** записей для **учетных записей в любом организационном каталоге и личных учетНых записях Майкрософт**.</span><span class="sxs-lookup"><span data-stu-id="9c914-114">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="9c914-115">В разделе **URI перенаправления**установите первый раскрывающийся список `Web` и ПРИСВОЙТЕ ему значение URL-адрес приложения ASP.NET, скопированный на шаге 1.</span><span class="sxs-lookup"><span data-stu-id="9c914-115">Under **Redirect URI**, set the first drop-down to `Web` and set the value to the ASP.NET app URL you copied in step 1.</span></span>

    ![Снимок страницы "регистрация приложения"](./images/aad-register-an-app.png)

1. <span data-ttu-id="9c914-117">Выберите **регистр**.</span><span class="sxs-lookup"><span data-stu-id="9c914-117">Choose **Register**.</span></span> <span data-ttu-id="9c914-118">На странице **учебника по ASP.NET Graph** СКОПИРУЙТЕ значение **идентификатора Application (Client)** и сохраните его, он понадобится на следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="9c914-118">On the **ASP.NET Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Снимок экрана с ИДЕНТИФИКАТОРом приложения для новой регистрации приложения](./images/aad-application-id.png)

1. <span data-ttu-id="9c914-120">Выберите пункт **Проверка** подлинности в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="9c914-120">Select **Authentication** under **Manage**.</span></span> <span data-ttu-id="9c914-121">НаХождение неЯвного раздела **предоставления разрешений** и включение **маркеров ID**.</span><span class="sxs-lookup"><span data-stu-id="9c914-121">Locate the **Implicit grant** section and enable **ID tokens**.</span></span> <span data-ttu-id="9c914-122">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="9c914-122">Choose **Save**.</span></span>

    ![Снимок экрана с неЯвным разделом предоставления](./images/aad-implicit-grant.png)

1. <span data-ttu-id="9c914-124">Выберите **Сертификаты _амп_ секреты** в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="9c914-124">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="9c914-125">Нажмите кнопку **создать секрет клиента** .</span><span class="sxs-lookup"><span data-stu-id="9c914-125">Select the **New client secret** button.</span></span> <span data-ttu-id="9c914-126">Введите значение в поле **Описание** и выберите один из вариантов исТечения **срока действия** и нажмите кнопку **добавить**.</span><span class="sxs-lookup"><span data-stu-id="9c914-126">Enter a value in **Description** and select one of the options for **Expires** and choose **Add**.</span></span>

    ![Снимок экрана: диалоговое окно добавления секрета клиента](./images/aad-new-client-secret.png)

1. <span data-ttu-id="9c914-128">Скопируйте значение секрета клиента, прежде чем покинуть эту страницу.</span><span class="sxs-lookup"><span data-stu-id="9c914-128">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="9c914-129">Это потребуется на следующем этапе.</span><span class="sxs-lookup"><span data-stu-id="9c914-129">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="9c914-130">Этот секрет клиента никогда не отображается еще раз, поэтому убедитесь, что вы хотите скопировать его сейчас.</span><span class="sxs-lookup"><span data-stu-id="9c914-130">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![Снимок экрана с недавно добавленным секретом клиента](./images/aad-copy-client-secret.png)