---
title: Совместная работа с другими участниками над приложениями LUIS в Azure | Документация Майкрософт
description: Сведения об организации совместной работы с другими участниками над приложениями Интеллектуальной службы распознавания речи (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/05/2018
ms.author: v-geberr
ms.openlocfilehash: c0451f7621a3c18dbf365f3a03934924c030092f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382096"
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

## <a name="set-application-as-public"></a>Настройка общедоступного режима приложения
Дополнительные сведения см. в разделе [Public app endpoint access](luis-concept-security.md#public-app-endpoint-access) (Доступ к конечной точке общедоступного приложения).

### <a name="transfer-of-ownership"></a>Передача права владения
Хотя LUIS сейчас не поддерживает передачу права владения, приложение можно экспортировать, и другой пользователь LUIS сможет его импортировать. Возможны незначительные различия в оценках LUIS между двумя приложениями. 
