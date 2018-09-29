---
title: Развертывание моделей на устройствах IoT Edge из службы машинного обучения Azure | Документы Майкрософт
description: Узнайте, как развернуть модель из службы машинного обучения Azure для устройств Azure IoT Edge. Развертывание модели на граничном устройстве позволяет оценивать данные на устройстве, а не отправлять их в облако и ждать ответа.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shipatel
author: shivanipatel
manager: cgronlun
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: 03d692ddfd6f41fd559e9b921f0214a9cd2ada22
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225231"
---
# <a name="prepare-to-deploy-models-on-iot-edge"></a>Подготовка к развертыванию моделей в IoT Edge

В этом документе вы научитесь использовать службу машинного обучения Azure для подготовки обученной модели к развертыванию на устройстве Azure IoT Edge.

Устройство Azure IoT Edge — это устройство под управлением Linux или Windows, которое запускает среду выполнения Azure IoT Edge. Модели машинного обучения могут развертываться на этих устройствах как модули IoT Edge. Развертывание модели на устройстве IoT Edge позволяет устройству использовать эту модель напрямую, а не отправлять данные в облако для обработки. При этом сокращается время отклика и объем передачи данных.

Прежде чем развертывать модель на граничном устройстве, выполните действия, приведенные в этом документе, чтобы подготовить модель и устройство.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начать работу.

* Рабочая область службы машинного обучения Azure. Чтобы создать ее, выполните действия, приведенные в документе [Начало работы со службой машинного обучения Azure](quickstart-get-started.md).

* Среда разработки. Дополнительные сведения см. в документе [Настройка среды разработки](how-to-configure-environment.md).

* [Центр Интернета вещей](../../iot-hub/iot-hub-create-through-portal.md) в подписке Azure. 

* Обученная модель. Пример обучения модели см. в документе [Обучение модели классификации изображений с помощью машинного обучения Azure](tutorial-train-models-with-aml.md).

## <a name="prepare-the-iot-device"></a>Подготовка устройства IoT

Чтобы узнать, как зарегистрировать устройство и установить среду выполнения IoT, выполните действия, описанные в документе [Краткое руководство. Развертывание вашего первого модуля IoT Edge на устройстве Linux x64](../../iot-edge/quickstart-linux.md).

## <a name="register-the-model"></a>Регистрация модели

Модули Azure IoT Edge основаны на образах контейнеров. Чтобы развернуть модель на устройство IoT Edge, выполните следующие действия для регистрации модели в рабочей области машинного обучения Azure и создайте образ Docker. 

> [!IMPORTANT]
> Если для обучения модели использовалось машинное обучение Azure, эта модель уже может быть зарегистрирована в вашей рабочей области. В этом случае пропустите шаг 3.

1. Инициализируйте рабочую область и загрузите файл config.json:

    ```python
    from azureml.core  import Workspace

    #Load existing workspace from the config file info.
    ws  = Workspace.from_config()
    ```    

1. Зарегистрируйте модель в своей рабочей области. Вместо текста по умолчанию введите путь, имя, теги и описание вашей модели:

    ```python
    from azureml.core.model import Model
    model = Model.register(model_path = "model.pkl", # this path points to the local file
                        model_name = "best_model", # the model gets registered as this name
                        tags = {'attribute': "myattribute", 'classification': "myclassification"},
                        description = "My awesome model",
                        workspace = ws)
    ```    

1. Извлеките зарегистрированную модель: 

    ```python
    from azureml.core.model import Model

    model_name = "best_model"
    model = Model(ws, model_name)                     
    ```    

## <a name="create-a-docker-image"></a>Создайте образ Docker.

1. Создайте **скрипт оценки** с именем `score.py`. Этот файл используется для запуска модели в образе. В нем должны содержаться следующие функции.

    * Функция `init()`, которая обычно загружает модель в глобальный объект. Эта функция выполняется только один раз при запуске контейнера Docker. 

    * Функция `run(input_data)` использует модель для прогнозирования значения на основе входных данных. Входные и выходные данные для запуска обычно используют JSON для сериализации и десериализации, но поддерживаются и другие форматы.

    Пример см. в [руководстве по классификации изображений](tutorial-deploy-models-with-aml.md#make-script).

1. Создайте **файл среды** с именем `myenv.yml`. Этот файл представляет собой спецификацию среды Conda, и в нем перечислены все зависимости, необходимые для скрипта и модели. Пример см. в [руководстве по классификации изображений](tutorial-deploy-models-with-aml.md#make-myenv).

1. Настройте образ Docker с помощью файлов `score.py` и `myenv.yml`:
    
    ```python
    from azureml.core.image import Image, ContainerImage
    
    #Image configuration
    image_config = ContainerImage.image_configuration( runtime = "python", 
                           execution_script = "score.py",
                           conda_file = "myenv.yml", 
                           tags = {"attributes", "calssification"},
                           description = "Image that contains my model",
                           
                        )
    ```    

1. Создайте образ с помощью конфигурации модели и образа:

    ```python
    image = ContainerImage.create (name = "myimage", 
                           models = [model], #this is the model object
                           image_config = image_config,
                           workspace = ws
                        )
    ```     

    Создание образа занимает около 5 минут.

## <a name="get-the-container-registry-credentials"></a>Получение учетных данных реестра контейнеров

Azure IoT требуются учетные данные для реестра контейнеров, в котором служба машинного обучения Azure хранит образы Docker. Чтобы получить эти учетные данные, выполните следующие действия.

1. Войдите на [портале Azure](https://portal.azure.com/signin/index).

1. Перейдите в свою рабочую область машинного обучения Azure и выберите __Обзор__. Чтобы перейти к параметрам реестра контейнеров, нажмите ссылку __Реестр__.

    ![Изображение записи реестра контейнеров](./media/how-to-deploy-to-iot/findregisteredcontainer.png)

1. В реестре контейнеров выберите раздел **Ключи доступа**, а затем включите учетную запись администратора.

    ![Изображение экрана ключей доступа](./media/how-to-deploy-to-iot/findaccesskey.png)

1. Сохраните значения для полей "Сервер входа", "Имя пользователя" и "Пароль". 

   Эти учетные данные необходимы для предоставления устройству IoT Edge доступа к образам в закрытом реестре контейнеров.

## <a name="next-steps"></a>Дополнительная информация

Вы выполнили подготовку к развертыванию. Теперь выполните действия, приведенные в документе [Развертывание модулей Azure IoT Edge с портала Azure](../../iot-edge/how-to-deploy-modules-portal.md), чтобы развернуть ваше граничное устройство. При настройке __параметров реестра__ для устройства используйте учетные данные, настроенные ранее.
