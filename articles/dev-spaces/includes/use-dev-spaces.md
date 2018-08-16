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
ms.openlocfilehash: a8f5adc8e6745fdbfd31d1a9cb9407672d61da07
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129365"
---
### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Настройка кластера AKS для использования Azure Dev Spaces

Откройте окно командной строки и введите приведенную ниже команду Azure CLI, используя группу ресурсов, в которую входит кластер AKS, и имя кластера AKS. Эта команда настраивает в кластере поддержку Azure Dev Spaces.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

