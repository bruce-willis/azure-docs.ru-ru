---
title: Создание первой функции в Azure с помощью Visual Studio | Документация Майкрософт
description: Создание и публикация активируемой HTTP функции Azure с помощью Visual Studio.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: функции azure, функции, обработка событий, вычисления, независимая архитектура
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 05/22/2018
ms.author: glenga
ms.custom: mvc, devcenter, , vs-azure, 23113853-34f2-4f
ms.openlocfilehash: 582e949c6fdf4333690264dfd5b1cf04234efdd4
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092858"
---
# <a name="create-your-first-function-using-visual-studio"></a>Создание первой функции с помощью Visual Studio

Решение "Функции Azure" позволяет выполнять код в [бессерверной](https://azure.microsoft.com/overview/serverless-computing/) среде без необходимости создавать виртуальную машину или публиковать веб-приложение.

Изучив эту статью, вы научитесь использовать инструменты Visual Studio 2017 для службы "Функции Azure" и локально тестировать функцию hello world. Затем вы опубликуете код функции в Azure. Эти средства доступны как часть рабочей нагрузки Azure для разработки в Visual Studio 2017.

В этом разделе содержится [видео](#watch-the-video), демонстрирующее те же основные действия.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством:

* Установите [Visual Studio 2017](https://azure.microsoft.com/downloads/) и убедитесь, что также установлена рабочая нагрузка **разработки Azure**.

* Убедитесь, что используются [новейшие средства Функций Azure](functions-develop-vs.md#check-your-tools-version).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Создание проекта приложения-функции

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio создает проект и класс в нем, содержащий стандартный код для выбранного типа функции. Атрибут метода **FunctionName** задает имя функции. Атрибут **HttpTrigger** указывает, что функция вызывается HTTP-запросом. Стандартный код отправляет HTTP-ответ, включающий в себя значение из текста или строки запроса. Вы можете добавить выходные привязки данных к функции, применив для метода соответствующие атрибуты. Дополнительные сведения см. в разделе [Триггеры и привязки](functions-dotnet-class-library.md#triggers-and-bindings) в статье [Справочник разработчика C# по функциям Azure](functions-dotnet-class-library.md).

Созданный проект функции и функцию, активируемую HTTP, можно протестировать на локальном компьютере.

## <a name="test-the-function-locally"></a>Локальное тестирование функции

Основные инструменты службы "Функции Azure" позволяют запускать проекты функций Azure на локальном компьютере разработчика. Вам будет предложено установить эти инструменты при первом запуске функции из Visual Studio.

1. Чтобы проверить работу функции, нажмите клавишу F5. Если будет предложено, примите запрос от Visual Studio на скачивание и установку основных инструментов службы Функции Azure (CLI). Кроме того, вам может понадобиться включить исключение брандмауэра, чтобы инструменты могли обрабатывать HTTP-запросы.

2. Скопируйте URL-адрес функции из выходных данных среды выполнения функций Azure.

    ![Локальная среда выполнения Azure](./media/functions-create-your-first-function-visual-studio/functions-vstools-f5.png)

3. Вставьте URL-адрес запроса в адресную строку браузера. Добавьте строку запроса `?name=<yourname>` в этот URL-адрес и выполните запрос. Ниже показан ответ в браузере на локальный запрос GET, возвращаемый функцией: 

    ![Ответ функции localhost в браузере](./media/functions-create-your-first-function-visual-studio/functions-test-local-browser.png)

4. Нажмите клавиши SHIFT+F5, чтобы остановить отладку.

Убедитесь, что функция правильно работает на локальном компьютере. Затем опубликуйте проект в Azure.

## <a name="publish-the-project-to-azure"></a>Публикация проекта в Azure

Перед публикацией проекта убедитесь, что в вашей подписке Azure есть приложения-функция. Можно создать приложение-функцию непосредственно в Visual Studio.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Тестирование функции в Azure

1. Скопируйте базовый URL-адрес приложения-функции на странице профиля публикации. Замените часть `localhost:port` URL-адреса, который использовался при локальной проверке функции новым базовым URL-адресом. Как и ранее, добавьте строку запроса `?name=<yourname>` в этот URL-адрес и выполните запрос.

    URL-адрес, вызывающий функцию, активирующую HTTP, должен быть в следующем формате:

        http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

2. Вставьте этот URL-адрес HTTP-запроса в адресную строку браузера. Ниже показан ответ в браузере на удаленный запрос GET, возвращаемый функцией:

    ![Ответ функции в браузере](./media/functions-create-your-first-function-visual-studio/functions-test-remote-browser.png)

## <a name="watch-the-video"></a>Просмотреть видео

> [!VIDEO https://www.youtube-nocookie.com/embed/DrhG-Rdm80k]

## <a name="next-steps"></a>Дополнительная информация

С помощью Visual Studio вы создали и опубликовали приложение-функцию C# с простой функцией, активируемой HTTP.

* [Узнайте, как добавлять входные и выходные привязки, которые интегрируются с другими службами](functions-develop-vs.md#add-bindings).
* [Получите дополнительные сведения о разработке функций в качестве библиотек классов .NET](functions-dotnet-class-library.md).
