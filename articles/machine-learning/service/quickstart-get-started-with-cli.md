---
title: Приступая к работе со службой машинного обучения Azure с использованием расширения CLI | Документы Майкрософт
description: В этом кратком руководстве вы узнаете, как приступить к работе со службой машинного обучения Azure с помощью расширения CLI для машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
author: rastala
ms.author: roastala
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 296bbc12cb326c416c87948db0ce6d7b5ff114da
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972731"
---
# <a name="quickstart-get-started-with-azure-machine-learning-using-the-cli-extension"></a>Краткое руководство. Приступая к работе со службой машинного обучения Azure с использованием расширения CLI

В этом кратком руководстве вы приступите к работе со [службой машинного обучения Azure](overview-what-is-azure-ml.md) (предварительная версия) с помощью расширения CLI для машинного обучения.

С помощью интерфейса командной строки вы научитесь выполнять следующие действия:

1. Создавать рабочую область в своей подписке Azure. Рабочая область используется одним или несколькими пользователями для хранения вычислительных ресурсов, моделей, развертываний и журналов запуска в облаке.
1. Подключать проект к рабочей области.   Проект предоставляет собой локальную папку, содержащую сценарии и файлы конфигурации, необходимые для решения задачи машинного обучения.  
1. Запускать сценарий Python, который записывает некоторые значения за несколько итераций, в вашем проекте.
1. Просматривать записанные значения в журнале выполнения рабочей области.

> [!NOTE]
> Для удобства следующие ресурсы Azure автоматически добавляются в рабочую область, если они доступны в вашем регионе: [реестр контейнеров](https://azure.microsoft.com/services/container-registry/), [хранилище](https://azure.microsoft.com/services/storage/), [аналитические сведения о приложении](https://azure.microsoft.com/services/application-insights/) и [хранилище ключей](https://azure.microsoft.com/services/key-vault/).

Созданные ресурсы могут использоваться в качестве необходимых компонентов для других руководств и статей с практическими рекомендациями по машинному обучению Azure.

Этот интерфейс командной строки был создан на базе пакета <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a> для службы машинного обучения Azure на основе Python.

## <a name="prerequisites"></a>Предварительные требования

Перед выполнением инструкций в этом руководстве убедитесь, что у вас есть следующие необходимые компоненты:

+ Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.
+ Установленный [Python 3.5 или более поздней версии](https://www.python.org/)
+ Установленный [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="install-the-cli-extension"></a>Установка расширения CLI

На компьютере откройте редактор командной строки и установите [расширение машинного обучения в Azure CLI](reference-azure-machine-learning-cli.md).  Установка может занять несколько минут.

```azurecli
az extension add azureml-sdk
```

## <a name="install-the-sdk"></a>Установка пакета SDK

[!INCLUDE [aml-install-sdk](../../../includes/aml-install-sdk.md)]

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов — это контейнер, содержащий связанные ресурсы для решения Azure. С использованием Azure CLI войдите в Azure, укажите подписку и создайте группу ресурсов.

В окне командной строки войдите в Azure, выполнив команду `az login`. Следуйте инструкциям на экране для интерактивного входа в систему:
    
   ```azurecli
   az login
   ```

Выведите список доступных подписок Azure и укажите ту, которую вы хотите использовать:
   ```azurecli
   az account list --output table
   az account set --subscription <your-subscription-id>
   az account show
   ```
   где \<your-subscription-id\> — идентификатор подписки, которую вы хотите использовать. Не используйте квадратные скобки.

Создайте группу ресурсов для размещения рабочей области.
В этом кратком руководстве:
   + Имя группы ресурсов — `docs-aml`.
   + Регион — `eastus2`. 

   ```azurecli
   az group create -n docs-aml -l eastus2
   ```

## <a name="create-a-workspace-and-a-project-folder"></a>Создание рабочей области и папки проекта

В окне командной строки создайте рабочую область машинного обучения Azure в группе ресурсов.


   В этом кратком руководстве:
   + Имя рабочей области — `docs-ws`.
   + Имя группы ресурсов — `docs-aml`.

   ```azurecli
   az ml workspace create -n docs-ws -g docs-aml
   ```

В окне командной строки создайте папку на локальном компьютере для проекта машинного обучения Azure.

   ```
   mkdir docs-prj
   cd docs-prj
   ```

## <a name="create-a-python-script"></a>Создание скрипта Python

[!INCLUDE [aml-create-script-pi](../../../includes/aml-create-script-pi.md)]

## <a name="run-the-script"></a>Запуск сценария

Подключите папку как проект к рабочей области. Аргумент `--history` задает имя файла журнала выполнения, содержащего метрики для каждого запуска.

   ```azurecli
   az ml project attach --history my_history -w docs-ws -g docs-aml
   ```

Запустите сценарий на локальном компьютере.

   ```azurecli
   az ml run submit -c local pi.py
   ```

   Эта команда выполняет код и выводит ссылку в консоль. Скопируйте и вставьте ссылку в браузер.

В веб-браузере перейдите по URL-адресу. Откроется веб-портал с результатами выполнения. Вы можете просмотреть результаты запуска или предыдущих запусков, если они существуют.

Панель мониторинга портала поддерживается только в браузерах Edge, Chrome и Firefox.

   ![просмотр истории](./media/quickstart-get-started/web-results.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Дополнительная информация
Вы создали ресурсы, необходимые для начала экспериментов и развертывания моделей. Вы создали проект, выполнили сценарий и изучили журнал выполнения сценария.

Для получения дополнительных сведений о рабочем процессе воспользуйтесь руководствами по созданию, обучению и развертыванию модели для машинного обучения Azure.

> [!div class="nextstepaction"]
> [Руководство: создание, обучение и развертывание](tutorial-train-models-with-aml.md)