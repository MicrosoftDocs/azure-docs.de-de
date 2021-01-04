---
title: Durchführen von Livestreaming mit lokalen Encodern mit dem Azure-Portal | Microsoft-Dokumentation
description: In diesem Tutorial werden Sie durch die Schritte zum Erstellen eines Kanals geführt, der für eine Pass-Through-Bereitstellung konfiguriert ist.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 6f4acd95-cc64-4dd9-9e2d-8734707de326
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 707c12cac6bbceee925c4710eff29482f687d47f
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040729"
---
# <a name="perform-live-streaming-with-on-premises-encoders-using-azure-portal"></a>Durchführen von Livestreaming mit lokalen Encodern mit dem Azure-Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Portal](media-services-portal-live-passthrough-get-started.md)
> * [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
> * [REST](/rest/api/media/operations/channel)
> 
> 

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](../latest/index.yml) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

In diesem Tutorial werden Sie durch die Schritte des Azure-Portals zum Erstellen eines **Kanals** geführt, der für eine Pass-Through-Bereitstellung konfiguriert ist. 

## <a name="prerequisites"></a>Voraussetzungen
Zum Abschließen dieses Lernprogramms müssen folgende Voraussetzungen erfüllt sein:

* Ein Azure-Konto. Ausführliche Informationen finden Sie unter [Einen Monat kostenlos testen](https://azure.microsoft.com/pricing/free-trial/). 
* Media Services-Konto. Informationen zum Erstellen eines Media Services-Kontos finden Sie unter [Gewusst wie: Erstellen eines Media Services Kontos](media-services-portal-create-account.md).
* Eine Webcam. Beispielsweise den [Telestream Wirecast-Encoder](media-services-configure-wirecast-live-encoder.md). 

Wir empfehlen Ihnen dringend, die folgenden Artikel zu lesen:

* [Azure Media Services RTMP Support and Live Encoders (in englischer Sprache).](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
* [Übersicht über Livestreaming mit Azure Media Services](media-services-manage-channels-overview.md)
* [Livestreaming mit lokalen Encodern, die Datenströme mit Mehrfachbitrate erstellen](media-services-live-streaming-with-onprem-encoders.md)

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Allgemeines Livestreamingszenario

In den folgenden Schritten werden Aufgaben beschrieben, die beim Erstellen von gängigen Livestreaming-Anwendungen mit Kanälen ausgeführt werden, die für die Pass-Through-Bereitstellung konfiguriert sind. In diesem Tutorial wird veranschaulicht, wie Sie einen Pass-Through-Kanal und Liveereignisse erstellen und verwalten.

> [!NOTE]
> Stellen Sie sicher, dass sich der Streamingendpunkt, von dem aus Sie die Inhalte streamen möchten, im Status **Wird ausgeführt** befindet. 
    
1. Schließen Sie eine Videokamera an einen Computer an. <br/>Ausrüstungsanregungen finden Sie unter [Simple and Portable Event Video Gear Setup]( https://link.medium.com/KNTtiN6IeT) (Einfache und tragbare Videoausrüstung für Veranstaltungen).
1. Starten und konfigurieren Sie einen lokalen Liveencoder, der einen RTMP- oder Fragmented MP4-Datenstrom mit mehreren Bitraten ausgibt. Weitere Informationen finden Sie unter [Microsoft Azure Media Services RTMP-Support und Liveencoder](https://go.microsoft.com/fwlink/?LinkId=532824).<br/>Sehen Sie sich auch den folgenden Blog an: [Live streaming production with OBS](https://link.medium.com/ttuwHpaJeT) (Livestreamproduktion mit OBS).
   
    Dieser Schritt kann auch nach der Erstellung des Kanals ausgeführt werden.
1. Erstellen und starten Sie einen Pass-Through-Kanal.
1. Rufen Sie die Erfassungs-URL des Kanals ab. 
   
    Die Erfassungs-URL wird vom Liveencoder verwendet, um den Datenstrom an den Kanal zu senden.
1. Rufen Sie die Vorschau-URL des Kanals ab. 
   
    Verwenden Sie diese URL, um sicherzustellen, dass der Livestream ordnungsgemäß vom Kanal empfangen wird.
1. Erstellen Sie ein Liveereignis/-programm. 
   
    Bei Verwendung des Azure-Portals wird beim Erstellen eines Liveereignisses auch ein Objekt erstellt. 

1. Wenn Sie zum Starten von Streaming und Archivierung bereit sind, starten Sie das Ereignis bzw. Programm.
1. Optional kann vom Liveencoder eine Ankündigung gestartet werden. Die Ankündigung wird in den Ausgabedatenstrom eingefügt.
1. Sie können das Ereignis/Programm und damit das Streaming und die Archivierung des Ereignisses jederzeit beenden.
1. Löschen Sie das Ereignis/Programm (und optional das Medienobjekt).     

> [!IMPORTANT]
> Lesen Sie sich den Artikel [Livestreaming mit lokalen Encodern, die Datenströme mit Mehrfachbitrate erstellen](media-services-live-streaming-with-onprem-encoders.md) durch, um mehr über die Konzepte und Aspekte zu erfahren, die für das Livestreaming mit lokalen Encodern und Pass-Through-Kanälen wichtig sind.
> 
> 

## <a name="to-view-notifications-and-errors"></a>So zeigen Sie Benachrichtigungen und Fehler an
Klicken Sie auf das Symbol „Benachrichtigung“, wenn Sie Benachrichtigungen und Fehler anzeigen möchten, die vom Azure-Portal generiert wurden.

![Benachrichtigungen](./media/media-services-portal-passthrough-get-started/media-services-notifications.png)

## <a name="create-and-start-pass-through-channels-and-events"></a>Erstellen und Starten von Pass-Through-Kanälen und -Ereignissen
Einem Kanal sind Ereignisse/Programme zugeordnet, mit denen Sie das Veröffentlichen und Speichern von Segmenten in einem Livedatenstrom steuern können. Kanäle verwalten Ereignisse. 

Über die Länge des **Archivierungsfensters** können Sie die Anzahl der Stunden angeben, für die Sie den aufgezeichneten Inhalt des Programms beibehalten möchten. Dieser Wert kann von mindestens 5 Minuten bis zu einem Höchstwert von 25 Stunden eingestellt werden. Von der Länge des Archivierungsfensters wird außerdem bestimmt, wie lange von Clients von der aktuellen Liveposition aus maximal rückwärts gesucht werden kann. Ereignisse können über die angegebene Zeitspanne laufen. Inhalte, die über das Zeitfenster hinausgehen, werden jedoch fortlaufend verworfen. Der Wert dieser Eigenschaft legt außerdem fest, wie lange Clientmanifeste wachsen können.

Jedes Ereignis ist mit einem Medienobjekt verknüpft. Zum Veröffentlichen des Ereignisses müssen Sie einen OnDemand-Locator für das zugehörige Asset erstellen. Mithilfe dieses Locators können Sie eine Streaming-URL erstellen, die Sie Ihren Kunden bereitstellen können.

Ein Kanal unterstützt bis zu drei gleichzeitig ausgeführte Ereignisse, sodass Sie mehrere Archive desselben eingehenden Datenstroms erstellen können. Auf diese Weise können Sie verschiedene Teile eines Ereignisses nach Bedarf veröffentlichen und archivieren. Beispielsweise könnte Ihre Geschäftsanforderung darin bestehen, 6 Stunden eines Programms zu archivieren, jedoch nur die letzten 10 Minuten zu senden. Dazu müssen Sie zwei Programme erstellen, die gleichzeitig ausgeführt werden. Ein Programm wird auf die Archivierung von 6 Stunden des Ereignisses festgelegt. Dieses Programm wird jedoch nicht veröffentlicht. Das andere Programm wird auf die Archivierung von 10 Minuten festgelegt. Dieses Programm wird veröffentlicht.

Es ist nicht ratsam, vorhandene Liveereignisse wiederzuverwenden. Erstellen und starten Sie stattdessen für jedes Ereignis ein neues Ereignis.

Wenn Sie zum Starten von Streaming und Archivierung bereit sind, starten Sie das Ereignis. Sie können das Programm und damit das Streaming und die Archivierung des Ereignisses jederzeit beenden. 

Zum Löschen von archivierten Inhalten beenden und löschen Sie das Ereignis und löschen anschließend das zugehörige Medienobjekt. Medienobjekte können nicht gelöscht werden, wenn sie von Ereignissen verwendet werden. Zuerst muss das betreffende Ereignis gelöscht werden. 

Auch nach dem Beenden und Löschen des Ereignisses können die Benutzer archivierte Inhalte als bedarfsgesteuertes Video streamen, solange das Medienobjekt nicht gelöscht wurde.

Wenn Sie die archivierten Inhalte beibehalten möchten, diese aber nicht für das Streaming verfügbar sein sollen, löschen Sie den Streaminglocator.

### <a name="to-use-the-portal-to-create-a-channel"></a>So erstellen Sie mit dem Portal einen Kanal
In diesem Abschnitt erfahren Sie, wie Sie mithilfe der Option **Schnellerfassung** einen Pass-Through-Kanal erstellen.

Weitere Informationen zu Pass-Through-Kanälen finden Sie unter [Livestreaming mit lokalen Encodern, die Multi-Bitrate-Datenströme erstellen](media-services-live-streaming-with-onprem-encoders.md).

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihr Azure Media Services-Konto aus.
2. Klicken Sie im Fenster **Einstellungen** auf **Livestreaming**. 
   
    ![Erste Schritte](./media/media-services-portal-passthrough-get-started/media-services-getting-started.png)
   
    Das Fenster **Livestreaming** wird angezeigt.
3. Klicken Sie auf **Schnellerfassung** , um einen Pass-Through-Kanal mit dem RTMP-Erfassungsprotokoll zu erstellen.
   
    Das Fenster **EINEN NEUEN KANAL ERSTELLEN** wird angezeigt.
4. Geben Sie dem neuen Kanal einen Namen, und klicken Sie auf **Erstellen**. 
   
    Der Pass-Through-Kanal mit dem RTMP-Erfassungsprotokoll wird erstellt.

## <a name="create-events"></a>Erstellen von Ereignissen
1. Wählen Sie einen Kanal aus, dem Sie ein Ereignis hinzufügen möchten.
2. Klicken Sie auf die Schaltfläche **Live Event** (Liveereignis).

![Ereignis](./media/media-services-portal-passthrough-get-started/media-services-create-events.png)

## <a name="get-ingest-urls"></a>Abrufen von Erfassungs-URLs
Wenn der Kanal erstellt wurde, können Sie Erfassungs-URLs abrufen, die Sie dem Liveencoder bereitstellen. Diese URLs werden vom Encoder zur Eingabe eines Livedatenstroms verwendet.

![Screenshot der Seite „Livestreaming“ mit einem ausgewählten Kanal und dem Kanalbereich](./media/media-services-portal-passthrough-get-started/media-services-channel-created.png)

## <a name="watch-the-event"></a>Ansehen des Ereignisses
Klicken Sie zum Überwachen des Ereignisses im Azure-Portal auf **Überwachen** , oder kopieren Sie die Streaming-URL, und verwenden Sie einen Player Ihrer Wahl. 

![Erstellt](./media/media-services-portal-passthrough-get-started/media-services-default-event.png)

Das Liveereignis wird automatisch in On-Demand-Inhalt konvertiert, wenn es beendet wird.

## <a name="clean-up"></a>Bereinigung
Weitere Informationen zu Pass-Through-Kanälen finden Sie unter [Livestreaming mit lokalen Encodern, die Multi-Bitrate-Datenströme erstellen](media-services-live-streaming-with-onprem-encoders.md).

* Ein Kanal kann nur beendet werden, wenn auch alle Ereignisse/Programme des Kanals beendet wurden.  Wenn der Kanal beendet ist, fallen keine Kosten an. Wenn Sie den Kanal erneut starten, weist er die gleiche Erfassungs-URL auf, damit Sie den Encoder nicht erneut konfigurieren müssen.
* Ein Kanal kann nur gelöscht werden, wenn auch alle Liveereignisse des Kanals gelöscht wurden.

## <a name="view-archived-content"></a>Anzeigen von archivierten Inhalten
Auch nach dem Beenden und Löschen des Ereignisses können die Benutzer archivierte Inhalte als bedarfsgesteuertes Video streamen, solange das Medienobjekt nicht gelöscht wurde. Medienobjekte können nicht gelöscht werden, wenn sie von Ereignissen verwendet werden. Zuerst muss das betreffende Ereignis gelöscht werden. 

Wählen Sie zum Verwalten der Objekte **Einstellung** aus, und klicken Sie auf **Assets**.

![Objekte](./media/media-services-portal-passthrough-get-started/media-services-assets.png)

## <a name="next-step"></a>Nächster Schritt
Überprüfen Sie die Media Services-Lernpfade.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
