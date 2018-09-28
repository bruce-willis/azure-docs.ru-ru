---
title: Отправка пользовательских метрик для ресурса Azure в хранилище метрик Azure Monitor с использованием REST API
description: Отправка пользовательских метрик для ресурса Azure в хранилище метрик Azure Monitor с использованием REST API
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: c01440437eae3cb076627ab9f2221e33b833f472
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977242"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-using-a-rest-api"></a>Отправка пользовательских метрик для ресурса Azure в хранилище метрик Azure Monitor с использованием REST API

В этой статье показано, как отправлять пользовательские метрики для ресурсов Azure в хранилище метрик Azure Monitor с помощью REST API.  Когда метрики находятся в Azure Monitor, вы можете делать с ними все, что вы делаете со стандартными метриками, например: составление диаграмм, оповещений, маршрутизация их на другие внешние инструменты и т. д.  

>[!NOTE] 
>REST API позволяет отправлять пользовательские метрики только для ресурсов Azure. Чтобы отправлять метрики для ресурсов в разных средах или локально, можно использовать [Application Insights](../application-insights/app-insights-api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Создание и авторизация субъекта-службы для генерации метрик 

Создайте субъект-службу в клиенте Azure Active Directory, используя инструкции из раздела [Создание субъекта-службы](../azure-resource-manager/resource-group-create-service-principal-portal.md). 

Выполняя это действие, обратите внимание на следующее. 

- Вы можете указать любой URL-адрес для входа в систему.  
- Создание нового секрета клиента для этого приложения  
- Сохраните ключ и идентификатор клиента для использования в последующих шагах.  

Предоставьте приложению, созданному на шаге 1, доступ издателя метрик мониторинга к ресурсу, из которого вы хотите генерировать метрики. Если вы планируете использовать приложение для генерации пользовательских метрик ко многим ресурсам, можно предоставить эти разрешения группе ресурсов или уровню подписки. 

## <a name="get-an-authorization-token"></a>Получение маркера авторизации
Откройте командную строку и выполните следующую команду
```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step> " -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Сохраните маркер доступа из ответа

![Маркер доступа](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Генерация метрики через REST API 

1. Вставьте приведенный ниже код JSON в файл и сохраните его как custommetric.json на локальном компьютере. Обновите параметр времени в JSON-файле. 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20”, 
        "data": { 
            "baseData": { 
                "metric": "QueueDepth", 
                "namespace": "QueueProcessing", 
                "dimNames": [ 
                  "QueueName", 
                  "MessageType" 
                ], 
                "series": [ 
                  { 
                    "dimValues": [ 
                      "ImagesToProcess", 
                      "JPEG" 
                    ], 
                    "min": 3, 
                    "max": 20, 
                    "sum": 28, 
                    "count": 3 
                  } 
                ] 
            } 
        } 
    } 
    ``` 

1. В окне командной строки опубликуйте данные метрики 
    - Регион Azure должен соответствовать региону развертывания ресурса, для которого генерируется метрика. 
    - ResourceID — это идентификатор ресурса Azure, по которому вы отслеживаете метрику.  
    - Маркер доступа. Вставьте ранее приобретенный маркер

    ```Shell 
    curl -X POST curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId> /metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
    ```
1. Измените метку времени и значения в JSON-файле. 
1. Повторите предыдущие два шага несколько раз, чтобы у вас были данные в течение нескольких минут.

## <a name="troubleshooting"></a>Устранение неполадок 
Если вы получаете сообщение об ошибке с некоторой части процесса, учитывайте следующее.

1. Вы не можете издавать метрики по подписке или группе ресурсов как ресурс Azure. 
1. Вы не можете поместить метрику, которая старше 20 минут, в хранилище. Хранилище метрик оптимизировано для оповещений и создания диаграмм в режиме реального времени. 
2. Число имен измерений должно соответствовать значениям и наоборот. Проверьте значения. 
2. Вы можете генерировать метрики по регионам, которые не поддерживают пользовательские метрики. Дополнительные сведения см. в разделе [о поддерживаемых регионах пользовательских метрик](metrics-custom-overview.md#supported-regions) 



## <a name="view-your-metrics"></a>Просмотр метрик 

1. Войдите на портал Azure. 

1. В меню слева щелкните **Монитор**. 

1. На странице "Монитор" щелкните **Метрики**. 

   ![Маркер доступа](./media/metrics-store-custom-rest-api/metrics.png) 

1. Измените период агрегирования на **Последние 30 минут**.  

1. В раскрывающемся списке *ресурса* выберите тот, по которому вы генерировали метрику.  

1. В раскрывающемся списке *пространства имен* выберите **QueueProcessing** 

1. В раскрывающемся списке *метрик* выберите **QueueDepth**.  

 
## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения о настраиваемых метриках см. в [этой статье](metrics-custom-overview.md).