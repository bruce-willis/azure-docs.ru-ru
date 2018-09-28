---
title: Примеры для быстрого начала работы с командной строкой в Azure Monitor
description: Примеры команд интерфейса командной строки для функций монитора Azure Monitor. Azure Monitor — это служба Microsoft Azure, которая позволяет отправлять оповещения и осуществлять вызов URL-адресов на основе значений настроенных данных телеметрии, а также выполнять автоматическое масштабирование облачных служб, виртуальных машин и веб-приложений.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: robb
ms.component: ''
ms.openlocfilehash: 1728511402d48904fa968790f790314fdf57767c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961524"
---
# <a name="azure-monitor-cli-quick-start-samples"></a>Примеры для быстрого начала работы с командной строкой в Azure Monitor
В этой статье приведены примеры команд интерфейса командной строки для работы с функциями Azure Monitor. Azure Monitor позволяет выполнять автомасштабирование облачных служб, виртуальных машин и веб-приложений, отправлять оповещения и осуществлять вызов URL-адресов на основе значений настроенных данных телеметрии.

## <a name="prerequisites"></a>Предварительные требования

Если вы еще не установили Azure CLI, выполните инструкции по [установке Azure CLI](/cli/azure/install-azure-cli). Для запуска CLI в качестве интерактивного интерфейса в браузере вы также можете использовать [Azure Cloud Shell](/azure/cloud-shell). Сведения обо всех доступных командах см. в [справке по интерфейсу командной строки Azure Monitor](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). 

## <a name="log-in-to-azure"></a>Вход в Azure
Первый шаг — войти в учетную запись Azure.

```azurecli
az login
```

После выполнения этой команды необходимо будет войти, следуя инструкциям на экране. Все команды работают в контексте подписки по умолчанию.

Для получения сведений о текущей подписке используйте следующую команду.

```azurecli
az account show
```

Чтобы изменить подписку для рабочего контекста, используйте следующую команду.

```azurecli
az account set -s <Subscription ID or name>
```

Чтобы просмотреть список всех поддерживаемых команд Azure Monitor, выполните следующие действия.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Просмотр журнала действий для подписки

Чтобы просмотреть события журнала действий, выполните шаги ниже.

```azurecli
az monitor activity-log list
```

Попробуйте выполнить следующие действия, чтобы просмотреть все доступные параметры.

```azurecli
az monitor activity-log list -h
```

Ниже приведен пример получения списка журналов для группы ресурсов

```azurecli
az monitor activity-log list --resource-group <group name>
```

Пример получения списка журналов для вызывающего объекта

```azurecli
az monitor activity-log list --caller myname@company.com
```

Пример получения списка журналов для вызывающего объекта и типа ресурса в определенном диапазоне дат

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Работа с оповещениями 
> [!NOTE]
> Сейчас в интерфейсе командной строки поддерживаются только оповещения (классические). 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Получение правил оповещений (классических) в группе ресурсов

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Создание метрики правил оповещения (классические)

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Удаление правил оповещений (классических)

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Профили журнала

В этом разделе описана работа с профилями журнала.

### <a name="get-a-log-profile"></a>Получение профиля журнала

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Добавление профиля журнала с хранением

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Добавление профиля журнала с хранением и концентратором событий

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Удаление профиля журнала

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Диагностика

В этом разделе описана работа с параметрами диагностики.

### <a name="get-a-diagnostic-setting"></a>Получение параметра диагностики

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-log-setting"></a>Создание параметра для журнала диагностики 

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <storage account ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

### <a name="delete-a-diagnostic-setting"></a>Удаление параметра диагностики

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Autoscale

В этом разделе описана работа с параметрами автомасштабирования. Эти примеры необходимо изменить.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Получение параметров автомасштабирования для группы ресурсов

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Получение параметров автомасштабирования по имени в группе ресурсов

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Настройка параметров автоматического масштабирования

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```
