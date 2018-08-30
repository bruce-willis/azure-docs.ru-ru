---
title: Экспорт и удаление данных клиентов. LUIS в Azure Cognitive Services | Документация Майкрософт
description: Справочник по экспорту и удалению данных клиентов в Интеллектуальной службе распознавания речи (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/23/2018
ms.author: diberry
ms.openlocfilehash: c6fba28ea3a4b24f02b62b6c3f124569378e5bc8
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43130512"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Экспорт и удаление данных клиентов в Интеллектуальной службе распознавания речи (LUIS) в Cognitive Services

## <a name="summary-of-customer-data-request-features"></a>Сводка о возможностях запроса данных клиента
Интеллектуальная служба распознавания речи (LUIS) сохраняет клиентское содержимое для своей работы. При этом пользователи LUIS могут полностью управлять просмотром, экспортом и удалением своих данных. Это можно сделать с помощью [веб-портала](luis-reference-regions.md) LUIS или [программных API-интерфейсов LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Клиентское содержимое хранится в зашифрованном виде в региональном хранилище Microsoft Azure и включает в себя следующее:

- Содержимое учетных записей пользователей, собранное при регистрации.
- Учебные данные, необходимые для построения моделей (т. е. намерения и сущности).
- Запросы пользователей, записанные в журнал во время выполнения для помощи в улучшении пользовательских моделей.
  - Пользователи могут отключить ведение журнала запросов, добавив `&log=false` к запросу. Дополнительные сведения см. [здесь](luis-resources-faq.md#how-can-i-disable-the-logging-of-utterances).

## <a name="deleting-customer-data"></a>Удаление данных клиента
Пользователи LUIS могут полностью контролировать удаление любого пользовательского содержимого либо через веб-портал LUIS, либо с помощью программных API-интерфейсов LUIS. В следующей таблице представлены ссылки, помогающие освоить оба способа:

| | **Учетная запись пользователя** | **Приложения** | **Фразы** | **Запросы конечных пользователей** |
| --- | --- | --- | --- | --- |
| **Портал** | [Ссылка](luis-how-to-account-settings.md) | [Ссылка](luis-how-to-start-new-app.md#delete-app) | [Ссылка](luis-how-to-start-new-app.md#delete-app) | [Ссылка](luis-how-to-start-new-app.md#delete-app) |
| **Интерфейсы API** | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Экспорт данных клиента
Пользователи LUIS имеют полный доступ к просмотру данных на портале, но их необходимо экспортировать с помощью программных API-интерфейсов LUIS. В следующей таблице представлены ссылки, которые помогают экспортировать данные через программные API-интерфейсы LUIS:

| | **Учетная запись пользователя** | **Приложения** | **Фразы** | **Запросы конечных пользователей** |
| --- | --- | --- | --- | --- |
| **Интерфейсы API** | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Ссылка](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |


## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по регионам LUIS](./luis-reference-regions.md)
