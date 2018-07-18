---
title: Управление устройствами с помощью решения для удаленного мониторинга в Azure | Документы Майкрософт
description: В этом руководстве объясняется, как управлять устройствами с помощью акселератора решений для удаленного мониторинга.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/12/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 63baf6397b2542311525bac740c50b5eacbd35cf
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097433"
---
# <a name="tutorial-configure-and-manage-devices-connected-to-your-monitoring-solution"></a>Учебник. Настройка и администрирование устройств, подключенных к решению для мониторинга

В этом руководстве показано, как настраивать подключенные устройства Интернета вещей и управлять ими с помощью акселератора решений для удаленного мониторинга. Вы добавите новое устройство в акселератор решений, настроите его и обновите встроенное ПО устройства.

Компания Contoso заказала новые машины для расширения производства. Пока компания ждет доставки машин, нужно выполнить моделирование, чтобы проверить поведение решения. Для проведения моделирования необходимо добавить новое имитированное устройство в акселератор решений для удаленного мониторинга и проверить правильность его реагирования на действия и обновления конфигурации.

Для расширенной настройки устройств и управления ими в акселераторе решений для удаленного мониторинга используются компоненты Центра Интернета вещей, такие как [задания](../iot-hub/iot-hub-devguide-jobs.md) и [прямые методы](../iot-hub/iot-hub-devguide-direct-methods.md). Хотя в этом руководстве применяются имитированные устройства, разработчик устройств может реализовать прямые методы на [физическом устройстве, подключенном к акселератору решений для удаленного мониторинга](iot-accelerators-connecting-devices.md).

В этом руководстве вы:

>[!div class="checklist"]
> * подготовите имитированное устройство;
> * протестируете имитированное устройство;
> * обновите встроенное ПО устройства;
> * перенастроите устройство;
> * упорядочите устройства.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам понадобится развернутый экземпляр акселератора решений для удаленного мониторинга в подписке Azure.

Если акселератор решений для удаленного мониторинга еще не развернут, выполните краткое руководство по [развертыванию облачного решения для удаленного мониторинга](quickstart-remote-monitoring-deploy.md).

## <a name="add-a-simulated-device"></a>Добавление имитированного устройства

Откройте страницу **Устройства** в решении и щелкните **+ Новое устройство**.

