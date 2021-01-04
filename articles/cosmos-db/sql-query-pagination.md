---
title: Paginierung in Azure Cosmos DB
description: Erfahren Sie etwas über Pagingkonzepte und Fortsetzungstoken.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.openlocfilehash: c49fee169b7bd01ee7cf8a6d539c2125cf6568b3
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96545314"
---
# <a name="pagination-in-azure-cosmos-db"></a>Paginierung in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In Azure Cosmos DB können Abfragen mehrere Ergebnisseiten enthalten. In diesem Dokument werden die Kriterien erläutert, mit denen die Abfrage-Engine von Azure Cosmos DB entscheidet, ob Abfrageergebnisse auf mehrere Seiten aufgeteilt werden. Sie können optional Fortsetzungstoken verwenden, um Abfrageergebnisse zu verwalten, die mehrere Seiten umfassen.

## <a name="understanding-query-executions"></a>Grundlegendes zu Abfrageausführungen

Manchmal werden Abfrageergebnisse auf mehrere Seiten aufgeteilt. Die Ergebnisse der einzelnen Seiten werden durch eine separate Abfrageausführung generiert. Wenn Abfrageergebnisse nicht in einer einzelnen Ausführung zurückgegeben werden können, teilt Azure Cosmos DB die Ergebnisse automatisch auf mehrere Seiten auf.

Sie können die maximale Anzahl von Elementen angeben, die von einer Abfrage zurückgegeben werden, indem Sie den `MaxItemCount`festlegen. Der `MaxItemCount` wird pro Anforderung angegeben und besagt, dass die Abfrage-Engine maximal diese Anzahl von Elementen zurückgibt. Sie können `MaxItemCount` auf `-1` festlegen, wenn Sie keine Beschränkung für die Anzahl der Ergebnisse pro Abfrageausführung festlegen möchten.

Darüber hinaus gibt es weitere Gründe dafür, dass die Abfrage-Engine Abfrageergebnisse möglicherweise auf mehrere Seiten aufteilen muss. Dazu gehören:

- Der Container wurde gedrosselt, und es waren keine RUs verfügbar, um mehr Abfrageergebnisse zurückzugeben.
- Die Antwort der Abfrageausführung war zu groß.
- Die Dauer der Abfrageausführung war zu lang.
- Es war für die Abfrage-Engine effizienter, die Ergebnisse in zusätzlichen Ausführungen zurückzugeben.

Die Anzahl der Elemente, die pro Abfrageausführung zurückgegeben werden, ist immer kleiner oder gleich `MaxItemCount`. Es ist jedoch möglich, dass andere Kriterien die Anzahl der Ergebnisse, die von der Abfrage zurückgegeben werden konnten, eingeschränkt haben. Wenn Sie dieselbe Abfrage mehrmals ausführen, ist die Anzahl der Seiten möglicherweise nicht immer gleich. Wenn beispielsweise eine Abfrage gedrosselt wird, sind möglicherweise weniger Ergebnisse pro Seite verfügbar, sodass die Abfrage zusätzliche Seiten enthält. In einigen Fällen kann es auch vorkommen, dass Ihre Abfrage eine leere Ergebnisseite zurückgibt.

## <a name="handling-multiple-pages-of-results"></a>Verarbeiten mehrerer Ergebnisseiten

Um akkurate Abfrageergebnisse zu gewährleisten, sollten Sie alle Seiten durchlaufen. Sie sollten weitere Abfragen ausführen, bis keine zusätzlichen Seiten mehr vorhanden sind.

Im Folgenden finden Sie einige Beispiele für die Verarbeitung der Ergebnisse von Abfragen mit mehreren Seiten:

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L280)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176)
- [Node.js SDK](https://github.com/Azure/azure-sdk-for-js/blob/83fcc44a23ad771128d6e0f49043656b3d1df990/sdk/cosmosdb/cosmos/samples/IndexManagement.ts#L128-L140)
- [Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/examples.py#L89)

## <a name="continuation-tokens"></a>Fortsetzungstoken

Sie können im .NET SDK und im Java SDK optional Fortsetzungstoken als Lesezeichen für den Fortschritt der Abfrage verwenden. Azure Cosmos DB-Abfrageausführungen sind auf der Serverseite zustandslos und können jederzeit mit dem Fortsetzungstoken fortgesetzt werden. Fortsetzungstoken werden im Node.js SDK nicht unterstützt. Beim Python SDK werden sie für Abfragen in einer einzelnen Partition unterstützt, und der PK muss im Options-Objekt angegeben werden, da das Vorhandensein in der Abfrage selbst nicht ausreicht.

Im Folgenden finden Sie ein Beispiel für die Verwendung von Fortsetzungstoken:

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L699-L734)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216)
- [Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/test/test_query.py#L533)

Wenn die Abfrage ein Fortsetzungstoken zurückgibt, stehen zusätzliche Abfrageergebnisse bereit.

In der REST-API von Azure Cosmos DB können Sie Fortsetzungstoken mit dem `x-ms-continuation`-Header verwalten. Wie bei Abfragen mit dem .NET oder Java SDK bedeutet ein nicht leerer `x-ms-continuation`-Antwortheader, dass die Abfrage zusätzliche Ergebnisse aufweist.

Sofern Sie dieselbe SDK-Version verwenden, laufen Fortsetzungstoken nie ab. Sie können optional [die Größe eines Fortsetzungstokens einschränken](/dotnet/api/microsoft.azure.documents.client.feedoptions.responsecontinuationtokenlimitinkb?preserve-view=true&view=azure-dotnet#Microsoft_Azure_Documents_Client_FeedOptions_ResponseContinuationTokenLimitInKb). Unabhängig von der Datenmenge oder der Anzahl physischer Partitionen auf Ihrem Container geben Abfragen ein einzelnes Fortsetzungstoken zurück.

Sie können keine Fortsetzungstoken für Abfragen mit [GROUP BY](sql-query-group-by.md) oder [DISTINCT](sql-query-keywords.md#distinct) verwenden, da diese Abfragen eine beträchtliche Zustandsmenge speichern müssten. Bei Abfragen mit `DISTINCT` können Sie Fortsetzungstoken verwenden, wenn Sie der Abfrage `ORDER BY` hinzufügen.

Im Folgenden finden Sie ein Beispiel für eine Abfrage mit `DISTINCT`, die ein Fortsetzungstoken verwenden könnte:

```sql
SELECT DISTINCT VALUE c.name
FROM c
ORDER BY c.name
```

## <a name="next-steps"></a>Nächste Schritte

- [Einführung in Azure Cosmos DB](introduction.md)
- [Azure Cosmos DB-.NET-Beispiele](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [ORDER BY-Klausel](sql-query-order-by.md)
