---
title: Azure Storage-Beispiele mit .NET | Microsoft Docs
description: Sie können Beispielcode und -anwendungen für Azure Storage anzeigen, herunterladen und ausführen. Entdecken Sie Beispiele für erste Schritte mit Blobs, Warteschlangen, Tabellen und Dateien, indem Sie die Speicherclientbibliotheken für .NET verwenden.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 05/03/2019
ms.author: mhopkins
ms.subservice: common
ms.openlocfilehash: df7c14f1ee83015303657f9a0babde3d60c92292
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209688"
---
# <a name="azure-storage-samples-using-net"></a>Azure Storage-Beispiele mit .NET

## <a name="net-sample-index"></a>Index für .NET-Beispiele

Die folgende Tabelle enthält eine Übersicht über unser Beispielrepository und in jedem Beispiel behandelten Szenarien. Klicken Sie auf die Links, um den zugehörigen Beispielcode in GitHub anzuzeigen.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Endpunkt</th><th style="font-size:110%">Szenario</th><th style="font-size:110%">Beispielcode</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Blob</b></td>
<td>Blob anfügen</td> 
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs#L1144">Erste Schritte mit Blobs</a></td> 
</tr> 
<tr> 
<td>Blockblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Webanwendung für eine Fotogalerie mit Azure Blob Storage</a></td>
</tr> 
<tr> 
<td>Clientseitige Verschlüsselung</td>
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs">Beispiele für die Blobverschlüsselung</a></td>
</tr> 
<tr> 
<td>Kopieren von Blobs</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Erste Schritte mit Blobs</a></td>
</tr> 
<tr> 
<td>Create Container</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Webanwendung für eine Fotogalerie mit Azure Blob Storage</a></td>
</tr> 
<tr> 
<td>Delete Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Webanwendung für eine Fotogalerie mit Azure Blob Storage</a></td>
</tr> 
<tr> 
<td>Delete Container</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Erste Schritte mit Blobs</a></td>
</tr> 
<tr> 
<td>Blobmetadaten/-eigenschaften/-statistiken</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Erste Schritte mit Blobs</a></td>
</tr> 
<tr> 
<td>Container-ACL/Metadaten/Eigenschaften</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Webanwendung für eine Fotogalerie mit Azure Blob Storage</a></td>
</tr> 
<tr> 
<td>Get Page Ranges</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Erste Schritte mit Blobs</a></td>
</tr> 
<tr> 
<td>Leaseblob/-container</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Erste Schritte mit Blobs</a></td>
</tr> 
<tr> 
<td>Listenblob/-container</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Erste Schritte mit Blobs</a></td>
</tr> 
<tr> 
<td>Seitenblob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Erste Schritte mit Blobs</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Erste Schritte mit Blobs</a></td>
</tr>   
<tr> 
<td>Diensteigenschaften</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Erste Schritte mit Blobs</a></td>
</tr>           
<tr> 
<td>Snapshot Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">Sichern von Azure-VM-Datenträgern mit inkrementellen Momentaufnahmen</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>Datei</b></td>
<td>Erstellen von Freigaben/Verzeichnissen/Dateien</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET – File Storage-Beispiel</a></td> 
</tr>
<tr> 
<td>Löschen von Freigaben/Verzeichnissen/Dateien</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">Erste Schritte mit dem Azure-Dateidienst in .NET</a></td> 
</tr> 
<tr> 
<td>Verzeichniseigenschaften/-metadaten</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET – File Storage-Beispiel</a></td> 
</tr> 
<tr> 
<td>Herunterladen von Dateien</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET – File Storage-Beispiel</a></td> 
</tr> 
<tr> 
<td>Dateieigenschaften/-metadaten/-metriken</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET – File Storage-Beispiel</a></td> 
</tr> 
<tr> 
<td>Dateidiensteigenschaften</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET – File Storage-Beispiel</a></td> 
</tr> 
<tr> 
<td>Auflisten von Verzeichnissen und Dateien</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET – File Storage-Beispiel</a></td> 
</tr>
<tr> 
<td>Auflisten von Freigaben</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET – File Storage-Beispiel</a></td> 
</tr>
<tr> 
<td>Freigabeeigenschaften/-metadaten/-statistiken</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET – File Storage-Beispiel</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>Warteschlange</b></td>
<td>Hinzufügen von Nachrichten</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Erste Schritte mit dem Azure-Warteschlangendienst in .NET</a></td> 
</tr> 
<tr> 
<td>Clientseitige Verschlüsselung</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Azure Storage .NET – clientseitige Verschlüsselung von Warteschlangen</a></td> 
</tr> 
<tr> 
<td>Erstellen von Warteschlangen</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Erste Schritte mit dem Azure-Warteschlangendienst in .NET</a></td> 
</tr> 
<tr> 
<td>Löschen von Nachrichten/Warteschlangen</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Erste Schritte mit dem Azure-Warteschlangendienst in .NET</a></td> 
</tr> 
<tr> 
<td>Nachricht einsehen</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Erste Schritte mit dem Azure-Warteschlangendienst in .NET</a></td> 
</tr> 
<tr> 
<td>Warteschlangen-ACL/-metadaten/-statistiken</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Erste Schritte mit dem Azure-Warteschlangendienst in .NET</a></td> 
</tr> 
<tr> 
<td>Warteschlangendiensteigenschaften</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Erste Schritte mit dem Azure-Warteschlangendienst in .NET</a></td> 
</tr> 
<tr> 
<td>Aktualisieren von Nachrichten</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Erste Schritte mit dem Azure-Warteschlangendienst in .NET</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Tabelle</b></td>
<td>Erstellen von Tabellen</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Verwalten von Nebenläufigkeit mit Azure Storage - Beispielanwendung</a></td> 
</tr> 
<tr> 
<td>Löschen von Entitäten/Tabellen</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Getting Started with Azure Table Storage in .NET</a> (Erste Schritte mit Azure Table Storage in .NET)</td> 
</tr> 
<tr> 
<td>Einfügen/Zusammenführen/Ersetzen von Entitäten</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Verwalten von Nebenläufigkeit mit Azure Storage - Beispielanwendung</a></td> 
</tr> 
<tr> 
<td>Entitäten abfragen</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Getting Started with Azure Table Storage in .NET</a> (Erste Schritte mit Azure Table Storage in .NET)</td> 
</tr> 
<tr> 
<td>Abfragen von Tabellen</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Getting Started with Azure Table Storage in .NET</a> (Erste Schritte mit Azure Table Storage in .NET)</td> 
</tr> 
<tr> 
<td>Tabellen-ACL/-eigenschaften</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs">Getting Started with Azure Table Storage in .NET</a> (Erste Schritte mit Azure Table Storage in .NET)</td> 
</tr> 
<tr> 
<td>Entität aktualisieren</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Verwalten von Nebenläufigkeit mit Azure Storage - Beispielanwendung</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Bibliothek mit Azure-Codebeispielen

Wechseln Sie zum Anzeigen der vollständigen Beispielbibliothek zur [Bibliothek mit Azure-Codebeispielen](https://azure.microsoft.com/resources/samples/?service=storage), die Beispiele für Azure Storage enthält, die Sie herunterladen und lokal ausführen können. Die Bibliothek mit Codebeispielen enthält Beispielcode im ZIP-Format. Alternativ können Sie die einzelnen Beispiele im GitHub-Repository suchen und klonen.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Leitfäden mit ersten Schritten

Lesen Sie den folgenden Leitfäden, um Anleitungen zum Installieren und zu den ersten Schritten mit den Azure Storage-Clientbibliotheken zu erhalten.

* [Erste Schritte mit dem Azure Blob-Dienst in .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Erste Schritte mit dem Azure-Warteschlangendienst in .NET](../storage-dotnet-how-to-use-queues.md)
* [Erste Schritte mit dem Azure-Tabellenspeicherdienst in .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Erste Schritte mit dem Azure-Dateidienst in .NET](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Beispielen für andere Sprachen:

* Java: [Azure Storage-Beispiele mit Java](storage-samples-java.md)
* Alle anderen Sprachen: [Beispiele für Azure Storage](../storage-samples.md)
