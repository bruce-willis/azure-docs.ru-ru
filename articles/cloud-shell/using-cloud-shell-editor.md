---
title: Использование редактора Azure Cloud Shell | Документация Майкрософт
description: Общие сведения о том, как использовать редактор Azure Cloud Shell.
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: juluk
ms.openlocfilehash: caf6e18a9a30654710f5445ed6ab957a5253d62e
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259677"
---
# <a name="using-the-azure-cloud-shell-editor"></a>Использование редактора Azure Cloud Shell

Azure Cloud Shell включает в себя встроенный редактор файлов, созданный на основе [редактора Monaco](https://github.com/Microsoft/monaco-editor) с открытым исходным кодом. Редактор Cloud Shell поддерживает такие функции, как выделение кода, палитра команд и обозреватель файлов.

![Редактор Cloud Shell](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>Открытие редактора

Для простого создания и редактирования файлов запустите редактор, выполнив команду `code .` в терминале Cloud Shell. Это действие открывает редактор с вашей активной рабочей папкой, настроенной в терминале.

Чтобы напрямую открыть файл для быстрого редактирования, запустите `code <filename>` для открытия редактора без обозревателя файлов.

Чтобы открыть редактор с помощью кнопки в пользовательском интерфейсе, щелкните значок редактора `{}` на панели инструментов. Откроется редактор, и в обозревателе файлов по умолчанию будет установлен каталог `/home/<user>`.

## <a name="closing-the-editor"></a>Закрытие редактора

Чтобы закрыть редактор, откройте панель действий `...` в верхней правой части окна редактора и выберите `Close editor`.

![Закрытие редактора](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>Палитра команд

Чтобы запустить палитру команду, используйте ключ `F1`, когда фокус установлен на редакторе. Открыть палитру команд также можно на панели действий.

![Палитра команд](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Участие в развитии редактора Monaco

Поддержка выделения кода в редакторе Cloud Shell реализуется с помощью функций верхнего уровня при использовании определений синтаксиса Monarch в [редакторе Monaco](https://github.com/Microsoft/monaco-editor). Чтобы узнать, как внести свой вклад, перейдите к этому [руководству по Monaco](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md).

## <a name="next-steps"></a>Дополнительная информация
[Краткое руководство по Bash в Azure Cloud Shell](quickstart.md)
[Функции и средства для Azure Cloud Shell](features.md)