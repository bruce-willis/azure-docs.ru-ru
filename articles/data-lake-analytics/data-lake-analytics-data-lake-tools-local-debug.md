---
title: Локальная отладка кода Azure Data Lake Analytics | Документация Майкрософт
description: Узнайте, как выполнять отладку заданий U-SQL на локальной рабочей станции с помощью Средств Azure Data Lake для Visual Studio.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: 181512c12c1e72e6aa8205aabd5ea22816d4c5df
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37890900"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Локальная отладка кода Azure Data Lake Analytics

Вы можете использовать Средства Azure Data Lake для Visual Studio, чтобы выполнять и отлаживать код Azure Data Lake Analytics на рабочей станции так же, как в службе Azure Data Lake.

Узнайте, [как выполнить скрипт U-SQL на локальном компьютере](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Локальная отладка скриптов и сборок C#

Можно выполнять отладку сборок C# без их отправки и регистрации в службе Azure Data Lake Analytics. Точки останова можно установить в файле кода и в ссылочном проекте C#.

### <a name="to-debug-local-code-in-code-behind-file"></a>Отладка локального кода в файле кода

1. Установите точки останова в файле кода.
2. Нажмите клавишу F5 для запуска локальной отладки сценария.

> [!NOTE]
   > Следующая процедура работает только в Visual Studio 2015. В более ранних версиях Visual Studio необходимо вручную добавить PDB-файлы.  
   >
   >

### <a name="to-debug-local-code-in-a-referenced-c-project"></a>Отладка локального кода в указанном проекте C#

1. Создайте проект сборки C# и постройте его для создания выходной библиотеки DLL.
2. Зарегистрируйте библиотеку DLL с помощью инструкции U-SQL:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Установите точки останова в коде C#.
4. Нажмите клавишу F5 для локальной отладки сценария со ссылками на библиотеку DLL C#.


## <a name="next-steps"></a>Дополнительная информация

- Более сложный запрос см. в руководстве [Анализ журналов веб-сайта с помощью службы Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Для просмотра сведений о заданиях см. статью [Использование браузера и представления для заданий Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
- Дополнительные сведения см. в статье [Использование представления выполнения вершин в инструментах Data Lake для Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
