---
title: Часто задаваемые вопросы о службе "Импорт и экспорт Azure" | Документация Майкрософт
description: Ответы на часто задаваемые вопросы о службе "Импорт и экспорт Azure".
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/22/2018
ms.author: alkohli
ms.component: common
ms.openlocfilehash: edaff86531a9c40064d25a046bbbb70f48b75c84
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027135"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Служба "Импорт и экспорт Azure". Часто задаваемые вопросы 
Ниже приведены вопросы, которые могут возникнуть при использовании службы "Импорт и экспорт Azure" для передачи данных в хранилище Azure, и ответы на них. Вопросы и ответы упорядочены по следующим категориям:

- Сведения о службе "Импорт и экспорт"
- подготовка дисков для импорта и экспорта;
- задания импорта и экспорта;
- Отправка дисков
- Разное 

## <a name="about-importexport-service"></a>Сведения о службе "Импорт и экспорт"

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Можно ли скопировать хранилище файлов Azure с помощью службы "Импорт и экспорт Azure"?

Да. Служба "Импорт и экспорт Azure" поддерживает импорт в хранилище файлов Azure. Сейчас экспорт файлов Azure не поддерживается.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Доступна ли служба "Импорт и экспорт Azure" для подписок CSP?

Да. Служба "Импорт и экспорт Azure" поддерживает подписки поставщиков облачных решений (CSP).

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Можно ли использовать службу "Импорт и экспорт Azure" для копирования почтовых ящиков PST и данных SharePoint в Office 365?

Да. Дополнительные сведения см. в статье [Import PST files or SharePoint data to Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx) (Общие сведения об импорте PST-файлов и данных SharePoint в Office 365).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Можно ли использовать службу "Импорт и экспорт Azure" для копирования резервных копий в службу Azure Backup в автономном режиме?

