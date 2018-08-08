---
title: Вопросы и ответы о Службе Azure Kubernetes
description: Ответы на распространенные вопросы о Службе Azure Kubernetes.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/27/2018
ms.author: iainfou
ms.openlocfilehash: b64c770bca84fba8cbed98e420abf649897f7a17
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39345860"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Вопросы и ответы о Службе Azure Kubernetes (AKS)

В этой статье представлены ответы на часто задаваемые вопросы о Службе Azure Kubernetes.

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>В каких регионах Azure сейчас доступна Служба Azure Kubernetes (AKS)?

[Список регионов и уровни доступности][aks-regions] для Службы Azure Kubernetes см. в документации.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Применяются ли обновления для системы безопасности к узлам агентов AKS?

Azure автоматически применяет исправления системы безопасности для узлов в кластере в соответствии с графиком резервного копирования ночью. Тем не менее, вы несете ответственность за обеспечение перезагрузки узлов при необходимости. У вас есть несколько вариантов выполнения перезагрузки узла.

- Вручную на портале Azure или Azure CLI.
- Обновив кластер AKS. Кластер автоматически обновляет [узлы cordon и drain](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/), а затем создает их резервную копию с помощью последнего образа Ubuntu. Обновите образ операционной системы в узлах, не изменяя версии Kubernetes. Укажите текущую версию кластера в `az aks upgrade`.
- С помощью [Kured](https://github.com/weaveworks/kured) (управляющая программа перезагрузки с открытым исходным кодом для Kubernetes). Kured работает в виде [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) и проверяет каждый узел на наличие файла, указывающего на необходимость перезагрузки. Затем программа выполняет оркестрацию перезагрузок в кластере, применив процессы cordon и drain, описанные ранее.

## <a name="does-aks-support-node-autoscaling"></a>Поддерживает ли AKS автомасштабирование узла?

Да, автоматическое масштабирование доступно через [Kubernetes autoscaler][auto-scaler] для Kubernetes 1.10.

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>Поддерживает ли AKS для Kubernetes управление доступом на основе ролей (RBAC)?

Да, RBAC можно включить при [развертывании кластера AKS из Azure CLI или шаблона Azure Resource Manager](https://docs.microsoft.com/en-us/azure/aks/aad-integration). Скоро аналогичная функция появится и на портале Azure.

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Какие контроллеры допуска Kubernetes поддерживает AKS? Можно ли добавлять и удалять контроллеры допуска?

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

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>Можно ли ограничить доступ к серверу API Kubernetes только моей виртуальной сетью?

На данный момент нет. Сервер API Kubernetes представляется с виде общедоступного полного доменного имени (FQDN). Вы должны управлять доступом к своему кластеру с помощью [управления доступом на основе ролей Kubernetes (RBAC) и Azure Active Directory (AAD)](https://docs.microsoft.com/en-us/azure/aks/aad-integration).

## <a name="is-azure-key-vault-integrated-with-aks"></a>Интегрируется ли Azure Key Vault с AKS?

AKS пока не имеет собственной интеграции с Azure Key Vault. При этом [KeyVault Flex Volume](https://github.com/Azure/kubernetes-keyvault-flexvol) допускает прямую интеграцию из групп Kubernetes (pod) для секретов KeyVault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Можно ли запускать контейнеры Windows Server в AKS?

Для запуска контейнеров Windows Server необходимо запустить узлы под управлением Windows Server. Сейчас узлы на основе Windows Server в AKS недоступны. Тем не менее вы можете использовать Virtual Kubelet для создания расписания контейнеров Windows в Экземплярах контейнеров Azure, чтобы управлять ими как частью кластера AKS. См. дополнительные сведения об [использовании Virtual Kubelet с AKS][virtual-kubelet].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Почему с AKS создаются две группы ресурсов?

Каждое развертывание AKS охватывает две группы ресурсов. Первая создается пользователем и содержит только ресурс службы Kubernetes. Во время развертывания поставщик ресурсов AKS автоматически создает вторую группу ресурсов с именем, например *MC_myResourceGroup_myAKSCluster_eastus*. Вторая группа ресурсов содержит все ресурсы инфраструктуры, связанные с кластером (например, виртуальные машины, сеть и хранилище). Она создается для упрощения процесса очистки ресурсов.

Если вы создаете ресурсы, которые будут использоваться с кластером AKS (например, учетные записи хранения или зарезервированные общедоступные IP-адреса), их следует помещать в автоматически созданную группу ресурсов.

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS предлагает соглашение об уровне обслуживания?

В соглашении об уровне обслуживания (SLA) поставщик соглашается возместить клиенту стоимость использования службы, если уровень обслуживания не будет достигнут. Так как сама служба AKS является бесплатной, нет суммы для возмещения и, следовательно, никакого официального SLA. Тем не менее мы стремимся поддерживать доступность AKS не менее 99,5 % для сервера API Kubernetes.

<!-- LINKS - internal -->

[aks-regions]: ./container-service-quotas.md
[virtual-kubelet]: virtual-kubelet.md

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
