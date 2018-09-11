---
title: Установка и использование расширения Visual Studio Code для Azure Terraform
description: Сведения о том, как установить и использовать расширение Azure Terraform в Visual Studio Code.
services: terraform
ms.service: terraform
keywords: terraform, azure, devops, visual studio code, extension
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/31/2018
ms.openlocfilehash: ad042525ead23959be57a5dd2d5735bcedfedb1b
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43668331"
---
# <a name="install-and-use-the-azure-terraform-visual-studio-code-extension"></a>Установка и использование расширения Visual Studio Code для Azure Terraform

Расширение Microsoft Azure Terraform для Visual Studio Code повышает производительность разработчиков при создании, тестировании и использовании Terraform в Azure. Это расширение реализует поддержку команд Terraform, визуализацию графиков ресурсов и интеграцию с Azure Cloud Shell из Visual Studio Code.

В этой статье раскрываются следующие темы:
> [!div class="checklist"]
> * использование Terraform для автоматизации и упрощения подготовки служб Azure;
> * установка и использование расширения Visual Studio Code Microsoft Terraform для служб Azure;
> * использование Visual Studio Code для разработки, проектирования и выполнения планов Terraform.

## <a name="prerequisites"></a>Предварительные требования
- **Подписка Azure**. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.

- **Terraform**. [Установите и настройте Terraform](/azure/virtual-machines/linux/terraform-install-configure).

