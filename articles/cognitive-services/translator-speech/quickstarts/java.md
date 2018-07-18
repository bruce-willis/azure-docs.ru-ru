---
title: Краткое руководство по Java для Azure Cognitive Services и API перевода речи Microsoft Translator | Документация Microsoft
description: Получите информацию и примеры кода, которые помогут вам приступить к работе с API перевода речи Microsoft Translator, входящим в состав Microsoft Cognitive Services в Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: 447164bd8d786fbfc46dff878b77f11a286bcfb8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380557"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-java"></a>Краткое руководство по использованию API перевода речи Microsoft Translator с Java 
<a name="HOLTop"></a>

В этой статье показано использование API перевода речи Microsoft Translator для перевода произнесенных слов в WAV-файле.

## <a name="prerequisites"></a>предварительным требованиям

Для компиляции и запуска этого кода вам потребуется [пакет JDK 7 или 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). При желании можно воспользоваться интегрированной средой разработки Java, но и обычного текстового редактора будет достаточно.

Вам потребуются файлы [javax.websocket-api-1.1.jar](http://central.maven.org/maven2/javax/websocket/javax.websocket-api/1.1/) и [tyrus-standalone-client-1.9.jar](http://repo1.maven.org/maven2/org/glassfish/tyrus/bundles/tyrus-standalone-client/1.9/).

Вам потребуется WAV-файл с именем speak.wav, расположенный в той же папке, что и исполняемый файл, который вы скомпилируете из приведенного ниже кода. Этот WAV-файл должен быть в стандартном моноформате PCM: 16 бит, 16 кГц. Такой WAV-файл можно получить из [API перевода текстов](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Speak).

Необходимо иметь [учетную запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с **API перевода речи Microsoft Translator**. Вам понадобится платный ключ подписки, указанный на [панели мониторинга Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Перевод речи

Следующий код переводит речь с одного языка на другой.

1. Создайте проект Java в любой интегрированной среде разработки.
2. Добавьте указанный ниже код.
3. Замените значение `key` действительным ключом доступа для своей подписки.
4. Запустите программу.

Config.java:

```java
import java.util.*;

import javax.websocket.ClientEndpointConfig;

public class Config extends ClientEndpointConfig.Configurator {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace the subscriptionKey string value with your valid subscription key.
    static String key = "ENTER KEY HERE";

    @Override
    public void beforeRequest (Map<String,List<String>> headers) {
        headers.put("Ocp-Apim-Subscription-Key", Arrays.asList (key));
    }
}
```

Client.java:

```java
import java.io.*;
import java.net.*;
import java.util.*;

import javax.websocket.ClientEndpoint;
import javax.websocket.CloseReason;
import javax.websocket.ContainerProvider;
import javax.websocket.OnClose;
import javax.websocket.OnError;
import javax.websocket.OnMessage;
import javax.websocket.OnOpen;
import javax.websocket.Session;
import javax.websocket.WebSocketContainer;

/* Useful reference links:
    https://docs.oracle.com/javaee/7/api/javax/websocket/Session.html
    https://docs.oracle.com/javaee/7/api/javax/websocket/RemoteEndpoint.Basic.html
    https://docs.oracle.com/javaee/7/api/javax/websocket/OnMessage.html
    http://www.oracle.com/technetwork/articles/java/jsr356-1937161.html
*/

@ClientEndpoint(configurator = Config.class)
public class Client {
    static String host = "wss://dev.microsofttranslator.com";
    static String path = "/speech/translate";
    static String params = "?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa";
    static String uri = host + path + params;

    static String input_path = "speak.wav";
    static String output_path = "speak2.wav";

    Session session = null;

    @OnOpen
    public void onOpen(Session session) {
        this.session = session;
        System.out.println ("Connected.");
        SendAudio ();
    }

    @OnMessage
    public void onTextMessage(String message, Session session){
        System.out.println ("Text message received.");
        System.out.println (message);
    }

/*
Use the following signature to receive the message in parts:
    public void onBinaryMessage(byte[] buffer, boolean last, Session session)
Use the following signature to receive the entire message:
    public void onBinaryMessage(byte[] buffer, Session session)
See:
    https://docs.oracle.com/javaee/7/api/javax/websocket/OnMessage.html
*/
    @OnMessage
    public void onBinaryMessage(byte[] buffer, Session session) {
        try {
            System.out.println ("Binary message received.");
            FileOutputStream stream = new FileOutputStream(output_path);
            stream.write(buffer);
            stream.close();
            System.out.println ("Message contents written to file.");
            Close ();
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }

    @OnError
    public void onError(Session session, Throwable e) {
        System.out.println ("Error message received: " + e.getMessage ());
    }

    @OnClose
    public void myOnClose (CloseReason reason) {
        System.out.println ("Close message received: " + reason.getReasonPhrase());
    }

    public void SendAudio() {
        try {
            File file = new File(input_path);
            FileInputStream stream = new FileInputStream (file);
            byte buffer[] = new byte[(int)file.length()];
            stream.read(buffer);
            stream.close();
            sendMessage (buffer);
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }

    public void sendMessage(byte[] message) {
        try {
            System.out.println ("Sending audio.");
            OutputStream stream = this.session.getBasicRemote().getSendStream();
            stream.write (message);
/* Make sure the audio file is followed by silence.
 * This lets the service know that the audio input is finished. */
            System.out.println ("Sending silence.");
            byte silence[] = new byte[3200000];
            stream.write (silence);
            stream.flush();
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }

    public void Connect () throws Exception {
        try {
            System.out.println("Connecting.");
            WebSocketContainer container = ContainerProvider.getWebSocketContainer();
/* Some code samples show container.connectToServer as returning a Session, but this seems to be false. */
            container.connectToServer(this, new URI(uri));
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }

    public void Close () throws Exception {
        try {
            System.out.println("Closing connection.");
            this.session.close ();
        } catch (Exception e) {
            e.printStackTrace ();
        }
    }
}
```

Speak.java:

```java
/*
Download javax.websocket-api-1.1.jar from:
    http://central.maven.org/maven2/javax/websocket/javax.websocket-api/1.1/
Download tyrus-standalone-client-1.9.jar from:
    http://repo1.maven.org/maven2/org/glassfish/tyrus/bundles/tyrus-standalone-client/1.9/

Compile and run with:
    javac Config.java -cp .;javax.websocket-api-1.1.jar
    javac Client.java -cp .;javax.websocket-api-1.1.jar;tyrus-standalone-client-1.9.jar
    javac Speak.java
    java -cp .;javax.websocket-api-1.1.jar;tyrus-standalone-client-1.9.jar Speak
*/

public class Speak {
    public static void main(String[] args) {
        try {
            Client client = new Client ();
            client.Connect ();
            // Wait for the reply.
            System.out.println ("Press any key to exit the application at any time.");
            System.in.read();
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

**Перевод речевого ответа**

Успешным результатом будет создание файла с именем speak2.wav. Этот файл содержит перевод слов, произнесенных в файле speak.wav.

[Вверх](#HOLTop)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по API перевода речи](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>См. также 

[Общие сведения об API перевода речи](../overview.md)
[Справочник по API](http://docs.microsofttranslator.com/speech-translate.html)
