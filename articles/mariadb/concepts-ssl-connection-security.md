---
title: Возможность подключения SSL в базе данных Azure для MariaDB
description: Сведения о настройке базы данных Azure для MariaDB и связанных приложений для правильного использования SSL-соединений
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: dda5df58a83ddd3ce42fa887c3c32a3e23954920
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946652"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>Возможность подключения SSL в базе данных Azure для MariaDB
База данных Azure для MariaDB поддерживает подключение сервера базы данных к клиентским приложениям с помощью SSL (Secure Sockets Layer). Применение SSL-соединений между сервером базы данных и клиентскими приложениями обеспечивает защиту от атак "злоумышленник в середине" за счет шифрования потока данных между сервером и приложением.

## <a name="default-settings"></a>Параметры по умолчанию
По умолчанию в службе базы данных должно быть настроено обязательное использование SSL-соединений при подключении к MariaDB.  Мы рекомендуем не отключать параметр SSL без необходимости.

При подготовке нового сервера базы данных Azure для MariaDB с помощью портала Azure и интерфейса командной строки применение SSL-соединений включается по умолчанию.

Строки подключения для различных языков программирования отображаются на портале Azure. Они включают необходимые параметры SSL для подключения к базе данных. На портале Azure выберите свой сервер. В разделе **Параметры** выберите **Строки подключения**. Значение параметра SSL зависит от соединителя. Например, может использоваться "ssl=true", "sslmode=require", "sslmode=required" или другой вариант.

<!-- To learn how to enable or disable SSL connection when developing application, refer to [How to configure SSL](howto-configure-ssl.md).-->

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения о [правилах брандмауэра сервера](concepts-firewall-rules.md)
- Дополнительные сведения о [настройке SSL](howto-configure-ssl.md)
