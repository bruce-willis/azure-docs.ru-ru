---
title: Использование оповещений и устранение проблем на устройствах с помощью решения для удаленного мониторинга в Azure | Документы Майкрософт
description: В этом руководстве объясняется, как использовать оповещения для выявления и устранения проблем на устройствах, подключенных к акселератору решений для удаленного мониторинга.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/18/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 9607705220450b30d2ffaf0f2be9fa2a5664b879
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081794"
---
# <a name="troubleshoot-and-remediate-device-issues"></a>Устранение неполадок на устройстве

В этом руководстве показано, как выявлять и устранять проблемы на подключенных устройствах Интернета вещей с помощью акселератора решений для удаленного мониторинга. С помощью оповещений в панели мониторинга акселератора решений вы можете выявлять проблемы, а затем устранять их с помощью удаленных заданий.

Contoso тестирует новый **прототип** устройства в поле. Оператор Contoso во время тестирования замечает, что **прототип** устройства неожиданно активирует на панели мониторинга оповещение о температуре. Теперь следует изучить реакцию на событие неисправного **прототипа** устройства и устранить соответствующую проблему.

Изучив это руководство, вы:

>[!div class="checklist"]
> * Анализ оповещения от устройства
> * Устранение проблем на устройстве

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством вам понадобится развернутый экземпляр акселератора решений для удаленного мониторинга в подписке Azure.

Если акселератор решений для удаленного мониторинга еще не развернут, выполните краткое руководство по [развертыванию облачного решения для удаленного мониторинга](quickstart-remote-monitoring-deploy.md).

## <a name="investigate-an-alert"></a>Изучение оповещения

На странице**Панель мониторинга** появились непредвиденные оповещения о температуре, поступающие из правила, связанного с **прототипом** устройства:

[![Оповещения на панели мониторинга](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-expanded.png#lightbox)

Чтобы выяснить причину неполадки, щелкните параметр**Explore Alert** (Просмотреть оповещение) рядом с оповещением:

[![Просмотр оповещений на панели мониторинга](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-expanded.png#lightbox)

В подробном представлении оповещения отображаются такие сведения:

* Время активации оповещения.
* Сведения о состоянии устройств, связанных с оповещением.
* Данные телеметрии с устройства, связанного с оповещением.

[![Сведения об оповещении](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-expanded.png#lightbox)

Чтобы подтвердить оповещение, выберите все элементы **Alert occurrences** (Вхождения оповещений) и затем **Acknowledge** (Подтвердить). Таким образом вы дадите другим операторам знать, что ознакомились с оповещением и работаете над ним:

[![Подтверждение оповещения](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-expanded.png#lightbox)

Когда вы подтвердите оповещение, состояние экземпляра изменится на **Acknowledged** (Подтверждено).

В списке вы увидите **прототип** устройства, отвечающий за оповещение о температуре:

[![Список устройств, вызвавших оповещение](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-expanded.png#lightbox)

## <a name="resolve-the-issue"></a>Устранение проблемы

Для устранения проблемы с **прототипом** устройства необходимо вызвать метод **DecreaseTemperature** на устройстве.

Для работы с устройством выберите его в списке устройств и нажмите **Задания**. Для модели устройства **Прототип** указано шесть методов, которые должно поддерживать устройство:

[![Методы, которые поддерживает устройство](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-expanded.png#lightbox)

Выберите **DecreaseTemperature** и задайте имя задания **DecreaseTemperature**. Нажмите кнопку **Применить**:

[![Создание задания по уменьшению температуры](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-expanded.png#lightbox)

Для отслеживания состояния задания щелкните **Просмотреть состояние задания**. В представлении **Задания** можно отследить все задания и вызовы методов в решении:

[![Мониторинг задания по уменьшению температуры](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-expanded.png#lightbox)

Чтобы убедиться, что температура устройства снизилась, просмотрите данные телеметрии на странице **Панель мониторинга**:

[![Просмотр данных о снижении температуры](./media/iot-accelerators-remote-monitoring-maintain/jobresult-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/jobresult-expanded.png#lightbox)

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве описывается, как использовать оповещения для выявления проблем на устройствах, а также какие действия следует предпринять для их устранения. Сведения о подключении физического устройства к акселератору решений см. в соответствующих статьях с инструкциями.

После того как вы ознакомились с принципами устранения проблем, далее рекомендуется ознакомиться с руководством [Подключение устройства к акселератору решений для удаленного мониторинга](iot-accelerators-connecting-devices.md).
