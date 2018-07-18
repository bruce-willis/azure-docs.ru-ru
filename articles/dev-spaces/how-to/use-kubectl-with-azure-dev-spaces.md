---
title: Использование kubectl с Azure Dev Spaces | Документация Майкрософт
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Быстрая разработка в Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 3dc0dd4b571f716bcabb67c4cbef1ea6d762eb94
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248664"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Использование kubectl с Azure Dev Spaces

В Azure Dev Spaces можно обращаться к кластеру Kubernetes и использовать существующие средства Kubernetes, например `kubectl`.

При выполнении команды `az aks use-dev-spaces` автоматически добавляется контекст конфигурации `kubectl`, поэтому средство kubectl уже должно быть подключено к кластеру Kubernetes в Azure Dev Spaces. Примеры:
- Подтверждение текущего контекста: `kubectl config current-context`.
- Вывод списка всех доступных контекстов: `kubectl config get-contexts`. 
- Изменение контекста: `kubectl config use-context <context-name>`.
- Просмотр панели мониторинга Kubernetes: выполните команду `kubectl proxy`, а затем откройте в браузере адрес, который создает эта команда (добавьте `/ui` к URL-адресу для перехода к панели мониторинга Kubernetes).
- Вывод списка запущенных служб в пространстве Azure Dev Spaces по умолчанию с именем *default*: `kubectl get services --namespace=default`.

