---
title: Режим работы имитированного устройства в решении имитации устройств в Azure | Документация Майкрософт
description: В этой статье описывается, как определить режим работы имитированного устройства в решении имитации устройств с помощью JavaScript.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: 43edbc653ddbd55aab5e722071de1f2cf4bcd1c4
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344522"
---
# <a name="implement-the-device-model-behavior"></a>Реализация поведения модели устройства

В статье [Общие сведения о схеме модели устройства](iot-accelerators-device-simulation-device-schema.md) описана схема, определяющая модель имитированного устройства. В ней описаны два типа файлов JavaScript, реализующих поведение имитированного устройства:

- **Состояние**. Файлы JavaScript, которые запускаются через фиксированные промежутки времени для обновления внутреннего состояния устройства.
- **Метод**. Файлы JavaScript, которые запускаются, когда решение вызывает метод на устройстве.

В этой статье раскрываются следующие темы:

>[!div class="checklist"]
> * управление состоянием имитированного устройства;
> * определение ответа имитированного устройства на вызов метода из Центра Интернета вещей, к которому он подключен;
> * отладка сценариев.

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>Дополнительная информация

В этой статье описывается, как определить поведение собственной настраиваемой модели имитированного устройства. Из этой статьи вы узнали, как выполнять следующие задачи:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * управление состоянием имитированного устройства;
> * определение ответа имитированного устройства на вызов метода из Центра Интернета вещей, к которому он подключен;
> * отладка сценариев.

Теперь, когда вы узнали, как определить поведение имитированного устройства, предлагаем ознакомиться со статьей [Создание имитированного устройства](iot-accelerators-device-simulation-create-simulated-device.md).

Дополнительные сведения для разработчиков о решении имитации устройств см. в разделе [Developer Reference Guide](https://github.com/Azure/device-simulation-dotnet/wiki/Simulation-Service-Developer-Reference-Guide) (Справочник разработчика).
