---
title: Визуализируйте данные Azure IoT Central на панели мониторинга Power BI | Документация Майкрософт
description: Используйте шаблон решения Azure IoT Central Analytics Power BI для визуализации и анализа данных IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 5cb55e73b379b909811bde728d2ab39e29635bf5
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190705"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Визуализация и анализ данных Azure IoT Central на панели мониторинга Power BI

![Конвейер шаблона решения Power BI](media/howto-connect-powerbi/iot-continuous-data-export.png)

Используйте шаблон решения Azure IoT Central Analytics Power BI для создания мощной панель мониторинга Power BI для мониторинга производительности устройств Интернета вещей. С помощью панели мониторинга Power BI можно выполнять следующие действия.
- Отслеживать количество данных, которые отправляют устройства за период времени
- Сравнивать объем данных телеметрии, состояний и событий
- Определять устройства, имеющие множество измерений
- Наблюдать за историческими тенденциями измерений устройств
- Определять проблемные устройства, которые отправляют множество важных событий

Этот шаблон решения устанавливает конвейер, который принимает данные в учетной записи хранения больших двоичных объектов Azure из [непрерывного экспорта данных](howto-export-data.md). Эти данные проходят через службы Функции Azure, Фабрику данных Azure и Базу данных SQL Azure, что обрабатывают и преобразуют данные, которые должны визуализироваться и анализироваться в отчете Power BI, который можно загрузить в виде файла PBIX. Все эти ресурсы создаются в подписке Azure, поэтому можно настроить каждый компонент в соответствии с потребностями. Этот шаблон решения полностью открыт, поэтому можно получить дополнительные сведения об архитектуре и расширить решение, посетив [репозиторий Github](https://aka.ms/iotcentralgithubpowerbisolutiontemplate).

**[Шаблон решения аналитики Azure IoT Central Analytics от Microsoft AppSource](https://aka.ms/iotcentralpowerbisolutiontemplate)**.

## <a name="prerequisites"></a>Предварительные требования
Для настройки шаблона требуется следующее.
- Доступ к подписке Azure
- Данные экспортированы с использованием [непрерывного экспорта данных](howto-export-data.md) из вашего приложения IoT Central. Рекомендуется включать измерения, устройства и потоки шаблонов устройств, чтобы максимально использовать панель мониторинга Power BI.
- Power BI Desktop (последняя версия)
- Power BI Pro (если необходимо совместно использовать панели мониторинга с другими пользователями)

## <a name="reports"></a>Отчеты

Два отчета создаются автоматически. 

Первый отчет показывает исторический обзор измерений, сообщенных устройствами, и сортирует различные типы измерений и устройства, которые отправили наибольшее количество измерений.

![Отчет Power BI страница 1](media/howto-connect-powerbi/template-page1-hasdata.PNG)

Второй отчет детализирует события и показывает исторический обзор ошибок и предупреждений. Он также показывает, какие устройства сообщают о самом большом количестве событий, а также о событиях с ошибкой и с предупреждениями.

![Отчет Power BI страница 2](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="resources"></a>Ресурсы

Посетите AppSource, чтобы получить [шаблон решения Azure IoT Central Analytics](https://aka.ms/iotcentralpowerbisolutiontemplate).

Дополнительные сведения об архитектуре и расширении решения см. в [репозитории Github](https://aka.ms/iotcentralgithubpowerbisolutiontemplate).

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы узнали, как визуализировать данные в Power BI, ознакомьтесь со следующей статьей.

> [!div class="nextstepaction"]
> [Управление устройствами в приложении Azure IoT Central](howto-manage-devices.md)
