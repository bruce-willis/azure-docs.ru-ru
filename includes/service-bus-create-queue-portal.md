---
title: включение файла
description: включение файла
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/29/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ed298fc8f13685c4872c4c54ba1e447debea79f
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702642"
---
Убедитесь в том, что вы уже создали пространство имен служебной шины, как показано [здесь][namespace-how-to].

1. Войдите на [портал Azure][azure-portal].
2. В левой области навигации портала щелкните **Служебная шина**. Если элемент **Служебная шина** не отображается, щелкните **Все службы**.
3. Щелкните пространство имен, в котором вы хотите создать очередь. В нашем примере это **sbnstest1**.
   
    ![Создание очереди][createqueue1]
4. В окне пространства имен выберите раздел **Очереди**, а затем в окне **Очереди** нажмите кнопку **+ Очередь**.
   
    ![Выберите "Очереди"][createqueue2]
5. Введите **имя очереди**, остальные значения по умолчанию не изменяйте.
   
    ![Нажмите кнопку "Создать"][createqueue3]
6. Нажмите кнопку **Создать** в нижней части окна.

[createqueue1]: ./media/service-bus-create-queue-portal/create-queue1.png
[createqueue2]: ./media/service-bus-create-queue-portal/create-queue2.png
[createqueue3]: ./media/service-bus-create-queue-portal/create-queue3.png

[namespace-how-to]: ../articles/service-bus-messaging/service-bus-create-namespace-portal.md
[azure-portal]: https://portal.azure.com
