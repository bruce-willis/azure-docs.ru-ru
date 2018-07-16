---
title: Учетные данные в Azure Content Moderator | Документация Майкрософт
description: Управление учетными данными Content Moderator, используемыми с интерфейсами API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 4531fa4c8bbb7bb9c1daeaaac6f9e7078dae250a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380104"
---
# <a name="manage-credentials"></a>Управление учетными данными

Учетные данные Content Moderator можно создать с помощью следующих средств:

- [Портал Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- [Инструмент проверки Content Moderator](http://contentmoderator.cognitive.microsoft.com/)

В этой статье объясняется, где их можно найти и как они связаны между собой.

## <a name="the-azure-portal"></a>Портал Azure

На панели мониторинга портала Azure выберите свою учетную запись Content Moderator. В разделе **Управление ресурсами** выберите **Ключи**. Чтобы скопировать ключ, щелкните значок справа от ключа.

![Ключи Content Moderator на портале Azure](images/credentials-azure-portal-keys.PNG)

### <a name="how-to-use-your-azure-account-with-the-review-tool"></a>Как использовать учетную запись Azure с инструментом проверки
Чтобы использовать свой ключ Azure с интерфейсами API проверки, скопируйте идентификатор ресурса, указанный на экране **Свойства** на приведенном ниже снимке экрана. Затем введите его на экране учетных данных инструмента проверки в поля **Whitelisted Resource Id(s)** (Разрешенные идентификаторы ресурсов), как показано в следующем разделе **Идентификатор ресурса**. 

Чтобы использовать свой ключ Azure для рабочих процессов, доступных в Content Moderator, введите его в поле **Ocp-Apim-Subscription-Key** в разделе **Workflow Settings** (Параметры рабочего процесса), как показано в разделе **Рабочие процессы** ниже.

> [!NOTE]
> После ввода в инструмент проверки ключа и идентификатора ресурса из подписки Azure ключ **Ocp-Apim-Subscription-Key** для пробной версии, отображенный на экране "Credentials" (Учетные данные), больше не используется, но всегда будет доступен.
> Ключ пробной версии позволяет выполнить до 5000 транзакций в месяц с частотой в 1 запрос в секунду.

![Идентификатор ресурса Content Moderator на портале Azure](images/credentials-azure-portal-resourceid.PNG)


## <a name="the-review-tool"></a>Инструмент проверки

На панели мониторинга инструмента проверки на вкладке **Settings** (Параметры) выберите **Credentials** (Учетные данные).

![Учетные данные Content Moderator в инструменте проверки](images/credentials-trial-resource-workflow.PNG)

В следующем разделе более подробно рассматривается приведенное выше изображение.


### <a name="api"></a>API

В первой части указаны **конечная точка API проверки**, **идентификатор команды** и **Ocp-Apim-Subscription-Key** (ключ пробной версии Content Moderator, созданный вместе с командой проверки). Используйте их для вызова всех интерфейсов API Content Moderator, включая API проверки.

Также запишите свой идентификатор региона для конечной точки API. Например, **westus** — регион в https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0.

![Ключ Content Moderator в инструменте проверки](images/credentials-trialkey.PNG)


### <a name="resource-id"></a>Идентификатор ресурса

Вторая часть сначала пустая и не содержит идентификаторов ресурсов. **Чтобы использовать ключ подписки Azure с API проверки, перейдите на экран идентификатора ресурса, как показано выше, и скопируйте его в указанное поле**. Щелкните **+**, чтобы сохранить свой идентификатор ресурса.

> [!NOTE]
> Регион подписки Content Moderator должен совпадать с регионом команды проверки, чтобы обеспечить распознавание команды и доступ к ее данным. Например, в изображениях на этой странице регион **Западная часть США** **(4)** содержит подписку Azure для Content Moderator и вашу команду проверки.

![Идентификатор ресурса Content Moderator в инструменте проверки](images/credentials-resourceids.PNG)


### <a name="workflows"></a>Рабочие процессы

В третьей части отображены сведения, используемые для выполнения рабочих процессов. Сначала в ней отображается автоматически созданный ключ пробной версии по умолчанию. 

**Замените его своим ключом Azure, когда получите подписку Azure**. С помощью других двух полей можно использовать списки терминов и изображений для операций проверки текста и оценки изображений соответственно.

![Учетные данные рабочего процесса Content Moderator в инструменте проверки](images/credentials-workflow.PNG)


## <a name="next-steps"></a>Дополнительная информация

* Узнайте, как использовать учетные данные Content Moderator в своих [рабочих процессах](workflows.md).
