---
title: Руководство по управлению устройствами с помощью решения для удаленного мониторинга в Azure | Документация Майкрософт
description: В этом руководстве объясняется, как управлять устройствами с помощью акселератора решений для удаленного мониторинга.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/19/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: cd8e8c1fe1b77113968b7af635f45f9e0e077b7c
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159203"
---
# <a name="tutorial-configure-and-manage-devices-connected-to-your-monitoring-solution"></a>Учебник. Настройка и администрирование устройств, подключенных к решению для мониторинга

В этом руководстве показано, как настраивать подключенные устройства Интернета вещей и управлять ими с помощью акселератора решений для удаленного мониторинга. Вы добавите новое устройство в акселератор решения, настроите это устройство и обновите его встроенное ПО.

Компания Contoso заказала новые машины для расширения производства. Пока компания ждет доставки машин, нужно выполнить моделирование, чтобы проверить поведение решения. Для проведения моделирования необходимо добавить новое имитированное устройство "Двигатель" в акселератор решений для удаленного мониторинга и проверить правильность его реагирования на действия и обновления конфигурации.

Для расширенной настройки устройств и управления ими в акселераторе решений для удаленного мониторинга используются компоненты Центра Интернета вещей, такие как [задания](../iot-hub/iot-hub-devguide-jobs.md) и [прямые методы](../iot-hub/iot-hub-devguide-direct-methods.md). Хотя в этом руководстве применяются имитированные устройства, разработчик устройств может реализовать прямые методы на [физическом устройстве, подключенном к акселератору решений для удаленного мониторинга](iot-accelerators-connecting-devices.md).

Изучив это руководство, вы:

