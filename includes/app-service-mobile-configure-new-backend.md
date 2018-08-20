---
title: включение файла
description: включение файла
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/25/2018
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 1d3bfb7bc8a5432392dba3b0c5019902b3e59773
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39514079"
---
1. Нажмите кнопку **Службы приложений**, выберите нужную серверную часть мобильных приложений, откройте раздел **Быстрый запуск**, а затем выберите клиентскую платформу (iOS, Android, Xamarin или Cordova).

    ![Портал Azure с выделенным пунктом "Mobile Apps Quickstart" (Быстрый запуск мобильных приложений)][quickstart]

1. Если подключение к базе данных не настроено, создайте его, сделав следующее:

    ![Подключение мобильных приложений к базе данных на портале Azure][connect]

    a. Создайте сервер и базу данных SQL. Может потребоваться оставить для поля имени строки подключения значение по умолчанию MS_TableConnectionString для выполнения шага 3 ниже.

    ![Создание базы данных и сервера для мобильных приложений на портале Azure][server]

    b. Подождите, пока подключение к данным не будет успешно создано.

    ![Уведомление портала Azure об успешном создании подключения к данным][notification]

    c. Подключение к данным должно быть успешными.

    ![Уведомление на портале Azure: "Уже имеется подключение к данным"][already-connection]

1. В разделе **2. Создание API таблицы** выберите **язык серверной части** — Node.js.

1. Примите подтверждение и выберите **Создание таблицы TodoItem**.
    В базе данных будет создана таблица с элементами списка дел.

    >[!IMPORTANT]
    > При переключении существующей серверной части на Node.js все содержимое перезаписывается. Чтобы вместо этого создать серверную часть .NET, см. раздел [Work with the .NET backend server SDK for Azure Mobile Apps][instructions] (Работа с пакетом SDK для серверной части .NET мобильных приложений Azure).

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
