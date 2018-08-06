---
title: Потоковая передача данных в Центры событий Azure для Apache Kafka | Документация Майкрософт
description: Потоковая передача данных в концентраторы событий с помощью протокола Kafka и API-интерфейсов.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2018
ms.author: bahariri
ms.openlocfilehash: ec6061cac7188f3f94fa1ec0bf138b9398387099
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413626"
---
# <a name="stream-into-event-hubs-for-the-apache-kafka"></a>Потоковая передача данных в Центры событий для Apache Kafka
В этом кратком руководстве показано, как выполнять потоковую передачу данных в концентраторы событий с поддержкой Kafka без необходимости менять клиенты протоколов или запускать собственные кластеры. Вы узнаете, как обеспечить взаимодействие отправителей и объектов-получателей с концентраторами событий с поддержкой Kafka, изменив конфигурацию в приложениях. Центры событий Azure поддерживают [Apache Kafka версии 1.0.](https://kafka.apache.org/10/documentation.html)

> [!NOTE]
> Этот пример можно найти на сайте [GitHub](https://github.com/Azure/azure-event-hubs).

## <a name="prerequisites"></a>Предварительные требования

Ниже указаны требования для работы с этим кратким руководством.

* Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начать работу.
* [Комплект разработчика Java (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Скачайте](http://maven.apache.org/download.cgi) и [установите](http://maven.apache.org/install.html) двоичный архив Maven.
* [Git](https://www.git-scm.com/)
* [Пространство имен концентраторов событий с поддержкой Kafka](event-hubs-create.md).

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Создание пространства имен концентраторов событий с поддержкой Kafka

1. Войдите на [портал Azure] и щелкните **Создать ресурс** в левой верхней части экрана.

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

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Отправка и получение сообщений с использованием Kafka в концентраторах событий

1. Клонируйте [репозиторий концентраторов событий Azure](https://github.com/Azure/azure-event-hubs).

2. Перейдите на страницу `azure-event-hubs/samples/kafka/quickstart/producer`.

3. Обновите сведения о конфигурации для отправителя в файле по адресу `src/main/resources/producer.config` следующим образом:

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
4. Выполните код отправителя и потоковую передачу данных в концентраторы событий с поддержкой Kafka.
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
5. Перейдите на страницу `azure-event-hubs/samples/kafka/quickstart/consumer`.

6. Обновите для объекта-получателя сведения о конфигурации в файле по адресу `src/main/resources/consumer.config` следующим образом.
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. Используя клиенты Kafka, запустите код объекта-получателя и обработку из включенных концентраторов событий с поддержкой Kafka.

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Если у кластера концентратора событий с поддержкой Kafka появятся события, можно начать получать их от объекта-получателя.

## <a name="next-steps"></a>Дополнительная информация
В этой статье вы узнаете, как выполнять потоковую передачу данных в Центры событий с поддержкой Kafka без необходимости менять клиенты протоколов или запускать собственные кластеры. Для получения дополнительных сведений перейдите к следующему руководству:

> [!div class="nextstepaction"]
> [Использование Kafka MirrorMaker с концентраторами событий для экосистем Kafka](event-hubs-kafka-mirror-maker-tutorial.md)
