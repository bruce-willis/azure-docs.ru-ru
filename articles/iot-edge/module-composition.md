---
title: Составление модулей Azure IoT Edge | Документация Майкрософт
description: Узнайте, как в манифесте развертывания объявляются развертываемые модули и способы их развертывания и как создать маршруты для передачи сообщений между этими модулями.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: af4a831c084ae10b381b8e08fd0ce4798b21b394
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382177"
---
# <a name="learn-how-to-use-deployment-manifests-to-deploy-modules-and-establish-routes"></a>Сведения об использовании манифестов развертывания для развертывания модулей и установки маршрутов

На каждом устройстве IoT Edge работает по меньшей мере два модуля – $edgeAgent и $edgeHub, составляющие среду выполнения IoT Edge. Помимо этих двух стандартных модулей на любом устройстве IoT Edge можно запустить множество других модулей для выполнения любого числа процессов. При одновременном развертывании этих модулей на устройстве необходим способ объявления о том, какие модули включены и каким образом они взаимодействуют друг с другом. 

*Манифест развертывания* — это документ JSON, который описывает:

* конфигурацию агента Edge, включая образ контейнера для каждого модуля, учетные данные для доступа к частным реестрам контейнеров и инструкции по созданию и администрированию каждого модуля;
* конфигурацию концентратора Edge, которая описывает поток сообщений между модулями и в конечном итоге к Центру Интернета вещей;
* (необязательно) требуемые свойства двойников модулей.

Все устройства IoT Edge должны быть настроены с помощью манифеста развертывания. Недавно установленная среда выполнения IoT Edge будет сообщать код ошибки, пока не будет настроена с помощью допустимого манифеста. 

В руководствах по Azure IoT Edge манифест развертывания создается с помощью мастера на портале Azure IoT Edge. Также с помощью REST или пакета SDK службы Центра Интернета вещей можно применить манифест развертывания программно. Дополнительные сведения см. в статье [Основные сведения о развертываниях IoT Edge для отдельных устройств или в требуемом масштабе (предварительная версия)][lnk-deploy].

## <a name="create-a-deployment-manifest"></a>Создание манифеста развертывания

На высоком уровне манифест развертывания настраивает требуемые свойства двойника модуля для развернутых модулей IoT Edge на устройстве IoT Edge. Два из этих модулей всегда присутствуют: `$edgeAgent` и `$edgeHub`.

Допустимым является только тот манифест развертывания, который содержит среду выполнения IoT Edge (агент и концентратор).

Манифест имеет следующую структуру:

