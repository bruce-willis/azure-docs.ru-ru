---
title: Создание приложения-функции Azure с использованием Java и IntelliJ | Документация Майкрософт
description: Практическое руководство по созданию и публикации простого независимого HTTP-приложения с использованием функций Java и IntelliJ для Azure.
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: azure functions, functions, event processing, compute, serverless architecture, java
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: e926bfb023fe3edfd564aa6389e21f6594bec169
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39117064"
---
# <a name="create-your-first-function-with-java-and-intellij-preview"></a>Создание первой функции с использованием Java и IntelliJ (предварительная версия)

> [!NOTE] 
> Сейчас доступна предварительная версия Java для Функций Azure.

В этой статье показано, как создать [независимый](https://azure.microsoft.com/overview/serverless-computing/) проект с помощью IntelliJ IDEA и Apache Maven, протестировать и отладить его на своем компьютере из среды IDE и развернуть его в Функциях Azure. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Настройка среды разработки

Для разработки функций приложения с помощью Java и IntelliJ должны быть установлены следующие компоненты:

-  [Java Developer Kit (JDK)](https://www.azul.com/downloads/zulu/) версии 8.
-  [Apache Maven](https://maven.apache.org) 3.0 или более поздней версии.
-  [IntelliJ IDEA](https://www.jetbrains.com/idea/download), версия Community или Ultimate с инструментами Maven.
-  [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> Переменной среде JAVA_HOME необходимо присвоить расположение установки JDK, чтобы завершить выполнение заданий этого краткого руководства.

Рекомендуется установить [Azure Functions Core Tools, версии 2](functions-run-local.md#v2), которые предоставляют возможность записи, выполнения и отладки функций Azure. 


## <a name="create-a-functions-project"></a>Создание проекта Функций Azure

1. В IntelliJ IDEA щелкните **Создать новый проект**.  
1. Выберите создать из **Maven**
1. Установите флажок **Создать из архетипа** и **Добавить архетип** для [ azure-functions-archetype ](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
    - GroupId: com.microsoft.azure
    - ArtifactId: azure-functions-archetype
    - Версия: используйте последнюю версию из [центрального репозитория](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Создание Maven в IntelliJ](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Нажмите кнопку **Далее** и введите сведения для текущего проекта и в конечном итоге нажмите **Готово**.

Maven создает файлы проекта в новой папке с именем _artifactId_. Созданный код проекта представляет собой простую функцию[активации HTTP](/azure/azure-functions/functions-bindings-http-webhook), которая возвращает текст HTTP-запроса.

## <a name="run-functions-locally-in-the-ide"></a>Запуск функций в локальной среде IDE

> [!NOTE]
> Для локальных запуска и отладки функций, нужно установить [Azure Functions Core Tools версии 2](functions-run-local.md#v2).

1. Выберите "Импортировать изменения" или убедитесь, что включен [автоматический импорт](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Откройте панель инструментов **Maven Projects** (Проекты Maven)
1. В разделе "Жизненный цикл" дважды щелкните **пакет**, чтобы упаковать и собрать решения, а затем создать целевой каталог.
1. В разделе "Подключаемые модули" -> azure-functions дважды щелкните **azure-functions:run**, чтобы запустить локальную среду выполнения Azure.  
  ![Панель инструментов Maven для Функций Azure](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

После того как вы протестировали функцию, закройте диалоговое окно запуска. Только один узел функции может быть активным и работать локально одновременно.

### <a name="debug-the-function-in-intellij"></a>Отладка функции в IntelliJ

Вы можете отлаживать функции в IntelliJ, путем подключения к узлу функции после запуска.  Запустите функции Azure локально, используя приведенные выше инструкции, а затем в меню **Запуск** выберите **Присоединить к локальному процессу**.  Вы должны наблюдать процесс на порту 5005.  После присоединения вы можете использовать попадание в точки останова и отлаживать их в своем функциональном приложении.

По завершении остановите отладчик и запущенный процесс. Только один узел функции может быть активным и работать локально одновременно.

## <a name="deploy-the-function-to-azure"></a>Развертывание функции для Azure

В процессе развертывания для Функций Azure используются данные учетной записи из Azure CLI. [Войдите с помощью Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) прежде чем продолжить, используя командную строку компьютера.

```azurecli
az login
```

Разверните свой код в новое приложение-функцию с помощью целевого объекта Maven `azure-functions:deploy` или выберите параметр azure-functions:deploy в окне Maven Projects (Проекты Maven).

```
mvn azure-functions:deploy
```

После завершения развертывания появится URL-адрес, который можно использовать для доступа к приложению-функции Azure:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о разработке функции Java см. в статье [Azure Functions Java developer guide](functions-reference-java.md) (Руководство разработчика Java для Функций Azure).
- Добавьте в проект дополнительные функции с помощью различных триггеров целевого объекта Maven`azure-functions:add`.
