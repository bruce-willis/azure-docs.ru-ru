---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 0a74079677a084b2d4e8221cf5a74b356126811d
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42816232"
---
Компонент мобильных приложений в службе приложений Azure использует [Центры уведомлений Azure] для отправки push-уведомлений, поэтому вам нужно настроить центр уведомлений для мобильного приложения.

1. На [портал Azure] щелкните **Службы приложений**, а затем выберите серверную часть приложения. В разделе **Параметры** выберите **Push**.
2. Чтобы добавить в приложение ресурс концентратора уведомлений, нажмите кнопку **Подключить**. Вы можете создать центр или подключиться к существующему.

    ![Настройка концентратора](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Теперь центр уведомлений подключен к серверной части проекта вашего мобильного приложения. Позднее вы настроите этот концентратор уведомлений, чтобы подключиться к системе отправки уведомлений платформы (PNS), которая отправляет push-уведомления на устройства.

[портал Azure]: https://portal.azure.com/
[центры уведомлений Azure]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
