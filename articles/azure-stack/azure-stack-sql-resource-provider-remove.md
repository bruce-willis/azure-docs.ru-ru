---
title: Удаление поставщика ресурсов SQL в Azure Stack | Документация Майкрософт
description: Сведения об удалении поставщика ресурсов SQL из развертывания Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 9f90201cad0f74923460c2f25eff4de98dc6690a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294786"
---
# <a name="removing-the-mysql-resource-provider"></a>Удаление поставщика ресурсов MySQL  
Перед удалением поставщика ресурсов SQL сначала нужно удалить все его зависимости.

## <a name="remove-the-mysql-resource-provider"></a>Удаление поставщика ресурсов MySQL 

1. Убедитесь, что удалены все имеющиеся зависимости поставщика ресурсов SQL.

  > [!NOTE]
  > Удаление поставщика ресурсов SQL продолжится, даже если в данный момент его используют зависимые ресурсы. 
  
2. Найдите исходный пакет развертывания, который вы скачали ранее для этой версии адаптера поставщика ресурсов SQL.
3. Повторно запустите сценарий развертывания со следующими параметрами.
    - Используйте параметр -Uninstall.
    - IP-адрес или DNS-имя привилегированной конечной точки.
    - Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке.
    - Учетные данные администратора службы Azure Stack. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack.

## <a name="next-steps"></a>Дополнительная информация
[Предложение служб приложений как PaaS](azure-stack-app-service-overview.md)
