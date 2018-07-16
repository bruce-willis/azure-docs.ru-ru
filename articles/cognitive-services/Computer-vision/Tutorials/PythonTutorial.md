---
title: Руководство по использованию API компьютерного зрения для Python | Документация Майкрософт
description: Узнайте о том, как использовать API компьютерного зрения с Python с помощью записных книжек Jupyter в службах Microsoft Cognitive Services. Визуализируйте результаты с помощью популярных библиотек.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 02/25/2017
ms.author: kefre
ms.openlocfilehash: a093c2d066e70a8daf1fe1cd33ccf794ecb196af
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380676"
---
# <a name="computer-vision-api-python-tutorial"></a>Руководство по API компьютерного зрения для Python

В этом руководстве показано, как использовать API компьютерного зрения с помощью Python и как визуализировать результаты с помощью популярных библиотек. Для работы с этим руководством используется Jupyter. Чтобы узнать, как приступить к работе с интерактивными записными книжками Jupyter, обратитесь к [документации по Jupyter](http://jupyter.readthedocs.io/en/latest/index.html). 

### <a name="opening-the-tutorial-notebook-in-jupyter"></a>Открытие примера записной книжки в Jupyter 

1. Перейдите к [примеру записной книжке на GitHub](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Нажмите зеленую кнопку, чтобы клонировать или скачать пример. 
3. Откройте командную строку и перейдите в папку _Cognitive-Vision-Python-master\Jupyter Notebook_. 
4. Выполните команду **jupyter notebook** из командной строки. Запустится Jupyter.
5. В окне Jupyter щелкните _Computer Vision API Example.ipynb_, чтобы открыть пример записной книжки. 

### <a name="running-the-tutorial"></a>Запуск примера

Для использования этой записной книжки потребуется ключ подписки на API компьютерного зрения. Перейдите на страницу [Подписка](https://azure.microsoft.com/try/cognitive-services/), чтобы зарегистрироваться. На странице "Вход" войдите с помощью учетной записи Майкрософт. После этого вы сможете оформить подписку и получить бесплатные ключи. Завершив регистрацию, вставьте ключ в раздел переменных записной книжки (см. ниже). Будет использоваться либо первичный, либо вторичный ключ. Не забудьте заключить ключ в кавычки, чтобы сделать его строковым значением.

```python
# Variables

_url = 'https://westcentralus.api.cognitive.microsoft.com/vision/v1/analyses'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```
