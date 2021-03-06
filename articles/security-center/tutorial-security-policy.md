---
title: Руководство по работе с центром безопасности Azure. Определение и оценка политик безопасности | Документация Майкрософт
description: Руководство по работе с центром безопасности Azure. Определение и оценка политик безопасности
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: terrylan
ms.openlocfilehash: 15c69bce87ede96eb3a7bc0bada4e4f6a6669abb
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358614"
---
# <a name="tutorial-define-and-assess-security-policies"></a>Руководство: определение и оценка политик безопасности
Центр безопасности помогает обеспечить соответствие требованиям компании или нормативным требованиям к безопасности с помощью политик безопасности, которые определяет требуемую конфигурацию для рабочих нагрузок. После того как вы определили политики для своих подписок Azure и адаптировали их к типу рабочей нагрузки или конфиденциальности данных, центр безопасности может предоставлять рекомендации по обеспечению безопасности ресурсов вычислений, приложений, сети, данных, хранилища, удостоверений и доступа. Из этого учебника вы узнаете следующее:

> [!div class="checklist"]
> * Настройка политики безопасности
> * оценка состояния безопасности ресурсов.

Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования
Для выполнения инструкций этого руководства требуется ценовая категория центра безопасности "Стандартный". Вы можете использовать центр безопасности категории "Стандартный" бесплатно в течение первых 60 дней. Следуйте инструкциям в [кратком руководстве по центру безопасности Azure](security-center-get-started.md), чтобы обновить ценовую категорию до уровня "Стандартный".

## <a name="configure-security-policy"></a>Настройка политики безопасности
Центр безопасности автоматически создает политику безопасности по умолчанию для каждой из ваших подписок Azure. Политики безопасности состоят из рекомендаций, которые можно включать или отключать в соответствии с требованиями безопасности этой подписки. Стандартную политику безопасности может изменить пользователь с правами владельца, участника или администратора безопасности подписки.

1. В главном меню центра безопасности выберите **Политика безопасности**.
2. Выберите подписку, которую нужно использовать.

  ![Политика безопасности](./media/tutorial-security-policy/tutorial-security-policy-fig1.png)  

3. В разделе **Компоненты политики** выберите пункт **Политика безопасности**.
4. Для каждой конфигурации безопасности, которую вы хотите отслеживать, выберите значение **Вкл**. Центр безопасности будет постоянно оценивать конфигурацию среды и в случае возникновения уязвимости центр безопасности создаст соответствующие рекомендации. Выберите значение **Выкл.**, если конфигурация безопасности не рекомендуется или неприменима в вашей среде. Например, в среде разработки и тестирования может не требоваться тот же уровень безопасности, что и в производственной среде. Выберите политики, применимые к вашей среде, и нажмите кнопку **Сохранить**.

  ![Конфигурация системы безопасности](./media/tutorial-security-policy/tutorial-security-policy-fig6.png)  

Подождите, пока центр безопасности обработает эти политики и создаст рекомендации. Для реализации некоторых конфигураций, например обновлений системы и настроек операционной системы, может потребоваться до 12 часов. После этого группы безопасности сети и конфигурации шифрования будут оцениваться практически мгновенно. Когда вы увидите рекомендации на панели мониторинга центра безопасности, можете переходить к следующему шагу.

