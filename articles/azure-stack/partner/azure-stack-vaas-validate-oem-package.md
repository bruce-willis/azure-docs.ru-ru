---
title: Проверка пакетов поставщика вычислительной техники (OEM) с помощью проверки как услуги Azure Stack | Документация Майкрософт
description: Узнайте, как проверить пакеты поставщика вычислительной техники (OEM) с помощью проверки как услуги.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: a08f439780e0080d8da2cde1531e1580dbdad14f
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234482"
---
# <a name="validate-oem-packages"></a>Проверка пакетов OEM

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Вы можете протестировать новый пакет OEM при изменение встроенного ПО или драйверов для проверки завершенного решения. Пакет подписывается корпорацией Майкрософт после прохождения теста. В проверке должен содержаться обновленный пакет расширений OEM с драйверами и встроенным ПО, которые прошли проверки по логотипу и PCS Windows Server.

Все тесты завершаются в течение 24 часов с результатом **успешно**. Если какой-либо из тестов имеет в результате **сбой**, зарегистрируйте ошибку в [программе Microsoft Collaborate](https://aka.ms/collaborate) и уведомите о ней корпорацию Майкрософт, отправив сообщение по адресу [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com).

## <a name="get-your-oem-package-signed"></a>Подписание пакета OEM

1. Убедитесь, что применено текущее ежемесячное обновление. Самую последнюю версию см. в [заметках о выпуске в разделе "Обзор" статьи "Документация оператора по Azure Stack"](https://docs.microsoft.com/azure/azure-stack/).

    Обновления программного обеспечения Майкрософт для Azure Stack обозначены с использованием соглашения об именовании. Например, 1803 указывает на обновление за март 2018 г. Сведения о политике обновления, заметках о частоте и выпуске Azure Stack см. в разделе [Политика обслуживания Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-servicing-policy).

1. Проверьте состояние работоспособности системы, выполнив команду **Test-AzureStack**, как описано в статье [Запуск проверочного теста в Azure Stack]. Устраните предупреждения и исправьте ошибки перед запуском каких-либо тестов.

2. На [портале проверки](https://azurestackvalidation.com) выберите имеющееся решение. Если вы не добавили решение, см. [этот раздел](azure-stack-vaas-validate-solution-new.md#add-a-new-solution).

3. Чтобы запустить новый рабочий процесс, нажмите кнопку **Start** (Начать) на плитке **Package Validations** (Проверки пакета).

    ![Проверки пакета](media/image3.png)

4.  Укажите строку подключения диагностики. Инструкции см. в статье [Set up your validation as a service account](azure-stack-vaas-set-up-account.md) (Настройка учетной записи проверки как услуги).

    Для каждого выполнения проверки пакета необходимо указать пакет расширения OEM. Укажите пакет OEM, установленный в решении во время развертывания Azure Stack. Инструкции см. в разделе [Создание Azure Storage Blob для хранения журналов](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs).

    Файл JSON с переменными среды должен использоваться для завершения ввода данных в обязательных полях для выполнения, чтобы избежать ошибок при вводе данных. Инструкции см. в статье [Workflow common parameters for Azure Stack validation as a service](azure-stack-vaas-parameters.md) (Стандартные параметры рабочего процесса для проверки как услуги Azure Stack).

5. Запустите тесты.

6. Когда все тесты будут успешно выполнены, отправьте имя своего решения и проверки пакета по адресу [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) для запроса на подписание пакета.

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения см. в статье [Документация партнера по Azure Stack](https://docs.microsoft.com/azure/azure-stack/partner).
