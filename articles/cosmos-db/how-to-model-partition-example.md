---
title: Modellieren und Partitionieren von Daten in Azure Cosmos DB anhand eines praktischen Beispiels
description: Erfahren Sie anhand eines praktischen Beispiels, wie Sie Daten mithilfe der Azure Cosmos DB-Kern-API modellieren und partitionieren.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/23/2019
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: c3cdc0a9fb9fa236fae37a52194f446278a42f72
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616245"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>Modellieren und Partitionieren von Daten in Azure Cosmos DB anhand eines praktischen Beispiels
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Dieser Artikel veranschaulicht auf Grundlage verschiedener Azure Cosmos DB-Konzepte wie [Datenmodellierung](modeling-data.md), [Partitionierung](partitioning-overview.md) und [bereitgestelltem Durchsatz](request-units.md) die Erstellung eines Datenentwurfs anhand einer praktischen Übung.

Wenn Sie normalerweise mit relationalen Datenbanken arbeiten, haben Sie sich wahrscheinlich bestimmte Gewohnheiten und Vorstellungen im Hinblick auf das Entwerfen eines Datenmodells angeeignet. Die meisten dieser bewährten Methoden lassen sich aufgrund der spezifischen Einschränkungen, aber auch der einzigartigen Vorteile von Azure Cosmos DB, nicht gut umsetzen und können dazu führen, dass Sie sich für suboptimale Lösungen entscheiden. Dieser Artikel führt Sie Schritt für Schritt durch den gesamten Modellierungsprozess für einen realen Anwendungsfall in Azure Cosmos DB – von der Elementmodellierung bis hin zur Zusammenstellung der Entitäten und Containerpartitionierung.

[Laden Sie Quellcode aus der Community herunter, oder sehen Sie ihn sich an](https://github.com/jwidmer/AzureCosmosDbBlogExample), der die Konzepte aus diesem Artikel veranschaulicht. Dieses Codebeispiel wurde von einem Mitglied der Community beigesteuert. Das Azure Cosmos DB-Team bietet hierzu keinen Support an.

## <a name="the-scenario"></a>Szenario

In dieser Übung befassen wir uns mit einer Blogplattform, auf der *Benutzer* die Möglichkeit haben, *Beiträge* zu erstellen. Benutzer können diese Beiträge auch *mit „Gefällt mir“ markieren* und ihnen *Kommentare* hinzufügen.

> [!TIP]
> Einige Wörter sind hier *kursiv* hervorgehoben. Sie stellen die Art von „Dingen“ dar, die von unserem Modell bearbeitet werden sollen.

Weitere Anforderungen für unsere Spezifikation:

- Auf einer Startseite wird ein Feed der kürzlich erstellten Beiträge angezeigt.
- Wir können alle Beiträge für einen Benutzer, alle Kommentare für einen Beitrag und alle „Gefällt mir“-Markierungen für einen Beitrag abrufen.
- Beiträge werden mit dem Benutzernamen der Autoren sowie der Anzahl von Kommentaren und „Gefällt mir“-Markierungen zurückgegeben.
- Kommentare und „Gefällt mir“-Markierungen werden ebenfalls mit dem Benutzernamen des Benutzers, von denen sie erstellt wurden, zurückgegeben.
- Bei der Anzeige in Listenform muss für Beiträge nur eine gekürzte Zusammenfassung des Inhalts angezeigt werden.

## <a name="identify-the-main-access-patterns"></a>Identifizieren der wichtigsten Zugriffsmuster

Zunächst strukturieren wir unsere anfängliche Spezifikation, indem wir die Zugriffsmuster der Lösung ermitteln. Das Verständnis der Anforderungen, die das Modell verarbeiten muss, ist beim Entwerfen eines Datenmodells für Azure Cosmos DB wichtig, um sicherzustellen, dass das Modell diese Anforderungen effizient verarbeitet.

Um die Verfolgung des Prozesses zu erleichtern, kategorisieren wir die verschiedenen Anforderungen als Befehle oder Abfragen. Dazu greifen wir auf das Vokabular von [CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation) (Command and Query Responsibility Segregation) zurück: Befehle sind Schreibanforderungen (d. h. Absichten, das System zu aktualisieren), und Abfragen sind schreibgeschützte Anforderungen.

Hier sehen Sie die Liste der Anforderungen, die die Plattform verfügbar machen muss:

- **[C1]** : Einen Benutzer erstellen/bearbeiten
- **[Q1]** : Einen Benutzer abrufen
- **[C2]** : Einen Beitrag erstellen/bearbeiten
- **[Q2]** : Einen Beitrag abrufen
- **[Q3]** : Beiträge eines Benutzers in Kurzform auflisten
- **[C3]** : Einen Kommentar erstellen
- **[Q4]** : Kommentare für einen Beitrag auflisten
- **[C4]** : Einen Beitrag mit „Gefällt mir“ markieren
- **[Q5]** : „Gefällt mir“-Markierungen eines Beitrags auflisten
- **[Q6]** : Die *x* neuesten Beiträge in Kurzform auflisten (Feed)

Zu diesem Zeitpunkt haben wir uns noch keine Gedanken über die Details gemacht, die die einzelnen Entitäten (Benutzer, Beitrag usw.) enthalten. In der Regel ist dies einer der ersten Schritte beim Entwerfen eines relationalen Speichers, da wir ermitteln müssen, welchen Tabellen, Spalten, Fremdschlüsseln usw. diese Entitäten entsprechen. Bei einer Dokumentdatenbank, die bei Schreibvorgängen kein Schema erzwingt, ist dies weniger problematisch.

Die Zugriffsmuster von Beginn an zu identifizieren, ist vor allem deswegen wichtig, weil diese Liste von Anforderungen unsere Testsammlung sein wird. Jedes Mal, wenn wir das Datenmodell durchlaufen, sehen wir uns jede der Anforderungen an und überprüfen ihre Leistung und Skalierbarkeit.

## <a name="v1-a-first-version"></a>Version 1: Die erste Version

Wir beginnen mit zwei Containern: `users` und `posts`.

### <a name="users-container"></a>Container „users“

In diesem Container werden nur Benutzerelemente gespeichert:

```json
{
    "id": "<user-id>",
    "username": "<username>"
}
```

Wir partitionieren diesen Container nach `id`, d. h. jede logische Partition innerhalb des Containers enthält nur ein Element.

### <a name="posts-container"></a>Container „posts“

Dieser Container hostet Beiträge, Kommentare und „Gefällt mir“-Markierungen:

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "title": "<post-title>",
    "content": "<post-content>",
    "creationDate": "<post-creation-date>"
}

