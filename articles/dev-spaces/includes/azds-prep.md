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
ms.openlocfilehash: 5ff41442d32739763332fa9bab59543ea3c7804a
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129356"
---
## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Подготовка кода для разработки Docker и Kubernetes
На данный момент у вас есть базовое веб-приложение, которое можно запустить локально. Теперь вы упакуете его в контейнер, создав ресурсы, определяющие контейнер приложения и способ его развертывания в Kubernetes. Эту задачу легко выполнить с помощью Azure Dev Spaces: 

1. Запустите VS Code и откройте папку `webfrontend`. (Вы можете игнорировать любые запросы по умолчанию на добавление ресурсов отладки или восстановления проекта.)
1. Откройте встроенный терминал в VS Code (выберите **Представление > Интегрированный терминал**).
1. Запустите эту команду (убедитесь, что ваша текущая папка — **webfrontend**):

    ```cmd
    azds prep --public
    ```

Команда `azds prep` Azure CLI создает ресурсы Docker и Kubernetes с параметрами по умолчанию:
* `./Dockerfile` описывает образ контейнера приложения и способ создания и запуска исходного кода в контейнере.
* В [диаграмме Helm](https://docs.helm.sh) в разделе `./charts/webfrontend` описано, как развернуть контейнер в Kubernetes.

Сейчас изучать все содержимое этих файлов не требуется. Однако стоит отметить, что **те же ресурсы Kubernetes и Docker конфигурации как кода, могут использоваться для разработки и производства, обеспечивая тем самым лучшую согласованность в разных средах.**
 
С помощью команды `prep` также создается файл `./azds.yaml`. Он является файлом конфигурации для Azure Dev Spaces. Он предоставляет артефактам Docker и Kubernetes дополнительную конфигурацию, которая обеспечивает последовательную разработку в Azure.
