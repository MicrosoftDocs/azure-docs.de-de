---
title: Modellierung von Daten in Azure Cosmos DB
titleSuffix: Azure Cosmos DB
description: Lernen Sie die Datenmodellierung in NoSQL-Datenbanken sowie die Unterschiede zwischen der Datenmodellierung in einer relationalen Datenbank und in einer Dokumentdatenbank kennen.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a141177846def9c94216684c1083d0d336eeda1e
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333238"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Datenmodellierung in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Obwohl schemafreie Datenbanken wie Azure Cosmos DB das Speichern und Abfragen unstrukturierter und teilstrukturierter Daten sehr erleichtern, sollten Sie etwas Zeit in die Überlegung investieren, wie Ihr Datenmodell den Dienst im Hinblick auf Leistung, Skalierbarkeit und niedrigste Kosten optimal nutzen kann.

Wie werden die Daten gespeichert? Wie wird Ihre Anwendung Daten abrufen und abfragen? Ist Ihre Anwendung eher leseintensiv oder schreibintensiv?

Nach dem Lesen dieses Artikels können Sie die folgenden Fragen beantworten:

* Was ist Datenmodellierung und warum sollte ich mich dafür interessieren?
* Inwiefern unterscheidet sich die Datenmodellierung in Azure Cosmos DB von einer relationalen Datenbank?
* Wie kann ich Datenbeziehungen in einer nicht-relationalen Datenbank ausdrücken?
* Wann bette ich Daten ein, und wann verknüpfe ich sie?

## <a name="embedding-data"></a>Einbetten von Daten

Versuchen Sie beim ersten Modellieren von Daten in Azure Cosmos DB Ihre Entitäten als **eigenständige Elemente** , dargestellt als JSON-Dokumente, zu behandeln.

Zum Vergleich betrachten wir zunächst die Datenmodellierung in einer relationalen Datenbank. Das folgende Beispiel zeigt, wie eine Person in einer relationalen Datenbank gespeichert werden kann.

:::image type="content" source="./media/sql-api-modeling-data/relational-data-model.png" alt-text="Relationales Datenbankmodell" border="false":::

Bei der Arbeit mit relationalen Datenbanken besteht die Strategie im Normalisieren Ihrer gesamten Daten. Beim Normalisieren von Daten wird in der Regel eine Entität, z.B. eine Person, in einzelne Komponenten aufgeschlüsselt. Im obigen Beispiel kann eine Person über mehrere Kontaktdetaildatensätze sowie mehrere Adressdatensätze verfügen. Kontaktdetails können durch weiteres Extrahieren allgemeiner Felder, wie z.B. eines Typs, weiter aufgeschlüsselt werden. Das gleiche gilt für die Adresse, jeder Datensatz kann vom Typ *Home* oder *Business* sein.

Die beim Normalisieren von Daten geltende Prämisse besteht darin, dass das **Speichern von redundanten Daten in jedem Datensatz zu vermeiden** ist und dass stattdessen auf die einzelnen Daten verwiesen werden soll. Um in diesem Beispiel die Informationen zu einer Person mit allen ihren Kontaktdaten und Adressen zu lesen, müssen Sie JOINS verwenden, um Ihre Daten zur Runtime wieder effektiv zusammenzusetzen (oder denormalisieren).

```sql
SELECT p.FirstName, p.LastName, a.City, cd.Detail
FROM Person p
JOIN ContactDetail cd ON cd.PersonId = p.Id
JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
JOIN Address a ON a.PersonId = p.Id
```

Für das Aktualisieren einer einzelnen Person mit allen ihren Kontaktdaten und Adressen sind Schreibvorgänge über viele einzelne Tabellen hinweg erforderlich.

Sehen Sie sich jetzt an, wie Sie die gleichen Daten als eigenständige Entität in Azure Cosmos DB modellieren können.

```json
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "addresses": [
        {
            "line1": "100 Some Street",
            "line2": "Unit 1",
            "city": "Seattle",
            "state": "WA",
            "zip": 98012
        }
    ],
    "contactDetails": [
        {"email": "thomas@andersen.com"},
        {"phone": "+1 555 555-5555", "extension": 5555}
    ]
}
```