{
    "id": "<comment-id>",
    "type": "comment",
    "postId": "<post-id>",
    "userId": "<comment-author-id>",
    "content": "<comment-content>",
    "creationDate": "<comment-creation-date>"
}

{
    "id": "<like-id>",
    "type": "like",
    "postId": "<post-id>",
    "userId": "<liker-id>",
    "creationDate": "<like-creation-date>"
}
```

Wir partitionieren diesen Container nach `postId`, d. h. jede logische Partition innerhalb des Containers enthält einen Beitrag, alle Kommentare zu diesem Beitrag und alle „Gefällt mir“-Markierungen des Beitrags.

Für die in diesem Container gespeicherten Elemente haben wir eine `type`-Eigenschaft hinzugefügt, um zwischen den drei Entitätstypen zu unterscheiden, die der Container hostet.

Zudem haben wir uns aus den folgenden Gründen entschieden, auf zugehörige Daten zu verweisen, anstatt sie einzubetten (Details zu diesen Konzepten finden Sie in [diesem Abschnitt](modeling-data.md)):

- Es gibt keine Obergrenze für die Anzahl von Beiträgen, die ein Benutzer erstellen kann.
- Beiträge können beliebig lang sein.
- Es gibt keine Obergrenze für die Anzahl von Kommentaren und „Gefällt mir“-Markierungen eines Beitrags.
- Es soll möglich sein, einem Beitrag einen Kommentar hinzuzufügen oder ihn mit „Gefällt mir“ zu markieren, ohne den Beitrag selbst zu aktualisieren.

## <a name="how-well-does-our-model-perform"></a>Wie gut funktioniert das Modell?

Der nächste Schritt besteht darin, die Leistung und Skalierbarkeit der ersten Version zu bewerten. Für jede der zuvor identifizierten Anforderungen messen wir die Wartezeit und die von der Anforderung verbrauchte Anzahl von Anforderungseinheiten (Request Unit, RU). Diese Messung wird für ein Dummydataset ausgeführt, das 100.000 Benutzer mit fünf bis 50 Beiträgen pro Benutzer und bis zu 25 Kommentare und 100 „Gefällt mir“-Markierungen pro Beitrag enthält.

### <a name="c1-createedit-a-user"></a>[C1]: Einen Benutzer erstellen/bearbeiten

Diese Anforderung ist einfach zu implementieren, da lediglich ein Element im Container `users` erstellt oder aktualisiert wird. Die Anforderungen werden dank des Partitionsschlüssels `id` gut auf alle Partitionen verteilt.

:::image type="content" source="./media/how-to-model-partition-example/V1-C1.png" alt-text="Schreiben eines einzelnen Elements in den Container „users“" border="false":::

| **Latenz** | **Verbrauchte RUs** | **Leistung** |
| --- | --- | --- |
| 7 ms | 5,71 RUs | ✅ |

### <a name="q1-retrieve-a-user"></a>[Q1]: Einen Benutzer abrufen

Zum Abrufen eines Benutzers wird das entsprechende Element aus dem Container `users` gelesen.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q1.png" alt-text="Abrufen eines einzelnen Elements aus dem Container „users“" border="false":::

| **Latenz** | **Verbrauchte RUs** | **Leistung** |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="c2-createedit-a-post"></a>[C2]: Einen Beitrag erstellen/bearbeiten

Ähnlich wie bei **[C1]** muss bei dieser Anforderung nur in den Container `posts` geschrieben werden.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Schreiben eines einzelnen Elements in den Container „posts“" border="false":::

| **Latenz** | **Verbrauchte RUs** | **Leistung** |
| --- | --- | --- |
| 9 ms | 8,76 RUs | ✅ |

### <a name="q2-retrieve-a-post"></a>[Q2]: Einen Beitrag abrufen

Zunächst rufen wir das entsprechende Dokument aus dem Container `posts` ab. Da gemäß unserer Spezifikation der Benutzername des Beitragsautors und die Anzahl von Kommentaren und „Gefällt mir“-Markierungen des Beitrags aggregiert werden müssen, reicht dies jedoch nicht aus, und es müssen drei weitere SQL-Abfragen ausgegeben werden.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q2.png" alt-text="Abrufen eines Beitrags und Aggregieren der zusätzlichen Daten" border="false":::

Jede der zusätzlichen Abfragen filtert nach dem Partitionsschlüssel des jeweiligen Containers. Dies ist das erwünschte Verhalten, da dadurch die Leistung und Skalierbarkeit maximiert werden. Im Endeffekt müssen jedoch zum Zurückgeben eines einzelnen Beitrags vier Vorgänge ausgeführt werden. Dies werden wir in einer der nächsten Iterationen verbessern.

| **Latenz** | **Verbrauchte RUs** | **Leistung** |
| --- | --- | --- |
| 9 ms | 19,54 RUs | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3]: Beiträge eines Benutzers in Kurzform auflisten

Zunächst müssen wir die gewünschten Beiträge mit einer SQL-Abfrage abrufen, die die entsprechenden Beiträge für den jeweiligen Benutzer abruft. Außerdem müssen wir aber weitere Abfragen ausführen, um den Benutzernamen des Autors und die Anzahl von Kommentaren und „Gefällt mir“-Markierungen zu aggregieren.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q3.png" alt-text="Abrufen aller Beiträge für einen Benutzer und Aggregieren der zusätzlichen Daten" border="false":::

Diese Implementierung hat viele Nachteile:

- Die Abfragen zum Aggregieren der Anzahl von Kommentaren und „Gefällt mir“-Markierungen müssen für jeden von der ersten Abfrage zurückgegebenen Beitrag ausgeführt werden.
- Die Hauptabfrage filtert nicht nach dem Partitionsschlüssel des Containers `posts`, was zu einer Auffächerung und einem Partitionsscan im Container führt.

| **Latenz** | **Verbrauchte RUs** | **Leistung** |
| --- | --- | --- |
| 130 ms | 619,41 RUs | ⚠ |

### <a name="c3-create-a-comment"></a>[C3]: Einen Kommentar erstellen

Ein Kommentar wird erstellt, indem das entsprechende Element in den Container `posts` geschrieben wird.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Schreiben eines einzelnen Elements in den Container „posts“" border="false":::

| **Latenz** | **Verbrauchte RUs** | **Leistung** |
| --- | --- | --- |
| 7 ms | 8,57 RUs | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4]: Kommentare eines Beitrags auflisten

Wir beginnen mit einer Abfrage, die alle Kommentare für den jeweiligen Beitrag abruft. Zudem müssen wir auch hier die Benutzernamen separat für jeden Kommentar aggregieren.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q4.png" alt-text="Abrufen aller Kommentare für einen Beitrag und Aggregieren der zusätzlichen Daten" border="false":::

Obwohl die Hauptabfrage nach dem Partitionsschlüssel des Containers filtert, beeinträchtig das separate Aggregieren der Benutzernamen die Gesamtleistung. Dies werden wir später verbessern.

| **Latenz** | **Verbrauchte RUs** | **Leistung** |
| --- | --- | --- |
| 23 ms | 27,72 RUs | ⚠ |

### <a name="c4-like-a-post"></a>[C4]: Einen Beitrag mit „Gefällt mir“ markieren

Wie bei **[C3]** erstellen wir das entsprechende Element im Container `posts`.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Schreiben eines einzelnen Elements in den Container „posts“" border="false":::

| **Latenz** | **Verbrauchte RUs** | **Leistung** |
| --- | --- | --- |
| 6 ms | 7,05 RUs | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5]: „Gefällt mir“-Markierungen eines Beitrags auflisten

Wie bei **[Q4]** fragen wir die „Gefällt mir“-Markierungen für den Beitrag ab, und anschließend aggregieren wir die zugehörigen Benutzernamen.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q5.png" alt-text="Abrufen aller „Gefällt mir“-Markierungen für einen Beitrag und Aggregieren der zusätzlichen Daten" border="false":::

| **Latenz** | **Verbrauchte RUs** | **Leistung** |
| --- | --- | --- |
| 59 ms | 58,92 RUs | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6]: Die „x“ neuesten Beiträge in Kurzform auflisten (Feed)

Wir rufen die neuesten Beiträge ab, indem wir den Container `posts` absteigend nach Erstellungsdatum sortiert abfragen, und anschließend aggregieren wir die Benutzernamen sowie die Anzahl von Kommentaren und „Gefällt mir“-Markierungen für jeden der Beiträge.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q6.png" alt-text="Abrufen der neuesten Beiträge und Aggregieren der zusätzlichen Daten" border="false":::

Auch hier filtert die ursprüngliche Abfrage nicht nach dem Partitionsschlüssel des Containers `posts`, was zu einer kostspieligen Auffächerung führt. In diesem Fall sind die Auswirkungen sogar noch gravierender, da das Resultset wesentlich größer ist und die Ergebnisse mit einer `ORDER BY`-Klausel sortiert werden, die die Kosten aufgrund der erforderlichen Anforderungseinheiten erhöht.

| **Latenz** | **Verbrauchte RUs** | **Leistung** |
| --- | --- | --- |
| 306 ms | 2\.063,54 RUs | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>Überlegungen zur Leistung von Version 1

Wenn wir uns die Leistungsprobleme im vorherigen Abschnitt ansehen, können wir zwei primäre Arten von Problemen erkennen:

- Manche Anforderungen erfordern mehrere Abfragen, um alle Daten zu erfassen, die zurückgegeben werden müssen.
- Manche Abfragen filtern nicht nach dem Partitionsschlüssel der jeweiligen Container, was zu einer Auffächerung führt und die Skalierbarkeit beeinträchtigt.

Diese Probleme werden wir nun beheben. Beginnen wir mit dem ersten Problem.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>Version 2: Verwenden der Denormalisierung zum Optimieren der Leseabfragen

Der Grund, weshalb in einigen Fällen zusätzliche Anforderungen erforderlich sind, ist, dass die Ergebnisse der ersten Anforderung nicht alle Daten enthalten, die wir zurückgeben müssen. Bei einem nicht relationalen Datenspeicher wie Azure Cosmos DB wird ein solches Problem meist durch Denormalisieren der Daten im Dataset gelöst.

In unserem Beispiel ändern wir die Beitragselemente, indem wir den Benutzernamen des Beitragsautors, die Anzahl von Kommentaren und die Anzahl von „Gefällt mir“-Markierungen hinzufügen:

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

Außerdem ändern wir die Kommentar- und „Gefällt mir“-Markierungselemente, indem wir den Benutzernamen des Benutzers hinzufügen, von denen sie erstellt wurden:

```json
{
    "id": "<comment-id>",
    "type": "comment",
    "postId": "<post-id>",
    "userId": "<comment-author-id>",
    "userUsername": "<comment-author-username>",
    "content": "<comment-content>",
    "creationDate": "<comment-creation-date>"
}

