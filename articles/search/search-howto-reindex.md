---
title: Neuerstellen eines Suchindex
titleSuffix: Azure Cognitive Search
description: Hinzufügen neuer Elemente, Aktualisieren vorhandener Elemente oder Dokumente oder Löschen veralteter Dokumente in einer Neuerstellung oder einer Teilindizierung, um einen Index der kognitiven Azure-Suche zu aktualisieren.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.openlocfilehash: ae8fd7f405beb20d516835ccb80b86e769fd0393
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697083"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Neuerstellen eines Index in Azure Cognitive Search.

In diesem Artikel werden die Neuerstellung eines Index der kognitiven Azure-Suche und die Umstände erläutert, unter denen eine Neuerstellung erforderlich ist. Außerdem werden Empfehlungen gegeben, wie die Auswirkungen von Neuerstellungen auf laufende Abfrageanforderungen auf ein Mindestmaß reduziert werden können.

Eine *Neuerstellung* bezieht sich auf Löschen und Neuerstellen der physischen Datenstrukturen, die einem Index zugeordnet sind, einschließlich aller auf Feldern basierenden invertierten Indizes. In der kognitiven Azure-Suche können einzelne Felder nicht gelöscht und neu erstellt werden. Um einen Index neu zu erstellen, muss der gesamte Feldspeicher gelöscht, basierend auf einem vorhandenen oder überarbeiteten Indexschema neu erstellt und dann mit Daten neu aufgefüllt werden, die mithilfe von Push an den Index übertragen oder per Pull aus externen Quellen bezogen werden. 

Normalerweise werden Indizes während der Entwicklung neu erstellt, wenn Sie den Indexentwurf durchlaufen. Es kann jedoch vorkommen, dass Sie einen Index auf Produktionsebene neu erstellen müssen, um Strukturänderungen zu berücksichtigen, wenn beispielsweise komplexe Typen oder Felder zu Vorschlagsfunktionen hinzugefügt werden.

## <a name="rebuild-versus-refresh"></a>„Neu erstellen“ im Vergleich zu „Aktualisieren“

Die Neuerstellung sollte nicht mit dem Aktualisieren des Inhalts eines Indexes mit neuen, geänderten oder gelöschten Dokumenten verwechselt werden. Die Aktualisierung eines Suchkorpus ist fast in jeder Suchanwendung eine Selbstverständlichkeit, wobei einige Szenarien aktuelle Aktualisierungen erfordern (z. B. wenn ein Suchkorpus Bestandsänderungen in einer Onlineverkaufsanwendung widerspiegeln muss).

Solange Sie die Struktur des Index nicht ändern, können Sie einen Index mit den gleichen Techniken aktualisieren, mit denen Sie den Index anfänglich geladen haben:

* Bei der Indizierung im Pushmodus können Sie [Dokumente hinzufügen, aktualisieren oder löschen](/rest/api/searchservice/addupdate-or-delete-documents) aufrufen, um die Änderungen in einen Index zu pushen.

* Für Indexer können Sie die [Indexerausführung planen](search-howto-schedule-indexers.md) und Änderungsnachverfolgung oder Zeitstempel verwenden, um das Delta zu identifizieren. Wenn Aktualisierungen schneller widergespiegelt werden müssen, als es von einem Scheduler geleistet werden kann, können Sie stattdessen die Indizierung im Pushmodus verwenden.

## <a name="rebuild-conditions"></a>Bedingungen für die Neuerstellung

Wenn eine der folgenden Bedingungen erfüllt ist, löschen Sie den Index, und erstellen Sie ihn neu. 

