---
title: Общие сведения о службе Azure Kubernetes
description: Служба Azure Kubernetes упрощает развертывание и администрирование контейнерных приложений в Azure.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 06/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 294ccd70e0339ed5ad68022f002e4864fc52b452
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286726"
---
# <a name="azure-kubernetes-service-aks"></a>Служба Azure Kubernetes (AKS)

Служба Azure Kubernetes (AKS) упрощает развертывание управляемого кластера Kubernetes в Azure. AKS снижает сложность управления службой Kubernetes и связанные временные затраты, делегируя ответственность за большую их часть облаку Azure. Размещенная в Azure служба Kubernetes отвечает за критические задачи, в частности за мониторинг работоспособности и техническое обслуживание. Кроме того, эта служба предоставляется бесплатно, оплачиваются только узлы агента в кластерах, а не мастера.

В этом документе представлены общие сведения о компонентах службы Kubernetes Azure (AKS).

## <a name="flexible-deployment-options"></a>Параметры гибкого развертывания

Служба Azure Kubernetes предлагает портал, командную строку и параметры развертывания управляемые шаблонами (шаблоны Resource Manager и Terraform). При развертывании кластера AKS мастер Kubernetes и все узлы развертываются и настраиваются под ваши потребности. Дополнительные компоненты, например дополнительные сети, интеграция Azure Active Directory и мониторинг, можно также настроить во время развертывания.

Дополнительные сведения см. в разделах [Краткое руководство по развертыванию кластера службы Azure Kubernetes][aks-portal] (с помощью портала) или [Краткое руководство по развертыванию кластера службы Azure Kubernetes][aks-cli] (с помощью CLI).

## <a name="identity-and-security-management"></a>Управление идентификаторами и безопасностью

Кластеры AKS поддерживают [Управление доступом на основе ролей (RBAC)][kubernetes-rbac]. Кластер AKS также может быть настроен для интеграции с Azure Active Directory. В этой конфигурации доступ к службе Kubernetes можно настроить на основе идентификатора и группового членства в Active Directory.

Дополнительные сведения см. в разделе [Integrate Azure Active Directory with AKS][aks-aad] (Включение AKS в Azure AD).

## <a name="integrated-logging-and-monitoring"></a>Встроенное ведение журналов и мониторинг

Контейнер работоспособности обеспечивает визуализацию производительности, собирая метрики памяти и процессора из контейнеров, узлов и контроллеров. Также собираются журналы контейнеров. Эти данные хранятся в рабочей области службы Log Analytics и доступны через портал Azure, Azure CLI или конечную точку REST.

Дополнительные сведения см. в разделе [Мониторинг работоспособности службы Azure Kubernetes (AKS) (предварительная версия)][container-health].

## <a name="cluster-node-scaling"></a>Масштабирование узла кластера

По мере увеличения спроса на ресурсы узлы кластера AKS можно соответственно масштабировать. Если потребности в ресурсах снижаются, узлы в кластере можно удалить путем масштабирования. Операции масштабирования AKS можно выполнить с помощью портала Azure или интерфейса командной строки Azure.

Дополнительные сведения см. в разделе [Масштабирование кластера службы Azure Kubernetes][aks-scale].

## <a name="cluster-node-upgrades"></a>Обновления узла кластера

Служба Kubernetes Azure предлагает несколько версий Kubernetes. После того, как новая версия станет доступной в AKS, с помощью средств портала Azure или Azure CLI кластер можно обновить. Чтобы свести к минимуму время простоя работающих приложений, в процессе обновления узлы тщательно блокируются и останавливаются.

Дополнительные сведения см. в статье [Обновление кластера службы Azure Kubernetes (AKS)][aks-upgrade].

## <a name="http-application-routing"></a>Маршрутизация приложений HTTP

Решение маршрутизации приложений HTTP упрощает доступ к приложениям, развернутым в кластере Службы контейнеров Azure (AKS). При установке этого параметра решение маршрутизации приложений HTTP настраивает контроллер входящего трафика в кластере AKS. После того, как приложения были развернуты, общедоступные DNS-имена будут настроены автоматически.

