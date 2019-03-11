<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="ecf3a-101">В этом упражнении вы создадите регистрацию нового веб-приложения Azure AD с помощью портала реестра приложений (ARP).</span><span class="sxs-lookup"><span data-stu-id="ecf3a-101">In this exercise, you will create a new Azure AD web application registration using the Application Registry Portal (ARP).</span></span>

1. <span data-ttu-id="ecf3a-102">Откройте браузер и перейдите на [портал регистрации приложений](https://apps.dev.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="ecf3a-102">Open a browser and navigate to the [Application Registration Portal](https://apps.dev.microsoft.com).</span></span> <span data-ttu-id="ecf3a-103">Вход с использованием **личной учетной записи** (с учетной записью Майкрософт) или **рабочей или учебНой учетной записи**.</span><span class="sxs-lookup"><span data-stu-id="ecf3a-103">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="ecf3a-104">В верхней части страницы выберите **Добавить приложение** .</span><span class="sxs-lookup"><span data-stu-id="ecf3a-104">Select **Add an app** at the top of the page.</span></span>

    > [!NOTE]
    > <span data-ttu-id="ecf3a-105">Если на странице отображается несколько кнопок **Добавить приложение** , выберите ту, которая соответствует списку **приложений** для конвергенции.</span><span class="sxs-lookup"><span data-stu-id="ecf3a-105">If you see more than one **Add an app** button on the page, select the one that corresponds to the **Converged apps** list.</span></span>

1. <span data-ttu-id="ecf3a-106">На странице **Регистрация приложения** задайте для параметра **имя приложения** значение **ASP.NET Graph** и нажмите кнопку **создать**.</span><span class="sxs-lookup"><span data-stu-id="ecf3a-106">On the **Register your application** page, set the **Application Name** to **ASP.NET Graph Tutorial** and select **Create**.</span></span>

    ![Снимок экрана: создание нового приложения на веб-сайте портала регистрации приложений](./images/arp-create-app-01.png)

1. <span data-ttu-id="ecf3a-108">На странице **Регистрация учебника ASP.NET Graph** в разделе **свойства** скопируйте **идентификатор приложения** так, как он понадобится позже.</span><span class="sxs-lookup"><span data-stu-id="ecf3a-108">On the **ASP.NET Graph Tutorial Registration** page, under the **Properties** section, copy the **Application Id** as you will need it later.</span></span>

    ![Снимок экрана с ИДЕНТИФИКАТОРом только что созданного приложения](./images/arp-create-app-02.png)

1. <span data-ttu-id="ecf3a-110">ПроКрутите список вниз до раздела **секреты приложения** .</span><span class="sxs-lookup"><span data-stu-id="ecf3a-110">Scroll down to the **Application Secrets** section.</span></span>

    1. <span data-ttu-id="ecf3a-111">Выберите **создать новый пароль**.</span><span class="sxs-lookup"><span data-stu-id="ecf3a-111">Select **Generate New Password**.</span></span>
    1. <span data-ttu-id="ecf3a-112">В диалоговом окне **новый пароль** в созданном пароле скопируйте содержимое поля так, как оно потребуется позже.</span><span class="sxs-lookup"><span data-stu-id="ecf3a-112">In the **New password generated** dialog, copy the contents of the box as you will need it later.</span></span>

        > <span data-ttu-id="ecf3a-113">**Важно!** Этот пароль никогда не отображается еще раз, поэтому убедитесь, что вы хотите скопировать его сейчас.</span><span class="sxs-lookup"><span data-stu-id="ecf3a-113">**Important:** This password is never shown again, so make sure you copy it now.</span></span>

    ![Снимок экрана с новым паролем приложения](./images/arp-create-app-03.png)

1. <span data-ttu-id="ecf3a-115">Определите URL-адрес приложения ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="ecf3a-115">Determine your ASP.NET app's URL.</span></span> <span data-ttu-id="ecf3a-116">В обозревателе решений Visual Studio выберите проект **Graph – Tutorial** .</span><span class="sxs-lookup"><span data-stu-id="ecf3a-116">In Visual Studio's Solution Explorer, select the **graph-tutorial** project.</span></span> <span data-ttu-id="ecf3a-117">В окне **Свойства** найдите значение **URL-адрес**.</span><span class="sxs-lookup"><span data-stu-id="ecf3a-117">In the **Properties** window, find the value of **URL**.</span></span> <span data-ttu-id="ecf3a-118">Скопируйте это значение.</span><span class="sxs-lookup"><span data-stu-id="ecf3a-118">Copy this value.</span></span>

    ![Снимок экрана: окно "Свойства" в Visual Studio](./images/vs-project-url.png)

1. <span data-ttu-id="ecf3a-120">ПроКрутите окно вниз до раздела **платформы** .</span><span class="sxs-lookup"><span data-stu-id="ecf3a-120">Scroll down to the **Platforms** section.</span></span>

    1. <span data-ttu-id="ecf3a-121">Нажмите кнопку **Добавить платформу**.</span><span class="sxs-lookup"><span data-stu-id="ecf3a-121">Select **Add Platform**.</span></span>
    1. <span data-ttu-id="ecf3a-122">В диалоговом окне **Добавление платформы** выберите **веб**.</span><span class="sxs-lookup"><span data-stu-id="ecf3a-122">In the **Add Platform** dialog, select **Web**.</span></span>

        ![Снимок экрана: создание платформы для приложения](./images/arp-create-app-04.png)

    1. <span data-ttu-id="ecf3a-124">В поле **веб-** платформа введите URL-адрес, который вы скопировали из свойств проекта Visual Studio для **URL-адресов перенаправления**.</span><span class="sxs-lookup"><span data-stu-id="ecf3a-124">In the **Web** platform box, enter the URL you copied from the Visual Studio project's properties for the **Redirect URLs**.</span></span>

        ![Снимок экрана: недавно добавленная веб-платформа для приложения](./images/arp-create-app-05.png)

1. <span data-ttu-id="ecf3a-126">ПроКрутите страницу вниз и выберите команду **сохранить**.</span><span class="sxs-lookup"><span data-stu-id="ecf3a-126">Scroll to the bottom of the page and select **Save**.</span></span>