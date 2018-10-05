---
title: Получение ключей подписки — Пользовательская служба распознавания речи
titlesuffix: Azure Cognitive Services
description: Сведения о том, как получить ключи подписки для вызовов Пользовательской службы распознавания речи.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: e4694928baf98bdb0d6aacead8dffec6bb73d6f7
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224778"
---
# <a name="obtain-subscription-keys"></a>Получение ключей подписки
Чтобы приступить к работе с Пользовательской службой распознавания речи Azure, необходимо сначала связать свою учетную запись с подпиской Azure. Доступны подписки для бесплатных и платных уровней. Сведения об уровнях можно узнать на [странице цен](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-key"></a>Получение ключа подписки
1. Ключ подписки можно получить на портале Azure одним из двух способов.

    * Перейдите на [портал Azure](https://ms.portal.azure.com) и добавьте новый API Cognitive Services, выполнив поиск _Cognitive Services_ и выбрав **API-интерфейсы Cognitive Services**.

      ![Поиск Cognitive Services](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription.png)

    * Можно также перейти непосредственно к [API-интерфейсам Cognitive Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServices).

        ![API-интерфейсы Cognitive Services](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription2.png)

    
1. Заполните обязательные поля, приведенные ниже.

      a. **Имя учетной записи** Введите имя на свой выбор. Запомните его, чтобы вашу подписку Cognitive Services можно было найти в списке ресурсов.

      b. **Подписка**. Выберите одну из подписок Azure.

      c. **Тип API**. Выберите **Пользовательская служба распознавания речи (предварительная версия)**.

      d. **Расположение.** В данный момент выбран регион **Западная часть США**.

      д. **Ценовая категория**. Выберите подходящую вам категорию. Категория **F0** является бесплатной. Допустимые квоты описаны на [странице цен](https://www.microsoft.com/cognitive-services/en-us/pricing).

      ![Создание учетной записи Cognitive Services](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-blade.png)

1. Вам следует найти в списке ресурсов либо представление на панели мониторинга, либо службу с указанным именем учетной записи. Выбрав этот элемент, вы увидите общие сведения о вашей службе. В списке слева в разделе **Управление ресурсами** выберите **Ключи**. Скопируйте значение **КЛЮЧ 1**.

      Этот ключ подписки требуется на следующих шагах.

      ![КЛЮЧ 1](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys2.png)

      > [!NOTE]
      > Не копируйте **идентификатор подписки** на странице общих сведений. На следующем шаге вам потребуется ключ подписки.
      >

      ![Идентификатор подписки на странице общих сведений](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys.png)

1. Чтобы ввести ключ подписки, на ленте в правом верхнем углу выберите свою учетную запись пользователя. В раскрывающееся меню выберите **Подписки**.

      ![Элемент меню "Подписки"](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-selection.png)

    Отобразится таблица подписок. При первом открытии она будет пустой.

    ![Таблица подписок](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-list.png)

1. Нажмите кнопку **Добавить**. Введите имя подписки и ключ подписки. Это может быть **КЛЮЧ 1** (первичный ключ) или **КЛЮЧ 2** (вторичный ключ) из вашей подписки.

      ![Имя ключа подписки](../../../media/cognitive-services/custom-speech-service/custom-speech-enter-subsciption.png)

Для передачи данных, обучения модели или выполнения развертывания необходимо связать действия Пользовательской службы распознавания речи с подпиской Azure. Это может быть подписка бесплатной или платной категории. Дополнительные сведения см. на [странице с расценками](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-id"></a>Получение идентификатора подписки
Чтобы получить идентификатор подписки, перейдите на портал Azure. Выполните поиск имен *Cognitive Services* и *Пользовательская служба распознавания речи*, после чего следуйте инструкциям.

> [!NOTE]
> Ключ подписки потребуется позже в этой процедуре.
>

## <a name="next-steps"></a>Дополнительная информация
* Приступите к созданию [пользовательской акустической модели](cognitive-services-custom-speech-create-acoustic-model.md).
* Приступите к созданию [пользовательской языковой модели](cognitive-services-custom-speech-create-language-model.md).
