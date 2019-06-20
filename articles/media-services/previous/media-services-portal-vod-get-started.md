---
title: Erste Schritte zum Bereitstellen von Video On Demand über das Azure-Portal | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie einen einfachen Dienst zur Übermittlung von Video On Demand-Inhalten mit einer Azure Media Services-Anwendung in Azure-Portal implementieren.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 6c98fcfa-39e6-43a5-83a5-d4954788f8a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 5df666dc2bd574c168d6b5f65dd6a909564a921f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64868428"
---
# <a name="get-started-with-delivering-content-on-demand-by-using-the-azure-portal"></a>Erste Schritte zum Bereitstellen von On Demand-Inhalten über das Azure-Portal

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie einen einfachen Dienst zur Übermittlung von Video On Demand-Inhalten mit einer Azure Media Services-Anwendung in Azure-Portal implementieren.

## <a name="prerequisites"></a>Voraussetzungen
Für dieses Tutorial benötigen Sie Folgendes:

* Ein Azure-Konto. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/). 
* Media Services-Konto. Informationen zum Erstellen eines Media Services-Kontos finden Sie unter [Erstellen eines Azure Media Services-Kontos mithilfe des Azure-Portals](media-services-portal-create-account.md).

Dieses Lernprogramm enthält die folgenden Aufgaben:

1. Starten des Streamingendpunkts
2. Hochladen einer Videodatei
3. Codieren der Quelldatei in einen Satz von MP4-Dateien mit adaptiver Bitrate
4. Veröffentlichen des Medienobjekts und Abrufen von URLs für Streaming und progressiven Download  
5. Wiedergeben Ihrer Inhalte

## <a name="start-the-streaming-endpoint"></a>Starten des Streamingendpunkts

Eines der gängigsten Szenarien bei der Verwendung von Azure Media Services ist die Übertragung von Videos mittels Adaptive Bitrate Streaming. Media Services bietet eine dynamische Paketerstellung. Mit der dynamischen Paketerstellung können Sie Ihre mit adaptiver Bitrate codierten MP4-Inhalte in Just-In-Time-Streamingformaten übermitteln, die von Media Services unterstützt werden. Beispiele hierfür sind Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming und Dynamic Adaptive Streaming über HTTP (DASH oder MPEG-DASH). Mithilfe von Adaptive Bitrate Streaming von Media Services können Sie Ihre Videos übermitteln, ohne vorab gepackte Versionen der einzelnen Streamingformate zu speichern.

> [!NOTE]
> Beim Erstellen Ihres Media Services-Kontos wird Ihrem Konto ein Standard-Streamingendpunkt im Zustand **Beendet** hinzugefügt. Wenn Sie mit dem Streamen Ihrer Inhalte beginnen und die dynamische Paketerstellung sowie die dynamische Verschlüsselung verwenden möchten, muss der Streamingendpunkt, von dem Sie Inhalte streamen möchten, in den Zustand **Wird ausgeführt** versetzt werden. 

So starten Sie den Streamingendpunkt:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie **Einstellungen** > **Streamingendpunkte** aus. 
3. Wählen Sie den Standard-Streamingendpunkt aus. Das Fenster **DEFAULT STREAMING ENDPOINT DETAILS** (DETAILS ZUM STANDARD-STREAMINGENDPUNKT) wird angezeigt.
4. Wählen Sie das Symbol **Starten** aus.
5. Wählen Sie die Schaltfläche **Speichern** aus.

