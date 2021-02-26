---
title: Geänderte und gelöschte Blobs
titleSuffix: Azure Cognitive Search
description: Nach einem ersten Suchindexbuild, der aus Azure Blob Storage importiert wird, kann die nachfolgende Indizierung nur die Blobs übernehmen, die geändert oder gelöscht werden. In diesem Artikel werden die Details erläutert.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: b23dabb4388331de9e37ee9db1d4b9d727ccde68
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430559"
---
# <a name="how-to-set-up-change-and-deletion-detection-for-blobs-in-azure-cognitive-search-indexing"></a>Einrichten der Erkennung von Änderungen und Löschungen für Blobs in der Azure Cognitive Search-Indizierung

Nachdem ein erster Suchindex erstellt wurde, sollen nachfolgende Indexeraufräge vielleicht nur neue und geänderte Dokumente aufnehmen. Wenn Sie einen Zeitplan für die Triggerindizierung verwenden, wird die Änderungserkennung bei Suchinhalten aus Azure Blob Storage automatisch durchgeführt. Standardmäßig werden wie vom `LastModified`-Zeitstempel des Blobs angegeben nur die geänderten Blobs vom Dienst neu indiziert. Im Gegensatz zu anderen von Suchindexern unterstützten Datenquellen verfügen Blobs immer über einen Zeitstempel, wodurch die Notwendigkeit entfällt, eine Richtlinie für die Änderungserkennung manuell einzurichten.

Die Änderungserkennung ist eine Standardeinstellung, die Erkennung von Löschungen hingegen nicht. Wenn Sie gelöschte Dokumente erkennen möchten, stellen Sie sicher, dass Sie einen Ansatz für „vorläufiges Löschen“ verwenden. Wenn Sie die Blobs direkt löschen, werden die entsprechenden Dokumente nicht aus dem Suchindex entfernt.

Es gibt zwei Möglichkeiten, den Ansatz des vorläufigen Löschens zu implementieren:

+ Natives vorläufiges Löschen von Blobs (Vorschau), nachfolgend beschrieben
+ [Vorläufiges Löschen mithilfe benutzerdefinierter Metadaten](#soft-delete-using-custom-metadata)

## <a name="native-blob-soft-delete-preview"></a>Natives vorläufiges Löschen von Blobs (Vorschau)

Bei dieser Methode zum Erkennen von Löschungen ermittelt Azure Cognitive Search anhand des [nativen Features zum vorläufigen Löschen von Blobs](../storage/blobs/soft-delete-blob-overview.md) in Azure Blob Storage, ob Blobs in einen vorläufig gelöschten Zustand versetzt wurden. Wenn Blobs in diesem Zustand erkannt werden, verwendet ein Suchindexer diese Informationen, um das entsprechende Dokument aus dem Index zu entfernen.

> [!IMPORTANT]
> Die Unterstützung für das native vorläufige Löschen von Blobs ist als Vorschauversion verfügbar. Die Vorschaufunktion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Dieses Feature wird durch die [REST-API-Version 2020-06-30-Preview](./search-api-preview.md) bereitgestellt. Derzeit werden weder das Portal noch das .NET SDK unterstützt.

### <a name="prerequisites"></a>Voraussetzungen

+ [Aktivieren von „Vorläufiges Löschen“ für Blobs](../storage/blobs/soft-delete-blob-enable.md).
+ Blobs müssen sich in einem Azure Blob Storage-Container befinden. Die Cognitive Search-Richtlinie zum nativen vorläufigen Löschen von Blobs wird für Blobs aus Azure Data Lake Storage Gen2 nicht unterstützt.
+ Dokumentschlüssel für die Dokumente in Ihrem Index müssen die entweder einer Blobeigenschaft oder Blobmetadaten zugewiesen werden.
+ Sie müssen die Vorschau-REST-API (`api-version=2020-06-30-Preview`) verwenden, um die Unterstützung für vorläufiges Löschen zu konfigurieren.

### <a name="how-to-configure-deletion-detection-using-native-soft-delete"></a>Konfigurieren der Löscherkennung mithilfe des nativen, vorläufigen Löschens

1. In Blobspeicher legen Sie beim Aktivieren des vorläufiges Löschens für die Aufbewahrungsrichtlinie einen Wert fest, der wesentlich höher ist als der Intervallzeitplan Ihres Indexers. Wenn ein Problem beim Ausführen des Indexers vorliegt oder eine große Anzahl von Dokumenten indiziert werden muss, steht dem Indexer viel Zeit für die endgültige Verarbeitung der vorläufig gelöschten Blobs zur Verfügung. Indexer von Azure Cognitive Suche löschen nur dann ein Dokument aus dem Index, wenn sie das Blob verarbeiten, während es sich in einem vorläufig gelöschten Zustand befindet.

1. Legen Sie in Cognitive Search für die Datenquelle eine Richtlinie zur Erkennung des nativen vorläufigen Löschens von Blobs fest. Ein entsprechendes Beispiel ist nachfolgend dargestellt. Da dieses Feature in der Vorschauversion verfügbar ist, müssen Sie die Vorschau-REST-API verwenden.

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

1. [Führen Sie den Indexer aus](/rest/api/searchservice/run-indexer), oder legen Sie fest, dass der Indexer [gemäß einem Zeitplan](search-howto-schedule-indexers.md) ausgeführt wird. Wenn der Indexer ausgeführt wird und einen Blob verarbeitet, der sich im vorläufig gelöschten Zustand befindet, wird das entsprechende Suchdokument aus dem Index entfernt.

### <a name="reindexing-undeleted-blobs-using-native-soft-delete-policies"></a>Erneutes Indizieren nicht gelöschter Blobs (mithilfe von nativen Richtlinien für vorläufiges Löschen)

Wenn Sie einen vorläufig gelöschten Blob im Blobspeicher wiederherstellen, wird er vom Indexer nicht immer neu indiziert. Das liegt daran, dass der Indexer den `LastModified`-Zeitstempel des Blobs verwendet, um festzustellen, ob eine Indizierung erforderlich ist. Wenn ein vorläufig gelöschtes Blob wiederhergestellt wird, wird sein `LastModified`-Zeitstempel nicht aktualisiert. Falls der Indexer bereits Blobs mit aktuelleren `LastModified`-Zeitstempeln verarbeitet hat, wird das wiederhergestellte Blob nicht neu indiziert. 

Um sicherzustellen, dass ein wieder hergestelltes Blob neu indiziert wird, müssen Sie den `LastModified`-Zeitstempel des Blobs aktualisieren. Eine Möglichkeit hierfür besteht darin, die Metadaten des Blobs erneut zu speichern. Sie müssen die Metadaten nicht ändern, aber durch das erneute Speichern der Metadaten wird der `LastModified`-Zeitstempel des Blobs aktualisiert, sodass der Indexer weiß, dass er ihn neu indizieren muss.

## <a name="soft-delete-using-custom-metadata"></a>Vorläufiges Löschen mithilfe benutzerdefinierter Metadaten

Diese Methode verwendet die Metadaten eines Blobs, um zu bestimmen, ob ein Suchdokument aus dem Index entfernt werden soll. Diese Methode erfordert zwei separate Aktionen: Zunächst wird das Suchdokument aus dem Index und dann das Blob in Azure Storage gelöscht.

Sowohl bei der Blobspeicherung als auch bei Cognitive Search gibt es Schritte, die zu befolgen sind, aber es bestehen keine weiteren Featureabhängigkeiten. Diese Funktion wird in allgemein verfügbaren APIs unterstützt.

1. Fügen Sie dem Blob ein Metadaten-Schlüssel-Wert-Paar hinzu, um in Azure Cognitive Search anzugeben, dass das Blob logisch gelöscht ist.

1. Konfigurieren Sie für die Datenquelle eine Richtlinie zur Erkennung von Spalten für vorläufiges Löschen. Bei der folgenden Richtlinie wird ein Blob beispielsweise als gelöscht angesehen, wenn es über die Metadateneigenschaft `IsDeleted` mit dem Wert `true` verfügt:

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
    ```

1. Nachdem der Indexer das Blob verarbeitet und das Dokument aus dem Index gelöscht hat, können Sie das Blob in Azure Blob Storage löschen.

### <a name="reindexing-undeleted-blobs-using-custom-metadata"></a>Erneutes Indizieren wiederhergestellter Blobs (mithilfe von benutzerdefinierten Metadaten)

Nachdem ein Indexer ein gelöschtes Blob verarbeitet und das entsprechende Suchdokument aus dem Index gelöscht hat, wird das Blob nicht erneut überprüft, wenn Sie es später wiederherstellen und der `LastModified`-Zeitstempel des Blobs älter als die letzte Indexerausführung ist.

Wenn Sie dieses Dokument neu indizieren möchten, ändern Sie `"softDeleteMarkerValue" : "false"` für das Blob, und führen Sie den Indexer erneut aus.

## <a name="next-steps"></a>Nächste Schritte

+ [Indexer in der kognitiven Azure-Suche](search-indexer-overview.md)
+ [Indizieren von Dokumenten in Azure Blob Storage mit der kognitiven Azure-Suche](search-howto-indexing-azure-blob-storage.md)
+ [Hinzufügen der Volltextsuche zu Azure-Blobdaten mithilfe von Azure Cognitive Search](search-blob-storage-integration.md)