---
title: Службы и технологии обеспечения безопасности Azure | Документация Майкрософт
description: В статье представлен проверенный список статей о службах и технологиях обеспечения безопасности в Azure.
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2018
ms.author: barclayn
ms.openlocfilehash: e52cee2cb642de6e54270c597e6ed99f7162d0ed
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641464"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Службы и технологии безопасности, доступные в Azure

Имеющиеся и будущие пользователи Azure часто спрашивают нас о списке доступных в Azure служб и технологий, связанных с обеспечением безопасности.

При оценке поставщиков облачных служб полезно иметь следующие сведения. Поэтому мы предоставили этот список, чтобы помочь вам приступить к работе.

Со временем этот список будет дополнен — также, как и линейка предложений Azure. Просматривайте эту страницу время от времени, чтобы быть в курсе последних новостей о наших услугах и технологиях, связанных с безопасностью.

## <a name="general-azure-security"></a>Общая безопасность Azure
|Service|ОПИСАНИЕ|
|--------|--------|
|[Центр&nbsp;безопасности&nbsp;Azure](../security-center/security-center-intro.md)| Облачное решение защиты рабочих нагрузок обеспечивает управление безопасностью и расширенную защиту от угроз для гибридных облачных рабочих нагрузок.|
|[Хранилище ключей Azure](../key-vault/key-vault-overview.md)| Защищенное хранилище секретов для паролей, строк подключения и другой информации, необходимой для работы приложения. |
|[Служба Log Analytics](../log-analytics/log-analytics-overview.md)|Служба мониторинга собирает телеметрию и другие данные, а также предоставляет язык запросов и модуль аналитики для доставки оперативных аналитических сведений для приложений и ресурсов. Решение можно использовать отдельно или совместно с другими службами, например центром обеспечения безопасности. |
|[Лаборатория для разработки и тестирования Azure](../devtest-lab/devtest-lab-overview.md)|Это служба, помогающая разработчикам и тест-инженерам быстро создавать среды в Azure при минимальных потерях и контроле издержек.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](azure-security-disk-encryption-overview.md)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Безопасность хранилищ
|Service|ОПИСАНИЕ|
|------|--------|
| [Шифрование&nbsp;службы&nbsp;хранения&nbsp;Azure](../storage/common/storage-service-encryption.md)|Функция безопасности, которая автоматически шифрует данные в хранилище Azure.   |
|[Гибридное хранилище, зашифрованное с помощью StorSimple](../storsimple/storsimple-ova-overview.md)| Решение интегрированного хранилища, которое управляет задачами хранилища на локальных устройствах и в облачном хранилище Azure.|
|[Шифрование Azure на стороне клиента](../storage/common/storage-client-side-encryption.md)| Решение шифрования на стороне клиента, которое шифрует данные в клиентских приложениях перед передачей их в службу хранилища Azure, а также расшифровывает данные при скачивании. |
| [Подписанные URL-адреса службы хранилища Azure](../storage/common/storage-dotnet-shared-access-signature-part-1.md)|Подпись общего доступа обеспечивает делегированный доступ к ресурсам в вашей учетной записи хранения.  |
|[Ключи учетной записи хранения Azure](../storage/common/storage-create-storage-account.md)| Метод управления доступом к службе хранилища Azure, используемый для аутентификации при получении доступа к учетной записи хранения. |
|[Разработка для хранилища файлов Azure с помощью .NET](../storage/files/storage-files-introduction.md)|Технология безопасности сети, которая обеспечивает автоматическое сетевое шифрование для протокола общего доступа к файлам SMB. |
|[Аналитика службы хранилища Azure](https://docs.microsoft.com/en-us/rest/api/storageservices/Storage-Analytics)| Технология ведения журнала и создания метрики для данных в учетной записи хранения. |

<!------>

## <a name="database-security"></a>Безопасность базы данных
|Service|ОПИСАНИЕ|
|------|--------|
| [Брандмауэр&nbsp;SQL&nbsp;Azure](../sql-database/sql-database-firewall-configure.md)|Функция управления доступом сети, обеспечивающая защиту от сетевых атак базы данных. |
|[Уровень шифрования&nbsp;ячейки&nbsp;SQL&nbsp;Azure](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Технология безопасности базы данных, которая обеспечивает шифрование на более детальном уровне.  |
| [Шифрование подключений&nbsp;SQL&nbsp;Azure](../sql-database/sql-database-control-access.md)|Чтобы обеспечить безопасность, база данных SQL управляет доступом с помощью правил брандмауэра, ограничивающих подключение по IP-адресу, механизмов проверки подлинности, требующих удостоверений пользователей, и методов авторизации, ограничивающих действия и данные для пользователей. |
| [Постоянное шифрование в Azure SQL](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Защищает конфиденциальные данные, такие как номера кредитных карт или номера национальной идентификации (например, номера социального страхования США), которые хранятся в базах данных SQL Server и базах данных SQL Azure.  |
| [Прозрачное шифрование данных&nbsp;SQL&nbsp;Azure](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Компонент безопасности базы данных, который шифрует хранилище всей базы данных. |
| [Аудит баз данных SQL Azure](../sql-database/sql-database-auditing.md)|Возможность аудита базы данных SQL, позволяющая отслеживать события базы данных и записывать их в журнал аудита в учетной записи хранения Azure.  |


## <a name="identity-and-access-management"></a>Управление удостоверениями и доступом
|Service|ОПИСАНИЕ|
|------|--------|
| [Управление доступом&nbsp;на основе&nbsp;ролей&nbsp;Azure](../active-directory/role-based-access-control-configure.md)|Функция контроля доступа, которая позволяет предоставлять пользователям доступ только к ресурсам, доступ к которым основан на их ролях в рамках организации.  |
| [Azure Active Directory](../active-directory/active-directory-whatis.md)|Облачный репозиторий аутентификации, который поддерживает несколько клиентов, облачный каталог и несколько служб управления удостоверениями в рамках Azure.  |
| [Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-overview.md)|Это служба управления идентификацией, которая позволяет администрировать процессы входа и регистрации пользователей, а также управлять их профилями при использовании приложений на основе Azure.   |
| [Доменные службы Azure Active Directory](../active-directory-domain-services/active-directory-ds-overview.md)| Облачная и управляемая версия доменных служб Active Directory. |
| [Многофакторная идентификация Azure](../active-directory/authentication/multi-factor-authentication.md)| Подготовка безопасности, которая использует несколько различных форм аутентификации и проверки перед предоставлением защищенной информации. |

## <a name="backup-and-disaster-recovery"></a>Резервное копирование и аварийное восстановление
|Service|ОПИСАНИЕ|
|------|--------|
| [Azure&nbsp;Backup](../backup/backup-introduction-to-azure-backup.md)| Служба Azure, используемая для резервного копирования и восстановления данных в облаке Azure. |
| [Azure&nbsp;Site&nbsp;Recovery](../site-recovery/site-recovery-overview.md)|Онлайн-служба, которая реплицирует рабочие нагрузки, выполняемые на физических и виртуальных машинах, с основного сайта в дополнительное расположение, чтобы включить восстановление служб после сбоя. |

## <a name="networking"></a>Сеть
|Service|ОПИСАНИЕ|
|------|--------|
| [Группы&nbsp;безопасности&nbsp;сети](../virtual-network/virtual-networks-nsg.md)| Функция управления доступом на основе сети, использующая 5 кортежей для разрешения и запрета.  |
| [VPN-шлюз Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)| Сетевое устройство, используемое в качестве конечной точки VPN для обеспечения доступа между локальными сетями к виртуальным сетям Azure.  |
| [Шлюз приложений Azure](../application-gateway/application-gateway-introduction.md)|Расширенный балансировщик нагрузки веб-приложения, который может выполнять маршрутизацию на основе URL-адреса и выполнять SSL-разгрузку. |
| [Подсистема балансировщика нагрузки Azure](../load-balancer/load-balancer-overview.md)|Подсистема балансировки нагрузки сети приложения TCP/UDP. |
| [Azure ExpressRoute](../expressroute/expressroute-introduction.md)| Выделенная ссылка WAN между локальными сетями и виртуальными сетями Azure. |
| [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)| Балансировщик нагрузки глобальной DNS.|
| [Прокси приложения Azure](../active-directory/active-directory-application-proxy-get-started.md)| Внешний интерфейс аутентификации, используемый для защиты удаленного доступа к веб-приложениям, размещенным локально. |