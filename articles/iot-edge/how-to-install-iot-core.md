---
title: Установка Azure IoT Edge в ОС Windows IoT Базовая | Документация Майкрософт
description: Установка среды выполнения Azure IoT Edge на устройстве под управлением ОС Windows IoT Базовая
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 03/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f57db00894dab80f96f45111331d47a173520ced
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576004"
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Установка среды выполнения Azure IoT Edge в ОС Windows IoT Базовая (предварительная версия)

Azure IoT Edge и [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/) поддерживают взаимодействие, обеспечивая граничные вычисления данных даже с небольших устройств. Среда выполнения Azure IoT Edge может работать даже на крошечных одноплатных устройствах (SBC), которые весьма распространены в отрасли Интернета вещей. 

В этой статье описано, как подготовить среду выполнения на плате разработки под управлением Windows IoT Базовая. 

**Сейчас в Windows IoT Базовая предусмотрена поддержка Azure IoT Edge только при использовании x64-разрядных процессоров Intel.**

## <a name="install-the-container-runtime"></a>Установка среды выполнения контейнера

1. Настройте плату с помощью образа IoT Базовая **Сборка 17134 (RS4)**. 
1. Включите устройство и [войдите на него удаленно через PowerShell][lnk-powershell].
1. В консоли PowerShell установите среду выполнения контейнеров. 

   ```powershell
   Invoke-WebRequest https://master.dockerproject.org/windows/x86_64/docker-0.0.0-dev.zip -o temp.zip
   Expand-Archive .\temp.zip $env:ProgramFiles -f
   Remove-Item .\temp.zip
   $env:Path += ";$env:programfiles\docker"
   SETX /M PATH "$env:Path"
   dockerd --register-service
   start-service docker
   ```

   >[!NOTE]
   >Эта среда выполнения контейнеров взята из сервера сборки проекта Moby и предназначена только для ознакомительных целей. Команда Docker не тестировала эту среду, не рекомендует ее и не поддерживает.

## <a name="finish-installing"></a>Завершение установки

Установите управляющую программу для обеспечения безопасности IoT Edge и настройте ее, следуя инструкциям в [этой статье][lnk-install-windows-on-windows]

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда на устройстве запущена среда выполнения IoT Edge, узнайте, как [развертывать и отслеживать модули IoT Edge в нужном масштабе][lnk-deploy].

<!--Links-->
[lnk-install-windows-on-windows]: how-to-install-iot-edge-windows-with-windows.md
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
