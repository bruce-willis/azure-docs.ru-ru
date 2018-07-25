---
title: Контрольный список готовности рабочей среды Azure Service Fabric | Документация Майкрософт
description: Подготовка приложения Service Fabric и рабочей среде кластера с помощью рекомендаций.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/10/2018
ms.author: subramar
ms.openlocfilehash: 5092bf377f6ab213a2db52e01fb7b89ff0c218ce
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39127491"
---
# <a name="production-readiness-checklist"></a>Контрольный список готовности рабочей среды

Готовы ли ваше приложение и кластер к приему рабочего трафика? Запуск и тестирование приложения и кластера не обязательно означает, что они готовы к перемещению в рабочую среду. Обеспечьте бесперебойную работу приложения и кластера, выполнив следующий контрольный список. Мы настоятельно рекомендуем выполнить все его пункты. Разумеется, вы можете использовать альтернативные решения для какого-либо пункта (например, собственные инфраструктуры диагностики).


## <a name="pre-requisites-for-production"></a>Предварительные требования для рабочей среды

1. Для кластеров с более чем 20 ядрами или 10 узлами создайте выделенный тип основного узла для системных служб. Добавьте [ограничения размещения](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md), чтобы зарезервировать тип основного узла для системных служб. 
2. Используйте номер SKU D2v2 или выше для типа основного узла. Рекомендуется выбрать номер SKU с емкостью диска по меньшей мере 50 ГБ.
2. Рабочие кластеры должны быть [защищены](service-fabric-cluster-security.md). Примером настройки безопасного кластера может послужить этот [шаблон кластера](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG). Используйте общие имена сертификатов и старайтесь не использовать самозаверяющие сертификаты.
4. Добавьте [ограничения ресурсов для контейнеров и служб](service-fabric-resource-governance.md) таким образом, чтобы они не занимали более 75 % ресурсов узла. 
5. Изучите и задайте [уровень устойчивости](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Уровень устойчивости Silver или выше рекомендуется использовать для типов узлов, на которых выполняются рабочие нагрузки с отслеживанием состояния. Для типа основного узла следует задать уровень устойчивости не ниже Silver.
6. Изучите и выберите [уровень надежности](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) типа узла. Рекомендуется использовать уровень надежности Silver или выше.
7. Протестируйте загрузку и масштабирование своих рабочих нагрузок, чтобы определить [требования к емкости](service-fabric-cluster-capacity.md) кластера. 
8. Службы и приложения отслеживаются, также создаются и сохраняются журналы приложений. При этом могут создаваться оповещения. Пример приведен в разделах [Добавление ведения журнала в приложение Service Fabric](service-fabric-how-to-diagnostics-log.md) и [Мониторинг контейнеров с помощью Log Analytics](service-fabric-diagnostics-oms-containers.md).
9. Для мониторинга кластера используются оповещения (например, посредством [OMS](service-fabric-diagnostics-event-analysis-oms.md)). 
10. Для мониторинга базовой инфраструктуры масштабируемого набора виртуальных машин также используются оповещения (например, посредством [Log Analytics](service-fabric-diagnostics-oms-agent.md)).
11. В кластере всегда есть [основные и дополнительные сертификаты](service-fabric-cluster-security-update-certs-azure.md) (то есть вы не будете заблокированы).
12. Используйте разные кластеры для среды разработки, промежуточной среды и рабочей среды. 
13. [Обновления приложений](service-fabric-application-upgrade.md) и [обновления кластера](service-fabric-tutorial-upgrade-cluster.md) следует сначала проверить на кластерах разработки и промежуточных кластерах. 
14. Отключите автоматические обновления на рабочих кластерах и включите их на кластерах разработки и промежуточных кластерах (при необходимости можно выполнить откат). 
15. Установите целевую точку восстановления (RPO) для службы, а также настройте [процесс аварийного восстановления](service-fabric-disaster-recovery.md) и протестируйте его.
16. Запланируйте [масштабирование](service-fabric-cluster-scaling.md) кластера вручную или программным способом.
17. Запланируйте [установку исправлений](service-fabric-patch-orchestration-application.md) на узлах кластера. 
18. Установите конвейер CI/CD, чтобы последние внесенные изменения постоянно проверялись. Например, можно использовать [VSTS](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) или [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md).
19. Выполните нагрузочное тестирование кластеров разработки и промежуточных кластеров с помощью [службы анализа сбоев](service-fabric-testability-overview.md) и вызовите управляемый [хаос](service-fabric-controlled-chaos.md). 
20. Запланируйте [масштабирование](service-fabric-concepts-scalability.md) приложений. 


Если вы используете модель программирования Reliable Services или Reliable Actors в Service Fabric, необходимо выполнить следующие пункты.
21. Обновляйте приложения во время локальной разработки, чтобы убедиться, что код службы учитывает маркер отмены в методе `RunAsync` и закрывает пользовательские прослушиватели связи.
22. Избегайте [типичных проблем](service-fabric-work-with-reliable-collections.md) при использовании Reliable Collections.
23. Отслеживайте счетчики производительности памяти среды CLR .NET при выполнении нагрузочных тестов. Кроме того, проверяйте наличие высокой частоты сбора мусора или неконтролируемого увеличения размера кучи.
24. Храните автономные резервные копии [Reliable Services и Reliable Actors](service-fabric-reliable-services-backup-restore.md) и проверьте процесс восстановления. 


## <a name="optional-best-practices"></a>Дополнительные рекомендации

Хотя выше перечислены достаточные предварительные требования для переноса в рабочую среду, следует также рассмотреть следующие пункты.
25. Подключите [модель работоспособности Service Fabric](service-fabric-health-introduction.md), чтобы расширить встроенные возможности оценки работоспособности и создания отчетов.
26. Разверните пользовательскую службу наблюдения, которая отслеживает приложение и сообщает о [загрузке](service-fabric-cluster-resource-manager-metrics.md) для [балансировки ресурсов](service-fabric-cluster-resource-manager-balancing.md). 


## <a name="next-steps"></a>Дополнительная информация
* [Руководство. Развертывание кластера Service Fabric на платформе Windows в виртуальной сети Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Руководство. Развертывание кластера Service Fabric на платформе Linux в виртуальной сети Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Дополнительные сведения о [жизненном цикле приложения](service-fabric-application-lifecycle.md) Service Fabric.
