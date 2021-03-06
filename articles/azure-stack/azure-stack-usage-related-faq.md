---
title: Часто задаваемые вопросы об API использования | Документация Майкрософт
description: Список средств измерения в Azure Stack, сравнение с API использования в Azure, время использования и время сообщения, коды ошибок.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: e4bb17bd068076a9ffa9bcab5826f0efa84a19d7
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47406011"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Часто задаваемые вопросы об API использования в Azure Stack

В этой статье содержатся ответы на некоторые часто задаваемые вопросы об API использования в Azure Stack.

## <a name="what-meter-ids-can-i-see"></a>Какие доступны идентификаторы средств измерения?
Отчеты об использовании для следующих поставщиков ресурсов:

### <a name="network"></a>Сеть
  
**Идентификатор учета**: F271A8A388C44D93956A063E1D2FA80B  
**Название учета**: Static IP Address Usage  
**Единица измерения**: IP-адреса  
**Примечания**: количество используемых IP-адресов. При вызове API использования с детализацией до дней индикатор возвращает число IP-адресов, умноженное на количество часов.  
  
**Идентификатор учета**: 9E2739BA86744796B465F64674B822BA  
**Название учета**: Dynamic IP Address Usage  
**Единица измерения**: IP-адреса  
**Примечания**: количество используемых IP-адресов. При вызове API использования с детализацией до дней индикатор возвращает число IP-адресов, умноженное на количество часов.  
  
### <a name="storage"></a>Хранилище
  
**Идентификатор учета**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Название учета**: TableCapacity  
**Единица измерения**: ГБ\*час  
**Примечания**: общая емкость, потребляемая таблицами.  
  
**Идентификатор учета**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Название учета**: PageBlobCapacity  
**Единица измерения**: ГБ\*час  
**Примечания**: общая емкость, потребляемая страничными BLOB-объектами.  
  
**Идентификатор учета**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Название учета**: QueueCapacity  
**Единица измерения**: ГБ\*час  
**Примечания**: общая емкость, потребляемая очередью.  
  
**Идентификатор учета**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**Название учета**: BlockBlobCapacity  
**Единица измерения**: ГБ\*час  
**Примечания**: общая емкость, потребляемая блочными BLOB-объектами.  
  
**Идентификатор учета**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Название учета**: TableTransactions  
**Единица измерения**: количество запросов (в десятках тысяч)  
**Примечания**: запросы к службе таблиц (в десятках тысяч).  
  
**Идентификатор учета**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**Название учета**: TableDataTransIn  
**Единица измерения**: объем входящих данных (в ГБ)  
**Примечания**: объем входящих данных службы таблиц (в ГБ).  
  
**Идентификатор учета**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**Название учета**: TableDataTransOut  
**Единица измерения**: объем исходящих данных (в ГБ)  
**Примечания**: объем исходящих данных службы таблиц (в ГБ).  
  
**Идентификатор учета**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**Название учета**: BlobTransactions  
**Единица измерения**: количество запросов (в десятках тысяч)  
**Примечания**: запросы к службе BLOB-объектов (в десятках тысяч).  
  
**Идентификатор учета**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**Название учета**: BlobDataTransIn  
**Единица измерения**: объем входящих данных (в ГБ)  
**Примечания**: объем входящих данных службы BLOB-объектов (в ГБ).  
  
**Идентификатор учета**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**Название учета**: BlobDataTransOut  
**Единица измерения**: объем исходящих данных (в ГБ)  
**Примечания**: объем исходящих данных службы BLOB-объектов (в ГБ).  
  
**Идентификатор учета**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**Название учета**: QueueTransactions  
**Единица измерения**: количество запросов (в десятках тысяч)  
**Примечания**: запросы к службе очередей (в десятках тысяч).  
  
**Идентификатор учета**: E518E809-E369-4A45-9274-2017B29FFF25  
**Название учета**: QueueDataTransIn  
**Единица измерения**: объем входящих данных (в ГБ)  
**Примечания**: объем входящих данных службы очередей (в ГБ).  
  
**Идентификатор учета**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Название учета**: QueueDataTransOut  
**Единица измерения**: объем исходящих данных (в ГБ)  
**Примечания**: объем исходящих данных службы очередей (в ГБ).  

### <a name="compute"></a>Службы вычислений 
  
**Идентификатор учета**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**Название учета**: Base VM Size Hours  
**Единица измерения**: час виртуального ядра  
**Примечания**: количество виртуальных ядер, умноженное на количество часов работы виртуальной машины.  
  
