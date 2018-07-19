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
ms.openlocfilehash: 2f2177b289ba9d13ba635bddcd61748e1e8ab0d1
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39063048"
---
### <a name="run-the-service"></a>Запуск службы

1. Чтобы запустить службу, нажмите клавишу F5 (или введите `azds up` в окне терминала). Служба автоматически запустится в выбранной среде `default/scott`. 
1. Убедитесь, что служба запущена в своей среде, еще раз выполнив команду `azds list-up`. Во-первых, вы увидите, что экземпляр `mywebapi` теперь выполняется в среде `default/scott` (версия, запущенная в `default`, по-прежнему выполняется, но не отображается). Во-вторых, URL-адрес точки доступа для `webfrontend` имеет префикс с текстом "scott.s.". Этот URL-адрес является уникальным для среды `default/scott`. Он указывает на то, что запросы, отправленные на URL-адрес scott, сначала будут направляться к службам в среде `default/scott`, а при сбое возвратятся к службам в среде `default`.

```
Name         Space          Chart              Ports   Updated     Access Points
-----------  --------       -----------------  ------  ----------  -------------
mywebapi     default/scott  mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default        webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Эта встроенная возможность Azure Dev Spaces позволяет выполнять тестирование кода в общей среде, не требуя от каждого разработчика повторно создавать множество служб в своем пространстве. Для такой маршрутизации требуется, чтобы код приложения отправлял заголовки распространения, как показано на предыдущем шаге этого руководства.

### <a name="test-code-in-a-space"></a>Проверка кода в среде
Чтобы проверить свою новую версию `mywebapi` в сочетании с `webfrontend`, откройте в браузере URL-адрес общедоступной точки доступа `webfrontend` и перейдите на страницу About (Сведения). Отобразится новое сообщение.

Теперь удалите часть "scott.s" из URL-адреса и обновите браузер. Снова будет наблюдаться старое поведение (версия `mywebapi`, запущенная в `default`).
