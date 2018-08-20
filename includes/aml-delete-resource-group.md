---
title: включение файла
description: включение файла
services: machine-learning
author: j-martens
ms.service: machine-learning
ms.author: jmartens
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 07/27/2018
ms.openlocfilehash: 2ce7e083444eb6c4e02f04f36eb0810ea37d8ae7
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39513789"
---
>[!IMPORTANT]
>Созданные ресурсы могут использоваться в качестве необходимых компонентов при работе с другими руководствами по Машинному обучению Azure. 


Если вы не собираетесь использовать ресурсы, созданные при работе с этим кратким руководством, удалите их, чтобы не платить за их использование.

1. На портале Azure выберите **Группы ресурсов** в левой части окна.  
 
   ![Удаление на портале Azure](./media/aml-delete-resource-group/delete-resources.png)

1. В списке выберите созданную группу ресурсов.

1. Выберите **Удалить группу ресурсов**.

1. Введите имя группы ресурсов, а затем нажмите кнопку **Удалить**.

   Появится ошибка "Не удается удалить ресурс до удаления вложенных ресурсов". Вам нужно удалить эти вложенные ресурсы. См. дополнительные сведения об [устранении неполадок](../articles/machine-learning/service/known-issues-and-troubleshooting-guide.md#cant-delete-experimentation-account). 