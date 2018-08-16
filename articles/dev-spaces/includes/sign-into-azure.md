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
ms.openlocfilehash: 875dd9d768b5f40f2d4ed3fad5b14a6cd6f6f6ba
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129368"
---
### <a name="sign-in-to-azure-cli"></a>Вход в Azure CLI
Войдите в Azure. В окне терминала введите следующую команду:

```cmd
az login
```

> [!Note]
> Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free).

#### <a name="if-you-have-multiple-azure-subscriptions"></a>Если у вас несколько подписок Azure...
Можно просматривать свои подписки, выполнив следующую команду: 

```cmd
az account list
```
Найдите подписку с `isDefault: true` в выходных данных JSON.
Если это не та подписка, которую нужно использовать, вы можете изменить подписку по умолчанию:

```cmd
az account set --subscription <subscription ID>
```
