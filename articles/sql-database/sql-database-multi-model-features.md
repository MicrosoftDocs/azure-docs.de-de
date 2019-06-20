---
title: Azure SQL-Datenbank-Funktionen für mehrere Modelle | Microsoft-Dokumentation
description: Azure SQL-Datenbank ermöglicht Ihnen das Arbeiten mit mehreren Datenmodellen in der gleichen Datenbank.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: 84efdb0297a2dc69497baee5fb746fb51d02b1b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939517"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Funktionen für mehrere Modelle in Azure SQL-Datenbank

Datenbanken mit mehreren Modellen ermöglichen Ihnen das Speichern von und Arbeiten mit Daten, die in verschiedenen Datenformaten vorliegen, wie z.B. relationale Daten, Graphen, JSON/XML-Dokumente Schlüssel-Wert-Paare usw.

## <a name="when-to-use-multi-model-capabilities"></a>Wann werden Funktionen für mehrere Modelle verwendet?

Azure SQL-Datenbank wurde für die Arbeit mit dem relationalen Modell konzipiert, das in den meisten Fällen die beste Leistung für eine Vielzahl von allgemeinen Anwendungen bietet. Azure SQL-Datenbank ist jedoch nicht auf relationale Daten beschränkt. Mit Azure SQL-Datenbank können Sie eine Vielzahl nicht relationaler Formate verwenden, die eng in das relationale Modell integriert sind.
Die Verwendung der Funktionen für mehrere Modelle von Azure SQL-Datenbank sollten Sie in folgenden Fällen in Betracht ziehen:
- Sie verfügen über einige Informationen oder Strukturen, die für NoSQL-Modelle besser geeignet sind, und möchten keine separate NoSQL-Datenbank verwenden.
- Ein Großteil Ihrer Daten eignet sich zwar für das relationale Modell, Sie müssen aber einige Daten im NoSQL-Format modellieren.
- Sie möchten die umfangreiche Transact-SQL-Sprache zum Abfragen und Analysieren von relationalen und NoSQL-Daten nutzen und sie in eine Vielzahl von Tools und Anwendung integrieren, die die SQL-Sprache verwenden können.
- Sie möchten Datenbankfunktionen wie [In-Memory-Technologien](sql-database-in-memory.md) anwenden, um die Leistung von Analyse oder Verarbeitung Ihrer NoSQL-Datenstrukturen zu verbessern. Sie möchten vielleicht auch die [Transaktionsreplikation](sql-database-managed-instance-transactional-replication.md) oder [lesbare Replikate](sql-database-read-scale-out.md) verwenden, um eine Kopie Ihrer Daten an einem anderen Ort zu erstellen und die primäre Datenbank von einigen Analyseworkloads zu entlasten.

## <a name="overview"></a>Übersicht

