---
title: Создание концентраторов событий с поддержкой Kafka | Документация Майкрософт
description: Сведения о создании пространства имен концентраторов событий Azure с поддержкой Kafka с помощью портала Azure.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 05/07/2018
ms.author: shvija
ms.openlocfilehash: 7a05a89fdf99efb6470ee9c8695f349cf22b8ebb
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442133"
---
# <a name="create-kafka-enabled-event-hubs"></a>Создание концентраторов событий с поддержкой Kafka

Концентраторы событий Azure представляют собой платформу как услугу (PaaS) для потока больших данных, которая принимает миллионы событий в секунду и предоставляет небольшую задержку и высокий уровень пропускной способности для аналитики и визуализации в режиме реального времени.

Концентраторы событий Azure для экосистемы Kafka предоставляют конечную точку. Эта конечная точка позволяет вашему пространству имен концентраторов событий распознавать протокол [Apache Kafka](https://kafka.apache.org/intro) и API в собственном коде. Эта возможность позволяет подключиться к концентраторам событий, как к темам Kafka, не изменяя клиентов протокола или запустив собственные кластеры. Концентраторы событий для экосистемы Kafka поддерживают [Apache Kafka версии 1.0.](https://kafka.apache.org/10/documentation.html) и более поздние версии.

В этой статье описывается создание пространства имен концентраторов событий и получение строки подключения, необходимой для подключения приложений Kafka к концентраторам событий с поддержкой Kafka.

## <a name="prerequisites"></a>предварительным требованиям

Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Создание пространства имен концентраторов событий с поддержкой Kafka

1. Войдите на [портал Azure][Azure portal] и щелкните **Создать ресурс** в левой верхней части экрана.

2. Найдите концентраторы событий и выберите отображаемые параметры:
    
    ![Поиск концентраторов событий на портале](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Укажите уникальное имя и включите Kafka в пространстве имен. Нажмите кнопку **Создать**.
    
    ![Создание пространства имен](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. Создав пространство имен, на вкладке **Параметры** щелкните **Политики общего доступа** для получения строки подключения.

    ![Выбор политик общего доступа](./media/event-hubs-create/create-event-hub7.png)

5. Вы можете выбрать значение по умолчанию **RootManageSharedAccessKey** или добавить новую политику. Щелкните имя политики и скопируйте строку подключения. 
    
    ![Выбор политики](./media/event-hubs-create/create-event-hub8.png)
 
6. Добавьте эту строку подключения в конфигурацию приложения Kafka.

Теперь можно выполнять потоковую передачу событий из приложений, использующих протокол Kafka, в концентраторы событий.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о концентраторах событий см. в следующих статьях:

* [Потоковая передача данных в концентраторы событий для экосистемы Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Event Hubs for Kafka ecosystems](event-hubs-for-kafka-ecosystem-overview.md) (Концентраторы событий для экосистемы Kafka)
* [Что такое концентраторы событий?](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
