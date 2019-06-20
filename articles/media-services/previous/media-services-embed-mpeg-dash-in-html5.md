---
title: Einbetten eines Videos mit adaptivem Streaming im MPEG-DASH-Format in eine HTML5-Anwendung mit DASH.js | Microsoft-Dokumentation
description: In diesem Thema wird die Einbettung eines Videos mit adaptivem Streaming im MPEG-DASH-Format mithilfe von DASH.js in eine HTML5-Anwendung erläutert.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: f521fd11a2053cf8cf1ea0f9f91667fe475f0eee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61464197"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Einbetten eines Videos mit adaptivem Streaming im MPEG-DASH-Format in eine HTML5-Anwendung mit DASH.js  

## <a name="overview"></a>Übersicht
MPEG-DASH ist ein ISO-Standard für adaptives Streaming von Videoinhalten, der Entwicklern wichtige Vorteile bietet, die qualitativ hochwertige Videoausgaben mit adaptivem Streaming bereitstellen möchten. Mit MPEG-DASH wird der Videostream automatisch einer niedrigeren Auflösung angepasst, wenn das Netzwerk überlastet ist. Dadurch verringert sich die Wahrscheinlichkeit eines "angehaltenen" Videos, während der Player die nächsten Sekunden für die Wiedergabe herunterlädt (Pufferung). Wenn die Netzwerküberlastung abnimmt, kehrt der Videoplayer zu einem Streaming mit höherer Qualität zurück. Diese Möglichkeit, die erforderliche Bandbreite anzupassen, führt außerdem zu einer kürzeren Startzeit für Videos. Das bedeutet, dass die ersten Sekunden in einem schnell herunterladbaren Segment mit niedriger Qualität wiedergegeben werden können und die Qualität anschließend erhöht wird, wenn genügend Inhalte gepuffert wurden.

Dash.js ist ein Open Source-MPEG-DASH-Videoplayer, der in JavaScript geschrieben wurde. Es handelt sich um einen robusten, plattformübergreifenden Player, der kostenlos in Anwendungen zur Videowiedergabe verwendet werden kann. Er stellt die MPEG-DASH-Wiedergabe in allen Browsern zur Verfügung, die W3C MSE (Media Source Extensions) unterstützen. Zurzeit sind dies Chrome, Microsoft Edge und IE11, für andere Browser wurde eine baldige Unterstützung von MSE zugesichert. Weitere Informationen zu DASH.js finden Sie im dash.js-Repository von GitHub.

## <a name="creating-a-browser-based-streaming-video-player"></a>Erstellen eines browserbasierten Streaming-Videoplayers
So erstellen Sie eine einfache Webseite, auf der ein Videoplayer mit den erwarteten Bedienelementen angezeigt wird, z. B. Wiedergeben, Anhalten, Bildsuchlauf usw.

1. Erstellen einer HTML-Seite
2. Hinzufügen des Videotags
3. Hinzufügen des Players "dash.js"
4. Initialisieren des Players
5. Hinzufügen von CSS-Formatvorlagen
6. Anzeigen der Ergebnisse in einem Browser, der MSE implementiert

Die Initialisierung des Players kann in nur wenigen Codezeilen des JavaScript-Codes ausgeführt werden. Mit dash.js ist es sehr einfach, MPEG-DASH-Videos in Ihre browserbasierten Anwendungen einzubetten.

## <a name="creating-the-html-page"></a>Erstellen der HTML-Seite
Im ersten Schritt wird eine HTML-Standardseite mit dem **video**-Element erstellt und die Datei unter dem Namen „basicPlayer.html“ gespeichert, wie im folgenden Beispiel veranschaulicht:

```html
    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>
```

## <a name="adding-the-dashjs-player"></a>Hinzufügen des Players "DASH.js"
Zum Hinzufügen der Referenzimplementierung von „dash.js“ zu Ihrer Anwendung müssen Sie die Datei „dash.all.js“ von der neuesten Version des dash.js-Projekts abrufen. Diese Datei sollte im JavaScript-Ordner der Anwendung gespeichert werden. Der gesamte dash.js-Code ist in dieser Datei bequem zusammengefasst. Wenn Sie sich das dash.js-Repository ansehen, finden Sie dort die einzelnen Dateien, den Testcode und vieles mehr. Wenn Sie allerdings nur „dash.js“ verwenden möchten, benötigen Sie nur die Datei „dash.all.js“.

Zum Hinzufügen des Players "dash.js" zu Ihren Anwendungen fügen Sie dem Anfangsabschnitt von "basicPlayer.html" ein Skript-Tag hinzu:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

Erstellen Sie anschließend eine Funktion zum Initialisieren des Players beim Laden der Seite. Fügen Sie das folgende Skript nach der Zeile ein, in der Sie "dash.all.js" laden:

```html
    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>
```

Diese Funktion erstellt zunächst einen DashContext. Dieser wird zum Konfigurieren der Anwendung für eine bestimmte Laufzeitumgebung verwendet. Aus technischer Sicht definiert er die Klassen, die das Dependency Injection-Framework beim Erstellen der Anwendung verwenden soll. In den meisten Fällen verwenden Sie „Dash.di.DashContext“.

Als Nächstes instanziieren Sie MediaPlayer, die Hauptklasse des dash.js-Frameworks. Diese Klasse enthält die benötigten Kernmethoden, z. B. „Wiedergeben“ oder „Anhalten“, und verwaltet die Beziehung mit dem Videoelement sowie die Interpretation der MPD-Datei (Media Presentation Description), die das Video beschreibt, das wiedergeben werden soll.

Die startup()-Funktion der MediaPlayer-Klasse wird aufgerufen, um sicherzustellen, dass der Player für die Wiedergabe des Videos bereit ist. Diese Funktion stellt unter anderem sicher, dass alle erforderlichen Klassen (wie vom Kontext definiert) geladen wurden. Wenn der Player bereit ist, können Sie ihn mithilfe der attachView()-Funktion mit dem Videoelement verknüpfen. Mit der Startfunktion kann MediaPlayer den Videostream in das Element einfügen und zudem die Wiedergabe nach Bedarf steuern.

Übergeben Sie die URL der MPD-Datei an den MediaPlayer, damit dieser weiß, welches Video wiedergegeben werden soll. Die soeben erstellte setupVideo()-Funktion muss ausgeführt werden, sobald die Seite vollständig geladen ist. Verwenden Sie dazu das onload-Ereignis des Textelements. Ändern Sie das `<body>`-Element wie folgt:

```html
    <body onload="setupVideo()">
```

Legen Sie schließlich die Größe des Videoelements mithilfe von CSS fest. In einer Umgebung für adaptives Streaming ist dies besonders wichtig, da sich die Größe des wiedergegebenen Videos ändern kann, wenn die Wiedergabe an veränderte Netzwerkbedingungen angepasst wird. In dieser einfachen Demo wird das Videoelement einfach auf 80 % des verfügbaren Browserfensters gesetzt, indem das folgende CSS zum Anfangsabschnitt der Seite hinzugefügt wird:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Wiedergeben eines Videos
Zeigen Sie zur Wiedergabe eines Videos in Ihrem Browser auf die Datei "basicPlayback.html", und klicken Sie dann im angezeigten Videoplayer auf die Schaltfläche für die Wiedergabe.

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Siehe auch
[Entwickeln von Videoplayeranwendungen](media-services-develop-video-players.md)

[dash.js-Repository von GitHub (in englischer Sprache)](https://github.com/Dash-Industry-Forum/dash.js) 

