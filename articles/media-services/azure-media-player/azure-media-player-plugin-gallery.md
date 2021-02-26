---
title: Katalog mit Azure Media Player-Plug-Ins
description: Dieser Artikel enthält eine Liste der für Azure Media Player verfügbaren Plug-Ins.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 71fa79cb8847d16ac0890f9aba647cb8f5e2e444
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089341"
---
# <a name="azure-media-player-plugin-gallery"></a>Katalog mit Azure Media Player-Plug-Ins #

## <a name="plugins"></a>Plug-Ins ##

| Plug-In-Name                         | Demo-URL                    | Quellcode                | BESCHREIBUNG    |
|-------------------------------------|-----------------------------|----------------------------|----------------|
| Zusätzliche Funktionen                 | | | |
| **Neu!** AMP360Video                | [Demo](http://www.babylonjs.com/demos/amp360video/)                        | [GitHub](https://github.com/BabylonJS/Extensions/tree/master/Amp360Video)                     | Mit diesem Plug-In können Sie 360-Grad-Videos in AMP entweder auf Ihrem Desktop oder auf VR-kompatiblen Geräten visualisieren. Die vollständige Dokumentation finden Sie [hier](https://doc.babylonjs.com/extensions/amp360video). |
|  Sprite Tip (Sprite-Tipp)                         | [Demo](https://www.smwcentral.net/?p=section&a=details&id=10301)                        | [GitHub](https://github.com/RickShahid/SpriteTip)                    | AMP-Plug-In (Azure Media Player) für das Zeitachsenrendering eines Videominiaturbild-Sprite, das auf der Grundlage von Media Encoder Standard (MES) in Azure Media Services (AMS) generiert wird |
| Diagnostics Overlay (Diagnoseüberlagerung)                 | [Demo](https://openidconnectweb.azurewebsites.net/Diagnoverlay.html)                        | [GitHub](https://github.com/willzhan/diagnoverlay)                     | Mit diesem Plug-In wird Folgendes angezeigt: alle Schlüsselparameter, Videostatistiken, alle Ereignisse im Lebenszyklus der Videowiedergabe sowie DRM-Schutzinformationen wie Schlüssel-ID und Lizenzerwerbs-URLs, sofern diese geschützt sind.                                                                                                                                                                      |
| Frame rate and Timecode calculator (Rechner für Bildfrequenz und Zeitcode) | Keine Demo verfügbar | [GitHub](https://github.com/mconverti/media-services-javascript-azure-media-player-framerate-timecode-calculator-plugin)                     | Dieses Plug-In berechnet die Bildfrequenz eines Videos auf der Grundlage der MP4-Boxen `tfhd`/`trun` des ersten MPEG-DASH-Videofragments, analysiert den Zeitskalenwert aus dem MPEG-DASH-Clientmanifest und bietet zudem die Möglichkeit, den Zeitcode für eine bestimmte absolute Zeit des Players zu generieren. (Außerdem gibt das Plug-In die absolute Zeit des Players auf Basis des Zeitcodes an.) |
| <strike>Wiedergabegeschwindigkeit</strike>                      | [Demo](https://azure-samples.github.io/media-services-javascript-Azure-Media-Player-playback-rate-plugin/)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Mit diesem Plug-In können Betrachter die Geschwindigkeit des Videos steuern. *Beachten Sie, dass diese Funktion in Version AMP v2.0.0 und höheren Versionen automatisch verfügbar, aber standardmäßig deaktiviert ist.* Informationen zum Aktivieren dieser Funktion finden Sie in [diesen Beispielen](https://github.com/Azure-Samples/azure-media-player-samples). |
| Hover Time Tip (Info zur Dauer der Mauszeigerbewegung)                      | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Dieses Plug-In zeigt über der Statusleiste Informationen zur Dauer der Mauszeigerbewegung für zeitgenaues Suchen an. *Hinweis: Dieses Plug-In ist bereits in AMP integriert*. Wenn Sie sich für seine Programmierung interessieren, können Sie es sich jedoch genauer ansehen.                                                                                                                 |
| Titelüberlagerung                       | [Demo](https://azure-samples.github.io/media-services-javascript-azure-media-player-title-overlay-plugin)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-title-overlay-plugin)                     | Mit diesem Plug-In werden konfigurierbare Videotitel auf dem Bildschirm überlagert. |
| Timeline Markers (Zeitachsenmarker)                    | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timelinemarkers/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-timeline-markers-plugin)                     | Dieses Plug-In akzeptiert ein Array von Zeitangaben und überlagert auf der Statusleiste kleine Marker zu diesen Zeiten. |
| Analytics                           | | | |
| Application Insights                | [Blogbeitrag](https://azure.microsoft.com/blog/player-analytics-azure-media-player-plugin/)                   | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-application-insights-plugin)                     | Ein Plug-In, das Ihre Player-Metriken nachverfolgt und für eine intuitive grafische Darstellung der Player-Benutzeroberfläche Ihrer Betrachter in Power BI überträgt |
| Google Analytics                    | –                         | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-google-analytics-plugin)                     | Google Analytics-Plug-In für Azure Media Player |
| Diagnose                         | | | |
| Diagnostics Output (Diagnoseausgabe)                  | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/diagnosticslogger/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-diagnostic-logger-plugin)                     | Dieses Plug-In gibt ein Array von Diagnosen von Ihrem Player aus. Wenn Sie es in Aktion sehen möchten, rufen Sie den Demolink auf, und öffnen Sie Ihre JavaScript-Konsole. |
| Erleichterte Bedienung                      | | | |
| Vergrößern                             | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/zoom/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-zoom-plugin)                     | Mit diesem Plug-In wird eine ziehbare Vergrößerungsskala auf dem Bildschirm des Players angezeigt, sodass Betrachter Ihre Inhalte vergrößern können. |
| Live Captions (Liveuntertitel)                       | [Azure-Blogbeitrag](https://azure.microsoft.com/blog/live-real-time-captions-with-azure-media-services-and-player/), [SubPLY-Beitrag](http://www.subply.com/en/Products/AzureLiveCaptions.htm) | – | *Weitere Infos finden Sie in den Beiträgen.* End-to-End-Workflow für das Plug-In zur Liveuntertitelung in Azure Media Player. Klicken Sie auf den Link ganz links, um die SubPly-Website aufzurufen und mehr über die Lösung zu erfahren. |
| Abkürzungstasten                            | <strike>[Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)</strike>                        | <strike>[GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-hot-keys-plugin)</strike>                     | Mit dem Plug-In für Abkürzungstasten können Betrachter verschiedene Aspekte des Players mit generischen Plug-In-Kombinationen steuern, etwa F für „Fullscreen“ (Vollbild), M für „Mute“ (Stumm) und Pfeiltasten für die Steuerung der Statusleiste. *Hinweis: Dieses Plug-In wurde bereits in AMP integriert, kann aber als Ressource verwendet werden.* |
| Soziale Netzwerke                              | | | |
| Freigabe                               | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/share/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-social-share-plugin)                     | Dieses Plug-In fügt der Steuerleiste des Players eine Freigabeschaltfläche hinzu, damit Ihre Betrachter das angezeigte Video über Facebook, Twitter oder LinkedIn mit Freunden teilen können. |

## <a name="next-steps"></a>Nächste Schritte ##

- [Schnellstart für Azure Media Player](azure-media-player-quickstart.md)
