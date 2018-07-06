---
title: Создание прозрачного шлюза с помощью Azure IoT Edge (Windows) | Документация Майкрософт
description: Узнайте, как создать прозрачный шлюз, позволяющий обрабатывать информацию на нескольких устройствах, с помощью Azure IoT Edge.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: edc44f0ab2d2cc737807dd8ad543997cdd75bd43
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035212"
---
# <a name="create-a-windows-iot-edge-device-that-acts-as-a-transparent-gateway"></a>Создание устройства Windows IoT Edge, выступающего в роли прозрачного шлюза

В этой статье приведены подробные инструкции по использованию устройства IoT Edge в качестве прозрачного шлюза. В оставшейся части статьи под термином *шлюз IoT Edge* подразумевается устройство IoT Edge, которое используется в качестве прозрачного шлюза. Дополнительные сведения см. в документации по [использованию устройства IoT Edge в качестве шлюза][lnk-edge-as-gateway].

>[!NOTE]
>В данный момент:
> * если шлюз не подключен к Центру Интернета вещей, подчиненные устройства не смогут выполнить аутентификацию с помощью шлюза;
> * устройства IoT Edge не могут подключиться к шлюзам IoT Edge.
> * подчиненные устройства не поддерживают передачу файлов.

При создании прозрачного шлюза основные трудности связаны с безопасным подключением шлюза к подчиненным устройствам. Решение Azure IoT Edge позволяет настраивать безопасные TLS-подключения между этими устройствами с использованием инфраструктуры открытых ключей. В этом случае мы разрешаем подчиненному устройству подключаться к устройству IoT Edge, выполняющему роль прозрачного шлюза.  Чтобы обеспечить приемлемый уровень безопасности, подчиненное устройство должно подтвердить удостоверение пограничного устройства, так как нужно разрешить подключение только между устройствами и шлюзами определенного пользователя и предотвратить подключение к потенциально вредоносным шлюзам.

Вы можете создать любую инфраструктуру сертификата, обеспечивающую доверие, необходимое для топологии "устройство — шлюз". В этой статье предполагается та же настройка сертификата, что и для включения [безопасности, обеспечиваемой сертификатами ЦС X.509][lnk-iothub-x509], в Центре Интернета вещей. Она включает в себя сертификат ЦС X.509, связанный с определенным Центром Интернета вещей (ЦС владельца Центра Интернета вещей), ряд сертификатов, подписанных этим ЦС, и ЦС для пограничного устройства.

![Установка шлюза][1]

Шлюз предоставляет сертификат ЦС своего пограничного устройства подчиненному устройству при инициировании подключения. Подчиненное устройство выполняет проверку, чтобы получить подтверждение того, что сертификат ЦС пограничного устройства подписан сертификатом ЦС владельца. В результате этого процесса подчиненное устройство получает подтверждение о том, что шлюз предоставлен надежным источником.

Ниже приведены пошаговые инструкции по созданию сертификатов и их правильной установке.

## <a name="prerequisites"></a>предварительным требованиям
1.  [Установите среду выполнения Azure IoT Edge][lnk-install-windows-x64] на устройстве Windows, которое необходимо использовать в качестве прозрачного шлюза.

1. Установите OpenSSL для Windows. Это можно сделать множеством способов. Ниже описано, как сделать это с помощью vcpkg.
   1. Загрузите и установите vcpkg с помощью приведенных команд, которые следует выполнить от имени администратора PowerShell. Перейдите в каталог, в который нужно установить OpenSSL, например `$VCPKGDIR`.

   ```PowerShell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg integrate install
   .\vcpkg install openssl:x64-windows
   ```

   1. Задайте переменной среды `OPENSSL_ROOT_DIR` значение `$VCPKGDIR\vcpkg\packages\openssl_x64-windows` и добавьте строку `$VCPKGDIR\vcpkg\packages\openssl_x64-windows\tools\openssl` к переменной среды `PATH`.

