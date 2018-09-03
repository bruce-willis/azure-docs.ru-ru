---
title: Руководство по выявлению проблем с устройствами с помощью решения для удаленного мониторинга в Azure | Документация Майкрософт
description: В этом руководстве показано, как с помощью правил и действий автоматически обнаруживать проблемы на устройстве на основе пороговых значений в решении для удаленного мониторинга.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/19/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 6759568a678394f7cec4ac9f0bdd99d8ed1db9de
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42886796"
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

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="review-the-existing-rules"></a>Просмотр существующих правил

На странице **Правила** в акселераторе решений показан список актуальных правил:

[![Страница "Правила"](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-expanded.png#lightbox)

Для просмотра правил, которые применяются только к холодильникам, примените фильтр. Вы можете просмотреть сведения о правиле и изменить его, выбрав его в списке:

[![Просмотр сведений о правиле](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-expanded.png#lightbox)

## <a name="create-a-rule"></a>Создание правила

Чтобы создать правило, генерирующее предупреждение, когда давление в холодильнике превышает 150 фунтов на квадратный дюйм, щелкните **Новое правило**: Чтобы создать правило, используйте следующие значения:

| Параметр          | Значение                                 |
| ---------------- | ------------------------------------- |
| Имя правила        | Предупреждение устройства Chiller                       |
| ОПИСАНИЕ      | Давление устройства Chiller превышает 150 фунтов на квадратный дюйм |
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

## <a name="create-an-advanced-rule"></a>Создание расширенного правила

Чтобы создать правило с несколькими условиями, которое активирует критически важное оповещение, если за последние пять минут средний уровень влажности в холодильнике превышал 80 %, а температура превышала 24 градуса по Цельсию, щелкните **Новое правило**. Чтобы создать правило, используйте следующие значения:

| Параметр          | Значение                                 |
| ---------------- | ------------------------------------- |
| Имя правила        | Критический уровень влажности и температуры устройства Chiller    |
| ОПИСАНИЕ      | Критический уровень влажности и температуры |
| Группа устройств     | Группа устройств **Chillers**             |
| Вычисление      | Средняя                               |
| Период времени      | 5                                     |
| Поле условия 1| Влажность                              |
| Оператор условия 1 | Больше                      |
| Значение условия 1    | 80                                |
| Уровень серьезности  | критические ошибки.                              |

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

Временно отключить правило можно в списке правил. Выберите правило для отключения и щелкните **Отключить**. **Состояние** правила в списке изменится, указывая, что правило отключено. Вы можете снова включить правило, которое ранее было отключено, используя ту же процедуру.

[![Отключение правила](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-expanded.png#lightbox)

Чтобы включить или отключить несколько правил одновременно, выберите их в списке.

## <a name="delete-a-rule"></a>Удаление правила

Чтобы окончательно удалить правило, удалите его из списка правил. Выберите правило для удаления и щелкните **Удалить**.

[![Удаление правила](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdelete-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdelete-expanded.png#lightbox)

Когда вы подтвердите удаление правила, вы сможете также удалить все оповещения, связанные с правилом, на странице **Обслуживание**.

[![Удаление правила](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdeletetidy-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdeletetidy-expanded.png#lightbox)

Вы можете удалить только одно правило за раз.

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве было показано, как создавать правила, активирующие оповещения в решении, и управлять ими с помощью страницы **Правила** в акселераторе решений для удаленного мониторинга. Чтобы узнать, как настраивать подключенные устройства и управлять ими с помощью акселератора решений, перейдите к следующему руководству.

> [!div class="nextstepaction"]
> [Настройки и администрирование устройств, подключенных к решению для мониторинга](iot-accelerators-remote-monitoring-manage.md)