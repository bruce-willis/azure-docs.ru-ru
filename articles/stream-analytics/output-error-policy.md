---
title: Политики обработки ошибок вывода в Azure Stream Analytics
description: Узнайте о том, какие политики обработки ошибок вывода доступны в Azure Stream Analytics.
services: stream-analytics
author: sidram
ms.author: sidram
manager: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/27/2018
ms.openlocfilehash: f854e8ce35ac9d0a99b4a7dc1cdc66724114a5c4
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392571"
---
# <a name="output-error-policy"></a>Политика обработки ошибок вывода
В этой статье описываются политики обработки ошибок вывода, которые могут быть настроены в Azure Stream Analytics.

Политики обработки ошибок вывода применяются только к ошибкам преобразования данных, возникающим, когда выходное событие, созданное заданием Stream Analytics, не соответствует схеме целевого приемника. Это политику можно настроить, выбрав параметр **Повторить** или **Удалить**. На портале Azure в задании Stream Analytics перейдите к разделу **Настройка** и щелкните **Политика обработки ошибок**, чтобы сделать выбор.

![Политика обработки ошибок: расположение](./media/stream-analytics-error-policy/stream-analytics-error-policy-locate.PNG)


## <a name="retry"></a>Повторить попытку
Когда происходит ошибка, Azure Stream Analytics бесконечно повторяет попытку записи события, пока запись не будет выполнена. Время ожидания для повторных попыток не задано. В конечном итоге обработку всех последующих событий блокирует событие, которое Azure Stream Analytics безуспешно пытается записать. Это политика обработки ошибок вывода по умолчанию.

## <a name="drop"></a>Удаление
Azure Stream Analytics будет удалять все выходные события, вызывающее ошибку преобразования данных. Восстановить удаленные события для повторной обработки позже невозможно.


Для всех временных ошибок (например, сбоев сети) выполняется повторная попытка независимо от конфигурации политики обработки ошибок вывода.


## <a name="next-steps"></a>Дополнительная информация
[Руководство по устранению неполадок для Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