**Идентификатор учета**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**Название учета**: Windows VM Size Hours  
**Единица измерения**: час виртуального ядра  
**Примечания**: количество виртуальных ядер, умноженное на количество часов работы виртуальной машины.  
  
**Идентификатор учета**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**Название учета**: VM size hours  
**Единица измерения**: час виртуальной машины  
**Примечания**: с учетом как базовой виртуальной машины, так и виртуальной машины Windows. Корректировка по ядрам не учитывается  
  
### <a name="managed-disks"></a>Управляемые диски

**Идентификатор учета**: 5d76e09f-4567-452a-94cc-7d1f097761f0   
**Название учета**: S4   
**Единица измерения**: число дисков   
**Примечания**: Управляемый диск класса Standard — 32 ГБ 

**Идентификатор учета**: dc9fc6a9-0782-432a-b8dc-978130457494   
**Название учета**: S6   
**Единица измерения**: число дисков   
**Примечания**: Управляемый диск класса Standard — 64 ГБ 

**Идентификатор учета**: e5572fce-9f58-49d7-840c-b168c0f01fff   
**Название учета**: S10   
**Единица измерения**: число дисков   
**Примечания**: Управляемый диск класса Standard — 128 ГБ 

**Идентификатор учета**: 9a8caedd-1195-4cd5-80b4-a4c22f9302b8   
**Название учета**: S15   
**Единица измерения**: число дисков   
**Примечания**: Управляемый диск класса Standard — 256 ГБ 

**Идентификатор учета**: 5938f8da-0ecd-4c48-8d5a-c7c6c23546be   
**Название учета**: S20   
**Единица измерения**: число дисков      
**Примечания**: Управляемый диск класса Standard — 512 ГБ 

**Идентификатор учета**: 7705a158-bd8b-4b2b-b4c2-0782343b81e6   
**Название учета**: S30   
**Единица измерения**: число дисков   
**Примечания**: Управляемый диск класса Standard — 1024 ГБ 

**Идентификатор учета**: d9aac1eb-a5d1-42f2-b617-9e3ea94fed88   
**Название учета**: S40   
**Единица измерения**: число дисков   
**Примечания**: Управляемый диск класса Standard — 2048 ГБ 

**Идентификатор учета**: a54899dd-458e-4a40-9abd-f57cafd936a7   
**Название учета**: S50   
**Единица измерения**: число дисков   
**Примечания**: Управляемый диск класса Standard — 4096 ГБ 

**Идентификатор учета**: 5c105f5f-cbdf-435c-b49b-3c7174856dcc   
**Название учета**: P4   
**Единица измерения**: число дисков   
**Примечания**: Управляемый диск класса Premium — 32 ГБ 

**Идентификатор учета**: 518b412b-1927-4f25-985f-4aea24e55c4f   
**Название учета**: P6   
**Единица измерения**: число дисков   
**Примечания**: Управляемый диск класса Premium — 64 ГБ 

**Идентификатор учета**: 5cfb1fed-0902-49e3-8217-9add946fd624   
**Название учета**: P10   
**Единица измерения**: число дисков   
**Примечания**: Управляемый диск класса Premium — 128 ГБ  

**Идентификатор учета**: 8de91c94-f740-4d9a-b665-bd5974fa08d4   
**Название учета**: P15  
**Единица измерения**: число дисков   
**Примечания**: Управляемый диск класса Premium — 256 ГБ 

**Идентификатор учета**: c7e7839c-293b-4761-ae4c-848eda91130b   
**Название учета**: P20   
**Единица измерения**: число дисков   
**Примечания**: Управляемый диск класса Premium — 512 ГБ 

**Идентификатор учета**: 9f502103-adf4-4488-b494-456c95d23a9f   
**Название учета**: P30   
**Единица измерения**: число дисков   
**Примечания**: Управляемый диск класса Premium — 1024 ГБ 

**Идентификатор учета**: 043757fc-049f-4e8b-8379-45bb203c36b1   
**Название учета**: P40   
**Единица измерения**: число дисков    
**Примечания**: Управляемый диск класса Premium — 2048 ГБ 

**Идентификатор учета**: c0342c6f-810b-4942-85d3-6eaa561b6570   
**Название учета**: P50   
**Единица измерения**: число дисков   
**Примечания**: Управляемый диск класса Premium — 4096 ГБ 

**Идентификатор учета**: 8a409390-1913-40ae-917b-08d0f16f3c38   
**Название учета**: ActualStandardDiskSize   
**Единица измерения**: байт      
**Примечания**: фактический размер управляемого диска класса Standard  

