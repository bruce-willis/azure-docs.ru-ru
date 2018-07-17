---
title: Преобразование и защита API с помощью службы управления API Azure | Документация Майкрософт
description: Узнайте, как защитить API с помощью политик квот и регулирования (ограничения скорости).
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: b94f6ad4c7c6f3b5e93cdb890e053a3d1678e161
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38722955"
---
# <a name="transform-and-protect-your-api"></a>Преобразование и защита API 

В этом руководстве показано, как преобразовать API, чтобы он не раскрывал сведения о серверной части. Например, иногда нужно скрыть сведения о технологических решениях, которые применяются в серверной части. Или же нужно скрыть исходный URL-адрес, присутствующий в тексте HTTP-ответа от API, указав вместо него шлюз службы управления API.

Также в этом руководстве показано, насколько легко можно защитить серверный API, настроив ограничения скорости в службе управления API Azure. Например, вы можете ограничить количество вызовов к API, чтобы разработчики не перегружали его. Дополнительные сведения см. в статье [Обработка ошибок в политиках управления API](api-management-policies.md).

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * преобразование API для удаления заголовков ответа;
> * замена исходных URL-адресов в тексте ответа API URL-адресом шлюза службы управления API;
> * защита API путем добавления политик ограничения скорости (регулирования);
> * проверка преобразований.

![Политики](./media/transform-api/api-management-management-console.png)

## <a name="prerequisites"></a>предварительным требованиям

+ Выполните задачи в кратком руководстве по [созданию экземпляра службы управления API Azure](get-started-create-service-instance.md).
+ Кроме того, выполните задачи из руководства по [импорту и публикации первого API](import-and-publish.md).
 
