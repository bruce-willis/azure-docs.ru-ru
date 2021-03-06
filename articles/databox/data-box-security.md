---
title: Обзор безопасности Microsoft Azure Data Box | Документация Майкрософт
description: Описывает функции безопасности Azure Data Box в устройстве, службе и данных, которые находятся в Data Box.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 26f9cd589a754914a5c7b7ee700f9cefb1421d51
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995007"
---
# <a name="azure-data-box-security-and-data-protection"></a>Безопасность и защита данных в Azure Data Box

Data Box предоставляет безопасное решение для защиты данных, гарантируя, что только авторизованные субъекты могут просматривать, изменять или удалять данные. В этой статье описываются функции безопасности Azure Data Box, которые помогают защитить каждый компонент решения Data Box и хранимые на нем данные. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="data-flow-through-components"></a>Поток данных через компоненты

Решение Microsoft Azure Data Box состоит из четырех основных компонентов, которые взаимодействуют друг с другом.

- **Служба Azure Data Box, размещенная в Azure** — это служба управления, с помощью которой можно заказывать и настраивать устройства, а затем отслеживать заказы до их завершения.
- **Устройство Data Box** — это устройство передачи, которое доставляется к вам с целью импорта локальных данных в Azure. 
- **Клиенты или серверы, подключенные к устройству** — это клиенты инфраструктуры, которые подключаются к устройству Data Box и содержат данные, которые необходимо защитить.
- **Облачное хранилище** – расположение в облаке Azure, где хранятся данные. Обычно это учетная запись хранения, связанная с созданным ресурсом Azure Data Box.

На следующей схеме показан поток данных через решение Azure Data Box из локального компьютера в хранилище Azure.

![Безопасность Data Box](media/data-box-security/data-box-security-2.png)

## <a name="security-features"></a>Функции безопасности

Data Box предоставляет безопасное решение для защиты данных, гарантируя, что только авторизованные субъекты могут просматривать, изменять или удалять данные. Функции безопасности этого решения предназначены для диска и для связанной службы, обеспечивающих безопасность хранимых на них данных. 

### <a name="data-box-device-protection"></a>Защита устройства Data Box

Устройство Data Box защищено с помощью следующих функций:

- износоустойчивый корпус устройства, который выдерживает удары, неблагоприятную транспортировку и условия окружающей среды; 
- гарантийные пломбы, указывающие на незаконное изменение устройства во время транспортировки;
- обнаружение незаконного изменения оборудования и программного обеспечения, которое предотвращает дальнейшие операции с устройством;
- запуск только программного обеспечения, предназначенного специально для Data Box;
- загрузка в заблокированном состоянии;
- контроль доступа к устройству с помощью пароля разблокировки устройства;
- доступ к учетным данным для копирования данных на устройство и из него.

### <a name="data-box-data-protection"></a>Защита данных Data Box

Входные и выходные данные Data Box защищены следующими функциями:

- 256-битное шифрование AES для неактивных данных; 
- зашифрованные протоколы могут использоваться для данных в процессе использования;
- безопасное удаление данных с устройства после завершения передачи данных в Azure. Удаление данных осуществляется в соответствии со стандартами NIST 800-88r1.

### <a name="data-box-service-protection"></a>Защита службы Data Box

Служба Data Box защищена с помощью следующих функций.

- Для доступа к службе Data Box необходимо, чтобы у организации была подписка Azure, включающая Data Box. Подписка обеспечивает доступ к функциям, предоставляемым на портале Azure.
- Так как служба Data Box размещается в Azure, она защищена средствами безопасности Azure. Дополнительные сведения о функциях безопасности, предоставляемых Microsoft Azure, см. в [центре управления безопасностью Microsoft Azure](https://www.microsoft.com/TrustCenter/Security/default.aspx). 
- Служба Data Box хранит пароль разблокировки, который используется для разблокирования устройства в службе. 
- Служба Data Box хранит сведения о заказе и его состояние в службе. Эта информация удаляется при удалении самого заказа. 

## <a name="managing-personal-data"></a>Управление персональными данными

Azure Data Box собирает и отображает личные сведения в следующих ключевых экземплярах службы.

- **Параметры уведомлений**. Когда создается заказ, адреса электронной почты пользователей настраиваются в параметрах уведомлений. Эти сведения может просматривать администратор. Эта информация удаляется службой, когда задание выполнено или когда заказ удаляется.

- **Сведения о заказе**. После создания заказа адрес доставки, электронная почта, контактная информация пользователей хранятся на портале Azure. Сохраненная информация включает следующее.

    - имя контактного лица;
    - номер телефона.
    - Email
    - почтовый адрес;
    - City
    - почтовый индекс;
    - Состояние
    - страна, область, край, округ, регион;
    - номер счета перевозчика;
    - номер отслеживания доставки.

    Сведения о заказе удаляются службой Data Box после завершения задания или при удалении заказа.

- **Адрес доставки**. После размещения заказа служба Data Box предоставляет адрес доставки сторонним операторам, таким как UPS или DHL. 

Дополнительные сведения см. в политике конфиденциальности Майкрософт в [центре управления безопасностью](https://www.microsoft.com/trustcenter).


## <a name="security-guidelines-reference"></a>Ссылка на рекомендации по безопасности

В Data Box реализованы следующие рекомендации по безопасности. 

|Рекомендация   |ОПИСАНИЕ   |
|---------|---------|
|[IEC 60529 IP52](http://www.iec.ch/)    | Для защиты от пыли и воды         |
|[ISTA 2A](https://ista.org/docs/2Aoverview.pdf)     | Для стойкости к неблагоприятным условиям транспортировки          |
|[NIST SP 800-147](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-147.pdf)      | Для безопасного обновления встроенного ПО         |
|[FIPS 140-2 уровня 2](https://csrc.nist.gov/csrc/media/publications/fips/140/2/final/documents/fips1402.pdf)      | Для защиты данных         |
|[NIST SP 800-88r1](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf)      | Для очистки данных         |

## <a name="next-steps"></a>Дополнительная информация

- Ознакомьтесь с разделом [Azure Data Box system requirements](data-box-system-requirements.md) (Системные требования Azure Data Box).
- Изучите [ограничения Data Box](data-box-limits.md).
- Быстрое развертывание [Azure Data Box](data-box-quickstart-portal.md) на портале Azure.
