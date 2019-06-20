---
title: Azure Media Services-Konzepte | Microsoft Docs
description: Dieses Thema bietet eine Übersicht über Azure Media Services-Konzepte.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 2b28dde812dcce120c951730c27809f7f024e122
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64681558"
---
# <a name="azure-media-services-concepts"></a>Azure Media Services-Konzepte 

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

Dieses Thema bietet eine Übersicht über die wichtigsten Konzepte von Media Services.

## <a name="a-idassetsassets-and-storage"></a><a id="assets"/>Medienobjekte und Speicher
### <a name="assets"></a>Objekte
Ein [Medienobjekt](https://docs.microsoft.com/rest/api/media/operations/asset) enthält digitale Dateien (z.B. Video, Audio, Bilder, Sammlungen von Miniaturansichten, Textspuren und Untertiteldateien) sowie die Metadaten zu diesen Dateien. Nachdem die digitalen Dateien in ein Medienobjekt geladen wurden, können Sie in den Codierungs- und Streaming-Workflows der Media Services verwendet werden.

Ein Medienobjekt ist einem Blobcontainer im Azure Storage-Konto zugeordnet, und die im Medienobjekt enthaltenen Dateien werden als Blockblobs in diesem Container gespeichert. Seitenblobs werden von Azure Media Services nicht unterstützt.

Bei der Entscheidung, welche Medieninhalte in einem Medienobjekt hochgeladen und gespeichert werden sollen, ist Folgendes zu beachten:

* Ein Medienobjekt sollte nur eine einzige, eindeutige Instanz des Medieninhalts enthalten, beispielsweise eine einzige Bearbeitung einer TV-Folge, eines Films oder einer Werbung.
* Ein Medienobjekt sollte nicht mehrere Varianten oder Bearbeitungen einer audiovisuellen Datei enthalten. Ein Beispiel für eine nicht ordnungsgemäße Verwendung eines Medienobjekts wäre der Versuch, mehrere TV-Folgen, Werbungen oder mehrere Kameraeinstellungen aus einer einzigen Produktion in einem Medienobjekt zu speichern. Das Speichern mehrerer Varianten oder Bearbeitungen einer audiovisuellen Datei in einem Medienobjekt kann zu Schwierigkeiten beim Übermitteln von Codierungsaufträgen, beim Streamen und beim Sichern der Übermittlung des Medienobjekts zu einem späteren Zeitpunkt im Workflow führen.  

### <a name="asset-file"></a>Medienobjektdatei
Die [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) -Entität stellt eine tatsächliche Video- oder Audiodatei dar, die in einem Blob-Container gespeichert ist. Eine Medienobjektdatei ist immer mit einem Medienobjekt verknüpft, wobei ein Medienobjekt eine oder mehrere Dateien enthalten kann. Der Media Services Encoder-Task schlägt fehl, wenn ein Medienobjektdatei-Objekt keiner digitalen Datei in einem BLOB-Container zugeordnet ist.

Die **AssetFile** -Instanz und die eigentliche Mediendatei sind zwei verschiedene Objekte. Die AssetFile-Instanz enthält Metadaten zur Mediendatei, während die Mediendatei die tatsächlichen Medieninhalte enthält.

Sie sollten nicht versuchen, den Inhalt von BLOB-Containern, die mit Media Services generiert wurden, ohne die Verwendung von Media Service-APIs zu ändern.

### <a name="asset-encryption-options"></a>Optionen zur Medienobjektverschlüsselung
Je nach Art der Inhalte, die Sie hochladen, speichern und bereitstellen möchten, stellt Media Services verschiedene Verschlüsselungsoptionen zur Verfügung, aus denen Sie auswählen können.

>[!NOTE]
>Es wird keine Verschlüsselung verwendet. Dies ist der Standardwert. Bei Verwendung dieser Option sind Ihre Inhalte während der Übertragung oder im Ruhezustand im Speicher nicht geschützt.

Wenn Sie planen, eine MP4-Datei über progressives Herunterladen zu übermitteln, verwenden Sie diese Option zum Hochladen der Inhalte.

**StorageEncrypted** – Verwenden Sie diese Option, um Ihre unverschlüsselten Inhalte lokal mithilfe der AES-256-Bit-Verschlüsselung zu verschlüsseln und sie dann in Azure Storage hochzuladen, wo sie verschlüsselt im Ruhezustand gespeichert werden. Medienobjekte, die durch Speicherverschlüsselung geschützt sind, werden automatisch entschlüsselt, vor der Codierung in einem verschlüsselten Dateisystem platziert und optional vor dem Hochladen als neues Ausgabemedienobjekt erneut verschlüsselt. Der primäre Anwendungsfall für die Speicherverschlüsselung ist, wenn Sie Ihre qualitativ hochwertigen Eingabemediendateien mit starker Verschlüsselung beim Speichern im Ruhezustand auf dem Datenträger sichern möchten. 

Um ein speicherverschlüsseltes Medienobjekt zu übermitteln, müssen Sie die Übermittlungsrichtlinie des Medienobjekts konfigurieren und Media Services so mitteilen, wie die Inhalte bereitgestellt werden sollen. Bevor das Medienobjekt gestreamt werden kann, wird die Speicherverschlüsselung vom Streamingserver entfernt und der Inhalt mithilfe der angegebenen Übermittlungsrichtlinie (AES, PlayReady oder unverschlüsselt) gestreamt. 

**CommonEncryptionProtected**: Verwenden Sie diese Option, wenn Sie Inhalte mit Common Encryption oder PlayReady-DRM (z.B. mit PlayReady-DRM geschütztes Smooth Streaming) verschlüsseln (oder bereits verschlüsselte Inhalte hochladen) möchten.

**EnvelopeEncryptionProtected**: Verwenden Sie diese Option, wenn Sie HTTP Live Streaming (HLS) schützen (oder bereits geschütztes HLS hochladen) möchten, das mit dem Advanced Encryption Standard (AES) verschlüsselt ist. Wenn Sie bereits mit AES verschlüsseltes HLS hochladen, muss die Verschlüsselung mithilfe von Transform Manager erfolgt sein.

### <a name="access-policy"></a>Zugriffsrichtlinie
Eine [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) definiert Berechtigungen (wie Lesen, Schreiben und Auflisten) sowie die Dauer des Zugriffs auf ein Medienobjekt. Ein AccessPolicy-Objekt wird normalerweise an einen Locator übergeben, der anschließend für den Zugriff auf die in einem Medienobjekt enthaltenen Dateien verwendet wird.

>[!NOTE]
>Es gilt ein Grenzwert von 1.000.000 Richtlinien für verschiedene AMS-Richtlinien (z.B. für die Locator-Richtlinie oder für ContentKeyAuthorizationPolicy). Wenn Sie immer die gleichen Tage/Zugriffsberechtigungen verwenden, z.B. Richtlinien für Locator, die für einen längeren Zeitraum vorgesehen sind (Richtlinien ohne Upload), sollten Sie dieselbe Richtlinien-ID verwenden. Weitere Informationen finden Sie in [diesem](media-services-dotnet-manage-entities.md#limit-access-policies) Thema.

### <a name="blob-container"></a>BLOB-Container
Ein BLOB-Container dient zur Gruppierung eines Satzes von BLOBs. BLOB-Container werden in Media Services als Grenzpunkt für die Zugriffssteuerung und SAS-Locators (Shared Access Signature) für Medienobjekte verwendet. Ein Azure Storage-Konto kann eine unbegrenzte Anzahl von BLOB-Containern enthalten. In einem Container kann eine beliebige Anzahl von BLOBs gespeichert sein.

>[!NOTE]
> Sie sollten nicht versuchen, den Inhalt von BLOB-Containern, die mit Media Services generiert wurden, ohne die Verwendung von Media Service-APIs zu ändern.
> 
> 

### <a name="a-idlocatorslocators"></a><a id="locators"/>Locators
[Locator](https://docs.microsoft.com/rest/api/media/operations/locator)bieten einen Einstiegspunkt für den Zugriff auf die in einem Medienobjekt enthaltenen Dateien. Anhand einer Zugriffsrichtlinie werden die Berechtigungen eines Clients und die Dauer definiert, für die der Client auf eine bestimmte Ressource zugreifen kann. Locator können eine n:1-Beziehung mit einer Zugriffsrichtlinie aufweisen, wobei verschiedene Locator verschiedenen Clients verschiedene Startzeiten und Verbindungstypen bereitstellen können, wobei alle dieselben Berechtigungen und Einstellungen für die Dauer verwenden; aufgrund einer Einschränkung der Richtlinie für den gemeinsamen Zugriff, die von Azure Storage Services festgelegt wird, können jedoch nicht mehr als fünf eindeutige Locator mit einem bestimmten Medienobjekt gleichzeitig verknüpft sein. 

Media Services unterstützt zwei Arten von Locatorobjekten: OnDemandOrigin-Locators, die zum Streamen von Medien (z.B. MPEG DASH, HLS oder Smooth Streaming) oder für den progressiven Download von Medien verwendet werden, und SAS-URL-Locators, die zum Hochladen oder Herunterladen von Mediendateien in bzw. von Azure Storage verwendet werden. 

>[!NOTE]
>Die Berechtigung zum Auflisten (AccessPermissions.List) sollte beim Erstellen eines OnDemandOrigin-Locators nicht verwendet werden. 

### <a name="storage-account"></a>Speicherkonto
Alle Zugriffe auf den Azure-Speicher erfolgen über ein Speicherkonto. Ein Media Service-Konto kann mit einem oder mehreren Speicherkonten verknüpft werden. Ein Konto kann eine unbegrenzte Anzahl von Containern enthalten, solange die Gesamtgröße unter 500 TB pro Speicherkonto bleibt.  Media Services enthält Tools auf SDK-Ebene, mit denen Sie mehrere Speicherkonten verwalten und bei der Verteilung Ihrer Medienobjekte einen Lastenausgleich durchführen können. Beim Hochladen der Objekte in diese Konten kann dieser Ausgleich basierend auf Kennzahlen oder anhand einer zufälligen Verteilung erfolgen. Weitere Informationen finden Sie unter [Arbeiten mit Azure Storage](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

## <a name="jobs-and-tasks"></a>Aufträge und Aufgaben
Ein [Auftrag](https://docs.microsoft.com/rest/api/media/operations/job) wird normalerweise zum Verarbeiten (beispielsweise zum Indizieren oder Codieren) einer einzelnen Audio-/Videopräsentation verwendet. Wenn Sie mehrere Videos verarbeiten, erstellen Sie einen Auftrag für jedes zu verschlüsselnde Video.

Ein Auftrag enthält Metadaten zur auszuführenden Verarbeitung. Jeder Auftrag enthält eine oder mehrere [Aufgaben](https://docs.microsoft.com/rest/api/media/operations/task). Diese geben eine unteilbare Verarbeitungsaufgabe, die zugehörigen Eingabe- und Ausgabemedienobjekte, einen Medienprozessor und die zugehörigen Einstellungen an. Aufgaben innerhalb eines Auftrags können verkettet werden, wobei das Ausgabemedienobjekt als Eingabemedienobjekt für die nächste Aufgabe dient. Auf diese Weise kann ein Auftrag die gesamten Verarbeitungsschritte für eine Medienpräsentation umfassen.

## <a id="encoding"></a>Codieren
Azure Media Services bietet verschiedene Optionen für die Codierung von Medien in der Cloud.

Bei Verwendung von Media Services ist es wichtig, den Unterschied zwischen Codecs und Dateiformaten zu kennen.
Bei Codecs handelt es sich um die Software, die die Algorithmen für die Komprimierung/Dekomprimierung implementiert. Dateiformate dagegen sind die Container, die das komprimierte Video enthalten.

Media Services bietet dynamische Paketerstellung zum Übermitteln Ihrer MP4-Dateien mit adaptiver Bitrate oder Smooth Streaming-codierten Inhalte in Streamingformaten, die von Media Services unterstützt werden (MPEG-DASH, HLS, Smooth Streaming), ohne dass Sie diese Streamingformate erneut packen müssen.

Um die [dynamische Paketerstellung](media-services-dynamic-packaging-overview.md) nutzen zu können, müssen Sie Ihre Zwischendatei (Quelldatei) in einen Satz von MP4-Dateien oder Smooth Streaming-Dateien mit adaptiver Bitrate codieren. Außerdem muss mindestens ein Standard- oder Premium-Streamingendpunkt im gestarteten Zustand vorhanden sein.

Media Services unterstützt die folgenden On-Demand-Encoder, die in diesem Artikel beschrieben werden:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Media Encoder Premium Workflow](media-services-encode-asset.md#media-encoder-premium-workflow)

Informationen zu unterstützten Encodern finden Sie unter [Encoder](media-services-encode-asset.md).

## <a name="live-streaming"></a>Livestreaming
In Azure Media Services repräsentiert ein Kanal eine Pipeline zum Verarbeiten von Livestreaminginhalten. Es gibt zwei Arten, auf die Live-Eingabedatenströme von Kanälen empfangen werden können:

* Von einem lokalen Liveencoder wird Multi-Bitrate-RTMP oder -Smooth Streaming (fragmentiertes MP4) an den Kanal gesendet. Sie können die folgenden Liveencoder verwenden, von denen Multi-Bitrate-Smooth Streaming ausgegeben werden kann: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco und Elemental. Die folgenden Liveencoder geben RTMP aus: Adobe Flash Live Encoder, Telestream Wirecast, Teradek, Haivision und Tricaster. Die erfassten Streams durchlaufen Kanäle ohne weitere Transcodierung oder Codierung. Auf Anforderung wird der Datenstrom den Kunden von Media Services bereitgestellt.
* Ein Datenstrom mit nur einer Bitrate (in einem der folgenden Formate: RTMP oder Smooth Streaming [fragmentiertes MP4]) wird an den Kanal gesendet, der zum Ausführen der Livecodierung mit Media Services aktiviert wurde. Vom Kanal wird dann eine Livecodierung des Single-Bitrate-Eingabedatenstroms in einen Multi-Bitrate-Videodatenstrom (adaptiv) ausgeführt. Auf Anforderung wird der Datenstrom den Kunden von Media Services bereitgestellt.

### <a name="channel"></a>Kanal
In Media Services sind [Kanäle](https://docs.microsoft.com/rest/api/media/operations/channel)für die Verarbeitung von Livestreaminginhalten zuständig. Ein Kanal stellt einen Eingabeendpunkt (Erfassungs-URL) bereit, den Sie dann einem Live-Transcoder vorlegen. Der Kanal empfängt Live-Eingabestreams aus dem Live-Transcoder und stellt diese zum Streamen durch einen oder mehrere StreamingEndpoints zur Verfügung. Zudem bieten Kanäle einen Vorschauendpunkt (Vorschau-URL), mit dem Sie eine Vorschau des Streams anzeigen und überprüfen können, bevor Sie diesen weiter verarbeiten und übermitteln.

Beim Erstellen des Kanals können Sie die Erfassungs-URL und die Vorschau-URL erhalten. Zum Abfragen dieser URLs muss sich der Kanal nicht im gestarteten Zustand befinden. Wenn Sie zum Übermitteln von Daten aus einem Live-Transcoder an den Kanal bereit sind, muss der Kanal gestartet werden. Sobald der Live-Transcoder mit dem Erfassen von Daten beginnt, können Sie eine Vorschau des Streams anzeigen.

Jedes Media Services-Konto kann mehrere Kanäle, mehrere Programme und mehrere StreamingEndpoints enthalten. Je nach den Anforderungen an Bandbreite und Sicherheit können StreamingEndpoint-Dienste einem oder mehreren Kanälen zugeordnet werden. Jeder StreamingEndpoint kann von jedem Kanal abrufen.

### <a name="program-event"></a>Programm (Ereignis)
Mit einem [Programm (Ereignis)](https://docs.microsoft.com/rest/api/media/operations/program) können Sie die Veröffentlichung und Speicherung von Segmenten in einem Livestream steuern. Die Programme (Ereignisse) werden von den Kanälen verwaltet. Die Beziehung zwischen Kanal und Programm ähnelt herkömmlichen Medien, bei denen ein Kanal einen konstanten Stream von Inhalten aufweist und ein Programm auf ein zeitlich festgelegtes Ereignis in diesem Kanal ausgerichtet ist.
Über die Eigenschaft **ArchiveWindowLength** können Sie die Anzahl der Stunden festlegen, für die Sie den aufgezeichneten Inhalt für das Programm beibehalten möchten. Dieser Wert kann von mindestens 5 Minuten bis zu einem Höchstwert von 25 Stunden eingestellt werden.

ArchiveWindowLength bestimmt außerdem die maximale Dauer, für die Clients von der aktuellen Liveposition aus rückwärts suchen können. Programme können über die angegebene Zeitspanne laufen. Inhalte, die über das Zeitfenster hinausgehen, werden jedoch fortlaufend verworfen. Der Wert dieser Eigenschaft legt außerdem fest, wie lange Clientmanifeste wachsen können.

Jedes Programm (Ereignis) ist mit einem Medienobjekt verknüpft. Zum Veröffentlichen des Programms müssen Sie einen Locator für das zugehörige Medienobjekt erstellen. Mithilfe dieses Locators können Sie eine Streaming-URL erstellen, die Sie Ihren Kunden bereitstellen können.

Ein Kanal unterstützt bis zu drei gleichzeitig ausgeführte Programme, sodass Sie mehrere Archive desselben eingehenden Datenstroms erstellen können. Auf diese Weise können Sie verschiedene Teile eines Ereignisses nach Bedarf veröffentlichen und archivieren. Beispielsweise könnte Ihre Geschäftsanforderung darin bestehen, 6 Stunden eines Programms zu archivieren, jedoch nur die letzten 10 Minuten zu senden. Dazu müssen Sie zwei Programme erstellen, die gleichzeitig ausgeführt werden. Ein Programm wird auf die Archivierung von 6 Stunden des Ereignisses festgelegt. Dieses Programm wird jedoch nicht veröffentlicht. Das andere Programm wird auf die Archivierung von 10 Minuten festgelegt. Dieses Programm wird veröffentlicht.

Weitere Informationen finden Sie unter

* [Arbeiten mit Kanälen, die zum Ausführen von Livecodierung mit Azure Media Services aktiviert wurden](media-services-manage-live-encoder-enabled-channels.md)
* [Arbeiten mit Kanälen, die Livedatenströme mit mehreren Bitraten von lokalen Encodern empfangen](media-services-live-streaming-with-onprem-encoders.md)
* [Kontingente und Einschränkungen](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Schützen von Inhalten
### <a name="dynamic-encryption"></a>Dynamische Verschlüsselung
Azure Media Services ermöglicht die Sicherung Ihrer Medien ab dem Zeitpunkt, an dem sie Ihren Computer durch Speicherung, Verarbeitung und Übermittlung verlassen. Mit Media Services können Sie Inhalte dynamisch verschlüsselt übermitteln, und zwar mit AES (Advanced Encryption Standard unter Verwendung eines 128-Bit-Verschlüsselungsschlüssels) und Common Encryption (CENC, allgemeine Verschlüsselung) mit PlayReady- oder Widevine-DRM. Media Services bietet auch einen Dienst für die Übermittlung von AES-Schlüsseln und PlayReady-Lizenzen an autorisierte Clients.

Zurzeit können Sie die folgenden Streamingformate verschlüsseln: HLS, MPEG DASH und Smooth Streaming. Progressive Downloads können nicht verschlüsselt werden.

Wenn ein Medienobjekt durch Media Services verschlüsselt werden soll, müssen Sie dem Medienobjekt einen Verschlüsselungsschlüssel (CommonEncryption oder EnvelopeEncryption) zuordnen und zusätzlich Autorisierungsrichtlinien für den Schlüssel konfigurieren.

Um ein speicherverschlüsseltes Asset zu übermitteln, müssen Sie die Übermittlungsrichtlinie des Assets konfigurieren, um anzugeben, wie das Asset bereitgestellt werden soll.

Wenn ein Player einen Stream anfordert, verwendet Media Services den angegebenen Schlüssel, um den Inhalt mit einer Umschlagverschlüsselung (mit AES) oder allgemeinen Verschlüsselung (mit PlayReady oder Widevine) dynamisch zu verschlüsseln. Um den Stream zu entschlüsseln, fordert der Player den Schlüssel vom Schlüsselübermittlungsdienst an. Um zu entscheiden, ob der Benutzer berechtigt ist, den Schlüssel zu erhalten, wertet der Dienst die Autorisierungsrichtlinien aus, die Sie für den Schlüssel angegeben haben.

### <a name="token-restriction"></a>Token-Einschränkung
Die Autorisierungsrichtlinie für Inhaltsschlüssel kann eine oder mehrere Autorisierungseinschränkungen aufweisen: offen, Tokeneinschränkung oder IP-Einschränkung. Eine durch Token eingeschränkte Richtlinie gilt nur zusammen mit einem Token, das von einem Secure Token Service (STS) ausgestellt wurde. Media Services unterstützt Token im Simple Web Tokens (SWT)-Format und JSON Web Token (JWT)-Format. Secure Token Services werden von Media Services nicht bereitgestellt. Sie können einen benutzerdefinierten STS erstellen. Der STS muss für die Erstellung eines mit dem angegebenen Schlüssel signierten Tokens und die Ausstellungsansprüche konfiguriert sein, die Sie in der Konfiguration der Tokeneinschränkung angegeben haben. Der Schlüsselübermittlungsdienst von Media Services gibt den angeforderten Schlüssel (oder die Lizenz) an den Client zurück, wenn das Token gültig ist und die Ansprüche im Token mit den für den Schlüssel (oder die Lizenz) konfigurierten Ansprüchen übereinstimmen.

Bei der Konfiguration der Richtlinie mit Token-Einschränkung müssen die Parameter PrimaryVerificationKey, Issuer und Audience angegeben werden. PrimaryVerificationKey enthält den Schlüssel, mit dem das Token signiert wurde, und Issuer ist der STS (Secure Token Service), von dem das Token ausgestellt wurde. Audience (manchmal auch Scope) beschreibt den Verwendungszweck des Tokens oder die Ressource, auf die durch das Token Zugriff gewährt wird. Der Schlüsselübermittlungsdienst von Media Services überprüft, ob die Werte im Token mit den Werten in der Vorlage übereinstimmen.

Weitere Informationen finden Sie in den folgenden Artikeln:
- [Schützen von Inhalten – Übersicht](media-services-content-protection-overview.md)
- [Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselübermittlungsdiensts](media-services-protect-with-aes128.md)
- [Schützen mit PlayReady/Widevine](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Übermitteln
### <a name="a-iddynamicpackagingdynamic-packaging"></a><a id="dynamic_packaging"/>Dynamische Paketerstellung
Bei der Arbeit mit Media Services empfiehlt es sich, Zwischendateien immer in einen MP4-Satz mit adaptiver Bitrate zu codieren und anschließend mithilfe der [dynamischen Paketerstellung](media-services-dynamic-packaging-overview.md)in das gewünschte Format zu konvertieren.

### <a name="streaming-endpoint"></a>Streamingendpunkt
Ein StreamingEndpoint stellt einen Streamingdienst dar, der Inhalte zur weiteren Verteilung direkt in einer Client-Player-Anwendung oder einem Content Delivery Network (CDN) bereitstellen kann. (Azure Media Services bietet jetzt die Azure-CDN-Integration.) Der ausgehende Stream des Streamingendpunkt-Diensts kann ein Livestream oder ein Video on Demand-Medienobjekt in Ihrem Media Services-Konto sein. Media Services-Kunden wählen je nach ihren Anforderungen entweder einen **Standard**-Streamingendpunkt oder mindestens einen **Premium**-Streamingendpunkt. Ein Standard-Streamingendpunkt eignet sich für die meisten Streamingworkloads. 

Ein Standard-Streamingendpunkt eignet sich für die meisten Streamingworkloads. Standard-Streamingendpunkte bieten die Flexibilität, Ihre Inhalte durch dynamische Paketerstellung in HLS, MPEG-DASH und Smooth Streaming sowie dynamische Verschlüsselung für Microsoft PlayReady, Google Widevine, Apple Fairplay und AES128 auf praktisch jedem Gerät bereitzustellen.  Sie können durch die Azure-CDN-Integration auch von sehr kleinen bis zu sehr großen Zielgruppen mit Tausenden von gleichzeitigen Zuschauern skaliert werden. Wenn Sie über eine erweiterte Workload verfügen, Ihre Anforderungen an die Streamingkapazität nicht für die Durchsatzziele von Standard-Streamingendpunkten geeignet sind oder Sie die Kapazität des StreamingEndpoint-Diensts steuern möchten, um steigende Bandbreitenanforderungen zu bewältigen, wird empfohlen, Skalierungseinheiten (auch als Premium-Streamingeinheiten bezeichnet) zuzuordnen.

Es wird empfohlen, dynamische Paketerstellung oder dynamische Verschlüsselung zu verwenden.

>[!NOTE]
>Beim Erstellen Ihres AMS-Kontos wird dem Konto ein **Standard**-Streamingendpunkt mit dem Status **Beendet** hinzugefügt. Um mit dem Streamen der Inhalte zu beginnen und die dynamische Paketerstellung und dynamische Verschlüsselung zu nutzen, muss der Streamingendpunkt, von dem Sie Inhalte streamen möchten, den Status **Wird ausgeführt** aufweisen. 

Weitere Informationen finden Sie in [diesem](media-services-portal-manage-streaming-endpoints.md) Thema.

In der Standardeinstellung können Sie Ihrem Media Services-Konto bis zu zwei Streaming-Endpunkte hinzufügen. Informationen zum Anfordern eines höheren Kontingents finden Sie unter [Kontingente und Einschränkungen](media-services-quotas-and-limitations.md).

Eine Abrechnung erfolgt nur für ausgeführte StreamingEndpoints.

### <a name="asset-delivery-policy"></a>Übermittlungsrichtlinie für Medienobjekte
Einer der Schritte im Workflow zur Inhaltsübermittlung in Media Services ist das Konfigurieren von [Übermittlungsrichtlinien für Medienobjekte](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy), die gestreamt werden sollen. Anhand der Übermittlungsrichtlinie für Medienobjekte kann Media Services ermitteln, wie das Medienobjekt übermittelt werden soll, also mit welchem Streamingprotokoll das Medienobjekt dynamisch verpackt werden soll (z. B. MPEG-DASH, HLS, Smooth Streaming oder alle) und ob und wie das Medienobjekt ggf. dynamisch verschlüsselt werden soll (Umschlag- oder allgemeine Verschlüsselung).

Damit ein speicherverschlüsseltes Medienobjekt gestreamt werden kann, entfernt der Streamingserver die Speicherverschlüsselung und streamt den Inhalt anhand der angegebenen Übermittlungsrichtlinie. Wenn Sie ein Medienobjekt für die Übermittlung beispielsweise mit einem Schlüssel für die AES-Verschlüsselung (Advanced Encryption Standard) verschlüsseln möchten, legen Sie den Richtlinientyp auf DynamicEnvelopeEncryption fest. Um die Speicherverschlüsselung zu entfernen und das Medienobjekt unverschlüsselt zu streamen, legen Sie den Richtlinientyp auf NoDynamicEncryption fest.

### <a name="progressive-download"></a>Progressiver Download
Der progressive Download ermöglicht die Wiedergabe von Medien, bevor die gesamte Datei heruntergeladen wurde. Sie können nur eine MP4-Datei progressiv herunterladen.

>[!NOTE]
>Verschlüsselte Medienobjekte müssen entschlüsselt werden, wenn diese für den progressiven Download verfügbar sein sollen.

Um Benutzern URLs für den progressiven Download bereitzustellen, müssen Sie zuerst einen OnDemandOrigin-Locator erstellen. Beim Erstellen des Locators erhalten Sie den Basispfad für das Medienobjekt. Anschließend müssen Sie den Namen der MP4-Datei anhängen. Beispiel:

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>Streaming-URLs
Streamen Ihre Inhalte auf Clients. Um Benutzern Streaming-URLs bereitzustellen, müssen Sie zuerst einen OnDemandOrigin-Locator erstellen. Beim Erstellen des Locators erhalten Sie den Basispfad für das Medienobjekt mit den Inhalten, die Sie streamen möchten. Um diese Inhalte streamen zu können, müssen Sie diesen Pfad jedoch ändern. Zum Erstellen einer vollständigen URL für die Streaming-Manifestdatei müssen Sie den Pfadwert des Locators mit dem Dateinamen des Manifests (dateiname.ism) verketten. Fügen Sie dem Locatorpfad anschließend "/Manifest" und ein geeignetes Format (falls erforderlich) hinzu.

Sie können auch den Inhalt über eine SSL-Verbindung streamen. Zu diesem Zweck stellen Sie sicher, dass die Streaming-URLs mit HTTPS beginnen. AMS unterstützt derzeit SSL mit benutzerdefinierten Domänen nicht.  

>[!NOTE]
>Sie können nur über SSL streamen, wenn der Streamingendpunkt, von dem aus Sie Ihre Inhalte übermitteln, nach dem 10. September 2014 erstellt wurde. Wenn die Streaming-URLs auf Streamingendpunkten basieren, die nach dem 10. September erstellt wurden, enthält die URL „streaming.mediaservices.windows.net“ (das neue Format). Streaming-URLs, die „origin.mediaservices.windows.net“ (das alte Format) enthalten, unterstützen kein SSL. Wenn die URL im alten Format vorliegt und Sie über SSL streamen möchten, erstellen Sie einen neuen Streamingendpunkt. Verwenden Sie die URLs, die basierend auf dem neuen Streaming-Endpunkt erstellt wurden, um Ihre Inhalte über SSL zu streamen.

In der folgenden Liste werden verschiedene Streamingformate beschrieben und Beispiele angegeben:

* Smooth Streaming

{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

* MPEG DASH

{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest(Format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

* Apple HTTP Live Streaming (HLS) V4

{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest(Format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

* Apple HTTP Live Streaming (HLS) V3

{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest(Format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

