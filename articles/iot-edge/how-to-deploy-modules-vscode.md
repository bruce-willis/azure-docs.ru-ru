---
title: Развертывание модулей Azure IoT Edge (VS Code) | Документация Майкрософт
description: Использование Visual Studio Code для развертывания модулей на устройстве IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5867012e0322e520b4ef234599fa25a2ec08138d
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325774"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Развертывание модулей Azure IoT Edge из Visual Studio Code

Создав модули IoT Edge с в соответствии с определенной бизнес-логикой, вы наверняка захотите развернуть их на устройствах, которые будут работать на переднем крае. Если у вас есть несколько модулей, которые работают вместе для сбора и обработки данных, вы можете развернуть их одновременно и объявить правила маршрутизации, которые их соединяют. 

В этой статье показано, как создать манифест развертывания JSON и применить этот файл для отправки этого развертывания на устройство IoT Edge. Информацию о создании развертываний, предназначенных для нескольких устройств с определенными значениями тегов, см. в разделе [Развертывание и мониторинг модулей IoT Edge в нужном масштабе (предварительная версия)](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Предварительные требования

* [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) в подписке Azure. 
* [Устройство IoT Edge](how-to-register-device-portal.md) с установленной средой выполнения IoT Edge. 
* [Visual Studio Code](https://code.visualstudio.com/).
* [Расширение Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 

## <a name="configure-a-deployment-manifest"></a>Настройка манифеста развертывания

Манифест развертывания — это документ JSON, в котором определены развертываемые модули, способ передачи данных между этими модулями и требуемые свойства для двойников модулей. Дополнительные сведения о работе манифестов развертывания и об их создании см. в статье [Сведения об использовании, настройке и повторном использовании модулей Azure IoT Edge (предварительная версия)](module-composition.md).

Чтобы развернуть модули с помощью Visual Studio Code, сохраните манифест развертывания на локальный компьютер в формате JSON. В следующем разделе вам потребуется путь к файлу для запуска команды, применяющей конкретную конфигурацию к устройству.

Вот пример простого манифеста развертывания с одним модулем.

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
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
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
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
   
## <a name="sign-in-to-access-your-iot-hub"></a>Вход в систему для получения доступа к Центру Интернета вещей

Для выполнения действий с Центром Интернета вещей можно использовать расширения Интернета вещей Azure для Visual Studio Code. Для этого войдите в учетную запись Azure и выберите Центр Интернета вещей, с которым вы хотите работать.

1. Откройте в Visual Studio Code представление **Explorer**.

2. В нижней части Explorer разверните раздел **Устройства Центра Интернета вещей Azure**. 

   ![Развернутый раздел "Устройства Центра Интернета вещей Azure"](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

3. Щелкните значок **...** в заголовке раздела **Устройства Центра Интернета вещей Azure**. Если значок многоточия не отображается, наведите курсор на заголовок. 

4. Щелкните **Выбрать Центр Интернета вещей**.

5. Если вы еще не вошли в учетную запись Azure, сделайте это сейчас, следуя инструкциям. 

6. Выберите подписку Azure. 

7. Выберите нужный Центр Интернета вещей. 


## <a name="deploy-to-your-device"></a>Развертывание на устройстве

Для развертывания модулей на устройстве следует применить манифест развертывания, в который были заранее внесены сведения о модулях. 

1. В обозревателе Visual Studio Code разверните раздел **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure). 

2. Щелкните правой кнопкой мыши на устройстве, которое необходимо настроить с помощью манифеста развертывания. 

3. Выберите **Create Deployment for IoT Edge Device** (Создать развертывание для устройств IoT Edge). 

4. Перейдите к файлу JSON манифеста развертывания, который необходимо использовать, и нажмите **Выберите манифест развертывания Edge**. 

   ![Выбор манифеста развертывания Edge](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)


Результаты развертывания будут напечатаны в выходных данных VS Code. Если целевое устройство запущено и подключено к Интернету, успешные развертывания применяются в течение нескольких минут. 

## <a name="view-modules-on-your-device"></a>Просмотр модулей, установленных на устройстве

Завершив развертывание модулей на устройстве, их можно просмотреть в разделе **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure). Выберите стрелку рядом с устройством IoT Edge, чтобы развернуть его. Отобразятся все модули, которые выполняются в настоящий момент. 

Если вы недавно развернули новые модули на устройстве, наведите указатель мыши на заголовок раздела **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure) и выберите значок обновления, чтобы обновить представление. 

Щелкните правой кнопкой мыши на название модуля, чтобы его просмотреть и отредактировать. 

## <a name="next-steps"></a>Дополнительная информация

Изучите раздел [Развертывание и мониторинг модулей IoT Edge в нужном масштабе (предварительная версия)](how-to-deploy-monitor.md)