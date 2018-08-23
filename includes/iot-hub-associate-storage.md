---
title: включение файла
description: включение файла
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/20/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 892dd050829dce242035e2b875ea43ed13910d4a
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246080"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Связывание учетной записи хранения Azure с Центром Интернета вещей

Так как приложение виртуального устройства отправляет файл в большой двоичный объект, необходимо связать учетную запись [хранилища Azure](../articles/storage/common/storage-quickstart-create-account.md) с Центром Интернета вещей. При связывании учетной записи хранения Azure с Центром Интернета вещей Центр Интернета вещей создает URI SAS. Устройство может использовать этот URI SAS для безопасной передачи файла в контейнер больших двоичных объектов. Служба Центра Интернета вещей и пакеты SDK для устройств координируют процесс создания URI SAS, делая его доступным для устройства, которое отправляет файл.

Чтобы связать учетную запись хранения Azure с Центром Интернета вещей, следуйте инструкциям в статье о [настройке отправки файлов с помощью портала Azure](../articles/iot-hub/iot-hub-configure-file-upload.md). Убедитесь, что контейнер больших двоичных объектов связан с вашим Центром Интернета вещей и что уведомления файлов включены.

![Включение уведомлений файлов на портале](media/iot-hub-associate-storage/enable-file-notifications.png)