---
title: включение файла
description: включение файла
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 08/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3cbe634d862682a5f6b06c2cfc77a4d3b03954f9
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42809510"
---
Код всех функций конкретного приложения-функции хранится в корневой папке (`wwwroot`), содержащей файл конфигурации главного узла и одну или несколько вложенных папок. Каждая вложенная папка содержит код для отдельной функции, как показано в следующем примере:

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
 | - bin
 | | - mycompiledcsharp.dll
```

Файл host.json содержит конфигурацию среды выполнения. Он находится в корневой папке приложения-функции. Дополнительные сведения о доступных параметрах см. в [справочнике по host.json](../articles/azure-functions/functions-host-json.md).

У каждой функции есть папка, которая содержит один или несколько файлов кода, файл конфигурации function.json и другие зависимости. Для проекта библиотеки классов C# скомпилированный файл библиотеки классов (DLL) развертывается во вложенную папку `bin`.