Mit dem oben stehenden Ansatz haben wir den Personendatensatz **denormalisiert** , indem wir alle Informationen im Zusammenhang mit dieser Person, etwa ihre Kontaktdaten und Adressen, in ein *einzelnes JSON-Dokument* **eingebettet** haben.
Da wir nicht auf ein festes Schema beschränkt sind, haben wir darüber hinaus die Flexibilität, z. B. Kontaktdetails in vollständig verschiedenen Formen zu haben.

Das Abrufen eines vollständigen Personendatensatzes aus der Datenbank besteht jetzt aus einem **einzelnen Lesevorgang** eines einzelnen Containers und für ein einzelnes Element. Das Aktualisieren eines Personendatensatzes zusammen mit den Kontaktinformationen und Adressen ist auch ein **einzelner Schreibvorgang** in einem einzelnen Element.

Durch das Denormalisieren von Daten muss Ihre Anwendung u. U. weniger Abfragen und Aktualisierungen ausgeben, um allgemeine Vorgänge abzuschließen.

### <a name="when-to-embed"></a>Wann Sie einbetten sollten

Verwenden Sie in der Regel eingebettete Datenmodelle in den folgenden Fällen:

* Zwischen Entitäten gibt es **contained** -Beziehungen.
* Zwischen Entitäten gibt es **eins-zu-viele** -Beziehungen.
* Es gibt eingebettete Daten, die sich **selten ändern**.
* Es gibt eingebettete Daten, die nicht **grenzenlos** wachsen.
* Es gibt eingebettete Daten, die **häufig gemeinsam abgefragt werden**.

> [!NOTE]
> In der Regel bieten denormalisierte Datenmodelle eine bessere **Leseleistung** .

### <a name="when-not-to-embed"></a>Wann Sie nicht einbetten sollten

In Azure Cosmos DB gilt zwar die Faustregel, dass alles denormalisiert wird und alle Daten in ein einzelnes Element eingebettet werden sollen. Dies kann jedoch zu Situationen führen, die es zu vermeiden gilt.

Nehmen Sie beispielsweise diesen JSON-Ausschnitt.

```json
{
    "id": "1",
    "name": "What's new in the coolest Cloud",
    "summary": "A blog post by someone real famous",
    "comments": [
        {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
        {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
        …
        {"id": 100001, "author": "jane", "comment": "and on we go ..."},
        …
        {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
        …
        {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
    ]
}
```

So könnte eine Beitragsentität mit eingebetteten Kommentaren aussehen, wenn wir ein typisches Blog- oder CMS-System modellieren würden. Das Problem bei diesem Beispiel besteht darin, dass das Kommentar-Array **unbegrenzt** ist, d.h. es gibt (praktisch) keine Begrenzung hinsichtlich der Anzahl an Kommentaren zu einem einzelnen Beitrag. Dies kann problematisch werden, da die Größe des Elements unendlich zunehmen könnte.

Mit zunehmender Größe des Elements wird die Möglichkeit zum Übertragen von Daten über das Netz sowie zum Lesen und Aktualisieren des Elements bei Skalierung beeinträchtigt.

In einem solchen Fall sollte lieber das folgende Datenmodell in Betracht gezogen werden.

```json
Post item:
{
    "id": "1",
    "name": "What's new in the coolest Cloud",
    "summary": "A blog post by someone real famous",
    "recentComments": [
        {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
        {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
        {"id": 3, "author": "jane", "comment": "....."}
    ]
}

Comment items:
{
    "postId": "1"
    "comments": [
        {"id": 4, "author": "anon", "comment": "more goodness"},
        {"id": 5, "author": "bob", "comment": "tails from the field"},
        ...
        {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
    ]
},
{
    "postId": "1"
    "comments": [
        {"id": 100, "author": "anon", "comment": "yet more"},
        ...
        {"id": 199, "author": "bored", "comment": "will this ever end?"}
    ]
}
```

Bei diesem Modell sind die drei aktuellsten Kommentare in den Beitragscontainer eingebettet, wobei es sich diesmal um ein Array mit einem festen Satz von Attributen handelt. Die anderen Kommentare sind zu Stapeln von je 100 Kommentaren gruppiert und in separaten Elementen gespeichert. Die Größe des Stapels wurde auf 100 festgelegt, da unsere fiktive Anwendung es dem Benutzer ermöglicht, 100 Kommentare gleichzeitig zu laden.  

