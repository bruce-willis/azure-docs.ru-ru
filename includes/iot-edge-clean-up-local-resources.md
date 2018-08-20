---
title: включение файла
description: включение файла
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/10/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: cdb60ffb3ba3c31c336c8b74c46621792c707f74
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2018
ms.locfileid: "40046772"
---
### <a name="delete-local-resources"></a>Удаление локальных ресурсов

Если вы хотите удалить среду выполнения IoT Edge и связанные ресурсы с устройства, используйте команды в соответствии с операционной системой устройства. 

#### <a name="windows"></a>Windows

Удалите среду выполнения IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Uninstall-SecurityDaemon
   ```

При удалении среды выполнения IoT Edge созданные с ее помощью контейнеры остановятся, но будут по-прежнему храниться на устройстве. Просмотрите все контейнеры.

   ```powershell
   docker ps -a
   ```

Удалите контейнеры среды выполнения, созданные на устройстве.

   ```powershell
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

Удалите дополнительные контейнеры, которые указаны в выходных данных `docker ps`, ссылаясь на имена контейнеров. 

#### <a name="linux"></a>Linux

Удалите среду выполнения IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

При удалении среды выполнения IoT Edge созданные с ее помощью контейнеры остановятся, но будут по-прежнему храниться на устройстве. Просмотрите все контейнеры.

   ```bash
   sudo docker ps -a
   ```

Удалите контейнеры среды выполнения, созданные на устройстве.

   ```bash
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

Удалите дополнительные контейнеры, которые указаны в выходных данных `docker ps`, ссылаясь на имена контейнеров. 

Удалите среду выполнения контейнера.

   ```bash
   sudo apt-get remove --purge moby
   ```