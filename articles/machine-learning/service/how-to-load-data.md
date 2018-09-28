---
title: Загрузка данных с помощью пакета SDK для подготовки данных машинного обучения Azure (Python)
description: Узнайте, как загружать данные с помощью пакета SDK для подготовки данных машинного обучения Azure. Можно загрузить различные типы входных данных, указать типы и параметры файлов данных или воспользоваться функцией интеллектуального чтения SDK, чтобы автоматически обнаружить тип файла.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 000870e3273799930f519ff32d6b072d8c2d1f10
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989691"
---
#<a name="load-data-with-the-azure-machine-learning-data-prep-sdk"></a>Загрузка данных с SDK для подготовки данных машинного обучения Azure

[SDK для подготовки данных машинного обучения Azure](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py) позволяет загружать разные типы входных данных. Можно указать тип файла данных и его параметры или использовать функцию чтения пакета SDK для автоматического обнаружения типа файла.

## <a name="read-lines"></a>Чтение строк
Одним из самых простых способов загрузки данных является их чтение в качестве текстовых строк.

```
dataflow = dprep.read_lines(path='./data/text_lines.txt')
dataflow.head(5)
```
||график;|
|----|-----|
|0|Дата\|\| Минимальная температура\|\| Максимальная температура|
|1|2015-07-1 \|\| -4,1 \|\| 10,0|
|2|2015-07-2 \|\| -0,8 \|\| 10,8|
|3|2015-07-3 \|\| -7,0 \|\| 10,5|
|4.|2015-07-4 \|\| -5,5 \|\| 9,3|

После ввода данных можно получить кадр данных pandas для всего набора данных.

```
df = dataflow.to_pandas_dataframe()
df
```

||график;|
|----|-----|
|0|Дата\|\| Минимальная температура\|\| Максимальная температура|
|1|2015-07-1\|\| 4,1\|\| 10,0|
|2|2015-07-2\|\| 0,8\|\| 10,8|
|3|2015-07-3\|\| 7,0\|\| 10,5|
|4.|2015-07-4\|\| 5,5\|\| 9,3|

## <a name="read-csv"></a>Чтение CSV
При чтении файлов с разделителями можно позволить соответствующей среде выполнения вывести параметры разбора (например, разделитель, кодировка, использование заголовков и т. д.), а не предоставлять их. В данном примере попытаемся прочитать файл, указав только его местоположение. 

```
# SAS expires June 16th, 2019
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dataflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0||stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|АЛАБАМА|1|101710|Округ Хейл|10171002158| |
|2|АЛАБАМА|1|101710|Округ Хейл|10171002162| |
|3|АЛАБАМА|1|101710|Округ Хейл|10171002156| |
|4.|АЛАБАМА|1|101710|Округ Хейл|10171000588|2|

Один из параметров, которые можно указать, — это число строк, которые необходимо пропустить, в считываемых файлах. Используйте следующий код, чтобы отфильтровать повторяющиеся строки.
```
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1)
dataflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|АЛАБАМА|1|101710|Округ Хейл|10171002158|29|
|1|АЛАБАМА|1|101710|Округ Хейл|10171002162|40 |
|2|АЛАБАМА|1|101710|Округ Хейл|10171002156| 43|
|3|АЛАБАМА|1|101710|Округ Хейл|10171000588|2|
|4.|АЛАБАМА|1|101710|Округ Хейл|10171000589|23 |

Затем можно просмотреть типы данных столбцов.
```
dataflow.head(1).dtypes

stnam                     object
fipst                     object
leaid                     object
leanm10                   object
ncessch                   object
schnam10                  object
MAM_MTH00numvalid_1011    object
dtype: object
```
К сожалению, все наши столбцы возвратились как строки. Это обусловлено тем, что по умолчанию SDK для подготовки данных машинного обучения Azure не меняет тип данных. Мы считываем данные из текстового файла, поэтому SDK считывает все значения как строки. Тем не менее для этого примера нужно выполнить разбор числовых столбцов в качестве чисел. Для этого можно задать для параметра inference_arguments значение current_culture.

