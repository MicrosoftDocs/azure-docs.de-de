---
title: Livestreaming mit Azure Media Services zum Erstellen von Datenströmen mit Mehrfachbitrate im Azure-Portal | Microsoft-Dokumentation
description: In diesem Lernprogramm werden Sie durch die Schritte zum Erstellen eines Kanals über das Azure-Portal geführt, von dem ein Single-Bitrate-Livedatenstrom empfangen und in einen Multi-Bitrate-Datenstrom codiert wird.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 504f74c2-3103-42a0-897b-9ff52f279e23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: daab3c3b2a5b756686a4867350478faaa1142279
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64726859"
---
# <a name="perform-live-streaming-using-media-services-to-create-multi-bitrate-streams-with-azure-portal"></a>Livestreaming mit Media Services zum Erstellen von Datenströmen mit Mehrfachbitrate im Azure-Portal  
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
> * [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
> * [REST-API](https://docs.microsoft.com/rest/api/media/operations/channel)
> 

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

In diesem Lernprogramm werden Sie durch die Schritte zum Erstellen eines **Kanals** geführt, von dem ein Single-Bitrate-Livedatenstrom empfangen und in einen Multi-Bitrate-Datenstrom codiert wird.

Weitere grundlegende Informationen zu Kanälen, die für Livecodierung aktiviert sind, finden Sie unter [Arbeiten mit Kanälen, die zum Ausführen von Livecodierung mit Azure Media Services aktiviert wurden](media-services-manage-live-encoder-enabled-channels.md).

## <a name="common-live-streaming-scenario"></a>Allgemeines Livestreamingszenario
Im Folgenden werden grundlegende Schritte zum Erstellen allgemeiner Livestreaminganwendungen erläutert.

> [!NOTE]
> Die maximal empfohlene Dauer eines Liveereignisses beträgt derzeit 8 Stunden. Wenden Sie sich an amslived@microsoft.com, falls Sie einen Kanal über längere Zeiträume ausführen müssen.

1. Schließen Sie eine Videokamera an einen Computer an. <br/>Ausrüstungsanregungen finden Sie unter [Simple and Portable Event Video Gear Setup]( https://link.medium.com/KNTtiN6IeT) (Einfache und tragbare Videoausrüstung für Veranstaltungen).

    Wenn Sie keinen Zugriff auf eine Kamera haben, können Sie mit Tools wie [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) einen Livefeed aus einer Videodatei generieren.
1. Starten und konfigurieren Sie einen lokalen Liveencoder, von dem ein Einzelbitraten-Datenstrom in einem der folgenden Protokolle ausgegeben wird: RTMP oder Smooth Streaming. Weitere Informationen finden Sie unter [Microsoft Azure Media Services RTMP-Support und Liveencoder](https://go.microsoft.com/fwlink/?LinkId=532824). <br/>Sehen Sie sich auch den folgenden Blog an: [Live streaming production with OBS](https://link.medium.com/ttuwHpaJeT) (Livestreamproduktion mit OBS).

    Dieser Schritt kann auch nach der Erstellung des Kanals ausgeführt werden.
1. Erstellen Sie einen Kanal, und starten Sie ihn. 
1. Rufen Sie die Erfassungs-URL des Kanals ab. 

    Die Erfassungs-URL wird vom Liveencoder verwendet, um den Datenstrom an den Kanal zu senden.
1. Rufen Sie die Vorschau-URL des Kanals ab. 

    Verwenden Sie diese URL, um sicherzustellen, dass der Livestream ordnungsgemäß vom Kanal empfangen wird.
1. Erstellen Sie ein Ereignis/Programm (dadurch wird auch ein Asset erstellt). 
1. Veröffentlichen Sie das Ereignis (dadurch wird ein OnDemand-Locator für das zugehörige Medienobjekt erstellt).    
1. Wenn Sie zum Starten von Streaming und Archivierung bereit sind, starten Sie das Ereignis.
1. Optional kann vom Liveencoder eine Ankündigung gestartet werden. Die Ankündigung wird in den Ausgabedatenstrom eingefügt.
1. Sie können das Ereignis und damit das Streaming und die Archivierung des Ereignisses jederzeit beenden.
1. Löschen Sie das Ereignis (und optional das Asset).   

## <a name="prerequisites"></a>Voraussetzungen

Zum Abschließen dieses Lernprogramms müssen folgende Voraussetzungen erfüllt sein:

* Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. 
  Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* Media Services-Konto. Informationen zum Erstellen eines Media Services-Kontos finden Sie unter [Erstellen eines Kontos](media-services-portal-create-account.md).
* Sie benötigen eine Webcam und einen Encoder, von dem ein Single-Bitrate-Livedatenstrom gesendet wird.

## <a name="create-a-channel"></a>Erstellen eines Kanals

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) die Option „Media Services“ aus, und klicken Sie dann auf den Media Services-Kontonamen.
2. Wählen Sie **Livestreaming**.
3. Wählen Sie **Benutzerdefiniert erstellen**. Mit dieser Option können Sie einen Kanal erstellen, der für Live Encoding aktiviert ist.

    ![Erstellen eines KANALS](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel.png)
4. Klicken Sie auf **Einstellungen**.

   1. Wählen Sie den Kanaltyp **Live Encoding** . Mit diesem Typen geben Sie an, dass Sie einen Kanal erstellen möchten, der für Livecodierung aktiviert ist. Dies bedeutet, dass der eingehende Single-Bitrate-Datenstrom an den Kanal gesendet und mithilfe angegebener Liveencodereinstellungen in einen Multi-Bitrate-Datenstrom codiert wird. Weitere Informationen finden Sie unter [Livestreaming mit Azure Media Services zum Erstellen von Datenströmen mit Mehrfachbitrate](media-services-manage-live-encoder-enabled-channels.md). Klicken Sie auf OK.
   2. Geben Sie den Namen eines Kanals an.
   3. Klicken Sie unten auf dem Bildschirm auf „OK“.
5. Wählen Sie die Registerkarte **Erfassen** .

   1. Auf dieser Seite können Sie ein Streamingprotokoll auswählen. Für den Kanaltyp **Live Encoding** stehen folgende gültige Protokolloptionen zur Verfügung:

      * Fragmentiertes Single-Bitrate-MP4 (Smooth Streaming)
      * Single-Bitrate-RTMP

        Ausführliche Informationen zu den einzelnen Protokollen finden Sie unter [Livestreaming mit Azure Media Services zum Erstellen von Datenströmen mit Mehrfachbitrate](media-services-manage-live-encoder-enabled-channels.md).

        Sie können die Protokolloption nicht ändern, während der Kanal oder seine zugehörigen Ereignisse bzw. Programme ausgeführt werden. Wenn Sie andere Protokolle benötigen, erstellen Sie für jedes Streamingprotokoll einen separaten Kanal.  
   2. Sie können IP-Einschränkungen auf die Erfassung anwenden. 

       Sie können die IP-Adressen definieren, die zum Erfassen von Videos in diesem Kanal zugelassen sind. Zulässige IP-Adressen können als eine einzelne IP-Adresse (z. B. „10.0.0.1“), als IP-Adressbereich mithilfe einer IP-Adresse und einer CIDR-Subnetzmaske (z.B. „10.0.0.1/22“) oder als IP-Adressbereich mithilfe einer IP-Adresse und einer Subnetzmaske in punktierter Dezimalschreibweise (z.B. „10.0.0.1(255.255.252.0)“) angegeben werden.

       Wenn keine IP-Adressen angegeben werden und keine Regeldefinition vorhanden ist, so ist keine IP-Adresse zulässig. Um alle IP-Adressen zuzulassen, erstellen Sie eine Regel und legen 0.0.0.0/0 fest.
6. Wenden Sie auf der Registerkarte **Vorschau** die IP-Einschränkung auf die Vorschau an.
7. Geben Sie auf der Registerkarte **Codierung** die Codierungsvoreinstellung an. 

    Derzeit ist die einzige Systemvoreinstellung, die Sie auswählen können, **Default 720p**. Öffnen Sie ein Microsoft-Support-Ticket, um eine benutzerdefinierte Voreinstellung anzugeben. Geben Sie anschließend den Namen der für Sie erstellten Voreinstellung ein. 

> [!NOTE]
> Derzeit kann der Kanalstart bis zu 30 Minuten dauern. Das Zurücksetzen des Kanals kann bis zu 5 Minuten dauern.
> 
> 

Nachdem Sie den Kanal erstellt haben, können Sie auf den Kanal klicken und **Einstellungen** auswählen, um die Konfigurationen Ihrer Kanäle anzusehen. 

Weitere Informationen finden Sie unter [Livestreaming mit Azure Media Services zum Erstellen von Datenströmen mit Mehrfachbitrate](media-services-manage-live-encoder-enabled-channels.md).

## <a name="get-ingest-urls"></a>Abrufen von Erfassungs-URLs
Wenn der Kanal erstellt wurde, können Sie Erfassungs-URLs abrufen, die Sie dem Liveencoder bereitstellen. Diese URLs werden vom Encoder zur Eingabe eines Livedatenstroms verwendet.

![Erfassungs-URLs](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)

## <a name="create-and-manage-events"></a>Erstellen und Verwalten von Ereignissen

### <a name="overview"></a>Übersicht
Einem Kanal sind Ereignisse/Programme zugeordnet, mit denen Sie das Veröffentlichen und Speichern von Segmenten in einem Livedatenstrom steuern können. Kanäle verwalten Ereignisse/Programme. Die Beziehung zwischen Kanal und Programm ähnelt herkömmlichen Medien, bei denen ein Kanal einen konstanten Stream von Inhalten aufweist und ein Programm auf ein zeitlich festgelegtes Ereignis in diesem Kanal ausgerichtet ist.

Über die Länge des **Archivierungsfensters** können Sie die Anzahl der Stunden angeben, für die Sie den aufgezeichneten Inhalt des Ereignisses beibehalten möchten. Dieser Wert kann von mindestens 5 Minuten bis zu einem Höchstwert von 25 Stunden eingestellt werden. Von der Länge des Archivierungsfensters wird außerdem bestimmt, wie lange von Clients von der aktuellen Liveposition aus maximal rückwärts gesucht werden kann. Ereignisse können über die angegebene Zeitspanne laufen. Inhalte, die über das Zeitfenster hinausgehen, werden jedoch fortlaufend verworfen. Der Wert dieser Eigenschaft legt außerdem fest, wie lange Clientmanifeste wachsen können.

Jedes Ereignis ist mit einem Asset verknüpft. Zum Veröffentlichen des Ereignisses müssen Sie einen OnDemand-Locator für das zugehörige Medienobjekt erstellen. Mithilfe dieses Locators können Sie eine Streaming-URL erstellen, die Sie Ihren Kunden bereitstellen können.

Ein Kanal unterstützt bis zu drei gleichzeitig ausgeführte Ereignisse, sodass Sie mehrere Archive desselben eingehenden Datenstroms erstellen können. Auf diese Weise können Sie verschiedene Teile eines Ereignisses nach Bedarf veröffentlichen und archivieren. Beispielsweise könnte Ihre Geschäftsanforderung darin bestehen, sechs Stunden eines Ereignisses zu archivieren, jedoch nur die letzten zehn Minuten zu senden. Dazu müssen Sie zwei Ereignisse erstellen, die gleichzeitig ausgeführt werden. Ein Ereignis wird auf die Archivierung von sechs Stunden des Ereignisses festgelegt. Dieses Programm wird jedoch nicht veröffentlicht. Das andere Ereignis wird auf die Archivierung von zehn Minuten festgelegt. Dieses Programm wird veröffentlicht.

Verwenden Sie vorhandene Programme nicht erneut für nachfolgende Ereignisse. Erstellen und starten Sie stattdessen für jedes Ereignis ein neues Programm.

Wenn Sie zum Starten von Streaming und Archivierung bereit sind, starten Sie ein Ereignis bzw. Programm. Sie können das Ereignis und damit das Streaming und die Archivierung des Ereignisses jederzeit beenden. 

Zum Löschen von archivierten Inhalten beenden und löschen Sie das Ereignis und löschen anschließend das zugehörige Medienobjekt. Assets können nicht gelöscht werden, wenn sie vom Ereignis verwendet werden. Zuerst muss das betreffende Ereignis gelöscht werden. 

Auch nach dem Beenden und Löschen des Ereignisses können die Benutzer archivierte Inhalte als bedarfsgesteuertes Video streamen, solange das Medienobjekt nicht gelöscht wurde.

Wenn Sie die archivierten Inhalte beibehalten möchten, diese aber nicht für das Streaming verfügbar sein sollen, löschen Sie den Streaminglocator.

### <a name="createstartstop-events"></a>Erstellen, Starten und Beenden von Ereignissen
Wenn der Datenstrom in den Kanal gelangt, können Sie das Streamingereignis starten, indem Sie ein Medienobjekt, ein Programm und einen Streaminglocator erstellen. Dadurch wird der Datenstrom archiviert und über den Streamingendpunkt für die Zuschauer verfügbar gemacht. 

>[!NOTE]
>Beim Erstellen Ihres AMS-Kontos wird dem Konto ein **Standard**-Streamingendpunkt mit dem Status **Beendet** hinzugefügt. Um mit dem Streamen der Inhalte zu beginnen und die dynamische Paketerstellung und dynamische Verschlüsselung zu nutzen, muss der Streamingendpunkt, von dem Sie Inhalte streamen möchten, den Status **Wird ausgeführt** aufweisen. 

Es gibt zwei Möglichkeiten, das Ereignis zu starten: 

1. Klicken Sie auf der Seite **Kanal** auf **Liveereignis**, um ein neues Ereignis hinzuzufügen.

    Geben Sie Folgendes an: Name des Ereignisses, Name des Assets, Archivfenster, Verschlüsselungsoption.

    ![Programm erstellen](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)

    Wenn Sie das Kontrollkästchen **Publish this live event now** (Dieses Liveereignis jetzt veröffentlichen) aktiviert gelassen haben, werden das Ereignis und die VERÖFFENTLICHUNGS-URLs erstellt.

    Sie können auf **Start**klicken, wenn Sie bereit zum Streamen des Ereignisses sind.

    Wenn Sie das Ereignis gestartet haben, können Sie auf **Watch** (Ansehen) klicken, um die Wiedergabe der Inhalte zu starten.
2. Sie können auch einfach auf der Seite **Kanal** auf die Schaltfläche **Live schalten** klicken. Dadurch erstellen Sie ein Standardasset, ein Programm und einen Streaminglocator.

    Das Ereignis hat den Namen **default** , und das Archivfenster ist auf acht Stunden festgelegt.

Sie können das veröffentlichte Ereignis auf der Seite **Live event** (Liveereignis) ansehen. 

Wenn Sie auf **Off Air**(Deaktivieren) klicken, werden alle Liveereignisse beendet. 

## <a name="watch-the-event"></a>Ansehen des Ereignisses
Klicken Sie zum Überwachen des Ereignisses im Azure-Portal auf **Überwachen** , oder kopieren Sie die Streaming-URL, und verwenden Sie einen Player Ihrer Wahl. 

![Erstellt](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-play-event.png)

Das Liveereignis konvertiert Ereignisse automatisch in On-Demand-Inhalt, wenn es beendet wird.

## <a name="clean-up"></a>Bereinigen
Wenn die Streamingereignisse beendet sind und Sie die zuvor bereitgestellten Ressourcen bereinigen möchten, führen Sie das folgende Verfahren aus:

* Beenden Sie die Datenstromeingabe vom Encoder.
* Beenden Sie den Kanal. Wenn der Kanal beendet ist, fallen keine Kosten an. Wenn Sie den Kanal erneut starten, weist er die gleiche Erfassungs-URL auf, damit Sie den Encoder nicht erneut konfigurieren müssen.
* Sie können Ihren Streamingendpunkt beenden, sofern Sie das Archiv Ihres Liveereignisses nicht als bedarfsgesteuerten Datenstrom bereitstellen möchten. Durch Kanäle im angehaltenen Zustand fallen keine Kosten an.

## <a name="view-archived-content"></a>Anzeigen von archivierten Inhalten
Auch nach dem Beenden und Löschen des Ereignisses können die Benutzer archivierte Inhalte als bedarfsgesteuertes Video streamen, solange das Medienobjekt nicht gelöscht wurde. Medienobjekte können nicht gelöscht werden, wenn sie von Ereignissen verwendet werden. Zuerst muss das betreffende Ereignis gelöscht werden. 

Wählen Sie zum Verwalten der Objekte **Einstellung** aus, und klicken Sie auf **Assets**.

![Objekte](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-assets.png)

## <a name="considerations"></a>Überlegungen
* Die maximal empfohlene Dauer eines Liveereignisses beträgt derzeit 8 Stunden. Wenden Sie sich an amslived@microsoft.com, falls Sie einen Kanal über längere Zeiträume ausführen müssen.
* Stellen Sie sicher, dass sich der Streamingendpunkt, von dem aus Sie die Inhalte streamen möchten, im Status **Wird ausgeführt** befindet.

## <a name="next-step"></a>Nächster Schritt
Überprüfen Sie die Media Services-Lernpfade.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

