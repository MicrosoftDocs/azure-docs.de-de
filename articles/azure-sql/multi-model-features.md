---
title: Funktionen für mehrere Modelle
description: Mit Microsoft Azure SQL können Sie mit mehreren Datenmodellen in derselben Datenbank arbeiten.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: d5a067272caf9b072117ba57b7b16f8d78a8b456
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791646"
---
# <a name="multi-model-capabilities-of-azure-sql-database--sql-managed-instance"></a>Funktionen für mehrere Modelle in Azure SQL-Datenbank und SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Datenbanken mit mehreren Modellen ermöglichen Ihnen das Speichern von und Arbeiten mit Daten, die in verschiedenen Datenformaten vorliegen, z. B. relationale Daten, Graphen, JSON/XML-Dokumente Schlüssel-Wert-Paare usw.

## <a name="when-to-use-multi-model-capabilities"></a>Wann werden Funktionen für mehrere Modelle verwendet?

Die [Azure SQL-Produktfamilie](azure-sql-iaas-vs-paas-what-is-overview.md) wurde dazu konzipiert, mit dem relationalen Modell zu funktionieren, das in den meisten Fällen für eine Vielzahl verschiedener allgemeinen Anwendungen die beste Leistung erzielt. Die Azure SQL-Produktfamilie ist jedoch nicht auf relationale Daten beschränkt. Mit Azure SQL-Produkten können Sie auch eine Vielzahl nicht relationaler Formate verwenden, die tiefgreifend mit dem relationalen Modell integriert sind.
Sie sollten die Funktionen für mehrere Modelle der Azure SQL-Produktfamilie in den folgenden Situationen in Erwägung ziehen:

- Sie verfügen über einige Informationen oder Strukturen, die für NoSQL-Modelle besser geeignet sind, und möchten keine separate NoSQL-Datenbank verwenden.
- Ein Großteil Ihrer Daten eignet sich zwar für das relationale Modell, Sie müssen aber einige Daten im NoSQL-Format modellieren.
- Sie möchten die umfangreiche Transact-SQL-Sprache zum Abfragen und Analysieren von relationalen und NoSQL-Daten nutzen und sie in eine Vielzahl von Tools und Anwendung integrieren, die die SQL-Sprache verwenden können.
- Sie möchten Datenbankfunktionen wie [In-Memory-Technologien](in-memory-oltp-overview.md) anwenden, um die Leistung von Analyse oder Verarbeitung Ihrer NoSQL-Datenstrukturen zu verbessern. Sie möchten vielleicht auch die [Transaktionsreplikation](managed-instance/replication-transactional-overview.md) oder [lesbare Replikate](database/read-scale-out.md) verwenden, um eine Kopie Ihrer Daten an einem anderen Ort zu erstellen und die primäre Datenbank von einigen Analyseworkloads zu entlasten.

## <a name="overview"></a>Übersicht

Die Azure SQL-Produktfamilie stellt die folgenden Features für mehrere Modelle bereit:

- [Graphfunktionen](#graph-features) ermöglichen es Ihnen, Ihre Daten als Satz aus Knoten und Kanten darzustellen und die standardmäßige, um den `MATCH`-Operator erweiterte Transact-SQL-Sprache zu verwenden, um die Graphdaten abzufragen.
- [JSON-Funktionen](#json-features) ermöglichen es Ihnen, JSON-Dokumente in Tabellen zu platzieren sowie relationale Daten in JSON-Dokumente und umgekehrt zu transformieren. Sie können die um JSON-Funktionen erweiterte Transact-SQL-Standardsprache zum Analysieren von Dokumenten verwenden. Darüber hinaus können Sie nicht geclusterte Indizes, Columnstore-Indizes oder speicheroptimierte Tabellen verwenden, um Ihre Abfragen zu optimieren.
- [Räumliche Funktionen](#spatial-features) ermöglichen es Ihnen, geografische und geometrische Daten zu speichern sowie die Daten mithilfe der räumlichen Indizes zu indizieren und sie mit räumlichen Abfragen abzurufen.
- Mit [XML-Funktionen](#xml-features) können Sie XML-Daten in Ihrer Datenbank speichern und indizieren und native XQuery/XPath-Vorgänge für die Arbeit mit XML-Daten verwenden. Azure SQL-Produkte umfassen eine spezialisierte, integrierte XML-Abfrage-Engine, die XML-Daten verarbeitet.
- [Schlüssel-Wert-Paare](#key-value-pairs) werden nicht explizit als spezielle Funktionen unterstützt, da sie nativ als zweispaltige Tabellen modelliert werden können.

  > [!Note]
  > Sie können JSON-Pfadausdrücke, XQuery/XPath-Ausdrücke, räumliche Funktionen und Graphabfrageausdrücke in ein und derselben Transact-SQL-Abfrage verwenden, um auf alle in der Datenbank gespeicherten Daten zuzugreifen. Darüber hinaus können alle Tools und Programmiersprachen, die Transact-SQL-Abfragen ausführen können, auch diese Abfrageschnittstelle verwenden, um auf Daten in mehreren Modellen zuzugreifen. Dies ist der entscheidende Unterschied zu solchen Datenbanken mit mehreren Modellen – wie z.B. [Azure Cosmos DB](../cosmos-db/index.yml) –, die spezielle APIs für die verschiedenen Datenmodelle bereitstellen.

In den folgenden Abschnitten erfahren Sie mehr über die wichtigsten Funktionen für mehrere Modelle, die in Azure SQL-Produkten enthalten sind.

## <a name="graph-features"></a>Diagrammfunktionen

Azure SQL-Produktfamilie bietet Graphdatenbankfunktionen, mit denen Sie m:n-Beziehungen in Datenbanken modellieren können. Ein Graph ist eine Sammlung aus Knoten (oder Scheitelpunkten) und Kanten (oder Beziehungen). Ein Knoten repräsentiert eine Entität (z.B. eine Person oder eine Organisation), und eine Kante repräsentiert eine Beziehung zwischen den beiden Knoten, die durch die Kante verbunden sind (z.B. „Gefällt mir“-Markierungen oder Freunde). Folgende Features machen eine Graphdatenbank einmalig:

- Kanten oder Beziehungen sind Entitäten der ersten Klasse in einer Graphdatenbank, denen Attribute oder Eigenschaften zugeordnet sein können.
- Eine einzelne Kante kann flexibel mehrere Knoten in einer Graphdatenbank verbinden.
- Musterabgleiche und Navigationsabfragen über mehrere Hops lassen sich ganz einfach ausdrücken.
- Transitive Abschlüsse und polymorphe Abfragen lassen sich ebenfalls sehr einfach ausdrücken.

Die [Graphbeziehungen und Graphabfragefunktionen](/sql/relational-databases/graphs/sql-graph-overview) sind in Transact-SQL integriert und profitieren von den Vorteilen der SQL Server-Datenbank-Engine als grundlegendes Datenbankverwaltungssystem.

### <a name="when-to-use-a-graph-capability"></a>Verwenden einer Graphfunktion

Eine Graphdatenbank kann keine Ergebnisse erzielen, die nicht auch mit einer relationalen Datenbank erreicht werden können. Eine Graphdatenbank vereinfacht aber den Ausdruck bestimmter Abfragen. Bei der Entscheidung für die eine oder die andere Lösung spielen folgende Faktoren eine Rolle:

- Sie müssen hierarchische Daten modellieren, bei denen ein Knoten über mehrere übergeordnete Elemente verfügen kann, sodass HierarchyId nicht verwendet werden kann.
- Ihre Anwendung weist komplexe n:n-Beziehungen auf, und wenn die Anwendung weiterentwickelt wird, kommen neue Beziehungen hinzu.
- Sie müssen miteinander verbundene Daten und Beziehungen analysieren.

## <a name="json-features"></a>JSON-Funktionen

Mit Azure SQL-Produkten können Sie Daten analysieren und abfragen, die im [JSON](https://www.json.org/)-Format (JavaScript Object Notation) dargestellt werden, und Ihre relationalen Daten als JSON-Text exportieren.

JSON ist ein gängiges Datenformat zum Austauschen von Daten in modernen Web- und mobilen Anwendungen. JSON wird auch zum Speichern von halbstrukturierten Daten in Protokolldateien oder in NoSQL-Datenbanken wie [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) verwendet. Viele REST-Webdienste geben Ergebnisse als JSON-Text formatiert zurück oder akzeptieren Daten, die im JSON-Format formatiert sind. Die meisten Azure-Dienste, z.B. [Azure Cognitive Search](https://azure.microsoft.com/services/search/), [Azure Storage](https://azure.microsoft.com/services/storage/)und [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) verfügen über REST-Endpunkte, die Daten im JSON-Format zurückgeben oder verarbeiten.


Mit den Produkten der Azure SQL-Familie können Sie problemlos mit JSON-Daten arbeiten und Ihre Datenbank mit modernen Diensten integrieren. Für die Arbeit mit JSON-Daten werden Ihnen die folgenden Funktionen zur Verfügung gestellt:

![JSON-Funktionen](./media/multi-model-features/image_1.png)

Mithilfe der integrierten Funktionen [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql), [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql) und [ISJSON](/sql/t-sql/functions/isjson-transact-sql) können Sie Daten aus einem JSON-Text extrahieren oder überprüfen, ob er ordnungsgemäß formatiert ist. Mit der Funktion [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql) können Sie einen Wert im JSON-Text aktualisieren. Bei komplexeren Abfragen und Analysen kann die Funktion [OPENJSON](/sql/t-sql/functions/openjson-transact-sql) ein Array von JSON-Objekten in eine Reihe von Zeilen umwandeln. Für das zurückgegebene Resultset kann eine beliebige SQL-Abfrage ausgeführt werden. Schließlich können Sie mit einer [FOR JSON](/sql/relational-databases/json/format-query-results-as-json-with-for-json-sql-server) -Klausel in den relationalen Tabellen gespeicherte Daten als JSON-Text formatieren.

Weitere Informationen finden Sie unter [Arbeiten mit JSON-Daten](database/json-features.md).
[JSON](/sql/relational-databases/json/json-data-sql-server) ist ein wichtiges Feature der SQL Server-Datenbank-Engine.

### <a name="when-to-use-a-json-capability"></a>Verwenden einer JSON-Funktion

In bestimmten Szenarien können Dokumentmodelle statt relationaler Modelle verwendet werden:

- Ein hohes Maß an Normalisierung des Schemas bringt keine nennenswerten Vorteile, da Sie auf alle Felder der Objekte gleichzeitig zugreifen oder normalisierte Teile der Objekte nie aktualisieren. Das normalisierte Modell erhöht jedoch aufgrund der großen Anzahl von Tabellen, die Sie zum Abrufen der Daten verknüpfen müssen, die Komplexität Ihrer Abfragen.
- Sie arbeiten mit Anwendungen, die nativ JSON-Dokumente als Kommunikations- oder Datenmodell verwenden, und Sie möchten keine zusätzlichen Ebenen einführen, um relationale Daten in JSON und umgekehrt zu transformieren.
- Sie müssen Ihr Datenmodell vereinfachen, indem Sie untergeordnete Tabellen oder Entität-Objekt-Wert-Muster denormalisieren.
- Sie müssen im JSON-Format gespeicherte Daten laden oder exportieren, ohne ein weiteres Tool zum Analysieren der Daten zu verwenden.

## <a name="spatial-features"></a>Räumliche Funktionen

Räumliche Daten repräsentieren Daten zur physischen Position und Form geometrischer Objekte. Diese Objekte können Punktpositionen oder komplexere Objekte wie Länder/Regionen, Straßen oder Seen sein.

 Die folgenden beiden Typen von räumlichen Daten werden unterstützt: 

- Der geometrische Datentyp stellt Daten in einem euklidischen (flachen) Koordinatensystem dar.
- Der geografische Datentyp stellt Daten in einem Erdkugel-Koordinatensystem dar.

Es gibt eine Reihe von räumlichen Objekten, die in Azure SQL-Produkten verwendet werden können, um Daten zu analysieren und abzufragen, die im [JSON](https://www.json.org/)-Format (JavaScript Object Notation) dargestellt werden. Sie können Ihre relationalen Daten dann als JSON-Text exportieren.
Beispiele hierfür sind unter anderem [Point](/sql/relational-databases/spatial/point), [LineString](/sql/relational-databases/spatial/linestring) und [Polygon](/sql/relational-databases/spatial/polygon).

Außerdem stellen die Produkte der Azure SQL-Familie spezialisierte [räumliche Indizes](/sql/relational-databases/spatial/spatial-indexes-overview) bereit, die zur Verbesserung der Leistung Ihrer räumlichen Abfragen verwendet werden können.

Die [Unterstützung räumlicher Funktionen](/sql/relational-databases/spatial/spatial-data-sql-server) ist ein wichtiges Feature der SQL Server-Datenbank-Engine.

## <a name="xml-features"></a>XML-Funktionen

Die SQL Server-Datenbank-Engine stellt eine leistungsstarke Plattform zur Entwicklung umfassender Anwendungen für die Verwaltung halbstrukturierter Daten bereit. Die XML-Unterstützung ist in alle Komponenten der Datenbank-Engine integriert und umfasst Folgendes:

- Den XML-Datentyp. XML-Werte können nativ in einer XML-Datentypspalte gespeichert werden, die untypisiert gelassen oder gemäß einer Sammlung von XML-Schemas typisiert werden kann. Sie können die XML-Spalte indizieren.
- Die Möglichkeit, eine XQuery-Abfrage für in Spalten gespeicherte XML-Daten und Variablen des XML-Typs anzugeben. XQuery-Funktionen können in jeder Transact-SQL-Abfrage verwendet werden, die auf das in Ihrer Datenbank verwendete Datenmodell zugreift.
- Automatische Indizierung aller Elemente in XML-Dokumenten mit dem [primären XML-Index](/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) oder Angeben der genauen Pfade für die Indizierung mit dem [sekundären XML-Index](/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET für das Massenladen von XML-Daten.
- Transformation relationaler Daten in das XML-Format.

[XML](/sql/relational-databases/xml/xml-data-sql-server) ist ein wichtiges Feature der SQL Server-Datenbank-Engine.

### <a name="when-to-use-an-xml-capability"></a>Verwenden einer XML-Funktion

In bestimmten Szenarien können Dokumentmodelle statt relationaler Modelle verwendet werden:

- Ein hohes Maß an Normalisierung des Schemas bringt keine nennenswerten Vorteile, da Sie auf alle Felder der Objekte gleichzeitig zugreifen oder normalisierte Teile der Objekte nie aktualisieren. Das normalisierte Modell erhöht jedoch aufgrund der großen Anzahl von Tabellen, die Sie zum Abrufen der Daten verknüpfen müssen, die Komplexität Ihrer Abfragen.
- Sie arbeiten mit Anwendungen, die nativ XML-Dokumente als Kommunikations- oder Datenmodell verwenden, und Sie möchten keine zusätzlichen Ebenen einführen, um relationale Daten in XML und umgekehrt zu transformieren.
- Sie müssen Ihr Datenmodell vereinfachen, indem Sie untergeordnete Tabellen oder Entität-Objekt-Wert-Muster denormalisieren.
- Sie müssen im XML-Format gespeicherte Daten laden oder exportieren, ohne ein weiteres Tool zum Analysieren der Daten zu verwenden.

## <a name="key-value-pairs"></a>Schlüssel-Wert-Paare

Die Azure SQL-Produktfamilie umfasst keine speziellen Typen oder Strukturen, die Schlüssel-Wert-Paar unterstützen, da Schlüssel-Wert-Strukturen nativ als standardmäßige relationale Tabellen dargestellt werden können:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Sie können diese Schlüssel-Wert-Struktur ohne jede Einschränkung an Ihre Anforderungen anpassen. Ein Beispiel: Ein Wert kann ein XML-Dokument anstelle des `nvarchar(max)`-Typs sein. Wenn der Wert ein JSON-Dokument ist, können Sie eine `CHECK`-Einschränkung einfügen, die die Gültigkeit des JSON-Inhalts überprüft. Sie können eine beliebige Anzahl von Werten für einen einzigen Schlüssel in den zusätzlichen Spalten hinzufügen, berechnete Spalten und Indizes hinzufügen, um den Datenzugriff zu vereinfachen und zu optimieren, die Tabelle als speicheroptimierte reine Schematabelle definieren, um die Leistung zu verbessern, und vieles mehr.

Ein Praxisbeispiel für die effektive Nutzung des relationalen Modells als Schlüssel-Wert-Paar-Lösung finden Sie im Blogbeitrag [How bwin is using In-Memory OLTP to achieve unprecedented performance and scale](/archive/blogs/sqlcat/how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale) (Wie bwin In-Memory-OLTP für beispiellose Leistung und Skalierung nutzt). In diesem Beitrag wird die ASP.NET-Cachinglösung erläutert, mit der das Unternehmen 1.200.000 Batches pro Sekunde verarbeitet.

## <a name="next-steps"></a>Nächste Schritte

Die Funktionen für mehrere Modelle der Azure SQL-Produktfamilie sind ebenfalls grundlegende Features der SQL Server-Datenbank-Engine, die innerhalb der Azure SQL-Produktfamilie geteilt werden. Weitere Informationen zu diesen Features finden Sie auf den Dokumentationsseiten zu relationalen SQL-Datenbanken:

- [Graph-Verarbeitung](/sql/relational-databases/graphs/sql-graph-overview)
- [JSON-Daten](/sql/relational-databases/json/json-data-sql-server)
- [Unterstützung räumlicher Funktionen](/sql/relational-databases/spatial/spatial-data-sql-server)
- [XML-Daten](/sql/relational-databases/xml/xml-data-sql-server)