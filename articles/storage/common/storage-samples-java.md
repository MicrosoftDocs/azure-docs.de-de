---
title: Azure Storage-Beispiele mit Java | Microsoft Docs
description: Sie können Beispielcode und -anwendungen für Azure Storage anzeigen, herunterladen und ausführen. Entdecken Sie Beispiele für erste Schritte mit Blobs, Warteschlangen, Tabellen und Dateien, indem Sie die Speicherclientbibliotheken für Java verwenden.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 05/03/2019
ms.author: mhopkins
ms.subservice: common
ms.openlocfilehash: 3d241f1905244d3a8039372262f84ba0fd25220d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209776"
---
# <a name="azure-storage-samples-using-java"></a>Azure Storage-Beispiele mit Java

## <a name="java-sample-index"></a>Index für Java-Beispiele

Die folgende Tabelle enthält eine Übersicht über unser Beispielrepository und in jedem Beispiel behandelten Szenarien. Klicken Sie auf die Links, um den zugehörigen Beispielcode in GitHub anzuzeigen.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Endpunkt</th><th style="font-size:110%">Szenario</th><th style="font-size:110%">Beispielcode</th></tr></thead><tbody>
<tr>
<td rowspan="16"><b>Blob</b></td>
<td>Blob anfügen</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Erste Schritte mit dem Azure Blob-Dienst in Java</a></td>
</tr>
<tr>
<td>Blockblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Erste Schritte mit dem Azure Blob-Dienst in Java</a></td>
</tr>
<tr>
<td>Clientseitige Verschlüsselung</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption">Erste Schritte mit clientseitiger Azure-Verschlüsselung in Java</a></td>
</tr>
<tr>
<td>Kopieren von Blobs</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Erste Schritte mit dem Azure Blob-Dienst in Java</a></td>
</tr>
<tr>
<td>Create Container</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Erste Schritte mit dem Azure Blob-Dienst in Java</a></td>
</tr>
<tr>
<td>Delete Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Erste Schritte mit dem Azure Blob-Dienst in Java</a></td>
</tr>
<tr>
<td>Delete Container</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Erste Schritte mit dem Azure Blob-Dienst in Java</a></td>
</tr>
<tr>
<td>Blobmetadaten/-eigenschaften/-statistiken</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Erste Schritte mit dem Azure Blob-Dienst in Java</a></td>
</tr>
<tr>
<td>Container-ACL/Metadaten/Eigenschaften</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Erste Schritte mit dem Azure Blob-Dienst in Java</a></td>
</tr>
<tr>
<td>Get Page Ranges</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java#L399">Erste Schritte mit dem Azure Blob-Dienst in Java</a></td>
</tr>
<tr>
<td>Leaseblob/-container</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Erste Schritte mit dem Azure Blob-Dienst in Java</a></td>
</tr>
<tr>
<td>Listenblob/-container</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Erste Schritte mit dem Azure Blob-Dienst in Java</a></td>
</tr>
<tr>
<td>Seitenblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Erste Schritte mit dem Azure Blob-Dienst in Java</a></td>
</tr>
<tr>
<td>SAS</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/89540f018f1160ce55619c6fe7b5f5ff57d0ce10/src/test/java/com/microsoft/azure/storage/Samples.java#L513">Beispiel für SAS-Tests</a></td>
</tr>   
<tr>
<td>Diensteigenschaften</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Erste Schritte mit dem Azure Blob-Dienst in Java</a></td>
</tr>
<tr>
<td>Snapshot Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Erste Schritte mit dem Azure Blob-Dienst in Java</a></td>
</tr>
<tr>
<td rowspan="9"><b>Datei</b></td>
<td>Erstellen von Freigaben/Verzeichnissen/Dateien</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Erste Schritte mit dem Azure-Dateidienst in Java</a></td>
</tr>
<tr>
<td>Löschen von Freigaben/Verzeichnissen/Dateien</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Erste Schritte mit dem Azure-Dateidienst in Java</a></td>
</tr>
<tr>
<td>Verzeichniseigenschaften/-metadaten</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Erste Schritte mit dem Azure-Dateidienst in Java</a></td>
</tr>
<tr>
<td>Herunterladen von Dateien</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Erste Schritte mit dem Azure-Dateidienst in Java</a></td>
</tr>
<tr>
<td>Dateieigenschaften/-metadaten/-metriken</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Erste Schritte mit dem Azure-Dateidienst in Java</a></td>
</tr>
<tr>
<td>Dateidiensteigenschaften</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Erste Schritte mit dem Azure-Dateidienst in Java</a></td>
</tr>
<tr>
<td>Auflisten von Verzeichnissen und Dateien</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Erste Schritte mit dem Azure-Dateidienst in Java</a></td>
</tr>
<tr>
<td>Auflisten von Freigaben</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Erste Schritte mit dem Azure-Dateidienst in Java</a></td>
</tr>
<tr>
<td>Freigabeeigenschaften/-metadaten/-statistiken</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Erste Schritte mit dem Azure-Dateidienst in Java</a></td>
</tr>
<tr>
<td rowspan="8"><b>Warteschlange</b></td>
<td>Hinzufügen von Nachrichten</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java#L63">Erste Schritte mit dem Azure-Warteschlangendienst in Java</a></td>
</tr>
<tr>
<td>Clientseitige Verschlüsselung</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption/blob/master/src/gettingstarted/KeyVaultGettingStarted.java">Erste Schritte mit clientseitiger Azure-Verschlüsselung in Java</a></td>
</tr>
<tr>
<td>Erstellen von Warteschlangen</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Erste Schritte mit dem Azure-Warteschlangendienst in Java</a></td>
</tr>
<tr>
<td>Löschen von Nachrichten/Warteschlangen</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Erste Schritte mit dem Azure-Warteschlangendienst in Java</a></td>
</tr>
<tr>
<td>Nachricht einsehen</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Erste Schritte mit dem Azure-Warteschlangendienst in Java</a></td>
</tr>
<tr>
<td>Warteschlangen-ACL/-metadaten/-statistiken</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Erste Schritte mit dem Azure-Warteschlangendienst in Java</a></td>
</tr>
<tr>
<td>Warteschlangendiensteigenschaften</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Erste Schritte mit dem Azure-Warteschlangendienst in Java</a></td>
</tr>
<tr>
<td>Aktualisieren von Nachrichten</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Erste Schritte mit dem Azure-Warteschlangendienst in Java</a></td>
</tr>
<tr>
<td rowspan="7"><b>Tabelle</b></td>
<td>Erstellen von Tabellen</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Erste Schritte mit dem Azure-Tabellenspeicherdienst in Java</a></td>
</tr>
<tr>
<td>Löschen von Entitäten/Tabellen</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Erste Schritte mit dem Azure-Tabellenspeicherdienst in Java</a></td>
</tr>
<tr>
<td>Einfügen/Zusammenführen/Ersetzen von Entitäten</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Erste Schritte mit dem Azure-Tabellenspeicherdienst in Java</a></td>
</tr>
<tr>
<td>Entitäten abfragen</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Erste Schritte mit dem Azure-Tabellenspeicherdienst in Java</a></td>
</tr>
<tr>
<td>Abfragen von Tabellen</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Erste Schritte mit dem Azure-Tabellenspeicherdienst in Java</a></td>
</tr>
<tr>
<td>Tabellen-ACL/-eigenschaften</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java">Erste Schritte mit dem Azure-Tabellenspeicherdienst in Java</a></td>
</tr>
<tr>
<td>Entität aktualisieren</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Erste Schritte mit dem Azure-Tabellenspeicherdienst in Java</a></td>
</tr>
</tbody>
</table>
<br/>

