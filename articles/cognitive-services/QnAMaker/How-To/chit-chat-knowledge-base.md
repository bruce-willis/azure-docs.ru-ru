---
title: Добавление беседы в базу знаний QnA Maker
titleSuffix: Azure Cognitive Services
description: Добавление пользовательских бесед к боту при создании базы знаний делает его более общительным и приятным. QnA Maker позволяет легко добавлять заранее заданный набор самых интересных бесед в базу знаний. Это может стать основой для бесед бота и сэкономить время и затраты на их написание с нуля.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/24/2018
ms.author: tulasim
ms.openlocfilehash: 6f542d095211242fcf6dcb13d4426534e32e57ab
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041733"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Добавление беседы в базу знаний

Добавление пользовательских бесед к боту делает его более общительным и приятным. Функция беседы в QnA Maker позволяет легко добавлять заранее заданный набор самых интересных бесед в базу знаний. Это может стать основой личности бота и сэкономить время и затраты на ее написание с нуля.  

Этот набор данных содержит около 100 сценариев бесед в трех тонах: профессионала, друга и комика. Выберите персонажа, тон которого больше всего напоминает тон бота. QnA Maker получает пользовательский запрос и пытается сопоставить его с наиболее близкой по теме беседой в разделе "Вопросы и ответы". 

Некоторые примеры разных персонажей: <!-- added quotes so acrolinx doesn't score these sentences -->.
|Пользовательский запрос|Профессионал|Друг|Комик|
|--|--|--|--|
|`You are awesome`|`I aim to serve.`|`That's so nice of you!`|`Flattery. I like it.`|
|`Are you hungry?`|`I don't need to eat.`|`I only do food for thought.`|`Eating would require a lot of things I don't have. Like a digestive system. And silverware.`|
|`Sing a song`|`I'm afraid I'm not musically inclined.`|`La la la, tra la la. I'm awesome at this.`|`You can't handle my dulcet tones.`|
|`Who made you?`|`There wouldn't be time to list everyone.`|`So many people!`|`Nerds.`|


> [!NOTE]
> На данный момент беседа доступна только на английском языке. 

## <a name="add-chit-chat-during-kb-creation"></a>Добавление беседы во время создания базы знаний
При создании базы знаний после добавления исходных URL-адресов и файлов есть возможность добавить и беседу. Выберите персонажа для своей беседы. Если вы не хотите добавлять беседу или если в источниках данных уже есть поддержка беседы, выберите **None** (Не требуется). 
   
![Добавление беседы во время создания](../media/qnamaker-how-to-chit-chat/create-kb-chit-chat.png)

## <a name="add-chit-chat-to-an-existing-kb"></a>Добавление беседы в существующую базу знаний
Выберите базу знаний и перейдите на страницу **Параметры**. Здесь есть ссылка на все наборы данных бесед в соответствующем формате **TSV**. Загрузите желаемую личность, а затем отправьте ее как источник файла. Не изменяйте формат или метаданные при загрузке и отправке файла. 
  
![Добавление беседы в существующую базу знаний](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Изменение вопросов и ответов беседы
Во время изменения базы знаний вы увидите новый источник для беседы, зависящий от выбранного персонажа. Теперь вы можете добавлять измененные вопросы или редактировать ответы как и в любом другом источнике. 

![Изменение вопросов и ответов беседы](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

## <a name="add-additional-chit-chat-questions-and-answers"></a>Добавление в беседу дополнительных вопросов и ответов
Вы можете добавлять в беседу вопросы и ответы, которых нет в стандартном наборе. Убедитесь, что вы не дублируете пару вопрос-ответ, которая уже включена в набор. При добавлении любая новая пара вопрос-ответ также будет добавлена в **Editorial** (редакционный) источник. Чтобы убедиться в том, что компонент ранжировки будет понимать, что это беседа, добавьте ключ метаданных или пару значений "Editorial: chit-chat", как показано на следующем изображении.
   
![Добавление вопросов и ответов беседы](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Удаление беседы из существующей базы знаний
Выберите базу знаний и перейдите на страницу **Параметры**. Конкретный источник беседы указан как файл с именем выбранного персонажа. Вы можете удалить его как исходный файл.

![Удаление беседы из базы знаний](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Импорт базы знаний](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>См. также 

[Общие сведения о QnA Maker](../Overview/overview.md)
