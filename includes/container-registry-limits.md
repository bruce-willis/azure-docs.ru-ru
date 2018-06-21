---
title: включение файла
description: включение файла
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 05/29/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 942b9bdf0201acaefe3333bcf928772899b9bdc2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34665100"
---
| Ресурс | базовая; | Стандартная | Премиум |
|---|---|---|---|---|
| Служба хранилища | 10 ГБ | 100 ГБ| 500 ГБ |
| Максимальный размер уровня образа | 20 ГиБ | 20 ГиБ | 50 ГиБ |
| Операций чтения в минуту<sup>1, 2</sup> | 1000 | 3000 | 10 000 |
| Операций записи в минуту<sup>1, 3</sup> | 100 | 500 | 2000 |
| Пропускная способность скачивания в Мбит/с<sup>1</sup> | 30 | 60 | 100 |
| Пропускная способность передачи в Мбит/с<sup>1</sup> | 10 | 20 | 50 |
| Объекты Webhook | 2 | 10 | 100 |
| Георепликация | Недоступно | Недоступно | [Поддерживаются](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *Операции чтения*, *операции записи* и *пропускная способность* представляют собой минимальные расчеты. ACR стремится к повышению производительности по мере использования.

<sup>2</sup> [При извлечении образа Docker](https://docs.docker.com/registry/spec/api/#pulling-an-image) осуществляется преобразование в несколько операций чтения на основе числа слоев в образе, а также получении манифеста.

<sup>3</sup> [При отправке образа Docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) осуществляется преобразование в несколько операций записи, в зависимости от числа слоев, которые необходимо отправить. Команда `docker push` включает *операции чтения* для получения манифеста для имеющегося образа.