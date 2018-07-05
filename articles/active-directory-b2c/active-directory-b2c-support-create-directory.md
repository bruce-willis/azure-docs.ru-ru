---
title: Устранение неполадок при создании клиентов в Azure Active Directory B2C | Документация Майкрософт
description: В этой статье описаны проблемы и их решения при создании клиента Azure Active Directory или клиента Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 009f7ac2f7e614b7e07623e41888973f1a2b254d
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440988"
---
# <a name="troubleshoot-creating-an-azure-active-directory-or-azure-active-directory-b2c-tenant"></a>Устранение неполадок при создании клиентов Azure Active Directory или Azure Active Directory B2C 

## <a name="create-an-azure-ad-tenant"></a>Создание клиента Azure AD
Если вам не удается создать клиент Azure Active Directory (Azure AD) с первого раза, попробуйте сделать это еще раз. Если проблема не исчезла, обратитесь в службу поддержки Azure.

## <a name="create-an-azure-ad-b2c-tenant"></a>Создание клиента Azure AD B2C
При возникновении проблем во время [создания клиента Azure Active Directory B2C](active-directory-b2c-get-started.md) попробуйте сделать следующее.

* Если клиент Azure AD B2C не отображается в списке клиентов, повторите попытку создания клиента.
* Если клиент Azure AD B2C не отображается в списке клиентов и появляется указанное ниже сообщение об ошибке, удалите клиента и создайте его заново.

    "Could not complete the creation of the B2C tenant 'contosob2c'. Please visit this [link](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) for more guidance." (Не удалось завершить создание клиента B2C contosob2c. Щелкните ссылку, чтобы получить дополнительные рекомендации).
* При удалении существующего клиента Azure AD B2C и его повторном создании с тем же доменным именем могут возникнуть известные проблемы. При создании клиента Azure AD B2C необходимо использовать другое доменное имя.
* Если ни одно из приведенных решений не помогло, обратитесь в службу поддержки Azure. Дополнительные сведения см. в статье [Azure Active Directory B2C: регистрация запросов в службу поддержки](active-directory-b2c-support.md).

