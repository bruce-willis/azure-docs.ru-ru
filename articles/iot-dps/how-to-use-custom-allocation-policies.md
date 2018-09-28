---
title: Как использовать пользовательские политики выделения со Службой подготовки устройств к добавлению в Центр Интернета вещей Azure | Документация Майкрософт
description: Как использовать пользовательские политики выделения со Службой подготовки устройств к добавлению в Центр Интернета вещей Azure.
author: wesmc7777
ms.author: wesmc
ms.date: 08/15/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 503a8026fe11d1cdb3d0fc0c2680d8d545a1c992
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955256"
---
# <a name="how-to-use-custom-allocation-policies"></a>Как использовать пользовательские политики выделения


Пользовательская политика выделения обеспечивает больший контроль над назначением устройств в Центре Интернета вещей. Это достигается с помощью пользовательского кода в [функции Azure](../azure-functions/functions-overview.md), используемого для назначения устройств Центру Интернета вещей. Служба подготовки устройств вызывает ваш код функции Azure, предоставляя группу Центра Интернета вещей. Код функции возвращает данные Центра Интернета вещей для подготовки устройства.

С помощью функции пользовательских политик выделения можно определить собственные политики выделения, если политики, предоставленные службой подготовки устройств, не соответствуют требованиям вашего сценария.

Например, вам может понадобиться проверить сертификат, который устройство использует во время подготовки, и назначить устройство Центру Интернета вещей на основе свойства сертификата. Возможно, сведения об устройствах хранятся в базе данных, и требуется отправит запрос к этой базе данных, чтобы определить, какому Центру Интернета вещей следует назначить устройство.


В этой статье показана работа с пользовательскими политиками с помощью кода функции Azure на языке C#. Создаются два новых Центра Интернета вещей, представляющие *отдел тостеров Contoso* и *отдел тепловых насосов Contoso*. Устройства, запрашивающие подготовку, должны иметь идентификатор регистрации с одним из следующих суффиксов, чтобы они были приняты для подготовки:

- **-contoso-tstrsd-007**: отдел тостеров Contoso;
- **-contoso-hpsd-088**: отдел тепловых насосов Contoso.

