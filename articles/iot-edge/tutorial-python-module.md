---
title: Руководство по использованию Azure IoT Edge с кодом Python | Документы Майкрософт
description: В этом руководстве показано, как создать модуль IoT Edge с кодом Python и его развертывание на пограничном устройстве
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 42af2b5ec6b591929f37afebe6546d61b8a3a02a
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082854"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device"></a>Руководство. Разработка модуля IoT Edge с кодом Python и его развертывание на имитированном устройстве

Вы можете использовать модули IoT Edge для развертывания кода, который реализует вашу бизнес-логику непосредственно на устройствах IoT Edge. В этом руководстве рассматриваются создание и развертывание модуля IoT Edge, который фильтрует данные датчика. Вы используете имитированное устройство IoT Edge, созданное при работе с руководством по развертыванию Azure IoT Edge, на имитированном устройстве в [Windows][lnk-quickstart-win] или [Linux][lnk-quickstart-lin]. Из этого руководства вы узнаете, как выполнять следующие задачи:    

> [!div class="checklist"]
> * создание модуля Python для IoT Edge с помощью Visual Studio Code;
> * Использование Visual Studio Code и Docker для создания образа Docker и его публикация в реестре. 
> * Развертывание модуля на устройстве IoT Edge.
> * Просмотр сформированных данных


Модуль IoT Edge, создаваемый в этом руководстве, фильтрует данные температуры, созданные вашим устройством. Оно отправляет сообщения, только если температура превышает заданное пороговое значение. Такой тип пограничного анализа удобен для сокращения объема данных, передаваемых в облако и сохраняемых в нем. 

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free), прежде чем начинать работу.


## <a name="prerequisites"></a>Предварительные требования

