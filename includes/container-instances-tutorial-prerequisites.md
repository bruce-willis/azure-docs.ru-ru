---
title: включение файла
description: включение файла
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 03/20/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: da63a5418ab94623f6ce3c9f35a085dd8b198d1a
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48858100"
---
Для работы с этим руководством вам потребуются следующие ресурсы:

**Azure CLI**. Интерфейс командной строки Azure версии 2.0.29 или более поздней, установленный на локальном компьютере. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, обратитесь к статье [Установка Azure CLI][azure-cli-install].

**Docker**. Для выполнения действий, описанных в этом руководстве, требуется базовое понимание таких основных понятий Docker, как контейнеры и образы контейнеров, а также знание основных команд `docker`. См. [общие сведения о Docker и контейнерах][docker-get-started].

**Модуль Docker**. Для работы с этим руководством требуется среда разработки Docker, установленная локально. Docker предоставляет пакеты, которые настраивают среду с Docker для [macOS][docker-mac], [Windows][docker-windows] и [Linux][docker-linux].

> [!IMPORTANT]
> Так как Azure Cloud Shell не включает управляющую программу Docker, для работы с этим руководством *необходимо* установить среду разработки Azure CLI и модуль Docker на свой *локальный компьютер*. В этом руководстве Azure Cloud Shell не используется.

<!-- LINKS - External -->
[docker-get-started]: https://docs.docker.com/engine/docker-overview/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
