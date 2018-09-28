---
title: Расширение интерфейса командной строки для Машинного обучения Azure
description: Узнайте о расширении интерфейса командной строки машинного обучения для службы Машинное обучение Azure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 09/24/2018
ms.openlocfilehash: 5d14373b265ea30d235cc5bc7b87ee13c4fd8105
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991799"
---
# <a name="what-is-the-azure-machine-learning-cli"></a>Что такое интерфейс командной строки Машинного обучения Azure?

Расширение интерфейса командной строки Машинного обучения Azure используется для ученых и разработчиков, работающих с этой службой. Оно позволяет быстро автоматизировать рабочие процессы машинного обучения и объединять их в рабочей среде. А также:
+ выполнять эксперименты для создания моделей машинного обучения;

+ регистрировать модели машинного обучения для использования клиентами;

+ упаковывать, развертывать и отслеживать жизненный цикл моделей машинного обучения.

Этот интерфейс командной строки машинного обучения является расширением [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) и был построен с <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a> на основе Python для службы Машинное обучение Azure.

> [!NOTE]
> Интерфейс командной строки находится на раннем этапе предварительной версии и будет обновлен.

## <a name="installing-and-uninstalling"></a>Установка и удаление

Можно установить интерфейс командной строки с помощью следующей команды из индекса PyPi предварительной версии.
```AzureCLI
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-0.1.50-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

Для удаления интерфейса командной строки можно использовать следующую команду.
```AzureCLI
az extension remove -n azure-cli-ml
```

## <a name="using-the-cli-vs-the-sdk"></a>Использование интерфейса командной строки и пакета SDK
Интерфейс командной строки больше подходит для автоматизации разработки пользователя или как часть конвейера непрерывной интеграции и доставки. Он оптимизирован для обработки редких и очень параметризованных задач. 

Примеры приведены ниже.
- Вычисление подготовки.
- Отправка параметризованного эксперимента.
- Регистрация модели, создание образа.
- Развертывание службы.

Специалисты по анализу данных рекомендуют использовать пакет SDK для Машинного обучения Azure.

## <a name="common-machine-learning-cli-commands"></a>Распространенные команды для интерфейса командной строки машинного обучения

Используйте богатый набор команд `az ml` для взаимодействия службы в любой среде командной строки, включая облачную оболочку портала Azure.

Ниже приведен пример общих команд.

### <a name="workspace-creation--compute-setup"></a>Настройка создания и вычисления рабочей области

+ Создайте рабочую область Машинного обучения Azure, ресурс верхнего уровня для машинного обучения.
  ```AzureCLI
  az ml workspace create -n myworkspace -g myresourcegroup
  ```

+ Установите интерфейс командной строки для использования этой рабочей области по умолчанию.
```AzureCLI
az configure --defaults aml_workspace=myworkspace group=myresourcegroup
```

+ Создайте DSVM (Виртуальная машина для обработки и анализа данных) для обучения моделей. Также можно создать кластеры BatchAI для распределенного обучения.
  ```AzureCLI
  az ml computetarget setup dsvm -n mydsvm
  ```

### <a name="experiment-submission"></a>Отправка эксперимента
+ Вложите проект (конфигурация запуска) для отправки эксперимента. Это делается для отслеживания нескольких запусков эксперимента.
  ```AzureCLI
  az ml project attach --experiment-name myhistory
  ```

+ Отправьте эксперимент в службу Машинное обучение Azure целевому объекту вычисления на ваш выбор (в этом примере используется Виртуальная машина для обработки и анализа данных)
  ```AzureCLI
  az ml run submit -c mydsvm train.py
  ```

+ Просмотрите список отправленных экспериментов.
```AzureCLI
az ml history list
```

### <a name="model-registration-image-ceation--deployment"></a>Регистрация модели, создание и развертывание образа

+ Зарегистрируйте модель с помощью Машинного обучения Azure.
  ```AzureCLI
  az ml model register -n mymodel -m mymodel.pkl  -w myworkspace -g myresourcegroup
  ```

+ Создайте образ для размещения модели и зависимостей машинного обучения. 
  ```AzureCLI
  az ml image create -n myimage -r python -m rfmodel.pkl -f score.py -c myenv.yml
  ```

+ Разверните упакованную модель в целевой объект, включая ACI и AKS.
  ```AzureCLI
  az ml service create aci -n myaciservice -i myimage:1
  ```
    
## <a name="full-command-list"></a>Полный список команд
Полный список команд для расширения интерфейса командной строки (и их поддерживаемых параметров) можно найти, выполнив ```az ml COMMANDNAME -h```. 
