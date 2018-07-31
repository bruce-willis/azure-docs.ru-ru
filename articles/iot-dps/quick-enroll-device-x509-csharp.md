---
title: В этом кратком руководстве показано, как регистрировать устройства X.509 в Службе подготовки устройств Azure с помощью C# | Документация Майкрософт
description: С помощью этого краткого руководства можно зарегистрировать устройства X.509 в Службе подготовки устройств к добавлению в Центр Интернета вещей Azure с помощью C#
author: wesmc7777
ms.author: wesmc
ms.date: 01/21/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: cf2a2dfbb46b8958e10431ba61e4bd8bc7ae18d6
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39204965"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-c"></a>Краткое руководство. Регистрация устройств X.509 в Службе подготовки устройств с помощью C#

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]


В этом кратком руководстве показано, как использовать C# для программного создания [группы регистрации](concepts-service.md#enrollment-group), которая использует промежуточный или корневой сертификат CA X.509. Эта группа регистрации создается с помощью [пакета SDK для Центра Интернета вещей Microsoft Azure для .NET](https://github.com/Azure/azure-iot-sdk-csharp) и примера приложения C# .NET Core. Группа регистрации управляет доступом к службе подготовки устройств, которые совместно используют стандартный сертификат для подписи в цепочке сертификатов. Дополнительные сведения см. в разделе [Управление доступом устройств к службе подготовки с использованием сертификатов X.509](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). Дополнительные сведения об использовании инфраструктуры открытых ключей на основе сертификатов X.509 с Центром Интернета вещей и службой подготовки устройств см. в статье [Device Authentication using X.509 CA Certificates](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview) (Проверка подлинности устройств с помощью сертификатов ЦС X.509). 

В этом руководстве предполагается, что Центр Интернета вещей и экземпляр Службы подготовки устройств уже созданы. Если эти ресурсы еще не созданы, прежде чем продолжить, выполните действия, описанные в руководстве по [настройке Службы подготовки устройств к добавлению в Центр Интернета вещей на портале Azure](./quick-setup-auto-provision.md).

Описанные в этой статье действия применимы к виртуальным машинам Windows и Linux, но статья предназначена для работы с машинами Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Предварительные требования

