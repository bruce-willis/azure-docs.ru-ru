---
title: Сохранение данных с помощью модуля SQL Azure IoT Edge | Документация Майкрософт
description: Узнайте, как хранить данные локально на устройстве IoT Edge, используя модуль SQL Server
services: iot-edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d0837787dcac44d2cc43701ac181ec7eac2dfa2c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38687221"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Руководство по хранению данных в пограничной системе с помощью баз данных SQL Server

Используйте Azure IoT Edge и SQL Server для хранения и запроса данных в пограничной системе. Azure IoT Edge поставляется со встроенными базовыми функциями хранения, которые кэшируют сообщения, если устройство переходит в автономный режим, а затем переадресовывает их при повторном подключении. Однако вам могут потребоваться дополнительные возможности хранения данных, например возможность запрашивать данные локально. При использовании локальных баз данных устройства IoT Edge могут выполнять более сложные вычисления без необходимости поддерживать соединение с Центром Интернета вещей. Например, специалист может визуализировать последние данные датчиков на компьютере локально, даже если эти данные передаются в облако только один раз в месяц, чтобы помочь улучшить модель машинного обучения.

В этой статье содержатся сведения для развертывания базы данных SQL Server на устройство IoT Edge. Функции Azure, запущенные на устройстве IoT Edge, структурируют входящие данные, а затем отправляют их в базу данных. Шаги, описанные в этой статье, также можно применить к другим базам данных, которые работают в контейнерах, например MySQL или PostgreSQL.

Из этого руководства вы узнаете, как выполнять такие задачи: 

> [!div class="checklist"]
> * Использование Visual Studio Code для создания функции Azure
> * Развертывание базы данных SQL на устройстве IoT Edge
> * Использование кода Visual Studio для создания модулей и их развертывание на устройстве IoT Edge
> * Просмотр сформированных данных

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>предварительным требованиям

