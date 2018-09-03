---
title: включение файла
description: включение файла
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bf73f64cc1e1789b770d7fae5f62e0ec27425263
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42915164"
---
## <a name="generate-the-certificate-signing-request-file"></a>Создание файла запроса на подпись сертификата

Служба push-уведомлений Apple (APNS) использует сертификаты для аутентификации push-уведомлений. Следуйте приведенным указаниям, чтобы создать сертификата push-уведомлений, необходимый для отправки и получения уведомлений. Дополнительные сведения об этих понятиях см. в официальной документации по [Службе push-уведомлений Apple](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Создайте файл запроса подписи сертификата (CSR), используемый Apple для создания подписанного сертификата push-уведомлений.

1. На компьютере Mac запустите средство **Keychain Access**. Его можно запустить из папки **Utilities** (Служебные программы) или **Other** (Другое) на панели запуска.
2. Щелкните **Keychain Access**, разверните **Certificate Assistant** (Помощник по сертификатам), а затем выберите **Request a Certificate from a Certificate Authority...** (Запросить сертификат в центре сертификации...).

    ![Использование Keychain Access для запрашивания нового сертификата](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. Выберите ваши параметры **User Email Address** (Адрес электронной почты пользователя) и **Common Name** (Общее имя), установите флажок **Saved to disk** (Сохранено на диск), а затем нажмите кнопку **Continue** (Продолжить). Оставьте поле **CA Email Address** (Адрес электронной почты ЦС) пустым, поскольку оно не является обязательным.

    ![Требуемые сведения о сертификате](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Введите имя файла запроса подписи сертификата (CSR) в поле **Save As** (Сохранить как), выберите расположение в поле **Where** (Папка) и нажмите кнопку **Save** (Сохранить).

    ![Выбор имени файла сертификата](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    При этом CSR-файл сохраняется в выбранном месте; по умолчанию — на рабочем столе. Запомните расположение, выбранное для файла.

Затем вы зарегистрируете свое приложение в Apple, включите push-уведомления и передадите экспортированный CSR-файл для создания сертификата push-уведомлений.

## <a name="register-your-app-for-push-notifications"></a>Регистрация приложения для работы с push-уведомлениями

Чтобы иметь возможность отправлять push-уведомления в приложение iOS, необходимо зарегистрировать ваше приложение в системе Apple, а также зарегистрировать его для получения push-уведомлений.  

1. Если ваше приложение еще не зарегистрировано, перейдите на [портал подготовки iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) в центре разработчиков Apple, выполните вход с использованием вашего идентификатора Apple, щелкните **Identifiers** (Идентификаторы) и **App IDs** (Идентификаторы приложений), а затем нажмите кнопку **+**, чтобы зарегистрировать новое приложение.

    ![Страница с идентификаторами приложения на портале подготовки iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Обновите для нового приложения следующие три поля и нажмите кнопку **Продолжить**.

    * **Name** (Имя). В разделе **App ID Description** (Описание идентификатора приложения) в поле **Name** (Имя) введите описательное имя своего приложения.
    * **Bundle Identifier** (Идентификатор набора). В разделе **Explicit App ID** (Явный идентификатор приложения) введите в форму `<Organization Identifier>.<Product Name>` **идентификатор набора**, как упоминалось в [руководстве по распространению приложения](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). Используемые значения *Organization Identifier* (Идентификатор организации) и *Product Name* (Имя продукта) должны соответствовать идентификатору организации и имени продукта, которые вы укажете при создании проекта XCode. На следующем снимке экрана *NotificationHubs* используется как идентификатор организации, а *GetStarted* — как название продукта. Соответствие значениям, которые будут использоваться в проекте XCode, позволит вам использовать правильный профиль публикации в XCode.
    * **Push Notifications** (Push-уведомления). В разделе **App Services** (Службы приложений) установите флажок **Push Notifications** (Push-уведомления).

    ![Форма для регистрации нового идентификатора приложения](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

    При этом будет создан идентификатор вашего приложения и запрошено подтверждение информации. Нажмите кнопку **Register** (Зарегистрировать), чтобы подтвердить новый идентификатор приложения.

    Нажав кнопку **Register** (Зарегистрировать), вы увидите экран **Registration complete** (Регистрация завершена), как показано на изображении ниже. Нажмите кнопку **Done**(Готово).

    ![Завершенная регистрация идентификатора приложения](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)

3. В центре разработчиков в разделе App IDs (Идентификаторы приложений) найдите созданный идентификатор и щелкните его строку.

    ![Список идентификаторов приложений](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    Если щелкнуть идентификатор приложения, отобразятся сведения о приложении. В нижней части экрана нажмите кнопку **Изменить** .

    ![Страница изменения идентификатора приложения](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

4. Прокрутите до нижней части экрана и нажмите кнопку **Create Certificate...** (Создать сертификат...) в разделе **Development Push SSL Certificate** (SSL-сертификат разработки push-уведомлений).

    ![Кнопка создания сертификата для идентификатора приложения](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Откроется помощник **Add iOS Certificate** (Добавление сертификата iOS).

    > [!NOTE]
    > В этом учебнике используется сертификат разработки. Тот же процесс используется при регистрации сертификата производства. Только убедитесь, что при отправке уведомлений используется тот же тип сертификата.

5. Нажмите кнопку **Choose File** (Выбрать файл), перейдите к папке, где был сохранен CSR-файл, созданный в первом задании, а затем нажмите кнопку **Generate** (Создать).

    ![Страница отправки созданного сертификата CSR](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

6. Когда сертификат будет создан с помощью портала, нажмите кнопку **Download** (Загрузить), а затем — **Done** (Готово).

    ![Страница скачивания созданного сертификата](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    При этом сертификат скачивается и сохраняется на вашем компьютере в папке **Загрузки**.

    ![Поиск файла сертификата в папке "Загрузки"](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > По умолчанию скачанный файл сертификата разработки называется **aps_development.cer**.

7. Дважды щелкните скачанный сертификат push-уведомлений **aps_development.cer**.

    При этом новый сертификат устанавливается в Keychain, как на следующем изображении.

    ![Список сертификатов Keychain Access с новым сертификатом](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Имя вашего сертификата может отличаться, но оно будет начинаться с префикса **Apple Development iOS Push Services:**.

8. В программе Keychain Access щелкните правой кнопкой мыши новый сертификат push-уведомлений, созданный в категории **Certificates** (Сертификаты). Щелкните **Export** (Экспорт), укажите имя файла, выберите формат **.p12** и нажмите кнопку **Save** (Сохранить).

    ![Экспорт сертификата в формате р12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Запишите имя файла и расположение экспортируемого сертификата в формате P12. Он используется для проверки подлинности с помощью имен точек доступа.

    > [!NOTE]
    > В этом учебнике создается файл Quickstart.p12. Имя файла и расположение могут отличаться.

## <a name="create-a-provisioning-profile-for-the-app"></a>Создание профиля подготовки для приложения

1. На [портале подготовки iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) выберите **Provisioning Profiles** (Профили подготовки), щелкните **All** (Все) и нажмите кнопку **+** (+), чтобы создать профиль. Отобразится мастер **Add iOS Provisiong Profile** (Добавление профиля подготовки для iOS):

    ![Список с профилем подготовки](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. В разделе **Development** (Разработка) выберите **iOS App Development** (Разработка приложений для iOS) в качестве типа профиля подготовки и нажмите кнопку **Continue** (Продолжить).

3. Затем в раскрывающемся списке **App ID** (Идентификатор приложения) выберите созданный идентификатор приложения и нажмите кнопку **Continue** (Продолжить).

    ![Выбор идентификатора приложения](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

4. На экране **Select certificates** (Выбор сертификатов) выберите обычный сертификат для разработки, используемый для подписывания кода, и нажмите кнопку **Continue** (Продолжить). Это не созданный сертификат push-уведомлений.

    ![Выбор сертификата](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

5. Затем выберите **Devices** (Устройства) для тестирования и нажмите кнопку **Continue** (Продолжить).

    ![Выбор устройств](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

6. Наконец, укажите имя профиля в поле **Profile Name** (Имя профиля) и нажмите кнопку **Generate** (Создать).

    ![Выбор имени профиля подготовки](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

7. Когда новый профиль подготовки будет создан, загрузите и установите его на компьютер с Xcode. Затем нажмите кнопку **Done**(Готово).

    ![Скачивание профиля подготовки](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)
