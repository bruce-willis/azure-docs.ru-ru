---
title: Добавление пользовательского сертификата ЦС — служба управления API Azure | Документация Майкрософт
description: Узнайте, как добавить пользовательский сертификат ЦС в службе управления API Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: 9d3399ba6ee724d91117486744ad1431f53edbce
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053268"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Добавление пользовательского сертификата ЦС в службе управления API Azure

Служба управления API Azure позволяет выполнять установку сертификатов ЦС на компьютере внутри доверенных корневых и промежуточных хранилищ сертификатов. Эту функциональность следует использовать, если вашим службам требуется пользовательский сертификат ЦС.

В этом руководстве описывается, как на портале Azure управлять сертификатами в экземпляре службы управления API Azure.

## <a name="step1"> </a>Передача сертификата ЦС

![Добавление сертификатов ЦС](media/api-management-howto-ca-certificates/00.png)

Чтобы передать новый сертификат ЦС, выполните действия ниже. Если экземпляр службы управления API еще не создан, выполните инструкции из руководства [Создание экземпляра службы управления API Azure](get-started-create-service-instance.md).

1. На портале Azure перейдите к экземпляру службы управления API Azure.

2. В меню выберите **Сертификаты ЦС**.

3. Щелкните **+ Добавить**.  

    ![Добавление сертификатов ЦС](media/api-management-howto-ca-certificates/01.png)  

4. Перейдите к сертификату и выберите хранилище сертификатов. Нужен только открытый ключ, поэтому пароль не требуется.

    ![Добавление сертификатов ЦС](media/api-management-howto-ca-certificates/02.png)  

5. Выберите команду **Сохранить**. Это может занять несколько минут.

    ![Добавление сертификатов ЦС](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Вы можете отправить сертификат ЦС с помощью команды PowerShell `New-AzureRmApiManagementSystemCertificate`.

## <a name="step1a"> </a>Удаление сертификата клиента

Чтобы удалить сертификат, щелкните контекстное меню **...** и выберите **Удалить** рядом с именем сертификата.

![Удаление сертификатов ЦС](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a