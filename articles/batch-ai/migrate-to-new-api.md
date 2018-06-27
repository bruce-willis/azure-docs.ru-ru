---
title: Переход на обновленный API Azure Batch AI | Документация Майкрософт
description: Узнайте, как обновить скрипты и код Azure Batch AI для использования таких новых ресурсов, как рабочие области и эксперименты.
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.openlocfilehash: c5e4c1569464d2e204edf13fe7534d80780524e8
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294966"
---
# <a name="migrate-to-the-updated-batch-ai-api"></a>Переход на обновленный API Batch AI

В REST API службы Batch AI версии 2018-05-01, соответствующих пакетах SDK и средствах Batch AI представлены существенные изменения и новые возможности.

Эта статья предназначена для тех, кто пользовался предыдущей версией API Batch AI. Из статьи вы узнаете, как изменить код и скрипты для работы с новым API. 

## <a name="whats-changing"></a>Изменения

По просьбам клиентов мы снимаем ограничения на количество заданий и упрощаем управление ресурсами Batch AI. С новым API появятся два новых ресурса: *рабочие области* и *эксперименты*. Связанные задания обучения можно собирать в эксперименты, а все связанные ресурсы Batch AI (кластеры, файловые серверы, эксперименты, задания) — упорядочивать в рабочие области.  

* **Рабочая область**. Высокоуровневая коллекция всех типов ресурсов Batch AI. Кластеры и файловые серверы находятся в рабочей области. Работа разных групп или проектов обычно находится в разных рабочих областях. Например, у вас может быть две рабочие области: для разработки и тестирования. В пределах одной подписки, вероятно, будет использоваться ограниченное количество рабочих областей. 

* **Эксперимент**. Коллекция, позволяющая одновременно управлять всеми связанными заданиями, например отправлять к ним запросы. В эксперимент, к примеру, можно сгруппировать все задания, выполняемые при точной настройке гиперпараметров. 

На рисунке ниже приведен пример иерархии ресурсов. 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

## <a name="monitor-and-manage-existing-resources"></a>Мониторинг существующих ресурсов и управление ими
В каждой группе ресурсов, в которой вы уже создали кластеры, задания или файловые серверы Batch AI, служба Batch AI создаст рабочую область с именем `migrated-<region>` (например, `migrated-eastus`) и эксперимент с именем `migrated`. Эту рабочую область и эксперимент нужно использовать для доступа к созданным ранее заданиям, кластерам и файловым серверам. 

### <a name="portal"></a>Microsoft Azure 
Чтобы получить доступ к созданным ранее заданиям, кластерам и файловым серверам с помощью портала, сначала выберите рабочую область `migrated-<region>`. После этого вы сможете изменить размер кластера, удалить его, просмотреть состояние заданий, выходные данные и др. 

### <a name="sdks"></a>Пакеты SDK 
Чтобы получить доступ к созданным ранее задачам, кластерам или файловым серверам с помощью пакета SDK для Batch AI, укажите в параметрах имена рабочей области и эксперимента. 

Соответствующие изменения показаны ниже на примере пакета SDK для Python. В других пакетах SDK для Batch AI изменения выглядят примерно так же. 


#### <a name="get-old-cluster"></a>Получение старого кластера 

