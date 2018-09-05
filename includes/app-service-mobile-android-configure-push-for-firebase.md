---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: bb03e4b5b04a0272d8fa9b032da5adb50878b620
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42809677"
---
1. На [портале Azure](https://portal.azure.com/) щелкните **Просмотреть все** > **Службы приложений**, а затем выберите серверную часть своего мобильного приложения. В разделе **Параметры** щелкните **App Service Push** (Push-уведомления службы приложений) и выберите имя центра уведомлений.
2. Выберите **Google (GCM)**, введите значение **ключа сервера**, полученное от Firebase в ходе предыдущей процедуры, и нажмите кнопку **Сохранить**.

    ![Указание ключа API на портале](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

Теперь серверная часть вашего мобильного приложения настроена для использования Firebase Cloud Messaging. Это позволяет отправлять push-уведомления приложению Android через центр уведомлений.
