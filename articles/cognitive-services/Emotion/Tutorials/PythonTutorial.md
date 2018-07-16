---
title: Руководство по использованию API распознавания эмоций с Python | Документация Майкрософт
description: Чтобы научиться использовать API распознавания эмоций Cognitive Services с Python, используйте записную книжку Jupyter. Визуализируйте полученные результаты с помощью популярных библиотек.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 70c8ca48c651601f3d7cbb3717c32bfe112176fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380689"
---
# <a name="emotion-api-using-python-tutorial"></a>Руководство по использованию API распознавания эмоций с Python

> [!IMPORTANT]
> Срок действия предварительной версии API Bing для поиска видео истекает 30 октября 2017 г. Оцените новую [предварительную версию API "Индексатор видео"](https://azure.microsoft.com/services/cognitive-services/video-indexer/), которая позволяет легко извлекать полезные сведения из видео и оптимизировать возможности обнаружения содержимого (например, результаты поиска) с помощью обнаружения произнесенных слов, лиц, символов и эмоций. [Узнайте больше](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Чтобы упростить начало работы с API распознавания эмоций, в записной книжке Jupyter ниже показано, как использовать этот API в Python и как визуализировать результаты с помощью популярных библиотек. 

[Ссылка на записную книжку в GitHub](https://github.com/Microsoft/Cognitive-Emotion-Python/blob/master/Jupyter%20Notebook/Emotion%20Analysis%20Example.ipynb)

### <a name="using-the-jupyter-notebook"></a>Использование Jupyter Notebook

Для интерактивного использования записной книжки необходимо клонировать ее и запустить в Jupyter. Чтобы узнать, как приступить к работе с интерактивными записными книжками Jupyter, следуйте инструкциям на странице http://jupyter.readthedocs.org/en/latest/install.html. 

Для использования этой записной книжки вам потребуется ключ подписки на API распознавания эмоций. Чтобы зарегистрироваться, перейдите на [страницу подписки](https://azure.microsoft.com/try/cognitive-services/). Выполните вход с помощью учетной записи Майкрософт, после чего вы сможете оформить подписку и получить бесплатные ключи. После завершения процедуры регистрации вставьте ключ в раздел variables, как показано ниже. Подойдет как первичный, так и вторичный ключ.

```
Python Example 

#Variables

_url = 'https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10

```
