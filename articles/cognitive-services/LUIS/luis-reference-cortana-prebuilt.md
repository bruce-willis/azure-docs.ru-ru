---
title: Справочник по предварительно созданным приложениям Cortana | Документация Майкрософт
description: Справочник по личному помощнику Cortana — предварительно созданному приложению из комплекта LUIS (Language Understanding Intelligent Service).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: cd808c30a6781fc0252992c13ba247486e35ad44
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380373"
---
# <a name="cortana-prebuilt-app-reference"></a>Справочник по предварительно созданным приложениям Cortana
Эта справка — список намерений и сущностей, которые распознает предварительно созданное приложение Cortana.

## <a name="cortana-prebuilt-intents"></a>Предварительно созданные намерения
Предварительно созданное приложение персонального помощника Cortana может определить следующие намерения.

| Намерение | Примеры высказываний |
|--------| ------------------|
| builtin.intent.alarm.alarm_other|Обновить мой сигнал с 7:30 на 8:00 <br/>Перенеси мой сигнал с 8:00 на 9:00 |
| builtin.intent.alarm.delete_alarm|Удалить сигнал <br/>Удалить сигнал будильника|
| builtin.intent.alarm.find_alarm|На который час настроен мой будильник? <br/> Мой будильник включен? |
| builtin.intent.alarm.set_alarm|Включить мой будильник<br/>Можешь установить сигнал на 12:00 с заметкой принять антибиотики?|
| builtin.intent.alarm.snooze|Отложи сигнал на 5 минут<br/>Отложи сигнал|
| builtin.intent.alarm.time_remaining| Сколько еще времени до звонка будильника? <br/> Сколько у меня времени до следующего сигнала?|
| builtin.intent.alarm.turn_off_alarm | Отключи сигнал на 07:00 <br/> Отключи будильник на 07:00 |
| builtin.intent.calendar.change_calendar_entry| Перенеси встречу<br/>Перенеси встречу с сегодня на завтра|
|builtin.intent.calendar.check_availability|Занят ли Тим в пятницу?<br/>Свободен ли Браян в субботу?|
|builtin.intent.calendar.connect_to_meeting|Подключение к собранию<br/>Присоединиться к собранию в сети|
|builtin.intent.calendar.create_calendar_entry|Необходимо запланировать встречу с Тони на пятницу<br/>Запланировать встречу с Лизой на 14:00 в воскресенье|
|builtin.intent.calendar.delete_calendar_entry|Удалить мою встречу<br/>Удалить из моего календаря собрание на 15:00 завтра|
|builtin.intent.calendar.find_calendar_entry|Покажи мой календарь<br/>Покажи мой календарь на неделю|
|builtin.intent.calendar.find_calendar_when|Когда я в следующий раз встречаюсь с Джоном?<br/>В котором часу я встречаюсь с Ларри в понедельник?|
|builtin.intent.calendar.find_calendar_where|Покажи расположение моего собрания в 6 часов<br/>Где я встречаюсь с Джоном?|
|builtin.intent.calendar.find_calendar_who|С кем должна пройти эта встреча?<br/>Кто еще приглашен?|
|builtin.intent.calendar.find_calendar_why|Есть подробности о моей встрече в 11:00?<br/>Что это за встреча с Джоном?|
|builtin.intent.calendar.find_duration|Сколько будет длиться моя следующая встреча?<br/>Сколько будет длиться собрание сегодня после обеда?|
|builtin.intent.calendar.time_remaining|Сколько у меня времени до следующей встречи?<br/>Сколько у меня еще остается времени до следующей встречи?|
|builtin.intent.communication.add_contact|Сохрани этот номер и подпиши именем Хосе<br/>Помести Джейсона в мой список контактов|
|builtin.intent.communication.answer_phone|Ответ<br/>Ответь на звонок|
|builtin.intent.communication.assign_nickname|Измени псевдоним для Николаса<br/>Добавь псевдоним для Джона Доу|
|builtin.intent.communication.call_voice_mail|Голосовая почта<br/>Набери номер голосовой почты|
|builtin.intent.communication.find_contact|Покажи мой список контактов<br/>Найди контакты|
|builtin.intent.communication.forwarding_off|Останови переадресацию вызовов<br/>Отключи переадресацию вызовов|
|builtin.intent.communication.forwarding_on|Настрой переадресацию вызовов для отправки вызовов на домашний телефон<br/>Переадресуй вызовы на домашний телефон|
|builtin.intent.communication.ignore_incoming|Не отвечай на этот вызов<br/>Не сейчас, я занят|
|builtin.intent.communication.ignore_with_message|Не отвечай на этот вызов, но отправь сообщение<br/>Пропусти и отправь текст обратно|
|builtin.intent.communication.make_call|Позвони Бобу и Джону<br/>Позвони маме и папе|
|builtin.intent.communication.press_key|Нажми на звездочку<br/>нажми 1 2 3|
|builtin.intent.communication.read_aloud|Читай текст<br/>Что она сказала в сообщении?|
|builtin.intent.communication.redial|Повтори вызов последнего номера телефона<br/>Повтори вызов|
|builtin.intent.communication.send_email|Отправь сообщение Майку Уотерсу: "Майк, ужин на прошлой неделе был великолепным!"<br/>Отправь сообщение Бобу|
|builtin.intent.communication.send_text|Отправь текст Бобу и Джону<br/>Message|
|builtin.intent.communication.speakerphone_off|Выключи громкую связь<br/>Отключи громкую связь|
|builtin.intent.communication.speakerphone_on|Режим громкой связи<br/>Включи громкую связь|
|builtin.intent.mystuff.find_attachment|Найди документ, который Джон отправил мне вчера на почту <br/>Найди документ от Джона|
|builtin.intent.mystuff.find_my_stuff|Измени мой вчерашний список покупок<br/>Найди мои заметки по химии от сентября
|builtin.intent.mystuff.search_messages|Открой сообщение <br/>отправляемых из облака на устройство|
|builtin.intent.mystuff.transform_my_stuff|Поделись списком покупок с моим мужем<br/>Удали список покупок|
|builtin.intent.ondevice.open_setting|Открой параметры cortana <br/>Перейди к уведомлениям|
|builtin.intent.ondevice.pause|Выключи музыку<br/>Отключи музыку|
|builtin.intent.ondevice.play_music|Я хочу услышать песню "owner of a lonely heart"<br/>Воспроизведи религиозную музыку|
|builtin.intent.ondevice.recognize_song|Что это за песня?<br/>Проанализируй и получи название песни|
|builtin.intent.ondevice.repeat|Повтори этот трек<br/>Воспроизведи песню еще раз|
|builtin.intent.ondevice.resume|Перезапусти музыку<br/>Продолжай воспроизведение|
|builtin.intent.ondevice.skip_back|Создай резервную копию трека<br/>Предыдущая песня|
|builtin.intent.ondevice.skip_forward|Следующая песня<br/>Пропусти трек|
|builtin.intent.ondevice.turn_off_setting|Выключи Wi-Fi<br/>Отключи режим "в самолете"|
|builtin.intent.ondevice.turn_on_setting|Включи Wi-Fi<br/>Включи Bluetooth|
|builtin.intent.places.add_favorite_place|Добавь этот адрес в избранное<br/>Сохрани это местоположение в избранном|
|builtin.intent.places.book_public_transportation|Купи билет на поезд<br/>Закажи проездной на трамвай|
|builtin.intent.places.book_taxi|Можешь найти мне поездку на это время?<br/>Найди такси|
|builtin.intent.places.check_area_traffic|Какая загрузка дороги 520?<br/>Покажи дорожное движение на і-5|
|builtin.intent.places.check_into_place|Зарегистрируй меня в Джоя<br/>Зарегистрируй меня здесь|
|builtin.intent.places.check_route_traffic|Покажи дорожное движение на Кирклэнд<br/>Покажи дорожное движение на Сиэтл|
|builtin.intent.places.find_place|Где я живу? <br/>Покажи мне 3 лучшие японские рестораны|
|builtin.intent.places.get_address|Покажи мне адрес ресторана на улице Робсон<br/>Какой адрес ближайшей кофейни?|
|builtin.intent.places.get_coupon|Найди передачи по телевидению в моем районе<br/>Скидки в Маунтин Вью|
|builtin.intent.places.get_distance|Какое расстояние до отеля Holiday Inn?<br/>Какое расстояние до Тахо?|
|builtin.intent.places.get_hours|Как работает этот бар по понедельникам?<br/>Когда открывается библиотека?|
|builtin.intent.places.get_menu|Покажи меню ресторана Applebee<br/>Что в меню ресторана Sizzler?|
|builtin.intent.places.get_phone_number|Покажи номер хозяйственного магазина<br/>Какой номер телефона ближайшей кофейни?|
|builtin.intent.places.get_price_range|Сколько стоит ужин в этом ресторане?<br/>На сколько дорого в этом кафе?|
|builtin.intent.places.get_reviews|Покажи отзывы о местных магазинах стройматериалов<br/>Я хочу увидеть отзывы о ресторане|
|builtin.intent.places.get_route|Покажи мне направление к|Можно ли дойти до пиццерии пешком?|
|builtin.intent.places.get_star_rating|Сколько звезд у французской прачечной?<br/>Аквариум в Монтеррее хорош?|
|builtin.intent.places.get_transportation_schedule|Когда отчаливает паром в Сан-Франциско?<br/>Когда отправляется последний поезд в Сиэтл?|
|builtin.intent.places.get_travel_time|Сколько времени ехать в Денвер из Сан-Франциско?<br/>Сколько времени потребуется, чтобы добраться до Сан-Франциско отсюда?|
|builtin.intent.places.make_call|Позвони доктору Смиту в Белвью<br/>Позвони в магазин строительных товаров на 1-ой улице|
|builtin.intent.places.rate_place|Покажи рейтинг этого ресторана<br/>Оцени ресторан на 5 звезд|
|builtin.intent.places.show_map|Какое мое текущее местоположение? <br/>Какое мое местоположение?|
|builtin.intent.places.takes_reservations|Можно ли забронировать посещение Olive Garden?<br/>Можно ли забронировать посещение художественной галереи?|
|builtin.intent.reminder.change_reminder|Изменение напоминания<br/>Перенеси сегодняшнее дневное напоминание на 30 минут|
|builtin.intent.reminder.create_single_reminder|Напомни мне проснуться в 07:00<br/>Напомни о праздновании Хеллоуина с Лукой в 16:40|
|builtin.intent.reminder.delete_reminder|Удали это напоминание<br/>Удали картинку напоминания|
|builtin.intent.reminder.find_reminder|Есть ли у меня какие-то напоминания на сегодня?<br/>Есть ли у меня какое-то напоминание о вечеринке Луки|
|builtin.intent.reminder.read_aloud|Прочти напоминание вслух<br/>Прочти напоминание|
|builtin.intent.reminder.snooze|Отложи напоминание до завтра<br/>Отложи это напоминание|
|builtin.intent.reminder.turn_off_reminder|Отключи напоминание<br/>Отклони напоминание о поездке в аэропорт|
|builtin.intent.weather.change_temperature_unit|Измени с градусов Фаренгейта в градусы Кельвина<br/>Измени с градусов Фаренгейта в градусы Цельсия|
|builtin.intent.weather.check_weather|Покажи прогноз погоды для моей предстоящей поездки в Сиэтл<br/>Какая будет погода в эти выходные?|
|builtin.intent.weather.check_weather_facts|Какая погода на Гавайях в декабре?<br/>Какая температура была в это время в прошлом году?|
|builtin.intent.weather.compare_weather|Сравни высокие и низкие температуры в Далласе и Хьюстоне, штат Техас<br/>Сравни погоду в Солт-Лейк-Сити и Нью-Йорке|
|builtin.intent.weather.get_frequent_locations|Покажи мое наиболее частое местоположение<br/>Покажи наиболее частые остановки|
|builtin.intent.weather.get_weather_advisory|Предупреждения о погоде<br/>Покажи метеосводку в Сакраменто|
|builtin.intent.weather.get_weather_maps|Отобрази синоптическую карту<br/>Покажи синоптическую карту Африки|
|builtin.intent.weather.question_weather|Будет ли завтра с утра туманно?<br/>Нужно ли мне будет убирать снег на этих выходных?|
|builtin.intent.weather.show_weather_progression|Покажи местный метеолокатор<br/>Запусти метеолокатор|
|builtin.intent.none|Сколько вам лет?<br/>Открой камеру|

