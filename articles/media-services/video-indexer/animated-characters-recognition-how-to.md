---
title: 'Erkennung animierter Figuren mit Video Indexer: Vorgehensweise'
titleSuffix: Azure Media Services
description: Hier erfahren Sie, wie Sie die Erkennung animierter Figuren mit Video Indexer verwenden.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.custom: references_regions
ms.topic: how-to
ms.date: 12/07/2020
ms.author: juliako
ms.openlocfilehash: 9effac182acdea6fcb41ed26faf6c2f6535a5cbf
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906164"
---
# <a name="use-the-animated-character-detection-preview-with-portal-and-api"></a>Verwenden der Erkennung animierter Figuren (Vorschau) mithilfe des Portals und der API 

Der Video Indexer-Dienst von Azure Media Services unterstützt die Ermittlung, Gruppierung und Erkennung von Figuren in animierten Inhalten. Diese Funktion ist über das Azure-Portal und die API verfügbar. Lesen Sie diese [Übersicht](animated-characters-recognition.md).

Im vorliegenden Artikel wird veranschaulicht, wie Sie die Erkennung animierter Figuren mit dem Azure-Portal und der Video Indexer-API verwenden.

## <a name="use-the-animated-character-detection-with-portal"></a>Verwenden der Erkennung animierter Figuren über das Portal 

Dieser Abschnitt enthält eine schrittweise Einführung in die Verwendung des Modells für die Erkennung animierter Figuren. 

Da die Custom Vision-Integration in den Testkonten von Video Indexer verwaltet wird, können Sie mit dem Erstellen und Verwenden des animierten Figurenmodells beginnen und den folgenden Abschnitt überspringen („Herstellen einer Verbindung mit Ihrem Custom Video Indexer-Konto“).

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Herstellen einer Verbindung mit Ihrem Custom Vision-Konto (nur kostenpflichtige Konten)

Wenn Sie über ein kostenpflichtiges Video Indexer-Konto verfügen, müssen Sie zunächst eine Verbindung mit einem Custom Vision-Konto herstellen. Sollten Sie noch nicht über ein Custom Vision-Konto verfügen, erstellen Sie eins. Weitere Informationen finden Sie unter [Was ist Azure Custom Vision?](../../cognitive-services/custom-vision-service/overview.md).

> [!NOTE]
> Beide Konten müssen sich in derselben Region befinden. Die Custom Vision-Integration wird in der Region „Japan“ derzeit nicht unterstützt.

#### <a name="connect-a-custom-vision-account-with-api"></a>Verbinden eines Custom Vision-Kontos mit API 

Führen Sie die folgenden Schritte aus, um Ihr Custom Vision-Konto mit Video Indexer zu verbinden, oder um das Custom Vision-Konto zu ändern, das zurzeit mit Video Indexer verbunden ist:

