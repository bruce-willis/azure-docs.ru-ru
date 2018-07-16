---
title: Экспорт и удаление данных в Пользовательской службе визуального распознавания — Azure Cognitive Services | Документация Майкрософт
description: Узнайте, как экспортировать и удалять данные в Пользовательской службе визуального распознавания.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/25/2018
ms.author: v-jaswel
ms.openlocfilehash: 49fc49b3181f56c23167cfbae18911e7db441f8c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382677"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Экспорт и удаление данных пользователя в Пользовательской службе визуального распознавания

Content Moderator собирает данные пользователей, необходимые для работы службы, но пользователи могут полностью контролировать просмотр, экспорт и удаление своих данных с помощью [API обучения](https://go.microsoft.com/fwlink/?linkid=865446) Пользовательской службы визуального распознавания.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Дополнительные сведения о том, как экспортировать и удалять данные пользователей в Пользовательской службе визуального распознавания, см. в следующей таблице.

| Данные | Операция экспорта | Операция удаления |
| ---- | ---------------- | ---------------- |
| Сведения об учетной записи (ключи подписки) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Удаляются с помощью портала Azure (подписки Azure) или с помощью кнопки "Удалить учетную запись" на странице параметров CustomVision.ai (подписки учетной записи Майкрософт) |
| Сведения об итерациях | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Сведения о производительности итераций | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Список итераций | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Проекты и сведения о проектах | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) и [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) |
| Теги изображений | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) и [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) |
| Изображения | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (предоставляется URI для скачивания изображения) и [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (предоставляется URI для скачивания изображения) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) |
| Экспортированные модели | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Удаляются при удалении учетной записи |