>[!div class="checklist"]
> * Подготовка имитированного устройства.
> * протестируете имитированное устройство;
> * обновите встроенное ПО устройства;
> * Перенастройка устройства.
> * упорядочите устройства.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [iot-iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="add-a-simulated-device"></a>Добавление имитированного устройства

Откройте страницу **Устройства** в решении и щелкните **+ Новое устройство**.

[![Подготовка имитированного устройства](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

На панели **Новое устройство** выберите вариант **Имитированное**, оставьте число подготавливаемых устройств равным **1**, выберите модель устройства **Неисправный модуль** и нажмите кнопку **Применить**, чтобы создать имитированное устройство:

[![Подготовка имитированного устройства "Двигатель"](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>Тестирование имитированного устройства

Чтобы проверить, отправляет ли имитированное устройство "Двигатель"данные телеметрии и сообщает ли оно значения свойств, выберите его в списке устройств на странице **Устройства**. Актуальные сведения о двигателе отображаются на панели **Сведения об устройстве**.

[![Просмотр сведений об имитированном устройстве "Двигатель"](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

На панели **Сведения об устройстве** проверьте, отправляет ли новое устройство данные телеметрии. Для просмотра поступаемого с устройства потока телеметрических данных о вибрации выберите пункт **Вибрация**.

[![Выбор потока телеметрической информации для просмотра](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

На панели **Сведения об устройстве** содержатся и другие сведения об устройстве, например значения тегов, методы, которые оно поддерживает, и свойства, о которых сообщает устройство.

Чтобы просмотреть подробные диагностические сведения, прокрутите вниз содержимое панели **Сведения об устройстве** и изучите данные в разделе **Диагностика**.

## <a name="act-on-a-device"></a>Работа с устройством

Чтобы проверить, правильно ли имитированное устройство "Двигатель" реагирует на действия, инициируемые с панели мониторинга, выполните метод **FirmwareUpdate**. Для работы с устройством с помощью метода выберите его в списке устройств и щелкните **Задания**. Вы можете выбрать несколько устройств для одновременной работы с ними. На панели **Задания** выберите **Выполнить метод**. Для модели устройства **Двигатель** определено три метода: **FirmwareUpdate**, **FillTank** и **EmptyTank**.

[![Методы устройства "Двигатель"](./media/iot-accelerators-remote-monitoring-manage/devicesmethods-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmethods-expanded.png#lightbox)

Выберите метод **FirmwareUpdate**, задайте имя задания **UpdateEngineFirmware**, укажите версию встроенного ПО **2.0.0**, задайте код URI встроенного ПО **http://contoso.com/engine.bin**, а затем нажмите кнопку **Применить**:

[![Метод для планирования обновления встроенного ПО](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatejob-inline.png)](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatejob-expanded.png#lightbox)

Для отслеживания состояния задания щелкните **Просмотреть состояние задания**.

[![Отслеживание задания обновления встроенного ПО по расписанию](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatestatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatestatus-expanded.png#lightbox)

Когда выполнение задания завершится, вернитесь на страницу **Устройства**. Для устройства "Двигатель" будет указана новая версия встроенного ПО.

Если на странице **Устройства** выбрано несколько устройств разных типов, вы все же можете создать задание для выполнения метода на этих устройствах. На панели **Задания** отображаются только те методы, которые являются общими для всех выбранных устройств.

## <a name="reconfigure-a-device"></a>Перенастройка устройства

Чтобы проверить, можно ли изменить свойства конфигурации двигателя, выберите его в списке устройств на странице **Устройства**. Затем щелкните **Задания** и выберите пункт **Перенастроить**. На панели заданий отображаются значения свойств выбранного устройства, которые можно изменить:

[![Перенастройка устройства](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

Чтобы изменить расположение двигателя, задайте имя задания **UpdateEngineLocation**, укажите долготу **-122,15**, расположение **Фабрика 2**, широту **47,62**, а затем нажмите кнопку **Применить**:

[![Обновление значения для свойства устройства](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

Для отслеживания состояния задания щелкните **Просмотреть состояние задания**.

[![Обновление значения для свойства устройства](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

Когда выполнение задания завершится, перейдите на страницу **Панель мониторинга**. Устройство "Двигатель" отображается в новом месте:

[![Просмотр расположения двигателя](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>Упорядочение устройств

Чтобы упростить организацию и управление устройствами, оператор может пометить их тегом с именем команды. У Contoso есть две различные команды для выездного обслуживания:

* команда обслуживания автомобилей обслуживает грузовики и прототипы устройств.
* команда обслуживания зданий управляет холодильниками, лифтами и двигателями;

Чтобы отобразить все устройства, перейдите на страницу **Устройства** и выберите фильтр **Все устройства**:

[![Показать все устройства](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>Добавление тегов

Выберите все устройства **Грузовики** и **Прототипы**. Щелкните **Задания**.

На панели **Задания** выберите **Тег**, присвойте заданию имя **AddConnectedVehicleTag**, а затем добавьте текстовый тег с именем **FieldService** и значением **ConnectedVehicle**. Нажмите кнопку **Применить**:

[![Добавление тегов к устройствам-прототипам и грузовикам](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

На странице устройств выберите устройства **Холодильник**, **Лифт** и **Двигатель**. Щелкните **Задания**.

На панели **Задания** выберите **Тег**, присвойте заданию имя **AddSmartBuildingTag**, а затем добавьте текстовый тег с именем **FieldService** и значением **SmartBuilding**. Нажмите кнопку **Применить**:

[![Добавление тегов к холодильникам, двигателям и лифтам](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>Создание фильтров

Теперь значения тегов можно использовать для создания фильтров. На странице **Устройства** выберите **Управление группами устройств**:

[![Управление группами устройств](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

Создайте текстовый фильтр, который использует имя тега **FieldService** и значение **SmartBuilding** в условии. Сохраните фильтр как **Smart Building** (Интеллектуальное здание):

[![Создание фильтра Smart Building](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

Создайте текстовый фильтр, который использует имя тега **FieldService** и значение **ConnectedVehicle** в условии. Сохраните фильтр как **Connected Vehicle** (Подключенный автомобиль).

[![Создание фильтра Connected Vehicle](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

Теперь оператор Contoso может запрашивать устройства на основе рабочей группы.

[![Создание фильтра Connected Vehicle](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

[!INCLUDE [iot-iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве было показано, как настраивать устройства, подключенные к акселератору решений для удаленного мониторинга, и управлять ими. Чтобы узнать, как выявлять и устранять проблемы с подключенными устройствами с помощью акселератора решений, перейдите к следующему руководству.

> [!div class="nextstepaction"]
> [Определение и устранение проблем с устройствами, подключенными к решению для мониторинга, с помощью оповещений устройств](iot-accelerators-remote-monitoring-maintain.md)
