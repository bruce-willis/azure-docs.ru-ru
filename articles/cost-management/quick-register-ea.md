---
title: Регистрация соглашения Enterprise Azure в Cloudyn | Документы Майкрософт
description: Используйте соглашение Enterprise для регистрации в службе Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/14/2018
ms.topic: quickstart
ms.custom: ''
ms.service: cost-management
manager: dougeby
ms.openlocfilehash: bed1ea3785c35d1053a0ff2147c3bdd797e28581
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996644"
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>Регистрация соглашения Azure Enterprise и просмотр данных о затратах

Используйте соглашение Enterprise Azure для регистрации в службе Cloudyn. Такая регистрация предоставляет доступ к порталу Cloudyn. В этом кратком руководстве описывается процесс регистрации, который необходимо пройти для создания пробной подписки Cloudyn и входа на портал Cloudyn. Здесь также показано, как начать просматривать данные о затратах.

Служба "Управление затратами Azure" предоставляет аналогичные возможности в Cloudyn. Служба "Управление затратами Azure" является собственным решением Azure по управлению затратами. Она позволяет анализировать затраты, создавать бюджеты и управлять ими, экспортировать данные, а также просматривать рекомендации и реагировать на них с целью экономии средств. Дополнительные сведения см. в статье [Управление затратами Azure](overview-cost-mgt.md).

## <a name="sign-in-to-azure"></a>Вход в Azure

- Войдите на портал Azure по адресу http://portal.azure.com.

## <a name="register-with-cloudyn"></a>Регистрация в Cloudyn

1. На портале Azure в списке служб выберите **Cost Management + Billing** (Управление затратами + Выставление счетов).
2. В разделе **Обзор** щелкните **Cloudyn**.  
    ![Страница Cloudyn](./media/quick-register-ea/cost-mgt-billing-service.png)
3. На странице **Cloudyn** нажмите кнопку **Go to Cloudyn** (Перейти к Cloudyn), чтобы открыть страницу регистрации Cloudyn в новом окне.
4. На странице пробной регистрации на портале Cloudyn введите название вашей компании, а затем выберите **Azure Enterprise Enrollment Administrator** (Администратор регистрации Azure Enterprise).  
    ![пробная регистрация](./media/quick-register-ea/trial-reg.png)
5. Введите свой ключ API для регистрации на портале Enterprise Portal. Если у вас нет под рукой ключа, то щелкните ссылку [Enterprise Portal](https://ea.azure.com) и выполните описанные ниже действия.
  1. Войдите на веб-сайт Azure Enterprise и щелкните **Отчеты**. Затем выберите **API Access Key** (Ключ доступа к API) и скопируйте первичный ключ.  
    ![Ключ API EA](./media/quick-register-ea/ea-key.png)
  3. Вернитесь на страницу регистрации и вставьте эти ключ API.
6. Примите условия использования, а затем проверьте свой ключ. Нажмите кнопку **Next** (Далее), чтобы авторизовать Cloudyn для сбора данных ресурсов Azure. Собранные данные включают в себя сведения об использовании, производительности, выставлении счетов, а также данные тегов из подписок.  
    ![проверка ключа](./media/quick-register-ea/ea-key-validated.png)
7. В разделе **Invite other stakeholders** (Пригласить других заинтересованных лиц) можно добавить пользователей, введя их адреса электронной почты. По завершении нажмите кнопку **Next** (Далее). На добавление платежных данных в Cloudyn в зависимости от размера регистрации в Azure может потребоваться до 24 часов.
8. Щелкните **Go to Cloudyn** (Перейти к Cloudyn), чтобы открыть портал Cloudyn. Затем на странице **Cloud Accounts Management** (Управление облачными учетными записями) должны отобразиться сведения вашей зарегистрированной учетной записи EA.

Руководство по регистрации соглашения Enterprise см. в видео [How to Find Your EA Enrollment ID and API Key for use in Cloudyn](https://youtu.be/u_phLs_udig) (Как найти свой идентификатор регистрации EA и ключ API для использования в службе Cloudyn).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы использовали данные соглашения Azure Enterprise для регистрации в Cloudyn. Вы также вошли на портал Cloudyn и начали просматривать данные о затратах. Дополнительные сведения о Cloudyn см. в руководстве для Cloudyn.

> [!div class="nextstepaction"]
> [Просмотр сведений об использовании и затратах](./tutorial-review-usage.md)
