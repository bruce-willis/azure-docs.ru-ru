---
title: Краткое руководство. Периодическое резервное копирование и восстановление в Azure Service Fabric (предварительная версия) | Документация Майкрософт
description: Использование функции периодического резервного копирования и восстановления Service Fabric для включения периодического резервного копирования данных приложения.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAA58600-897E-4CEE-9D1C-93FACF98AD1C
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2018
ms.author: hrushib
ms.openlocfilehash: 50ee0d91b27805e4db785e5df211660900333e7f
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990305"
---
# <a name="quickstart-periodic-backup-and-restore-in-azure-service-fabric-preview"></a>Краткое руководство. Периодическое резервное копирование и восстановление в Azure Service Fabric (предварительная версия)
> [!div class="op_single_selector"]
> * [Кластеры в Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Изолированные кластеры](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric — это платформа распределенных систем, которая упрощает разработку надежных распределенных облачных приложений на основе микрослужб и управление ими. Она позволяет запускать микрослужбы с отслеживанием и без отслеживания состояния. Службы с отслеживанием состояния могут поддерживать изменчивое, авторитетное состояние за пределами запроса и ответа или полной транзакции. Если служба с отслеживанием состояния отключена в течение длительного времени или теряет информацию вследствие аварии, может возникнуть необходимость восстановления до недавней резервной копии состояния для дальнейшего использования службы.

Service Fabric реплицирует состояние на нескольких узлах, обеспечивая высокую доступность службы. Даже в случае сбоя одного узла в кластере служба будет оставаться доступной. Однако в некоторых случаях все же желательно обеспечить устойчивость данных службы к более масштабным сбоям.
 
Например, службе может потребоваться создать резервную копию своих данных для защиты от таких сценариев:
- в случае полной потери всего кластера Service Fabric.
- Полной потери большей части реплик секций служб.
- Если из-за административных ошибок произошло случайное удаление или повреждение состояния. Например, администратор с достаточными привилегиями ошибочно удалил службу.
- Если из-за ошибок в службе происходит повреждение данных. Например, это может произойти, если при обновлении кода службы начинается запись поврежденных данных в надежную коллекцию. В этом случае может потребоваться вернуть код и данные в предыдущее состояние.
- При автономной обработке данных. Автономная обработка данных для бизнес-аналитики, которая происходит отдельно от службы, создающей данные, может быть очень удобной.

Service Fabric предоставляет встроенный API для выполнения [резервного копирования и восстановления](service-fabric-reliable-services-backup-restore.md) на момент во времени. Разработчики приложений могут использовать эти API для периодического резервного копирования состояния службы. Кроме того, если администраторы службы хотят активировать резервное копирование из-за пределов службы в определенный момент время, например перед обновлением приложения, разработчики должны предоставить возможность получения резервной копии (и восстановления) в виде API этой службы. Поддержка резервных копий влечет за собой дополнительные расходы. Например, вы можете создать 5 добавочных резервных копий каждые полчаса, а затем — полную резервную копию. После создания полной резервной копии можно удалить предыдущие добавочные резервные копии. Такой подход требует написания дополнительного кода, что приводит к дополнительным затратам при разработке приложений.

Периодическое резервное копирование данных приложения является основной потребностью для обеспечения процесса управления распределенным приложением и его защиты от потери данных либо длительной недоступности службы. Service Fabric предоставляет дополнительную службу резервного копирования и восстановления, которая позволяет настраивать периодическое резервное копирование надежных служб с отслеживанием состояния (включая службы субъекта) без необходимости написания дополнительного кода. Она также упрощает восстановление ранее сделанных резервных копий. 

> [!NOTE]
> В настоящее время доступна **предварительная версия** функции периодического резервного копирования и восстановления. Она не поддерживается для рабочих нагрузок. 
>

Service Fabric предоставляет набор API для использования функций, связанных с возможностью периодического резервного копирования и восстановления.

- Планирование периодического резервного копирования надежных служб с отслеживанием состояния и Reliable Actors с поддержкой передачи резервных копий во внешние места хранения. Поддерживаемые места хранения
    - Хранилище Azure
    - Файловый ресурс (в локальной среде)
- Перечисление резервных копий.
- Запуск нерегламентированного резервного копирования секции.
- Восстановление секции с помощью предыдущей резервной копии.
- Временная остановка резервного копирования.
- Управление хранением резервных копий (предстоящих).

## <a name="prerequisites"></a>Предварительные требования
* Кластер Service Fabric с платформой Fabric версии 6.2 и выше. Кластер должен быть установлен на Windows Server. Инструкции по созданию кластера Service Fabric с помощью шаблона ресурсов Azure см. в [этой статье](service-fabric-cluster-creation-via-arm.md).
* Сертификат X.509 для шифрования секретов, необходимых для подключения к хранилищу резервных копий. Сведения о получении или создании сертификата X.509 см. в статье [Создание кластера Service Fabric в Azure с помощью Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).
* Надежное приложения Service Fabric с отслеживанием состояния, созданное с помощью пакета SDK Service Fabric версии 3.0 или выше. Приложение, ориентированное на .Net Core 2.0, нужно создавать с помощью пакета SDK Service Fabric версии 3.1 или выше.
* Создайте учетную запись хранения Azure для хранения резервных копий приложения.

## <a name="enabling-backup-and-restore-service"></a>Включение резервного копирования и восстановления службы
Сначала необходимо включить _службу резервного копирования и восстановления_ в кластере. Получите шаблон для кластера, который требуется развернуть. Вы можете использовать [примеры шаблонов](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) или создать шаблон Resource Manager. Включите _службу резервного копирования и восстановления_ следующим образом:

1. Убедитесь, что версия `apiversion` ресурса `Microsoft.ServiceFabric/clusters` имеет значение **`2018-02-01`**. А если не имеет, то обновите ее, как показано во фрагменте ниже.

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Включите _службу резервного копирования и восстановления_ , добавив следующий раздел `addonFeatures` после раздела `properties`, как показано во фрагменте кода ниже: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Настройте сертификат X.509 для шифрования учетных данных. Это необходимо, чтобы перед сохранением зашифровать учетные данные, предоставленные для подключения к хранилищу. Настройте сертификат шифрования, добавив следующий раздел `BackupRestoreService` после раздела `fabricSettings`, как показано во фрагменте кода ниже: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. После обновления шаблона кластера примените указанные выше изменения и дождитесь завершения развертывания или обновления. По завершении _служба резервного копирования и восстановления_ запустится в кластере. Uri этой службы — `fabric:/System/BackupRestoreService`. Она может быть расположена в разделе системных служб в обозревателе Service Fabric. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Включение периодического резервного копирования для надежной службы с отслеживанием состояния и Reliable Actors
Давайте рассмотрим шаги ниже, чтобы включить периодическое резервное копирование для надежной службы с отслеживанием состояния, а также службы Reliable Actors. Предполагается следующее:
- Кластер был настроен с помощью сертификата безопасности X.509 со _службой резервного копирования и восстановления_.
- Надежная служба с отслеживанием состояния развернута в кластере. В этом кратком руководстве Uri приложения — `fabric:/SampleApp`, а Uri службы с отслеживанием состояния, относящейся к этому приложению, — `fabric:/SampleApp/MyStatefulService`. Эта служба развертывается с одним разделом с идентификатором `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.
- Сертификат клиента с ролью администратора устанавливается в _My_ (_Personal_) имени хранилища сертификатов _CurrentUser_ на компьютере, откуда будут вызываться сценарии. В этом примере в качестве отпечатка этого сертификата используется `1b7ebe2174649c45474a4819dafae956712c31d3`. Дополнительные сведения о сертификатах клиента см. в статье [Контроль доступа на основе ролей для клиентов Service Fabric](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Создание политики архивации

Первым шагом является создание политики архивации, содержащей расписание резервного копирования, сведения о целевом хранилище для данных резервного копирования, имени политики и максимальном количестве добавочных резервных копий, которые необходимо разрешить до запуска полной резервной копии. 

В качестве хранилища резервных копий используйте созданную ранее учетную запись хранения Azure. Контейнер `backup-container` настроен для хранения резервных копий. Если контейнер с таким именем не существует, он создается во время отправки резервных копий. Укажите в параметре `ConnectionString` действительную строку подключения к учетной записи хранения Azure. Вместо `account-name` укажите имя учетной записи хранения, а вместо `account-key` — ее ключ.

Выполните следующий сценарий PowerShell, чтобы вызвать требуемый REST API для создания политики. Вместо `account-name` укажите имя учетной записи хранения, а вместо `account-key` — ее ключ.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

### <a name="enable-periodic-backup"></a>Включение периодического резервного копирования
После определения политики архивации для соответствия требованиям защиты данных приложения необходимо связать ее с приложением. В зависимости от требований политику резервного копирования можно связать с приложением, службой или секцией.

Выполните следующий сценарий PowerShell для вызова необходимого REST API, чтобы связать политику резервного копирования `BackupPolicy1`, созданную на предыдущем шаге, с приложением `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Проверка работоспособности периодического резервного копирования

После включения резервного копирования на уровне приложения все секции, принадлежащие надежным службам с отслеживанием состояния и службами Reliable Actors в рамках приложения, будут периодически получать резервные копии согласно соответствующей политике архивации. 

![Событие работоспособности резервных копий секции][0]

### <a name="list-backups"></a>Перечисление резервных копий

Резервные копии, связанные со всеми секциями, принадлежащими надежным службам с отслеживанием состояния и службам Reliable Actors приложения, можно перечислить с помощью API _GetBackups_. Резервные копии могут быть перечислены для приложения, службы или секции.

Выполните следующий сценарий PowerShell, чтобы вызвать API HTTP для перечисления резервных копий, созданных для всех секций внутри приложения `SampleApp`.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.2-preview"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```
Пример выходных данных описанного выше процесса.

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 

BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 

BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            : 
```

## <a name="preview-limitation-caveats"></a>Ограничения и предупреждения, связанные с предварительной версией
- Отсутствие встроенных командлетов PowerShell для платформы Service Fabric.
- Отсутствие поддержки интерфейса командной строки Service Fabric.
- Отсутствие поддержки автоматического удаления ранее созданных резервных копий. [Скрипт хранения резервной копии](https://github.com/Microsoft/service-fabric-scripts-and-templates/tree/master/scripts/BackupRetentionScript) можно использовать как внешнее средство автоматизации очистки резервной копии.
- Отсутствие поддержки кластеров Service Fabric в Linux.

## <a name="next-steps"></a>Дополнительная информация
- [Основные сведения о настройке периодического резервного копирования](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Backup restore REST API reference](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore) (Справочник по REST API службы резервного копирования и восстановления)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent_Azure.png

