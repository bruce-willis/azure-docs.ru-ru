---
title: Запуск основных инструментов службы "Функции Azure" | Документация Майкрософт
description: Узнайте, как программировать и тестировать функции Azure из командной строки или терминала на локальном компьютере, прежде чем запускать их в службе "Функции Azure".
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/26/2018
ms.author: glenga
ms.openlocfilehash: 57011e1f7633688e00a4639ba36fd4442073161d
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618620"
---
# <a name="work-with-azure-functions-core-tools"></a>Запуск основных инструментов службы "Функции Azure"

Основные инструменты службы "Функции Azure" позволяют разрабатывать и тестировать функции на локальном компьютере из командной строки или терминала. Локальные функции можно подключать к действующим службам Azure, а отладку функций можно выполнять на локальном компьютере с помощью полной среды выполнения службы "Функции Azure". Также есть возможность развернуть приложение-функцию в подписке Azure.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

## <a name="core-tools-versions"></a>Версии основных инструментов

Есть две версии основных инструментов службы "Функции Azure". Версия, использование которой зависит от локальной среды разработки, выбора языка и требуемого уровня поддержки.

+ [Версия 1.x](#v1) поддерживает общедоступную (GA) версию 1.x среды выполнения. Эта версия поддерживается только на компьютерах с ОС Windows и устанавливается из [пакета npm](https://docs.npmjs.com/getting-started/what-is-npm). В этой версии можно создавать функции на экспериментальных языках, которые не имеют официальной поддержки. Дополнительные сведения см. в разделе [Supported languages in Azure Functions](supported-languages.md) (Поддерживаемые языки службы "Функции Azure")

+ [Версия 2.x](#v2): поддерживает версию [2.x среды выполнения](functions-versions.md). Эта версия поддерживает [Windows](#windows-npm), [macOS](#brew) и [Linux](#linux). Использует диспетчеры пакетов определенной платформы или пакеты npm для установки. Как и среда выполнения 2.x эта версия основных инструментов на данный момент доступна для ознакомления. 

Если иное не указано, примеры в этой статье предназначены для версии 2.x. Чтобы получать важные обновления версии 2.x, включая объявления о критических изменениях, перейдите в репозиторий [объявлений о Службе приложений Azure](https://github.com/Azure/app-service-announcements/issues).

## <a name="install-the-azure-functions-core-tools"></a>Установка основных инструментов Функций Azure

[Основные инструменты службы "Функции Azure"] являются локальной версией среды выполнения "Функции Azure", которую можно запускать на локальном компьютере для разработки. Она также предоставляет команды для создания функций, подключения к Azure и развертывания проектов функций.

### <a name="v1"></a>Версия 1.x

В исходной версии инструментов используется среда выполнения Функций 1.x. Эта версия использует .NET Framework (4.7.1) и поддерживается только на компьютерах с Windows. Прежде чем устанавливать инструменты версии 1.x, необходимо [установить NodeJS](https://docs.npmjs.com/getting-started/installing-node), в состав которого входит пакет npm.

Чтобы установить инструменты версии 1.x, используйте следующую команду:

```bash
npm install -g azure-functions-core-tools
```

### <a name="v2"></a>Версия 2.x

>[!NOTE]
> Среда выполнения Функций Azure версии 2.0 доступна в предварительной версии. Сейчас поддерживаются не все возможности Функций Azure. Дополнительные сведения см. в статье [Обзор версий среды выполнения для решения "Функции Azure"](functions-versions.md). 

В версии 2.x инструментов используется среда выполнения Функций Azure версии 2.x, которая основана на .NET Core. Эта версия поддерживается на всех платформах, которые поддерживает .NET Core 2.x, включая [Windows](#windows-npm), [macOS](#brew) и [Linux](#linux).

#### <a name="windows-npm"></a>Windows

На следующих шагах пакет npm используется для установки основных инструментов на компьютерах с Windows. Кроме того, можно использовать [Chocolatey](https://chocolatey.org/). Дополнительные сведения см. в [файле сведений об основных инструментах](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Установите [.NET Core 2.1 для Windows](https://www.microsoft.com/net/download/windows).

2. Установите [Node.js], который содержит пакет npm. Для версии 2.x этих инструментов поддерживается только версия Node.js 8.5 и более поздние.

3. Установите пакет основных инструментов:

    ```bash
    npm install -g azure-functions-core-tools@core
    ```

#### <a name="brew"></a>MacOS с Homebrew

На следующих шагах Homebrew используется для установки основных инструментов на компьютерах macOS.

1. Установите [.NET Core 2.1 для macOS](https://www.microsoft.com/net/download/macos).

2. Установите [Homebrew](https://brew.sh/), если вы этого не сделали ранее.

3. Установите пакет основных инструментов:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools 
    ```

#### <a name="linux"></a> Linux (Ubuntu/Debian) с APT

На следующих шагах [APT](https://wiki.debian.org/Apt) используется для установки основных инструментов на дистрибутив Linux Ubuntu/Debian. Чтобы выполнить установку на другие дистрибутивы Linux, ознакомьтесь с [файлом сведений об основных инструментах](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Установите [.NET Core 2.1 для Linux](https://www.microsoft.com/net/download/linux).

2. Зарегистрируйте ключ продукта Майкрософт как доверенный:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

3. Убедитесь, что на сервере используется одна из соответствующих версий Ubuntu, указанных в таблице ниже. Чтобы добавить источник apt, выполните команду:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    sudo apt-get update
    ```

    | Дистрибутив Linux | Version (версия) |
    | --------------- | ----------- |
    | Ubuntu 17.10    | `artful`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

4. Установите пакет основных инструментов:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

## <a name="create-a-local-functions-project"></a>Создание локального проекта службы "Функции"

Каталог проекта функций содержит файлы [host.json](functions-host-json.md) и [local.settings.json](#local-settings-file), а также вложенные папки, которые содержат код для отдельных функций. Этот каталог является эквивалентом приложения-функции в Azure. Дополнительные сведения о структуре папок службы "Функции Azure" см. в [Руководстве для разработчиков по Функциям Azure](functions-reference.md#folder-structure).

Версия 2.x требует при ее создании выбрать для проекта язык по умолчанию, и все добавленные функции будут использовать шаблоны языка по умолчанию. В версии 1.x пользователю необходимо указывать язык при каждом создании функции.

В окне терминала или из командной строки выполните следующую команду, чтобы создать проект и локальный репозиторий Git:

```bash
func init MyFunctionProj
```

При запуске команды в версии 2.x необходимо выбрать среду выполнения для проекта. Для разработки функций JavaScript выберите следующий **узел**.

```output
Select a worker runtime:
dotnet
node
java
```

Для выбора языка используйте СТРЕЛКИ ВВЕРХ и ВНИЗ и клавишу ВВОД. Результат для проекта JavaScript выглядит примерно следующим образом.

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

Чтобы создать проект без локального репозитория Git, используйте параметр `--no-source-control [-n]`.

> [!IMPORTANT]
> По умолчанию версия 2.x средства Core создает проекты приложений функций для среды выполнения .NET как [проектов класса C#](functions-dotnet-class-library.md) (.csproj). Это проекты C#, которые могут использоваться с Visual Studio 2017 или Visual Studio Code, собираются во время тестирования и при публикации в Azure. Если вы хотите создавать и работать с тем же файлом сценария C# (.csx), созданным в версии 1.x и на портале, необходимо указать параметр `--csx` при создании и развертывании функций.

## <a name="register-extensions"></a>Регистрация расширений

В версии 2.х среды выполнения "Функции Azure" нужно явно зарегистрировать расширения привязки (типы привязки), используемые в приложении-функции.

[!INCLUDE [Register extensions](../../includes/functions-core-tools-install-extension.md)]

Дополнительные сведения см. в статье [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md#register-binding-extensions).

## <a name="local-settings-file"></a>Файл с локальными параметрами

Файл local.settings.json хранит параметры приложения, строки подключения и параметры основных инструментов службы "Функции Azure". Он имеет следующую структуру:

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*"
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

| Параметр      | ОПИСАНИЕ                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | Если задано значение **true**, все значения шифруются с помощью ключа локального компьютера. Используется с командами `func settings`. Значение по умолчанию — **false**. |
| **Значения** | Коллекция параметров приложения и строк подключения, используемых при локальном выполнении. Эти значения соответствуют параметрам приложения в приложении-функции в Azure, таким как **AzureWebJobsStorage** и **AzureWebJobsDashboard**. Многие триггеры и привязки имеют свойство, относящееся к строке подключения параметра приложения, например **Connection** для [триггера хранилища BLOB-объектов](functions-bindings-storage-blob.md#trigger---configuration). Для таких свойств требуется параметр приложения, определенный в массиве **Values**. <br/>**AzureWebJobsStorage** — это необходимый параметр приложения для триггеров, отличных от HTTP. При наличии [эмулятора хранилища Azure](../storage/common/storage-use-emulator.md), установленного локально, можно задать **AzureWebJobsStorage** для использования эмулятора `UseDevelopmentStorage=true` и Core Tools. Во время разработки это удобно, но следует проверить подключение к фактическому хранилищу перед развертыванием. |
| **Host** | Параметры в этом разделе служат для настройки хост-процесса Функций при выполнении в локальной среде. |
| **LocalHttpPort** | Задает порт по умолчанию, используемый при выполнении локального узла Функций (`func host start` и `func run`). Параметр командной строки `--port` имеет приоритет над этим значением. |
| **CORS** | Определяет источники, для которых разрешен [общий доступ к ресурсам независимо от источника (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Источники указываются в виде разделенного запятыми списка без пробелов. Допускается подстановочное значение (\*), разрешающее запросы из любого источника. |
| **ConnectionStrings** | Не применяйте эту коллекцию для строк подключения, используемых функциями привязки. Эта коллекция используется только платформами, которые обычно получают строки подключений из раздела файла конфигурации **ConnectionStrings**, например [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Строки подключения, содержащиеся в этом объекте, добавляются в среду с типом поставщика [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Элементы этой коллекции не публикуются в Azure с другими параметрами приложения. Необходимо явным образом добавить эти значения в коллекцию **Строки подключений** в настройках приложения-функции. Если вы создаете параметр [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) в коде функции, следует сохранить значение строки подключения в **Параметрах приложения** с другими подключениями. |

Эти значения параметров приложения-функции также могут считываться в коде как переменные среды. Дополнительные сведения см. в разделе о переменных среды в этих справочниках для определенного языка:

+ [Предкомпилированный код C#](functions-dotnet-class-library.md#environment-variables)
+ [Скрипт C# (CSX)](functions-reference-csharp.md#environment-variables)
+ [F#](functions-reference-fsharp.md#environment-variables)
+ [Java](functions-reference-java.md#environment-variables) 
+ [JavaScript](functions-reference-node.md#environment-variables)

Параметры в файле local.settings.json используются инструментами Функций только при выполнении в локальной среде. По умолчанию эти параметры не переносятся автоматически при публикации проекта в Azure. [При публикации](#publish) используйте параметр `--publish-local-settings`, чтобы добавить эти параметры в приложение-функцию в Azure. Значения **ConnectionStrings** никогда не публикуются.

Если для **AzureWebJobsStorage** не задана допустимая строка подключения к хранилищу и не используется эмулятор, выводится следующее сообщение об ошибке:  

>Отсутствует значение AzureWebJobsStorage в local.settings.json. This is required for all triggers other than HTTP. You can run 'func azure functionapp fetch-app-settings <functionAppName>' or specify a connection string in local.settings.json (Отсутствует значение AzureWebJobsStorage в local.settings.json. Оно требуется для всех триггеров, отличных от HTTP. Выполните команду func azure functionapp fetch-app-settings или укажите строку подключения в файле local.settings.json).

### <a name="get-your-storage-connection-strings"></a>Получение параметров строк подключения службы хранилища

Даже при использовании эмулятора хранилища для разработки приложений можно проверить подключение к фактическому хранилищу. При условии, что ваша [учетная запись хранения создана](../storage/common/storage-create-storage-account.md), действительную строку подключения к хранилищу можно получить одним из следующих способов:

+ [портал Azure]. Перейдите к учетной записи хранения, выберите **Ключи доступа** в разделе **Параметры**, а затем скопируйте одно из значений **Строка подключения**.

  ![Копирование строки подключения с портала Microsoft Azure](./media/functions-run-local/copy-storage-connection-portal.png)

+ Подключитесь к учетной записи Azure с помощью [Обозревателя службы хранилища Azure](http://storageexplorer.com/). В **Explorer** разверните свою подписку, выберите учетную запись хранения и скопируйте основную или вторичную строку подключения. 

  ![Скопируйте строку подключения из Обозревателя службы хранилища](./media/functions-run-local/storage-explorer.png)

+ Используйте Core Tools для загрузки строки подключения из Azure при помощи одной из следующих команд:

    + Загрузите все параметры из существующего приложения-функции:

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    + Получите строку подключения для указанной учетной записи хранения:

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    
    Если вы еще не вошли в Azure, вам будет предложено сделать это.

## <a name="create-func"></a>Создание функции

Чтобы создать функцию, выполните следующую команду:

```bash
func new
```

В версии 2.x при запуске `func new` для приложения-функции предлагается выбрать шаблон языка по умолчанию, а затем имя для функции. В версии 1.x также предлагается выбрать язык.

```output
Select a language: Select a template:
Blob trigger
Cosmos DB trigger
Event Grid trigger
HTTP trigger
Queue trigger
SendGrid
Service Bus Queue trigger
Service Bus Topic trigger
Timer trigger
```

Код функции создается во вложенной папке с предоставленным именем функции, как показано в следующем выходе триггера очереди.

```output
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
```

Эти параметры можно также задать в команде, указав следующие аргументы.

| Аргумент     | ОПИСАНИЕ                            |
| ------------------------------------------ | -------------------------------------- |
| **`--language -l`**| Язык программирования шаблона, например C#, F# или JavaScript. Этот параметр необходим в версии 1.x. В версии 2.x этот параметр либо вообще не используется, либо для проекта выбирается язык по умолчанию. |
| **`--template -t`** | Используйте команду `func templates list`, чтобы просмотреть полный список доступных шаблонов для каждого поддерживаемого языка.   |
| **`--name -n`** | Имя функции. |
| **`--csx`** | (Версия 2.x). Создаются те же шаблоны сценариев C# (.csx), которые используются в версии 1.x и на портале. |

Например, чтобы создать триггер HTTP на JavaScript одной командой, выполните следующую команду.

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

Чтобы создать активируемую с помощью очереди функцию одной командой, выполните следующую команду.

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>Запуск функций локально

Чтобы запустить проект службы "Функции", запустите узел этой службы. Узел включает триггеры для всех функций в проекте.

```bash
func host start
```

`func host start` имеет указанные ниже параметры.

| Параметр     | ОПИСАНИЕ                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | Локальный порт для прослушивания. Значение по умолчанию: 7071. |
| **`--debug <type>`** | Запускается узел с открытым портом отладки, чтобы была возможность подключиться к процессу **func.exe** из [Visual Studio Code](https://code.visualstudio.com/tutorials/functions-extension/getting-started) или [Visual Studio 2017](functions-dotnet-class-library.md). Параметр *\<тип\>* имеет значения `VSCode` и `VS`.  |
| **`--cors`** | Список разрешенных источников CORS, разделенный запятыми без пробелов. |
| **`--nodeDebugPort -n`** | Порт отладчика узла. Значение по умолчанию — значение из launch.json или 5858. |
| **`--debugLevel -d`** | Уровень трассировки консоли (off, verbose, info, warning или error). Значение по умолчанию — info.|
| **`--timeout -t`** | Время ожидания для запуска узла службы "Функции" в секундах. Значение по умолчанию — 20 секунд.|
| **`--useHttps`** | Привязка к `https://localhost:{port}`, а не к `http://localhost:{port}`. По умолчанию этот параметр создает доверенный сертификат на компьютере.|
| **`--pause-on-error`** | Приостановка для получения дополнительных входных данных перед выходом из процесса. Используется при запуске основных инструментов из Visual Studio или VS Code.|

При запуске узла службы "Функции" выводится URL-адрес функций, активируемых по HTTP:

```bash
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="passing-test-data-to-a-function"></a>Передача тестовых данных в функцию

Чтобы протестировать функции в локальной среде, [запустите узел службы "Функции"](#start) и вызовите конечные точки на локальном сервере, используя HTTP-запросы. Вызываемая конечная точка зависит от типа функции.

>[!NOTE]  
> В примерах в этой статье используется инструмент cURL для отправки HTTP-запросов из терминала или командной строки. Вы можете использовать любой инструмент для отправки HTTP-запросов к локальному серверу. Инструмент cURL доступен по умолчанию в системах на основе Linux. При использовании Windows [инструмент cURL](https://curl.haxx.se/) необходимо сначала скачать и установить.

Дополнительные сведения о тестировании функций см. в статье [Методика тестирования кода с помощью Функций Azure](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Функции, активируемые по протоколу HTTP или с помощью веб-перехватчика

Вызовите следующую конечную точку, чтобы запустить в локальной среде функции, активируемые по протоколу HTTP или с помощью веб-перехватчика.

    http://localhost:{port}/api/{function_name}

Используйте то же имя сервера и порт, прослушиваемый узлом службы "Функции". Их можно найти в выходных данных, полученных при запуске узла службы "Функции". Этот URL-адрес можно вызвать с помощью любого метода HTTP с поддержкой триггера. 

Следующая команда cURL активирует функцию быстрого запуска `MyHttpTrigger` из запроса GET с параметром _name_, который передается в строке запроса. 

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```
В следующем примере представлена та же функция, вызываемая из запроса POST с передачей параметра _name_ в тексте запроса:

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Запросы GET можно выполнять из браузера, передавая данные в строке запроса. Для всех остальных методов HTTP необходимо использовать cURL, Fiddler, Postman или аналогичный инструмент тестирования HTTP.  

#### <a name="non-http-triggered-functions"></a>Функции, не активируемые по протоколу HTTP

Все виды функций, кроме триггеров HTTP и веб-перехватчиков, можно тестировать в локальной среде путем вызова конечной точки администрирования. Вызов этой конечной точки при помощи запроса HTTP POST на локальном сервере активирует функцию. При необходимости можно передать тестовые данные в среду выполнения в тексте запроса POST. Это аналогично выполнению функции с помощью вкладки **Тест** на портале Azure.  

Вызовите следующую конечную точку администрирования, чтобы активировать функции, отличные от HTTP:

    http://localhost:{port}/admin/functions/{function_name}

Чтобы передать тестовые данные в конечную точку администрирования функции, укажите данные в тексте запроса POST. Текст сообщения должен иметь следующий формат JSON:

```JSON
{
    "input": "<trigger_input>"
}
````

Значение `<trigger_input>` содержит данные в формате, ожидаемом функцией. В следующем примере представлен запрос POST к функции `QueueTriggerJS`. В этом случае входные данные представляют собой строку, соответствующую сообщению, которое нужно найти в очереди.

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Использование команды `func run` в версии 1.x

>[!IMPORTANT]  
> Команда `func run` не поддерживается в версии 2.x инструментов. Дополнительные сведения см. в статье [Выбор целевых версий среды выполнения Функций Azure](set-runtime-version.md).

Вы также можете вызвать функцию напрямую с помощью `func run <FunctionName>` и предоставить входные данные для нее. Эта команда аналогична выполнению функции с помощью вкладки **Тест** на портале Azure. 

`func run` имеет указанные ниже параметры.

| Параметр     | ОПИСАНИЕ                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Встроенное содержимое. |
| **`--debug -d`** | Подключение отладчика к хост-процессу перед выполнением функции.|
| **`--timeout -t`** | Время ожидания (в секундах), пока не будет готов локальный узел службы "Функции".|
| **`--file -f`** | Имя файла для использования в качестве содержимого.|
| **`--no-interactive`** | Не запрашивает тип входных данных. Полезно для сценариев автоматизации.|

Например, для вызова функции, активируемой по HTTP, и передачи основного содержимого выполните следующую команду:

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

### <a name="viewing-log-files-locally"></a>Просмотр файлов журнала в локальной среде

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="publish"></a>Публикация в Azure

Чтобы опубликовать проект функций для приложения-функции в Azure, используйте команду `publish`:

```bash
func azure functionapp publish <FunctionAppName>
```

Можно использовать приведенные ниже параметры.

| Параметр     | ОПИСАНИЕ                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Публикация параметров из файла local.settings.json в Azure с запросом на перезапись, если параметр уже существует. Если используется эмулятор хранилища, измените параметр приложения на [подключение действующего хранилища](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Должен использоваться с `-i`. При другом значении перезаписывает локальное значение AppSettings в Azure. Значение по умолчанию — запрос.|

Эта команда публикует в существующее приложение-функцию в Azure. Если в подписке не существует `<FunctionAppName>`, то возникает ошибка. Чтобы узнать, как создать приложение-функцию из командной строки или из окна терминала, используя Azure CLI, см. статью [Создание приложения-функции для выполнения без сервера](./scripts/functions-cli-create-serverless.md).

Команда `publish` отправляет содержимое в каталог проекта функций. При удалении файлов в локальной среде команда `publish` не удаляет их из Azure. Удалить файлы в Azure можно с помощью [средства Kudu](functions-how-to-use-azure-function-app-settings.md#kudu) на [портал Azure].  

>[!IMPORTANT]  
> При создании приложения-функции в Azure по умолчанию используется версия 1.x среды выполнения Функций. Чтобы заставить приложение-функцию использовать версию 2.x среды выполнения, добавьте параметр приложения `FUNCTIONS_EXTENSION_VERSION=beta`.  
Используйте следующий код Azure CLI для добавления этого параметра в приложение-функцию:

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings FUNCTIONS_EXTENSION_VERSION=beta   
```

## <a name="next-steps"></a>Дополнительная информация

Основные инструменты службы "Функции Azure" [имеют открытый код и размещаются на GitHub](https://github.com/azure/azure-functions-cli).  
Чтобы зарегистрировать ошибку или отправить запрос на функцию, [откройте вопрос на GitHub](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Основные инструменты службы "Функции Azure"]: https://www.npmjs.com/package/azure-functions-core-tools
[портал Azure]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