{
    "id": "<like-id>",
    "type": "like",
    "postId": "<post-id>",
    "userId": "<liker-id>",
    "userUsername": "<liker-username>",
    "creationDate": "<like-creation-date>"
}
```

### <a name="denormalizing-comment-and-like-counts"></a>Denormalisieren der Anzahl von Kommentaren und „Gefällt mir“-Markierungen

Wir möchten erreichen, dass bei jeder Hinzufügung eines Kommentars oder einer „Gefällt mir“-Markierung die Anzahl `commentCount` oder `likeCount` im zugehörigen Beitrag erhöht wird. Da unser Container `posts` nach `postId` partitioniert ist, befinden sich das neue Element (Kommentar oder „Gefällt mir“-Markierung) und der dazugehörige Beitrag in der gleichen logischen Partition. Wir können daher eine [gespeicherte Prozedur](stored-procedures-triggers-udfs.md) zum Ausführen des Vorgangs verwenden.

Anstatt lediglich ein neues Element im Container `posts` hinzuzufügen, rufen wir jetzt beim Erstellen eines Kommentars ( **[C3]** ) die folgende gespeicherte Prozedur für den Container auf:

```javascript
function createComment(postId, comment) {
  var collection = getContext().getCollection();

  collection.readDocument(
    `${collection.getAltLink()}/docs/${postId}`,
    function (err, post) {
      if (err) throw err;

      post.commentCount++;
      collection.replaceDocument(
        post._self,
        post,
        function (err) {
          if (err) throw err;

          comment.postId = postId;
          collection.createDocument(
            collection.getSelfLink(),
            comment
          );
        }
      );
    })
}
```

Diese gespeicherte Prozedur akzeptiert die ID des Beitrags und den Text des neuen Kommentars als Parameter und führt anschließend die folgenden Aktionen aus:

- Abrufen des Beitrags
- Erhöhen der Anzahl von `commentCount`
- Ersetzen des Beitrags
- Hinzufügen des neuen Kommentars

Da gespeicherte Prozeduren als unteilbare Transaktionen ausgeführt werden, sind der Wert von `commentCount` und die tatsächliche Anzahl von Kommentaren immer synchron.

Beim Hinzufügen neuer „Gefällt mir“-Markierungen rufen wir eine ähnliche gespeicherte Prozedur auf, um `likeCount` zu erhöhen.

### <a name="denormalizing-usernames"></a>Denormalisieren von Benutzernamen

Benutzernamen erfordern eine andere Herangehensweise, da sich Benutzer nicht nur in unterschiedlichen Partitionen, sondern auch in einem anderen Container befinden. Wenn wir Daten partitions- und containerübergreifend denormalisieren müssen, können wir den [Änderungsfeed](change-feed.md) des Quellcontainers verwenden.

In unserem Beispiel verwenden wir den Änderungsfeed des Containers `users`, um auf jede von Benutzern durchgeführte Aktualisierung des Benutzernamens zu reagieren. Wenn ein Benutzer seinen Benutzernamen aktualisiert, verteilen wir die Änderung, indem wir eine weitere gespeicherte Prozedur für den Container `posts` aufrufen:

:::image type="content" source="./media/how-to-model-partition-example/denormalization-1.png" alt-text="Denormalisieren von Benutzernamen im Container „posts“" border="false":::

```javascript
function updateUsernames(userId, username) {
  var collection = getContext().getCollection();
  
  collection.queryDocuments(
    collection.getSelfLink(),
    `SELECT * FROM p WHERE p.userId = '${userId}'`,
    function (err, results) {
      if (err) throw err;

      for (var i in results) {
        var doc = results[i];
        doc.userUsername = username;

        collection.upsertDocument(
          collection.getSelfLink(),
          doc);
      }
    });
}
```

Diese gespeicherte Prozedur akzeptiert die ID des Benutzers und seinen neuen Benutzernamen als Parameter und führt anschließend die folgenden Aktionen aus:

- Abrufen aller Elemente, die mit der `userId` übereinstimmen (Beiträge, Kommentare oder „Gefällt mir“-Markierungen)
- Folgende Vorgänge für jedes dieser Elemente:
  - Ersetzen von `userUsername`
  - Ersetzen des Elements

> [!IMPORTANT]
> Dieser Vorgang ist kostspielig, da die gespeicherte Prozedur für jede Partition des Containers `posts` ausgeführt werden muss. Wir gehen davon aus, dass die meisten Benutzer bei der Registrierung einen geeigneten Benutzernamen auswählen, den sie niemals ändern, und diese Aktualisierung daher nur sehr selten ausgeführt wird.

## <a name="what-are-the-performance-gains-of-v2"></a>Welche Leistungsverbesserungen bietet Version 2?

### <a name="q2-retrieve-a-post"></a>[Q2]: Einen Beitrag abrufen

Da wir die Denormalisierung hinzugefügt haben, müssen wir zum Verarbeiten dieser Anforderung nur ein einziges Element abrufen.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q2.png" alt-text="Abrufen eines einzelnen Elements aus dem Container „posts“" border="false":::

| **Latenz** | **Verbrauchte RUs** | **Leistung** |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4]: Kommentare eines Beitrags auflisten

Auch in diesem Fall können wir die zusätzlichen Anforderungen zum Abrufen der Benutzernamen weglassen und stattdessen eine einzige Abfrage verwenden, die nach dem Partitionsschlüssel filtert.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q4.png" alt-text="Abrufen aller Kommentare für einen Beitrag" border="false":::

| **Latenz** | **Verbrauchte RUs** | **Leistung** |
| --- | --- | --- |
| 4 ms | 7,72 RUs | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5]: „Gefällt mir“-Markierungen eines Beitrags auflisten

Beim Auflisten der „Gefällt mir“-Markierungen haben wir die gleiche Situation.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q5.png" alt-text="Abrufen aller „Gefällt mir“-Markierungen für einen Beitrag" border="false":::

| **Latenz** | **Verbrauchte RUs** | **Leistung** |
| --- | --- | --- |
| 4 ms | 8,92 RUs | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>Version 3: Sicherstellen der Skalierbarkeit aller Anforderungen

Wenn wir uns die Verbesserungen der Gesamtleistung ansehen, fällt auf, dass wir zwei Anforderungen nicht vollständig optimiert haben: **[Q3]** und **[Q6]** . Dies sind die Anforderungen mit Abfragen, die nicht nach dem Partitionsschlüssel der jeweiligen Container filtern.

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3]: Beiträge eines Benutzers in Kurzform auflisten

Diese Anforderung profitiert bereits von den Verbesserungen, die wir zur Vermeidung zusätzlicher Abfragen in Version 2 eingeführt haben.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q3.png" alt-text="Diagramm mit der Abfrage zum Auflisten von Beiträgen eines Benutzers in Kurzform" border="false":::

Die verbleibende Abfrage filtert jedoch noch immer nicht nach dem Partitionsschlüssel des Containers `posts`.

Die Lösung für dieses Problem ist im Grunde einfach:

1. Die Anforderung *muss* nach der `userId` filtern, da wir alle Beiträge für einen bestimmten Benutzer abrufen wollen.
1. Die Leistung dieser Anforderung ist nicht gut, da sie für den Container `posts` ausgeführt wird, der nicht nach `userId` partitioniert ist.
1. Wir könnten das Leistungsproblem natürlich beheben, indem wir die Anforderung für einen Container ausführen, der nach `userId` partitioniert *ist*.
1. Tatsächlich haben wir bereits einen solchen Container: den Container `users`.

Wir fügen daher eine zweite Denormalisierungsebene hinzu, indem wir alle Posts im Container `users` duplizieren. Auf diese Weise erhalten wir eine Kopie unserer Beiträge, die lediglich nach anderen Dimensionen partitioniert ist. Die Beiträge können dadurch deutlich effizienter anhand der `userId` abgerufen werden.

Der Container `users` enthält jetzt zwei Arten von Elementen:

```json
{
    "id": "<user-id>",
    "type": "user",
    "userId": "<user-id>",
    "username": "<username>"
}

