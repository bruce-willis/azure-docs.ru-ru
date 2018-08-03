---
title: Управление ключами конечной точки в LUIS | Документация Майкрософт
description: Используйте Language Understanding (LUIS) для управления программным API, конечными точками и внешними ключами.
titleSuffix: Azure
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: diberry
ms.openlocfilehash: 127c09a022f5efb95ab6a5ec2db0de633b437a54
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223044"
---
# <a name="manage-your-luis-endpoint-keys"></a>Управление ключами конечной точки в LUIS
Ключ позволяет разработать и опубликовать приложение LUIS или отправить запрос к конечной точке. 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
## <a name="key-concepts"></a>Основные понятия
Сведения о разработке приложений LUIS и работе с конечными точками приведены в разделе [Ключи в LUIS](luis-concept-keys.md).

<a name="create-and-use-an-endpoint-key"></a>
## <a name="assign-endpoint-key"></a>Назначение ключа конечной точки
На странице **Публикация приложения** в таблице **Ресурсы и ключи** уже указан ключ. Это ключ разработки (начальный ключ). 

1. Создайте ключ LUIS на [портале Azure](https://portal.azure.com). Дальнейшие инструкции см. в разделе [Управление ключами подписки конечной точки Azure](luis-how-to-azure-subscription.md).
 
2. Чтобы добавить ключ LUIS, созданный на предыдущем шаге, нажмите кнопку **Добавить ключ** и открыть диалоговое окно **Назначение ключа приложению**. 

    ![Назначение ключа приложению](./media/luis-manage-keys/assign-key.png)
3. Выберите клиента в диалоговом окне. 
 
    > [!Note]
    > В Azure клиент представляет идентификатор клиента или организации, связанных со службой, в Azure Active Directory. Если ранее вы уже зарегистрировались для получения подписки Azure со своей учетной записью Майкрософт, у вас уже есть клиент! При входе на портал Azure автоматически выполняется вход в [клиент, используемый по умолчанию](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant). Вы можете использовать этот клиент, но при этом может потребоваться создать учетную запись администратора организации.

4. Выберите подписку Azure, связанную с ключом LUIS Azure, который вы хотите добавить.

5. Выберите учетную запись LUIS Azure. Регион учетной записи отображается в скобках. 

    ![Выберите ключ](./media/luis-manage-keys/assign-key-filled-out.png)

6. После назначения ключа конечной точки используйте его во всех запросах конечной точки. 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="publishing-regions"></a>Регионы публикации
Ознакомьтесь с дополнительными сведениями о [регионах](luis-reference-regions.md) публикации, включая публикацию в [Европе](luis-reference-regions.md#publishing-to-europe) и [Австралии](luis-reference-regions.md#publishing-to-australia). Регионы публикации отличаются от регионов разработки. Убедитесь, что вы разрабатываете приложение в регионе разработки, соответствующем желаемому региону публикации.

## <a name="unassign-key"></a>Отмена назначения ключа

* В **списке "Ресурсы и ключи"** щелкните значок корзины рядом с сущностью, для которой вы хотите отменить назначение. Нажмите кнопку **ОК** в окне подтверждения, чтобы подтвердить удаление.
 
    ![Отмена назначения сущности](./media/luis-manage-keys/unassign-key.png)

> [!NOTE]
> При отмене назначения ключа LUIS он не удаляется из подписки Azure.

## <a name="next-steps"></a>Дополнительная информация

Используйте ключ для публикации приложения на странице **Публикация приложения**. Инструкции по публикации см. в разделе [Публикация приложения](luis-how-to-publish-app.md).