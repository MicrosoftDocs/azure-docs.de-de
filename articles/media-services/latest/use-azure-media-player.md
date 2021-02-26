---
title: Wiedergabe mit dem Azure Media Player – Azure
description: Azure Media Player ist ein Webvideoplayer für die Wiedergabe von Medieninhalten aus Microsoft Azure Media Services über eine Vielzahl von Browsern und Geräten.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/17/2019
ms.author: inhenkel
ms.openlocfilehash: cf4916341a97868de757804b570212f1cc1105b2
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898120"
---
# <a name="playback-with-azure-media-player"></a>Wiedergabe mit Azure Media Player

Azure Media Player ist ein Webvideoplayer für die Wiedergabe von Medieninhalten aus Microsoft Azure Media Services über eine Vielzahl von Browsern und Geräten. Der Azure Media Player nutzt Industriestandards wie HTML5, MSE (Media Source Extensions) und EME (Encrypted Media Extensions) für ein funktionsreiches adaptives Streaming. Wenn diese Standards auf einem Gerät oder in einem Browser nicht verfügbar sind, verwendet Azure Media Player Flash und Silverlight als Fallback-Technologien. Unabhängig von der verwendeten Wiedergabetechnologie müssen Entwickler eine einheitliche Schnittstelle für JavaScript für den Zugriff auf APIs verwenden. Dies ermöglicht die Inhaltswiedergabe von Azure Media Services über eine breite Palette von Geräten und Browsern ohne jeglichen Zusatzaufwand.

Mit Microsoft Azure Media Services können Inhalte in den Streamingformaten HLS, DASH und Smooth Streaming wiedergegeben werden. Der Azure Media Player erkennt diese unterschiedlichen Formate und übernimmt automatisch die beste Verknüpfung basierend auf den Funktionen von Plattform und Browser. Media Services ermöglicht auch die dynamische Verschlüsselung von Medienobjekten mit PlayReady-Verschlüsselung oder AES-128-Bit-Umschlagverschlüsselung. Der Azure Media Player ermöglicht bei entsprechender Konfiguration die Entschlüsselung von PlayReady- und AES-128-Bit-verschlüsselten Inhalten.

> [!NOTE]
> Für Inhalte mit Widevine-Verschlüsselung ist die HTTPS-Wiedergabe erforderlich.

## <a name="use-azure-media-player-demo-page"></a>Verwenden der Azure Media Player-Demoseite

### <a name="start-using"></a>Jetzt beginnen

Sie können die [Azure Media Player-Demoseite](https://aka.ms/azuremediaplayer) verwenden, um Azure Media Services-Beispiele oder Ihren eigenen Stream wiederzugeben.  

Fügen Sie zum Wiedergeben eines neuen Videos eine andere URL ein, und wählen Sie die Option **Aktualisieren**.

Wählen Sie **Erweiterte Optionen**, um die Wiedergabeoptionen zu konfigurieren (z.B. Technologie, Sprache oder Verschlüsselung).

![Azure Media Player](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Überwachen der Diagnosedaten eines Videostreams

Sie können mithilfe der [Azure Media Player-Demoseite](https://aka.ms/azuremediaplayer) die Diagnosedaten eines Videostreams überwachen.

![Azure Media Player-Diagnose](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Einrichten von Azure Media Player in Ihrem HTML-Code

Die Einrichtung von Azure Media Player ist einfach. Es dauert nur einige Augenblicke, bis die einfache Wiedergabe von Medieninhalten über Ihr Media Services-Konto möglich ist. Ausführliche Informationen zur Einrichtung und Konfiguration von Azure Media Player finden Sie in der [Azure Media Player-Dokumentation](../azure-media-player/azure-media-player-overview.md).

## <a name="additional-notes"></a>Zusätzliche Hinweise

* Widevine ist ein von Google Inc. bereitgestellter Dienst, der den Vertragsbedingungen und der Datenschutzrichtlinie von Google, Inc. unterliegt.

## <a name="next-steps"></a>Nächste Schritte

* [Azure Media Player-Dokumentation](../azure-media-player/azure-media-player-overview.md)
* [Azure Media Player-Beispiele](https://github.com/Azure-Samples/azure-media-player-samples)