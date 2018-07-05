---
title: Создание кластера службы Azure Kubernetes (AKS)
description: Созадние кластера AKS с помощью интерфейса командной строки или портала Azure.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 684bf44c6c3c67ce1d5c798f3406270d76a8e2fa
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029238"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>Создание кластера службы Azure Kubernetes (AKS)

Кластер Службы Azure Kubernetes (AKS) можно создать с помощью портала Azure или Azure CLI.

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Используйте команду [az aks create][az-aks-create], чтобы создать кластер AKS.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

Для команды `az aks create` доступны следующие параметры. Дополнительные сведения о каждом из этих аргументов см. в [справочнике по Azure CLI][az-aks-create] для AKS.

| Аргумент | ОПИСАНИЕ | Обязательно |
|---|---|:---:|
| `--name` `-n` | Имя ресурса для управляемого кластера. | Да |
| `--resource-group` `-g` | Имя группы ресурсов Службы Azure Kubernetes. | Да |
| `--admin-username` `-u` | Имя пользователя для службы "Виртуальные машины Linux".  По умолчанию: azureuser. | Нет |
| `--aad-client-app-id` | (Предварительная версия) Идентификатор клиентского приложения Azure Active Directory типа "Машинный код". | Нет |
| `--aad-server-app-id` | (Предварительная версия) Идентификатор серверного приложения Azure Active Directory типа "Веб-приложение или API". | Нет |
| `--aad-server-app-secret` | (Предварительная версия) Секрет серверного приложения Azure Active Directory. | Нет |
| `--aad-tenant-id` | (Предварительная версия) Идентификатор клиента Azure Active Directory. | Нет |
| `--admin-username` `-u` | Учетная запись пользователя для создания на узле виртуальных машин для доступа по протоколу SSH.  По умолчанию: azureuser. | Нет |
| ` --client-secret` | Секрет, связанный с субъектом-службой. | Нет |
| `--dns-name-prefix` `-p` | Префикс DNS для общедоступного IP-адреса кластера. | Нет |
| `--dns-service-ip` | IP-адрес, назначенный службе DNS Kubernetes. | Нет |
| `--docker-bridge-address` | IP-адрес и маска сети, назначенные мосту Docker. | Нет |
| `--enable-addons` `-a` | Включение надстроек Kubernetes в виде списка с разделителями-запятыми. | Нет |
| `--enable-rbac` `-r` | Включение управления доступом на основе ролей Kubernetes. | Нет |
| `--generate-ssh-keys` | Создание файлов открытого и закрытого ключей SSH, если они отсутствуют. | Нет |
| `--kubernetes-version` `-k` | Версия Kubernetes, используемая для создания кластера, например 1.7.9 или 1.9.6. | Нет |
| `--locaton` `-l` | Расположение автоматически созданной группы ресурсов. | Нет |
| `--max-pods` `-m` | Максимальное число контейнеров pod, развертываемых на узле. | Нет |
| `--network-plugin` | Используемый подключаемый модуль сети Kubernetes. | Нет |
| `--no-ssh-key` `-x` | Запрет использования или создания локального ключа SSH. | Нет |
| `--no-wait` | Не ожидать завершения длительной операции. | Нет |
| `--node-count` `-c` | Количество узлов по умолчанию в пулах узлов.  По умолчанию: 3. | Нет |
| `--node-osdisk-size` | Размер диска ОС (в ГБ) виртуальной машины пула узлов. | Нет |
| `--node-vm-size` `-s` | Размер виртуальной машины.  По умолчанию: Standard_D1_v2. | Нет |
| `--pod-cidr` | Диапазон IP-адресов нотации CIDR, из которого назначаются IP-адреса pod при использовании kubenet. | Нет |
| `--service-cidr` | Диапазон IP-адресов нотации CIDR, из которого назначаются IP-адреса кластера службы. | Нет |
| `--service-principal` | Субъект-служба, используемый для аутентификации кластера. | Нет |
| `--ssh-key-value` | Значение файла ключа SSH или путь к файлу ключа.  По умолчанию: ~ /.ssh/id_rsa.pub. | Нет |
| `--tags` | Разделенные пробелами теги в формате "key[=value]". Используйте '' для очистки существующих тегов. | Нет |
| `--vnet-subnet-id` | Идентификатор подсети в существующей виртуальной сети, где требуется развернуть кластер. | Нет |
| `--workspace-resource-id` | Идентификатор ресурса существующей рабочей области Log Analytics, используемый для хранения данных мониторинга. | Нет |

## <a name="azure-portal"></a>Портал Azure

См. дополнительные сведения о [развертывании кластера Службе Azure Kubernetes (AKS) с помощью портала Azure][aks-portal-quickstart].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
