---
title: Запуск Функций Azure из пакета | Документация Майкрософт
description: Настройте запуск функций в среде выполнения Функций Azure путем подключения файла пакета развертывания, содержащего файлы проекта приложения-функции.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
editor: ''
tags: ''
ms.service: functions
ms.devlang: multiple
ms.topic: conceptual
ms.workload: na
ms.date: 08/22/2018
ms.author: glenga
ms.openlocfilehash: 273b1a84b5ebe5f5751a05ef24e4721e7226bca6
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819543"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Запуск функций Azure из файла пакета

> [!NOTE]
> Функциональные возможности, описываемые в этой статье, в настоящее время находятся в предварительной версии. Они недоступны для функций в Linux.

В Azure можно запустить функции непосредственно из файла пакета развертывания в приложении-функции. Другой вариант — развернуть файлы проекта функции в каталоге `d:\home\site\wwwroot` приложения-функции.

В этой статье описываются преимущества запуска функций из пакета. Здесь также показано, как включить эту функцию в приложении-функции.

## <a name="benefits-of-running-from-a-package-file"></a>Преимущества запуска из файла пакета
  
Запуск функции из файла пакета обеспечивает несколько преимуществ:

+ Снижается риск возникновения проблем из-за блокировки копирования файлов.
+ Можно выполнить развертывание в рабочее приложение (с помощью перезапуска).
+ Обеспечивается надежность файлов, выполняемых в приложении.
+ Повышается производительность [развертываний Azure Resource Manager](functions-infrastructure-as-code.md).
+ Может уменьшаться время "холодного запуска" функций JavaScript.

Дополнительные сведения см. в [этом объявлении](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Настройка запуска функций из пакета

Чтобы настроить запуск приложения-функции из пакета, нужно просто добавить `WEBSITE_RUN_FROM_ZIP` в параметрах приложения-функции. Параметр `WEBSITE_RUN_FROM_ZIP` может иметь одно из следующих значений:

| Значение  | ОПИСАНИЕ  |
|---------|---------|
|**`<url>`**  | Расположение определенного файла пакета, который вы хотите запустить. При использовании хранилища BLOB-объектов следует использовать закрытый контейнер с [подписанным URL-адресом (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-a-storage-account-by-using-a-shared-access-signature-sas), чтобы настроить доступ к пакету в среде выполнения функций. Можно использовать [Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/) для передачи файлов пакета в учетную запись хранения больших двоичных объектов.         |
| **`1`**  | Запуск из файла пакета в папке `d:\home\data\SitePackages` приложения-функции. В этом случае требуется, чтобы в папке также был файл с именем `packagename.txt`. Этот файл содержит только имя файла пакета в папке без каких-либо пробелов. |

Далее показано приложение-функция, настроенное для запуска из ZIP-файла, размещенного в хранилище BLOB-объектов Azure:

![Параметр приложения WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> В настоящее время поддерживаются только ZIP-файлы пакета.

## <a name="integration-with-zip-deployment"></a>Интеграция с помощью развертывания из ZIP-файла

[Развертывание из ZIP-файла][Zip deployment for Azure Functions] — это функция Службы приложений Azure, которая позволяет развертывать проект приложения-функции в каталог `wwwroot`. Проект упакован как ZIP-файл развертывания. Те же API можно использовать для развертывания пакета в папку `d:\home\data\SitePackages`. Если для параметра приложения `WEBSITE_RUN_FROM_ZIP` задано значение `1`, API-интерфейсы развертывания из ZIP-файла копируют пакет в папку `d:\home\data\SitePackages`, а не извлекают файлы в `d:\home\site\wwwroot`. Также создается файл `packagename.txt`. Затем после перезагрузки приложение-функция запускается из пакета и `wwwroot` становится доступным только для чтения. Дополнительные сведения о развертывании из ZIP-файла см. в статье [Непрерывное развертывание Функций Azure из ZIP-файла](deployment-zip-push.md).

## <a name="adding-the-websiterunfromzip-setting"></a>Добавление параметра WEBSITE_RUN_FROM_ZIP

[!ДОБАВИТЬ [Параметры приложения-функции](../../includes/functions-app-settings.md]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Непрерывное развертывание для функций Azure](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md