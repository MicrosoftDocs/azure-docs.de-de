---
title: Gängige Szenarien für Microsoft Azure Media Services | Microsoft-Dokumentation
description: Dieser Artikel bietet eine Übersicht über gängige Szenarien für Microsoft Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/3/2020
ms.author: inhenkel
ms.openlocfilehash: 001c535a2b39898673f2d587ee807d43b4d5f60a
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348541"
---
# <a name="microsoft-azure-media-services-common-scenarios"></a>Gängige Szenarien für Microsoft Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. Sehen Sie sich die neuste Version – [Media Services v3](../latest/media-services-overview.md) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

Mit Microsoft Azure Media Services (AMS) können Sie Video- oder Audioinhalte auf sichere Weise hochladen, speichern, codieren, verpacken und bedarfsgesteuert oder als Livestream auf verschiedenen Clients (wie Fernsehern, PCs und mobilen Geräten) bereitstellen.

Dieser Artikel zeigt gängige Szenarien für die Übermittlung von Inhalten (live oder on-demand).

## <a name="overview"></a>Übersicht

### <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Einen Monat kostenlos testen](https://azure.microsoft.com).
* Ein Azure Media Services-Konto. Weitere Informationen finden Sie unter [Erstellen von Konten](media-services-portal-create-account.md).
* Der Streamingendpunkt, von dem aus Sie die Inhalte streamen möchten, muss sich im Status **Wird ausgeführt** befinden.

    Beim Erstellen Ihres AMS-Kontos wird dem Konto ein **Standard** -Streamingendpunkt mit dem Zustand **Beendet** hinzugefügt. Um mit dem Streamen der Inhalte zu beginnen und die dynamische Paketerstellung und dynamische Verschlüsselung zu nutzen, muss sich der Streamingendpunkt im Zustand **Wird ausgeführt** befinden.

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>Häufig verwendete Objekte bei der Entwicklung mit dem AMS-OData-Modell

Die folgende Abbildung zeigt einige der am häufigsten verwendeten Objekte beim Entwickeln mit dem Media Services OData-Modell.

Klicken Sie auf Bild, um es in voller Größe anzeigen.  

