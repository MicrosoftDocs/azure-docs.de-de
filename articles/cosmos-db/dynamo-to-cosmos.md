---
title: Migrieren einer Anwendung von Amazon DynamoDB zu Azure Cosmos DB
description: Hier erfahren Sie, wie Sie Ihre .NET-Anwendung von Amazon DynamoDB zu Azure Cosmos DB migrieren.
author: manishmsfte
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/29/2020
ms.author: mansha
ms.openlocfilehash: 9b4b5fca8017a906fa44b02edcf5f0bdcf6166b3
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93334224"
---
# <a name="migrate-your-application-from-amazon-dynamodb-to-azure-cosmos-db"></a>Migrieren einer Anwendung von Amazon DynamoDB zu Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB ist eine skalierbare, global verteilte, vollständig verwaltete Datenbank. Sie bietet garantierten Zugriff mit geringer Wartezeit auf Ihre Daten. Weitere Informationen zu Azure Cosmos DB finden Sie im Artikel [Übersicht](introduction.md). In diesem Artikel wird beschrieben, wie Sie Ihre .NET-Anwendung von DynamoDB mit minimalen Codeänderungen zu Azure Cosmos DB migrieren.

## <a name="conceptual-differences"></a>Konzeptionelle Unterschiede

Im Folgenden sind die wichtigsten konzeptionellen Unterschiede zwischen Azure Cosmos DB und DynamoDB aufgeführt:

|  DynamoDB | Azure Cosmos DB  |
|---|---|
|Nicht verfügbar|  Datenbank |
|Tabelle      |  Collection |
|  Element |  Dokument |
|attribute|Feld|
|Sekundärer Index|Sekundärer Index|
|Primärschlüssel – Partitionsschlüssel|Partitionsschlüssel|
|Primärer Schlüssel – Sortierschlüssel| Nicht erforderlich |
|STREAM|ChangeFeed|
|Compute-Einheit für Schreibvorgänge|Anforderungseinheit (flexibel, kann für Lese- und Schreibvorgänge verwendet werden)|
|Compute-Einheit für Lesevorgänge    |Anforderungseinheit (flexibel, kann für Lese- und Schreibvorgänge verwendet werden)|
|Globale Tabellen| Nicht erforderlich. Sie können die Region bei der Bereitstellung des Azure Cosmos-Kontos direkt auswählen (die Region können Sie später ändern).|

## <a name="structural-differences"></a>Strukturelle Unterschiede

Im Vergleich zu DynamoDB weist Azure Cosmos DB eine einfachere JSON-Struktur auf. Das folgende Beispiel veranschaulicht die Unterschiede.

**DynamoDB** :

Das folgende JSON-Objekt stellt das Datenformat in DynamoDB dar.

```json
{
TableName: "Music",
KeySchema: [
{ 
  AttributeName: "Artist",
  KeyType: "HASH", //Partition key
},
{ 
  AttributeName: "SongTitle",
  KeyType: "RANGE" //Sort key
}
],
AttributeDefinitions: [
{ 
  AttributeName: "Artist",
  AttributeType: "S"
},
{ 
  AttributeName: "SongTitle",
  AttributeType: "S"
}
],
ProvisionedThroughput: {
  ReadCapacityUnits: 1,
  WriteCapacityUnits: 1
 }
}
 ```

**Azure Cosmos DB** :

Das folgende JSON-Objekt stellt das Datenformat in Azure Cosmos DB dar.

```json
{
"Artist": "",
"SongTitle": "",
"AlbumTitle": "",
"Year": 9999,
"Price": 0.0,
"Genre": "",
"Tags": ""
}
```

## <a name="migrate-your-data"></a>Migrieren Ihrer Daten

Es stehen verschiedene Optionen zur Verfügung, mit denen Sie Ihre Daten zu Azure Cosmos DB migrieren können. Weitere Informationen finden Sie im Artikel [Optionen zum Migrieren von lokalen oder Clouddaten zu Azure Cosmos DB](cosmosdb-migrationchoices.md).

## <a name="migrate-your-code"></a>Migrieren Ihres Codes

