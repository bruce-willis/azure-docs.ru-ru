---
title: Устранение неполадок в службе "Экземпляры контейнеров Azure"
description: Подробные сведения об устранении неполадок в службе "Экземпляры контейнеров Azure"
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/19/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 550b53cf40133c8a67306c61cbfa7dae21be4648
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39163779"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Устранение распространенных неполадок с помощью службы "Экземпляры контейнеров Azure"

В этой статье показано, как устранять распространенные неполадки при развертывании контейнеров и управлении ими в службе "Экземпляры контейнеров Azure".

## <a name="naming-conventions"></a>Соглашения об именовании.

При определении спецификации контейнера некоторые параметры требуют соблюдения ограничений на именование. Ниже приведена таблица особых требований для свойств группы контейнеров. Дополнительные сведения о соглашениях об именовании Azure см. в разделе [Правила именования и ограничения][azure-name-restrictions] в центре архитектуры Azure.

| Область | Длина | Регистр | Допустимые знаки | Рекомендуемый шаблон | Пример |
| --- | --- | --- | --- | --- | --- | --- |
| Имя группы контейнеров | От 1 до 64 |Без учета регистра |Буквы, цифры, дефис в любом месте, за исключением первого знака |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Имя контейнера | От 1 до 64 |Без учета регистра |Буквы, цифры, дефис в любом месте, за исключением первого знака |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Порты контейнера | От 1 до 65 535 |Целое число  |Целое число от 1 до 65 535. |`<port-number>` |`443` |
| Метка имени DNS | 5–63 |Без учета регистра |Буквы, цифры, дефис в любом месте, за исключением первого знака |`<name>` |`frontend-site1` |
| Переменная среды | 1–63 |Без учета регистра |Буквы, цифры и подчеркивание (_) в любом месте, за исключением первого знака |`<name>` |`MY_VARIABLE` |
| Имя тома | 5–63 |Без учета регистра |Буквы в нижнем регистре, цифры и дефисы в любом месте, кроме первого или последнего знака. Не может содержать два дефиса подряд. |`<name>` |`batch-output-volume` |

## <a name="os-version-of-image-not-supported"></a>Версия ОС образа не поддерживается

Если вы укажете образ, не поддерживаемый службой "Экземпляры контейнеров Azure", будет возвращена ошибка `OsVersionNotSupported`. Ошибка похожа на следующую, где `{0}` — это имя образа, который вы пытались развернуть:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Эта ошибка часто возникает при развертывании образов Windows, основанных на выпуске Semi-Annual Channel (SAC). Например, версии Windows 1709 и 1803 являются выпусками SAC и вызывают такую ошибку при развертывании.

Служба "Экземпляры контейнеров Azure" поддерживает образы Windows на основе версий канала долгосрочного обслуживания (LTSC). Чтобы устранить эту проблему, при развертывании контейнеров Windows всегда следует развертывать образы на основе LTSC.

Дополнительные сведения о версиях LTSC и SAC Windows см. в статье [Обзор Semi-Annual Channel для Windows Server][windows-sac-overview].

## <a name="unable-to-pull-image"></a>Сбой получения образа

Если службе "Экземпляры контейнеров Azure" не удалось изначально получить образ, в течение некоторого периода она будет повторять эту операцию. Если операция по извлечению образа продолжает завершаться сбоем, ACI в конечном итоге не выполняет развертывание, и вы можете увидеть ошибку `Failed to pull image`.

Чтобы устранить эту проблему, удалите экземпляр контейнера и повторите развертывание. Убедитесь, что образ существует в реестре и что имя образа введено верно.

Если образ невозможно получить, в выходных данных команды [az container show][az-container-show] отображаются следующие события:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"microsoft/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"microsoft/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

## <a name="container-continually-exits-and-restarts"></a>Контейнер постоянно завершает работу и перезагружается

Если контейнер завершает работу и автоматически перезагружается, возможно, вам нужно настроить [политику перезагрузки](container-instances-restart-policy.md) **OnFailure** или **Never**. Если вы настроили **OnFailure**, но контейнер по-прежнему перезагружается, возможно, существует проблема в выполняемом в контейнере приложении или скрипте.