1. Navigieren Sie zu [www.customvision.ai](https://www.customvision.ai), und melden Sie sich an.
1. Kopieren Sie die folgenden Schlüssel: 

    * Trainingsschlüssel (für die Trainingsressource)
    * Vorhersageschlüssel (für die Vorhersageressource)
    * Endpunkt 
    * Vorhersageressourcen-ID
    
    > [!NOTE]
    > Um alle Schlüssel bereitzustellen, benötigen Sie zwei separate Ressourcen in Custom Vision, eine für das Training und eine für die Vorhersage.
1. Wechseln Sie zu [Video Indexer](https://vi.microsoft.com/), und melden Sie sich an.
1. Klicken Sie oben rechts auf der Seite auf das Fragezeichen, und wählen Sie **API-Referenz** aus.
1. Stellen Sie sicher, dass Sie API Management abonniert haben, indem Sie auf die Registerkarte **Produkte** klicken. Wenn Sie über eine verbundene API verfügen, können Sie mit dem nächsten Schritt fortfahren, andernfalls abonnieren Sie eine. 
1. Klicken Sie im Entwicklerportal auf **Vollständige API-Referenz**, und navigieren Sie zu **Vorgänge**.  
1. Wählen Sie **Custom Vision-Konto verbinden (PREVIEW)** aus, und klicken Sie auf **Testen**.
1. Füllen Sie die erforderlichen Felder sowie das Zugriffstoken aus, und klicken Sie auf **Senden**. 

    Weitere Informationen zum Abrufen des Video Indexer-Zugriffstokens finden Sie im [Entwicklerportal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?) sowie in der relevanten [Dokumentation](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api).  
1. Sobald der Aufruf die Antwort „200 OK“ zurückgegeben hat, ist Ihr Konto verbunden.
1. Um Ihre Verbindung zu überprüfen, wechseln Sie zum [Video Indexer](https://vi.microsoft.com/)-Portal:
1. Klicken Sie in der oberen rechten Ecke auf die Schaltfläche **Anpassung des Inhaltsmodells**.
1. Wechseln Sie zur Registerkarte **Animierte Figuren**.
1. Nachdem Sie auf „Modelle verwalten“ in „Custom Vision“ geklickt haben, werden Sie an das Custom Vision-Konto übertragen, mit dem Sie gerade verbunden sind.

> [!NOTE]
> Derzeit werden nur Modelle unterstützt, die über Video Indexer erstellt wurden. Modelle, die mit Custom Vision erstellt wurden, sind nicht verfügbar. Ferner besteht die bewährte Methode darin, Modelle, die mit Video Indexer erstellt wurden, ausschließlich über die Video Indexer-Plattform zu bearbeiten, da mit Custom Vision vorgenommene Änderungen zu unbeabsichtigten Ergebnissen führen können.

### <a name="create-an-animated-characters-model"></a>Erstellen eines Modells für animierte Figuren

1. Navigieren Sie zur [Video Indexer](https://vi.microsoft.com/)-Website, und melden Sie sich an.
1. Klicken Sie in der oberen rechten Ecke der Seite auf die Schaltfläche „Anpassung des Inhaltsmodells“.

    ![Screenshot: Seite „Video Indexer“ mit ausgewählter Schaltfläche für die Anpassung des Inhaltsmodells in der rechten oberen Ecke](./media/animated-characters-recognition/content-model-customization.png)
1. Navigieren Sie im Abschnitt für die Modellanpassung zur Registerkarte **Animated characters** (Animierte Figuren).
1. Klicken Sie auf **Modell hinzufügen**.
1. Benennen Sie das Modell, und drücken Sie die EINGABETASTE, um den Namen zu speichern.

> [!NOTE]
> Es wird empfohlen, für jede animierte Serie jeweils ein eigenes Custom Vision-Modell zu verwenden. 

### <a name="index-a-video-with-an-animated-model"></a>Indizieren eines Videos mit einem animierten Modell

1. Klicken Sie im oberen Menü auf die Schaltfläche **Hochladen**.
1. Wählen Sie ein hochzuladendes Video (Datei oder URL) aus.
1. Klicken Sie auf **Erweiterte Optionen**.
1. Wählen Sie unter **People / Animated characters** (Personen/animierte Figuren) die Option **Animation models** (Animationsmodelle) aus.
1. Wenn Sie über ein einzelnes Modell verfügen, wird es automatisch ausgewählt. Sind mehrere Modelle vorhanden, können Sie das passende Modell über das Dropdownmenü auswählen.
1. Klicken Sie auf „Hochladen“.
1. Nachdem das Video indiziert wurde, werden die erkannten Figuren im Abschnitt **Animated characters** (Animierte Figuren) im Bereich **Erkenntnisse** angezeigt.

> [!NOTE] 
> Vor dem Taggen und Trainieren des Modells werden alle Figuren mit „Unknown #X“ (Unbekannt #X) benannt. Nachdem Sie das Modell trainiert haben, werden sie auch erkannt.

### <a name="customize-the-animated-characters-models"></a>Anpassen der Modelle für animierte Figuren

1. Taggen und trainieren Sie das Modell.

    1. Taggen Sie die erkannte Figur, indem Sie ihren Namen bearbeiten. Sobald das Modell mit einer Figur trainiert wurde, wird sie im nächsten Video erkannt, das mit diesem Modell indiziert wird. 
    1. Um eine animierte Figur in Ihrem Video zu taggen, navigieren Sie zur Registerkarte **Erkenntnisse**, und klicken Sie rechts oben im Fenster auf die Schaltfläche **Bearbeiten**.
    1. Klicken Sie im Bereich **Erkenntnisse** auf eine der erkannten animierten Figuren, und ändern Sie ihren Namen von „Unknown #X“ (Unbekannt #X) bzw. dem Namen, der der Figur zuvor zugewiesen wurde, in einen anderen Namen.
    1. Nachdem Sie den neuen Namen eingegeben haben, klicken Sie neben dem neuen Namen auf das Prüfsymbol. Dadurch wird der neue Name in dem Modell in Video Indexer gespeichert.
    1. Nachdem Sie alle gewünschten Namen bearbeitet haben, müssen Sie das Modell trainieren.

        Öffnen Sie die Anpassungsseite, klicken Sie auf die Registerkarte für **animierte Figuren**, und klicken Sie anschließend auf die Schaltfläche **Trainieren**, um Ihr Modell zu trainieren.
         
        Wenn Sie über ein kostenpflichtiges Konto verfügen, können Sie auf den Link **Verwalten von Modellen in Customer Vision** klicken (wie unten gezeigt). Daraufhin werden Sie zur Seite des Modells in **Custom Vision** weitergeleitet.
 
        ![Anpassung des Inhaltsmodells](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. Nach dem Trainieren werden die trainierten Figuren in allen Videos erkannt, die erstmals oder erneut mit diesem Modell indiziert werden. 
    Bei kostenpflichtigen Konten mit Zugriff auf das Custom Vision-Konto können die Modelle und getaggten Bilder dort angezeigt werden. Weitere Informationen zur Verbesserung Ihrer Klassifizierung in Custom Vision finden Sie [hier](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md).

1. Löschen Sie eine animierte Figur.

    1. Um eine animierte Figur aus Ihren Videoerkenntnissen zu löschen, navigieren Sie zur Registerkarte **Erkenntnisse**, und klicken Sie rechts oben im Fenster auf die Schaltfläche **Bearbeiten**.
    1. Wählen Sie die animierte Figur aus, und klicken Sie anschließend unter ihrem Namen auf die Schaltfläche **Löschen**.

    > [!NOTE]
    > Dadurch wird die Erkenntnis aus diesem Video gelöscht. Das Modell bleibt davon unberührt.

1. Löschen Sie ein Modell.

    1. Klicken Sie im oberen Menü auf die Schaltfläche **Anpassung des Inhaltsmodells**, und navigieren Sie zur Registerkarte **Animated characters** (Animierte Figuren).
    1. Klicken Sie rechts neben dem Modell, das Sie löschen möchten, auf die Auslassungspunkte und anschließend auf die Schaltfläche „Löschen“.
    
    * Kostenpflichtiges Konto: Die Verbindung zwischen Modell und Video Indexer wird getrennt und kann nicht wiederhergestellt werden.
    * Testkonto: Das Modell wird auch aus Custom Vision gelöscht. 
    
        > [!NOTE]
        > Bei einem Testkonto steht nur ein einzelnes Modell zur Verfügung. Nachdem Sie es gelöscht haben, können Sie keine weiteren Modelle trainieren.

## <a name="use-the-animated-character-detection-with-api"></a>Verwenden der Erkennung animierter Figuren über die API 

1. Stellen Sie eine Verbindung mit einem Custom Vision-Konto her.

    Wenn Sie über ein kostenpflichtiges Video Indexer-Konto verfügen, müssen Sie zunächst eine Verbindung mit einem Custom Vision-Konto herstellen. <br/>
    Sollten Sie noch nicht über ein Custom Vision-Konto verfügen, müssen Sie eins erstellen. Weitere Informationen finden Sie unter [Was ist Azure Custom Vision?](../../cognitive-services/custom-vision-service/overview.md).

    [Stellen Sie unter Verwendung der API eine Verbindung mit Ihrem Custom Vision-Konto her.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag)
1. Erstellen Sie ein Modell für animierte Figuren.

    Verwenden Sie die [API zum Erstellen von Animationsmodellen](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag).
1. Indizieren Sie ein Video (erstmals oder erneut).

    Verwenden Sie die [API für die erneute Indizierung](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?). 
1. Passen Sie die Modelle für animierte Figuren an.

    Verwenden Sie die [API zum Trainieren von Animationsmodellen](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag).

### <a name="view-the-output"></a>Anzeigen der Ausgabe

Sehen Sie sich die animierten Figuren in der generierten JSON-Datei an.

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>Einschränkungen

* Die Animationserkennung wird momentan in der Region „Asien, Osten“ nicht unterstützt.
* Kleine oder weit entfernte Figuren werden in Videos mit schlechter Qualität möglicherweise nicht ordnungsgemäß erkannt.
* Es wird empfohlen, für jede Gruppe von animierten Figuren (beispielsweise pro animierter Serie) ein eigenes Modell zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

[What is Video Indexer? (preview)](video-indexer-overview.md) (Was ist Video Indexer? (Vorschauversion))