```
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dataflow.head(1).dtypes

stnam                      object
fipst                     float64
leaid                     float64
leanm10                    object
ncessch                   float64
schnam10                   object
ALL_MTH00numvalid_1011    float64
dtype: object
```
Несколько столбцов были правильно определены как числа, а их тип определен как float64. После ввода данных можно получить кадр данных pandas для всего набора данных.

```
df = dataflow.to_pandas_dataframe()
df
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|АЛАБАМА|Округ Хейл|1,017100e+10|49,0|
|1|АЛАБАМА|Округ Хейл|1,017100e+10|40,0|
|2|АЛАБАМА|Округ Хейл|1,017100e+10|43,0|
|3|АЛАБАМА|Округ Хейл|1,017100e+10|2,0|
|4.|АЛАБАМА|Округ Хейл|1,017100e+10|23,0|

## <a name="read-excel"></a>Чтение Excel
SDK для подготовки данных машинного обучения Azure включает функцию `read_excel` для загрузки файлов Excel.
```
dataflow = dprep.read_excel(path='./data/excel.xlsx')
dataflow.head(5)
```
||Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|Гоба|Железо, IVB|60000000,0|Найдено|1920,0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |–19,58333|17,91667|
|1|Кейп-Йорк|Железо, IIIAB|58200000,0|Найдено|1818,0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |76,13333|–64,93333|
|2|Кампо-дель-Сьело|Железо, IAB-MG|50000000,0|Найдено|1576,0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |–27,46667|–60,58333|
|3|Canyon Diablo|Железо, IAB-MG|30000000,0|Найдено|1891,0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |35,05000|–111,03333|
|4.|Armanty|Железо, IIIE|28000000,0|Найдено|1898,0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |47,00000|88,00000|

Вы загрузили первый лист файла Excel. Того же результата можно достичь, явно указав имя листа, который требуется загрузить. Если требуется вместо этого загрузить второй лист, можно указать его имя в качестве аргумента.
```
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dataflow.head(5)
```

||Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|None|None|None|None|None|None|None|None|None|
|1|None|None|None|None|None|None|None|None|None|
|2|None|None|None|None|None|None|None|None|None|
|3|RANK;|Название|Студия|Весь мир|Эта страна / %|Column1|Другие страны / %|Column2|Год ^|
|4.|1|Аватар|Фокс|2788|760,5|0,273|2027,5|0,727|2009^|5|

Как вы видите, в таблице на втором листе имеются заголовки и три пустые строки. Необходимо изменить аргументы функции соответственно.
```
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_header=True, skip_rows=3)
df = dataflow.to_pandas_dataframe()
df
```

||RANK;|Название|Студия|Весь мир|Эта страна / %|Column1|Другие страны / %|Column2|Год ^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Аватар|Фокс|2788|760,5|0,273|2027,5|0,727|2009^|
|1|2|Титаник|Par.|2186,8|658,7|0,301|1528,1|0,699|1997^|
|2|3|Мстители (Marvel)|BV|1518,6|623,4|0,41|895,2|0,59|2012|
|3|4.|Гарри Поттер и Дары Смерти. Часть 2|WB|1341,5|381|0,284|960,5|0,716|2011|
|4.|5|Холодное сердце|BV|1274,2|400,7|0,314|873,5|0,686|2013|

## <a name="read-fixed-width-files"></a>Чтение файлов с фиксированной шириной
Для файлов с фиксированной шириной можно указать список смещений. Предполагается, что первый столбец всегда начинается со смещения 0.

```
dataflow = dprep.read_fwf('./data/fixed_width_file.txt', offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dataflow.head(5)
```
||010000|99999|BOGUS NORWAY|НЕТ|NO_1|ENRS|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|BOGUS NORWAY|НЕТ|НЕТ|ENSO||||
|1|010010|99999|JAN MAYEN|НЕТ|JN|ENJA|+70933|-008667|+00090|
|2|010013|99999|ROST|НЕТ|НЕТ|||||
|3|010014|99999|SOERSTOKKEN|НЕТ|НЕТ|ENSO|+59783|+005350|+00500|
|4.|010015|99999|BRINGELAND|НЕТ|НЕТ|ENBL|+61383|+005867|+03270|


Если в файлах нет заголовков, считается, что данные начинаются с первой строки. Передавая `PromoteHeadersMode.NONE` аргументу ключевых слов заголовка, можно избежать обнаружения заголовка и получить правильные данные.

```
dataflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)

