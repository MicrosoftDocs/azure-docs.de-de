---
title: Änderungsfeed in der Azure Cosmos DB-API für Cassandra
description: Erfahren Sie, wie Sie den Änderungsfeed in der Azure Cosmos DB-API für Cassandra verwenden, um die an Ihren Daten vorgenommenen Änderungen abzurufen.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: 5252993c560b30836c65bf2d395173513993c307
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092397"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Änderungsfeed in der Azure Cosmos DB-API für Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Die Unterstützung für den [Änderungsfeed](change-feed.md) in der Azure Cosmos DB-API für Cassandra steht über die Abfrageprädikate in der Cassandra Query Language (CQL) zur Verfügung. Mit diesen Prädikatsbedingungen können Sie die Änderungsfeed-API abfragen. Anwendungen können die an einer Tabelle vorgenommenen Änderungen mithilfe des Primärschlüssels (auch als Partitionsschlüssel bezeichnet) abrufen, der in CQL erforderlich ist. Sie können dann anhand der Ergebnisse weitere Maßnahmen ergreifen. Änderungen an den Zeilen in der Tabelle werden in der Reihenfolge ihres Änderungszeitpunkts und die Sortierreihenfolge pro Partitionsschlüssel erfasst.

Im folgenden Beispiel wird gezeigt, wie Sie einen Änderungsfeed für alle Zeilen in einer Cassandra-API-Keyspace-Tabelle mithilfe von .NET abrufen. Das Prädikat COSMOS_CHANGEFEED_START_TIME() wird direkt in CQL verwendet, um Elemente im Änderungsfeed ab einer angegebenen Startzeit (in diesem Fall das aktuelle Datum und die Uhrzeit) abzufragen. Sie können das ganze Beispiel für C# [hier](/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/) und für Java [hier](https://github.com/Azure-Samples/cosmos-changefeed-cassandra-java) herunterladen.

In jeder Iterationen wird die Abfrage mithilfe des Pagingzustands am letzten Punkt fortgesetzt, an dem Änderungen gelesen wurden. Sie können einen kontinuierlichen Stream neuer Änderungen an der Tabelle im Keyspace erkennen. Es werden Änderungen an Zeilen angezeigt, die eingefügt oder aktualisiert wurden. Die Überwachung auf Löschvorgänge mithilfe des Änderungsfeeds wird in der Cassandra-API derzeit nicht unterstützt.

# <a name="java"></a>[Java](#tab/java)

```java
        Session cassandraSession = utils.getSession();

        try {
              DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");  
               LocalDateTime now = LocalDateTime.now().minusHours(6).minusMinutes(30);  
               String query="SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME()='" 
                    + dtf.format(now)+ "'";
               
             byte[] token=null; 
             System.out.println(query); 
             while(true)
             {
                 SimpleStatement st=new  SimpleStatement(query);
                 st.setFetchSize(100);
                 if(token!=null)
                     st.setPagingStateUnsafe(token);
                 
                 ResultSet result=cassandraSession.execute(st) ;
                 token=result.getExecutionInfo().getPagingState().toBytes();
                 
                 for(Row row:result)
                 {
                     System.out.println(row.getString("user_name"));
                 }
             }
                    

        } finally {
            utils.close();
            LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL");
        }

```

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```
---

Um die Änderungen an einer einzelnen Zeile nach Primärschlüssel abzurufen, können Sie der Abfrage den Primärschlüssel hinzufügen. Im folgenden Beispiel wird gezeigt, wie Sie Änderungen für die Zeile mit „user_id = 1“ nachverfolgen.

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

# <a name="java"></a>[Java](#tab/java)

```java
    String query="SELECT * FROM uprofile.user where user_id=1 and COSMOS_CHANGEFEED_START_TIME()='" 
                    + dtf.format(now)+ "'";
    SimpleStatement st=new  SimpleStatement(query);
```
---
## <a name="current-limitations"></a>Aktuelle Einschränkungen

Es gelten die folgenden Einschränkungen, wenn der Änderungsfeed mit der Cassandra-API verwendet wird:

* Einfügungen und Aktualisierungen werden zurzeit unterstützt. Löschvorgänge werden noch nicht unterstützt. Als Problemumgehung können Sie den gelöschten Zeilen eine schwache Markierung hinzufügen. Fügen Sie z. B. in der Zeile ein Feld mit dem Namen „deleted“ hinzu, und legen Sie es auf „true“ fest.
* Das letzte Update wird wie in der SQL-Kern-API beibehalten, und zwischengeschaltete Aktualisierungen der Entität sind nicht verfügbar.


## <a name="error-handling"></a>Fehlerbehandlung

Die folgenden Fehlercodes und -meldungen werden bei der Verwendung des Änderungsfeeds in der Cassandra-API unterstützt:

* **HTTP-Fehlercode 429:** Wenn der Änderungsfeed gedrosselt ist, wird eine leere Seite zurückgegeben.

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Ressourcen für die Cassandra-API von Azure Cosmos DB mithilfe von Azure Resource Manager-Vorlagen](./templates-samples-cassandra.md)