---
title: Общие сведения о Хранилище таблиц. Хранилище объектов в Azure | Документация Майкрософт
description: Хранение структурированных данных в облаке в хранилище таблиц Azure (хранилище данных NoSQL).
services: storage
author: SnehaGunda
ms.service: storage
ms.devlang: dotnet
ms.topic: overview
ms.date: 04/23/2018
ms.author: sngun
ms.component: tables
ms.openlocfilehash: 316e10d56e5c235c4d64dde0fde4dcb9429629c8
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39527793"
---
# <a name="introduction-to-table-storage-in-azure"></a>Общие сведения о Хранилище таблиц Azure

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Хранилище таблиц Azure — это служба, которая хранит структурированные данные NoSQL в облаке, предоставляя хранилище ключей и атрибутов с бессхемной конструкцией. Такая конструкция хранилища таблиц позволяет легко адаптировать данные по мере расширения приложения. Разным типам приложений может быть предоставлен быстрый и экономичный доступ к хранилищу таблиц. Такое хранилище обычно дешевле, чем традиционные хранилища SQL для похожих объемов данных.

Хранилище таблиц можно использовать для хранения гибких наборов данных, например пользовательских данных для веб-приложений, адресных книг, сведений об устройстве или метаданных любого другого типа, которые требуются вашей службе. В таблице можно хранить любое количество сущностей, а учетная запись хранения может содержать любое количество таблиц в пределах емкости учетной записи.

[!INCLUDE [storage-table-concepts-include](../../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>Дополнительная информация

* [Обозреватель хранилищ Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) — это бесплатное автономное приложение от корпорации Майкрософт, позволяющее визуализировать данные из службы хранилища Azure на платформе Windows, macOS и Linux.

* [Getting Started with Azure Table Storage in .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) (Приступая к работе с хранилищем таблиц Azure в .NET)

* Дополнительные сведения о доступных API-интерфейсах см. в справочной документации по службе таблиц:

    * [Справочник по клиентской библиотеке хранилища для .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)

    * [Справочник по REST API](http://msdn.microsoft.com/library/azure/dd179355)
