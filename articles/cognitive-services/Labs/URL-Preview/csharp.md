---
title: Краткое руководство по C#. Использование службы предварительного просмотра URL-адресов в проектах в Microsoft Cognitive Services | Документация Майкрософт
description: Начало работы со службой предварительного просмотра URL-адресов в проектах, входящей в состав Microsoft Cognitive Services в Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 17d44bd0c23d0a1e67da5a0e91248700d3166c1a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381776"
---
# <a name="url-preview-query-in-c"></a>Запрос на предварительный просмотр URL-адреса в C#

Следующий пример C# создает представление для предварительного просмотра URL-адреса веб-сайта SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого кода на компьютерах под управлением Windows потребуется [Visual Studio 2017](https://www.visualstudio.com/downloads/). (Подойдет бесплатный выпуск Community Edition.)

Получите ключ доступа для бесплатной пробной версии [Cognitive Services Labs](https://aka.ms/answersearchsubscription).

## <a name="code-scenario"></a>Сценарий кода

Следующий пример C# создает представление для предварительного просмотра URL-адреса веб-сайта SwiftKey: https://swiftkey.com/en. 

Он реализуется с помощью следующих действий:
1. Объявление переменных для указания конечной точки и URL-адреса запроса для предварительного просмотра.  
2. Создание запроса.
3. Добавление заголовка *Ocp-Apim-Subscription-Key*. 
4. Асинхронное выполнение веб-запроса. 
5. Считывание ответа.
6. Вывод заголовков и результатов в формате JSON на консоль.

**Исходный код**.

```
using System;
using System.IO;
using System.Net;
using System.Text;

namespace UrlPrevCshp
{
    class Program
    {
        static void Main(string[] args)
        {
            String uriBase = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search";
            String searchQuery = "https://swiftkey.com/en"; 
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery);

            // Do the Web request and get response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR-SUBSCRIPTION-KEY";
            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            Console.WriteLine("\nHTTP Headers:\n");
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    Console.WriteLine(header + ": " + response.Headers[header]);
            }

            Console.WriteLine(JsonPrettyPrint(json));
            

            Console.ReadKey();
        }


        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }

    }
}

```
## <a name="running-the-application"></a>Запуск приложения

Чтобы запустить приложение, выполните следующие действия:

1. Создайте консольное приложение в Visual Studio.
2. Замените `Program.cs` указанным кодом.
3. Замените значение `YOUR-ACCESS-KEY` ключом доступа, допустимым для вашей подписки.
4. Запустите программу.

## <a name="next-steps"></a>Дополнительная информация
- [Краткое руководство — Java](java-quickstart.md)
- [Краткое руководство — JavaScript](javascript.md)
- [Краткое руководство — Node](node-quickstart.md)
- [Краткое руководство — Python](python-quickstart.md)