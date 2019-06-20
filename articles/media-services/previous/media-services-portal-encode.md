---
title: Codieren eines Medienobjekts mit Media Encoder Standard im Azure-Portal | Microsoft-Dokumentation
description: Dieses Tutorial führt Sie durch die Schritte zur Codierung eines Medienobjekts mit Media Encoder Standard im Azure-Portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 90190f426419e65bd580b9004ae76a2c6b0c12e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463146"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Codieren eines Medienobjekts mit Media Encoder Standard im Azure-Portal

> [!NOTE]
> Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Bei der Arbeit mit Azure Media Services ist eines der häufigsten Szenarien das Streaming mit adaptiver Bitrate an Clients. Media Services unterstützt die folgenden Technologien mit Adaptive Bitrate Streaming: Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming und Dynamic Adaptive Streaming über HTTP (DASH oder MPEG-DASH). Um die Videos für das Streaming mit adaptiver Bitrate vorzubereiten, müssen Sie zuerst das Quellvideo in Dateien mit mehreren Bitraten codieren. Mit Azure Media Encoder Standard können Sie Ihre Videos codieren.  

Media Services bietet eine dynamische Paketerstellung. Mit der dynamischen Paketerstellung können Sie Ihre MP4-Dateien mit mehreren Bitraten in HLS, Smooth Streaming und MPEG-DASH ohne erneutes Packen in diesen Streamingformaten übermitteln. Mit der dynamischen Paketerstellung können Sie die Dateien in einem einzigen Format speichern und zahlen auch nur für dieses eine Format. Media Services reagiert mit der richtigen Antwort auf die Anforderung eines Clients.

Für die dynamische Paketerstellung müssen Sie Ihre Quelldatei als einen Satz von MP4-Dateien mit Mehrfachbitrate codieren. Die Codierungsschritte werden später in diesem Artikel gezeigt.

Unter [Ändern Sie den Typ reservierter Einheiten](media-services-portal-scale-media-processing.md) erhalten Sie Informationen über das Skalieren der Medienverarbeitung.

## <a name="encode-in-the-azure-portal"></a>Codieren im Azure-Portal

So codieren Sie Ihre Inhalte mithilfe von Media Encoder Standard:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihr Azure Media Services-Konto aus.
2. Wählen Sie **Einstellungen** > **Ressourcen**. Wählen Sie das Medienobjekt aus, das Sie codieren möchten.
3. Wählen Sie die Schaltfläche **Codieren** aus.
4. Wählen Sie im Bereich **Medienobjekt codieren** den Prozessor **Media Encoder Standard** und eine Voreinstellung aus. Informationen zu Voreinstellungen finden Sie unter [Verwenden von Azure Media Encoder Standard zum automatischen Generieren einer Reihe von Bitraten](media-services-autogen-bitrate-ladder-with-mes.md) sowie unter [Aufgabenvoreinstellungen für MES (Media Encoder Standard)](media-services-mes-presets-overview.md). Wählen Sie die Voreinstellung aus, die am besten zu Ihrem Eingabevideo passt. Wenn Sie also beispielsweise wissen, dass das Eingabevideo eine Auflösung von 1920 &#215; 1080 Pixel hat, sollten Sie die Voreinstellung **H264 Multiple Bitrate 1080p** wählen. Bei einem Video mit geringer Auflösung (640 &#215; 360) sollte die Voreinstellung **H264 Multiple Bitrate 1080p** nicht verwendet werden.
   
   Zur einfacheren Ressourcenverwaltung können Sie den Namen des Ausgabemedienobjekts und den Namen des Auftrags bearbeiten.
   
   ![Codieren von Medienobjekten](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Klicken Sie auf **Erstellen**.

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nächste Schritte
* [Überwachen des Codierauftragsstatus mithilfe des Azure-Portals](media-services-portal-check-job-progress.md).  

