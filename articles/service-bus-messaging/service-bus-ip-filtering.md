---
title: Фильтрация подключений по протоколу IP в Служебной шине Azure | Документация Майкрософт
description: Сведения об использовании фильтрации IP-адресов для блокирования подключений к Служебной шине Azure с определенных IP-адресов.
services: service-bus
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: clemensv
ms.openlocfilehash: ccb759a9151d734aa99a6f6b9c68e6072874dd84
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394834"
---
# <a name="use-ip-filters"></a>Использование фильтрации IP-адресов

Для сценариев, в которых доступ к служебной шине Azure требуется только из определенных известных расположений, функция *фильтрации IP-адресов* позволяет настроить правила отклонения или разрешения трафика на основе IPv4-адреса отправителя. Например, это могут быть адреса корпоративного шлюза NAT.

## <a name="when-to-use"></a>Сценарии использования

Возможность блокирования конечных точек Служебной шины для определенных IP-адресов полезна в двух случаях:

- Служебная шина должна принимать трафик только из определенного диапазона IP-адресов и отклонять остальной трафик. Допустим, Служебная шина используется вместе с [Azure Express Route][express-route] для создания частных подключений к локальной инфраструктуре.
- В этом случае требуется отклонять трафик с IP-адресов, которые были определены администратором Служебной шины как подозрительные.

## <a name="how-filter-rules-are-applied"></a>Применение правил фильтрации

Правила фильтрации IP-адресов применяются на уровне пространства имен Служебной шины. Поэтому они действуют для всех клиентских подключений по любым поддерживаемым протоколам.

Любые попытки подключения с IP-адреса, который соответствует правилам блокирования для пространства имен Служебной шины, отклоняются. В ответе клиенту правило фильтрации IP-адресов не упоминается.

## <a name="default-setting"></a>Значение по умолчанию

По умолчанию раздел **Фильтрация IP-адресов** на портале для Служебной шины пуст. Этот означает, что по умолчанию пространство имен принимает подключения с любых IP-адресов. То есть, такое значение параметра по умолчанию равноценно правилу, которое принимает диапазон IP-адресов 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Оценка правила фильтрации IP-адресов

Правила фильтрации IP-адресов применяются по порядку, поэтому первое правило, которое соответствует IP-адресу, определяет действие (принять или отклонить).

Например, чтобы адреса в диапазоне 70.37.104.0/24 принимались, а все остальные отклонялись, первым правилом в списке должно быть именно правило о приеме трафика из диапазона адресов 70.37.104.0/24. Следующим должно быть правило об отклонении всех адресов; для этого используется диапазон 0.0.0.0/0.

> [!NOTE]
> Отклонение IP-адресов может препятствовать взаимодействию других служб Azure (таких как Azure Stream Analytics, Виртуальные машины Azure или Device Explorer на портале) со Служебной шиной.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Создание правила виртуальной сети с использованием шаблонов Azure Resource Manager

Следующий шаблон Resource Manager позволяет добавить правило виртуальной сети в пространство имен существующей Служебной шины.

Параметры шаблона.

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
            "type": "Microsoft.ServiceBus/Namespaces/IPFilterRules",
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

Инструкции по ограничению доступа к Служебной шине из виртуальных сетей Azure, см. по следующей ссылке:

- [Use Virtual Network service endpoints with Azure Service Bus][lnk-vnet] (Использование конечных точек виртуальной сети для Служебной шины Azure)

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services