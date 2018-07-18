---
title: 'Интерфейс командной строки Azure Service Fabric: sfctl container | Документация Майкрософт'
description: Описание команд sfctl container интерфейса командной строки Azure Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: cd3725ac547a1ed1fd9207dc48ba3b6227e85ef1
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763982"
---
# <a name="sfctl-container"></a>Контейнер sfctl
Команды, связанные с контейнером, следует выполнять в узле кластера.

## <a name="commands"></a>Команды

|Get-Help|ОПИСАНИЕ|
| --- | --- |
| invoke-api | Вызов интерфейса REST API контейнера. |
| журналы | Получение журналов контейнера. |

## <a name="sfctl-container-invoke-api"></a>sfctl container invoke-api
Вызов интерфейса REST API контейнера.

### <a name="arguments"></a>Аргументы

|Аргумент|ОПИСАНИЕ|
| --- | --- |
| --application-id [обязательный параметр] | Удостоверение приложения. |
| --code-package-instance-id [обязательный параметр] | Идентификатор экземпляра пакета кода, который можно получить с помощью команды service code-package-list. |
| --code-package-name [обязательный параметр] | Имя пакета кода. |
| --container-api-uri-path [обязательный параметр] | URI-адрес интерфейса REST API контейнера (вставьте {ID} вместо имени и идентификатора контейнера). |
| --node-name [обязательный параметр] | Имя узла. |
| --service-manifest-name [обязательный параметр] | Имя манифеста службы. |
| --container-api-body | Текст HTTP-запроса к интерфейсу REST API контейнера. |
| --container-api-content-type | Тип содержимого интерфейса REST API контейнера (значение по умолчанию: application/json). |
| --container-api-http-verb | HTTP-команда для интерфейса REST API контейнера (значение по умолчанию: GET). |
| --timeout -t | Время ожидания сервера в секундах.  Значение по умолчанию\: 60. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|ОПИСАНИЕ|
| --- | --- |
| --debug | Увеличение уровня детализации ведения журнала для отображения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат выходных данных.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. здесь: http\://jmespath.org/. |
| --verbose | Повышение уровня детализации ведения журнала. Используйте параметр --debug, чтобы получить полные журналы отладки. |

## <a name="sfctl-container-logs"></a>sfctl container logs
Получение журналов контейнера.

### <a name="arguments"></a>Аргументы

|Аргумент|ОПИСАНИЕ|
| --- | --- |
| --application-id [обязательный параметр] | Удостоверение приложения. |
| --code-package-instance-id [обязательный параметр] | Идентификатор экземпляра пакета кода, который можно получить с помощью команды service code-package-list. |
| --code-package-name [обязательный параметр] | Имя пакета кода. |
| --node-name [обязательный параметр] | Имя узла. |
| --service-manifest-name [обязательный параметр] | Имя манифеста службы. |
| --tail | Возвращает только указанное число строк с конца журналов. Укажите целое число или значение all, чтобы вернуть все строки журналов. Значение по умолчанию: all. |
| --timeout -t | Время ожидания сервера в секундах.  Значение по умолчанию\: 60. |

### <a name="global-arguments"></a>Глобальные аргументы

|Аргумент|ОПИСАНИЕ|
| --- | --- |
| --debug | Увеличение уровня детализации ведения журнала для отображения всех журналов отладки. |
| --help -h | Отображение этого справочного сообщения и выход. |
| --output -o | Формат выходных данных.  Допустимые значения\: json, jsonc, table, tsv.  Значение по умолчанию\: json. |
| --query | Строка запроса JMESPath. Дополнительные сведения и примеры см. здесь: http\://jmespath.org/. |
| --verbose | Повышение уровня детализации ведения журнала. Используйте параметр --debug, чтобы получить полные журналы отладки. |

## <a name="next-steps"></a>Дополнительная информация
- [Настройте](service-fabric-cli.md) интерфейс командной строки Service Fabric.
- Узнайте, как использовать интерфейс командной строки Service Fabric, с помощью [примеров сценариев](/azure/service-fabric/scripts/sfctl-upgrade-application).