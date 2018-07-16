---
title: Получение ключей подписки для Пользовательской службы распознавания речи в Azure | Документация Майкрософт
description: Узнайте, как для получить ключи подписки для вызовов к Пользовательской службе распознавания речи в Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: fcef86a19a77581ff82b64173e2ac68b26ae708a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380193"
---
# <a name="obtain-subscription-keys"></a>Получение ключей подписки
Чтобы приступить к работе с Пользовательской службой распознавания речи Azure, необходимо сначала связать свою учетную запись с подпиской Azure. Доступны подписки для бесплатных и платных уровней. Сведения об уровнях можно узнать на [странице цен](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-key"></a>Получение ключа подписки
1. Ключ подписки можно получить на портале Azure одним из двух способов.

    * Перейдите на [портал Azure](https://ms.portal.azure.com) и добавьте новый API Cognitive Services, выполнив поиск _Cognitive Services_ и выбрав **API-интерфейсы Cognitive Services**.

      ![Поиск Cognitive Services](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription.png)

    * Можно также перейти непосредственно к [API-интерфейсам Cognitive Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServices).

        ![API-интерфейсы Cognitive Services](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription2.png)

    
2. Заполните обязательные поля, приведенные ниже.

      a. **Имя учетной записи** Введите имя на свой выбор. Запомните его, чтобы вашу подписку Cognitive Services можно было найти в списке ресурсов.

      Б. **Подписка**. Выберите одну из подписок Azure.

      c. **Тип API**. Выберите **Пользовательская служба распознавания речи (предварительная версия)**.

      d. **Расположение.** В данный момент выбран регион **Западная часть США**.

      д. **Ценовая категория**. Выберите подходящую вам категорию. Категория **F0** является бесплатной. Допустимые квоты описаны на [странице цен](https://www.microsoft.com/cognitive-services/en-us/pricing).

      ![Создание учетной записи Cognitive Services](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-blade.png)

3. Вам следует найти в списке ресурсов либо представление на панели мониторинга, либо службу с указанным именем учетной записи. Выбрав этот элемент, вы увидите общие сведения о вашей службе. В списке слева в разделе **Управление ресурсами** выберите **Ключи**. Скопируйте значение **КЛЮЧ 1**.

      Этот ключ подписки требуется на следующих шагах.

      ![КЛЮЧ 1](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys2.png)

      > [!NOTE]
      > Не копируйте **идентификатор подписки** на странице общих сведений. На следующем шаге вам потребуется ключ подписки.
      >

      ![Идентификатор подписки на странице общих сведений](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys.png)

4. Чтобы ввести ключ подписки, на ленте в правом верхнем углу выберите свою учетную запись пользователя. В раскрывающееся меню выберите **Подписки**.

      ![Элемент меню "Подписки"](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-selection.png)

    Отобразится таблица подписок. При первом открытии она будет пустой.

    ![Таблица подписок](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-list.png)

5. Нажмите кнопку **Добавить**. Введите имя подписки и ключ подписки. Это может быть **КЛЮЧ 1** (первичный ключ) или **КЛЮЧ 2** (вторичный ключ) из вашей подписки.

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
