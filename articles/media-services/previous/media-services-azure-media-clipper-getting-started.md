---
title: Erste Schritte mit Azure Media Clipper | Microsoft-Dokumentation
description: Erste Schritte mit Azure Media Clipper, einem Tool zum Erstellen von Videoclips aus AMS-Medienobjekten
services: media-services
keywords: Clip;Schneiden;Subclip;Codierung;Medien
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 51848b9ba4d18b3ac7d652cfbd97cab6b85f2ee8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61466271"
---
# <a name="create-clips-with-azure-media-clipper"></a>Erstellen von Clips mit Azure Media Clipper
Dieser Abschnitt zeigt Ihnen die grundlegenden Schritte für den Einstieg in Azure Media Clipper. In den darauffolgenden Abschnitten wird die Konfiguration von Azure Media Clipper erläutert.

- Fügen Sie der Kopfzeile Ihres Dokuments zunächst die folgenden Links für Azure Media Player und Azure Media Clipper hinzu. Es empfiehlt sich, in den URLs explizit eine bestimmte Version von Clipper und Azure Media Player anzugeben. Verwenden Sie in Produktionsumgebungen nicht die letzte Version dieser Ressourcen, da diese sich ggf. ändern können.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Fügen Sie anschließend die folgenden Klassen zu dem div-Element hinzu, in dem Sie Clipper instanziieren möchten.

```javascript
<div id="root" class="azure-subclipper" />
```

Um das dunkle Design zu aktivieren, fügen Sie optional die dark-skin-Klasse hinzu:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- Als Nächstes instanziieren Sie Clipper mit dem folgenden API-Aufruf:

```javascript
var subclipper = new subclipper({
    selector: '#root',
    logLevel: 'info',
    restVersion: '2.0',
    minimumMarkerGap: 6,
    submitSubclipCallback: onSubmitSubclip,
    singleBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with single H264Layers
                }, {
                    // Audio Codec
                }]
    },
    multiBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with multiple H264Layers
                }, {
                    // Audio Codec
                }]
    },
    keymap: {
      // See below for more info
    },
   // Enable the Video Assets panel in the widget to automatically load assets (input contract)
    assetsPanelLoaderCallback: onLoadAssets,
    height: 600,
    subclippingMode: 'all',
    filterAssetsTypes: true,
    speedLevels: [
        { name: '4x', value: 4.0 },
        { name: '3x', value: 3.0 },
        { name: '2x', value: 2.0 },
        { name: 'normal', value: 1.0 },
        { name: '0.75x', value: 0.75 },
        { name: '0.5x', value: 0.5 },
    ],
    resetOnJobDone: false,
    autoplayVideo: true,
    language: 'en'    
});
```

