---
title: Einbetten von Video Indexer-Widgets in Ihre Anwendungen
titlesuffix: Azure Media Services
description: Erfahren Sie, wie Sie Video Indexer-Widgets in Ihre Anwendungen einbetten.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 5acd9b68368f56000a0a32d1ade310cf30143950
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799381"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>Einbetten von Video Indexer-Widgets in Ihre Anwendungen

In diesem Artikel wird gezeigt, wie Sie Video Indexer-Widgets in Ihre Anwendungen einbetten können. Video Indexer unterstützt das Einbetten von zwei Widget-Typen in Ihre Anwendung: **Kognitive Erkenntnisse** und **Player**. 

Ab Version 2 enthält die Basis-URL des Widgets die Region des Kontos. Ein Konto in der Region „USA, Westen“ generiert z.B.: `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Typen von Widgets

### <a name="cognitive-insights-widget"></a>Widget „Kognitive Erkenntnisse“

Ein Widget vom Typ **Kognitive Erkenntnisse** enthält alle visuellen Erkenntnisse, die für Sie aus dem Prozess der Videoindizierung extrahiert wurden. Für das Widget vom Typ „Kognitive Erkenntnisse“ werden die folgenden optionalen URL-Parameter unterstützt:

|NAME|Definition|BESCHREIBUNG|
|---|---|---|
|Widgets|Durch Komma getrennte Zeichenfolgen|Ermöglicht das Steuern der Erkenntnisse, die Sie rendern möchten. <br/>Beispiel: Mit `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` werden nur Benutzeroberflächenerkenntnisse für Personen und Marken gerendert.<br/>Verfügbare Optionen: People (Personen), Keywords (Stichwörter), Annotations (Anmerkungen), Brands (Marken), Sentiments (Stimmungen), Transcript (Transkript), Search (Suche)<br/>nicht unterstützt über URL bei „version=2“<br/><br/>**Hinweis:** Der URL-Parameter „widgets“ wird in Version 2 nicht unterstützt. |

### <a name="player-widget"></a>Player-Widget

Mit einem **Player**-Widget können Sie das Video mit einer adaptiven Bitrate streamen. Für das Player-Widget werden die folgenden optionalen URL-Parameter unterstützt:

|NAME|Definition|BESCHREIBUNG|
|---|---|---|
|t|Sekunden ab Start|Aktiviert die Wiedergabe durch den Player ab dem angegebenen Zeitpunkt.<br/>Beispiel: t=60|
|captions|Sprachcode|Ruft die Beschriftungen beim Laden des Widgets in der angegebenen Sprache ab, damit sie im Menü für die Beschriftungen verfügbar sind.<br/>Beispiel: captions=en-US|
|showCaptions|Boolescher Wert|Dient zum Laden des Players mit bereits geladenen Beschriftungen.<br/>Beispiel: showCaptions=true|
|type||Aktiviert ein Design für den Audioplayer (Videoteil wird entfernt).<br/>Beispiel: type=audio|
|autoplay|Boolescher Wert|Gibt an, ob der Player beim Laden mit der Wiedergabe des Videos beginnen soll (Standardeinstellung: TRUE).<br/>Beispiel: autoplay=false|
|Language|Sprachcode|Dienst zum Steuern der Sprache des Players (Standardeinstellung: en-US)<br/>Beispiel: language=de-DE|

## <a name="embedding-public-content"></a>Einbetten von öffentlichen Inhalten

1. Navigieren Sie zur [Video Indexer](https://www.videoindexer.ai/)-Website, und melden Sie sich an.
2. Klicken Sie auf das Video, das Sie verwenden möchten.
3. Klicken Sie auf die Schaltfläche „Einbetten“, die unter dem Video angezeigt wird.

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Nach dem Klicken auf die Schaltfläche wird auf dem Bildschirm ein modales Element für das Einbetten angezeigt, in dem Sie auswählen können, welches Widget Sie in Ihre Anwendung einbetten möchten.
    Beim Auswählen eines Widgets (**Player** oder **Kognitive Erkenntnisse**) wird der eingebettete Code generiert, den Sie in Ihre Anwendung einfügen können.
 
4. Wählen Sie den gewünschten Widget-Typ aus (**Kognitive Erkenntnisse** oder **Player**).
5. Kopieren Sie den Einbettungscode, und fügen Sie ihn Ihrer Anwendung hinzu. 

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

## <a name="embedding-private-content"></a>Einbetten von privaten Inhalten

Sie können Codes aus eingebetteten Popups (wie im vorherigen Abschnitt gezeigt) nur für **öffentliche** Videos abrufen. 

Wenn Sie ein **privates** Video einbetten möchten, müssen Sie ein Zugriffstoken im Attribut **src** von **iframe** übergeben:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Verwenden Sie die API des [**Widgets „Einblicke erhalten“**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget), um den Inhalt des Widgets des Typs „Kognitive Erkenntnisse“ abzurufen. Oder verwenden Sie [**Get Video Access Token**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) (Videozugriffstoken abrufen), und fügen Sie es wie oben gezeigt der URL als Abfrageparameter hinzu. Geben Sie diese URL als **src**-Wert von **iframe** an.

Wenn Sie für Ihr eingebettetes Widget Funktionen zum Bearbeiten von Erkenntnissen bereitstellen möchten (wie in unserer Webanwendung), müssen Sie ein Zugriffstoken mit Bearbeitungsberechtigungen übergeben. Verwenden Sie [**Get Insights Widget**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) oder [**Get Video Access Token**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) mit **&allowEdit=true**. 

## <a name="widgets-interaction"></a>Interaktion von Widgets

Das Widget **Kognitive Erkenntnisse** kann mit einem Video in Ihrer Anwendung interagieren. In diesem Abschnitt wird veranschaulicht, wie Sie diese Interaktion erreichen.

![Widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Ursprungsübergreifende Kommunikation

Der Video Indexer-Dienst geht wie folgt vor, um für Video Indexer-Widgets die Kommunikation mit anderen Komponenten zu ermöglichen:

- Verwendung der HTML5-Methode **postMessage** für die ursprungsübergreifende Kommunikation 
- Überprüfung der Nachricht für den VideoIndexer.ai-Ursprung 

Wenn Sie Ihren eigenen Playercode implementieren und die Integration mit Widgets vom Typ **Kognitive Erkenntnisse** durchführen möchten, sind Sie selbst dafür verantwortlich, den Ursprung der Nachricht von „VideoIndexer.ai“ zu überprüfen.

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>Einbetten beider Widget-Typen in Ihre Anwendung bzw. Ihren Blog (empfohlen) 

In diesem Abschnitt wird veranschaulicht, wie Sie die Interaktion zwischen zwei Video Indexer-Widgets so konfigurieren, dass der Player zum relevanten Moment springt, wenn ein Benutzer in Ihrer Anwendung auf das Steuerelement für Erkenntnisse klickt.

`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

