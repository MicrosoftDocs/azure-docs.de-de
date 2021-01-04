---
title: Auflisten von Azure Storage-Ressourcen mit der C++-Clientbibliothek
description: Erfahren Sie, wie Sie die Auflistungs-APIs in Microsoft Azure Storage Client Library for C++ verwenden können, um Container, BLOBs, Warteschlangen, Tabellen und Entitäten aufzuzählen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/23/2017
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: 8c8e2d2ddf6899e62bc95bc1e52c84eccdc3a91e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92784097"
---
# <a name="list-azure-storage-resources-in-c"></a>Auflisten von Azure Storage-Ressourcen in C++

Auflistungsvorgänge sind der Schlüssel für viele Entwicklungsszenarien mit Azure Storage. Dieser Artikel beschreibt, wie Objekte in Azure Storage mit den Auflistungs-APIs, die in der Microsoft Azure Storage Client Library for C++ bereitgestellt werden, am effizientesten aufgelistet werden können.

> [!NOTE]
> Diese Anleitung gilt für die Version 2.x der Azure Storage Client Library for C++, die über [NuGet](https://www.nuget.org/packages/wastorage) oder [GitHub](https://github.com/Azure/azure-storage-cpp) verfügbar ist.

Die Storage Client Library stellt eine Vielzahl von Methoden bereit, um Objekte in Azure Storage aufzulisten oder abzufragen. Dieser Artikel behandelt die folgenden Szenarien:

* Auflisten von Containern in einem Konto
* Auflisten von BLOBs in einem Container oder virtuellen BLOB-Verzeichnis
* Auflisten von Warteschlangen in einem Konto
* Auflisten von Tabellen in einem Konto
* Abfragen von Entitäten in einer Tabelle

Jede dieser Methoden wird mit unterschiedlichen Überladungen für unterschiedliche Szenarios dargestellt.

## <a name="asynchronous-versus-synchronous"></a>Asynchrone und synchrone Vorgänge

Da die Storage Client Library for C++ auf Basis der [C++ REST-Bibliothek](https://github.com/Microsoft/cpprestsdk) erstellt wurde, werden asynchrone Vorgänge grundsätzlich unterstützt, indem [pplx::task](https://microsoft.github.io/cpprestsdk/classpplx_1_1task.html) verwendet wird. Beispiel:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

Synchrone Vorgänge umschließen die entsprechenden asynchronen Vorgänge:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Wenn Sie mit mehreren Threadinganwendungen oder -diensten arbeiten, empfiehlt es sich, dass Sie die asynchronen APIs direkt verwenden, anstatt einen Thread zu erstellen, mit dem die synchronen APIs aufgerufen werden, wodurch die Leistung erheblich beeinträchtigt wird.

## <a name="segmented-listing"></a>Segmentiertes Auflisten

Der Umfang von Cloud-Speicher erfordert segmentiertes Auflisten. Beispielsweise können Sie über eine Million BLOBs in einem Azure-BLOB-Container oder über eine Milliarde Entitäten in einer Azure-Tabelle haben. Dies sind keine theoretischen Zahlen, sondern echte Anwendungsfälle von Kunden.

Daher ist es unpraktisch, alle Objekte in einer einzigen Antwort aufzulisten. Stattdessen können Sie Objekte mithilfe von Paging (Auslagern) auflisten. Jede der Auflistungs-APIs hat eine *segmentierte* Überladung.

Die Antwort für einen Vorgang mit segmentierter Auflistung enthält Folgendes:

* *_segment* , das die Ergebnisse enthält, die für einen einzelnen Aufruf der Auflistungs-API zurückgegeben wurden.
* *continuation_token* , das an den nächsten Aufruf übergeben wird, damit die nächste Seite mit Ergebnissen abgerufen wird. Gibt es keine weiteren zurückzugebenden Ergebnisse, ist das Fortsetzungstoken (continuation_token) gleich NULL.

Ein typischer Aufruf zum Auflisten alle BLOBs in einem Container könnte beispielsweise wie der folgende Codeausschnitt aussehen. Der Code ist in unseren [Beispielen](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted.cpp)verfügbar:

```cpp
// List blobs in the blob container
azure::storage::continuation_token token;
do
{
    azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}

    token = segment.continuation_token();
}
while (!token.empty());
```

Beachten Sie, dass die Anzahl von Ergebnissen, die in einer Seite zurückgegeben werden, durch den Parameter *max_results* in der Überladung jeder API gesteuert werden kann. Beispiel:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Wenn Sie den *max_results* -Parameter nicht angeben, wird in einer einzelnen Seite der standardmäßige maximale Wert von bis zu 5.000 Ergebnissen zurückgegeben.

Beachten Sie außerdem, dass eine Abfrage für den Azure-Tabellenspeicher möglicherweise keine Datensätze oder weniger Datensätze zurückgibt, als der Wert des von Ihnen angegebenen *max_results* -Parameters festlegt. Dies gilt selbst dann, wenn das Fortsetzungstoken nicht leer ist. Ein Grund hierfür ist möglicherweise, dass die Abfrage nicht in fünf Sekunden abgeschlossen werden konnte. Solange das Fortsetzungstoken nicht leer ist, sollte die Abfrage fortgesetzt werden, und Ihr Code sollte die Größe der Segmentergebnisse nicht voraussetzen.

Das empfohlene Codierungsmuster für die meisten Szenarios ist segmentiertes Auflisten, das expliziten Fortschritt für Auflisten oder Abfragen bereitstellt und festlegt, wie der Dienst auf jede Anforderungen reagiert. Insbesondere für C++-Anwendungen oder -Dienste kann eine auf niedrigerer Ebene erfolgende Steuerung des Auflistungsfortschritts hilfreich sein, den Arbeitsspeicher und die Leistung zu kontrollieren.

## <a name="greedy-listing"></a>Gieriges Auflisten

Frühere Versionen der Storage Client Library for C++ (Version 0.5.0 Preview und früher) enthalten APIs für nicht segmentiertes Auflisten für Tabellen und Warteschlangen, wie im folgenden Beispiel:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Diese Methoden wurden als Wrapper von segmentierten APIs implementiert. Für jede Antwort der segmentierten Auflistung fügt der Code die Ergebnisse an einen Vektor an und gibt alle Ergebnisse zurück, nachdem die vollständigen Container durchsucht sind.

Dieser Ansatz kann funktionieren, wenn das Speicherkonto oder die Speichertabelle eine kleine Anzahl von Objekten enthält. Bei einer größeren Anzahl von Objekten kann die Menge von benötigtem Arbeitsspeicher aber unbegrenzt zunehmen, weil alle Ergebnisse im Arbeitsspeicher verbleiben. Ein Auflistungsvorgang kann sehr viel Zeit beanspruchen, in der der Aufrufer keine Informationen über den Fortschritt des Vorgangs hat.

Diese gierigen Auflistungs-APIs im SDK gibt es in C#, in Java oder in der JavaScript-Umgebung Node.js nicht. Um die potenziellen Probleme bei Verwendung dieser gierigen APIs zu vermeiden, wurden sie in Version 0.6.0 Preview entfernt.

Für den Fall, dass in Ihrem Code diese gierigen APIs aufgerufen werden:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Ändern Sie Ihren Code so, dass in ihm die APIs für segmentiertes Auflisten verwendet werden:

```cpp
azure::storage::continuation_token token;
do
{
    azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        process_entity(*it);
    }

    token = segment.continuation_token();
} while (!token.empty());
```

Durch Angeben des *max_results* -Parameters des Segments können Sie zwischen der Anzahl von Anforderungen und der Arbeitsspeichernutzung abwägen, um den Leistungsanforderungen Ihrer Anwendung gerecht zu werden.

Außerdem sollten Sie, wenn Sie APIs für segmentiertes Auflisten verwenden, die Daten aber in einer lokalen Sammlung in einem „gierigen“ Stil speichern, Ihren Code so ändern, dass das Speichern von Daten in einer lokalen Sammlung sorgfältig entsprechend dem Umfang erfolgt.

## <a name="lazy-listing"></a>Faules (lazy) Auflisten

Obwohl gieriges Auflisten möglicherweise Probleme verursacht, ist es praktisch, wenn der Container nicht zu viele Objekte enthält.

Wenn Sie auch C# oder Oracle Java SDKs verwenden, sind Sie vermutlich mit dem Enumerable-Programmiermodell vertraut, das ein faules Auflisten bereitstellt, bei dem die Daten an einem bestimmten Offset nur abgerufen werden, wenn dies erforderlich ist. In C++ bietet die iteratorbasierte Vorlage einen ähnlichen Ansatz.

Eine typische API für faules Auflisten, in der **list_blobs** als Beispiel verwendet wird, sieht wie folgt aus:

```cpp
list_blob_item_iterator list_blobs() const;
```

Ein typischer Codeausschnitt, in dem das Muster für faules Auflisten verwendet wird, könnte folgendermaßen aussehen:

```cpp
// List blobs in the blob container
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}
```

Beachten Sie, dass faules Auflisten nur im synchronen Modus verfügbar ist.

Im Gegensatz zum gierigen Auflisten werden beim faulen Auflisten Daten nur bei Bedarf abgerufen. Konkret heißt das, faules Auflisten ruft nur dann Daten aus Azure Storage ab, wenn der nächste Iterator in das nächste Segment verschoben wird. Daher wird die Arbeitsspeichernutzung mit einer begrenzten Größe gesteuert, und der Vorgang ist schnell.

APIs für faules Auflisten sind in der Version 2.2.0 der Storage Client Library for C++ enthalten.

## <a name="conclusion"></a>Zusammenfassung

In diesem Artikel werden verschiedene Überladungen für Auflistungs-APIs für unterschiedliche Objekte erläutert, die in der Storage Client Library for C++ enthalten sind. Zusammenfassung:

* Asynchrone APIs sollten in allen Fällen verwendet werden, in denen mit Threading gearbeitet wird.
* Segmentiertes Auflisten ist für die meisten Fälle zu empfehlen.
* Faules (lazy) Auflisten wird in der Bibliothek als bequemer Wrapper für Fälle mit synchroner Verarbeitung bereitgestellt.
* Gieriges Auflisten ist nicht zu empfehlen und wurde aus der Bibliothek entfernt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Storage und zur Client Library for C++ finden Sie in den folgenden Ressourcen.

* [Verwenden des Blob-Speichers mit C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Verwenden des Tabellenspeichers mit C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Verwenden des Warteschlangenspeichers in C++](../queues/storage-c-plus-plus-how-to-use-queues.md)
* [Azure Storage Client Library for C++ API Documentation.](https://azure.github.io/azure-storage-cpp/)
* [Azure Storage-Teamblog](/archive/blogs/windowsazurestorage/)
* [Azure Storage-Dokumentation](https://azure.microsoft.com/documentation/services/storage/)