---
title: Настройка отправки файлов в Центр Интернета вещей с помощью Azure CLI | Документация Майкрософт
description: Настройка отправки файлов в Центр Интернета вещей Azure, используя кроссплатформенный Azure CLI.
author: dominicbetts
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 6cd0b657c8d0352c41e0da538396b166d633306a
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42143414"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Настройка отправки файлов в Центре Интернета вещей с помощью Azure CLI

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Чтобы [передать файлы с устройства](iot-hub-devguide-file-upload.md), сначала необходимо связать учетную запись хранения Azure с Центром Интернета вещей. Можно использовать существующую учетную запись хранения или создать новую.

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* Центр интернета вещей Azure. Чтобы создать Центр Интернета вещей, выполните [команду `az iot hub create`](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) или [Создайте Центр Интернета вещей с помощью портала Azure](iot-hub-create-through-portal.md).

* Учетная запись хранения Azure. Если у вас еще нет учетной записи хранения Azure, вы можете использовать [Управление учетными записями хранения в Azure CLI](../storage/common/storage-azure-cli.md#manage-storage-accounts) или [Создайте учетную запись хранения](../storage/common/storage-create-storage-account.md) с помощью портала Azure.

## <a name="sign-in-and-set-your-azure-account"></a>Выполнение входа и установка учетной записи Azure

Войдите в учетную запись Azure и выберите подписку.

1. В командной строке запустите [команду login](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest):

    ```azurecli
    az login
    ```

    Следуйте инструкциям, чтобы выполнить аутентификацию с использованием кода и войти в учетную запись Azure через веб-браузер.

2. Если у вас есть несколько подписок Azure, то при выполнении входа в Azure вы получаете доступ ко всем учетным записям Azure, связанным с вашими учетными данными. Используйте следующую [команду для вывода учетных записей Azure](https://docs.microsoft.com/cli/azure/account), доступных для использования:

    ```azurecli
    az account list
    ```

    Используйте следующую команду, чтобы выбрать подписку, которая будет использоваться для выполнения команд для создания Центра Интернета вещей. Вы можете использовать имя подписки или идентификатор из выходных данных предыдущей команды:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Получение сведений об учетной записи хранения

В следующих шагах предполагается, что для создания учетной записи хранения вы использовали модель развертывания **с помощью Resource Manager**, а не **классическую** модель развертывания.

Для настройки отправки файлов с ваших устройств необходима строка подключения учетной записи хранения Azure. Эта учетная запись хранения должна относиться к той же подписке, что и Центр Интернета вещей. Кроме того, вам понадобится имя контейнера BLOB-объектов в учетной записи хранения. Для получения ключей учетной записи хранения используйте следующую команду:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Запишите значение **connectionString**. Оно понадобится вам на следующих этапах.

Для отправки файлов можно использовать существующий контейнер BLOB-объектов или создать новый:

* Чтобы получить список имеющихся контейнеров больших двоичных объектов в вашей учетной записи хранения, выполните следующую команду:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Для создания контейнера больших двоичных объектов в учетной записи хранения выполните следующую команду:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Передача файла

Теперь с помощью данных учетной записи хранения можно настроить Центр Интернета вещей для включения функции [отправки файлов в Центр Интернета вещей](iot-hub-devguide-file-upload.md).

Для настройки потребуются следующие значения:

* **Контейнер хранилища**. Контейнер BLOB-объектов в учетной записи хранения Azure в текущей подписке Azure, который нужно связать с Центром Интернета вещей. Необходимые сведения об учетной записи хранения вы получили в предыдущем разделе. Центр Интернета вещей автоматически генерирует универсальные коды ресурсов (URI) подписанных URL-адресов с разрешениями на запись в этом контейнере больших двоичных объектов, чтобы устройства могли их использовать во время передач файлов.

* **Receive notifications for uploaded files** (Получать уведомления об отправленных файлах). Включите или отключите уведомления об отправке файлов.

* **SAS TTL** (Срок жизни SAS). Этот параметр определяет срок жизни универсальных кодов ресурса (URI) SAS, возвращаемых Центром Интернета вещей на устройство. Значение по умолчанию — один час.

* **File notification settings default TTL** (Стандартный срок жизни уведомления о файле). Срок жизни уведомления об отправке файла. Значение по умолчанию — один день.

* **File notification maximum delivery count**(Максимальное число доставок уведомления о файле): число попыток доставки уведомления о передаче файла, предпринимаемых Центром Интернета вещей. Значение по умолчанию — 10.

Чтобы настроить параметры отправки файлов в Центре Интернета вещей, выполните следующие команды Azure CLI:

<!--Robinsh this is out of date, add cloud powershell -->

Выполните следующие команды в оболочке Bash:

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Вы можете просмотреть конфигурацию отправки файла в Центре Интернета вещей, выполнив следующую команду:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о возможностях Центра Интернета вещей, связанных с отправкой файлов, см. в разделе [Отправка файлов с помощью Центра Интернета вещей](iot-hub-devguide-file-upload.md).

Дополнительные сведения об управлении Центром Интернета вещей в Azure см. по следующим ссылкам:

* [Массовое управление удостоверениями устройств Центра Интернета вещей](iot-hub-bulk-identity-mgmt.md)
* [Общие сведения о метриках Центра Интернета вещей](iot-hub-metrics.md)
* [Мониторинг операций](iot-hub-operations-monitoring.md)

Для дальнейшего изучения возможностей Центра Интернета вещей см. следующие статьи:

* [Руководство разработчика для Центра Интернета вещей](iot-hub-devguide.md)
* [Краткое руководство. Развертывание первого модуля IoT Edge на устройстве под управлением 64-разрядной ОС Linux](../iot-edge/tutorial-simulate-device-linux.md)
* [Комплексная защита в Интернете вещей](../iot-fundamentals/iot-security-ground-up.md)