| Bedingung | BESCHREIBUNG |
|-----------|-------------|
| Ändern einer Felddefinition | Für die Überarbeitung eines Feldnamens, Datentyps oder spezifischer [Indexattribute](/rest/api/searchservice/create-index) (durchsuchbar, filterbar, sortierbar, facettenreich) ist eine vollständige Neuerstellung erforderlich. |
| Zuweisen eines Analysetools zu einem Feld | [Analysetools](search-analyzers.md) werden in einem Index definiert und dann Feldern zugewiesen. Sie können einem Index jederzeit eine neue Analysetooldefinition hinzufügen, aber Sie können ein Analysetool nur *zuweisen*, wenn das Feld erstellt wird. Dies gilt sowohl für das **Analysetool** als auch die **indexAnalyzer**-Eigenschaften. Die **searchAnalyzer**-Eigenschaft ist eine Ausnahme (Sie können diese Eigenschaft einem vorhandenen Feld zuweisen). |
| Aktualisieren oder Löschen einer Analysetooldefinition in einem Index | Sie können eine bestehende Analysetoolkonfiguration (Analysetool, Tokenizer, Tokenfilter oder Zeichenfilter) im Index nicht löschen oder ändern, es sei denn, Sie erstellen den gesamten Index neu. |
| Hinzufügen eines Felds zu einer Vorschlagsfunktion | Wenn ein Feld bereits vorhanden ist, und Sie es einer [Vorschlagsfunktion](index-add-suggesters.md) hinzufügen möchten, müssen Sie den Index neu erstellen. |
| Löschen eines Felds | Um alle Spuren eines Felds physisch entfernen zu können, müssen Sie den Index neu erstellen. Wenn eine sofortige Neuerstellung nicht praktikabel ist, können Sie den Anwendungscode so ändern, dass der Zugriff auf das "gelöschte" Feld deaktiviert wird, oder den [$select-Abfrageparameter](search-query-odata-select.md) verwenden, um auszuwählen, welche Felder im Resultset angezeigt werden. Die Felddefinition und die Inhalte bleiben physisch bis zur nächsten Neuerstellung im Index, wenn Sie ein Schema verwenden, bei dem das betreffende Feld ausgelassen wird. |
| Wechseln zwischen Ebenen | Wenn Sie mehr Kapazität benötigen, gibt es kein direktes Upgrade im Azure-Portal. Es muss ein neuer Dienst erstellt werden, und Indizes müssen von Grund auf für den neuen Dienst erstellt werden. Um diesen Prozess zu automatisieren, können Sie den **index-backup-restore**-Beispielcode in diesem [.NET-Beispielrepository für die kognitive Azure-Suche](https://github.com/Azure-Samples/azure-search-dotnet-samples) verwenden. Diese App sichert Ihren Index in einer Reihe von JSON-Dateien und erstellt ihn dann in einem Suchdienst, den Sie angeben, neu.|

## <a name="update-conditions"></a>Aktualisieren von Bedingungen

Viele weitere Änderungen können ohne Auswirkungen auf die vorhandenen physischen Strukturen vorgenommen werden. Insbesondere ist für die folgenden Änderungen *keine* Indexneuerstellung erforderlich. Für diese Änderungen können Sie [eine Indexdefinition entsprechend aktualisieren](/rest/api/searchservice/update-index).

+ Hinzufügen eines neuen Felds
+ Festlegen des Attribut **Abrufbar** für ein vorhandenes Feld
+ Festlegen eines **searchAnalyzer** für ein vorhandenes Feld
+ Hinzufügen einer neuen Analysetooldefinition in einem Index
+ Hinzufügen, Aktualisieren oder Löschen von Bewertungsprofilen
+ Hinzufügen, Aktualisieren oder Löschen von CORS-Einstellungen
+ Hinzufügen, Aktualisieren oder Löschen von synonymMaps

Wenn Sie ein neues Feld hinzufügen, erhalten vorhandene indizierte Dokumente einen NULL-Wert für das neue Feld. Bei einer späteren Datenaktualisierung werden die mit der kognitiven Azure-Suche hinzugefügten NULL-Werte durch Werte aus externen Quelldaten ersetzt. Weitere Informationen zum Aktualisieren von Indexinhalten finden Sie unter [Hinzufügen, Aktualisieren oder Löschen von Dokumenten](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="how-to-rebuild-an-index"></a>Neuerstellen eines Indexes

Während der Entwicklung ändert sich das Indexschema häufig. Sie können dies einplanen, indem Sie Indizes mit einem kleinen repräsentativen Datensatz erstellen, die schnell gelöscht, neu erstellt und wieder geladen werden können.

Für Anwendungen, die bereits in der Produktion eingesetzt werden, empfiehlt sich die Erstellung eines neuen Indexes, der parallel zu einem vorhandenen Index ausgeführt wird, um eine Downtime von Abfragen zu vermeiden. Ihr Anwendungscode ermöglicht die Umleitung auf den neuen Index.

Die Indizierung wird nicht im Hintergrund ausgeführt, und der Dienst gleicht die zusätzliche Indizierung mit laufenden Abfragen aus. Während der Indizierung können Sie [Abfrageanforderungen im Portal überwachen](search-monitor-queries.md), um sicherzustellen, dass die Abfragen innerhalb eines angemessenen Zeitraums abgeschlossen werden.

1. Bestimmen Sie, ob eine Neuerstellung erforderlich ist. Wenn Sie nur Felder hinzufügen oder einen Teil des Indexes ändern, der nicht mit Feldern zusammenhängt, können Sie die [Definition möglicherweise einfach aktualisieren](/rest/api/searchservice/update-index), ohne sie zu löschen, neu zu erstellen und vollständig neu zu laden.

1. [Rufen Sie eine Indexdefinition ab](/rest/api/searchservice/get-index), falls Sie zukünftig darauf verweisen müssen.

1. [Löschen Sie den vorhandenen Index](/rest/api/searchservice/delete-index), vorausgesetzt, dass Sie nicht parallel neue und alte Indizes ausführen. 

   Alle Abfragen, die sich an diesen Index richten, werden sofort gelöscht. Denken Sie daran, dass das Löschen eines Indexes nicht rückgängig gemacht werden kann. Der physische Speicher für die Feldsammlung und andere Konstrukte wird zerstört. Bedenken Sie vor dem Löschen in Ruhe die Auswirkungen. 

1. [Erstellen Sie einen überarbeiteten Index](/rest/api/searchservice/create-index), wobei der Text der Anforderung geänderte oder modifizierte Felddefinitionen enthält.

1. [Laden Sie den Index mit Dokumenten](/rest/api/searchservice/addupdate-or-delete-documents) aus einer externen Quelle.

Bei der Indexerstellung wird für jedes Feld im Indexschema physischer Speicher zugewiesen, und für jedes durchsuchbare Feld wird ein invertierter Index erstellt. Nicht durchsuchbare Felder können in Filtern oder Ausdrücken verwendet werden, besitzen aber keine invertierten Indizes und können nicht mit der Volltext- oder Fuzzysuche durchsucht werden. Bei einer Indexneuerstellung werden diese invertierten Indizes gelöscht und basierend auf dem von Ihnen angegebenen Indexschema neu erstellt.

Wenn Sie den Index laden, wird der invertierte Index der einzelnen Felder mit allen eindeutigen, mit Token versehenen Wörtern aus den einzelnen Dokumenten aufgefüllt, zusammen mit einer Zuordnung zu entsprechenden Dokument-IDs. Beim Indizieren eines Datasets mit Hotels enthält ein invertierter Index, der für ein Feld „Stadt“ erstellt wurde, beispielsweise Begriffe für Seattle, Portland usw. Bei Dokumenten, die Seattle oder Portland im Feld „Stadt“ enthalten, wird die Dokument-ID neben dem Begriff aufgeführt. Bei jedem Vorgang zum [Hinzufügen, Aktualisieren oder Löschen](/rest/api/searchservice/addupdate-or-delete-documents) wird die Liste aus Begriffen und Dokument-IDs entsprechend aktualisiert.

> [!NOTE]
> Wenn Sie strikte SLA-Anforderungen einhalten müssen, sollten Sie speziell für diese Aufgabe einen neuen Dienst bereitstellen, bei dem Entwicklung und Indizierung vollständig isoliert von einem Produktionsindex stattfinden. Ein separater Dienst wird auf eigener Hardware ausgeführt, sodass keine Ressourcenkonflikte entstehen können. Nach Abschluss der Entwicklung behalten Sie den neuen Index bei und leiten Abfragen an den neuen Endpunkt und Index um, oder Sie führen fertiggestellten Code aus, um einen überarbeiteten Index für den ursprünglichen Dienst der kognitiven Azure-Suche zu veröffentlichen. Zurzeit gibt es keinen Mechanismus zum Verschieben eines sofort einsatzbereiten Indexes zu einem anderen Dienst.

## <a name="check-for-updates"></a>Suchen nach Updates

Sie können mit der Abfrage eines Indexes beginnen, sobald das erste Dokument geladen wurde. Wenn Sie die ID eines Dokuments kennen, gibt die [REST-API zur Dokumentsuche](/rest/api/searchservice/lookup-document) das jeweilige Dokument zurück. Für umfangreichere Testvorgänge sollten Sie warten, bis der Index vollständig geladen wurde, und anschließend den erwarteten Kontext anhand von Abfragen überprüfen.

Sie können den [Suchexplorer](search-explorer.md) oder ein Webtesttool wie [Postman oder Visual Studio Code](search-get-started-rest.md) verwenden, um nach aktualisierten Inhalten zu suchen.

Wenn Sie ein Feld hinzugefügt oder umbenannt haben, verwenden Sie [$select](search-query-odata-select.md), um dieses Feld zurückzugeben: `search=*&$select=document-id,my-new-field,some-old-field&$count=true`

## <a name="see-also"></a>Weitere Informationen

+ [Indexer (Übersicht)](search-indexer-overview.md)
+ [Bedarfsorientiertes Indizieren großer Datasets](search-howto-large-index.md)
+ [Indizieren im Portal](search-import-data-portal.md)
+ [Azure SQL-Datenbank-Indexer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-Indexer](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-Indexer](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-Indexer](search-howto-indexing-azure-tables.md)
+ [Sicherheit in der kognitiven Azure-Suche](search-security-overview.md)