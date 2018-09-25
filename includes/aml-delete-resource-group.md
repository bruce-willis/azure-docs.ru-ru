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
ms.openlocfilehash: dcd2854585e2c776f361aa4718af6b99d8cbb593
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47002078"
---
>[!IMPORTANT]
>Созданные ресурсы могут использоваться в качестве необходимых компонентов при работе с другими руководствами по Машинному обучению Azure. 


Если вы не собираетесь использовать ресурсы, созданные при работе с этим кратким руководством, удалите их, чтобы не платить за их использование.

1. На портале Azure выберите **Группы ресурсов** в левой части окна.  
 
   ![Удаление на портале Azure](./media/aml-delete-resource-group/delete-resources.png)

1. В списке выберите созданную группу ресурсов.

1. Выберите **Удалить группу ресурсов**.

1. Введите имя группы ресурсов, а затем нажмите кнопку **Удалить**.

   Появится ошибка "Не удается удалить ресурс до удаления вложенных ресурсов". Вам нужно удалить эти вложенные ресурсы. См. дополнительные сведения об [устранении неполадок](../articles/machine-learning/desktop-workbench/known-issues-and-troubleshooting-guide.md#cant-delete-experimentation-account). 