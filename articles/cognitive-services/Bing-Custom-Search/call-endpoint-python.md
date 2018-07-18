---
title: Вызов конечной точки службы пользовательского поиска Bing с помощью Python и Microsoft Cognitive Services
description: В этом кратком руководстве показано, как для запрашивать результаты поиска из экземпляра службы пользовательского поиска с помощью Python для вызова конечной точки службы пользовательского поиска Bing.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 889762ae0b401438f25546738268c584ddd58389
ms.sourcegitcommit: 15bfce02b334b67aedd634fa864efb4849fc5ee2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2018
ms.locfileid: "35382957"
---
# <a name="call-bing-custom-search-endpoint-python"></a>Конечная точка службы пользовательского поиска Bing (Python)

В этом кратком руководстве показано, как для запрашивать результаты поиска из экземпляра службы пользовательского поиска с помощью Python для вызова конечной точки службы пользовательского поиска Bing. 

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим кратким руководством вам понадобится:

- Экземпляр службы пользовательского поиска. Ознакомьтесь с разделом [Create your first Bing Custom Search instance](quick-start.md) (Создание первого экземпляра службы пользовательского поиска Bing).

-  Установленный компонент [Python](https://www.python.org/).

- [Учетная запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с **API-интерфейсами поиска Bing**. Для этого краткого руководства достаточно иметь [бесплатную пробную версию](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search). Потребуется ключ доступа, предоставляемый при активации бесплатной пробной версии. Можно также использовать ключ платной подписки, указанный на панели мониторинга Azure. 

## <a name="run-the-code"></a>Выполнение кода

Чтобы вызвать конечную точку службы пользовательского поиска Bing, выполните следующие действия.

1. Создайте каталог для своего кода.
2. Из командной строки или терминала с правами администратора перейдите в эту папку.
3. Установите модуль Python **requests**.
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>
7. Создайте файл BingCustomSearch.py и скопируйте в него приведенный ниже код.
8. Замените **YOUR-SUBSCRIPTION-KEY** и **YOUR-CUSTOM-CONFIG-ID** своими ключом и идентификатором конфигурации (см. шаг 1).

    ``` Python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=' + searchTerm + '&customconfig=' + customConfigId
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```
9. Выполните код с помощью приведенной ниже команды.
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>Дополнительная информация
- [Настройка размещенного пользовательского интерфейса](./hosted-ui.md)
- [Use decoration markers to highlight text](./hit-highlighting.md) (Использование маркеров оформления для выделения текста)
- [Разбивка веб-страниц на страницы](./page-webpages.md)