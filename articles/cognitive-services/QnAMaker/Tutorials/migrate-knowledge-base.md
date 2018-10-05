---
title: Перенос баз знаний, созданных в предварительной версии, — QnA Maker
titleSuffix: Azure Cognitive Services
description: Импорт базы знаний
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 0cb8a185407c7b180a170f1f9b9d76aa28a24de5
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031634"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Миграция базы знаний с помощью экспорта и импорта
О выпуске общедоступной версии QnA Maker было объявлено 7 мая 2018 г. на конференции \\\build\. Общедоступная версия QnA Maker имеет новую архитектуру, основанную на Azure. Базы знаний, созданные с помощью бесплатной предварительной версии QnA Maker, будет необходимо перенести в общедоступную версию QnA Maker. Предварительная версия QnA Maker будет признана устаревшей в ноябре 2018 г. Дополнительные сведения об изменениях в общедоступной версии QnA Maker см. в объявлении о выпуске общедоступной версии QnA Maker в этой [публикации блога](https://aka.ms/qnamakerga-blog).

Теперь QnA Maker присвоена [модель ценообразования](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/qna-maker/).

Предварительные требования
> [!div class="checklist"]
> * Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.
> * Настройте новую [службу QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker-preview-portal"></a>Перенос базы знаний из портала предварительной версии QnA Maker
1. Перейдите на [портал предварительной версии QnA Maker](https://aka.ms/qnamaker-old-portal
) и щелкните **My services** (Мои службы).
2. Выберите базу знаний, которую требуется перенести, щелкнув значок редактирования.

    ![Редактирование базы знаний](../media/qnamaker-how-to-migrate-kb/preview-editkb.png)

3. Щелкните **Download knowledge base** (Скачать базу знаний), чтобы скачать TSV-файл с содержимым базы знаний: вопросами, ответами, метаданными и именами источников данных, из которых они были извлечены.

    ![Скачайте базу знаний](../media/qnamaker-how-to-migrate-kb/preview-download.png)

4. Войдите на [портал QnA Maker](https://qnamaker.ai) с помощью учетных данных Azure и щелкните **Create new service** (Создать службу).

    ![Создание базы знаний ](../media/qnamaker-how-to-create-kb/create-new-service.png)
    
5. Если служба QnA Maker еще не создана, выберите **Create a QnA service** (Создать службу QnA). В противном случае выберите службу QnA Maker из раскрывающихся списков на шаге 2. Выберите службу QnA Maker, в которой будет размещаться база знаний.

    ![Установка службы QnA](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

6. Создайте пустую базу знаний. 

    ![Настройка источников данных](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Введите **имя** для службы. Поддерживаются повторяющиеся имена, а также специальные знаки в имени.
    - Пропустите отправку файлов или URL-адресов, так как вам нужно использовать данные из собственной базы знаний предварительной версии. На этом этапе вы создадите пустую базу знаний.

7. Нажмите кнопку **Создать**.

    ![Создание базы знаний](../media/qnamaker-how-to-create-kb/create-kb.png)

8. В этой новой базе знаний откройте вкладку **Параметры** и выберите **Импорт базы знаний**. При этом будут импортированы вопросы, ответы и метаданные, а также сохранены имена источников данных, из которых они были извлечены.

   ![Импорт базы знаний](../media/qnamaker-how-to-migrate-kb/Import.png)

9. **Проверьте** новую базу знаний с помощью панели "Тестирование". Узнайте, как [проверить базу знаний](../How-To/test-knowledge-base.md).
10. **Опубликуйте** базу знаний. Узнайте, как [опубликовать базу знаний](../How-To/publish-knowledge-base.md).
11. Используйте приведенную ниже конечную точку в коде приложения или бота. Сведения о [создании бота QnA](../Tutorials/create-qna-bot.md).

    ![Значения QnA Maker](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

На этом этапе все содержимое базы знаний, вопросы, ответы и метаданные, а также имена исходных файлов и URL-адреса будут импортированы в новую базу знаний. 

## <a name="chatlogs-and-alterations"></a>Журналы чатов и преобразования
Преобразования (синонимы) не импортируются автоматически. Используйте [API-интерфейсы V2](https://aka.ms/qnamaker-v2-apis) для экспорта изменений из стека предварительной версии и [API-интерфейсы V4](https://aka.ms/qnamaker-v4-apis) для замены данных в новом стеке.

Возможность миграции журналов чатов (chatlog) не предусмотрена, так как новый стек использует для хранения журналов чатов Application Insights. Тем не менее можно скачать журналы чатов с [портала предварительной версии](https://aka.ms/qnamaker-old-portal).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Редактирование базы знаний](../How-To/edit-knowledge-base.md)
