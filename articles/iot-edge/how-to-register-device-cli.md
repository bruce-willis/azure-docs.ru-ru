---
title: Регистрация нового устройства Azure IoT Edge с помощью CLI | Документация Майкрософт
description: Использование расширения Интернета вещей для Azure CLI 2.0 для регистрации нового устройства IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 07/27/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 451f4df31cd1c520b14227829923f72fe80c38c3
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325502"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli-20"></a>Регистрация нового устройства Azure IoT Edge с помощью Azure CLI 2.0

Чтобы использовать устройства Интернета вещей в Azure IoT Edge, нужно зарегистрировать их в Центре Интернета вещей. После регистрации устройства вы получите строку подключения, которую можно использовать при настройке устройства для рабочих нагрузок Edge. 

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) — это кроссплатформенная программа командной строки с открытым кодом для управления ресурсами Azure (например, IoT Edge). Она позволяет управлять ресурсами Центра Интернета вещей Azure, экземплярами службы подготовки устройств и связанными концентраторами без дополнительной настройки. Новое расширение Интернета вещей поставляет в Azure CLI 2.0 компоненты для управления устройствами и всеми возможностями IoT Edge.

В этой статье объясняется, как зарегистрировать новое устройство IoT Edge с помощью Azure CLI 2.0.

## <a name="prerequisites"></a>Предварительные требования

* [Центр Интернета вещей](../iot-hub/iot-hub-create-using-cli.md) в подписке Azure. 
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) в среде. Вам понадобится как минимум Azure CLI 2.0 версии 2.0.24 или более поздней. Для проверки используйте `az –-version`. Эта версия поддерживает команды расширения az и представляет собой платформу команд Knack. 
* [Расширение Интернета вещей для Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>Создание устройства

Чтобы создать идентификатор устройства в Центре Интернета вещей, используйте следующую команду: 

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Эта команда принимает три параметра:
* **device-id** содержит описательное имя устройства, уникальное в пределах Центра Интернета вещей;
* **hub-name** содержит имя Центра Интернета вещей;
* **edge-enabled** указывает, что устройство предназначено для использования с IoT Edge.

   ![Создание устройства IoT Edge](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>Просмотр данных обо всех устройствах

Следующая команда позволяет просмотреть все устройства в Центре Интернета вещей:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Для всех устройств, зарегистрированных в качестве устройства IoT Edge, свойство **capabilities.iotEdge** будет иметь значение **true**. 

## <a name="retrieve-the-connection-string"></a>Получение строки подключения

Когда все будет готово к настройке устройства, вам понадобится строка подключения, которая связывает физическое устройство с его идентификатором в Центре Интернета вещей. Используйте следующую команду, чтобы получить строку подключения для одного устройства.

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name] 
   ```

В параметре идентификатора устройства учитывается регистр. Копируйте строку подключения без кавычек. 

## <a name="next-steps"></a>Дополнительная информация

Подробнее о [развертывании модулей на устройстве с помощью Azure CLI 2.0](how-to-deploy-modules-cli.md)