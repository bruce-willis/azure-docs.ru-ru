---
title: Настройка исходной среды (репликация физических серверов в Azure) | Документация Майкрософт
description: В этой статье приведены сведения о настройке локальной среды для запуска репликации физических серверов под управлением Windows или Linux в Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/21/2018
ms.author: raynew
ms.openlocfilehash: 0cbba45ce49667293d8f16bf370424acd70ff78b
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213492"
---
# <a name="set-up-the-source-environment-physical-server-to-azure"></a>Настройка исходной среды (репликация физических серверов в Azure)

В этой статье приведены сведения о настройке локальной среды для запуска репликации физических серверов под управлением Windows или Linux в Azure.

## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что у вас уже имеется:
- Хранилище служб восстановления на [портале Azure](http://portal.azure.com "портал Azure").
- Физический компьютер для установки сервера конфигурации.
- Если вы отключили TLS 1.0 на компьютере, на котором вы устанавливаете сервер конфигурации, включите TLS 1.2 и установите .NET Framework версии 4.6 или более поздней (надежное шифрование должно быть отключено). [Узнайте больше](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1).

### <a name="configuration-server-minimum-requirements"></a>Минимальные требования к серверу конфигурации
В следующей таблице перечислены минимальные требования к оборудованию, программному обеспечению и сети сервера конфигурации.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> Сервер конфигурации не поддерживает прокси-серверы на основе HTTPS.

## <a name="choose-your-protection-goals"></a>Выбор целевых объектов для защиты

1. На портале Azure откройте колонку **Хранилища служб восстановления** и выберите свое хранилище.
2. В меню **Ресурс** хранилища выберите **Приступая к работе** > **Site Recovery** > **Шаг 1. Подготовка инфраструктуры** > **Цель защиты**.

    ![Выбор цели](./media/physical-azure-set-up-source/choose-goals.png)
3. На странице **Цель защиты** выберите **To Azure** (В Azure), а затем — **Без виртуализации или иное**. Нажмите кнопку **ОК**.

    ![Выбор цели](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>Настройка исходной среды

1. Если у вас нет сервера конфигурации, в окне **Prepare source** (Подготовка источника) щелкните **+Configuration server** (+Сервер конфигурации).

  ![Настройка источника](./media/physical-azure-set-up-source/plus-config-srv.png)
2. В колонке **Добавление сервера** в поле **Тип сервера** должно быть указано **Сервер конфигурации**.
4. Скачайте файл единой установки Site Recovery.
5. Скачайте ключ регистрации хранилища. При запуске программы единой установки вам потребуется ключ регистрации. Ключ действителен в течение пяти дней после создания.

    ![Настройка источника](./media/physical-azure-set-up-source/set-source2.png)
6. На компьютере, используемом в качестве сервера конфигурации, запустите **программу единой установки Azure Site Recovery**, чтобы установить сервер конфигурации, сервер обработки и главный целевой сервер.

#### <a name="run-azure-site-recovery-unified-setup"></a>Выполнение единой установки Azure Site Recovery

> [!TIP]
> Если системное время на компьютере отличается от местного более чем на 5 минут, то регистрация сервера конфигурации завершится сбоем. Перед началом установки синхронизируйте системное время с [сервером времени](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service).

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Сервер конфигурации можно установить с помощью командной строки. [Узнайте больше](physical-manage-configuration-server.md#install-from-the-command-line).


## <a name="common-issues"></a>Распространенные проблемы

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Дополнительная информация

Следующий этап заключается в [настройке целевой среды](physical-azure-set-up-target.md) в Azure.