Folgende Parameter sind im Aufruf der Initialisierungsmethode erforderlich:
- `selector` {ERFORDERLICH, Zeichenfolge}: CSS-Selektor des passenden HTML-Elements, in dem das Widget gerendert werden soll.
- `restVersion` {ERFORDERLICH, Zeichenfolge}: Die Zielversion der Azure Media Services-REST-API. Die REST-Version definiert das Format der vom Widget generierten Ausgabe. Derzeit wird nur 2.0 unterstützt.
- `submitSubclipCallback` {REQUIRED, promise}: Die aufgerufene Rückruffunktion, wenn auf die Schaltfläche „Senden“ des Widgets geklickt wird. Die Rückruffunktion sollte die vom Widget generierte Ausgabe erwarten (eine Renderauftragskonfiguration oder eine Filterdefinition). Weitere Informationen finden Sie in der Rückruffunktion zum Senden des Subclips.
- `logLevel` {OPTIONAL, {'info', 'warn', 'error'}}: Legt die Protokollierungsebene fest, die in der Browserkonsole angezeigt werden soll. Standardwert: Fehler
- `minimumMarkerGap` {OPTIONAL, Int}: Die Mindestlänge eines Subclips (in Sekunden). Hinweis: Der Wert sollte mindestens 6 betragen; dies ist außerdem der Standardwert.
- `singleBitrateMp4Profile` {OPTIONAL, JSON object}: Das MP4-Profil mit Einzelbitrate, das für die vom Widget generierte Renderauftragskonfiguration verwendet werden soll. Wenn kein Profil angegeben ist, wird das [MP4-Standardprofil mit Einzelbitrate](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p) verwendet.
- `multiBitrateMp4Profile` {OPTIONAL, JSON object}: Das MP4-Profil mit Mehrfachbitrate, das für die vom Widget generierte Renderauftragskonfiguration verwendet werden soll. Wenn kein Profil angegeben ist, wird das [MP4-Standardprofil mit Mehrfachbitrate](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p) verwendet.
- `keymap` {OPTIONAL, json object}: Ermöglicht die Anpassung der Tastenkombinationen des Widgets. Weitere Informationen finden Sie unter [Konfigurieren von Azure Media Clipper-Tastenkombinationen](media-services-azure-media-clipper-keyboard-shortcuts.md).
- `assetsPanelLoaderCallback` {OPTIONAL, promise}: Die Rückruffunktion, die aufgerufen wird, um jedes Mal, wenn ein Benutzer zum Ende des Medienobjektbereichs scrollt, (asynchron) eine neue Seite mit Medienobjekten in den Bereich zu laden. Weitere Informationen finden Sie in der Rückruffunktion zum Laden des Medienobjektbereichs.
- `height` {OPTIONAL, number}: Die Gesamthöhe des Widgets. Die Mindesthöhe beträgt 600 px ohne Medienobjektbereich und 850 px mit Medienobjektbereich.
- `subclippingMode` (OPTIONAL, {'all', 'render', 'filter'}): Die zulässigen Subclipmodi. Der Standardwert lautet „Alle“.
- `filterAssetsTypes` (OPTIONAL, bool): filterAssetsTypes ermöglicht Ihnen, die Filterdropdownliste im Medienobjektbereich ein- und auszublenden. Der Standardwert lautet „true“.
- `speedLevels` (OPTIONAL, array): speedLevels ermöglicht das Festlegen verschiedener Geschwindigkeiten für die Videowiedergabe. Weitere Informationen finden Sie in der [Dokumentation zu Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions).
- `resetOnJobDone` (OPTIONAL, bool): resetOnJobDone ermöglicht Clipper, die Subcliperstellung auf den ursprünglichen Zustand zurückzusetzen, wenn ein Auftrag erfolgreich übermittelt wurde.
- `autoplayVideo` (OPTIONAL, bool): autoplayVideo ermöglicht Clipper, das Video nach dem Laden automatisch wiederzugeben. Der Standardwert lautet „true“.
- `language` {OPTIONAL, string}: „language“ legt die Sprache des Widgets fest. Wenn dieser Parameter nicht angegeben ist, versucht das Widget, die Nachrichten basierend auf der Browsersprache zu lokalisieren. Wenn im Browser keine Sprache ermittelt werden kann, wird standardmäßig Englisch verwendet. Weitere Informationen finden Sie im Abschnitt [Konfigurieren der Lokalisierung](media-services-azure-media-clipper-localization.md).
- `languages` {OPTIONAL, JSON}: Der languages-Parameter ersetzt das Standardwörterbuch der Sprachen durch ein vom Benutzer definiertes Wörterbuch. Weitere Informationen finden Sie im Abschnitt [Konfigurieren der Lokalisierung](media-services-azure-media-clipper-localization.md).
- `extraLanguages` (OPTIONAL, JSON): Der extraLanguages-Parameter fügt dem Standardwörterbuch neue Sprachen hinzu. Weitere Informationen finden Sie im Abschnitt [Konfigurieren der Lokalisierung](media-services-azure-media-clipper-localization.md).

## <a name="typescript-definition"></a>TypeScript-Definition
Eine [TypeScript](https://www.typescriptlang.org/)-Definitionsdatei für Clipper finden Sie [hier](https://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>Azure Media Clipper-API
Dieser Abschnitt dokumentiert die von Clipper bereitgestellte API-Benutzeroberfläche.

- `ready(handler)`: Bietet eine Möglichkeit zur Ausführung von JavaScript, sobald Clipper vollständig geladen und einsatzbereit ist.
- `load(assets)`: Lädt eine Liste von Medienobjekten in die Widgetzeitleiste (sollte nicht zusammen mit „assetsPanelLoaderCallback“ verwendet werden). Informationen zum Laden von Medienobjekten in Clipper finden Sie in diesem [Artikel](media-services-azure-media-clipper-load-assets.md).
- `setLogLevel(level)`: Legt die Protokollierungsebene fest, die in der Browserkonsole angezeigt werden soll. Mögliche Werte: `info`, `warn`, `error`.
- `setHeight(height)`: Legt die Gesamthöhe des Widgets in Pixeln fest. Die Mindesthöhe beträgt 600 px ohne Medienobjektbereich und 850 px mit Medienobjektbereich.
- `version`: Ruft die Widgetversion ab.

## <a name="next-steps"></a>Nächste Schritte
Dies sind die nächsten Schritte für die Konfiguration von Azure Media Clipper:
- [Laden von Medienobjekten in Azure Media Clipper](media-services-azure-media-clipper-load-assets.md)
- [Konfigurieren von benutzerdefinierten Tastenkombinationen](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Übermitteln von Schnittaufträgen aus Clipper](media-services-azure-media-clipper-submit-job.md)
- [Konfigurieren der Lokalisierung](media-services-azure-media-clipper-localization.md)