---
title: Фильтрация подключений по протоколу IP для Центров событий Azure | Документация Майкрософт
description: Использование фильтрации IP-адресов для блокирования подключений к Центрам событий Azure с определенных IP-адресов.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 08/26/2018
ms.author: shvija
ms.openlocfilehash: 6d96eac3ecd249de3ba0da82eff95c45e45fa02d
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746201"
---
# <a name="use-ip-filters"></a>Использование фильтрации IP-адресов

Для сценариев, в которых доступ к Центрам событий Azure требуется только из определенных известных расположений, функция *фильтрации IP-адресов* позволяет настроить правила отклонения или разрешения трафика на основе IPv4-адреса отправителя. Например, это могут быть адреса корпоративного шлюза NAT.

## <a name="when-to-use"></a>Сценарии использования

Возможность блокирования конечных точек Центров событий для определенных IP-адресов является важной в двух случаях:

- Концентратор событий должен принимать трафик только из определенного диапазона IP-адресов и отклонять весь остальной трафик. Допустим, Центры событий используются с [Azure Express Route][express-route] для создания частных подключений к локальной инфраструктуре. 
- В этом случае требуется отклонять трафик с IP-адресов, которые администратор Центров событий определил как подозрительные.

## <a name="how-filter-rules-are-applied"></a>Применение правил фильтрации

Правила фильтрации IP-адресов применяются на уровне пространства имен Центров событий. Поэтому они действуют для всех клиентских подключений по любым поддерживаемым протоколам.

Любые попытки подключения с IP-адреса, который соответствует правилам блокирования для пространства имен Центров событий, отклоняются. В ответе клиенту правило фильтрации IP-адресов не упоминается.

## <a name="default-setting"></a>Значение по умолчанию

По умолчанию раздел **Фильтрация IP-адресов** на портале для Центров событий пуст. Этот означает, что по умолчанию концентратор событий принимает подключения с любых IP-адресов. То есть, такое значение параметра по умолчанию равноценно правилу, которое принимает диапазон IP-адресов 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Оценка правила фильтрации IP-адресов

Правила фильтрации IP-адресов применяются по порядку, поэтому первое правило, которое соответствует IP-адресу, определяет действие (принять или отклонить).

Например, чтобы адреса в диапазоне 70.37.104.0/24 принимались, а все остальные отклонялись, первым правилом в списке должно быть именно правило о приеме трафика из диапазона адресов 70.37.104.0/24. Следующим должно быть правило об отклонении всех адресов; для этого используется диапазон 0.0.0.0/0.

> [!NOTE]
> Отклонение IP-адресов может препятствовать взаимодействию других служб Azure (таких как Azure Stream Analytics, Виртуальные машины Azure или Device Explorer на портале) с Центрами событий.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Создание правила виртуальной сети с использованием шаблонов Azure Resource Manager

Следующий шаблон Resource Manager позволяет добавить правило виртуальной сети в существующее пространство имен Центров событий.

Параметры шаблона:

- Значение **ipFilterRuleName** должно быть уникальной строкой буквенно-цифровых символов длиной не более 128 символов без учета регистра.
- Параметр **ipFilterAction** поддерживает значения **Reject** (Отклонить) и **Accept** (Подтвердить), которые определяют действие, применяемое этим правилом фильтрации IP-адресов.
- Зачение **ipMask** — это один IPv4-адрес или блок IP-адресов в нотации CIDR. Например, значение 70.37.104.0/24 в нотации CIDR представляет 256 IPv4-адресов в диапазоне от 70.37.104.0 до 70.37.104.255. Число 24 обозначает количество значимых битов префикса для адресов этого диапазона.

```json
{  
   "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{     
          "namespaceName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the namespace"
             }
          },
          "ipFilterRuleName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the Authorization rule"
             }
          },
          "ipFilterAction":{  
             "type":"string",
             "allowedValues": ["Reject", "Accept"],
             "metadata":{  
                "description":"IP Filter Action"
             }
          },
          "IpMask":{  
             "type":"string",
             "metadata":{  
                "description":"IP Mask"
             }
          }
      },
    "resources": [
        {
            "apiVersion": "2018-01-01-preview",
            "name": "[concat(parameters('namespaceName'), '/', parameters('ipFilterRuleName'))]",
            "type": "Microsoft.EventHub/Namespaces/IPFilterRules",
            "properties": {
                "FilterName":"[parameters('ipFilterRuleName')]",
                "Action":"[parameters('ipFilterAction')]",              
                "IpMask": "[parameters('IpMask')]"
            }
        } 
    ]
}
```

Инструкции по развертыванию шаблона см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell][lnk-deploy].

## <a name="next-steps"></a>Дополнительная информация

Инструкции по ограничению доступа к Центрам событий из виртуальных сетей Azure, см. по следующей ссылке:

- [Конечные точки служб виртуальной сети для Центров событий][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md