Das Einbetten von Daten ist auch dann keine gute Idee, wenn die eingebetteten Daten häufig in Elementen verwendet und häufig geändert werden.

Nehmen Sie beispielsweise diesen JSON-Ausschnitt.

```json
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "holdings": [
        {
            "numberHeld": 100,
            "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
        },
        {
            "numberHeld": 50,
            "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
        }
    ]
}
```

Dabei kann es sich um das Aktienportfolio einer Person handeln. Wir haben uns dazu entschlossen, die Aktiendaten in jedes Portfoliodokument einzubetten. In einer Umgebung, in der verknüpfte Daten häufig geändert werden, wie z. B. eine Aktienhandelsanwendung, bedeutet das Einbetten von sich häufig ändernden Daten, dass Sie bei jedem Aktienhandel jedes Portfoliodokument aktualisieren müssen.

Die Aktie *zaza* kann jeden Tag mehrere Hundert Mal gehandelt werden, und Tausende Benutzer besitzen *zaza* in ihrem Portfolio. Bei einem Datenmodell wie dem obigen müssten wir zigtausend Portfoliodokumente mehrmals täglich aktualisieren, was zu einem schlecht skalierbaren System führt.

## <a name="referencing-data"></a>Verweisen auf Daten

Das Einbetten von Daten funktioniert in vielen Fällen gut, aber es gibt Szenarien, bei denen das Denormalisieren von Daten mehr Probleme verursacht als es wert ist. Was also können wir jetzt tun?

Relationale Datenbanken sind nicht der einzige Ort, an dem Sie Beziehungen zwischen Entitäten herstellen können. In einer Dokumentdatenbank können sich Informationen in einem Dokument befinden, die sich auf Daten in anderen Dokumenten beziehen. Es wird nicht empfohlen, Systeme zu erstellen, die besser für eine relationale Datenbank in Azure Cosmos DB oder eine andere Dokumentdatenbank geeignet wären, aber einfache Beziehungen sind in Ordnung und können sinnvoll sein.

Im JSON-Abschnitt unten verwenden wir das vorherige Beispiel eines Aktienportfolios, aber dieses Mal verweisen wir auf den Aktieneintrag im Portfolio, anstatt ihn einzubetten. Auf diese Weise müssen wir nur dieses eine Aktiendokument aktualisieren, wenn sich der Aktieneintrag mehrmals am Tag ändert.

```json
Person document:
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "holdings": [
        { "numberHeld":  100, "stockId": 1},
        { "numberHeld":  50, "stockId": 2}
    ]
}

Stock documents:
{
    "id": "1",
    "symbol": "zaza",
    "open": 1,
    "high": 2,
    "low": 0.5,
    "vol": 11970000,
    "mkt-cap": 42000000,
    "pe": 5.89
},
{
    "id": "2",
    "symbol": "xcxc",
    "open": 89,
    "high": 93.24,
    "low": 88.87,
    "vol": 2970200,
    "mkt-cap": 1005000,
    "pe": 75.82
}
```

Ein unmittelbarer Nachteil dieses Ansatzes besteht jedoch darin, dass, wenn Ihre Anwendung Informationen zu jeder Aktie anzeigen muss, die bei der Anzeige des Portfolios einer Person enthalten sein müssen, Sie mehrere Roundtrips zur Datenbank durchführen müssen, um die Informationen für jedes Aktiendokument zu laden. Hier haben wir beschlossen, die Effizienz der Schreibvorgänge zu verbessern, die mehrmals täglich durchgeführt werden, wir nehmen wiederum die Beeinträchtigung der Lesevorgänge hin, die sich potenziell weniger stark auf die Leistung dieses speziellen Systems auswirken.

> [!NOTE]
> Normalisierte Datenmodelle **können mehrere Roundtrips zum Server erfordern** .

### <a name="what-about-foreign-keys"></a>Was ist mit Fremdschlüsseln?

Da es derzeit kein Konzept für eine Einschränkung gibt, Fremdschlüssel oder anderweitig, sind alle Beziehungen zwischen Dokumenten in Dokumenten im Grunde "schwache Verknüpfungen" und werden nicht von der Datenbank selbst überprüft. Wenn Sie sicherstellen möchten, dass die Daten, auf die ein Dokument verweist, tatsächlich vorhanden sind, müssen Sie dies in der Anwendung durchführen oder serverseitige Trigger oder gespeicherte Prozeduren für Azure Cosmos DB verwenden.

