---
title: 'Schnellstart: Herstellen einer Verbindung mit Go: Azure Database for PostgreSQL-Einzelserver'
description: Dieser Schnellstart enthält ein Beispiel für die Programmiersprache Go, das Sie nutzen können, um eine Verbindung mit einem Azure Database for PostgreSQL-Einzelserver herzustellen und Daten daraus abzufragen.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.devlang: go
ms.topic: quickstart
ms.date: 5/6/2019
ms.openlocfilehash: eb844790ac0ae97c281f6d2d7022bee559f545cd
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93331929"
---
# <a name="quickstart-use-go-language-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Schnellstart: Verwenden der Sprache Go zum Herstellen einer Verbindung mit einem Azure Database for PostgreSQL-Einzelserver sowie zum Abfragen von Daten

In dieser Schnellstartanleitung erfahren Sie, wie Sie mit Code in der Sprache [Go](https://golang.org/) (golang) eine Verbindung mit einer Azure-Datenbank für PostgreSQL herstellen. Es wird veranschaulicht, wie Sie SQL-Anweisungen zum Abfragen, Einfügen, Aktualisieren und Löschen von Daten in der Datenbank verwenden. In diesem Artikel wird davon ausgegangen, dass Sie mit der Entwicklung unter Verwendung der Sprache Go vertraut sind, aber noch keine Erfahrung mit Azure-Datenbank für PostgreSQL haben.

## <a name="prerequisites"></a>Voraussetzungen
In diesem Schnellstart werden die Ressourcen, die in den folgenden Anleitungen erstellt wurden, als Startpunkt verwendet:
- [Erstellen einer Datenbank – Portal](quickstart-create-server-database-portal.md)
- [Erstellen einer Datenbank – Azure-CLI](quickstart-create-server-database-azure-cli.md)

## <a name="install-go-and-pq-connector"></a>Installieren von Go und dem pq-Connector
Installieren Sie [Go](https://golang.org/doc/install) und den [reinen Go-Postgres-Treiber (pq)](https://github.com/lib/pq) auf Ihrem eigenen Computer. Führen Sie je nach Plattform die geeigneten Schritte aus:

### <a name="windows"></a>Windows
1. [Laden Sie Go für Microsoft Windows herunter](https://golang.org/dl/), und installieren Sie es gemäß der [Installationsanleitung](https://golang.org/doc/install).
2. Starten Sie die Eingabeaufforderung über das Startmenü.
3. Erstellen Sie einen Ordner für Ihr Projekt, z. B. „`mkdir  %USERPROFILE%\go\src\postgresqlgo`“.
4. Wechseln Sie zum Projektordner. Beispiel: `cd %USERPROFILE%\go\src\postgresqlgo`.
5. Legen Sie die Umgebungsvariable für GOPATH so fest, dass sie auf das Quellcodeverzeichnis verweist. `set GOPATH=%USERPROFILE%\go`.
6. Installieren Sie den [reinen Go-Postgres-Treiber (pq)](https://github.com/lib/pq) durch Ausführen des Befehls `go get github.com/lib/pq`.

   Zusammenfassung: Installieren Sie Go, und führen Sie anschließend an der Eingabeaufforderung die folgenden Befehle aus:
   ```cmd
   mkdir  %USERPROFILE%\go\src\postgresqlgo
   cd %USERPROFILE%\go\src\postgresqlgo
   set GOPATH=%USERPROFILE%\go
   go get github.com/lib/pq
   ```

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Starten Sie die Bash-Shell. 
2. Führen Sie `sudo apt-get install golang-go` aus, um Go zu installieren.
3. Erstellen Sie in Ihrem Basisverzeichnis einen Ordner für Ihr Projekt. Beispiel: `mkdir -p ~/go/src/postgresqlgo/`.
4. Wechseln Sie zu dem Ordner. Beispiel: `cd ~/go/src/postgresqlgo/`.
5. Legen Sie die GOPATH-Umgebungsvariable so fest, dass sie auf ein gültiges Quellverzeichnis verweist (beispielsweise auf den Go-Ordner Ihres aktuellen Basisverzeichnisses). Führen Sie in der Bash-Shell `export GOPATH=~/go` aus, um das Go-Verzeichnis als GOPATH für die aktuelle Shellsitzung hinzuzufügen.
6. Installieren Sie den [reinen Go-Postgres-Treiber (pq)](https://github.com/lib/pq) durch Ausführen des Befehls `go get github.com/lib/pq`.

   Zusammenfassung: Führen Sie die folgenden Bash-Befehle aus:
   ```bash
   sudo apt-get install golang-go
   mkdir -p ~/go/src/postgresqlgo/
   cd ~/go/src/postgresqlgo/
   export GOPATH=~/go/
   go get github.com/lib/pq
   ```

### <a name="apple-macos"></a>macOS (Apple)
1. Laden Sie Go gemäß der [Installationsanleitung](https://golang.org/doc/install) für Ihre Plattform herunter, und führen Sie die Installation durch. 
2. Starten Sie die Bash-Shell. 
3. Erstellen Sie in Ihrem Basisverzeichnis einen Ordner für Ihr Projekt. Beispiel: `mkdir -p ~/go/src/postgresqlgo/`.
4. Wechseln Sie zu dem Ordner. Beispiel: `cd ~/go/src/postgresqlgo/`.
5. Legen Sie die GOPATH-Umgebungsvariable so fest, dass sie auf ein gültiges Quellverzeichnis verweist (beispielsweise auf den Go-Ordner Ihres aktuellen Basisverzeichnisses). Führen Sie in der Bash-Shell `export GOPATH=~/go` aus, um das Go-Verzeichnis als GOPATH für die aktuelle Shellsitzung hinzuzufügen.
6. Installieren Sie den [reinen Go-Postgres-Treiber (pq)](https://github.com/lib/pq) durch Ausführen des Befehls `go get github.com/lib/pq`.

   Zusammenfassung: Installieren Sie Go, und führen Sie anschließend die folgenden Bash-Befehle aus:
   ```bash
   mkdir -p ~/go/src/postgresqlgo/
   cd ~/go/src/postgresqlgo/
   export GOPATH=~/go/
   go get github.com/lib/pq
   ```

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen
Rufen Sie die Verbindungsinformationen ab, die zum Herstellen einer Verbindung mit der Azure-Datenbank für PostgreSQL erforderlich sind. Sie benötigen den vollqualifizierten Servernamen und die Anmeldeinformationen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.
2. Klicken Sie im Azure-Portal im linken Menü auf **Alle Ressourcen** , und suchen Sie dann nach dem soeben erstellten Server, z.B. **mydemoserver**.
3. Klicken Sie auf den Servernamen.
4. Notieren Sie sich im Bereich **Übersicht** des Servers den **Servernamen** und den **Anmeldenamen des Serveradministrators**. Wenn Sie Ihr Kennwort vergessen haben, können Sie es in diesem Bereich auch zurücksetzen.
 :::image type="content" source="./media/connect-go/1-connection-string.png" alt-text="Azure Database for PostgreSQL-Servername":::

## <a name="build-and-run-go-code"></a>Erstellen und Ausführen von Go-Code 
1. Golang-Code kann in einem Nur-Text-Editor wie dem Editor in Microsoft Windows, [vi](https://manpages.ubuntu.com/manpages/xenial/man1/nvi.1.html#contenttoc5) oder [Nano](https://www.nano-editor.org/) (Ubuntu) oder TextEdit (macOS) geschrieben werden. Falls Sie eine umfangreichere interaktive Entwicklungsumgebung (Interactive Development Environment, IDE) bevorzugen, können Sie [GoLand](https://www.jetbrains.com/go/) von Jetbrains, [Visual Studio Code](https://code.visualstudio.com/) von Microsoft oder [Atom](https://atom.io/) verwenden.
2. Fügen Sie den Golang-Code aus den folgenden Abschnitten in Textdateien ein, und speichern Sie sie in Ihrem Projektordner mit der Dateiendung „\*.go“ – beispielsweise `%USERPROFILE%\go\src\postgresqlgo\createtable.go` (Windows-Pfad) oder `~/go/src/postgresqlgo/createtable.go` (Linux-Pfad).
3. Suchen Sie im Code nach den Konstanten `HOST`, `DATABASE`, `USER` und `PASSWORD`, und ersetzen Sie die Beispielwerte durch Ihre eigenen Werte.  
4. Starten Sie die Eingabeaufforderung oder die Bash-Shell. Wechseln Sie in das Verzeichnis Ihres Projektordners. Beispiel für Windows: `cd %USERPROFILE%\go\src\postgresqlgo\`. Beispiel für Linux: `cd ~/go/src/postgresqlgo/`. Einige der genannten IDE-Umgebungen bieten Debug- und Laufzeitfunktionen ohne Shell-Befehle.
5. Geben Sie den Befehl `go run createtable.go` ein, um den Code auszuführen und die Anwendung zu kompilieren und auszuführen. 
6. Falls Sie aus dem Code eine native Anwendung erstellen möchten, führen Sie stattdessen `go build createtable.go` aus, und starten Sie anschließend `createtable.exe`, um die Anwendung auszuführen.

## <a name="connect-and-create-a-table"></a>Herstellen einer Verbindung und Erstellen einer Tabelle
Verwenden Sie den folgenden Code, um eine Verbindung herzustellen und eine Tabelle zu erstellen, indem Sie eine **CREATE TABLE** -SQL-Anweisung gefolgt von **INSERT INTO** -SQL-Anweisungen zum Hinzufügen von Zeilen zur Tabelle nutzen.

Der Code importiert drei Pakete: das [sql-Paket](https://golang.org/pkg/database/sql/), das [pq-Paket](https://godoc.org/github.com/lib/pq) als Treiber für die Kommunikation mit dem PostgresSQL-Server und das [fmt-Paket](https://golang.org/pkg/fmt/) für die Ein- und Ausgabe in der Befehlszeile.

Mit dem Code wird die [sql.Open()](https://godoc.org/github.com/lib/pq#Open)-Methode aufgerufen, um eine Verbindung mit der Azure Database for PostgreSQL-Datenbank herzustellen, und die Verbindung wird mit der [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping)-Methode überprüft. Ein [Datenbankhandle](https://golang.org/pkg/database/sql/#DB) wird durchgängig für den Verbindungspool des Datenbankservers eingesetzt. Im Code wird die [Exec()](https://golang.org/pkg/database/sql/#DB.Exec)-Methode mehrfach aufgerufen, um mehrere SQL-Befehle auszuführen. Es wird jedes Mal eine benutzerdefinierte checkError()-Methode verwendet, um das Auftreten eines Fehlers zu überprüfen, und der Vorgang wird beendet, falls dies der Fall ist.

Ersetzen Sie die Parameter `HOST`, `DATABASE`, `USER` und `PASSWORD` durch Ihre eigenen Werte. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mydemoserver.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mydemoserver"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Drop previous table of same name if one exists.
    _, err = db.Exec("DROP TABLE IF EXISTS inventory;")
    checkError(err)
    fmt.Println("Finished dropping table (if existed)")

    // Create table.
    _, err = db.Exec("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
    checkError(err)
    fmt.Println("Finished creating table")

    // Insert some data into table.
    sql_statement := "INSERT INTO inventory (name, quantity) VALUES ($1, $2);"
    _, err = db.Exec(sql_statement, "banana", 150)
    checkError(err)
    _, err = db.Exec(sql_statement, "orange", 154)
    checkError(err)
    _, err = db.Exec(sql_statement, "apple", 100)
    checkError(err)
    fmt.Println("Inserted 3 rows of data")
}
```

## <a name="read-data"></a>Lesen von Daten
Verwenden Sie den folgenden Code, um die Daten mit einer **SELECT** -SQL-Anweisung zu verbinden und zu lesen. 

Der Code importiert drei Pakete: das [sql-Paket](https://golang.org/pkg/database/sql/), das [pq-Paket](https://godoc.org/github.com/lib/pq) als Treiber für die Kommunikation mit dem PostgresSQL-Server und das [fmt-Paket](https://golang.org/pkg/fmt/) für die Ein- und Ausgabe in der Befehlszeile.

Mit dem Code wird die [sql.Open()](https://godoc.org/github.com/lib/pq#Open)-Methode aufgerufen, um eine Verbindung mit der Azure Database for PostgreSQL-Datenbank herzustellen, und die Verbindung wird mit der [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping)-Methode überprüft. Ein [Datenbankhandle](https://golang.org/pkg/database/sql/#DB) wird durchgängig für den Verbindungspool des Datenbankservers eingesetzt. Die SELECT-Abfrage wird ausgeführt, indem die [db.Query()](https://golang.org/pkg/database/sql/#DB.Query)-Methode aufgerufen wird, und die sich ergebenden Zeilen werden in einer Variablen vom Typ [rows](https://golang.org/pkg/database/sql/#Rows) gespeichert. Der Code liest die Spaltendatenwerte in der aktuellen Zeile mit der [rows.Scan()](https://golang.org/pkg/database/sql/#Rows.Scan)-Methode und führt eine Schleife für die Zeilen durch, indem der Iterator [rows.Next()](https://golang.org/pkg/database/sql/#Rows.Next) verwendet wird, bis keine weiteren Zeilen mehr vorhanden sind. Die Spaltenwerte jeder Zeile werden auf der Konsole ausgegeben. Es wird jedes Mal eine benutzerdefinierte checkError()-Methode verwendet, um das Auftreten eines Fehlers zu überprüfen, und der Vorgang wird beendet, falls dies der Fall ist.

Ersetzen Sie die Parameter `HOST`, `DATABASE`, `USER` und `PASSWORD` durch Ihre eigenen Werte. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mydemoserver.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mydemoserver"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Read rows from table.
    var id int
    var name string
    var quantity int

    sql_statement := "SELECT * from inventory;"
    rows, err := db.Query(sql_statement)
    checkError(err)
    defer rows.Close()

    for rows.Next() {
        switch err := rows.Scan(&id, &name, &quantity); err {
        case sql.ErrNoRows:
            fmt.Println("No rows were returned")
        case nil:
            fmt.Printf("Data row = (%d, %s, %d)\n", id, name, quantity)
        default:
            checkError(err)
        }
    }
}
```

## <a name="update-data"></a>Aktualisieren von Daten
Verwenden Sie den folgenden Code, um eine Verbindung herzustellen und die Daten mit der SQL-Anweisung **UPDATE** zu aktualisieren.

Der Code importiert drei Pakete: das [sql-Paket](https://golang.org/pkg/database/sql/), das [pq-Paket](https://godoc.org/github.com/lib/pq) als Treiber für die Kommunikation mit dem Postgres-Server und das [fmt-Paket](https://golang.org/pkg/fmt/) für die Ein- und Ausgabe in der Befehlszeile.

Mit dem Code wird die [sql.Open()](https://godoc.org/github.com/lib/pq#Open)-Methode aufgerufen, um eine Verbindung mit der Azure Database for PostgreSQL-Datenbank herzustellen, und die Verbindung wird mit der [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping)-Methode überprüft. Ein [Datenbankhandle](https://golang.org/pkg/database/sql/#DB) wird durchgängig für den Verbindungspool des Datenbankservers eingesetzt. Im Code wird die [Exec()](https://golang.org/pkg/database/sql/#DB.Exec)-Methode aufgerufen, um die SQL-Anweisung auszuführen, mit der die Tabelle aktualisiert wird. Eine benutzerdefinierte checkError()-Methode wird verwendet, um das Auftreten eines Fehlers zu überprüfen, und der Vorgang wird beendet, falls dies der Fall ist.

Ersetzen Sie die Parameter `HOST`, `DATABASE`, `USER` und `PASSWORD` durch Ihre eigenen Werte. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mydemoserver.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mydemoserver"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Modify some data in table.
    sql_statement := "UPDATE inventory SET quantity = $2 WHERE name = $1;"
    _, err = db.Exec(sql_statement, "banana", 200)
    checkError(err)
    fmt.Println("Updated 1 row of data")
}
```

## <a name="delete-data"></a>Löschen von Daten
Verwenden Sie den folgenden Code, um die Daten mit einer **DELETE** -SQL-Anweisung zu verbinden und zu löschen. 

Der Code importiert drei Pakete: das [sql-Paket](https://golang.org/pkg/database/sql/), das [pq-Paket](https://godoc.org/github.com/lib/pq) als Treiber für die Kommunikation mit dem Postgres-Server und das [fmt-Paket](https://golang.org/pkg/fmt/) für die Ein- und Ausgabe in der Befehlszeile.

Mit dem Code wird die [sql.Open()](https://godoc.org/github.com/lib/pq#Open)-Methode aufgerufen, um eine Verbindung mit der Azure Database for PostgreSQL-Datenbank herzustellen, und die Verbindung wird mit der [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping)-Methode überprüft. Ein [Datenbankhandle](https://golang.org/pkg/database/sql/#DB) wird durchgängig für den Verbindungspool des Datenbankservers eingesetzt. Im Code wird die [Exec()](https://golang.org/pkg/database/sql/#DB.Exec)-Methode aufgerufen, um die SQL-Anweisung auszuführen, die eine Zeile aus der Tabelle löscht. Eine benutzerdefinierte checkError()-Methode wird verwendet, um das Auftreten eines Fehlers zu überprüfen, und der Vorgang wird beendet, falls dies der Fall ist.

Ersetzen Sie die Parameter `HOST`, `DATABASE`, `USER` und `PASSWORD` durch Ihre eigenen Werte. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mydemoserver.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mydemoserver"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Delete some data from table.
    sql_statement := "DELETE FROM inventory WHERE name = $1;"
    _, err = db.Exec(sql_statement, "orange")
    checkError(err)
    fmt.Println("Deleted 1 row of data")
}
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe mit dem folgenden Befehl, um alle in dieser Schnellstartanleitung verwendeten Ressourcen zu bereinigen:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Migrieren der Datenbank mit Export und Import](./howto-migrate-using-export-and-import.md)
