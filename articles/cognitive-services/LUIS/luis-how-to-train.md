---
title: Обучение приложения LUIS в Azure | Документация Майкрософт
description: Использование Интеллектуальной службы распознавания речи (LUIS) для обучения модели.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: diberry
ms.openlocfilehash: e947df20141b0b9870f318f410488aea23bafcf5
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223190"
---
# <a name="train-your-luis-app"></a>Обучение приложения LUIS

Обучение приложения Интеллектуальной службы распознавания речи (LUIS) позволяет улучшить распознавание естественного языка. Следует обучать приложение LUIS после обновления модели, например добавления, изменения, пометки или удаления сущностей, намерений или фраз. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

Обучение и [тестирование](luis-concept-test.md) приложения — это итеративный процесс. Обученное приложение LUIS следует протестировать с использованием примеров фраз, чтобы проверить правильность распознавания намерений и сущностей. Если распознавание выполняется неправильно, внесите изменения в приложение, а затем обучите его и протестируйте еще раз. 

## <a name="train-your-app"></a>Обучение приложения
Чтобы начать итеративный процесс, необходимо сначала хотя бы один раз обучить приложение LUIS. Перед обучением убедитесь, что для каждого намерения имеется минимум одно высказывание.

1. Откройте приложение, выбрав его имя на странице **Мои приложения**. 

2. В приложении выберите **Обучить** на панели сверху. 

    ![Кнопка обучения](./media/luis-how-to-train/train-button.png)

3. После завершения обучения в верхней части браузера появится зеленая строка уведомлений.

    ![Страница обучения и тестирования приложения](./media/luis-how-to-train/train-success.png)

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Если в приложении есть одно или несколько намерений, которые не содержат примеров фраз, такое приложение обучить нельзя. Добавьте фразы для всех намерений. Дополнительные сведения см. в статье о [добавлении примеров фраз](luis-how-to-add-example-utterances.md).

## <a name="next-steps"></a>Дополнительная информация

* [Label suggested utterances with LUIS](luis-how-to-review-endoint-utt.md) (Добавление меток к предлагаемым фразам в LUIS) 
* [Use features to improve your LUIS app's performance](luis-how-to-add-features.md) (Использование функций для повышения производительности приложения LUIS) 