Устройства будут подготовлены в соответствии с этими обязательными суффиксами в идентификаторе регистрации. Эти устройства будут имитироваться с помощью примера для подготовки из [пакета SDK Azure IoT для C](https://github.com/Azure/azure-iot-sdk-c). 

В этой статье мы выполним следующие процедуры.

* Использование Azure CLI для создания центров Интернета вещей для двух отделов Contoso (**отдела тостеров Contoso** и **отдела тепловых насосов Contoso**).
* Создание группы регистрации с помощью функции Azure для пользовательской политики выделения.
* Создание ключей устройства для имитации двух устройств.
* Подготовка среды разработки для пакета SDK Azure IoT для C.
* Имитация устройств для проверки их подготовки в соответствии с кодом примера пользовательской политики выделения.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

* Выполните процедуру, описанную в кратком руководстве по [настройке службы подготовки устройств Центра Интернета вещей на портале Azure](./quick-setup-auto-provision.md).
* Visual Studio 2015 или [Visual Studio 2017](https://www.visualstudio.com/vs/) со включенной рабочей нагрузкой [Разработка классических приложений на C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).
* Установите последнюю версию [Git](https://git-scm.com/download/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-two-divisional-iot-hubs"></a>Создание Центров Интернета вещей для двух отделов

В этом разделе с помощью Azure Cloud Shell вы создадите два Центра Интернета вещей, представляющие **отдел тостеров Contoso** и **отдел тепловых насосов Contoso**.

1. В Azure Cloud Shell создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az-group-create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. 

    В следующем примере создается группа ресурсов с именем *contoso-us-resource-group* в регионе *eastus*. Рекомендуется использовать эту группу для всех ресурсов, созданных в рамках этой статьи. Это поможет упростить очистку после завершения работы.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. В Azure Cloud Shell создайте Центр Интернета вещей **отдела тостеров Contoso** с помощью команды [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). Этот Центр Интернета вещей будет добавлен в группу *contoso-us-resource-group*.

    В следующем примере создается Центр Интернета вещей *contoso-toasters-hub-1098* в расположении *eastus*. Необходимо указать собственное уникальное имя центра. Придумайте собственный суффикс в имени центра вместо **1098**. В примере кода для пользовательской политики выделения требуется наличие слова `-toasters-` в имени центра.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Выполнение этой команды может занять несколько минут.

3. В Azure Cloud Shell создайте Центр Интернета вещей **отдела тепловых насосов Contoso** с помощью команды [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). Этот Центр Интернета вещей также будет добавлен в группу *contoso-us-resource-group*.

    В следующем примере создается Центр Интернета вещей *contoso-heatpumps-hub-1098* в расположении *eastus*. Необходимо указать собственное уникальное имя центра. Придумайте собственный суффикс в имени центра вместо **1098**. В примере кода для пользовательской политики выделения требуется наличие слова `-heatpumps-` в имени центра.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Выполнение этой команды также может занять несколько минут.




## <a name="create-the-enrollment"></a>Создание регистрации

В этом разделе вы создадите группу регистрации, использующую пользовательскую политику выделения. Для удобства в этой статье используется [аттестация симметричного ключа](concepts-symmetric-key-attestation.md) с регистрацией. В качестве более безопасного решения рекомендуется использовать [аттестацию сертификатов X.509](concepts-security.md#x509-certificates) с цепочкой доверия.

1. Войдите на [портал Azure](http://portal.azure.com) и перейдите к своему экземпляру службы подготовки устройств.

2. Выберите вкладку **Управление регистрациями**, а затем нажмите кнопку **Добавить группу регистрации** в верхней части страницы. 

3. В разделе **Добавление группы регистрации** введите приведенные ниже сведения, а затем нажмите кнопку **Сохранить**.

    **Имя группы**: введите **contoso-custom-allocated-devices**.

    **Тип аттестации**: выберите **Симметричный ключ**.

    **Автоматически создавать ключи**: этот флажок должен быть уже установлен.

    **Выберите способ назначения устройств для Центров**: выберите **Custom (Use Azure Function)** (Пользовательский (функция Azure)).

    ![Добавление группы регистрации пользовательского выделения для аттестации симметричного ключа](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)


4. На странице **Добавление группы регистрации** щелкните **Link a new IoT hub** (Привязать новый Центр Интернета вещей), чтобы привязать Центры Интернета вещей обоих отделов.

    **Подписка**: если у вас несколько подписок, выберите подписку, в которой вы создали Центры Интернета вещей для отделов.

    **Центр Интернета вещей**: выберите один из созданных центров отделов.

    **Политика доступа**: выберите **iothubowner**.

    ![Связывание Центров Интернета вещей отделов со службой подготовки](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)


5. После связывания Центров Интернета вещей обоих отделов необходимо на странице **Добавление группы регистрации** выбрать их в качестве группы Центров Интернета вещей для группы регистрации, как показано ниже.

    ![Создание группы центров отделов для регистрации](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)


6. Прокрутите страницу **Добавление группы регистрации** вниз до раздела **Select Azure Function** (Выбор функции Azure) и щелкните **Create a new function app** (Создание приложения-функции).

7. На открывшейся странице создания **приложения-функции** введите приведенные ниже параметры новой функции и нажмите кнопку **Создать**.

    **Имя приложения**: введите уникальное имя приложения-функции. В качестве примера показано имя **contoso-function-app-1098**.

    **Группа ресурсов**: выберите **Использовать существующий** и **contoso-us-resource-group**, чтобы все ресурсы, созданные в рамках этой статьи, хранились вместе.

    **Application Insights**: для данного упражнения вы можете отключить эту функцию.

    ![Создание приложения-функции](./media/how-to-use-custom-allocation-policies/function-app-create.png)


8. Вернувшись на страницу **Добавление группы регистрации**, убедитесь, что выбрано новое приложение-функция. Может потребоваться повторно выбрать подписку, чтобы обновить список приложений-функций.

    Выбрав новое приложение-функцию, щелкните **Create a new function** (Создать функцию).

    ![Создание приложения-функции](./media/how-to-use-custom-allocation-policies/click-create-new-function.png)

    Откроется новое приложение-функция.

9. Щелкните в нем, чтобы создать функцию.

    ![Создание приложения-функции](./media/how-to-use-custom-allocation-policies/new-function.png)

    Используйте параметры по умолчанию для новой функции, чтобы создать **веб-перехватчик с API** с помощью языка **CSharp**. Щелкните **Create this function** (Создать эту функцию).

    Будет создана функция C# с именем **HttpTriggerCSharp1**.

10. Замените код этой функции C# приведенным ниже кодом и щелкните **Сохранить**.    

    ```C#
    #r "Newtonsoft.Json"
    using System.Net;
    using System.Text;
    using Newtonsoft.Json;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // Just some diagnostic logging
        log.Info("C# HTTP trigger function processed a request.");
        log.Info("Request.Content:...");    
        log.Info(req.Content.ReadAsStringAsync().Result);

        // Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.Info("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.Info("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.Info("Unknown device registration");
                }
            }
        }

        return (fail)
            ? req.CreateResponse(HttpStatusCode.BadRequest, message)
            : new HttpResponseMessage(HttpStatusCode.OK)
            {
                Content = new StringContent(JsonConvert.SerializeObject(obj, Formatting.Indented), Encoding.UTF8, "application/json")
            };
    }    

    public class DeviceTwinObj
    {
        public string deviceId {get; set;}
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public string IoTHub {get; set;}
        public DeviceTwinObj initialTwin {get; set;}
        public string[] linkedHubs {get; set;}
        public string enrollment {get; set;}
    }
    ```


11. Вернувшись на страницу **Добавление группы регистрации**, убедитесь, что эта новая функция выбрана. Может потребоваться повторно выбрать приложение-функцию, чтобы обновить список функций.

    После выбора новой функции щелкните **Сохранить**, чтобы сохранить группу регистрации.

    ![Наконец, сохраните группу регистрации.](./media/how-to-use-custom-allocation-policies/save-enrollment.png)


12. После сохранения регистрации снова откройте ее и запомните или запишите **первичный ключ**. Для формирования ключей необходимо сначала сохранить регистрацию. Этот ключ будет позднее использоваться для создания уникальных ключей устройства для имитированных устройств.


## <a name="derive-unique-device-keys"></a>Получение производных уникальных ключей

В этом разделе вы создадите два уникальных ключа устройства. Один ключ будет использоваться для имитированного устройства toaster. Другой ключ будет использоваться для имитированного устройства heat pump.

Чтобы создать ключ устройства, используйте **первичный ключ**, записанный ранее, для вычисления [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) на основе идентификатора регистрации каждого устройства и преобразования результата в формат Base64.

Используйте следующие два идентификатора регистрации устройств и вычислите ключи для обоих устройств. Оба идентификатора регистрации содержат допустимый суффикс для работы с примером кода для пользовательской политики выделения:

- **breakroom499-contoso-tstrsd-007**
- **mainbuilding167-contoso-hpsd-088**

#### <a name="linux-workstations"></a>Рабочие станции Linux

Если вы используете рабочую станцию Linux, можно использовать openssl для формирования производных ключей устройств, как показано в следующем примере.

Замените значение **KEY** значением **первичного ключа**, записанным ранее.

```bash
KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

REG_ID1=breakroom499-contoso-tstrsd-007
REG_ID2=mainbuilding167-contoso-hpsd-088

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
```

```bash
breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```


#### <a name="windows-based-workstations"></a>Рабочие станции на основе Windows

Если вы используете рабочую станцию Windows, можно использовать PowerShell для формирования производных ключей устройств, как показано в следующем примере.

Замените значение **KEY** значением **первичного ключа**, записанным ранее.

```PowerShell
$KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

$REG_ID1='breakroom499-contoso-tstrsd-007'
$REG_ID2='mainbuilding167-contoso-hpsd-088'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($key)
$sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
$sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
$derivedkey1 = [Convert]::ToBase64String($sig1)
$derivedkey2 = [Convert]::ToBase64String($sig2)

echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
```

```PowerShell
breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```


Имитированные устройства будут использовать производные ключи устройств с соответствующим идентификатором регистрации для выполнения аттестации симметричного ключа.




## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Подготовка среды разработки для пакета SDK Azure IoT для C

В этом разделе вы подготовите среду разработки, которая используется для сборки [пакета SDK Azure IoT для C](https://github.com/Azure/azure-iot-sdk-c). В пакет SDK входит пример кода для имитированного устройства. Для этого имитированного устройства будет выполнена попытка подготовки во время последовательности загрузки.

В этом разделе описывается использование рабочей станции Windows. Пример для Linux приведен в инструкциях по настройке виртуальных машин в статье [Подготовка к мультитенантности](how-to-provision-multitenant.md).



1. Скачайте версию 3.11.4 [системы сборки CMake](https://cmake.org/download/). Проверьте загруженный бинарный дистрибутив, используя соответствующее значение криптографического хэша. В следующем примере для проверки криптографического хэша для дистрибутива MSI x64 версии 3.11.4 использовалась среда Windows PowerShell:

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    Приведенные ниже значения хэша для версии 3.11.4 были указаны на сайте CMake на момент написания этой статьи.

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

    **Перед** установкой `CMake` очень важно установить на компьютер необходимые компоненты Visual Studio (Visual Studio с рабочей нагрузкой "Разработка классических приложений на C++"). После установки компонентов и проверки загрузки установите систему сборки CMake.

2. Откройте командную строку или оболочку Git Bash. Выполните следующую команду для клонирования репозитория GitHub пакета SDK Azure IoT для C.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Размер этого репозитория в настоящее время составляет примерно 220 МБ. Выполнение этой операции может занять несколько минут.


3. Создайте подкаталог `cmake` в корневом каталоге репозитория Git и перейдите в эту папку. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Выполните приведенную ниже команду, чтобы создать версию пакета SDK для используемой клиентской платформы разработки. Эта команда также создает решение Visual Studio для имитированного устройства в каталоге `cmake`. 

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```
    
    Если `cmake` не удастся найти компилятор C++, могут возникнуть ошибки сборки во время выполнения предыдущей команды. В этом случае попробуйте, выполнить эту команду в [командной строке Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    После успешного создания последние несколько строк выходных данных будут выглядеть следующим образом:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```




## <a name="simulate-the-devices"></a>Имитация устройств

В этом разделе вы обновите пример для подготовки **prov\_dev\_client\_sample**, который размещен в пакете SDK Azure IoT для C, настроенном ранее. 

Пример кода имитирует последовательность загрузки устройства, которое отправляет запрос на подготовку в экземпляр службы подготовки устройств. При выполнении последовательности загрузки устройство toaster будет распознано и назначено Центру Интернета вещей с помощью пользовательской политики выделения.

1. На портале Azure выберите вкладку **Обзор** службы подготовки устройств и запишите значение **_области идентификатора_**.

    ![Извлеките сведения о конечной точке службы подготовки устройств из колонки на портале](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. В Visual Studio откройте файл решения **azure_iot_sdks.sln**, который был создан ранее в результате запуска CMake. Файл решения должен находиться в следующем расположении:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. В окне *Обозреватель решений* Visual Studio перейдите в папку **Provision\_Samples**. Разверните пример проекта с именем **prov\_dev\_client\_sample**. Разверните **исходные файлы** и откройте **prov\_dev\_client\_sample.c**.

4. Найдите константу `id_scope` и замените ее значение ранее скопированным значением **области идентификатора**. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Найдите определение функции `main()` в том же файле. Проверьте, чтобы переменной `hsm_type` было задано значение `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, как показано ниже.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Щелкните проект **prov\_dev\_client\_sample** правой кнопкой мыши и выберите пункт **Назначить запускаемым проектом**. 

#### <a name="simulate-the-contoso-toaster-device"></a>Имитация устройства toaster Contoso

1. В окне *Обозреватель решений* Visual Studio перейдите в проект **hsm\_security\_client** и разверните его. Разверните **Исходные файлы** и откройте **hsm\_client\_key.c**. 

    Найдите объявление констант `REGISTRATION_NAME` и `SYMMETRIC_KEY_VALUE`. Внесите следующие изменения в файл и сохраните его.

    Обновите значение константы `REGISTRATION_NAME`, указав идентификатор регистрации для устройства toaster **breakroom499-contoso-tstrsd-007**.
    
    Обновите значение константы `SYMMETRIC_KEY_VALUE`, указав ключ устройства, созданный для устройства toaster. Значение **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** приведено только в качестве примера.

    ```c
    static const char* const REGISTRATION_NAME = "breakroom499-contoso-tstrsd-007";
    static const char* const SYMMETRIC_KEY_VALUE = "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=";
    ```

2. В меню Visual Studio выберите **Отладка** > **Запуск без отладки**, чтобы запустить решение. При появлении запроса перестроить проект щелкните **Да**, чтобы перестроить его перед запуском.

    Следующий результат является примером успешной загрузки имитированного устройства toaster и его подключения к экземпляру службы подготовки для назначения Центру Интернета вещей отдела тостеров с помощью пользовательской политики выделения.

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```


#### <a name="simulate-the-contoso-heat-pump-device"></a>Имитация устройства heat pump Contoso

1. В окне *Обозреватель решений* Visual Studio перейдите в проект **hsm\_security\_client** и разверните его. Разверните **Исходные файлы** и откройте **hsm\_client\_key.c**. 

    Найдите объявление констант `REGISTRATION_NAME` и `SYMMETRIC_KEY_VALUE`. Внесите следующие изменения в файл и сохраните его.

    Обновите значение константы `REGISTRATION_NAME`, указав идентификатор регистрации для устройства heat pump **mainbuilding167-contoso-hpsd-088**.
    
    Обновите значение константы `SYMMETRIC_KEY_VALUE`, указав ключ устройства, созданный для устройства heat pump. Значение **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** приведено только в качестве примера.

    ```c
    static const char* const REGISTRATION_NAME = "mainbuilding167-contoso-hpsd-088";
    static const char* const SYMMETRIC_KEY_VALUE = "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=";
    ```

7. В меню Visual Studio выберите **Отладка** > **Запуск без отладки**, чтобы запустить решение. При появлении запроса перестроить проект щелкните **Да**, чтобы перестроить его перед запуском.

    Следующий результат является примером успешной загрузки имитированного устройства heat pump и его подключения к экземпляру службы подготовки для назначения Центру Интернета вещей отдела тепловых насосов с помощью пользовательской политики выделения.

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```




## <a name="troubleshooting-custom-allocation-policies"></a>Устранение неполадок пользовательских политик выделения

Ниже приведены ожидаемые сценарии и коды ошибок, которые могут возникнуть. Используйте эту таблицу при устранении неполадок в работе пользовательских политик выделения с функциями Azure.


| Сценарий | Результат регистрации из службы подготовки | Результаты пакета SDK для подготовки |
| -------- | --------------------------------------------- | ------------------------ |
| Веб-перехватчик возвращает ответ "200 ОК" со свойством iotHubHostName, которому присвоено допустимое имя узла Центра Интернета вещей | Состояние результата: "Назначено"  | Пакет SDK возвращает PROV_DEVICE_RESULT_OK, а также сведения о центре |
| Веб-перехватчик возвращает ответ "200 ОК" со свойством iotHubHostName, но его значение является пустой строкой или NULL | Состояние результата: "Сбой"<br><br> Код ошибки: CustomAllocationIotHubNotSpecified (400208) | Пакет SDK возвращает PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Веб-перехватчик возвращает ответ "401 — недостаточно прав" | Состояние результата: "Сбой"<br><br>Код ошибки: CustomAllocationUnauthorizedAccess (400209) | Пакет SDK возвращает PROV_DEVICE_RESULT_UNAUTHORIZED |
| Была создана индивидуальная регистрация для отключения устройства | Состояние результата: "Отключено" | Пакет SDK возвращает PROV_DEVICE_RESULT_DISABLED |
| Веб-перехватчик возвращает код ошибки больше или равный 429 | Попытка повторной оркестрации DPS будет выполнена несколько раз. Текущее состояние политики повтора:<br><br>&nbsp;&nbsp; счетчик попыток: 10;<br>&nbsp;&nbsp; исходный интервал: 1 с;<br>&nbsp;&nbsp; шаг увеличения: 9 с. | Пакет SDK пропустит ошибку и отправит еще одно сообщение для получения состояния в указанное время. |
| Веб-перехватчик возвращает любой другой код состояния | Состояние результата: "Сбой"<br><br>Код ошибки: CustomAllocationFailed (400207) | Пакет SDK возвращает PROV_DEVICE_RESULT_DEV_AUTH_ERROR |


## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете продолжить работу с ресурсами, созданными при работе с этой статьей, не удаляйте их. В противном случае выполните следующие действия, чтобы удалить все созданные ресурсы, за которые может взиматься плата.

Здесь предполагается, что вы создали все используемые в этой статье ресурсы, как было указано, в одной группе ресурсов, **contoso-us-resource-group**.

> [!IMPORTANT]
> Удаление группы ресурсов — процесс необратимый. Группа ресурсов и все содержащиеся в ней ресурсы удаляются без возможности восстановления. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы. Если вы создали Центр Интернета вещей в группе ресурсов, содержащей ресурсы, которые нужно сохранить, удалите только ресурс Центра Интернета вещей, не удаляя всю группу ресурсов.
>

Удаление группы ресурсов по имени.

1. Войдите на [портал Azure](https://portal.azure.com) и щелкните **Группы ресурсов**.

2. Введите в текстовое поле **Фильтровать по имени...** имя вашей группы ресурсов: **contoso-us-resource-group**. 

3. Справа от своей группы ресурсов в списке результатов щелкните **...**, а затем выберите **Удалить группу ресурсов**.

4. Подтвердите операцию удаления группы ресурсов. Еще раз введите имя группы ресурсов для подтверждения и нажмите кнопку **Удалить**. Через некоторое время группа ресурсов и все ее ресурсы будут удалены.

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о повторной подготовке см. в статье, посвященной [основным понятиям повторной подготовки устройств в центре Интернета вещей](concepts-device-reprovision.md). 
- Дополнительные сведения об отмене подготовки см. в статье [Как отменить подготовку устройств, которые были автоматически подготовлены](how-to-unprovision-devices.md). 