1. Kopieren Sie den Einbettungscode des **Player**-Widgets.
2. Kopieren Sie den Einbettungscode von **Kognitive Erkenntnisse**.
3. Fügen Sie die [**Mediator-Datei**](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) für die Kommunikation zwischen den beiden Widgets hinzu:

`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Wenn ein Benutzer in Ihrer Anwendung jetzt auf das Steuerelement für Erkenntnisse klickt, springt der Player zum relevanten Moment.

Weitere Informationen finden Sie in [dieser Demo](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Einbetten des Widgets „Kognitive Erkenntnisse“ und Verwenden von Azure Media Player zum Wiedergeben der Inhalte

In diesem Abschnitt wird veranschaulicht, wie Sie die Interaktion zwischen einem Widget vom Typ **Kognitive Erkenntnisse** und einer Azure Media Player-Instanz per [AMP-Plug-In](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js) einrichten.
 
1. Fügen Sie ein Video Indexer-Plug-In für den AMP-Player hinzu.<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Instanziieren Sie Azure Media Player mit dem Video Indexer-Plug-In.

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

3. Kopieren Sie den Einbettungscode von **Kognitive Erkenntnisse**.

Sie sollten nun mit Ihrer Azure Media Player-Instanz kommunizieren können.

Weitere Informationen finden Sie in [dieser Demo](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>Einbetten des Video Indexer-Widgets „Kognitive Erkenntnisse“ und Verwenden Ihres eigenen Players (beliebiger Player)

Bei Verwendung Ihres eigenen Players müssen Sie diesen selbst so anpassen, dass die Kommunikation möglich ist. 

1. Fügen Sie Ihren Videoplayer ein.

    Dies kann beispielsweise ein HTML5-Standardplayer sein:

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Betten Sie das Widget „Kognitive Erkenntnisse“ ein.
3. Implementieren Sie die Kommunikation für Ihren Player, indem Sie auf das Ereignis „message“ lauschen. Beispiel: 

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

Weitere Informationen finden Sie in [dieser Demo](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Hinzufügen von Untertiteln

Wenn Sie Video Indexer-Erkenntnisse in Ihren eigenen AMP-Player einbetten, können Sie die **GetVttUrl**-Methode verwenden, um Untertitel zu erhalten. Sie können auch eine JavaScript-Methode aus dem Video Indexer-AMP-Plug-In abrufen (**getSubtitlesUrl**, wie bereits gezeigt). 

## <a name="customizing-embeddable-widgets"></a>Anpassen von für die Einbettung geeigneten Widgets

### <a name="cognitive-insights-widget"></a>Widget „Kognitive Erkenntnisse“

Sie können die gewünschten Erkenntnisse auswählen, indem Sie sie als Wert für den folgenden URL-Parameter angeben, der dem abgerufenen Einbettungscode hinzugefügt wird (aus der API oder der Webanwendung): `&widgets=<list of wanted widgets>`.

Mögliche Werte: people (Personen), keywords (Schlüsselwörter), sentiments (Stimmungen), transcript (Transkript), search (Suche).

Wenn Sie beispielsweise ein Widget einbetten möchten, das nur Erkenntnisse zu Personen und zur Suche enthalten soll, sieht die iframe-URL für die Einbettung wie folgt aus:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

Der Titel des iframe-Fensters kann auch angepasst werden, indem für die iframe-URL `&title=<YourTitle>` angegeben wird. (Dient zum Anpassen des HTML-Werts \<title>.)
    
Falls Sie Ihrem iframe-Fenster beispielsweise den Titel „MyInsights“ geben möchten, sieht die URL wie folgt aus:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Beachten Sie, dass diese Option nur in Fällen relevant ist, in denen Sie die Erkenntnisse in einem neuen Fenster öffnen müssen.

### <a name="player-widget"></a>Player-Widget

Wenn Sie den Video Indexer-Player einbetten, können Sie die Größe des Players wählen, indem Sie die iframe-Größe angeben.

Beispiel: 

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Standardmäßig verfügt der Video Indexer-Player über automatisch generierte Untertitel, die auf dem Transkript des Videos basieren. Diese Daten wurden aus dem Video mit der Quellsprache extrahiert, die beim Hochladen des Videos gewählt wurde.

Wenn Sie für das Einbetten eine andere Sprache verwenden möchten, können Sie der URL für die Einbettung des Players `&captions=< Language | ”all” | “false” >` hinzufügen oder „all“ als Wert angeben, falls die Untertitel aller Sprachen verfügbar sein sollen.
Falls die Untertitel standardmäßig angezeigt werden sollen, können Sie `&showCaptions=true` übergeben.

Die URL für die Einbettung sieht dann wie folgt aus: 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

Wenn Sie Untertitel deaktivieren möchten, können Sie FALSE als Wert für den Parameter „captions“ übergeben.

Automatische Wiedergabe: Standardmäßig beginnt der Player mit der Wiedergabe des Videos. Sie können dies deaktivieren, indem Sie für die obige URL für die Einbettung „&autoplay=false“ übergeben.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Anzeigen und Bearbeiten von Video Indexer-Erkenntnissen finden Sie in [diesem Artikel](video-indexer-view-edit.md).

Sehen Sie sich außerdem auch [Video Indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ) an.
