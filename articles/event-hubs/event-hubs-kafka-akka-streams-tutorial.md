---
title: Использование Akka Streams с концентраторами событий Azure для экосистемы Kafka | Документация Майкрософт
description: Подключение Akka Streams к концентратору событий с поддержкой Kafka
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.date: 06/06/2018
ms.author: bahariri
ms.openlocfilehash: 9db27340a2210ea0be0564b15241952477e592ba
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303184"
---
# <a name="using-akka-streams-with-event-hubs-for-kafka-ecosystem"></a>Использование Akka Streams с концентраторами событий для экосистемы Kafka

Одним из ключевых преимуществ использования платформы Apache Kafka является экосистема платформ, к которым она может подключиться. Концентраторы событий с поддержкой Kafka сочетают в себе гибкость Kafka с масштабируемостью, согласованностью и поддержкой экосистемы Azure.

В этом руководстве показано, как выполнять подключение Akka Streams к концентраторам событий с поддержкой Kafka без необходимости менять клиенты протоколов или запускать собственные кластеры. Концентраторы событий Azure для экосистемы Kafka поддерживают [Apache Kafka версии 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством выполните следующие предварительные требования:

* Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начать работу.
* [Комплект разработчика Java (JDK) 1.8+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
    * В Ubuntu выполните команду `apt-get install default-jdk`, чтобы установить JDK.
    * Обязательно настройте переменную среды JAVA_HOME так, чтобы она указывала на папку, в которой установлен пакет JDK.
* [Скачайте](http://maven.apache.org/download.cgi) и [установите](http://maven.apache.org/install.html) двоичный архив Maven.
    * В Ubuntu выполните команду `apt-get install maven`, чтобы установить Maven.
* [Git](https://www.git-scm.com/downloads)
    * В Ubuntu выполните команду `sudo apt-get install git`, чтобы установить Git.

## <a name="create-an-event-hubs-namespace"></a>Создание пространства имен концентраторов событий

Для отправки и получения данных из любой службы концентраторов событий требуется пространство имен концентраторов событий. Инструкции по получению конечной точки Kafka концентраторов событий см. в статье [Создание концентраторов событий с поддержкой Kafka](event-hubs-create-kafka-enabled.md). Скопируйте строку подключения к концентраторам событий для дальнейшего использования.

## <a name="clone-the-example-project"></a>Клонирование примера проекта

Теперь, когда у вас есть строка подключения к концентраторам событий с поддержкой Kafka, клонируйте репозиторий концентраторов событий Azure и перейдите к вложенной папке `akka`.

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/akka
```

## <a name="akka-streams-producer"></a>Производитель Akka Streams

Используя предоставленный пример производителя Akka Streams, отправьте сообщения в службу концентратора событий.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Предоставление конечной точки Kafka концентраторов событий

#### <a name="producer-applicationconf"></a>application.conf производителя

Обновите значения `bootstrap.servers` и `sasl.jaas.config` в `producer/src/main/resources/application.conf`, чтобы перенаправить производителя на конечную точку Kafka концентраторов событий с правильной аутентификацией.

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-producer-from-the-command-line"></a>Запуск производителя из командной строки

Чтобы запустить производитель из командной строки, создайте JAR-файл, а затем запустите его из Maven (или создайте JAR-файл с помощью Maven, затем запустите его в Java, добавив необходимые JAR-файлы Kafka в путь к классу):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

Производитель начинает отправлять события в концентратор событий с поддержкой Kafka в раздел `test` и выводит события в STDOUT.

## <a name="akka-streams-consumer"></a>Потребитель Akka Streams

Используя предоставленный пример потребителя, получите сообщения от концентраторов событий с поддержкой Kafka.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Предоставление конечной точки Kafka концентраторов событий

#### <a name="consumer-applicationconf"></a>application.conf потребителя

Обновите значения `bootstrap.servers` и `sasl.jaas.config` в `consumer/src/main/resources/application.conf`, чтобы перенаправить потребителя на конечную точку Kafka концентраторов событий с правильной аутентификацией.

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-consumer-from-the-command-line"></a>Запуск потребителя из командной строки

Чтобы запустить потребителя из командной строки, создайте JAR-файл, а затем запустите его из Maven (или создайте JAR-файл с помощью Maven, затем запустите его в Java, добавив необходимые JAR-файлы Kafka в путь к классу):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Если в концентраторе событий с поддержкой Kafka есть события (например, если ваш производитель также работает), потребитель начинает получать события из раздела `test`. 

Дополнительные сведения об Akka Streams см. в руководстве по [Akka Streams Kafka](https://doc.akka.io/docs/akka-stream-kafka/current/home.html).

## <a name="next-steps"></a>Дополнительная информация

* [Что такое концентраторы событий?](event-hubs-what-is-event-hubs.md)
* [Подробнее о концентраторах событий для экосистемы Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Используйте [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) для [потоковой передачи событий из локальной системы Kafka к концентраторам событий с поддержкой Kafka в облаке](event-hubs-kafka-mirror-maker-tutorial.md).
* Узнайте, как выполнять потоковую передачу в концентраторы событий с поддержкой Kafka с помощью [собственных приложений Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md) или [Apache Flink](event-hubs-kafka-flink-tutorial.md).
