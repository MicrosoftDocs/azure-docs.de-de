---
title: Verwenden des Video.js-Players mit Azure Media Services
description: In diesem Artikel erfahren Sie, wie Sie das HTML-Videoobjekt und JavaScript mit Azure Media Services verwenden.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: a0b357705de04ed4c2be3223f9dd07f61f75d970
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954579"
---
# <a name="how-to-use-the-videojs-player-with-azure-media-services"></a>Verwenden des Video.js-Players mit Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Übersicht

Video.js ist ein für HTML5 konzipierter Webvideoplayer. Er gibt adaptive Medienformate wie DASH und HLS in einem Browser wieder, und zwar ohne Plug-Ins oder Flash. Stattdessen verwendet Video.js die offenen Webstandards MSE (Media Source Extensions) und EME (Encrypted Media Extensions). Darüber hinaus unterstützt der Player die Videowiedergabe auf Desktops und mobilen Geräten.

Die offizielle Dokumentation finden Sie unter [https://docs.videojs.com/](https://docs.videojs.com/).

## <a name="sample-code"></a>Beispielcode
Beispielcode für diesen Artikel steht unter [Azure-Samples/media-services-3rdparty-player-samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples) zur Verfügung.

## <a name="implement-the-player"></a>Implementieren des Players

1. Erstellen Sie zum Hosten des Players eine Datei namens `index.html`. Fügen Sie die folgenden Codezeilen hinzu. (Die Versionen können ggf. durch neuere ersetzt werden.)

    ```html
    <html>
      <head>
        <link href="https://vjs.zencdn.net/7.8.2/video-js.css" rel="stylesheet" />
      </head>
      <body>
        <video id="video" class="video-js vjs-default-skin vjs-16-9" controls data-setup="{}">
        </video>

        <script src="https://vjs.zencdn.net/7.8.2/video.js"></script>
        <script src="https://cdn.jsdelivr.net/npm/videojs-contrib-eme@3.7.0/dist/videojs-contrib-eme.min.js"></script>
        <script type="module" src="index.js"></script>
      </body>
    <html>
    ```

2. Fügen Sie eine Datei namens `index.js` mit folgendem Code hinzu:

    ```javascript
    var videoJS = videojs("video");
    videoJS.src({
      src: "manifestUrl",
      type: "protocolType",
    });
    ```

3. Ersetzen Sie `manifestUrl` durch die HLS- oder DASH-URL des Streaminglocators Ihres Medienobjekts. Diese finden Sie im Azure-Portal auf der Seite des Streaminglocators.
    ![Streaminglocator-URLs](media/how-to-shaka-player/streaming-urls.png)

4. Ersetzen Sie `protocolType` durch folgende Optionen:

    - „application/x-mpegURL“ für HLS-Protokolle
    - „application/dash+xml“ für DASH-Protokolle

### <a name="set-up-captions"></a>Einrichten von Untertiteln

Führen Sie die Methode `addRemoteTextTrack` aus, und gehen Sie wie folgt vor:

- Ersetzen Sie `subtitleKind` durch `"captions"`, `"subtitles"`, `"descriptions"` oder `"metadata"`.  
- Ersetzen Sie `caption` durch den Pfad der VTT-Datei. (Die VTT-Datei muss sich im gleichen Host befinden, um CORS-Fehler zu vermeiden.)
- Ersetzen Sie `subtitleLang` durch den BCP 47-Code für die Sprache (also beispielsweise `"eng"` für Englisch oder `"es"` für Spanisch).
- Ersetzen Sie `subtitleLabel` durch den gewünschten Anzeigenamen für Untertitel.

```javascript
videojs.players.video.addRemoteTextTrack({
  kind: subtitleKind,
  src: caption,
  srclang: subtitleLang,
  label: subtitleLabel
});
```

### <a name="set-up-token-authentication"></a>Einrichten der Tokenauthentifizierung

Das Token muss im Autorisierungsfeld des Anforderungsheaders festgelegt werden. Um Probleme mit CORS zu vermeiden, darf dieses Token nur in Anforderungen mit `'keydeliver'` in der URL festgelegt werden. Dies wird mithilfe der folgenden Codezeilen erreicht:

```javascript
setupTokenForDecrypt (options) {
  if (options.uri.includes('keydeliver')) {
    options.headers = options.headers || {}
    options.headers.Authorization = 'Bearer=' + this.getInputToken()
  }

  return options
}
```

Anschließend muss die obige Funktion an das Ereignis `videojs.Hls.xhr.beforeRequest` angefügt werden.

```javascript
videojs.Hls.xhr.beforeRequest = setupTokenForDecrypt;
```

### <a name="set-up-aes-128-encryption"></a>Einrichten der AES-128-Verschlüsselung

Die AES-128-Verschlüsselung wird von Video.js ohne zusätzliche Konfiguration unterstützt. 

> [!NOTE] 
> Es gibt aktuell ein [Problem](https://github.com/videojs/video.js/issues/6717) mit der Verschlüsselung und HLS/DASH-CMAF-Inhalten, die nicht wiedergegeben werden können.

### <a name="set-up-drm-protection"></a>Einrichten von DRM-Schutz

Zu Unterstützung von DRM-Schutz muss die offizielle Erweiterung [videojs-contrib-eme](https://github.com/videojs/videojs-contrib-eme) hinzugefügt werden. Eine CDN-Version funktioniert ebenfalls.

1. In der oben beschriebenen Datei `index.js` muss die EME-Erweiterung initialisiert werden. Hierzu muss `videoJS.eme();` *vor* dem Hinzufügen der Videoquelle hinzugefügt werden:

   ```javascript
    videoJS.eme();
   ```

2. Nun können Sie die URLs der DRM-Dienste und die URLs der entsprechenden Lizenzen definieren:

   ```javascript
   videoJS.src({
       keySystems: {
           "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL",
           "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
           "com.apple.fps.1_0": {
            certificateUri: "YOUR FAIRPLAY CERTIFICATE URL",
            licenseUri: "YOUR FAIRPLAY LICENSE URL"
           }
         }
       })

   ```

#### <a name="acquiring-the-license-url"></a>Ermitteln der Lizenz-URL

Die Lizenz-URL kann wie folgt ermittelt werden:

- Überprüfen Sie Ihre DRM-Anbieterkonfiguration.
- Bei Verwendung des Beispiels können Sie auch das Dokument `output.json` überprüfen, das beim Ausführen des PowerShell-Skripts *setup-vod.ps1* (für VODs) bzw. des Skripts *start-live.ps1* (für Livestreams) generiert wurde. Diese Datei enthält auch die KIDs.

#### <a name="using-tokenized-drm"></a>Verwenden von tokenisiertem DRM

Zur Verwendung von tokenisiertem DRM-Schutz muss der Eigenschaft `src` des Players die folgende Zeile hinzugefügt werden:

```javascript
videoJS.src({
src: ...,
emeHeaders: {'Authorization': "Bearer=" + "YOUR TOKEN"},
keySystems: {...
```

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Azure Media Player](../azure-media-player/azure-media-player-overview.md)  
- [Schnellstart: Verschlüsseln von Inhalten mithilfe des Portals](encrypt-content-quickstart.md)
