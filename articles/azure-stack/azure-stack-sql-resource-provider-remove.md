---
title: Удаление поставщика ресурсов SQL в Azure Stack | Документация Майкрософт
description: Узнайте, как удалить поставщик ресурсов SQL из развертывания Azure Stack.
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
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 150d1c40463aa04527bdd6e356a4c24ef68b02ef
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301904"
---
# <a name="remove-the-sql-resource-provider"></a>Удаление поставщика ресурсов SQL

Прежде чем удалить поставщик ресурсов SQL, необходимо удалить все его зависимости. Кроме того, потребуется скопировать пакет развертывания, который был использован для установки поставщика ресурсов.

## <a name="to-remove-the-sql-resource-provider"></a>Удаление поставщика ресурсов SQL

1. Убедитесь, что удалены все имеющиеся зависимости поставщика ресурсов SQL.

   > [!NOTE]
   > Удаление поставщика ресурсов SQL продолжится, даже если в данный момент его используют зависимые ресурсы.
  
2. Получите копию двоичного файла поставщика ресурсов SQL и запустите файл для самостоятельного извлечения содержимого во временный каталог.

3. Откройте новую консоль PowerShell с повышенными привилегиями и перейдите в каталог, в который ранее извлекли двоичные файлы поставщика ресурсов SQL.

4. Запустите сценарий DeploySqlProvider.ps1 со следующими параметрами.

    - **Uninstall**. Удаляет поставщик ресурсов и все связанные с ним ресурсы.
    - **PrivilegedEndpoint**. IP-адрес или DNS-имя привилегированной конечной точки.
    - **CloudAdminCredential**. Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке.
    - **AzCredential**. Учетные данные администратора службы Azure Stack. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack.

## <a name="next-steps"></a>Дополнительная информация

[Предложение служб приложений как PaaS](azure-stack-app-service-overview.md)
