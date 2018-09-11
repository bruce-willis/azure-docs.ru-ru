---
title: Мониторинг веб-приложения ASP.NET с помощью Azure Application Insights | Документация Майкрософт
description: В этой статье предоставляются инструкции для быстрой настройки мониторинга веб-приложения ASP.NET с помощью ASP.NET.
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/13/2018
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: db8aa2d1bb5d79b5d2c9b04789b4ac18fbec5897
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43664596"
---
# <a name="start-monitoring-your-aspnet-web-application"></a>Запуск мониторинга веб-приложения ASP.NET

С помощью Azure Application Insights можно легко отслеживать доступность, производительность и использование своего веб-приложения.  Вы также можете быстро идентифицировать и диагностировать ошибки в приложении, не дожидаясь, пока пользователь сообщит о них.  На основе сведений о производительности и эффективности приложения, получаемых из Application Insights, можно сделать обдуманный выбор по поддержке и улучшению приложения.

В этом руководстве показано, как добавить Application Insights в имеющееся веб-приложение ASP.NET и начать анализировать статистику в реальном времени с помощью одного из различных методов, которые можно использовать для анализа работы приложения. Если у вас нет веб-приложения ASP.NET, его можно создать, следуя руководству [по созданию веб-приложения ASP.NET в Azure](../app-service/app-service-web-get-started-dotnet-framework.md).

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим кратким руководством сделайте следующее:

- Установите [Visual Studio 2017](https://www.visualstudio.com/downloads/) с указанными ниже рабочими нагрузками:
    - ASP.NET и веб-разработка.
    - разработка Azure;


Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="enable-application-insights"></a>Включение Application Insights

1. Откройте проект в Visual Studio 2017.
2. В меню "Проект" выберите **Настроить Application Insights**. С помощью Visual Studio пакет SDK Application Insights добавляется в приложение.

    > [!IMPORTANT]
    > Процесс добавления Application Insights зависит от типа шаблона ASP.NET. Если вы используете **пустой** шаблон или шаблон **мобильного приложения Azure**, выберите **Проект** > **Add Application Insights Telemetry** (Добавить телеметрию Application Insights). Для работы со всеми другими шаблонами ASP.NET ознакомьтесь с инструкциями на предыдущем шаге. 

3. Щелкните **Приступить к работе** (в более ранних версиях Visual Studio это кнопка **Начать бесплатно**).

    ![Добавление Application Insights в Visual Studio](./media/quick-monitor-portal/add-application-insights-b.png)

4. Выберите свою подписку и щелкните **Зарегистрироваться**.

5. Запустите приложение, выбрав в меню **Отладить** пункт **Начать отладку** или нажав клавишу F5.

## <a name="confirm-app-configuration"></a>Подтверждение настройки приложения

Application Insights собирает данные телеметрии из приложения независимо от того, где оно работает. Чтобы просмотреть эти данные, сделайте следующее.

1. Откройте Application Insights, последовательно выбрав **Вид** -> **Другие окна** -> **Поиск по Application Insights**.  Это телеметрия из текущего сеанса.<BR><br>![Данные телеметрии в Visual Studio](./media/quick-monitor-portal/telemetry-in-vs.png)

2. Щелкните первый запрос в списке (в этом примере GET Home/Index), чтобы просмотреть сведения о нем. Обратите внимание, что состояние кода и время ответа поставляются вместе с другой ценной информацией о запросе.<br><br>![Сведения об ответе в Visual Studio](media/quick-monitor-portal/request-details.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Запуск мониторинга на портале Azure

Теперь можно открыть Application Insights на портале Azure для просмотра различных сведений о запущенном приложении.

1. Щелкните правой кнопкой мыши папку **Connected Services Application Insights** (Подключенные службы Application Insights) в обозревателе решений и щелкните **Открыть портал Application Insights**.  Появятся некоторые сведения о приложении и разнообразные параметры.

    ![Схема сопоставления приложений](media/quick-monitor-portal/overview-001.png)

2. Щелкните **Схема сопоставления приложений**, чтобы получить визуальный макет отношений зависимости между компонентами приложения.  Каждый компонент показывает ключевой показатель эффективности, такие как производительность, сбои и оповещения.

    ![Схема сопоставления приложений](media/quick-monitor-portal/application-map-001.png)

3. Щелкните значок **Аналитика приложений** ![Карта приложений](media/quick-monitor-portal/app-analytics-icon.png) на одном из компонентов приложений.  Откроется окно **Application Insights Analytics** (Application Insights — аналитика), которое предоставляет полнофункциональный язык запросов для анализа всех данных, собранных Application Insights.  В этом случае создается запрос, который преобразовывает число запросов для просмотра в виде диаграммы.  Вы можете записывать собственные запросы для анализа других данных.

    ![Analytics](media/quick-monitor-portal/analytics.png)

4. Вернитесь на страницу **Обзор** и щелкните **Live Stream**.  После этого появится статистика приложения во время его работы.  Включается такая информация, как число входящих запросов, их продолжительность и все возникающие ошибки.  Вы также можете проверить важные метрики производительности, такие как процессор и память.

    ![Live Stream](media/quick-monitor-portal/live-stream.png)

Если вы готовы к размещению приложения в Azure, его можно опубликовать сейчас. Выполните действия, описанные в разделе [Обновление и повторное развертывание приложения](../app-service/app-service-web-get-started-dotnet.md#update-the-app-and-redeploy).

## <a name="next-steps"></a>Дополнительная информация
В этом кратком руководстве было показано, как включить мониторинг приложения с помощью Azure Application Insights.  Перейдите к руководствам, чтобы узнать как использовать статистику мониторинга и обнаруживать проблемы в своем приложении.

> [!div class="nextstepaction"]
> [Find and diagnose run-time exceptions with Azure Application Insights](app-insights-tutorial-runtime-exceptions.md) (Поиск и диагностика исключений времени выполнения с помощью Azure Application Insights)
