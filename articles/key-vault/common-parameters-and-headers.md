---
title: Распространенные параметры и заголовки
description: В этой статье представлены параметры и заголовки, общие для всех операций, которые можно выполнять для ресурсов Key Vault.
services: key-vault
documentationcenter: ''
author: bryanla
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: a715d13ca9-d6e8-4e54-ac5e-0ed9400fb15b15d13ca9-d6e8-4e54-ac5e-0ed9400fb15b
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: bryanla
ms.openlocfilehash: a319dc670b5b1dab163b2d3aa623fc4fb9ce1c3a
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2018
ms.locfileid: "42143397"
---
# <a name="common-parameters-and-headers"></a>Распространенные параметры и заголовки

Следующие сведения относятся ко всем операциям, которые можно выполнять для ресурсов Key Vault.

- Замена `{api-version}` на api-version в универсальном коде ресурса (URI).
- Замена `{subscription-id}` на идентификатор подписки в URI.
- Замена `{resource-group-name}` на группу ресурсов. Дополнительные сведения см. в статье об использовании групп ресурсов для управления ресурсами Azure.
- Замена `{vault-name}` на имя хранилища ключей в URI.
- Установка значения application/json в качестве заголовка Content-Type.
- Установка веб-маркера JSON, полученного из Azure Active Directory (AAD), в качестве заголовка авторизации. Дополнительные сведения см. в статье [Authentication, requests and responses](authentication-requests-and-responses.md) (Аутентификация, запросы и ответы).

## <a name="common-error-response"></a>Распространенное сообщение об ошибке
Служба будет использовать коды состояния HTTP для указания успеха или сбоя. Кроме того, ошибки содержат ответ в следующем формате:

   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  

|Имя элемента | type | ОПИСАНИЕ |
|---|---|---|
| Код | строка | Тип возникшей ошибки.|
| Message | строка | Описание причины ошибки. |



## <a name="see-also"></a>См. также
 [Azure Key Vault REST API reference](/rest/api/keyvault/) (Справочник по REST API для Azure Key Vault)
