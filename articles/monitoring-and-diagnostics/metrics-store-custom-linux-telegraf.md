---
title: Сбор пользовательских метрик для виртуальной машины Linux с помощью InfluxData Telegraf Agent
description: Сбор пользовательских метрик для виртуальной машины Linux с помощью InfluxData Telegraf Agent
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 651706b269cf24eca85e0601384ef63cb6ed06a2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990711"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Сбор пользовательских метрик для виртуальной машины Linux с помощью InfluxData Telegraf Agent

Azure Monitor позволяет собирать пользовательские метрики с помощью телеметрии приложения, агента, запущенного в ресурсах Azure, или даже систем внешнего мониторинга и отправлять их непосредственно в Azure Monitor. В этой статье основное внимание уделено инструкциям по развертыванию [InfluxData](https://www.influxdata.com/) Telegraf Agent на виртуальной машине Linux в Azure и настройке агента для публикации метрик в Azure Monitor. 

## <a name="influxdata-telegraf-agent"></a>InfluxData Telegraf Agent 

[Telegraf](https://docs.influxdata.com/telegraf/v1.7/) — это агент, управляемый подключаемым модулем, который позволяет собирать метрики из более чем 150 различных источников. В зависимости от того, какие рабочие нагрузки выполняются на виртуальной машине (например, MySQL, NGINX, Apache и т.д.), в агенте можно настроить использование специализированных входных подключаемых модулей для сбора метрик. Выходные подключаемые модули позволяют агенту выполнять запись в места назначения на ваш выбор. Агент Telegraf интегрирован непосредственно с REST API пользовательских метрик Azure Monitor и поддерживает "выходной подключаемый модуль Azure Monitor". Это позволяет агенту собирать метрики конкретной рабочей нагрузки на виртуальной машине Linux и отправлять их в качестве пользовательских метрик в Azure Monitor. 

 ![Обзор агента Telegraph](./media/metrics-store-custom-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Отправка пользовательских метрик 

В рамках этого руководства мы развернем виртуальную машину Linux под управлением операционной системы Ubuntu 16.04 LTS. Агент Telegraf поддерживается для большинства операционных систем Linux. Пакеты Debian и RPM с распакованными двоичными файлами Linux доступны на портале загрузки InfluxData. Дополнительные инструкции по установке и доступные варианты установки Telegraf см. в этом руководстве по установке. 

Войдите на [портал Azure](https://portal.azure.com).

Создание виртуальной машины Linux: 

1. В области навигации слева выберите  **Создать ресурс** . 
1. Введите в поле поиска *виртуальная машина*.  
1. Выберите *Ubuntu 16.04 LTS* и нажмите кнопку **Создать**. 
1. Укажите имя виртуальной машины, например  *MyTelegrafVM*.  
1. Сохраните тип диска **SSD** и укажите **имя пользователя**, например  *azureuser*. 
1. В области  *Тип проверки подлинности* выберите  **Пароль**, а затем введите пароль, который будет использоваться позже для SSH в этой виртуальной машине. 
1. Выберите  **Create new resource group** (Создать группу ресурсов) и укажите имя, например  *myResourceGroup*.  Выберите нужное расположение и нажмите кнопку  **ОК**. 

     ![Создание виртуальной машины Ubuntu](./media/metrics-store-custom-linux-telegraf/create-vm.png)

1. Выберите размер виртуальной машины. Вы можете установить фильтр в области "Тип вычислений" или "Тип диска". 

     ![Обзор агента Telegraph. Размер виртуальной машины.](./media/metrics-store-custom-linux-telegraf/vm-size.png)

1. На странице  **Параметры** щелкните  **Сеть** > **Группа безопасности сети** > **Выберите общедоступные входящие порты** и укажите  *HTTP*  и  *SSH (22)*. Сохраните остальные значения по умолчанию и нажмите кнопку  **ОК**. 

1. На странице сводной информации выберите "Создать", чтобы начать развертывание виртуальной машины. 

1. Новая виртуальная машина закрепляется на панели мониторинга портала Azure. Когда развертывание завершится, автоматически отобразятся сводные сведения о виртуальной машине. 

1. В колонке виртуальной машины перейдите на вкладку **Удостоверение** и выберите значение *Вкл.* для назначаемого системой удостоверения в вашей виртуальной машине. 
 
![временная замена](./media/metrics-store-custom-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Подключение к виртуальной машине 

Создайте SSH-подключение к виртуальной машине. Нажмите кнопку "Подключиться" на странице обзора виртуальной машины. 

![временная замена](./media/metrics-store-custom-linux-telegraf/connect-VM-button2.png)

На странице подключения к виртуальной машине сохраните значения по умолчанию, чтобы использовать подключение по DNS-имени через порт 22. В поле Login using VM local account  (Вход с использованием локальной учетной записи виртуальной машины) представлена команда для подключения. Нажмите кнопку рядом, чтобы скопировать эту команду. Ниже представлен пример команды подключения по SSH: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Вставьте команду подключения по SSH в оболочку, например, Azure Cloud Shell или Bash на Ubuntu в Windows, или используйте клиент SSH, чтобы создать подключение. 

## <a name="install-and-configure-telegraf"></a>Установка и настройка Telegraf 

Чтобы установить пакет Telegraf Debian на виртуальной машине, выполните следующие команды в рамках сеанса SSH: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Файл конфигурации Telegraf определяет операции Telegraf. Пример файла конфигурации по умолчанию установлен по пути "/etc/telegraf/telegraf.conf". В примере файла конфигурации перечислены все возможные входные и выходные подключаемые модули. Тем не менее мы собираемся создать пользовательский файл конфигурации и настроить агент для его использования, выполнив следующие команды. 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]
> Приведенные выше команды включают только два входных подключаемых модуля "cpu" и "mem", вы можете добавить больше входных подключаемых модулей (Docker, NGINX, MySQL и т.д.) в зависимости от рабочей нагрузки, выполняемой на компьютере. Полный список входных подключаемых модулей см. здесь. 

Наконец для запуска агента с помощью новой конфигурации мы принудительно остановим и запустим агент, выполнив следующие команды. 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Теперь агент будет собирать метрики из каждого из указанных входных подключаемых модулей и выдавать их в Azure Monitor. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Отображение метрик Telegraf на портале Azure 

1. Откройте [портал Azure](https://portal.azure.com). 

1. Перейдите к новой вкладке "Монитор" и выберите  **Метрики**.  
     ![временная замена](./media/metrics-store-custom-linux-telegraf/metrics.png)

1. Выберите свою виртуальную машину в селекторе ресурсов.

     ![временная замена](./media/metrics-store-custom-linux-telegraf/metric-chart.png)

1. Выберите пространство имен *Telegraf/CPU* (Telegraf/ЦП) и выберите метрику *usage_system*. Вы можете отфильтровать по измерениям в этой метрике или разделить их.  

     ![временная замена](./media/metrics-store-custom-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Дополнительная конфигурация 

В приведенном выше руководстве содержатся сведения о том, как настроить агент Telegraf для сбора метрик из нескольких основных входных подключаемых модулей. Агент Telegraf поддерживает более 150 входных подключаемых модулей, некоторые из них поддерживают дополнительные варианты конфигурации. Компания InfluxData опубликовала [список поддерживаемых подключаемых модулей](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) и инструкции по [их настройке](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/).  

Кроме того, в этом пошаговом руководстве разрешено использовать агент Telegraf для выдачи метрик виртуальной машине, на которой развернут агент. Агент Telegraf можно также использовать как сборщик и сервер пересылки метрик для других ресурсов. Сведения от том, как настроить агент для выдачи метрик для других ресурсов Azure, см. в статье [Azure Monitor Custom Metrics Output for Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md) (Выходные данные пользовательских метрик Azure Monitor для Telegraf).  

## <a name="clean-up-resources"></a>Очистка ресурсов 

Если группа ресурсов, виртуальная машина и все связанные с ними ресурсы вам больше не требуются, их можно удалить. Для этого выберите группу ресурсов для виртуальной машины, выберите действие "Удалить" и подтвердите имя удаляемой группы ресурсов. 

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения о настраиваемых метриках см. в [этой статье](metrics-custom-overview.md).


