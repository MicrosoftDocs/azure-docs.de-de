---
title: 'Azure Cosmos DB: Gremlin-Antwortheader'
description: Referenzdokumentation zu Serverantwortmetadaten, die zusätzliche Problembehandlung ermöglichen
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: christopheranderson
ms.author: chrande
ms.openlocfilehash: 3f5996b281c1985747f754e3796e9fb84f90fdd3
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93356959"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Azure Cosmos DB: Gremlin-Serverantwortheader
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

In diesem Artikel werden Header behandelt, die der Cosmos DB Gremlin-Server bei der Anforderungsausführung an den Aufrufer zurückgibt. Diese Header sind nützlich für die Problembehandlung der Anforderungsleistung, beim Erstellen von Anwendungen, die sich nativ in den Cosmos DB-Dienst integrieren lassen, und bei der Vereinfachung des Kundensupports.

Beachten Sie, dass Sie die Portierbarkeit Ihrer Anwendung auf andere Gremlin-Implementierungen einschränken, wenn Sie eine Abhängigkeit von diesen Headern schaffen. Andererseits erhalten Sie eine engere Integration in Cosmos DB Gremlin. Diese Header sind kein TinkerPop-Standard.

## <a name="headers"></a>Header

| Header | type | Beispielwert | Wenn eingebunden | Erklärung |
| --- | --- | --- | --- | --- |
| **x-ms-request-charge** | double | 11,3243 | Erfolg und Fehler | Menge des Sammlungs- oder Datenbankdurchsatzes, der in [Anforderungseinheiten (RU/s oder RUs)](request-units.md) für eine Teilantwortnachricht verbraucht wird. Dieser Header ist in jeder Fortsetzung für Anforderungen vorhanden, die aus mehreren Blöcken bestehen. Er gibt die Kosten eines bestimmten Antwortblocks wieder. Nur bei Anforderungen, die aus einem einzelnen Antwortblock bestehen, stimmt dieser Header mit den Gesamtkosten des Durchlaufs überein. Bei der Mehrzahl komplexer Durchläufe stellt dieser Wert jedoch partielle Kosten dar. |
| **x-ms-total-request-charge** | double | 423,987 | Erfolg und Fehler | Menge des Sammlungs- oder Datenbankdurchsatzes, der in [Anforderungseinheiten (RU/s oder RUs)](request-units.md) für die gesamte Anforderung verbraucht wird. Dieser Header ist in jeder Fortsetzung für Anforderungen vorhanden, die aus mehreren Blöcken bestehen. Gibt die kumulierten Kosten seit dem Beginn der Anforderung an. Der Wert dieses Headers im letzten Block gibt die gesamten Anforderungskosten an. |
| **x-ms-server-time-ms** | double | 13,75 | Erfolg und Fehler | Dieser Header ist für die Behandlung von Latenzproblemen enthalten. Er gibt die Zeitspanne in Millisekunden an, die der Cosmos DB Gremlin-Server benötigt hat, um eine Teilantwortnachricht auszuführen und zu generieren. Wenn Sie den Wert dieses Headers verwenden und ihn mit den allgemeinen Anwendungen für die Latenzzeit von Anforderungen vergleichen, können Sie den Mehraufwand für Netzwerklatenz berechnen. |
| **x-ms-total-server-time-ms** | double | 130,512 | Erfolg und Fehler | Die Gesamtzeit in Millisekunden, die der Cosmos DB Gremlin-Server benötigt hat, um den gesamten Durchlauf auszuführen. Dieser Header ist in jeder Teilantwort enthalten. Er stellt eine kumulative Ausführungszeit seit dem Start der Anforderung dar. Die letzte Antwort gibt die Gesamtausführungszeit an. Dieser Header ist hilfreich, um zwischen Client und Server als Quelle von Latenz zu unterscheiden. Sie können die Ausführungszeit für den Durchlauf auf dem Client mit dem Wert dieses Headers vergleichen. |
| **x-ms-status-code** | long | 200 | Erfolg und Fehler | Der Header gibt den internen Grund für den Abschluss oder die Beendigung der Anforderung an. Der Anwendung wird empfohlen, den Wert dieses Headers zu überprüfen und Korrekturmaßnahmen zu ergreifen. |
| **x-ms-substatus-code** | long | 1003 | Nur Fehler | Bei Cosmos DB handelt es sich um eine Datenbank mit mehreren Modellen, die auf der vereinheitlichten Speicherschicht basiert. Dieser Header enthält zusätzliche Erkenntnisse über die Fehlerursache beim Auftreten von Fehlern in den unteren Schichten des Hochverfügbarkeitsstapels. Der Anwendung wird empfohlen, diesen Header zu speichern und zu verwenden, wenn Kontakt mit dem Cosmos DB-Kundensupport aufgenommen wird. Der Wert dieses Headers ist für die schnelle Problembehandlung durch einen Cosmos DB-Techniker nützlich. |
| **x-ms-retry-after-ms** | string (TimeSpan) | "00:00:03.9500000" | Nur Fehler | Dieser Header ist eine Zeichenfolgendarstellung eines .NET [TimeSpan](/dotnet/api/system.timespan)-Typs. Dieser Wert ist nur in Anforderungen enthalten, die aufgrund der bereitgestellten Durchsatzauslastung fehlgeschlagen sind. Die Anwendung sollte den Durchlauf nach einem angegebenen Zeitraum erneut übermitteln. |
| **x-ms-activity-id** | string (Guid) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Erfolg und Fehler | Der Header enthält einen eindeutigen serverseitigen Bezeichner für eine Anforderung. Jeder Anforderung wird vom Server ein eindeutiger Bezeichner zu Nachverfolgungszwecken zugewiesen. Anwendungen sollten Aktivitätsbezeichner protokollieren, die vom Server für Anforderungen zurückgegeben werden, zu denen sich Kunden möglicherweise an den Kundensupport wenden möchten. Cosmos DB-Supportmitarbeiter können bestimmte Anforderungen durch diese Bezeichner in der Cosmos DB-Diensttelemetrie ermitteln. |

