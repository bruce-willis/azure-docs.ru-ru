---
title: Общие сведения об использовании OpenShift в Azure | Документация Майкрософт
description: Общие сведения об использовании OpenShift в Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: e3ab060c1cea28f83c18dc89aeea7716ec86572a
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190349"
---
# <a name="openshift-in-azure"></a>Использование OpenShift в Azure

OpenShift — это открытая и расширяемая платформа приложений-контейнеров, которая позволяет использовать Docker и Kubernetes на предприятии.  

OpenShift включает Kubernetes для оркестрации контейнеров и управления ими. Эта платформа включает инструменты для разработчиков и операций, позволяющие:

- быстро разрабатывать приложения;
- легко развертывать и масштабировать решения;
- обслуживать жизненный цикл команд и приложений в долгосрочной перспективе.

Существует несколько версий OpenShift.

- OKD (прежнее название — OpenShift Origin);
- Платформа контейнеров OpenShift
- OpenShift Online
- OpenShift Dedicated

Пользователи могут использовать две версии (из четырех, описанных в этой статье) для развертывания в Azure решений OpenShift Origin и OpenShift Container Platform.

## <a name="okd-formerly-openshift-origin"></a>OKD (прежнее название — OpenShift Origin);

OKD — это высокоуровневый проект OpenShift с [открытым кодом](https://www.okd.io/), который поддерживается сообществом. OKD можно установить в ОС CentOS или Red Hat Enterprise Linux (RHEL).

## <a name="openshift-container-platform"></a>Платформа контейнеров OpenShift

OpenShift Container Platform — это [коммерческая версия](https://www.openshift.com) корпоративного класса, которая разрабатывается и поддерживается компанией Red Hat. Чтобы использовать эту версию, клиенты должен приобрести необходимые права доступа к платформе OpenShift Container Platform. Кроме того, они сами устанавливают и администрируют всю инфраструктуру.

Так как клиенты являются владельцами платформы, они могут установить ее в локальном центре обработки данных или в общедоступном облаке (Azure, AWS или Google).

## <a name="openshift-online"></a>OpenShift Online

OpenShift Online — это *мультитенантная* платформа OpenShift (на базе Container Platform) под управлением Red Hat. Red Hat управляет всей базовой инфраструктурой (виртуальными машинами, кластером OpenShift, сетью и хранилищем). 

Чтобы использовать эту версию, клиенты развертывают контейнеры, но при этом они не могут выбирать, на каких узлах эти контейнеры выполняются. Так как OpenShift Online — это мультитенантная среда, контейнеры могут размещаться на тех же узлах виртуальных машин, что и контейнеры других клиентов. Стоимость вычисляется из расчета на один контейнер.

## <a name="openshift-dedicated"></a>OpenShift Dedicated

OpenShift Dedicated — это *однотенантная* платформа OpenShift (на базе Container Platform) под управлением Red Hat. Red Hat управляет всей базовой инфраструктурой (виртуальными машинами, кластером OpenShift, сетью, хранилищем и т. д.). Кластер связан только с одним клиентом. Выполняется он в общедоступном облаке (AWS или Google; выпуск для Azure ожидается в начале 2018 года). Начальный кластер включает четыре узла приложений стоимостью 48 000 долл. США в год (оплачивается заранее).

## <a name="next-steps"></a>Дополнительная информация

- [Общие предварительные требования для OpenShift в Azure](./openshift-prerequisites.md)
- [Развертывание OpenShift Origin в Azure](./openshift-origin.md)
- [Развертывание платформы OpenShift Container Platform](./openshift-container-platform.md)
- [Задачи, выполняемые после развертывания](./openshift-post-deployment.md)
- [Устранение неполадок с развертыванием OpenShift](./openshift-troubleshooting.md)
