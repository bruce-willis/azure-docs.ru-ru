---
title: Устранение неполадок QnA Maker | Документация Майкрософт
titleSuffix: Azure
description: Способы устранения неполадок в среде выполнения QnA Maker.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 06/04/2018
ms.author: saneppal
ms.openlocfilehash: 23847c81a39ea392b6e64575406c9dc338b852de
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "35383233"
---
# <a name="qnamaker-troubleshooting"></a>Устранение неполадок QnA Maker
QnA Maker состоит из компонентов, размещенных в учетной записи Azure пользователя. Для отладки пользователям может потребоваться оперировать ресурсами Azure для QnA Maker или предоставить группе поддержки QnA Maker дополнительные сведения об их конфигурации.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Как получить последние обновления для среды выполнения QnA Maker
Среда выполнения QnA Maker является частью развернутой службы приложений Azure в случае [создания службы QnA Maker](./set-up-qnamaker-service-azure.md) на портале Azure. Периодически устанавливаются обновления для этой среды выполнения. Чтобы применить последние обновления к конфигурации QnA Maker, необходимо перезапустить службу приложений.
1. Перейдите к службе QnA Maker (группе ресурсов) на [портале Azure](https://portal.azure.com).

    ![Группа ресурсов Azure для QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Щелкните службу приложений и откройте раздел "Обзор".

     ![Служба приложений QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Перезапустите службу приложений. Эта операция должна потребовать нескольких секунд. Обратите внимание на то, что нисходящие приложения и боты, созданные на основе этой службы QnA Maker, будут недоступны для пользователей в течение периода ее перезапуска.

    ![Перезапуск службы приложений QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Как получить имя узла службы QnA Maker
Имя узла службы QnA Maker удобно использовать для отладки при обращении в службу поддержки QnA Maker или на портал UserVoice. Имя узла — это URL-адрес вида https://*{имя_узла}*.azurewebsites.net.
    
1. Перейдите к службе QnA Maker (группе ресурсов) на [портале Azure](https://portal.azure.com).

    ![Группа ресурсов Azure для QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Щелкните службу приложений.

     ![Служба приложений QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. URL-адрес узла указан в разделе "Обзор".

    ![Имя узла QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Использование API QnA Maker](./upgrade-qnamaker-service.md)
