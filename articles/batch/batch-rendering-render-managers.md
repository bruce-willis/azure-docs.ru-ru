---
title: Поддержка диспетчера рендеринга в пакетной службе Azure
description: Использование Azure для рендеринга с использованием интеграции диспетчера рендеринга в пакетную службу Azure
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 798b2b457016856662f392af25d987788f73c242
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2018
ms.locfileid: "40036755"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Использование пакетной службы Azure с диспетчерами фермы рендеринга

Если вы используете существующую локальную ферму рендеринга, скорее всего для управления ее емкостью и заданиями рендеринга настроен диспетчер рендеринга.

Azure предоставляет встроенную поддержку или поддержку через надстройки для многих популярных диспетчеров рендеринга. Это позволяет добавлять и удалять виртуальные машины Azure, включая содержащие лицензии приложений с оплатой по мере использования и (или) низкоприоритетные виртуальные машины.

Поддерживаются следующие диспетчеры рендеринга:

* [PipelineFX Qube!](https://www.pipelinefx.com/);
* [Royal Render](http://www.royalrender.de/);
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/).

## <a name="using-azure-with-pipelinefx-qube"></a>Использование Azure с PipelineFX Qube

Соответствующие скрипты и инструкции по использованию виртуальных машин пула пакетной службы Azure в качестве рабочих ролей Qube вы можете получить в [репозитории GitHub](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Использование Azure с Royal Render

Для Royal Render в Azure и пакетной службе Azure реализована встроенная поддержка интеграции, что позволяет расширить ферму рендеринга за счет виртуальных машин на платформе Azure. Краткое описание этого варианта вы найдете в [файлах справки](http://www.royalrender.de/help8/index.html?Cloudrendering.html).

Пример клиента Royal Render с интеграцией в Azure можно изучить в статье [об истории клиента Jellyfish Pictures](https://customers.microsoft.com/en-gb/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Использование Azure с Thinkbox Deadline

Соответствующие скрипты и инструкции по использованию виртуальных машин пула пакетной службы Azure в качестве ведомых узлов Deadline вы можете получить в [репозитории GitHub](https://github.com/Azure/azure-deadline).

## <a name="next-steps"></a>Дополнительная информация

Попробуйте настроить интеграцию пакетной службы Azure с используемым диспетчером рендеринга, применяя соответствующий подключаемый модуль и инструкции на сайте GitHub, если потребуется.