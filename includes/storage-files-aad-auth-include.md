---
title: включение файла
description: включение файла
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 903074c78180ab2cd755abcf4207232f2851804e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47019699"
---
[Служба файлов Azure](../articles/storage/files/storage-files-introduction.md) поддерживает проверку подлинности на основе удостоверений по протоколу SMB (Server Message Block) (предварительная версия) через [доменные службы Azure Active Directory (Azure AD)](../articles/active-directory-domain-services/active-directory-ds-overview.md). Присоединенные к домену виртуальные машины Windows имеют доступ к файловым ресурсам Azure с помощью учетных данных [Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md). 

Azure AD проверяет подлинность удостоверения, например пользователя, группы или субъекта-службы, с помощью [управления доступом на основе ролей (RBAC)](../articles/role-based-access-control/overview.md). Можно определить пользовательские роли RBAC, которые распространяются на общие наборы разрешений, используемых для доступа к службе файлов Azure. При назначении пользовательской роли RBAC удостоверению Azure AD этому удостоверению предоставляется доступ к общей папке Azure в соответствии с этими разрешениями.

В рамках предварительной версии служба файлов Azure также поддерживает сохранение, наследование и применение [списков DACL NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) ко всем файлам и каталогам в общей папке. При копировании данных из общей папки в службу файлов Azure или наоборот можно указать, что поддерживаются списки DACL NTFS. Таким образом, можно реализовать сценарии резервного копирования, использующие службу файлов Azure, сохраняя списки DACL NTFS между локальной и облачной общими папками. 

> [!NOTE]
> В предварительной версии проверка подлинности Azure AD по протоколу SMB не поддерживается для виртуальных машин Linux. Поддерживаются только виртуальные машины Windows.
>
> Проверка подлинности Azure AD доступна только для учетных записей хранения, созданных после 24 сентября 2018 г.
