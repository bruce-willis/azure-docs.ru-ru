---
title: Развертывание модулей IoT Edge Azure | Документация Майкрософт
description: Развертывание модулей на устройстве IoT Edge с помощью расширения Интернета вещей для Azure CLI 2.0
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/08/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 02a7533cf1c06142f564ed9fd6008a25be9fc603
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035305"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli-20"></a>Развертывание модулей IoT Edge Azure с помощью Azure CLI 2.0

Создав модули IoT Edge с в соответствии с определенной бизнес-логикой, вы наверняка захотите развернуть их на устройствах, которые будут работать в граничной среде. Если у вас есть несколько модулей, которые совместно собирают и (или) обрабатывают данные, вы можете развернуть их одновременно и объявить правила маршрутизации для их объединения. 

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) — это кроссплатформенная программа командной строки с открытым кодом для управления ресурсами Azure (например, IoT Edge). Она позволяет управлять ресурсами Центра Интернета вещей Azure, экземплярами службы подготовки устройств и связанными концентраторами без дополнительной настройки. Новое расширение Интернета вещей поставляет в Azure CLI 2.0 компоненты для управления устройствами и всеми возможностями IoT Edge.

В этой статье описано, как создать манифест развертывания в формате JSON и применить этот файл для отправки развертывания на устройство IoT Edge. Информацию о создании развертываний, предназначенных для нескольких устройств с определенными значениями тегов, см. в руководстве по [развертыванию и мониторингу большого числа модулей IoT Edge](how-to-deploy-monitor-cli.md).

## <a name="prerequisites"></a>предварительным требованиям

* [Центр Интернета вещей](../iot-hub/iot-hub-create-using-cli.md) в подписке Azure. 
* [Устройство IoT Edge](how-to-register-device-cli.md) с установленной средой выполнения IoT Edge.
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) в среде. Вам понадобится как минимум Azure CLI 2.0 версии 2.0.24 или более поздней. Для проверки используйте `az –-version`. Эта версия поддерживает команды расширения az и представляет собой платформу команд Knack. 
* [Расширение Интернета вещей для Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Настройка манифеста развертывания

Манифест развертывания — это документ JSON, в котором определены развертываемые модули, способ передачи данных между этими модулями и требуемые свойства для двойников модулей. Дополнительные сведения о работе манифестов развертывания и об их создании см. в руководстве по [использованию, настройке и повторном использовании модулей Azure IoT Edge](module-composition.md).

Чтобы развернуть модули с помощью Azure CLI 2.0, сохраните манифест развертывания на локальном компьютере в формате TXT-файла. Путь к файлу, указанный в следующем разделе, потребуется вам для запуска команды, которая применяет конфигурацию к устройству. 

Вот пример простого манифеста развертывания с одним модулем:

   ```json
   {
     "moduleContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {
                 "registryName": {
                   "username": "",
                   "password": "",
                   "address": ""
                 }
               }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "microsoft/azureiotedge-agent:1.0-preview",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "microsoft/azureiotedge-hub:1.0-preview",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "tempSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "tempSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="deploy-to-your-device"></a>Развертывание на устройстве

Для развертывания модулей на устройстве следует применить манифест развертывания, в который вы заранее внесли все сведения о модулях. 

Следующая команда применяет конфигурацию к устройству IoT Edge:

   ```cli
   az iot hub apply-configuration --device-id [device id] --hub-name [hub name] --content [file path]
   ```

В параметре идентификатора устройства учитывается регистр символов. Параметр content указывает на сохраненный ранее файл манифеста развертывания. 

## <a name="view-modules-on-your-device"></a>Просмотр модулей, установленных на устройстве

Завершив развертывание модулей на устройстве, вы можете просмотреть их список с помощью следующей команды: 

Просмотрите модули на устройстве IoT Edge:
    
   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

В параметре идентификатора устройства учитывается регистр.

   ![Вывод списка модулей](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Дополнительная информация

См. дополнительные сведения о [развертывании и мониторинге большого числа модулей IoT Edge.](how-to-deploy-monitor.md)