---
title: Создание Центра Интернета вещей с помощью Azure CLI | Документация Майкрософт
description: Как создать Центр Интернета вещей с помощью Azure CLI.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: 95741b1a00c47468c7189e0103608c1dd7fa1d90
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046176"
---
# <a name="create-an-iot-hub-using-azure-cli"></a>Создание Центра Интернета вещей с помощью Azure CLI

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

В этой статье показано, как создать Центр Интернета вещей с помощью Azure CLI.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Выполнение входа и установка учетной записи Azure

Если вы запускаете Azure CLI локально вместо того, чтобы использовать Cloud Shell, необходимо войти в учетную запись Azure.

В командной строке запустите [команду login](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli):

    ```azurecli
    az login
    ```

Следуйте инструкциям, чтобы выполнить аутентификацию с использованием кода и войти в учетную запись Azure через веб-браузер.

## <a name="create-an-iot-hub"></a>Создание центра Интернета вещей

Создайте группу ресурсов и добавьте Центр Интернета вещей с помощью Azure CLI.

1. Создайте Центр Интернета вещей в группе ресурсов. Используйте имеющуюся группу ресурсов либо выполните следующую [команду для создания группы ресурсов](https://docs.microsoft.com/cli/azure/resource):
    
   ```azurecli
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > В предыдущем примере создается группа ресурсов в расположении западной части США. Список доступных расположений можно просмотреть, выполнив эту команду: 
   >
   >``` bash
   >az account list-locations -o table
   >```
   >

2. Выполните в своей группе ресурсов следующую [команду для создания Центра Интернета вещей](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create), указав глобально уникальное имя своего Центра Интернета вещей:
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Предыдущая команда создает Центр Интернета вещей в оплачиваемой ценовой категории S1. Дополнительные сведения см. на странице с [ценами на Центр Интернета вещей Azure](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Удаление Центра Интернета вещей

Вы можете использовать Azure CLI, чтобы [удалить отдельный ресурс](https://docs.microsoft.com/cli/azure/resource), например Центр Интернета вещей, или группу ресурсов со всеми ресурсами, включая любые Центры Интернета вещей.

Чтобы [удалить Центр Интернета](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete) вещей, выполните следующую команду:

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Чтобы [удалить группу ресурсов](https://docs.microsoft.com/cli/azure/group#az-group-delete) со всеми ресурсами, выполните следующую команду:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об использовании Центра Интернета вещей см. в следующих статьях:

* [Руководство разработчика для Центра Интернета вещей](iot-hub-devguide.md)
* [Создание Центра Интернета вещей с помощью портала Azure](iot-hub-create-through-portal.md)
