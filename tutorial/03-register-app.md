<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="6da2c-101">В этом упражнении вы создадите регистрацию нового веб-приложения Azure AD с помощью центра администрирования Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="6da2c-101">In this exercise, you will create a new Azure AD web application registration using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="6da2c-102">Определите SSL-URL-адрес приложения ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="6da2c-102">Determine your ASP.NET app's SSL URL.</span></span> <span data-ttu-id="6da2c-103">В обозревателе решений Visual Studio выберите проект **Graph – Tutorial** .</span><span class="sxs-lookup"><span data-stu-id="6da2c-103">In Visual Studio's Solution Explorer, select the **graph-tutorial** project.</span></span> <span data-ttu-id="6da2c-104">В окне **Свойства** найдите значение **URL-адрес SSL**.</span><span class="sxs-lookup"><span data-stu-id="6da2c-104">In the **Properties** window, find the value of **SSL URL**.</span></span> <span data-ttu-id="6da2c-105">Скопируйте эго.</span><span class="sxs-lookup"><span data-stu-id="6da2c-105">Copy this value.</span></span>

    ![Снимок экрана: окно "Свойства" в Visual Studio](./images/vs-project-url.png)

1. <span data-ttu-id="6da2c-107">Откройте браузер и перейдите к [Центру администрирования Azure Active Directory](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="6da2c-107">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="6da2c-108">Войдите с помощью **личной учетной записи** (т.е. учетной записи Microsoft) или **рабочей (учебной) учетной записи**.</span><span class="sxs-lookup"><span data-stu-id="6da2c-108">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="6da2c-109">Выберите **Azure Active Directory** в левой панели навигации, а затем выберите **Регистрация приложений** в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="6da2c-109">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="6da2c-110">Снимок экрана с регистрациями приложений</span><span class="sxs-lookup"><span data-stu-id="6da2c-110">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="6da2c-111">Выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="6da2c-111">Select **New registration**.</span></span> <span data-ttu-id="6da2c-112">На странице**Зарегистрировать приложение** задайте необходимые значения следующим образом.</span><span class="sxs-lookup"><span data-stu-id="6da2c-112">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="6da2c-113">Введите **имя** `ASP.NET Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="6da2c-113">Set **Name** to `ASP.NET Graph Tutorial`.</span></span>
    - <span data-ttu-id="6da2c-114">Введите **поддерживаемые типы учетных записей** для **учетных записей в любом каталоге организаций и личных учетных записей Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="6da2c-114">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="6da2c-115">В разделе **URI адрес перенаправления** введите значение в первом раскрывающемся списке `Web` и задайте значение URL-адреса приложения ASP.NET, который вы скопировали в первом шаге.</span><span class="sxs-lookup"><span data-stu-id="6da2c-115">Under **Redirect URI**, set the first drop-down to `Web` and set the value to the ASP.NET app URL you copied in step 1.</span></span>

    ![Снимок страницы "регистрация приложения"](./images/aad-register-an-app.png)

1. <span data-ttu-id="6da2c-117">Выберите **регистр**.</span><span class="sxs-lookup"><span data-stu-id="6da2c-117">Select **Register**.</span></span> <span data-ttu-id="6da2c-118">На странице **учебника по ASP.NET Graph** СКОПИРУЙТЕ значение **идентификатора Application (Client)** и сохраните его, он понадобится на следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="6da2c-118">On the **ASP.NET Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Снимок экрана с ИДЕНТИФИКАТОРом приложения для новой регистрации приложения](./images/aad-application-id.png)

1. <span data-ttu-id="6da2c-120">Выберите **Проверка подлинности** в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="6da2c-120">Select **Authentication** under **Manage**.</span></span> <span data-ttu-id="6da2c-121">Найдите раздел **Неявное представление** и включите **Маркеры идентификации**.</span><span class="sxs-lookup"><span data-stu-id="6da2c-121">Locate the **Implicit grant** section and enable **ID tokens**.</span></span> <span data-ttu-id="6da2c-122">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="6da2c-122">Select **Save**.</span></span>

    ![Снимок экрана с неявным разделом предоставления](./images/aad-implicit-grant.png)

1. <span data-ttu-id="6da2c-124">Выберите **Сертификаты и секреты** в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="6da2c-124">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="6da2c-125">Нажмите кнопку **Новый секрет клиента**.</span><span class="sxs-lookup"><span data-stu-id="6da2c-125">Select the **New client secret** button.</span></span> <span data-ttu-id="6da2c-126">Введите значение в поле **Описание** и выберите один из вариантов истечения **срока действия** , а затем нажмите кнопку **добавить**.</span><span class="sxs-lookup"><span data-stu-id="6da2c-126">Enter a value in **Description** and select one of the options for **Expires** and select **Add**.</span></span>

    ![Снимок экрана: диалоговое окно добавления секрета клиента](./images/aad-new-client-secret.png)

1. <span data-ttu-id="6da2c-128">Скопируйте значение секрета клиента, а затем покиньте эту страницу.</span><span class="sxs-lookup"><span data-stu-id="6da2c-128">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="6da2c-129">Оно вам понадобится на следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="6da2c-129">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="6da2c-130">Это секрет клиента, он никогда не отображается еще раз, поэтому убедитесь, что вы скопировали его.</span><span class="sxs-lookup"><span data-stu-id="6da2c-130">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![Снимок экрана с недавно добавленным секретом клиента](./images/aad-copy-client-secret.png)
