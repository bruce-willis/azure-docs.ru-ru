---
title: Расширение Azure Terraform для VS Code | Документация Майкрософт
description: Сведения о том, как установить и использовать расширение Terraform в Visual Studio Code.
keywords: terraform, devops, virtual machine, azure
author: v-mavick
ms.author: v-mavick
ms.date: 08/14/2018
ms.topic: article
ms.prod: vs-code
ms.openlocfilehash: 0c88879faae100372055479ad4edb8c36d8f557d
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190995"
---
# <a name="azure-terraform-vs-code-extension"></a>Расширение Azure Terraform для VS Code

Расширение Microsoft Azure Terraform для Visual Studio Code (VS Code) помогает повысить производительность разработчиков при создании, тестировании и использовании Terraform в Azure. Это расширение предоставляет поддержку команд Terraform, визуализацию графиков ресурсов и интеграцию с Azure Cloud Shell из Visual Studio Code.

## <a name="what-you-do"></a>Что нужно сделать

- Установите на локальном компьютере исполняемый файл HashiCorp Terraform с открытым исходным кодом.
- Установите расширение VS Code Terraform для Azure в локальной системе VS Code.

## <a name="what-you-learn"></a>Что вы узнаете

Из этого руководства вы узнаете:

- Как Terraform автоматизирует и упрощает процесс подготовки служб Azure.
- Как установить и использовать расширение VS Code Microsoft Terraform для служб Azure.
- Как с помощью VS Code проектировать, создавать и выполнять планы Terraform.

## <a name="what-you-need"></a>Необходимые элементы

