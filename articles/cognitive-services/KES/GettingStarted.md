---
title: Начало работы со Службой поиска и распознавания данных | Документация Майкрософт
description: Используйте Службу поиска и распознавания данных (KES), чтобы создать обработчик для интерактивного поиска в научных публикациях в Microsoft Cognitive Services.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 02dc9368eef02d6fa507335ef3171e923412acca
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380765"
---
<a name="getting-started"></a>
# <a name="get-started-with-the-knowledge-exploration-service"></a>Начало работы со Службой поиска и распознавания данных
В этом пошаговом руководстве используйте Службу поиска и распознавания данных (KES), чтобы создать обработчик для интерактивного поиска научных публикаций. Вы можете установить программу командной строки, [`kes.exe`](CommandLine.md) и все примеры файлов из [пакета SDK Службы поиска и распознавания данных](https://www.microsoft.com/en-us/download/details.aspx?id=51488).

Пример научных публикаций содержит образцы 1000 научных статей, опубликованных исследователями корпорации Майкрософт.  Каждая статья связана с названием, годом публикации, авторами и ключевыми словами. Во время публикации каждый автор помечается кодом, именем и принадлежностью. Каждое ключевое слово может быть связано с набором синонимов (например, ключевое слово "метод опорных векторов" может быть связано с синонимом svm).

<a name="defining-schema"></a>
## <a name="define-the-schema"></a>Определение схемы
Схема описывает структуру атрибутов объектов в домене. Она задает имя и тип данных каждому атрибуту в формате файла JSON. Ниже приведен пример содержимого файла *Academic.schema*.

```json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

Здесь вы определяете тип данных как строка, целое число и строковой атрибут для *названия*, *года* и *ключевого слова* соответственно. Так как авторам присвоены код, имя и принадлежность, то вы определяете *автора* как составной атрибут с тремя вложенными атрибутами: *Author.Id*, *Author.Name* и *Author.Affiliation*.

По умолчанию атрибуты поддерживают все операции, доступные для соответствующего типа данных, включая *equals*, *starts_with* и *is_between*. Так как код автора используется только внутри в качестве идентификатора, переопределите значение по умолчанию и укажите *equals* как единственную индексированную операцию.

Для атрибута *ключевое слово*, разрешите синонимам соответствовать каноническим значениям ключевого слова, указав файл синонимов *Keyword.syn* в определении атрибута. Этот файл содержит список пар канонических и синонимических значений:

```json
...
["support vector machine","support vector machines"]
["support vector machine","support vector networks"]
["support vector machine","support vector regression"]
["support vector machine","support vector"]
["support vector machine","svm machine learning"]
["support vector machine","svm"]
["support vector machine","svms"]
["support vector machine","vector machine"]
...
```

Дополнительные сведения об определении схемы см. в [этой статье](SchemaFormat.md).

<a name="generating-data"></a>
## <a name="generate-data"></a>Создание данных
В файле данных приводится список публикаций для индексации, где для каждой строки указаны значения атрибутов статьи в [формате JSON](http://json.org/).  Следующий пример представляет собой одну строку из файла данных *Academic.data*, отформатированного для удобства чтения:

```
...
{
    "logprob": -16.714,
    "Title": "the world wide telescope",
    "Year": 2001,
    "Author": [
        {
            "Id": 717694024,
            "Name": "alexander s szalay",
            "Affiliation": "microsoft"
        },
        {
            "Id": 2131537204,
            "Name": "jim gray",
            "Affiliation": "microsoft"
        }
    ]
}
...
```

В этом фрагменте кода указываются такие атрибуты статьи, как *название* и *год* с типом строка JSON и число соответственно. Несколько значений представлены с помощью массивов JSON. Так как *автор* является составным атрибутом, то каждое значение представляется с помощью объекта JSON, состоящим из вложенных атрибутов. Атрибуты с отсутствующими значениями, такие как *ключевое слово*, в этом случае, можно исключить из JSON-представления.

Для различия подобности разных статей, укажите относительную лог-вероятность, используя встроенный атрибут *logprob*. Задав вероятность *p* от 0 до 1, вычисляется лог-вероятность как log(*p*), где log() — функция натурального логарифма.

Для получения дополнительных сведений см. статью [Формат данных](DataFormat.md).

<a name="building-index"></a>
## <a name="build-a-compressed-binary-index"></a>Создание сжатого двоичного индекса
После того как у вас будет файл схемы и файл данных, можно создавать сжатые двоичные индексы объектов данных с помощью [`kes.exe build_index`](CommandLine.md#build_index-command). В этом примере создается файл индекса *Academic.index* из входных данных файла схемы *Academic.schema* и файла данных *Academic.data*. Используйте следующую команду:

`kes.exe build_index Academic.schema Academic.data Academic.index`

Для быстрого создания прототипов за пределами Azure [`kes.exe build_index`](CommandLine.md#build_index-command) может локально создавать малые индексы из файлов данных, содержащих до 10 000 объектов. Для обработки больших файлов данных  можно либо выполнить команду внутри [виртуальной Машины Windows в Azure](../../../articles/virtual-machines/windows/quick-create-portal.md), либо выполнить удаленную сборку в Azure. Дополнительные сведения см. в разделе [Увеличение масштаба](#scaling-up).

<a name="authoring-grammar"></a>
## <a name="use-an-xml-grammar-specification"></a>Использование спецификации грамматики XML
Грамматика задает набор запросов на естественном языке, которые служба может интерпретировать, заодно и как запросы на естественном языке, преобразованные в выражения семантических запросов. В этом примере используется грамматика, заданная в файле *academic.xml*:

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="Academic.schema" name="academic"/>

  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>

    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>

    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>

      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>

        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>

        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>

  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="lt" name="year"/>
          </item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="gt" name="year"/>
          </item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```

Для получения дополнительных сведений о синтаксисе спецификации грамматики см. раздел [Формат файла grammar](GrammarFormat.md).

<a name="compiling-grammar"></a>
## <a name="compile-the-grammar"></a>Компиляция грамматики
После того как у вас будет спецификация грамматики XML, можно скомпилировать ее в двоичную грамматику с помощью [`kes.exe build_grammar`](CommandLine.md#build_grammar-command). Обратите внимание, что если грамматика импортирует схему, файл схемы должен находиться в том же расположении, что и грамматика XML. В этом примере из входного XML-файла грамматики *Academic.xml* создается двоичный файла грамматики *Academic.grammar*. Используйте следующую команду:

`kes.exe build_grammar Academic.xml Academic.grammar`

<a name="hosting-index"></a>
## <a name="host-the-grammar-and-index-in-a-web-service"></a>Размещение грамматики и индекса в веб-службе
Для быстрого создания прототипов можно разместить грамматику и индекс в веб-службе на локальном компьютере с помощью [`kes.exe host_service`](CommandLine.md#host_service-command). Теперь можно получить доступ к службе через [веб-API](WebAPI.md) для проверки построения грамматики и правильности данных. В этом примере в http://localhost:8000/ размещается файл грамматики *Academic.grammar* и файл индекса *Academic.index*. Используйте следующую команду:

`kes.exe host_service Academic.grammar Academic.index --port 8000`

При этом инициируется локальный экземпляр веб-службы. Можно интерактивно проверить службу, перейдя в браузере на `http::localhost:<port>`. Для получения дополнительных сведений см. раздел [Служба тестирования](#testing-service).

Можно также непосредственно вызывать различные [веб-API](WebAPI.md) для тестирования интерпретации естественного языка, завершения запроса, оценки структурированных запросов и вычисления гистограммы. Чтобы остановить службу, введите quit в командной строке `kes.exe host_service` или нажмите клавиши CTRL + C. Ниже приведены некоторые примеры:

* [http://localhost:8000/interpret?query=papers by susan t dumais](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20dumais)
* [http://localhost:8000/interpret?query=papers by susan t d&complete=1](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20d&complete=1)
* [http://localhost:8000/evaluate?expr=Composite(Author.Name=='susan t dumais')&attributes=Title,Year,Author.Name,Author.Id&count=2](http://localhost:8000/evaluate?expr=Composite%28Author.Name==%27susan%20t%20dumais%27%29&attributes=Title,Year,Author.Name,Author.Id&count=2)
* [http://localhost:8000/calchistogram?expr=And(Composite(Author.Name=='susan t dumais'),Year>=2013)&attributes=Year,Keyword&count=4](http://localhost:8000/calchistogram?expr=And%28Composite%28Author.Name=='susan%20t%20dumais'%29,Year>=2013%29&attributes=Year,Keyword&count=4)

За пределами Azure [`kes.exe host_service`](CommandLine.md#host_service-command) ограничена по индексам до 10 000 объектов. Другие ограничения включают скорость API в 10 запросов в секунду и общее количество в 1000 запросов перед автоматическим завершением процесса. Чтобы обойти эти ограничения, выполните команду изнутри [виртуальной машины Windows в Azure](../../../articles/virtual-machines/windows/quick-create-portal.md) или разверните облачную службу Azure с помощью команды [`kes.exe deploy_service`](CommandLine.md#deploy_service-command). Дополнительные сведения см. в разделе [Развертывание службы](#deploying-service).

<a name="scaling-up"></a>
## <a name="scale-up-to-host-larger-indices"></a>Масштабирование для размещения больших индексов
При выполнении `kes.exe` за пределами Azure индекс ограничивается до 10 000 объектов. Можно создать и разместить большие индексы с помощью Azure. Зарегистрируйтесь на [бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/). Кроме того, если вы являетесь подписчиком Visual Studio или MSDN, вы можете [активировать преимущества для подписчиков](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Такие предложения предлагают деньги на счете в Azure каждый месяц.

Чтобы разрешить `kes.exe` доступ к учетной записи Azure, [загрузите файл параметров публикации Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) с портала Azure. Если будет предложено, войдите с требуемой учетной записи Azure. Сохраните файл как *AzurePublishSettings.xml* в рабочей папке, в которой запускаете `kes.exe`.

Существует два способа создания и размещения больших индексов. Первый — подготовить схему и файл данных на виртуальной машине Windows в Azure. Затем запустите [`kes.exe build_index`](#building-index) для локального построения индекса на виртуальной машине, без каких-либо ограничений размера. Результирующий индекс может размещаться локально на виртуальной машине, используя [`kes.exe host_service`](#hosting-service) для быстрого создания прототипов, опять же без каких-либо ограничений. Подробные инструкции см. в статье [Краткое руководство. Создание виртуальной машины под управлением Windows на портале Azure](../../../articles/virtual-machines/windows/quick-create-portal.md).

Второй метод предусматривает выполнение удаленной сборки Azure с помощью [`kes.exe build_index`](CommandLine.md#build_index-command) с параметром `--remote`. Указывается размер виртуальной машины Azure. Если указан параметр `--remote`, команда создает временную виртуальную машину Azure указанного размера. Затем строит индекс на виртуальной машине, передает индекс в целевое хранилище BLOB-объектов и удаляет виртуальную машину после завершения. Подписка Azure оплачивается по стоимости виртуальной машины во время создания индекса.

Эта возможность удаленной сборки Azure позволяет выполнять [`kes.exe build_index`](CommandLine.md#build_index-command) в любой среде. При выполнении удаленной сборки входная схема и аргументы данных могут быть размещены по пути файла или по URL-адресам [хранилища BLOB-объектов Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Выходной аргумент индекса должен быть URL-адресом хранилища BLOB-объектов Azure. Чтобы создать учетную запись хранения Azure, см. инструкции в статье [Об учетных записях хранения Azure](../../storage/common/storage-create-storage-account.md). Для эффективного копирования файлов в и из хранилища BLOB-объектов, используйте программу [AzCopy](../../storage/common/storage-use-azcopy.md).

В этом примере можно предположить, что следующий контейнер хранилища BLOB-объектов уже был создан по адресу: http://&lt;*account*&gt;.blob.core.windows.net/&lt;*container*&gt;/. Он содержит схему *Academic.schema*, заданный ссылкой файл синонимов *Keywords.syn* и полномасштабный файл данных *Academic.full.data*. Можно удаленно создать полный индекс с помощью следующей команды:

`kes.exe build_index http://<account>.blob.core.windows.net/<container>/Academic.schema http://<account>.blob.core.windows.net/<container>/Academic.full.data http://<account>.blob.core.windows.net/<container>/Academic.full.index --remote <vm_size>`

Обратите внимание, что может потребоваться 5–10 минут для построения индекса на временной виртуальной машине. Для быстрого создания прототипов можно сделать следующее:
- Локальная разработка с меньшим набором данных на любом компьютере.
- Вручную [создайте виртуальную машину Azure](../../../articles/virtual-machines/windows/quick-create-portal.md), [подключитесь к ней](../../../articles/virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) через удаленный рабочий стол, установите [пакет SDK Службы поиска и распознавания данных](https://www.microsoft.com/en-us/download/details.aspx?id=51488) и запустите [`kes.exe`](CommandLine.md) с виртуальной машины.

Разбиение на страницы замедляет процесс сборки. Чтобы избежать разбиения на страницы, используйте виртуальную машину, которая имеет в три раза больший объем ОЗУ, чем размер файла входных данных для построения индекса. Используйте виртуальную машину с 1 ГБ дополнительной оперативной памяти больше, чем размер индекса для размещения. Список доступных размеров виртуальных машин см. в разделе [Размеры виртуальных машин Windows в Azure](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

<a name="deploying-service"></a>
## <a name="deploy-the-service"></a>Развертывание службы
После того как у вас будет грамматика и индекс, вы готовы к развертыванию службы в облачной службе Azure. Чтобы создать новую облачную служб Azure, см. статью [Создание и развертывание облачной службы](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md). На этом этапе не следует указывать пакет развертывания.  

При создании облачной службы можно использовать [`kes.exe deploy_service`](CommandLine.md#deploy_service-command) для развертывания службы. В облачной службе Azure есть два слота развертывания: рабочий и промежуточный. Службу, которая принимает текущий пользовательский трафик, следует изначально развернуть на промежуточном слоте. Дождитесь, пока служба запустится и инициализирует саму себя. Затем вы можете отправлять несколько запросов для проверки развертывания и убедиться, что оно прошло основные проверки.

[Переключите](../../../articles/cloud-services/cloud-services-nodejs-stage-application.md) содержимое промежуточного слота на рабочий слот, чтобы текущий трафик теперь направлялся к новой развернутой службе. Можно повторить этот процесс при развертывании обновленной версии службы с новыми данными. Как и с другими облачными службами Azure, при необходимости можно использовать портал Azure для настройки [автомасштабирования](../../../articles/cloud-services/cloud-services-how-to-scale-portal.md).

В этом примере развертывается индекс *Academic* для промежуточного слота существующей облачной службы с виртуальными машинами размера *< vm_size >*. Используйте следующую команду:

`kes.exe deploy_service http://<account>.blob.core.windows.net/<container>/Academic.grammar http://<account>.blob.core.windows.net/<container>/Academic.index <serviceName> <vm_size> --slot Staging`

Список доступных размеров виртуальных машин см. в разделе [Размеры виртуальных машин Windows в Azure](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

Развернув службу, можно вызывать различные [веб-API](WebAPI.md) для тестирования интерпретации естественного языка, завершения запроса, оценки структурированных запросов и вычисления гистограммы.  

<a name="testing-service"></a>
## <a name="test-the-service"></a>Тестирование службы
Перейдите на хост-компьютер из веб-браузера для отладки службы в режиме реального времени. Для развертывания локальной службы через [host_service](#hosting-service) посетите `http://localhost:<port>/`.  Для развертывания облачной службы Azure через [host_service](#deploying-service) посетите `http://<serviceName>.cloudapp.net/`.

Эта страница содержит ссылку на сведения о базовой статистике вызова API, а также грамматики и индекса, размещенных в этой службе. Эта страница также содержит интерфейс интерактивного поиска, который демонстрирует использование веб-API. Введите запросы в поле поиска, чтобы просмотреть результаты [интерпретации](interpretMethod.md), [оценки](evaluateMethod.md) и [вычисления гистограммы](calchistogramMethod.md) вызовов API. Источник базового HTML для этой страницы также служит в качестве примера того, как интегрировать веб-API в приложение для создания интерактивного поиска с широкими возможностями.


