---
title: Решение для управления обновлениями в Azure
description: Цель этой статьи — помочь вам понять, как использовать решение по управлению обновлениями Azure для управления обновлениями на компьютерах Windows и Linux.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 06/28/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d12ce7b3416fac5de5cba9543f3bed5bcedf2012
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39115679"
---
# <a name="update-management-solution-in-azure"></a>Решение для управления обновлениями в Azure

С помощью решения по управлению обновлениями в службе автоматизации Azure можно управлять обновлениями операционной системы для компьютеров Windows и Linux, развернутых в Azure, локальных средах или других поставщиках облачных услуг. Благодаря ему вы сможете быстро оценить состояние доступных обновлений на всех компьютерах агентов и управлять установкой необходимых обновлений на серверах.

Вы можете включить управление обновлениями для виртуальных машин непосредственно из учетной записи службы автоматизации Azure. Сведения о включении управления обновлениями для виртуальных машин из учетной записи службы автоматизации см. в статье [Управление обновлениями для нескольких виртуальных машин Azure](manage-update-multi.md). Вы также можете включить управление обновлениями для одной виртуальной машины на странице виртуальной машины на портале Azure. Этот сценарий доступен для виртуальных машин [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) и [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management).

## <a name="solution-overview"></a>Обзор решения

Ниже перечислены конфигурации, которые используются компьютерами, управляемыми с помощью решения "Управление обновлениями", для выполнения развертываний оценок и обновлений:

* Microsoft Monitoring Agent (MMA) для Windows или Linux;
* служба настройки требуемого состояния PowerShell;
* гибридные рабочие роли Runbook службы автоматизации;
* службы Центра обновления Майкрософт или Windows Server Update Services (WSUS) для компьютеров с Windows.

На схеме ниже показано концептуальное представление потока данных и его поведения, а также, как решение анализирует и применяет обновления безопасности ко всем подключенным компьютерам Windows Server и Linux в рабочей области:

![Поток процесса управления обновлениями](media/automation-update-management/update-mgmt-updateworkflow.png)

После того как компьютер проверит соответствие обновлениям, агент переадресовывает сведения в Azure Log Analytics в пакетном режиме. На компьютере с Windows проверка на соответствия по умолчанию выполняется через каждые 12 часов.

Помимо расписания проверки при перезапуске MMA в течение 15 минут запускается проверка на соответствие обновлений. Это происходит перед установкой обновления и после нее.

На компьютере с Linux проверка на соответствия по умолчанию выполняется через каждые 3 часа. При перезапуске агента MMA проверка соответствия инициируется в течение 15 минут.

Решение формирует отчет по актуальности состояния компьютера в зависимости от настроенного для синхронизации источника. Если компьютер с Windows настроен на отправку отчетов в службы WSUS в зависимости от последней синхронизации WSUS с центром обновления Майкрософт, результаты центра обновления Майкрософт могут быть недостоверными. То же самое касается компьютеров с Linux, настроенных на отправку отчета в локальный, а не общедоступный репозиторий.

