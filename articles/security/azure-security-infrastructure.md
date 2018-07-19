---
title: Безопасность инфраструктуры Azure | Документы Майкрософт
description: В статье описывается, как корпорация Майкрософт обеспечивает безопасность наших центров обработки данных Azure.
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
ms.openlocfilehash: 397bd1f904b676a6ba020ec78fb1cad05c460be1
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903862"
---
# <a name="azure-infrastructure-security"></a>Безопасность инфраструктуры Azure
Microsoft Azure работает в центрах обработки данных, управляемых и обслуживаемых корпорацией Майкрософт. Эти географически распределенные центры обработки данных соответствуют основным отраслевым стандартам обеспечения безопасности и надежности, например ISO/IEC 27001:2013 и NIST SP 800-53. Контроль, управление и администрирование центров обработки данных осуществляется операционным персоналом Майкрософт. Наши специалисты имеют многолетний опыт обслуживания крупнейшей в мире сети веб-служб с круглосуточным доступом.

В этой серии статей рассказывается, как Майкрософт обеспечивает безопасность инфраструктуры Azure. Темы статей:

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
Важно понимать разделение ответственности между вашей организацией и корпорацией Майкрософт. В локальной среде весь стек принадлежит вам, но при переходе в облако некоторые обязанности переходят к корпорации Майкрософт. В приведенной ниже таблице ответственности показано, за какие области стека в программном обеспечении как услуге (SaaS), платформе как услуге (PaaS) и инфраструктуре как услуге (IaaS) несете ответственность вы, а за какие — корпорация Майкрософт.

![Общая ответственность][1]

Ниже перечислены обязанности, которые всегда остаются за вами независимо от типа развертывания.

- Данные
- Конечные точки
- Учетная запись.
- управление доступом

Убедитесь, что вы понимаете разделение обязанностей между вами и корпорацией Майкрософт в развертывании SaaS, PaaS и IaaS. Дополнительные сведения см. в разделе [Общие обязанности для облачных вычислений](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

## <a name="next-steps"></a>Дополнительная информация
Для получения дополнительных сведений о том, что делает Майкрософт для защиты инфраструктуры Azure, см.:

- [Объекты Azure, локальная среда и физическая безопасность](azure-physical-security.md)
- [Доступность инфраструктуры Azure](azure-infrastructure-availability.md)
- [Компоненты и границы информационной системы Azure](azure-infrastructure-components.md)
- [Сетевая архитектура Azure](azure-infrastructure-network.md)
- [Рабочая сеть Azure](azure-production-network.md)
- [Возможности безопасности базы данных SQL Microsoft Azure](azure-infrastructure-sql.md)
- [Производственные операции и управление Azure](azure-infrastructure-operations.md)
- [Мониторинг инфраструктуры Azure](azure-infrastructure-monitoring.md)
- [Целостность инфраструктуры Azure](azure-infrastructure-integrity.md)
- [Защита клиентских данных в Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png