* Устройство Azure IoT Edge, которое вы создали при работе с кратким руководством для устройств [Linux](quickstart-linux.md) или [Windows](quickstart.md).
* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# для расширения Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Расширение Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Пакет SDK для .NET Core 2.1](https://www.microsoft.com/net/download). 
* [Docker CE](https://docs.docker.com/install/) на компьютере, на котором ведется разработка. 

## <a name="create-a-container-registry"></a>Создание реестра контейнеров
В этом руководстве вы используете расширение Azure IoT Edge для Visual Studio Code, чтобы создать модуль, и создадите **образ контейнера** из файлов. Затем вы отправите этот образ в **реестр**, содержащий ваши образы и управляющий ими. Наконец, вы развернете свой образ из реестра для выполнения на устройстве IoT Edge.  

Для работы с этим руководством можно использовать любые реестры, совместимые с Docker. Две популярные службы реестров Docker, доступные в облаке, — [реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) и [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). В этом руководстве используется реестр контейнеров Azure. 

1. На [портале Azure](https://portal.azure.com) выберите **Создать ресурс** > **Контейнеры** > **Реестр контейнеров**.

    ![создание реестра контейнеров](./media/tutorial-deploy-function/create-container-registry.png)

2. Укажите имя реестра и выберите подписку.
3. Для группы ресурсов рекомендуется использовать имя группы ресурсов, которое содержит ваш Центр Интернета вещей. Поместив все ресурсы для приложения в одну группу, вы можете управлять ими совместно. Например, при удалении группы ресурсов для тестирования все содержащиеся в ней ресурсы также удаляются. 
4. Установите для номера SKU значение **Базовый**, а для параметра **Администратор** установите значение **Включить**. 
5. Нажмите кнопку **Создать**.
6. После создания реестра контейнеров перейдите к нему и выберите **Ключи доступа**. 
7. Скопируйте значения **Сервер входа**, **Имя пользователя** и **Пароль**. Эти значения вам понадобятся позже при работе с этим руководством. 

## <a name="create-a-function-project"></a>Создание проекта функции

Чтобы отправить данные в базу данных, необходим модуль, который может правильно структурировать данные, а затем сохранить их в таблице. 

Следующий процесс позволяет создать модуль IoT Edge с использованием Visual Studio Code и расширения Azure IoT Edge.

1. Откройте Visual Studio Code.
2. Откройте интегрированный терминал VS Code, выберите **Вид** > **Интегрированный терминал**.
3. Выберите **Представление** > **Палитра команд** для открытия палитры команд VS Code.
4. В палитре команд введите и выполните команду **Azure: Sign in** и следуйте инструкциям для входа в учетную запись Azure. Если вы уже выполняли вход, этот шаг можно пропустить.
3. В палитре команд введите и выполните команду**Azure IoT Edge: New IoT Edge solution**. В палитре команд укажите следующие сведения для создания решения: 
   1. Выберите папку, где требуется создать решение. 
   2. Введите имя своего решения или примите имя по умолчанию **EdgeSolution**.
   3. Выберите **Azure Functions — C#** (Функции Azure — C#) как шаблон модуля. 
   4. Назовите модуль **sqlFunction**. 
   5. Укажите Реестр контейнеров Azure, созданный в предыдущем разделе, как репозиторий образов для первого модуля. Замените **localhost:5000** скопированным значением имени входа на сервер. Окончательная строка выглядит так: **\<registry name\>.azurecr.io/sqlFunction**.

4. Окно VS Code загружает рабочую область решения IoT Edge. Имеется папка **modules**, папка с расширением **VSCODE** и файл шаблона с манифестом развертывания. Откройте **modules** > **sqlFunction** > **EdgeHubTrigger-Csharp** > **run.csx**.

5. Замените содержимое файла на код, приведенный ниже.

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"
   #r "System.Data.SqlClient"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;
   using System.Threading.Tasks;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
       const int temperatureThreshold = 25;
       byte[] messageBytes = messageReceived.GetBytes();
       var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

       if (!string.IsNullOrEmpty(messageString))
       {
           // Get the body of the message and deserialize it
           var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

           //Store the data in SQL db
           const string str = "<sql connection string>";
           using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
           {
           conn.Open();
           var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
           var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
               using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
               {
               //Execute the command and log the # rows affected.
               var rows = await cmd.ExecuteNonQueryAsync();
               log.Info($"{rows} rows were updated");
               }
           }

           if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
           {
               // Send the message to the output as the temperature value is greater than the threshold
               var filteredMessage = new Message(messageBytes);
               // Copy the properties of the original message into the new Message object
               foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
               {
                   filteredMessage.Properties.Add(prop.Key, prop.Value);
               }
               // Add a new property to the message to indicate it is an alert
               filteredMessage.Properties.Add("MessageType", "Alert");
               // Send the message        
               await output.AddAsync(filteredMessage);
               log.Info("Received and transferred a message with temperature above the threshold");
           }
       }
   }

   //Define the expected schema for the body of incoming messages
   class MessageBody
   {
       public Machine machine {get;set;}
       public Ambient ambient {get; set;}
       public string timeCreated {get; set;}
   }
   class Machine
   {
      public double temperature {get; set;}
      public double pressure {get; set;}         
   }
   class Ambient
   {
      public double temperature {get; set;}
      public int humidity {get; set;}         
   }
   ```

6. В строке 24 замените строку **\>sql connection string\<** следующей. Свойство **Data Source** ссылается на имя контейнера SQL Server, который будет создан с именем **SQL** в следующем разделе. 

   ```C#
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. Сохраните файл **run.csx**. 

## <a name="add-a-sql-server-container"></a>Добавление контейнера SQL Server

[Манифест развертывания](module-composition.md) объявляет модули, которые среда выполнения IoT Edge установит на вашем устройстве IoT Edge. Вы добавили код для создания настраиваемого модуля службы "Функции" в предыдущем разделе, однако модуль SQL Server уже создан. Необходимо сообщить среде выполнения IoT Edge о том, что его нужно включить и настроить на устройстве. 

1. В обозревателе кода Visual Studio откройте файл **deployment.template.json**. 
2. Найдите раздел **moduleContent. $edgeAgent.properties.desired.modules**. Должно быть перечислено два модуля: **tempSensor**, создающий имитируемые данные, и модуль **sqlFunction**.
3. Добавьте следующий код для объявления третьего модуля:

   ```json
   "sql": {
       "version": "1.0",
       "type": "docker",
       "status": "running",
       "restartPolicy": "always",
       "settings": {
           "image": "",
           "createOptions": ""
       }
   }
   ```

4. В зависимости от операционной системы устройства IoT Edge обновите параметры **sql.settings** с помощью следующего кода:

   * Windows:

      ```json
      "image": "microsoft/mssql-server-windows-developer",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"C:\\\\mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   * Linux:

      ```json
      "image": "microsoft/mssql-server-linux:2017-latest",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"/var/opt/mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   >[!Tip]
   >Каждый раз при создании контейнера SQL Server в рабочей среде нужно [изменять стандартный пароль системного администратора](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password).

5. Сохраните файл **deployment.template.json**. 

## <a name="build-your-iot-edge-solution"></a>Сборка решения IoT Edge

В предыдущих разделах было создано решение с одним модулем, а затем добавлено другое в шаблон манифеста развертывания. Теперь необходимо собрать решение, создать образы контейнеров для модулей и отправить образы в реестр контейнеров. 

1. В файле deployment.template.json предоставьте среде выполнения IoT Edge учетные данные реестра, чтобы она могла получить доступ к образам модуля. Найдите раздел **moduleContent.$edgeAgent.properties.desired.runtime.settings**. 
2. Вставьте следующий код JSON после **loggingOptions**:

   ```JSON
   "registryCredentials": {
       "myRegistry": {
           "username": "",
           "password": "",
           "address": ""
       }
   }
   ```

3. Вставьте учетные данные реестра в поля **Имя пользователя**, **Пароль** и **Адрес**. Используйте значения, скопированные при создании службы "Реестр контейнеров Azure" в начале этого руководства.
4. Сохраните файл **deployment.template.json**.
5. Войдите в реестр в Visual Studio Code, чтобы вы могли отправить свои образы в реестр. Используйте учетные данные, которые были только что добавлены в манифест развертывания. Введите следующую команду в окне интегрированного терминала: 

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    Появится запрос на ввод пароля. Вставьте пароль в строку и нажмите клавишу **ВВОД**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

6. В обозревателе VS Code щелкните правой кнопкой мыши файл **deployment.template.json** и выберите **Build IoT Edge solution** (Создать решение IoT Edge). 

## <a name="deploy-the-solution-to-a-device"></a>Развертывание решения на устройстве

Модули на устройстве можно задать с помощью Центра Интернета вещей, однако доступ к Центру Интернета вещей и устройствам можно также получить через Visual Studio Code. В этом разделе настраивается доступ к Центру Интернета вещей. Используйте VS Code для развертывания решения на устройстве IoT Edge. 

1. В палитре команд VS Code выберите **Azure IoT Hub: Select IoT Hub**.
2. Следуйте указаниям по входу в учетную запись Azure. 
3. В палитре команд выберите подписку Azure, а затем выберите свой Центр Интернета вещей. 
4. В обозревателе VS Code разверните раздел **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure). 
5. Щелкните правой кнопкой мыши устройство, которое необходимо сделать целью развертывания, и выберите **Create Deployment for IoT Edge** (Создание развертывания для устройств IoT Edge). 
6. В проводнике перейдите к папке **config** в решении и выберите **deployment.json**. Щелкните **Select Edge Deployment Manifest** (Выбрать манифест развертывания Edge). 

