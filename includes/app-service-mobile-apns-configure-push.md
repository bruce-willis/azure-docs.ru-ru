---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 25bb5cfb0073f7533faddec43b38fb5031b82a43
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42809569"
---
1. На компьютере Mac запустите программу **Keychain Access**. На панели навигации слева в разделе **Category** (Категория) откройте **My Certificates** (Мои сертификаты). Найдите и откройте скачанный на предыдущем этапе SSL-сертификат. Выберите только сертификат (без закрытого ключа). Затем [экспортируйте его](https://support.apple.com/kb/PH20122?locale=en_US).
2. На [портале Azure](https://portal.azure.com/) последовательно выберите **Browse All (Просмотреть все)** > **Службы приложений**. Выберите серверную часть для функции "Мобильные приложения". 
3. В разделе **Параметры** выберите **Push-уведомления службы приложений**. Затем выберите имя концентратора уведомлений. 
4. Последовательно выберите пункты **Служба push-уведомлений Apple** > **Загрузить сертификат**. Передайте P12-файл, выбрав правильный **режим** (в зависимости от того, откуда получен SSL-сертификат клиента: из рабочей среды или песочницы). Сохраните внесенные изменения.

Теперь ваша служба настроена для работы с push-уведомлениями в iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
