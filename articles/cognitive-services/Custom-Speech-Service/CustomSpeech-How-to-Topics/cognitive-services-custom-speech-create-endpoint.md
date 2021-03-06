---
title: Создание пользовательской конечной точки для распознавания речи — Пользовательская служба распознавания речи
titlesuffix: Azure Cognitive Services
description: Узнайте, как создать пользовательскую конечную точку для преобразования речи в текст с помощью Пользовательской службы распознавания речи.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 07/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 958e97f708e7c6920bcb0d65d91656fcf00cd71a
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219029"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>Создание пользовательской конечной точки преобразования речи в текст
После создания пользовательской акустической модели или языковой модели их можно развернуть в пользовательской конечной точке преобразования речи в текст. 

## <a name="create-an-endpoint"></a>Создание конечной точки
Чтобы создать пользовательскую конечную точку, в меню **Настраиваемое распознавание речи** в верхней части страницы выберите **Развертывания**. Откроется страница **Развертывания**, которая содержит таблицу текущих пользовательских конечных точек. Если вы еще не создали конечные точки, таблица будет пустой. Текущий языковой стандарт отражается в заголовке таблицы. 

Чтобы создать развертывание для другого языка, выберите **Change Locale** (Изменить языковой стандарт). Дополнительные сведения о поддерживаемых языках см. в разделе [Поддерживаемые языковые стандарты в Пользовательской службе распознавания речи](cognitive-services-custom-speech-change-locale.md).

Чтобы создать конечную точку, выберите **Создать**. В области **Создать развертывание** введите данные пользовательского развертывания в поля **Имя** и **Описание**.

В поле со списком **Подписка** выберите подписку, которую вы хотите использовать. Если это подписка уровня S2, то вы сможете выбрать единицы масштабирования и ведение журнала содержимого. Дополнительные сведения о единицах масштабирования и ведении журнала см. в разделе [Единицы измерения и квоты Пользовательской службы распознавания речи](../cognitive-services-custom-speech-meters.md).

В следующей таблице показано сопоставление единиц масштабирования и доступных параллельных запросов.

| Единица масштабирования | Количество параллельных запросов |
| ------ | ----- |
| 0 | 1 |
| 1 | 5 |
| 2 | 10 |
| 3 | 15 |
| n | 5 * n |

Можно также включить или отключить ведение журнала содержимого. Таким образом можно выбрать, будет ли трафик конечной точки сохраняться для внутреннего использования корпорацией Майкрософт. Если эта функция не включена, трафик не сохраняется. Отключение ведения журнала содержимого приводит к дополнительным затратам. Дополнительную информацию можно получить на [странице цен](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/).

> [!NOTE]
> На странице цен ведения журнала содержимого называется функцией "Без трассировки".
>


Кроме того, корпорация Майкрософт предоставляет приблизительную оценку затрат, чтобы вы могли учесть влияние на стоимость единиц масштабирования и ведения журнала содержимого. Эта оценка является приблизительной и может отличаться от фактических затрат.

> [!NOTE]
> Эти параметры недоступны для подписок F0 (уровень "Бесплатный").
>

Из списка **Acoustic Model** (Акустическая модель) выберите соответствующую акустическую модель, а из списка **Языковая модель** — соответствующую языковую модель. При выборе акустической и языковой моделей всегда доступны базовые модели Майкрософт. Выбор базовой модели ограничивает возможные сочетания. Невозможно смешивать базовые разговорные модели с базовыми моделями поиска и диктовки.

![Страница "Создать развертывание"](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-create2.png)

> [!NOTE]
> Обязательно примите условия использования и сведения о ценах, установив соответствующий флажок.
>

После выбора акустической и языковой моделей щелкните **Создать**. Вы вернетесь на страницу **Развертывания**. Теперь таблица содержит запись, которая соответствует новой конечной точке. Состояние конечной точки отражает ее текущее состояние во время создания. Создание экземпляра новой конечной точки с пользовательскими моделями может занять до 30 минут. Если состояние развертывания — *Выполнено*, конечная точка готова к использованию.

![Страница "Развертывания"](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-ready.png)

Когда развертывание будет готово, его имя станет ссылкой. При щелчке этой ссылки отображается страница **Сведения о развертывании**, которая содержит URL-адреса, используемые вашей пользовательской конечной точкой для HTTP-запросов, или клиентскую библиотеку службы распознавания речи Microsoft Cognitive Services, которая использует веб-сокеты.

![Страница "Сведения о развертывании"](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-info2.png)

## <a name="next-steps"></a>Дополнительная информация

Другие руководства доступны по следующим ссылкам:
* [Использование пользовательской конечной точки преобразования речи в текст](cognitive-services-custom-speech-use-endpoint.md)
* [Руководство по созданию пользовательской акустической модели](cognitive-services-custom-speech-create-acoustic-model.md)
* [Создание пользовательской языковой модели](cognitive-services-custom-speech-create-language-model.md)
