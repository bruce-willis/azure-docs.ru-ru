---
title: Как запрашивать журналы из Azure Monitor для виртуальных машин | Microsoft Docs
description: Решение Azure Monitor для виртуальных машин перенаправляет метрики и данные журналов в службу Log Analytics. В этой статье описаны эти записи и приведены примеры запросов.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: 446268f28e7c87196023636889f03be2da92ecfd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967648"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms"></a>Как запрашивать журналы из Azure Monitor для виртуальных машин
Azure Monitor для виртуальных машин собирает метрики производительности и подключений, данные инвентаризации компьютеров и процессов, а также сведения о работоспособности и перенаправляет их в хранилище данных Log Analytics в службе Azure Monitor.  Вы можете выполнять [поиск](../log-analytics/log-analytics-log-searches.md) этих данных в службе Log Analytics. Эти данные используются в различных сценариях, таких как планирование миграции, анализ емкости, обнаружение и устранение проблем с производительностью по требованию.

## <a name="map-records"></a>Сопоставление записей
Для каждого уникального компьютера и процесса создается одна запись в час. Кроме того, записи создаются во время запуска компьютера или процесса, а также при подключении к функции "Схема" в Azure Monitor для виртуальных машин. В таблице ниже приведены свойства этих записей. Поля и значения в событиях ServiceMapComputer_CL сопоставляются с полями ресурса Machine (Компьютер) в API ServiceMap Azure Resource Manager. Поля и значения в событиях ServiceMapProcess_CL сопоставляются с полями ресурса Process (Процесс) в API ServiceMap Azure Resource Manager. Поле ResourceName_s совпадает с полем имени в соответствующем ресурсе Resource Manager. 

На основе созданных в системе свойств можно определить уникальные компьютеры или процессы:

- Для уникальной идентификации компьютера в рабочей области Log Analytics используйте свойство *ResourceId* или *ResourceName_s*.
- Для уникальной идентификации процесса в рабочей области Log Analytics используйте свойство *ResourceId*. Свойство *ResourceName_s* уникально в контексте компьютера, на котором выполняется процесс (MachineResourceName_s). 

Так как для указанных процесса и компьютера в заданном диапазоне времени могут существовать несколько записей, то запросы могут возвращать несколько записей для одного и того же компьютера или процесса. Чтобы включить только самую последнюю запись, добавьте к запросу строку "| dedup ResourceId".

### <a name="connections"></a>Подключения
Метрики подключений записываются в новой таблице VMConnection в службе Log Analytics. Эта таблица содержит сведения о подключениях (входящих и исходящих) для компьютера. Кроме того, доступ к метрикам подключений можно организовать с помощью API, включающих средства для получения определенной метрики в пределах временного окна.  Подключения TCP, созданные в результате *принятия* данных на сокете прослушивания, являются входящими, а подключения, созданные в результате *подключения* к заданному IP-адресу и порту, считаются исходящими. Направление подключения указано в свойстве Direction (Направление), которое может иметь значение **inbound** (Входящее) или **outbound** (Исходящее). 

Записи в этих таблицах создаются на основе данных, полученных программой Dependency Agent. Каждая запись соответствует наблюдению, выполняемому один раз в минуту. Свойство TimeGenerated указывает начало интервала времени. Каждая запись содержит сведения для определения соответствующего объекта, то есть подключения или порта, а также метрики, связанные с этим объектом. На данный момент система собирает данные, касающиеся только действий в сети, связанных с протоколом TCP/IPv4.

Чтобы можно было управлять затратами и сложностью, записи о подключениях не представляют отдельные физические сетевые подключения. Система группирует несколько физических сетевых подключений в логическое подключение, которое затем отображается в соответствующей таблице.  Это означает, что запись в таблице *VMConnection* представляет логическую группу, а не отдельные отслеживаемые физические подключения. Физические сетевые подключения, для которых используется одно и то же общее значение для указанных ниже атрибутов в течение заданного одноминутного интервала, агрегированы в одну логическую запись в таблице *VMConnection*. 

| Свойство | ОПИСАНИЕ |
|:--|:--|
|Direction |Направление подключения. Возможные значения: *inbound* (Входящее) или *outbound* (Исходящее). |
|Machine |Полное доменное имя компьютера. |
|Process |Удостоверение процесса или группы процессов, создающей или принимающей подключение. |
|SourceIp |IP-адрес источника. |
|DestinationIp |IP-адрес назначения. |
|DestinationPort |Номер порта назначения. |
|Протокол |Протокол, используемый для подключения.  Значение: *tcp*. |

Чтобы учесть влияние группирования, в указанных ниже свойствах записи приведены сведения о количестве сгруппированных физических подключений.