```python
cluster = client.clusters.get(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="delete-old-cluster"></a>Удаление старого кластера 

```python
client.clusters.delete(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="get-old-file-server"></a>Получение старого файлового сервера 

```python
cluster = client.fileservers.get(resource_group_name, 'migrated-<region>', fileserver_name)
```

#### <a name="delete-old-file-server"></a>Удаление старого файлового сервера  

```python
client.fileservers.delete(resource_group_name, 'migrated-<region>', fileserver_name)
``` 


#### <a name="get-old-job"></a>Получение старого задания 

```python
cluster = client.jobs.get(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```

#### <a name="delete-old-job"></a>Удаление старого задания

```python
client.jobs.delete(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```
 
### <a name="azure-cli"></a>Инфраструктура CLI Azure 
 
При работе с созданными ранее заданиями, кластерами или файловыми серверам через командную строку Azure (Azure CLI) указывайте имена рабочей области и эксперимента в параметрах `-w` и `-e`. 


#### <a name="get-old-cluster"></a>Получение старого кластера

```azurecli
az batchai cluster show -g resource-group-name -w migrated-<region> -n cluster-name
```


#### <a name="delete-old-cluster"></a>Удаление старого кластера 

```azurecli
az batchai cluster delete -g resource-group-name -w migrated-<region> -n cluster-name
```

#### <a name="get-old-file-server"></a>Получение старого файлового сервера

```azurecli
az batchai file-server show -g resource-group-name -w migrated-<region> -n fileserver-name
```


#### <a name="delete-old-file-server"></a>Удаление старого файлового сервера 

```azurecli
az batchai file-server delete -g resource-group-name -w migrated-<region> -n fileserver-name
``` 


#### <a name="get-old-job"></a>Получение старого задания

```azurecli
az batchai job show -g resource-group-name -w migrated-<region> -e migrated -n job-name
```


#### <a name="delete-old-job"></a>Удаление старого задания 

```azurecli
az batchai job delete -g resource-group-name -w migrated-<region> -e migrated -n job-name
``` 

## <a name="create-batch-ai-resources"></a>Создание ресурсов Batch AI 
 
Если вы используете один из существующих пакетов SDK для Batch AI, вам не нужно вносить изменения в код. Этот пакет можно использовать и для создания ресурсов Batch AI (задания, кластеры или файловые серверы). Если же вы перешли со старого пакета SDK на новый, вам нужно внести ряд изменений.
 
### <a name="create-workspace"></a>Создание рабочей области 
В зависимости от вашего сценария рабочую область можно один раз создать вручную через портал или командную строку. В командной строке рабочую область можно создать с помощью следующей команды: 

```azurecli
az batchai workspace create -g resource-group-name -n workspace-name
```

### <a name="create-experiment"></a>Создание эксперимента 


В зависимости от вашего сценария эксперимент можно один раз создать вручную через портал или командную строку. В командной строке эксперимент можно создать с помощью следующей команды: 

```azurecli
az batchai experiment create -g resource-group-name -w workspace-name -n experiment-name

```

### <a name="create-clusters-file-servers-and-jobs"></a>Создание кластеров, файловых серверов и заданий
 
Создавая задания, кластеры или файловые серверы на портале, следуйте инструкциям портала. Вам будет предложено указать имена рабочей области и эксперимента.

Если же для создания заданий, кластеров или файловых серверов используется новый пакет SDK, укажите параметр имени рабочей области. Соответствующие изменения показаны ниже на примере пакета SDK для Python. Вместо *workspace_name* и *experiment_name* укажите имена рабочей области и эксперимента, которые вы создали ранее. В других пакетах SDK для Batch AI изменения выглядят примерно так же. 


#### <a name="create-cluster"></a>Создание кластера 

```python
_ = client.clusters.create(resource_group_name, workspace_name, cluster_name, cluster_create_parameters).result()
```

#### <a name="create-file-server"></a>Создание файлового сервера 

```python
_ = client.fileservers.create(resource_group_name, workspace_name, fileserver_name, fileserver_create_parameters).result()
```

#### <a name="create-job"></a>Создание задания 

```python
_ = client.jobs.create(resource_group_name, workspace_name, experiment_name, job_name job_create_parameters).result()
```


## <a name="next-steps"></a>Дополнительная информация

* См. справку по API Batch AI: [CLI](/cli/azure/batchai), [.NET](/dotnet/api/overview/azure/batchai), [Java](/java/api/overview/azure/batchai), [Node.js](/javascript/api/overview/azure/batchai), [Python](/python/api/overview/azure/batchai) и [REST](/rest/api/batchai).