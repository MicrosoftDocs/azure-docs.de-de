---
title: Migrieren von Azure Media Services v2 zu v3
description: Dieser Artikel beschreibt Änderungen, die in Azure Media Services V3 eingeführt wurden, und zeigt Unterschiede zwischen den beiden Versionen.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure media services, streamen, übertragen, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 10/01/2020
ms.author: inhenkel
ms.openlocfilehash: 14544f58bcda56a55cef33de8fe0a70d5859b589
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510946"
---
# <a name="media-services-v2-vs-v3"></a>Media Services v2 und v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Dieser Artikel beschreibt Änderungen, die in Azure Media Services V3 eingeführt wurden, und zeigt Unterschiede zwischen den beiden Versionen.

## <a name="general-changes-from-v2"></a>Allgemeine Änderungen von v2

* Informationen zu Änderungen in Bezug auf Medienobjekte finden Sie weiter unten im Abschnitt [Für Medienobjekte spezifische Änderungen](#asset-specific-changes).
* Die v3-SDKs sind jetzt vom Storage-SDK entkoppelt, wodurch sich eine bessere Kontrolle über das zu verwendende Storage-SDK ergibt und Versionsprobleme vermieden werden. 
* In den v3-APIs werden alle Codierungsbitraten in Bits pro Sekunde angegeben. Dies unterscheidet sich von den Voreinstellungen von v2 Media Encoder Standard. Beispielsweise würde die Bitrate in v2 mit 128 (Kbit/s), in v3 jedoch mit 12.8000 (Bits/Sekunde) angegeben. 
* Entities AssetFiles, AccessPolicies und IngestManifests sind in v3 nicht vorhanden.
* ContentKeys ist jetzt keine Entität mehr, sondern eine Eigenschaft des Streaminglocators.
* Die Event Grid-Unterstützung ersetzt NotificationEndpoints.
* Die folgenden Entitäten wurden umbenannt:

   * JobOutput (v3) ersetzt Aufgabe (v2) und ist jetzt Teil eines Auftrags. Eingaben und Ausgaben befinden sich jetzt auf der Auftragsebene. Weitere Informationen finden Sie unter [Erstellen einer Auftragseingabe aus einer lokalen Datei](job-input-from-local-file-how-to.md). 

       Lauschen Sie auf EventGrid-Ereignisse, um den Verlauf des Auftragsstatus abzurufen. Weitere Informationen finden Sie unter [Behandeln von Event Grid-Ereignissen](reacting-to-media-services-events.md).
    * Der Streaminglocator ersetzt den Locator.
    * Das Liveereignis ersetzt den Kanal.<br/>Die Liveereignisabrechnung basiert auf Livekanal-Verbrauchseinheiten. Weitere Informationen finden Sie unter [Zustandswerte von Liveereignissen und Abrechnung](live-event-states-billing.md) und [Media Services – Preise](https://azure.microsoft.com/pricing/details/media-services/).
    * Die Liveausgabe ersetzt das Programm.
* Liveausgaben werden bei der Erstellung gestartet und beim Löschen beendet. In den v2-APIs haben Programme anders funktioniert, sie mussten nach der Erstellung gestartet werden.
* Um Informationen zu einem Auftrag abzurufen, müssen Sie den Transformationsnamen wissen, unter dem der Auftrag erstellt wurde. 
* In v2 werden [Eingabe](../previous/media-services-input-metadata-schema.md)- und [Ausgabe](../previous/media-services-output-metadata-schema.md)-Metadatendateien als Ergebnis eines Codierungsauftrags generiert. In v3 wurde das Metadatenformat von XML in JSON geändert. 
* In Media Services v2 kann der Initialisierungsvektor (IV) angegeben werden. In Media Services v3 kann FairPlay IV nicht angegeben werden. Obwohl es keine Auswirkungen auf Kunden hat, die Media Services sowohl für die Verpackung als auch für die Lizenzbereitstellung nutzen, kann es ein Problem darstellen, wenn ein DRM-System eines Drittanbieters zur Bereitstellung der FairPlay-Lizenzen verwendet wird (Hybridmodus). In diesem Fall ist es wichtig zu wissen, dass FairPlay IV von der cbcs-Schlüssel-ID abgeleitet wird und mit der folgenden Formel abgerufen werden kann:

    ```
    string cbcsIV =  Convert.ToBase64String(HexStringToByteArray(cbcsGuid.ToString().Replace("-", string.Empty)));
    ```

    durch

    ``` 
    public static byte[] HexStringToByteArray(string hex)
    {
        return Enumerable.Range(0, hex.Length)
            .Where(x => x % 2 == 0)
            .Select(x => Convert.ToByte(hex.Substring(x, 2), 16))
            .ToArray();
    }
    ```

    Weitere Informationen finden Sie unter [Azure Functions C#-Code für Media Services v3 im Hybridmodus für Live- und VOD-Vorgänge](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3).
 
> [!NOTE]
> Sehen Sie sich die Namenskonventionen an, die auf [Media Services v3-Ressourcen](media-services-apis-overview.md#naming-conventions) angewendet werden. Sehen Sie sich auch das [Benennen von Blobs](assets-concept.md#naming) an.

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Featurelücken in Bezug auf v2-APIs

Die v3-API weist in Bezug auf die v2-API die folgenden Featurelücken auf. Am Schließen der Lücken wird derzeit gearbeitet.

* Der Zugriff auf den [Premium-Encoder](../previous/media-services-encode-asset.md) und die älteren [Media Analytics-Prozessoren](../previous/legacy-components.md) (Azure Media Services Indexer 2 Preview, Face Redactor usw.) ist über v3 nicht möglich.<br/>Kunden, die eine Migration von der Media Indexer 1- oder 2-Vorschauversion durchführen möchten, können sofort die AudioAnalyzer-Voreinstellung in der v3-API verwenden.  Diese neue Voreinstellung enthält mehr Funktionen als die ältere Media Indexer 1- oder 2-Version. 
* Viele der [erweiterten Features von Media Encoder Standard in v2-APIs](../previous/media-services-advanced-encoding-with-mes.md) sind derzeit in v3 nicht verfügbar, z. B.:
  
    * Zusammenfügen von Medienobjekten
    * Überlagerungen
    * Zuschneiden
    * Einfügen einer stillen Audiospur bei einer Eingabe ohne Audio
    * Einfügen einer Videospur, wenn die Eingabe kein Video enthält
* Liveereignisse mit Transcodierung unterstützen derzeit keine Slate-Einfügungen beim Streamen und Werbemarkereinfügungen über API-Aufrufe. 
* Bewährte Methoden und Muster hinsichtlich der Verwendung der V2-REST-API für das .NET Core SDK finden Sie im `https://github.com/Azure-Samples/media-services-v2-dotnet-core-restsharp-sample.git`-Beispielcode.

## <a name="asset-specific-changes"></a>Für Medienobjekte spezifische Änderungen

* Für Medienobjekte (Ressourcen), die mit v3 erstellt wurden, unterstützt Media Services nur die serverseitige Speicherverschlüsselung, siehe [Azure Storage Service Encryption für ruhende Daten](../../storage/common/storage-service-encryption.md).
    * Sie können v3-APIs mit Ressourcen verwenden, die mit v2-APIs erstellt wurden, sofern für diese [Speicherverschlüsselung](../previous/media-services-rest-storage-encryption.md) (AES-256) von Media Services bereitgestellt wurde.
    * Mit der älteren AES-256-[Speicherverschlüsselung](../previous/media-services-rest-storage-encryption.md) können Sie mithilfe von v3-APIs keine neuen Medienobjekte erstellen.
* Die Eigenschaften des [Medienobjekt](assets-concept.md)s in v3 unterscheiden sich von denen in v2, siehe [Zuordnung der Eigenschaften des Medienobjekts in v3 zu v2](#map-v3-asset-properties-to-v2).
* Die Eigenschaft „IAsset.ParentAssets“ ist in v3 nicht vorhanden.

### <a name="map-v3-asset-properties-to-v2"></a>Zuordnung der Eigenschaften des Medienobjekts in v3 zu v2

Die folgende Tabelle zeigt, wie die Eigenschaften des [Medienobjekts](/rest/api/media/assets/createorupdate#asset) in v3 den Eigenschaften in v2 zugeordnet werden.

|v3-Eigenschaften|v2-Eigenschaften|
|---|---|
|`id` – (eindeutig) der vollständige Azure Resource Manager-Pfad, siehe Beispiele unter [Medienobjekt](/rest/api/media/assets/createorupdate)||
|`name` – (eindeutig) siehe [Benennungskonventionen](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id` – (eindeutiger) Wert beginnt mit dem `nb:cid:UUID:`-Präfix.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (Optionen für die Erstellung)|
|`type`||

### <a name="storage-side-encryption"></a>Speicherseitige Verschlüsselung

Zum Schutz Ihrer im Ruhezustand befindlichen Ressourcen sollten die Ressourcen durch die speicherseitige Verschlüsselung verschlüsselt werden. Die folgende Tabelle zeigt, wie die speicherseitige Verschlüsselung in Media Services funktioniert:

|Verschlüsselungsoption|BESCHREIBUNG|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services-Speicherverschlüsselung|AES-256-Verschlüsselung, Schlüssel von Media Services verwaltet.|Unterstützt<sup>(1)</sup>|Nicht unterstützt<sup>(2)</sup>|
|[Speicherdienstverschlüsselung für ruhende Daten](../../storage/common/storage-service-encryption.md)|Durch Azure Storage angebotene serverseitige Verschlüsselung – Schlüssel wird von Azure oder vom Kunden verwaltet.|Unterstützt|Unterstützt|
|[Clientseitige Storage-Verschlüsselung](../../storage/common/storage-client-side-encryption.md)|Durch Azure Storage angebotene clientseitige Verschlüsselung – Schlüssel wird vom Kunden in Key Vault verwaltet.|Nicht unterstützt|Nicht unterstützt|

<sup>1</sup> Media Services unterstützt zwar die Behandlung von Inhalten in Klartext/ohne jede Form der Verschlüsselung, doch wird davon abgeraten.

<sup>2</sup> In Media Services v3 wird Speicherverschlüsselung (AES-256-Verschlüsselung) nur für die Abwärtskompatibilität unterstützt, wenn Ihre Ressourcen mit Media Services v2 erstellt wurden. Dies bedeutet, dass v3 mit vorhandenen speicherverschlüsselten Ressourcen funktioniert, jedoch nicht die Erstellung neuer zulässt.

## <a name="code-differences"></a>Codeunterschiede

Die folgende Tabelle zeigt die Codeunterschiede zwischen v2 und v3 für gängige Szenarien.

|Szenario|v2-API|v3-API|
|---|---|---|
|Erstellen eines Medienobjekts und Hochladen einer Datei |[Beispiel für v2-.NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[Beispiel für v3-.NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Übermitteln eines Auftrags|[Beispiel für v2-.NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[Beispiel für v3-.NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Zeigt, wie zuerst eine Transformation erstellt und dann ein Auftrag übermittelt wird.|
|Veröffentlichen eines Medienobjekts mit AES-Verschlüsselung |1. Erstellen von ContentKeyAuthorizationPolicyOption<br/>2. Erstellen von ContentKeyAuthorizationPolicy<br/>3. Erstellen von AssetDeliveryPolicy<br/>4. Erstellen eines Medienobjekts und Hochladen von Inhalten ODER Übermitteln eines Auftrags und Verwenden des Ausgabemedienobjekts<br/>5. Zuordnen von AssetDeliveryPolicy zu Medienobjekt<br/>6. Erstellen von ContentKey<br/>7. Zuordnen von ContentKey zu einem Medienobjekt<br/>8. Erstellen von AccessPolicy<br/>9. Erstellen von Locator<br/><br/>[Beispiel für v2-.NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Erstellen einer Richtlinie für Inhaltsschlüssel<br/>2. Erstellen eines Medienobjekts<br/>3. Hochladen von Inhalten oder Verwenden von Medienobjekt als JobOutput<br/>4. Erstellen eines Streaminglocators<br/><br/>[Beispiel für v3-.NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Abrufen von Auftragsdetails und Verwalten von Aufträgen |[Verwalten von Aufträgen mit v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Verwalten von Aufträgen mit v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> Erstellen Sie ein Lesezeichen für diesen Artikel, und überprüfen Sie ihn regelmäßig auf Aktualisierungen.

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="next-steps"></a>Nächste Schritte

[Hinweise zur Migration von Media Services v2 zu v3](migrate-from-v2-to-v3.md)