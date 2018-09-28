---
title: Настройка среды разработки для машинного обучения Azure | Документы Майкрософт
description: Узнайте, как настроить среду разработки при работе со службой машинного обучения Azure. Изучив этот документ, вы научитесь использовать среды Conda, создавать файлы конфигурации и настраивать Jupyter Notebook, записные книжки Azure, интегрированные среды разработки, редакторы кода и виртуальную машину для обработки и анализа данных.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 8/6/2018
ms.openlocfilehash: 7796accffb7041e567c5e18857d09e105b5268ce
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961575"
---
# <a name="how-to-configure-a-development-environment-for-the-azure-machine-learning-service"></a>Как настроить среду разработки для службы машинного обучения Azure

Узнайте, как настроить среду разработки для работы со службой машинного обучения Azure. Вы узнаете, как создать файл конфигурации, который связывает вашу среду с рабочей областью машинного обучения Azure. Кроме того, вы узнаете, как настраивать следующие среды разработки:

* Jupyter Notebook на вашем компьютере
* Visual Studio Code.
* Выбранный вами редактор кода

Рекомендуется использовать [виртуальную среду Conda Continuum Anaconda](https://conda.io/docs/user-guide/tasks/manage-environments.html), чтобы изолировать свою рабочую среду и избежать конфликтов зависимости между пакетами. В этой статье приведены этапы настройки среды Conda и использования ее для машинного обучения Azure.


## <a name="prerequisites"></a>Предварительные требования

* Рабочая область службы машинного обучения Azure. Чтобы создать ее, выполните действия, приведенные в документе [Начало работы со службой машинного обучения Azure](quickstart-get-started.md).

* Диспетчер пакетов [Continuum Anaconda](https://www.anaconda.com/download/) или [Miniconda](https://conda.io/miniconda.html).

 * [Расширение Python](https://code.visualstudio.com/docs/python/python-tutorial) для среды Visual Studio Code.

## <a name="create-workspace-configuration-file"></a>Создание файла конфигурации рабочей области

Файл конфигурации рабочей области используется SDK для связи с рабочей областью службы машинного обучения Azure.  Существует два способа получения этого файла:

* При выполнении [быстрого запуска](quickstart-get-started.md) файл `config.json` создается автоматически в записных книжках Azure.  Файл содержит сведения о конфигурации вашей рабочей области.  Скачайте его в тот же каталог, где находятся скрипты или записные книжки, ссылающиеся на него.

* Выполните следующие действия для создания файла конфигурации.

    1. Откройте рабочую область на [портале Azure](https://portal.azure.com). Копируйте __имя рабочей области__, __группу ресурсов__ и __ИД подписки__. Эти значения используются для создания файла конфигурации.

       Панель мониторинга для рабочей области портала поддерживается только в браузерах Edge, Chrome и Firefox.
    
        ![Портал Azure](./media/how-to-configure-environment/configure.png) 
    
    3. В текстовом редакторе создайте файл с именем **config.json**.  Добавьте в этот файл следующее содержимое, вставив значения с портала.
    
        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
    
        >[!NOTE] 
        >Впоследствии в коде прочитайте этот файл с помощью `ws = Workspace.from_config()`.
    
    4. Не забудьте сохранить файл **config.json** в тот же каталог, что и скрипты и записные книжки, ссылающиеся на него.
    
## <a name="azure-notebooks-and-data-science-virtual-machine"></a>Записные книжки Azure и виртуальная машина для обработки и анализа данных Azure

Записные книжки Azure и виртуальные машины для обработки и анализа данных Azure предварительно настроены для работы со службой машинного обучения Azure. Необходимые компоненты, такие как SDK машинного обучения Azure, устанавливаются автоматически в этих средах.

Записные книжки Azure — это служба Jupyter Notebook в облаке Azure. Виртуальная машина обработки и анализа данных — это образ виртуальной машины, который предварительно настроен для обработки и анализа данных. Виртуальная машина включает в себя популярные средства, интегрированные среды разработки и пакеты, такие как записные книжки Jupyter, PyCharm и Tensorflow.

Для использования этих сред вам по-прежнему потребуется файл конфигурации рабочей области.

Дополнительные сведения о виртуальных машинах для анализа и обработки данных см. в документации [Виртуальные машины для анализа и обработки данных](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

Пример использования записных книжек Azure со службой машинного обучения Azure см. в документе [Начало работы со службой машинного обучения Azure](quickstart-get-started.md).

## <a name="configure-jupyter-notebooks-on-your-own-computer"></a>Настройка Jupyter Notebook на своем компьютере

1. Откройте командную строку или оболочку.

2. Чтобы создать среду conda, используйте следующие команды:

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

    Создание среды может занять несколько минут, так как, возможно, потребуется скачать Python 3.6 и другие компоненты.

3. Чтобы установить пакет SDK машинного обучения Azure с дополнительными компонентами для работы с записными книжками, выполните следующую команду:

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl,contrib]
    ```

    Установка пакета SDK может занять несколько минут.

4. Чтобы установить пакеты для экспериментов с машинным обучением, воспользуйтесь следующей командой и замените `<new package>` пакетом, который требуется установить.

    ```shell
    conda install <new package>
    ```

5. Чтобы установить conda с поддержкой сервера Jupyter Notebook и включить экспериментальные мини-приложения (для просмотра сведений о выполнении), используйте следующие команды:

    ```shell
    # install Jupyter 
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

6. Чтобы запустить Jupyter Notebook, используйте следующую команду:

    ```shell
    jupyter notebook
    ```

7. Откройте записную книжку и выберите myenv в качестве ядра. Проверьте, что SDK машинного обучения Azure установлен, выполнив следующую команду в ячейке блокнота:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>Откройте Visual Studio Code.

1. Откройте командную строку или оболочку.

2. Чтобы создать среду conda, используйте следующие команды:

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

2. Чтобы установить пакет SDK машинного обучения Azure, выполните следующую команду:
 
    ```shell
    pip install --upgrade azureml-sdk[automl,contrib]
    ```

4. Чтобы установить средства Visual Studio Code для искусственного интеллекта, см. запись о Visual Studio на Marketplace: [Инструменты для искусственного интеллекта](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai). 

5. Чтобы установить пакеты для экспериментов с машинным обучением, воспользуйтесь следующей командой и замените `<new package>` пакетом, который требуется установить.

    ```shell
    conda install <new package>
    ```

6. Запустите Visual Studio Code, а затем используйте __CTRL-SHIFT-P__ для получения __палитры команд__. Введите *Python: выбор интерпретатора*и выберите созданную среду conda.

    > [!NOTE]
    > Visual Studio Code автоматически учитывает среды conda на компьютере. Дополнительные сведения см. в документации по [Visual Studio Code](https://code.visualstudio.com/docs/python/environments#_conda-environments).

7. Чтобы проверить конфигурацию, воспользуйтесь Visual Studio Code для создания нового файла сценария Python со следующим кодом, а затем запустите его:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-code-editor-of-your-choice"></a>Настройка редактора кода по вашему выбору

Чтобы использовать выбранный редактор кода с SDK машинного обучения Azure, сначала создайте среду conda, как описано выше. Затем выполните инструкции для каждого редактора, чтобы использовать среду conda. Так, инструкции для PyCharm находятся здесь: [https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html).
 
## <a name="next-steps"></a>Дополнительная информация

* [Обучение модели в машинном обучении Azure с помощью набора данных MNIST](tutorial-train-models-with-aml.md)
