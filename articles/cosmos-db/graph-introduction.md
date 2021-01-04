---
title: Einführung in die Gremlin-API von Azure Cosmos DB
description: Erfahren Sie, wie Sie anhand der Graph-Abfragesprache Gremlin von Apache TinkerPop umfangreiche Diagramme mit niedrigen Latenzen durch Azure Cosmos DB speichern, abfragen und traversieren können.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 11/25/2020
ms.author: chrande
ms.openlocfilehash: c2165ea6273ad90cfb95242dfe72538c0a5ef64c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350057"
---
# <a name="introduction-to-gremlin-api-in-azure-cosmos-db"></a>Einführung in die Gremlin-API von Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

[Azure Cosmos DB](introduction.md) ist der global verteilte Datenbankdienst von Microsoft mit mehreren Modellen für unternehmenskritische Anwendungen. Diese Datenbank für mehrere Modelle unterstützt Dokument-, Schlüssel-Wert-Paar-, Diagramm- und spaltenbasierte Datenmodelle. Azure Cosmos DB bietet über die Gremlin-API einen Graphendatenbankdienst für einen vollständig verwalteten Datenbankdienst, der für einen beliebigen Maßstab entworfen wurde.  

:::image type="content" source="./media/graph-introduction/cosmosdb-graph-architecture.png" alt-text="Architektur von Azure Cosmos DB-Diagrammen" border="false":::

