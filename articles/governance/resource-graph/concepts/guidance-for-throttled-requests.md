---
title: Leitfaden für gedrosselte Anforderungen
description: Lernen Sie, parallel zu gruppieren, zu staffeln, zu paginieren und abzufragen, um zu vermeiden, dass Anforderungen durch Azure Resource Graph gedrosselt werden.
ms.date: 01/27/2021
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: ddd3cf4d411733e831c94039c3bc9aeaf0e95271
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917706"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Leitfaden für gedrosselte Anforderungen in Azure Resource Graph

Beim programmgesteuerten Erstellen und der häufigen Nutzung von Azure Resource Graph-Daten sollten Sie sich Gedanken dazu machen, welche Auswirkungen gedrosselte Anforderungen auf die Ergebnisse der Abfragen haben. Indem Sie die Anforderungsweise von Daten verändern, können Sie und Ihre Organisation die Drosselung von Anforderungen vermeiden und den Flow von zeitgenauen Daten zu Ihren Azure-Ressourcen aufrechterhalten.

In diesem Artikel werden vier Bereiche und Muster behandelt, die im Zusammenhang mit der Erstellung von Abfragen in Azure Resource Graph stehen:

- Grundlegendes zu Drosselungsheadern
- Gruppieren von Abfragen
- Staffelungsabfragen
- Auswirkungen der Paginierung

## <a name="understand-throttling-headers"></a>Grundlegendes zu Drosselungsheadern

Azure Resource Graph ordnet, abhängig von einem Zeitfenster, jedem Benutzer eine Kontingentnummer zu. Ein Benutzer kann innerhalb eines Zeitfensters von 5 Sekunden beispielsweise höchstens 15 Abfragen ohne Drosselung senden. Der Kontingentwert wird von vielen Faktoren bestimmt und kann verändert werden.

In jeder Abfrageantwort fügt Azure Resource Graph zwei Drosselungsheader ein:

- `x-ms-user-quota-remaining` (int): Das verbleibende Ressourcenkontingent für den Benutzer. Dieser Wert entspricht der Anzahl von Abfragen.
- `x-ms-user-quota-resets-after` (hh:mm:ss): Der Zeitraum, bis die Kontingentnutzung eines Benutzers zurückgesetzt wird.

