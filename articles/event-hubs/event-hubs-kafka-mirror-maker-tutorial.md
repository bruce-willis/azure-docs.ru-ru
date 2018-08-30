---
title: Использование Apache Kafka с Центрами событий Azure для экосистемы Kafka | Документация Майкрософт
description: Используйте Kafka MirrorMaker для зеркального отображения кластера Kafka в Центрах событий.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: mirror-maker
ms.custom: mvc
ms.date: 08/07/2018
ms.author: bahariri
ms.openlocfilehash: f3881d4448f44d44515ddb25072401d775d69b90
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42747194"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Использование Apache Kafka MirrorMaker с Центрами событий

В этом руководстве показано, как выполнить зеркальное отображение брокера Kafka в концентраторе событий с поддержкой Kafka с помощью средства Kafka MirrorMaker.

   ![Kafka MirrorMaker и Центры событий](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Этот пример можно найти на сайте [GitHub](https://github.com/Azure/azure-event-hubs).


Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * Создание пространства имен в Центрах событий
> * Клонирование примера проекта
> * Настройка кластера Kafka
> * Настройка Kafka MirrorMaker
> * Запуск Kafka MirrorMaker

## <a name="introduction"></a>Введение
Одним из важных аспектов для современных облачных приложений является возможность обновления, улучшения и изменения инфраструктуры без прерывания работы службы. В этом руководстве показано, каким образом концентратор событий с поддержкой Kafka и средство Kafka MirrorMaker могут интегрировать существующий конвейер Kafka в Azure путем зеркального отображения базы данных входного потока Kafka в службу "Центры событий". 

Конечная точка Kafka Центров событий Azure позволяет подключаться к Центрам событий Azure с помощью протокола Kafka (т. е. клиентов Kafka). После внесения минимальных изменений в приложение Kafka вы сможете подключаться к Центрам событий Azure и пользоваться преимуществами экосистемы Azure. Сейчас Центры событий на основе Kafka поддерживают Kafka 1.0 и более поздние версии.

## <a name="prerequisites"></a>Предварительные требования

В рамках этого руководства вам потребуются:

* Прочтите статью [Центры событий Azure для Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начать работу.
* [Комплект разработчика Java (JDK 1.7+)](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * В Ubuntu выполните команду `apt-get install default-jdk`, чтобы установить JDK.
    * Обязательно настройте переменную среды JAVA_HOME так, чтобы она указывала на папку, в которой установлен пакет JDK.
* [Скачайте](http://maven.apache.org/download.cgi) и [установите](http://maven.apache.org/install.html) двоичный архив Maven.
    * В Ubuntu выполните команду `apt-get install maven`, чтобы установить Maven.
* [Git](https://www.git-scm.com/downloads)
    * В Ubuntu выполните команду `sudo apt-get install git`, чтобы установить Git.

## <a name="create-an-event-hubs-namespace"></a>Создание пространства имен в Центрах событий

Для отправки и получения данных из любой службы Центров событий требуется пространство имен Центров событий. Инструкции о получении конечной точки Kafka концентраторов событий см. в статье [Creating a Kafka enabled Event Hub](event-hubs-create.md) (Создание концентраторов событий с поддержкой Kafka). Скопируйте строку подключения к Центрам событий для дальнейшего использования.

## <a name="clone-the-example-project"></a>Клонирование примера проекта

Теперь, когда у вас есть строка подключения к Центрам событий с поддержкой Kafka, клонируйте репозиторий Центров событий Azure и перейдите к вложенной папке `mirror-maker`.

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Настройка кластера Kafka

Инструкции по настройке кластера с требуемыми параметрами (или использовании существующего кластера Kafka) см. в [кратком руководстве по Kafka](https://kafka.apache.org/quickstart).

## <a name="configure-kafka-mirrormaker"></a>Настройка Kafka MirrorMaker

Средство Kafka MirrorMaker позволяет выполнять зеркальное отображение потока. При наличии исходного и конечного кластеров Kafka средство MirrorMaker гарантирует, что любые сообщения, отправляемые в исходный кластер, поступают в исходный и конечный кластеры. В этом примере показано, как выполнить зеркальное отображение исходного кластера Kafka в конечном концентраторе событий с поддержкой Kafka. Этот сценарий можно использовать для отправки данных из существующего конвейера Kafka в Центры событий без прерывания потока данных. 

Более подробные сведения о средстве Kafka MirrorMaker см. в [руководстве по зеркальному отображению Kafka и средству MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Чтобы настроить средство Kafka MirrorMaker, назначьте ему кластер Kafka в качестве потребителя или источника и концентратор событий с поддержкой Kafka в качестве производителя или конечного объекта.

#### <a name="consumer-configuration"></a>Конфигурация потребителя

Обновите файл конфигурации потребителя `source-kafka.config`, из которого средство MirrorMaker получает свойства исходного кластера Kafka.

##### <a name="source-kafkaconfig"></a>source-kafka.config

```xml
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Конфигурация производителя

Теперь обновите файл конфигурации производителя `mirror-eventhub.config`, который указывает средству MirrorMaker отправлять повторяющиеся (или зеркальные) данные в службу Центров событий. В частности, измените `bootstrap.servers` и `sasl.jaas.config` так, чтобы они указывали на конечную точку Kafka Центров событий. Для работы службы Центров событий требуется безопасное подключение (SASL), которое обеспечивается путем установки последних трех свойств в следующей конфигурации: 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Запуск Kafka MirrorMaker

Запустите сценарий Kafka MirrorMaker из корневого каталога Kafka с помощью только что обновленных файлов конфигурации. Скопируйте файлы конфигурации в корневой каталог Kafka или обновите пути к ним в следующей команде.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Чтобы убедиться, что события достигают концентратора событий с поддержкой Kafka, просмотрите статистику по входящим данным на [портале Azure](https://azure.microsoft.com/features/azure-portal/) или запустите потребитель в концентраторе событий.

Во время работы средства MirrorMaker события, отправляемые в исходный кластер Kafka, поступают на кластер Kafka и в зеркальную службу концентраторов событий с поддержкой Kafka. С помощью MirrorMaker и конечной точки Kafka Центров событий можно перенести существующий конвейер Kafka в управляемую службу Центров событий Azure, не изменяя существующий кластер и не прерывая текущий поток данных.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * Создание пространства имен в Центрах событий
> * Клонирование примера проекта
> * Настройка кластера Kafka
> * Настройка Kafka MirrorMaker
> * Запуск Kafka MirrorMaker

Перейдите к следующей статье, чтобы узнать больше о Центрах событий Azure для Apache Kafka:

> [!div class="nextstepaction"]
> [Использование Apache Flink с Центрами событий Azure для Kafka](event-hubs-kafka-flink-tutorial.md)