df = dataflow.to_pandas_dataframe()
df
```
||Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|BOGUS NORWAY|НЕТ|NO_1|ENRS|Column7|Column8|Column9|
|1|010003|99999|BOGUS NORWAY|НЕТ|НЕТ|ENSO||||
|2|010010|99999|JAN MAYEN|НЕТ|JN|ENJA|+70933|-008667|+00090|
|3|010013|99999|ROST|НЕТ|НЕТ|||||
|4.|010014|99999|SOERSTOKKEN|НЕТ|НЕТ|ENSO|+59783|+005350|+00500|
|5|010015|99999|BRINGELAND|НЕТ|НЕТ|ENBL|+61383|+005867|+03270|

## <a name="read-sql"></a>Чтение SQL
SDK для подготовки данных машинного обучения Azure также может загружать данные с серверов SQL. В настоящее время поддерживается только Microsoft SQL Server.
Для чтения данных с SQL Server создайте объект источника данных, содержащий правильную информацию.

```
secret = dprep.register_secret("[SECRET-USERNAME]", "[SECRET-PASSWORD]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=[DATABASE-PASSWORD])
```
Как вы видите, параметр пароля `MSSQLDataSource` принимает секретный объект. Секретный объект можно получить двумя способами:
-   Зарегистрируйте секрет и его значение в подсистеме выполнения. 
-   Создайте секрет только с идентификатором (полезно, если значение секрета уже зарегистрировано в среде выполнения).

После создания объекта источника данных можно продолжать считывать данные.
```
dataflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dataflow.head(5)
```

||ProductID|ИМЯ|ProductNumber|Цвет|StandardCost|ListPrice|Размер|Вес|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|ThumbNailPhoto|ThumbnailPhotoFileName|rowguid|ModifiedDate|
|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
|0|680|HL Road Frame - Black, 58|FR-R92B-58|Черный|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|None|None|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000+00:00|
|1|706|HL Road Frame - Red, 58|FR-R92R-58|Красный|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|None|None|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000+00:00|
|2|707|Шлем Sport-100, красный|HL-U509-R|Красный|13.0863|34.99|None|None|35|33|2005-07-01 00:00:00+00:00|None|None|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000+00:00|
|3|708|Шлем Sport-100, черный|HL-U509|Черный|13.0863|34.99|None|None|35|33|2005-07-01 00:00:00+00:00|None|None|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|a25a44fb-c2de-4268-958f-110b8d7621e2|2008-03-11 |10:01:36.827000+00:00|
|4.|709|Носки для горного велосипеда, M|SO-B909-M|Белый|3.3963|9.50|M|None|27|18|2005-07-01 00:00:00+00:00|2006-06-30 00:00:00+00:00|None|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|18f95f47-1540-4e02-8f1f-cc1bcb6828d0|2008-03-11 |10:01:36.827000+00:00|

```
df = dataflow.to_pandas_dataframe()
df.dtypes
```
```
ProductID                                     int64
Name                                         object
ProductNumber                                object
Color                                        object
StandardCost                                float64
ListPrice                                   float64
Size                                         object
Weight                                      float64
ProductCategoryID                             int64
ProductModelID                                int64
SellStartDate             datetime64[ns, UTC+00:00]
SellEndDate                                  object
DiscontinuedDate                             object
ThumbNailPhoto                               object
ThumbnailPhotoFileName                       object
rowguid                                      object
ModifiedDate              datetime64[ns, UTC+00:00]
dtype: object
```

## <a name="read-from-azure-data-lake-storage"></a>Чтение из хранилища Azure Data Lake Storage
Существует два способа получения пакетом SDK необходимого токена OAuth для доступа к хранилищу Azure Data Lake Storage:
-   Получить маркер доступа из последней сессии входа пользователя через интерфейс командной строки Azure
-   Использовать субъект-службу (SP) и сертификат как секрет

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>Использовать маркер доступа из недавнего сеанса интерфейса командной строки Azure
На локальном компьютере выполните следующую команду:

> [!NOTE] 
> Если учетная запись пользователя является участником нескольких клиентов Azure, необходимо указать соответствующий клиент в форме имени хоста URL-адреса AAD.


```
az login
az account show --query tenantId
dataflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dataflow.head(5) head
```
### <a name="create-a-service-principal-with-the-azure-cli"></a>Создание субъекта-службы с помощью Azure CLI
Можно использовать интерфейс командной строки Azure для создания субъекта-службы и соответствующего сертификата. Этот субъект-служба настроен в качестве устройства чтения, а область его действия ограничена учетной записью Azure Data Lake Storage dpreptestfiles
```
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```
Эта команда выдает `appId` и путь к файлу сертификата (обычно в домашней папке). Файл .crt содержит открытый сертификат и закрытый ключ в формате PEM.

Извлечение отпечатка:
```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

##### <a name="configure-an-azure-data-lake-storage-account-for-the-service-principal"></a>Настройка учетной записи Azure Data Lake Storage для субъекта-службы
Чтобы настроить ACL для файловой системы Azure Data Lake Storage, используйте objectId пользователя или, например, субъект-службу:
```
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```
##### <a name="configure-read-and-execute-access-for-the-azure-data-lake-storage-file-system"></a>Настройте доступ к файловой системе Azure Data Lake Storage с правом на чтение и выполнение.
Так как базовая модель ACL HDFS не поддерживает наследование, необходимо настроить ACL для папок и файлов по отдельности.
```
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r-x" --path /
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r--" --path /farmers-markets.csv
```
```
certThumbprint = 'C2:08:9D:9E:D1:74:FC:EB:E9:7E:63:96:37:1C:13:88:5E:B9:2C:84'
certificate = ''
with open('./data/adls-dpreptestfiles.crt', 'rt', encoding='utf-8') as crtFile:
    certificate = crtFile.read()

servicePrincipalAppId = "8dd38f34-1fcb-4ff9-accd-7cd60b757174"
```
#### <a name="acquire-an-oauth-access-token"></a>Получение маркера доступа OAuth
Используйте пакет `adal` (через `pip install adal`), чтобы создать контекст аутентификации на клиенте MSFT и получить маркер доступа OAuth. Что касается ADLS, ресурс в запросе маркера должен предназначаться https://datalake.azure.net, отличающемуся от большинства других ресурсов Azure.

```
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext('https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate('https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dataflow = dprep.read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dataflow.to_pandas_dataframe().head()
```

||FMID|MarketName|Веб-сайт|street|city|Округ|
|----|------|-----|----|----|----|----|
|0|1012063|Caledonia Farmers Market Association, Дэнвилл|https://sites.google.com/site/caledoniafarmers... ||Дэнвилл|Каледония|
|1|1011871|Stearns Homestead Farmers' Market|http://Stearnshomestead.com |6975 Ridge Road|Парма|Кайахога|
|2|1011878|100 Mile Market|http://www.pfcmarkets.com |507 Harrison St|Каламазу|Каламазу|
|3|1009364|106 S. Main Street Farmers Market|http://thetownofsixmile.wordpress.com/ |106 S. Main Street|Сикс Майл|||
|4.|1010691|10th Steet Community Farmers Market|http://agrimissouri.com/mo-grown/grodetail.php... |10th Street and Poplar|Ламар|Бартон|

