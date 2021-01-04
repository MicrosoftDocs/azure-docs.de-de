---
title: Erstellen einer Go-App mit der Azure Cosmos DB-Cassandra-API mithilfe des gocql-Clients
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie einen Go-Client für die Interaktion mit der Azure Cosmos DB-Cassandra-API verwenden.
ms.service: cosmos-db
author: abhirockzz
ms.author: abhishgu
ms.subservice: cosmosdb-cassandra
ms.devlang: go
ms.topic: quickstart
ms.date: 07/14/2020
ms.openlocfilehash: 595ec1aaa4aedc3916d1b4d46986dcabae887aaf
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076400"
---
# <a name="quickstart-build-a-go-app-with-the-gocql-client-to-manage-azure-cosmos-db-cassandra-api-data"></a>Schnellstart: Erstellen einer Go-App mit dem `gocql`-Client zum Verwalten von Azure Cosmos DB-Cassandra-API-Daten
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
> * [Golang](create-cassandra-go.md)
>

Azure Cosmos DB ist ein Multimodell-Datenbankdienst, mit dem Sie mithilfe der Funktionen für globale Verteilung und horizontale Skalierung schnell Dokument-, Tabellen-, Schlüssel-Wert- und Graph-Datenbanken erstellen und abfragen können. In dieser Schnellstartanleitung erstellen Sie zunächst ein Konto für die Azure Cosmos DB-Cassandra-API. Anschließend führen Sie eine Go-Anwendung aus, um einen Keyspace und eine Tabelle in Cassandra zu erstellen und einige Vorgänge auszuführen. Diese Go-App verwendet [gocql](https://github.com/gocql/gocql). Dabei handelt es sich um einen Cassandra-Client für die Sprache Go handelt. 

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?WT.mc_id=cassandrago-docs-abhishgu) Oder [testen Sie Azure Cosmos DB kostenlos](https://azure.microsoft.com/try/cosmosdb/?WT.mc_id=cassandrago-docs-abhishgu) ohne ein Azure-Abonnement.
- [Go](https://golang.org/) muss auf Ihrem Computer installiert sein, und Sie müssen über ausreichende praktische Kenntnisse über Go verfügen.
- [Git](https://git-scm.com/downloads).

## <a name="create-a-database-account"></a>Erstellen eines Datenbankkontos

Vor dem Erstellen einer Datenbank müssen Sie mit Azure Cosmos DB ein Cassandra-Konto erstellen.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Beginnen Sie mit dem Klonen der Anwendung über GitHub.

1. Öffnen Sie eine Eingabeaufforderung, und erstellen Sie einen Ordner namens `git-samples`.

    ```bash
    md "C:\git-samples"
    ```

2. Öffnen Sie ein Git-Terminalfenster, z.B. git bash. Wechseln Sie mit dem Befehl `cd` in den neuen Ordner, und installieren Sie die Beispiel-App.

    ```bash
    cd "C:\git-samples"
    ```

3. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-go-getting-started.git
    ```

## <a name="review-the-code"></a>Überprüfen des Codes

Dieser Schritt ist optional. Wenn Sie erfahren möchten, wie der Code die Datenbankressourcen erstellt, können Sie sich die folgenden Codeausschnitte ansehen. Andernfalls können Sie mit dem Abschnitt [Ausführen der Anwendung](#run-the-application) fortfahren.

Die Funktion `GetSession` (Teil von `utils\utils.go`) gibt eine Sitzung vom Typ [`*gocql.Session`](https://godoc.org/github.com/gocql/gocql#Session) zurück. Diese Sitzung wird zum Ausführen von Clustervorgängen wie Einfügen, Suchen usw. verwendet.

```go
func GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword string) *gocql.Session {
    clusterConfig := gocql.NewCluster(cosmosCassandraContactPoint)
    port, err := strconv.Atoi(cosmosCassandraPort)
    
    clusterConfig.Authenticator = gocql.PasswordAuthenticator{Username: cosmosCassandraUser, Password: cosmosCassandraPassword}
    clusterConfig.Port = port
    clusterConfig.SslOpts = &gocql.SslOptions{Config: &tls.Config{MinVersion: tls.VersionTLS12}}
    clusterConfig.ProtoVersion = 4
    
    session, err := clusterConfig.CreateSession()
    ...
    return session
}
```

Der Azure Cosmos DB-Cassandra-Host wird an die Funktion [`gocql.NewCluster`](https://godoc.org/github.com/gocql/gocql#NewCluster) übergeben, um eine Struktur vom Typ [`*gocql.ClusterConfig`](https://godoc.org/github.com/gocql/gocql#ClusterConfig) abzurufen, die dann zur Verwendung des Benutzernamens, des Kennworts, des Ports und der entsprechenden TLS-Version ([Sicherheitsanforderung in Bezug auf HTTPS-/SSL-/TLS-Verschlüsselung](./database-security.md?WT.mc_id=cassandrago-docs-abhishgu#how-does-azure-cosmos-db-secure-my-database)) konfiguriert wird.

Die Funktion `GetSession` wird dann über die `main`-Funktion (`main.go`) abgerufen.

```go
func main() {
    session := utils.GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword)
    defer session.Close()
    ...
}
```

Die Konnektivitätsinformationen und Anmeldeinformationen werden in Form von Umgebungsvariablen (aufgelöst in der `init`-Methode) akzeptiert.

```go
func init() {
    cosmosCassandraContactPoint = os.Getenv("COSMOSDB_CASSANDRA_CONTACT_POINT")
    cosmosCassandraPort = os.Getenv("COSMOSDB_CASSANDRA_PORT")
    cosmosCassandraUser = os.Getenv("COSMOSDB_CASSANDRA_USER")
    cosmosCassandraPassword = os.Getenv("COSMOSDB_CASSANDRA_PASSWORD")

    if cosmosCassandraContactPoint == "" || cosmosCassandraUser == "" || cosmosCassandraPassword == "" {
        log.Fatal("missing mandatory environment variables")
    }
}
```

Mithilfe davon werden anschließend verschiedene Vorgänge (Teil von `operations\setup.go`) in Azure Cosmos DB ausgeführt. Begonnen wird mit der Erstellung von `keyspace` und `table`.

Wie der Name bereits vermuten lässt, löscht die `DropKeySpaceIfExists`-Funktion das `keyspace`-Element nur, wenn es vorhanden ist.

```go
const dropKeyspace = "DROP KEYSPACE IF EXISTS %s"

func DropKeySpaceIfExists(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(dropKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to drop keyspace", err)
    }
    log.Println("Keyspace dropped")
}
```

Mit der Funktion `CreateKeySpace` wird `keyspace` erstellt (`user_profile`).

```go
const createKeyspace = "CREATE KEYSPACE %s WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"

func CreateKeySpace(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(createKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to create keyspace", err)
    }
    log.Println("Keyspace created")
}
```

Danach folgt die Tabellenerstellung (`user`), die von der Funktion `CreateUserTable` übernommen wird.

```go
const createTable = "CREATE TABLE %s.%s (user_id int PRIMARY KEY, user_name text, user_bcity text)"

func CreateUserTable(keyspace, table string, session *gocql.Session) {
    err := session.Query(fmt.Sprintf(createTable, keyspace, table)).Exec()
    if err != nil {
        log.Fatal("failed to create table ", err)
    }
    log.Println("Table created")
}
```

Nach der Erstellung des Keyspace und der Tabelle rufen Sie CRUD-Vorgänge auf (Teil von `operations\crud.go`). 

`InsertUser` wird zum Erstellen eines `User`-Elements verwendet. Die Benutzerinformationen (ID, Name und Ort) werden mithilfe von [`Bind`](https://godoc.org/github.com/gocql/gocql#Query.Bind) als Abfrageargumente festgelegt.

```go
const createQuery = "INSERT INTO %s.%s (user_id, user_name , user_bcity) VALUES (?,?,?)"

func InsertUser(keyspace, table string, session *gocql.Session, user model.User) {
    err := session.Query(fmt.Sprintf(createQuery, keyspace, table)).Bind(user.ID, user.Name, user.City).Exec()
    if err != nil {
        log.Fatal("Failed to create user", err)
    }
    log.Println("User created")
}
```

`FindUser` wird verwendet, um einen Benutzer (`model\user.go`) mithilfe einer bestimmten Benutzer-ID zu suchen, während [`Scan`](https://godoc.org/github.com/gocql/gocql#Iter.Scan) die (von Cassandra zurückgegebenen) Benutzerattribute an einzelne Variablen (`userid`, `name`, `city`) bindet. Dies ist nur eine der Methoden, bei denen Sie das Ergebnis als Suchabfrageergebnis verwenden können.

```go
const selectQuery = "SELECT * FROM %s.%s where user_id = ?"

func FindUser(keyspace, table string, id int, session *gocql.Session) model.User {
    var userid int
    var name, city string
    err := session.Query(fmt.Sprintf(selectQuery, keyspace, table)).Bind(id).Scan(&userid, &name, &city)

    if err != nil {
        if err == gocql.ErrNotFound {
            log.Printf("User with id %v does not exist\n", id)
        } else {
            log.Printf("Failed to find user with id %v - %v\n", id, err)
        }
    }
    return model.User{ID: userid, Name: name, City: city}
}
```

`FindAllUsers` wird zum Abrufen aller Benutzer verwendet. [`SliceMap`](https://godoc.org/github.com/gocql/gocql#Iter.SliceMap) wird als Kurzform verwendet, um alle Benutzerinformationen in Form eines Slice von `map`-Elementen zu erhalten. Stellen Sie sich jedes `map`-Element als Schlüssel-Wert-Paare vor. Dabei ist der Spaltenname (z. B. `user_id`) der Schlüssel zusammen mit dem jeweiligen Wert.

```go
const findAllUsersQuery = "SELECT * FROM %s.%s"

func FindAllUsers(keyspace, table string, session *gocql.Session) []model.User {
    var users []model.User
    results, _ := session.Query(fmt.Sprintf(findAllUsersQuery, keyspace, table)).Iter().SliceMap()

    for _, u := range results {
        users = append(users, mapToUser(u))
    }
    return users
}
```

Jedes `map`-Element mit Benutzerinformationen wird mithilfe der `mapToUser`-Funktion in ein `User`-Element konvertiert, das einfach den Wert aus der entsprechenden Spalte extrahiert und zum Erstellen einer Instanz der `User`-Struktur verwendet.

```go
func mapToUser(m map[string]interface{}) model.User {
    id, _ := m["user_id"].(int)
    name, _ := m["user_name"].(string)
    city, _ := m["user_bcity"].(string)

    return model.User{ID: id, Name: name, City: city}
}
```

## <a name="run-the-application"></a>Ausführen der Anwendung

Wie bereits erwähnt, akzeptiert die Anwendung Konnektivität und Anmeldeinformationen in Form von Umgebungsvariablen. 

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) in Ihrem Azure Cosmos DB-Konto die Option **Verbindungszeichenfolge** aus. 

    :::image type="content" source="./media/create-cassandra-go/copy-username-connection-string-azure-portal.png" alt-text="Anzeigen und Kopieren von Details im Azure-Portal auf der Seite „Verbindungszeichenfolge“":::

Kopieren Sie die Werte für die folgenden Attribute (`CONTACT POINT`, `PORT`, `USERNAME` und `PRIMARY PASSWORD`), und legen Sie sie auf die jeweiligen Umgebungsvariablen fest:

```shell
set COSMOSDB_CASSANDRA_CONTACT_POINT=<value for "CONTACT POINT">
set COSMOSDB_CASSANDRA_PORT=<value for "PORT">
set COSMOSDB_CASSANDRA_USER=<value for "USERNAME">
set COSMOSDB_CASSANDRA_PASSWORD=<value for "PRIMARY PASSWORD">
```

Wechseln Sie im Terminalfenster zum richtigen Ordner. Beispiel:

```shell
cd "C:\git-samples\azure-cosmosdb-cassandra-go-getting-started"
```

2. Führen Sie im Terminal den folgenden Befehl aus, um die Anwendung zu starten:

```shell
go run main.go
```

3. Im Terminalfenster werden Benachrichtigungen für die verschiedenen Vorgänge angezeigt, einschließlich Keyspace- und Tabelleneinrichtung, Benutzererstellung usw.

4. Öffnen Sie im Azure-Portal den **Daten-Explorer** , um diese neuen Daten abzufragen, zu ändern und zu verwenden. 

    :::image type="content" source="./media/create-cassandra-go/view-data-explorer-go-app.png" alt-text="Anzeigen der Daten im Daten-Explorer: Azure Cosmos DB":::

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie erfahren, wie Sie ein Azure Cosmos DB-Konto mit Cassandra-API erstellen und eine Go-App ausführen, die eine Cassandra-Datenbank und einen Cassandra-Container erstellt. Jetzt können Sie zusätzliche Daten in Ihr Azure Cosmos DB-Konto importieren. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB: Import Cassandra data](cassandra-import-data.md) (Azure Cosmos DB: Importieren von Cassandra-Daten)