## <a name="prebuilt-entities"></a>Предварительно созданные сущности 

Ниже приведены некоторые примеры сущностей, которые можно определить предварительно созданным приложением персонального помощника.

|Сущность |Пример сущности в высказывании |
|-------|------------------|
|builtin.alarm.alarm_state | Включи `off` мой будильник    <br/> Мой будильник `on`  | 
|builtin.alarm.duration |Отложи на `10 minutes`    <br/> Отложи сигнал на `5 minutes`  |
|builtin.alarm.start_date | Установи сигнал для `monday` на 07:00   <br/> Установи сигнал для `tomorrow` на 12:00   |
|builtin.alarm.start_time | Создай сигнал на `30 minutes`    <br/> Установи сигнал для выхода `in 20 minutes`   |
|builtin.alarm.title | Мой сигнал `wake up` включен? <br/> Установи сигнал `take antibiotics` на 11:45 с понедельника по пятницу |
|builtin.calendar.absolute_location | Создай встречу на завтра в `123 main street`   <br/> Собрание состоится в `cincinnati` 5-го июня    |
|builtin.calendar.contact_name|Внеси маркетинговую встречу в календарь и убедись, что `joe` там есть <br/>Я хочу устроить обед в ресторане и пригласить `paul` |   
|builtin.calendar.destination_calendar|Добавь это в мое расписание `work`   <br/>Добавь в мой календарь `personal` |
|builtin.calendar.duration| Настрой встречу для `an hour` на 18:00 <br/>  Закажи встречу `2 hour` с Джо |  
|builtin.calendar.end_date | Создай в календаре запись "отпуск" от завтра до `next monday` <br/>    Поставь статус "занят" до `monday, october 5th` | 
|builtin.calendar.end_time | Собрание заканчивается в `5:30 PM` <br/> Перенеси это с 11 на `noon`  |   
|builtin.calendar.implicit_location | Отмени встречу в департаменте <br/> Измени расположение дня рождения Майли на `poppy restaurant` |    
|builtin.calendar.move_earlier_time | Перенеси собрание на `an hour` <br/> Перенеси поход к стоматологу на `30 minutes`       |
|builtin.calendar.move_later_time | Перенеси поход к стоматологу `30 minutes`    <br/> Активно продвигать тезис `an hour` |  
|builtin.calendar.original_start_date | Перенеси поход к парикмахеру с вторника на среду <br/> Перенеси встречу с Кеном с `monday` на вторник |
|builtin.calendar.original_start_time | Повторно запланируй встречу с `2:00` до 3  <br/> Перенеси поход к стоматологу с `3:30` на 4 |  
|builtin.calendar.start_date | Когда начинается моя вечеринка в `flag day`? <br/> Запланируй обед для `friday after next` в полдень 
|builtin.calendar.start_time | Я хочу запланировать это на `this morning` <br/> Я хочу запланировать это в `morning` |  
|builtin.calendar.title | `vet appointment`  <br/> `dentist` вторник |
|builtin.communication.audio_device_type | Позвони с помощью `bluetooth`  <br/> Позвони используя мой `headset` | 
|builtin.communication.contact_name | Напиши `bob jones`  <br/> | Позвони `sarah`|
|builtin.communication.destination_platform | Позвони Дейву в `london` <br/> Позвони его `work line` |    
|builtin.communication.from_relationship_name | Покажи вызовы из моего `daughter` <br/> Прочти письма от `mom`   |   
|builtin.communication.key | Набери `star` <br/>  Нажми клавишу `hash`    |
|builtin.communication.message | Напиши письмо Карли, чтобы сказать, что `i'm running late` <br/> Напиши Ангусу Смиту `good luck on your exam` |    
|builtin.communication.message_category | Пометь новые письма для `follow up`  <br/> Пометь новые письма `high priority` |    
|builtin.communication.message_type | Отправь `email`   <br/> Прочти мои сообщения `text` вслух |
|builtin.communication.phone_number | Мне нужно набрать `1-800-328-9459` <br/>     Позвони `555-555-5555` |   
|builtin.communication.relationship_name | Напиши `husband` <br/>  Отправь письмо `family`| 
|builtin.communication.slot_attribute | Измени `recipient` <br/>    Измени `text` | 
|builtin.comminication.source_platform | Позвони ему из `skype` <br/> Позвони ему из моего `personal line` |
|builtin.mystuff.attachment| С документами `attached` <br/> Найди сообщения `attachment`, которое отправил Боб |
|builtin.mystuff.contact_name| Найди электронную таблицу, которую Лиза отправила `me` <br/> Где документ, который я отправил `susan` прошлой ночью? |
|builtin.mystuff.data_source | `c:\dev\` <br/> Мой `desktop` <br/> |`"resolution": { "resolution_type": "metadataItems", "metadataType": CanonicalEntity", "value": "desktop" }`|
|builtin.mystuff.data_type | Найди `document`, над которым я работал прошлой ночью <br/> |`"resolution": {"resolution_type": "metadataItems", "metadataType": "CanonicalEntity", "value":Document" }` <br/> Воспитать `visio diagram` Майка  |
|builtin.mystuff.end_date| Покажи мне документы, с которыми я работал в `between yesterday and today`   <br/> Найди, с каким документом я работал в `before thursday the 31st` |
|builtin.mystuff.end_time| Найди файлы, которые я сохранил `before noon` <br/> Найди, с каким документом я работал в `before 4pm`|      
|builtin.mystuff.file_action| Открой электронную таблицу, которую я вчера `saved` <br/> Найди электронную таблицу, которую `created` Кевин| 
|builtin.mystuff.from_contact_name | Найди предложение, отправленное мне `jason` <br/> Открой последние письмо от `isaac` |
|builtin.mystuff.keyword | Покажи файлы `french conjugation` <br/> Найди `marketing plan`, набросок которого я сделал вчера |
|builtin.mystuff.location | Документы, которые я отредактировал в `work` <br/> Фотографии сделанные в `paris` |
|builtin.mystuff.message_category | Найди мои сообщения `new` <br/> Найди мое письмо `high priority` |
|builtin.mystuff.message_type | Проверь `email` <br/>  Покажи мои сообщения `text`  |
|builtin.mystuff.source_platform | Поищи в моей `hotmail` почте письмо от Джона    <br/> Найди документы, которые я отправил с `work` |
|builtin.mystuff.start_date | Найди заметки с `january` <br/> Найди письмо, которое я отправил Робу `after january 1st` |
|builtin.mystuff.start_time | Найди письмо, которое я отправил Робу где-то в 14:00, но `after noon`? <br/> Найди лист, который мне отправила Кристина, и который я отредактировал `last night` | 
|builtin.mystuff.title | `c:\dev\mystuff.txt` <br/> `*.txt` |
|builtin.mystuff.transform_action | `download` файл, который мне отправил Джон <br/> `open` документ о рекомендации заметки |
|builtin.note.note_text | Создай список покупок, включая `pork chops, applesauce and milk` <br/> Сделай заметку `buy milk` |
|builtin.note.title | Сделай заметку о `grocery list` <br/> Сделай заметку о `people to call` |
|builtin.ondevice.music_artist_name | Воспроизведи все с помощью `rufus wainwright` <br/> Воспроизведи `garth brooks` музыку |   
|builtin.ondevice.music_genre | Покажи песни `classic rock` <br/> Играй `classical` музыку периода барокко |
|builtin.ondevice.music_playlist | Перемешай все песни Бритни Спирс в `workout` списке воспроизведения <br/> Воспроизведи `breakup` список
|builtin.ondevice.music_song_name | Воспроизведи `summertime` <br/> Воспроизведи `me and bobby mcgee` | 
|builtin.ondevice.setting_type | `quiet hours` <br/> `airplane mode` |
|builtin.places.absolute_location | Приведи меня к пересечению `5th and pike` <br/> Нет, мне нужно направление к `1 microsoft way redmond wa 98052` |
|builtin.places.atmosphere | Найди места `interesting` для отдыха    <br/> Где можно найти ресторан `casual`? |  
|builtin.places.audio_device_type |Позвони на почту в `hands free` <br/> Позвони в пиццерию с помощью `speakerphone` |    
|builtin.places.avoid_route | Избегай `toll road` <br/> Приведи меня в Сан-Франциско, избегая `construction on 101` |  
|builtin.places.cuisine | Ресторан `halal` с видом на горы   <br/> `kosher` прекрасный ужин на полуострове |
|builtin.places.date | Зарезервируй `next friday the 12th` <br/>  Открыт ли этот ресторан в `mondays`? |
|builtin.places.discount_type | Найди `coupon` для магазина <br/> Найди мне `coupon` |
|builtin.places.distance | Можно ли здесь хорошо поужинать `within 5 miles`? <br/> Найди один `within 15 miles` |   
|builtin.places.from_absolute_location | Направление от `45 elm street` домой <br/> Получи направление с `san francisco` в Пало-Алто  |
|builtin.places.from_place_name | Дорога из `post office` к 56-й Сентер-Стрит <br/> Получи направление с `home depot` в Айову |
|builtin.places.from_place_type | Направление из `work` к центру <br/>  Получи направление из `drug store` домой |
|builtin.places.meal_type | Ближайшие места для `dinner` <br/> Найди хорошее место для бизнеса `lunch` | 
|builtin.places.nearby | Покажи мне классные магазины `near`  <br/> Есть ли `around` здесь какой-то хороший ливанский ресторан? |
|builtin.places.open_status | Когда торговый центр `closed`? <br/> Покажи время `opening` магазина | 
|builtin.places.place_name | Перейди к `central park` <br/> Поищи `eiffel tower` |   
|builtin.places.place_type | `atms` <br/> `post office` |
|builtin.places.prefer_route | Покажи направления по маршруту `shortest` <br/> Возьми маршрут `fastest` | 
|builtin.places.price_range| Покажи список мест, которые `moderately affordable` <br/>  Я хочу один `expensive`   |
|builtin.places.product | Где здесь можно найти `fresh fish`?  <br/> Где здесь продают `bare minerals`? |
|builtin.places.public_transportation_route | Автобусное расписание для автобуса `m2` <br/> Автобусный маршрут `3x` |  
|builtin.places.rating | Покажи `3 star` рестораны <br/> Покажи результаты `3 stars or higher`  |
|builtin.places.reservation_number | Закажи столик для `seven` людей <br/>  Зарезервируй столик на `two` в ресторане |
|builtin.places.results_number | Покажи ближайшие кофейни `10` <br/> Покажи лучшие аквариумы `3`  
|builtin.places.service_provided | Куда можно поехать `whale watch` на автобусе? <br/> Мне нужен механик, чтобы `fix my brakes` |    
|builtin.places.time | Места, которые работают до `8 am` в субботу| Открыт ли этот ресторан `now`? |
|builtin.places.transportation_company | Расписания поездов на `new jersey transit` <br/> Могу ли я добраться туда на `bart` | 
|builtin.places.transportation_type | Где находится музыкальный магазин, куда я могу добраться `on foot`? <br/>  Покажи направления `biking` к городу Машико|
|builtin.places.travel_time | Я хочу научиться водить машину `less than 15 minutes` <br/>   Я хочу куда-то, куда я могу добраться за `under 15 minutes` |   
|builtin.reminder.absolute_location | Напомни, что надо позвонить отцу, когда я приземлюсь в `chicago` <br/> Когда я вернусь в `seattle`, напомни, что надо заправиться |
|builtin.reminder.contact_name | Когда позвонит `bob`, напомни о шутке <br/> Создай напоминание, чтобы сослаться на школьный автобус, когда я разговариваю с `arthur` |
|builtin.reminder.leaving_absolute_location | Напомни, что надо забрать Крейга, когда я выеду с `1200 main` |
|builtin.reminder.leaving_implicit_location | Напомни о заправке, когда я покину `work`  |
|builtin.reminder.original_start_date | Перенеси напоминание о газоне с `saturday` на воскресенье <br/> Перенеси напоминание о школе с `monday` на вторник   |
|builtin.reminder.relationship_name | Если позвонит мой `husband`, напомни сообщить ему о родительском собрании <br/> Напомни снова, когда позвонит `mom`|
|builtin.reminder.reminder_text | Напомни о `bring up my small spot of patchy skin`, когда позвонит доктор Смит  <br/> Напомни мне в 16:40 о `pick up dry cleaning`   |
|builtin.reminder.start_date | Напомни о `thursday after next` в 20:00  <br/> Напомни о `next thursday the 18th` в 20:00    |
|builtin.reminder.start_time | Создай напоминание о `in 30 minutes` <br/> Создай напоминание, чтобы полить цветы `this evening at 7` |  
|builtin.weather.absolute_location | Будет ли дождь в `boston`? <br/> Покажи прогноз погоды на `seattle`  |
|builtin.weather.date_range | Погода в Нью-Йорке `this weekend` <br/>   Поищи прогноз `five day` в Голливуде, штат Флорида |
|builtin.weather.suitable_for | Можно ли пойти `hiking` в шортах в эти выходные?   <br/> Будет ли сегодня хорошо сыграть в `walk`? | 
|builtin.weather.temperature_unit | Какая сегодня температура в `kelvin`   <br/> Какая температура в`celsius` |  
|builtin.weather.time_range | будет ли снег `tonight`? <br/> Сейчас `now` ветрено?  |
|builtin.weather.weather_condition | Покажи `precipitation` <br/> Какая сейчас толщина `snow` на озере Тахо?  |

