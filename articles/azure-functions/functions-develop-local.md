---
title: Разработка и выполнение Функций Azure локально | Документация Майкрософт
description: Узнайте, как программировать и тестировать функции Azure на локальном компьютере перед их запуском в Функциях Azure.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/27/2018
ms.author: glenga
ms.openlocfilehash: 4425c0594f4a3520f780b723d2ffbd41fc4b62c7
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2018
ms.locfileid: "42146743"
---
# <a name="code-and-test-azure-functions-locally"></a>Как программировать и тестировать Функции Azure в локальной среде

Хотя вы можете разрабатывать и тестировать Функции Azure на [портал Azure], многие разработчики предпочитают локальную среду разработки. Решение "Функции" позволяет с легкостью использовать предпочитаемый редактор кода и средства разработки для создания и тестирования функций на локальном компьютере. Локальные функции можно подключать к действующим службам Azure, а их отладку можно выполнять на локальном компьютере с помощью полной среды выполнения решения "Функции".

## <a name="local-development-environments"></a>Локальные среды разработки

Способ разработки функций на локальном компьютере зависит от настроек [языка](supported-languages.md) и инструментария. В нижеприведенной таблице указаны среды, которые поддерживают локальную разработку:

|Среда                              |Языки         |ОПИСАНИЕ|
|-----------------------------------------|------------|---|
| [Командная строка или терминал](functions-run-local.md) | [C# (библиотека классов)](functions-dotnet-class-library.md), [скрипт C# (CSX)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md) | [Основные инструменты службы "Функции Azure"] предоставляют базовую среду выполнения и шаблоны для создания функций, которые обеспечивают возможность локальной разработки. Версия 2.x поддерживает разработку в Linux, MacOS и Windows. Во всех средах применяют основные инструменты для локальной среды выполнения средства "Функции". |
|[Visual Studio Code](https://code.visualstudio.com/tutorials/functions-extension/getting-started)| [Скрипт C# (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md) | [Расширение Функций Azure для VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) добавляет поддержку средства "Функции" в VS Code. Требуются основные инструменты. Поддерживает разработку на платформах Linux, MacOS и Windows при использовании версии 2.x основных инструментов. Дополнительные сведения см. в статье [Развертывание в Azure с использованием Функций Azure](https://code.visualstudio.com/tutorials/functions-extension/getting-started).  |
| [Visual Studio 2017](functions-develop-vs.md) | [C# (библиотека классов)](functions-dotnet-class-library.md) | Инструменты решения "Функции Azure" включены в рабочую нагрузку **разработки Azure** в [Visual Studio 2017 версии 15.5](https://www.visualstudio.com/vs/) или более поздних версий. Позволяет компилировать функции в библиотеке классов и публиковать библиотеки DLL в Azure. Включает основные инструменты для локального тестирования. Дополнительные сведения см. в статье [Инструменты Функций Azure для Visual Studio](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) | [Java](functions-reference-java.md) | Интегрируется с основными инструментами и обеспечивает возможность разработки функций Java. Версия 2.x поддерживает разработку в Linux, MacOS и Windows. Дополнительные сведения см. в статье [Создание первой функции с помощью Java и Maven](functions-create-first-java-maven.md).|

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Каждая из этих локальных сред разработки позволяет создавать проекты приложений-функций и использовать предопределенные шаблоны решения "Функции" для создания новых функций. Каждая из них использует основные инструменты, чтобы вы могли тестировать и отлаживать функции в реальной среде выполнения решения "Функции" на своем компьютере как и любое другое приложение. Вы также можете опубликовать проект приложения-функции из любой из этих сред в Azure.  

## <a name="next-steps"></a>Дополнительная информация

+ Дополнительные сведения о локальной разработке скомпилированных функций C# с помощью Visual Studio 2017 см. в статье [Инструменты Функций Azure для Visual Studio](functions-develop-vs.md).
+ Дополнительные сведения о локальной разработке функций с помощью VS Code на компьютерах с ОС Mac, Linux или Windows см. в статье [VS Code documentation for Azure Functions](https://code.visualstudio.com/tutorials/functions-extension/getting-started) (Документация VS Code для Функций Azure).
+ Дополнительные сведения о разработке функций из командной строки или терминала см. в статье [Как программировать и тестировать Функции Azure в локальной среде](functions-run-local.md).

<!-- LINKS -->

[Основные инструменты службы "Функции Azure"]: https://www.npmjs.com/package/azure-functions-core-tools
[портал Azure]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
