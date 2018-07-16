---
title: Приложение Python для обнаружения аномалий — Microsoft Cognitive Services | Документация Майкрософт
description: Изучите записную книжку Python, в которой используется API обнаружения аномалий, входящий в состав служб Microsoft Cognitive Services. Отправьте исходные точки данных в API и получите ожидаемое значение и точки аномалий.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: d35f41ddab21aa155376ad52ff4084298dab8fc5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381716"
---
# <a name="anomaly-detection-python-application"></a>Приложение Python для обнаружения аномалий

В этом руководстве показано, как использовать API обнаружения аномалий в Python и как визуализировать результаты с использованием популярных библиотек. С помощью Jupyter можно запустить учебное руководство и проверить собственные данные, используя свой ключ подписки. Чтобы узнать, как начать работу с интерактивными записными книжками Jupyter Notebook, обратитесь к [документации по Jupyter](http://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="prerequisites"></a>Предварительные требования

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Подписка на API обнаружения аномалий и получение ключа подписки 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="download-the-example-code"></a>Скачивание примера кода

1. Перейдите к [примеру записной книжки на Github](https://github.com/MicrosoftAnomalyDetection/python-sample).
2. Нажмите зеленую кнопку, чтобы клонировать или скачать пример. 

## <a name="opening-the-tutorial-notebook-in-jupyter"></a>Открытие примера записной книжки в Jupyter

1. Откройте командную строку и перейдите в папку python-sample.
2. Выполните команду Jupyter Notebook из командной строки, которая запустит Jupyter.
3. В окне Jupyter щелкните <em>Anomaly Detection API Example.ipynb</em>, чтобы открыть пример записной книжки.   

## <a name="running-the-tutorial"></a>Запуск руководства

Для использования этой записной книжки вам потребуется ключ подписки на API обнаружения аномалий. Перейдите на страницу Subscription (Подписка), чтобы зарегистрироваться. Выполните вход с помощью учетной записи Майкрософт, после чего вы сможете оформить подписку и получить ключи. После завершения процедуры регистрации вставьте ключ в раздел variables записной книжки (воспроизведен ниже). Подойдет как первичный, так и вторичный ключ. Не забудьте заключить ключ в кавычки, чтобы сделать его строковым значением.

```Python

            # Variables
            endpoint = 'https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection'
            subscription_key = None #Here you have to paste your primary key

```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по REST API](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
