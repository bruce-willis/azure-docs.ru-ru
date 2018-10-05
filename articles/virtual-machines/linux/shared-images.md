---
title: Создание общих образов виртуальных машин с помощью Azure CLI | Документация Майкрософт
description: В этой статье вы узнаете, как с помощью Azure CLI создать общий образ виртуальной машины Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: axayjo
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/19/2018
ms.author: akjosh; cynthn
ms.custom: ''
ms.openlocfilehash: 2f8ddae05b97b3fa6f1d3f65681defdd4e5a38b7
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047058"
---
# <a name="preview-create-a-shared-image-gallery-with-the-azure-cli"></a>Предварительная версия. Создание коллекции общих образов с помощью Azure CLI

Коллекция общих образов значительно упрощает обмен пользовательскими образами в вашей организации. Пользовательские образы похожи на образы магазина, однако их можно создавать самостоятельно. Пользовательские образы можно использовать для начальной загрузки конфигураций, например при предварительной загрузке приложений, конфигураций приложений и других конфигураций операционной системы. Она позволяет предоставлять общий доступ к пользовательским образам виртуальной машины другим пользователям вашей организации в пределах региона или между регионами в клиенте AAD. Выберите образы, к которым нужно предоставить общий доступ, регионы, где они будут доступны, и пользователей, которым будет доступно совместное использование. Вы можете создать несколько коллекций, чтобы можно было логически группировать общие образы. Коллекция — это ресурс верхнего уровня, который обеспечивает полное управление доступом на основе ролей (RBAC). Образы могут быть версионными, и вы можете копировать каждую версию образа в другой набор регионов Azure. Коллекция работает только с Управляемыми образами.

В рамках этой статьи вы создадите собственную коллекцию и пользовательские образы виртуальной машины Azure. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание коллекции общих образов
> * создание определения общего образа;
> * создание версии общего образа;
> * создание виртуальной машины из общего образа;
> * удаление ресурсов.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0.46 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-common-shared-images-cli](../../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-vm"></a>Создание виртуальной машины

Создайте виртуальную машину из версии образа, используя [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive 
az vm create\
   -g myGalleryRG \
   -n myVM \
   --image "/subscriptions/<subscription-ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0" \
   --generate-ssh-keys
```

[!INCLUDE [virtual-machines-common-gallery-list-cli](../../../includes/virtual-machines-common-gallery-list-cli.md)]



## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о коллекциях общих образов см. в [обзорной статье](shared-image-galleries.md).
