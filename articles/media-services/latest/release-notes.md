---
title: 'Azure Media Services v3: Versionshinweise | Microsoft-Dokumentation'
description: Damit Sie bezüglich der aktuellen Entwicklungen auf dem neuesten Stand bleiben, bietet dieser Artikel Informationen zu Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 05/26/2019
ms.author: juliako
ms.openlocfilehash: dd632051ebce6afc3c1f7be118fe8d31cd806ec9
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66296807"
---
# <a name="azure-media-services-v3-release-notes"></a>Versionshinweise zu Azure Media Services v3

Damit Sie bezüglich der aktuellen Entwicklungen auf dem neuesten Stand bleiben, bietet dieser Artikel Informationen zu Folgendem:

* Neueste Versionen
* Bekannte Probleme
* Fehlerbehebungen
* Veraltete Funktionen

## <a name="known-issues"></a>Bekannte Probleme

> [!NOTE]
> Derzeit können Sie das Azure-Portal nicht für die Verwaltung von v3-Ressourcen verwenden. Verwenden Sie die [REST-API](https://aka.ms/ams-v3-rest-sdk), CLI oder eines der unterstützten SDKs.

Weitere Informationen finden Sie unter [Hinweise zur Migration von Media Services v2 zu v3](migrate-from-v2-to-v3.md#known-issues).

## <a name="may-2019"></a>Mai 2019

### <a name="hls-v4-and-above-support-in-dynamic-packaging"></a>Unterstützung von HLS v4 und höher in der dynamischen Paketerstellung 

Unterstützung für HLS (Version 4 oder höher) in der dynamischen Paketerstellung wurde für Ressourcen hinzugefügt, die mehrere Audiospuren mit mehreren Codecs und Sprachen besitzen.

Weitere Informationen finden Sie unter [Dynamische Paketerstellung](dynamic-packaging-overview.md).

### <a name="media-services-availability-in-korea-central-region"></a>Media Services-Verfügbarkeit in der Region „Südkorea, Mitte“

Media Services sind jetzt in der Region „Südkorea, Mitte“ verfügbar. 

Weiter Informationen finden Sie unter [Clouds und Regionen, in denen Media Services v3 verfügbar ist](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Leistungsverbesserungen

Updates wurden hinzugefügt, die Verbesserungen an der Media Services-Leistung umfassen.

* Die maximal unterstützte Dateigröße für die Verarbeitung wurde aktualisiert. Siehe [Kontingente und Einschränkungen](limits-quotas-constraints.md).
* [Verbesserungen der Codierungsgeschwindigkeit](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>April 2019

### <a name="new-presets"></a>Neue Voreinstellungen

* [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) wurde zu den Voreinstellungen des integrierten Analysetools hinzugefügt.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) wurde zu den Voreinstellungen des integrierten Encoders hinzugefügt. Weitere Informationen finden Sie unter [Inhaltsbezogene Codierung](cae-experimental.md). 

## <a name="march-2019"></a>März 2019

Die dynamische Paketerstellung unterstützt jetzt Dolby Atmos. Weitere Informationen finden Sie unter [Von der dynamischen Paketerstellung unterstützte Audiocodecs](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging).

Sie können nun eine Liste von Medienobjekt- oder Kontenfiltern angeben, die für Ihren Streaminglocator gelten würden. Weitere Informationen finden Sie unter [Zuordnen von Filtern mit Streaminglocator](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Februar 2019

Media Services v3 wird jetzt in nationalen Azure-Clouds unterstützt. Noch sind nicht alle Funktionen in allen Clouds verfügbar. Ausführliche Informationen finden Sie unter [Clouds und Regionen, in denen Azure Media Services v3 verfügbar ist](azure-clouds-regions.md).

Das Ereignis [Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) wurde den Azure Event Grid-Schemas für Media Services hinzugefügt.

## <a name="january-2019"></a>Januar 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard und MPI-Dateien 

Bei der Codierung mit Media Encoder Standard zum Erzeugen von MP4-Dateien wird eine neue MPI-Datei generiert und dem Ausgabeasset hinzugefügt. Diese MPI-Datei dient zum Verbessern der Leistung für die [dynamische Paketerstellung](dynamic-packaging-overview.md) und Streamingszenarios.

Sie sollten weder die MPI-Datei ändern oder entfernen noch beliebige Abhängigkeiten vom Vorhandensein (oder Nichtvorhandensein) einer solchen Datei in Ihren Dienst integrieren.

## <a name="december-2018"></a>Dezember 2018

Zu den Updates in der GA-Version der V3-API gehören:
       
* Die **PresentationTimeRange**-Eigenschaften sind nicht mehr für **Medienobjektfilter** und **Kontofilter** erforderlich. 
* Die Abfrageoptionen „$top“ und „$skip“ für **Aufträge** und **Transformationen** wurden entfernt, und „$orderby“ wurde hinzugefügt. Beim Hinzufügen der neuen Sortierungsfunktionalität wurde festgestellt, dass die Optionen „$top“ und „$skip“ versehentlich zuvor verfügbar gemacht wurden, obwohl sie nicht implementiert sind.
* Enumerationserweiterbarkeit wurde erneut aktiviert. Dieses Feature wurde in den Vorschauversionen des SDK aktiviert und wurde in der GA-Version versehentlich deaktiviert.
* Zwei vordefinierte Streamingrichtlinien wurden umbenannt. **SecureStreaming** ist jetzt **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** ist jetzt **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>November 2018

Das CLI 2.0-Modul ist ab sofort für [Azure Media Services v3 (allgemein verfügbar)](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) v2.0.50 verfügbar.

### <a name="new-commands"></a>Neue Befehle

- [az ams account](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az ams asset](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams content-key-policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az ams job](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [az ams live-event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [az ams live-output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [az ams streaming-endpoint](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [az ams streaming-locator](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az ams account mru:](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) Hiermit können Sie reservierte Einheiten für Medien verwalten. Weitere Informationen finden Sie unter [Skalieren von reservierten Einheiten für Medien](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Neue Features und Breaking Changes

#### <a name="asset-commands"></a>Assetbefehle

- Die Argumente ```--storage-account``` und ```--container``` wurden hinzugefügt.
- Standardwerte für Ablaufzeit (aktueller Zeitpunkt + 23 Std.) und Berechtigungen (Lesen) im Befehl ```az ams asset get-sas-url``` wurden hinzugefügt.

#### <a name="job-commands"></a>Auftragsbefehle

- Die Argumente ```--correlation-data``` und ```--label``` wurden hinzugefügt.
- ```--output-asset-names``` wurde in ```--output-assets``` umbenannt. Ab sofort wird eine durch Leerzeichen getrennte Ressourcenliste im Format „assetName=Bezeichnung“ akzeptiert. Assets ohne Bezeichnung können wie folgt gesendet werden: „assetName=“.

#### <a name="streaming-locator-commands"></a>streaming locator-Befehle

- Der Basisbefehl ```az ams streaming locator``` wurde durch ```az ams streaming-locator``` ersetzt.
- Die Argumente ```--streaming-locator-id``` und ```--alternative-media-id support``` wurden hinzugefügt.
- Das Argument ```--content-keys argument``` wurde aktualisiert.
- ```--content-policy-name``` wurde in ```--content-key-policy-name``` umbenannt.

#### <a name="streaming-policy-commands"></a>streaming policy-Richtlinien

- Der Basisbefehl ```az ams streaming policy``` wurde durch ```az ams streaming-policy``` ersetzt.
- Unterstützung für Verschlüsselungsparameter für ```az ams streaming-policy create``` wurde hinzugefügt.

#### <a name="transform-commands"></a>transform-Befehle

- Das Argument ```--preset-names``` wurde durch ```--preset``` ersetzt. Ab sofort kann nur noch eine einzelne Ausgabe/Voreinstellung festgelegt werden. (Wenn Sie weitere hinzufügen möchten, müssen Sie ```az ams transform output add``` ausführen.) Darüber hinaus können Sie eine benutzerdefinierte Voreinstellung für den Standard-Encoder (StandardEncoderPreset) festlegen, indem Sie den Pfad an Ihr benutzerdefiniertes JSON-Objekt übergeben.
- Für ```az ams transform output remove``` kann der zu entfernende Ausgabeindex übergeben werden.
- Die Argumente ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` wurden in ```az ams transform create``` und den Befehlen ```az ams transform output add``` hinzugefügt.

## <a name="october-2018---ga"></a>Oktober 2018 – Allgemeine Verfügbarkeit (GA)

In diesem Abschnitt werden die Oktober-Updates für Azure Media Services (AMS) beschrieben.

### <a name="rest-v3-ga-release"></a>REST v3-GA-Release

Das [REST v3-GA-Release](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) umfasst mehr APIs für den Livebetrieb, Manifestfilter für die Konto-/Ressourcenebene und DRM-Support.

#### <a name="azure-resource-management"></a>Azure-Ressourcenverwaltung 

Die Unterstützung der Azure-Ressourcenverwaltung ermöglicht eine einheitliche Verwaltung und Vorgangs-API (jetzt alles an einem Ort).

Ab dieser Version können Sie Resource Manager-Vorlagen verwenden, um Liveereignisse zu erstellen.

#### <a name="improvement-of-asset-operations"></a>Verbesserung von Medienobjektvorgängen 

Die folgenden Verbesserungen wurden eingeführt:

- Erfassen von HTTP(S)-URLs oder Azure Blob Storage-SAS-URLs
- Angeben von eigenen Containernamen für Medienobjekte 
- Einfachere Unterstützung der Ausgabe zur Erstellung von benutzerdefinierten Workflows mit Azure Functions

#### <a name="new-transform-object"></a>Neues Transform-Objekt

Mit dem neuen **Transform**-Objekt wird das Codierungsmodell vereinfacht. Es ist mit dem neuen Objekt einfach, Resource Manager-Vorlagen und -Voreinstellungen für die Codierung zu erstellen und gemeinsam zu nutzen. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Azure Active Directory-Authentifizierung und rollenbasierte Zugriffssteuerung

Mit der Azure AD-Authentifizierung und rollenbasierten Zugriffssteuerung (RBAC) werden sichere Transformationen, Liveereignisse, Richtlinien für Inhaltsschlüssel oder Medienobjekte nach Rolle oder Benutzer in Azure AD ermöglicht.

#### <a name="client-sdks"></a>Client-SDKs  

In Media Services v3 unterstützte Sprachen: .NET Core, Java, Node.js, Ruby, TypeScript, Python, Go.

#### <a name="live-encoding-updates"></a>Updates der Livecodierung

Die folgenden Updates für die Livecodierung wurden eingeführt:

- Neuer Modus mit geringer Latenz für den Livebetrieb (10 Sekunden für den End-to-End-Vorgang).
- Verbesserte RTMP-Unterstützung (höhere Stabilität und bessere Unterstützung für Quellcodierer).
- Sichere RTMPS-Erfassung.

    Bei der Erstellung eines Liveereignisses erhalten Sie jetzt vier Erfassungs-URLs. Die vier Erfassungs-URLs sind nahezu identisch und verfügen über das gleiche Streamingtoken (AppId). Nur der Portnummernteil unterscheidet sich. Zwei der URLs dienen als primäre URL und Backup-URL für RTMPS. 
- Unterstützung der 24-Stunden-Transcodierung. 
- Verbesserte Unterstützung für Werbeeinblendungen in RTMP über SCTE35.

#### <a name="improved-event-grid-support"></a>Verbesserte Event Grid-Unterstützung

Folgende Verbesserungen der Event Grid-Unterstützung wurden eingeführt:

- Azure Event Grid-Integration, um die Entwicklung mit Logic Apps und Azure Functions zu vereinfachen. 
- Abonnieren von Ereignissen für Codierung, Livekanäle und mehr.

### <a name="cmaf-support"></a>CMAF-Unterstützung

CMAF- und „cbcs“-Verschlüsselungsunterstützung für Apple HLS- (iOS 11 und höher) und MPEG-DASH-Player, die CMAF unterstützen.

### <a name="video-indexer"></a>Video Indexer

Das Video Indexer-GA-Release wurde im August angekündigt. Neue Informationen zu derzeit unterstützten Funktionen finden Sie unter [Was ist Video Indexer?](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Pläne für Änderungen

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Das Azure CLI 2.0-Modul mit Vorgängen für alle Features (z.B. Livebetrieb, Richtlinien für Inhaltsschlüssel, Konto-/Ressourcenfilter, Streamingrichtlinien) ist in Kürze verfügbar. 

### <a name="known-issues"></a>Bekannte Probleme

Nur Kunden, die die Vorschauversion der API für Ressourcen- oder Kontofilter verwendet haben, sind vom folgenden Problem betroffen.

Wenn Sie zwischen dem 28.09. und dem 12.10. mit der Media Services v3 CLI oder den zugehörigen APIs Ressourcen- oder Kontofilter erstellt haben, müssen Sie aufgrund eines Versionskonflikts alle Ressourcen- und Kontofilter entfernen und neu erstellen. 

## <a name="may-2018---preview"></a>Mai 2018 – Vorschau

### <a name="net-sdk"></a>.NET SDK

Das .NET SDK enthält die folgenden Funktionen:

* **Transformationen** und **Aufträge** zum Codieren oder Analysieren von Medieninhalten. Beispiele finden Sie in den Tutorials zum [Streamen von Dateien](stream-files-tutorial-with-api.md) und [Analysieren](analyze-videos-tutorial-with-api.md).
* **Streaminglocators** zum Veröffentlichen und Streamen von Inhalten auf Endbenutzergeräten
* **Streamingrichtlinien** und **Inhaltsschlüssel-Richtlinien** zum Konfigurieren der Schlüsselübermittlung und des Inhaltsschutzes (DRM) bei der Inhaltsbereitstellung
* **Liveereignisse** und **Liveausgaben** zum Konfigurieren der Erfassung und Archivierung von Livestreaminginhalten
* **Medienobjekte** zum Speichern und Veröffentlichen von Medieninhalten in Azure Storage 
* **Streamingendpunkte** zum Konfigurieren und Skalieren der dynamischen Paketerstellung, der Verschlüsselung und des Streamings von Live- und On-Demand-Medieninhalten

### <a name="known-issues"></a>Bekannte Probleme

* Wenn Sie einen Auftrag übermitteln, können Sie angeben, dass Ihr Quellvideo mithilfe von HTTPS, URLs, SAS-URLs oder Pfaden zu Dateien in Azure Blob Storage erfasst wird. AMS v3 unterstützt derzeit keine segmentierte Transfercodierung über HTTPS-URLs.

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="next-steps"></a>Nächste Schritte

[Übersicht](media-services-overview.md)
