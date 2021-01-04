---
title: 'Registrieren für Video Indexer und Hochladen Ihres ersten Videos: Azure'
titleSuffix: Azure Media Services
description: Erfahren Sie, wie Sie sich beim Video Indexer-Portal registrieren und Ihr erstes Video hochladen.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 10/30/2020
ms.author: juliako
ms.openlocfilehash: 7099b0d303965b987fb316170738cc82568dc6d6
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94505596"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Schnellstart: Registrieren und Hochladen Ihres ersten Videos

In dieser Schnellstartanleitung zu den ersten Schritten wird veranschaulicht, wie Sie sich bei der Video Indexer-Website anmelden und Ihr erstes Video hochladen.

Beim Erstellen eines Video Indexer-Kontos können Sie ein kostenloses Testkonto (mit einer bestimmten Anzahl von kostenlosen Indizierungsminuten) oder eine kostenpflichtige Option wählen (ohne Einschränkung durch eine Kontingentvorgabe). Bei der kostenlosen Testversion stellt Video Indexer bis zu 600 Minuten an kostenloser Indizierungszeit für Websitebenutzer und bis zu 2.400 Minuten an kostenloser Indizierungszeit für API-Benutzer bereit. Bei der kostenpflichtigen Option erstellen Sie ein Video Indexer-Konto, [das mit Ihrem Azure-Abonnement und einem Azure Media Services-Konto verbunden ist](connect-to-azure.md). Sie bezahlen pro Minute Indizierungszeit. Weitere Informationen finden Sie unter [Media Services – Preise](https://azure.microsoft.com/pricing/details/media-services/). 

## <a name="sign-up-for-video-indexer"></a>Registrieren bei Video Indexer

Um mit der Entwicklung mit Video Indexer zu beginnen, müssen Sie sich bei der [Video Indexer](https://www.videoindexer.ai/)-Website registrieren.

Nachdem Sie mit der Nutzung von Video Indexer begonnen haben, werden Ihre gesamten gespeicherten Daten und hochgeladenen Inhalte im ruhenden Zustand mit einem von Microsoft verwalteten Schlüssel verschlüsselt.

> [!NOTE]
> Lesen Sie die Informationen zu den [geplanten Änderungen an der Video Indexer-Websiteauthentifizierung](release-notes.md#planned-video-indexer-website-authenticatication-changes).

## <a name="upload-a-video-using-the-video-indexer-website"></a>Hochladen eines Videos auf der Video Indexer-Website

### <a name="supported-file-formats-for-video-indexer"></a>Unterstützte Dateiformate für Video Indexer

Im Artikel [Eingabecontainer/Dateiformate](../latest/media-encoder-standard-formats.md#input-containerfile-formats) finden Sie eine Liste der Dateiformate, die Sie mit Video Indexer verwenden können.

### <a name="upload-a-video"></a>Hochladen eines Videos

1. Melden Sie sich bei der [Video Indexer](https://www.videoindexer.ai/)-Website an.
1. Klicken Sie auf die Schaltfläche bzw. den Link **Hochladen**, um ein Video hochzuladen.

    > [!NOTE]
    > Der Name des Videos darf nicht mehr als 80 Zeichen umfassen.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="Upload":::
1. Nachdem Ihr Video hochgeladen wurde, beginnt Video Indexer mit dem Indizieren und Analysieren des Videos. Der Status wird angezeigt. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="Uploadstatus":::
1. Wenn Video Indexer die Analyse abgeschlossen hat, erhalten Sie eine E-Mail mit einem Link zu Ihrem Video und einer kurzen Beschreibung dazu, was in Ihrem Video gefunden wurde. Beispiel: Personen, gesprochene und geschriebene Wörter, Themen und benannte Entitäten.
1. Ihr Video ist später dann in der Bibliotheksliste aufgeführt, und Sie können unterschiedliche Vorgänge durchführen. Beispiel: Suchen, Erneutes Indizieren, Bearbeiten.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/uploaded.png" alt-text="Abgeschlossener Uploadvorgang":::
 
## <a name="see-also"></a>Weitere Informationen

Weitere Informationen finden Sie unter [Hochladen und Indizieren Ihrer Videos](upload-index-videos.md).

Nach dem Hochladen und Indizieren eines Videos können Sie die [Video Indexer-Website](video-indexer-view-edit.md) oder das [Video Indexer-Entwicklerportal](video-indexer-use-apis.md) verwenden, um die für das Video gewonnenen Erkenntnisse anzuzeigen. 

[Einstieg in APIs](video-indexer-use-apis.md)

## <a name="next-steps"></a>Nächste Schritte

Eine ausführliche Einführung erhalten Sie in unserem entsprechenden [Lab](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md). 

Am Ende des Workshops sind Sie gut darüber informiert, welche Arten von Informationen aus Video- und Audioinhalten extrahiert werden können. Sie sind dann besser vorbereitet, um Chancen in Bezug auf „Content Intelligence“ zu erkennen, KI-Funktionen für Videos in Azure vorzustellen und verschiedene Szenarien in Video Indexer zu demonstrieren.

