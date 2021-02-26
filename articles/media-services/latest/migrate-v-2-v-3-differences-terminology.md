---
title: Terminologie- und Entitätsänderungen in Media Services V3
description: In diesem Artikel werden die Unterschiede bei der Terminologie zwischen Azure Media Services V2 und V3 beschrieben.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: a2348e0578b60c59fd7205037bd42d7bb1e84fae
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953698"
---
# <a name="terminology-and-entity-changes-between-media-services-v2-and-v3"></a>Änderungen bei der Terminologie und bei Entitäten zwischen Media Services V2 und V3

![Logo des Migrationsleitfadens](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![Schritte bei der Migration 2](./media/migration-guide/steps-2.svg)

In diesem Artikel werden die Unterschiede bei der Terminologie zwischen Azure Media Services V2 und V3 beschrieben.

## <a name="naming-conventions"></a>Benennungskonventionen

Sehen Sie sich die Namenskonventionen an, die auf Media Services V3-Ressourcen angewandt werden. Informieren Sie sich auch über das [Benennen von Blobs](assets-concept.md#naming).

## <a name="terminology-changes"></a>Terminologieänderungen

- Ein *Locator* wird jetzt als *Streaminglocator* bezeichnet.
- Ein *Kanal* wird jetzt als *Liveereignis* bezeichnet.
- Ein *Programm* wird jetzt als *Liveausgabe* bezeichnet.
- Eine *Aufgabe* wird jetzt als *Auftragsausgabe* bezeichnet und ist Teil eines Auftrags.

## <a name="entity-changes"></a>Entitätsänderungen
| **V2-Entität**<!-- row --> | **V3-Entität** | **Leitfaden** | **Zugänglich in V3** | **Aktualisiert in V3** |
|--|--|--|--|--|
| `AccessPolicy`<!-- row --> | <!-- empty --> |  Die Entität `AccessPolicies` ist in V3 nicht vorhanden. | Nein | Nein |
| `Asset`<!-- row --> | `Asset` | <!-- empty --> | Ja | Ja |
| `AssetDeliveryPolicy`<!-- row --> | `StreamingPolicy` | <!-- empty --> | Ja | Nein |
| `AssetFile`<!-- row --> | <!-- empty --> |Die Entität `AssetFiles` ist in V3 nicht vorhanden. Die hochgeladenen Dateien (Speicherblobs) werden weiterhin als Dateien angesehen.<br/><br/> Verwenden Sie für das Durchlaufen der Blobs in einem Container stattdessen die Azure Storage-APIs. Es gibt zwei Möglichkeiten, mit einem Auftrag eine Transformation auf die Dateien anzuwenden:<br/><br/>Dateien wurden bereits in den Speicher hochgeladen: Der URI enthält die Objekt-ID für Aufträge, die für Medienobjekte in einem Speicherkonto ausgeführt werden sollen.<br/><br/>Dateien werden während der Transformations- und Auftragsverarbeitung hochgeladen: Das Medienobjekt wird im Speicher erstellt, eine SAS-URL wird zurückgegeben, Dateien werden in den Speicher hochgeladen, und anschließend wird die Transformation auf die Dateien angewandt. | Nein | Nein |
| `Channel`<!-- row --> | `LiveEvent` | Liveereignisse ersetzen Kanäle aus der V2-API. Sie übernehmen die meisten Funktionen und verfügen über mehr neue Features wie Livetranskriptionen, Standbymodus und Unterstützung für die RTMPS-Erfassung. <br/><br/>Siehe [Liveereignis im szenariobasierten Livestreaming](migrate-v-2-v-3-migration-scenario-based-live-streaming.md) | Nein | Nein |
| `ContentKey`<!-- row --> | <!-- empty --> | `ContentKeys` ist jetzt keine Entität mehr, sondern eine Eigenschaft des Streaminglocators.<br/><br/>  In V3 sind die Daten für symmetrische Schlüssel dem `StreamingLocator` (bei der Ausgabeverschlüsselung) oder dem Medienobjekt selbst (bei der clientseitigen Speicherverschlüsselung) zugeordnet. | Ja | Nein |
| `ContentKeyAuthorizationPolicy`<!-- row --> | `ContentKeyPolicy` | <!-- empty --> | Ja | Nein |
| `ContentKeyAuthorizationPolicyOption` <!-- row --> | <!-- empty --> |  `ContentKeyPolicyOptions` sind in der `ContentKeyPolicy` enthalten. | Ja | Nein |
| `IngestManifest`<!-- row --> | <!-- empty --> | Die Entität `IngestManifests` ist in V3 nicht vorhanden. Für das Hochladen von Dateien wird in V3 die Azure Storage-API verwendet. Zuerst werden Medienobjekte erstellt, und anschließend werden Dateien in den zugeordneten Speichercontainer hochgeladen. Es gibt viele alternative Möglichkeiten, Daten in einen Azure Storage-Container zu übertragen. `JobInputHttp` bietet auch eine Möglichkeit, die Auftragseingabe bei Bedarf von einer bestimmten URL herunterzuladen. | Nein | Nein |
| `IngestManifestAsset`<!-- row --> | <!-- empty --> | Es gibt viele alternative Möglichkeiten, Daten in einen Azure Storage-Container zu übertragen. `JobInputHttp` bietet auch eine Möglichkeit, die Auftragseingabe bei Bedarf von einer bestimmten URL herunterzuladen. | Nein | Nein |
| `IngestManifestFile`<!-- row --> | <!-- empty --> | Es gibt viele alternative Möglichkeiten, Daten in einen Azure Storage-Container zu übertragen. `JobInputHttp` bietet auch eine Möglichkeit, die Auftragseingabe bei Bedarf von einer bestimmten URL herunterzuladen. | Nein | Nein |
| `Job`<!-- row --> | `Job` | Erstellen Sie zunächst eine `Transform` und erst dann einen `Job`. | Nein | Nein |
| `JobTemplate`<!-- row --> | `Transform` | Verwenden Sie stattdessen einen `Transform`. Eine Transformation ist eine vom Auftrag unabhängige und wiederverwendbare Entität. | Nein | Nein |
| `Locator`<!-- row --> | `StreamingLocator` | <!--empty --> | Ja | Nein |
| `MediaProcessor`<!-- row --> | <!-- empty --> | Verwenden Sie beim Definieren einer Transformation die gewünschte Voreinstellung, anstatt den `MediaProcessor` anhand seines Namens zu verwenden. Mit der verwendeten Voreinstellung wird der vom Auftragssystem verwendete Medienprozessor bestimmt. Weitere Informationen finden Sie unter den Codierungsthemen in [Szenariobasierte Codierung](migrate-v-2-v-3-migration-scenario-based-encoding.md). <!--Probably needs a link to its own article so customers know Indexerv1 maps to AudioAnalyzerPreset in basic mode, etc.--> | Nein | Nicht verfügbar (in V2 schreibgeschützt) |
| `NotificationEndPoint`<!-- row --> | <!--empty --> | Benachrichtigungen werden in V3 über Azure Event Grid verarbeitet. Der `NotificationEndpoint` wird durch die Registrierung des Event Grid-Abonnements ersetzt, die auch die Konfiguration für die zu empfangenden Benachrichtigungstypen einschließt (wurde in V2 von der `JobNotificationSubscription` des Auftrags, der `TaskNotificationSubscription` der Aufgabe und der `ComponentMonitoringSetting` für Telemetriedaten behandelt). Die V2-Telemetriedaten wurden zwischen Azure Event Grid und Azure Monitor aufgeteilt, um sie an die Verbesserungen des größeren Azure-Ökosystems anzupassen. | Nein | Nein |
| `Program`<!-- row --> | `LiveOutput` | Liveausgaben ersetzen in der V3-API Programme.  | Nein | Nein |
| `StreamingEndpoint`<!-- row --> | `StreamingEndpoint` | Streamingendpunkte bleiben größtenteils unverändert. Sie werden für die dynamische Paketerstellung, Verschlüsselung und Übermittlung von HLS- und DASH-Inhalten für das Live- und On-Demand-Streaming entweder direkt an der Quelle oder über das CDN verwendet. Zu den neuen Features gehören die Unterstützung einer verbesserten Azure Monitor-Integration und die Diagrammerstellung. |  Ja | Ja |
| `Task`<!-- row --> | `JobOutput` | Ersetzt durch `JobOutput` (in der API nun keine separate Entität mehr).  Weitere Informationen finden Sie unter den Codierungsthemen in [Szenariobasierte Codierung](migrate-v-2-v-3-migration-scenario-based-encoding.md). | Nein | Nein |
| `TaskTemplate`<!-- row --> | `TransformOutput` | Ersetzt durch `TransformOutput` (in der API nun keine separate Entität mehr). Weitere Informationen finden Sie unter den Codierungsthemen in [Szenariobasierte Codierung](migrate-v-2-v-3-migration-scenario-based-encoding.md). | Nein | Nein |
| `Inputs`<!-- row --> | `Inputs` | Eingaben und Ausgaben befinden sich jetzt auf der Auftragsebene. Weitere Informationen finden Sie unter den Codierungsthemen in [Szenariobasierte Codierung](migrate-v-2-v-3-migration-scenario-based-encoding.md). | Nein | Nein |
| `Outputs`<!-- row --> | `Outputs` | Eingaben und Ausgaben befinden sich jetzt auf der Auftragsebene.  In V3 wurde das Metadatenformat von XML in JSON geändert.  Liveausgaben werden bei der Erstellung gestartet und beim Löschen beendet. Weitere Informationen finden Sie unter den Codierungsthemen in [Szenariobasierte Codierung](migrate-v-2-v-3-migration-scenario-based-encoding.md). | Nein | Nein |


| **Weitere Änderungen** | **Version 2**  | **Version 3** |
|---|---|---|
| **Speicher** <!--new row --> |||
| Speicher <!--new row --> | | Die V3-SDKs sind jetzt vom Storage SDK entkoppelt, sodass sich eine bessere Kontrolle über die zu verwendende Version des Storage SDK ergibt und Versionsprobleme vermieden werden.                      |
| **Codieren** <!--new row --> |||
| Bitraten für die Codierung <!--new row --> | Bitraten, gemessen in KBit/s, z. B.: 128 (KBit/s)| Bits pro Sekunde (Bit/s), z. B.: 128.000 (Bit/s)|
| Codieren von DRM mit FairPlay <!--new row --> | In Media Services V2 konnte der Initialisierungsvektor (IV) angegeben werden. | In Media Services V3 kann FairPlay IV nicht angegeben werden.|
| Premium-Encoder <!--new row --> | Premium-Encoder und ältere Indexer| Der Zugriff auf den [Premium-Encoder](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset) und die älteren [Media Analytics-Prozessoren](https://docs.microsoft.com/azure/media-services/previous/legacy-components) (Azure Media Services Indexer 2 Preview, Face Redactor usw.) ist über V3 nicht möglich. Im Media Encoder Standard wurde Unterstützung für die Audiokanalzuordnung hinzugefügt.  Weitere Informationen finden Sie in der Dokumentation zu [Audiodaten bei der Media Services-Codierung mit Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json).  | Weitere Informationen finden Sie unter den Codierungsthemen in [Szenariobasierte Codierung](migrate-v-2-v-3-migration-scenario-based-encoding.md). |
| **Transformationen und Aufträge** <!--new row -->|||
| Auftragsbasierte Verarbeitung von HTTPS <!--new row --> |<!-- empty -->| Für dateibasierte Auftragsverarbeitung können Sie als Eingabe eine HTTPS-URL verwenden. Sie müssen noch keine Inhalte in Azure gespeichert haben und müssen auch keine Medienobjekte erstellen. |
| ARM-Vorlagen für Aufträge <!--new row --> | ARM-Vorlagen waren in V2 noch nicht vorhanden. | Eine Transformation kann verwendet werden, um wiederverwendbare Konfigurationen und Azure Resource Manager-Vorlagen zu erstellen, und um Verarbeitungseinstellungen zwischen mehreren Kunden oder Mandanten zu isolieren. |
| **Liveereignisse** <!--new row --> |||
| Streamingendpunkt <!--new row --> | Ein Streamingendpunkt stellt einen Streamingdienst dar, der Inhalte zur weiteren Verteilung direkt in einer Clientwiedergabeanwendung oder einem Content Delivery Network (CDN) bereitstellen kann. | Streamingendpunkte bleiben größtenteils unverändert. Sie werden für die dynamische Paketerstellung, Verschlüsselung und Übermittlung von HLS- und DASH-Inhalten für das Live- und On-Demand-Streaming entweder direkt an der Quelle oder über das CDN verwendet.  Zu den neuen Features gehören die Unterstützung einer verbesserten Azure Monitor-Integration und die Diagrammerstellung. |
| Liveereigniskanäle <!--new row --> | Kanäle sind für die Verarbeitung von Livestreaminginhalten zuständig. Ein Kanal stellt einen Eingabeendpunkt (Erfassungs-URL) bereit, den Sie dann einem Live-Transcoder vorlegen. Der Kanal empfängt Liveeingabestreams vom Livetranscoder und stellt diese zum Streamen durch Streamingendpunkte zur Verfügung. Zudem bieten Kanäle einen Vorschauendpunkt (Vorschau-URL), mit dem Sie eine Vorschau des Streams anzeigen und überprüfen können, bevor Sie diesen weiter verarbeiten und übermitteln.| Liveereignisse ersetzen Kanäle aus der V2-API. Sie übernehmen die meisten Funktionen und verfügen über mehr neue Features wie Livetranskriptionen, Standbymodus und Unterstützung für die RTMPS-Erfassung. |
| Liveereignisprogramme <!--new row --> | Mit einem Programm können Sie die Veröffentlichung und Speicherung von Segmenten in einem Livestream steuern. Kanäle verwalten Programme. Die Beziehung zwischen Kanal und Programm ähnelt herkömmlichen Medien, bei denen ein Kanal einen konstanten Stream von Inhalten aufweist und ein Programm auf ein zeitlich festgelegtes Ereignis in diesem Kanal ausgerichtet ist. Sie können in Stunden angeben, wie lange der aufgezeichnete Inhalt für das Programm beibehalten werden soll. Legen Sie dazu die `ArchiveWindowLength`-Eigenschaft fest. Dieser Wert kann von mindestens 5 Minuten bis zu einem Höchstwert von 25 Stunden eingestellt werden.| Liveausgaben ersetzen in der V3-API Programme. |
| Länge von Liveereignissen <!--new row --> |<!-- empty -->| Wenn Media Services zum Transcodieren eines Beitragsfeeds mit Einzelbitrate in einen Ausgabestream mit mehreren Bitraten verwendet wird, können Sie Liveereignisse streamen, die bis zu 24/7 Stunden lang sind.|
| Latenz von Liveereignissen <!--new row --> |<!-- empty -->| Neue Unterstützung für Livestreaming mit niedriger Latenz für Liveereignisse. |
| Vorschau für Liveereignisse <!--new row --> |<!-- empty -->| Die Vorschau von Liveereignissen unterstützt die dynamische Paketerstellung und dynamische Verschlüsselung. Dadurch wird in der Vorschau der Inhaltsschutz sowie auch die Paketerstellung für DASH und HLS ermöglicht. |
| RTMPS für Liveereignisse <!--new row --> |<!-- empty-->| Verbesserte RTMPS-Unterstützung mit höherer Stabilität und besserer Unterstützung für Quellcodierer. |
| Sichere RTMPS-Erfassung für Liveereignisse <!--new row --> | | Bei der Erstellung eines Liveereignisses erhalten Sie vier Erfassungs-URLs. Die vier Erfassungs-URLs sind nahezu identisch und verfügen über das gleiche Streamingtoken (`AppId`). Nur der Portnummernteil unterscheidet sich. Zwei der URLs dienen als primäre URL und Backup-URL für RTMPS.| 
| Transkription von Liveereignissen <!--new row --> |<!-- empty--> | Azure Media Service übermittelt Video, Audio und Text in unterschiedlichen Protokollen. Wenn Sie Ihren Livestream mittels MPEG-DASH oder HLS/CMAF veröffentlichen, stellt unser Dienst neben den Video- und Audiospuren auch den transkribierten Text im IMSC1.1-kompatiblen TTML-Format bereit.|
| Standbymodus bei Liveereignissen <!--new row --> | In V2 war kein Standbymodus verfügbar. | Der Standbymodus ist ein neues V3-Feature, das bei der Verwaltung von Pools der heißen Ebene für Liveereignisse hilfreich ist. Kunden können jetzt ein Liveereignis zu niedrigeren Kosten im Standbymodus starten, bevor sie es in den Ausführungszustand versetzen. Dies verbessert die Startzeiten des Kanals und senkt die Kosten für den Betrieb von Pools auf heißer Ebene für schnellere Starts. |
| Abrechnung von Liveereignissen <!--new row --> | <!-- empty-->| Die Abrechnung von Liveereignissen basiert auf Livekanal-Verbrauchseinheiten. |
| Liveausgaben <!--new row --> | Programme mussten nach der Erstellung gestartet werden. | Liveausgaben werden bei der Erstellung gestartet und beim Löschen beendet. |

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
