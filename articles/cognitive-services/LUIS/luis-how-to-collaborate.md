---
title: Совместная работа с другими участниками над приложениями LUIS в Azure | Документация Майкрософт
description: Сведения об организации совместной работы с другими участниками над приложениями Интеллектуальной службы распознавания речи (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/05/2018
ms.author: diberry
ms.openlocfilehash: 9ea0269439b3d00bf36186cf2fd5c73311526bec
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225607"
---
# <a name="collaborate-with-others-on-language-understanding-luis-apps"></a>Совместная работа с другими пользователями над приложениями Интеллектуальной службы распознавания речи (LUIS)  

Над приложением LUIS можно работать совместно с другими пользователями. 

## <a name="owner-and-collaborators"></a>Владелец и участники совместной работы
У приложения есть один владелец, но может быть много участников совместной работы. 

## <a name="add-collaborator"></a>Добавление участника совместной работы

Чтобы разрешить участникам совместной работы редактировать приложение LUIS, на странице **Settings** (Параметры) приложения LUIS введите адрес электронной почты участника совместной работы и нажмите кнопку **Add collaborator** (Добавить участника совместной работы).

![Добавление участника совместной работы](./media/luis-how-to-collaborate/add-collaborator.png)

* Участники совместной работы могут входить и редактировать приложение LUIS в то же время, когда вы работаете над этим приложением. <!--If a collaborator edits the LUIS app, you see a notification at the top of the browser.-->
* Участники совместной работы не могут добавлять других участников совместной работы.

Дополнительные сведения см. в разделе [Совместная работа с другими пользователями над приложениями Интеллектуальной службы распознавания речи (LUIS)](luis-how-to-account-settings.md#azure-active-directory-tenant-user). 

## <a name="set-application-as-public"></a>Настройка общедоступного режима приложения
Дополнительные сведения см. в разделе [Public app endpoint access](luis-concept-security.md#public-app-endpoint-access) (Доступ к конечной точке общедоступного приложения).

### <a name="transfer-of-ownership"></a>Передача права владения
Хотя LUIS сейчас не поддерживает передачу права владения, приложение можно экспортировать, и другой пользователь LUIS сможет его импортировать. Возможны незначительные различия в оценках LUIS между двумя приложениями. 
