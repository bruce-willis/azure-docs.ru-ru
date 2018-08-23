---
title: Включение функции "Сбор" в Центрах событий с помощью портала | Документация Microsoft
description: Включите функцию "Сбор" в Центрах событий с помощью портала Azure.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: ff80bc2452c9826a5c51c146a957fddc72d2dbc2
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2018
ms.locfileid: "41919828"
---
# <a name="enable-event-hubs-capture-using-the-azure-portal"></a>Включение функции "Сбор" в Центрах событий с помощью портала Azure

[Функция "Сбор" в Центрах событий][capture-overview] Azure позволяет автоматически доставлять данные потоковой передачи из Центров событий в выбранную учетную запись [хранилища BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/) или [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Запись можно настроить при создании концентратора событий с помощью [портала Azure](https://portal.azure.com). Данные можно записывать в контейнер [хранилища BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/) или в учетную запись [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Подробнее см. в статье [Общие сведения о функции "Сбор" в Центрах событий Azure][capture-overview].

## <a name="capture-data-to-an-azure-storage-account"></a>Запись данных в учетную запись службы хранилища Azure  

Запись можно включить при создании концентратора событий. Для этого на экране **Создание концентратора событий** на портале нажмите кнопку **Вкл**. Затем укажите учетную запись хранения и контейнер, выбрав **Служба хранилища Azure** в поле **поставщика записи**. Так как для функции "Сбор" в Центрах событий и хранилища используется проверка подлинности со взаимодействием между службами, указывать строку подключения к хранилищу не нужно. Средство выбора ресурсов автоматически выбирает универсальный код ресурса (URI) для вашей учетной записи хранения. При использовании Azure Resource Manager этот универсальный код ресурса необходимо явным образом указать как строку.

Продолжительность времени окна по умолчанию составляет 5 минут. Минимальное значение равно 1, а максимальное — 15. Диапазон **окна размера** составляет 10–500 МБ.

![][1]

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>Запись данных в учетную запись Azure Data Lake Store

Для записи данных в Azure Data Lake Store создайте учетную запись Data Lake Store и концентратор событий.

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>Создание учетной записи и папок Azure Data Lake Store

1. Создайте учетную запись Data Lake Store, следуя инструкциям в статье [Начало работы с Azure Data Lake Store с помощью портала Azure](../data-lake-store/data-lake-store-get-started-portal.md).
2. Следуйте инструкциям по [назначению разрешений для Центров событий](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs), чтобы создать папку в рамках учетной записи Data Lake Store, в которой нужно собирать данные из концентраторов событий. Затем назначьте разрешения для Центров событий, чтобы разрешить запись данных в вашу учетную запись Data Lake Store.  

### <a name="create-an-event-hub"></a>Создание концентратора событий

1. Обратите внимание, что концентратор событий должен находиться в той же подписке Azure, что и только что созданная учетная запись Azure Data Lake Store. Создайте концентратор событий, нажав кнопку **Вкл.** в разделе **Запись** на странице портала **Создание концентратора событий**. 
2. На странице портала **Создание концентратора событий** в поле **поставщика записи** выберите **Azure Data Lake Store**.
3. В поле **Выберите Data Lake Store** укажите учетную запись Data Lake Store, созданную ранее, а в поле **Data Lake Path** (Путь к Data Lake) введите путь к созданной папке данных.

    ![][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Добавление или настройка функции записи для существующего концентратора событий

Функцию "Сбор" можно настроить в существующих концентраторах событий, расположенных в пространствах имен Центров событий. Чтобы включить запись в существующем концентраторе событий или изменить параметры записи, щелкните пространство имен. Появится экран обзора. Затем щелкните концентратор событий, для которого необходимо включить запись или изменить ее параметры. Наконец, на открывшейся странице слева щелкните параметр **записи** и измените параметры записи, как показано на следующих снимках экрана.

### <a name="azure-blob-storage"></a>Хранилище больших двоичных объектов Azure

![][2]

### <a name="azure-data-lake-store"></a>Хранилище озера данных Azure

![][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о функции "Сбор" в Центрах событий см. в [этой статье][capture-overview].
- Функцию "Сбор" в Центрах событий можно также настроить с помощью шаблонов Azure Resource Manager. См. дополнительные сведения [о включении записи с помощью шаблона Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Начало работы с Azure Data Lake Store с помощью портала Azure](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md