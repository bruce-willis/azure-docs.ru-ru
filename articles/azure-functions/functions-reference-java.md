---
title: Справочник разработчика Java по Функциям Azure | Документация Майкрософт
description: Информация о разработке функций на языке Java.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, functions, event processing, webhooks, dynamic compute, serverless architecture, java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 08/10/2018
ms.author: routlaw
ms.openlocfilehash: f0dc471e8875ad0d738fce10421c3586752148b9
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092315"
---
# <a name="azure-functions-java-developer-guide"></a>Руководство разработчика Java по Функциям Azure

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

## <a name="programming-model"></a>Модель программирования 

Функция Azure должна существовать как метод без отслеживания состояния, который обрабатывает входные данные и создает выходные данные. Вы можете написать методы экземпляра, но функция не должна зависеть от полей экземпляра класса. Все методы функции должны иметь модификатор доступа `public`.

Вы можете добавить несколько функций в проект. Не добавляйте функции в отдельные JAR-файлы.

## <a name="triggers-and-annotations"></a>Триггеры и заметки

 Функции Azure вызываются триггером, таким как HTTP-запрос, таймер или обновление данных. Функция должна обработать такой триггер и связанные с ним входные данные и создать на их основе одно или несколько выходных значений.

Используйте заметки Java, включенные в пакет [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation), чтобы привязать входные и выходные данные к своим методам. Пример кода с использованием заметок доступен в [справочных документах Java](/java/api/com.microsoft.azure.functions.annotation) для каждой заметки и справочной документации по привязке службы "Функции Azure", например для [триггеров HTTP](/azure/azure-functions/functions-bindings-http-webhook).

