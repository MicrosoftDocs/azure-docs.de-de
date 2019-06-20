---
title: 'Neuerstellen eines Azure Search-Indexes oder Aktualisieren von durchsuchbarem Inhalt: Azure Search'
description: Fügen Sie neue Elemente zu einer Neuerstellung oder einer inkrementellen Teilindizierung hinzu, aktualisieren Sie bereits vorhandene Elemente oder Dokumente, oder löschen Sie veraltete Dokumente, um einen Azure Search-Index zu aktualisieren.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2595912732389c8a415d1854a84a7b9c182e4dc7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60871147"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Neuerstellen eines Azure Search-Indexes

In diesem Artikel werden die Neuerstellung eines Azure Search-Indexes und die Umstände erläutert, unter denen eine Neuerstellung erforderlich ist. Außerdem werden Empfehlungen bereitgestellt, wie die Auswirkungen von Neuerstellungen auf laufende Abfrageanforderungen auf ein Mindestmaß reduziert werden können.

Eine *Neuerstellung* bezieht sich auf Löschen und Neuerstellen der physischen Datenstrukturen, die einem Index zugeordnet sind, einschließlich aller auf Feldern basierenden invertierten Indizes. In Azure Search können einzelne Felder nicht gelöscht und neu erstellt werden. Um einen Index neu zu erstellen, muss der gesamte Feldspeicher gelöscht, basierend auf einem vorhandenen oder überarbeiteten Indexschema neu erstellt und dann mit Daten neu aufgefüllt werden, die mithilfe von Push an den Index übertragen oder per Pull aus externen Quellen bezogen werden. Normalerweise werden Indizes während der Entwicklung neu erstellt. Es kann jedoch vorkommen, dass Sie einen Index auf Produktionsebene neu erstellen müssen, um Strukturänderungen zu berücksichtigen, wenn beispielsweise komplexe Typen oder Felder zu Vorschlagsfunktionen hinzugefügt werden.