## <a name="status-codes"></a>Statuscodes

Die häufigsten vom Server zurückgegebenen Statuscodes werden unten aufgeführt.

| Status | Erklärung |
| --- | --- |
| **401** | Die Fehlermeldung `"Unauthorized: Invalid credentials provided"` wird zurückgegeben, wenn das Authentifizierungskennwort nicht mit dem Cosmos DB-Kontoschlüssel übereinstimmt. Navigieren Sie im Azure-Portal zu Ihrem Cosmos DB Gremlin-Konto, und vergewissern Sie sich, dass der Schlüssel richtig ist.|
| **404** | Gleichzeitige Vorgänge, bei denen versucht wird, den gleichen Edge oder Vertex parallel zu löschen und zu aktualisieren. Mit der Fehlermeldung `"Owner resource does not exist"` wird darauf hingewiesen, dass die angegebene Datenbank oder Sammlung in den Verbindungsparametern im Format `/dbs/<database name>/colls/<collection or graph name>` fehlerhaft ist.|
| **408** | `"Server timeout"` gibt an, dass die Durchlaufzeit mehr als **30 Sekunden** betrug und vom Server abgebrochen wurde. Optimieren Sie Ihre Durchläufe für eine schnelle Ausführung, indem Sie Vertices oder Edges bei jedem Hop des Durchlaufs filtern, um den Suchbereich einzugrenzen.|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` Dies tritt normalerweise auf, wenn ein Scheitelpunkt oder eine Kante mit einem Bezeichner bereits im Diagramm vorhanden ist.| 
| **412** | Der Statuscode wird durch die Fehlermeldung `"PreconditionFailedException": One of the specified pre-condition is not met` ergänzt. Dieser Fehler ist ein Hinweis auf eine Verletzung der optimistischen Nebenläufigkeitskontrolle zwischen dem Lesen eines Edge oder Vertex und dem Zurückschreiben in den Speicher nach der Änderung. Die häufigsten Fälle, in denen dieser Fehler auftritt, sind Eigenschaftsänderungen, z.B. `g.V('identifier').property('name','value')`. Die Gremlin-Engine liest den Vertex, ändert ihn und schreibt ihn dann zurück. Wenn es einen weiteren Durchlauf gibt, der parallel ausgeführt wird und versucht, den gleichen Vertex oder Edge zu schreiben, erhält einer der Vorgänge diesen Fehler. Die Anwendung sollte den Durchlauf erneut an den Server übermitteln.| 
| **429** | Die Anforderung wurde gedrosselt und sollte nach dem Zeitraum wiederholt werden, der mit dem Wert unter **x-ms-retry-after-ms** festgelegt ist.| 
| **500** | Eine Fehlermeldung der Art `"NotFoundException: Entity with the specified id does not exist in the system."` weist darauf hin, dass eine neue Datenbank bzw. Sammlung mit dem gleichen Namen erstellt wurde. Dieser Fehler wird innerhalb von fünf Minuten entfernt, wenn die Änderung verbreitet wird, und Caches in den unterschiedlichen Cosmos DB-Komponenten werden ungültig gemacht. Verwenden Sie immer eindeutige Namen für Datenbanken und Sammlungen, um dieses Problem zu vermeiden.| 
| **1000** | Dieser Statuscode wird zurückgegeben, wenn der Server eine Nachricht erfolgreich analysiert hat, aber keine Ausführung erfolgen konnte. Dies weist in der Regel auf ein Problem mit der Abfrage hin.| 
| **1001** | Dieser Code wird zurückgegeben, wenn der Server die Durchlaufausführung abschließt, die Antwort aber nicht zurück an den Client serialisiert. Dieser Fehler kann auftreten, wenn der Durchlauf ein komplexes Ergebnis generiert, das zu groß ist oder nicht der TinkerPop-Protokollspezifikation entspricht. Die Anwendung sollte den Durchlauf vereinfachen, wenn dieser Fehler auftritt. | 
| **1003** | `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"` wird zurückgegeben, wenn der Durchlauf die zulässige Speichergrenze überschreitet. Die Speichergrenze beträgt **2 GB** pro Durchlauf.| 
| **1004** | Dieser Statuscode gibt eine falsch formatierte Graphanforderung an. Die Anforderung kann falsch formatiert sein, wenn die Deserialisierung fehlschlägt, der Nicht-Werttyp als Werttyp deserialisiert oder ein nicht unterstützter Gremlin-Vorgang angefordert wird. Die Anwendung sollte die Anforderung nicht wiederholen, da sie nicht erfolgreich sein wird. | 
| **1007** | Dieser Statuscode wird normalerweise mit der Fehlermeldung `"Could not process request. Underlying connection has been closed."` zurückgegeben. Dieser Fall kann auftreten, wenn der Clienttreiber versucht, eine Verbindung zu verwenden, die vom Server geschlossen wird. Die Anwendung sollte den Durchlauf mit einer anderen Verbindung wiederholen.
| **1008** | Der Cosmos DB Gremlin-Server kann Verbindungen beenden, um den Datenverkehr im Cluster auszugleichen. Clienttreiber sollten diesen Fall verarbeiten und nur aktive Verbindungen verwenden, um Anforderungen an den Server zu senden. Gelegentlich erkennen Clienttreiber ggf. nicht, dass die Verbindung geschlossen wurde. Wenn die Anwendung auf einen Fehler stößt (`"Connection is too busy. Please retry after sometime or open more connections."`), sollte sie den Durchlauf mit einer anderen Verbindung wiederholen.

## <a name="samples"></a>Beispiele

Eine Clientbeispielanwendung auf Basis von Gremlin.Net, die ein Statusattribut liest:

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

Ein Beispiel, das zeigt, wie ein Statusattribut aus dem Gremlin Java-Client gelesen werden kann:

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>Nächste Schritte
* [HTTP-Statuscodes für Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [Allgemeine Azure Cosmos DB REST-Antwortheader](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [Anforderungen für den TinkerPop Graph-Treiberanbieter]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)