Wenn ein Sicherheitsprinzipal Zugriff auf mehr als 5000 Abonnements innerhalb des [Abfragebereichs](./query-language.md#query-scope) der Mandanten- oder Verwaltungsgruppe hat, ist die Antwort auf die ersten 5000 Abonnements begrenzt, und der Header `x-ms-tenant-subscription-limit-hit` wird als `true` zurückgegeben.

Zur Veranschaulichung der Funktionsweise von Headern sehen wir uns eine Abfrageantwort mit dem Header und den Werten von `x-ms-user-quota-remaining: 10` und `x-ms-user-quota-resets-after: 00:00:03` an.

- In den nächsten 3 Sekunden werden höchstens 10 Abfragen ohne Drosselung übermittelt.
- In 3 Sekunden werden die Werte von `x-ms-user-quota-remaining` und `x-ms-user-quota-resets-after` auf `15` bzw. `00:00:05` zurückgesetzt.

Ein Beispiel für die Verwendung der Header zum Durchführen eines _Backoff_ für Abfrageanforderungen finden Sie im Beispiel in [Parallele Abfrage](#query-in-parallel).

## <a name="grouping-queries"></a>Gruppieren von Abfragen

Das Gruppieren von Abfragen nach Abonnement, Ressourcengruppe oder einer einzelnen Ressource ist effizienter als das Parallelisieren von Abfragen. Die Kontingentkosten für eine größere Abfrage sind oft geringer als die Kontingentkosten für viele kleine und gezielte Abfragen. Die Gruppengröße sollte kleiner als _300_ sein.

- Beispiel für einen schlecht optimierten Batchverarbeitungsansatz

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "Resoures | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Beispiel 1 für einen optimierten Gruppierungsansatz

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int groupSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / groupSize; ++i)
  {
      var currSubscriptionGroup = subscriptionIds.Skip(i * groupSize).Take(groupSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionGroup,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Beispiel 2 für einen optimierten Gruppierungsansatz zum Abrufen mehrerer Ressourcen in einer Abfrage

  ```kusto
  Resources | where id in~ ({resourceIdGroup}) | project name, type
  ```

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int groupSize = 100;
  for (var i = 0; i <= resourceIds.Count / groupSize; ++i)
  {
      var resourceIdGroup = string.Join(",",
          resourceIds.Skip(i * groupSize).Take(groupSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIdGroup}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Staffelungsabfragen

Aufgrund der Art und Weise, wie die Drosselung erzwungen wird, werden gestaffelte Abfragen empfohlen. Dies bedeutet, dass Sie die Abfragen in vier Zeitfenster von jeweils 5 Sekunden staffeln, anstatt 60 Abfragen gleichzeitig zu senden:

- Nicht gestaffelter Abfragezeitplan

  | Abfrageanzahl         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Zeitintervall (in Sekunden) | 0-5 | 5-10 | 10-15 | 15 – 20 |

- Gestaffelter Abfragezeitplan

  | Abfrageanzahl         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Zeitintervall (in Sekunden) | 0-5 | 5-10 | 10-15 | 15 – 20 |

Es folgt ein Beispiel der Verwendung von Drosselungsheadern beim Abfragen von Azure Resource Graph:

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>Gleichzeitige Abfrage

Auch wenn die Gruppierung gegenüber der Parallelisierung empfohlen wird, gibt es Situationen, in denen Abfragen nicht einfach gruppiert werden können. In diesen Fällen sollten Sie Azure Resource Graph abfragen, indem Sie mehrere Abfragen parallel senden. Es folgt ein auf den Drosselungsheadern in solchen Szenarios basierendes Beispiel für _Backoff_:

```csharp
IEnumerable<IEnumerable<string>> queryGroup = /* Groups of queries  */
// Run groups in parallel.
await Task.WhenAll(queryGroup.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>Paginierung

Da Azure Resource Graph in einer einzelnen Abfrageantwort höchstens 1000 Einträge zurückgibt, müssen Sie Ihre Abfragen möglicherweise [paginieren](./work-with-data.md#paging-results), um das vollständige Dataset zu erhalten, das Sie suchen. Einige Azure Resource Graph-Clients verarbeiten die Paginierung jedoch unterschiedlich.

- C# SDK

  Wenn Sie das „ResourceGraph“-SDK verwenden, erfolgt die Paginierung durch das Übergeben des von der vorherigen Abfrageantwort an die paginierte Abfrage zurückgegebenen Skiptokens. Das bedeutet, dass Sie Ergebnisse aus allen paginierten Aufrufen sammeln und diese am Ende vereinen müssen. In diesem Fall beansprucht jede gesendete paginierte Abfrage ein Abfragekontingent:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "Resources | project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- Azure CLI/Azure PowerShell

  Wenn Sie die Azure CLI oder Azure PowerShell verwenden, werden Abfragen an Azure Resource Graph automatisch paginiert, um maximal 5000 Einträge zu fetchen. Die Abfrageergebnisse geben eine kombinierte Liste mit Einträgen aus allen paginierten Aufrufen zurück. In diesem Fall nutzt eine einzelne paginierte Abfrage (je nach Anzahl der Einträge im Abfrageergebnis) möglicherweise mehr als ein Abfragekontingent. Im folgenden Beispiel werden bei der einzelnen Ausführung einer Abfrage etwa bis zu fünf Abfragekontingente verwendet:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>Werden die Anforderungen weiterhin gedrosselt?

Kontaktieren Sie das Microsoft-Team über [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com), wenn die Anforderungen nach dem Ausführen der oben genannten Empfehlungen auch weiterhin gedrosselt werden.

Geben Sie die folgenden Informationen an:

- Ihren spezifischen Anwendungsfall und Anforderungen an den Gerätetreiber Ihres Unternehmens für ein höheres Drosselungslimit.
- Auf wie viele Ressourcen haben Sie Zugriff? Wie viele davon werden von einer einzelnen Abfrage zurückgegeben?
- Welche Arten von Ressourcen möchten Sie verwenden?
- Welches Abfragemuster verwenden Sie? X Abfragen pro Y Sekunden usw.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur verwendeten Sprache finden Sie unter [Einfache Abfragen](../samples/starter.md).
- Informationen zur anspruchsvolleren Nutzung finden Sie unter [Erweiterte Abfragen](../samples/advanced.md).
- Erfahren Sie mehr über das [Erkunden von Ressourcen](explore-resources.md).