## <a name="upload-files"></a>Hochladen von Dateien
Um Videos mit Media Services streamen zu können, müssen Sie die Quellvideos hochladen, mit mehreren Bitraten codieren und das Ergebnis veröffentlichen. Der erste Schritt ist in diesem Abschnitt beschrieben. 

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihr Azure Media Services-Konto aus.
2. Wählen Sie **Einstellungen** > **Ressourcen**. Wählen Sie die Schaltfläche **Hochladen**.
   
    ![Hochladen von Dateien](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    Das Fenster **Upload a video asset** (Videoobjekt hochladen) wird angezeigt.
   
   > [!NOTE]
   > Die Dateigröße für den Videoupload ist für Media Services nicht begrenzt.
   > 
   > 
3. Navigieren Sie auf Ihrem Computer zu dem Video, das Sie hochladen möchten. Wählen Sie das Video und anschließend **OK** aus.  
   
    Der Upload beginnt. Der Status wird unter dem Dateinamen angezeigt.  

Nach Abschluss des Uploadvorgangs wird das neue Medienobjekt im Bereich **Assets** (Medienobjekte) angezeigt. 

## <a name="encode-assets"></a>Codieren von Medienobjekten
Für die dynamische Paketerstellung müssen Sie Ihre Quelldatei als einen Satz von MP4-Dateien mit Mehrfachbitrate codieren. Die Codierungsschritte werden in diesem Abschnitt gezeigt.

### <a name="encode-assets-in-the-portal"></a>Codieren von Medienobjekten im Portal
So codieren Sie Ihre Inhalte mithilfe von Media Encoder Standard im Azure-Portal:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihr Azure Media Services-Konto aus.
2. Wählen Sie **Einstellungen** > **Ressourcen**. Wählen Sie das Medienobjekt aus, das Sie codieren möchten.
3. Wählen Sie die Schaltfläche **Codieren** aus.
4. Wählen Sie im Bereich **Medienobjekt codieren** den Prozessor **Media Encoder Standard** und eine Voreinstellung aus. Informationen zu Voreinstellungen finden Sie unter [Verwenden von Azure Media Encoder Standard zum automatischen Generieren einer Reihe von Bitraten](media-services-autogen-bitrate-ladder-with-mes.md) sowie unter [Aufgabenvoreinstellungen für MES (Media Encoder Standard)](media-services-mes-presets-overview.md). Wählen Sie die Voreinstellung aus, die am besten zu Ihrem Eingabevideo passt. Wenn Sie also beispielsweise wissen, dass das Eingabevideo eine Auflösung von 1920 &#215; 1080 Pixel hat, sollten Sie die Voreinstellung **H264 Multiple Bitrate 1080p** wählen. Bei einem Video mit geringer Auflösung (640 &#215; 360) sollte die Voreinstellung **H264 Multiple Bitrate 1080p** nicht verwendet werden.
   
   Zur einfacheren Ressourcenverwaltung können Sie den Namen des Ausgabemedienobjekts und den Namen des Auftrags bearbeiten.
   
   ![Codieren von Medienobjekten](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Klicken Sie auf **Erstellen**.

### <a name="monitor-encoding-job-progress"></a>Überwachen des Codierauftragsstatus
Sie können den Status des Codierungsauftrags überwachen, indem Sie oben auf der Seite die Option **Einstellungen** und anschließend **Aufträge** auswählen.

![Aufträge](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Veröffentlichen von Inhalten
Damit Sie Benutzern eine URL zum Streamen oder Herunterladen Ihrer Inhalte zur Verfügung stellen können, müssen Sie zunächst einen Locator erstellen, um das Medienobjekt zu veröffentlichen. Locators ermöglichen den Zugriff auf Dateien im Medienobjekt. Azure Media Services unterstützt zwei Arten von Locators: 

* **Streaminglocators (OnDemandOrigin)** . Streaminglocators werden für adaptives Streaming verwendet. Beispiele für adaptives Streaming sind HLS, Smooth Streaming und MPEG-DASH. Für die Erstellung eines Streaminglocators muss Ihr Medienobjekt eine ISM-Datei enthalten. 
* **Progressive Locators (Shared Access Signature, SAS)** . Progressive Locators werden verwendet, um Videos per progressivem Download bereitzustellen.

Eine HLS-Streaming-URL können Sie erstellen, indem Sie *(format=m3u8-aapl)* an die URL anhängen:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)

Eine Streaming-URL für die Wiedergabe von Smooth Streaming-Medienobjekten hat das folgende Format:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest

Eine MPEG-DASH-Streaming-URL können Sie erstellen, indem Sie *(format=mpd-time-csf)* an die URL anhängen:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)

Eine SAS-URL hat das folgende Format:

    {blob container name}/{asset name}/{file name}/{shared access signature}

> [!NOTE]
> Locators, die vor März 2015 über das Azure-Portal erstellt wurden, laufen nach zwei Jahren ab.  
> 
> 

Das Ablaufdatum eines Locators kann mithilfe einer [REST-API](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) oder mithilfe einer [.NET-API](https://go.microsoft.com/fwlink/?LinkID=533259) aktualisiert werden. 

> [!NOTE]
> Wenn Sie das Ablaufdatum eines SAS-Locators aktualisieren, ändert sich auch die URL.

### <a name="to-use-the-portal-to-publish-an-asset"></a>So verwenden Sie das Portal zum Veröffentlichen eines Objekts
1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihr Azure Media Services-Konto aus.
2. Wählen Sie **Einstellungen** > **Ressourcen**. Wählen Sie das Objekt aus, das Sie veröffentlichen möchten.
3. Wählen Sie die Schaltfläche **Veröffentlichen** aus.
4. Wählen Sie den Locatortyp aus.
5. Wählen Sie **Hinzufügen**.
   
    ![Veröffentlichen des Videos](./media/media-services-portal-vod-get-started/media-services-publish1.png)

Die URL wird der Liste mit den **veröffentlichten URLs** hinzugefügt.

## <a name="play-content-from-the-portal"></a>Wiedergeben von Inhalten im Portal
Sie können Ihr Video in einem Inhaltsplayer im Azure-Portal testen.

Wählen Sie das Video und anschließend die Schaltfläche **Wiedergabe** aus.

![Wiedergeben des Videos im Azure-Portal](./media/media-services-portal-vod-get-started/media-services-play.png)

Folgende Überlegungen sollten berücksichtigt werden:

* Starten Sie den Standard-Streamingendpunkt, um mit dem Streamen zu beginnen.
* Vergewissern Sie sich, dass das Video veröffentlicht wurde.
* Der Media Player des Azure-Portals verwendet für die Wiedergabe den Standard-Streamingendpunkt. Wenn Sie einen anderen Streamingendpunkt für die Wiedergabe verwenden möchten, können Sie die URL markieren, kopieren und in einen anderen Player einfügen. So können Sie Ihr Video beispielsweise mit dem [Azure Media Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html) testen.

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nächste Schritte
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
