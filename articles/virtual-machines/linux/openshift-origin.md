---
title: Развертывание OKD в Azure | Документация Майкрософт
description: Развертывание OKD в Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: 75a02e61adf3e5477b9945afc778e867d5d9c88c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958141"
---
# <a name="deploy-okd-in-azure"></a>Развертывание OKD в Azure

Существует два способа развертывания OKD (ранее OpenShift Origin) в Azure.

- Вы можете вручную развернуть все необходимые компоненты инфраструктуры Azure, а затем следовать инструкциям из [документации](https://docs.okd.io/3.10/welcome/index.html) по источнику OKD.
- Вы можете воспользоваться [шаблоном Resource Manager](https://github.com/Microsoft/openshift-origin), который упрощает развертывание кластера OKD.

## <a name="deploy-by-using-the-okd-template"></a>Развертывание с помощью шаблона OKD

Используйте для параметра `aadClientId` значение `appId` из созданного ранее субъекта-службы.

В примере ниже создается файл параметров с именем azuredeploy.parameters.json, который содержит все необходимые входные данные.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

### <a name="deploy-by-using-azure-cli"></a>Развертывание с помощью Azure CLI


> [!NOTE] 
> Для выполнения следующей команды необходим Azure CLI.8 или более поздней версии. Узнать свою версию CLI можно с помощью команды `az --version`. Чтобы обновить версию CLI, ознакомьтесь со статьей [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

В следующем примере кластер OKD и все связанные ресурсы развертываются в группу ресурсов myResourceGroup с именем развертывания myOpenShiftCluster. Шаблон хранится в репозитории GitHub, и ссылка на него указывается в локальном файле параметров с именем azuredeploy.parameters.json.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Развертывание длится не менее 25 минут в зависимости от общего количества развертываемых узлов. После завершения развертывания в терминале отобразятся URL-адрес консоли OKD и DNS-имя главного узла OpenShift.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="connect-to-the-okd-cluster"></a>Подключение к кластеру OKD

После завершения развертывания подключитесь к консоли OKD через браузер, используя `OpenShift Console Uri`. Кроме того, к главному узлу OKD можно подключиться с помощью следующей команды:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если группа ресурсов, кластер OpenShift и все связанные ресурсы больше не нужны, их можно удалить с помощью команды [az group delete](/cli/azure/group#az_group_delete).

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

- [Задачи, выполняемые после развертывания](./openshift-post-deployment.md)
- [Устранение неполадок с развертыванием OpenShift](./openshift-troubleshooting.md)
- [Начало работы с OKD](https://docs.okd.io/latest/getting_started/index.html)
