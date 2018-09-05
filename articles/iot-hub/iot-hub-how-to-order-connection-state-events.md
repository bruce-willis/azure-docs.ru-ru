---
title: Упорядочение событий изменения состояния подключения устройства из Центра Интернета вещей Azure с использованием Azure Cosmos DB | Документация Майкрософт
description: В этой статье описывается, как упорядочивать и записывать события изменения состояния подключения устройств из Центра Интернета вещей Azure с использованием Azure Cosmos DB для поддержания последнего состояния подключения.
services: iot-hub
documentationcenter: ''
author: ash2017
manager: briz
editor: ''
ms.service: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: asrastog
ms.openlocfilehash: dd3c750e93646624e46c46afd871ef75af905bf0
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43145848"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Упорядочение событий изменения состояния подключения устройства из Центра Интернета вещей Azure с использованием Azure Cosmos DB

Сетка событий Azure поможет вам создавать приложения на основе событий и легко интегрировать события Центра Интернета вещей в бизнес-приложения. В этой статье вы познакомитесь с настройкой, которую можно использовать для отслеживания и сохранения последнего состояния подключения устройства в Cosmos DB. Мы будем использовать порядковый номер, доступный в событиях отключения и подключения устройства, и сохраним последнее состояние в Cosmos DB. Кроме того, мы используем хранимую процедуру — логику приложения, которая выполняется в коллекции Cosmos DB.

Порядковый номер является строковым представлением шестнадцатеричного числа. Сравнение строк можно использовать для идентификации большего числа. Если вы преобразуете строку в шестнадцатеричную, то число будет 256-битным. Порядковый номер строго возрастает, и номер последнего события будет выше номера других событий. Это полезно, если у вас есть устройство, которое часто подключается и отключается, и вы хотите убедиться, что только последнее событие используется для запуска нижестоящего действия, так как Сетка событий Azure не поддерживает упорядочение событий.

## <a name="prerequisites"></a>Предварительные требования

* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](http://azure.microsoft.com/pricing/free-trial/).
* Активная учетная запись API SQL Azure Cosmos DB. Если вы ее еще не создали, ознакомьтесь с соответствующими инструкциями в разделе [Создание учетной записи базы данных](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-dotnet#create-a-database-account).
* Коллекция в базе данных. Пошаговое руководство см. в разделе [Добавление коллекции](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-dotnet#add-a-collection).
* Центр Интернета вещей в Azure. Если вы его еще не создали, ознакомьтесь с соответствующими инструкциями в статье [Подключение устройства к Центру Интернета вещей с помощью .NET](../iot-hub/iot-hub-csharp-csharp-getstarted.md). 

## <a name="create-a-stored-procedure"></a>Создание хранимой процедуры

Сначала создайте хранимую процедуру и настройте ее для запуска логики, которая сравнивает порядковые номера входящих событий и записывает последнее событие каждого устройства в базе данных.

1. В API SQL для Cosmos DBI выберите **Обозреватель данных** > **Элементы** > **Новая хранимая процедура**.

   ![Создание хранимой процедуры](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Введите идентификатор хранимой процедуры и вставьте следующий код в поле Stored Procedure body (Текст хранимой процедуры). Обратите внимание, что этот код должен заменить любой существующий код в тексте хранимой процедуры. Этот код поддерживает одну строку на идентификатор устройства и записывает последнее состояние подключения этого идентификатора устройства, определяя наивысший порядковый номер. 

    ```javascript
    // SAMPLE STORED PROCEDURE
    function UpdateDevice(deviceId, moduleId, hubName, connectionState, connectionStateUpdatedTime, sequenceNumber) {
      var collection = getContext().getCollection();
      var response = {};
      
      var docLink = getDocumentLink(deviceId, moduleId);

      var isAccepted = collection.readDocument(docLink, function(err, doc) {
        if (err) {
          console.log('Cannot find device ' + docLink + ' - ');
          createDocument();
        } else {
          console.log('Document Found - ');
          replaceDocument(doc);
        }
      });

      function replaceDocument(document) {
        console.log(
          'Old Seq :' +
            document.sequenceNumber +
            ' New Seq: ' +
            sequenceNumber +
            ' - '
        );
        if (sequenceNumber > document.sequenceNumber) {
          document.connectionState = connectionState;
          document.connectionStateUpdatedTime = connectionStateUpdatedTime;
          document.sequenceNumber = sequenceNumber;

          console.log('replace doc - ');

          isAccepted = collection.replaceDocument(docLink, document, function(
            err,
            updated
          ) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(updated);
            }
          });
        } else {
          getContext()
            .getResponse()
            .setBody('Old Event - current: ' + document.sequenceNumber + ' Incoming: ' + sequenceNumber);
        }
      }
      function createDocument() {
        document = {
          id: deviceId + '-' + moduleId,
          deviceId: deviceId,
          moduleId: moduleId,
          hubName: hubName,
          connectionState: connectionState,
          connectionStateUpdatedTime: connectionStateUpdatedTime,
          sequenceNumber: sequenceNumber
        };
        console.log('Add new device - ' + collection.getAltLink());
        isAccepted = collection.createDocument(
          collection.getAltLink(),
          document,
          function(err, doc) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(doc);
            }
          }
        );
      }

      function getDocumentLink(deviceId, moduleId) {
        return collection.getAltLink() + '/docs/' + deviceId + '-' + moduleId;
      }
    }
    ```

3. Сохраните хранимую процедуру: 

    ![Сохранение хранимой процедуры](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Создайте приложение логики

Сначала создайте приложение логики и добавьте триггер службы "Сетка событий", отслеживающий группу ресурсов для виртуальной машины. 

### <a name="create-a-logic-app-resource"></a>Создание ресурса приложения логики

1. На [портале Azure](https://portal.azure.com) выберите **Создать** > **Интеграция** > **Приложение логики**.

   ![Создание приложения логики](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Присвойте уникальное в вашей подписке имя приложению логики и выберите ту же подписку, группу ресурсов и расположение, которые используются для Центра Интернета вещей. 
3. По завершении установите флажок **Закрепить на панели мониторинга** и нажмите кнопку **Создать**.

   Вы создали ресурс Azure для приложения логики. После того, как Azure развернет приложение логики, конструктор Logic Apps отобразит несколько распространенных шаблонов, чтоб вы могли быстрее приступить к работе.

   > [!NOTE] 
   > Если выбрать параметр **Закрепить на панели мониторинга**, приложение логики автоматически откроется в конструкторе Logic Apps. В противном случае вы можете вручную найти и открыть свое приложение логики.

4. В конструкторе приложений логики в разделе **Шаблоны** выберите **Пустое приложение логики**, чтобы создать приложение с нуля.

### <a name="select-a-trigger"></a>Выбор триггера

Триггер представляет собой определенное событие, которое запускает приложение логики. В этом руководстве триггер, который инициирует рабочий процесс, получает запрос через HTTP.  

1. На панели поиска триггеров и соединителей введите **HTTP**.
2. Выберите в качестве триггера вариант **Запрос — при получении HTTP-запроса**. 

   ![Выбор триггера запроса HTTP](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Выберите **Использовать пример полезной нагрузки, чтобы создать схему**. 

   ![Выбор триггера запроса HTTP](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

4. Вставьте пример кода JSON ниже в текстовое поле, а затем нажмите кнопку **Готово**:

   ```json
   [{
    "id": "fbfd8ee1-cf78-74c6-dbcf-e1c58638ccbd",
    "topic":
      "/SUBSCRIPTIONS/DEMO5CDD-8DAB-4CF4-9B2F-C22E8A755472/RESOURCEGROUPS/EGTESTRG/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/MYIOTHUB",
    "subject": "devices/Demo-Device-1",
    "eventType": "Microsoft.Devices.DeviceConnected",
    "eventTime": "2018-07-03T23:20:11.6921933+00:00",
    "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
      "hubName": "MYIOTHUB",
      "deviceId": "48e44e11-1437-4907-83b1-4a8d7e89859e",
      "moduleId": ""
    },
    "dataVersion": "1",
    "metadataVersion": "1"
   }]
   ```

5. Вы можете получить всплывающее уведомление: **Не забудьте включить заголовок Content-Type со значением application/json в запросе.** Можно спокойно проигнорировать это уведомление и перейти к следующему разделу. 

### <a name="create-a-condition"></a>Создание условия

Условия помогают запускать определенные действия после удовлетворения определенного условия в рабочем процессе приложения логики. Когда условие выполняется, можно определить нужное действие. В этом руководстве применяется условие, проверяющее, соответствует ли значение eventType подключенному или отключенному устройству. Действие выполнит хранимую процедуру в вашей базе данных. 

1. Щелкните **Новый шаг**, а затем **Встроенные элементы** и **Условие**. 

2. Заполните условие, как показано ниже, чтобы выполнять действие только для событий, связанных с подключением и отключением устройств:
  * Выберите значение: **eventType**.
  * Измените "is equal to" на **ends with**.
  * Выберите значение: **nected**.

   ![Заполнение условия](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

3. Если условие истинно, щелкните **Добавить действие**.
  
   ![Добавление действия в том случае, если значение равно true](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

4. Найдите Cosmos DB и щелкните **Azure Cosmos DB — Выполнить хранимую процедуру**.

   ![Поиск Cosmos DB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

5. Заполните форму для выполнения хранимой процедуры, выбрав значения из своей базы данных. Введите значение ключа секции и параметры, как показано ниже. 

   ![Заполнение действия приложения логики](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Сохраните приложение логики. 

### <a name="copy-the-http-url"></a>Копирование URL-адреса HTTP

Скопируйте для триггера URL-адрес, который прослушивают приложения логики, а затем закройте конструктор Logic Apps. Этот URL-адрес вы используете для настройки сетки событий. 

1. Щелкните поле конфигурации триггера **При получении HTTP-запроса**, чтобы развернуть его. 
2. Скопируйте значение **URL-адрес HTTP POST**, нажав кнопку копирования рядом с этим параметром. 

   ![Копирование URL-адреса HTTP POST](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Сохраните этот URL-адрес, чтобы использовать его в следующем разделе. 

## <a name="configure-subscription-for-iot-hub-events"></a>Настройка подписки на события Центра Интернета вещей

В этом разделе вы настроите Центр Интернета вещей для публикации событий по мере их появления. 

1. Найдите нужный Центр Интернета вещей на портале Azure. 
2. Выберите **События**.

   ![Открытие сведений сетки событий](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Выберите **Подписка на события**. 

   ![Создание подписки на события](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Создайте подписку на события со следующими значениями: 
   * **Тип события**: снимите флажок "Подписка на все типы событий" и выберите **Device Connected** (Устройство подключено) и **Device Disconnected** (Устройство отключено) в меню.
   * **Сведения о конечной точке**: выберите для типа конечной точки **Веб-перехватчик**, щелкните выбранную конечную точку, вставьте URL-адрес, скопированный из приложения логики, и подтвердите выбор.

   ![Выбор URL-адреса конечной точки](./media/iot-hub-how-to-order-connection-state-events/endpoint-url.png)

   * **Сведения о подписке на события**: укажите описательное имя и выберите **Схема сетки событий**. Когда вы закончите, форма должна выглядеть следующим образом: 

   ![Пример формы подписки на события](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

5. Выберите **Создать**, чтобы сохранить подписку на события.

## <a name="observe-events"></a>Просмотр событий
Теперь, когда ваша подписка на события настроена, давайте протестируем, подключив устройство.

### <a name="register-a-device-in-iot-hub"></a>Регистрация устройства в Центре Интернета вещей

1. В Центре Интернета вещей выберите **IoT Devices** (Устройства Интернета вещей). 
2. Выберите **Добавить**.
3. Для **идентификатора устройства** введите `Demo-Device-1`.
4. Щелкните **Сохранить**. 
5. Вы можете добавить несколько устройств с разными идентификаторами.

   ![Результат](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Скопируйте значение из поля **Строка подключения — первичный ключ** для последующего использования.

   ![Результат](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Запуск симулятора Raspberry Pi

1. Давайте используем веб-симулятор Raspberry Pi для имитации подключения устройства.

[Запустить симулятор Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-applciation-on-the-raspberry-pi-web-simulator"></a>Запуск примера приложения в веб-симуляторе Raspberry Pi
Следующие действия активируют событие, связанное с устройством.

1. В области кода замените значение заполнителя в 15 строке на строку подключения устройства Центра Интернета вещей.

   ![Результат](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Запустите приложение, щелкнув **Выполнить**.

Должны отобразиться следующие результаты, содержащие данные датчика и сообщения, которые отправляются в Центр Интернета вещей.

   ![Результат](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Нажмите кнопку **Остановить**, чтобы остановить симулятор, и запустите событие **Device Disconnected** (Устройство отключено).

Вы запустили пример приложения, чтобы собрать данные датчика и отправить их в Центр Интернета вещей. 

### <a name="observe-events-in-cosmos-db"></a>Просмотр событий в Cosmos DB

Вы можете увидеть результаты выполненной хранимой процедуры в вашем документе Cosmos DB. Вот как это будет выглядеть. Обратите внимание, что каждая строка содержит последнее состояние подключения каждого устройства.

   ![Результат](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Использование Azure CLI

Вместо того чтобы использовать портал Azure, шаги для работы с Центром Интернета вещей можно выполнить с помощью Azure CLI. С дополнительными сведениями можно ознакомиться в статьях, посвященных созданию [подписки на события](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) и [устройств Интернета вещей](https://docs.microsoft.com/cli/azure/iot/device) с использованием Azure CLI.

## <a name="clean-up-resources"></a>Очистка ресурсов

В этом руководстве используются ресурсы, за которые в подписке Azure предусмотрена плата. По завершении работы с этим руководством и тестирования результатов отключите или удалите ресурсы, которые больше не нужны. 

Чтобы не потерять работу, проделанную в приложении логики, мы рекомендуем отключить ресурсы, а не удалять их. 

1. Перейдите в приложение логики.
2. В колонке **Обзор** выберите **Удалить** или **Отключить**. 

В подписке может быть только один бесплатный Центр Интернета вещей. Если для работы с этим руководством вы создали бесплатный Центр Интернета вещей, его можно не удалять, так как никакая плата взиматься не будет.

1. Перейдите в Центр Интернета вещей. 
2. В колонке **Обзор** выберите **Удалить**. 

Даже если вы не удалите свой Центр Интернета вещей, вы можете удалить созданную подписку на события. 

1. В Центре Интернета вещей выберите **Сетка событий**.
2. Выберите подписку на события, которую нужно удалить. 
3. Нажмите кнопку **Удалить**. 

Чтобы удалить учетную запись Azure Cosmos DB на портале Azure, щелкните правой кнопкой мыши имя учетной записи и выберите команду **Удалить учетную запись**. Подробные инструкции см. в разделе [Удаление учетной записи Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/manage-account#delete).

## <a name="next-steps"></a>Дополнительная информация

* Узнайте больше о [реагировании на события в Центре Интернета вещей, используя службу "Сетка событий" для запуска действий](../iot-hub/iot-hub-event-grid.md).
* [Send email notifications about Azure IoT Hub events using Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md) (Отправка уведомлений электронной почты о событиях в Центре Интернета вещей Azure с помощью Logic Apps)
* Узнайте о дополнительных возможностях службы [Сетка событий](../event-grid/overview.md).


