---
title: включение файла
description: включение файла
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 07/27/2018
ms.openlocfilehash: 12f0f17a7c25696d2c8ff5b427f4b103617e5678
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47010747"
---
В окне командной строки или оболочки создайте и активируйте среду диспетчера пакетов conda с numpy и cython. В этом примере используется Python 3.6.

  + Действия для ОС Windows.
       ```sh 
       conda create -n myenv Python=3.6 cython numpy
       conda activate myenv
       ```

  + Действия для ОС Linux и MacOS.
       ```sh 
       conda create -n myenv Python=3.6 cython numpy
       source activate myenv
       ```

Установка пакета SDK.
   ```sh 
   pip install azureml-sdk
   ```