* Устройство Azure IoT Edge, которое вы создали при работе с кратким руководством для [Linux](quickstart-linux.md).

   >[!Note]
   >Модули Python для Azure IoT Edge не поддерживают устройства Windows или ARM. 

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Расширение Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Расширение Visual Studio Code для Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* [Docker](https://docs.docker.com/engine/installation/) на компьютере с Visual Studio Code. Для этого руководства достаточно выпуска Community Edition (CE). 
* [Python](https://www.python.org/downloads/).
* [PIP](https://pip.pypa.io/en/stable/installing/#installation) для установки пакетов Python (обычно входит в состав установки Python).

## <a name="create-a-container-registry"></a>Создание реестра контейнеров
В этом руководстве вы используете расширение Azure IoT Edge для Visual Studio Code, чтобы создать модуль, и создадите **образ контейнера** из файлов. Затем вы отправите этот образ в **реестр**, содержащий ваши образы и управляющий ими. Наконец, вы развернете свой образ из реестра для выполнения на устройстве IoT Edge.  

Для работы с этим руководством можно использовать любые реестры, совместимые с Docker. Две популярные службы реестров Docker, доступные в облаке, — [реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) и [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). В этом руководстве используется реестр контейнеров Azure. 

1. На [портале Azure](https://portal.azure.com) выберите **Создать ресурс** > **Контейнеры** > **Реестр контейнеров Azure**.
2. Укажите имя реестра, выберите подписку, выберите группу ресурсов и задайте номер SKU **Базовый**. 
3. Нажмите кнопку **Создать**.
4. После создания реестра контейнеров перейдите к нему и выберите **Ключи доступа**. 
5. **Включите** параметр **Пользователь-администратор**.
6. Скопируйте значения **Сервер входа**, **Имя пользователя** и **Пароль**. Эти значения вам понадобятся позже при работе с этим руководством. 

## <a name="create-an-iot-edge-module-project"></a>Создание проекта модуля IoT Edge
Описанный ниже процесс позволяет создать модуль Python для IoT Edge с использованием Visual Studio Code и расширения Azure IoT Edge.

### <a name="create-a-new-solution"></a>Создание решения

С помощью пакета Python **cookiecutter** создайте шаблон решений Python, на основе которого можно выполнить сборку. 

1. В Visual Studio Code выберите **Вид** > **Интегрированный терминал**, чтобы открыть интегрированный терминал VS Code.

2. В окне встроенного терминала введите следующую команду для установки (или обновления) пакета**cookiecutter**, который вы используете для создания шаблона решения Edge в VS Code:

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. Выберите **Представление** > **Палитра команд** для открытия палитры команд VS Code. 

4. В палитре команд введите и выполните команду **Azure: Sign in** и следуйте инструкциям для входа в учетную запись Azure. Если вы уже выполняли вход, этот шаг можно пропустить.

5. В палитре команд введите и выполните команду**Azure IoT Edge: New IoT Edge solution**. В палитре команд укажите следующие сведения для создания решения: 

   1. Выберите папку, где требуется создать решение. 
   2. Введите имя своего решения или примите имя по умолчанию **EdgeSolution**.
   3. Выберите **Python Module** (Модуль Python) как шаблон модуля. 
   4. Назовите модуль **PythonModule**. 
   5. Укажите Реестр контейнеров Azure, созданный в предыдущем разделе, как репозиторий образов для первого модуля. Замените **localhost:5000** скопированным значением имени входа на сервер. Окончательная строка выглядит так: **\<registry name\>.azurecr.io/pythonmodule**.
 
Окно VS Code загружает рабочую область решения IoT Edge. Существует папка **modules**, файл шаблона манифеста развертывания и файл с расширением **ENV**. 

### <a name="add-your-registry-credentials"></a>Добавление учетных данных реестра

Файл среды хранит учетные данные для репозитория контейнеров и совместно использует их со средой выполнения IoT Edge. Среде выполнения нужны эти учетные данные, чтобы извлечь частный образ на устройство IoT Edge. 

1. Откройте в обозревателе VS Code файл с расширением **ENV**. 
2. Обновите поля с **именем пользователя** и **паролем**, скопированные из своего реестра контейнера Azure. 
3. Сохраните этот файл. 

### <a name="update-the-module-with-custom-code"></a>Обновление модуля с помощью пользовательского кода

Каждый шаблон содержит пример кода, который принимает имитируемые данные с датчиков модуля **tempSensor** и направляет их в Центр Интернета вещей. В этом разделе добавьте код, который расширяет pythonModule, для анализа сообщений перед их отправкой. 

1. В обозревателе VS Code откройте **modules** > **PythonModule** > **main.py**.

2. В верхней части **main.py** импортируйте библиотеку `json`.

    ```python
    import json
    ```

3. Добавьте `TEMPERATURE_THRESHOLD` и `TWIN_CALLBACKS` в разделе глобальных счетчиков. Порог температуры задает значение измеренной температуры компьютера, при превышении которого данные отправляются в Центр Интернета вещей.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

4. Замените функцию `receive_message_callback` следующим кодом:

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we will forward this message onto the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

5. Добавьте новую функцию `module_twin_callback`. Эта функция будет вызываться при обновлении нужного свойства.

    ```python
    # module_twin_callback is invoked when twin's desired properties are updated.
    def module_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print ( "\nTwin callback called with:\nupdateStatus = %s\npayload = %s\ncontext = %s" % (update_state, payload, user_context) )
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
    ```

6. В классе `HubManager` добавьте в метод `__init__` новую строку, которая инициализирует только что добавленную функцию `module_twin_callback`.

    ```python
    # sets the callback when a twin's desired properties are updated.
    self.client.set_module_twin_callback(module_twin_callback, self)
    ```

7. Сохраните этот файл.

## <a name="build-your-iot-edge-solution"></a>Сборка решения IoT Edge

В предыдущем разделе вы создали решение IoT Edge и добавили код в PythonModule, который будет отфильтровывать сообщения, где указанная температура компьютера ниже допустимого порогового значения. Теперь необходимо создать решение в качестве образа контейнера и передать его в реестр контейнеров. 

1. Войдите в Docker, введя следующую команду в окне интегрированного терминала Visual Studio Code, чтобы передать образ модуля в ACR: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Укажите имя пользователя, пароль и сервер входа, скопированные из Реестра контейнеров Azure при его создании. Или получите их снова из раздела **Access keys** (Ключи доступа) в разделе реестра на портале Azure.

2. В обозревателе VS Code откройте файл **deployment.template.json** в рабочей области решения IoT Edge. 

   Этот файл указывает `$edgeAgent` развернуть два модуля: **tempSensor**, который моделирует данные устройства, и **PythonModule**. В качестве значения `PythonModule.image` устанавливается версия образа amd64 для Linux. Общие сведения о манифестах развертывания см. в статье [Сведения об использовании, настройке и повторном использовании модулей Azure IoT Edge (предварительная версия)](module-composition.md).

   Этот файл также содержит учетные данные реестра. В файле шаблона в качестве имени пользователя и пароля указываются заполнители. При создании манифеста развертывания поля обновляются с помощью значений, добавленных в файл с расширением**ENV**. 

3. Добавьте двойник модуля PythonModule в манифест развертывания. Вставьте следующее содержимое JSON в нижней части раздела `moduleContent` после двойника модуля `$edgeHub`: 
    ```json
        "PythonModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Сохраните этот файл.

5. В обозревателе VS Code щелкните правой кнопкой мыши файл **deployment.template.json** и выберите **Build IoT Edge solution** (Создать решение IoT Edge). 

Когда вы указываете службе Visual Studio Code создать решение, она сначала принимает данные в шаблоне развертывания и создает файл `deployment.json` в новой папке **config**. Затем выполняются две команды в интегрированном терминале: `docker build` и `docker push`. Они создают ваш код Python, упаковывают его в контейнеры и передают в реестр контейнера, указанный при инициализации решения. 

Полный адрес образа контейнеров с тегом можно получить из команды `docker build`, выполняемой в интегрированном терминале VS Code. Адрес образа создается на основе информации из файла `module.json` в формате **\<repository\>:\<version\>-\<platform\>**. В этом руководстве он должен выглядеть так: **registryname.azurecr.io/pythonmodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>Развертывание и запуск решения

Вы можете использовать портал Azure для развертывания модуля Python на устройстве IoT Edge, как вы делали в кратком руководстве, но можно также развернуть и отслеживать модули из Visual Studio Code. В следующих разделах используется расширение Azure IoT Edge для VS Code, указанное в предварительных требованиях. Установите его сейчас, если вы этого еще не сделали. 

1. Выберите **Представление** > **Палитра команд** для открытия палитры команд VS Code.

2. Найдите и выполните команду **Azure: Sign in**. Следуйте инструкциям, чтобы войти в свою учетную запись Azure. 

3. В палитре команд найдите и выполните команду **Azure IoT Hub: Select IoT Hub**. 

4. Выберите подписку, содержащую ваш Центр Интернета вещей, а затем выберите Центр Интернета вещей, к которому необходимо получить доступ.

5. В обозревателе VS Code разверните раздел **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure). 

6. Щелкните имя устройства IoT Edge правой кнопкой мыши, а затем выберите **Create Deployment for IoT Edge device** (Создание развертывания для устройства IoT Edge). 

7. Перейдите в папку решения, которая содержит PythonModule. Откройте папку **config** и выберите файл **deployment.json**. Щелкните **Select Edge Deployment Manifest** (Выбрать манифест развертывания Edge).

8. Обновите раздел **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure). Вы должны увидеть новый модуль **PythonModule**, работающий вместе с модулем **TempSensor**, а также **$edgeAgent** и **$edgeHub**. 

## <a name="view-generated-data"></a>Просмотр сформированных данных

1. Для отслеживания данных, поступающих в Центр Интернета вещей, нажмите кнопку **...** и выберите **Start Monitoring D2C Messages** (Начать мониторинг сообщений D2C).
2. Для отслеживания сообщений D2C для конкретного устройства щелкните его правой кнопкой мыши в списке и выберите **Start Monitoring D2C Messages** (Начать мониторинг сообщений D2C).
3. Чтобы перестать отслеживать данные, выполните команду **Azure IoT Hub: Stop monitoring D2C message** в палитре команд. 
4. Для просмотра или обновления двойника модуля щелкните модуль правой кнопкой мыши в списке и выберите **Edit module twin** (Редактирование двойника модуля). Чтобы обновить двойник модуля, сохраните файл JSON двойника, затем щелкните правой кнопкой мыши область редактора и выберите **Update Module Twin** (Обновить двойник модуля).
5. Чтобы просмотреть журналы Docker, можно установить [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) для VS Code и найти свои запущенные модули локально в обозревателе Docker. В контекстном меню выберите пункт **Show Logs** (Показывать журналы) для просмотра в интегрированном терминале. 

## <a name="clean-up-resources"></a>Очистка ресурсов 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

При переходе к следующей рекомендованной статье можно сохранить уже созданные ресурсы и конфигурации и повторно их использовать.

В противном случае можно удалить локальные конфигурации и ресурсы Azure, созданные в рамках этой статьи, чтобы избежать расходов. 

> [!IMPORTANT]
> Удаление ресурсов Azure и группы ресурсов является необратимым. Группа ресурсов и все содержащиеся в ней ресурсы удаляются без возможности восстановления. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы. Если вы создали Центр Интернета вещей в группе ресурсов, содержащей ресурсы, которые нужно сохранить, удалите только ресурс Центра Интернета вещей, не удаляя всю группу ресурсов.
>

Чтобы удалить только Центр Интернета вещей, выполните следующую команду, указав имена центра и группы ресурсов:

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


Чтобы удалить группу ресурсов по имени, выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com) и щелкните **Группы ресурсов**.

2. Введите в текстовое поле **Фильтровать по имени...** имя группы ресурсов, содержащей Центр Интернета вещей. 

3. Справа от своей группы ресурсов в списке результатов щелкните **...**, а затем выберите **Удалить группу ресурсов**.

4. Подтвердите операцию удаления группы ресурсов. Еще раз введите имя группы ресурсов для подтверждения и нажмите кнопку **Удалить**. Через некоторое время группа ресурсов и все ее ресурсы будут удалены.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы создали модуль IoT Edge, который содержит код для фильтрации необработанных данных, созданных вашим устройством IoT Edge. Вы можете перейти к одному из следующих руководств, чтобы узнать о других способах, с помощью которых Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве

> [!div class="nextstepaction"]
> [Deploy Azure Function as an IoT Edge module - preview](tutorial-deploy-function.md)
>  (Развертывание функции Azure в виде модуля IoT Edge (предварительная версия))[Deploy Azure Stream Analytics as an IoT Edge module - preview](tutorial-deploy-stream-analytics.md) (Развертывание Azure Stream Analytics в виде модуля IoT Edge (предварительная версия))


<!-- Links -->
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
