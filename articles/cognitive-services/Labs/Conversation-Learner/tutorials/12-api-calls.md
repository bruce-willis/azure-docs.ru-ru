---
title: Использование вызовов API в модели Conversation Learner в Microsoft Cognitive Services | Документация Майкрософт
titleSuffix: Azure
description: Сведения об использовании вызовов API в модели Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 1d4013d736d8cfcb75874bc0c86d20b86ab4dd62
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215845"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Добавление вызовов API в модель Conversation Learner

В этом руководстве показано, как добавлять вызовы API в модель. Вызовы API — это функции, которые вы можете определить и сохранить в боте, чтобы приложение Conversation Learner смогло их вызывать.

## <a name="video"></a>Видео

[![Предварительная версия руководства 12](http://aka.ms/cl-tutorial-12-preview)](http://aka.ms/blis-tutorial-12)

## <a name="requirements"></a>Требования
Для работы с этим руководством требуется запущенный бот обучения tutorialAPICalls.ts.

    npm run tutorial-api-calls

## <a name="details"></a>Сведения

- Вызовы API позволяют считывать и изменять информацию в сущностях.
- Вызовы API имеют доступ к объекту диспетчера памяти.
- Вызовы API могут принимать аргументы, что позволяет применять один и тот же вызов API для разных целей.

### <a name="open-the-demo"></a>Запуск демонстрационного примера

В веб-интерфейсе в списке моделей щелкните Tutorial-12-APICalls. 

### <a name="entities"></a>Сущности

Для этой модели определена одна сущность с именем number.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>Количество вызовов API
Код для вызовов API определен в файле C:\<installedpath\>\src\demos\tutorialAPICalls.ts.

![](../media/tutorial12_apicalls.PNG)

- Первым здесь указан обратный вызов API — RandomGreeting. Он возвращает случайное приветствие, определенное в переменной greeting.
- Обратный вызов API Multiply умножает два числа, предоставленные пользователем. Он возвращает результат умножения этих двух чисел. Здесь демонстрируется передача входных данных в обратные вызовы API. Обратите внимание, что первым аргументом является диспетчер памяти. 
- Обратный вызов API ClearEntities очищает сущность number, чтобы пользователь мог ввести следующее число. Здесь демонстрируется управление сущностями в вызовах API.

### <a name="actions"></a>Действия

Мы создали четыре действия. 

![](../media/tutorial12_actions.PNG)

- Действие коммуникации "What number do you want to multiply by 12?" (Какое число вы хотите умножить на 12?) дополнено тремя разными вызовами API, которые иллюстрируют типичное применение вызовов API.

- RandomGreeting — это действие без ожидания. Чтобы настроить его, мы выбрали в диалоговом окне "Create Action" (Создать действие) тип действия API_LOCAL, а затем выбрали элемент RandomGreeting. 

![](../media/tutorial12_setupapicall.PNG)

Кнопка "Refresh" (Обновить) рядом с API полезна в тех случаях, когда нужно остановить бот и внести изменения в API-интерфейсы. Нажатие этой кнопки позволяет получить последнюю версию API.

Итак, вот как мы создали действие Multiply: выбрав тип API_Local и нужный API, мы ввели имя сущности ($number) для первого входного параметра (num1string) и числовое значение (12) для второго входного параметра (num2string). Такой подход позволяет реализовать уровень косвенных обращений между ботом и вызовами API, чтобы один обратный вызов можно было сопоставить с несколькими действиями в системе и назначать их разными способами.

![](../media/tutorial12_actionmultiply.PNG)

### <a name="train-dialog"></a>Обучение диалогу

Давайте подробнее рассмотрим процесс обучения диалогу.

1. Щелкните Train Dialogs (Обучение диалогам), а затем New Train Dialog (Обучение новому диалогу).
1. Введите строку hi (Привет).
2. Щелкните "Score Action" (Оценить действие).
3. Щелкните, чтобы выбрать RandomGreeting. Выполнится вызов API, возвращающий произвольное приветствие.
3. Щелкните действие "What number do you want to multiply by 12?" (Какое число вы хотите умножить на 12?).
4. Введите число 8. Щелкните "Score Actions" (Оценка действий).
4. Выберите "Multiply $number 12" (Умножить на число 12). Запомните результат умножения.
5. Выберите "Clear Entities" (Очистить сущности).
    - Значение `number` сущности теперь не имеет значения.
3. Щелкните действие "What number do you want to multiply by 12?" (Какое число вы хотите умножить на 12?).
4. Щелкните "Done Testing" (Завершить тестирование).

![](../media/tutorial12_dialog.PNG)

Итак, вы узнали, как зарегистрировать обратные вызовы API, увидели типичные примеры их использования, научились определять аргументы и передавать в них значения и сущности.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Карты. Часть 1](./13-cards-1.md)
