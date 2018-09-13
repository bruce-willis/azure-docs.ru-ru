---
title: Визуализация данных телеметрии имитации устройств с помощью службы "Аналитика временных рядов Azure" | Документация Майкрософт
description: Сведения о настройке среды службы "Аналитика временных рядов Azure" для изучения и анализа данных телеметрии, созданных акселератором решений для имитации устройств.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 49cecca4e5ebef9f43fdda16cfa1fdc2ad7b6f94
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382882"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Использование службы "Аналитика временных рядов Azure" для визуализации данных телеметрии, отправленных из акселератора решений для имитации устройств

Акселератор решений для имитации устройств позволяет создавать данные телеметрии с имитированных устройств для тестирования ваших решений Центра Интернета вещей. В этом руководстве показано, как визуализировать и анализировать имитированные данные телеметрии с помощью среды службы "Аналитика временных рядов".

## <a name="prerequisites"></a>Предварительные требования

Для дальнейших действий, описанных в этом руководстве, вам потребуется активная подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

Действия в этом руководстве предполагают, что вы развернули акселератор решений для имитации устройств в подписке Azure. Если акселератор решений еще не развернут, выполните действия, описанные в кратком руководстве [по развертыванию и запуску облачного решения для имитации устройств](quickstart-device-simulation-deploy.md).

В этой статье предполагается, что имя акселератора решений — **contoso-simulation**. Замените **contoso-simulation** именем вашего акселератора решений, когда будете выполнять следующие шаги.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-accelerators-create-tsi.md](../../includes/iot-accelerators-create-tsi.md)]

## <a name="start-a-simulation"></a>Запуск имитации

Перед тем как использовать обозреватель службы "Аналитика временных рядов", настройте акселератор решений для имитации устройств, чтобы создать данные телеметрии. На следующем снимке экрана показана имитация с 10 холодильниками:

![Выполнение имитации устройств](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Обозреватель службы "Аналитика временных рядов"

Обозреватель службы "Аналитика временных рядов" — веб-приложение, которое можно использовать, чтобы визуализировать данные телеметрии.

1. На портале Azure выберите вкладку **Обзор** для службы "Аналитика временных рядов".

1. Чтобы открыть веб-приложение обозревателя службы "Аналитика временных рядов", щелкните **Go to Environment** (Перейти к среде):

    ![Обозреватель службы "Аналитика временных рядов"](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. На панели выбора времени выберите **Последние 30 минут** в быстром меню времени и щелкните **Поиск**:

    ![Поиск в обозревателе службы "Аналитика временных рядов"](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. На панели условий в левой части экрана выберите **температуру** в качестве **меры** и **iothub-connection-device-id** в качестве значения **Разделение по**:

    ![Запрос в обозревателе службы "Аналитика временных рядов"](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Щелкните правой кнопкой мыши диаграмму и выберите **Обзор событий**.

    ![События в обозревателе службы "Аналитика временных рядов"](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Данные событий отображаются в сетке:

    ![Таблица в обозревателе службы "Аналитика временных рядов"](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Нажмите кнопку представления "Перспектива".

    ![Представление "Перспектива" в обозревателе службы "Аналитика временных рядов"](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Щелкните **+**, чтобы добавить новый запрос в перспективу:

    ![Добавление запроса в обозревателе службы "Аналитика временных рядов"](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Выберите **Последние 30 минут** в качестве диапазона времени, **Влажность** в качестве **меры** и **iothub-connection-device-id** в качестве значения **Разделение по**:

    ![Запрос в обозревателе службы "Аналитика временных рядов"](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Нажмите кнопку представления "Перспектива", чтобы просмотреть панель мониторинга данных телеметрии устройства.

    ![Панель мониторинга в обозревателе службы "Аналитика временных рядов"](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

[!INCLUDE [iot-accelerators-cleanup-tsi.md](../../includes/iot-accelerators-cleanup-tsi.md)]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о том, как изучать и запрашивать данные в обозревателе службы "Аналитика временных рядов", см. в [этой статье](../time-series-insights/time-series-insights-explorer.md).