1.  Получите скрипты, чтобы с использованием указанной ниже команды создать необходимые сертификаты, которые не используются в рабочих целях. Эти скрипты помогут создать необходимые сертификаты для настройки прозрачного шлюза.

   ```PowerShell
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

1. Перейдите в каталог, в котором вы планируете работать. В дальнейшем он будет называться $WRKDIR.  Все файлы будут созданы в этом каталоге.

   cd $WRKDIR

1. Скопируйте файлы конфигурации и скриптов в рабочую папку.
   ```PowerShell
   copy azure-iot-sdk-c\tools\CACertificates\*.cnf .
   copy azure-iot-sdk-c\tools\CACertificates\ca-certs.ps1 .
   ```

1. Позвольте PowerShell выполнить эти скрипты, выполнив следующую команду:
   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. Добавьте используемые скриптами функции в глобальное пространство имен PowerShell с помощью техники dot-sourcing, выполнив следующую команду:
   ```PowerShell
   . .\ca-certs.ps1
   ```

1. Убедитесь, что средство OpenSSL установлено правильно и не возникают ли конфликты имен с имеющимися сертификатами, выполнив следующую команду:
   ```PowerShell
   Test-CACertsPrerequisites
   ```

## <a name="certificate-creation"></a>Создание сертификата
1.  Создайте сертификат ЦС владельца и один промежуточный сертификат. Они размещаются в каталоге `$WRKDIR`.

   ```PowerShell
   New-CACertsCertChain rsa
   ```

   Выходные данные выполнения скриптов представляют собой следующие сертификаты и ключи:
   * Сертификаты
      * `$WRKDIR\certs\azure-iot-test-only.root.ca.cert.pem`
      * `$WRKDIR\certs\azure-iot-test-only.intermediate.cert.pem`
   * ключей
      * `$WRKDIR\private\azure-iot-test-only.root.ca.key.pem`
      * `$WRKDIR\private\azure-iot-test-only.intermediate.key.pem`

1.  Создайте сертификат ЦС пограничного устройства и закрытый ключ с помощью указанной ниже команды.

   >[!NOTE]
   > **Не** используйте то же самое имя, что и для узла DNS шлюза. Это приведет к ошибке сертификации клиентов для этих сертификатов.

      ```PowerShell
      New-CACertsEdgeDevice "<gateway device name>"
      ```

   Выходные данные выполнения скрипта представляют собой следующие сертификат и ключ:
   * `$WRKDIR\certs\new-edge-device.*`
   * `$WRKDIR\private\new-edge-device.key.pem`

## <a name="certificate-chain-creation"></a>Создание цепочки сертификатов
Создайте цепочку сертификатов, состоящую из сертификата ЦС владельца, промежуточного сертификата и сертификата ЦС пограничного устройства, выполнив следующую команду: Размещение в файле цепочки позволяет легко установить ее на пограничном устройстве, выступающем в роли прозрачного шлюза.

   ```PowerShell
   Write-CACertsCertificatesForEdgeDevice "<gateway device name>"
   ```

## <a name="installation-on-the-gateway"></a>Установка на шлюзе
1.  Скопируйте следующие файлы из каталога $WRKDIR в любое расположение на устройстве Edge, которое теперь будет называться $CERTDIR. Если вы создавали сертификаты на пограничном устройстве, пропустите этот этап.

   * Сертификат ЦС устройства — `$WRKDIR\certs\new-edge-device-full-chain.cert.pem`
   * Закрытый ключ ЦС устройства — `$WRKDIR\private\new-edge-device.key.pem`
   * ЦС владельца — `$WRKDIR\certs\azure-iot-test-only.root.ca.cert.pem`

2.  В файле конфигурации YAML управляющей программы безопасности для свойств `certificate` укажите путь к каталогу, в котором размещены файлы сертификатов и ключей.

```yaml
certificates:
  device_ca_cert: "$CERTDIR\certs\new-edge-device-full-chain.cert.pem"
  device_ca_pk: "$CERTDIR\private\new-edge-device.key.pem"
  trusted_ca_certs: "$CERTDIR\certs\azure-iot-test-only.root.ca.cert.pem"