### <a name="when-to-reference"></a>Wann Sie verweisen sollten

Verwenden Sie in der Regel normalisierte Datenmodelle in den folgenden Fällen:

* Zur Darstellung von **1:n** -Beziehungen.
* Zur Darstellung von **m:n** -Beziehungen.
* Wenn sich zugehörige Daten **häufig ändern**.
* Wenn referenzierte Daten möglicherweise **unbegrenzt** sind.

> [!NOTE]
> Wenn eine Normalisierung in der Regel eine bessere **Schreibleistung** bietet.

### <a name="where-do-i-put-the-relationship"></a>Wo erstelle ich die Beziehung?

Das Wachstum der Beziehung hilft bei der Bestimmung, in welchem Dokument der Verweis gespeichert werden sollte.

Sehen Sie sich den unten stehenden JSON-Code an, in dem Verleger und Bücher modelliert werden.

```json
Publisher document:
{
    "id": "mspress",
    "name": "Microsoft Press",
    "books": [ 1, 2, 3, ..., 100, ..., 1000]
}

Book documents:
{"id": "1", "name": "Azure Cosmos DB 101" }
{"id": "2", "name": "Azure Cosmos DB for RDBMS Users" }
{"id": "3", "name": "Taking over the world one JSON doc at a time" }
...
{"id": "100", "name": "Learn about Azure Cosmos DB" }
...
{"id": "1000", "name": "Deep Dive into Azure Cosmos DB" }
```

Wenn die Anzahl der Bücher für jeden Verleger klein ist und nur über begrenztes Wachstum verfügt, kann es nützlich sein, den Buchverweis im Verlegerdokument zu speichern. Wenn die Anzahl der Bücher pro Verleger jedoch unbegrenzt ist, würde dieses Datenmodell zu veränderbaren, wachsenden Arrays führen, wie im obigen Verlegerbeispieldokument gezeigt.

Durch ein paar Änderungen entsteht ein Modell, das weiterhin die gleichen Daten darstellt, aber diese großen veränderbaren Sammlungen werden vermieden.

```json
Publisher document:
{
    "id": "mspress",
    "name": "Microsoft Press"
}

Book documents:
{"id": "1","name": "Azure Cosmos DB 101", "pub-id": "mspress"}
{"id": "2","name": "Azure Cosmos DB for RDBMS Users", "pub-id": "mspress"}
{"id": "3","name": "Taking over the world one JSON doc at a time"}
...
{"id": "100","name": "Learn about Azure Cosmos DB", "pub-id": "mspress"}
...
{"id": "1000","name": "Deep Dive into Azure Cosmos DB", "pub-id": "mspress"}
```

Im Beispiel oben haben wir die unbegrenzte Auflistung im Verlegerdokument gelöscht. Stattdessen haben wir nur einen Verweis auf den Verleger in jedem Buchdokument hinzugefügt.

### <a name="how-do-i-model-manymany-relationships"></a>Wie modelliere ich Viele-zu-viele-Beziehungen?

In einer relationalen Datenbank *m:n* -Beziehungen häufig mit Verknüpfungstabellen modelliert, bei denen einfach Datensätze aus anderen Tabellen miteinander verknüpft werden.


:::image type="content" source="./media/sql-api-modeling-data/join-table.png" alt-text="Verknüpfen von Tabellen" border="false":::

Möglicherweise sind Sie versucht, dasselbe mit Dokumenten zu replizieren, wobei jedoch ein Datenmodell entsteht, das etwa folgendermaßen aussieht.

```json
Author documents:
{"id": "a1", "name": "Thomas Andersen" }
{"id": "a2", "name": "William Wakefield" }

Book documents:
{"id": "b1", "name": "Azure Cosmos DB 101" }
{"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
{"id": "b3", "name": "Taking over the world one JSON doc at a time" }
{"id": "b4", "name": "Learn about Azure Cosmos DB" }
{"id": "b5", "name": "Deep Dive into Azure Cosmos DB" }

Joining documents:
{"authorId": "a1", "bookId": "b1" }
{"authorId": "a2", "bookId": "b1" }
{"authorId": "a1", "bookId": "b2" }
{"authorId": "a1", "bookId": "b3" }
```