В API службы экземпляров контейнеров есть свойство `restartCount`. Чтобы узнать количество перезагрузок контейнера, выполните в Azure CLI команду [az container show][az-container-show]. Ниже приведен пример выходных данных (сокращены для удобства), в конце которого можно увидеть свойство `restartCount`.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> Большинство образов контейнера для дистрибутивов Linux настраивают оболочку, например bash, в качестве команды по умолчанию. Оболочка сама по себе не является долго работающей службой. Поэтому, если для этих контейнеров настроена политика перезагрузки **Always**, они сразу же завершают работу и начинают циклически перезагружаться.

## <a name="container-takes-a-long-time-to-start"></a>Контейнер долго запускается

Вот два основных фактора, влияющих на время запуска контейнера в экземплярах контейнеров Azure:

* [Размер образа](#image-size)
* [Расположение образа](#image-location)

Для образов Windows действуют [дополнительные рекомендации](#cached-windows-images).

### <a name="image-size"></a>Размер образа

Если контейнер запускается долго, но в конечном итоге успешно, обратите внимание на размер образа контейнера. Так как служба "Экземпляры контейнеров Azure" извлекает образ контейнера по требованию, время запуска непосредственно связано с его размером.

Размер образа контейнера можно просмотреть с помощью команды `docker images` в интерфейсе командной строки Docker:

```console
$ docker images
REPOSITORY                  TAG       IMAGE ID        CREATED        SIZE
microsoft/aci-helloworld    latest    7f78509b568e    13 days ago    68.1MB
```

Чтобы обеспечить маленький размер образа, нужно добавить в него только то, что требуется во время выполнения. Один из способов — это [многоэтапная сборка][docker-multi-stage-builds]. За счет многоэтапной сборки можно очень просто гарантировать, что итоговый образ содержит артефакты, необходимые для приложения, и в нем отсутствует дополнительное содержимое, которое требовалось во время сборки.

### <a name="image-location"></a>Расположение образа

Чтобы получение образа меньше влияло на время запуска контейнера, вы также можете разместить образ контейнера в [реестре контейнеров Azure](/azure/container-registry/) того же региона, в котором будут развернуты экземпляры контейнеров. Таким образом сокращается сетевой путь, который должен пройти образ контейнера, а также значительно сокращается время загрузки.

### <a name="cached-windows-images"></a>Кэшированные образы Windows

Для образов на основе конкретных образов Windows экземпляры контейнеров Azure используют механизм кэширования, чтобы сократить время запуска контейнера.

Чтобы ускорить запуск, используйте для контейнера Windows одну из **трех последних версий** следующих **двух образов**:

* [Windows Server 2016][docker-hub-windows-core] (только LTS)
* [Nano Server Windows Server 2016][docker-hub-windows-nano]

### <a name="windows-containers-slow-network-readiness"></a>Контейнеры Windows замедляют период готовности сети

В контейнерах Windows при первоначальном создании в течение 5 секунд может отсутствовать входящее или исходящее соединение. После начальной установки работа с контейнерами в сети будет восстановлена соответствующим образом.

## <a name="resource-not-available-error"></a>Ошибка при недоступном ресурсе

Из-за изменения нагрузки региональных ресурсов в Azure при попытке развернуть экземпляр контейнера может появляться следующая ошибка:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Эта ошибка означает, что из-за высокой нагрузки в регионе, в котором вы пытаетесь выполнить развертывание, сейчас не удается выделить ресурсы, указанные для контейнера. Чтобы устранить эту проблему, используйте один или несколько способов, указанных ниже.

* Убедитесь, что параметры развертывания контейнера соответствуют параметрам, определенным в статье [Квоты и доступность по регионам для службы "Экземпляры контейнеров Azure"](container-instances-quotas.md#region-availability).
* Укажите для контейнера более низкие значения ЦП и памяти.
* Выполните развертывание в другом регионе Azure.
* Выполните развертывание позднее.

## <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Не удается подключиться к основному API Docker или запустить привилегированные контейнеры.

Служба "Экземпляры контейнеров Azure" не предоставляет прямой доступ к базовой инфраструктуре, в которой размещены группы контейнеров. Это включает в себя доступ к API Docker, запущенному на узле контейнера, и выполнение привилегированных контейнеров. Если требуется взаимодействие с Docker, обратитесь к [справочной документации по REST](https://aka.ms/aci/rest), чтобы узнать о поддерживаемых API в службе "Экземпляры контейнеров Azure". Также можно отправить запрос на [форумах обратной связи ACI](https://aka.ms/aci/feedback).

## <a name="next-steps"></a>Дополнительная информация
Узнайте, как [получить журналы контейнеров и события](container-instances-get-logs.md), чтобы помочь в отладке контейнеров.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az_container_show