* Установите [Visual Studio 2017](https://www.visualstudio.com/vs/).
* Установите [пакет SDK для .NET Core](https://www.microsoft.com/net/download/windows).
* Установите [Git](https://git-scm.com/download/).



## <a name="prepare-test-certificates"></a>Подготовка тестовых сертификатов

Для этого краткого руководства требуется файл PEM или CER, который содержит общедоступную часть промежуточного или корневого сертификата ЦС X.509. Этот сертификат должен быть отправлен в службу подготовки и проверен в ней. 

[Пакет SDK для Центра Интернета вещей Azure](https://github.com/Azure/azure-iot-sdk-c) содержит средства тестирования, с помощью которых можно создать цепочку сертификатов X.509, передать корневой или промежуточный сертификат из этой цепочки и подтвердить владение сертификатом в службе. Сертификаты, созданные с помощью инструментария пакета SDK, можно использовать только в **целях тестовой разработки**. Эти сертификаты **не должны использоваться в рабочей среде**. В них жестко заданы пароли ("1234"), срок действия которых истекает через 30 дней. Сведения о получении сертификатов, подходящих для рабочего использования, см. в документации Центра Интернета вещей Azure в статье [Как получить сертификат ЦС X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate).

Чтобы создать сертификаты с помощью этих средств тестирования, выполните следующие действия. 
 
1. Откройте командную строку или оболочку Git Bash и перейдите в рабочую папку на компьютере. Выполните следующую команду для клонирования репозитория GitHub [пакета SDK для устройства C Интернета вещей Azure](https://github.com/Azure/azure-iot-sdk-c):
    
  ```cmd/sh
  git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
  ```

  Размер этого репозитория в настоящее время составляет примерно 220 МБ. Выполнение этой операции может занять несколько минут.

  Средства тестирования находятся в клонированном репозитории *azure-iot-sdk-c/tools/CACertificates*.    

2. Выполните действия, описанные в статье [Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) (Управление тестовыми сертификатами ЦС для образцов и руководств). 

Помимо средств в *пакете SDK для Центра Интернета вещей Microsoft Azure для .NET* в [примере проверки сертификата группы](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples/GroupCertificateVerificationSample) показано, как подтвердить владение в C# с помощью имеющегося промежуточного или корневого сертификата ЦС X.509. 


## <a name="get-the-connection-string-for-your-provisioning-service"></a>Получение строки подключения к службе подготовки

Чтобы запустить пример из этого краткого руководства, требуется строка подключения к службе подготовки.
1. Войдите на портал Azure, нажмите кнопку **Все ресурсы** в меню слева и откройте Службу подготовки устройств. 
2. Щелкните **Политики общего доступа**, а затем выберите нужную политику доступа, чтобы открыть ее свойства. В окне **Политика доступа** скопируйте и запишите строку подключения первичного ключа. 

    ![Получение строки подключения к службе подготовки на портале](media/quick-enroll-device-x509-csharp/get-service-connection-string.png)

## <a name="create-the-enrollment-group-sample"></a>Создание примера группы регистрации 

В этом разделе показано, как создать консольное приложение .NET Core, которое добавляет группу регистрации в службу подготовки. С некоторыми изменениями c помощью этих шагов можно также создать консольное приложение [Windows IoT Базовая](https://developer.microsoft.com/en-us/windows/iot), чтобы добавить группу регистрации. Дополнительные сведения о разработке с помощью Windows IoT Базовая см. в этой [документации](https://docs.microsoft.com/windows/iot-core/).
1. В Visual Studio добавьте в новое решение проект консольного приложения Visual C# .NET Core с помощью шаблона проекта **консольного приложения (.NET Core)**. Убедитесь, что указана версия платформы .NET 4.5.1 или более поздняя версия. Назовите проект **CreateEnrollmentGroup**.

    ![Новый проект классического приложения Windows на языке Visual C#](media//quick-enroll-device-x509-csharp/create-app.png)

2. В обозревателе решений щелкните правой кнопкой мыши проект **CreateEnrollmentGroup** и выберите **Управление пакетами NuGet**.
3. В окне **Диспетчер пакетов NuGet** нажмите кнопку **Обзор**, найдите **Microsoft.Azure.Devices.Provisioning.Service**, щелкните **Установить**, чтобы установить пакет **Microsoft.Azure.Devices.Provisioning.Service**, и примите условия использования. В результате скачивается и устанавливается [клиентский пакет NuGet для службы "Подготовка устройств к добавлению в Центр Интернета вещей"](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) и его зависимости, а также добавляется соответствующая ссылка.

    ![Окно "Диспетчер пакетов NuGet"](media//quick-enroll-device-x509-csharp/add-nuget.png)

4. Добавьте в начало файла **Program.cs** после других инструкций `using` следующие инструкции `using`:
   
   ```csharp
   using System.Security.Cryptography.X509Certificates;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```
    
5. Добавьте следующие поля в класс **Program** .  
   - Замените значение заполнителя **ProvisioningConnectionString** строкой подключения к службе подготовки, для которой необходимо создать регистрацию.
   - Замените заполнитель **X509RootCertPath** путем к PEM- или CER-файлу, которые представляют открытую часть ранее отправленного и проверенного службой подготовки промежуточного или корневого сертификата ЦС X.509.
   - При необходимости можно изменить значение **EnrollmentGroupId**. Строка может содержать только символы в нижнем регистре и дефисы. 

   > [!IMPORTANT]
   > В рабочем коде следует учитывать следующие рекомендации по безопасности:
   >
   > - Жесткое программирование строки подключения для администратора службы подготовки противоречит рекомендациям по обеспечению безопасности. Вместо этого строки подключения должны храниться без угрозы безопасности, например в безопасном файле конфигурации или в реестре.
   > - Обязательно отправьте только открытую часть сертификата подписи. Никогда не отправляйте в службу подготовки PFX- (PKCS12) или PEM-файлы, содержащие закрытые ключи.
        
   ```csharp
   private static string ProvisioningConnectionString = "{Your provisioning service connection string}";
   private static string EnrollmentGroupId = "enrollmentgrouptest";
   private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
   ```
    
6. Добавьте следующий метод в класс **Program**. Этот код создает запись группы регистрации, а затем вызывает метод **CreateOrUpdateEnrollmentGroupAsync** в **ProvisioningServiceClient**, чтобы добавить группу регистрации в службу подготовки.
   
   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");
 
       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new enrollmentGroup config
           Console.WriteLine("\nCreating a new enrollmentGroup...");
           var certificate = new X509Certificate2(X509RootCertPath);
           Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);
           EnrollmentGroup enrollmentGroup =
                   new EnrollmentGroup(
                           EnrollmentGroupId,
                           attestation)
                   {
                       ProvisioningStatus = ProvisioningStatus.Enabled
                   };
           Console.WriteLine(enrollmentGroup);
           #endregion
 
           #region Create the enrollmentGroup
           Console.WriteLine("\nAdding new enrollmentGroup...");
           EnrollmentGroup enrollmentGroupResult =
               await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);
           Console.WriteLine("\nEnrollmentGroup created with success.");
           Console.WriteLine(enrollmentGroupResult);
           #endregion
 
       }
   }
   ```

7. Наконец, замените текст метода **Main** следующими строками:
   
   ```csharp
   RunSample().GetAwaiter().GetResult();
   Console.WriteLine("\nHit <Enter> to exit ...");
   Console.ReadLine();
   ```
        
8. Выполните сборку решения.

## <a name="run-the-enrollment-group-sample"></a>Запуск примера группы регистрации
  
1. Запустите пример в Visual Studio, чтобы создать группу регистрации.
 
2. После успешного создания в окне командной строки отобразятся свойства новой группы регистрации.

    ![Свойства регистрации в выходных данных команды](media/quick-enroll-device-x509-csharp/output.png)

3. Чтобы проверить, создана ли группа регистрации, в колонке сводки службы подготовки устройств на портале Azure выберите **Управление регистрациями**, а затем перейдите на вкладку **Группы регистрации**. Вы должны увидеть новую запись регистрации, которая соответствует идентификатору регистрации, используемому в примере. Щелкните запись, чтобы проверить отпечаток сертификата и другие ее свойства.

    ![Свойства регистрации на портале](media/quick-enroll-device-x509-csharp/verify-enrollment-portal.png)
 
## <a name="clean-up-resources"></a>Очистка ресурсов
Если вы планируете изучить пример службы C#, не удаляйте ресурсы, которые создали при работе с этим руководством. Если вы не планируете продолжать работу, следуйте инструкциям ниже, чтобы удалить все созданные ресурсы.

1. Закройте окно выходных данных примера C#, если оно открыто на компьютере.
2. Перейдите к службе подготовки устройств на портале Azure, щелкните **Управление регистрациями** и выберите вкладку **Группы регистрации**. Выберите *Идентификатор регистрации* для записи регистрации, которую вы создали в процессе работы с этим руководством, и нажмите кнопку **Удалить** в верхней части колонки.  
3. На странице службы подготовки устройств на портале Azure щелкните **Сертификаты**, выберите переданный для работы с руководством сертификат, а затем нажмите кнопку **Удалить** в верхней части окна  **Сведения о сертификате**.  
 
## <a name="next-steps"></a>Дополнительная информация
В этом кратком руководстве вы создали группу регистрации сертификата X.509 промежуточного или корневого центра сертификации с помощью службы "Подготовка устройств к добавлению в Центр Интернета вещей". Дополнительные сведения о подготовке устройств см. в руководстве по настройке службы подготовки устройств на портале Azure. 
 
> [!div class="nextstepaction"]
> [Руководства по службе подготовки устройств для Центра Интернета вещей Azure](./tutorial-set-up-cloud.md)
