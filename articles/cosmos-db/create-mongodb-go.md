---
title: Verbinden einer Go-Anwendung mit der Azure Cosmos DB-API für MongoDB
description: In dieser Schnellstartanleitung wird veranschaulicht, wie Sie eine vorhandene Go-Anwendung mit der Azure Cosmos DB-API für MongoDB verbinden.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: go
ms.topic: quickstart
ms.date: 04/24/2020
ms.openlocfilehash: 3f6ff0f81c1979297ed0144eca336cb252c2e787
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574359"
---
# <a name="quickstart-connect-a-go-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Schnellstart: Verbinden einer Go-Anwendung mit der Azure Cosmos DB-API für MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB ist ein Multimodell-Datenbankdienst, mit dem Sie mithilfe der Funktionen für globale Verteilung und horizontale Skalierung schnell Dokument-, Tabellen-, Schlüssel-Wert- und Graph-Datenbanken erstellen und abfragen können. In dieser Schnellstartanleitung erstellen und verwalten Sie ein Azure Cosmos DB-Konto mithilfe von Azure Cloud Shell, klonen eine vorhandene Beispielanwendung von GitHub und konfigurieren sie für das Arbeiten mit Azure Cosmos DB. 

Bei der Beispielanwendung handelt es sich um ein befehlszeilenbasiertes `todo`-Verwaltungstool, das in Go geschrieben wurde. Die Azure Cosmos DB-API für MongoDB ist [mit dem MongoDB Wire Protocol kompatibel](./mongodb-introduction.md#wire-protocol-compatibility), sodass alle MongoDB-Clienttreiber eine Verbindung damit herstellen können. Diese Anwendung verwendet den [Go-Treiber für MongoDB](https://github.com/mongodb/mongo-go-driver) so, dass es für die Anwendung transparent ist, dass die Daten in einer Azure Cosmos DB-Datenbank gespeichert sind.

## <a name="prerequisites"></a>Voraussetzungen
- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free) Oder [testen Sie Azure Cosmos DB kostenlos](https://azure.microsoft.com/try/cosmosdb/) ohne ein Azure-Abonnement. Sie können auch den [Azure Cosmos DB-Emulator](https://aka.ms/cosmosdb-emulator) mit der Verbindungszeichenfolge `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` verwenden.
- [Go](https://golang.org/) muss auf Ihrem Computer installiert sein, und Sie müssen über ausreichende praktische Kenntnisse über Go verfügen.
- [Git](https://git-scm.com/downloads).
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Führen Sie die folgenden Befehle aus, um das Beispielrepository zu klonen.

1. Öffnen Sie eine Eingabeaufforderung, erstellen Sie einen neuen Ordner namens `git-samples`, und schließen Sie dann die Eingabeaufforderung.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Öffnen Sie ein Git-Terminalfenster (z.B. git bash), und verwenden Sie den Befehl `cd`, um in den neuen Ordner zu gelangen und dort die Beispiel-App zu installieren.

    ```bash
    cd "C:\git-samples"
    ```

3. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-go-mongodb-quickstart
    ```

## <a name="review-the-code"></a>Überprüfen des Codes

Dieser Schritt ist optional. Wenn Sie erfahren möchten, wie die Anwendung funktioniert, können Sie sich die folgenden Codeausschnitte ansehen. Andernfalls können Sie mit dem Abschnitt [Ausführen der Anwendung](#run-the-application) fortfahren. Das Anwendungslayout sieht wie folgt aus:

```bash
.
├── go.mod
├── go.sum
└── todo.go
```

Die folgenden Codeausschnitte stammen alle aus der Datei `todo.go`.

### <a name="connecting-the-go-app-to-azure-cosmos-db"></a>Herstellen einer Verbindung für die Go-App mit Azure Cosmos DB

[`clientOptions`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions) kapselt die Verbindungszeichenfolge für Azure Cosmos DB, die mithilfe einer Umgebungsvariable übergeben wird (Details finden Sie im nächsten Abschnitt). Die Verbindung wird mithilfe der Funktion [`mongo.NewClient`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#NewClient) initialisiert, an die die `clientOptions`-Instanz übergeben wird. Die [Funktion `Ping`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Client.Ping) wird aufgerufen, um die erfolgreiche Konnektivität zu bestätigen (dies ist eine Fail-Fast-Strategie).

```go
    ctx, cancel := context.WithTimeout(context.Background(), time.Second*10)
    defer cancel()

    clientOptions := options.Client().ApplyURI(mongoDBConnectionString).SetDirect(true)
    
    c, err := mongo.NewClient(clientOptions)
    err = c.Connect(ctx)
    if err != nil {
        log.Fatalf("unable to initialize connection %v", err)
    }

    err = c.Ping(ctx, nil)
    if err != nil {
        log.Fatalf("unable to connect %v", err)
    }
```

> [!NOTE] 
> Die Verwendung der [`SetDirect(true)`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions.SetDirect)-Konfiguration ist wichtig. Ohne diese Konfiguration erhalten Sie den folgenden Verbindungsfehler: `unable to connect connection(cdb-ms-prod-<azure-region>-cm1.documents.azure.com:10255[-4]) connection is closed`
>

### <a name="create-a-todo-item"></a>Erstellen eines `todo`-Elements

Zum Erstellen eines `todo`-Elements rufen wir ein Handle für eine [`mongo.Collection`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection) ab und rufen die Funktion [`InsertOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.InsertOne) auf. 

```go
func create(desc string) {
    c := connect()
    ctx := context.Background()
    defer c.Disconnect(ctx)

    todoCollection := c.Database(database).Collection(collection)
    r, err := todoCollection.InsertOne(ctx, Todo{Description: desc, Status: statusPending})
    if err != nil {
        log.Fatalf("failed to add todo %v", err)
    }
```

Wir übergeben eine `Todo`-Struktur mit der Beschreibung und dem Status (der anfangs auf `pending` festgelegt ist).

```go
type Todo struct {
    ID          primitive.ObjectID `bson:"_id,omitempty"`
    Description string             `bson:"description"`
    Status      string             `bson:"status"`
}
```
### <a name="list-todo-items"></a>Auflisten von `todo`-Elementen

Wir können TODO-Elemente basierend auf Kriterien auflisten. Ein [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) wird erstellt, um die Filterkriterien zu kapseln.

```go
func list(status string) {
    .....
    var filter interface{}
    switch status {
    case listAllCriteria:
        filter = bson.D{}
    case statusCompleted:
        filter = bson.D{{statusAttribute, statusCompleted}}
    case statusPending:
        filter = bson.D{{statusAttribute, statusPending}}
    default:
        log.Fatal("invalid criteria for listing todo(s)")
    }
```

[`Find`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.Find) wird verwendet, um basierend auf dem Filter nach Dokumenten zu suchen, und das Ergebnis wird in einen Slice von `Todo` konvertiert.

```go
    todoCollection := c.Database(database).Collection(collection)
    rs, err := todoCollection.Find(ctx, filter)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
    var todos []Todo
    err = rs.All(ctx, &todos)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
```

Abschließend werden die Informationen im Tabellenformat gerendert.

```go
    todoTable := [][]string{}

    for _, todo := range todos {
        s, _ := todo.ID.MarshalJSON()
        todoTable = append(todoTable, []string{string(s), todo.Description, todo.Status})
    }

    table := tablewriter.NewWriter(os.Stdout)
    table.SetHeader([]string{"ID", "Description", "Status"})

    for _, v := range todoTable {
        table.Append(v)
    }
    table.Render()
```

### <a name="update-a-todo-item"></a>Aktualisieren eines `todo`-Elements

Ein `todo`-Element kann basierend auf der zugehörigen `_id` aktualisiert werden. Ein [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D)-Filter wird basierend auf der `_id` erstellt, und für die aktualisierten Informationen wird ein weiterer Filter erstellt, in diesem Fall ein neuer Status (`completed` oder `pending`). Abschließend wird die Funktion [`UpdateOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.UpdateOne) mit dem Filter und dem aktualisierten Dokument aufgerufen.

```go
func update(todoid, newStatus string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
    filter := bson.D{{"_id", oid}}
    update := bson.D{{"$set", bson.D{{statusAttribute, newStatus}}}}
    _, err = todoCollection.UpdateOne(ctx, filter, update)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
```

### <a name="delete-a-todo"></a>Löschen eines `todo`-Elements

Ein `todo`-Element wird basierend auf der zugehörigen `_id` gelöscht und in Form einer [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D)-Instanz gekapselt. [`DeleteOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.DeleteOne) wird zum Löschen des Dokuments aufgerufen.

```go
func delete(todoid string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("invalid todo ID %v", err)
    }
    filter := bson.D{{"_id", oid}}
    _, err = todoCollection.DeleteOne(ctx, filter)
    if err != nil {
        log.Fatalf("failed to delete todo %v", err)
    }
}
```

## <a name="build-the-application"></a>Erstellen der Anwendung

Wechseln Sie in das Verzeichnis, in dem Sie die Anwendung geklont haben, und erstellen Sie die Anwendung (mit `go build`).

```bash
cd monogdb-go-quickstart
go build -o todo
```

Damit bestätigen Sie, dass die Anwendung ordnungsgemäß erstellt wurde.

```bash
./todo --help
```

## <a name="setup-azure-cosmos-db"></a>Einrichten von Azure Cosmos DB

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

Wenn Sie die Befehlszeilenschnittstelle lokal installieren und verwenden möchten, müssen Sie für dieses Thema die Azure CLI in Version 2.0 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Wenn Sie die CLI installieren oder aktualisieren müssen, finden Sie die entsprechende Anleitung unter [Installieren der Azure CLI]. 

Wenn Sie eine installierte Azure CLI verwenden, melden Sie sich mit dem Befehl [az login](/cli/azure/reference-index#az-login) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm. Sie können diesen Schritt überspringen, wenn Sie Azure Cloud Shell verwenden.

```azurecli
az login 
``` 
   
### <a name="add-the-azure-cosmos-db-module"></a>Hinzufügen des Azure Cosmos DB-Moduls

Wenn Sie eine installierte Azure CLI verwenden, überprüfen Sie, ob die `cosmosdb`-Komponente bereits installiert ist, indem Sie den `az`-Befehl ausführen. Wenn `cosmosdb` in der Liste der Basisbefehle enthalten ist, fahren Sie mit dem nächsten Befehl fort. Sie können diesen Schritt überspringen, wenn Sie Azure Cloud Shell verwenden.

Wenn `cosmosdb` nicht in der Liste der Basisbefehle enthalten ist, installieren Sie die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) erneut.

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group#az-group-create) eine [Ressourcengruppe](../azure-resource-manager/management/overview.md). Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen wie Web-Apps, Datenbanken und Speicherkonten bereitgestellt und verwaltet werden. 

Im folgenden Beispiel wird eine Ressourcengruppe in der Region „Europa, Westen“ erstellt. Wählen Sie einen eindeutigen Namen für die Ressourcengruppe aus.

Wenn Sie Azure Cloud Shell verwenden, wählen Sie **Ausprobieren** aus, befolgen Sie die Anweisungen auf dem Bildschirm für die Anmeldung, und kopieren Sie dann den Befehl in die Eingabeaufforderung.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

### <a name="create-an-azure-cosmos-db-account"></a>Erstellen eines Azure Cosmos DB-Kontos

Erstellen Sie mit dem Befehl [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) ein Cosmos-Konto.

Ersetzen Sie im folgenden Befehl den Platzhalter `<cosmosdb-name>` durch Ihren eigenen eindeutigen Cosmos-Kontonamen. Da dieser eindeutige Name als Teil des Cosmos DB-Endpunkts (`https://<cosmosdb-name>.documents.azure.com/`) verwendet wird, muss er für alle Cosmos-Konten in Azure eindeutig sein. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

Der `--kind MongoDB`-Parameter ermöglicht MongoDB-Clientverbindungen.

Nach dem Erstellen des Azure Cosmos DB-Kontos zeigt die Azure-CLI ähnliche Informationen wie im folgenden Beispiel an. 

> [!NOTE]
> In diesem Beispiel wird JSON als Azure CLI-Ausgabeformat (Standardeinstellung) verwendet. Informationen zur Verwendung anderer Ausgabeformate finden Sie unter [Ausgabeformate für Azure CLI-Befehle](/cli/azure/format-output-azure-cli).

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

### <a name="retrieve-the-database-key"></a>Abrufen des Datenbankschlüssels

Um eine Verbindung mit der Cosmos-Datenbank herstellen zu können, benötigen Sie den Datenbankschlüssel. Rufen Sie den Primärschlüssel mit dem Befehl [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) ab.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Die Azure-CLI gibt Informationen ähnlich wie im folgenden Beispiel aus. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

## <a name="configure-the-application"></a>Konfigurieren der Anwendung 

<a name="devconfig"></a>
### <a name="export-the-connection-string-mongodb-database-and-collection-names-as-environment-variables"></a>Exportieren Sie die Verbindungszeichenfolge, die MongoDB-Datenbank und die Sammlungsnamen als Umgebungsvariablen. 

```bash
export MONGODB_CONNECTION_STRING="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> Die Option `ssl=true` ist wichtig, um die Voraussetzungen für Cosmos DB zu erfüllen. Weitere Informationen finden Sie unter [Anforderungen an die Verbindungszeichenfolge](connect-mongodb-account.md#connection-string-requirements).
>

Ersetzen Sie bei der Umgebungsvariable `MONGODB_CONNECTION_STRING` die Platzhalter `<COSMOSDB_ACCOUNT_NAME>` und `<COSMOSDB_PASSWORD>`.

1. `<COSMOSDB_ACCOUNT_NAME>`: Der Name des von Ihnen erstellten Azure Cosmos DB-Kontos
2. `<COSMOSDB_PASSWORD>`: Der im vorherigen Schritt extrahierte Datenbankschlüssel

```bash
export MONGODB_DATABASE=todo-db
export MONGODB_COLLECTION=todos
```

Sie können für `MONGODB_DATABASE` und `MONGODB_COLLECTION` Ihre bevorzugten Werte auswählen oder die Standardwerte unverändert übernehmen.

## <a name="run-the-application"></a>Ausführen der Anwendung

So erstellen Sie ein `todo`

```bash
./todo --create "Create an Azure Cosmos DB database account"
```

Wenn der Vorgang erfolgreich war, wird eine Ausgabe mit der MongoDB-`_id` des neu erstellten Dokuments angezeigt:

```bash
added todo ObjectID("5e9fd6befd2f076d1f03bd8a")
```

Erstellen eines weiteren `todo`-Elements

```bash
./todo --create "Get the MongoDB connection string using the Azure CLI"
```

Auflisten aller `todo`-Elemente

```bash
./todo --list all
```

Es sollten die eben von Ihnen hinzugefügten Elemente im Tabellenformat angezeigt werden:

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | pending   |
|                            | database account               |           |
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

Wenn Sie den Status eines `todo`-Elements aktualisieren (z. B. den Status in `completed` ändern) möchten, verwenden Sie die `todo`-ID.

```bash
./todo --update 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Auflisten der abgeschlossenen `todo`-Elemente

```bash
./todo --list completed
```

Es sollte nur das eben von Ihnen aktualisierte Element angezeigt werden.

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | completed |
|                            | database account               |           |
+----------------------------+--------------------------------+-----------+
```

### <a name="view-data-in-data-explorer"></a>Anzeigen von Daten im Daten-Explorer

Die in Azure Cosmos DB gespeicherten Daten können im Azure-Portal angezeigt und abgefragt werden.

Melden Sie sich im [Azure-Portal](https://portal.azure.com) in Ihrem Webbrowser an, um die im vorherigen Schritt erstellten Benutzerdaten anzuzeigen, abzufragen und mit ihnen zu arbeiten.

Geben Sie im Suchfeld oben **Azure Cosmos DB** ein. Wenn sich das Blatt Ihres Cosmos-Kontos öffnet, wählen Sie Ihr Cosmos-Konto aus. Wählen Sie im linken Navigationsbereich **Daten-Explorer** aus. Erweitern Sie Ihre Sammlung im Bereich „Sammlungen“. Anschließend können Sie die Dokumente in der Sammlung anzeigen, die Daten abfragen und sogar gespeicherte Prozeduren, Trigger und UDFs erstellen und ausführen. 

:::image type="content" source="./media/create-mongodb-go/go-cosmos-db-data-explorer.png" alt-text="Daten-Explorer mit dem neu erstellten Dokument":::


Löschen eines `todo`-Elements anhand der ID

```bash
./todo --delete 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Auflisten aller `todo`-Elemente zur Bestätigung

```bash
./todo --list all
```

Das `todo`-Element, das Sie eben gelöscht haben, sollte nicht angezeigt werden.

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie erfahren, wie Sie mit Azure Cloud Shell ein Azure Cosmos DB-Konto für die MongoDB-API erstellen sowie eine Go-Befehlszeilen-App erstellen und ausführen, um `todo`-Elemente zu verwalten. Jetzt können Sie zusätzliche Daten in Ihr Azure Cosmos DB-Konto importieren.

> [!div class="nextstepaction"]
> [Import MongoDB data into Azure Cosmos DB (Importieren von Daten aus MongoDB in Azure Cosmos DB)](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)