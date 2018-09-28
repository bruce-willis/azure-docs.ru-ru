---
title: Обзор расширения Системы диагностики Azure
description: Диагностику Azure можно использовать для отладки, оценки производительности, мониторинга, а также анализа трафика в облачных службах, на виртуальных машинах и в Service Fabric.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: ef1422db799db6d635ad9f03908e3a34f312e408
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974244"
---
# <a name="what-is-azure-diagnostics-extension"></a>Расширение системы диагностики Microsoft Azure
Расширение диагностики Azure — это агент, который позволяет выполнять сбор диагностических данных о развернутом приложении. Можно использовать модуль диагностики из различных источников. Сейчас поддерживаются веб-роль и рабочая роль облачной службы Azure (классические), виртуальные машины, масштабируемые наборы виртуальных машин и Service Fabric. Для других служб Azure предусмотрены другие методы диагностики. Ознакомьтесь с [общими сведениями о мониторинге в Azure](monitoring-overview.md).

## <a name="linux-agent"></a>Агент Linux
[Linux-версия расширения](../virtual-machines/linux/diagnostic-extension.md) доступна для виртуальных машин под управлением ОС Linux. Собранные данные статистики и поведения могут отличаться от тех, которые были собраны в версии для Windows.

## <a name="data-you-can-collect"></a>Собираемые данные
Расширение системы диагностики Azure может собирать следующие типы данных:

| источник данных | ОПИСАНИЕ |
| --- | --- |
| Счетчики производительности |Системные и пользовательские счетчики производительности |
| Журналы приложений |Сообщения трассировки, записанные вашим приложением |
| Журналы событий Windows |Информация, отправляемая системой ведения журналов событий Windows |
| Источник событий .NET |События записи кода с использованием класса .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) |
| Журналы IIS |Информация о веб-сайтах IIS |
| Трассировка событий Windows на основе манифестов |События трассировки событий Windows, созданные любым процессом. (1) |
| Аварийные дампы |Информация о состоянии процесса в случае сбоя приложения |
| Пользовательские журналы ошибок |Журналы, созданные вашим приложением или службой |
| Журналы инфраструктуры системы диагностики Azure |Информация о самой системе диагностики |

(1) Чтобы получить список поставщиков трассировки событий Windows, выполните `c:\Windows\System32\logman.exe query providers` в окне консоли на компьютере, с которого требуется собирать информацию.

## <a name="data-storage"></a>Хранилище данных
Расширение хранит свои данные в указанной [учетной записи службы хранилища Azure](azure-diagnostics-storage.md).

Также данные можно отправить в [Application Insights](../application-insights/app-insights-cloudservices.md). Другим вариантом является их потоковая передача в [концентратор событий](../event-hubs/event-hubs-what-is-event-hubs.md), позволяющий отправить данные службам мониторинга, не связанным с Azure.

### <a name="azure-monitor"></a>Azure Monitor
Вы также можете отправить данные в Azure Monitor. Сейчас приемник Azure Monitor работает только с данными счетчиков производительности. Он позволяет направлять данные счетчиков производительности с виртуальной машины, из масштабируемого набора виртуальных машин или облачной службы в Azure Monitor в виде пользовательских метрик. Приемник Azure Monitor поддерживает такие функции:
* Получение данных всех счетчиков производительности, отправляемых в Azure Monitor с помощью [API-метрик Azure Monitor](https://docs.microsoft.com/rest/api/monitor/).
* Настройка оповещений для всех данных счетчиков производительности, отправляемых в Azure Monitor, с помощью нового [унифицированного интерфейса оповещений](monitoring-overview-unified-alerts.md) в Azure Monitor.
* Трактование оператора подстановочного знака в счетчиках производительности как экземпляра в метрике.  Так, если вы собираете данные счетчика "LogicalDisk(\*)/DiskWrites/sec" (Количество записей на логический диск \* в секунду), вы можете отфильтровать или разделить эти данные по отдельным экземплярам для каждого логического диска виртуальной машины (C:, D: и т. д.), чтобы построить график или настроить оповещения.

Дополнительные сведения о том, как настроить этот приемник, см. в статье [Схема конфигурации системы диагностики Azure версии 1.3 и более поздней](azure-diagnostics-schema-1dot3-and-later.md).

## <a name="versioning-and-configuration-schema"></a>Управление версиями и конфигурация схемы
Дополнительные сведения см. в статье [Azure Diagnostics Version History and Schema](azure-diagnostics-versioning-history.md) (Журнал и схема версий системы диагностики Azure).


## <a name="next-steps"></a>Дополнительная информация
Выберите службы, в которых вы хотите собирать данные диагностики, и выполните действия, описанные в перечисленных ниже статьях. Справку по отдельным задачам см. в общих статьях о диагностике Azure.

## <a name="cloud-services-using-azure-diagnostics"></a>Облачные службы с использованием диагностики Azure
* Если используется Visual Studio, см. статью [Отладка облачной службы или виртуальной машины Azure в Visual Studio](../vs-azure-tools-debug-cloud-services-virtual-machines.md). В остальных случаях см. следующие статьи:
* [Мониторинг облачных служб](../cloud-services/cloud-services-how-to-monitor.md)
* [Включение системы диагностики Azure в облачных службах Azure](../cloud-services/cloud-services-dotnet-diagnostics.md)

Более подробные сведения см. в статьях:

* [Application Insights для облачных служб Azure](../application-insights/app-insights-cloudservices.md)
* [Трассировка потока в приложении облачных служб с помощью системы диагностики Azure](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Включение системы диагностики Azure на виртуальной машине под управлением Windows с помощью PowerShell](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Виртуальные машины
* Если используется Visual Studio, см. статью [Отладка облачной службы или виртуальной машины Azure в Visual Studio](../vs-azure-tools-debug-cloud-services-virtual-machines.md). В остальных случаях см. следующие статьи:
* [Включение диагностики на виртуальных машинах Azure](../virtual-machines-dotnet-diagnostics.md)

Более подробные сведения см. в статьях:

* [Включение системы диагностики Azure на виртуальной машине под управлением Windows с помощью PowerShell](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Создание виртуальной машины Windows с мониторингом и диагностикой с использованием шаблона Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
Начните со статьи [Мониторинг и диагностика состояния служб в локальной среде разработки](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). В дереве навигации слева от нее вы найдете ссылки на множество других статей по диагностике Service Fabric.

## <a name="general-articles"></a>Общие статьи
* Узнайте, как [использовать счетчики производительности в Azure](../cloud-services/diagnostics-performance-counters.md).
* Если возникнут проблемы с запуском диагностики или поиском данных в таблицах хранилища Azure, см. статью [Устранение неполадок с помощью системы диагностики Azure](azure-diagnostics-troubleshooting.md).