```
## <a name="deploy-edgehub-to-the-gateway"></a>Развертывание модуля EdgeHub на шлюзе
Одной из ключевых возможностей Azure IoT Edge является развертывание модулей на устройствах IoT Edge из облака. В этом разделе описано, как создать пустое, на первый взгляд, развертывание; однако центр Edge автоматически добавляется во все развертывания даже при отсутствии других модулей. Центр Edge — единственный модуль, который нужен на пограничном устройстве, чтобы оно работало как прозрачный шлюз, поэтому достаточно просто создать пустое развертывание. 
1. Найдите нужный Центр Интернета вещей на портале Azure.
2. Перейдите к **IoT Edge** и выберите устройство IoT Edge, которое нужно использовать в качестве шлюза.
3. Щелкните **Set Modules** (Настроить модули).
4. Щелкните **Далее**.
5. В окне **Укажите маршруты** необходимо настроить маршрут по умолчанию для отправки всех сообщений из всех модулей в Центр Интернета вещей. Если такого маршрута нет, добавьте приведенный ниже код, а затем щелкните **Далее**.
   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```
6. В окне проверки шаблона щелкните **Отправить**.

## <a name="installation-on-the-downstream-device"></a>Установка подчиненного устройства
Подчиненное устройство можно сделать любым приложением с помощью [пакета SDK для устройств Azure IoT][lnk-devicesdk]. Пример такого простого приложения описан в статье [Подключение устройства к Центру Интернета вещей с помощью .NET][lnk-iothub-getstarted]. Приложение подчиненного устройства должно доверять сертификату **ЦС владельца** для проверки TLS-подключений к устройствам шлюза. Как правило, это действие можно выполнить двумя способами: на уровне ОС или на уровне приложения (для некоторых языков).

### <a name="os-level"></a>На уровне ОС
Установка этого сертификата в хранилище сертификатов операционной системы позволит всем приложениям использовать сертификат ЦС владельца в качестве доверенного сертификата.

* Ubuntu. В примере ниже показано, как установить сертификат ЦС на узле под управлением ОС Ubuntu.

   ```cmd
   sudo cp $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem  /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```
 
    Должно отобразиться следующее сообщение: Updating certificates in /etc/ssl/certs... 1 added, 0 removed; done.

* Windows. [В этой](https://msdn.microsoft.com/en-us/library/cc750534.aspx) статье подробно описано, как сделать это на устройстве под управлением ОС Windows с помощью мастера импорта сертификатов.

### <a name="application-level"></a>На уровне приложения
Для приложений .NET можно добавить следующий фрагмент кода, чтобы подтвердить сертификат в формате PEM. Инициализируйте переменную `certPath` со значением `$CERTDIR\certs\azure-iot-test-only.root.ca.cert.pem`.

   ```
   using System.Security.Cryptography.X509Certificates;

   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

## <a name="connect-the-downstream-device-to-the-gateway"></a>Подключение подчиненного устройства к шлюзу
Необходимо инициализировать пакет SDK для устройств Центра Интернета вещей со строкой подключения, ссылающейся на имя узла устройства шлюза. Это можно сделать, добавив свойство `GatewayHostName` в строку подключения устройства. Например, ниже приведен пример строки подключения устройства для устройства, к которому добавляется свойство `GatewayHostName`:

   ```
   HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com
   ```

   >[!NOTE]
   >Это пример команды, с помощью которой можно проверить правильность настроек. Должно отобразиться следующее сообщение: verified OK.
   >
   >openssl s_client -connect mygateway.contoso.com:8883 -CAfile $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem -showcerts

## <a name="routing-messages-from-downstream-devices"></a>Маршрутизация сообщений с подчиненных устройств
Среда выполнения IoT Edge может маршрутизировать сообщения, отправляемые с подчиненных устройств. Например, сообщения, отправляемые модулями. Благодаря этому можно анализировать данные на базе модулей, запущенных на шлюзе, прежде чем данные будут отправлены в облако. Ниже представлен маршрут, по которому сообщения отправляются с подчиненного устройства `sensor` на модуль `ai_insights`.

   ```json
   { "routes":{ "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" } }
   ```

Подробные сведения о маршрутизации сообщений см. в [статье о составлении модулей][lnk-module-composition].

## <a name="next-steps"></a>Дополнительная информация
[Understand the requirements and tools for developing IoT Edge modules - preview][lnk-module-dev] (Сведения о требованиях и средствах разработки модулей IoT Edge (предварительная версия)).

<!-- Images -->
[1]: ./media/how-to-create-transparent-gateway/gateway-setup.png

<!-- Links -->
[lnk-install-windows-x64]: ./how-to-install-iot-edge-windows-with-windows.md
[lnk-module-composition]: ./module-composition.md
[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus
