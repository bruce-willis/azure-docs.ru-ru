---
title: Руководство. Использование модуля Azure IoT Edge с кодом C | Документация Майкрософт
description: В этом руководстве показано, как создать модуль IoT Edge c кодом C и развернуть его на пограничном устройстве
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 07/30/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 31560cbd4d8b4572ce930db7ffb8753f3e4a4bc0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425924"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Руководство. Разработка модуля IoT Edge с кодом C и его развертывание на имитированном устройстве

Вы можете использовать модули IoT Edge для развертывания кода, который реализует вашу бизнес-логику непосредственно на устройствах IoT Edge. В этом руководстве рассматриваются создание и развертывание модуля IoT Edge, который фильтрует данные датчика. Из этого руководства вы узнаете, как выполнять следующие задачи:    

> [!div class="checklist"]
> * Использование Visual Studio Code для создания модуля IoT Edge c кодом C
> * Использование Visual Studio Code и Docker для создания образа Docker и его публикации в реестре контейнеров 
> * Развертывание модуля на устройстве IoT Edge.
> * Просмотр сформированных данных


Модуль IoT Edge, создаваемый в этом руководстве, фильтрует данные температуры, созданные вашим устройством. Оно отправляет сообщения, только если температура превышает заданное пороговое значение. Такой тип пограничного анализа удобен для сокращения объема данных, передаваемых в облако и сохраняемых в нем. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Предварительные требования

Устройство Azure IoT Edge.

* В качестве устройства Azure IoT Edge можно использовать компьютер, на котором ведется разработка, или виртуальную машину. Для этого выполните действия, описанные в кратком руководстве для устройств [Linux](quickstart-linux.md) или [Windows](quickstart.md).

Облачные ресурсы.

* [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) цен. категории "Стандартный" в Azure. 

Ресурсы разработки.

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Расширение C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) для Visual Studio Code.
* [Расширение Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).
* [Docker CE](https://docs.docker.com/install/). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-container-registry"></a>Создание реестра контейнеров
В этом руководстве вы используете расширение Azure IoT Edge для Visual Studio Code, чтобы создать модуль, и создадите **образ контейнера** из файлов. Затем вы отправите этот образ в **реестр**, содержащий ваши образы и управляющий ими. Наконец, вы развернете свой образ из реестра для выполнения на устройстве IoT Edge.  

Для работы с этим руководством можно использовать любые реестры, совместимые с Docker. Две популярные службы реестров Docker, доступные в облаке, — [реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) и [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). В этом руководстве используется реестр контейнеров Azure. 

Следующая команда Azure CLI создает реестр в группе ресурсов с именем **IoTEdgeResources**. Замените **{acr_name}** уникальным именем реестра. 

   ```azurecli-interactive
   az acr create --resource-group IoTEdgeResources --name {acr_name} --sku Basic --admin-enabled true
   ```

Получите учетные данные для вашего реестра. 

   ```azurecli-interactive
   az acr credential show --name {acr_name}
   ```

Скопируйте значения **имени пользователя** и один из паролей. Эти значения будут использоваться далее в руководстве при публикации образа Docker в реестре и добавлении учетных данных реестра в среду выполнения Edge. 

## <a name="create-an-iot-edge-module-project"></a>Создание проекта модуля IoT Edge
На следующих этапах показано, как создать проект модуля IoT Edge на основе .NET Сore 2.0 с использованием Visual Studio Code и расширения Azure IoT Edge.
1. В Visual Studio Code выберите **Вид** > **Интегрированный терминал**, чтобы открыть интегрированный терминал VS Code.
2. Выберите **Представление** > **Палитра команд** для открытия палитры команд VS Code. 
3. В палитре команд введите и выполните команду **Azure: Sign in** и следуйте инструкциям для входа в учетную запись Azure. Если вы уже выполняли вход, этот шаг можно пропустить.
4. В палитре команд введите и выполните команду**Azure IoT Edge: New IoT Edge solution**. В палитре команд укажите следующие сведения для создания решения: 
   1. Выберите папку, где требуется создать решение. 
   2. Введите имя своего решения или примите имя по умолчанию **EdgeSolution**.
   3. Выберите **C Module** (Модуль C) как шаблон модуля. 
   4. Присвойте модулю имя **CModule**. 
   5. Укажите Реестр контейнеров Azure, созданный в предыдущем разделе, как репозиторий образов для первого модуля. Замените **localhost:5000** скопированным значением имени входа на сервер. Окончательная строка выглядит так: **\<registry name\>.azurecr.io/cmodule**.
 
4. Окно VS Code загружает рабочую область решения IoT Edge. Имеется папка **modules**, папка с расширением **VSCODE**, файл с расширением **ENV** и файл шаблона с манифестом развертывания. Код модуля по умолчанию реализуется как модуль канала. 

5. Для фильтрации сообщений в формате JSON импортируйте библиотеку JSON для модуля C. Вы можете выбрать любую библиотеку JSON или написать собственную для выполнения анализа JSON в модуле C. В шагах ниже в качестве примера используется библиотека [Parson](https://github.com/kgabis/parson).
   1. Скачайте файлы **parson.c** и **parson.h** из [Репозитория Github Parson](https://github.com/kgabis/parson). Скопируйте и вставьте эти два файла в папку **CModule**.
   2. Откройте **Модули** > **CModule** > **CMakeLists.txt**. Добавьте следующие строки, чтобы импортировать библиотеку Parson с именем "my_parson".

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. В **CMakeLists.txt** к ссылке `target_link_libraries` добавьте `my_parson`.
   4. Откройте **Модули** > **CModule** > **main.c**. Внизу раздела include введите следующий код, чтобы включить `parson.h` для поддержки библиотеки JSON.

      ```c
      #include "parson.h"
      ```

6. Добавьте глобальную переменную `temperatureThreshold` после раздела include. Эта переменная задает значение, которое должно быть превышено измеренной температурой, чтобы данные были отправлены в Центр Интернета вещей. 

    ```c
    static double temperatureThreshold = 25;
    ```

7. Замените функцию `CreateMessageInstance` приведенным ниже кодом. Эта функция выделяет контекст для обратного вызова. 

    ```c
    static MESSAGE_INSTANCE* CreateMessageInstance(IOTHUB_MESSAGE_HANDLE message)
    {
        MESSAGE_INSTANCE* messageInstance = (MESSAGE_INSTANCE*)malloc(sizeof(MESSAGE_INSTANCE));
        if (NULL == messageInstance)
        {
            printf("Failed allocating 'MESSAGE_INSTANCE' for pipelined message\r\n");
        }
        else
        {
            memset(messageInstance, 0, sizeof(*messageInstance));

            if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
            {
                free(messageInstance);
                messageInstance = NULL;
            }
            else
            {
                messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
                MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
                if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
                {
                    printf("ERROR: Map_AddOrUpdate Failed!\r\n");
                }
            }
        }

        return messageInstance;
    }
    ```

8. Замените функцию `InputQueue1Callback` приведенным ниже кодом. Эта функция реализует фактический фильтр обмена сообщениями. 

    ```c
    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) != IOTHUB_MESSAGE_OK)
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n", 
                messagesReceivedByInput1Queue, messageBody);

        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

9. Добавьте функцию `moduleTwinCallback`. Этот метод принимает изменения требуемых свойств из двойника модуля и соответствующим образом изменяет переменную **temperatureThreshold**. У каждого модуля есть собственный двойник модуля, что позволяет настроить код, выполняемый в модуле, непосредственно из облака.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n", 
            ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

10. Добавьте обратный вызов для контекста `moduleTwinCallback` в функции `SetupCallbacksForModule`. Замените функцию `SetupCallbacksForModule` следующим кодом.

    ```c
    static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
    {
        int ret;

        if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
        {
            printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
            ret = __FAILURE__;
        }
        else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
        {
            printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
            ret = __FAILURE__;
        }
        else
        {
            ret = 0;
        }

        return ret;
    }
    ```

11. Сохраните этот файл.

## <a name="build-your-iot-edge-solution"></a>Сборка решения IoT Edge

В предыдущем разделе вы создали решение IoT Edge и добавили код в CModule, который будет отфильтровывать сообщения, где указанная температура компьютера ниже допустимого порога. Теперь необходимо создать решение в качестве образа контейнера и передать его в реестр контейнеров. 

1. Войдите в Docker, введя следующую команду в окне интегрированного терминала Visual Studio Code, чтобы передать образ модуля в ACR: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Укажите имя пользователя, пароль и сервер входа, скопированные из Реестра контейнеров Azure при его создании. Или получите их снова из раздела **Access keys** (Ключи доступа) в разделе реестра на портале Azure.

2. В обозревателе VS Code откройте файл **deployment.template.json** в рабочей области решения IoT Edge. Этот файл указывает `$edgeAgent` развернуть два модуля: **tempSensor** и **CModule**. В качестве значения `CModule.image` устанавливается версия образа amd64 для Linux. Общие сведения о манифестах развертывания см. в статье [Сведения об использовании, настройке и повторном использовании модулей Azure IoT Edge (предварительная версия)](module-composition.md).

3. В файле **deployment.template.json** есть раздел **registryCredentials**, хранящий учетные данные реестра Docker. Фактические пары имени пользователя и пароля хранятся в ENV-файле, который игнорируется git.

4. Добавьте двойник модуля CModule в манифест развертывания. Вставьте следующее содержимое JSON в нижней части раздела `moduleContent` после двойника модуля `$edgeHub`: 
    ```json
        "CModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Сохраните этот файл.
5. В обозревателе VS Code щелкните правой кнопкой мыши файл **deployment.template.json** и выберите **Build IoT Edge solution** (Создать решение IoT Edge). 

Когда вы указываете службе Visual Studio Code создать решение, она сначала принимает данные в шаблоне развертывания и создает файл `deployment.json` в новой папке **config**. Затем выполняются две команды в интегрированном терминале: `docker build` и `docker push`. Они создают ваш код, упаковывают `CModule.dll` в контейнеры и передают в реестр контейнеров, который вы указали при инициализации решения. 

Полный адрес образа контейнера с тегом можно увидеть в окне интегрированного терминала VS Code. Адрес образа создается на основе информации из файла `module.json` в формате **\<repository\>:\<version\>-\<platform\>**. В этом руководстве он должен выглядеть так: **registryname.azurecr.io/cmodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>Развертывание и запуск решения

1. Настройте расширение Azure IoT Toolkit со строкой подключения для вашего Центра Интернета вещей: 
    1. Откройте обозреватель VS Code, выбрав **Вид** > **Обозреватель**. 
    2. В проводнике щелкните **Azure IoT Hub Devices** (Устройства Центра Интернета вещей) и нажмите кнопку **...**. Щелкните **Select IoT Hub** (Выбрать Центр Интернета вещей). Следуйте инструкциям для входа в учетную запись Azure и выберите свой Центр Интернета вещей. 
       Обратите внимание, что вы также можете выполнить настройку, щелкнув параметр **Set IoT Hub Connection String** (Настроить строку подключения Центра Интернета вещей). Введите во всплывающем окне строку подключения Центра Интернета вещей, к которому подключается ваше устройство IoT Edge.

2. В обозревателе устройств Центра Интернета вещей Azure щелкните идентификатор устройства IoT Edge правой кнопкой мыши и выберите **Create Deployment for IoT Edge device** (Создать развертывание для устройства IoT Edge). Выберите файл **deployment.json** в папке **config** и щелкните **Select Edge Deployment Manifest** (Выбрать манифест развертывания Edge).

3. Нажмите кнопку "Обновить". Появится новый модуль **CModule**, работающий вместе с модулями **TempSensor**, **$edgeAgent** и **$edgeHub**. 

## <a name="view-generated-data"></a>Просмотр сформированных данных

1. Для отслеживания данных, поступающих в Центр Интернета вещей, нажмите кнопку **...** и выберите **Start Monitoring D2C Messages** (Начать мониторинг сообщений D2C).
2. Для отслеживания сообщений D2C для конкретного устройства щелкните его правой кнопкой мыши в списке и выберите **Start Monitoring D2C Messages** (Начать мониторинг сообщений D2C).
3. Чтобы перестать отслеживать данные, выполните команду **Azure IoT Hub: Stop monitoring D2C message** в палитре команд. 
4. Для просмотра или обновления двойника модуля щелкните модуль правой кнопкой мыши в списке и выберите **Edit module twin** (Редактирование двойника модуля). Чтобы обновить двойник модуля, сохраните файл JSON двойника, затем щелкните правой кнопкой мыши область редактора и выберите **Update Module Twin** (Обновить двойник модуля).
5. Чтобы просмотреть журналы Docker, можно установить [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) для VS Code и найти свои запущенные модули локально в обозревателе Docker. В контекстном меню выберите пункт **Show Logs** (Показывать журналы) для просмотра в интегрированном терминале.
 
## <a name="clean-up-resources"></a>Очистка ресурсов 

При переходе к следующей рекомендованной статье можно сохранить уже созданные ресурсы и конфигурации и повторно их использовать.

В противном случае можно удалить локальные конфигурации и ресурсы Azure, созданные в рамках этой статьи, чтобы избежать расходов. 

> [!IMPORTANT]
> Удаление групп ресурсов Azure — процесс необратимый. Группа ресурсов и все содержащиеся в ней ресурсы удаляются без возможности восстановления. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы. Если вы создали Центр Интернета вещей в группе ресурсов, содержащей ресурсы, которые нужно сохранить, удалите только ресурс Центра Интернета вещей, не удаляя всю группу ресурсов.
>

Чтобы удалить только Центр Интернета вещей, выполните следующую команду, указав имена центра и группы ресурсов:

```azurecli-interactive
az iot hub delete --name {hub_name} --resource-group IoTEdgeResources
```


Чтобы удалить группу ресурсов по имени, выполните следующие действия.

   ```azurecli-interactive
   az group delete --name IoTEdgeResources 
   ```



## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали модуль IoT Edge, который содержит код для фильтрации необработанных данных, созданных вашим устройством IoT Edge. Дополнительные сведения о создании собственных модулей см. в разделе [Use Visual Studio Code to develop and debug C modules for Azure IoT Edge](how-to-develop-c-module.md) (Использование Visual Studio Code для разработки и отладки модулей C для IoT Edge Azure). Вы можете перейти к следующим руководствам, чтобы узнать о других способах, с помощью которых Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве.

> [!div class="nextstepaction"]
> [Руководство по хранению данных в пограничной системе с помощью баз данных SQL Server](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