Dieser Artikel behandelt die Migration von Anwendungscode zu Azure Cosmos DB. Dies ist der entscheidende Aspekt bei der Datenbankmigration. In den folgenden Abschnitten finden Sie eine Gegenüberstellung des Codes für Amazon DynamoDB und dem entsprechenden Codeausschnitt für Azure Cosmos DB, um Ihnen den Einstieg zu erleichtern.

Zum Herunterladen des Quellcodes klonen Sie das folgende Repository:

```bash
git clone https://github.com/Azure-Samples/DynamoDB-to-CosmosDB
```

### <a name="pre-requisites"></a>Voraussetzungen

- .NET Framework 4.7.2
- Visual Studio 2019
- Zugriff auf ein Azure Cosmos DB-SQL-API-Konto
- Lokale Installation von Amazon DynamoDB
- Java 8
- Führen Sie die herunterladbare Version von Amazon DynamoDB auf Port 8000 aus. (Sie können den Code ändern und konfigurieren.)

### <a name="set-up-your-code"></a>Einrichten des Codes

Fügen Sie das folgende „NuGet package“ Ihrem Projekt hinzu:

```bash
Install-Package Microsoft.Azure.Cosmos 
```

### <a name="establish-connection"></a>Herstellen der Verbindung

**DynamoDB** :

In Amazon DynamoDB wird der folgende Code zum Herstellen der Verbindung verwendet:

```csharp
    AmazonDynamoDBConfig addbConfig = new AmazonDynamoDBConfig() ;
        addbConfig.ServiceURL = "endpoint";
        try { aws_dynamodbclient = new AmazonDynamoDBClient( addbConfig ); }
```

**Azure Cosmos DB** :

Aktualisieren Sie Ihren Code wie folgt, um Azure Cosmos DB zu verbinden:

```csharp
client_documentDB = new CosmosClient("your connectionstring from the Azure portal");
```

**Optimieren der Verbindung in Azure Cosmos DB**

Bei Azure Cosmos DB können Sie die folgenden Optionen verwenden, um die Verbindung zu optimieren:

* **ConnectionMode** : Verwenden Sie den direkten Verbindungsmodus, um eine Verbindung mit den Datenknoten im Azure Cosmos DB-Dienst herzustellen. Verwenden Sie den Gatewaymodus nur zum Initialisieren und Zwischenspeichern der logischen Adressen, und führen Sie bei Änderungen eine Aktualisierung aus. Weitere Informationen finden Sie im Artikel [Konnektivitätsmodi](sql-sdk-connection-modes.md).

* **ApplicationRegion** : Diese Option wird verwendet, um die bevorzugte georeplizierten Region festzulegen, die für die Interaktion mit Azure Cosmos DB verwendet wird. Weitere Informationen finden Sie im Artikel [Globale Verteilung](distribute-data-globally.md).

* **ConsistencyLevel** : Diese Option wird verwendet, um die Standardkonsistenzebene zu überschreiben. Weitere Informationen finden Sie im Artikel [Konsistenzebenen](consistency-levels.md).

* **BulkExecutionMode** : Diese Option wird zum Ausführen von Massenvorgängen verwendet, indem die *AllowBulkExecution* -Eigenschaft auf „true“ festgelegt wird. Weitere Informationen finden Sie im Artikel [Massenimport](tutorial-sql-api-dotnet-bulk-import.md).

   ```csharp
   client_cosmosDB = new CosmosClient(" Your connection string ",new CosmosClientOptions()
   { 
    ConnectionMode=ConnectionMode.Direct,
    ApplicationRegion=Regions.EastUS2,
    ConsistencyLevel=ConsistencyLevel.Session,
    AllowBulkExecution=true  
   });
   ```

### <a name="provision-the-container"></a>Bereitstellen des Containers

**DynamoDB** :

Zum Speichern der Daten in Amazon DynamoDB müssen Sie zuerst die Tabelle erstellen. In diesem Prozess definieren Sie das Schema, den Schlüsseltyp und die Attribute wie im folgenden Code:

```csharp
// movies_key_schema
public static List<KeySchemaElement> movies_key_schema
  = new List<KeySchemaElement>
{
  new KeySchemaElement
  {
    AttributeName = partition_key_name,
    KeyType = "HASH"
  },
  new KeySchemaElement
  {
    AttributeName = sort_key_name,
    KeyType = "RANGE"
  }
};

// key names for the Movies table
public const string partition_key_name = "year";
public const string sort_key_name      = "title";
  public const int readUnits=1, writeUnits=1; 

    // movie_items_attributes
    public static List<AttributeDefinition> movie_items_attributes
  = new List<AttributeDefinition>
{
  new AttributeDefinition
  {
    AttributeName = partition_key_name,
    AttributeType = "N"
  },
  new AttributeDefinition
  {
    AttributeName = sort_key_name,
    AttributeType = "S"
  }

CreateTableRequest  request;
CreateTableResponse response;

// Build the 'CreateTableRequest' structure for the new table
request = new CreateTableRequest
{
  TableName             = table_name,
  AttributeDefinitions  = table_attributes,
  KeySchema             = table_key_schema,
  // Provisioned-throughput settings are always required,
  // although the local test version of DynamoDB ignores them.
  ProvisionedThroughput = new ProvisionedThroughput( readUnits, writeUnits );
};
```

**Azure Cosmos DB** :

In Amazon DynamoDB müssen Sie die Compute-Einheiten für Schreib- und Lesevorgänge bereitstellen. In Azure Cosmos DB geben Sie den Durchsatz hingegen als [Anforderungseinheiten (RU/s)](request-units.md) an, die dynamisch für alle Vorgänge verwendet werden können. Die Daten sind nach dem Schema „Datenbank --> Container--> Element“ angeordnet. Der Durchsatz kann auf Datenbank- und/oder Sammlungsebene festgelegt werden.

So erstellen Sie eine Datenbank

```csharp
await client_cosmosDB.CreateDatabaseIfNotExistsAsync(movies_table_name);
```

So erstellen Sie den Container

```csharp
await cosmosDatabase.CreateContainerIfNotExistsAsync(new ContainerProperties() { PartitionKeyPath = "/" + partitionKey, Id = new_collection_name }, provisionedThroughput);
```

### <a name="load-the-data"></a>Laden der Daten

**DynamoDB** :

Der folgende Code zeigt, wie Sie die Daten in Amazon DynamoDB laden. Das moviesArray besteht aus einer Liste von JSON-Dokumenten. Anschließend müssen Sie das JSON-Dokument durchlaufen und in Amazon DynamoDB laden:

```csharp
int n = moviesArray.Count;
for( int i = 0, j = 99; i < n; i++ )
    {
  try
  {
    string itemJson = moviesArray[i].ToString();
    Document doc = Document.FromJson(itemJson);
    Task putItem = moviesTable.PutItemAsync(doc);
    if( i >= j )
    {
      j++;
      Console.Write( "{0,5:#,##0}, ", j );
      if( j % 1000 == 0 )
        Console.Write( "\n " );
      j += 99;
    }
    await putItem;
```

**Azure Cosmos DB** :

In Azure Cosmos DB können Sie `moviesContainer.CreateItemStreamAsync()` zum Streamen und Schreiben verwenden. In diesem Beispiel wird der JSON-Code jedoch in den Typ *MovieModel* deserialisiert, um das Typumwandlungsfeature zu veranschaulichen. Der Code wird in mehreren Threads ausgeführt, wobei die verteilte Architektur von Azure Cosmos DB verwendet und das Laden beschleunigt wird:

```csharp
List<Task> concurrentTasks = new List<Task>();
for (int i = 0, j = 99; i < n; i++)
{
  try
  {
      MovieModel doc= JsonConvert.DeserializeObject<MovieModel>(moviesArray[i].ToString());
      doc.Id = Guid.NewGuid().ToString();
      concurrentTasks.Add(moviesContainer.CreateItemAsync(doc,new PartitionKey(doc.Year)));
      {
          j++;
          Console.Write("{0,5:#,##0}, ", j);
          if (j % 1000 == 0)
              Console.Write("\n               ");
          j += 99;
      }
      
  }
  catch (Exception ex)
  {
      Console.WriteLine("\n     ERROR: Could not write the movie record #{0:#,##0}, because:\n       {1}",
                          i, ex.Message);
      operationFailed = true;
      break;
  }
}
await Task.WhenAll(concurrentTasks);
```

