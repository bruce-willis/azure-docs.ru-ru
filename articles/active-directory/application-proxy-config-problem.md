---
title: Проблема при создании приложения прокси приложения | Документы Майкрософт
description: Сведения об устранении неполадок при создании приложений прокси приложения на портале администрирования Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 97795593ecc83a0b15f018861f5cd9139d26443b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335114"
---
# <a name="problem-creating-an-application-proxy-application"></a>Проблема при создании приложения прокси приложения 

Ниже приведены некоторые наиболее распространенные проблемы, возникающие при создании приложения прокси приложения.

## <a name="recommended-documents"></a>Рекомендуемые документы 

Дополнительные сведения о создании приложения прокси приложения на портале администрирования см. в статье [Публикация приложений с помощью прокси приложения Azure AD](manage-apps/application-proxy-publish-azure-portal.md).

Если при создании приложения по инструкциям из этого документа возникает ошибка, см. описание ошибки для получения сведений о ее исправлении. Большинство сообщений об ошибках включают в себя предлагаемое исправление. 

## <a name="specific-things-to-check"></a>Что нужно проверить

Чтобы избежать распространенных ошибок, проверьте следующее:

-   Используется учетная запись администратора с разрешением на создание приложения прокси приложения.

-   Внутренний URL-адрес является уникальным.

-   Внешний URL-адрес является уникальным.

-   URL-адрес начинается с http или https и заканчивается на "/".

-   URL-адрес должен включать имя домена, а не IP-адрес.

Сообщение об ошибке при создании приложения должно отображаться в правом верхнем углу. Для просмотра сообщений об ошибках также можно выбрать значок уведомления.

   ![Уведомление](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Дополнительная информация
[Включение прокси приложения на портале Azure](manage-apps/application-proxy-enable.md)
