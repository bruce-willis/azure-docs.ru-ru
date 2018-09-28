---
title: Разработка пользовательских функций .NET Standard для заданий Edge в Azure Stream Analytics (предварительная версия)
description: Вы узнаете, как написать пользовательские функции на C# для заданий Edge в Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9aa61e95eb808c38646fa9b8cefd4004f5477ee6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974669"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-edge-jobs-preview"></a>Разработка пользовательских функций .NET Standard для заданий Edge в Azure Stream Analytics (предварительная версия)

Azure Stream Analytics предлагает язык запросов, аналогичный SQL, для выполнения преобразований и вычислений в потоках данных событий. Существует множество встроенных функций, но некоторые сложные сценарии требуют дополнительных возможностей. С помощью пользовательских функций .NET Standard вы можете вызывать свои функции, написанные на любом языке .NET Standard (C#, F# и т. д.) для расширения языка запросов Stream Analytics. Пользовательские функции позволяют выполнять сложные математические вычисления, импортировать пользовательские модели ML с помощью ML.NET и использовать пользовательскую логику заполнения отсутствующих данных. Пользовательские функции для заданий Edge в Stream Analytics сейчас находятся на этапе предварительной версии, и их не следует использовать в рабочих нагрузках.

## <a name="overview"></a>Обзор
Инструменты Visual Studio для Azure Stream Analytics позволяют легко создавать пользовательские функции, локально тестировать задания (даже в автономном режиме) и публиковать задания Stream Analytics в Azure. После публикации в Azure вы можете развернуть задание на устройствах Интернета вещей с помощью центра Интернета вещей.

Существует три способа реализации пользовательских функций:

* файлы CodeBehind в проекте ASA;
* пользовательская функция из локального проекта;
* существующий пакет из учетной записи хранения Azure.

## <a name="package-path"></a>Путь к пакету

Формат любых пакетов пользовательских функций имеет путь `/UserCustomCode/CLR/*`. Библиотеки динамической компоновки (DLL) и ресурсы копируются в папку `/UserCustomCode/CLR/*`, которая позволяет изолировать библиотеки DLL пользователей от системы и библиотек DLL Azure Stream Analytics.

## <a name="supported-types-and-mapping"></a>Поддерживаемые типы и сопоставление

|**Тип пользовательской функции (C#)**  |**Тип Azure Stream Analytics**  |
|---------|---------|
|Bool  |  bigint   |
|int32  |  bigint   |
|int64  |  bigint   |
|float;  |  Double   |
|Double  |  Double   |
|строка  |  nvarchar(max)   |
|dateTime  |  dateTime   |
|struct  |  IRecord   |
|object  |  IRecord   |
|Массив,  |  IArray   |
|dictionary<string, object>  |  IRecord   |

## <a name="codebehind"></a>CodeBehind
Вы можете написать пользовательские функции в CodeBehind **Script.asql**. Инструменты Visual Studio автоматически скомпилируют файл CodeBehind в файл сборки. При отправке задания в Azure сборки будут упакованы в ZIP-файл и отправлены в учетную запись хранения. Чтобы узнать, как написать пользовательскую функцию на C# с помощью CodeBehind, воспользуйтесь руководством [Пользовательские функции на C# для заданий Edge в Stream Analytics](stream-analytics-edge-csharp-udf.md). 

## <a name="local-project"></a>Локальный проект
Пользовательские функции можно написать в сборке, на которую позже будет ссылаться запрос Azure Stream Analytics. Это рекомендуется для сложных функций, требующих всех возможностей языка .NET Standard помимо языка выражений, таких как процедурная логика и рекурсия. Пользовательские функции из локального проекта можно также использовать, если нужно предоставить логику функций в нескольких запросах Azure Stream Analytics. Добавление пользовательских функций в локальный проект позволяет выполнить локальную отладку и тестирование функций из Visual Studio.

Создание ссылки на локальный проект

1. Создайте библиотеку классов в решении.
2. Напишите код в классе. Обратите внимание, что классы должны быть определены как *public*, а объекты — как *static public*. 
3. Выполните сборку проекта.
4. Сошлитесь на новый класс в проекте Azure Stream Analytics.
5. Добавьте новую функцию в проект Azure Stream Analytics.
6. Настройте путь к сборке в файле конфигурации задания `EdgeJobConfig.json`.
7. Выполните повторную сборку проекта функций и проекта Azure Stream Analytics.  

### <a name="example"></a>Пример

В этом примере **UDFTest** — проект библиотеки классов C#, а **ASAEdgeUDFDemo** — проект Edge в Azure Stream Analytics, который будет ссылаться на **UDFTest**.

![Проект IoT Edge в Azure Stream Analytics в Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Создайте проект на C#, который позволит добавить ссылку на пользовательскую функцию C# из запроса Azure Stream Analytics.
    
   ![Создание проекта IoT Edge в Azure Stream Analytics в Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Добавьте ссылку на проект C# в проекте ASA Edge. Щелкните правой кнопкой мыши узел "Ссылки" и выберите "Добавить ссылку".

   ![Добавление ссылки на проект C# в Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Выберите имя проекта C# в списке. 
    
   ![Выбор имени проекта C# в списке ссылок](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. В списке **Ссылки** в **обозревателе решений** должен отобразиться проект **UDFTest**.

   ![Просмотр ссылки на пользовательскую функцию в обозревателе решений Edge в Azure Stream Analytics](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Щелкните правой кнопкой мыши папку **Функции** и выберите **Новый элемент**.

   ![Добавление нового элемента в функции в решении Edge в Azure Stream Analytics в Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Добавьте функцию C# **SquareFunction.json** в проект Azure Stream Analytics.

   ![Выбор функции C# в списке элементов Edge в Stream Analytics в Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Дважды щелкните функцию в **обозревателе решений**, чтобы открыть диалоговое окно настройки.

   ![Настройка функции C# в Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. В окне настройки функции C# выберите **Загрузить из ссылки на проект ASA** и щелкните имена связанных сборки, класса и метода в раскрывающемся списке. Для ссылки на методы, типы и функции в запросе Edge в Stream Analytics классы должны быть определены как *public*, а объекты — как *static public*.

   ![Настройка функции C# Stream Analytics](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Существующие пакеты

Вы можете создать пользовательские функции .NET Standard в любой IDE на ваш выбор и вызвать их из запроса Azure Stream Analytics. Сначала скомпилируйте код и упакуйте все библиотеки DLL. Формат пакета имеет путь `/UserCustomCode/CLR/*`. После отправьте `UserCustomCode.zip` в корень контейнера в учетной записи хранения Azure.

После отправки ZIP-файлов пакетов в учетную запись хранения Azure вы можете использовать функции в запросах Azure Stream Analytics. Все, что нужно сделать, — включить сведения о хранилище в конфигурацию задания Edge в Stream Analytics. Протестировать функцию локально с помощью этого параметра невозможно, так как инструменты Visual Studio не смогут скачать ваш пакет. Путь к пакету анализируется непосредственно в службе. 

Настройка пути к сборке в файле конфигурации задания "EdgeJobConfig.json"

Разверните раздел **Настройка пользовательского кода** и укажите следующие предложенные значения в конфигурации:

 |**Параметр**  |**Рекомендуемое значение**  |
 |---------|---------|
 |Источник сборки  |  Ссылка на локальный проект или код программной части   |
 |Ресурс  |  Выберите данные текущей учетной записи   |
 |Подписка  |  Выберите свою подписку.   |
 |Учетная запись хранения  |  Выберите учетную запись хранения.   |
 |Контейнер  |  Выберите контейнер, созданный в учетной записи хранения.   |

    ![Azure Stream Analytics Edge job configuration in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-job-config.png)

## <a name="limitations"></a>Ограничения
Предварительная версия пользовательских функций сейчас имеет следующие ограничения.

* Языки .NET Standard можно использовать только для Azure Stream Analytics на IoT Edge. Для облачных заданий можно написать пользовательские функции JavaScript. Дополнительные сведения см. в руководстве [Пользовательские функции JavaScript Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md).

* Пользовательские функции .NET Standard можно создать только в Visual Studio и опубликовать в Azure. Версии пользовательских функций .NET Standard только для чтения можно просмотреть в разделе **Функции** на портале Azure. Создание функций .NET Standard не поддерживается на портале Azure.

* Редактор запросов на портале Azure отображает ошибку при использовании пользовательской функции .NET Standard на портале. 

* Так как пользовательский код имеет общий контекст с подсистемой Azure Stream Analytics, он не может содержать ссылки на объекты с конфликтующим значением namespace/dll_name в коде Azure Stream Analytics. Например, ссылки на *Newtonsoft Json* запрещены.

## <a name="next-steps"></a>Дополнительная информация

* [Руководство. Написание пользовательской функции C# для задания Edge в Azure Stream Analytics (предварительная версия)](stream-analytics-edge-csharp-udf.md)
* [Руководство. Пользовательские функции JavaScript Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Просмотр заданий Azure Stream Analytics с помощью Visual Studio](stream-analytics-vs-tools.md)
