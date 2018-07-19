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
ms.openlocfilehash: 44ce986fcfdf079d3077c007a378f3467073d00d
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991037"
---
### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Настройка кластера AKS для использования Azure Dev Spaces

Откройте окно командной строки и введите приведенную ниже команду Azure CLI, используя группу ресурсов, в которую входит кластер AKS, и имя кластера AKS. Эта команда настраивает в кластере поддержку Azure Dev Spaces.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

