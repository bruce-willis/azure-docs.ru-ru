---
title: включение файла
description: включение файла
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/29/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: bad7e87cb194fb7d8846e7e675881658a1d33b1e
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37138166"
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
