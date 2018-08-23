---
title: Подсчет символов в API перевода текстов Microsoft Translator | Документация Майкрософт
description: Из этой статьи вы узнаете, как подсчитываются символы в API перевода текстов Microsoft Translator.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: 1b4987509c17e4064d7c54608395e272efa8de3b
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2018
ms.locfileid: "41929903"
---
# <a name="how-the-microsoft-translator-text-api-counts-characters"></a>Подсчет символов в API перевода текстов Microsoft Translator

Microsoft Translator считает каждый символ входных данных. Подсчет ведется в символах Юникода, а не в байтах. Суррогатные символы Юникода подсчитываются как два символа. Пробелы и символы разметки подсчитываются как символы. Длина ответа не имеет значения.

Вызовы методов Detect и BreakSentence не учитываются при подсчете использованных символов. Тем не менее предполагается, что вызовы методов Detect и BreakSentence логически соотносятся с использованием других функций, которые учитываются. Корпорация Майкрософт оставляет за собой право начать подсчет символов в методах Detect и BreakSentence. 

Дополнительные сведения о подсчете символов см. в разделе [часто задаваемых вопросов о Microsoft Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