Azure SQL bietet die folgenden Funktionen für mehrere Modelle:
- [Graphfunktionen](#graph-features) ermöglichen es Ihnen, Ihre Daten als Satz aus Knoten und Kanten darzustellen und die standardmäßige, um den `MATCH`-Operator erweiterte Transact-SQL-Sprache zu verwenden, um die Graphdaten abzufragen.
- [JSON-Funktionen](#json-features) ermöglichen es Ihnen, JSON-Dokumente in Tabellen zu platzieren sowie relationale Daten in JSON-Dokumente und umgekehrt zu transformieren. Sie können die um JSON-Funktionen erweiterte Transact-SQL-Standardsprache zum Analysieren von Dokumenten verwenden. Darüber hinaus können Sie nicht geclusterte Indizes, Columnstore-Indizes oder speicheroptimierte Tabellen verwenden, um Ihre Abfragen zu optimieren.
- [Räumliche Funktionen](#spatial-features) ermöglichen es Ihnen, geografische und geometrische Daten zu speichern sowie die Daten mithilfe der räumlichen Indizes zu indizieren und sie mit räumlichen Abfragen abzurufen.
- Mit [XML-Funktionen](#xml-features) können Sie XML-Daten in Ihrer Datenbank speichern und indizieren und native XQuery/XPath-Vorgänge für die Arbeit mit XML-Daten verwenden. Azure SQL-Datenbank verfügt über eine spezielle integrierte XML-Abfrage-Engine, die XML-Daten verarbeitet.
- [Schlüssel-Wert-Paare](#key-value-pairs) werden nicht explizit als spezielle Funktionen unterstützt, da sie nativ als zweispaltige Tabellen modelliert werden können.

  > [!Note]
  > Sie können JSON-Pfadausdrücke, XQuery/XPath-Ausdrücke, räumliche Funktionen und Graphabfrageausdrücke in ein und derselben Transact-SQL-Abfrage verwenden, um auf alle in der Datenbank gespeicherten Daten zuzugreifen. Darüber hinaus können alle Tools und Programmiersprachen, die Transact-SQL-Abfragen ausführen können, auch diese Abfrageschnittstelle verwenden, um auf Daten in mehreren Modellen zuzugreifen. Dies ist der entscheidende Unterschied zu solchen Datenbanken mit mehreren Modellen – wie z.B. [Azure Cosmos DB](/azure/cosmos-db/) –, die spezielle APIs für die verschiedenen Datenmodelle bereitstellen.

In den folgenden Abschnitten erfahren Sie mehr über die wichtigsten Azure SQL-Datenbank-Funktionen für mehrere Modelle.

## <a name="graph-features"></a>Diagrammfunktionen

Azure SQL-Datenbank bietet Graphdatenbankfunktionen, mit denen Sie n:n-Beziehungen in der Datenbank modellieren können. Ein Graph ist eine Sammlung aus Knoten (oder Scheitelpunkten) und Kanten (oder Beziehungen). Ein Knoten repräsentiert eine Entität (z.B. eine Person oder eine Organisation), und eine Kante repräsentiert eine Beziehung zwischen den beiden Knoten, die durch die Kante verbunden sind (z.B. „Gefällt mir“-Markierungen oder Freunde). Folgende Features machen eine Graphdatenbank einmalig:
- Kanten oder Beziehungen sind Entitäten der ersten Klasse in einer Graphdatenbank, denen Attribute oder Eigenschaften zugeordnet sein können.
- Eine einzelne Kante kann flexibel mehrere Knoten in einer Graphdatenbank verbinden.
- Musterabgleiche und Navigationsabfragen über mehrere Hops lassen sich ganz einfach ausdrücken.
- Transitive Abschlüsse und polymorphe Abfragen lassen sich ebenfalls sehr einfach ausdrücken.

Graphbeziehungen und Graphabfragefunktionen sind in Transact-SQL integriert und profitieren von der Verwendung von SQL Server als grundlegendes Verwaltungssystem für die Datenbank.
Die [Graphverarbeitung](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) ist eine grundlegende Funktion der SQL Server-Datenbank-Engine, weitere Informationen über die Graphverarbeitung finden Sie also in entsprechenden Artikeln zu diesem Thema.

### <a name="when-to-use-a-graph-capability"></a>Verwenden einer Graphfunktion

Eine Graphdatenbank kann keine Ergebnisse erzielen, die nicht auch mit einer relationalen Datenbank erreicht werden können. Eine Graphdatenbank vereinfacht aber den Ausdruck bestimmter Abfragen. Bei der Entscheidung für die eine oder die andere Lösung spielen folgende Faktoren eine Rolle:

- Sie müssen hierarchische Daten modellieren, bei denen ein Knoten über mehrere übergeordnete Elemente verfügen kann, sodass HierarchyId nicht verwendet werden kann.
- Ihre Anwendung weist komplexe n:n-Beziehungen auf, und wenn die Anwendung weiterentwickelt wird, kommen neue Beziehungen hinzu.
- Sie müssen miteinander verbundene Daten und Beziehungen analysieren.

## <a name="json-features"></a>JSON-Funktionen

In Azure SQL-Datenbank können Sie Daten analysieren und abfragen, die im JavaScript Object Notation-Format [(JSON)](https://www.json.org/) dargestellt werden, und Ihre relationalen Daten als JSON-Text exportieren.

JSON ist ein gängiges Datenformat zum Austauschen von Daten in modernen Web- und mobilen Anwendungen. JSON wird auch zum Speichern von halbstrukturierten Daten in Protokolldateien oder in NoSQL-Datenbanken wie [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) verwendet. Viele REST-Webdienste geben Ergebnisse als JSON-Text formatiert zurück oder akzeptieren Daten im JSON-Format. Die meisten Azure-Dienste, z.B. [Azure Search](https://azure.microsoft.com/services/search/), [Azure Storage](https://azure.microsoft.com/services/storage/)und [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) verfügen über REST-Endpunkte, die Daten im JSON-Format zurückgeben oder verarbeiten.

Mit Azure SQL-Datenbank können Sie problemlos mit JSON-Daten arbeiten und Ihre Datenbank in moderne Dienste integrieren. Azure SQL-Datenbank stellt die folgenden Funktionen für das Arbeiten mit JSON-Daten bereit:

![JSON-Funktionen](./media/sql-database-json-features/image_1.png)

Mithilfe der integrierten Funktionen [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx) und [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx) können Sie Daten aus einem JSON-Text extrahieren oder überprüfen, ob er ordnungsgemäß formatiert ist. Mit der Funktion [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) können Sie einen Wert im JSON-Text aktualisieren. Bei komplexeren Abfragen und Analysen kann die Funktion [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) ein Array von JSON-Objekten in eine Reihe von Zeilen umwandeln. Für das zurückgegebene Resultset kann eine beliebige SQL-Abfrage ausgeführt werden. Schließlich können Sie mit einer [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) -Klausel in den relationalen Tabellen gespeicherte Daten als JSON-Text formatieren.

Weitere Informationen finden Sie unter [Erste Schritte mit JSON-Features in der Azure SQL-Datenbank](sql-database-json-features.md).
[JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) ist eine grundlegende Funktion der SQL Server-Datenbank-Engine, weitere Informationen darüber finden Sie also in entsprechenden Artikeln zu diesem Thema.

### <a name="when-to-use-a-json-capability"></a>Verwenden einer JSON-Funktion

In bestimmten Szenarien können Dokumentmodelle statt relationaler Modelle verwendet werden:
- Ein hohes Maß an Normalisierung des Schemas bringt keine nennenswerten Vorteile, da Sie auf alle Felder der Objekte gleichzeitig zugreifen oder normalisierte Teile der Objekte nie aktualisieren. Das normalisierte Modell erhöht jedoch aufgrund der großen Anzahl von Tabellen, die Sie zum Abrufen der Daten verknüpfen müssen, die Komplexität Ihrer Abfragen.
- Sie arbeiten mit Anwendungen, die nativ JSON-Dokumente als Kommunikations- oder Datenmodell verwenden, und Sie möchten keine zusätzlichen Ebenen einführen, um relationale Daten in JSON und umgekehrt zu transformieren.
- Sie müssen Ihr Datenmodell vereinfachen, indem Sie untergeordnete Tabellen oder Entität-Objekt-Wert-Muster denormalisieren.
- Sie müssen im JSON-Format gespeicherte Daten laden oder exportieren, ohne ein weiteres Tool zum Analysieren der Daten zu verwenden.

## <a name="spatial-features"></a>Räumliche Funktionen

Räumliche Daten repräsentieren Daten zur physischen Position und Form geometrischer Objekte. Diese Objekte können Punktpositionen oder komplexere Objekte wie Länder/Regionen, Straßen oder Seen sein.

Azure SQL-Datenbank unterstützt zwei Arten von räumlichen Daten: geometrische und geografische.
- Der geometrische Datentyp stellt Daten in einem euklidischen (flachen) Koordinatensystem dar.
- Der geografische Datentyp stellt Daten in einem Erdkugel-Koordinatensystem dar.

Es gibt eine Reihe von räumlichen Objekten, die in Azure SQL-Datenbanken verwendet werden können, z. B. [Point](https://docs.microsoft.com/sql/relational-databases/spatial/point), [LineString](https://docs.microsoft.com/sql/relational-databases/spatial/linestring), [Polygon](https://docs.microsoft.com/sql/relational-databases/spatial/polygon) usw.

Azure SQL-Datenbank stellt auch spezielle [räumliche Indizes](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview) bereit, mit denen Sie die Leistung Ihrer räumlichen Abfragen verbessern können.

Die [Unterstützung räumlicher Funktionen](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) ist eine grundlegende Funktion der SQL Server-Datenbank-Engine, weitere Informationen darüber finden Sie also in entsprechenden Artikeln zu diesem Thema.

## <a name="xml-features"></a>XML-Funktionen

SQL Server stellt eine leistungsstarke Plattform zur Entwicklung umfassender Anwendungen für die Verwaltung halbstrukturierter Daten bereit. Die Unterstützung für XML ist in alle Komponenten von SQL Server integriert und umfasst Folgendes:

- Den XML-Datentyp. XML-Werte können nativ in einer XML-Datentypspalte gespeichert werden, die untypisiert gelassen oder gemäß einer Sammlung von XML-Schemas typisiert werden kann. Sie können die XML-Spalte indizieren.
- Die Möglichkeit, eine XQuery-Abfrage für in Spalten gespeicherte XML-Daten und Variablen des XML-Typs anzugeben. XQuery-Funktionen können in jeder Transact-SQL-Abfrage verwendet werden, die auf das in Ihrer Datenbank verwendete Datenmodell zugreift.
- Automatische Indizierung aller Elemente in XML-Dokumenten mit dem [primären XML-Index](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) oder Angeben der genauen Pfade für die Indizierung mit dem [sekundären XML-Index](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET für das Massenladen von XML-Daten.
- Transformation relationaler Daten in das XML-Format.

[XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) ist eine grundlegende Funktion der SQL Server-Datenbank-Engine, weitere Informationen darüber finden Sie also in entsprechenden Artikeln zu diesem Thema.

### <a name="when-to-use-an-xml-capability"></a>Verwenden einer XML-Funktion

In bestimmten Szenarien können Dokumentmodelle statt relationaler Modelle verwendet werden:
- Ein hohes Maß an Normalisierung des Schemas bringt keine nennenswerten Vorteile, da Sie auf alle Felder der Objekte gleichzeitig zugreifen oder normalisierte Teile der Objekte nie aktualisieren. Das normalisierte Modell erhöht jedoch aufgrund der großen Anzahl von Tabellen, die Sie zum Abrufen der Daten verknüpfen müssen, die Komplexität Ihrer Abfragen.
- Sie arbeiten mit Anwendungen, die nativ XML-Dokumente als Kommunikations- oder Datenmodell verwenden, und Sie möchten keine zusätzlichen Ebenen einführen, um relationale Daten in XML und umgekehrt zu transformieren.
- Sie müssen Ihr Datenmodell vereinfachen, indem Sie untergeordnete Tabellen oder Entität-Objekt-Wert-Muster denormalisieren.
- Sie müssen im XML-Format gespeicherte Daten laden oder exportieren, ohne ein weiteres Tool zum Analysieren der Daten zu verwenden.

## <a name="key-value-pairs"></a>Schlüssel-Wert-Paare

Azure SQL-Datenbank besitzt keine speziellen Typen oder Strukturen, die Schlüssel-Wert-Paar unterstützen, da Schlüssel-Wert-Strukturen nativ als standardmäßige relationale Tabellen dargestellt werden können:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Sie können diese Schlüssel-Wert-Struktur ohne jede Einschränkung an Ihre Anforderungen anpassen. Ein Beispiel: Ein Wert kann ein XML-Dokument anstelle des `nvarchar(max)`-Typs sein. Wenn der Wert ein JSON-Dokument ist, können Sie eine `CHECK`-Einschränkung einfügen, die die Gültigkeit des JSON-Inhalts überprüft. Sie können eine beliebige Anzahl von Werten für einen einzigen Schlüssel in den zusätzlichen Spalten hinzufügen, berechnete Spalten und Indizes hinzufügen, um den Datenzugriff zu vereinfachen und zu optimieren, die Tabelle als speicheroptimierte reine Schematabelle definieren, um die Leistung zu verbessern, und vieles mehr.

Ein Praxisbeispiel für die effektive Nutzung des relationalen Modells als Schlüssel-Wert-Paar-Lösung finden Sie im Blogbeitrag [How bwin is using In-Memory OLTP to achieve unprecedented performance and scale](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) (Wie bwin In-Memory-OLTP für beispiellose Leistung und Skalierung nutzt). In diesem Beitrag wird die ASP.NET-Cachinglösung erläutert, mit der das Unternehmen 1.200.000 Batches pro Sekunde verarbeitet.

## <a name="next-steps"></a>Nächste Schritte
Azure SQL-Datenbank-Funktionen für mehrere Modelle sind ebenfalls grundlegende Features der SQL Server-Datenbank-Engine, die von Azure SQL-Datenbank und SQL Server gemeinsam genutzt werden. Weitere Informationen zu diesen Features finden Sie auf den Dokumentationsseiten zu relationalen SQL-Datenbanken:

* [Graph-Verarbeitung](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [JSON-Daten](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [Unterstützung räumlicher Funktionen](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [XML-Daten](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
