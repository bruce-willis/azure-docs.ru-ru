---
title: Отправляйте метрики операционной системы на виртуальной машине в хранилище данных Azure Monitor для виртуальной машины Windows (классическая)
description: Отправляйте метрики операционной системы на виртуальной машине в хранилище данных Azure Monitor для виртуальной машины Windows (классическая)
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.component: ''
ms.openlocfilehash: cb803450f7765ae62292ff3afb7f32209b437f78
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978936"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-data-store-for-a-windows-virtual-machine-classic"></a>Отправляйте метрики операционной системы на виртуальной машине в хранилище данных Azure Monitor для виртуальной машины Windows (классическая)

[Расширение Windows Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) (WAD) Azure Monitor дает возможность собирать метрики и журналы из операционной системы на виртуальной машине (гостевая ОС), работающей как часть виртуальной машины, облачной службы или кластера Service Fabric. Расширение может отправлять телеметрию во множество различных расположений, перечисленных в предыдущей статье.

В этой статье описывается процесс отправки метрик производительности операционной системы на виртуальной машине под управлением Windows (классическая) к хранилищу метрик Azure Monitor. Начиная с WAD версии 1.11, метрики можно записывать напрямую в хранилище метрик Azure Monitor, где уже собраны стандартные платформы метрик. Хранение их в этом расположении позволяет получить доступ к тем же действиям, которые доступны для метрик платформы.  К этим действиям относятся оповещения практически в реальном времени, построение диаграмм, маршрутизация, доступ из REST API и многое другое.  В прошлом расширение WAD выполняло запись в службу хранилища Azure, но не в хранилище данных Azure Monitor. 

Процесс, описанный в этой статье, работает только для классических виртуальных машин под управлением операционной системы Windows.

## <a name="pre-requisites"></a>Предварительные требования

- Вам необходима подписка [администратора службы или соадминистратора](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md) Azure. 

- Подписки необходимо зарегистрировать в [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1). 

- Необходимо установить [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) или использовать [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md). 

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Создание классической виртуальной машины и учетной записи хранения

1. Создайте классическую виртуальную машину с помощью портала Azure ![Создание классической виртуальной машины](./media/metrics-store-custom-guestos-classic-vm/create-classic-vm.png).

1. При создании этой виртуальной машины выберите создание новой классической учетной записи хранения. Мы воспользуемся этой учетной записью хранения на следующих шагах.

1. На портале Azure перейдите к колонке ресурсов учетной записи хранения, выберите **ключи** и запишите имя и ключ учетной записи хранения. Эти ключи вам понадобятся на последующих шагах в разделе ![Ключи доступа к хранилищу](./media/metrics-store-custom-guestos-classic-vm/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Создание субъекта-службы

Создайте субъект-службу в клиенте Azure Active Directory, используя инструкции из раздела [Создание субъекта-службы](../azure-resource-manager/resource-group-create-service-principal-portal.md). Выполняя это действие, обратите внимание на следующее. 
- Создание нового секрета клиента для этого приложения  
- Сохраните ключ и идентификатор клиента для использования в последующих шагах.

Предоставьте этому приложению "Издатель метрик мониторинга" доступ к ресурсу, из которого вы хотите издавать метрики. Вы можете использовать группу ресурсов или целую подписку.  

> [!NOTE]
> Расширение системы диагностики будет использовать субъект-службу для проверки подлинности в Azure Monitor и издавать метрики для вашей классической виртуальной машины.

## <a name="author-diagnostics-extension-configuration"></a>Конфигурация расширения диагностики автора

1. Подготовьте файл конфигурации расширения диагностики WAD. Этот файл определяет, какие журналы и счетчики производительности расширения системы диагностики следует собирать для классической виртуальной машины. Пример приведен ниже.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
            </PerformanceCounters>
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" />
                <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" />
            </WindowsEventLog>
            <CrashDumps>
                <CrashDumpConfiguration processName="WaIISHost.exe" />
                <CrashDumpConfiguration processName="WaWorkerHost.exe" />
                <CrashDumpConfiguration processName="w3wp.exe" />
            </CrashDumps>
            <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" />
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
        </DiagnosticMonitorConfiguration>
        <SinksConfig>
        </SinksConfig>
        </WadCfg>
        <StorageAccount />
    </PublicConfig>
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <StorageAccount name="" endpoint="" />
    </PrivateConfig>
    <IsEnabled>true</IsEnabled>
    </DiagnosticsConfiguration>
    ```
1. В разделе "SinksConfig" вашего файла диагностики укажите новый приемник Azure Monitor.

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide your Classic VM’s Resource ID </ResourceId>
                <Region>Region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. В разделе файла конфигурации, в котором указан список счетчиков производительности, метрики из которых необходимо собрать, перейдите по счетчикам производительности в приемник Azure Monitor "AzMonSink".

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. В закрытой конфигурации определите учетную запись Azure Monitor и добавьте информацию о субъекте-службе, используемой для издания метрик.

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. Сохраните файл локально.

## <a name="deploy-diagnostics-extension-to-your-cloud-service"></a>Развертывание расширения системы диагностики в облачной службе

1. Запустите PowerShell и войдите в систему.

    ```powershell
    Login-AzureRmAccount
    ```

1. Начните с установки контекста для классической виртуальной машины.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Задайте контекст классической учетной записи хранения, созданной в виртуальной машине.

    ```powershell
    $StorageContext = New-AzureStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Задайте путь к файлу диагностики в переменной с помощью следующей команды.

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Подготовка обновления классической виртуальной машины в файле диагностики с настроенным приемником Azure Monitor

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Разверните обновление виртуальной машины, выполнив следующую команду.

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> В рамках установки расширения диагностики по-прежнему необходимо предоставить учетную запись хранения. Все журналы и счетчики производительности, указанные в файле конфигурации диагностики, должны записываться в указанной учетной записи хранения.

## <a name="plot-the-metrics-in-the-azure-portal"></a>График метрик на портале Azure

1.  Перейдите на портал Azure.

1.  В меню слева щелкните "Монитор".

1.  В колонке "Монитор" щелкните **Метрики**
   ![Переместить метрики](./media/metrics-store-custom-guestos-classic-vm/navigate-metrics.png).

1. В раскрывающемся списке ресурсов выберите классическую виртуальную машину.

1. В раскрывающемся списке выберите **azure.vm.windows.guest**.

1. В раскрывающемся списке метрик выберите **Память / Использование выделенной памяти (в байтах)**
   ![Графические метрики](./media/metrics-store-custom-guestos-classic-vm/plot-metrics.png).


## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения о настраиваемых метриках см. в [этой статье](metrics-custom-overview.md).