Im Gegensatz zu Neuerstellungen, bei denen ein Index offlinegeschaltet wird, wird die *Datenaktualisierung* als Hintergrundaufgabe ausgeführt. Sie können Dokumente mit minimalen Unterbrechungen für Abfrageworkloads hinzufügen, entfernen und ersetzen, auch wenn die Ausführung von Abfragen in der Regel längere Zeit in Anspruch nimmt. Weitere Informationen zum Aktualisieren von Indexinhalten finden Sie unter [Hinzufügen, Aktualisieren oder Löschen von Dokumenten](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="rebuild-conditions"></a>Bedingungen für die Neuerstellung

| Bedingung | BESCHREIBUNG |
|-----------|-------------|
| Ändern einer Felddefinition | Für die Überarbeitung eines Feldnamens, Datentyps oder spezifischer [Indexattribute](https://docs.microsoft.com/rest/api/searchservice/create-index) (durchsuchbar, filterbar, sortierbar, facettenreich) ist eine vollständige Neuerstellung erforderlich. |
| Zuweisen eines Analysetools zu einem Feld | [Analysetools](search-analyzers.md) werden in einem Index definiert und dann Feldern zugewiesen. Sie können einem Index jederzeit eine neue Analysetooldefinition hinzufügen, aber Sie können ein Analysetool nur *zuweisen*, wenn das Feld erstellt wird. Dies gilt sowohl für das **Analysetool** als auch die **indexAnalyzer**-Eigenschaften. Die **searchAnalyzer**-Eigenschaft ist eine Ausnahme (Sie können diese Eigenschaft einem vorhandenen Feld zuweisen). |
| Aktualisieren oder Löschen einer Analysetooldefinition in einem Index | Sie können eine bestehende Analysetoolkonfiguration (Analysetool, Tokenizer, Tokenfilter oder Zeichenfilter) im Index nicht löschen oder ändern, es sei denn, Sie erstellen den gesamten Index neu. |
| Hinzufügen eines Felds zu einer Vorschlagsfunktion | Wenn ein Feld bereits vorhanden ist, und Sie es einer [Vorschlagsfunktion](index-add-suggesters.md) hinzufügen möchten, müssen Sie den Index neu erstellen. |
| Löschen eines Felds | Um alle Spuren eines Felds physisch entfernen zu können, müssen Sie den Index neu erstellen. Wenn eine sofortige Wiederherstellung nicht sinnvoll ist, können Sie den Anwendungscode so ändern, dass der Zugriff auf das Feld „gelöscht“ deaktiviert wird. Die Felddefinition und die Inhalte bleiben physisch bis zur nächsten Neuerstellung im Index, wenn Sie ein Schema verwenden, bei dem das betreffende Feld ausgelassen wird. |
| Wechseln zwischen Ebenen | Wenn Sie mehr Kapazität benötigen, gibt es kein direktes Upgrade. Am neuen Kapazitätspunkt wird ein neuer Dienst erstellt, und Indizes müssen von Grund auf für den neuen Dienst erstellt werden. |

Alle übrigen Änderungen können ohne Auswirkungen auf die vorhandenen physischen Strukturen vorgenommen werden. Insbesondere ist für die folgenden Änderungen *keine* Indexneuerstellung erforderlich:

+ Hinzufügen eines neuen Felds
+ Festlegen des Attribut **Abrufbar** für ein vorhandenes Feld
+ Festlegen eines **searchAnalyzer** für ein vorhandenes Feld
+ Hinzufügen einer neuen Analysetooldefinition in einem Index
+ Hinzufügen, Aktualisieren oder Löschen von Bewertungsprofilen
+ Hinzufügen, Aktualisieren oder Löschen von CORS-Einstellungen
+ Hinzufügen, Aktualisieren oder Löschen von synonymMaps

Wenn Sie ein neues Feld hinzufügen, erhalten vorhandene indizierte Dokumente einen NULL-Wert für das neue Feld. Bei einer späteren Datenaktualisierung ersetzen Werte aus externen Quelldaten die von Azure Search hinzugefügten NULL-Werte. Weitere Informationen zum Aktualisieren von Indexinhalten finden Sie unter [Hinzufügen, Aktualisieren oder Löschen von Dokumenten](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="partial-or-incremental-indexing"></a>Teilindizierung oder inkrementelle Indizierung

In Azure Search können Sie die Indizierung nicht auf Feldbasis steuern und bestimmte Felder löschen oder neu erstellen. Ebenso gibt es keinen integrierten Mechanismus für das [Indizieren von Dokumenten basierend auf Kriterien](https://stackoverflow.com/questions/40539019/azure-search-what-is-the-best-way-to-update-a-batch-of-documents). Alle Anforderungen im Hinblick auf die kriteriengesteuerte Indizierung müssen durch benutzerdefinierten Code abgedeckt werden.

Eine Möglichkeit, die jedoch einfach umzusetzen ist, ist die *Aktualisierung von Dokumenten* in einem Index. Für viele Suchlösungen sind die externen Quelldaten nur temporär zugänglich, und die Synchronisierung zwischen Quelldaten und einem Suchindex ist gängige Praxis. Rufen Sie im Code den Vorgang zum [Hinzufügen, Aktualisieren oder Löschen von Dokumenten](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) oder die [.NET-Entsprechung](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexesoperationsextensions.createorupdate?view=azure-dotnet) auf, um den Indexinhalt zu aktualisieren oder um Werte für ein neues Feld hinzuzufügen.

## <a name="partial-indexing-with-indexers"></a>Teilindizierung mit Indexern

[Indexer](search-indexer-overview.md) vereinfachen die Aufgabe der Datenaktualisierung. Ein Indexer kann nur eine einzige Tabelle oder Sicht in der externen Datenquelle indizieren. Zum Indizieren mehrerer Tabellen besteht der einfachste Ansatz darin, eine Sicht zu erstellen, die Tabellen verknüpft und die Spalten projiziert, die Sie indizieren möchten. 

Wenn Sie Indexer verwenden, die Daten aus externen Quellen durchforsten, überprüfen Sie die Quelldaten auf eine Spalte „High Water Mark“ (Obergrenzenmarkierung). Falls vorhanden, können Sie diese zur Erkennung inkrementeller Änderungen verwenden, indem Sie nur die Zeilen mit neuen oder überarbeiteten Inhalten auswählen. Für den [Azure Blob-Speicher](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) wird ein `lastModified`-Feld verwendet. Im [Azure-Tabellenspeicher](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection) dient `timestamp` demselben Zweck. Genauso weisen sowohl der [Azure SQL-Datenbank-Indexer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) als auch der [Azure Cosmos DB-Indexer](search-howto-index-cosmosdb.md#indexing-changed-documents) Felder zum Kennzeichnen von Zeilenupdates auf. 

Weitere Informationen zu Indexern finden Sie in der [Übersicht über Indexer](search-indexer-overview.md) und im Artikel zur [REST-API zum Zurücksetzen von Indexern](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="how-to-rebuild-an-index"></a>Neuerstellen eines Indexes

Planen Sie häufige, vollständige Neuerstellungen für die aktive Entwicklung ein, wenn sich Indexschemas im Flux-Zustand befinden. Für Anwendungen, die bereits in der Produktion eingesetzt werden, empfiehlt sich die Erstellung eines neuen Indexes, der parallel zu einem vorhandenen Index ausgeführt wird, um eine Downtime von Abfragen zu vermeiden.

Für Indexupdates sind Lese-/Schreibberechtigungen auf Dienstebene erforderlich. 

Sie können einen Index nicht im Portal neu erstellen. Sie können programmgesteuert die [REST-API zur Indexaktualisierung](https://docs.microsoft.com/rest/api/searchservice/update-index) oder [äquivalente .NET-APIs](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexesoperations.createorupdatewithhttpmessagesasync?view=azure-dotnet) für eine vollständige Neuerstellung aufrufen. Die Anforderung einer Indexaktualisierung ist identisch mit der [REST-API zur Indexerstellung](https://docs.microsoft.com/rest/api/searchservice/create-index), hat aber einen anderen Kontext.

Der folgende Workflow ist eigentlich für die REST-API bestimmt, gilt aber gleichermaßen für das .NET SDK.

1. Wenn Sie den Indexnamen wiederverwenden, [löschen Sie den vorhandenen Index](https://docs.microsoft.com/rest/api/searchservice/delete-index). 

   Alle Abfragen, die sich an diesen Index richten, werden sofort gelöscht. Das Löschen eines Indexes kann nicht rückgängig gemacht werden. Der physische Speicher für die Feldsammlung und andere Konstrukte wird zerstört. Die Auswirkungen des Löschvorgangs für einen Index müssen Ihnen unbedingt klar sein, bevor Sie den Index löschen. 

2. Formulieren Sie eine Anforderung zur [Indexaktualisierung](https://docs.microsoft.com/rest/api/searchservice/update-index) mit Ihrem Dienstendpunkt, API-Schlüssel und einem [Administratorschlüssel](https://docs.microsoft.com/azure/search/search-security-api-keys). Ein Administratorschlüssel ist für Schreibvorgänge erforderlich.

3. Geben Sie im Anforderungstext ein Indexschema mit den geänderten oder modifizierten Felddefinitionen an. Der Anforderungstext enthält das Indexschema sowie Konstrukte für Bewertungsprofile, Analysetools, Vorschlagsfunktionen und CORS-Optionen. Schemaanforderungen werden unter [Erstellen eines Indexes](https://docs.microsoft.com/rest/api/searchservice/create-index) dokumentiert.

4. Senden Sie eine Anforderung zur [Indexaktualisierung](https://docs.microsoft.com/rest/api/searchservice/update-index), um den physischen Ausdruck des Indexes in Azure Search neu zu erstellen. 

5. [Laden Sie den Index mit Dokumenten](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) aus einer externen Quelle.

Bei der Indexerstellung wird für jedes Feld im Indexschema physischer Speicher zugewiesen, und für jedes durchsuchbare Feld wird ein invertierter Index erstellt. Nicht durchsuchbare Felder können in Filtern oder Ausdrücken verwendet werden, besitzen aber keine invertierten Indizes und können nicht mit der Volltext- oder Fuzzysuche durchsucht werden. Bei einer Indexneuerstellung werden diese invertierten Indizes gelöscht und basierend auf dem von Ihnen angegebenen Indexschema neu erstellt.

Wenn Sie den Index laden, wird der invertierte Index der einzelnen Felder mit allen eindeutigen, mit Token versehenen Wörtern aus den einzelnen Dokumenten aufgefüllt, zusammen mit einer Zuordnung zu entsprechenden Dokument-IDs. Beim Indizieren eines Datasets mit Hotels enthält ein invertierter Index, der für ein Feld „Stadt“ erstellt wurde, beispielsweise Begriffe für Seattle, Portland usw. Bei Dokumenten, die Seattle oder Portland im Feld „Stadt“ enthalten, wird die Dokument-ID neben dem Begriff aufgeführt. Bei jedem Vorgang zum [Hinzufügen, Aktualisieren oder Löschen](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) wird die Liste aus Begriffen und Dokument-IDs entsprechend aktualisiert.

> [!NOTE]
> Wenn Sie strikte SLA-Anforderungen einhalten müssen, sollten Sie speziell für diese Aufgabe einen neuen Dienst bereitstellen, bei dem Entwicklung und Indizierung vollständig isoliert von einem Produktionsindex stattfinden. Ein separater Dienst wird auf eigener Hardware ausgeführt, sodass keine Ressourcenkonflikte entstehen können. Nach Abschluss der Entwicklung behalten Sie den neuen Index bei und leiten Abfragen an den neuen Endpunkt und Index um, oder Sie führen fertiggestellten Code aus, um einen überarbeiteten Index für Ihren ursprünglichen Azure Search-Dienst zu veröffentlichen. Zurzeit gibt es keinen Mechanismus zum Verschieben eines sofort einsatzbereiten Indexes zu einem anderen Dienst.

## <a name="view-updates"></a>Anzeigen von Updates

Sie können mit der Abfrage eines Indexes beginnen, sobald das erste Dokument geladen wurde. Wenn Sie die ID eines Dokuments kennen, gibt die [REST-API zur Dokumentsuche](https://docs.microsoft.com/rest/api/searchservice/lookup-document) das jeweilige Dokument zurück. Für umfangreichere Testvorgänge sollten Sie warten, bis der Index vollständig geladen wurde, und anschließend den erwarteten Kontext anhand von Abfragen überprüfen.

## <a name="see-also"></a>Weitere Informationen

+ [Indexer (Übersicht)](search-indexer-overview.md)
+ [Bedarfsorientiertes Indizieren großer Datasets](search-howto-large-index.md)
+ [Indizieren im Portal](search-import-data-portal.md)
+ [Azure SQL-Datenbank-Indexer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-Indexer](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-Indexer](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-Indexer](search-howto-indexing-azure-tables.md)
+ [Sicherheit in Azure Search](search-security-overview.md)