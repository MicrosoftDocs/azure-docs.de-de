---
title: Indexer (Übersicht)
titleSuffix: Azure Cognitive Search
description: Hier erfahren Sie, wie Sie Azure SQL-Datenbank, SQL Managed Instance, Azure Cosmos DB oder Azure-Speicher per Crawler durchlaufen, um durchsuchbare Daten zu extrahieren und einen Azure Cognitive Search-Index aufzufüllen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: df393f54e7dfa83c8fc0feab3dbf42a156b296b7
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430461"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexer in Azure Cognitive Search

Ein *Indexer* in Azure Cognitive Search ist ein Crawler, der durchsuchbaren Text und Metadaten aus einer externen Azure-Datenquelle extrahiert und einen Suchindex mithilfe von Feld-zu-Feld-Zuordnungen zwischen Quelldaten und Ihrem Index auffüllt. Dieser Ansatz wird auch als „Pullmodell“ bezeichnet, weil der Dienst Daten abruft, ohne dass Sie Code schreiben müssen, der einem Index Daten hinzufügt. Indexer steuern auch die Funktionen der [KI-Anreicherung](cognitive-search-concept-intro.md) von Cognitive Search und integrieren die externe Verarbeitung von Inhalten auf dem Weg zu einem Index.

Indexer gibt es nur in Azure, mit einzelnen Indexern für [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md), [Azure Table Storage](search-howto-indexing-azure-tables.md) und [Blob Storage](search-howto-indexing-azure-blob-storage.md). Beim Konfigurieren eines Indexers geben Sie eine Datenquelle (Ursprung) sowie einen Index (Ziel) an. Bei mehreren Quellen, z. B. Blob Storage, gibt es zusätzliche Konfigurationseigenschaften, die für diesen Inhaltstyp spezifisch sind.

Sie können Indexer bei Bedarf oder nach einem Zeitplan für die regelmäßige Datenaktualisierung ausführen (z. B. alle fünf Minuten). Für häufigere Aktualisierungen ist ein [Pushmodell](search-what-is-data-import.md) erforderlich, bei dem Daten gleichzeitig in Azure Cognitive Search und in Ihrer externen Datenquelle aktualisiert werden.

## <a name="usage-scenarios"></a>Verwendungsszenarios

Sie können einen Indexer entweder als einzige Datenerfassungsmethode oder in einer Kombination aus Techniken verwenden, die Inhalte laden und optional auch transformieren oder anreichern. In der folgenden Tabelle werden die Hauptszenarios zusammengefasst.

