---
title: Экспорт или удаление данных — Content Moderator
titlesuffix: Azure Cognitive Services
description: Сведения о том, как экспортировать и удалять данные в Content Moderator.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: v-jaswel
ms.openlocfilehash: 9e671fa9735406ec425b105bb121d3a029ef19dc
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227237"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Экспорт и удаление данных пользователя в Content Moderator

Content Moderator собирает данные пользователей, необходимые для работы службы, но пользователи могут полностью контролировать просмотр, экспорт и удаление своих данных с помощью [пользовательского интерфейса проверки](http://contentmoderator.cognitive.microsoft.com/) и [API-интерфейсов](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Дополнительные сведения о том, как экспортировать и удалять данные пользователей в Content Moderator, см. в следующей таблице.

| Данные | Операция экспорта | Операция удаления. |
| ---- | ---------------- | ---------------- |
| Сведения об учетной записи (ключи подписки) | Недоступно | Удаление с помощью портала Azure (подписки Azure). Также можно воспользоваться кнопкой **Удалить команду** на странице "Параметры команды" [пользовательского интерфейса проверки](http://contentmoderator.cognitive.microsoft.com/). |
| Изображения для настраиваемого сопоставления | [Получение идентификаторов изображений](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Изображения хранятся в формате одностороннего защищаемого хэширования, и способа извлечь фактические изображения не существует. | [Удаление всех изображений](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Или удаление ресурса Content Moderator с помощью портала Azure. |
| Условия для настраиваемого сопоставления | [Получение всех условий](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | [Удаление всех условий](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Или удаление ресурса Content Moderator с помощью портала Azure. |
| Теги | Недоступно | Можно использовать значок **Удалить**, доступный для каждого тега на странице Tag settings (Параметры тегов) пользовательского интерфейса проверки. Также можно воспользоваться кнопкой **Удалить команду** на странице "Параметры команды" [пользовательского интерфейса проверки](http://contentmoderator.cognitive.microsoft.com/). |
| Проверки | [Получение проверки](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Воспользуйтесь кнопкой **Удалить команду** на странице "Параметры команды" [пользовательского интерфейса проверки](http://contentmoderator.cognitive.microsoft.com/).
| Пользователи | Недоступно | Воспользуйтесь значком **Удалить**, доступным для каждого пользователя на странице "Параметры команды" [пользовательского интерфейса проверки](http://contentmoderator.cognitive.microsoft.com/). Также можно воспользоваться кнопкой **Удалить команду** на странице "Параметры команды" [пользовательского интерфейса проверки](http://contentmoderator.cognitive.microsoft.com/). |

