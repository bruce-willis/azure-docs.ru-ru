---
title: Получение списка поддерживаемых языков с помощью API перевода текстов и Java | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Из этого краткого руководства вы узнаете, как получить список поддерживаемых языков перевода и транслитерации, результаты поиска по словарю и примеры с помощью API перевода текстов и Java в Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: af8b50d6600354601c8516c20a079028cefe2b16
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2018
ms.locfileid: "43770701"
---
# <a name="quickstart-get-supported-languages-with-java"></a>Краткое руководство. Получение списка поддерживаемых языков с помощью Java

Из этого краткого руководства вы узнаете, как получить список поддерживаемых языков перевода и транслитерации, результаты поиска по словарю и примеры с помощью API перевода текстов.

## <a name="prerequisites"></a>Предварительные требования

Для компиляции и запуска этого кода вам потребуется [пакет JDK 7 или 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). При желании можно воспользоваться интегрированной средой разработки Java, но обычный текстовый редактор также подойдет.

Чтобы использовать API перевода текстов, вам также потребуется ключ подписки. Сведения об этом см. в статье [Регистрация для использования API перевода текстов](translator-text-how-to-signup.md).

## <a name="languages-request"></a>Запрос метода Languages

Приведенный ниже код возвращает список поддерживаемых языков перевода и транслитерации, а также результаты поиска по словарю и примеры использования с помощью метода [Languages](./reference/v3-0-languages.md).

1. Создайте проект Java в любом редакторе кода.
2. Добавьте указанный ниже код.
3. Замените значение `subscriptionKey` ключом доступа, допустимым для подписки.
4. Запустите программу.

```java
import java.io.*;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/* NOTE: To compile and run this code:
1. Save this file as Languages.java.
2. Run:
    javac Languages.java -cp .;gson-2.8.1.jar -encoding UTF-8
3. Run:
    java -cp .;gson-2.8.1.jar Languages
*/

public class Languages {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

    static String host = "https://api.cognitive.microsofttranslator.com";
    static String path = "/languages?api-version=3.0";

    static String output_path = "output.txt";

    public static String GetLanguages () throws Exception {
        URL url = new URL (host + path);

        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("GET");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setDoOutput(true);

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream(), "UTF-8"));

        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void WriteToFile (String data) throws Exception {
        String json = prettify (data);
        Writer outputStream = new BufferedWriter(new OutputStreamWriter(new FileOutputStream(output_path), "UTF-8"));
        outputStream.write(json);
        outputStream.close();
    }

    public static void main(String[] args) {
        try {
            String response = GetLanguages ();
            WriteToFile (response);
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

## <a name="languages-response"></a>Ответ метода Languages

В случае успешного выполнения ответ возвращается в формате JSON, как показано в примере ниже:

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  },
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с примерами кода из этого и других кратких руководств, включая перевод и транслитерацию, а также с другими примерами проектов перевода текстов в GitHub.

> [!div class="nextstepaction"]
> [Примеры для Java на сайте GitHub](https://aka.ms/TranslatorGitHub?type=&language=java)