## <a name="assess-security-of-resources"></a>Оценка безопасности ресурсов
1. В соответствии с политиками безопасности, которые были включены, центр безопасности предоставит набор рекомендаций при необходимости. Необходимо начать с просмотра рекомендаций для виртуальной машины и компьютеров. Откройте панель мониторинга центра безопасности. Выберите вкладку **Обзор**, а затем выберите **Вычисления и приложения**.

  ![Службы вычислений](./media/tutorial-security-policy/tutorial-security-policy-fig2.png)

  Просмотрите каждую рекомендацию. В первую очередь обратите внимание на рекомендации, выделенные красным цветом (высокий приоритет). Некоторые из этих рекомендаций содержат исправления, которые можно реализовать непосредственно в центре безопасности, например, исправление [проблем с защитой конечных точек](https://docs.microsoft.com/azure/security-center/security-center-install-endpoint-protection). Другие рекомендации содержат только правила применения исправлений, например рекомендация шифрования диска.

2. Когда вы выполните все применимые к ресурсам вычислений рекомендации, можете перейти к следующей рабочей нагрузке — ресурсам сети. Откройте панель мониторинга центра безопасности. Выберите вкладку **Обзор**, затем — **Сеть**.

  ![Сеть](./media/tutorial-security-policy/tutorial-security-policy-fig3.png)

  Страница рекомендаций для ресурсов сети содержит список проблем безопасности, связанных с конфигурацией сети, конечными точками, подключенными к Интернету, и топологией сети. Так же, как и рекомендации для **вычислений и приложений**, некоторые рекомендации для ресурсов сети будут содержать встроенные исправления, а некоторые нет.

3. Когда вы выполните все применимые к ресурсам сети рекомендации, можете перейти к следующей рабочей нагрузке — хранилищу и данным. Откройте панель мониторинга центра безопасности. Выберите вкладку **Обзор**, а затем — **Data & storage** (Данные и хранилище).

  ![Ресурсы данных](./media/tutorial-security-policy/tutorial-security-policy-fig4.png)

  Страница **Ресурсы данных** содержит рекомендации включения аудита для серверов и баз данных SQL Azure, включения шифрования для баз данных SQL и шифрования учетной записи хранения Azure. Если у вас нет этих рабочих нагрузок, вы не увидите рекомендации. Так же, как и рекомендации для **вычислений и приложений**, некоторые рекомендации для данных и хранилищ будут содержать встроенные исправления, а некоторые нет.

4. Когда вы выполните все применимые к данным и хранилищам рекомендации, можете перейти к следующей рабочей нагрузке — удостоверениям и доступу. Откройте панель мониторинга центра безопасности. Выберите вкладку **Обзор**, а затем — **Удостоверения и доступ**.

  ![Удостоверения и доступ](./media/tutorial-security-policy/tutorial-security-policy-fig5.png)

  Страница **Удостоверения и доступ** содержит такие рекомендации, как:

   - включение MFA для привилегированных учетных записей в подписке;
   - Удалите внешние учетные записи с разрешениями на запись из подписки
   - удаление привилегированных внешних учетных записей из подписки;

## <a name="clean-up-resources"></a>Очистка ресурсов
Другие руководства в этой серии созданы на основе этого документа. Если вы планируете продолжить работу со следующими руководствами, продолжайте использовать уровень "Стандартный" и оставьте включенной автоматическую подготовку. Если вы не планируете продолжать или хотите вернуться к уровню "Бесплатный", выполните следующие действия.

1. Вернитесь в главное меню центра безопасности и выберите пункт **Политика безопасности**.
2. Выберите подписку или политику, которую хотите вернуть на уровень "Бесплатный". Откроется окно **Политика безопасности**.
3. В разделе **Компоненты политики** выберите **Ценовая категория**.
4. Выберите уровень **Бесплатный**, чтобы изменить уровень подписки со стандартного на бесплатный.
5. Щелкните **Сохранить**.

Чтобы отключить автоматическую подготовку, выполните следующие действия:

1. Вернитесь в главное меню центра безопасности и выберите пункт **Политика безопасности**.
2. Выберите подписку, в которой нужно отключить автоматическую подготовку.
3. В колонке **Security policy – Data Collection** (Политика безопасности — сбор данных) в разделе **Подключение** выберите **Отключить**, чтобы отключить автоматическую подготовку.
4. Щелкните **Сохранить**.

>[!NOTE]
> При отключении автоматической подготовки агент Microsoft Monitoring Agent не будет удален с виртуальных машин Azure, на которых он был подготовлен. Если отключить ее, мониторинг безопасности ваших ресурсов будет ограничен.
>

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали о базовом определении политики и оценке безопасности рабочей нагрузки с помощью центра безопасности, например:

> [!div class="checklist"]
> * Настройка политики безопасности для обеспечения соответствия требованиям вашей компании или нормативным требованиям к безопасности.
> * Оценка безопасности ресурсов вычислений, сети, SQL и хранилища, а также ресурсов приложений.

Перейдите к следующему руководству, чтобы узнать, как использовать центр безопасности для защиты ваших ресурсов.

> [!div class="nextstepaction"]
> [Защита ресурсов](tutorial-protect-resources.md)
