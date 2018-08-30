---
title: Использование средств Azure Data Lake для Visual Studio Code
description: Узнайте, как с помощью средств Azure Data Lake для Visual Studio Code создавать, тестировать и выполнять скрипты U-SQL.
services: data-lake-analytics
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: b06b4c4484e3f41b64ff2d60ce558726c555a2e3
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047917"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Использование средств Azure Data Lake для Visual Studio Code

Из этой статьи вы узнаете, как создавать, тестировать и запускать скрипты U-SQL, используя средства Azure Data Lake для Visual Studio Code (VS Code). Эти сведения также представлены в следующем видеоролике:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Предварительные требования

Средства Azure Data Lake для VS Code поддерживают Windows, Linux и MacOS.  

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

Для MacOS и Linux:
- [пакет SDK для .NET Core 2.0](https://www.microsoft.com/net/download/core);
- [Mono 5.2.x](http://www.mono-project.com/download/).

## <a name="install-azure-data-lake-tools"></a>Установка средств озера данных Azure

После установки необходимых компонентов можно установить Средства Azure Data Lake для VS Code.

**Установка Средств Azure Data Lake**

1. Откройте Visual Studio Code.
2. Выберите **Расширения** в области слева. В поле поиска введите **Средства Azure Data Lake**.
3. Выберите **Установить** рядом с элементом **Средства Azure Data Lake**. 

   ![Выбранные параметры для установки средств Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Через несколько секунд кнопка **Установить** изменится на **Перезагрузить**.
4. Нажмите кнопку **Перезагрузить**, чтобы активировать расширение **Средства Azure Data Lake**.
5. Выберите **Перезагрузить окно** для подтверждения. **Средства Azure Data Lake** появятся в области **Расширения**.

 
## <a name="activate-azure-data-lake-tools"></a>Активация Средств Azure Data Lake
Чтобы активировать расширение, создайте новый или откройте имеющийся USQL-файл. 


## <a name="work-with-u-sql"></a>Работа с U-SQL

Для работы с U-SQL нужно открыть файл или папку U-SQL.

**Открытие примера скрипта**

Откройте палитру команд (CTRL+SHIFT+P) и введите **ADL: Open Sample Script**. При этом откроется другой экземпляр этого примера. Вы также можете изменить, настроить и отправить скрипт в этом экземпляре.

**Открытие папки для проекта U-SQL**

1. В Visual Studio Code выберите меню **Файл**, а затем — **Открыть папку**.
2. Укажите папку и выберите **Выбрать папку**.
3. Выберите меню **Файл**, а затем **Создать**. К проекту будет добавлен файл с именем Untitled-1.
4. Введите следующий код в файле Untitled-1:

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    Этот скрипт создает в папке/output файл departments.csv с некоторыми данными.

5. Сохраните файл в открытой папке, присвоив ему имя **myUSQL.usql**.

**Компиляция скрипта U-SQL**

1. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд. 
2. Введите **ADL: Compile Script**. Результаты компиляции отображаются в окне **Вывод**. Чтобы запустить компиляцию задания U-SQL можно также щелкнуть правой кнопкой мыши файл скрипта и выбрать **ADL: Compile Script**. Результат компиляции отобразится в области **Вывод**.
 
**Отправка скрипта U-SQL**

1. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд. 
2. Введите **ADL: Submit Job**. Также можно щелкнуть правой кнопкой мыши файл скрипта и выбрать **ADL: Submit Job**. 

После отправки задания U-SQL отобразятся журналы отправки в окне **Вывод** в VS Code. В области справа появится представление задания. Если отправка пройдет успешно, также появится URL-адрес задания. URL-адрес задания можно открыть в веб-браузере, чтобы отслеживать состояние задания в реальном времени. 

На вкладке **СВОДКА** в представлении задания отображаются сведения о задании. К основным функциям относятся повторная отправка, дублирование и открытие скрипта на портале. На вкладке **ДАННЫЕ** в представлении задания можно просмотреть входные и выходные файлы, а также файлы ресурсов. Файлы можно скачать на локальный компьютер.

![Вкладка "Сводка" в представлении задания](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Вкладка "Данные" в представлении задания](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Установка контекста по умолчанию**

Если параметры для отдельных файлов не заданы, вы можете задать контекст по умолчанию, чтобы применять эти параметры ко всем файлам скриптов.

1. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд. 
2. Введите команду **ADL: Set Default Context**. Вы также можете щелкнуть редактор скриптов правой кнопкой мыши и выбрать **ADL: Set Default Context**.
3. Выберите учетную запись, базу данных и схему. Параметры сохранятся в файле конфигурации xxx_settings.json.

   ![Учетная запись, база данных и схема, заданные в качестве контекста по умолчанию](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Установка параметров скрипта**

1. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд. 
2. Введите команду **ADL: Set Script Parameters**.
3. Откроется файл xxx_settings.json со следующими свойствами:

   - **account**. Учетная запись Azure Data Lake Analytics из подписки Azure, необходимая для компиляции и запуска заданий U-SQL. Необходимо настроить учетную запись вычислений, прежде чем компилировать и запускать задания U-SQL.
   - **database**. База данных в вашей учетной записи. По умолчанию используется база данных **master**.
   - **schema**. Схема в базе данных. Значение по умолчанию — **dbo**.
   - **optionalSettings**:
        - **priority**. Значение приоритета в диапазоне от 1 до 1000, где 1 — наивысший приоритет. По умолчанию используется значение **1000**.
        - **degreeOfParallelism**. Значение степени параллелизма в диапазоне от 1 до 150. Значением по умолчанию является максимально допустимый коэффициент параллелизма в учетной записи Azure Data Lake Analytics. 

   ![Содержимое JSON-файла](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
> [!NOTE] 
> Если контекст по умолчанию не задан, после сохранения конфигурации сведения об учетной записи, базе данных и схеме появятся в строке состояния в левом нижнем углу соответствующего USQL-файла.

**Установка игнорирования Git**

1. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд. 
2. Введите **ADL: Set Git Ignore**.

   - Если в вашей рабочей папке VS Code нет файла **.gitIgnore**, в ней будет создан файл с именем **.gitIgnore**. По умолчанию в файл будут добавлены четыре элемента (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**). По мере необходимости можно вносить другие изменения.
   - Если в рабочей папке VS Code уже есть файл **.gitIgnore**, то в файл **.gitIgnore** будут добавлены четыре элемента (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**), если они не были добавлены ранее.

   ![Элементы в файле .gitIgnore](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)


## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Работа с файлами кода программной части: С#, Python и R

Средства Azure Data Lake поддерживают разные пользовательские коды. Инструкции см. в статье [Разработка U-SQL с помощью Python, R, и C Sharp для Azure Data Lake Analytics в VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Работа со сборками

Дополнительные сведения о разработке сборок см. в статье [Разработка сборок U-SQL для заданий Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).

Средства Data Lake можно использовать для регистрации сборки пользовательского кода в каталоге Data Lake Analytics.

**Регистрация сборки**

Зарегистрировать сборку можно с помощью команды **ADL: Register Assembly** или **ADL: Register Assembly (Advanced)**.

**Для регистрации с помощью команды ADL: Register Assembly**
1.  Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд.
2.  Введите **ADL: Register Assembly**. 
3.  Укажите локальный путь к сборке. 
4.  Выберите учетную запись Data Lake Analytics.
5.  Выберите базу данных.

В браузере откроется портал и начнется процесс регистрации сборки.  

Более удобный способ вызова команды **ADL: Register Assembly** — щелкнуть правой кнопкой мыши DLL-файл в обозревателе. 

**Регистрация с помощью команды ADL: Register Assembly (Advanced)**
1.  Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд.
2.  Введите команду **ADL: Register Assembly (Advanced)**. 
3.  Укажите локальный путь к сборке. 
4.  Отобразится JSON-файл. Просмотрите и при необходимости измените зависимости сборки и параметры ресурсов. Инструкции отображаются в окне **Вывод**. Чтобы перейти к регистрации сборки, сохраните (CTRL+S) JSON-файл.

    ![JSON-файл с зависимостями сборки и параметрами ресурсов](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
>[!NOTE]
>- Средства Azure Data Lake автоматически определяют, есть ли у библиотеки DLL зависимости сборки. Обнаруженные зависимости отображаются в JSON-файле. 
>- Ресурсы DLL (например, TXT, PNG и CSV) можно отправлять в ходе регистрации сборки. 

Еще один способ запуска команды **ADL: Register Assembly (Advanced)** — щелкнуть правой кнопкой мыши DLL-файл в проводнике. 

В приведенном ниже коде U-SQL показано, как вызвать сборку. В этом примере имя сборки — *test*.


        REFERENCE ASSEMBLY [test];

        @a = 
            EXTRACT 
                Iid int,
            Starts DateTime,
            Region string,
            Query string,
            DwellTime int,
            Results string,
            ClickedUrls string 
            FROM @"Sample/SearchLog.txt" 
            USING Extractors.Tsv();

        @d =
            SELECT DISTINCT Region 
            FROM @a;

        @d1 = 
            PROCESS @d
            PRODUCE 
                Region string,
            Mkt string
            USING new USQLApplication_codebehind.MyProcessor();

        OUTPUT @d1 
            TO @"Sample/SearchLogtest.txt" 
            USING Outputters.Tsv();


## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Локальные запуск и отладка U-SQL для пользователей Windows
Локальное выполнение U-SQL тестирует локальные данные и проверяет скрипт локально перед публикацией кода в Data Lake Analytics. С помощью функции локальной отладки можно завершить следующие задачи перед отправкой кода в Data Lake Analytics: 
- Отладка кода программной части C#. 
- Пошаговая отладка кода. 
- Проверка скрипта локально.

Инструкции по локальному выполнению и отладке см. в статье [Локальный запуск и локальная отладка U-SQL в Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).


## <a name="connect-to-azure"></a>Подключение к Azure

Перед компиляцией и выполнением скрипта U-SQL в Data Lake Analytics необходимо подключиться к учетной записи Azure.

<b id="sign-in-by-command">Подключение к Azure с помощью команды</b>

1.  Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд. 
2.  Введите текст **ADL: Login**. Сведения для входа появятся в правом нижнем углу.

    ![Ввод команды входа](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![Уведомление о входе и аутентификации](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  Выберите **Копировать и открыть**, чтобы открыть [веб-страницу входа](https://aka.ms/devicelogin). Вставьте код в поле, а затем нажмите **Продолжить**.

    ![Веб-страница входа](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  
     
4.  Следуйте инструкциям, предоставленным на веб-странице входа. Когда соединение установлено, имя учетной записи Azure отображается в левом нижнем углу окна VS Code. 

> [!NOTE] 
>- В следующий раз средства Data Lake автоматически выполнят вход, если вы не выйдете из системы.
>- Если для учетной записи используется двухфакторная проверка подлинности, мы советуем использовать проверку подлинности через телефон, а не PIN-код.


Для выхода введите команду **ADL: Logout**.

**Подключение к Azure из обозревателя**

Разверните узел **AZURE DATALAKE**, нажмите **Войти в Azure**, а затем выполните действия 3 и 4 из раздела [Подключение к Azure с помощью команды](#sign-in-by-command).

![Выбор команды "Войти в Azure" в обозревателе](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Выйти из обозревателя нельзя. Чтобы выйти, следуйте инструкциями из раздела [Подключение к Azure с помощью команды](#sign-in-by-command).


## <a name="create-an-extraction-script"></a>Создание скрипта извлечения 
Вы можете создать скрипт для извлечения CSV-, TSV- и TXT-файлов с помощью команды **ADL: Create EXTRACT Script** или обозревателя Azure Data Lake.

**Создание скрипта извлечения с помощью команды**

1. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд, и введите **ADL: Create EXTRACT Script**.
2. Укажите полный путь к файлу службы хранилища Azure и нажмите клавишу ВВОД.
3. Выберите одну учетную запись.
4. Выберите разделитель, чтобы извлечь ТХТ-файл. 

![Процесс создания скрипта извлечения](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Скрипт извлечения создается на основе ваших записей. Для скрипта, который не может обнаружить столбцы, выберите один из этих двух параметров. В противном случае будет создан только один скрипт.

![Результат создания скрипта извлечения](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**Создание скрипта извлечения из обозревателя**

Еще один способ создать скрипт извлечения — с помощью контекстного меню CSV-, TSV- или TXT-файла в Azure Data Lake Store или хранилище BLOB-объектов Azure.

![Команда "Создать скрипт EXTRACT" в контекстном меню](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Интеграция с Azure Data Lake Analytics с помощью команды

Вы можете использовать ресурсы Azure Data Lake Analytics, чтобы отобразить учетные записи, получить доступ к метаданным и просмотреть задания аналитики. 

**Получение списка учетных записей Azure Data Lake Analytics в подписке Azure**

1. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд.
2. Введите команду **ADL: List Accounts**. Учетные записи отображаются в области **Вывода**.

**Доступ к метаданным Azure Data Lake Analytics**

1.  Нажмите клавиши CTRL+SHIFT+P, а затем введите **ADL: List Tables**.
2.  Выберите одну из учетных записей Data Lake Analytics.
3.  Выберите одну из баз данных Data Lake Analytics.
4.  Выберите одну из схем. Можно просмотреть список таблиц.

**Просмотр заданий Azure Data Lake Analytics**
1.  Откройте палитру команд (CTRL+SHIFT+P) и выберите **ADL: Show Jobs**. 
2.  Выберите Data Lake Analytics или локальную учетную запись. 
3.  Подождите, пока появится список заданий для учетной записи.
4.  Выберите задание из списка. В правой области средств Data Lake откроется представление задания, а в выходных данных VS Code будут показаны некоторые сведения.

    ![Список заданий](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Интеграция с Azure Data Lake Store при помощи команды

С помощью команд для Azure Data Lake Store можно выполнять следующие действия:
 - [Просмотр ресурсов Azure Data Lake Store](#list-the-storage-path) 
 - [Просмотр файла Azure Data Lake Store](#preview-the-storage-file) 
 - [Отправка файла непосредственно в Azure Data Lake Store в VS Code](#upload-file-or-folder)
 - [Скачивание файла непосредственно из Azure Data Lake Store в VS Code](#download-file)

### <a name="list-the-storage-path"></a>Вывод пути к хранилищу 

**Вывод пути к хранилищу через палитру команд**

1. Щелкните редактор сценариев правой кнопкой мыши и выберите **ADL: List Path** (ADL: путь к списку).
2. Выберите папку из списка либо выберите **Enter a path** или **Browse from root path**. В этом примере выбран вариант **Enter a path**. 
3. Выберите учетную запись Data Lake Analytics.
4. Перейдите к папке хранилища или укажите путь к ней (например, /output/).  

В палитре команд выводятся сведения о пути на основе ваших записей.

![Результаты из пути к хранилищу](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Более удобный способ вывода относительного пути — через контекстное меню.

**Вывод пути к хранилищу через контекстное меню**

Щелкните строку пути правой кнопкой мыши и выберите **List Path**.

![Пункт "List Path" в контекстном меню](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>Предварительный просмотр файла хранилища

1. Щелкните редактор сценариев правой кнопкой мыши и выберите **ADL: Preview File** (ADL: предварительный просмотр файла).
2. Выберите учетную запись Data Lake Analytics. 
3. Введите путь к файлу службы хранилища Azure (например, /output/SearchLog.txt). 

В VS Code откроется файл.

![Шаги и результат просмотра файла хранилища](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Просмотреть файл также можно через контекстное меню полного или относительного пути к файлу в редакторе скриптов. 

### <a name="upload-a-file-or-folder"></a>Отправка файла или папки

1. Щелкните редактор сценариев правой кнопкой мыши и выберите **Upload File** (Отправить файл) или **Upload Folder** (Отправить папку).
2. Выберите один или несколько файлов (если выбрана команда **Upload File**) или целую папку (если выбрана команда **Upload Folder**). Затем нажмите кнопку **Отправить**. 
3. Выберите папку хранилища из списка либо выберите **Enter a path** или **Browse from root path**. В этом примере выбран вариант **Enter a path**. 
4. Выберите учетную запись Data Lake Analytics. 
5. Перейдите к папке хранилища или укажите путь к ней (например, /output/). 
6. Нажмите **Выбрать текущую папку**, чтобы указать цель отправки.

![Действия и результаты отправки файла или папки](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    

Отправить файлы в хранилище также можно через контекстное меню полного или относительного пути к файлу в редакторе скриптов.

Вы можете [отслеживать состояние отправки](#check-storage-tasks-status).


### <a name="download-a-file"></a>скачать файл; 
Скачать файл можно с помощью команды **ADL: Download File** или **ADL: Download File (Advanced)**.

**Скачивание файла с помощью команды ADL: Download File (Advanced)**
1. Щелкните правой кнопкой мыши редактор скриптов, а затем выберите **Download File (Advanced)** (Скачать файл (Дополнительно)).
2. В VS Code отобразится JSON-файл. Вы можете ввести несколько путей к файлам, чтобы загрузить несколько файлов одновременно. Инструкции отображаются в окне **Вывод**. Чтобы продолжить скачивание файлов, сохраните (CTRL+S) JSON-файл.

    ![JSON-файл с путями для скачивания файлов](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

В окне **Вывод** отобразится состояние скачивания файла.

![Окно вывода с состоянием скачивания](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Вы можете [отслеживать состояние скачивания](#check-storage-tasks-status).

**Скачивание файла с помощью команды ADL: Download File**

1. Щелкните редактор сценариев правой кнопкой мыши, выберите **Download File**, а затем выберите целевую папку в диалоговом окне **Выбор папки**.
2. Выберите папку из списка либо выберите **Enter a path** или **Browse from root path**. В этом примере выбран вариант **Enter a path**. 
3. Выберите учетную запись Data Lake Analytics. 
4. Перейдите к папке хранилища или введите путь к ней (например, /output/) и выберите файл для скачивания.

![Действия и результат скачивания файла](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

Скачать файлы хранилища также можно через контекстное меню полного или относительного пути к файлу в редакторе скриптов.

Вы можете [отслеживать состояние скачивания](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Проверка состояния задач хранилища
В строке состояния отображается состояние отправки и скачивания. Выберите строку состояния, и состояние появится на вкладке **ВЫВОД**.

![Строка состояния и выходные данные](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Интеграция с Azure Data Lake Analytics из обозревателя

После входа все подписки для учетной записи Azure будут перечислены в области слева, в разделе **AZURE DATALAKE**. 

![Обозреватель Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Навигация по метаданным Data Lake Analytics

Разверните узел своей подписки Azure. В узле **Базы данных U-SQL** вы можете просматривать свою базу данных U-SQL, в том числе такие папки, как **Схемы**, **Учетные данные**, **Сборки**, **Таблицы** и **Индекс**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Управление объектами метаданных Data Lake Analytics

Разверните узел **Базы данных U-SQL**. Вы можете создать базу данных, схему, таблицу, тип таблицы, индекс или статистику, щелкнув соответствующий узел правой кнопкой мыши и выбрав **Скрипт для создания** в контекстном меню. На открывшейся странице отредактируйте скрипт в соответствии со своими потребностями. Затем отправьте задание, щелкнув его правой кнопкой мыши и выбрав команду **ADL: Submit Job**. 

Завершив создание элемента, щелкните узел правой кнопкой мыши и выберите **Обновить**, чтобы элемент появился в списке. Вы также можете удалить элемент, открыв правой кнопкой мыши контекстное меню и выбрав **Удалить**.

![Команда "Скрипт для создания" в контекстном меню обозревателя Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Страница скрипта для нового элемента](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Регистрация сборки Data Lake Analytics

Вы можете зарегистрировать сборку в соответствующей базе данных, щелкнув правой кнопкой мыши узел **Assemblies** и выбрав **Register assembly**.

![Команда "Зарегистрировать сборку" в контекстном меню узла Assemblies](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Интеграция с Azure Data Lake Store из обозревателя

Перейдите в **Data Lake Store**:

- Вы можете щелкнуть узел папки правой кнопкой мыши, а затем использовать команды **Обновить**, **Удалить**, **Отправить**, **Загрузить папку**, **Копировать относительный путь** и **Копировать полный путь** в контекстном меню.

   ![Команды контекстного меню для узла папки в обозревателе Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Вы можете щелкнуть узел файла правой кнопкой мыши, а затем использовать команды **Просмотреть**, **Скачать**, **Удалить**, **Создать скрипт EXTRACT** (доступна только для CSV-, TSV- и TXT-файлов), **Копировать относительный путь** и **Копировать полный путь** в контекстном меню.

   ![Команды контекстного меню для узла файла в обозревателе Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Интеграция с хранилищем BLOB-объектов Azure из обозревателя

Перейдите в хранилище BLOB-объектов:

- Вы можете щелкнуть правой кнопкой мыши узел контейнера BLOB-объектов, а затем использовать команды **Refresh**, **Delete Blob Container** и **Upload Blob** в контекстном меню.

   ![Команды контекстного меню для узла контейнера в хранилище BLOB-объектов](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Вы можете щелкнуть правой кнопкой мыши узел папки, а затем использовать команды **Обновить** и **Отправить BLOB-объект** в контекстном меню.

   ![Команды контекстного меню для узла папки в хранилище BLOB-объектов](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Вы можете щелкнуть узел файла правой кнопкой мыши, а затем использовать команды **Просмотреть/Изменить**, **Скачать**, **Удалить**, **Создать сценарий EXTRACT** (доступна только для CSV-, TSV- и TXT-файлов), **Копировать относительный путь** и **Копировать полный путь** в контекстном меню.

    ![Команды контекстного меню для узла файла в хранилище BLOB-объектов](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Открытие обозревателя Data Lake на портале
1. Нажмите клавиши CTRL+SHIFT+P, чтобы открыть палитру команд.
2. Введите команду **Open Web Azure Storage Explorer** либо щелкните правой кнопкой мыши относительный или полный путь в редакторе скриптов, а затем выберите пункт **Open Web Azure Storage Explorer**.
3. Выберите учетную запись Data Lake Analytics.

В средствах Data Lake откроется путь к хранилищу Azure на портале Azure. Можно найти путь и предварительно просмотреть файл в Интернете.

## <a name="additional-features"></a>Дополнительные функции

Средства Data Lake для VS Code поддерживают следующие функции:

-   **Автозаполнение IntelliSense**. Предлагаемые варианты отображаются во всплывающих окнах вокруг ключевых слов, методов и переменных. Разные значки обозначают разные объекты:

    - тип данных Scala;
    - сложный тип данных;
    - встроенные пользовательские типы;
    - коллекции и классы .NET;
    - выражения C#;
    - встроенные функции и объекты C#, определяемые пользователем; 
    - функции U-SQL;
    - функции U-SQL для работы с окнами;
 
    ![типы объектов IntelliSense.](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   **Автозаполнение IntelliSense для метаданных Data Lake Analytics**. Средства Data Lake загружают сведения о метаданных Data Lake Analytics локально. Функция IntelliSense автоматически заполняет объекты метаданными Data Lake Analytics. К этим объектам относятся база данных, схема, таблица, представление, функция с табличным значением, процедуры и сборки C#.
 
    ![Метаданные IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   **Маркер ошибки IntelliSense**. Средства Data Lake подчеркивают ошибки при редактировании файлов U-SQL и C#. 
-   **Выделение синтаксиса**. Средства Data Lake используют цвета для выделения переменных, ключевых слов, типов данных и функций. 

    ![Синтаксис с различными цветами](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Рекомендуем обновить средства Azure Data Lake для Visual Studio до версии 2.3.3000.4 или выше. Предыдущие версии являются устаревшими и недоступными для скачивания.  
   
## <a name="next-steps"></a>Дополнительная информация
- [Разработка U-SQL с помощью Python, R, и C Sharp для Azure Data Lake Analytics в VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Локальный запуск и локальная отладка U-SQL в Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Начало работы с Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-get-started-portal.md)
- [Разработка скриптов U-SQL с помощью средств Data Lake для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
