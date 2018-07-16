---
title: Краткое руководство по API поиска ответов в проектах с использованием Java. Microsoft Cognitive Services | Документация Майкрософт
description: Начало использования API поиска ответов в проектах на языке Java.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 4e1f606e1564981589e638e0e51a8b42633ca7b0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381717"
---
# <a name="project-answer-search-query-in-java"></a>Запрос к API поиска ответов в проектах на Java
В этой статье с помощью Java демонстрируется API поиска ответов Bing, входящий в состав служб Microsoft Cognitive Services в Azure. API представляет собой веб-службу REST, совместимую с любым языком программирования, который может выполнять HTTP-запросы и анализировать JSON.
 
В примере кода используется Java с минимальными внешними зависимостями.  Его также можно выполнить в Linux или Mac OS X с помощью Mono.

## <a name="prerequisites"></a>предварительным требованиям

Получите ключ доступа для бесплатной пробной версии [Cognitive Services Labs](https://aka.ms/answersearchsubscription).

## <a name="request"></a>Запрос 

Следующий код создает `WebRequest`, задает заголовок ключа доступа и добавляет строку запроса "Gibraltar" (Гибралтар).  Затем код отправляет запрос и назначает ответ строке, содержащей текст JSON.

````
    static String host = "https://api.labs.cognitive.microsoft.com";
    static String path = "/answerSearch/v7.0/search";

    // construct URL of search request (endpoint + query string)

    URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + &mkt=en-us");
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();

    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
````

## <a name="complete-code"></a>Полный код

API поиска ответов Bing возвращает результаты из поисковой системы Bing.
1. Скачайте или установите библиотеку gson.
2. Создайте проект Java в используемой вами интегрированной среде разработки или редакторе.
3. Добавьте указанный ниже код.
4. Замените значение subscriptionKey ключом доступа, допустимым для вашей подписки.
5. Запустите программу.

````
package knowledgeAPI;
import java.io.InputStream;
import java.net.*;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Scanner;

import javax.net.ssl.HttpsURLConnection;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

public class KnowledgeSrch {

        // Replace the subscriptionKey string value with your valid subscription key.
        static String subscriptionKey = "YOUR-ACCESS-KEY";

        static String host = "https://api.labs.cognitive.microsoft.com";
        static String path = "/answerSearch/v7.0/search";

        static String searchTerm = "Gibraltar";

        public static SearchResults SearchKnowledge (String searchQuery) throws Exception {

            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&mkt=en-us");
            
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

            // receive JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();

            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);

            // extract Bing-related HTTP headers
            Map<String, List<String>> headers = connection.getHeaderFields();
            for (String header : headers.keySet()) {
                if (header == null) continue;      // may have null key
                if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
                    results.relevantHeaders.put(header, headers.get(header).get(0));
                }
            }

            stream.close();
            return results;
        }

        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }

        public static void main (String[] args) {
            try {
                System.out.println("Searching the Web for: " + searchTerm);

                SearchResults result = SearchKnowledge(searchTerm);

                System.out.println("\nRelevant HTTP Headers:\n");
                for (String header : result.relevantHeaders.keySet())
                    System.out.println(header + ": " + result.relevantHeaders.get(header));

                System.out.println("\nJSON Response:\n");
                System.out.println(prettify(result.jsonResponse));
            }
            catch (Exception e) {
                e.printStackTrace(System.out);
                System.exit(1);
            }
        }
    }

    // Container class for search results encapsulates relevant headers and JSON data
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
}

````

## <a name="next-steps"></a>Дополнительная информация
- [Краткое руководство — C#](c-sharp-quickstart.md)
- [Краткое руководство — Java](java-quickstart.md)
- [Краткое руководство — Node](node-quickstart.md)