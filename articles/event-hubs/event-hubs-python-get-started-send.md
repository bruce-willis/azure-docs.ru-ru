---
title: Отправка событий в Центры событий Azure с помощью Python | Документация Майкрософт
description: Приступая к отправке событий в Центры событий с помощью Python.
services: event-hubs
author: sethmanheim
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/26/2018
ms.author: sethm
ms.openlocfilehash: 762e21cfc7d16b614eb637c569f8bfc5b6115db1
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43703524"
---
# <a name="send-events-to-event-hubs-using-python"></a>Отправка событий в Центры событий с помощью Python

Концентраторы событий Azure — это высокомасштабируемая система управления событиями, которая может принимать миллионы событий в секунду, позволяя приложениям обрабатывать и анализировать большие объемы данных, сформированных подключенными устройствами и другими системами. После сбора событий в концентратор событий вы можете получать и обрабатывать события с помощью внутрипроцессных обработчиков или перенаправлять их в другие аналитические системы.

Дополнительные сведения о концентраторах событий см. в статье [Что такое концентраторы событий Azure?][Event Hubs overview].

В этом руководстве представлены инструкции по отправке событий в концентратор событий из приложения, написанного на Python. Для получения событий ознакомьтесь со статьей [Receive events from Event Hubs using Python](event-hubs-python-get-started-receive.md) (Получение событий из концентраторов событий с помощью Python).

Из [этих примеров GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) в руководство взят код, который можно проверить, чтобы увидеть полное рабочее приложение, включая инструкции импорта и объявления переменных. Другие примеры доступны в той же папке GitHub.

## <a name="prerequisites"></a>Предварительные требования

Для работы с данным руководством вам потребуется:

- Python 3.4 или более поздней версии.
- Существующее пространство имен Центров событий и концентратор событий. Эти сущности можно создать, следуя инструкциям в [этой статье](event-hubs-create.md). 

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]


## <a name="install-python-package"></a>Установка пакета Python

Чтобы установить пакет Python для Центров событий, откройте окно командной строки с Python в пути, а затем выполните следующую команду. 

```bash
pip install azure-eventhub
```

## <a name="create-a-python-script-to-send-events"></a>Создание сценария Python для отправки событий

Создайте приложение Python, отправляющее события в концентратор событий:

1. Откройте предпочитаемый редактор Python, например [Visual Studio Code][Visual Studio Code].
2. Создайте сценарий с именем **send.py**. Этот сценарий отправляет 100 событий в ваш концентратор событий.
3. Вставьте следующий код в send.py, заменив значения ADDRESS, USER и KEY значениями, полученными на портале Azure в предыдущем разделе: 

```python
import sys
import logging
import datetime
import time
import os

from azure.eventhub import EventHubClient, Sender, EventData

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"

try:
    if not ADDRESS:
        raise ValueError("No EventHubs URL supplied.")

    # Create Event Hubs client
    client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
    sender = client.add_sender(partition="0")
    client.run()
    try:
        start_time = time.time()
        for i in range(100):
            print("Sending message: {}".format(i))
            sender.send(EventData(str(i)))
    except:
        raise
    finally:
        end_time = time.time()
        client.stop()
        run_time = end_time - start_time
        logger.info("Runtime: {} seconds".format(run_time))

except KeyboardInterrupt:
    pass
```

## <a name="send-events"></a>Отправка событий

Чтобы выполнить сценарий, откройте окно командной строки с Python в пути, а затем выполните следующую команду.

```bash
start python send.py
```
 
## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы отправили события в концентратор событий с помощью Python, чтобы получать события, ознакомьтесь со статьей [Receive events from Event Hubs using Python](event-hubs-python-get-started-receive.md) (Получение событий из концентраторов событий с помощью Python).

Чтобы узнать больше о Центрах событий, посетите следующие страницы:

* [Общие сведения о Центрах событий Azure][Event Hubs overview].
* [Создание концентратора событий](event-hubs-create.md)
* [Часто задаваемые вопросы о Центрах событий](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
