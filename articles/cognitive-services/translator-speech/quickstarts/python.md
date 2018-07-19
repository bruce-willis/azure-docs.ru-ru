---
title: Краткое руководство по Python для Azure Cognitive Services и API перевода речи Microsoft | Документация Майкрософт
description: Сведения и примеры кода для быстрого начала использования API перевода речи Microsoft в Microsoft Cognitive Services в Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 07/17/2018
ms.author: v-jaswel
ms.openlocfilehash: 40535f400bc5359019e89320489721bf481c1210
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39124833"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-python"></a>Краткое руководство по API перевода речи Microsoft с помощью Python 
<a name="HOLTop"></a>

В этой статье показано использование API перевода речи Microsoft для перевода слов, произнесенных в файле WAV.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого кода потребуется [Python 3.x](https://www.python.org/downloads/).

Также потребуется установить [пакет websocket-client](https://pypi.python.org/pypi/websocket-client) для Python.

Вам необходимо иметь файл WAV, который называется "speak.wav" в той же папке, что и исполняемый файл, который будете компилировать из приведенного ниже кода. Этот файл WAV должен быть в стандартном формате PCM: 16 бит, 16 кГц, моно.

Необходимо иметь [учетную запись Cognitive Services API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с **Microsoft Translator Speech API**. Вам понадобится платный ключ подписки, доступный из [панели мониторинга Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Преобразование текста

Следующий код преобразовывает текст с одного языка на другой.

1. Создайте новый проект Python в избранном интерфейсе IDE.
2. Добавьте указанный ниже код.
3. Замените значение `key` ключом доступа, допустимым для подписки.
4. Запустите программу.

```python
# -*- coding: utf-8 -*-

# To install this package, run:
#   pip install websocket-client
import websocket

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'wss://dev.microsofttranslator.com'
path = '/speech/translate';
params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa'
uri = host + path + params

input_file = 'speak.wav'
output_file = 'speak2.wav'

output = bytearray ()

def on_open (client):
    print ("Connected.")

# r = read. b = binary.
    with open (input_file, mode='rb') as file:
        data = file.read()

    print ("Sending audio.")
    client.send (data, websocket.ABNF.OPCODE_BINARY)
# Make sure the audio file is followed by silence.
# This lets the service know that the audio input is finished.
    print ("Sending silence.")
    client.send (bytearray (32000), websocket.ABNF.OPCODE_BINARY)

def on_data (client, message, message_type, is_last):
    global output
    if (websocket.ABNF.OPCODE_TEXT == message_type):
        print ("Received text data.")
        print (message)
# For some reason, we receive the data as type websocket.ABNF.OPCODE_CONT.
    elif (websocket.ABNF.OPCODE_BINARY == message_type or websocket.ABNF.OPCODE_CONT == message_type):
        print ("Received binary data.")
        print ("Is last? " + str(is_last))
        output = output + message
        if (True == is_last):
# w = write. b = binary.
            with open (output_file, mode='wb') as file:
                file.write (output)
                print ("Wrote data to output file.")
            client.close ()
    else:
        print ("Received data of type: " + str (message_type))

def on_error (client, error):
    print ("Connection error: " + str (error))

def on_close (client):
    print ("Connection closed.")

client = websocket.WebSocketApp(
    uri,
    header=[
        'Ocp-Apim-Subscription-Key: ' + key
    ],
    on_open=on_open,
    on_data=on_data,
    on_error=on_error,
    on_close=on_close
)

print ("Connecting...")
client.run_forever()
```

**Ответ переведенной речи**

Успешным результатом будет создание файла с названием "speak2.wav". Этот файл содержит переводы слов произнесенных в "speak.wav".

[Вверх](#HOLTop)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Учебник перевода речи](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>См. также 

[Microsoft Translator Speech API](../overview.md) (API перевода речи Microsoft)
[API Reference](http://docs.microsofttranslator.com/speech-translate.html) (Ссылка на API)
