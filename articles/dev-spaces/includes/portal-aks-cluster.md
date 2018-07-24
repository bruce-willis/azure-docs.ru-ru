---
title: включение файла
description: включение файла
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 05736495d0d4a0c3a5072d29ad27801b6d4a7241
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967917"
---
## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Создание и включение кластера Kubernetes для Azure Dev Spaces

1. Войдите на портал Azure по адресу http://portal.azure.com.
1. Выберите **Создать ресурс**, выполните поиск **Kubernetes**, затем выберите **Kubernetes Service** (Служба Kubernetes)  >  **Создать**.

   Выполните приведенные ниже действия под каждым заголовком в форме создания кластера AKS.

    - **PROJECT DETAILS** (Сведения о проекте): выберите подписку Azure и создайте новую или выберите существующую группу ресурсов Azure.
    - **Сведения о кластере.** Для кластера AKS укажите имя, регион (сейчас нужно выбрать EastUS, Central US, WestEurope, WestUS2, CanadaCentral или CanadaEast), версию и префикс имени DNS.
    - **Масштаб**. Выберите размер виртуальной машины для узлов агента AKS и количество узлов. Если вы только начинаете работать с Azure Dev Spaces, одного узла будет достаточно, чтобы ознакомиться с функциями. После развертывания кластера вы сможете легко изменить количество узлов в любое время. Обратите внимание, что размер виртуальной машины невозможно изменить после создания кластера AKS. Но после развертывания кластера AKS можно легко создать новый кластер с виртуальными машинами большего размера и с помощью Dev Spaces выполнить повторное развертывание в большем кластере, если требуется масштабировать ресурсы.

   Выберите Kubernetes 1.10.3 или более поздней версии.

   ![Параметры конфигурации Kubernetes](../media/common/Kubernetes-Create-Cluster-2.PNG)

   По завершении выберите **Next: Networking** (Далее: сети).

1. Убедитесь, что маршрутизация приложений HTTP включена.

   ![Включение маршрутизации приложений HTTP](../media/common/Kubernetes-Create-Cluster-3.PNG)

    > [!IMPORTANT]
    > Обязательно включите параметр "Маршрутизация приложений HTTP" при создании кластера AKS. Вы не сможете изменить значение этого параметра позже.

1. Выберите нужный параметр для управления доступом на основе ролей (RBAC). Служба Azure Dev Spaces поддерживает кластеры как с включенным, так и с отключенным механизмом RBAC.

    ![Параметр RBAC](../media/common/k8s-RBAC.PNG)

1. Выберите **Review + create** (Проверить и создать), а по завершении щелкните **Create** (Создать).
