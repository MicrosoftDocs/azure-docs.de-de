---
title: Verwenden vorhandener Player für die Wiedergabe Ihrer Inhalte – Azure | Microsoft-Dokumentation
description: In diesem Artikel finden Sie vorhandene Player, die Sie zum Wiedergeben Ihrer Inhalte verwenden können.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2020
ms.author: inhenkel
ms.openlocfilehash: f15e2553ec3f3beed4dae809cc6c37f01837a40c
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302905"
---
# <a name="playing-your-content-with-existing-players"></a>Wiedergabe Ihrer Inhalte mit vorhandenen Playern

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure Media Services unterstützt zahlreiche gängige Streamingformate wie Smooth Streaming, HTTP Live Streaming und MPEG-Dash. In diesem Thema finden Sie Verweise auf vorhandene Player, die Sie zum Testen von Datenströmen verwenden können.

## <a name="the-azure-portal-media-services-content-player"></a>Der Media Services-Inhaltsplayer im Azure-Portal

Im **Azure** -Portal wird ein Inhaltsplayer bereitgestellt, mit dem Sie Ihre Videos testen können.

Klicken Sie auf den gewünschten Videoinhalt (vergewissern Sie sich, dass er [veröffentlicht](media-services-portal-publish.md)wurde), und klicken Sie auf die Schaltfläche **Abspielen** am unteren Rand des Portals.

Folgende Überlegungen sollten berücksichtigt werden:

* Der **MEDIA SERVICES-INHALTSPLAYER** gibt die Inhalt vom standardmäßigen Streamingendpunkt wieder. Wenn Sie die Wiedergabe von einem anderen Streamingendpunkt starten möchten, verwenden Sie einen anderen Player. Beispiel: [Azure Media Player](https://aka.ms/azuremediaplayer).

### <a name="azure-media-player"></a>Azure Media Player

Verwenden Sie [Azure Media Player](https://aka.ms/azuremediaplayer) zum Wiedergeben Ihrer Inhalte (offen oder geschützt) in den folgenden Formaten:

* Smooth Streaming
* MPEG DASH
* HLS
* Progressive MP4

### <a name="flash-player"></a>Flash Player

#### <a name="playready-with-token"></a>PlayReady mit Token

[http://reference.dashif.org/dash.js/nightly/samples/dash-if-reference-player/index.html](http://reference.dashif.org/dash.js/nightly/samples/dash-if-reference-player/index.html)

### <a name="dash-players"></a>DASH-Player

[DASH-Player](http://players.akamai.com/players/dashjs)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>Andere

Sie können zum Testen von HLS-URLs auch Folgendes verwenden:

* **Safari** auf einem iOS-Gerät oder
* **3ivx HLS Player** unter Windows.

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
