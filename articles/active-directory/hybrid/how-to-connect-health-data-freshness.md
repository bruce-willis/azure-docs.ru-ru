---
title: Azure AD Connect Health. Оповещение "Данные службы работоспособности неактуальны" | Документация Майкрософт
description: В этом документе описана причина отправки оповещения "Данные службы работоспособности неактуальны" и действия по устранению этой неполадки.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: 430ea5f0a6f737d7632a4352c24d893368b80558
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46310383"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Оповещение "Данные службы работоспособности неактуальны"

## <a name="overview"></a>Обзор
<li>Расширение Azure AD Connect Health создает оповещение об актуальности данных, если не получает все точки данных от сервера в течение двух часов. Заголовок оповещения — **Данные службы работоспособности неактуальны**. </li>
<li>Оповещение с состоянием **Предупреждение** срабатывает, если служба Connect Health не получает отправленные с сервера частичные элементы данных в течение двух часов. Оповещение с состоянием "Предупреждение" не инициирует отправку уведомлений по электронной почте для администратора клиента. </li>
<li>Оповещение с состоянием **Ошибка** срабатывает, если служба Connect Health не получает никаких отправленных с сервера элементов данных в течение двух часов. Оповещение с состоянием "Ошибка" инициирует отправку уведомлений по электронной почте для администратора клиента. </li>

>[!IMPORTANT] 
> Для этого оповещения соблюдается [политика хранения данных](reference-connect-health-user-privacy.md#data-retention-policy) Connect Health

## <a name="troubleshooting-steps"></a>Действия по устранению неполадок 
* Откройте [раздел требований](how-to-connect-health-agent-install.md#requirements) и убедитесь, что все они соблюдены.
* Используйте [средство тестирования подключения](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) для обнаружения возможных проблем с подключением.


## <a name="next-steps"></a>Дополнительная информация
* [Часто задаваемые вопросы об Azure AD Connect Health](reference-connect-health-faq.md)
