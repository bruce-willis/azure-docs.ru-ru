---
title: Удаление кластера службы Azure Kubernetes (AKS)
description: Удаление кластера AKS с помощью интерфейса командной строки или портала Azure.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: c8eab17a5c635560d9a5274eb038845238968e02
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439941"
---
# <a name="delete-an-azure-kubernetes-service-aks-cluster"></a>Удаление кластера службы Azure Kubernetes (AKS)

При удалении кластера Службы Azure Kubernetes группа ресурсов, в которой развернут этот кластер, остается, но удаляются все относящиеся к AKS ресурсы. В этом документе показано, как удалить кластер AKS с помощью портала Azure и Azure CLI.

Помимо кластера можно удалить группу ресурсов, в которой он был развернут. При этом также удаляется и кластер AKS.

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Используйте команду [az aks delete][az-aks-delete], чтобы удалить кластер AKS.

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

Для команды `az aks delete` доступны следующие параметры.

| Аргумент | ОПИСАНИЕ | Обязательно |
|---|---|:---:|
| `--name` `-n` | Имя ресурса для управляемого кластера. | Да |
| `--resource-group` `-g` | Имя группы ресурсов Службы Azure Kubernetes. | Да |
| `--no-wait` | Не ожидать завершения длительной операции. | Нет |
| `--yes` `-y` | Не запрашивать подтверждение. | Нет |

## <a name="azure-portal"></a>Портал Azure

На портале Azure перейдите к группе ресурсов, содержащей ресурс Службы Azure Kubernetes (AKS), выберите этот ресурс и щелкните **Удалить**. Отобразится запрос на подтверждение операции удаления.

![Удаление кластера AKS с помощью портала](media/container-service-delete-cluster/delete-aks-portal.png)

<!-- LINKS - internal -->
[az-aks-delete]: /cli/azure/aks?view=azure-cli-latest#az-aks-delete