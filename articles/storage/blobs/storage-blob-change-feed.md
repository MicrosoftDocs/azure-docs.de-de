---
title: Änderungsfeed in Azure Blob Storage | Microsoft-Dokumentation
description: Erfahren Sie mehr über Änderungsfeedprotokolle in Azure Blob Storage und deren Verwendung.
author: normesta
ms.author: normesta
ms.date: 09/08/2020
ms.topic: how-to
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 7174f7dd53387de9a569a5ddcadc08c32692c749
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997102"
---
# <a name="change-feed-support-in-azure-blob-storage"></a>Änderungsfeed in Azure Blob Storage

Zweck des Änderungsfeeds ist es, Transaktionsprotokolle für alle Änderungen bereitzustellen, die in den Blobs und Blobmetadaten in Ihrem Speicherkonto auftreten. Der Änderungsfeed bietet **sortierte**, **garantierte**, **permanente**, **unveränderliche** und **schreibgeschützte** Protokolle dieser Änderungen. Clientanwendungen können diese Protokolle jederzeit lesen, entweder im Streaming- oder im Batchmodus. Der Änderungsfeed ermöglicht es Ihnen, effiziente und skalierbare Lösungen zu erstellen, mit denen Änderungsereignisse, die in Ihrem Blob Storage-Konto auftreten, kostengünstig verarbeitet werden.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