Дополнительные сведения см. в статье [Маршрутизация приложений HTTP][aks-http-routing].

## <a name="gpu-enabled-nodes"></a>Узлы с графическим процессором

Служба AKS поддерживает создание пулов узлов с процессорами GPU. В настоящее время Azure предоставляет одну или несколько виртуальных машин с поддержкой процессоров GPU. Виртуальные машины с поддержкой процессоров GPU предназначены для рабочих нагрузок с большим объемом вычислений, графической обработки и визуализаций.

Дополнительные сведения см. в статье [Использование GPU в AKS][aks-gpu].

## <a name="development-tooling-integration"></a>Интеграция средств разработки

Kubernetes обладает обширной экосистемой средств разработки и управления. К ним можно отнести Helm, Draft а также расширение Kubernetes для Visual Studio Code Вместе со Службой Azure Kubernetes эти средства будут работать бесперебойно.

Кроме того, рабочие среды Azure Dev Spaces предоставляют быстрый итеративный интерфейс разработки Kubernetes для команд. Для того, чтобы запускать и отлаживать контейнеры непосредственно в службе Azure Kubernetes (AKS) требуется минимальная конфигурация.

Дополнительные сведения см. в статье [Рабочие среды Azure для разработчиков][azure-dev-spaces].

В проекте Azure DevOps обеспечивается простое решение, которое позволяет использовать существующий код и репозиторий Git в Azure. Проектом DevOps автоматически создаются такие ресурсы Azure, как AKS, конвейер выпуска в VSTS, содержащий определение сборки для CI, который настраивает определения выпуска для компакт-диска и создает ресурс Azure Application Insights для мониторинга.

Дополнительные сведения см. [Tutorial: Deploy your ASP.NET Core App to Azure Kubernetes Service (AKS) with the Azure DevOps Project][azure-devops] (Руководство. Разверните ASP.NET Core App в службе Azure Kubernetes (AKS) с помощью проекта Azure DevOps.).

## <a name="virtual-network-integration"></a>Интеграция виртуальной сети

Кластер AKS может быть развернут в существующей виртуальной сети. В этой конфигурации, каждому контейнеру pod в кластере назначается IP-адрес в виртуальной сети. Они могут напрямую взаимодействовать с другими контейнерами pod в кластере, а также другими узлами в виртуальной сети. Также группа контейнеров pod может подключаться к другим службам одноранговой виртуальной сети, а также к локальным сетям через ExpressRoute и VPN типа "сеть — сеть" (S2S).

Дополнительные сведения см. в статье [Конфигурация сети в службе Azure Kubernetes (AKS)][aks-networking].

## <a name="private-container-registry"></a>Реестр частных контейнеров

Интеграция с реестром контейнеров Azure (ACR) частного хранилища образов Docker.

Дополнительные сведения см в статье [Общие сведения о частных реестрах контейнеров Docker в Azure][acr-docs].

## <a name="storage-volume-support"></a>Поддержка тома хранилища

Служба Azure Kubernetes (AKS) поддерживает подключение томов хранилища для постоянных данных. Кластеры AKS создаются с поддержкой файлов и дисков Azure.

Дополнительные сведения см. в статьях [Использование постоянных томов со службой файлов Azure][azure-files] и [Постоянные тома с дисками Azure][azure-disk].

## <a name="docker-image-support"></a>Поддержка образа Docker

Служба Azure Kubernetes (AKS) поддерживает формат образа Docker.

## <a name="kubernetes-certification"></a>Сертификация Kubernetes

Служба Azure Kubernetes (AKS) была сертифицирована Cloud Native Computing Foundation (CNCF) в качестве согласованной с Kubernetes.

## <a name="regulatory-compliance"></a>Соблюдение нормативных требований

Служба Azure Kubernetes (AKS) соответствует всем требованиям стандартов SOC, ISO и PCI DSS.

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о развертывании AKS и управлении ею из краткого руководства по AKS.

> [!div class="nextstepaction"]
> [Краткое руководство по развертыванию кластера службы Azure Kubernetes (AKS)][aks-cli]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./aad-integration.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./networking-overview.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./scale-cluster.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/en-us/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/en-us/azure/devops-project/overview
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md

