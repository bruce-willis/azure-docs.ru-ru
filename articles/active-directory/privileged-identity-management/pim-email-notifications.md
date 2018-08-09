---
title: Уведомления по электронной почте в Azure AD PIM | Документы Майкрософт
description: Описание уведомлений по электронной почте в Azure AD Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 07/24/2018
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.openlocfilehash: 7943b4fb8c2027b50ce04c30d21f1b0a58f98ace
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621588"
---
# <a name="email-notifications-in-azure-ad-pim"></a>Уведомления по электронной почте в Azure AD PIM

При возникновении ключевых событий в Azure AD Privileged Identity Management (PIM) соответствующему администратору или пользователю отправляются уведомления по электронной почте. Например, PIM отправляет сообщения о следующих событиях:

- если активация привилегированной роли ожидает утверждения;
- если активация привилегированной роли утверждена;
- если привилегированная роль активирована;
- если привилегированная роль назначена;
- если служба AD PIM включена.

Начиная с конца июля 2018 г. обновится адрес отправителя и внешний вид уведомлений по электронной почте, отправляемых через PIM. Это обновление повлияет на ресурсы PIM для Azure AD и PIM для Azure. Все события, которые ранее вызвали отправку уведомления по электронной почте, будут продолжать отправлять сообщение. Некоторые электронные сообщения будут содержать обновленные, уточняющие сведения.

## <a name="sender-email-address"></a>Адрес электронной почты отправителя

Начиная с конца июля 2018 г. в уведомлениях по электронной почте указывается следующий адрес:

- Адрес электронной почты:  **azure-noreply@microsoft.com**
- Отображаемое имя: Microsoft Azure

Ранее в уведомлениях по электронной почте указывался следующий адрес:

- Адрес электронной почты:  **azureadnotifications@microsoft.com**
- Отображаемое имя: Служба уведомлений Microsoft Azure AD

## <a name="email-subject-line"></a>Строка темы электронного сообщения

С конца июля 2018 года уведомления по электронной почте для ролей ресурсов Azure AD и Azure будут содержать префикс **PIM** в строке темы. Ниже приведен пример:

- "PIM: Alain Charon was permanently assigned the Backup Reader role" (PIM: Алану Шарону была назначена постоянная роль читателя для службы резервного копирования).

## <a name="pim-emails-for-azure-ad-roles"></a>Сообщения электронной почты PIM для ролей Azure AD

Начиная с конца июля 2018 г. обновился внешний вид уведомлений по электронной почте PIM для ролей Azure AD. Ниже приведен пример сообщения электронной почты, отправляемого при активации пользователем привилегированной роли для вымышленной организации Contoso.

![Новое сообщение электронной почты PIM для ролей Azure AD](./media/pim-email-notifications/email-directory-new.png)

Ранее при активации привилегированной роли сообщение выглядело следующим образом.

![Прежнее сообщение электронной почты PIM для ролей Azure AD](./media/pim-email-notifications/email-directory-old.png)

## <a name="pim-emails-for-azure-resource-roles"></a>Сообщения электронной почты PIM для ролей ресурсов Azure

Начиная с конца июля 2018 г. обновился внешний вид уведомлений по электронной почте PIM для ролей ресурсов Azure AD. Ниже приведен пример сообщения электронной почты, отправляемого при назначении пользователю привилегированной роли для вымышленной организации Contoso.

![Новое сообщение электронной почты PIM для ролей ресурсов Azure](./media/pim-email-notifications/email-resources-new.png)

Ранее при назначении пользователю привилегированной роли сообщение выглядело следующим образом.

![Прежнее сообщение электронной почты PIM для ролей ресурсов Azure](./media/pim-email-notifications/email-resources-old.png)

## <a name="next-steps"></a>Дополнительная информация

- [Управление параметрами активации ролей в Azure AD PIM](pim-how-to-change-default-settings.md)
- [Утверждения в Azure AD PIM](azure-ad-pim-approval-workflow.md)