## <a name="azure-code-samples-library"></a>Bibliothek mit Azure-Codebeispielen

Wechseln Sie zum Anzeigen der vollständigen Beispielbibliothek zur [Bibliothek mit Azure-Codebeispielen](https://azure.microsoft.com/resources/samples/?service=storage), die Beispiele für Azure Storage enthält, die Sie herunterladen und lokal ausführen können. Die Bibliothek mit Codebeispielen enthält Beispielcode im ZIP-Format. Alternativ können Sie die einzelnen Beispiele im GitHub-Repository suchen und klonen.

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>Leitfäden mit ersten Schritten

Lesen Sie den folgenden Leitfäden, um Anleitungen zum Installieren und zu den ersten Schritten mit den Azure Storage-Clientbibliotheken zu erhalten.

* [Erste Schritte mit dem Azure Blob-Dienst in Java](../blobs/storage-quickstart-blobs-java.md)
* [Erste Schritte mit dem Azure-Warteschlangendienst in Java](../queues/storage-java-how-to-use-queue-storage.md)
* [Erste Schritte mit dem Azure-Tabellenspeicherdienst in Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Erste Schritte mit dem Azure-Dateidienst in Java](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Beispielen für andere Sprachen:

* .NET: [Azure Storage-Beispiele mit .NET](storage-samples-dotnet.md)
* Alle anderen Sprachen: [Beispiele für Azure Storage](storage-samples.md)
