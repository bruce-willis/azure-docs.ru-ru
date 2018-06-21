---
title: Запрос данных Avro с помощью Azure Data Lake Analytics | Документация Майкрософт
description: В этой статье описано, как с помощью свойств текста сообщения передать данные телеметрии устройства в хранилище BLOB-объектов и запросить записанные в это хранилище данные в формате Avro.
services: iot-hub
documentationcenter: ''
author: ksaye
manager: obloch
ms.service: iot-hub
ms.topic: article
ms.date: 05/29/2018
ms.author: Kevin.Saye
ms.openlocfilehash: 98a30155c73a937042b4bea6568543fb5152d748
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726677"
---
# <a name="query-avro-data-using-azure-data-lake-analytics"></a>Запрос данных Avro с помощью Azure Data Lake Analytics

В этой статье показано, как запросить данные Avro, чтобы повысить эффективность маршрутизации сообщений из Центра Интернета вещей Azure в службы Azure. Как указано в записи блога [Azure IoT Hub message routing: now with routing on message body] (Маршрутизация сообщений с помощью Центра Интернета вещей: маршрутизация по тексту сообщений), Центр Интернета вещей поддерживает маршрутизацию по свойствам или тексту сообщения. Дополнительные сведения см. в разделе [Маршрутизация по тексту сообщений][Routing on message bodies]. 

Проблема заключается в том, что, маршрутизируя сообщения в хранилище BLOB-объектов, Центр Интернета вещей Azure записывает содержимое в формате Avro, который имеет и свойства сообщения, и свойства текста сообщения. Обратите внимание, что Центр Интернета вещей поддерживает запись данных в хранилище BLOB-объектов только в формате Avro, а этот формат не используется на каких-либо других конечных точках. Дополнительные сведения см. в разделе [Использование контейнеров службы хранилища Azure][When using Azure storage containers]. Формат Avro отлично подходит для хранения данных и сообщения. Но в этом формате труднее запрашивать данные в отличие от формата JSON или CSV.

Чтобы устранить проблемы, связанные с требованиями и форматами нереляционных больших данных, вы можете применить шаблоны больших данных к данным масштабирования и преобразования. Один из шаблонов с оплатой за запрос используется в Azure Data Lake Analytics (ADLA). Он рассматривается в этой статье. Вы можете легко выполнить этот запрос в Hadoop или другом решении, но зачастую ADLA лучше подходит при применении подхода с оплатой за запрос. В U-SQL есть средство извлечения данных в формате Avro. Дополнительные сведения и примеры использования U-SQL с данными Avro см. [U-SQL Avro Example].

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Запрос данных Avro и их экспорт в файл CSV
В этом разделе описано, как запросить данные Avro и экспортировать их в CSV-файл в хранилище BLOB-объектов Azure. Затем эти данные можно легко поместить в другие хранилища или репозитории.

1. Настройте в Центре Интернета вещей маршрутизацию данных на конечную точку хранилища BLOB-объектов Azure, выбрав сообщения с помощью свойства в их тексте.

    ![Снимок экрана шага 1а][img-query-avro-data-1a]

    ![Снимок экрана шага 1б][img-query-avro-data-1b]

2. Убедитесь, что на устройстве настроено кодирование, а также используется необходимый тип содержимого и данные в свойствах или тексте сообщения, как указано в документации по продукту. Проверить, заданы ли эти атрибуты правильно, можно в средстве Device Explorer (см. ниже).

    ![Снимок экрана шага 2][img-query-avro-data-2]

3. Настройте Azure Data Lake Store (ADLS) и экземпляр Azure Data Lake Analytics. Хотя Центр Интернета вещей Azure не маршрутизирует данные в Azure Data Lake Store, эта служба по-прежнему требуется.

    ![Снимок экрана шага 3][img-query-avro-data-3]

4. В ADLA настройте хранилище BLOB-объектов Azure как дополнительное (то же хранилище BLOB-объектов), в которое Центр Интернета вещей Azure маршрутизирует данные.

    ![Снимок экрана шага 4][img-query-avro-data-4]
 
5. Как указано в статье [U-SQL Avro Example] (Пример работы U-SQL с данными Avro), требуется 4 файла DLL.  Загрузите эти файлы в расположение в ADLS.

    ![Снимок экрана шага 5][img-query-avro-data-5] 

6. В Visual Studio создайте проект U-SQL
 
    ![Снимок экрана шага 6][img-query-avro-data-6]

7. Скопируйте содержимое приведенного ниже скрипта и вставьте его в новый файл. Измените 3 выделенные разделы: учетная запись ADLA, связанные пути к файлам DLL и правильный путь к учетной записи хранения.
    
    ![Снимок экрана шага 7а][img-query-avro-data-7a]

    Фактический скрипт U-SQL, который необходимо добавить в файл CSV, выглядит так:
    
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

    Выполнение показанного ниже скрипта в ADLA занимает 5 минут при использовании 10 единиц аналитики и обработке 177 файлов. Сводные сведение добавляются в файл CSV.
    
    ![Снимок экрана шага 7б][img-query-avro-data-7b]

    Просмотрев выходные данные, вы увидите, что содержимое Avro преобразовалось в CSV-файл. Перейдите к шагу 8, если хотите проанализировать файл JSON.
    
    ![Снимок экрана шага 7в][img-query-avro-data-7c]

    
8. Большинство сообщений Центра Интернета вещей находятся в формате JSON.  Добавив следующие строки, вы сможете проанализировать сообщение в формате JSON. Это позволит добавить предложения WHERE и вывести только необходимые данные.

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

9. Просмотрев выходные данные, вы увидите столбцы каждого элемента в команде select. 
    
    ![Снимок экрана шага 8][img-query-avro-data-8]

## <a name="next-steps"></a>Дополнительная информация
Из этой статьи вы узнали, как запросить данные Avro, чтобы повысить эффективность маршрутизации сообщений из Центра Интернета вещей Azure в службы Azure.

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

[U-SQL Avro Example]:https://github.com/Azure/usql/tree/master/Examples/AvroExamples (Пример работы U-SQL с данными Avro)

[lnk-iot-sa-land]: ../iot-accelerators/index.md
[Руководство разработчика для Центра Интернета вещей]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
