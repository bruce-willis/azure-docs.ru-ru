---
title: Службы мультимедиа Azure и поддержка лицензии Apple FairPlay | Документация Майкрософт
description: В этом разделе приводится обзор требований и конфигурации лицензии Apple FairPlay.
author: juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2018
ms.author: juliako
ms.openlocfilehash: a68896d061040843990318cbc39eaf1aaa3c8b27
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39115120"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Требования и конфигурация лицензии Apple FairPlay 

Службы мультимедиа позволяют шифровать содержимое HLS с помощью **Apple FairPlay** (AES-128 CBC). Службы мультимедиа также обеспечивают доставку лицензий FairPlay. Когда проигрыватель пытается воспроизвести содержимое, защищенное с помощью FairPlay, в службу доставки лицензий отправляется запрос на получение лицензии. Если служба лицензий утверждает запрос, она выдает лицензию, которая отправляется клиенту и используется для расшифровки и воспроизведения указанного содержимого.

Службы мультимедиа также предоставляют интерфейсы API, которые можно использовать для настройки лицензий FairPlay. В этом разделе рассматриваются требования к лицензиям FairPlay и показано, как настроить лицензию **FairPlay** с помощью интерфейсов API Служб мультимедиа. 

## <a name="requirements"></a>Требования

При использовании Служб мультимедиа для шифрования содержимого HLS с помощью **Apple FairPlay** и доставки лицензий FairPlay требуется следующее.

* Регистрация в [программе разработки Apple](https://developer.apple.com/).
* Корпорация Apple требует от владельца содержимого получить [пакет развертывания](https://developer.apple.com/contact/fps/). Подтвердите, что у вас реализован модуль безопасности ключа (KSM) с использованием служб мультимедиа Azure и что вы подали запрос на окончательный пакет FPS. Окончательный пакет FPS содержит инструкции по сертификации и получению секретного ключа приложения (ASK). ASK используется для настройки FairPlay.
* Перед доставкой ключей или лицензий Служб мультимедиа должны быть настроены следующие параметры.

    * **Сертификат приложения (AC)**  — это PFX-файл, который содержит закрытый ключ. Создайте этот файл и зашифруйте его с помощью пароля. PFX-файл быть в формате Base64.

        В следующих действиях описано, как создать PFX-файл сертификата для FairPlay.

        1. Установите OpenSSL со страницы https://slproweb.com/products/Win32OpenSSL.html.

            Перейдите в папку, где расположены сертификат FairPlay и другие файлы, полученные от Apple.
        2. Выполните следующую команду из командной строки. Она преобразует CER-файл в PEM-файл.

            "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
        3. Выполните следующую команду из командной строки. Она преобразует PEM-файл в PFX-файл, который содержит закрытый ключ. Затем OpenSSL запросит пароль для PFX-файла.

            "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
            
    * **Пароль к сертификату приложения** — пароль для создания PFX-файла.
    * **ASK** — ключ, полученный при создании сертификата с использованием портала разработчиков Apple. Каждая группа разработчиков получает уникальный ASK. Сохраните копию ASK в безопасном расположении. ASK необходимо настроить как FairPlayAsk для Служб мультимедиа.
    
* На стороне клиента FPS должны быть заданы следующие значения:

  * **Сертификат приложения (AC)**  — это CER-файл или DER-файл, содержащий открытый ключ, который операционная система использует для шифрования некоторых полезных данных. Службы мультимедиа следует связать с этим сертификатом, так как он используется проигрывателем. Служба доставки ключей расшифровывает их, используя соответствующий закрытый ключ.

* Для воспроизведения зашифрованного потока FairPlay получите реальный ASK, а затем создайте реальный сертификат. В результате этого процесса создаются три элемента:

  * DER-файл;
  * PFX-файл;
  * пароль для PFX-файла.

## <a name="fairplay-and-player-apps"></a>FairPlay и приложения проигрывателя

Если содержимое шифруется с помощью **Apple FairPlay**, отдельные образцы видео и аудиоданных шифруются в режиме **AES-128 CBC**. **Потоковая передача FairPlay** (FPS): интегрируется в операционные системы устройств и поддерживается iOS и Apple TV по умолчанию. Включение FPS в Safari (OS X) реализуется благодаря поддержке интерфейса расширений зашифрованных носителей (EME).

Проигрыватель мультимедиа Azure также поддерживает воспроизведение FairPlay. Дополнительные сведения см. в [документации по Проигрывателю мультимедиа Azure](https://amp.azure.net/libs/amp/latest/docs/index.html).

С помощью пакета SDK для iOS можно разрабатывать собственные приложения проигрывателя. Для воспроизведения содержимого FairPlay необходимо реализовать протокол обмена лицензиями. Этот протокол не указывается компанией Apple. Каждое приложение может само выбирать, как отправлять запросы доставки ключей. Служба доставки ключей FairPlay служб мультимедиа ожидает, что SPC поступит как сообщение публикации www-form-url в следующем формате:

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>Настройка FairPlay: пример для .NET

API Служб мультимедиа можно использовать для настройки лицензий FairPlay. Когда проигрыватель пытается воспроизвести содержимое, защищенное с помощью FairPlay, в службу доставки лицензий отправляется запрос на получение лицензии. Если служба лицензий утвердит запрос, служба выдаст лицензию. Лицензия отправляется клиенту и используется для расшифровки и воспроизведения указанного содержимого.

> [!NOTE]
> Обычно настраивать параметры политики FairPlay необходимо только один раз, так как у вас будет один набор сертификации и ASK.

В следующем примере для настройки лицензии используется [пакет SDK Служб мультимедиа для .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models?view=azure-dotnet).

```csharp
private static ContentKeyPolicyFairPlayConfiguration ConfigureFairPlayPolicyOptions()
{

    string askHex = "";
    string FairPlayPfxPassword = "";

    var appCert = new X509Certificate2("FairPlayPfxPath", FairPlayPfxPassword, X509KeyStorageFlags.Exportable);

    byte[] askBytes = Enumerable
        .Range(0, askHex.Length)
        .Where(x => x % 2 == 0)
        .Select(x => Convert.ToByte(askHex.Substring(x, 2), 16))
        .ToArray();

    ContentKeyPolicyFairPlayConfiguration fairPlayConfiguration =
    new ContentKeyPolicyFairPlayConfiguration
    {
        Ask = askBytes,
        FairPlayPfx =
                Convert.ToBase64String(appCert.Export(X509ContentType.Pfx, FairPlayPfxPassword)),
        FairPlayPfxPassword = FairPlayPfxPassword,
        RentalAndLeaseKeyType =
                ContentKeyPolicyFairPlayRentalAndLeaseKeyType
                .PersistentUnlimited,
        RentalDuration = 2249
    };

    return fairPlayConfiguration;
}
```

## <a name="next-steps"></a>Дополнительная информация

Узнайте о возможностях [защиты с помощью технологии DRM](protect-with-drm.md).