Входные и выходные данные триггера можно также определить в файле [function.json](/azure/azure-functions/functions-reference#function-code) для функции, а не использовать заметки. В этом случае не рекомендуется использовать файл `function.json` вместо заметок.

> [!IMPORTANT] 
> Чтобы локально выполнять триггеры BLOB-объекта, очереди или таблицы хранилища Azure, необходимо настроить учетную запись хранения Azure в файле [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file).

Пример использования заметок.

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Та же функция без применения заметок:

```java
package com.example;

public class MyClass {
    public static String echo(String in) {
        return in;
    }
}
```

И соответствующий файл `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="third-party-libraries"></a>Сторонние библиотеки 

Служба "Функции Azure" поддерживает использование сторонних библиотек. По умолчанию все зависимости, указанные в файле `pom.xml` проекта, будут автоматически связаны во время выполнения `mvn package`. Зависимости библиотек, не указанных как зависимости в файле `pom.xml`, поместите в каталог `lib` в корневой папке функции. Зависимости, размещенные в каталоге `lib`, будут добавлены в загрузчик системного класса во время выполнения.

## <a name="data-type-support"></a>Поддержка типов данных

Для входных и выходных данных вы можете использовать любые типы данных в Java, в том числе стандартные, пользовательские типы Java или специализированные типы Azure, которые определяются в пакете `azure-functions-java-library`. Среда выполнения Функций Azure попытается преобразовывать полученные входные данные в тот тип, который указан в вашем коде.

### <a name="strings"></a>строк

Значения, передаваемые методам функции, будут приводиться к строковым значениям, если для соответствующего входного параметра функции указан тип `String`. 

### <a name="plain-old-java-objects-pojos"></a>Объекты POJO

Строки в формате JSON будут приводиться к типам Java, если подпись входных данных функции ожидает данные конкретного типа Java. Это преобразование позволяет передавать данные в формате JSON и работать с типами Java.

Типы POJO, используемые как входные данные для функций, должны иметь такой же модификатор доступа `public`, как и соответствующие методы функции. Нет необходимости объявлять модификатор `public`для полей класса POJO. Например, строка JSON `{ "x": 3 }` может быть преобразована в такой тип POJO:

```Java
public class MyData {
    private int x;
}
```

### <a name="binary-data"></a>Двоичные данные

Двоичные данные представляются в коде функций Azure в формате `byte[]`. Чтобы привязать двоичные входные и выходные данные к функции, в файле function.json задайте для поля `dataType` значение `binary`.

```json
 {
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "blob",
      "name": "content",
      "direction": "in",
      "dataType": "binary",
      "path": "container/myfile.bin",
      "connection": "ExampleStorageAccount"
    },
  ]
}
```

Теперь эти данные можно использовать в коде функции так:

```java
// Class definition and imports are omitted here
public static String echoLength(byte[] content) {
}
```

Пустые входные значения могут соответствовать `null` в аргументе функций, но для устранения потенциальных пустых значений рекомендуется использовать `Optional<T>`.


## <a name="function-method-overloading"></a>Перегрузка методов функции

Вы можете использовать перегрузку, создавая несколько методов функции с одним именем и разными типами данных. Например, в классе могут быть `String echo(String s)` и `String echo(MyType s)`. Служба "Функции Azure" определяет, какой метод вызвать на основе типа входных данных (для входных данных HTTP тип MIME `text/plain` приводит к `String`, тогда как `application/json` представляет `MyType`).

## <a name="inputs"></a>Входные данные

Входные данные в Функциях Azure делятся на две категории: входные данные от триггера и дополнительные входные данных. Они по-разному представлены в `function.json`, но использование в коде Java полностью идентично. Давайте рассмотрим в качестве примера следующий фрагмент кода:

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String in,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") MyObject obj
    ) {
        return "Hello, " + in + " and " + obj.getKey() + ".";
    }

    public static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

При активации этой функции HTTP-запрос передается в функцию с помощью `String in`. Запись возвращается из Хранилища таблиц Azure на основе идентификатора в URL-адресе маршрута и становится доступной как `obj` в тексте функции.

## <a name="outputs"></a>outputs

Выходные данные можно выразить как возвращаемое значение или как параметры вывода. Если существует только один выход, мы рекомендуем использовать возвращаемое значение. Для нескольких выходов нужно использовать параметры вывода.

Возвращаемое значение является самым простым форматом вывода. Вы просто возвращаете значение любого типа, а среда выполнения Функций Azure пытается маршалировать его в фактический тип (например, HTTP-ответ).  Вы можете применить любые выходные заметки к методу функции (свойство имени заметки должно быть $return), чтобы определить выходные данные возвращаемого значения.

Чтобы вернуть несколько выходных значений, используйте тип `OutputBinding<T>`, который определен в пакете `azure-functions-java-library`. Если вам нужно одновременно создать HTTP-ответ и отправить сообщение в очередь, можно использовать примерно такой код:

Например, функция копирования содержимого большого двоичного объекта может быть определена как в коде ниже. Здесь заметка `@StorageAccount` используется для предотвращения дублирования свойства подключения для `@BlobTrigger` и `@BlobOutput`.

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Используйте `OutputBinding<byte[]`> для создания двоичного выходного значения (для параметров). Для возвращаемых значений просто используйте `byte[]`.

## <a name="specialized-types"></a>Специализированные типы

Иногда функции нужен более точный контроль над входными и выходными данными. В пакете `azure-functions-java-core` предоставляются специализированные типы, которые позволяют управлять сведениями о запросе и изменять возвращаемое состояние триггера HTTP:

| Специализированные типы      |       Цель        | Типичное применение                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Триггер HTTP     | Получение метода, заголовков или запросов |
| `HttpResponseMessage<T>` | Привязка к выходным данным HTTP | Возврат кодов состояния, отличных от 200   |

> [!NOTE] 
> Вы также можете использовать заметки `@BindingName` для получения HTTP-заголовков и запросов. Например, `@BindingName("name") String query` перебирает все заголовки и запросы из HTTP-запроса и передает эти значения методу. Например, `query` примет значение `"test"`, если будет получен запрос с URL-адресом `http://example.org/api/echo?name=test`.

### <a name="metadata"></a>Метаданные

