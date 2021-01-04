---
title: Verwenden der .NET-Bibliothek „BulkExecutor“ für Massenimports und Updatevorgänge in Azure Cosmos DB
description: Führen Sie Massenimport und -aktualisierung der Azure Cosmos DB-Dokumente mithilfe der BulkExecutor-.NET-Bibliothek durch.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: how-to
ms.date: 03/23/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 6f6994717ff4c730fb27bd26c40d199fb198e528
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96019955"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Verwenden der BulkExecutor-.NET-Bibliothek zum Ausführen von Massenvorgängen in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!NOTE]
> Die in diesem Artikel beschriebene BulkExecutor-Bibliothek wird für Anwendungen verwaltet, die das .NET SDK 2.x verwenden. Für neue Anwendungen können Sie die **Massenunterstützung** verwenden, die direkt mit dem [.NET SDK 3.x](tutorial-sql-api-dotnet-bulk-import.md) verfügbar ist und keine externe Bibliothek erfordert. 

> Wenn Sie derzeit die BulkExecutor-Bibliothek verwenden und eine Migration zur Massenunterstützung für das neuere SDK planen, führen Sie die Schritte im [Migrationsleitfaden](how-to-migrate-from-bulk-executor-library.md) aus, um Ihre Anwendung zu migrieren.

Dieses Tutorial enthält Anleitungen zum Verwenden der BulkExecutor-.NET-Bibliothek zum Importieren und Aktualisieren von Dokumenten in einem Azure Cosmos-Container. Informationen zur BulkExecutor-Bibliothek und dazu, wie Sie damit massiven Durchsatz und riesige Speichermengen nutzen können, finden Sie im Artikel [BulkExecutor-Bibliothek – Übersicht](bulk-executor-overview.md). In diesem Tutorial wird eine .NET-Beispielanwendung vorgestellt, die zufällig generierte Dokumente in einen Azure Cosmos-Container massenimportiert. Nach dem Import wird erläutert, wie Sie die importierten Daten per Massenvorgang aktualisieren, indem Sie Patches als Vorgänge angeben, die für bestimmte Dokumentfelder ausgeführt werden sollen.

Zurzeit wird die BulkExecutor-Bibliothek nur von den SQL-API- und Gremlin-API-Konten in Azure Cosmos DB unterstützt. Dieser Artikel beschreibt, wie Sie die BulkExecutor-.NET-Bibliothek mit SQL-API-Konten verwenden. Weitere Informationen zum Verwenden der BulkExecutor-.NET-Bibliothek mit Gremlin-API-Konten finden Sie unter [Ausführen von Massenvorgängen in der Gremlin-API von Azure Cosmos DB](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Voraussetzungen

* Falls Sie Visual Studio 2019 noch nicht installiert haben, können Sie [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) herunterladen und verwenden. Aktivieren Sie beim Setup von Visual Studio die Option „Azure-Entwicklung“.

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.

* Sie können [Azure Cosmos DB ohne ein Azure-Abonnement testen](https://azure.microsoft.com/try/cosmosdb/) – kostenlos und ohne Verpflichtungen. Alternativ dazu können Sie den [Azure Cosmos DB-Emulator](./local-emulator.md) mit dem Endpunkt `https://localhost:8081` verwenden. Den Primärschlüssel finden Sie unter [Authentifizieren von Anforderungen](local-emulator.md#authenticate-requests).

* Erstellen Sie ein Azure Cosmos DB-SQL-API-Konto mithilfe der Schritte, die im Abschnitt [Erstellen eines Datenbankkontos](create-sql-api-dotnet.md#create-account) des .NET-Schnellstarttutorials beschrieben werden.

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Beginnen Sie jetzt mit der Arbeit mit Code, indem Sie eine .NET-Beispielanwendung von GitHub herunterladen. Diese Anwendung führt Massenvorgänge für die in Ihrem Azure Cosmos-Konto gespeicherten Daten aus. Um die Anwendung zu klonen, öffnen Sie eine Eingabeaufforderung, navigieren Sie zu dem Verzeichnis, in das Sie die Anwendung kopieren möchten, und führen Sie den folgenden Befehl aus:

```bash
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Das geklonte Repository enthält zwei Beispiele: BulkImportSample und BulkUpdateSample. Sie können jede der Beispielanwendungen öffnen, die Verbindungszeichenfolgen in der Datei „App.config“ auf die Verbindungszeichenfolgen Ihres Azure Cosmos DB-Kontos aktualisieren, die Lösung erstellen und sie ausführen.

Die Anwendung BulkImportSample generiert zufällig Dokumente und importiert sie per Massenvorgang in Ihr Azure Cosmos-Konto. Die Anwendung BulkUpdateSample aktualisiert die importierten Dokumente per Massenvorgang, indem sie Patches als Vorgänge angibt, die für bestimmte Dokumentfelder ausgeführt werden sollen. In den nächsten Abschnitten sehen wir uns den Code in jeder dieser Beispiel-Apps an.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Massenimport von Daten in ein Azure Cosmos-Konto

1. Navigieren Sie zum Ordner „BulkImportSample“, und öffnen Sie die Datei „BulkImportSample.sln“.  

2. Die Verbindungszeichenfolgen von Azure Cosmos DB werden aus der Datei „App.config“ abgerufen, wie im folgenden Code gezeigt:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Die Massenimportanwendung erstellt eine neue Datenbank und einen Container mit dem Datenbanknamen, dem Containernamen und den Durchsatzwerten, die in der Datei „App.config“ angegeben sind.

3. Als Nächstes wird das DocumentClient-Objekt mit dem direkten TCP-Verbindungsmodus initialisiert:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. Das BulkExecutor-Objekt wird mit hohen Wiederholungswerten für Wartezeit und gedrosselte Anforderungen initialisiert. Dann werden die Werte auf 0 festgelegt, um die Überlastungssteuerung während der Lebensdauer an BulkExecutor zu übergeben.  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. Die Anwendung ruft die BulkImportAsync-API auf. Die .NET-Bibliothek stellt zwei Überladungen der Massenimport-API bereit: Die eine akzeptiert eine Liste von serialisierten JSON-Dokumenten, die andere akzeptiert eine Liste von deserialisierten POCO-Dokumenten. Weitere Informationen zu den Definitionen jeder dieser überladenen Methoden finden Sie in der [API-Dokumentation](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet&preserve-view=true).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **Die BulkImportAsync-Methode akzeptiert die folgenden Parameter:**
   
   |**Parameter**  |**Beschreibung** |
   |---------|---------|
   |enableUpsert    |   Ein Flag zum Aktivieren von Upsert-Vorgängen für die Dokumente. Wenn bereits ein Dokument mit der angegebenen ID vorhanden ist, wird es aktualisiert. Standardmäßig ist „false“ festgelegt.      |
   |disableAutomaticIdGeneration    |    Ein Flag zum Deaktivieren der automatischen Generierung einer ID. Standardmäßig ist „true“ festgelegt.     |
   |maxConcurrencyPerPartitionKeyRange    | Der maximale Grad an Parallelität pro Partitionsschlüsselbereich. Bei einer Festlegung auf NULL verwendet die Bibliothek den Standardwert 20. |
   |maxInMemorySortingBatchSize     |  Die maximale Anzahl von Dokumenten, für die von dem Dokumentenumerator ein Pull ausgeführt wird, der in jeder Phase an den API-Aufruf übergeben wird. Für die speicherinterne Sortierungsphase, die vor dem Massenimport stattfindet, sorgt die Festlegung dieses Parameters auf NULL dafür, dass die Bibliothek den standardmäßigen Mindestwert „(documents.count, 1000000)“ verwendet.       |
   |cancellationToken    |    Das Abbruchtoken für ein ordnungsgemäßes Beenden des Massenimportvorgangs.     |

   **Definition von Massenimport-Antwortobjekten** – das Ergebnis des Massenimport-API-Aufrufs enthält die folgenden Attribute:

   |**Parameter**  |**Beschreibung**  |
   |---------|---------|
   |NumberOfDocumentsImported (long)   |  Die Gesamtanzahl von Dokumenten, die von allen an den Massenimport-API-Aufruf gesendeten Dokumenten erfolgreich importiert wurden.       |
   |TotalRequestUnitsConsumed (double)   |   Die Gesamtanzahl von Anforderungseinheiten, die vom Massenimport-API-Aufruf verbraucht wurden.      |
   |TotalTimeTaken (TimeSpan)    |   Die Gesamtzeit, die der Massenimport-API-Aufruf benötigt, um die Ausführung abzuschließen.      |
   |BadInputDocuments (List\<object>)   |     Die Liste der Dokumente mit ungültigem Format, die im Massenimport-API-Aufruf nicht erfolgreich importiert wurden. Korrigieren Sie die zurückgegebenen Dokumente, und wiederholen Sie den Import. Zu Dokumenten mit ungültigem Format gehören auch Dokumente, deren ID-Wert keine Zeichenfolge ist (NULL-Werte oder andere Datentypen werden als ungültig betrachtet).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Massenaktualisierung von Daten in Ihrem Azure Cosmos-Konto

Sie können vorhandene Dokumente mithilfe der BulkUpdateAsync-API aktualisieren. In diesem Beispiel legen Sie das Feld `Name` auf einen neuen Wert fest und entfernen das Feld `Description` aus den vorhandenen Dokumenten. Alle unterstützten Aktualisierungsvorgänge finden Sie in der [API-Dokumentation](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet&preserve-view=true).

1. Navigieren Sie zum Ordner „BulkUpdateSample“, und öffnen Sie die Datei „BulkUpdateSample.sln“.  

2. Definieren Sie die Aktualisierungselemente zusammen mit den entsprechenden Vorgängen zum Aktualisieren von Feldern. In diesem Beispiel verwenden Sie `SetUpdateOperation`, um das Feld `Name` zu aktualisieren, und `UnsetUpdateOperation`, um das Feld `Description` aus allen Dokumenten zu entfernen. Sie können auch andere Vorgänge ausführen, wie z.B. das inkrementelle Erhöhen eines Dokumentfelds um einen bestimmten Wert, das Verschieben bestimmter Werte in ein Arrayfeld oder das Entfernen eines bestimmten Werts aus einem Arrayfeld. Informationen zu den verschiedenen Methoden, die von der API für die Massenaktualisierung bereitgestellt werden, finden Sie in der [API-Dokumentation](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet&preserve-view=true).

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. Die Anwendung ruft die BulkUpdateAsync-API auf. Informationen zur Definition der BulkUpdateAsync-Methode finden Sie in der [API-Dokumentation](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet&preserve-view=true).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **Die BulkUpdateAsync-Methode akzeptiert die folgenden Parameter:**

   |**Parameter**  |**Beschreibung** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   Der maximale Grad an Parallelität pro Partitionsschlüsselbereich. Bei einer Festlegung dieses Parameters auf NULL verwendet die Bibliothek den Standardwert (20).   |
   |maxInMemorySortingBatchSize    |    Die maximale Anzahl von Aktualisierungselementen, für die von dem Enumerator für Aktualisierungselemente ein Pull ausgeführt wird, der in jeder Phase an den API-Aufruf übergeben wird. Für die speicherinterne Sortierungsphase, die vor der Massenaktualisierung stattfindet, sorgt die Festlegung dieses Parameters auf NULL dafür, dass die Bibliothek den standardmäßigen Mindestwert „(updateItems.count, 1000000)“ verwendet.     |
   | cancellationToken|Das Abbruchtoken für ein ordnungsgemäßes Beenden des Massenaktualisierungsvorgangs. |

   **Definition von Massenaktualisierungs-Antwortobjekten** – das Ergebnis des Massenaktualisierungs-API-Aufrufs enthält die folgenden Attribute:

   |**Parameter**  |**Beschreibung** |
   |---------|---------|
   |NumberOfDocumentsUpdated (long)    |   Die Anzahl von Dokumenten, die von allen an den Massenaktualisierungs-API-Aufruf gesendeten Dokumenten erfolgreich aktualisiert wurden.      |
   |TotalRequestUnitsConsumed (double)   |    Die Gesamtanzahl von Anforderungseinheiten, die vom Massenaktualisierungs-API-Aufruf verbraucht wurden.    |
   |TotalTimeTaken (TimeSpan)   | Die Gesamtzeit, die der Massenaktualisierungs-API-Aufruf benötigt, um die Ausführung abzuschließen. |
    
## <a name="performance-tips"></a>Leistungstipps 

Berücksichtigen Sie bei der Verwendung der BulkExecutor-Bibliothek die folgenden Punkte, um die Leistung zu verbessern:

* Um die beste Leistung zu erzielen, führen Sie Ihre Anwendung auf einem virtuellen Azure-Computer in der Region aus, die Sie für Schreibvorgänge in Ihrem Azure Cosmos-Konto verwenden.  

* Es empfiehlt sich, ein einzelnes `BulkExecutor`-Objekt für die gesamte Anwendung auf einem einzelnen virtuellen Computer zu instanziieren, das einem bestimmten Azure Cosmos-Container entspricht.  

* Eine einzelne Ausführung einer Massenvorgang-API verbraucht eine große Menge an CPU- und Netzwerk-E/A-Ressourcen des Clientcomputers. (Dies wird erreicht, indem mehrere Tasks intern erzeugt werden.) Vermeiden Sie das Erzeugen mehrerer gleichzeitiger Tasks in Ihrem Anwendungsprozess, der Massenvorgang-API-Aufrufe ausführt. Wenn ein einzelner Massenvorgang-API-Aufruf, der auf einem einzelnen virtuellen Computer ausgeführt wird, nicht den gesamten Durchsatz des Containers verbrauchen kann (wenn der Durchsatz mehr als 1 Million Anforderungseinheiten pro Sekunde beträgt), ist es besser, separate virtuelle Computer zu erstellen, um die Massenvorgang-API-Aufrufe gleichzeitig auszuführen.  

* Stellen Sie sicher, dass die Methode `InitializeAsync()` aufgerufen wird, nachdem ein BulkExecutor-Objekt instanziiert wurde, um die Partitionszuordnung für den Cosmos-Zielcontainer abzurufen.  

* Stellen Sie in der App.config-Datei Ihrer Anwendung sicher, dass **gcServer** aktiviert ist, um eine bessere Leistung zu erzielen.
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* Die Bibliothek gibt Ablaufverfolgungen aus, die entweder in einer Protokolldatei oder in der Konsole gesammelt werden können. Um beides zu aktivieren, fügen Sie den folgenden Code zur Datei „App.config“ Ihrer Anwendung hinzu.

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
  ```

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu NuGet-Paketen und Versionshinweise finden Sie unter den [Details zum BulkExecutor-SDK](sql-api-sdk-bulk-executor-dot-net.md).
