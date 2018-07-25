---
title: Azure Cloud Shell для пользователей Windows | Документация Майкрософт
description: Руководство для пользователей, не знакомых с системами Linux
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/03/2018
ms.author: damaerte
ms.openlocfilehash: 5e318a0f64033aa0c4b306e547c11e1994afa229
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37861471"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>PowerShell в Azure Cloud Shell для пользователей Windows

В мае 2018 года были [объявлены](https://azure.microsoft.com/blog/pscloudshellrefresh/) изменения для PowerShell в Azure Cloud Shell.  Теперь работа с PowerShell в Azure Cloud Shell выполняется на PowerShell Core 6 в Linux.
В результате этого изменения некоторые аспекты поведения PowerShell в Cloud Shell отличаются от того, что вам знакомо по Windows PowerShell 5.1.

## <a name="case-sensitivity"></a>Чувствительность к регистру

В Windows не учитывается регистр для путей в файловой системе.  В Linux файловая система учитывает регистр.
Это означает, что `file.txt` и `FILE.txt` ранее считались одним и тем же файлом, но теперь будут разными.
Необходимо использовать правильный регистр для завершения `tab` в файловой системе.  Та часть синтаксиса, которая обрабатывается PowerShell, например командлеты `tab`, используется без учета регистра. 

## <a name="windows-powershell-alias-vs-linux-utilities"></a>Псевдонимы Windows PowerShell и служебные программы Linux

Существующие команды в Linux, такие как `ls`, `sort` и `sleep` имеют приоритет над одноименными псевдонимами PowerShell.  Ниже приведены распространенные псевдонимы, которые теперь удаляются, и эквивалентные им команды.  

|Удаленный псевдоним   |Эквивалентная команда   |
|---|---|
|`ls`     | `dir` <br> `Get-ChildItem` |
|`sort`   | `Sort-Object` |
|`sleep`  | `Start-Sleep` |

## <a name="persisting-home-vs-homeclouddrive"></a>Сохраняемая переменная $home и $home\clouddrive

Для пользователей, которые использовали на облачном диске сохраняемые скрипты и другие файлы, теперь между сеансами сохраняется значение домашнего каталога $HOME.

## <a name="powershell-profile"></a>Профиль PowerShell

По умолчанию профиль PowerShell не создается.  Чтобы создать профиль, создайте каталог `PowerShell` в `$HOME/.config`.  В `$HOME/.config/PowerShell` вы сможете создать свой профиль с именем `Microsoft.PowerShell_profile.ps1`.

## <a name="whats-new-in-powershell-core-6"></a>Новые возможности в PowerShell Core 6

Дополнительные сведения о новинках в PowerShell Core 6 приводятся в [документации по PowerShell](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6) и в записи блога [Getting Started with PowerShell Core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/) (Приступая к работе с PowerShell Core).