Метаданные поступают из разных источников, таких как заголовки HTTP, HTTP-запросы и [метаданные триггеров](/azure/azure-functions/functions-triggers-bindings#trigger-metadata-properties). Используйте заметку `@BindingName` с именем метаданных, чтобы получить значение.

Например, `queryValue` в следующем фрагменте кода будет иметь значение `"test"`, если запрошенный URL-адрес — это `http://{example.host}/api/metadata?name=test`.

```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class MyClass {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```

## <a name="execution-context"></a>Контекст выполнения

Взаимодействие со средой выполнения Функций Azure выполняется с помощью объекта `ExecutionContext`, который определен в пакете `azure-functions-java-library`. Используйте объект `ExecutionContext`, чтобы в коде программы получить сведения о вызове и среде выполнения функции.

### <a name="custom-logging"></a>Настраиваемое ведение журналов

Доступ к средству ведения журнала среды выполнения функций выполняется с помощью объекта `ExecutionContext`. Это средство привязано к Azure Monitor и позволяет вам сохранять предупреждения и ошибки, возникшие во время выполнения функции.

Следующий пример кода записывает в журнал предупреждение, если получено пустое тело запроса.

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Просмотр журналов и трассировки

Вы можете использовать интерфейс Azure CLI для потоковой передачи журналов со стандартными данными и журналов ошибок в формате Java, а также ведения других журналов приложений. Сначала настройте приложение-функцию, чтобы записывать журнал приложений с помощью Azure CLI:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Чтобы передать выходные данные журналов в приложение-функцию с помощью Azure CLI, откройте новое окно командной строки, Bash или сеанс терминала и введите следующую команду:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
Команда [az webapp log tail](/cli/azure/webapp/log) позволяет отфильтровать выходные данные с помощью параметра `--provider`. 

Чтобы скачать файлы журналов как отдельный ZIP-файл с использованием интерфейса командной строки Azure, откройте командную строку, Bash или сеанс терминала и введите следующую команду:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Перед выполнением этой команды необходимо включить ведение журнала файловой системы на портале Azure или в Azure CLI.

## <a name="environment-variables"></a>Переменные среды

Из соображений безопасности храните секретную информацию, такую как ключи или маркеры, за пределами исходного кода. Используйте ключи и маркеры в своем коде функции, прочитав их из переменных среды.

Чтобы задать переменные среды при локальном выполнении Функций Azure, можно добавить эти переменные в файл local.settings.json. Если этот файл отсутствует в корневом каталоге проекта функции, вы можете создать его. Файл должен выглядеть следующим образом:

```xml
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "AzureWebJobsDashboard": ""
  }
}
```

Каждое сопоставление ключа и значения в карте `values` станет доступно во время выполнения как переменная среды, доступ к которой осуществляется путем вызова `System.getenv("<keyname>")`, например `System.getenv("AzureWebJobsStorage")`. Добавление дополнительных пар ключа и значения приемлемо и рекомендуется.

> [!NOTE]
> При таком подходе добавьте файл local.settings.json в файл игнорирования репозитория, чтобы он не был зафиксирован.

Так как ваш код теперь зависит от этих переменных среды, можно войти на портал Azure, чтобы задать те же пары "ключ — значение" в настройках приложения-функции. В таком случае код будет функционировать одинаково как при локальном тестировании, так и при развертывании в Azure.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о разработке функций Azure на Java см. в следующих статьях.

* [Рекомендации по функциям Azure](functions-best-practices.md)
* [Справочник разработчика по функциям Azure](functions-reference.md)
* [Azure Functions triggers and bindings (Триггеры и привязки в Функциях Azure)](functions-triggers-bindings.md)
- Разработка и отладка в локальной среде с помощью [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) и [Eclipse](functions-create-maven-eclipse.md). 
* [Remote Debug Java Azure Functions with Visual Studio Code (Удаленная отладка функций Azure на языке Java с помощью Visual Studio Code)](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Подключаемый модуль Maven для функций Azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md). Упрощает создание функций благодаря использованию цели `azure-functions:add` и позволяет подготовить промежуточный каталог для [развертывания ZIP-файла](deployment-zip-push.md).
