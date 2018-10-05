---
title: Настройка встроенного образа Python в службе приложений Azure
description: В этом учебнике описываются возможности создания и настройки приложения Python в службе приложений Azure с помощью встроенного образа Python.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2018
ms.author: astay;cephalin
ms.custom: mvc
ms.openlocfilehash: 9316805993b81e4d2511e833e0cc8f240807a1f9
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228558"
---
# <a name="configure-built-in-python-image-in-azure-app-service-preview"></a>Настройка встроенного образа Python в службе приложений Azure (предварительная версия)

В этой статье показано, как настроить встроенный образ Python в [службу приложений на платформе Linux](app-service-linux-intro.md) для запуска приложений Python.

## <a name="python-version"></a>Версия Python

В службе приложений на платформе Linux используется среда выполнения Python версии `python-3.7.0`.

## <a name="supported-frameworks"></a>Поддерживаемые платформы

В среде выполнения `python-3.7` поддерживаются все совместимые с ней версии веб-платформ Web Server Gateway Interface(WSGI).

## <a name="package-management"></a>Управление пакетами

Во время публикации на Git, подсистема Kudu ищет файл [requirements.txt](https://pip.pypa.io/en/stable/user_guide/#requirements-files) в корне репозитория и с помощью `pip` автоматически устанавливает пакеты в Azure.

Чтобы перед публикацией создать данный файл, необходимо перейти к корневому репозиторию и в среде Python выполнить следующую команду.

```bash
pip freeze > requirements.txt
```

## <a name="configure-your-python-app"></a>Настройка приложения Python

Для запуска приложения Python во встроенном образе Python в службе приложений используется сервер [Gunicorn](http://gunicorn.org/). Gunicorn — это HTTP сервер с интерфейсом WSGI для Python в UNIX. Настройка Gunicorn для проектов Django и Flask выполняется службой приложений автоматически.

### <a name="django-app"></a>Приложение Django

Во время публикации проекта Django, содержащего модуль `wsgi.py`, Azure автоматически вызывает Gunicorn с помощью следующей команды.

```bash
gunicorn <path_to_wsgi>
```

### <a name="flask-app"></a>Приложение Flask

Во время публикации приложения Flask, когда точка входа расположена в модуле `application.py` или `app.py`, Azure автоматически вызывает Gunicorn с помощью одной из следующих команд.

```bash
gunicorn application:app
```

или 

```bash
gunicorn app:app
```

### <a name="customize-start-up"></a>Настройка запуска

Чтобы определить пользовательскую точку входа приложения, сначала с помощью пользовательской команды в Gunicorn необходимо создать файл _.txt_ и поместить его в корень проекта. Например, чтобы сервер сначала запускал модуль _helloworld.py_ и переменную `app`, необходимо создать файл _startup.txt_, который содержит следующий код.

```bash
gunicorn helloworld:app
```

На странице [Параметры приложения](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) выберите **Python|3.7** в качестве **Стек времени выполнения** и введите имя **Загрузочного файла**, используемое на предыдущем шаге. Например _startup.txt_.