| Szenario |Strategie |
|----------|---------|
| Einzelne Datenquelle | Dieses Muster ist das einfachste: eine einzige Datenquelle ist der einzige Inhaltsanbieter für einen Suchindex. In der Quelle identifizieren Sie ein Feld mit eindeutigen Werten, die im Suchindex als Dokumentschlüssel dienen sollen. Der eindeutige Wert wird als Bezeichner verwendet. Alle anderen Quellfelder werden entsprechenden Feldern in einem Index implizit oder explizit zugeordnet. </br></br>Ein Punkt dabei ist, dass der Wert eines Dokumentschlüssels aus den Quelldaten stammt. Ein Suchdienst generiert keine Schlüsselwerte. Bei nachfolgenden Ausführungen werden eingehende Dokumente mit neuen Schlüsseln hinzugefügt, während eingehende Dokumente mit vorhandenen Schlüsseln entweder zusammengeführt oder überschrieben werden. Dies ist davon abhängig, ob Indexfelder NULL sind oder ausgefüllt werden. |
| Mehrere Datenquellen | Ein Index kann Inhalte aus mehreren Quellen akzeptieren, wobei jede Ausführung neuen Inhalt aus einer anderen Quelle bringt. </br></br>Ein Ergebnis könnte ein Index sein, der Dokumente nach jeder Indexerausführung erstellt, wobei ganze Dokumente vollständig aus jeder Quelle erstellt werden. So stammen beispielsweise die Dokumente 1–100 aus Blob Storage, die Dokumente 101–200 stammen aus Azure SQL usw. Die Herausforderung bei diesem Szenario ist das Entwerfen eines Indexschemas, das für alle eingehenden Daten funktioniert, und eine Dokumentschlüsselstruktur, die im Suchindex einheitlich ist. Systemintern sind die Werte, die ein Dokument eindeutig identifizieren, „metadata_storage_path“ in einem Blobcontainer und ein Primärschlüssel in einer SQL-Tabelle. Sie können sich vorstellen, dass eine oder beide Quellen so geändert werden müssen, dass Schlüsselwerte – unabhängig vom Inhaltsursprung – in einem gemeinsamen Format bereitgestellt werden. Bei diesem Szenario sollten Sie davon ausgehen, dass Sie ein gewisses Maß an Vorverarbeitung zum Homogenisieren der Daten durchführen müssen, damit sie in einen einzelnen Index gepullt werden können. </br></br>Eine Alternative könnten Suchdokumente sein, die bei der ersten Ausführung teilweise und dann durch nachfolgende Ausführungen weiter aufgefüllt werden, um Werte aus anderen Quellen einzubeziehen. Beispielsweise stammen die Felder 1–10 aus Blob Storage, die Felder 11–20 aus Azure SQL usw. Die Herausforderung bei diesem Muster besteht darin sicherzustellen, dass das Ziel jedes Indizierungslaufs dasselbe Dokument ist. Zum Zusammenführen von Feldern in ein vorhandenes Dokument muss der Dokumentschlüssel übereinstimmen. Eine Demonstration dieses Szenarios finden Sie im [Tutorial: Indizieren von mehreren Datenquellen mithilfe des .NET SDK](tutorial-multiple-data-sources.md) |
| Mehrere Indexer | Bei Verwendung mehrerer Datenquellen werden unter Umständen auch mehrere Indexer benötigt, um Laufzeitparameter, den Zeitplan oder Feldzuordnungen anpassen zu können. Mehrere Sätze aus Indexern und Datenquellen können zwar auf den gleichen Index ausgerichtet sein, dabei besteht jedoch die Gefahr, dass bereits vorhandene Werte im Index durch Indexerausführungen überschrieben werden. Wenn ein zweiter Satz aus Indexer und Datenquelle die gleichen Dokumente und Felder verwendet, werden sämtliche Werte der ersten Ausführung überschrieben. Feldwerte werden vollständig ersetzt. Ein Indexer kann keine Werte aus mehreren Ausführungen im gleichen Feld zusammenführen.</br></br>Ein weiterer Anwendungsfall für mehrere Indexer ist das [regionsübergreifende Aufskalieren von Cognitive Search](search-performance-optimization.md#use-indexers-for-updating-content-on-multiple-services). Es kann sein, dass sich mehrere Kopien des gleichen Suchindex in unterschiedlichen Regionen befinden. Zum Synchronisieren des Suchindexinhalts können mehrere Indexer vorhanden sein, die Daten aus der gleichen Datenquelle pullen und jeweils einen anderen Suchindex als Ziel verwenden.</br></br>Für die [parallele Indizierung](search-howto-large-index.md#parallel-indexing) besonders großer Datasets ist ebenfalls eine Strategie mit mehreren Indexern erforderlich. Jeder Indexer ist auf eine Teilmenge der Daten ausgerichtet. |
| Inhaltstransformation | Cognitive Search unterstützt optionale Verhaltensweisen der [KI-Anreicherung](cognitive-search-concept-intro.md), die Bildanalyse und Verarbeitung natürlicher Sprache zum Erstellen neuer durchsuchbarer Inhalte und Struktur hinzufügen. KI-Anreicherung ist über ein angefügtes [Skillset](cognitive-search-working-with-skillsets.md) indexergesteuert. Für die KI-Anreicherung benötigt der Indexer noch einen Index und eine Azure-Datenquelle, aber in diesem Szenario wird der Indexerausführung eine Skillsetverarbeitung hinzugefügt. |

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Unterstützte Datenquellen

Indexer durchforsten Datenspeicher in Azure.

+ [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (in der Vorschau)
+ [Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL-Datenbank](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [SQL Managed Instance](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [SQL Server auf virtuellen Azure-Computern](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

Indexerverbindungen mit Remotedatenquellen können über standardmäßige Internetverbindungen (öffentlich) oder über verschlüsselte privaten Verbindungen hergestellt werden, wenn Sie virtuelle Azure-Netzwerke für Client-Apps verwenden. Sie können auch Verbindungen mit Authentifizierung über eine vertrauenswürdigen Dienstidentität einrichten. Weitere Informationen zu sicheren Verbindungen finden Sie unter [Gewähren des Zugriffs über private Endpunkte](search-indexer-securing-resources.md#granting-access-via-private-endpoints) sowie unter [Einrichten einer Indexerverbindung mit einer Datenquelle mithilfe einer verwalteten Identität](search-howto-managed-identities-data-sources.md).

## <a name="stages-of-indexing"></a>Phasen der Indizierung

Bei einer anfänglichen Ausführung, bei der der Index leer ist, liest ein Indexer alle Daten, die in der Tabelle oder im Container bereitgestellt werden. Bei nachfolgenden Ausführungen kann der Indexer in der Regel nur die geänderten Daten erkennen und abrufen. Bei Blobdaten erfolgt die Änderungserkennung automatisch. Bei anderen Datenquellen wie Azure SQL oder Cosmos DB muss die Änderungserkennung aktiviert werden.

Für jedes empfangene Dokument implementiert oder koordiniert der Indexer mehrere Schritte – vom Abrufen des Dokuments bis hin zur Übergabe an eine Suchmaschine für die Indizierung. Optional ist ein Indexer auch bei der Initiierung von Skillset-Ausführungen und -Ausgaben dienlich, vorausgesetzt, dass ein Skillset definiert ist.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="Indexerphasen" border="false":::

### <a name="stage-1-document-cracking"></a>Phase 1: Dokumententschlüsselung

Dokumententschlüsselung ist der Vorgang des Öffnens von Dateien und des Extrahierens von Inhalt. Abhängig vom Typ der Datenquelle versucht der Indexer, verschiedene Vorgänge auszuführen, um potenziell indizierbaren Inhalt zu extrahieren.  

Beispiele:  

+ Wenn es sich bei dem Dokument um einen Datensatz in einer [Azure SQL-Datenquelle](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) handelt, extrahiert der Indexer alle Felder für den Datensatz.
+ Wenn das Dokument eine PDF-Datei in einer [Azure Blob Storage-Datenquelle](search-howto-indexing-azure-blob-storage.md) ist, extrahiert der Indexer Text, Bilder und Metadaten.
+ Wenn das Dokument ein Datensatz in einer [Cosmos DB-Datenquelle](search-howto-index-cosmosdb.md) ist, extrahiert der Indexer die Felder und Unterfelder aus dem Cosmos DB-Dokument.

### <a name="stage-2-field-mappings"></a>Phase 2: Feldzuordnungen 

Ein Indexer extrahiert Text aus einem Quellfeld und sendet ihn an ein Zielfeld in einem Index oder Wissensspeicher. Wenn Feldnamen und -typen übereinstimmen, ist der Pfad klar. Möglicherweise möchten Sie aber unterschiedliche Namen oder Typen in der Ausgabe, in welchem Fall Sie dem Indexer mitteilen müssen, wie das Feld zugeordnet werden soll. 

Dieser Schritt erfolgt nach der Dokumententschlüsselung, aber vor Transformationen, wenn der Indexer aus den Quelldokumenten liest. Wenn Sie eine [Feldzuordnung](search-indexer-field-mappings.md) definieren, wird der Wert des Quellfelds unverändert an das Zielfeld gesendet. 

### <a name="stage-3-skillset-execution"></a>Phase 3: Skillset-Ausführung

Skillset-Ausführung ist ein optionaler Schritt, der die integrierte oder benutzerdefinierte KI-Verarbeitung aufruft. Sie wird ggf. für die optische Zeichenerkennung (Optical Character Recognition, OCR) in Form einer Bildanalyse benötigt, wenn es sich bei den Quelldaten um ein binäres Bild handelt, oder für eine Sprachübersetzung, wenn der Inhalt in verschiedenen Sprachen vorliegt. 

Wie auch immer die Transformation aussieht, die Skillset-Ausführung ist der Zeitpunkt, an dem eine Anreicherung erfolgt. Wenn ein Indexer eine Pipeline ist, können Sie sich einen [Skillset](cognitive-search-defining-skillset.md) als „Pipeline innerhalb der Pipeline“ vorstellen.

### <a name="stage-4-output-field-mappings"></a>Phase 4: Ausgabefeldzuordnungen

Wenn Sie ein Skillset einbeziehen, müssen Sie höchstwahrscheinlich auch Ausgabefeldzuordnungen einbeziehen. Die Ausgabe eines Skillsets ist eigentlich eine Struktur von Informationen, die als *angereichertes Dokument* bezeichnet wird. Mithilfe von Ausgabefeldzuordnungen können Sie auswählen, welche Teile dieser Struktur Feldern in Ihrem Index zugeordnet werden sollen. Erfahren Sie, wie Sie [Ausgabefeldzuordnungen definieren](cognitive-search-output-field-mapping.md).

Während Feldzuordnungen Literalwerte aus der Datenquelle Zielfeldern zuordnen, informieren Ausgabefeldzuordnungen den Indexer, wie die transformierten Werte im angereicherten Dokument den Zielfeldern im Index zugeordnet werden sollen. Anders als bei Feldzuordnungen, die als optional angesehen werden, müssen Sie immer eine Ausgabefeldzuordnung für jeglichen transformierten Inhalt definieren, der sich in einem Index befinden muss.

Die nächste Abbildung zeigt eine Beispieldarstellung einer Indexer-[Debugsitzung](cognitive-search-debug-session.md)der Indexerphasen: Dokumententschlüsselung, Feldzuordnungen, Skillset-Ausführung und Ausgabefeldzuordnungen.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="Beispieldebugsitzung" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-workflow"></a>Grundlegender Workflow

Indexer können Features bereitstellen, die für die Datenquelle eindeutig sind. In dieser Hinsicht variieren einige Aspekte von Indexern oder der Datenquellenkonfiguration nach Indexertyp. Für alle Indexer werden aber die gleiche grundlegende Zusammenstellung und die gleichen Anforderungen verwendet. Die Schritte, die für alle Indexer gelten, sind unten beschrieben.

### <a name="step-1-create-a-data-source"></a>Schritt 1: Erstellen einer Datenquelle

Indexer benötigen ein *Datenquellenobjekt* mit einer Verbindungszeichenfolge und ggf. Anmeldeinformationen. Rufen Sie [Azure Cognitive Search-REST-API zum Erstellen einer Datenquelle](/rest/api/searchservice/create-data-source) oder die Klasse [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) auf, um die Ressource zu erstellen.

Datenquellen werden unabhängig von den Indexern, die darauf zugreifen, konfiguriert und verwaltet. Dies bedeutet, dass eine Datenquelle von mehreren Indexern verwendet werden kann, um mehr als einen Index gleichzeitig zu laden.

### <a name="step-2-create-an-index"></a>Schritt 2: Erstellen eines Index

Mit einem Indexer werden einige Aufgaben in Bezug auf die Datenerfassung automatisiert, aber das Erstellen eines Index gehört im Allgemeinen nicht dazu. Als Voraussetzung hierfür müssen Sie über einen vordefinierten Index mit Feldern verfügen, die den Feldern in Ihrer externen Datenquelle entsprechen. Felder müssen nach Name und Datentyp übereinstimmen. Andernfalls können Sie [Feldzuordnungen definieren](search-indexer-field-mappings.md), um die Zuordnung einzurichten. Weitere Informationen zum Strukturieren eines Index finden Sie unter [Index erstellen (Azure Cognitive Search-REST-API)](/rest/api/searchservice/Create-Index) sowie unter [Klasse „SearchIndex“](/dotnet/api/azure.search.documents.indexes.models.searchindex).

> [!Tip]
> Indexer können zwar keinen Index für Sie erstellen, diese Aufgabe kann jedoch mit dem Assistenten **Daten importieren** im Portal ausgeführt werden. In den meisten Fällen kann der Assistent ein Indexschema aus vorhandenen Metadaten in der Quelle ableiten und ein vorläufiges Indexschema bereitstellen, das Sie inline bearbeiten können, während der Assistent aktiv ist. Sobald der Index für den Dienst erstellt wurde, ist die weitere Bearbeitung im Portal hauptsächlich auf das Hinzufügen neuer Felder beschränkt. Verwenden Sie den Assistenten zum Erstellen, aber nicht zum Überarbeiten eines Index. Praxisnahe Lerninhalte finden Sie in der [exemplarischen Vorgehensweise zum Portal](search-get-started-portal.md).

### <a name="step-3-create-and-run-or-schedule-the-indexer"></a>Schritt 3: Erstellen und Ausführen (oder Planen) des Indexers

Ein Indexer wird direkt nach der [Erstellung](/rest/api/searchservice/Create-Indexer) für den Suchdienst ausgeführt. Ob der Zugriff auf die Datenquelle möglich oder das Skillset gültig ist, erfahren Sie erst, wenn Sie den Indexer erstellen oder ausführen. Nach der ersten Ausführung können Sie ihn bei Bedarf mithilfe von [Indexer ausführen](/rest/api/searchservice/run-indexer) erneut ausführen oder aber [eine Zeitplanserie definieren](search-howto-schedule-indexers.md). 

Sie können den Indexerstatus im Portal oder über die [API zum Abrufen des Indexer-Status](/rest/api/searchservice/get-indexer-status) überwachen. Sie sollten auch [Abfragen für den Index](search-query-create.md) ausführen, um zu überprüfen, ob das Ergebnis Ihren Erwartungen entspricht.

## <a name="next-steps"></a>Nächste Schritte

Nach dieser Einführung können Sie sich als Nächstes mit Indexereigenschaften und -parametern sowie mit der Planung und Überwachung von Indexern vertraut machen. Alternativ können Sie zur Liste mit den [unterstützten Datenquellen](#supported-data-sources) zurückkehren und sich ausführlicher über eine bestimmte Quelle informieren.

+ [Erstellen von Indexern in Azure Cognitive Search](search-howto-create-indexers.md)
+ [Zeitplanindexer](search-howto-schedule-indexers.md)
+ [Definieren von Feldzuordnungen](search-indexer-field-mappings.md)
+ [Überwachen des Status und der Ergebnisse von Indexern der kognitiven Azure-Suche](search-howto-monitor-indexers.md)