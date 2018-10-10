---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e3961878663a26d721478effc01d968e21e34c18
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47020801"
---
Войдите в свою подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране. Дополнительные сведения о входе в систему см. в статье [Начало работы с Azure CLI](/cli/azure/get-started-with-azure-cli).

```azurecli
az login
```

Если у вас есть несколько подписок Azure, укажите подписки для этой учетной записи.

```azurecli
az account list --all
```

Укажите подписку, которую нужно использовать.

```azurecli
az account set --subscription <replace_with_your_subscription_id>
```