{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

Beachten Sie dabei Folgendes:

- Wir haben ein Feld `type` im Benutzerelement hinzugefügt, um Benutzer von Beiträgen zu unterscheiden.
- Außerdem haben wir ein Feld `userId` im Benutzerelement hinzugefügt, das mit dem Feld `id` redundant, aber erforderlich ist, da der Container `users` jetzt nach `userId` partitioniert ist (und nicht wie zuvor nach `id`).

Um diese Denormalisierung zu erreichen, verwenden wir wieder den Änderungsfeed. Dieses Mal reagieren wir auf den Änderungsfeed des Containers `posts`, um alle neuen oder aktualisierten Beiträge im Container `users` zu verteilen. Da zum Auflisten von Beiträgen nicht der vollständige Inhalt zurückgegeben werden muss, können wir die Beiträge zudem im Prozess abschneiden.

:::image type="content" source="./media/how-to-model-partition-example/denormalization-2.png" alt-text="Denormalisieren von Beiträgen im Container „users“" border="false":::

Jetzt können wir unsere Abfrage an den Container `users` weiterleiten und dabei nach dem Partitionsschlüssel des Containers filtern.

:::image type="content" source="./media/how-to-model-partition-example/V3-Q3.png" alt-text="Abrufen aller Beiträge für einen Benutzer" border="false":::

| **Latenz** | **Verbrauchte RUs** | **Leistung** |
| --- | --- | --- |
| 4 ms | 6,46 RUs | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6]: Die „x“ neuesten Beiträge in Kurzform auflisten (Feed)