- **Visual Studio Code**. Установите подходящую для вашей среды версию [Visual Studio Code](https://code.visualstudio.com/download).

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
>Чтобы проверить наличие Node.js, откройте окно терминала и введите команду `node -v`.

### <a name="install-graphviz"></a>Установка GraphViz

Чтобы использовать функцию визуализации в Terraform, вам нужно [установить GraphViz](http://graphviz.org/).

>[!NOTE]
>Чтобы проверить наличие GraphViz, откройте окно терминала и введите команду `dot -V`.

### <a name="install-the-azure-terraform-visual-studio-code-extension"></a>Установка расширения Visual Studio Code для Azure Terraform

1. Запустите Visual Studio Code.

1. Выберите **Расширения**.

    ![Кнопка "Расширения"](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

1. Используйте текстовое поле **Поиск расширений в Marketplace** для поиска расширения Azure Terraform:

    ![Поиск расширений Visual Studio Code в Marketplace](media/terraform-vscode-extension/tf-search-extensions.png)

1. Щелкните **Установить**.

    >[!NOTE]
    >Когда вы щелкнете **Установить** рядом с расширением Azure Terraform, Visual Studio Code автоматически установит расширение Azure Account. Azure Account — это файл зависимостей для расширения Azure Terraform, который используется для аутентификации подписки Azure и расширений, имеющих отношение к Azure.

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>Проверка наличия расширения Terraform в Visual Studio Code

1. Выберите **Расширения**.

1. В текстовое поле поиска введите `@installed`.

    ![Установленные расширения](media/terraform-vscode-extension/tf-installed-extensions.png)

Расширение Azure Terraform появится в списке установленных расширений.

![Установленные расширения Terraform](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

Теперь вы можете выполнять из Visual Studio Code все поддерживаемые команды Terraform в среде Cloud Shell.

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>Упражнение 1. Пошаговое руководство по основным командам Terraform

В этом упражнении вы создадите и выполните базовый файл конфигурации Terraform, который подготавливает новую группу ресурсов Azure.

### <a name="prepare-a-test-plan-file"></a>Подготовка файла для плана тестирования

1. В строке меню Visual Studio Code выберите **Файл > Создать файл**.

1. Перейдите к [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#) и скопируйте код из блока **Example Usage**:

1. Вставьте скопированный код в новый файл, созданный в Visual Studio Code.

    ![Вставка кода блока Example Usage](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >Вы можете изменить значение **Имя** для группы ресурсов, но это имя должно быть уникальным в пределах подписки Azure.

1. В строке меню выберите **Файл > Сохранить как**.

1. В диалоговом окне **Сохранение как** перейдите в любую удобную папку и щелкните **Создать папку**. (Вместо имени *Новая папка* присвойте этой папке более понятное имя.)

    >[!NOTE]
    >В нашем примере папка получает имя TERRAFORM-TEST-PLAN.

1. Убедитесь, что новая папка выделена (выбрана) и щелкните **Открыть**.

1. В диалоговом окне **Сохранение как** измените имя файла на *main.tf*.

    ![Сохранение файла с именем main.tf](media/terraform-vscode-extension/tf-save-as-main.png)

1. Щелкните **Сохранить**.
- В строке меню выберите **Файл > Открыть папку**. Найдите и выберите папку, которую вы только что создали.

### <a name="run-terraform-init-command"></a>Выполнение команды Terraform *init*

1. Запустите Visual Studio Code.

1. В строке меню Visual Studio Code выберите **Файл > Открыть папку**, а затем найдите и выберите созданный файл *main.tf*.

    ![Файл main.tf](media/terraform-vscode-extension/tf-main-tf.png)

1. В строке меню выберите **Просмотр > Палитра команд > Azure Terraform: init**.

1. Когда появится запрос на подтверждение, щелкните **ОК**.

    ![Запрос на открытие Cloud Shell](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

1. При первом запуске Cloud Shell из новой папки вам будет предложено настроить веб-приложение. Щелкните **Open**(Открыть).

    ![Первый запуск Cloud Shell](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

1. Откроется страница приветствия Azure Cloud Shell. Выберите Bash или PowerShell.

    ![Страница приветствия Azure Cloud Shell](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >В нашем примере выбран вариант Bash (Linux).

1. Если вы еще не настроили учетную запись хранения Azure, откроется следующий экран. Щелкните **Создать хранилище**.

    ![Подключенное хранилище отсутствует](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. Azure Cloud Shell запускается в оболочке, которую вы выбрали ранее, и отображает сведения о только что созданном облачном диске.

    ![Ваш облачный диск успешно создан](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

1. Теперь можно выйти из Cloud Shell.

1. В строке меню выберите **Просмотр** > **Палитра команд** > **Azure Terraform: init**.

    ![Terraform успешно инициализирован](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>Визуализация плана

Ранее с помощью этого руководства вы установили GraphViz. Terraform с помощью GraphViz может создать визуальное представление плана выполнения или конфигурации. Расширение Azure Terraform для Visual Studio Code реализует эту функцию с помощью команды *visualize*.

- В строке меню выберите **Просмотр > Палитра команд > Azure Terraform: visualize**.

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

1. Укажите `yes`.

    ![Согласие на выполнение команды Terraform apply](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>Проверка выполнения команды Terraform plan

Чтобы проверить создание группы ресурсов Azure, сделайте следующее:

1. Перейдите на портал Azure.

1. В области навигации слева выберите **Группа ресурсов**.

    ![Проверка нового ресурса](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

Новая группа ресурсов должна быть указана в столбце **Имя**.

>[!NOTE]
>Вам можете не закрывать окно портала Azure — оно нам понадобится на следующем шаге.

### <a name="run-terraform-destroy-command"></a>Выполнение команды Terraform *destroy*

1. В строке меню выберите **Просмотр** > **Палитра команд** > **Azure Terraform: destroy**.

    ![Команда Terraform destroy](media/terraform-vscode-extension/tf-terraform-destroy.png)

1. Введите *Yes* (Да).

    ![Согласие на выполнение команды Terraform destroy](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>Проверка удаления группы ресурсов

Чтобы проверить удаление группы ресурсов в Terraform, сделайте следующее:

1. Щелкните **Обновить** на странице **Группы ресурсов** на портале Azure.

1. Удаленная группа ресурсов должна исчезнуть из этого списка.

    ![Проверка удаления группы ресурсов](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>Упражнение 2. Модуль Terraform *compute*

В этом упражнении показано, как загрузить модуль Terraform *compute* в среду Visual Studio Code.

### <a name="clone-the-terraform-azurerm-compute-module"></a>Клонирование модуля terraform-azurerm-compute

1. Используйте [эту ссылку](https://github.com/Azure/terraform-azurerm-compute), чтобы открыть страниц модуля Terraform Azure Rm Compute на GitHub.

1. Выберите **Clone or download**(Клонировать или скачать).

    ![Клонирование или скачивание](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >В нашем примере папке присвоено имя *terraform-azurerm-compute*.

### <a name="open-the-folder-in-visual-studio-code"></a>Откройте эту папку в Visual Studio Code.

1. Запустите Visual Studio Code.

1. На панели меню выберите **Файл > Открыть папку**, а затем найдите и выберите созданную на предыдущем шаге папку.

    ![Папка terraform-azurerm-compute](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>Инициализация Terraform

Прежде чем выполнять команды Terraform в Visual Studio Code, нужно скачать подключаемые модули для двух поставщиков Azure: random и azurerm.

1. На панели терминала в интегрированной среде разработки Visual Studio Code введите следующую команду: `terraform init`.

    ![Команда Terraform init](media/terraform-vscode-extension/tf-terraform-init-command.png)

1. Введите `az login`, нажмите клавишу **ВВОД** и выполните инструкции на экране.

### <a name="module-test-lint"></a>Проверка модуля: *lint*

1. В строке меню выберите **Просмотр > Палитра команд > Azure Terraform: execute test**.

1. В списке вариантов типа теста выберите **lint**.

    ![Выбор типа теста](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

1. Когда появится запрос на подтверждение, щелкните **ОК** и следуйте инструкциям на экране.

    ![Запрос на открытие Cloud Shell](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>При выполнении тестов **lint** или **end-to-end** Azure с использованием службы контейнеров подготавливает тестовый компьютер к выполнению реального теста. Это означает, что результаты тестов обычно возвращаются только через несколько минут.

Через некоторое время вы увидите на панели терминала список, который выглядит примерно так:

![Результаты теста Lint](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="module-test-end-to-end"></a>Тестирование модуля: *end-to-end*

1. В строке меню выберите **Просмотр > Палитра команд > Azure Terraform: execute test**.

1. В списке вариантов типа теста выберите **end-to-end**.

    ![Выбор типа теста](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

1. Когда появится запрос на подтверждение, щелкните **ОК** и следуйте инструкциям на экране.

    ![Запрос на открытие Cloud Shell](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>При выполнении тестов **lint** или **end-to-end** Azure с использованием службы контейнеров подготавливает тестовый компьютер к выполнению реального теста. Это означает, что результаты тестов обычно возвращаются только через несколько минут.

Через некоторое время вы увидите на панели терминала список, который выглядит примерно так:

![Результаты сквозной проверки](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Список модулей Terraform, доступных для Azure и других поддерживаемых поставщиков](https://registry.terraform.io/)