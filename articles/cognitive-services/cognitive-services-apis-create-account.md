---
title: Создание учетной записи API-интерфейсов Cognitive Services на портале Azure | Документация Майкрософт
description: Описывается, как создать учетную запись API-интерфейсов Microsoft Cognitive Services на портале Azure.
services: cognitive-services
documentationcenter: ''
author: garyericson
manager: cgronlun
editor: ''
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: cognitive-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: garye
ms.reviewer: gibattag
ms.openlocfilehash: 3697dd0628f0028cb486139e92c032f0d757c6ed
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380888"
---
# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>Создание учетной записи API-интерфейсов Cognitive Services на портале Azure

Чтобы использовать API-интерфейсы Microsoft Cognitive Services, необходимо сначала создать учетную запись на портале Azure.

1. Войдите на [портале Azure](http://portal.azure.com).

2. Щелкните **+ Create a resource** (+ Создать ресурс).

3. В Azure Marketplace выберите **Искусственный интеллект и Cognitive Services** и откройте список доступных API-интерфейсов. Щелкните **Показать все** для просмотра полного списка API-интерфейсов Cognitive Services. Щелкните выбранный API, чтобы продолжить.

    ![Выбор API-интерфейсов Cognitive Services](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. На странице **Создание** введите приведенные ниже сведения.

   - **Имя учетной записи**: имя используемой учетной записи. Рекомендуется использовать описательное имя, например *AFaceAPIAccount*.

   - **Подписка**: выберите одну из доступных подписок Azure, в которых имеются разрешения участника.

   - **Тип API**: выберите API Cognitive Services, который вы хотите использовать. Дополнительные сведения о различных доступных API-интерфейсах Cognitive Services см. на сайте [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

   - **Ценовая категория**: затраты на учетную запись Cognitive Services зависят от фактического использования и выбранных параметров. Дополнительную информацию о ценах на каждый API см. на [страницах цен](https://azure.microsoft.com/pricing/details/cognitive-services/).

   - **Группа ресурсов**: группа ресурсов — это коллекция ресурсов с одинаковым жизненным циклом, разрешениями и политиками. Дополнительные сведения о группах ресурсов см. в статье [Управление ресурсами Azure через портал](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Расположение группы ресурсов**: этот параметр необходимо указать только в том случае, если выбранный API является глобальным (не привязанным к точке). Если API является глобальным и не привязан к расположению, необходимо указать расположение группы ресурсов, в которой размещаются метаданные, связанные с учетной записью API-интерфейсов Cognitive Services. Это расположение никак не влияет на доступность вашей учетной записи. Дополнительные сведения о группе ресурсов см. в статье [Управление ресурсами Azure через портал](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Явное подтверждение условий использования веб-службы.** Чтобы создать учетную запись, владельцам или участникам подписки (определенным с помощью [управления доступом на основе ролей Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)) необходимо явным образом признать условия, применяемые к службам Cognitive Services, в разделе [Условия использования веб-служб (OST)](https://www.microsoft.com/en-us/Licensing/product-licensing/products.aspx). 

     Владелец подписки может отключить создание учетной записи Cognitive Services для определенной группы ресурсов или подписки с помощью [политик Azure](../azure-policy/azure-policy-introduction.md) в соответствии со статьей об [использовании портала Azure для назначения политик ресурсов и управления ими](../azure-policy/assign-policy-definition.md), присвоив определение политики "Недопустимые типы ресурсов" и указав **Microsoft.CognitiveServices/accounts** в качестве целевого типа ресурсов.

     Если создание учетной записи было отключено, во время ее создания может отображаться следующая ошибка:

     ![Ошибка создания учетной записи](media/cognitive-services-apis-create-account/error-message.png)

5. Чтобы закрепить учетную запись на панели мониторинга на портале Azure, щелкните **Закрепить на панели мониторинга**.

6. Щелкните **Создать** , чтобы создать учетную запись.

После успешного развертывания учетной записи Cognitive Services щелкните ее элемент на панели мониторинга, чтобы просмотреть сведения об этой учетной записи.

**URL-адрес конечной точки** из раздела **Обзор** и ключи из раздела **Ключи** можно использовать, чтобы выполнять вызовы API в своих приложениях.

![Отображение сведений об учетной записи](media/cognitive-services-apis-create-account/display-account.png)

![Отображение ключей учетной записи](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о всех доступных службах Microsoft Cognitive Services см. в разделе [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

Краткие руководства с примерами использования некоторых API-интерфейсов Cognitive Services:

 - [Краткие руководства по компьютерному зрению для C#](/computer-vision/quickstarts/csharp.md)
 - [Использование API анализа текста с Python](/text-analytics/quickstarts/python.md)
 - [Использование API распознавания лиц с JavaScript](/face/quickstarts/javascript.md)
