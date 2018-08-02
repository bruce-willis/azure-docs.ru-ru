---
title: Безопасность инфраструктуры Azure | Документы Майкрософт
description: В этой статье описывается, как корпорация Майкрософт обеспечивает безопасность центров обработки данных Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 9385c6ea8d2a04e9a8595a22e6e2ff7638394b41
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172978"
---
# <a name="azure-infrastructure-security"></a>Безопасность инфраструктуры Azure
Microsoft Azure работает в центрах обработки данных, управляемых и обслуживаемых корпорацией Майкрософт. Эти географически распределенные центры обработки данных соответствуют основным отраслевым стандартам обеспечения безопасности и надежности, например ISO/IEC 27001:2013 и NIST SP 800-53. Контроль, управление и администрирование центров обработки данных осуществляется операционным персоналом Майкрософт. Наши специалисты имеют многолетний опыт обслуживания крупнейшей в мире сети веб-служб с круглосуточным доступом.

В этой серии статей приводятся сведения о том, как корпорация Майкрософт обеспечивает безопасность инфраструктуры Azure. Темы статей:

- [Физическая безопасность](azure-physical-security.md)
- [Доступность](azure-infrastructure-availability.md)
- [Компоненты и границы](azure-infrastructure-components.md)
- [Сетевая архитектура](azure-infrastructure-network.md)
- [Рабочая сеть](azure-production-network.md)
- [База данных SQL](azure-infrastructure-sql.md)
- [Операции](azure-infrastructure-operations.md)
- [Мониторинг](azure-infrastructure-monitoring.md)
- [Целостность](azure-infrastructure-integrity.md)
- [Защита данных](azure-protection-of-customer-data.md)

## <a name="shared-responsibility-model"></a>Модель общей ответственности
Важно понимать разделение ответственности между вашей организацией и корпорацией Майкрософт. В локальной среде весь стек принадлежит вам, но при переходе в облако некоторые обязанности переходят к корпорации Майкрософт. На следующем рисунке показаны области обязанностей в зависимости от типа развертывания стека (программное обеспечение как услуга [SaaS], платформа как услуга [PaaS], инфраструктура как услуга [IaaS] и локальная среда).

![Рисунок с отображением обязанностей][1]

Независимо от типа развертывания вы всегда отвечаете за следующее:

- Данные
- Конечные точки
- Учетная запись.
- управление доступом

Убедитесь, что вы понимаете разделение обязанностей между вами и корпорацией Майкрософт в развертывании SaaS, PaaS и IaaS. Дополнительные сведения см. на странице [общих обязанностей для облачных вычислений](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о действиях корпорации Майкрософт для защиты инфраструктуры Azure приведены в следующих статьях:

- [Объекты, локальная среда и физическая безопасность в Azure](azure-physical-security.md)
- [Доступность инфраструктуры Azure](azure-infrastructure-availability.md)
- [Компоненты и границы информационной системы Azure](azure-infrastructure-components.md)
- [Сетевая архитектура Azure](azure-infrastructure-network.md)
- [Рабочая сеть Azure](azure-production-network.md)
- [Возможности безопасности Базы данных SQL Azure](azure-infrastructure-sql.md)
- [Операции и управление в рабочей среде Azure](azure-infrastructure-operations.md)
- [Мониторинг инфраструктуры Azure](azure-infrastructure-monitoring.md)
- [Целостность инфраструктуры Azure](azure-infrastructure-integrity.md)
- [Защита данных клиентов в Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png
