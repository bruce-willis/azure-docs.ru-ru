---
title: Приложения пакетной службы для рендеринга
description: Предварительно установленные приложения пакетной службы для рендеринга
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 28acd1b7275694d38a52f14d2b2c32b79cc8183e
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2018
ms.locfileid: "40036810"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Предварительно установленные приложения в образах виртуальных машин для рендеринга

Вы можете использовать в пакетной службе Azure любые приложения для рендеринга Но некоторые распространенные версии уже включены в образы виртуальных машин Azure Marketplace.

Везде, где это возможно, вам будет доступно лицензирование с оплатой по мере использования для всех предустановленных приложений для рендеринга. При создании пула пакетной службы вы можете указать необходимые приложения, и далее поминутно платить за использование виртуальных машин и приложений. Цены на приложения цены указаны на [странице с ценами на пакетную службу Azure](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Некоторые приложения поддерживают только платформу Windows, но большинство из них доступны как в Windows, так и в Linux.

## <a name="applications-on-centos-7-rendering-nodes"></a>Приложения на узлах CentOS 7 для рендеринга

* Autodesk Maya I/O 2017 обновление 5 (версия сборки: 201708032230);
* Autodesk Maya I/O 2018 обновление 2 (версия сборки: 201711281015);
* Autodesk Arnold for Maya 2017 (Arnold версии 5.0.1.1) MtoA-2.0.1.1-2017;
* Autodesk Arnold for Maya 2018 (Arnold версии 5.0.1.4) MtoA-2.1.0.3-2018;
* Chaos Group V-Ray for Maya 2017 (версия 3.60.04);
* Chaos Group V-Ray for Maya 2018 (версия 3.60.04);
* Blender (2.68).

## <a name="applications-on-windows-server-2016-rendering-nodes"></a>Приложения на узлах Windows Server 2016 для рендеринга

* Autodesk Maya I/O 2017 обновление 5 (версия 17.4.5459);
* Autodesk Maya I/O 2018 обновление 3 (версия 18.3.0.7040);  
* Autodesk 3ds Max I/O 2019 обновление 1 (версия 21.10.1314);
* Autodesk 3ds Max I/O 2018 обновление 4 (версия 20.4.0.4254);
* Autodesk Arnold for Maya (Arnold версии 5.0.1.1) MtoA-2.0.1.1-2017;
* Autodesk Arnold for Maya (Arnold версии 5.0.1.4) MtoA-2.0.2.3-2018;
* Autodesk Arnold for 3ds Max (Arnold версии 5.0.2.4) (версия 1.2.926);
* Chaos Group V-Ray for Maya (версия 3.52.03);
* Chaos Group V-Ray for 3ds Max (версия 3.60.02);
* Blender (2.79).

## <a name="next-steps"></a>Дополнительная информация

Чтобы использовать образы виртуальных машин для рендеринга, их необходимо указывать в конфигурации пула при создании пула. Подробнее см. статью о [возможностях рендеринга в пакетной службе](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).