- Компьютер под управлением Windows 10, Linux или macOS 10.10 (или более поздней версии).
- [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US).
- Активная подписка Azure. Вы можете активировать [бесплатную 30-дневную пробную учетную запись Microsoft Azure](https://azure.microsoft.com/free/).
- Установка средства [Terraform](https://www.terraform.io/) с открытым кодом на локальном компьютере.
  
## <a name="prepare-your-dev-environment"></a>Подготовка среды разработки

### <a name="install-git"></a>Установка Git

Чтобы выполнить упражнения из этой статьи, вам нужно [установить Git](https://git-scm.com/).

### <a name="install-hashicorp-terraform"></a>Установка HashiCorp Terraform

Следуйте инструкциям по [установке Terraform](https://www.terraform.io/intro/getting-started/install.html) на странице HashiCorp:

- Скачивание Terraform
- Установка Terraform
- Проверка правильности установки Terraform

>[!Tip]
>Обязательно выполните инструкции по настройке системной переменной PATH.

### <a name="install-nodejs"></a>Установка Node.js

Чтобы использовать Terraform в Cloud Shell, вам нужно [установить Node.js](https://nodejs.org/) версии 6.0 или более поздней.

>[!NOTE]
>Чтобы проверить установку Node.js, откройте окно терминала и введите команду `node -v`.

### <a name="install-graphviz"></a>Установка GraphViz

Чтобы использовать функцию визуализации в Terraform, вам нужно [установить GraphViz](http://graphviz.org/).

>[!NOTE]
>Чтобы проверить установку GraphViz, откройте окно терминала и введите команду `dot -V`.

### <a name="install-the-azure-terraform-vs-code-extension"></a>Установите расширение Azure Terraform для VS Code.

1. Запустите VS Code.
2. Щелкните значок **Расширения**.

    ![Кнопка "Расширения"](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

3. Используйте текстовое поле **Поиск расширений в Marketplace** для поиска расширения Azure Terraform:

    ![Поиск расширений VS Code в Marketplace](media/terraform-vscode-extension/tf-search-extensions.png)

4. Щелкните **Install**(Установить).

    >[!NOTE]
    >Когда вы щелкнете **Установить** рядом с расширением Azure Terraform, VS Code автоматически установит расширение Azure Account. Azure Account — это файл зависимостей для расширения Azure Terraform, который используется для аутентификации подписки Azure и расширений, имеющих отношение к Azure.

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>Проверка наличия расширения Terraform в Visual Studio Code

1. Щелкните значок "Расширения".
2. В текстовое поле поиска введите `@installed`.

    ![Установленные расширения](media/terraform-vscode-extension/tf-installed-extensions.png)

Расширение Azure Terraform появится в списке установленных расширений.

![Установленные расширения Terraform](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

Теперь вы можете выполнять из VS Code все поддерживаемые команды Terraform в среде Cloud Shell.

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>Упражнение 1. Пошаговое руководство по основным командам Terraform

В этом упражнении вы создадите и выполните базовый файл конфигурации Terraform, который подготавливает новую группу ресурсов Azure.

### <a name="prepare-a-test-plan-file"></a>Подготовка файла для плана тестирования

1. В строке меню VS Code выберите **Файл > Создать файл**.
2. Перейдите к [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#) и скопируйте код из блока **Example Usage**:
3. Вставьте скопированный код в файл, созданный в VS Code.

    ![Вставка кода блока Example Usage](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >Вы можете изменить значение **Имя** для группы ресурсов, но это имя должно быть уникальным в пределах подписки Azure.

4. В строке меню выберите **Файл > Сохранить как**.
5. В диалоговом окне **Сохранение как** перейдите в любую удобную папку и щелкните **Создать папку**. (Вместо имени *Новая папка* присвойте этой папке более понятное имя.)

    >[!NOTE]
    >В нашем примере папка получает имя TERRAFORM-TEST-PLAN.

6. Убедитесь, что новая папка выделена (выбрана) и щелкните **Открыть**.
7. В диалоговом окне **Сохранение как** измените имя файла на *main.tf*.

    ![Сохранение файла с именем main.tf](media/terraform-vscode-extension/tf-save-as-main.png)

8. Выберите команду **Сохранить**.
- В строке меню выберите **Файл > Открыть папку**. Найдите и выберите папку, которую вы только что создали.

### <a name="run-terraform-init-command"></a>Выполнение команды Terraform *init*

1. Запустите Visual Studio Code.
2. В строке меню VS Code выберите **Файл > Открыть папку**, а затем найдите и выберите созданный файл *main.tf*.

    ![Файл main.tf](media/terraform-vscode-extension/tf-main-tf.png)

3. В строке меню выберите **Просмотр > Палитра команд > Azure Terraform: Init**.
4. Через несколько секунд появится *запрос на открытие Cloud Shell*. Щелкните **ОК**.

    ![Запрос на открытие Cloud Shell](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

5. При первом запуске Cloud Shell из новой папки вам будет предложено настроить веб-приложение. Щелкните **Открыть**.

    ![Первый запуск Cloud Shell](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

6. Откроется страница приветствия Azure Cloud Shell. Выберите Bash или PowerShell.

    ![Страница приветствия Azure Cloud Shell](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >В нашем примере выбран вариант Bash (Linux).

7. Если вы еще не настроили учетную запись хранения Azure, откроется следующий экран. Щелкните **Создать хранилище**.

    ![Подключенное хранилище отсутствует](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. Azure Cloud Shell запускается в оболочке, которую вы выбрали ранее, и отображает сведения о только что созданном облачном диске.

    ![Ваш облачный диск успешно создан](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

9. Теперь можно выйти из Cloud Shell.
10. В строке меню выберите **Просмотр** > **Палитра команд** > **Azure Terraform: Init**.

    ![Terraform успешно инициализирован](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>Визуализация плана

Ранее с помощью этого руководства вы установили GraphViz. Terraform с помощью GraphViz может создать визуальное представление плана выполнения или конфигурации. Расширение Azure Terraform для VS Code реализует эту функцию через команду *визуализации*.

- В **строке меню** выберите **Просмотр > Палитра команд > Azure Terraform: Visualize**.

    ![Визуализация плана](media/terraform-vscode-extension/tf-graph.png)

### <a name="run-terraform-plan-command"></a>Выполнение команды Terraform *plan*

Команда Terraform *plan* позволяет проверить, будет ли план выполнения с некоторым набором изменений выполнять ожидаемые действия.

>[!NOTE]
>Команда Terraform *plan* не изменяет реальные ресурсы Azure. Чтобы внести изменения в план, используется команда Terraform *apply*.

- В строке меню выберите **Просмотр** > **Палитра команд** > **Azure Terraform: plan**.

    ![План Terraform](media/terraform-vscode-extension/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>Выполнение команды Terraform *apply*

Если вы полностью довольны результатами выполнения команды Terraform *plan*, примените команду *apply*.

1. В строке меню выберите **Просмотр** > **Палитра команд** > **Azure Terraform: apply**.

    ![Применение Terraform](media/terraform-vscode-extension/tf-terraform-apply.png)

2. Введите *yes*.

    ![Согласие на выполнение команды Terraform apply](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>Проверка выполнения команды Terraform plan

Чтобы проверить создание группы ресурсов Azure, сделайте следующее:

1. Перейдите на портал Azure.
2. В области навигации слева выберите **Группа ресурсов**.

    ![Проверка нового ресурса](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

Новая группа ресурсов должна быть указана в столбце **Имя**.

>[!NOTE]
>Вам можете не закрывать окно портала Azure — оно нам понадобится на следующем шаге.

### <a name="run-terraform-destroy-command"></a>Выполнение команды Terraform *destroy*

1. В строке меню выберите **Просмотр** > **Палитра команд** > **Azure Terraform: destroy**.

    ![Команда Terraform destroy](media/terraform-vscode-extension/tf-terraform-destroy.png)

2. Введите *yes*.

    ![Согласие на выполнение команды Terraform destroy](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>Проверка удаления группы ресурсов

Чтобы проверить удаление группы ресурсов в Terraform, сделайте следующее:

1. Щелкните **Обновить** на странице *Группы ресурсов* на портале Azure.
2. Удаленная группа ресурсов должна исчезнуть из этого списка.

    ![Проверка удаления группы ресурсов](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>Упражнение 2. Модуль Terraform *compute*

В этом упражнении вы узнаете, как загрузить модуль Terraform *compute* в среду VS Code.

### <a name="clone-the-terraform-azurerm-compute-module"></a>Клонирование модуля terraform-azurerm-compute

1. Используйте [эту ссылку](https://github.com/Azure/terraform-azurerm-compute), чтобы открыть страниц модуля Terraform Azure Rm Compute на GitHub.
2. Выберите **Clone or download** (Клонировать или скачать).

    ![Клонирование или скачивание](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >В нашем примере папке присвоено имя *terraform-azurerm-compute*.

### <a name="open-the-folder-in-vs-code"></a>Открытие папки в VS Code

1. Запустите Visual Studio Code.
2. На **панели меню** выберите **Файл > Открыть папку**, а затем найдите и выберите созданную на предыдущем шаге папку.

    ![Папка terraform-azurerm-compute](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>Инициализация Terraform

Прежде чем выполнять команды Terraform в VS Code, нужно скачать подключаемые модули для двух поставщиков Azure: random и azurerm.

1. На панели терминала в интегрированной среде разработки VS Code введите следующую команду: `terraform init`.

    ![Команда Terraform init](media/terraform-vscode-extension/tf-terraform-init-command.png)

2. Введите `az login` и выполните инструкции на экране.

### <a name="module-test-lint"></a>Проверка модуля: *lint*

1. В **строке меню** выберите **Просмотр > Палитра команд > Azure Terraform: Execute Test**.
2. В списке вариантов типа теста выберите **lint**.

    ![Выбор типа теста](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

3. На *запрос открытия Cloud Shell* подтвердите согласие, нажав кнопку **ОК**, и следуйте инструкциям на экране.

    ![Запрос на открытие Cloud Shell](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>При выполнении тестов **lint** или **end-to-end** Azure с использованием службы контейнеров подготавливает тестовый компьютер к выполнению реального теста. Это означает, что результаты тестов обычно возвращаются только через несколько минут.

Через некоторое время вы увидите на панели терминала список, который выглядит примерно так:

![Результаты теста Lint](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="module-test-end-to-end"></a>Тестирование модуля: *end-to-end*

1. В **строке меню** выберите **Просмотр > Палитра команд > Azure Terraform: Execute Test**.
2. В списке вариантов типа теста выберите **end-to-end**.

    ![Выбор типа теста](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

3. На *запрос открытия Cloud Shell* подтвердите согласие, нажав кнопку **ОК**, и следуйте инструкциям на экране.

    ![Запрос на открытие Cloud Shell](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>При выполнении тестов **lint** или **end-to-end** Azure с использованием службы контейнеров подготавливает тестовый компьютер к выполнению реального теста. Это означает, что результаты тестов обычно возвращаются только через несколько минут.

Через некоторое время вы увидите на панели терминала список, который выглядит примерно так:

![Результаты сквозной проверки](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>Дополнительная информация

Итак, вы изучили некоторые действия Terraform, которые позволяют упростить подготовку служб Azure из Visual Studio Code. Теперь можно переходить к изучению следующих ресурсов.
- [Реестр модулей Terraform](https://registry.terraform.io/) перечисляет все модули Terraform, доступные для Azure и других поддерживаемых поставщиков.

Для каждого из модулей в списке предоставляется следующая информация:

- обобщенное описание возможностей и характеристик модуля;
- пример использования;
- тестовые конфигурации, в которых описаны процессы создания, запуска и тестирования для каждого модуля на локальном компьютере разработки;
- файл Dockerfile, который позволяет выполнять локальную сборку и запуск модуля в среде разработки.