```json
{
    "modulesContent": {
        "$edgeAgent": {
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": {
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "{module1}": {  // optional
            "properties.desired": {
                // desired properties of module with id {module1}
            }
        },
        "{module2}": {  // optional
            ...
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Настройка модулей

Для среды выполнения IoT Edge необходимо указать способ установки модулей в развертывании. Сведения по управлению и настройке всех модулей добавляются в требуемые свойства **$edgeAgent**. Сюда также входят параметры настройки самого агента Edge. 

Полный список свойств, которые могут или должны быть включены, см. в статье [Свойства двойников модулей EdgeAgent и EdgeHub](module-edgeagent-edgehub.md).

Свойства $edgeAgent имеют такую структуру:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
            "settings":{
                "registryCredentials":{ // give the edge agent access to container images that aren't public
                    }
                }
            }
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "{module1}": { // optional
                // configuration and management details
            },
            "{module2}": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Объявление маршрутов

Концентратор Edge предоставляет способ декларативно маршрутизировать сообщения между модулями, а также между модулями и Центром Интернета вещей. Концентратор Edge управляет всем обменом данными, так что информация о маршруте содержится в требуемых свойствах **$edgeHub**. Один экземпляр развертывания может иметь несколько маршрутов.

Маршруты объявляются в требуемых свойствах **$edgeHub**. Синтаксис объявления следующий:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "{route1}": "FROM <source> WHERE <condition> INTO <sink>",
            "{route2}": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Каждый маршрут имеет исходную и конечную точки. При необходимости можно также добавить условие, чтобы фильтровать сообщения. 


### <a name="source"></a>Источник
Исходная точка определяет то место, откуда поступают сообщения. В качестве такой точки может выступать любой из следующих вариантов:

| Источник | ОПИСАНИЕ |
| ------ | ----------- |
| `/*` | Все сообщения с устройства в облако из любого устройства или модуля. |
| `/messages/*` | Все сообщения с устройства в облако, отправленные устройством или модулем с некоторыми выходными данными или без них. |
| `/messages/modules/*` | Все сообщения с устройства в облако, отправленные модулем с некоторыми выходными данными или без них. |
| `/messages/modules/{moduleId}/*` | Все сообщения с устройства в облако, отправленные {ИД_модуля} без выходных данных. |
| `/messages/modules/{moduleId}/outputs/*` | Все сообщения с устройства в облако, отправленные {ИД_модуля} с некоторыми выходными данными. |
| `/messages/modules/{moduleId}/outputs/{output}` | Все сообщения с устройства в облако, отправленные {ИД_модуля} с использованием {выходные данные}. |

### <a name="condition"></a>Условие
Условие является необязательным при объявлении маршрута. Если нужно передать все сообщения от конечной точки к исходной, просто не заполняйте предложение **WHERE**. Можно также воспользоваться [языком запросов Центра Интернета вещей][lnk-iothub-query] для фильтрации определенных сообщений или типов сообщений, удовлетворяющих заданному условию.

Сообщения, которые проходят между модулями в IoT Edge, форматируются так же, как сообщения, которые проходят между вашими устройствами и Центром Интернета вещей. Все сообщения представлены в формате JSON с параметрами **systemProperties**, **appProperties** и **body**. 

Запросы для всех трех параметров создаются с помощью следующего синтаксиса: 

* свойства системы — `$<propertyName>` или `{$<propertyName>}`;
* свойства приложения — `<propertyName>`;
* свойства текста —`$body.<propertyName>`. 

Примеры создания запросов к свойствам сообщений см. в разделе [Выражения запросов по маршрутам сообщений, отправляемых с устройства в облако](../iot-hub/iot-hub-devguide-query-language.md#device-to-cloud-message-routes-query-expressions).

Характерным примером для IoT Edge является ситуация, когда вам нужно выполнить фильтрацию для сообщений, поступивших на устройство шлюза с конечного устройства. Поступающие от модулей сообщения содержат системное свойство **connectionModuleId**. Так что, если нужно направить сообщения от конечных устройств к Центру Интернета вещей, используйте следующий маршрут, чтобы исключить сообщения от модулей.

```sql
FROM /messages/\* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Приемник
Конечная точка определяет пункт назначения отправленных сообщений. В качестве такой точки может выступать любой из следующих вариантов:

| Приемник | ОПИСАНИЕ |
| ---- | ----------- |
| `$upstream` | Отправляет сообщение в Центр Интернета вещей. |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | Отправляет сообщение во входные данные `{input}` модуля `{moduleId}`. |

IoT Edge предоставляет гарантии как минимум однократной доставки. Если не удается доставить сообщение в приемник по маршруту, центр IoT Edge сохраняет сообщение локально. Например, если центр IoT Edge не может подключиться к Центру Интернета вещей или не подключен целевой модуль.

Концентратор Edge хранит сообщения до окончания срока хранения, указанного в свойстве `storeAndForwardConfiguration.timeToLiveSecs` [требуемых свойств концентратора Edge](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Определение или обновление требуемых свойств 

В манифесте развертывания можно указать требуемые свойства для двойника каждого модуля, развернутого на устройстве IoT Edge. Если требуемые свойства заданы в манифесте развертывания, они перезаписывают любые требуемые свойства, находящиеся на данный момент в двойнике модуля.

Если не указать требуемые свойства двойника модуля в манифесте развертывания, Центр Интернета вещей не будет никоим образом изменять двойник модуля. Вы сможете задать требуемые свойства программно.

Для изменения двойников модуля используются те же механизмы, что и для двойников устройства. Дополнительные сведения см. в статье [Общие сведения о двойниках устройств и их использование в Центре Интернета вещей](../iot-hub/iot-hub-devguide-device-twins.md).   

## <a name="deployment-manifest-example"></a>Пример манифеста развертывания

Этот пример документа JSON манифеста развертывания.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "{password}",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": ""
            }
          }
        },
        "modules": {
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Дополнительная информация

* Полный список свойств, которые могут или должны быть у $edgeAgent и $edgeHub, см. в статье [Свойства двойников модулей EdgeAgent и EdgeHub](module-edgeagent-edgehub.md).

* Теперь, когда вы знаете, как используются модули IoT Edge, ознакомьтесь со статьей [Understand the requirements and tools for developing IoT Edge modules - preview][lnk-module-dev] (Общие сведения о требованиях и средствах разработки модулей IoT Edge (предварительная версия)).

[lnk-deploy]: module-deployment-monitoring.md
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