**Идентификатор учета**: 1273b16f-8458-4c34-8ce2-a515de551ef6  
**Название учета**: ActualPremiumDiskSize   
**Единица измерения**: байт      
**Примечания**: фактический размер управляемого диска класса Premium 

**Идентификатор учета**: 89009682-df7f-44fe-aeb1-63fba3ddbf4c  
**Название учета**: ActualStandardSnapshotSize   
**Единица измерения**: байт   
**Примечания**: моментальный снимок фактического размера управляемого диска класса Standard.  

**Идентификатор учета**: 95b0c03f-8a82-4524-8961-ccfbf575f536   
**Название учета**: ActualPremiumSnapshotSize   
**Единица измерения**: байт   
**Примечания**: фактический размер управляемого диска класса Premium.   

### <a name="sql-rp"></a>Sql RP
  
**Идентификатор учета**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Название учета**: DatabaseSizeHourSqlMeter  
**Единица измерения**: MБ\*час  
**Примечания**: общая емкость базы данных в момент создания. При вызове API использования с детализацией до дней индикатор возвращает число мегабайтов, умноженное на количество часов.  
  
### <a name="mysql-rp"></a>MySql RP   
  
**Идентификатор учета**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**Название учета**: DatabaseSizeHourMySqlMeter  
**Единица измерения**: MБ\*час  
**Примечания**: общая емкость базы данных в момент создания. При вызове API использования с детализацией до дней индикатор возвращает число мегабайтов, умноженное на количество часов.    
### <a name="key-vault"></a>Key Vault   
  
**Идентификатор учета**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**Название учета**: Key Vault transactions  
**Единица измерения**: количество запросов (в десятках тысяч)  
**Примечания**: количество запросов, полученных REST API для плоскости данных хранилища ключей.  
  
**Идентификатор учета**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**Название учета**: Advanced keys transactions  
**Единица измерения**: 10 000 транзакций  
**Примечания**: транзакции ключей RSA 3K или 4K, использующих технологию ECC. (предварительная версия)  
  
### <a name="app-service"></a>Служба приложений   
  
**Идентификатор учета**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**Название учета**: App Service  
**Единица измерения**: час виртуального ядра  
**Примечания**: количество виртуальных ядер, используемых для запуска службы приложений. Примечание. На основе этой метрики корпорация Майкрософт взимает плату за использование Службы приложений в Azure Stack. Поставщики облачных служб могут рассчитывать использование ресурсов своими клиентами на основе других метрик Службы приложений (см. ниже)  
  
**Идентификатор учета**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Название учета**: Functions Requests  
**Единица измерения**: 10 запросов  
**Примечания**: общее количество запрошенных выполнений (за 10 выполнений). Выполнения вычисляются при каждом выполнении функции в ответ на событие или активируются привязкой.  
  
**Идентификатор учета**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**Название учета**: Functions — Compute  
**Единица измерения**: ГБ/с  
**Примечания**: потребление ресурсов, измеряемое в гигабайтах в секунду (ГБ/с). **Наблюдаемое потребление ресурсов** рассчитывается так: средний размер памяти в гигабайтах умножается на время выполнения функции в миллисекундах. Память, используемая функцией, округляется до ближайших 128 МБ (до максимального размера памяти, составляющего 1536 МБ), а время выполнения — до ближайшей 1 мс. Минимальные значения времени выполнения и памяти для однократного выполнения функции составляют 100 мс и 128 МБ соответственно.  
  
**Идентификатор учета**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**Название учета**: Shared App Service Hours  
**Единица измерения**: 1 час  
**Примечания**: за час использования общего плана службы приложений. Тарификация по планам обслуживания производится на основе каждого приложения  
  
**Идентификатор учета**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**Название учета**: Free App Service Hours  
**Единица измерения**: 1 час  
**Примечания**: за час использования бесплатного плана службы приложений. Тарификация по планам обслуживания производится на основе каждого приложения  
  
**Идентификатор учета**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**Название учета**: Small Standard App Service Hours  
**Единица измерения**: 1 час  
**Примечания**: вычисляется на основе размера и количества экземпляров.  
  
**Идентификатор учета**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**Название учета**: Medium Standard App Service Hours  
**Единица измерения**: 1 час  
**Примечания**: вычисляется на основе размера и количества экземпляров.  
  
**Идентификатор учета**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**Название учета**: Large Standard App Service Hours  
**Единица измерения**: 1 час  
**Примечания**: вычисляется на основе размера и количества экземпляров.  
  
