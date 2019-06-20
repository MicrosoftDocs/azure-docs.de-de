---
title: Migrieren von Azure Media Services V2 zu V3 | Microsoft-Dokumentation
description: Dieser Artikel beschreibt Änderungen, die in Azure Media Services V3 eingeführt wurden, und zeigt Unterschiede zwischen den beiden Versionen. Dieser Artikel bietet außerdem Hilfestellung bei der Migration von Media Services v2 zu v3.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, streamen, übertragen, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: da8d1000ebe2695dbc95d475027722962a3305d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65555879"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Hinweise zur Migration von Media Services v2 zu v3

Dieser Artikel beschreibt Änderungen, die in Azure Media Services V3 eingeführt wurden, zeigt Unterschiede zwischen den beiden Versionen und enthält die Migrationsanleitung.

Wenn Sie derzeit über einen Videodienst verfügen, der auf Basis von der [älteren APIs von Media Services v2](../previous/media-services-overview.md) entwickelt wurden, überprüfen Sie vor der Migration zu den v3-APIs die folgenden Richtlinien und Überlegungen. Die v3-API bietet zahlreiche Vorteile und neue Features, die die Entwicklungsumgebung und Funktionen von Media Services verbessern. Wie im Abschnitt [Bekannte Probleme](#known-issues) dieses Artikels erläutert, gibt es auch einige Einschränkungen aufgrund von Änderungen zwischen den API-Versionen. Diese Seite wird laufend aktualisiert, da das Media Services-Team die v3-APIs weiterhin verbessert und die Diskrepanz zwischen den Versionen behebt. 

> [!NOTE]
> Derzeit können Sie das Azure-Portal nicht für die Verwaltung von v3-Ressourcen verwenden. Verwenden Sie die [REST-API](https://aka.ms/ams-v3-rest-ref), die [Befehlszeilenschnittstelle](https://aka.ms/ams-v3-cli-ref) oder eines der unterstützten [SDKs](media-services-apis-overview.md#sdks).

## <a name="benefits-of-media-services-v3"></a>Vorteile von Media Services v3
  
### <a name="api-is-more-approachable"></a>Besser zugängliche API

*  v3 basiert auf einer vereinheitlichten API-Oberfläche, die sowohl Verwaltungs- als auch Betriebsfunktionen auf der Basis von Azure Resource Manager bereitstellt. Azure Resource Manager-Vorlagen können zum Erstellen und Bereitstellen von Transformationen, Streamingendpunkten, Liveereignissen und mehr verwendet werden.
* Dokument [OpenAPI-Spezifikation (früher als Swagger bezeichnet)](https://aka.ms/ams-v3-rest-sdk).
    Macht das Schema für alle Dienstkomponenten verfügbar, einschließlich dateibasierter Codierung.
* SDKs verfügbar für [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [Node.js](https://aka.ms/ams-v3-nodejs-ref), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Go](https://aka.ms/ams-v3-go-ref) und Ruby.
* [Azure CLI](https://aka.ms/ams-v3-cli-ref)-Integration für einfache Skriptunterstützung.

### <a name="new-features"></a>Neue Funktionen

* Für dateibasierte Auftragsverarbeitung können Sie als Eingabe eine HTTP(S)-URL verwenden.<br/>Sie müssen noch keine Inhalte in Azure gespeichert haben und müssen auch keine Objekte erstellen.
* Führt das Konzept von [Transformationen](transforms-jobs-concept.md) für dateibasierte Auftragsverarbeitung ein. Eine Transformation kann verwendet werden, um wiederverwendbare Konfigurationen und Azure Resource Manager-Vorlagen zu erstellen, und um Verarbeitungseinstellungen zwischen mehreren Kunden oder Mandanten zu isolieren.
* Ein Medienobjekt kann mehrere [Streaminglocators](streaming-locators-concept.md) mit verschiedenen Einstellungen für die [dynamische Paketerstellung](dynamic-packaging-overview.md) und dynamische Verschlüsselung haben.
* Der [Inhaltsschutz](content-key-policy-concept.md) unterstützt Features mit mehreren Schlüsseln.
* Wenn Media Services zum Transcodieren eines Beitragsfeeds mit Einzelbitrate in einen Ausgabestream mit mehreren Bitraten verwendet wird, können Sie Liveereignisse streamen, die bis zu 24 Stunden lang sind.
* Neue Unterstützung für Livestreaming mit niedriger Latenz für Liveereignisse. Weitere Informationen finden Sie unter [Latenz](live-event-latency.md).
* Die Vorschau von Liveereignissen unterstützt die [dynamische Paketerstellung](dynamic-packaging-overview.md) und dynamische Verschlüsselung. Dadurch wird in der Vorschau der Inhaltsschutz sowie auch die Paketerstellung für DASH und HLS ermöglicht.
* Die Liveausgabe ist einfacher zu verwenden als die Programmentität in den v2-APIs. 
* Verbesserte RTMP-Unterstützung (höhere Stabilität und bessere Unterstützung für Quellcodierer).
* Sichere RTMPS-Erfassung.<br/>Bei der Erstellung eines Liveereignisses erhalten Sie vier Erfassungs-URLs. Die vier Erfassungs-URLs sind nahezu identisch und verfügen über das gleiche Streamingtoken (AppId). Nur der Portnummernteil unterscheidet sich. Zwei der URLs dienen als primäre URL und Backup-URL für RTMPS.   
* Sie verfügen über rollenbasierte Zugriffssteuerung (RBAC) für Ihre Entitäten. 

## <a name="changes-from-v2"></a>Änderungen von V2

* Für Ressourcen, die mit v3 erstellt wurden, unterstützt Media Services nur die serverseitige Speicherverschlüsselung, siehe [Azure Storage Service Encryption für ruhende Daten](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * Sie können v3-APIs mit Ressourcen verwenden, die mit v2-APIs erstellt wurden, sofern für diese [Speicherverschlüsselung](../previous/media-services-rest-storage-encryption.md) (AES-256) von Media Services bereitgestellt wurde.
    * Mit der älteren AES-256-[Speicherverschlüsselung](../previous/media-services-rest-storage-encryption.md) können Sie mithilfe von v3-APIs keine neuen Medienobjekte erstellen.
* Die v3-SDKs sind jetzt vom Storage-SDK entkoppelt, wodurch sich eine bessere Kontrolle über das zu verwendende Storage-SDK ergibt und Versionsprobleme vermieden werden. 
* In den v3-APIs werden alle Codierungsbitraten in Bits pro Sekunde angegeben. Dies unterscheidet sich von den Voreinstellungen von v2 Media Encoder Standard. Beispielsweise würde die Bitrate in v2 mit 128 (Kbit/s), in v3 jedoch mit 12.8000 (Bits/Sekunde) angegeben. 
* Entities AssetFiles, AccessPolicies und IngestManifests sind in v3 nicht vorhanden.
* Die Eigenschaft „IAsset.ParentAssets“ ist in v3 nicht vorhanden.
* ContentKeys ist jetzt keine Entität mehr, sondern eine Eigenschaft des Streaminglocators.
* Die Event Grid-Unterstützung ersetzt NotificationEndpoints.
* Die folgenden Entitäten wurden umbenannt:
    * Die Auftragsausgabe ersetzt die Aufgabe und ist jetzt Teil eines Auftrags.
    * Der Streaminglocator ersetzt den Locator.
    * Das Liveereignis ersetzt den Kanal.<br/>Die Liveereignisabrechnung basiert auf Livekanal-Verbrauchseinheiten. Weitere Informationen finden Sie unter [Zustandswerte von Liveereignissen und Abrechnung](live-event-states-billing.md) und [Media Services – Preise](https://azure.microsoft.com/pricing/details/media-services/).
    * Die Liveausgabe ersetzt das Programm.
* Liveausgaben werden bei der Erstellung gestartet und beim Löschen beendet. In den v2-APIs haben Programme anders funktioniert, sie mussten nach der Erstellung gestartet werden.
*  Um Informationen zu einem Auftrag abzurufen, müssen Sie den Transformationsnamen wissen, unter dem der Auftrag erstellt wurde. 

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Featurelücken in Bezug auf v2-APIs

Die v3-API weist in Bezug auf die v2-API die folgenden Featurelücken auf. Am Schließen der Lücken wird derzeit gearbeitet.

* Der Zugriff auf den [Premium-Encoder](../previous/media-services-premium-workflow-encoder-formats.md) und die älteren [Media Analytics-Prozessoren](../previous/media-services-analytics-overview.md) (Azure Media Services Indexer 2 Preview, Face Redactor usw.) ist über v3 nicht möglich.<br/>Kunden, die eine Migration von der Media Indexer 1- oder 2-Vorschauversion durchführen möchten, können sofort die AudioAnalyzer-Voreinstellung in der v3-API verwenden.  Diese neue Voreinstellung enthält mehr Funktionen als die ältere Media Indexer 1- oder 2-Version. 
* Viele der [erweiterten Features von Media Encoder Standard in v2-APIs](../previous/media-services-advanced-encoding-with-mes.md) sind derzeit in v3 nicht verfügbar, z. B.:
    * Ausschneiden (für On-Demand- und Live-Szenarien)
    * Zusammenfügen von Medienobjekten
    * Überlagerungen
    * Zuschneiden
    * Miniaturbild-Sprites
    * Einfügen einer stillen Audiospur bei einer Eingabe ohne Audio
    * Einfügen einer Videospur, wenn die Eingabe kein Video enthält
* Liveereignisse mit Transcodierung unterstützen derzeit keine Slate-Einfügungen beim Streamen und Werbemarkereinfügungen über API-Aufrufe. 

> [!NOTE]
> Erstellen Sie ein Lesezeichen für diesen Artikel, und überprüfen Sie ihn regelmäßig auf Aktualisierungen.
 
## <a name="code-differences"></a>Codeunterschiede

Die folgende Tabelle zeigt die Codeunterschiede zwischen v2 und v3 für gängige Szenarien.

|Szenario|V2-API|V3-API|
|---|---|---|
|Erstellen eines Medienobjekts und Hochladen einer Datei |[Beispiel für v2-.NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[Beispiel für v3-.NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Übermitteln eines Auftrags|[Beispiel für v2-.NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[Beispiel für v3-.NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Zeigt, wie zuerst eine Transformation erstellt und dann ein Auftrag übermittelt wird.|
|Veröffentlichen eines Medienobjekts mit AES-Verschlüsselung |1. Erstellen von ContentKeyAuthorizationPolicyOption<br/>2. Erstellen von ContentKeyAuthorizationPolicy<br/>3. Erstellen von AssetDeliveryPolicy<br/>4. Erstellen eines Medienobjekts und Hochladen von Inhalten ODER Übermitteln eines Auftrags und Verwenden des Ausgabemedienobjekts<br/>5. Zuordnen von AssetDeliveryPolicy zu Medienobjekt<br/>6. Erstellen von ContentKey<br/>7. Zuordnen von ContentKey zu einem Medienobjekt<br/>8. Erstellen von AccessPolicy<br/>9. Erstellen von Locator<br/><br/>[Beispiel für v2-.NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Erstellen einer Richtlinie für Inhaltsschlüssel<br/>2. Erstellen eines Medienobjekts<br/>3. Hochladen von Inhalten oder Verwenden von Medienobjekt als JobOutput<br/>4. Erstellen eines Streaminglocators<br/><br/>[Beispiel für v3-.NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Abrufen von Auftragsdetails und Verwalten von Aufträgen |[Verwalten von Aufträgen mit v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Verwalten von Aufträgen mit v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="known-issues"></a>Bekannte Probleme

* Derzeit können Sie das Azure-Portal nicht für die Verwaltung von v3-Ressourcen verwenden. Verwenden Sie die [REST-API](https://aka.ms/ams-v3-rest-sdk), CLI oder eines der unterstützten SDKs.
* Sie müssen in Ihrem Konto reservierte Einheiten für Medien (Media Reserved Units, MRUs) bereitstellen, um die Parallelität und Leistung Ihrer Aufträge zu steuern. Das gilt insbesondere für Aufträge mit Video- oder Audioanalyse. Weitere Informationen finden Sie unter [Übersicht über das Skalieren der Medienverarbeitung](../previous/media-services-scale-media-processing-overview.md). Die MRUs können per [CLI 2.0 für Media Services v3](media-reserved-units-cli-how-to.md), über das [Azure-Portal](../previous/media-services-portal-scale-media-processing.md) oder mithilfe der [v2-APIs](../previous/media-services-dotnet-encoding-units.md) verwaltet werden. MRUs müssen unabhängig davon bereitgestellt werden, ob Sie Media Services v2- oder v3-APIs verwenden.
* Mit der v3-API erstellte Media Services-Entitäten können von der v2-API nicht verwaltet werden.  
* Es wird nicht empfohlen, mit v2-APIs erstellte Entitäten über die v3-APIs zu verwalten. Es folgen Beispiele für die Unterschiede, aufgrund deren die Entitäten in zwei Versionen nicht kompatibel sind:   
    * In v2 erstellte Aufträge und Aufgaben werden in v3 nicht angezeigt, da sie keiner Transformation zugeordnet sind. Es wird empfohlen, zu v3-Transformationen und -Aufträge zu wechseln. Während des Wechsels müssen die aktiven v2-Aufträge für einen relativ kurzen Zeitraum überwacht werden.
    * Mit v2 erstellte Kanäle und Programme (die Liveereignissen und Liveausgaben in v3 zugeordnet sind) können nicht mehr mit v3 verwaltet werden. Es wird empfohlen, zu einem günstigen Zeitpunkt (beim Beenden des Kanals) zu v3-Liveereignissen und -Liveausgaben zu wechseln.<br/>Derzeit können Sie kontinuierlich ausgeführte Kanäle nicht migrieren.  

> [!NOTE]
> Diese Seite wird laufend aktualisiert, da das Media Services-Team die v3-APIs weiterhin verbessert und die Diskrepanz zwischen den Versionen behebt.

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="next-steps"></a>Nächste Schritte

Um zu erfahren, wie einfach es ist, mit der Codierung und dem Streaming von Videodateien zu beginnen, lesen Sie [Streamen von Dateien](stream-files-dotnet-quickstart.md). 

