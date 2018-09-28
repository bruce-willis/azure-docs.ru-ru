---
title: Отправляйте метрики операционной системы на виртуальной машине в облачную службу хранилища метрик Azure Monitor.
description: Отправляйте метрики операционной системы на виртуальной машине в облачную службу хранилища метрик Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: be27ff3f8dda3209a011c3ad79d1a7a1f1d259fe
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986920"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-service"></a>Отправляйте метрики операционной системы на виртуальной машине в облачную службу хранилища метрик Azure Monitor.

[Расширение системы диагностики Azure (Windows)](azure-diagnostics.md) (WAD) Azure Monitor позволяет собирать метрики и журналы из операционной системы на виртуальной машине (гостевая ОС), выполняемой как часть виртуальной машины, облачной службы или кластера Service Fabric.  Расширение может отправлять телеметрию во множество различных расположений, перечисленных в предыдущей статье.  

В этой статье описывается процесс отправки метрик производительности операционной системы на виртуальной машине для классической облачной службы Azure к хранилищу метрик Azure Monitor. Начиная с WAD версии 1.11, метрики можно записывать напрямую в хранилище метрик Azure Monitor, где уже собраны стандартные платформы метрик. Хранение их в этом расположении позволяет получить доступ к тем же действиям, которые доступны для метрик платформы.  К этим действиям относятся оповещения практически в реальном времени, построение диаграмм, маршрутизация, доступ из REST API и многое другое.  В прошлом расширение WAD выполняло запись в службу хранилища Azure, но не в хранилище данных Azure Monitor.  

Процесс, описанный в этой статье, работает только для счетчиков производительности в облачных службах Azure. Он не работает для других пользовательских метрик. 
   

## <a name="pre-requisites"></a>Предварительные требования

- Вам необходима подписка [администратора службы или соадминистратора](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md) Azure. 

- Подписки необходимо зарегистрировать в [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1). 

- Необходимо установить [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) или использовать [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md). 


## <a name="provision-cloud-service-and-storage-account"></a>Подготовка к работе облачной службы и учетной записи хранилища 

1. Создание и развертывание классической облачной службы (пример приложения классической облачной службы и развертывания можно найти [здесь](../cloud-services/cloud-services-dotnet-get-started.md)). 

2. Вы можете использовать существующую учетную запись хранения или развернуть новую. Проще всего, если учетная запись хранения находится в том же регионе, что классическая облачная служба, которую вы только что создали. На портале Azure перейдите к колонке ресурсов учетной записи хранения и выберите **ключи**. Введите записанные на предыдущем шаге имя и ключ учетной записи хранения.

   ![Ключи учетной записи хранения](./media/metrics-store-custom-guestos-classic-cloud-service/storage-keys.png)



## <a name="create-a-service-principal"></a>Создание субъекта-службы 

Создайте субъект-службу в клиенте Azure Active Directory, используя инструкции, найденные по адресу ../azure/azure-resource-manager/resource-group-create-service-principal-portal. Выполняя это действие, обратите внимание на следующее. 
  - Вы можете указать любой URL-адрес для входа в систему.  
  - Создание нового секрета клиента для этого приложения  
  - Сохраните ключ и идентификатор клиента для использования в последующих шагах.  

Предоставьте приложению, созданному на предыдущем шаге, доступ *издателя метрик мониторинга* к ресурсу, из которого вы хотите генерировать метрики. Если вы планируете использовать приложение для генерации пользовательских метрик ко многим ресурсам, можно предоставить эти разрешения группе ресурсов или уровню подписки.  

> [!NOTE]
> Расширение системы диагностики будет использовать субъект-службу для проверки подлинности в Azure Monitor и издавать метрики для облачной службы. 

## <a name="author-diagnostics-extension-configuration"></a>Конфигурация расширения диагностики автора 

Подготовьте файл конфигурации расширения диагностики WAD. Этот файл определяет, какие журналы и счетчики производительности расширения системы диагностики следует собирать для облачной службы. Ниже приведен пример файла конфигурации диагностики  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
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

В разделе "SinksConfig" вашего файла диагностики укажите новый приемник Azure Monitor. 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

В разделе файла конфигурации, где находится список счетчиков производительности, добавьте приемник Azure Monitor. Эта запись гарантирует, что все указанные счетчики производительности направляются в Azure Monitor, как метрики. При необходимости счетчики производительности можно добавлять или удалять. 

```XML
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink"> 
 <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
  … 
</PerformanceCounters> 
```
Наконец в закрытой конфигурации добавьте раздел *Учетная запись Azure Monitor*. Введите идентификатор клиента и секрет субъекта-службы, созданной на предыдущем шаге. 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```
 
Сохраните этот файл диагностики локально.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Развертывание расширения системы диагностики в облачной службе 

Запустите PowerShell и войдите в Azure 

```PowerShell
Login-AzureRmAccount 
```

Храните сведения о сведениях учетной записи хранения, созданные на предыдущем шаге, в переменных, используя следующие команды. 

```PowerShell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```
 
Аналогично задайте путь к файлу диагностики в переменной с помощью следующей команды. 

```PowerShell
$diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>” 
```
 
Развертывание расширения системы диагностики в облачной службе с помощью файла диагностики используя приемник Azure Monitor, настроенный с помощью следующей команды 

```PowerShell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```
 
> [!NOTE] 
> В рамках установки расширения диагностики по-прежнему необходимо предоставить учетную запись хранения. Все журналы и счетчики производительности, указанные в файле конфигурации диагностики, должны записываться в указанной учетной записи хранения.  

## <a name="plot-metrics-in-the-azure-portal"></a>График метрик на портале Azure 

Переход на портал Azure 

 ![Метрики портала Azure](./media/metrics-store-custom-guestos-classic-cloud-service/navigate-metrics.png)

1. В меню слева щелкните "Монитор". 

1. В колонке "Монитор" щелкните вкладку предварительного просмотра метрики. 

1. В раскрывающемся списке ресурсов выберите классическую облачную службу. 

1. В раскрывающемся списке пространств имен выберите **azure.vm.windows.guest**. 

1. В раскрывающемся списке метрик выберите *Память или использования выделенной памяти (в байтах)*. 

Вы можете просмотреть общий объем памяти, используемый определенной ролью, и каждый экземпляр роли, используя возможности фильтрации и разделения размеров. 

 ![Метрики портала Azure](./media/metrics-store-custom-guestos-classic-cloud-service/metrics-graph.png)

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения о настраиваемых метриках см. в [этой статье](metrics-custom-overview.md).



