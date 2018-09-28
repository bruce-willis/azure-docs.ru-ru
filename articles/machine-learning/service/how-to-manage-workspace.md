---
title: Создание рабочих областей машинного обучения Azure и управление ими
description: Узнайте, как создавать, просматривать и удалять рабочие области машинного обучения Azure на портале Microsoft Azure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 09/24/2018
ms.openlocfilehash: 7d01a2e3ebd46315966c82a43a17ffc5b329b829
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954353"
---
# <a name="create-and-manage-azure-machine-learning-workspaces"></a>Создание рабочих областей машинного обучения Azure и управление ими

В этой статье вы выполните создание, просмотр и удаление [**рабочих областей машинного обучения Azure**](concept-azure-machine-learning-architecture.md#workspace) на портале Microsoft Azure для [службы "Машинное обучение Azure"](overview-what-is-azure-ml.md).  Можно также создавать и удалять рабочие области, [используя интерфейс командной строки (CLI)](reference-azure-machine-learning-cli.md) или [код Python](http://aka.ms/aml-sdk).

Для создания рабочей области понадобится подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="create-a-workspace"></a>Создание рабочей области 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view-a-workspace"></a>Просмотр рабочей области

1. В верхнем левом углу окна портала выберите **Все службы**. 

1. В поле фильтра **Все службы** введите **Рабочая область машинного обучения**.  

   ![поиск рабочей области машинного обучения Azure](media/how-to-manage-workspace/allservices-search1.png)

1. В результатах фильтра выберите **Рабочая область машинного обучения** для отображения списка рабочих областей. 

   ![поиск рабочей области машинного обучения Azure](media/how-to-manage-workspace/allservices-search.PNG)

1. Просмотрите список найденных рабочих областей. Можно выполнять фильтрацию на основе подписки, групп ресурсов и расположений.  

   ![Список рабочих областей службы "Машинное обучение Azure"](media/how-to-manage-workspace/allservices_view_workspace.PNG)

1. Выберите только что созданную рабочую область для отображения ее свойств.

   ![png](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Удаление рабочей области

Используйте кнопку "Удалить" в верхней части рабочей области, которую нужно удалить.

  ![png](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Очистка ресурсов 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Дополнительная информация

Воспользуйтесь полным руководством, чтобы научиться применять рабочую область для создания, обучения и развертывания моделей, используя службу "Машинное обучение Azure".

> [!div class="nextstepaction"]
> [Руководство. Обучение моделей](tutorial-train-models-with-aml.md)
