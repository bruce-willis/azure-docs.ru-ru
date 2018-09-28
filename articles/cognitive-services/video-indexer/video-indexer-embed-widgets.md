---
title: Пример. Внедрение мини-приложений Индексатора видео в приложение
titlesuffix: Azure Cognitive Services
description: Узнайте, как внедрить мини-приложения Индексатора видео в свое приложение.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: sample
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: 0d75a58ddf0607286d41867828119fdd05e07d22
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985585"
---
# <a name="example-embed-video-indexer-widgets-into-your-applications"></a>Пример. Внедрение мини-приложений Индексатора видео в приложение

В этой статье показано, как внедрить мини-приложения Индексатора видео в приложение. Индексатор видео поддерживает два типа мини-приложений, которые можно внедрить в приложение: **Cognitive Insights** и **Player**. 
## <a name="widget-types"></a>Типы мини-приложений

### <a name="cognitive-insights-widget"></a>Мини-приложение Cognitive Insights

Мини-приложение **Cognitive Insights** содержит все визуальные аналитические сведения, извлеченные при индексировании видео. Это мини-приложение поддерживает следующие необязательные параметры URL-адреса:

|ИМЯ|Определение|ОПИСАНИЕ|
|---|---|---|
|widgets|Строки, разделенные запятыми.|Позволяет управлять аналитическими сведениями, которые нужно преобразовать для просмотра. <br/>Пример: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` преобразует для просмотра в пользовательском интерфейсе только аналитические сведения о людях и торговых марках.<br/>Доступные значения: people, keywords, annotations, brands, sentiments, transcript, search.<br/>Не поддерживается с помощью URL-адреса, если используется version=2.<br/><br/>**Примечание.** Параметр URL-адреса **widgets** не поддерживается, если используется **version=2**. |
|версия|Версии мини-приложения **Cognitive Insights**|Чтобы получить последние обновления мини-приложения аналитических сведений, добавьте параметр запроса `?version=2` к URL-адресу для внедрения. Например, `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?version=2` <br/> Чтобы получить старую версию, просто удалите `version=2` из URL-адреса.

### <a name="player-widget"></a>Мини-приложение Player

Мини-приложение **Player** позволяет выполнять потоковую передачу видео с переменной скоростью. Это мини-приложение поддерживает следующие необязательные параметры URL-адреса:

|ИМЯ|Определение|ОПИСАНИЕ|
|---|---|---|
|t|Число секунд от начала видео.|Позволяет проигрывателю воспроизводить видео с определенной точки во времени.<br/>Пример: t=60|
|captions|Код языка|Позволяет получать субтитры на указанном языке при загрузке мини-приложения, чтобы они были доступны в соответствующем меню.<br/>Пример: captions=en-Us.|
|showCaptions|Логическое значение.|Позволяет проигрывателю загружаться с уже включенными субтитрами.<br/>Пример: showCaptions=true|
|Тип||Активирует обложку аудиопроигрывателя (видеочасть удаляется).<br/>Пример: type=audio|
|autoplay|Логическое значение.|Указывает, должен ли проигрыватель начать воспроизведение видео при загрузке (значение по умолчанию — true).<br/>Пример: autoplay=false|
|Язык|Код языка|Язык элементов управления проигрывателя (значение по умолчанию — en-US)<br/>Пример: language=de-DE|

## <a name="embedding-public-content"></a>Внедрение общедоступного содержимого

1. Откройте веб-сайт [Индексатора видео](https://www.videoindexer.ai/) и выполните вход.
2. Нажмите кнопку "Внедрить", расположенную под видео.

    ![Мини-приложение](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Когда вы нажмете кнопку, на экране появится модальное окно внедрения, в котором можно выбрать, какое мини-приложение нужно внедрить в приложение.
    После выбора мини-приложения (**Player** или **Cognitive Insights**) создается код внедрения, который нужно вставить в приложение.
 
3. Выберите необходимый тип мини-приложения (**Cognitive Insights** или **Player**).
4. Скопируйте и добавьте код внедрения в приложение. 

    ![Мини-приложение](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

## <a name="embedding-private-content"></a>Внедрение личного содержимого

Код внедрения из соответствующих всплывающих окон (как показано в предыдущем разделе) можно получить только для **общедоступных** видео. 

Чтобы внедрить **частное** видео, нужно передать маркер доступа в атрибуте **iframe** **src**:

     https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>
    
Используйте API [**Get Insights Widget**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?), чтобы получить содержимое мини-приложения Cognitive Insights, или используйте API [**Get Video Access Token**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?), добавив его в качестве параметра запроса в URL-адрес, как показано выше. Укажите этот URL-адрес как значение атрибута **iframe** **src**.

Чтобы включить редактирование аналитических сведений (например, как в нашем веб-приложении) во внедряемом мини-приложении, нужно передать маркер доступа с разрешениями на редактирование. Используйте API [**Get Insights Widget**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) или [**Get Video Access Token**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) с указанным параметром **&allowEdit=true**. 

## <a name="widgets-interaction"></a>Взаимодействие с мини-приложениями

Мини-приложение **Cognitive Insights** может взаимодействовать с видео в вашем приложении. В этом разделе показано, как реализовать это взаимодействие.

![Мини-приложение](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Обмен данными независимо от источника

Чтобы реализовать обмен данными между мини-приложениями Индексатора видео и другими компонентами, Индексатор видео выполняет следующие действия:

- использует метод HTML5 **postMessage** для обмена данными независимо от источника; 
- проверяет сообщения, поступающие из источника VideoIndexer.ai и в него. 

Если вы решили реализовать собственный код проигрывателя и интегрировать его с мини-приложением **Cognitive Insights**, вы должны самостоятельно проверять источник сообщения, поступающего от VideoIndexer.ai.

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>Внедрение мини-приложений обоих типов в приложение или блог (рекомендуется) 

В этом разделе показано, как реализовать взаимодействие между двумя мини-приложениями Индексатора видео. Когда пользователь щелкнет элемент управления аналитическими сведениями в приложении, проигрыватель перейдет к соответствующему моменту в видео.

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script> 

1. Скопируйте код внедрения для мини-приложения **Player**.
2. Скопируйте код внедрения для **Cognitive Insights**.
3. Добавьте [**файл медиатора**](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js), чтобы реализовать взаимодействие между двумя мини-приложениями:

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>

Теперь, когда пользователь щелкнет элемент управления аналитическими сведениями в приложении, проигрыватель перейдет к соответствующему моменту в видео.

Дополнительные сведения см. в [этом примере](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Внедрение мини-приложения Cognitive Insights и использование Проигрывателя мультимедиа Azure для воспроизведения содержимого

В этом разделе показано, как реализовать взаимодействие между мини-приложением **Cognitive Insights** и экземпляром Проигрывателя мультимедиа Azure (AMP) с помощью [подключаемого модуля AMP](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Добавьте подключаемый модуль Индексатора видео для Проигрывателя мультимедиа Azure.

        <script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>


2. Создайте экземпляр Проигрывателя мультимедиа Azure с подключаемым модулем Индексатора видео.

        // Init Source
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // Here is how to load vtt from VI, you can replace it with your vtt url.
            src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
            srclang: 'en',
            label: 'English'
            }];

            myPlayer.src([
            {
                "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
                "type": "application/vnd.ms-sstr+xml"
            }
            ], tracks);
        }

        // Init your AMP instance
        var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: true,
            controls: true,
            width: "640",
            height: "400",
            poster: "",
            plugins: {
            videobreakedown: {}
            }
        }, function () {
            // Activate the plugin
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true
            });

            // Set the source dynamically
            initSource.call(this);
        });

3. Скопируйте код внедрения для **Cognitive Insights**.

Теперь можно обмениваться данными с Проигрывателем мультимедиа Azure.

Дополнительные сведения см. в [этом примере](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>Внедрение мини-приложения Cognitive Insights Индексатора видео и использование собственного проигрывателя (то есть любого проигрывателя)

Если вы используете собственный проигрыватель, вам нужно самому настроить его, чтобы обеспечить обмен данными. 

1. Вставьте свой видеопроигрыватель.

    Например, стандартный проигрыватель HTML5.

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Внедрите мини-приложение Cognitive Insights.
3. Реализуйте обмен данными для проигрывателя, настроив ожидание передачи данных о событии message. Например: 

        <script>
    
            (function(){
            // Reference your player instance
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Here you need to call your player "jumpTo" implementation
                playerInstance.currentTime = evt.data.time;
               
                // Confirm arrival to us
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to message event
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>


Дополнительные сведения см. в [этом примере](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Добавление субтитров

Если вы внедряете Cognitive Insights Индексатора видео со своим проигрывателем AMP, можете использовать метод **GetVttUrl** для получения скрытых субтитров. Вы также можете вызвать метод JavaScript **getSubtitlesUrl** из подключаемого модуля AMP Индексатора видео (как показано выше). 

## <a name="customizing-embeddable-widgets"></a>Настройка внедряемых мини-приложений

### <a name="cognitive-insights-widget"></a>Мини-приложение Cognitive Insights
Вы можете выбрать необходимые типы аналитических сведений, указав их в качестве значений для следующего параметра URL-адреса, который добавляется в получаемый код внедрения (из API или веб-приложения):

**&widgets=** \<список необходимых мини-приложений>

Возможные значения: people, keywords, sentiments, transcript, search.

Например, если нужно внедрить мини-приложение, содержащее только такие аналитические сведения, как изображения людей и результаты поиска, внедряемый URL-адрес для iframe будет выглядеть так: https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search

Также можно настроить заголовок окна iframe, указав **&title=**<YourTitle> в URL-адресе для iframe. (Будет задано значение HTML-элемента \<title>).
Например, если для окна iframe нужно указать заголовок MyInsights, URL-адрес будет выглядеть так: https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights. Обратите внимание на то, что этот параметр применим, только когда аналитические сведения нужно открыть в новом окне.

### <a name="player-widget"></a>Мини-приложение Player
При внедрении проигрывателя Индексатора видео можно выбрать размер проигрывателя, указав размер для элемента iframe.

Например: 

    <iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />

По умолчанию в Player Индексатора видео будут автоматически создаваться скрытые субтитры на основе расшифрованной речи. Эти расшифрованные сведения извлекаются из видео на исходном языке, который был выбран при отправке видео.

Если нужно внедрить мини-приложение с другим языком, добавьте параметр **&captions=< Language | "all" | "false" >** в URL-адрес для внедрения проигрывателя или укажите значение all, чтобы получить субтитры на всех доступных языках.
Чтобы субтитры отображались по умолчанию, передайте **&showCaptions=true**.

URL-адрес для внедрения будет выглядеть следующим образом: https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian. Чтобы отключить субтитры, передайте значение false для параметра captions.

Автоматическое воспроизведение означает, что проигрыватель по умолчанию начинает воспроизводить видео. Вы можете отменить это действие, передав &autoplay=false в URL-адрес внедрения выше.

## <a name="next-steps"></a>Дополнительная информация

Сведения о том, как просматривать и редактировать аналитические сведения в Индексаторе видео, см. в [этой](video-indexer-view-edit.md) статье.

Кроме того, ознакомьтесь с [Индексатором видео codepen](https://codepen.io/videoindexer/pen/eGxebZ).
