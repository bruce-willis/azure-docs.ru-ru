---
title: включение файла
description: включение файла
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 8dbc90c3888a9c53db7d2ebeea2dd5f3ee5746a0
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939625"
---
## <a name="clean-up"></a>Очистка
Чтобы полностью удалить экземпляр Azure Dev Spaces из кластера, включая все среды разработки и работающие службы, используйте команду `az aks remove-dev-spaces`. Не забывайте, что это действие необратимо. Позднее вы сможете снова добавить в кластер поддержку Azure Dev Spaces, но в этом случае вам придется начать работу с нуля. Прежние службы и среды не будут восстановлены.

С помощью приведенного ниже примера кода можно вывести список контроллеров Azure Dev Spaces в активной подписке, а затем удалить контроллер Azure Dev Spaces, связанный с кластером AKS myaks в группе ресурсов myaks-rg.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```

