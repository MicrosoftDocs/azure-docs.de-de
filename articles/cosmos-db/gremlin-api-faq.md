---
title: Häufig gestellte Fragen zur Gremlin-API in Azure Cosmos DB
description: Hier finden Sie Antworten auf häufig gestellte Fragen zur Gremlin-API in Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 0374886482b43eefe6df5e107fd4da371633099d
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359777"
---
# <a name="frequently-asked-questions-about-the-gremlin-api-in-azure-cosmos-db"></a>Häufig gestellte Fragen zur Gremlin-API in Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

In diesem Artikel werden einige häufig gestellte Fragen zur Gremlin-API in Azure Cosmos DB beantwortet.

## <a name="how-to-evaluate-the-efficiency-of-gremlin-queries"></a>Auswerten der Effizienz von Gremlin-Abfragen

Mit dem Vorschauschritt **executionProfile()** können Sie eine Analyse des Abfrageausführungsplans bereitstellen. Dieser Schritt muss wie im folgenden Beispiel gezeigt am Ende einer Gremlin-Abfrage hinzugefügt werden:

**Abfragebeispiel**

```
g.V('mary').out('knows').executionProfile()
```

**Beispielausgabe**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

Die Ausgabe des Profils oben zeigt, wie viel Zeit zum Abrufen der Scheitelpunkt- und Kantenobjekte aufgewendet wird, sowie die Größe des Arbeitsdatasets. Dies bezieht sich auf die Standardkostenmessungen für Azure Cosmos DB-Abfragen.

## <a name="other-frequently-asked-questions"></a>Weitere häufig gestellte Fragen

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Wie werden RUs/Sek. beim Ausführen von Abfragen für eine Graphdatenbank in Rechnung gestellt?

Alle Graphobjekte – Scheitelpunkte und Kanten – werden im Back-End als JSON-Dokumente dargestellt. Da eine Gremlin-Abfrage jeweils ein oder mehrere Graphobjekte ändern kann, stehen die damit verbundenen Kosten in direkter Beziehung zu den Objekten – Kanten, die von der Abfrage verarbeitet werden. Denselben Prozess verwendet Azure Cosmos DB für alle anderen APIs. Weitere Informationen finden Sie unter [Anforderungseinheiten in Azure Cosmos DB](request-units.md).

Die RU-Gebühr basiert auf dem Arbeits-DataSet des Durchlaufs und nicht auf dem Resultset. Beispiel: Wenn eine Abfrage einen einzelnen Scheitelpunkt als Ergebnis ermitteln soll, dabei jedoch mehrere andere Objekte durchlaufen muss, basieren die Kosten auf allen Graphobjekten, die zum Berechnen des Ergebnisscheitelpunkts benötigt werden.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Welche maximale Skalierung kann eine Graphdatenbank in der Azure Cosmos DB-Gremlin-API aufweisen?

Azure Cosmos DB nutzt [horizontale Partitionierung](partitioning-overview.md), um einem Anstieg der Speicher- und Durchsatzanforderungen automatisch gerecht zu werden. Das Höchstmaß an Durchsatz und Speicherkapazität einer Workload richtet sich nach der Menge von Partitionen, die einem bestimmten Container zugeordnet sind. Ein Gremlin-API-Container weist jedoch einen bestimmten Satz von Richtlinien auf, um eine optimale bedarfsorientierte Leistung zu gewährleisten. Weitere Informationen zur Partitionierung und zu Best Practices finden Sie im Artikel [Partitionieren und horizontales Skalieren in Azure Cosmos DB](partitioning-overview.md).

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>Soll ich für die C#/.NET-Entwicklung das Paket „Microsoft.Azure.Graphs“ oder Gremlin.NET verwenden?

Die Azure Cosmos DB-Gremlin-API nutzt die Open-Source-Treiber als Hauptconnectors für den Dienst. Daher ist die empfohlene Option die Verwendung von [Treibern, die von Apache Tinkerpop unterstützt werden](https://tinkerpop.apache.org/).

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Wie kann ich mithilfe von Gremlin-Treibern vor Einschleusungsangriffen schützen?

Die meisten nativen Gremlin-Treiber von Apache TinkerPop lassen die Bereitstellung eines Wörterbuchs von Parametern für die Abfrageausführung zu. Hier finden Sie Beispiele für die Bereitstellung in [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) und in [Gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/main/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Warum erhalte ich die Fehlermeldung „Gremlin Query Compilation Error: Unable to find any method“ (Fehler bei der Kompilierung von Gremlin-Abfragen: Methoden konnten nicht gefunden werden)?

Die Azure Cosmos DB-Gremlin-API implementiert eine Teilmenge der auf der Gremlin-Oberfläche definierten Funktionen. Unterstützte Schritte und weitere Informationen finden Sie im Artikel [Gremlin-Unterstützung](gremlin-support.md).

Die beste Problemumgehung besteht darin, die Gremlin-Schritte mit den unterstützten Funktionen erneut zu schreiben, da alle wesentlichen Gremlin-Schritte von Azure Cosmos DB unterstützt werden.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Warum erhalte ich die Fehlermeldung „WebSocketException: The server returned status code '200' when status code '101' was expected“ (WebSocketException: Der Server hat den Statuscode „200“ zurückgegeben, obwohl der Statuscode „101“ erwartet wurde)?

Dieser Fehler wird wahrscheinlich ausgelöst, wenn der falsche Endpunkt verwendet wird. Der Endpunkt, der diesen Fehler generiert, hat das folgende Muster:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Dies ist der Dokumentendpunkt für Ihre Graphdatenbank.  Der richtige Endpunkt für die Verwendung ist der Gremlin-Endpunkt, der das folgende Format aufweist:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Warum erhalte ich den Fehler „RequestRateIsTooLarge“?

Dieser Fehler weist darauf hin, dass die zugeordneten Anforderungseinheiten pro Sekunde für die Verarbeitung der Abfrage nicht ausreichen. Dieser Fehler wird in der Regel angezeigt, wenn Sie eine Abfrage ausführen, die alle Scheitelpunkte abruft:

```
// Query example:
g.V()
```

Diese Abfrage versucht, alle Scheitelpunkte aus dem Graph abzurufen. Daher entsprechen die Kosten für diese Abfrage mindestens der Anzahl von Scheitelpunkten in Bezug auf RUs. Die RUs/Sek.-Einstellung muss dieser Abfrage entsprechend angepasst werden.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Warum werden meine Gremlin-Treiberverbindungen schließlich gelöscht?

Eine Gremlin-Verbindung wird über eine WebSocket-Verbindung hergestellt. WebSocket-Verbindungen haben zwar keine bestimmte Gültigkeitsdauer, die Gremlin-API von Azure Cosmos DB beendet Verbindungen im Leerlauf jedoch nach 30 Minuten Inaktivität.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Warum kann ich keine Fluent-API-Aufrufe in den nativen Gremlin-Treibern verwenden?

Fluent-API-Aufrufe werden von der Azure Cosmos DB-Gremlin-API noch nicht unterstützt. Fluent-API-Aufrufe erfordern ein internes Formatierungsfeature namens Bytecode-Unterstützung, das von der Azure Cosmos DB-Gremlin-API derzeit nicht unterstützt wird. Aus demselben Grund wird auch der aktuelle Gremlin-JavaScript-Treiber derzeit nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

* [Gremlin-Wire Protocol-Unterstützung in Azure Cosmos DB](gremlin-support.md)
* Erstellen, Abfragen und Durchlaufen einer Azure Cosmos DB-Graphdatenbank mithilfe der [Gremlin-Konsole](create-graph-gremlin-console.md)
