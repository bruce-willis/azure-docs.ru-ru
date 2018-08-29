---
title: Устранение неполадок проверки как услуги Azure Stack | Документация Майкрософт
description: Устранение неполадок проверки как услуги для Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 4372da2f1057c06761dd6abaf18c26df6e33640e
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234571"
---
# <a name="troubleshoot-validation-as-a-service"></a>Проверка как услуга: устранение неполадок

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ниже приведены распространенные проблемы, не связанные с выпусками программного обеспечения, и способы их решения.

## <a name="the-portal-shows-local-agent-in-debug-mode"></a>На портале отображается локальный агент в режиме отладки

Скорее всего это происходит, так как агенту не удается отправить пакеты пульса в службу из-за нестабильного сетевого подключения. Пакет пульса отправляется каждые пять минут. Если служба не получает пакет пульса на протяжении 15 минут, служба считает, что агент неактивный и больше не будет выполнять на нем тестирование. Просмотрите сообщение об ошибке в файле *Agenthost.log*, расположенном в каталоге, где был запущен агент.

> [!Note] 
> Все тесты, которые уже выполняются на агенте, продолжат работу, но в случае если пакет пульса не будет восстановлен до окончания этого теста, агент не сможет обновить состояние теста или передать журналы. Тест всегда будет отображаться в состоянии **Выполняется** и его понадобится отменить.

## <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>Процесс агента на компьютере был завершен во время выполнения теста. Основные принципы

Если процесс агента завершается некорректно, например при перезапуске компьютера, он завершен (использование клавиш CTRL+C в окне агента считается нормальным завершением работы), то потом тест, запущенный для него, будет и дальше отображаться с состоянием **Выполняется**. Если агент перезапускается, он обновит тест до состояния **Отменен**. Если агент не перезапускается, то тест отображается с состоянием **Выполняется** и его необходимо отменить вручную.

> [!Note] 
> Тесты в рабочем процессе запланированы последовательно выполняться. Тесты с состоянием **Ожидание** не будут выполняться, пока не выполнятся тесты с состоянием **Выполняется** в рамках того же рабочего процесса.

## <a name="handle-slow-network-connectivity"></a>Обработка медленного сетевого подключения

Вы можете скачать образ PIR в общую папку вашего локального центра обработки данных. Затем можно проверить образ.

<!-- This is from the appendix to the Deploy local agent topic. -->

### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>Скачивание образа PIR в локальную общую папку при медленной передаче трафика

1. Скачайте AzCopy из [vaasexternaldependencies(AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip).

2. Извлеките AzCopy.zip и перейдите в каталог с файлом AzCopy.exe.

3. Откройте Windows PowerShell из командной строки с повышенными привилегиями. Выполните следующие команды:

```PowerShell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare — локальный путь или путь к общей папке.

### <a name="verifying-pir-image-file-hash-value"></a>Проверка значения хэша файла образа PIR

Вы можете использовать командлет **Get-HashFile**, чтобы получить значение хэша для скачанных файлов образа общедоступного репозитория образов, чтобы проверить их целостность.

| Имя файла | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.vhd | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.vhd | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.vhd | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604-20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о проверке как услуге Azure Stack см. в [этой статье](https://docs.microsoft.com/azure/azure-stack/partner).