> [!NOTE]
> Для правильной передачи данных в службу решению управления обновлениями требуется, чтобы были включены определенные URL-адреса и порты. Дополнительные сведения о требованиях см. в разделе [Настройка сети](automation-hybrid-runbook-worker.md#network-planning).

Вы можете развернуть и установить обновления программного обеспечения на компьютерах, требующих обновлений с помощью запланированного развертывания. *Необязательные* обновления не включены в область развертывания для компьютеров с Windows. В область развертывания включаются только необходимые обновления. 

Запланированное развертывание определяет, какие целевые компьютеры будут получать применимые обновления. Для этого можно явным образом указать компьютеры или же выбрать [группу компьютеров](../log-analytics/log-analytics-computer-groups.md) на основе поиска по журналам определенного набора компьютеров. Вы также можете указать расписание для назначения и подтверждения периода времени, в течение которого могут быть установлены обновления. 

Обновления устанавливаются с помощью Runbook в службе автоматизации Azure. Эти модули Runbook нельзя просмотреть, и они не требуют настройки. При создании развертывания обновлений оно создает расписание, по которому в указанное время для компьютеров, включенных в развертывание, запускается главный Runbook обновлений. Этот Runbook запускает для каждого агента дочерний Runbook, который выполняет установку необходимых обновлений.

Целевой компьютер будет параллельно выполнять развертывания согласно дате и времени, указанным в развертывании обновлений. Перед установкой выполняется проверка, чтобы убедиться, что обновления по-прежнему необходимы. Если обновления для клиентских компьютеров WSUS не утверждены службой WSUS, развертывание обновлений завершится сбоем.

## <a name="clients"></a>Клиенты

### <a name="supported-client-types"></a>Поддерживаемые типы клиентов

В следующей таблице перечислены поддерживаемые операционные системы.

|Операционная система  |Примечания  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Поддерживает только оценки обновлений.         |
|Windows Server 2008 R2 с пакетом обновления 1 и более поздней версии     |Требуется .NET Framework 4.5 или более поздней версии. ([Скачать .NET Framework](/dotnet/framework/install/guide-for-developers).)<br/> Windows PowerShell, начиная с версии 4.0. ([Скачать WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855).)<br/> Для повышения надежности рекомендуем использовать Windows PowerShell 5.1.  ([Скачать WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616).)        |
|CentOS 6 (x86 или x64) и 7 (x64).      | У агентов Linux должен быть доступ к репозиторию обновлений. Для исправления на основе классификации требуется, чтобы программа yum вернула данные безопасности, которых в CentOS нет без дополнительной настройки.         |
|Red Hat Enterprise 6 (x86 или x64) и 7 (x64).     | У агентов Linux должен быть доступ к репозиторию обновлений.        |
|SUSE Linux Enterprise Server 11 (x86 или x64) и 12 (x64)     | У агентов Linux должен быть доступ к репозиторию обновлений.        |
|Ubuntu 14.04 LTS и 16.04 LTS (x86/x64)      |У агентов Linux должен быть доступ к репозиторию обновлений.         |

### <a name="unsupported-client-types"></a>Неподдерживаемые типы клиентов

В следующей таблице перечислены неподдерживаемые операционные системы:

|Операционная система  |Примечания  |
|---------|---------|
|Клиент Windows     | Клиентские операционные системы (например, Windows 7 и Windows 10) не поддерживаются.        |
|Nano Server Windows Server 2016     | Не поддерживается.       |

### <a name="client-requirements"></a>Требования к клиенту

#### <a name="windows"></a>Windows

На агентах Windows необходимо настроить связь с сервером служб WSUS или доступ к Центру обновления Майкрософт. Решение "Управление обновлениями" можно использовать вместе с System Center Configuration Manager. Дополнительные сведения о сценариях интеграции см. в разделе [Параметр Configuration](oms-solution-updatemgmt-sccmintegration.md#configuration). [Агент Windows](../log-analytics/log-analytics-agent-windows.md) является обязательным компонентом. При подключении виртуальной машины Azure этот агент устанавливается автоматически.

#### <a name="linux"></a>Linux

У компьютеров под управлением Linux должен быть доступ к репозиторию обновлений. Репозиторий обновлений может быть общедоступным или частным. Для взаимодействия с решением "Управление обновлениями" требуется TLS 1.1 или TLS 1.2. Это решение не поддерживает агент Operations Management Suite (OMS) для Linux, настроенный для отправки отчетов в несколько рабочих областей Log Analytics.

Дополнительные сведения по установке агента OMS для Linux и скачиванию последней версии см. [здесь](https://github.com/microsoft/oms-agent-for-linux). Дополнительные сведения по установке агента OMS для Windows см. в статье [Подключение компьютеров Windows к службе Log Analytics в Azure](../log-analytics/log-analytics-windows-agent.md).

## <a name="permissions"></a>Разрешения

Для создания развертываний обновлений и управления ими требуются определенные разрешения. Дополнительные сведения об этих разрешениях см. в разделе, посвященном [управлению обновлениями с доступом на основе ролей](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Компоненты решения

Решение состоит из приведенных ниже ресурсов. Они добавляются в учетную запись службы автоматизации. Эти ресурсы являются либо непосредственно подключенными агентами, либо они находятся в группе управления, связанной с Operations Manager.

### <a name="hybrid-worker-groups"></a>Группы гибридных рабочих ролей

После включения этого решения любой подключенный к рабочей области Log Analytics компьютер с Windows будет автоматически настроен в качестве гибридной рабочей роли Runbook для поддержки модулей Runbook, которые входят в это решение.

Для каждого компьютера с Windows, управляемого этим решением, эти роли будут указаны на странице **групп гибридных рабочих ролей** как **группа гибридных рабочих ролей системы** для учетной записи службы автоматизации. Используется соглашение об именовании *Имя узла FQDN_GUID*. Вы не можете выбрать эти группы с помощью модулей Runbook в своей учетной записи. При попытке произойдет сбой. Эти группы предназначены только для поддержки решения для управления.

Вы можете добавить компьютеры с Windows в группу гибридных рабочих ролей Runbook в учетной записи службы автоматизации, чтобы обеспечить поддержку модулей Runbook службы автоматизации, если вы используете одну и ту же учетную запись для решения и для участия в группе гибридных рабочих ролей Runbook. Эта функция добавлена в версии 7.2.12024.0 гибридной рабочей роли Runbook.

### <a name="management-packs"></a>Пакеты управления

Если группа управления System Center Operations Manager подключена к рабочей области Log Analytics, в Operations Manager будут установлены следующие пакеты. После добавления этого решения пакеты управления также будут установлены на компьютерах с Windows, подключенных напрямую. Управлять и настраивать эти пакеты управления не требуется.

* Пакет аналитики оценки обновления Microsoft System Center Advisor (Microsoft.IntelligencePacks.UpdateAssessment);
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration);
* пакет управления развертыванием обновлений.

Дополнительные сведения об обновлении пакетов управления для решений см. в статье [Подключение Operations Manager к Log Analytics](../log-analytics/log-analytics-om-agents.md).

> [!NOTE]
> Для полнофункционального управления системами с агентом Operations Manager при помощи решения "Управление обновлениями" агент необходимо обновить до Microsoft Monitoring Agent. Чтобы узнать, как обновить агент, см. [эту статью](/system-center/scom/deploy-upgrade-agents.md).

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Подтверждение подключения виртуальных машин, не относящихся к Azure

Чтобы подтвердить, что подключенные напрямую виртуальные машины взаимодействуют с Log Analytics, вы можете выполнить следующий поиск по журналу:

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Чтобы установить подключение агента к Log Analytics на компьютере с Windows, ознакомьтесь со следующей информацией:

1. На панели управления откройте **Microsoft Monitoring Agent**. На вкладке **Azure Log Analytics** агент отобразит сообщение: **The Microsoft Monitoring Agent has successfully connected to Log Analytics** (Microsoft Monitoring Agent успешно подключен к Log Analytics).
2. Откройте журнал событий Windows. Перейдите к **журналам приложения и служб или Operations Manager** и выполните поиск идентификатора события 3000 или 5002 в исходном **соединителе службы**. Эти события указывают, что компьютер был зарегистрирован для рабочей области Log Analytics и получает конфигурации.

Если агент не может взаимодействовать с Log Analytics, а агент настроен на взаимодействие с Интернетом через брандмауэр или прокси-сервер, вам необходимо подтвердить корректную настройку брандмауэра или прокси-сервера. Чтобы узнать, как проверить это, ознакомьтесь со статьей [Подключение компьютеров Windows к службе Log Analytics в Azure](../log-analytics/log-analytics-agent-windows.md) и [Сбор данных с компьютеров Linux, размещенных в вашем окружении](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Если при подключении этого решения системы Linux настроены для взаимодействия с прокси-сервером или шлюзом OMS, обновите разрешения *proxy.conf*, чтобы предоставить группе omiuser разрешение на чтение файла. Для этого выполните следующие команды:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Добавленные агенты Linux отобразят состояние **обновления** после оценки. Этот процесс может занять до 6 часов.

О том, как подтвердить, что группа управления Operations Manager взаимодействует с Log Analytics, описывается в разделе [Проверка интеграции Operations Manager с OMS](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-log-analytics).

## <a name="data-collection"></a>Сбор данных

### <a name="supported-agents"></a>Поддерживаемые агенты

В следующей таблице описаны подключенные источники, поддерживаемые этим решением.

| Подключенный источник | Поддерживаются | ОПИСАНИЕ |
| --- | --- | --- |
| Агенты Windows |Yes |Решение собирает сведения об обновлениях системы с агентов Windows и запускает установку требуемых обновлений. |
| Агенты Linux |Yes |Решение собирает сведения об обновлениях системы с агентов Linux и запускает установку требуемых обновлений для поддерживаемых дистрибутивов. |
| Группа управления Operations Manager |Yes |Решение собирает сведения о системных обновлениях с агентов, состоящих в подключенной группе обновления.<br/>Прямое подключение агента Operations Manager к Log Analytics не требуется. Данные пересылаются из группы управления в рабочую область Log Analytics. |

### <a name="collection-frequency"></a>Частота сбора

Дважды в день выполняется проверка каждого управляемого компьютера Windows. Каждые 15 минут вызывается API Windows, чтобы запросить время последнего обновления и определить, изменилось ли состояние. Если состояние изменилось, запускается проверка на соответствие. 

Каждые 3 часа выполняется проверка всех управляемых компьютеров Linux.

Отображение обновленных данных с управляемых компьютеров на панели мониторинга может занять от 30 минут до 6 часов.

## <a name="viewing-update-assessments"></a>Просмотр оценок обновления

Выберите **Управление обновлениями** в своей учетной записи службы автоматизации, чтобы просмотреть состояние компьютеров.

В этом представлении содержатся сведения о ваших компьютерах, отсутствующих обновлениях, развертываниях обновлений и запланированных развертываниях обновлений. В **столбце соответствия** указано время, когда компьютер в последний раз оценивался. В столбце **Готовность к обновлению агента** можно увидеть, работоспособен ли агент обновления. Если есть проблема, щелкните ссылку, чтобы перейти к документации по устранению неполадок, где можно найти шаги для устранения проблемы.

Чтобы запустить поиск по журналам, который возвращает информацию о компьютере, обновлении или развертывании, выберите элемент из списка. При этом откроется страница **поиска по журналам** с запросом на выбранный элемент:

![Представление по умолчанию решения "Управление обновлениями"](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>Установка обновлений

После оценки обновлений для всех компьютеров с Windows и Linux в рабочей области можно установить обязательные обновления, создав *развертывание обновлений*. Развертывание обновлений — это запланированная установка требуемых обновлений для одного или нескольких компьютеров. Вы можете указать дату и время развертывания, а также компьютер или группу компьютеров, которые будут включены в развертывание. Дополнительные сведения о группах компьютеров см. в статье [Использование групп компьютеров при поиске по журналам Log Analytics](../log-analytics/log-analytics-computer-groups.md).

 При включении групп компьютеров в развертывание обновлений они будут проанализированы один раз после создания расписания. Последующие изменения группы отображаться не будут. Чтобы обойти эту проблему, удалите запланированное развертывание обновлений, а затем повторно создайте его.

> [!NOTE]
> Виртуальные машины с Windows, развернутые по умолчанию из Azure Marketplace, настроены для получения автоматических обновлений из службы обновлений Windows. Поведение не изменяется при добавлении этого решения или виртуальных машин Windows в эту рабочую область. Если вы не управляете обновлениями с помощью этого решения, будет применено поведение по умолчанию (автоматическое применение обновлений).

Чтобы обновления применялись только в период обслуживания в Ubuntu, повторно настройте пакет unattended-upgrades и отключите автоматическое обновление. Дополнительные сведения о настройке пакета см. в разделе [Автоматические обновления](https://help.ubuntu.com/lts/serverguide/automatic-updates.html) руководства по Ubuntu Server.

Виртуальным машинам, которые созданы на основе доступных в Azure Marketplace предоставляемых по запросу образов Red Hat Enterprise Linux (RHEL), обеспечивается доступ к развернутому в Azure решению [Red Hat Update Infrastructure (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md). Любой другой дистрибутив Linux должен быть обновлен с помощью интернет-репозитория дистрибутивов посредством поддерживаемых методов.

## <a name="view-missing-updates"></a>Просмотр отсутствующих обновлений

Выберите **Отсутствующие обновления**, чтобы просмотреть список обновлений, отсутствующих на ваших компьютерах. Каждое обновление перечислено и может быть выбрано. Отображается информация о количестве компьютеров, которым оно требуется, операционная система и ссылка для получения дополнительных сведений. В области **Поиск журналов** отображаются дополнительные сведения об обновлениях.

## <a name="view-update-deployments"></a>Просмотр развертываний обновлений

Выберите вкладку **Развертывания обновлений**, чтобы просмотреть список существующих развертываний обновлений. Если выбрать какое-либо развертывание обновления в таблице, откроется область **Обновление запуска развертывания** для этого развертывания обновления.

![Обзор результатов развертывания обновлений](./media/automation-update-management/update-deployment-run.png)

## <a name="create-or-edit-an-update-deployment"></a>Создание или изменение развертывания обновлений

Чтобы создать развертывание обновления, выберите **Запланировать развертывание обновлений**. Откроется панель **Новое развертывание обновления**. Введите значения для свойств, описанных в следующей таблице и нажмите **Создать**:

| Свойство | Описание |
| --- | --- |
|ИМЯ |Уникальное имя для идентификации развертывания обновлений. |
|Операционная система| Выберите **Linux** или **Windows**.|
|Компьютеры, на которые нужно установить обновления |Щелкните сохраненный поиск или выберите **компьютер** в раскрывающемся списке и выберите отдельные компьютеры. |
|Классификации обновлений|Выберите все необходимые категории обновления. CentOS не поддерживает это без дополнительной настройки.|
|Исключаемые обновления|Введите обновления для исключения. Для Windows введите номер статьи базы знаний без префикса **KB**. Для Linux введите имя пакета или используйте подстановочный знак.  |
|Параметры расписания|Выберите время запуска, а затем — **однократное** применение или **повторяющееся**.|| Период обслуживания |Количество минут, установленное для обновлений. Значение должно составлять не менее 30 минут, но не более 6 часов. |

## <a name="update-classifications"></a>Классификации обновлений

В следующих таблицах содержатся списки классификаций обновлений в Управлении обновлениями с определениями для каждой классификации.

### <a name="windows"></a>Windows

|классификация;  |ОПИСАНИЕ  |
|---------|---------|
|критические обновления;     | Обновление для конкретной проблемы, которая относится к критической, не связанной с безопасностью ошибке.        |
|обновления для системы безопасности;     | Обновление для ошибки, связанной с безопасностью конкретного продукта.        |
|накопительные пакеты обновления;     | Накопительный набор исправлений, упакованных в один пакет для удобства развертывания.        |
|пакеты дополнительных компонентов;     | Новые функции продукта, которые распространяются вне выпуска продукта.        |
|пакеты обновления;     | Накопительный набор исправлений, применяемых к приложению.        |
|обновления определений;     | Обновление для вирусов или других файлов определений.        |
|Средства     | Служебная программа или функция, которая помогает выполнить одну или несколько задач.        |
|Обновления     | Обновление приложения или файла, установленного в настоящее время.        |

### <a name="linux"></a>Linux

|классификация;  |ОПИСАНИЕ  |
|---------|---------|
|критические обновления и обновления для системы безопасности;     | Обновления для конкретной проблемы или проблемы, связанной с безопасностью продукта.         |
|Другие обновления     | Все другие обновления, которые не являются критическими или обновлениями для системы безопасности.        |

В Linux решение по управлению обновлениями позволяет различать в облаке критически важные обновления и обновления безопасности при отображении данных оценки в связи с обогащением данных в облаке. Для исправления в решении по управлению обновлениями используются данные классификации, доступные на компьютере. В отличие от других дистрибутивов, в CentOS эти сведения недоступны без дополнительной настройки. Если у вас есть компьютеры CentOS, настроенные возвращать данные безопасности для следующей команды, решение по управлению обновлениями сможет выполнять исправления на основе классификаций.

```bash
sudo yum -q --security check-update
```

В настоящее время отсутствуют поддерживаемые методы настройки исходной доступности данных классификации в CentOS. Сейчас для клиентов, которые включили ее самостоятельно, предоставляется только возможная поддержка.

## <a name="ports"></a>порты;

Для Управления обновлениями требуются следующие адреса. Взаимодействие с этими адресами выполняется через порт 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

Дополнительные сведения о портах, которым необходима гибридная рабочая роль Runbook, см. в статье [Автоматизация ресурсов в центре обработки данных или облаке с помощью гибридной рабочей роли Runbook](automation-hybrid-runbook-worker.md#hybrid-worker-role).

При определении исключений рекомендуется использовать адреса из списка. Если вам нужны IP-адреса, вы можете скачать [список диапазонов IP-адресов центров обработки данных Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Файл обновляется еженедельно, и в нем отражаются развернутые в настоящее время диапазоны и все предстоящие изменения диапазонов IP-адресов.

## <a name="search-logs"></a>Поиск по журналам

Кроме подробных сведений, которые предоставляются на портале Azure, можно выполнить поиск по журналам. На страницах решений выберите **Log Analytics**. Откроется область **Поиск по журналам**.

Также вы можете узнать, как настроить запросы или использовать их с других клиентов, а также многое другое, ознакомившись с [документацией по API поиска Log Analytics](
https://dev.loganalytics.io/).

### <a name="sample-queries"></a>Примеры запросов

В следующих разделах приведены примеры запросов журнала для записей обновлений, которые собираются этим решением:

#### <a name="single-azure-vm-assessment-queries-windows"></a>Запросы оценки одной виртуальной машины Azure (Windows)

Замените значение VMUUID на GUID запрашиваемой виртуальной машины. Вы можете найти нужный VMUUID, выполнив следующий запрос в Log Analytics: `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Сводка об отсутствующих обновлениях

```
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="missing-updates-list"></a>Список отсутствующих обновлений

```
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

#### <a name="single-azure-vm-assessment-queries-linux"></a>Запросы оценки одной виртуальной машины Azure (Linux)

В некоторых дистрибутивах Linux наблюдается несоответствие [порядка байтов](https://en.wikipedia.org/wiki/Endianness) между значениями VMUUID, которое поступает из Azure Resource Manager и которое хранится в Log Analytics. Следующий запрос проверяет совпадение по порядку байтов. Чтобы вернулись правильные результаты, замените значения VMUUID форматом GUID с прямым и обратным порядком. Вы можете найти нужный VMUUID, выполнив следующий запрос в Log Analytics: `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Сводка об отсутствующих обновлениях

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Список отсутствующих обновлений

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

#### <a name="multi-vm-assessment-queries"></a>Запросы для оценки нескольких виртуальных машин

##### <a name="computers-summary"></a>Сводка по компьютерам

```
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeeedOtherUpdatesCount=sum(computersNeeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

##### <a name="missing-updates-summary"></a>Сводка об отсутствующих обновлениях

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="computers-list"></a>Список компьютеров

```
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

##### <a name="missing-updates-list"></a>Список отсутствующих обновлений

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="integrate-with-system-center-configuration-manager"></a>Интеграция с System Center Configuration Manager

Клиенты, которые инвестировали в System Center Configuration Manager для управления ПК, серверами и мобильными устройствами, также полагаются на надежность и зрелость Configuration Manager, чтобы помочь им управлять обновлениями программного обеспечения. Configuration Manager входит в их цикл управления обновлением программного обеспечения (SUM).

Сведения об интеграции решения по управлению с Sytem Center Configuration Manager см. в статье [Интеграция Integrate System Center Configuration Manager с решением "Управление обновлениями"](oms-solution-updatemgmt-sccmintegration.md).

## <a name="patch-linux-machines"></a>Исправление компьютеров Linux

В следующих разделах объясняются возможные проблемы, которые могут возникнуть при установке исправлений Linux.

### <a name="unexpected-os-level-upgrades"></a>Непредвиденные обновления на уровне ОС

В некоторых вариантах Linux, таких как Red Hat Enterprise Linux, обновления на уровне ОС можно реализовывать через пакеты. Это может привести к запуску управления обновлениями, когда изменяется номер версии операционной системы. Так как решение "Управление обновлениями" использует те же методы для обновления пакетов, что и администратор на компьютерах Linux в локальной среде, такое поведение является преднамеренным.

Чтобы избежать обновления версии ОС при запуске решения "Управление обновлениями", нужно использовать функцию **исключения**.

В Red Hat Enterprise Linux именем пакета для исключения должно быть имя redhat-release-server.x86_64.

![Исключаемые пакеты для Linux](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>Критически важные исправления или исправления системы безопасности не применяются

При развертывании обновлений на компьютер Linux вы можете выбрать категории обновлений. Это позволит отфильтровать обновления до тех, которые соответствуют указанным критериям. Этот фильтр применяется локально на компьютере при развертывании обновления.

Так как решение "Управление обновлениями" добавляет новые функции к обновлениям в облаке, некоторые обновления могут быть помечены в таком решении как обновления, влияющие на безопасность, хотя на локальном компьютере эти сведения отсутствуют. В результате, если вы применяете критические обновления для компьютера Linux, вы можете столкнуться с обновлениями, которые не помечены как влияющие на безопасность на таком компьютере, но при этом они не будут применены.

Тем не менее решение "Управление обновлениями" может по-прежнему сообщать о том, что обновления не совместимы с этим компьютером, так как оно содержит дополнительные сведения о соответствующем обновлении.

Развертывание обновлений на основе их классификации не поддерживается в CentOS без дополнительной настройки. В SUSE выбор *только* "Другие обновления" в качестве классификации может привести к тому, что некоторые обновления безопасности также будут установлены, если сначала потребуются обновления безопасности, связанные с zypper (диспетчер пакетов) или его зависимостями. Это ограничение zypper. В некоторых случаях вам может потребоваться перезапустить развертывание обновлений, чтобы проверить журнал обновлений.

## <a name="troubleshoot"></a>Устранение неполадок

Чтобы узнать, как устранить неполадки с решением управления обновлениями, ознакомьтесь с [этой статьей](troubleshoot/update-management.md).

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с руководством ниже, чтобы узнать, как управлять обновлениями для виртуальных машин Windows.

> [!div class="nextstepaction"]
> [Устранение неполадок при изменениях в среде](automation-tutorial-update-management.md)

* Используйте поиск по журналам в [Log Analytics](../log-analytics/log-analytics-log-searches.md), чтобы просматривать подробные данные об обновлениях.
* [Создавайте оповещения](../log-analytics/log-analytics-alerts.md) об обнаружении важных критических обновлений, отсутствующих на компьютерах, или отключении автоматических обновлений на компьютере.
