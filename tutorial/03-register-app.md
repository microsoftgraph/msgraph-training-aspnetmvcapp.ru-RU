<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы создадите регистрацию нового веб-приложения Azure AD с помощью центра администрирования Azure Active Directory.

1. Определите SSL-URL-адрес приложения ASP.NET. В обозревателе решений Visual Studio выберите проект **Graph – Tutorial** . В окне **Свойства** найдите значение **URL-адрес SSL**. Скопируйте эго.

    ![Снимок экрана: окно "Свойства" в Visual Studio](./images/vs-project-url.png)

1. Откройте браузер и перейдите к [Центру администрирования Azure Active Directory](https://aad.portal.azure.com). Войдите с помощью **личной учетной записи** (т.е. учетной записи Microsoft) или **рабочей (учебной) учетной записи**.

1. Выберите **Azure Active Directory** на панели навигации слева, затем выберите **Регистрация приложений** в разделе **Управление**.

    ![Снимок экрана с регистрациями приложений ](./images/aad-portal-app-registrations.png)

1. Выберите **Новая регистрация**. На странице**Зарегистрировать приложение** задайте необходимые значения следующим образом.

    - Введите **имя** `ASP.NET Graph Tutorial`.
    - Введите **поддерживаемые типы учетных записей** для **учетных записей в любом каталоге организаций и личных учетных записей Microsoft**.
    - В разделе **URI адрес перенаправления** введите значение в первом раскрывающемся списке `Web` и задайте значение URL-адреса приложения ASP.NET, который вы скопировали в первом шаге.

    ![Снимок страницы "регистрация приложения"](./images/aad-register-an-app.png)

1. Выберите **регистр**. На странице **учебника по ASP.NET Graph** СКОПИРУЙТЕ значение **идентификатора Application (Client)** и сохраните его, он понадобится на следующем шаге.

    ![Снимок экрана с ИДЕНТИФИКАТОРом приложения для новой регистрации приложения](./images/aad-application-id.png)

1. Выберите **Проверка подлинности** в разделе **Управление**. Найдите раздел **Неявное представление** и включите **Маркеры идентификации**. Нажмите кнопку **Сохранить**.

    ![Снимок экрана с неявным разделом предоставления](./images/aad-implicit-grant.png)

1. Выберите **Сертификаты и секреты** в разделе **Управление**. Нажмите кнопку **Новый секрет клиента**. Введите значение в поле **Описание** и выберите один из вариантов **истечения срока действия** , а затем нажмите кнопку **добавить**.

    ![Снимок экрана: диалоговое окно добавления секрета клиента](./images/aad-new-client-secret.png)

1. Скопируйте значение секрета клиента, а затем покиньте эту страницу. Оно вам понадобится на следующем шаге.

    > [!IMPORTANT]
    > Это секрет клиента, он никогда не отображается еще раз, поэтому убедитесь, что вы скопировали его.

    ![Снимок экрана с недавно добавленным секретом клиента](./images/aad-copy-client-secret.png)
