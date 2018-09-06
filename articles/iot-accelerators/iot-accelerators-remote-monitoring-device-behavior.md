---
title: Поведение имитированного устройства в решении удаленного мониторинга в Azure | Документация Майкрософт
description: В этой статье описывается, как определить поведение имитированного устройства в решении удаленного мониторинга с помощью JavaScript.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: a983c7307308534140ab8999593ac4c8c6992a42
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338512"
---
# <a name="implement-the-device-model-behavior"></a>Реализация поведения модели устройства

В статье [Общие сведения о схеме модели устройства](iot-accelerators-remote-monitoring-device-schema.md) описана схема, определяющая модель имитированного устройства. В ней описаны два типа файлов JavaScript, реализующих поведение имитированного устройства:

- Файлы JavaScript **состояния**, которые запускаются через фиксированные промежутки времени для обновления внутреннего состояния устройства.
- Файлы JavaScript **метода**, которые запускаются, когда решение вызывает метод на устройстве.

В этой статье раскрываются следующие темы:

>[!div class="checklist"]
> * управление состоянием имитированного устройства;
> * Определение ответа имитированного устройства на вызов метода из решения удаленного мониторинга
> * отладка сценариев.

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>Дополнительная информация

В этой статье описывается, как определить поведение собственной настраиваемой модели имитированного устройства. Из этой статьи вы узнали, как выполнять следующие задачи:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * управление состоянием имитированного устройства;
> * Определение ответа имитированного устройства на вызов метода из решения удаленного мониторинга
> * отладка сценариев.

Теперь, когда вы узнали, как определить поведение имитированного устройства, предлагаем ознакомиться со статьей [Создание имитированного устройства](iot-accelerators-remote-monitoring-create-simulated-device.md).

Дополнительные сведения для разработчиков о решении удаленного мониторинга см. в следующих источниках:

* [Справочник разработчика](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Руководство по устранению неполадок для разработчиков](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
