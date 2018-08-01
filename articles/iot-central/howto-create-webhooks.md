---
title: Создание веб-перехватчиков на правилах Azure IoT Central | Документация Майкрософт
description: Создание веб-перехватчиков в Azure IoT Central для автоматического уведомления других приложений о срабатывании правил.
author: viv-liu
ms.author: viviali
ms.date: 07/17/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 1e21076cafe21e6c0efcdf5a8146278eabd9ebc4
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39227836"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Создание действий веб-перехватчика на правилах Azure IoT Central

Веб-перехватчики позволяют подключить приложение IoT Central к другим приложениям и службам для удаленного мониторинга и уведомления. Веб-перехватчики автоматически уведомляют другие приложения и службы, к которым вы подключаетесь, при каждой активации правила в приложении IoT Central. Приложение IoT Central отправляет запрос POST к конечной точке HTTP другого приложения при каждой активации правила. Полезные данные будут содержать сведения об устройстве и об активации правила. 

## <a name="how-to-set-up-the-webhook"></a>Настройка веб-перехватчика
В этом примере необходимо подключиться к RequestBin, чтобы с помощью веб-перехватчиков получать уведомления при срабатывании правил. 

1. Откройте [RequestBin](http://requestbin.net/). 
1. Создайте новый RequestBin и скопируйте **Bin URL** (URL-адрес Bin). 
1. Создайте [Правило телеметрии](howto-create-telemetry-rules.md) или [Правило события](howto-create-event-rules.md). Сохраните правило и добавьте новое действие.
![Экран создания веб-перехватчика](media/howto-create-webhooks/webhookcreate.png)
1. Выберите действие веб-перехватчика, укажите отображаемое имя и вставьте URL-адрес Bin как URL-адрес обратного вызова. 
1. Сохранение правила

Теперь при срабатывании правила в RequestBin должен отобразиться новый запрос.

## <a name="payload"></a>Полезные данные
При запуске правила HTTP-запрос POST обрабатывается URL-адресом обратного вызова, содержащим полезные данные json со сведениями измерений, устройства, правила и приложения. Для правила телеметрии полезные данные могут выглядеть следующим образом.

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
        "deviceTemplate":{
            "id": "ID",
            "version":"1.0.0"
        },
        "properties":{
            "device":{
                "firmwareversion":"1.0"
            },
            "cloud":{
                "location":"One Microsoft Way"
            }
        },
        "measurements":{
            "telemetry":{
                "temperature":20,
                "pressure":10
            }
        }

    },
    "rule": {
        "id": "ID",
        "name": "High temperature alert",
        "enabled": true,
        "deviceTemplate": {
            "id":"GUID",
            "version":"1.0.0"
        }
    },
    "application": {
        "id": "ID",
        "name": "Contoso app",
        "subdomain":"contoso-app"
    }
}
```

## <a name="known-limitations"></a>Известные ограничения
В настоящее время нет программного метода подписки или отмены подписки на эти веб-перехватчики через API.

Если имеются идеи по улучшению этой функции, опубликуйте свои предложения на [форуме Uservoice](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Дополнительная информация
Теперь, когда вы узнали, как настроить и использовать веб-перехватчики, ознакомьтесь с [созданием рабочих процессов в Microsoft Flow](howto-add-microsoft-flow.md).
