---
title: Запрос данных Avro с помощью Azure Data Lake Analytics | Документы Майкрософт
description: В этой статье описано, как с помощью свойств текста сообщения передать данные телеметрии устройства в хранилище BLOB-объектов и запросить записанные в это хранилище данные в формате Avro.
services: iot-hub
documentationcenter: ''
author: ksaye
manager: obloch
ms.service: iot-hub
ms.topic: article
ms.date: 05/29/2018
ms.author: Kevin.Saye
ms.openlocfilehash: c56b567498047ee996018675134c252ec1de7e0c
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081374"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Запрос данных Avro с помощью Azure Data Lake Analytics

В этой статье показано, как запросить данные Avro, чтобы повысить эффективность маршрутизации сообщений из Центра Интернета вещей Azure в службы Azure. Как указано в записи блога [Azure IoT Hub message routing: now with routing on message body], Центр Интернета вещей поддерживает маршрутизацию по свойствам или тексту сообщения. Дополнительные сведения см. в разделе [Маршрутизация по тексту сообщений][Routing on message bodies]. 

Проблема заключается в том, что при маршрутизации сообщений в хранилище BLOB-объектов Azure Центр Интернета вещей Azure записывает содержимое в формате Avro, который включает свойство текста сообщения и свойство сообщения. Центр Интернета вещей поддерживает запись данных в хранилище BLOB-объектов только в формате Avro, и этот формат не используется на каких-либо других конечных точках. Дополнительные сведения см. в разделе [Использование контейнеров службы хранилища Azure][When using Azure storage containers]. Хотя формат Avro прекрасно подходит для хранения данных и сообщений, использовать его для опроса данных сложно. в отличие от формата JSON или CSV.

Чтобы устранить проблемы, связанные с требованиями и форматами нереляционных больших данных, и преодолеть эти сложности, вы можете применить шаблоны больших данных к данным масштабирования и преобразования. Один из шаблонов ("оплата за запрос") — это служба Azure Data Lake Analytics, которой посвящена эта статья. Вы можете легко выполнить этот запрос в Hadoop или другом решении, но зачастую Azure Data Lake Analytics лучше подходит при использовании подхода с оплатой за запрос. 

В U-SQL есть средство извлечения данных в формате Avro. Дополнительные сведения см. в [Пример U-SQL Avro].

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Запрос данных Avro и их экспорт в файл CSV
В этом разделе описано, как запросить данные Avro и экспортировать их в CSV-файл в хранилище BLOB-объектов Azure. При этом эти данные можно легко поместить в другие хранилища или репозитории.

1. Настройте маршрутизацию данных на конечную точку хранилища BLOB-объектов Azure в Центре Интернета вещей, используя свойство в тексте сообщения для выбора сообщений.

    ![Раздел "Пользовательские конечные точки"][img-query-avro-data-1a]

    ![Команда "Маршруты"][img-query-avro-data-1b]

2. Убедитесь, что в свойствах или тексте сообщения на устройстве настроены кодировка, тип содержимого и необходимые данные в соответствии с документацией по продукту. При просмотре этих атрибутов в средстве Device Explorer, как показано ниже, можно убедиться, что они установлены правильно.

    ![Область данных концентратора событий][img-query-avro-data-2]

3. Настройте экземпляр Azure Data Lake Store и экземпляр Data Lake Analytics. Центр Интернета вещей не выполняет маршрутизацию в экземпляр Data Lake Store, но эта маршрутизация необходима для экземпляра Data Lake Analytics.

    ![Экземпляры Data Lake Store и Data Lake Analytics][img-query-avro-data-3]

4. В Azure Data Lake Analytics настройте хранилище BLOB-объектов Azure как дополнительное хранилище. Это то же хранилище BLOB-объектов, в которое Центр Интернета вещей Azure маршрутизирует данные.

    ![Область "Источники данных"][img-query-avro-data-4]
 
5. Как было описано в разделе [Пример U-SQL Avro], вам потребуется четыре файла DLL. Отправьте эти файлы в расположение в экземпляре Data Lake Store.

    ![Четыре отправляемых файла DLL][img-query-avro-data-5] 

6. В Visual Studio создайте проект U-SQL.
 
    ![Создание проекта U-SQL][img-query-avro-data-6]

