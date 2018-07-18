---
title: Атрибуты экземпляра сущности конференции в Academic Knowledge API | Документация Майкрософт
description: Изучите атрибуты, которые вы можете использовать с экземпляром сущности конференции в Academic Knowledge API в Cognitive Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: ef2bca4346a4666905f3dfb7bd448720f3b0ef8b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380021"
---
# <a name="conference-instance-entity"></a>Экземпляр сущности конференции

<sub> *Следующие атрибуты относятся к экземпляру сущности конференции. (Ty = '4') </sub>

ИМЯ    |ОПИСАНИЕ                            |type       | Операции
------- | ------------------------------------- | --------- | ----------------------------
Идентификатор      |Идентификатор сущности                              |Int64      |Равно
CIN     |Нормализованное имя экземпляра конференции ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |Строка     |Равно
DCN     |Отображаемое имя экземпляра конференции ({ConferenceSeriesName} : {ConferenceInstanceYear})       |Строка     |Нет
CIL     |Расположение экземпляра конференции    |Строка     |Равно,<br/>StartsWith;
CISD    |Дата начала экземпляра конференции  |Дата       |Равно,<br/>IsBetween
CIED    |Дата окончания экземпляра конференции    |Дата       |Равно,<br/>IsBetween
CIARD   |Дата выполнения абстрактной регистрации экземпляра конференции  |Дата       |Равно,<br/>IsBetween
CISDD   |Дата выполнения отправки экземпляра конференции     |Дата       |Равно,<br/>IsBetween
CIFVD   |Дата выполнения окончательной версии экземпляра конференции  |Дата       |Равно,<br/>IsBetween
CINDD   |Дата уведомления экземпляра конференции   |Дата       |Равно,<br/>IsBetween
CD.T    |Заголовок события экземпляра конференции   |Дата       |Равно,<br/>IsBetween
CD.D    |Дата события экземпляра конференции    |Дата       |Равно,<br/>IsBetween
PCS.CN  |Имя ряда конференции экземпляра |Строка     |Равно
PCS.CId |Идентификатор ряда конференции экземпляра |Int64    |Равно
CC      |Общее количество цитируемых экземпляров конференции           |Int32      |Нет  
ECC     |Общее количество подсчитанного цитирования экземпляров конференции |Int32      |Нет


## <a name="extended-metadata-attributes"></a>Расширенные атрибуты метаданных ##

ИМЯ    | ОПИСАНИЕ               
--------|---------------------------    
FN      | Полное имя экземпляра конференции