### <a name="custom-worker-tiers"></a>Пользовательские рабочие уровни   
  
**Идентификатор учета**: *Пользовательские рабочие уровни*  
**Название учета**: Custom Worker Tiers  
**Единица измерения**: Час  
**Примечания**: детерминированный идентификатор учета создается на основе SKU и пользовательского имени рабочего уровня. Этот идентификатор уникален для каждого пользовательского рабочего уровня  
  
**Идентификатор учета**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**Название учета**: SNI SSL  
**Единица измерения**: за привязку SSL на основе SNI  
**Примечания**: служба приложений поддерживает два типа SSL-подключений: SSL-подключения с указанием имени сервера (SNI) и SSL-подключения с IP-адресом. SSL-подключения на основе SNI работают только в современных браузерах, в то время как SSL-подключения на основе IP-адресов работают во всех.  
  
**Идентификатор учета**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**Название учета**: IP SSL  
**Единица измерения**: за привязку SSL на основе IP  
**Примечания**: служба приложений поддерживает два типа SSL-подключений: SSL-подключения с указанием имени сервера (SNI) и SSL-подключения с IP-адресом. SSL-подключения на основе SNI работают только в современных браузерах, в то время как SSL-подключения на основе IP-адресов работают во всех.  
  
**Идентификатор учета**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**Название учета**: Web Process  
**Единица измерения**:  
**Примечания**: вычисляется за активный сайт в час.  
  
**Идентификатор учета**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**Название учета**: External Egress Bandwidth  
**Единица измерения**: ГБ  
**Примечания**: общее количество байт ответа на входящий запрос + общее количество байт ответа на исходящий запрос + общее количество байт ответа на входящий запрос FTP + общее количество байт ответа на запросы веб-развертывания.  
  

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsdocsmicrosoftcomazurebillingbilling-usage-rate-card-overviewazure-resource-usage-api-preview-currently-in-public-preview"></a>Какие различия между API использования в Azure Stack и [API использования в Azure](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-usage-api-preview) (в настоящее время в общедоступной предварительной версии)?
* API использования для клиента полностью совпадает с аналогичным API в Azure, за одним исключением: в настоящее время флаг *showDetails* для Azure Stack не поддерживается.
* API использования для поставщика относится только к Azure Stack.
* В настоящее время в Azure Stack недоступен [API RateCard](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-ratecard-api-preview), который используется в Azure.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>В чем различия между временем использования и временем сообщения?
В отчетах об использовании учитываются два показателя времени:

* **Время сообщения**. Время, когда информация о событии использования поступила в систему учета использования.
* **Время использования**. Время, когда происходило использование ресурса Azure Stack.

Для некоторых событий использования вы можете заметить расхождения между временем использования временем сообщения. Задержка в любой среде может длиться до нескольких часов.

Сейчас запросы можно создавать только *по времени сообщения*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Что означают коды ошибок в API использования?
| **Код состояния HTTP** | **Код ошибки** | **Описание** |
| --- | --- | --- |
| 400/Bad Request |*NoApiVersion* |Не указан параметр запроса *api-version* (версия API). |
| 400/Bad Request |*InvalidProperty* |Свойство отсутствует или имеет недопустимое значение. Сообщение об ошибке определяет недостающее свойство в тексте ответа. |
| 400/Bad Request |*RequestEndTimeIsInFuture* |Значение *reportedEndTime* относится к будущему. Для этого аргумента не допускаются значения в будущем. |
| 400/Bad Request |*SubscriberIdIsNotDirectTenant* |В вызове к API-интерфейсу для поставщика использовался идентификатор подписки, который указывает на недопустимый клиент вызывающего объекта. |
| 400/Bad Request |*SubscriptionIdMissingInRequest* |Отсутствует идентификатор подписки вызывающего объекта. |
| 400/Bad Request |*InvalidAggregationGranularity* |В запросе указано недопустимое значение для степени детализации статистической обработки. Допустимые значения: daily (ежедневно) и hourly (каждый час). |
| 503 |*ServiceUnavailable* |Произошла повторяемая ошибка из-за перегрузки службы или регулирования запросов. |

## <a name="next-steps"></a>Дальнейшие действия
[Выставление счетов клиентам и начисление платы за использование Azure Stack](azure-stack-billing-and-chargeback.md)

[Provider Resource Usage API](azure-stack-provider-resource-api.md) (API использования ресурсов для поставщиков)

[Tenant Resource Usage API](azure-stack-tenant-resource-usage-api.md) (API использования ресурсов для клиентов)
