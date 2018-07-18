---
title: Вопросы и ответы о Службе Azure Kubernetes
description: Ответы на распространенные вопросы о Службе Azure Kubernetes.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/25/2018
ms.author: iainfou
ms.openlocfilehash: ffd81835de82cc5a00b3f6705a7607a51bb3bfa0
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096457"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Вопросы и ответы о Службе Azure Kubernetes (AKS)

В этой статье представлены ответы на часто задаваемые вопросы о Службе Azure Kubernetes.

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>В каких регионах Azure сейчас доступна Служба Azure Kubernetes (AKS)?

- Восточная часть Австралии
- Центральная Канада
- Восточная Канада
- Центральный регион США
- Восток США
- Восток США 2
- Северная Европа
- Южная часть Великобритании
- Западная Европа
- Запад США
- Западный регион США 2

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Применяются ли обновления для системы безопасности к узлам агентов AKS?

Azure автоматически применяет исправления системы безопасности для узлов в кластере в соответствии с графиком резервного копирования ночью. Тем не менее, вы несете ответственность за обеспечение перезагрузки узлов при необходимости. У вас есть несколько вариантов выполнения перезагрузки узла.

- Вручную на портале Azure или Azure CLI.
- Обновив кластер AKS. Кластер автоматически обновляет [узлы cordon и drain](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/), а затем создает их резервную копию с помощью последнего образа Ubuntu. Обновите образ операционной системы в узлах, не изменяя версии Kubernetes. Укажите текущую версию кластера в `az aks upgrade`.
- С помощью [Kured](https://github.com/weaveworks/kured) (управляющая программа перезагрузки с открытым исходным кодом для Kubernetes). Kured работает в виде [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) и проверяет каждый узел на наличие файла, указывающего на необходимость перезагрузки. Затем он выполняет оркестрацию перезагрузок в кластере, применив процессы cordon и drain, описанные ранее.

## <a name="does-aks-support-node-autoscaling"></a>Поддерживает ли AKS автомасштабирование узла?

Да, автоматическое масштабирование доступно через [Kubernetes autoscaler][auto-scaler] для Kubernetes 1.10.

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>Поддерживает ли AKS для Kubernetes управление доступом на основе ролей (RBAC)?

Да, RBAC можно включить при развертывании кластера AKS из Azure CLI или шаблона Azure Resource Manager. Скоро аналогичная функция появится и на портале Azure.

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-this-be-configured"></a>Какие контроллеры допуска Kubernetes поддерживает AKS? Можно ли это настроить?

AKS поддерживает следующие [контроллеры допуска][admission-controllers]:

* NamespaceLifecycle
* LimitRanger
* ServiceAccount
* DefaultStorageClass
* DefaultTolerationSeconds
* MutatingAdmissionWebhook
* ValidatingAdmissionWebhook
* ResourceQuota
* DenyEscalatingExec
* AlwaysPullImages

Сейчас изменить список контроллеров допуска в AKS невозможно.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Можно ли развернуть AKS в имеющейся виртуальной сети?

Да, вы можете развернуть кластер AKS в существующей виртуальной сети с помощью [расширенного сетевого компонента](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md).

## <a name="is-azure-key-vault-integrated-with-aks"></a>Интегрируется ли Azure Key Vault с AKS?

AKS пока не имеет собственной интеграции с Azure Key Vault. Однако существуют предложенные сообществом решения, такие как [acs-keyvault-agent от Hexadite][hexadite].

## <a name="can-i-run-windows-server-containers-on-aks"></a>Можно ли запускать контейнеры Windows Server в AKS?

Для запуска контейнеров Windows Server необходимо запустить узлы под управлением Windows Server. Сейчас узлы на основе Windows Server в AKS недоступны. Если вам необходимо запустить контейнеры Windows Server в Kubernetes в Azure, см. документацию [по acs-engine](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/windows.md).

## <a name="why-are-two-resource-groups-created-with-aks"></a>Почему с AKS создаются две группы ресурсов?

Каждое развертывание AKS охватывает две группы ресурсов. Первая создается пользователем и содержит только ресурс службы Kubernetes. Во время развертывания поставщик ресурсов AKS автоматически создает вторую группу ресурсов с именем, например *MC_myResourceGroup_myAKSCluster_eastus*. Вторая группа ресурсов содержит все ресурсы инфраструктуры, связанные с кластером (например, виртуальные машины, сеть и хранилище). Она создается для упрощения процесса очистки ресурсов.

Если вы создаете ресурсы, которые будут использоваться с кластером AKS (например, учетные записи хранения или зарезервированный общедоступный IP-адрес), их следует помещать в автоматически созданную группу ресурсов.

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS предлагает соглашение об уровне обслуживания?

В соглашении об уровне обслуживания (SLA) поставщик соглашается возместить клиенту стоимость использования службы, если уровень обслуживания не будет достигнут. Так как сама служба AKS является бесплатной, нет суммы для возмещения и, следовательно, никакого официального SLA. Тем не менее мы стремимся поддерживать доступность не менее 99,5 % для сервера API Kubernetes.

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
