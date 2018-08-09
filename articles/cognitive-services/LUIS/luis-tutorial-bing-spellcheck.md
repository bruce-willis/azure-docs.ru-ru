---
title: Добавление API проверки орфографии Bing версии 7 в запросы LUIS | Документы Майкрософт
titleSuffix: Azure
description: Включите исправление слов с ошибками во фрагментах речи, добавив API проверки орфографии Bing версии 7 в запросы конечной точки LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/27/2018
ms.author: diberry
ms.openlocfilehash: 19774d2a87e9c74f291f030aab09cb21fe4a931b
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525228"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Исправление орфографических ошибок с помощью проверки орфографии Bing

Вы можете интегрировать приложение LUIS с [API проверки орфографии Bing версии 7](https://azure.microsoft.com/services/cognitive-services/spell-check/) для проверки орфографических ошибок во фрагментах речи до создания прогнозов по оценкам и сущностям. 

## <a name="create-first-key-for-bing-spell-check-v7"></a>Создание первого ключа для API проверки орфографии Bing версии 7
[Первый ключ API проверки орфографии Bing версии 7](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) предоставляется бесплатно. 

![Создание бесплатного ключа](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name"create-subscription-key"></a>
## <a name="create-endpoint-key"></a>Создание ключа конечной точки
Если срок действия бесплатного ключа истек, создайте ключ конечной точки.

1. Войдите на [портал Azure](https://portal.azure.com). 

2. В верхнем левом углу щелкните **Создать ресурс**.

3. В поле поиска введите `Bing Spell Check API V7`.

    ![Поиск API проверки орфографии Bing версии 7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Выберите службу. 

5. Справа появится информационная панель, содержащая сведения, включая юридическое уведомление. Выберите **Создать**, чтобы начать процесс создания подписки. 

6. На следующей панели введите параметры службы. Дождитесь завершения процесса создания службы.

    ![Введите параметры службы](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Выберите **Все ресурсы** под заголовком **Избранное** на панели навигации слева.

8. Выберите новую службу. Ее тип — **Cognitive Services**, а расположение — **глобальная**. 

9. На главной панели выберите **Ключи** для просмотра новых ключей.

    ![Получение ключей](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Скопируйте первый ключ. Вам потребуется только один из двух ключей. 

## <a name="using-the-key-in-luis-test-panel"></a>Использование ключа на панели тестирования LUIS
В LUIS есть два места для использования ключа. Первое — [панель тестирования](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Ключ не сохраняется в LUIS, а является переменной сеанса. Ключ нужно будет задавать каждый раз, когда потребуется, чтобы панель тестирования применила службу API проверки орфографии Bing версии 7 к фрагменту речи. Сведения о настройке ключа см. в разделе [инструкций](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) панели тестирования.

## <a name="adding-the-key-to-the-endpoint-url"></a>Добавление ключа в URL-адрес конечной точки
Ключ, передаваемый в параметрах строки запроса, требуется включать в каждый запрос конечной точки, чтобы применить исправление орфографических ошибок. Можно настроить чат-бот, вызывающий LUIS, или вызывать API конечной точки LUIS напрямую. Независимо от того, как вызывается конечная точка, каждый вызов должен включать необходимые данные для правильной работы функции исправления ошибок.

URL-адрес конечной точки включает несколько значений, которые должны быть переданы правильно. Ключ API проверки орфографии Bing версии 7 — еще одно из этих значений. Необходимо задать для параметра **spellCheck** значение true, а для значения **bing-spell-check-subscription-key** — значение ключа:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Отправка фрагментов речи с орфографическими ошибками в LUIS
1. В веб-браузере скопируйте предыдущую строку и замените `region`, `appId`, `luisKey` и `bingKey` собственными значениями. Убедитесь, что правильно выбрали регион конечной точки, если он отличается от [региона](luis-reference-regions.md) публикации.

2. Добавьте неправильно написанный фрагмент речи, например "How far is the mountainn?". В английском языке правильное написание `mountain` — с одной буквой `n`. 

3. Нажмите клавишу ВВОД для отправки запроса в LUIS.

4. Служба LUIS передаст ответ с результатом JSON для `How far is the mountain?`. Если API проверки орфографии Bing версии 7 обнаружит орфографическую ошибку, в поле `query` ответа JSON приложения LUIS будет указан исходный запрос, а в поле `alteredQuery` — исправленный запрос, отправленный в LUIS.

```
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Пропуск орфографических ошибок
Если вы не хотите использовать службу API проверки орфографии Bing версии 7, можно пометить фрагменты речи с орфографическими ошибками, чтобы служба LUIS могла изучать правильное написание, а также опечатки. Этот способ требует больше усилий, чем применение проверки орфографии.

## <a name="publishing-page"></a>Страница публикации
На странице [публикации](luis-how-to-publish-app.md) есть флажок **Включить проверку орфографии Bing**. Это удобный способ создания ключа и получения представления об изменении URL-адреса конечной точки. Тем не менее все равно необходимо использовать правильные параметры конечной точки для исправления орфографических ошибок для каждого фрагмента речи. 

> [!div class="nextstepaction"]
> [Подробнее о примерах фрагментов речи](luis-how-to-add-example-utterances.md)
