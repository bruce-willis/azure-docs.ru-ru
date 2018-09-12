---
title: Защита службы SQL Azure и данных в центре безопасности Azure | Документация Майкрософт
description: В этом документе рассматриваются рекомендации из центра безопасности Azure, которые помогают защитить ваши данные и службу SQL Azure, а также обеспечить соответствие политикам безопасности.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2017
ms.author: terrylan
ms.openlocfilehash: 0b3b8082412b12a0fffbaea04409a8bbb3f4ac15
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44295383"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Защита службы SQL Azure и данных в центре безопасности Azure
Центр безопасности Azure анализирует состояние безопасности ресурсов Azure. Когда центр безопасности выявляет потенциальные уязвимости в системе безопасности, он создает рекомендации по настройке необходимых элементов управления.  Рекомендации относятся к следующим типам ресурсов Azure: виртуальные машины, сети, служба SQL и данные, приложения.

В этой статье рассматриваются рекомендации, которые касаются службы SQL Azure и данных, а также рекомендации включения аудита для серверов и баз данных SQL Azure, включения шифрования для баз данных SQL и включения шифрования учетной записи хранения Azure.  В таблице ниже приведены справочные сведения относительно доступных рекомендаций для службы SQL и данных, а также о том, что происходит после их применения.

## <a name="available-sql-service-and-data-recommendations"></a>Доступные рекомендации для службы SQL и данных
| Рекомендации | ОПИСАНИЕ |
| --- | --- |
| [Включение аудита и обнаружения угроз на серверах SQL Server](security-center-enable-auditing-on-sql-servers.md) |Рекомендует включить аудит и обнаружение угроз для серверов SQL Azure (только для службы SQL Azure, не включая экземпляры SQL, работающие на виртуальных машинах). |
| [Включение аудита и обнаружения угроз для баз данных SQL](security-center-enable-auditing-on-sql-databases.md) |Рекомендует включить аудит и обнаружение угроз для баз данных SQL в Azure (только для службы SQL Azure, не включая экземпляры SQL, работающие на виртуальных машинах). |
| [Включение прозрачного шифрования данных в базах данных SQL](security-center-enable-transparent-data-encryption.md) |Рекомендует включить шифрование для баз данных SQL (только для службы SQL Azure). |

## <a name="see-also"></a>См. также
Дополнительные сведения о рекомендациях, которые относятся к другим типам ресурсов Azure, см. в следующих статьях:

* [Защита виртуальных машин в центре безопасности Azure.](security-center-virtual-machine-recommendations.md)
* [Защита приложений в центре безопасности Azure](security-center-application-recommendations.md)
* [Защита сети в центре безопасности Azure.](security-center-network-recommendations.md)

Дополнительные сведения о Центре безопасности см. в следующих статьях:

* [Настройка политик безопасности в Центре безопасности Azure](security-center-policies.md) — узнайте, как настроить политики безопасности для подписок и групп ресурсов Azure.
* [Управление оповещениями безопасности в Центре безопасности Azure и реагирование на них](security-center-managing-and-responding-alerts.md) — узнайте, как управлять оповещениями системы безопасности и реагировать на них.
* [Центр безопасности Azure: часто задаваемые вопросы](security-center-faq.md) — часто задаваемые вопросы об использовании этой службы.