[![Diagramm mit einigen der am häufigsten verwendeten Objekte bei der Entwicklung anhand des Azure Media Services-Objektdatenmodells.](./media/media-services-overview/media-services-overview-object-model-small.png)](./media/media-services-overview/media-services-overview-object-model.png#lightbox)

Sie können das gesamte Modell [hier](https://media.windows.net/API/$metadata?api-version=2.15) anzeigen.  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Schützen von Inhalte im Speicher und Übermitteln von Streamingmedien ohne Verschlüsselung

![VoD-Workflow](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. Laden Sie eine Mediendatei mit hoher Qualität in ein Medienobjekt hoch.

    Es wird empfohlen, eine Speicherverschlüsselung auf Ihr Medienobjekt anzuwenden, um Ihre Inhalte beim Hochladen und im Ruhezustand im Speicher zu schützen.

1. Codieren Sie das Medienobjekt in einen Satz von MP4-Dateien mit adaptiver Bitrate.

    Es wird empfohlen, eine Speicherverschlüsselung auf das Ausgabemedienobjekt anzuwenden, um Ihre Inhalte im Ruhezustand zu schützen.

1. Konfigurieren Sie eine Übermittlungsrichtlinie für Medienobjekte (wird zur dynamischen Paketerstellung verwendet).

    Wenn Ihr Medienobjekt speicherverschlüsselt ist, **müssen** Sie die Übermittlungsrichtlinien für Medienobjekte konfigurieren.
1. Veröffentlichen Sie das Medienobjekt durch Erstellen eines OnDemand-Locators.
1. Streamen Sie die veröffentlichten Inhalte.

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Schützen von Inhalten im Speicher, Übermitteln dynamisch verschlüsselter Streamingmedien

![Schützen mit PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. Laden Sie eine Mediendatei mit hoher Qualität in ein Medienobjekt hoch. Wenden Sie die Speicherverschlüsselung auf das Medienobjekt an.
1. Codieren Sie das Medienobjekt in einen Satz von MP4-Dateien mit adaptiver Bitrate. Wenden Sie die Speicherverschlüsselung auf das Ausgabemedienobjekt an.
1. Erstellen Sie einen Inhaltsverschlüsselungsschlüssel für das Medienobjekt, das während der Wiedergabe dynamisch verschlüsselt werden soll.
1. Konfigurieren Sie Autorisierungsrichtlinien für Inhaltsschlüssel.
1. Konfigurieren Sie Übermittlungsrichtlinien für Medienobjekte (wird zur dynamischen Paketerstellung und zur dynamischen Verschlüsselung verwendet).
1. Veröffentlichen Sie das Medienobjekt durch Erstellen eines OnDemand-Locators.
1. Streamen Sie die veröffentlichten Inhalte.

## <a name="deliver-progressive-download"></a>Bereitstellen eines progressiven Downloads

1. Laden Sie eine Mediendatei mit hoher Qualität in ein Medienobjekt hoch.
1. Codieren Sie das Medienobjekt in eine einzelne MP4-Datei.
1. Veröffentlichen Sie das Medienobjekt durch Erstellen eines OnDemand- oder SAS-Locators. Wenn Sie einen SAS-Locator verwenden, wird der Inhalt aus Azure Blob Storage heruntergeladen. Sie benötigen keine Streamingendpunkte im Status „Gestartet“.
1. Führen Sie einen progressiven Download der Inhalte durch.

## <a name="delivering-live-streaming-events"></a>Bereitstellen von Livestreaming-Ereignissen

1. Erfassen Sie Liveinhalte über verschiedene Livestreaming-Protokolle (wie etwa RTMP oder Smooth Streaming).
1. Optional: Codieren Sie Ihren Datenstrom als Datenstrom mit adaptiver Bitrate.
1. Zeigen Sie eine Vorschau Ihres Livestreams an.
1. Übermitteln Sie die Inhalte über folgende Optionen:
    1. Über allgemeine Streamingprotokolle (z. B. MPEG DASH, Smooth, HLS) direkt an Ihre Kunden
    1. An ein Content Delivery Network (CDN) zur weiteren Verteilung
    1. Per Aufzeichnen und Speichern des erfassten Inhalts zum späteren Streamen (Video-on-Demand)

Beim Livestreaming haben Sie die Wahl zwischen folgenden Routen:

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Arbeiten mit Kanälen, die Livedatenströme mit Mehrfachbitraten von lokalen Encodern empfangen (Pass-Through)

Das folgende Diagramm zeigt die Hauptkomponenten der AMS-Plattform, die am **Pass-Through-Workflow** beteiligt sind:

![Diagramm mit den Hauptkomponenten der AMS-Plattform, die am Passthrough-Workflow beteiligt sind](./media/scenarios-and-availability/media-services-live-streaming-current.png)

Weitere Informationen finden Sie unter [Arbeiten mit Kanälen, die Livedatenströme mit mehreren Bitraten von lokalen Encodern empfangen](media-services-live-streaming-with-onprem-encoders.md).

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Arbeiten mit Kanälen, die zum Ausführen von Livecodierung mit Azure Media Services aktiviert wurden

Das folgende Diagramm zeigt, welche Hauptkomponenten der AMS-Plattform an Livestreaming-Workflows beteiligt sind, wenn ein Kanal zum Ausführen der Livecodierung mit Media Services aktiviert ist.

![Liveworkflow](./media/scenarios-and-availability/media-services-live-streaming-new.png)

Weitere Informationen finden Sie unter [Arbeiten mit Kanälen, die zum Ausführen von Live Encoding mit Azure Media Services aktiviert wurden](media-services-manage-live-encoder-enabled-channels.md).

## <a name="consuming-content"></a>Nutzung von Inhalten

Azure Media Services stellt die Tools zur Verfügung, die zum Erstellen leistungsstarker, dynamischer Clientplayeranwendungen für die gängigsten Plattformen erforderlich sind, darunter: iOS-Geräte, Android-Geräte, Windows, Windows Phone, Xbox und Set-Top-Boxen.

## <a name="enabling-azure-cdn"></a>Aktivieren von Azure CDN

Von Media Services wird die Integration mit Azure CDN unterstützt. Informationen zum Aktivieren von Azure CDN finden Sie unter [Verwalten von Streamingendpunkten in Media Services-Konten](media-services-portal-manage-streaming-endpoints.md).

## <a name="scaling-a-media-services-account"></a>Skalieren eines Media Services-Kontos

AMS-Kunden können Streamingendpunkte, Medienverarbeitung und Speicherung in ihren AMS-Konten skalieren.

* Media Services-Kunden haben die Wahl zwischen einem **Standard** -Streamingendpunkt und einem **Premium** -Streamingendpunkt. Ein **Standard** -Streamingendpunkt ist für die meisten Streamingworkloads geeignet. Er bietet die gleichen Features wie ein **Premium** -Streamingendpunkt und skaliert die ausgehende Bandbreite automatisch.

    **Premium** -Streamingendpunkte eignen sich für komplexere Workloads und bieten eine dedizierte und skalierbare Bandbreitenkapazität. Kunden mit einem **Premium** -Streamingendpunkt erhalten standardmäßig eine einzelne Streamingeinheit (Streaming Unit, SU). Der Streamingendpunkt kann durch Hinzufügen von SUs skaliert werden. Jede SU stellt zusätzliche Bandbreitenkapazität für die Anwendung bereit. Weitere Informationen zur Skalierung von **Premium** -Streamingendpunkten finden Sie im Thema [Skalieren von Streamingendpunkten mithilfe des Azure-Portals](media-services-portal-scale-streaming-endpoints.md).

* Ein Media Services-Konto ist dem Typ einer reservierten Einheit zugeordnet, der die Geschwindigkeit bestimmt, mit der Medienverarbeitungsaufgaben erfolgen. Sie können zwischen den folgenden reservierten Einheitentypen wählen: **S1** , **S2** oder **S3**. Derselbe Codierungsauftrag wird bei Verwendung der reservierten Einheit **S2** beispielsweise schneller ausgeführt als mit dem Typ **S1**.

    Neben der Art der reservierten Einheit können Sie angeben, dass für Ihr Konto **reservierte Einheiten** (Reserved Units, RUs) bereitgestellt werden sollen. Die Anzahl bereitgestellter RUs bestimmt die Anzahl von Medienaufgaben, die gleichzeitig unter einem bestimmten Konto verarbeitet werden können.

    > [!NOTE]
    > RUs dienen der Parallelisierung der gesamten Medienverarbeitung, einschließlich der Indizierung von Aufträgen mit Azure Media Indexer. Allerdings erfolgt die Indizierung von Aufträgen im Gegensatz zur Codierung mit schnelleren reservierten Einheiten nicht schneller.

    Weitere Informationen finden Sie unter [Skalieren der Medienverarbeitung](media-services-portal-scale-media-processing.md).

* Außerdem können Sie Ihr Media Services-Konto skalieren, indem Sie Speicherkonten hinzufügen. Jedes Speicherkonto ist auf 500 TB beschränkt. Weitere Informationen finden Sie unter [Verwalten von Media Services-Medienobjekten für mehrere Speicherkonten](./media-services-managing-multiple-storage-accounts.md).

## <a name="next-steps"></a>Nächste Schritte

[Migrieren zu Media Services v3](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]