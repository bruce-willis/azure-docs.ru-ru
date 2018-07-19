---
title: 'Требование назначения пользователей: Azure AD | Документация Майкрософт'
description: Как требовать назначение пользователей для приложений Azure.
services: active-directory
documentationcenter: ''
author: kgremban
manager: mtillman
editor: ''
ms.assetid: 30b78cba-1e0f-472f-8314-f2250a9b91c3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: kgremban
robots: noindex
ms.openlocfilehash: 4519681d9b91383d27c00a992f85b0cb5d74f235
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859468"
---
# <a name="azure-ad-and-applications-require-user-assignment"></a>Azure AD и приложения: требование назначения пользователей
## <a name="requiring-user-assignment"></a>Требование назначения пользователей
1. Войдите на портал Azure с учетной записью администратора.
2. В главном меню выберите пункт **Все службы**.
3. Выберите каталог, который используете для приложения.
4. Откройте вкладку **Корпоративные приложения**.
5. Выберите приложение из списка приложений, связанных с данным каталогом.
6. Щелкните вкладку **Свойства**.
7. Установите переключатель **Требуется ли назн. польз.?** в положение "Да".
8. Нажмите кнопку **Сохранить** в верхней части экрана.

Теперь будет необходимо назначить пользователей и (или) группы для приложения. См. разделы [Назначение пользователей приложения](active-directory-applications-guiding-developers-assigning-users.md) и [Назначение групп для приложения](active-directory-applications-guiding-developers-assigning-groups.md).

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]
