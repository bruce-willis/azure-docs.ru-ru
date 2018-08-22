---
title: Создание виртуальной машины со статическим общедоступным IP-адресом с помощью портала Azure | Документация Майкрософт
description: Узнайте, как создать виртуальную машину со статическим общедоступным IP-адресом с помощью портала Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: jdial
ms.openlocfilehash: 9b6db45e38267c70adef3f5a341b8b918b9e78fb
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714433"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Создание виртуальной машины со статическим общедоступным IP-адресом с помощью портала Azure

Вы можете создать виртуальную машину со статическим общедоступным IP-адресом. Общедоступный IP-адрес позволяет подключиться к виртуальной машине из Интернета. Присвойте статический общедоступный IP-адрес, а не динамический, чтобы гарантировать, что адрес никогда не изменится. Дополнительные сведения о [статических общедоступных IP-адресах](virtual-network-ip-addresses-overview-arm.md#allocation-method). Чтобы изменить общедоступный IP-адрес, присвоенный существующей виртуальной машине, с динамического на статический или использовать частные IP-адреса, см. статью [Добавление, изменение и удаление IP-адресов для сетевого интерфейса Azure](virtual-network-network-interface-addresses.md). За использование общедоступных IP-адресов взимается [номинальная плата](https://azure.microsoft.com/pricing/details/ip-addresses). Кроме того, существует [ограничение](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) на число общедоступных IP-адресов, которые можно использовать в рамках одной подписки.

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу https://portal.azure.com.

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

1. Выберите **+ Создать ресурс** в верхнем левом углу окна портала Azure.
2. Выберите **Вычисления**, а затем выберите **Виртуальная машина Windows Server 2016** или другую операционную систему по своему выбору.
3. Введите или выберите следующие значения, примите значения по умолчанию для остальных параметров и нажмите кнопку **ОК**:

    |Параметр|Значение|
    |---|---|
    |ИМЯ|myVM|
    |Имя пользователя| Введите выбранное имя пользователя.|
    |Пароль| Введите выбранный пароль. Пароль должен включать минимум 12 символов и соответствовать [определенным требованиям к сложности](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Подписка| Выберите свою подписку.|
    |Группа ресурсов| Щелкните **Использовать существующую** и выберите **myResourceGroup**.|
    |Расположение| Выберите **Восточная часть США**.|

4. Выберите размер виртуальной машины и щелкните **Выбрать**.
5. В разделе **Параметры** выберите **Общедоступный IP-адрес**.
6. Введите *myPublicIpAddress*, выберите **Статический**, а затем — **ОК**, как показано на следующем снимке экрана:

   ![Выбор значения "Статический"](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Если общедоступный IP-адрес должен принадлежать к SKU "Стандартный", выберите **Стандартный** в разделе **SKU**. Дополнительные сведения о [номерах SKU общедоступных IP-адресов](virtual-network-ip-addresses-overview-arm.md#sku). Если виртуальная машина добавляется в серверный пул общедоступной подсистемы Azure Load Balancer, номера SKU общедоступных IP-адресов виртуальной машины и подсистемы балансировки нагрузки должны совпадать. Подробные сведения см. в статье [Что такое Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

6. Выберите порт или оставьте пустое поле в разделе **Выберите общедоступные входящие порты**. Порт 3389 выбран для обеспечения удаленного доступа к виртуальной машине Windows Server из Интернета. Открытие порта 3389 из Интернета не рекомендуется для производственных рабочих нагрузок.

   ![Выбор порта](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Оставьте значения по умолчанию для остальных параметров и нажмите кнопку **ОК**.
8. На странице **Сводка** выберите **Создать**. Развертывание виртуальной машины занимает несколько минут.
9. После развертывания виртуальной машины введите *myPublicIpAddress* в поле поиска в верхней части портала. При появлении пункта **myPublicIpAddress** в результатах поиска выберите его.
10. Присвоенный общедоступный IP-адрес и присвоенный адрес виртуальной машины **myVM** можно просмотреть в колонке, как показано на следующем снимке экрана:

    ![Просмотр общедоступного IP-адреса](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Платформа Azure присвоила общедоступный IP-адрес из адресов, используемых в регионе, в котором вы создали виртуальную машину. Вы можете загрузить список диапазонов (префиксов) для облаков Azure: [общедоступное](https://www.microsoft.com/download/details.aspx?id=56519), облако [правительства США](https://www.microsoft.com/download/details.aspx?id=57063), облако для [Китая](https://www.microsoft.com/download/details.aspx?id=57062) и [Германии](https://www.microsoft.com/download/details.aspx?id=57064).

11. Выберите **Конфигурация**, чтобы убедиться, что выбрано значение **Статический**.

    ![Просмотр общедоступного IP-адреса](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
Не изменяйте параметры IP-адресов в операционной системе виртуальной машины. Общедоступные IP-адреса Azure не поддерживаются в операционной системе. Вы можете добавлять параметры частных IP-адресов в операционную систему, но это рекомендуется делать только при необходимости и только после прочтения раздела о [добавлении частных IP-адресов в операционную систему](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите группу ресурсов и все содержащиеся в ней ресурсы, когда она станет не нужна.

1. В поле **Поиск** в верхней части портала введите *myResourceGroup*. Когда группа ресурсов **myResourceGroup** появится в результатах поиска, выберите ее.
2. Выберите **Удалить группу ресурсов**.
3. Введите *myResourceGroup* в поле **TYPE THE RESOURCE GROUP NAME:** (Введите имя группы ресурсов:) и нажмите кнопку **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения об [общедоступных IP-адресах в Azure](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- Дополнительные сведения обо всех [параметрах общедоступных IP-адресов](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Дополнительные сведения о [частных IP-адресах](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) и назначении [статического частного IP-адреса](virtual-network-network-interface-addresses.md#add-ip-addresses) виртуальной машине Azure
- Дополнительные сведения о создании виртуальных машин [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) и [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)