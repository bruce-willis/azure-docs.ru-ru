---
title: Подготовка главного компьютера для размещения Пакета средств разработки Azure Stack (ASDK) | Документация Майкрософт
description: В этой статье объясняется, как подготовить главный компьютер для установки Пакета средств разработки Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: fc9681ee286c30825ac908f9f97ae092808c783a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48802143"
---
# <a name="prepare-the-asdk-host-computer"></a>Подготовка главного компьютера для ASDK
Перед установкой ASDK на главном компьютере необходимо подготовить среду ASDK. После подготовки данные главного компьютера, на котором будет размещен пакет средств разработки, загружаются с виртуального жесткого диска CloudBuilder.vhdx, чтобы начать развертывание ASDK.

## <a name="prepare-the-development-kit-host-computer"></a>Подготовка главного компьютера для размещения пакета средств разработки
Перед установкой ASDK на главном компьютере необходимо подготовить среду ASDK.
1. Войдите на главный компьютер, где будет размещен пакет средств разработки, от имени локального администратора.
2. Переместите файл CloudBuilder.vhdx в корень диска C:\ (C:\CloudBuilder.vhdx).
3. Запустите следующий скрипт, чтобы скачать файл установщика пакета разработчика (asdk-installer.ps1) из репозитория инструментов [Azure Stack на GitHub](https://github.com/Azure/AzureStack-Tools) в папку **C:\AzureStack_Installer** на главном компьютере разработки:

  ```powershell
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Enforce usage of TLSv1.2 to download from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. Откройте консоль PowerShell с повышенными привилегиями, выполните скрипт **C:\AzureStack_Installer\asdk-installer.ps1** и щелкните **Prepare Environment** (Подготовка среды).

    ![](media/asdk-prepare-host/1.PNG) 

5. На странице **Select Cloudbuilder vhdx** (Выбор файла Cloudbuilder.vhdx) установщика найдите и выберите файл **Cloudbuilder.vhdx**, скачанный и извлеченный на [предыдущих шагах](asdk-download.md). На этой странице вы также можете установить флажок **Add drivers** (Добавление драйверов), если вам нужно добавить драйверы на главный компьютер разработки. Щелкните **Далее**.  

    ![](media/asdk-prepare-host/2.PNG)

6. На странице **дополнительных параметров** укажите сведения об учетной записи локального администратора для главного компьютера, где размещается пакет средств разработки, и нажмите кнопку **Next** (Далее). Можно также задать значения для следующих дополнительных параметров:
  - **Computername** (Имя компьютера). Задает имя узла комплекта разработки. Имя должно соответствовать требованиям к полному доменному имени, а его размер не должен превышать 15 символов. Значение по умолчанию — случайное имя компьютера, созданное Windows.
  - **Static IP configuration** (Конфигурация статических IP-адресов). Задает использование статического IP-адреса в развертывании. В противном случае при перезагрузке установщика в файл Cloudbuilder.vhdx сетевые интерфейсы настраиваются с помощью протокола DHCP.

    ![](media/asdk-prepare-host/3.PNG)

  > [!IMPORTANT]
  > Если на этом шаге не предоставить учетные данные локального администратора, вам понадобится прямой доступ или доступ KVM к главному компьютеру после перезагрузки компьютера в рамках настройки пакета средств разработки.

7. Если вы на предыдущем шаге выбрали конфигурацию статических IP-адресов, далее сделайте следующее:
    - Выберите сетевой адаптер. Проверьте, можете ли вы подключиться к адаптеру, а затем нажмите кнопку **Далее**.
    - Проверьте значения параметров **IP-адрес**, **Шлюз** и **DNS**, а затем нажмите кнопку **Далее**.
13. Нажмите кнопку **Далее**, чтобы запустить процесс подготовки.
14. Когда состояние подготовки изменится на **Завершено**, нажмите кнопку **Далее**.
15. Нажмите кнопку **Reboot now** (Перезагрузить сейчас), чтобы загрузить данные главного компьютера для размещения пакета средств разработки на виртуальный жесткий диск Cloudbuilder.vhdx и [продолжить процесс развертывания](asdk-install.md).

    ![](media/asdk-prepare-host/4.PNG)


## <a name="next-steps"></a>Дополнительная информация
[Установка ASDK](asdk-install.md)