Если развертывание завершено успешно, сообщение о подтверждении будет выведено в выходных данных VS Code. Кроме того, можно проверить, все ли модули отображаются и запущены на своем устройстве. 

На своем устройстве IoT Edge выполните следующую команду, чтобы увидеть состояние модулей. Это может занять несколько минут.

   ```bash
   sudo iotedge list
   ```

## <a name="create-the-sql-database"></a>Создание базы данных SQL

При применении манифеста развертывания на устройстве вы получаете три выполняемых модуля. Модуль tempSensor создает данные имитируемой среды. Модуль sqlFunction принимает данные и форматирует их для базы данных. 

В этом разделе показано, как настроить базу данных SQL для хранения данных температуры, полученных с датчиков. 

1. В программе командной строки подключитесь к базе данных. 
   * Контейнер Windows:
   
      ```cmd
      docker exec -it sql cmd
      ```
    
   * Контейнер Linux: 

      ```bash
      docker exec -it sql bash
      ```

2. Откройте программу командной строки для SQL.
   * Контейнер Windows:

      ```cmd
      sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

   * Контейнер Linux: 

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Password'
      ```

3. Создайте базу данных: 

   * Контейнер Windows
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = 'C:\mssql\measurementsdb.mdf')
      GO
      ```

   * Контейнер Linux
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. Определите таблицу.

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Можно настроить файл Docker SQL Server таким образом, чтобы SQL Server автоматически настраивался для развертывания на нескольких устройствах IoT Edge. Дополнительные сведения см. в статье о [демонстрационном проекте контейнера Microsoft SQL Server](https://github.com/twright-msft/mssql-node-docker-demo-app). 

## <a name="view-the-local-data"></a>Просмотр локальных данных

После создания таблицы модуль sqlFunction начинает сохранять данные в локальную базу данных SQL Server 2017 на устройстве IoT Edge. 

Изнутри программы командной строки SQL выполните следующую команду, чтобы просмотреть данные отформатированной таблицы: 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![Просмотр локальных данных](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)]

Удалите среду выполнения службы IoT Edge на основе своей платформы устройств Интернета вещей (Linux или Windows).

#### <a name="windows"></a>Windows

Удалите среду выполнения IoT Edge.

```Powershell
stop-service iotedge -NoWait
sleep 5
sc.exe delete iotedge
```

Удалите контейнеры, созданные на своем устройстве. 

```Powershell
docker rm -f $(docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor")
```

#### <a name="linux"></a>Linux

Удалите среду выполнения IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Удалите контейнеры, созданные на своем устройстве. 

```bash
sudo docker rm -f $(sudo docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor")
```

Удалите среду выполнения контейнера.

```bash
sudo apt-get remove --purge moby
```



## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали модуль службы "Функции Azure", который содержит код для фильтрации необработанных данных, созданных вашим устройством IoT Edge. Когда вы будете готовы создавать собственные модули, вы можете узнать больше о том, как [разрабатывать решения службы "Функции Azure" с помощью Azure IoT Edge для Visual Studio Code](how-to-develop-csharp-function.md). 

Перейдите к следующим руководствам, чтобы узнать о других способах, с помощью которых Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве.

> [!div class="nextstepaction"]
> [Развертывание Azure Stream Analytics в качестве модуля IoT Edge (предварительная версия)](tutorial-deploy-stream-analytics.md)