| Свойство | ОПИСАНИЕ |
|:--|:--|
|LinksEstablished |Количество физических сетевых подключений, созданных в течение отчетного периода. |
|LinksTerminated |Количество физических сетевых подключений, разорванных в течение отчетного периода. |
|LinksFailed |Количество физических сетевых подключений, которые не удалось создать во время отчетного периода. На данный момент эта информация доступна только для исходящих подключений. |
|LinksLive |Количество открытых физических сетевых подключений на конец отчетного периода.|

#### <a name="metrics"></a>Метрики

В дополнение к метрикам количества подключений в указанных ниже свойствах записи содержатся сведения об объемах отправленных и принятых данных для заданного логического подключения или порта.

| Свойство | ОПИСАНИЕ |
|:--|:--|
|BytesSent |Общее количество байтов, отправленных в течение отчетного периода. |
|BytesReceived |Общее количество байтов, принятых в течение отчетного периода. |
|Ответы |Количество откликов, наблюдаемых в течение отчетного периода. 
|ResponseTimeMax |Наибольшее время отклика (в миллисекундах), наблюдаемое во время отчетного периода.  Если значение не указано, свойство будет пустым.|
|ResponseTimeMin |Наименьшее время отклика (в миллисекундах), наблюдаемое во время отчетного периода.  Если значение не указано, свойство будет пустым.|
|ResponseTimeSum |Сумма всех значений времени отклика (в миллисекундах), наблюдаемых во время отчетного периода.  Если значение не указано, свойство будет пустым.|

Третий тип собираемых данных — время отклика. Это время, в течение которого вызывающая сторона ожидает обработки запроса, отправленного через подключение, и отклика на него от удаленной конечной точки. Полученное время отклика — это оценочное значение истинного времени отклика базового протокола приложений. Для его вычисления используется эвристика на основе наблюдения за потоком данных между источником и точкой назначения физического сетевого подключения. По сути, это разница между временем, когда последний байт запроса покидает отправителя, и временем, когда последний байт отклика возвращается к нему. Эти две метки времени используются для регистрации событий запроса и отклика для заданного физического подключения. Разность этих значений представляет собой время отклика для одного запроса. 

В первом выпуске данной функции мы используем алгоритм аппроксимации, который может работать с разными степенями успешности, которые зависят от того, какой протокол фактически используется для заданного сетевого подключения. Например, текущий подход хорошо работает для протоколов, основанных на запросах и откликах, например протокола HTTP(S), но не работает с однонаправленными или основанными на очереди сообщений протоколами.

Необходимо учитывать указанные ниже важные моменты.

1. Если процесс принимает подключения на один IP-адрес, но через несколько сетевых интерфейсов, то для каждого интерфейса будет создана отдельная запись. 
2. В записях с диапазонами IP-адресов не будет никаких сведений о действиях. Такие записи используются для обозначения того факта, что какой-либо порт компьютера открыт для входящего трафика.
3. Чтобы снизить уровень детализации и уменьшить объем данных, система пропускает записи с диапазонами IP-адресов, если имеется совпадающая запись (для того же процесса, порта и протокола) с указанным IP-адресом. Если запись с диапазоном IP-адресов пропущена, свойству IsWildcardBind записи с определенным IP-адресом будет присвоено значение True (Истина), указывающее, что порт доступен через любой интерфейс соответствующего компьютера.
4. Для портов, которые связаны только в определенном интерфейсе, свойство IsWildcardBind имеет значение False (Ложь).

#### <a name="naming-and-classification"></a>Именование и классификация
Для удобства IP-адрес удаленной стороны подключения включается в свойство RemoteIp. Для входящих подключений свойство RemoteIp имеет то же значение, что и свойство SourceIp, а для исходящих подключений у него будет такое же значение, что и у свойства DestinationIp. Свойство RemoteDnsCanonicalNames представляет канонические имена DNS, переданные компьютером для свойства RemoteIp. Свойства RemoteDnsQuestions и RemoteClassification зарезервированы для использования в будущем. 

#### <a name="geolocation"></a>Географическое положение
*VMConnection* для каждой записи подключения содержит указанные ниже свойства со сведениями о географическом положении для удаленной стороны. 

| Свойство | ОПИСАНИЕ |
|:--|:--|
|RemoteCountry |Название страны, в которой размещена сторона с IP-адресом, указанным в свойстве RemoteIp.  Пример: *United States* (США). |
|RemoteLatitude |Широта географического положения.  Пример: *47,68*. |
|RemoteLongitude |Долгота географического положения.  Пример: *–122,12*. |

#### <a name="malicious-ip"></a>Вредоносный IP-адрес
Система проверяет, не внесен ли адрес, указанный в свойстве RemoteIp в таблице *VMConnection*, в список IP-адресов, связанных с вредоносными действиями. Если система обнаружит, что адрес, указанный в свойстве RemoteIp, вредоносный, будут заполнены указанные ниже свойства записи (если IP-адрес не считается вредоносным, эти свойства пусты).

