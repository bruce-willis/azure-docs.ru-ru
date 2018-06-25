---
title: Вопросы и ответы о Службе Azure Kubernetes
description: Ответы на распространенные вопросы о Службе Azure Kubernetes.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/08/2018
ms.author: nepeters
ms.openlocfilehash: 79236ae7134a27b9a5b89ee8151803befa7b51e1
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260803"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Вопросы и ответы о Службе Azure Kubernetes (AKS)

В этой статье представлены ответы на часто задаваемые вопросы о Службе Azure Kubernetes.

> [!IMPORTANT]
> Служба Azure Kubernetes (AKS) сейчас доступна в режиме **предварительной версии**. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.
>

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>В каких регионах Azure сейчас доступна Служба Azure Kubernetes (AKS)?

- Центральная Канада
- Восточная Канада
- Центральный регион США
- Восток США
- Западная Европа

## <a name="when-will-additional-regions-be-added"></a>Когда будут добавлены другие регионы?

Другие регионы будут добавлены по мере увеличения спроса.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Применяются ли обновления для системы безопасности к узлам агентов AKS?

Azure автоматически применяет исправления системы безопасности для узлов в кластере в соответствии с графиком резервного копирования ночью. Тем не менее, вы несете ответственность за обеспечение перезагрузки узлов при необходимости. У вас есть несколько вариантов выполнения перезагрузки узла.

- Вручную на портале Azure или Azure CLI.
- Обновив кластер AKS. Кластер автоматически обновляет [узлы cordon и drain](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/), а затем создает их резервную копию с помощью последнего образа Ubuntu. Обновите образ операционной системы в узлах, не изменяя версии Kubernetes. Укажите текущую версию кластера в `az aks upgrade`.
- С помощью [Kured](https://github.com/weaveworks/kured) (управляющая программа перезагрузки с открытым исходным кодом для Kubernetes). Kured работает в виде [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) и проверяет каждый узел на наличие файла, указывающего на необходимость перезагрузки. Затем выполняет оркестрацию этих перезагрузок в кластере, применив процессы cordon и drain, описанные ранее.

## <a name="do-you-recommend-customers-use-acs-or-aks"></a>Какую службу вы рекомендуете использовать клиентам: ACS или AKS?

Пока AKS находится в режиме предварительной версии, рекомендуется создавать рабочие кластеры с помощью ACS для Kubernetes или [acs-engine](https://github.com/azure/acs-engine). AKS используется в средах разработки и тестирования, а также для подтверждающих концепцию развертываний.

## <a name="when-will-acs-be-deprecated"></a>Когда служба ACS будет считаться устаревшей?

Служба ACS будет считаться устаревшей, когда служба AKS станет общедоступной. У вас будет 12 месяцев после указанной даты для переноса кластеров в AKS. В течение этих 12 месяцев вы сможете выполнять все операции ACS.

## <a name="does-aks-support-node-autoscaling"></a>Поддерживает ли AKS автомасштабирование узла?

Автомасштабирование узла не поддерживается, но мы планируем добавить такую возможность. Вы можете просмотреть этот открытый код [реализации автомасштабирования][auto-scaler].

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>Поддерживает ли AKS для Kubernetes управление доступом на основе ролей (RBAC)?

Нет, сейчас в AKS не поддерживается RBAC, но в скором времени это будет исправлено.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Можно ли развернуть AKS в имеющейся виртуальной сети?

Да, это можно сделать с помощью [расширенного сетевого взаимодействия](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md).

## <a name="is-azure-key-vault-integrated-with-aks"></a>Интегрируется ли Azure Key Vault с AKS?

Нет, не интегрируется, но мы планируем реализовать такую возможность. Тем временем поработайте со решением от [Hexadite][hexadite].

## <a name="can-i-run-windows-server-containers-on-aks"></a>Можно ли запускать контейнеры Windows Server в AKS?

Для запуска контейнеров Windows Server необходимо запустить узлы под управлением Windows Server. В настоящее время узлы под управлением Windows Server находятся в режиме [закрытой предварительной версии](https://azure.microsoft.com/en-us/blog/kubernetes-on-azure/). Если вам необходимо запустить контейнеры Windows Server в Kubernetes в Azure вне предварительной версии, см. документацию [по acs-engine](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/windows.md).

## <a name="why-are-two-resource-groups-created-with-aks"></a>Почему с AKS создаются две группы ресурсов?

Каждое развертывание AKS охватывает две группы ресурсов. Первая создается пользователем и содержит только ресурсы AKS. Во время развертывания поставщик ресурсов AKS автоматически создает вторую группу ресурсов с именем, например *MC_myResourceGroup_myAKSCluster_eastus*. Вторая группа ресурсов содержит все ресурсы инфраструктуры, связанные с кластером (например, виртуальные машины, сеть и хранилище). Она создается для упрощения процесса очистки ресурсов.

Если вы создаете ресурсы, которые будут использоваться с кластером AKS (например, учетные записи хранения или зарезервированный общедоступный IP-адрес), их следует помещать в автоматически созданную группу ресурсов.

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS предлагает соглашение об уровне обслуживания?

В соглашении об уровне обслуживания (SLA) поставщик соглашается возместить клиенту стоимость использования службы, если уровень обслуживания не будет достигнут. Так как сама служба AKS является бесплатной, нет суммы для возмещения и, следовательно, никакого официального SLA. Тем не менее мы стремимся поддерживать доступность не менее 99,5 % для сервера API Kubernetes.

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
