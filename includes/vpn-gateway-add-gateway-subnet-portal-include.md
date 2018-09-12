---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a4101f3bfe83859eea525370b5eebcaa6e193a2d
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44168888"
---
1. На портале перейдите к виртуальной сети, для которой необходимо создать шлюз.
2. На странице виртуальной сети в разделе **Параметры** щелкните **Подсети**, чтобы открыть страницу "Подсети".
3. На странице **Подсети** щелкните **+Подсеть шлюза** в верхней части страницы. Откроется страница **Добавление подсети**.

  ![Добавить подсеть шлюза](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Add the gateway subnet")
  
4. В поле **Имя** автоматически добавляется значение GatewaySubnet. Значение GatewaySubnet необходимо для Azure при идентификации подсети в качестве подсети шлюза. Замените автоматически заполненное значение **диапазона адресов** в соответствии с требованиями к конфигурации.

  ![Добавление подсети шлюза](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet.png "Adding the gateway subnet")
  
5. Чтобы создать подсеть, в нижней части страницы щелкните **ОК**.
