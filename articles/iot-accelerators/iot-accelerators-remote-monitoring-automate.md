---
title: Выявление проблем с устройствами с помощью решения для удаленного мониторинга в Azure | Документы Майкрософт
description: В этом руководстве показано, как с помощью правил и действий автоматически обнаруживать проблемы на устройстве на основе пороговых значений в решении для удаленного мониторинга.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 1e3eaeec1d2eae3c36f285a3e4c536657504cbb8
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098487"
---
# <a name="tutorial-detect-issues-with-devices-connected-to-your-monitoring-solution"></a>Учебник. Выявление проблем с устройствами, подключенными к решению для мониторинга

В этом руководстве показано, как настроить акселератор решений для удаленного мониторинга с целью выявления проблем на подключенных устройствах Интернета вещей. Для определения проблем с устройствами необходимо добавить правила, которые создают оповещения на панели мониторинга решения.

Для ознакомления с правилами и оповещениями в руководстве используется имитированное устройство-холодильник. Холодильник управляется организацией под названием Contoso и подключен к акселератору решений для удаленного мониторинга. В Contoso уже есть правило, которое создает критическое оповещение, когда давление в холодильнике превышает 298 фунтов на квадратный дюйм. Оператору Contoso необходимо просмотреть первоначальные пики давления и определить холодильники, у которых могут быть неисправные датчики. Для этого необходимо добавить правило, которое создает предупреждение, когда давление в холодильнике превышает 150 фунтов на квадратный дюйм.

Также нужно, чтобы создавалось критически важное оповещение, если за последние пять минут средний уровень влажности в устройстве превышал 80 %, а температура устройства превышала 24 градуса по Цельсию.

Изучив это руководство, вы:

>[!div class="checklist"]
> * Просмотр правил в решении
> * Создание правила
> * Создание правила с несколькими условиями
> * Изменение имеющегося правила
> * Включение и отключение правила

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам понадобится развернутый экземпляр акселератора решений для удаленного мониторинга в подписке Azure.

Если акселератор решений для удаленного мониторинга еще не развернут, выполните краткое руководство по [развертыванию облачного решения для удаленного мониторинга](quickstart-remote-monitoring-deploy.md).

## <a name="view-the-existing-rules"></a>Просмотр существующих правил

На странице **Правила** в акселераторе решений показан список актуальных правил:

[![Страница "Правила"](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-expanded.png#lightbox)

Для просмотра правил, которые применяются только к холодильникам, примените фильтр:

[![Фильтрация списка правил](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2-expanded.png#lightbox)

Вы можете просмотреть сведения о правиле и изменить его, выбрав его в списке:

[![Просмотр сведений о правиле](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-expanded.png#lightbox)

Для включения или отключения одного или нескольких правил выберите их в списке:

[![Выбор нескольких правил](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2-expanded.png#lightbox)

## <a name="create-a-rule"></a>Создание правила

Чтобы создать правило, генерирующее предупреждение, когда давление в холодильнике превышает 150 фунтов на квадратный дюйм, щелкните **Новое правило**: Чтобы создать правило, используйте следующие значения:

| Параметр          | Значение                                 |
| ---------------- | ------------------------------------- |
| Имя правила        | Предупреждение устройства Chiller                       |
| Описание      | Давление устройства Chiller превышает 150 фунтов на квадратный дюйм |
| Группа устройств     | Группа устройств **Chillers**             |
| Вычисление      | Мгновенное                               |
| Поле условия 1| pressure                              |
| Оператор условия 1 | Больше                      |
| Значение условия 1    | 150                               |
| Уровень серьезности  | Предупреждение                               |

[![Создание правила предупреждения](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-expanded.png#lightbox)

Чтобы сохранить новое правило, нажмите кнопку **Применить**.

Сведения о том, активировано ли правило, можно посмотреть на странице **Правила** или **Панель мониторинга**.

[![Правило предупреждений активировано](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-expanded.png#lightbox)

## <a name="create-a-rule-with-multiple-conditions"></a>Создание правила с несколькими условиями

Чтобы создать правило с несколькими условиями, которое активирует критически важное оповещение, если за последние пять минут средний уровень влажности в холодильнике превышал 80 %, а температура превышала 24 градуса по Цельсию, щелкните **Новое правило**. Чтобы создать правило, используйте следующие значения:

| Параметр          | Значение                                 |
| ---------------- | ------------------------------------- |
| Имя правила        | Критический уровень влажности и температуры устройства Chiller    |
| Описание      | Критический уровень влажности и температуры |
| Группа устройств     | Группа устройств **Chillers**             |
| Вычисление      | Среднее                               |
| Период времени      | 5                                     |
| Поле условия 1| Влажность                              |
| Оператор условия 1 | Больше                      |
| Значение условия 1    | 80                                |
| Уровень серьезности  | Критические ошибки                              |

[![Создание правила с несколькими условиями, часть 1](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-expanded.png#lightbox)

Чтобы добавить второе условие, щелкните "+Добавить условие". Используйте следующие значения для нового условия:

| Параметр          | Значение                                 |
| ---------------- | ------------------------------------- |
| Поле условия 2| Температура                           |
| Оператор условия 2 | Больше                      |
| Значение условия 2    | 75                                |

[![Создание правила с несколькими условиями, часть 2](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-expanded.png#lightbox)

Чтобы сохранить новое правило, нажмите кнопку **Применить**.

Сведения о том, активировано ли правило, можно посмотреть на странице **Правила** или **Панель мониторинга**.

[![Правило с несколькими условиями активировано](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-expanded.png#lightbox)

## <a name="edit-an-existing-rule"></a>Изменение имеющегося правила

Чтобы внести изменения в имеющееся правило, выберите его в списке правил и нажмите **Изменить**.

[![Изменение правила](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-expanded.png#lightbox)

## <a name="disable-a-rule"></a>Отключение устройства

Временно отключить правило можно в списке правил. Выберите правило для отключения, а затем выберите **Отключить**. **Состояние** правила в списке изменится, указывая, что правило отключено. Вы можете снова включить правило, которое ранее было отключено, используя ту же процедуру.

[![Отключение правила](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-expanded.png#lightbox)

Чтобы включить или отключить несколько правил одновременно, выберите их в списке.

<!-- ## Delete a rule

To permanently delete a rule, choose the rule in the list of rules and then choose **Delete**.

You can delete multiple rules at the same time if you select multiple rules in the list.-->

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете перейти к следующему руководству, оставьте акселератор решений для удаленного мониторинга развернутым. Чтобы сократить расходы на выполнение акселератора решений, когда он не используется, можно остановить имитированные устройства на панели параметров:

[![Приостановка передачи данных телеметрии](./media/iot-accelerators-remote-monitoring-automate/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-automate/togglesimulation-expanded.png#lightbox)

Когда вы будете готовы перейти к следующему руководству, перезапустите имитированные устройства.

Если акселератор решений больше не требуется, удалите его со страницы [Подготовленные решения](https://www.azureiotsolutions.com/Accelerators#dashboard):

![Удаление решения](media/iot-accelerators-remote-monitoring-automate/deletesolution.png)

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве было показано, как создавать правила, активирующие оповещения в решении, и управлять ими с помощью страницы **Правила** в акселераторе решений для удаленного мониторинга. Чтобы узнать, как настраивать подключенные устройства и управлять ими с помощью акселератора решений, перейдите к следующему руководству.

> [!div class="nextstepaction"]
> [Настройки и администрирование устройств, подключенных к решению для мониторинга](iot-accelerators-remote-monitoring-manage.md)