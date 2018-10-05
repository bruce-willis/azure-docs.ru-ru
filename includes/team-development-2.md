---
title: включение файла
description: включение файла
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 8580e19f1955becb2cfaee13742054af1a9edb7f
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410458"
---
### <a name="run-the-service"></a>Запуск службы

1. Чтобы запустить службу, нажмите клавишу F5 (или введите `azds up` в окне терминала). Служба автоматически запустится в выбранной среде `default/scott`. 
1. Убедитесь, что служба запущена в своей среде, еще раз выполнив команду `azds list-up`. Во-первых, вы увидите, что экземпляр `mywebapi` теперь выполняется в среде `default/scott` (версия, запущенная в `default`, по-прежнему выполняется, но не отображается). Если выполнить `azds list-uris`, то можно отметить, что URL-адрес точки доступа для `webfrontend` имеет префикс с текстом "scott.s.". Этот URL-адрес является уникальным для среды `default/scott`. Он указывает на то, что запросы, отправленные на URL-адрес scott, сначала будут направляться к службам в среде `default/scott`, а при сбое возвратятся к службам в среде `default`.

```
Name                      DevSpace  Type     Updated  Status
------------------------  --------  -------  -------  -------
mywebapi                  scott     Service  3m ago   Running
mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
webfrontend               default   Service  26m ago  Running
```

```
Uri                                                            Status
-------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                    Tunneled
http://webfrontend.6364744826e042319629.canadaeast.aksapp.io/  Available
```

![](../articles/dev-spaces/media/common/space-routing.png)

Эта встроенная возможность Azure Dev Spaces позволяет выполнять тестирование кода в общей среде, не требуя от каждого разработчика повторно создавать множество служб в своем пространстве. Для такой маршрутизации требуется, чтобы код приложения отправлял заголовки распространения, как показано на предыдущем шаге этого руководства.

### <a name="test-code-in-a-space"></a>Проверка кода в среде
Чтобы проверить свою новую версию `mywebapi` в сочетании с `webfrontend`, откройте в браузере URL-адрес общедоступной точки доступа `webfrontend` и перейдите на страницу About (Сведения). Отобразится новое сообщение.

Теперь удалите часть "scott.s" из URL-адреса и обновите браузер. Снова будет наблюдаться старое поведение (версия `mywebapi`, запущенная в `default`).
