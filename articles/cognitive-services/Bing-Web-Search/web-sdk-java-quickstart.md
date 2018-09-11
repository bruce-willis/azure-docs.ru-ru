---
title: Краткое руководство. Использование пакета SDK Bing для поиска в Интернете для Java
description: Узнайте, как использовать пакет SDK Bing для поиска в Интернете для Java.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 08/22/2018
ms.author: erhopf
ms.openlocfilehash: cc7335b9f8b5596edef895ff5a42a1018b06a381
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43186733"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-java"></a>Краткое руководство. Использование пакета SDK Bing для поиска в Интернете для Java

Пакет SDK Bing для поиска в Интернете позволяет интегрировать API Bing для поиска в Интернете в Java-приложение. В этом кратком руководстве описано, как отправлять запрос, получать ответ в формате JSON, фильтровать и анализировать результаты.

Хотите увидеть код прямо сейчас? [Примеры для пакета SDK Bing для поиска в Интернете для Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/) доступны в GitHub.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам потребуется следующее:

* [пакет JDK 7 или 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html);
* [Apache Maven](https://maven.apache.org/download.cgi) или другой инструмент автоматизации сборки;
* ключ подписки;

## <a name="create-a-project-and-configure-your-pom-file"></a>Создание проекта и настройка файла POM

Создайте проект Java с помощью Maven или другого инструмента автоматизации сборки. Если вы используете Maven, добавьте следующие строки в файл POM. Замените все экземпляры `mainClass` своим приложением.

```xml
<build>
    <plugins>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.4.0</version>
        <configuration>
          <!--Your comment
            Replace the mainClass with the path to your java application.
            It should begin with com and doesn't require the .java extension.
            For example: com.bingwebsearch.app.BingWebSearchSample. This maps to
            The following directory structure:
            src/main/java/com/bingwebsearch/app/BingWebSearchSample.java.
          -->
          <mainClass>com.path.to.your.app.APP_NAME</mainClass>
        </configuration>
      </plugin>
      <plugin>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.0</version>
        <configuration>
          <source>1.7</source>
          <target>1.7</target>
        </configuration>
      </plugin>
      <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>attached</goal>
            </goals>
            <configuration>
              <descriptorRefs>
                <descriptorRef>jar-with-dependencies</descriptorRef>
              </descriptorRefs>
              <archive>
                <manifest>
                  <!--Your comment
                    Replace the mainClass with the path to your java application.
                    For example: com.bingwebsearch.app.BingWebSearchSample.java.
                    This maps to the following directory structure:
                    src/main/java/com/bingwebsearch/app/BingWebSearchSample.java.
                  -->
                  <mainClass>com.path.to.your.app.APP_NAME.java</mainClass>
                </manifest>
              </archive>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.9.0</version>
    </dependency>
    <dependency>
      <groupId>commons-net</groupId>
      <artifactId>commons-net</artifactId>
      <version>3.3</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-websearch</artifactId>
      <version>1.0.1</version>
    </dependency>
  </dependencies>
```

## <a name="declare-dependencies"></a>Объявление зависимостей

Откройте проект в используемой вами интегрированной среде разработки или редакторе и импортируйте эти зависимости:

```java
import com.microsoft.azure.cognitiveservices.search.websearch.BingWebSearchAPI;
import com.microsoft.azure.cognitiveservices.search.websearch.BingWebSearchManager;
import com.microsoft.azure.cognitiveservices.search.websearch.models.ImageObject;
import com.microsoft.azure.cognitiveservices.search.websearch.models.NewsArticle;
import com.microsoft.azure.cognitiveservices.search.websearch.models.SearchResponse;
import com.microsoft.azure.cognitiveservices.search.websearch.models.VideoObject;
import com.microsoft.azure.cognitiveservices.search.websearch.models.WebPage;
```

Если вы создали проект с помощью Maven, пакет будет уже объявлен. В противном случае объявите пакет. Например: 

```java
package com.bingwebsearch.app
```

## <a name="declare-the-bingwebsearchsample-class"></a>Объявление класса BingWebSearchSample

Объявите класс `BingWebSearchSample`. В нем будет содержаться большая часть кода, включая метод `main`.  

```java
public class BingWebSearchSample {

// The code in the following sections goes here.

}
```

## <a name="construct-a-request"></a>Создание запроса

Метод `runSample` находится в классе `BingWebSearchSample` и создает запрос. Скопируйте этот код в свое приложение:

```java
public static boolean runSample(BingWebSearchAPI client) {
    /*
     * This function performs the search.
     *
     * @param client instance of the Bing Web Search API client
     * @return true if sample runs successfully
     */
    try {
        /*
         * Performs a search based on the .withQuery and prints the name and
         * url for the first web pages, image, news, and video result
         * included in the response.
         */
        System.out.println("Searched Web for \"Xbox\"");
        // Construct the request.
        SearchResponse webData = client.bingWebs().search()
            .withQuery("Xbox")
            .withMarket("en-us")
            .withCount(10)
            .execute();

// Code continues in the next section...
```

## <a name="handle-the-response"></a>Обработка ответа

Теперь добавим код, чтобы проанализировать ответ и вывести результаты. Выводятся `name` и `url` для первой веб-страницы, образа, новостной статьи и видео, если они включены в объект ответа.

```java
/*
* WebPages
* If the search response contains web pages, the first result's name
* and url are printed.
*/
if (webData != null && webData.webPages() != null && webData.webPages().value() != null &&
        webData.webPages().value().size() > 0) {
    // find the first web page
    WebPage firstWebPagesResult = webData.webPages().value().get(0);

    if (firstWebPagesResult != null) {
        System.out.println(String.format("Webpage Results#%d", webData.webPages().value().size()));
        System.out.println(String.format("First web page name: %s ", firstWebPagesResult.name()));
        System.out.println(String.format("First web page URL: %s ", firstWebPagesResult.url()));
    } else {
        System.out.println("Couldn't find the first web result!");
    }
} else {
    System.out.println("Didn't find any web pages...");
}
/*
 * Images
 * If the search response contains images, the first result's name
 * and url are printed.
 */
if (webData != null && webData.images() != null && webData.images().value() != null &&
        webData.images().value().size() > 0) {
    // find the first image result
    ImageObject firstImageResult = webData.images().value().get(0);

    if (firstImageResult != null) {
        System.out.println(String.format("Image Results#%d", webData.images().value().size()));
        System.out.println(String.format("First Image result name: %s ", firstImageResult.name()));
        System.out.println(String.format("First Image result URL: %s ", firstImageResult.contentUrl()));
    } else {
        System.out.println("Couldn't find the first image result!");
    }
} else {
    System.out.println("Didn't find any images...");
}
/*
 * News
 * If the search response contains news articles, the first result's name
 * and url are printed.
 */
if (webData != null && webData.news() != null && webData.news().value() != null &&
        webData.news().value().size() > 0) {
    // find the first news result
    NewsArticle firstNewsResult = webData.news().value().get(0);
    if (firstNewsResult != null) {
        System.out.println(String.format("News Results#%d", webData.news().value().size()));
        System.out.println(String.format("First news result name: %s ", firstNewsResult.name()));
        System.out.println(String.format("First news result URL: %s ", firstNewsResult.url()));
    } else {
        System.out.println("Couldn't find the first news result!");
    }
} else {
    System.out.println("Didn't find any news articles...");
}

/*
 * Videos
 * If the search response contains videos, the first result's name
 * and url are printed.
 */
if (webData != null && webData.videos() != null && webData.videos().value() != null &&
        webData.videos().value().size() > 0) {
    // find the first video result
    VideoObject firstVideoResult = webData.videos().value().get(0);

    if (firstVideoResult != null) {
        System.out.println(String.format("Video Results#%s", webData.videos().value().size()));
        System.out.println(String.format("First Video result name: %s ", firstVideoResult.name()));
        System.out.println(String.format("First Video result URL: %s ", firstVideoResult.contentUrl()));
    } else {
        System.out.println("Couldn't find the first video result!");
    }
} else {
    System.out.println("Didn't find any videos...");
}
```

## <a name="declare-the-main-method"></a>Объявление метода main

В этом приложении метод main включает код, который создает экземпляр клиента, проверяет `subscriptionKey` и вызывает `runSample`. Не забудьте ввести действующий ключ подписки Azure, прежде чем продолжить работу.

```java
public static void main(String[] args) {
    try {
        // Enter a valid subscription key for your account.
        final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
        // Instantiate the client.
        BingWebSearchAPI client = BingWebSearchManager.authenticate(subscriptionKey);
        // Make a call to the Bing Web Search API.
        runSample(client);
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }
}
```

## <a name="run-the-program"></a>Запуск программы

А теперь запустите программу!

```console
mvn compile exec:java
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ключ подписки из кода программы после завершения проекта.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры пакета SDK для Java в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch)

## <a name="see-also"></a>См. также

* [Ссылка на пакет SDK для Java в Azure](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/websearch)