### <a name="create-a-document"></a>Erstellen eines Dokuments

**DynamoDB** :

Das Schreiben eines neuen Dokuments in Amazon DynamoDB ist nicht typsicher. Im folgenden Beispiel wird „newItem“ als Dokumenttyp verwendet:

```csharp
Task<Document> writeNew = moviesTable.PutItemAsync(newItem, token);
await writeNew;
```

**Azure Cosmos DB** :

Azure Cosmos DB bietet Typsicherheit aufgrund des Datenmodells. Hier wird ein Datenmodell mit dem Namen „MovieModel“ verwendet:

```csharp
public class MovieModel
{
    [JsonProperty("id")]
    public string Id { get; set; }
    [JsonProperty("title")]
    public string Title{ get; set; }
    [JsonProperty("year")]
    public int Year { get; set; }
    public MovieModel(string title, int year)
    {
        this.Title = title;
        this.Year = year;
    }
    public MovieModel()
    {

    }
    [JsonProperty("info")]
    public   MovieInfo MovieInfo { get; set; }

    internal string PrintInfo()
    {
        if(this.MovieInfo!=null)
        return            string.Format("\nMovie with title:{1}\n Year: {2}, Actors: {3}\n Directors:{4}\n Rating:{5}\n", this.Id, this.Title, this.Year, String.Join(",",this.MovieInfo.Actors), this.MovieInfo, this.MovieInfo.Rating);
        else
            return string.Format("\nMovie with  title:{0}\n Year: {1}\n",  this.Title, this.Year);
    }
}
```

In Azure Cosmos DB wird „MovieModel“ als „newItem“ verwendet:

```csharp
 MovieModel movieModel = new MovieModel()
            {
                Id = Guid.NewGuid().ToString(),
                Title = "The Big New Movie",
                Year = 2018,
                MovieInfo = new MovieInfo() { Plot = "Nothing happens at all.", Rating = 0 }
            };
    var writeNew= moviesContainer.CreateItemAsync(movieModel, new Microsoft.Azure.Cosmos.PartitionKey(movieModel.Year));
    await writeNew;
```

### <a name="read-a-document"></a>Lesen eines Dokuments

**DynamoDB** :

Zum Lesen von Amazon DynamoDB müssen Sie Primitive definieren:

```csharp
// Create Primitives for the HASH and RANGE portions of the primary key
Primitive hash = new Primitive(year.ToString(), true);
Primitive range = new Primitive(title, false);

  Task<Document> readMovie = moviesTable.GetItemAsync(hash, range, token);
  movie_record = await readMovie;
```

**Azure Cosmos DB** :

Azure Cosmos DB unterstützt natürliche Abfragen (LINQ):

```csharp
IQueryable<MovieModel> movieQuery = moviesContainer.GetItemLinqQueryable<MovieModel>(true)
                        .Where(f => f.Year == year && f.Title == title);
// The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
    foreach (MovieModel movie in movieQuery)
    {
      movie_record_cosmosdb = movie;
    }
```

Für die Dokumentsammlung im Beispiel oben gilt:

- Sie sind typsicher.
- Sie unterstützen die Möglichkeit natürlicher Abfragen.

### <a name="update-an-item"></a>Aktualisieren eines Elements

**DynamoDB** : So aktualisieren Sie das Element in Amazon DynamoDB

```csharp
updateResponse = await client.UpdateItemAsync( updateRequest );
````

**Azure Cosmos DB** :

In Azure Cosmos DB wird ein Update als Upsert-Vorgang behandelt, d. h., das Dokument wird eingefügt, wenn es nicht vorhanden ist:

```csharp
await moviesContainer.UpsertItemAsync<MovieModel>(updatedMovieModel);
```

### <a name="delete-a-document"></a>Löschen eines Dokuments

**DynamoDB** :

Um ein Element in Amazon DynamoDB zu löschen, müssen Sie erneut auf Primitive zurückgreifen:

```csharp
Primitive hash = new Primitive(year.ToString(), true);
      Primitive range = new Primitive(title, false);
      DeleteItemOperationConfig deleteConfig = new DeleteItemOperationConfig( );
      deleteConfig.ConditionalExpression = condition;
      deleteConfig.ReturnValues = ReturnValues.AllOldAttributes;
      
  Task<Document> delItem = table.DeleteItemAsync( hash, range, deleteConfig );
        deletedItem = await delItem;
