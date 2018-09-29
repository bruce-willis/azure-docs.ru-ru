---
title: Синхронизация конфигурации сети в Службе приложений Azure | Документация Майкрософт
description: В этой статье описано, как синхронизировать конфигурацию сети для плана размещения Службы приложений Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 03/07/2018
ms.openlocfilehash: d5de908166e8de1d45a36f97aee8934653e59623
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47163179"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Синхронизация конфигурации сети для плана размещения Службы приложений Azure

Несмотря на [интеграцию приложения с виртуальной сетью Azure](../app-service/web-sites-integrate-with-vnet.md), бывает так, что установить подключение к Управляемому экземпляру не удается. В этом случае вам нужно обновить конфигурацию сети для своего плана обслуживания. 

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Синхронизация конфигурации сети для плана размещения Службы приложений

Для этого выполните следующие действия:  

1. Перейдите к плану службы веб-приложений.
 
   ![План службы приложений](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Нажмите кнопку **Сети** и выберите **Щелкните здесь для управления**.
 
   ![Управление планом службы](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Выберите **виртуальную сеть** и щелкните **Синхронизировать сеть**. 
 
   ![Синхронизация сети](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Дождитесь завершения синхронизации.
  
   ![Синхронизация выполнена](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Теперь можно попробовать заново установить подключение к Управляемому экземпляру.

## <a name="next-steps"></a>Дополнительная информация

- См. дополнительные сведения о [настройке виртуальной сети для Управляемого экземпляра](sql-database-managed-instance-vnet-configuration.md).
