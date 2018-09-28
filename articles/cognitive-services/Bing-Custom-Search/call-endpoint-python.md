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
ms.openlocfilehash: 3666e92372e9bed80e5c0c7991dcac730cebb588
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967598"
---
# <a name="call-bing-custom-search-endpoint-python"></a>Конечная точка службы пользовательского поиска Bing (Python)

В этом кратком руководстве показано, как запрашивать результаты поиска из экземпляра службы пользовательского поиска с помощью Python для вызова конечной точки службы пользовательского поиска Bing. 

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

- Готовый экземпляр службы пользовательского поиска. Ознакомьтесь с разделом [Create your first Bing Custom Search instance](quick-start.md) (Создание первого экземпляра службы "Пользовательский поиск Bing").
- Установленный компонент [Python](https://www.python.org/).
- ключ подписки; Ключ подписки можно получить при активации [бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search). Кроме того, вы можете использовать ключ платной подписки из панели мониторинга Azure (см. раздел об [учетной записи API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    


## <a name="run-the-code"></a>Выполнение кода

Чтобы запустить этот пример, выполните следующее.

1. Создайте папку для своего кода.  
  
2. Из командной строки или терминала с правами администратора перейдите в эту папку.  
  
3. Установите модуль Python **requests**.  
  
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>  
      
4. Создайте файл с именем BingCustomSearch.py в созданной папке и скопируйте в него следующий код. Замените **YOUR-SUBSCRIPTION-KEY** и **YOUR-CUSTOM-CONFIG-ID** своими ключом подписки и идентификатором конфигурации.  
  
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
  
7. Выполните код с помощью приведенной ниже команды.  
  
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>Дополнительная информация
- [Настройка размещенного пользовательского интерфейса](./hosted-ui.md)
- [Use decoration markers to highlight text](./hit-highlighting.md) (Использование маркеров оформления для выделения текста)
- [Разбивка веб-страниц на страницы](./page-webpages.md)
