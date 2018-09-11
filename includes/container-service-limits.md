---
title: включение файла
description: включение файла
services: container-service
author: mmacy
ms.service: container-service
ms.topic: include
ms.date: 08/31/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 1e8913d31677f3da9b6eb9d2216d8d9ec8b186b4
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43667034"
---
| Ресурс | Ограничение по умолчанию |
| --- | :--- |
| Максимальное число кластеров в подписке | 100 |
| Максимальное число узлов в кластере | 100 |
| Максимальное число групп pod на один узел: [базовое сетевое взаимодействие с Kubenet][basic-networking] | 110 |
| Максимальное число групп pod на один узел: [расширенное сетевое взаимодействие с Azure CNI][advanced-networking] | Развертывание с помощью Azure CLI: 110<sup>1</sup><br />Шаблон Resource Manager: 110<sup>1</sup><br />Развертывание портала: 30 |

<sup>1</sup> Это значение можно изменить при развертывании кластера AKS с помощью Azure CLI или шаблона Resource Manager.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