7. Вставьте содержимое приведенного ниже сценария и в созданный файл. Измените три выделенных раздела: учетная запись Data Lake Analytics, связанные пути к файлам DLL и путь к учетной записи хранения.
    
    ![Три раздела, которые необходимо изменить][img-query-avro-data-7a]

    Фактический сценарий U-SQL, который выводит данные в файл CSV, приведен ниже:
    
    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll";
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @"/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll";
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @"/Assemblies/Avro/Newtonsoft.Json.dll";
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @"/Assemblies/Avro/log4net.dll";

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @"wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}";
        DECLARE @output_file string = @"/output/output.csv";

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@"
        {
        ""type"":""record"",
        ""name"":""Message"",
        ""namespace"":""Microsoft.Azure.Devices"",
        ""fields"":[{
        ""name"":""EnqueuedTimeUtc"",
        ""type"":""string""
        },
        {
        ""name"":""Properties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""SystemProperties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""Body"",
        ""type"":[""null"",""bytes""]
        }
        ]
        }");

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```    

    Для запуска этого сценария в Data Lake Analytics потребовалось пять минут. Сценарий был ограничен 10 аналитическими единицами и обработал 177 файлов. Результат в виде файла CSV показан на следующем рисунке:
    
    ![Результаты вывода в файл CSV][img-query-avro-data-7b]

    ![Выходные данные, преобразованные в файл CSV][img-query-avro-data-7c]

    Для анализа JSON перейдите к шагу 8.
    
8. Большинство сообщений Центра Интернета вещей находятся в формате файла JSON. Добавив следующие строки, вы сможете проанализировать сообщение в файле JSON. Это позволит добавить предложения WHERE и выводить только необходимые данные.

    ```sql
       @jsonify = SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body)) AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                   message["event"] AS msgevent,
                   message["object"] AS msgobject,
                   message["status"] AS msgstatus,
                   message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

    В выводе отображается столбец для каждого элемента в команде `SELECT`. 
    
    ![Вывод со столбцами для каждого элемента][img-query-avro-data-8]

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как запросить данные Avro, чтобы повысить эффективность маршрутизации сообщений из Центра Интернета вещей Azure в службы Azure.

Примеры комплексных решений, в которых используется Центр Интернета вещей, см. в [документации по акселераторам решений для удаленного мониторинга Интернета вещей Azure][lnk-iot-sa-land].

Дополнительные сведения о разработке решений с помощью Центра Интернета вещей см. в [Руководство разработчика для Центра Интернета вещей].

Дополнительные сведения о маршрутизации сообщений в Центре Интернета вещей см. в статье [Отправка и получение сообщений в Центре Интернета вещей][lnk-devguide-messaging].

<!-- Images -->
[img-query-avro-data-1a]: ./media/iot-hub-query-avro-data/query-avro-data-1a.png
[img-query-avro-data-1b]: ./media/iot-hub-query-avro-data/query-avro-data-1b.png
[img-query-avro-data-2]: ./media/iot-hub-query-avro-data/query-avro-data-2.png
[img-query-avro-data-3]: ./media/iot-hub-query-avro-data/query-avro-data-3.png
[img-query-avro-data-4]: ./media/iot-hub-query-avro-data/query-avro-data-4.png
[img-query-avro-data-5]: ./media/iot-hub-query-avro-data/query-avro-data-5.png
[img-query-avro-data-6]: ./media/iot-hub-query-avro-data/query-avro-data-6.png
[img-query-avro-data-7a]: ./media/iot-hub-query-avro-data/query-avro-data-7a.png
[img-query-avro-data-7b]: ./media/iot-hub-query-avro-data/query-avro-data-7b.png
[img-query-avro-data-7c]: ./media/iot-hub-query-avro-data/query-avro-data-7c.png
[img-query-avro-data-8]: ./media/iot-hub-query-avro-data/query-avro-data-8.png

<!-- Links -->
[Azure IoT Hub message routing: now with routing on message body]: https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/ (Маршрутизация сообщений с помощью Центра Интернета вещей: маршрутизация по тексту сообщений)

[Routing on message bodies]: iot-hub-devguide-query-language.md#routing-on-message-bodies
[When using Azure storage containers]:iot-hub-devguide-endpoints.md#when-using-azure-storage-containers

[Пример U-SQL Avro]:https://github.com/Azure/usql/tree/master/Examples/AvroExamples

[lnk-iot-sa-land]: ../iot-accelerators/index.yml
[Руководство разработчика для Центра Интернета вещей]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
