---
title: Поддержка добавления виртуальных машин Azure в существующую группу доступности | Документы Майкрософт
description: Поддержка добавления виртуальных машин Azure в существующую группу доступности.
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 13448f4b335d84264d4141cb4fb8c3eadcf0303e
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092146"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Поддержка добавления виртуальных машин Azure в существующую группу доступности

Иногда вы можете столкнуться с ограничениями при добавлении новых виртуальных машин в существующую группу доступности. На следующей диаграмме показано, какие серии виртуальных машин можно объединить в одной группе доступности.

Ниже приводится матрица поддержки для объединения различных типов виртуальных машин:

Серии и группа доступности|Вторая виртуальная машина|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Первая виртуальная машина|||||||
|A||ОК|ОК|ОК|ОК|ОК|
|Av2||ОК|ОК|ОК|ОК|ОК|
|D||ОК|ОК|ОК|ОК|ОК|
|Dv2||ОК|ОК|ОК|ОК|ОК|
|Dv3||ОК|ОК|ОК|ОК|ОК|

Все остальные серии не могут находиться в одной и той же группе доступности, так как для них требуется определенное оборудование.

Размер виртуальной машины A8 или A9 нельзя смешивать, потому что требуется выделенная сеть серверной сети RDMA.
