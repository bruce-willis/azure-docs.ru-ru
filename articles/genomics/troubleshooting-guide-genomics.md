---
title: 'Microsoft Genomics: руководство по устранению неполадок | Документация Майкрософт'
titleSuffix: Azure
description: Сведения о стратегиях устранения неполадок.
keywords: troubleshooting, error, debugging
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 07/18/2018
ms.openlocfilehash: 9bd1690003fd37b6c2edd0f0421cf8d0e74f8cb5
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144182"
---
# <a name="troubleshooting-guide"></a>Руководство по устранению неполадок
В этом обзоре описываются стратегии для решения распространенных проблем при использовании службы Microsoft Genomics. Ответы на общие часто задаваемые вопросы о Microsoft Genomics см. в [этой статье](frequently-asked-questions-genomics.md). 


## <a name="how-do-i-check-my-job-status"></a>Как проверить состояние задания?
Вы можете проверить состояние рабочего процесса, вызвав `msgen status` из командной строки, как показано ниже. 

```
msgen status -u URL -k KEY -w ID [-f CONFIG] 
```

Существуют три обязательных аргумента:
* URL — базовый универсальный код ресурса (URI) для API.
* KEY — ключ доступа к учетной записи Genomics. 
* ID — идентификатор рабочего процесса.

Чтобы получить аргументы URL и KEY, перейдите на портал Azure и откройте страницу учетной записи Genomics. В разделе **управления** выберите **Ключи доступа**. Вы получите URL-адрес API и ключи доступа.

Кроме того, можно указать путь к файлу конфигурации вместо того, чтобы вводить URL-адрес и ключ. Обратите внимание, что если указать эти аргументы в командной строке и использовать файл конфигурации, аргументы командной строки будут иметь приоритет. 

После вызова `msgen status` отобразится понятное сообщение об успешном выполнении рабочего процесса или сведения о причине сбоя задания. 


## <a name="get-more-information-about-my-workflow-status"></a>Получение дополнительных сведений о состоянии рабочего процесса

Чтобы получить дополнительные сведения о причине сбоя задания, можно просмотреть файлы журнала, которые были созданы во время рабочего процесса. В контейнере выходных данных находится папка `[youroutputfilename].logs.zip`.  Распаковав папку, вы увидите следующие элементы:

* outputFileList.txt — список выходных файлов, созданных во время выполнения рабочего процесса.
* standardError.txt — этот файл пуст.
* standardOutput.txt — содержит данные журналов рабочего процесса верхнего уровня. 
* Файлы журнала GATK — все файлы в папке `logs`.

Определение причины сбоя рабочего процесса можно начать с файла `standardoutput.txt`, так как он содержит подробную информацию о рабочем процессе. 

## <a name="common-issues-and-how-to-resolve-them"></a>Распространенные проблемы и способы их устранения
В этом разделе кратко описываются распространенные проблемы и способы их устранения.

### <a name="fastq-files-are-unmatched"></a>Несоответствие файлов FASTQ
В файлах FASTQ должны различаться только последние символы (/1 или /2) примера идентификатора. Если вы случайно отправили несогласованные файлы FASTQ, при вызове `msgen status` могут появиться следующие сообщения об ошибках.
* `Encountered an unmatched read`
* `Error reading a FASTQ file, make sure the input files are valid and paired correctly` 

Чтобы решить эту проблему, проверьте, согласованы ли отправленные в рабочий процесс файлы FASTQ. 


### <a name="error-uploading-bam-file-output-blob-already-exists-and-the-overwrite-option-was-set-to-false"></a>Ошибка при отправке файла BAM. Выходной большой двоичный объект уже существует, а для параметра перезаписи установлено значение False
Если отображается сообщение об ошибке `Error uploading .bam file. Output blob already exists and the overwrite option was set to False`, папка выходных данных уже содержит выходной файл с таким же именем.  Удалите имеющийся выходной файл или включите в файле конфигурации параметр перезаписи. Затем повторно отправьте рабочий процесс.

### <a name="when-to-contact-microsoft-genomics-support"></a>Когда следует обращаться в службу поддержки Microsoft Genomics
Если отображаются следующие сообщения, произошла внутренняя ошибка. 

* `Error locating input files on worker machine`
* `Process management failure`

Попробуйте повторно отправить рабочий процесс. Если у вас по-прежнему возникают проблемы с заданием или у вас есть другие вопросы, обратитесь в службу поддержки Microsoft Genomics с портала Azure. Дополнительные сведения о том, как отправить запрос в службу поддержки, см. [здесь](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Дополнительная информация
В этой статье вы узнали, как устранять распространенные проблемы с помощью службы Microsoft Genomics. Дополнительные сведения и ответы на общие вопросы см. в статье [Microsoft Genomics: часто задаваемые вопросы](frequently-asked-questions-genomics.md). 