[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Преобразование API для удаления заголовков ответа

В этом разделе показано, как скрыть заголовки HTTP, которые вы не хотите показывать пользователям. В этом примере из HTTP-ответов удаляются следующие заголовки:

* **X-Powered-By**,
* **X-AspNet-Version**.

### <a name="test-the-original-response"></a>Проверка исходного ответа

Чтобы увидеть исходный ответ, сделайте следующее.

1. В экземпляре службы APIM выберите **API** (в разделе **Управление API**).
2. В списке API выберите **Demo Conference API**.
3. Щелкните операцию **GetSpeakers**.
4. Щелкните вкладку **Тест** в верхней части экрана.
5. В нижней части страницы нажмите кнопку **Отправить**. 

    Здесь видно, что исходный ответ выглядит следующим образом:

    ![Политики](./media/transform-api/original-response.png)

### <a name="set-the-transformation-policy"></a>Настройка политики преобразования

1. Выберите **Demo Conference API**.
2. В верхней части экрана выберите вкладку **Конструктор**.
3. Выберите **Все операции**.
4. В окне **Обработка исходящих запросов** щелкните треугольник (рядом со значком карандаша) и выберите **Редактор кода**.
     ![Изменение политики](./media/set-edit-policies/set-edit-policies01.png)
5. Поместите курсор на **&lt;исходящий&gt;** элемент.
6. На правой панели в разделе **Политики преобразования** дважды щелкните **+Установка HTTP-заголовка**, чтобы вставить два фрагмента политики.

    ![Политики](./media/transform-api/transform-api.png)
7. Измените код элемента **<outbound>**, чтобы он выглядел так:

        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />

    ![Политики](./media/transform-api/set-policy.png)
8. Нажмите кнопку **Сохранить** .


## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>замена исходных URL-адресов в тексте ответа API URL-адресом шлюза службы управления API;

В этом разделе показано, как скрыть исходный URL-адрес, присутствующий в тексте HTTP-ответа от API, указав вместо него шлюз службы управления API.

### <a name="test-the-original-response"></a>Проверка исходного ответа

Чтобы увидеть исходный ответ, сделайте следующее.

1. Выберите **Demo Conference API**.
2. Щелкните операцию **GetSpeakers**.
3. Щелкните вкладку **Тест** в верхней части экрана.
4. В нижней части страницы нажмите кнопку **Отправить**. 

    Здесь видно, что исходный ответ выглядит следующим образом:

    ![Политики](./media/transform-api/original-response2.png)

### <a name="set-the-transformation-policy"></a>Настройка политики преобразования

1. Выберите **Demo Conference API**.
2. Выберите **Все операции**.
3. В верхней части экрана выберите вкладку **Конструктор**.
4. В окне **Обработка исходящих запросов** щелкните треугольник (рядом со значком карандаша) и выберите **Редактор кода**.
5. Поместите курсор на **&lt;исходящий&gt;** элемент.
6. На правой панели в разделе **Политики преобразования** щелкните **+Поиск и замена строки в тексте**.
7. Измените код **find-and-replace** (в элементе **\<outbound\>**), чтобы заменить URL-адрес адресом шлюза службы управления API. Например: 

        <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>защита API путем добавления политик ограничения скорости (регулирования);

В этом разделе показано, как добавить защиту для API внутреннего сервера, настроив для него ограничения скорости. Например, вы можете ограничить количество вызовов к API, чтобы разработчики не перегружали его. В этом примере установлено ограничение в 3 вызова за 15 секунд для каждого идентификатора подписки. Через 15 секунд разработчик может снова повторять вызов API.

1. Выберите **Demo Conference API**.
2. Выберите **Все операции**.
3. В верхней части экрана выберите вкладку **Конструктор**.
4. В окне **Обработка входящих запросов** щелкните треугольник (рядом со значком карандаша) и выберите **Редактор кода**.
5. Поместите курсор на **&lt;входящий&gt;** элемент.
6. На правой панели в разделе **Политики ограничения доступа** щелкните **Ограничить частоту вызовов для одного ключа**.
7. Замените код **rate-limit-by-key** (в элементе **\<inbound\>**) следующим фрагментом:

        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />

## <a name="test-the-transformations"></a>проверка преобразований.
        
Если на этом этапе взглянуть на код в редакторе, ваши политики будут выглядеть так:

    <policies>
        <inbound>
            <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
            <base />
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <set-header name="X-Powered-By" exists-action="delete" />
            <set-header name="X-AspNet-Version" exists-action="delete" />
            <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>

Оставшаяся часть этого раздела посвящена проверке преобразований политики, которые вы задали ранее.

### <a name="test-the-stripped-response-headers"></a>Проверка очистки заголовков в ответе

1. Выберите **Demo Conference API**.
2. Щелкните операцию **GetSpeakers**.
3. Откройте вкладку **Тест**.
4. Нажмите кнопку **Отправить**.

    Здесь вы увидите, что заголовки удалены из ответа:

    ![Политики](./media/transform-api/final-response1.png)

### <a name="test-the-replaced-url"></a>Проверка замены URL-адреса

1. Выберите **Demo Conference API**.
2. Щелкните операцию **GetSpeakers**.
3. Откройте вкладку **Тест**.
4. Нажмите кнопку **Отправить**.

    Здесь видно, что URL-адрес успешно заменен.

    ![Политики](./media/transform-api/final-response2.png)

### <a name="test-the-rate-limit-throttling"></a>Проверка предела скорости (регулирования)

1. Выберите **Demo Conference API**.
2. Щелкните операцию **GetSpeakers**.
3. Откройте вкладку **Тест**.
4. Щелкните **Отправить** три раза подряд.

    После третьего запроса вы получите ответ **429 — слишком много запросов**.
5. Подождите примерно 15 секунд и снова щелкните **Отправить**. Теперь вы должны получить ответ **200 — ОК**.

    ![Регулирование](./media/transform-api/test-throttling.png)

## <a name="video"></a>Видео

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * преобразование API для удаления заголовков ответа;
> * замена исходных URL-адресов в тексте ответа API URL-адресом шлюза службы управления API;
> * защита API путем добавления политик ограничения скорости (регулирования);
> * проверка преобразований.

Перейдите к следующему руководству:

> [!div class="nextstepaction"]
> [Monitor your API](api-management-howto-use-azure-monitor.md) (Мониторинг API)
