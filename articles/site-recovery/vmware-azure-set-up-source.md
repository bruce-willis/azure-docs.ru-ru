---
title: Настройка исходного окружения для репликации из VMware в Azure с помощью Azure Site Recovery | Документация Майкрософт
description: В этой статье описано, как настроить локальное окружение для репликации виртуальных машин VMware в Azure с помощью Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: ramamill
ms.openlocfilehash: 1380c1bc820a815fae317a86fcd0ee4f46dd9aa5
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952660"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Настройка исходного окружения для репликации из VMware в Azure

В этой статье описано, как настроить исходное локальное окружение для репликации виртуальных машин VMware в Azure. В ней указаны шаги для выбора сценария репликации, настройки локального компьютера в качестве сервера конфигурации Azure Site Recovery и автоматического обнаружения локальных виртуальных машин. 

## <a name="prerequisites"></a>предварительным требованиям

В этой статье предполагается, что вы:
- [настроили ресурсы](tutorial-prepare-azure.md) на [портале Azure](http://portal.azure.com);
- [настроили локальную среду VMware](vmware-azure-tutorial-prepare-on-premises.md), в том числе выделенную учетную запись для автоматического обнаружения.



## <a name="choose-your-protection-goals"></a>Выбор целевых объектов для защиты

1. На портале Azure откройте колонку **Хранилища служб восстановления** и выберите свое хранилище.
2. В меню ресурсов хранилища выберите **Getting Started** > **Site Recovery** > **Step 1: Prepare Infrastructure** > **Protection goal** (Приступая к работе > Site Recovery > Шаг 1. Подготовка инфраструктуры > Цель защиты).

    ![Выбор цели](./media/vmware-azure-set-up-source/choose-goals.png)
3. На странице **Protection goal** (Цель защиты) выберите **To Azure** (В Azure) и **Yes, with VMware vSphere Hypervisor** (Да, с использованием гипервизора VMware vSphere). Нажмите кнопку **ОК**.

    ![Выбор цели](./media/vmware-azure-set-up-source/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>Настройка сервера конфигурации

Вы можете настроить сервер конфигурации в качестве локальной виртуальной машины VMware с помощью шаблона Open Virtualization Application (OVA). Ознакомьтесь с [дополнительными сведениями](concepts-vmware-to-azure-architecture.md) о компонентах, которые будут установлены на виртуальной машине VMware.

1. Изучите дополнительные сведения о [необходимых компонентах](vmware-azure-deploy-configuration-server.md#prerequisites) для развертывания сервера конфигурации.
2. [Проверьте значения емкости](vmware-azure-deploy-configuration-server.md#capacity-planning) для развертывания.
3. [Скачайте](vmware-azure-deploy-configuration-server.md#download-the-template) и [импортируйте](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) шаблон OVA для настройки локальной виртуальной машины VMware, работающей на сервере конфигурации. Вместе с шаблоном предоставляется пробная лицензия сроком на 180 дней. По истечении этого периода клиенту нужно будет активировать Windows с помощью приобретенной лицензии.
4. Включите виртуальную машину VMware и [зарегистрируйте ее](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) в хранилище служб восстановления.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Добавление учетной записи VMware, используемой для автоматического обнаружения

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>Подключение к серверу VMware

Чтобы служба Azure Site Recovery могла обнаруживать виртуальные машины, запущенные в локальной среде, сервер VMware vCenter Server или узлы vSphere ESXi необходимо подключить к этой службе.

Нажмите кнопку **+vCenter** (+ vCenter Server), чтобы начать установку подключения сервера VMware vCenter Server или узла vSphere ESXi.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Распространенные проблемы
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Дополнительная информация
[Настройка целевой среды](./vmware-azure-set-up-target.md) в Azure.