Hier haben wir es mit einer ähnlichen Situation zu tun: Auch nach dem Beseitigen der zusätzlichen Abfragen durch das Hinzufügen der Denormalisierung in Version 2 filtert die verbleibende Abfrage nicht nach dem Partitionsschlüssel des Containers:

:::image type="content" source="./media/how-to-model-partition-example/V2-Q6.png" alt-text="Diagramm mit der Abfrage zum Auflisten der „x“ neuesten Beiträge in Kurzform" border="false":::

Gemäß dem obigen Ansatz muss die Anforderung auf eine Partition beschränkt werden, um die Leistung und Skalierbarkeit der Anforderung zu maximieren. Dies ist möglich, da wir nur eine begrenzte Anzahl von Elementen zurückgeben müssen. Zum Auffüllen der Startseite unserer Blogplattform müssen wir nur die 100 neuesten Beiträge abrufen, ohne das gesamte Dataset zu paginieren.

Zum Optimieren dieser letzten Anforderung fügen wir unserem Entwurf daher einen dritten Container hinzu, der einzig zur Verarbeitung dieser Anforderung dient. Wir denormalisieren unsere Beiträge im neuen Container `feed`:

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

Dieser Container ist nach `type` partitioniert, und der Typ ist bei unseren Elementen immer `post`. Dadurch wird sichergestellt, dass sich alle Elemente in diesem Container in derselben Partition befinden.