Das würde auch funktionieren. Wenn Sie jedoch einen Autor mit seinen Büchern oder ein Buch mit seinem Autor laden, sind immer mindestens zwei zusätzliche Abfragen für die Datenbank erforderlich. Eine Abfrage, um das Dokument zu verknüpfen, und dann eine weitere Abfrage zum Abrufen des tatsächlichen zu verknüpfenden Dokuments.

Wenn die Verknüpfungstabelle einfach nur zwei Informationen miteinander verbindet, dann könnte man sie auch einfach komplett löschen.
Stellen Sie sich einmal Folgendes vor:

```json
Author documents:
{"id": "a1", "name": "Thomas Andersen", "books": ["b1", "b2", "b3"]}
{"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

Book documents:
{"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
{"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
{"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
{"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}
```

Es gibt einen Autor, bei dem ich sofort weiß, welche Bücher er geschrieben hat, oder ich habe ein Buchdokument geladen, für das ich die IDs der Autoren kenne. Dadurch ersparen Sie sich Zwischenabfragen der Verknüpfungstabelle, wodurch wiederum die Anzahl der Serverroundtrips Ihrer Anwendung reduziert wird.

## <a name="hybrid-data-models"></a>Hybriddatenmodelle

Wir haben jetzt gesehen wie Daten eingebettet (bzw. denormalisiert) werden und wie auf Daten verwiesen wird (bzw. wie sie normalisiert werden), und beide Modelle haben ihre Vor- und Nachteile.

Doch es muss nicht immer ein Entweder-oder geben. Trauen Sie sich, die Systeme etwas zu vermischen.

Basierend auf bestimmten Verwendungsmustern und Arbeitsauslastungen gibt es möglicherweise Fälle, in denen eine Mischung aus eingebetteten und referenzierten Daten sinnvoll sein kann und zu einer einfacheren Anwendungslogik mit weniger Serverroundtrips führen kann, wobei gleichzeitig eine gute Leistung gewahrt wird.

Betrachten Sie das folgende JSON-Beispiel.

```json
Author documents:
{
    "id": "a1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "countOfBooks": 3,
    "books": ["b1", "b2", "b3"],
    "images": [
        {"thumbnail": "https://....png"}
        {"profile": "https://....png"}
        {"large": "https://....png"}
    ]
},
{
    "id": "a2",
    "firstName": "William",
    "lastName": "Wakefield",
    "countOfBooks": 1,
    "books": ["b1"],
    "images": [
        {"thumbnail": "https://....png"}
    ]
}

Book documents:
{
    "id": "b1",
    "name": "Azure Cosmos DB 101",
    "authors": [
        {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
        {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "https://....png"}
    ]
},
{
    "id": "b2",
    "name": "Azure Cosmos DB for RDBMS Users",
    "authors": [
        {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
    ]
}
```

Hier haben wir (hauptsächlich) das eingebettete Modell verwendet, bei dem Daten aus anderen Entitäten im übergeordneten Dokument eingebettet sind, in dem jedoch auf andere Daten verwiesen wird.

Wenn Sie das Buchdokument betrachten, sehen Sie ein paar interessante Felder, wenn wir das Array der Autoren betrachten. Es gibt ein `id`-Feld, bei dem es sich um das Feld handelt, das für einen Rückverweis auf ein Autorendokument verwendet wird (Standardmethode in einem normalisierten Modell). Es gibt aber auch die Felder `name` und `thumbnailUrl`. Wir hätten auch nur bei dem Feld `id` bleiben können, sodass die Anwendung alle zusätzlichen erforderlichen Informationen mithilfe des „Links“ aus dem jeweiligen Autorendokument abruft. Da unsere Anwendung jedoch zusammen mit jedem angezeigten Buch den Namen des Autors und ein Miniaturbild anzeigt, können wir pro Buch einen Roundtrip zum Server in einer Liste speichern, indem wir **einige** Daten vom Autor denormalisieren.

Sicherlich müssten wir, wenn sich der Name des Autors ändert oder das Foto aktualisiert werden soll, jedes jemals von ihm veröffentlichte Buch aktualisieren. Bei unserer Anwendung ist dies jedoch eine akzeptable Entwurfsentscheidung, da wir von der Annahme ausgehen, dass Autoren ihre Namen nicht sehr häufig ändern.  

