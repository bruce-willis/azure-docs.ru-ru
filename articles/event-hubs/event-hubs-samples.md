---
title: Примеры концентраторов событий Azure | Документация Майкрософт
description: Примеры концентраторов событий Azure
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2018
ms.author: shvija
ms.openlocfilehash: fbde6e5a5ed053d6c151b3af25535c397a496ef4
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005340"
---
# <a name="event-hubs-samples"></a>Примеры концентраторов событий 
Примеры концентраторов событий можно найти на [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples). В примерах продемонстрированы основные функции [Центров событий Azure](/azure/event-hubs/). В этой статье приведены категории примеров с описаниями и ссылками.

## <a name="net-samples"></a>Примеры для .NET

| Имя примера | ОПИСАНИЕ | 
| ----------- | ----------- | 
| [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) | В этом примере показано, как написать консольное приложение для .NET Core, которое отправляет набор событий в концентратор событий. |
| [SampleEHReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) | В этом примере показано, как создать консольное приложение .NET Core для получения набора событий из концентратора событий с помощью библиотеки узла обработчика событий.  | 

## <a name="java-samples"></a>Примеры для Java

| Имя примера | ОПИСАНИЕ | 
| ----------- | ----------- | 
| [SendBatch](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SendBatch)  | В этом примере показано, как принимать пакеты событий в концентратор событий. | 
| [SimpleSend](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend) | В этом примере показано, как принимать события в концентратор событий. |
| [AdvanceSendOptions](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/AdvancedSendOptions) | Этот пример иллюстрирует различные параметры, доступные для приема событий концентраторами. |
| [ReceiveByDateTime](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveByDateTime) | В этом примере показано, как получать события из раздела концентратора событий с использованием определенного смещения даты и времени. |
| [ReceiveUsingOffset](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingOffset) | В этом примере показано, как получать события из раздела концентратора событий с использованием определенного смещения даты. |  
| [ReceiveUsingSequenceNumber](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingSequenceNumber) | В этом примере показано, как получать события из разделов концентратора событий, используя порядковый номер. |   
| [EventProcessorSample](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/EventProcessorSample) |В этом примере показано, как получать события из концентратора событий, используя узел обработчика событий, который обеспечивает автоматический выбор разделов и переключение между несколькими параллельными приемниками. | 
| [AutoScaleOnIngress](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/AutoScaleOnIngress) | В этом примере показано, как концентратор событий может автоматически масштабироваться при высоких нагрузках. Пример отправляет события со скоростью, которая превышает установленную скорость концентратора событий, в результате чего концентратор событий будет увеличивать масштаб. | 
| [IngressBenchmark](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/IngressBenchmark) | Этот пример позволяет измерять скорость приема. | 

## <a name="next-steps"></a>Дополнительная информация
Чтобы узнать больше о концентраторах событий, обратитесь к следующим статьям:

- 
  [Общие сведения о Центрах событий](event-hubs-what-is-event-hubs.md)
- [Особенности концентраторов событий](event-hubs-features.md)
- [Часто задаваемые вопросы о концентраторах событий](event-hubs-faq.md)