Der Änderungsfeed wird in Form von [Blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) in einem speziellen Container Ihres Speicherkontos zu standardmäßigen [Blobpreisen](https://azure.microsoft.com/pricing/details/storage/blobs/) gespeichert. Sie können den Aufbewahrungszeitraum dieser Dateien Ihren Anforderungen entsprechend steuern (Informationen hierzu finden Sie in den [Bedingungen](#conditions) für das aktuelle Release). Änderungsereignisse werden in der [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html)-Formatspezifikation als Datensätze an den Änderungsfeed angehängt. Dies ist ein kompaktes, schnelles Binärformat, das umfangreiche Datenstrukturen mit Inlineschemas bereitstellt. Dieses Format wird häufig im Hadoop-Ökosystem, von Stream Analytics und von Azure Data Factory verwendet.

Sie können diese Protokolle asynchron, inkrementell oder vollständig verarbeiten. Eine beliebige Anzahl von Clientanwendungen kann den Änderungsfeed unabhängig, gleichzeitig und in der jeweils eigenen Geschwindigkeit lesen. Analyseanwendungen wie [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) oder [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) können Protokolle direkt als Avro-Dateien nutzen, sodass sie kostengünstig, mit hoher Bandbreite und ohne benutzerdefinierte Anwendung verarbeitet werden können.

Die Unterstützung eines Änderungsfeeds eignet sich gut für Szenarien, in denen Daten basierend auf geänderten Objekten verarbeitet werden. Anwendungen können beispielsweise Folgendes ausführen:

  - Aktualisieren eines sekundären Index, Synchronisieren mit einem Cache oder einer Suchmaschine oder andere Content Management-Szenarien.
  
  - Extrahieren von Erkenntnissen und Metriken aus geschäftlichen Analysen, basierend auf Änderungen an Ihren Objekten – entweder per Streaming oder im Batchmodus.
  
  - Speichern, Überwachen und Analysieren von Änderungen an Ihren Objekten über einen beliebigen Zeitraum hinweg, um Sicherheit, Compliance oder Business Intelligence für die Verwaltung von Unternehmensdaten zu erzielen.

  - Erstellen von Lösungen zum Sichern, Spiegeln oder Replizieren des Objektzustands in Ihrem Konto für Notfallverwaltung oder Compliance.

  - Erstellen von verbundenen Anwendungspipelines, die auf Änderungsereignisse reagieren oder Ausführungen basierend auf erstellten oder geänderten Objekten planen.
  
Der Änderungsfeed ist ein erforderliches Feature für [Objektreplikation und ](object-replication-overview.md) [Point-in-Time-Wiederherstellung für Blockblobs](point-in-time-restore-overview.md).

> [!NOTE]
> Der Änderungsfeed bietet ein permanentes, sortiertes Protokollmodell der Änderungen an einem Blob. Änderungen werden innerhalb weniger Minuten in das Änderungsfeedprotokoll geschrieben und verfügbar gemacht. Wenn Ihre Anwendung wesentlich schneller auf Ereignisse reagieren muss, sollten Sie stattdessen [Blob Storage-Ereignisse](storage-blob-event-overview.md) in Betracht ziehen. [Blob Storage-Ereignisse](storage-blob-event-overview.md) bieten einmalige Echtzeitereignisse, mit denen Ihre Azure Functions-Lösung oder Ihre Anwendungen schnell auf Blobänderungen reagieren können. 

## <a name="enable-and-disable-the-change-feed"></a>Aktivieren und Deaktivieren des Änderungsfeeds

Sie müssen den Änderungsfeed in Ihrem Speicherkonto aktivieren, damit Änderungen erfasst und aufgezeichnet werden. Deaktivieren Sie den Änderungsfeed, um die Erfassung von Änderungen zu beenden. Sie können Änderungen mithilfe von Azure Resource Manager-Vorlagen im Portal oder in PowerShell aktivieren und deaktivieren.

Folgende Aspekte müssen Sie berücksichtigen, wenn Sie den Änderungsfeed aktivieren.

- In jedem gespeicherten Speicherkonto gibt es nur einen einzelnen Änderungsfeed für den Blobdienst, und dieser wird im Container **$blobchangefeed** gespeichert.

- Erstellungen, Aktualisierungen und Löschungen werden nur auf der Blobdienstebene erfasst.

- Der Änderungsfeed zeichnet *alle* Änderungen für alle verfügbaren Ereignisse auf, die im Konto auftreten. Clientanwendungen können Ereignistypen nach Bedarf herausfiltern. (Informationen hierzu finden Sie in den [Bedingungen](#conditions) für das aktuelle Release.)

- Nur GPv2- und Blob Storage-Konten können den Änderungsfeed aktivieren. Premium-BlockBlobStorage-Konten und Konten für hierarchische Namespaces werden aktuell nicht unterstützt. GPv1-Speicherkonten werden zwar nicht unterstützt, können aber ohne Downtime auf GPv2 aktualisiert werden. Weitere Informationen finden Sie unter [Durchführen eines Upgrades auf ein Speicherkonto vom Typ „Allgemein v2“](../common/storage-account-upgrade.md).

### <a name="portal"></a>[Portal](#tab/azure-portal)

So aktivieren Sie den Änderungsfeed für Ihr Speicherkonto über das Azure-Portal:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihr Speicherkonto aus.

2. Navigieren Sie unter **Blob-Dienst** zur Option **Datenschutz**.

3. Klicken Sie unter **Blobänderungsfeed** auf **Aktiviert**.

4. Wählen Sie die Schaltfläche **Speichern** aus, um Ihre **Datenschutzeinstellungen** zu bestätigen.

    ![Screenshot: Einstellungen für den Schutz von Daten](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

So aktivieren Sie den Änderungsfeed mithilfe von PowerShell:

1. Installieren Sie die neueste Version von PowerShellGet.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Schließen Sie die PowerShell-Konsole, und öffnen Sie sie dann erneut.

3. Installieren Sie mindestens die Version 2.5.0 des Moduls **Az.Storage**.

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 2.5.0 –AllowClobber –Force
   ```

4. Melden Sie sich mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Authentifizierungsanweisungen auf dem Bildschirm.

   ```powershell
   Connect-AzAccount
   ```

5. Aktivieren Sie den Änderungsfeed für Ihr Speicherkonto.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[Vorlage](#tab/template)
So verwenden Sie eine Azure Resource Manager-Vorlage, um den Änderungsfeed für Ihr vorhandenes Speicherkonto über das Azure-Portal zu aktivieren:

1. Klicken Sie im Azure-Portal auf **Ressource erstellen**.

2. Geben Sie in **Marketplace durchsuchen** den Begriff **Vorlagenbereitstellung** ein, und drücken Sie dann die **EINGABETASTE**.

3. Wählen Sie **[Benutzerdefinierte Vorlage bereitstellen](https://portal.azure.com/#create/Microsoft.Template)** und anschließend **Eigene Vorlage im Editor erstellen** aus.

4. Fügen Sie folgenden JSON-Code im Vorlagen-Editor ein. Ersetzen Sie den Platzhalter `<accountName>` durch den Namen Ihres Speicherkontos.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts/blobServices",
           "apiVersion": "2019-04-01",
           "name": "<accountName>/default",
           "properties": {
               "changeFeed": {
                   "enabled": true
               }
           } 
        }]
   }
   ```
    
5. Klicken Sie auf die Schaltfläche **Speichern**, geben Sie die Ressourcengruppe des Kontos an, und klicken Sie dann auf die Schaltfläche **Kaufen**, um die Vorlage bereitzustellen und den Änderungsfeed zu aktivieren.

---

## <a name="consume-the-change-feed"></a>Nutzen des Änderungsfeeds

Der Änderungsfeed erzeugt verschiedene Metadaten- und Protokolldateien. Diese Dateien befinden sich im Container **$blobchangefeed** des Speicherkontos. 

> [!NOTE]
> Im aktuellen Release ist der Container „$blobchangefeed“ nur im Azure-Portal, aber nicht im Azure Storage-Explorer sichtbar. Der Container „$blobchangefeed“ kann zurzeit nicht angezeigt werden, wenn Sie die ListContainers-API aufrufen. Sie können aber die ListBlobs-API direkt im Container aufrufen, um die Blobs anzuzeigen.

Ihre Clientanwendungen können den Änderungsfeed nutzen, indem sie die Prozessorbibliothek für den Änderungsfeed im Blob verwenden, die mit dem Change Feed Processor SDK bereitgestellt wird. 

Informationen dazu finden Sie unter [Verarbeiten von Änderungsfeedprotokollen in Azure Blob Storage](storage-blob-change-feed-how-to.md).

## <a name="understand-change-feed-organization"></a>Grundlegendes zur Organisation von Änderungsfeeds

<a id="segment-index"></a>

### <a name="segments"></a>Segmente

Beim Änderungsfeed handelt es sich um ein Protokoll von Änderungen, das in **stündliche** *Segmente* unterteilt ist, aber alle paar Minuten erweitert und aktualisiert wird. Diese Segmente werden nur erstellt, wenn in dieser Stunde Änderungsereignisse für den Blob auftreten. So kann Ihre Clientanwendung Änderungen nutzen, die innerhalb bestimmter Zeiträume auftreten, ohne das gesamte Protokoll durchsuchen zu müssen. Weitere Informationen finden Sie in den [Spezifikationen](#specifications).

Ein verfügbares stündliches Segment des Änderungsfeeds wird in einer Manifestdatei beschrieben, die die Pfade zu den Änderungsfeeddateien für dieses Segment angibt. Die Auflistung des virtuellen Verzeichnisses `$blobchangefeed/idx/segments/` zeigt diese Segmente nach Uhrzeit geordnet an. Der Pfad des Segments beschreibt den Start des stündlichen Zeitbereichs, den das Segment repräsentiert. Sie können diese Liste verwenden, um die Protokollsegmente herauszufiltern, die für Sie von Interesse sind.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> Die Datei `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` wird automatisch erstellt, wenn Sie den Änderungsfeed aktivieren. Sie können diese Datei unbesorgt ignorieren. Dabei handelt es sich um eine immer leere Initialisierungsdatei. 

Die Segmentmanifestdatei (`meta.json`) zeigt den Pfad der Änderungsfeeddateien für dieses Segment in der Eigenschaft `chunkFilePaths` an. Beispiel für eine Segmentmanifestdatei:

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> Der Container `$blobchangefeed` wird erst angezeigt, wenn Sie das Änderungsfeedfeature für Ihr Konto aktiviert haben. Nach dem Aktivieren des Änderungsfeeds dauert es ein paar Minuten, bis die Blobs im Container aufgelistet werden können. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Ändern von Ereignisdatensätzen

Die Änderungsfeeddateien enthalten eine Reihe von Datensätzen mit Änderungsereignissen. Jeder Änderungsereignis-Datensatz entspricht einer Änderung an einem einzelnen Blob. Die Datensätze werden mithilfe der [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html)-Formatspezifikation serialisiert und in die Datei geschrieben. Die Datensätze können mithilfe der Avro-Dateiformatspezifikation gelesen werden. Zum Verarbeiten von Dateien in diesem Format stehen verschiedene Bibliotheken zur Verfügung.

Änderungsfeeddateien werden im virtuellen Verzeichnis `$blobchangefeed/log/` als [Anfügeblobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) gespeichert. Die erste Änderungsfeeddatei in jedem Pfad weist einen Zähler `00000` im Dateinamen auf (z. B. `00000.avro`). Dieser Zähler wird bei jeder Protokolldatei, die dem Pfad danach hinzugefügt wird, um 1 erhöht (z. B. `00001.avro`).

Die folgenden Ereignistypen werden in den Änderungsfeed-Datensätzen erfasst:
- BlobCreated
- BlobDeleted
- BlobPropertiesUpdated
- BlobSnapshotCreated

Im Folgenden sehen Sie ein Beispiel für einen in JSON konvertierten Änderungsfeed-Datensatz aus einer Änderungsfeeddatei:

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}
```

Eine ausführliche Beschreibung der einzelnen Eigenschaften finden Sie unter [Azure Event Grid-Ereignisschema für Blob Storage](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties). Die Ereignisse BlobPropertiesUpdated und BlobSnapshotCreated sind zurzeit exklusiv für den Änderungsfeed und werden noch nicht für Blob Storage-Ereignisse unterstützt.

> [!NOTE]
> Die Änderungsfeeddateien für ein Segment werden nicht sofort nach dem Erstellen eines Segments angezeigt. Die Verzögerung bewegt sich im normalen Intervall der Veröffentlichungslatenz des Änderungsfeeds, die wenige Minuten nach der Änderung beträgt.

<a id="specifications"></a>

## <a name="specifications"></a>Spezifikationen

- Änderungsereignis-Datensätze werden an den Änderungsfeed nur angefügt. Nachdem diese Datensätze angefügt wurden, sind sie unveränderlich und ihre Position bleibt stabil. Clientanwendungen können einen eigenen Prüfpunkt an der Leseposition des Änderungsfeeds verwalten.

- Änderungsereignis-Datensätze werden innerhalb weniger Minuten nach der Änderung angefügt. Clientanwendungen können Datensätze per Streamingzugriff direkt nach deren Anfügung oder zu jedem anderen Zeitpunkt per Massenzugriff nutzen.

- Änderungsereignis-Datensätze werden **pro Blob** in der Reihenfolge des Auftretens der Änderung sortiert. Die Reihenfolge von Änderungen über mehrere Blobs hinweg ist in Azure Blob Storage nicht definiert. Alle Änderungen an einem Segment sind vor allen Änderungen an nachfolgenden Segmenten aufgetreten.

- Änderungsereignis-Datensätze werden mithilfe der Formatspezifikation [Apache Avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html) in die Protokolldatei serialisiert.

- Änderungsereignis-Datensätze, bei denen der `eventType` den Wert `Control` aufweist, sind interne Systemdatensätze und spiegeln keine Änderung an Objekten in Ihrem Konto wider. Sie können diese Datensätze unbesorgt ignorieren.

- Werte im Eigenschaftenbehälter `storageDiagnonstics` dienen nur zur internen Verwendung und sind nicht für die Verwendung durch Ihre Anwendung konzipiert. Ihre Anwendungen sollten keine vertragliche Abhängigkeit von diesen Daten aufweisen. Sie können diese Eigenschaften unbesorgt ignorieren.

- Die vom Segment dargestellte Uhrzeit ist ein **ungefährer** Wert mit einem Spielraum von 15 Minuten vorher und nachher. Um also sicherzustellen, dass alle Datensätze einer angegebenen Uhrzeit genutzt werden, schließen Sie auch das vorherige und das nachfolgende Stundensegment ein.

- Jedes Segment kann eine andere Anzahl von `chunkFilePaths` aufweisen. Das liegt an der internen Partitionierung des Protokollstreams zur Verwaltung des Durchsatzes bei der Veröffentlichung. Die Protokolldateien in jedem `chunkFilePath` enthalten garantiert sich gegenseitig ausschließende Blobs und können parallel genutzt und verarbeitet werden, ohne dass dabei die Reihenfolge der Änderungen pro Blob während der Iteration verändert wird.

- Die Segmente beginnen im Status `Publishing`. Nachdem das Anfügen der Datensätze an das Segment abgeschlossen ist, lautet der Status `Finalized`. Protokolldateien in einem Segment, das nach dem Datum der `LastConsumable`-Eigenschaft in der Datei `$blobchangefeed/meta/Segments.json` datiert ist, sollten von Ihrer Anwendung nicht genutzt werden. Im Folgenden sehen Sie ein Beispiel der `LastConsumable`-Eigenschaft in einer `$blobchangefeed/meta/Segments.json`-Datei:

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="conditions"></a>

## <a name="conditions-and-known-issues"></a>Bedingungen und bekannte Probleme

Dieser Abschnitt enthält Informationen zu bekannten Problemen und Bedingungen im aktuellen Release des Änderungsfeeds. 

- Änderungsereignis-Datensätze für eine einzelne Änderung werden in Ihrem Änderungsfeed möglicherweise mehrmals angezeigt.
- Sie können die Lebensdauer der Änderungsfeed-Protokolldateien noch nicht durch Festlegen einer zeitbasierten Aufbewahrungsrichtlinie verwalten, und Sie können die Blobs nicht löschen.
- Die Eigenschaft `url` der Protokolldatei ist aktuell immer leer.
- Die `LastConsumable`-Eigenschaft der segments.json-Datei listet das allererste Segment, das vom Änderungsfeed abgeschlossen wird, nicht auf. Dieses Problem tritt nur nach Abschluss des ersten Segments auf. Alle nachfolgenden Segmente nach der ersten Stunde werden ordnungsgemäß in der `LastConsumable`-Eigenschaft aufgezeichnet.
- Der Container **$blobchangefeed** kann zurzeit nicht angezeigt werden, wenn Sie die ListContainers-API aufrufen, und er wird auch nicht im Azure-Portal oder im Speicher-Explorer angezeigt. Sie können den Inhalt anzeigen, indem Sie die ListBlobs-API für den $blobchangefeed-Container direkt aufrufen.
- Bei Speicherkonten, für die zuvor ein [Kontofailover](../common/storage-disaster-recovery-guidance.md) initiiert wurde, kann es vorkommen, dass die Protokolldatei nicht angezeigt wird. Auch bei zukünftigen Kontofailovern kann es zu Problemen mit der Protokolldatei kommen.

## <a name="faq"></a>Häufig gestellte Fragen

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Worin besteht der Unterschied zwischen Änderungsfeed und Storage Analytics-Protokollierung?
In Analytics-Protokollen werden alle Lese-, Schreib-, Auflistungs- und Löschvorgänge mit erfolgreichen und nicht erfolgreichen Anforderungen für alle Vorgänge erfasst. Analytics-Protokolle werden bestmöglich erstellt und es wird keine Reihenfolge garantiert.

Der Änderungsfeed ist eine Lösung, die Transaktionsprotokolle erfolgreicher Mutationen oder Änderungen an Ihrem Konto bereitstellt. Hierzu zählt beispielsweise die Erstellung, Änderung und Löschung von Blobs. Beim Änderungsfeed werden alle Ereignisse garantiert in der Reihenfolge erfolgreicher Änderungen pro Blob aufgezeichnet und angezeigt. Somit müssen keine überflüssigen Daten aus einer großen Menge von Lesevorgängen oder nicht erfolgreichen Anforderungen herausgefiltert werden. Der Änderungsfeed ist von Grund auf für die Entwicklung von Anwendungen konzipiert und optimiert, die bestimmte Garantien erfordern.

### <a name="should-i-use-change-feed-or-storage-events"></a>Sollten Änderungsfeeds oder Blob Storage-Ereignisse verwendet werden?
Sie können beide Features verwenden, da Änderungsfeeds und [Blob Storage-Ereignisse](storage-blob-event-overview.md) die gleichen Informationen mit der gleichen garantierten Zuverlässigkeit liefern. Der Hauptunterschied besteht bei der Wartezeit, Sortierung und Speicherung der Ereignisdatensätze. Der Änderungsfeed veröffentlicht Datensätze wenige Minuten nach der Änderung im Protokoll und garantiert die Reihenfolge von Änderungsvorgängen pro Blob. Blob Storage-Ereignisse werden in Echtzeit per Pushübertragung übermittelt und sind möglicherweise nicht geordnet. Änderungsfeedereignisse werden in Ihrem Speicherkonto dauerhaft als schreibgeschützte, stabile Protokolle gespeichert. Storage-Ereignisse sind dagegen kurzlebig und für die Nutzung durch den Ereignishandler vorgesehen (es sei denn, sie werden explizit gespeichert). Mit dem Änderungsfeed können die Protokolle von einer beliebigen Anzahl Ihrer Anwendungen nach Bedarf über Blob-APIs oder -SDKs genutzt werden. 

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich ein Beispiel dafür an, wie der Änderungsfeed von einer .NET-Clientanwendung gelesen werden kann. Informationen dazu finden Sie unter [Verarbeiten von Änderungsfeedprotokollen in Azure Blob Storage](storage-blob-change-feed-how-to.md).
- Erfahren Sie mehr über die Reaktion auf Ereignisse in Echtzeit. Informationen dazu finden Sie unter [Reaktion auf Blob Storage-Ereignisse](storage-blob-event-overview.md).
- Erfahren Sie mehr über detaillierte Protokollierungsinformationen für erfolgreiche und fehlgeschlagene Vorgänge für alle Anforderungen. Siehe [Azure Storage Analytics-Protokollierung](../common/storage-analytics-logging.md)