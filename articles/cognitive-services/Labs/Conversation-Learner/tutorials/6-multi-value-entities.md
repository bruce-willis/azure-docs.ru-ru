---
title: Использование многозначных сущностей в модели Conversation Learner в Microsoft Cognitive Services | Документация Майкрософт
titleSuffix: Azure
description: Сведения об использовании многозначных сущностей в модели Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 6193a515f0d8136e0d420b7554cf26fee8f50953
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173107"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Использование многозначных сущностей в модели Conversation Learner
В этом руководстве показано, как использовать многозначные свойства для сущностей.

## <a name="video"></a>Видео

[![Предварительная версия руководства 6](http://aka.ms/cl-tutorial-06-preview)](http://aka.ms/blis-tutorial-06)

##<a name="requirements"></a>Требования
Для работы с этим руководством требуется запущенный бот обучения общего назначения.

    npm run tutorial-general

## <a name="details"></a>Сведения
Многозначная сущность накапливает значения в список, а не хранит только одно значение.  Это полезно, когда пользователь может указать несколько значений для сущности, например начинки для пиццы.

Если сущность помечена как многозначная, каждый распознанный экземпляр этой сущности будет добавляться в список значений в памяти бота (вместо перезаписи единственного значения).

## <a name="steps"></a>Действия

### <a name="create-the-model"></a>Создание модели

1. В пользовательском веб-интерфейсе щелкните "Новая модель".
2. В поле имени введите MultiValueEntities. Затем щелкните "Create" (Создать).

### <a name="create-an-entity"></a>Создание сущности

1. Щелкните "Entities" (Сущности), затем — "New Entity" (Новая сущность).
2. В поле имени сущности введите Toppings (Начинки).
3. Установите флажок "Multi-valued" (Многозначная).
    - Многозначные сущности накапливают одно или несколько значений.
2. Установите флажок Negatable (Отрицаемая).  
    - Это позволит удалять начинки для пиццы из списка уже накопленных значений.
3. Щелкните Создать.

![](../media/tutorial6_entities.PNG)

### <a name="create-two-actions"></a>Создание двух действий

1. Нажмите "Actions" (Действия), затем "New Action" (Создать действие).
2. В поле "Response" (Ответ) введите строку "What toppings do you want?" (Какие начинки вы хотите добавить?)
3. В поле "Disqualifying Entities" (Блокирующие сущности) введите "Toppings" (Начинки).
3. Нажмите кнопку "Создать"

Теперь создайте второе действие.

1. Выберите "Actions" (Действия), затем "New Action" (Создать действие), чтобы создать второе действие.
3. В поле Response (Ответ) введите "Here are your toppings: $Toppings" (Вы выбрали такие начинки: $Toppings).
4. Нажмите кнопку "Создать"

Теперь у вас есть два действия.

![](../media/tutorial6_actions.PNG)

### <a name="train-the-bot"></a>Обучение бота

1. Щелкните "Train Dialogs" (Обучение диалогам), а затем — "New Train Dialog" (Обучение новому диалогу).
2. Введите "hello" (Привет).
3. Щелкните Score Actions (Оценка действий) и выберите "What toppings do you want?" (Какие начинки вы хотите добавить?).
2. Введите "mushrooms and cheese" (грибы и сыр). 
    - Вы можете пометить несколько, одну или ни одной сущности.
3. Щелкните "mushrooms" (грибы) и выберите Toppings (Начинки).
4. Щелкните "cheese" (сыр) и выберите Toppings (Начинки).
5. Щелкните "Score Actions" (Оценить действия).
    - Два значения теперь присутствуют в сущности Toppings (Начинки). 
6. Щелкните "Here are your toppings: $Toppings" (Вы выбрали такие начинки: $Toppings).

Мы можем добавить еще немного:

7. Введите "добавить перцы".
    - Щелкните "перцы" в области обнаружения сущностей и выберите Toppings (Начинки).
3. Щелкните Score Actions (Оценить действия).
    - В Toppings (Начинки) теперь появилось дополнительное значение "peppers" (перцы).
6. Щелкните "Here are your toppings: $Toppings" (Вы выбрали такие начинки: $Toppings).

Давайте удалим одну из начинок и добавим новую:

2. Введите "remove peppers and add sausage" (удалить перцы и добавить колбасу).
1. Выберите "peppers" (перцы) и щелкните красный крестик (X), чтобы удалить эту начинку.
2. Выберите "peppers" (перцы) и щелкните –Toppings (–Начинки).
3. Щелкните Score Actions (Оценить действия).
    - Элемент "peppers" (перцы) исчез из списка, но добавился элемент "sausage" (колбаса).
6. Щелкните "Here are your toppings: $Toppings" (Вы выбрали такие начинки: $Toppings).

Теперь давайте попробуйте удалить все значения:

6. Введите "remove mushrooms, remove cheese, and remove sausage" (удалить грибы, удалить сыр и удалить колбасу).
7. Щелкните каждый из трех элементов и выберите пункт –Toppings (–Начинки).
7. Щелкните Score Actions (Оценить действия).
    - Список начинок очищен.
2. Выберите "What toppings do you want?" (Какие начинки вы хотите добавить?).
3. Щелкните "Done Teaching" (Завершить обучение).

![](../media/tutorial6_dialogs.PNG)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Встроенные сущности](./7-built-in-entities.md)
