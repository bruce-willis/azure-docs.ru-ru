---
title: Добавление Kubernetes в Azure Stack Marketplace | Документация Майкрософт
description: Узнайте, как добавить Kubernetes в Azure Stack Marketplace.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 69bf788ef30a18bbe70e251fdd6a814d0f528f55
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994570"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Добавление Kubernetes в Azure Stack Marketplace

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

> [!note]  
> Система Kubernetes доступна в Azure Stack в предварительной версии. Чтобы выполнить инструкции, содержащиеся в этой статье, необходимо получить доступ к Kubernetes Cluster в Azure Stack Marketplace. Поэтому вам нужно [отправить запрос на получение доступа](https://aka.ms/azsk8).

Вы можете обеспечить своим пользователям доступ к Kubernetes из Azure Stack Marketplace. Развертывание Kubernetes выполняется за одну согласованную операцию.

В этой статье рассматривается развертывание и подготовка ресурсов для автономного кластера Kubernetes с помощью шаблона Azure Resource Manager. Прежде чем начать, проверьте настройки Azure Stack и глобальные параметры клиента Azure. Соберите необходимые сведения об Azure Stack. Добавьте необходимые ресурсы в клиент и Azure Stack Marketplace. Успешное добавление Kubernetes в Azure Stack Marketplace зависит от правильного выбора сервера Ubuntu, настраиваемого скрипта и элементов Kubernetes.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Создание плана, предложения и подписки

Создайте план, предложение и подписку для Kubernetes. Можно также использовать имеющийся план и предложение.

1. Войдите на [портал администрирования.](https://adminportal.local.azurestack.external)

1. Создайте базовый план. Инструкции см. в статье [Создание плана в Azure Stack](azure-stack-create-plan.md).

1. Создайте предложение. Инструкции см. в статье [Создание предложения в Azure Stack](azure-stack-create-offer.md).

1. Выберите **Предложения** и найдите созданное предложение.

1. В колонке "Предложение" выберите **Обзор**.

1. Щелкните **Изменить состояние**. Щелкните **Общедоступный**.

1. Чтобы создать подписку, последовательно выберите **+ Создать ресурс** > **Offers and Plans**(Предложения и планы) > **Подписка**.

    a. Введите значение в поле **Отображаемое имя**.

    b. Введите значение в поле **Пользователь**. Используйте учетную запись Azure AD, связанную с вашим клиентом.

    c. **Описание поставщика**

    d. В поле **Клиент каталога** укажите клиент Azure AD для Azure Stack. 

    д. Выберите **Предложение**. Выберите имя созданного предложения. Запишите идентификатор подписки.

## <a name="add-an-ubuntu-server-image"></a>Добавление образа сервера Ubuntu

Добавьте в Marketplace следующий образ сервера Ubuntu:

1. Войдите на [портал администрирования](https://adminportal.local.azurestack.external).

1. Выберите **Все службы**, а затем в категории **Администрирование** выберите пункт **Marketplace management** (Управление Marketplace).

1. Выберите **+ Add from Azure** (Добавить из Azure).

1. Укажите `UbuntuServer`.

1. Выберите сервер последней версии со следующим профилем:
    - **Издатель** : Canonical
    - **Предложение**: UbuntuServer
    - **SKU**: 16.04 LTS

1. Выберите **Скачать**.

## <a name="add-a-custom-script-for-linux"></a>Добавление настраиваемого скрипта для Linux

Добавьте Kubernetes в Marketplace, выполнив следующие действия:

1. Откройте [портал администрирования](https://adminportal.local.azurestack.external).

1. Выберите **Все службы**, а затем в категории **Администрирование** выберите пункт **Marketplace management** (Управление Marketplace).

1. Выберите **+ Add from Azure** (Добавить из Azure).

1. Укажите `Custom Script for Linux`.

1. Выберите скрипт со следующим профилем:
    - **Предложение**: настраиваемый скрипт для Linux 2.0
    - **Версия**: 2.0.6
    - **Издатель**: корпорация Майкрософт

    > [!Note]  
    > Могут отображаться несколько версий настраиваемого скрипта для Linux. Необходимо добавить соответствующую версию. Для Kubernetes необходим настраиваемый скрипт определенной версии.

1. Выберите **Скачать**.


## <a name="add-kubernetes-to-the-marketplace"></a>Добавление Kubernetes в Marketplace

1. Откройте [портал администрирования](https://adminportal.local.azurestack.external).

1. Выберите **Все службы**, а затем в категории **Администрирование** выберите пункт **Marketplace management** (Управление Marketplace).

1. Выберите **+ Add from Azure** (Добавить из Azure).

1. Укажите `Kubernetes`.

1. Выберите `Kubernetes Cluster`.

1. Выберите **Скачать**.

    > [!note]  
    > Для отображения элемента в Marketplace может потребоваться пять минут.

    ![kubernetes](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Обновление или удаление Kubernetes 

При обновлении Kubernetes необходимо удалить прежнюю версию Kubernetes из Azure Stack Marketplace. После этого можно выполнить инструкции из этой статьи по добавлению Kubernetes в Azure Stack Marketplace.

Чтобы удалить Kubernetes, выполните такие действия:

1. Запомните имя текущего элемента, например `Microsoft.AzureStackKubernetesCluster.0.2.0`.

1. Подключитесь к Azure Stack с помощью PowerShell.

1. Удалите элемент, выполнив следующий командлет PowerShell:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.2.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Дополнительная информация

[Развертывание Kubernetes в Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[Общие сведения о предложении служб в Azure Stack](azure-stack-offer-services-overview.md)