Im Beispiel gibt es **vorab berechnete Aggregatwerte** , um die aufwändige Verarbeitung eines Lesevorgangs zu vermeiden. Im Beispiel werden einige der im Autorendokument eingebetteten Daten zur Laufzeit berechnet. Jedes Mal, wenn ein neues Buch veröffentlicht wird, wird ein Buchdokument erstellt, **und** das Feld „CountOfBooks“ wird auf einen berechneten Wert festgelegt, basierend auf der Anzahl der Buchdokumente, die für einen bestimmten Autor vorhanden sind. Diese Optimierung wäre in schreibintensiven Systemen gut, in denen wir uns Berechnungen für Schreibvorgänge leisten können, um Lesevorgänge zu optimieren.

Die Möglichkeit über ein Modell mit vorab berechneten Felder zu verfügen, wird dank der Unterstützung von **Transaktionen mit mehreren Dokumenten** durch Azure Cosmos DB ermöglicht. Viele NoSQL-Speicher können keine Transaktionen über Dokumente hinweg durchführen und bevorzugen aufgrund dieser Einschränkung Entwurfsentscheidungen, wie z. B. "Immer alles einbetten". Mit Azure Cosmos DB können Sie serverseitige Trigger oder gespeicherte Prozeduren verwenden, mit denen Bücher eingefügt und Autoren in einer einzigen ACID-Transaktion aktualisiert werden. Sie **müssen** nicht alles in ein Dokument einbetten, nur um sicherzustellen, dass Ihre Daten konsistent bleiben.

## <a name="distinguishing-between-different-document-types"></a>Unterscheiden zwischen den verschiedenen Dokumenttypen

In einigen Szenarien möchten Sie möglicherweise verschiedene Dokumenttypen in derselben Sammlung zusammenfassen. Dies ist häufig der Fall, wenn Sie mehrere verwandte Dokumente in derselben [Partition](partitioning-overview.md) speichern möchten. Sie können z. B. Bücher und Buchbesprechungen in derselben Sammlung speichern und anhand der `bookId` partitionieren. In einem solchen Fall möchten Sie in der Regel Ihre Dokumente mit einem Feld hinzufügen, das ihren Typ angibt, um sie zu unterscheiden.

```json
Book documents:
{
    "id": "b1",
    "name": "Azure Cosmos DB 101",
    "bookId": "b1",
    "type": "book"
}

Review documents:
{
    "id": "r1",
    "content": "This book is awesome",
    "bookId": "b1",
    "type": "review"
},
{
    "id": "r2",
    "content": "Best book ever!",
    "bookId": "b1",
    "type": "review"
}
```

## <a name="next-steps"></a>Nächste Schritte

Die wichtigsten Erkenntnisse dieses Artikels bestehen darin, dass die Datenmodellierung in einer schemafreien Welt genauso wichtig ist wie eh und je.

Es gibt genauso wenig einen einzigen Weg, um Daten auf einem Bildschirm darzustellen, wie es nur einen einzigen Weg gibt, Ihre Daten zu modellieren. Sie müssen Ihre Anwendung kennen und wissen, wie die Daten erstellt, verwendet und verarbeitet werden. Erst dann können Sie durch Anwenden von einigen der hier vorgestellten Richtlinien damit beginnen, ein Modell zu erstellen, das die unmittelbaren Anforderungen der Anwendung berücksichtigt. Wenn sich Ihre Anwendungen ändern müssen, können Sie die Flexibilität einer schemafreien Datenbank nutzen, um diese Änderung zu übernehmen und um das Datenmodell problemlos weiterzuentwickeln.

Weitere Informationen zu Azure Cosmos DB finden Sie auf der [Dokumentationsseite](https://azure.microsoft.com/documentation/services/cosmos-db/) des Diensts.

Informationen zur horizontalen Partitionierung („Sharding“) Ihrer Daten auf mehreren Partitionen finden Sie unter [Partitionieren von Daten in Azure Cosmos DB](partitioning-overview.md).

Erfahren Sie mehr über das [Modellieren und Partitionieren von Daten in Azure Cosmos DB anhand eines praktischen Beispiels](how-to-model-partition-example.md).