```

**Azure Cosmos DB** :

In Azure Cosmos DB können wir das Dokument abrufen und asynchron löschen:

```csharp
var result= ReadingMovieItem_async_List_CosmosDB("select * from c where c.info.rating>7 AND c.year=2018 AND c.title='The Big New Movie'");
while (result.HasMoreResults)
{
  var resultModel = await result.ReadNextAsync();
  foreach (var movie in resultModel.ToList<MovieModel>())
  {
    await moviesContainer.DeleteItemAsync<MovieModel>(movie.Id, new PartitionKey(movie.Year));
  }
  }
```

### <a name="query-documents"></a>Abfragedokumente

**DynamoDB** :

In Amazon DynamoDB sind API-Funktionen erforderlich, um die Daten abzufragen:

```csharp
QueryOperationConfig config = new QueryOperationConfig( );
  config.Filter = new QueryFilter( );
  config.Filter.AddCondition( "year", QueryOperator.Equal, new DynamoDBEntry[ ] { 1992 } );
  config.Filter.AddCondition( "title", QueryOperator.Between, new DynamoDBEntry[ ] { "B", "Hzz" } );
  config.AttributesToGet = new List<string> { "year", "title", "info" };
  config.Select = SelectValues.SpecificAttributes;
  search = moviesTable.Query( config ); 
```

**Azure Cosmos DB** :

In Azure Cosmos DB können Sie Projektion und Filter in einer einfachen SQL-Abfrage ausführen:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.Year, c.Title, c.info from c where Year=1998 AND (CONTAINS(Title,'B') OR CONTAINS(Title,'Hzz'))");
```

Für Bereichsvorgänge wie „between“ müssen Sie einen Scan in Amazon DynamoDB durchführen:

```csharp
ScanRequest sRequest = new ScanRequest
{
  TableName = "Movies",
  ExpressionAttributeNames = new Dictionary<string, string>
  {
    { "#yr", "year" }
  },
  ExpressionAttributeValues = new Dictionary<string, AttributeValue>
  {
      { ":y_a", new AttributeValue { N = "1960" } },
      { ":y_z", new AttributeValue { N = "1969" } },
  },
  FilterExpression = "#yr between :y_a and :y_z",
  ProjectionExpression = "#yr, title, info.actors[0], info.directors, info.running_time_secs"
};

ClientScanning_async( sRequest ).Wait( );
```

In Azure Cosmos DB können Sie eine SQL-Abfrage und eine einzeilige Anweisung verwenden:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.title, c.info.actors[0], c.info.directors,c.info.running_time_secs from c where BETWEEN year 1960 AND 1969");
```

### <a name="delete-a-container"></a>Löschen eines Containers

**DynamoDB** :

Zum Löschen der Tabelle in Amazon DynamoDB können Sie Folgendes angeben:

```csharp
client.DeleteTableAsync( tableName );
```

**Azure Cosmos DB** :

Zum Löschen der Sammlung in Azure Cosmos DB können Sie Folgendes angeben:

```csharp
await moviesContainer.DeleteContainerAsync();
```
Löschen Sie dann bei Bedarf auch die Datenbank:

```csharp
await cosmosDatabase.DeleteAsync();
```

Wie Sie sehen, werden in Azure Cosmos DB natürliche Abfragen (SQL) unterstützt. Vorgänge sind asynchron und viel einfacher. Sie können den komplexen Code problemlos zu Azure Cosmos DB migrieren. Durch die Migration wird dieser vereinfacht.

### <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen über [Leistung und Optimierung](performance-tips.md)
- Weitere Informationen über das [Optimieren der Lese- und Schreibvorgänge](key-value-store-cost.md)
- Weitere Informationen über die [Überwachung in Cosmos DB](monitor-cosmos-db.md)