[![Подготовка имитированного устройства](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

На панели **Новое устройство** выберите вариант **Имитированное**, оставьте число подготавливаемых устройств равным **1**, выберите модель устройства **Неисправный модуль** и нажмите кнопку **Применить**, чтобы создать имитированное устройство:

[![Подготовка имитированного устройства "Модуль"](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>Тестирование имитированного устройства

Чтобы проверить, отправляет ли имитированное устройство данные телеметрии и сообщает ли оно значения свойств, выберите его в списке устройств на странице **Устройства**. Актуальные сведения об устройстве отображаются на панели **Сведения об устройстве**:

[![Просмотр сведений об имитированном устройстве "Модуль"](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

На панели **Сведения об устройстве** проверьте, отправляет ли новое устройство данные телеметрии. Для просмотра другого потока телеметрической информации с устройства выберите пункт **Вибрация**:

[![Выбор потока телеметрической информации для просмотра](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

На панели **Сведения об устройстве** содержатся и другие сведения об устройстве, например значения тегов, методы, которые оно поддерживает, и свойства, о которых сообщает устройство.

Для просмотра данных диагностики прокрутите страницу вниз до представления **Диагностика**.

[![Просмотр диагностики устройства](./media/iot-accelerators-remote-monitoring-manage/devicediagnostics-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicediagnostics-expanded.png#lightbox)

## <a name="act-on-a-device"></a>Работа с устройством

Чтобы проверить, правильно ли имитированное устройство реагирует на действия, инициируемые из акселератора решений, выполните метод **FirmwareUpdate**. Для работы с устройством с помощью метода выберите его в списке устройств и щелкните **Задания**. Вы можете выбрать несколько устройств для одновременной работы с ними. На панели **Задания** выберите **Выполнить метод**. Для модели устройства **Модуль** определено три метода: **FirmwareUpdate**, **FillTank** и **EmptyTank**.

[![Методы устройства "Модуль"](./media/iot-accelerators-remote-monitoring-manage/devicesmethods-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmethods-expanded.png#lightbox)

Выберите метод **FirmwareUpdate**, задайте имя задания **UpdateEngineFirmware**, укажите версию встроенного ПО **2.0.0**, задайте код URI встроенного ПО **http://contoso.com/engine.bin**, а затем нажмите кнопку **Применить**:

[![Метод для планирования обновления встроенного ПО](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatejob-inline.png)](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatejob-expanded.png#lightbox)

Для отслеживания состояния задания щелкните **Просмотреть состояние задания**.

[![Отслеживание задания обновления встроенного ПО по расписанию](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatestatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatestatus-expanded.png#lightbox)

Когда выполнение задания завершится, вернитесь на страницу **Устройства**. Для устройства "Модуль" будет указана новая версия встроенного ПО.

Если на странице **Устройства** выбрано несколько устройств разных типов, вы все же можете создать задание для выполнения метода на этих устройствах. На панели **Задания** отображаются только те методы, которые являются общими для всех выбранных устройств.

## <a name="reconfigure-a-device"></a>Перенастройка устройства

Чтобы проверить, можно ли изменить свойства конфигурации модуля, выберите его в списке устройств на странице **Устройства**. Затем щелкните **Задания** и выберите пункт **Перенастроить**. На панели заданий отображаются значения свойств выбранного устройства, которые можно изменить:

[![Перенастройка устройства](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

Чтобы изменить расположение модуля, задайте имя задания **UpdateEngineLocation**, укажите долготу **-122,15**, расположение **Фабрика 2**, широту **47,62**, а затем нажмите кнопку **Применить**:

[![Обновление значения для свойства устройства](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

Для отслеживания состояния задания щелкните **Просмотреть состояние задания**.

[![Обновление значения для свойства устройства](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

Когда выполнение задания завершится, перейдите на страницу **Панель мониторинга**. Устройство "Модуль" отображается в новом месте:

[![Просмотр расположения модуля](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>Упорядочение устройств

Чтобы упростить организацию и управление устройствами, оператор может пометить их тегом с именем соответствующей команды. У Contoso есть две различные команды для выездного обслуживания:

* команда обслуживания автомобилей обслуживает грузовики и прототипы устройств;
* команда обслуживания зданий управляет холодильниками, лифтами и двигателями.

Чтобы отобразить все устройства, перейдите на страницу **Устройства** и выберите фильтр **Все устройства**:

[![Показать все устройства](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>Добавление тегов

Выберите все устройства **Грузовики** и **Прототипы**. Щелкните **Задания**.

[![Выберите устройства-прототипы и грузовики](./media/iot-accelerators-remote-monitoring-manage/devicesmultiselect-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmultiselect-expanded.png#lightbox)

Выберите **Тег**, задайте имя задания **AddConnectedVehicleTag**, а затем добавьте текстовый тег с именем **FieldService** и значением **ConnectedVehicle**. Нажмите кнопку **Применить**:

[![Добавление тегов к устройствам-прототипам и грузовикам](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

На странице устройств выберите устройства **Холодильник**, **Лифт** и **Двигатель**. Щелкните **Задания**.

[![Выбор устройств-холодильников, двигателей и лифтов](./media/iot-accelerators-remote-monitoring-manage/devicesmultiselect2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmultiselect2-expanded.png#lightbox)

Выберите **Тег**, задайте имя задания **AddSmartBuildingTag**, а затем добавьте текстовый тег с именем **FieldService** и значением **SmartBuilding**. Нажмите кнопку **Применить**:

[![Добавление тегов к устройствам-холодильникам, двигателям и лифтам](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>Создание фильтров

Теперь значения тегов можно использовать для создания фильтров. На странице **Устройства** выберите **Управление группами устройств**:

[![Управление группами устройств](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

Создайте текстовый фильтр, который использует имя тега **FieldService** и значение **SmartBuilding** в условии. Сохраните фильтр как **Smart Building** (Интеллектуальное здание):

[![Создание фильтра Smart Building](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

Создайте текстовый фильтр, который использует имя тега **FieldService** и значение **ConnectedVehicle** в условии. Сохраните фильтр как **Connected Vehicle** (Подключенный автомобиль).

[![Создание фильтра Connected Vehicle](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

Теперь оператор Contoso может запрашивать устройства на основе рабочей группы.

[![Создание фильтра Connected Vehicle](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете перейти к следующему руководству, оставьте акселератор решений для удаленного мониторинга развернутым. Чтобы сократить расходы на выполнение акселератора решений, когда он не используется, можно остановить имитированные устройства на панели параметров:

[![Приостановка передачи данных телеметрии](./media/iot-accelerators-remote-monitoring-manage/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/togglesimulation-expanded.png#lightbox)

Когда вы будете готовы перейти к следующему руководству, перезапустите имитированные устройства.

Если акселератор решений больше не требуется, удалите его со страницы [Подготовленные решения](https://www.azureiotsolutions.com/Accelerators#dashboard):

![Удаление решения](media/iot-accelerators-remote-monitoring-manage/deletesolution.png)

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве было показано, как настраивать устройства, подключенные к акселератору решений для удаленного мониторинга, и управлять ими. Чтобы узнать, как выявлять и устранять проблемы с подключенными устройствами с помощью акселератора решений, перейдите к следующему руководству.

> [!div class="nextstepaction"]
> [Определение и устранение проблем с устройствами, подключенными к решению для мониторинга, с помощью оповещений устройств](iot-accelerators-remote-monitoring-maintain.md)