Zum Erreichen der Denormalisierung müssen wir nur die Änderungsfeedpipeline verknüpfen, die wir zuvor hinzugefügt haben, um die Beiträge an den neuen Container zu verteilen. Wichtig ist hierbei, dass nur die 100 neuesten Beiträge gespeichert werden dürfen. Andernfalls kann der Inhalt des Containers die maximale Größe einer Partition überschreiten. Zu diesem Zweck wird jedes Mal, wenn ein Dokument im Container hinzugefügt wird, ein [nachgestellter Trigger](stored-procedures-triggers-udfs.md#triggers) aufgerufen:

:::image type="content" source="./media/how-to-model-partition-example/denormalization-3.png" alt-text="Denormalisieren von Beiträgen im Container „feed“" border="false":::

Hier sehen Sie den Text des nachgestellten Triggers, der die Auflistung abschneidet:

```javascript
function truncateFeed() {
  const maxDocs = 100;
  var context = getContext();
  var collection = context.getCollection();

  collection.queryDocuments(
    collection.getSelfLink(),
    "SELECT VALUE COUNT(1) FROM f",
    function (err, results) {
      if (err) throw err;

      processCountResults(results);
    });

  function processCountResults(results) {
    // + 1 because the query didn't count the newly inserted doc
    if ((results[0] + 1) > maxDocs) {
      var docsToRemove = results[0] + 1 - maxDocs;
      collection.queryDocuments(
        collection.getSelfLink(),
        `SELECT TOP ${docsToRemove} * FROM f ORDER BY f.creationDate`,
        function (err, results) {
          if (err) throw err;

          processDocsToRemove(results, 0);
        });
    }
  }

  function processDocsToRemove(results, index) {
    var doc = results[index];
    if (doc) {
      collection.deleteDocument(
        doc._self,
        function (err) {
          if (err) throw err;

          processDocsToRemove(results, index + 1);
        });
    }
  }
}
```

Im letzten Schritt müssen Sie die Abfrage an den neuen Container `feed` umleiten:

:::image type="content" source="./media/how-to-model-partition-example/V3-Q6.png" alt-text="Abrufen der neuesten Beiträge" border="false":::

| **Latenz** | **Verbrauchte RUs** | **Leistung** |
| --- | --- | --- |
| 9 ms | 16,97 RUs | ✅ |

## <a name="conclusion"></a>Zusammenfassung

Werfen wir nun einen Blick auf die Verbesserungen der Gesamtleistung und Skalierbarkeit, die wir in den verschiedenen Versionen des Entwurfs eingeführt haben.

| | V1 | V2 | V3 |
| --- | --- | --- | --- |
| **[C1]** | 7 ms/5,71 RUs | 7 ms/5,71 RUs | 7 ms/5,71 RUs |
| **[Q1]** | 2 ms/1 RU | 2 ms/1 RU | 2 ms/1 RU |
| **[C2]** | 9 ms/8,76 RUs | 9 ms/8,76 RUs | 9 ms/8,76 RUs |
| **[Q2]** | 9 ms/19,54 RUs | 2 ms/1 RU | 2 ms/1 RU |
| **[Q3]** | 130 ms/619,41 RUs | 28 ms/201,54 RUs | 4 ms/6,46 RUs |
| **[C3]** | 7 ms/8,57 RUs | 7 ms/15,27 RUs | 7 ms/15,27 RUs |
| **[Q4]** | 23 ms/27,72 RUs | 4 ms/7,72 RUs | 4 ms/7,72 RUs |
| **[C4]** | 6 ms/7,05 RUs | 7 ms/14,67 RUs | 7 ms/14,67 RUs |
| **[Q5]** | 59 ms/58,92 RUs | 4 ms/8,92 RUs | 4 ms/8,92 RUs |
| **[Q6]** | 306 ms/2.063,54 RUs | 83 ms/532,33 RUs | 9 ms/16,97 RUs |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>Wir haben ein Szenario mit vielen Lesevorgängen optimiert.

Möglicherweise haben Sie bemerkt, dass wir uns auf die Verbesserung der Leistung von Leseanforderungen (Abfragen) konzentriert und dafür Abstriche bei den Schreibanforderungen (Befehle) in Kauf genommen haben. In vielen Fällen lösen Schreibvorgänge jetzt eine nachfolgende Denormalisierung über Änderungsfeeds aus, wodurch diese Vorgänge rechenintensiver werden und ihre Materialisierung mehr Zeit in Anspruch nimmt.

Dies wird durch die Tatsache gerechtfertigt, dass eine Blogplattform (wie die meisten Apps für soziale Medien) grundsätzlich leseintensiv ist. Die Anzahl zu verarbeitender Leseanforderungen ist daher sehr viel höher als die Anzahl von Schreibanforderungen. Deshalb ist es sinnvoll, den Kostenaufwand für die Ausführung von Schreibanforderungen zu erhöhen, um im Gegenzug die Kosten von Leseanforderungen zu reduzieren und die Leistung dieser Anforderungen zu verbessern.

Sehen wir uns dazu die größte Optimierung an, die wir erzielt haben: Für **[Q6]** haben wir die Anzahl verbrauchter RUs von 2.000 auf nur 17 RUs reduziert – durch die Denormalisierung von Beiträgen mit einem Kostenaufwand von etwa 10 RUs pro Element. Da wir deutlich mehr Feedanforderungen als Anforderungen zum Erstellen oder Aktualisieren von Beiträgen verarbeiten würden, sind diese Kosten für die Denormalisierung angesichts der Gesamteinsparungen vernachlässigbar.

### <a name="denormalization-can-be-applied-incrementally"></a>Denormalisierung kann inkrementell angewendet werden.

Um die in diesem Artikel behandelten Verbesserungen der Skalierbarkeit zu erreichen, müssen Daten im Dataset denormalisiert und dupliziert werden. Anzumerken ist hierbei, dass diese Optimierungen nicht sofort umgesetzt werden müssen. Abfragen, die nach Partitionsschlüsseln filtern, lassen sich besser skalieren. Partitionsübergreifende Abfragen können jedoch durchaus akzeptabel sein, wenn sie nur selten oder nur für eine begrenzte Menge an Daten aufgerufen werden. Wenn Sie nur einen Prototyp erstellen oder ein Produkt mit einer kleinen und kontrollierbaren Benutzerbasis einführen, können Sie diese Verbesserungen wahrscheinlich vorerst zurückstellen. Wichtig ist es dann jedoch, die Leistung Ihres Modells zu [überwachen](use-metrics.md), damit Sie entscheiden können, ob und wann diese Verbesserungen vorgenommen werden müssen.

Im Änderungsfeed, den wir zum Verteilen der Aktualisierungen an andere Container verwenden, werden diese Aktualisierungen dauerhaft gespeichert. Daher können alle Aktualisierungen seit der Erstellung des Containers und das Bootstrapping der denormalisierten Ansichten als ein einmaliger Aufholvorgang angefordert werden – selbst dann, wenn bereits viele Daten im System vorhanden sind.

## <a name="next-steps"></a>Nächste Schritte

Nach dieser Einführung in die praktische Datenmodellierung und -partitionierung können Sie die hier vorgestellten Konzepte in den folgenden Artikeln nachlesen:

- [Arbeiten mit Datenbanken, Containern und Elementen](account-databases-containers-items.md)
- [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partitionierung in Azure Cosmos DB)
- [Änderungsfeed in Azure Cosmos DB](change-feed.md)