Да. Дополнительные сведения см. в статье [Автономное резервное копирование в службе архивации Azure](../../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Можно ли приобрести диски для заданий импорта и экспорта у корпорации Майкрософт?

Нет. Необходимо предоставлять собственные диски для заданий импорта и экспорта.


## <a name="preparing-disks-for-importexport"></a>Подготовка дисков к импорту и экспорту

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Можно ли пропустить шаг подготовки дисков для задания импорта? Можно ли подготовить диск без копирования?

Нет. Каждый диск, используемый для импорта данных, необходимо подготовить с помощью средства WAImportExport. Также используйте это средство для копирования данных на диск.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Нужно ли провести какую-либо подготовку диска при создании задания экспорта?

Нет. Рекомендуется выполнить некоторые предварительные проверки. Чтобы узнать необходимое количество дисков, используйте команду PreviewExport средства WAImportExport. Дополнительные сведения см. в разделе [Предварительный просмотр использования дисков для задания экспорта](https://msdn.microsoft.com/library/azure/dn722414.aspx). Эта команда позволяет просмотреть потребление диска для выбранных больших двоичных объектов в зависимости от объема дисков, которые вы собираетесь использовать. Убедитесь также, что вы можете считывать и записывать данные на жесткий диск, который предоставлен для задания экспорта.

## <a name="importexport-jobs"></a>Задания импорта и экспорта

### <a name="can-i-cancel-my-job"></a>Можно ли отменить задание?
Да. Вы можете отменить задание, если его состояние указано как **Создание** или **Отправка**. Если указано другое состояние, задание отменить нельзя и его выполнение продолжается до последнего этапа.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Как долго на портале Azure отображаются состояния выполненных заданий?
Состояние выполненных заданий можно просматривать в течение 90 дней. Через 90 дней завершенные задания будут удалены.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Что мне нужно делать, чтобы импортировать или экспортировать более 10 дисков?
В рамках одного задания импорта или экспорта может обрабатываться только 10 дисков. Если необходимо поставить более 10 дисков, нужно создать несколько заданий. Диски, связанные с одним и тем же заданием, необходимо отправлять одним пакетом. Чтобы получить дополнительные сведения и инструкции, если емкость данных охватывает несколько заданий импорта дисков, свяжитесь с корпорацией Майкрософт по этому адресу: bulkimport@microsoft.com.                                                              

## <a name="shipping-disks"></a>Отправка дисков

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Какое максимальное количество жестких дисков для одной отгрузки?
Количество жестких дисков в одной отгрузке не ограничено. Если диски предназначены для нескольких заданий, можно: 
- пометить диски соответствующими именами заданий;
- обновить задания с использованием номера для отслеживания с суффиксом -1, -2 и т. д.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>Нужно ли отправлять вместе с жесткими дисками что-то еще?
Следует отправлять только жесткие диски. Не посылайте ничего другого, например, кабели питания или USB-кабели.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Обязательно ли отправлять диски через FedEx или DHL?
Вы можете отправлять диски в центр обработки данных Azure через любую транспортную компанию, включая FedEx, DHL и UPS, или почтовое ведомство США. Тем не менее для обратной отправки дисков из центра обработки данных следует указать следующее:

- номер учетной записи FedEx в США и ЕС или
- номер учетной записи DHL в регионах Азии и Австралии.

### <a name="are-there-any-restrictions-with-shipping-my-drive-internationally"></a>Существуют ли ограничения на международную пересылку дисков?
Обратите внимание, что отправляемые физические носители могут пересекать международные границы. Вы несете ответственность за то, что ваши физические носители и данные импортируются и (или0 экспортируются в соответствии с действующим законодательством. Перед отправкой физических носителей проконсультируйтесь и удостоверьтесь, что они эти носители и данные могут быть отправлены в определенный центр обработки данных на законных основаниях. Это поможет Майкрософт своевременно получить посылку.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>При создании задания был указан адрес доставки, который отличается от расположения учетной записи хранения. Что делать?

Некоторые расположения учетных записей хранения сопоставляются с альтернативными расположениями доставки. Ранее доступные места доставки можно было также временно сопоставить с другими расположениями. Перед отправкой дисков обязательно проверяйте адрес доставки, указанный при создании задания.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>При отправке диска транспортный агент запрашивает адрес и номер телефона контактного лица в центре обработки данных. Что нужно указать?

Номер телефона и адрес контактного лица в центре обработки данных предоставляется в ходе создания задания.


## <a name="miscellaneous"></a>Разное

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Что произойдет, если я случайно отправлю диски, которые не соответствуют требованиям поддержки?

Центр обработки данных Azure вернет диски, которые не соответствуют требованиям поддержки. Если только некоторые диски в посылке соответствуют требованиям поддержки, эти диски будут обработаны, а диски, не соответствующие требованиям, будут возвращены вам.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>Форматируете ли вы диски перед их возвращением?

Нет. Все диски шифруются по технологии BitLocker.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Каким образом можно получить доступ к импортируемым службой данным?

Чтобы получить доступ к данным в учетной записи хранения Azure, можно использовать портал Azure или [Обозреватель службы хранилища](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Как будут выглядеть мои данные в учетной записи хранения после завершения импорта? Сохранится ли иерархия моих каталогов?

При подготовке жесткого диска к заданию импорта место назначения определяется параметром /DstBlobPathOrPrefix в CSV-файле набора данных. Это целевой контейнер в учетной записи хранения, в который копируются данные с жесткого диска. В целевом контейнере для папок с жесткого диска создаются виртуальные каталоги, а для файлов — большие двоичные объекты. 

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Если на диске есть файлы, уже существующие в учетной записи хранения, будут ли эти файлы или большие двоичные объекты перезаписаны службой?

Это зависит от обстоятельств. При подготовке диска можно указать, нужно ли перезаписать целевые файлы или игнорировать совпадения. Для этого в CSV-файле набора данных укажите соответствующее значение параметра Disposition (rename, no-overwrite, overwrite). По умолчанию служба переименовывает новые файлы, а не перезаписывает имеющиеся большие двоичные объекты или файлы.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Совместимо ли средство WAImportExport с 32-разрядной операционной системой?
Нет. Средство WAImportExport совместимо только с 64-разрядной операционной системой Windows. Полный список поддерживаемых операционных систем см. в [этой статье](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements). 


### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Какой максимальный размер блочного и страничного BLOB-объектов, поддерживаемый службой "Импорт и экспорт Azure"?

Максимальный размер блочного BLOB-объекта составляет 4768 ТБ или 5 000 000 МБ.
А максимальный размер страничного BLOB-объекта равен 1 ТБ.


### <a name="does-azure-importexport-support-aes-256-encryption"></a>Поддерживает ли служба "Импорт и экспорт Azure" шифрование AES-256?
Служба "Импорт и экспорт Azure" использует шифрование AES-128 BitLocker по умолчанию. Его можно заменить на AES-256 перед копированием, выполнив шифрование с помощью BitLocker вручную. 

- Ниже приведен пример команды при использовании средства [WAImportExport V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip)
    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
    ```
- При использовании [WAImportExport V2](https://www.microsoft.com/en-us/download/details.aspx?id=55280) укажите "AlreadyEncrypted" и введите ключ в CSV-файл набора дисков.
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    ```

## <a name="next-steps"></a>Дополнительная информация

* [Что такое служба "Импорт и экспорт Azure"?](storage-import-export-service.md)


