---
title: Medienobjekte in Media Services – Azure| Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, was Medienobjekte sind und wie sie von Azure Media Services verwendet werden.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/11/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2afcf2066238414cd08e32901ffccf2a44718b6d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65551763"
---
# <a name="assets"></a>Objekte

In Azure Media Services enthält ein [Medienobjekt](https://docs.microsoft.com/rest/api/media/assets) Informationen zu digitalen in Azure Storage gespeicherten Dateien (z.B. Video- und Audiodateien, Bilder, Sammlungen von Miniaturansichten, Textspuren und Untertiteldateien). 

Ein Medienobjekt ist einem Blobcontainer im [Azure Storage-Konto](storage-account-concept.md) zugeordnet, und die im Medienobjekt enthaltenen Dateien werden als Blockblobs in diesem Container gespeichert. Azure Media Services unterstützt Blobebenen, wenn das Konto einen Speicher vom Typ „Allgemein v2“ (GPv2) verwendet. In GPv2-Speichern können Sie Dateien auf die [kalte Speicherebene oder die Archivspeicherebene](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) verschieben. Der **Archivspeicher** eignet sich für die Archivierung von Quelldateien, wenn diese nicht mehr benötigt werden (z.B. nach der Codierung).

Die **Archivspeicherebene** wird nur empfohlen, wenn Sie über sehr große Quelldateien verfügen, die bereits codiert wurden, und wenn der Codierungsauftrag in einem Ausgabeblobcontainer platziert wurde. Die Blobs in dem Ausgabecontainer, den Sie einem Medienobjekt zuordnen und zum Streamen oder Analysieren Ihrer Inhalte verwenden möchten, müssen auf den Speicherebenen **heiß** oder **kalt** vorhanden sein.

## <a name="upload-digital-files-into-assets"></a>Hochladen von digitalen Dateien in Medienobjekte

Nachdem die digitalen Dateien in den Speicher hochgeladen und einem Medienobjekt zugeordnet wurden, können sie in den Media Services-Workflows zum Codieren, Streamen und Analysieren von Inhalten verwendet werden. Einer der gängigsten Media Services-Workflows besteht aus dem Hochladen, Codieren und Streamen einer Datei. Dieser Abschnitt erläutert die allgemeinen Schritte.

> [!TIP]
> Bevor Sie mit der Entwicklung beginnen, lesen Sie [Entwickeln mit Media Services v3-APIs](media-services-apis-overview.md) (Informationen zum Zugreifen auf APIs, Namenskonventionen usw.).

1. Verwenden Sie die Media Services v3-API, um ein neues Medienobjekt für die Eingabe zu erstellen. Dieser Vorgang erstellt einen Container in dem Speicherkonto, das Ihrem Media Services-Konto zugeordnet ist. Die API gibt den Containernamen zurück (z.B. `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).
   
    Wenn Sie bereits über einen Blobcontainer verfügen, den Sie einem Medienobjekt zuordnen möchten, können Sie den Containernamen beim Erstellen des Medienobjekts angeben. Media Services unterstützt derzeit nur Blobs im Containerstamm, nicht mit Pfaden im Dateinamen. Daher funktioniert ein Container mit dem Dateinamen „input.mp4“. Ein Container mit dem Dateinamen „videos/inputs/input.mp4“ dagegen funktioniert nicht.

    Sie können die Azure CLI verwenden, um Dateien direkt in ein Speicherkonto und einen Container hochzuladen, für das bzw. den Sie in Ihrem Abonnement Berechtigungen besitzen. <br/>Der Containername muss eindeutig sein und Benennungsrichtlinien für Speicher befolgen. Der Name muss nicht der Formatierung für Containernamen für Media Services-Medienobjekte folgen (Asset-GUID). 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Rufen Sie eine SAS-URL mit Lese-/Schreibberechtigungen ab, die verwendet wird, um digitale Dateien in den Medienobjektcontainer hochzuladen. Sie können die Media Services-API verwenden, um [die Medienobjektcontainer-URLs aufzulisten](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Verwenden Sie die Azure Storage-APIs oder SDKs (z.B. [Storage-REST-API](../../storage/common/storage-rest-api-auth.md), [Java SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md) oder [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)), um Dateien in den Medienobjektcontainer hochzuladen. 
4. Verwenden Sie Media Services v3-APIs, um eine Transformation und einen Auftrag zur Verarbeitung Ihres Eingabemedienobjekts zu erstellen. Weitere Informationen finden Sie unter [Transformationen und Aufträge](transform-concept.md).
5. Streamen Sie die Inhalte des Ausgabemedienobjekts.

Unter [Erstellen einer Auftragseingabe aus einer lokalen Datei](job-input-from-local-file-how-to.md) finden Sie ein vollständiges .NET-Beispiel für folgende Aufgaben: Erstellen des Medienobjekts, Abrufen einer beschreibbaren SAS-URL zum Container des Medienobjekts im Container und Hochladen der Datei in den Container im Speicher über die SAS-URL.

### <a name="create-a-new-asset"></a>Erstellen des neuen Medienobjekts

> [!NOTE]
> Eigenschaften von Medienobjekten im Datetime-Typ liegen immer im UTC-Format vor.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Ein REST-Beispiel finden Sie unter [Erstellen eines Medienobjekts mit REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples).

Dieses Beispiel zeigt, wie Sie den **Anforderungstext** erstellen, in dem Sie hilfreiche Informationen wie Beschreibung, Containername, Speicherkonto und weitere Informationen angeben können.

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

Ein vollständiges Beispiel finden Sie unter [Erstellen einer Auftragseingabe aus einer lokalen Datei](job-input-from-local-file-how-to.md). In Media Services v3 kann die Eingabe eines Auftrags auch über HTTPS-URLs erstellt werden (siehe [Erstellen einer Auftragseingabe aus einer HTTPS-URL](job-input-from-http-how-to.md)).

## <a name="filtering-ordering-paging"></a>Filterung, Sortierung, Paging

Informationen finden Sie unter [Filterung, Sortierung, Paginierung von Media Services-Entitäten](entities-overview.md).

## <a name="storage-side-encryption"></a>Speicherseitige Verschlüsselung

Zum Schutz Ihrer im Ruhezustand befindlichen Ressourcen sollten die Ressourcen durch die speicherseitige Verschlüsselung verschlüsselt werden. Die folgende Tabelle zeigt, wie die speicherseitige Verschlüsselung in Media Services funktioniert:

|Verschlüsselungsoption|BESCHREIBUNG|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services-Speicherverschlüsselung|AES-256-Verschlüsselung, Schlüssel von Media Services verwaltet|Unterstützt<sup>(1)</sup>|Nicht unterstützt<sup>(2)</sup>|
|[Speicherdienstverschlüsselung für ruhende Daten](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Durch Azure Storage angebotene serverseitige Verschlüsselung – Schlüssel wird von Azure oder vom Kunden verwaltet|Unterstützt|Unterstützt|
|[Clientseitige Storage-Verschlüsselung](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Durch Azure Storage angebotene clientseitige Verschlüsselung – Schlüssel wird vom Kunden in Key Vault verwaltet|Nicht unterstützt|Nicht unterstützt|

<sup>1</sup> Media Services unterstützt zwar die Behandlung von Inhalten in Klartext/ohne jede Form der Verschlüsselung, doch wird davon abgeraten.

<sup>2</sup> In Media Services v3 wird Speicherverschlüsselung (AES-256-Verschlüsselung) nur für die Abwärtskompatibilität unterstützt, wenn Ihre Ressourcen mit Media Services v2 erstellt wurden. Dies bedeutet, dass v3 mit vorhandenen speicherverschlüsselten Ressourcen funktioniert, jedoch nicht die Erstellung neuer zulässt.

## <a name="next-steps"></a>Nächste Schritte

* [Streamen einer Datei](stream-files-dotnet-quickstart.md)
* [Verwenden eines Cloud-DVR](live-event-cloud-dvr.md)
* [Unterschiede zwischen Media Services v2 und v3](migrate-from-v2-to-v3.md)