| Свойство | ОПИСАНИЕ |
|:--|:--|
|MaliciousIp |Адрес, указанный в свойстве RemoteIp. |
|IndicatorThreadType | |
|ОПИСАНИЕ | |
|TLPLevel | |
|Достоверность | |
|Уровень серьезности | |
|FirstReportedDateTime | |
|LastReportedDateTime | |
|IsActive | |
|ReportReferenceLink | |
|AdditionalInformation | |

### <a name="servicemapcomputercl-records"></a>Записи ServiceMapComputer_CL
В записях типа *ServiceMapComputer_CL* содержатся данные инвентаризации для серверов с программой Dependency Agent. У этих записей есть свойства, приведенные в таблице ниже.

| Свойство | ОПИСАНИЕ |
|:--|:--|
| type | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | Уникальный идентификатор для компьютера в рабочей области |
| ResourceName_s | Уникальный идентификатор для компьютера в рабочей области |
| ComputerName_s | Полное доменное имя компьютера |
| Ipv4Addresses_s | Список IPv4-адресов сервера |
| Ipv6Addresses_s | Список IPv6-адресов сервера |
| DnsNames_s | Массив DNS-имен |
| OperatingSystemFamily_s | Windows или Linux |
| OperatingSystemFullName_s | Полное имя операционной системы  |
| Bitness_s | Разрядность компьютера (32- или 64-разрядный)  |
| PhysicalMemory_d | Объем физической памяти в МБ |
| Cpus_d | Количество ЦП |
| CpuSpeed_d | Скорость ЦП в МГц|
| VirtualizationState_s | *неизвестно*, *физический*, *виртуальный*, *гипервизор* |
| VirtualMachineType_s | *hyperv*, *vmware* и т. д. |
| VirtualMachineNativeMachineId_g | Идентификатор виртуальной машины, назначенный ее гипервизором |
| VirtualMachineName_s | Имя виртуальной машины |
| BootTime_t | Время загрузки |

### <a name="servicemapprocesscl-type-records"></a>Записи типа ServiceMapProcess_CL
В записях типа *ServiceMapProcess_CL* содержатся данные инвентаризации для подключенных по протоколу TCP процессов на серверах с программой Dependency Agent. У этих записей есть свойства, приведенные в таблице ниже.

| Свойство | ОПИСАНИЕ |
|:--|:--|
| type | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | Уникальный идентификатор для процесса в рабочей области |
| ResourceName_s | Уникальный идентификатор для процесса на компьютере, на котором он выполняется|
| MachineResourceName_s | Имя ресурса компьютера |
| ExecutableName_s | Имя исполняемого файла процесса |
| StartTime_t | Время запуска пула процессов |
| FirstPid_d | Первый идентификатор процесса в пуле процессов |
| Description_s | Описание процесса |
| CompanyName_s | Название организации |
| InternalName_s | Внутреннее имя |
| ProductName_s | Имя продукта |
| ProductVersion_s | Версия продукта |
| FileVersion_s | Версия файла |
| CommandLine_s | Командная строка |
| ExecutablePath _s | Путь к исполняемому файлу |
| WorkingDirectory_s | Рабочий каталог |
| UserName | Учетная запись, с которой выполняется процесс |
| UserDomain | Домен, в котором выполняется процесс |

## <a name="sample-log-searches"></a>Пример поисков журналов

### <a name="list-all-known-machines"></a>Список всех известных компьютеров
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Вывод сведений об объеме физической памяти для всех управляемых компьютеров
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s

### <a name="list-computer-name-dns-ip-and-os"></a>Список сведений об имени компьютера, DNS-имени, IP-адресе и ОС
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Поиск всех процессов с "sql" в командной строке
ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Поиск компьютера (самой последней записи) по имени ресурса
search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Поиск компьютера (самой последней записи) по IP-адресу
search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="list-all-known-processes-on-a-specified-machine"></a>Вывод списка всех известных процессов на определенном компьютере
ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="list-all-computers-running-sql"></a>Вывод списка всех компьютеров, на которых выполняется SQL
ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Вывод списка всех уникальных версий продукта cURL в центре обработки данных
ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Создание группы, объединяющей все компьютеры, на которых выполняется CentOS
ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Создание сводки исходящих подключений для группы компьютеров
```
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="next-steps"></a>Дополнительная информация
* Если вы еще не умеете создавать запросы в службе Log Analytics, узнайте, как делать это [на странице Log Analytics](../log-analytics/query-language/get-started-analytics-portal.md) на портале Azure.
* Узнайте больше о том, как [создавать поисковые запросы](../log-analytics/query-language/search-queries.md).