Dieser Artikel enthält eine Übersicht über die Gremlin-API von Azure Cosmos DB und erläutert, wie diese zum Speichern von umfangreichen Diagrammen mit Milliarden von Vertices und Edges verwendet werden können. Sie können die Graphen mit einer Latenz im Millisekundenbereich abfragen und die Graphstruktur mühelos entwickeln. Die Gremlin-API von Azure Cosmos DB basiert auf [Apache TinkerPop](https://tinkerpop.apache.org), einem Graphcomputingframework. Die Gremlin-API in Azure Cosmos DB verwendet die Gremlin-Abfragesprache.

Die Gremlin-API von Azure Cosmos DB kombiniert die Leistungsfähigkeit von Algorithmen für Graphdatenbanken mit einer hochskalierbaren und verwalteten Infrastruktur und bietet eine einzigartige, flexible Lösung für die meisten gängigen Datenprobleme, die mit mangelnder Flexibilität und relationalen Ansätzen einher gehen.

> [!NOTE]
> Der [serverlose Kapazitätsmodus](serverless.md) ist nun in der Gremlin-API von Azure Cosmos DB verfügbar.

## <a name="features-of-azure-cosmos-dbs-gremlin-api"></a>Features der Gremlin-API von Azure Cosmos DB
 
Azure Cosmos DB ist eine vollständig verwaltete Diagrammdatenbank, die eine globale Verteilung, flexible Skalierung von Speicher und Durchsatz, automatische Indizierung und Abfrage sowie einstellbare Konsistenzebenen bietet und den TinkerPop-Standard unterstützt.

Die Azure Cosmos DB Gremlin-API bietet die folgenden differenzierten Funktionen:

* **Flexibel skalierbarer Durchsatz und Speicher**

  Diagramme müssen in der Praxis über die Kapazität eines einzelnen Servers hinweg skaliert werden. Azure Cosmos DB unterstützt horizontal skalierbare Graphdatenbanken, die eine praktisch unbegrenzte Größe in Bezug auf den Speicher und bereitgestellten Durchsatz haben können. Bei zunehmender Skalierung der Graphdatenbank werden die Daten automatisch mithilfe der [Graphpartitionierung](./graph-partitioning.md) verteilt.

* **Replikation in mehreren Regionen**

  Azure Cosmos DB kann die Graphdaten automatisch in jeder Azure-Region weltweit replizieren. Die globale Replikation vereinfacht die Entwicklung von Anwendungen, die globalen Zugriff auf Daten erfordern. Zusätzlich zur Minimierung der Lese- und Schreiblatenz an jedem Ort der Welt bietet Azure Cosmos DB einen regionalen Failovermechanismus, mit dem die Kontinuität Ihrer Anwendung im seltenen Fall einer Dienstunterbrechung in einer Region sichergestellt werden kann.

* **Schnelle Abfragen und Traversierungen mit dem am weitesten verbreiteten Standard für Graphabfragen**

  Speichern Sie heterogene Vertices und Edges, und führen Sie dafür Abfragen über eine vertraute Gremlin-Syntax durch. Gremlin ist eine imperative, funktionale Abfragesprache, die eine umfangreiche Schnittstelle zur Implementierung gängiger Graphalgorithmen bietet.
  
  Azure Cosmos DB ermöglicht umfassende Echtzeitabfragen und -traversierungen, ohne dass Schemahinweise, sekundäre Indizes oder Ansichten festgelegt werden müssen. Mehr erfahren Sie unter [Abfragegraphen mithilfe von Gremlin](gremlin-support.md).

* **Vollständig verwaltete Graphdatenbank**

  Sie müssen sich nicht mehr mit der Verwaltung von Datenbanken und Rechenressourcen befassen. Die meisten vorhandenen Graphdatenbankplattformen sind an die Grenzen ihrer Infrastruktur gebunden und erfordern häufig einen hohen Wartungsaufwand für den Betrieb. 
  
  Als vollständig verwalteter Dienst entfällt bei Cosmos DB die Notwendigkeit, virtuelle Computer zu verwalten, Runtimesoftware zu aktualisieren, Sharding oder die Replikation zu verwalten oder komplexe Upgrades auf Datenebene durchzuführen. Alle Diagramme werden automatisch gesichert und vor regionalen Ausfällen geschützt. Dadurch können sich Entwickler auf die Bereitstellung von Anwendungen konzentrieren, anstatt Graphdatenbanken zu betreiben und zu verwalten. 

* **Automatische Indizierung**

  Alle Eigenschaften in Knoten (auch als Vertices bezeichnet) und Edges im Graphen werden von Azure Cosmos DB automatisch indiziert, ohne dass ein Schema oder die Erstellung sekundärer Indizes erwartet oder gefordert wird. Weitere Informationen zur Indizierung in Azure Cosmos DB finden Sie [hier](./index-overview.md).

* **Kompatibilität mit Apache TinkerPop**

  Azure Cosmos DB unterstützt den [Open-Source-Standard Apache TinkerPop](https://tinkerpop.apache.org/). Der TinkerPop-Standard verfügt über ein umfangreiches Ökosystem von Anwendungen und Bibliotheken, die sich problemlos in die Gremlin-API von Azure Cosmos DB integrieren lassen.

* **Einstellbare Konsistenzebenen**

  Azure Cosmos DB bietet fünf klar definierte Konsistenzebenen, um für Ihre Anwendung ein ausgewogenes Verhältnis zwischen Konsistenz und Leistung zu erreichen. Für Abfragen und Lesevorgänge bietet Azure Cosmos DB fünf verschiedene Konsistenzebenen – „stark“, „begrenzte Veraltung“, „Sitzung“, „Präfixkonsistenz“ und „letztlich“. Mit diesen granularen, wohldefinierten Konsistenzebenen können fundierte Kompromisse zwischen Konsistenz, Verfügbarkeit und Latenz geschlossen werden. Weitere Informationen finden Sie unter [Einstellbare Datenkonsistenzebenen in Azure Cosmos DB](consistency-levels.md).

## <a name="scenarios-that-use-gremlin-api"></a>Szenarien, in denen die Gremlin-API verwendet wird

Im Folgenden werden einige Szenarien vorgestellt, in denen die Diagrammunterstützung von Azure Cosmos DB hilfreich sein kann:

* **Soziale Netzwerke/Customer 365**

  Durch die Kombination von Daten über Ihre Kunden und deren Interaktionen mit anderen Personen können Sie individuelle Erlebnisse schaffen, das Kundenverhalten vorhersagen oder Personen mit ähnlichen Interessen vernetzen. Azure Cosmos DB kann zum Verwalten sozialer Netzwerke sowie zum Nachverfolgen von Kundenpräferenzen und -daten verwendet werden.

* **Empfehlungs-Engines**

  Dieses Szenario kommt häufig im Einzelhandel vor. Durch die Kombination von Informationen zu Produkten, Benutzern und Benutzerinteraktionen (z.B. Einkäufe, Surfverhalten oder Bewertungen eines Artikels) können Sie benutzerdefinierte Empfehlungen erstellen. Die geringe Latenz, flexible Skalierung und native Diagrammunterstützung von Azure Cosmos DB sind für diese Szenarien ideal.

* **Geodaten**

  Zahlreiche Anwendungen in den Bereichen Telekommunikation, Logistik und Reiseplanung müssen einen bestimmten Ort in einer bestimmten Region oder die kürzeste bzw. optimale Route zwischen zwei Orten finden. Azure Cosmos DB ist die ideale Lösung für derartige Probleme.

* **Internet der Dinge (IoT)**

  Durch die Modellierung von Netzwerken und Verbindungen zwischen IoT-Geräten als Graphen können Sie sich eine bessere Übersicht über den Status Ihrer Geräte und Ressourcen verschaffen. Sie können auch herausfinden, inwiefern sich Änderungen an einem Teil des Netzwerks möglicherweise auf andere Teile auswirken können.

## <a name="introduction-to-graph-databases"></a>Einführung in Graphdatenbanken

Daten sind in der Praxis naturgemäß vernetzt. Die konventionelle Datenmodellierung konzentriert sich darauf, Entitäten separat zu definieren und ihre Beziehungen zur Laufzeit zu berechnen. Obwohl dieses Modell seine Vorteile hat, kann sich die Verwaltung stark vernetzter Daten angesichts der Einschränkungen des Modells schwierig gestalten.  

Der Ansatz einer Graphdatenbank basiert dagegen auf persistenten Beziehungen in der Speicherebene, was zu hocheffizienten Abrufvorgängen der Graphen führt. Die Gremlin-API von Azure Cosmos DB unterstützt das [Eigenschaftsgraphmodell](https://tinkerpop.apache.org/docs/current/reference/#intro).

### <a name="property-graph-objects"></a>Eigenschaftsgraphobjekte

Ein [Eigenschaftsgraph](http://mathworld.wolfram.com/Graph.html) ist eine Struktur aus [Vertices](http://mathworld.wolfram.com/GraphVertex.html) und [Edges](http://mathworld.wolfram.com/GraphEdge.html). Beide Objekte können eine beliebige Anzahl von Schlüssel-Wert-Paaren als Eigenschaften aufweisen. 

* **Vertices/Knoten**: Vertices bezeichnen diskrete Entitäten, etwa eine Person, einen Ort oder ein Ereignis.

* **Edges/Beziehungen**: Edges bezeichnen Beziehungen zwischen Vertices. Beispielsweise könnte eine Person eine andere Person kennen, an einem Ereignis beteiligt sein und sich vor Kurzem an einem Ort befunden haben.

* **Eigenschaften** – Eigenschaften geben Informationen zu den Vertices und Edges an. Vertices und Edges können eine beliebige Anzahl an Eigenschaften umfassen. Mithilfe der Eigenschaften können die Objekte in einer Abfrage beschrieben und gefiltert werden. Zu Eigenschaften zählen beispielsweise Name und Alter eines Vertex sowie ein Edge, der einen Zeitstempel und/oder eine Gewichtung aufweisen kann.

* **Bezeichnung**: Eine Bezeichnung ist ein Name oder der Bezeichner eines Vertex oder eines Edge. Bezeichnungen können mehrere Vertices oder Edges gruppieren, sodass alle Vertices/Edges in einer Gruppe über eine bestimmte Bezeichnung verfügen. Ein Graph kann beispielsweise mehrere Vertices mit dem Bezeichnungstyp „Person“ aufweisen.

Graphdatenbanken werden oft NoSQL- oder nicht relationalen Datenbanken zugeordnet, da es keine Abhängigkeit von einem Schema oder einem eingeschränkten Datenmodell gibt. Aufgrund der fehlenden Schemas können vernetzte Strukturen natürlich und effizient modelliert und gespeichert werden.

### <a name="graph-database-by-example"></a>Graphdatenbank anhand eines Beispiels

Anhand eines Beispieldiagramms wird erläutert, wie Abfragen in Gremlin ausgedrückt werden können. Die folgende Abbildung zeigt eine Geschäftsanwendung, die Daten zu Benutzern, Interessen und Geräten in Form eines Diagramms verwaltet.  

:::image type="content" source="./media/gremlin-support/sample-graph.png" alt-text="Beispieldatenbank mit Personen, Geräten und Interessen" border="false"::: 

Dieser Graph weist folgende *Vertex*-Typen (in Gremlin auch „Bezeichnung“ genannt) auf:

* **Personen**: Das Diagramm enthält drei Personen: Robin, Thomas und Ben.
* **Interessen**: In diesem Beispiel interessieren sie sich für Football.
* **Geräte**: Die von den Personen verwendeten Geräte.
* **Betriebssysteme**: Die Betriebssysteme auf den Geräten.
* **Ort**: Die Orte, von denen aus auf die Geräte zugegriffen wird.

Die Beziehungen zwischen diesen Entitäten werden anhand von folgenden *Edge* typen dargestellt:

* **Kennt**: Beispiel: „Thomas kennt Robin.“
* **Interessiert an**: Um die Interessen der Personen in unserem Diagramm darzustellen, verwenden wir beispielsweise „Ben ist an Football interessiert.“.
* **Betriebssystem ausgeführt**: Auf dem Laptop wird das Windows-Betriebssystem ausgeführt.
* **Verwendet**: Dies gibt an, welches Gerät eine Person verwendet. Beispiel: „Robin verwendet ein Motorola-Telefon mit der Seriennummer 77.“
* **Standort**: Stellt den Standort dar, von dem aus auf die Geräte zugegriffen wird.

Die Gremlin-Konsole ist ein interaktives Terminal, das von Apache TinkerPop angeboten wird, und dieses Terminal wird verwendet, um mit den Graphdaten zu interagieren. Weitere Informationen finden Sie in der Schnellstartdokumentation unter [How to use the Gremlin Console](create-graph-gremlin-console.md) (Verwenden der Gremlin-Konsole). Sie können diese Vorgänge auch mithilfe von Gremlin-Treibern auf der Plattform Ihrer Wahl (Java, Node.js, Python oder .NET) ausführen. In den folgenden Beispielen wird gezeigt, wie Abfragen für diese Graphdaten mithilfe der Gremlin-Konsole ausgeführt werden.

Sehen wir uns zuerst CRUD an. Die folgende Gremlin-Anweisung fügt den Vertex „Thomas“ in das Diagramm ein:

```java
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Als Nächstes fügt die folgende Gremlin-Anweisung einen „kennt“-Edge zwischen Thomas und Robin ein.

```java
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

Die folgende Abfrage gibt die Vertices „Person“ in absteigender Reihenfolge ihrer Vornamen zurück:

```java
:> g.V().hasLabel('person').order().by('firstName', decr)
```

Diagramme eignen sich am besten, wenn Sie Fragen wie „Welche Betriebssysteme verwenden Freunde von Thomas?“ beantworten müssen. Um diese Information anhand des Diagramms zu ermitteln, können Sie diese Gremlin-Traversierung ausführen:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Diagrammunterstützung in Azure Cosmos DB finden Sie durch folgende Ressourcen:

* Erste Schritte durch das [Tutorial zum Azure Cosmos DB-Diagramm](create-graph-dotnet.md)
* Erfahren Sie mehr über das [Abfragen von Graphen in Azure Cosmos DB mithilfe von Gremlin](gremlin-support.md).