---
title: включение файла
description: включение файла
services: signalr
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 97b8ef5e260a853ecdffb3c502f8a5051dd0e143
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006264"
---
## <a name="create-an-azure-signalr-service-instance"></a>Создание экземпляра службы Azure SignalR

Приложение будет подключено к экземпляру службы SignalR в Azure.

1. Нажмите кнопку "Создать" в верхнем левом углу портала Azure. На экране "Создание" введите в поле поиска *Служба SignalR* и нажмите клавишу ВВОД.

    ![Поиск службы SignalR Azure](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-new.png)

1. Выберите **Служба SignalR** в результатах поиска и щелкните **Создать**.

1. Введите следующие параметры.

    | Параметр      | Рекомендуемое значение  | ОПИСАНИЕ                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Имя ресурса** | Глобально уникальное имя | Имя, которое определяет новый экземпляр службы SignalR. Допустимые символы: `a-z`, `0-9` и `-`.  | 
    | **Подписка** | Ваша подписка | Подписка, в рамках которой создается экземпляр службы SignalR. | 
    | **[Группа ресурсов](../../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Имя новой группы ресурсов, в которой будет создан экземпляр службы SignalR. | 
    | **Местоположение.** | Запад США | Выберите ближайший [регион](https://azure.microsoft.com/regions/). |
    | **Ценовая категория** | Free | Поработайте со службой Azure SignalR бесплатно. |
    | **Число единиц** |  Не применяется | Число единиц определяет количество подключений, которые поддерживает ваш экземпляр службы SignalR. Это можно настроить только для цен. категории "Стандартный". |

    ![Создание службы SignalR](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Щелкните **Создать**, чтобы начать развертывание экземпляра службы SignalR.
