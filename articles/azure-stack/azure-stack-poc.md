---
title: Что такое Azure Stack? | Документация Майкрософт
description: Azure Stack позволяет запускать службы Azure в вашем центре обработки данных.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 06/04/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 848df59b01cc0c03b9a5f3dae2644d469c8651bb
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234892"
---
# <a name="what-is-azure-stack"></a>Что такое Azure Stack?

Microsoft Azure Stack – это гибридная облачная платформа, позволяющая доставлять службы Azure из центра обработки данных. Эта платформа предназначена для поддержки развития бизнес-требований. Платформа Azure Stack может поддерживать новые сценарии для современных приложений, таких как пограничные и отключенные среды, или соответствовать определенным требованиям безопасности и законодательства.

Azure Stack предлагается в двух вариантах развертывания для соответствия вашим потребностям.

## <a name="azure-stack-integrated-systems"></a>Интегрированные системы Azure Stack
Интегрированные системы Azure Stack предоставляются благодаря партнерским отношениям Майкрософт и [партнеров по оборудованию](https://azure.microsoft.com/overview/azure-stack/integrated-systems/). Это позволяет создать решение с инновациями облачного уровня и простотой управления вычислениями. Так как Azure Stack предлагается в качестве встроенного оборудования и программного обеспечения системы, у вас есть необходимые гибкость и элемент управления, а также возможность внедрять инновации облачного уровня. Интегрированные системы Azure Stack, размер которых варьируется от 4 до 12 узлов, совместно поддерживаются партнером по оборудованию и корпорацией Майкрософт.  Использование интегрированных систем Azure Stack позволяет создавать новые сценарии и развертывать новые решения для производственных рабочих нагрузок.

## <a name="azure-stack-development-kit"></a>Пакет средств разработки Azure Stack

[Пакет средств разработки Azure Stack (ASDK)](.\asdk\asdk-what-is.md) – это развертывание Azure Stack с одним узлом, удобное для оценки и изучения Azure Stack.  Также вы можете использовать ASDK как среду разработки, для разработки приложений с помощью API-интерфейсов и инструментов, совместимых с Azure.

>[!Note]
>ASDK не предназначен для использования в качестве рабочей среды.

ASDK имеет такие ограничения:

* ASDK связан с одним поставщиком удостоверений Azure Active Directory (Azure AD) или служб федерации Active Directory (AD FS). В этом каталоге можно создать несколько пользователей и назначить им подписки.
* Так как компоненты Azure Stack развертываются на едином компьютере, для них доступны ограниченные физические ресурсы для клиентских ресурсов. Эта конфигурация не предназначена для оценки масштабируемости или производительности.
* Сценарии сети ограничены из-за одного узла и требований к развертыванию сетевой карты.

## <a name="next-steps"></a>Дополнительная информация

- [Основные возможности и концепции](azure-stack-key-features.md)
- [Azure Stack: An extension of Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/) (Azure Stack: расширение Azure), PDF
