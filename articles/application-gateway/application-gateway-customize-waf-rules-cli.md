---
title: Настройка правил брандмауэра веб-приложения для шлюза приложений Azure с помощью Azure CLI | Документы Майкрософт
description: Эта статья содержит сведения о том, как настроить правила брандмауэра веб-приложения в шлюзе приложений с помощью Azure CLI.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: victorh
ms.openlocfilehash: c02e4edabdcb73bc14c64b42788cddc98d78498c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964127"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-cli"></a>Настройка правил брандмауэра веб-приложения с помощью Azure CLI

> [!div class="op_single_selector"]
> * [портала Azure](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [интерфейс командной строки Azure](application-gateway-customize-waf-rules-cli.md)

Брандмауэр веб-приложения (WAF) шлюза приложений Azure обеспечивает защиту веб-приложений с помощью основного набора правил (CRS) открытого проекта безопасности веб-приложений (OWASP). Некоторые правила могут приводить к ложным срабатываниям и блокировке реального трафика. Поэтому шлюз приложений предоставляет возможность настроить правила и группы правил. Дополнительные сведения о конкретных правилах и группах правил см. в статье [Список групп правил и правил CRS брандмауэра веб-приложения](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Просмотр правил и групп правил

Ниже приведены примеры кода для просмотра правил и групп правил, которые можно настроить.

### <a name="view-rule-groups"></a>Просмотр групп правил

В следующем примере показано, как просмотреть группы правил:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

В результатах далее представлен сокращенный ответ из предыдущего примера.

```
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>Просмотр правил в группе правил

В примере ниже показано, как просмотреть правила в указанной группе правил.

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

В результатах далее представлен сокращенный ответ из предыдущего примера.

```
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>Отключение правил

В следующем примере отключаются правила `910018` и `910017` в шлюзе приложений.

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="next-steps"></a>Дополнительная информация

После настройки с отключением правил вы можете узнать, как просматривать журналы WAF. Дополнительные сведения см. в разделе [Журналы диагностики](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
