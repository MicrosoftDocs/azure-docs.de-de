---
title: 'Schnellstart: Herstellen einer Verbindung unter Verwendung von Go: Azure Database for MySQL'
description: Diese Schnellstartanleitung enthält mehrere Go-Codebeispiele, mit deren Hilfe Sie eine Verbindung mit Azure-Datenbank für MySQL herstellen und Daten daraus abfragen können.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.devlang: go
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: 0da32fb404972556a1c4c964eec692bad20a4db1
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535757"
---
# <a name="quickstart-use-go-language-to-connect-and-query-data-in-azure-database-for-mysql"></a>Schnellstart: Verwenden von Go zum Herstellen einer Verbindung und zum Abfragen von Daten in Azure Database for MySQL

In dieser Schnellstartanleitung erfahren Sie, wie Sie unter Windows, Linux (Ubuntu) und macOS (Apple) mithilfe von Code in der Programmiersprache [Go](https://golang.org/) eine Verbindung mit einer Azure Database for MySQL-Instanz herstellen. Es wird veranschaulicht, wie Sie SQL-Anweisungen zum Abfragen, Einfügen, Aktualisieren und Löschen von Daten in der Datenbank verwenden. Bei den Schritten in diesem Artikel wird davon ausgegangen, dass Sie mit der Go-Entwicklung vertraut sind und noch keine Erfahrung mit Azure Database for MySQL haben.

## <a name="prerequisites"></a>Voraussetzungen
In diesem Schnellstart werden die Ressourcen, die in den folgenden Anleitungen erstellt wurden, als Startpunkt verwendet:
- [Erstellen eines Servers für Azure-Datenbank für MySQL mithilfe des Azure-Portals](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Erstellen eines Servers für Azure-Datenbank für MySQL mithilfe der Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT] 
> Sicherstellen, dass der IP-Adresse, über die Sie eine Verbindung herstellen, die Firewallregeln des Servers über das [Azure-Portal](./howto-manage-firewall-using-portal.md) oder die [Azure CLI](./howto-manage-firewall-using-cli.md) hinzugefügt wurden

## <a name="install-go-and-mysql-connector"></a>Installieren von Go und dem MySQL-Connector
Installieren Sie auf Ihrem Computer [Go](https://golang.org/doc/install) und den [„go-sql“-Treiber für MySQL](https://github.com/go-sql-driver/mysql#installation). Führen Sie je nach Plattform die folgenden Schritte im entsprechenden Abschnitt aus:

### <a name="windows"></a>Windows
1. [Laden Sie Go für Microsoft Windows herunter](https://golang.org/dl/), und installieren Sie es gemäß der [Installationsanleitung](https://golang.org/doc/install).
2. Starten Sie die Eingabeaufforderung über das Startmenü.
3. Erstellen Sie einen Ordner für Ihr Projekt. Beispiel: `mkdir  %USERPROFILE%\go\src\mysqlgo`.
4. Wechseln Sie zum Projektordner. Beispiel: `cd %USERPROFILE%\go\src\mysqlgo`.
5. Legen Sie die Umgebungsvariable für GOPATH so fest, dass sie auf das Quellcodeverzeichnis verweist. `set GOPATH=%USERPROFILE%\go`.
6. Führen Sie den Befehl `go get github.com/go-sql-driver/mysql` aus, um den [Go-SQL-Treiber für MySQL](https://github.com/go-sql-driver/mysql#installation) zu installieren.

   Zusammenfassung: Installieren Sie Go, und führen Sie anschließend an der Eingabeaufforderung die folgenden Befehle aus:
   ```cmd
   mkdir  %USERPROFILE%\go\src\mysqlgo
   cd %USERPROFILE%\go\src\mysqlgo
   set GOPATH=%USERPROFILE%\go
   go get github.com/go-sql-driver/mysql
   ```

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Starten Sie die Bash-Shell. 
2. Führen Sie `sudo apt-get install golang-go` aus, um Go zu installieren.
3. Erstellen Sie in Ihrem Basisverzeichnis einen Ordner für Ihr Projekt. Beispiel: `mkdir -p ~/go/src/mysqlgo/`.
4. Wechseln Sie zu dem Ordner. Beispiel: `cd ~/go/src/mysqlgo/`.
5. Legen Sie die GOPATH-Umgebungsvariable so fest, dass sie auf ein gültiges Quellverzeichnis verweist (beispielsweise auf den Go-Ordner Ihres aktuellen Basisverzeichnisses). Führen Sie in der Bash-Shell `export GOPATH=~/go` aus, um das Go-Verzeichnis als GOPATH für die aktuelle Shellsitzung hinzuzufügen.
6. Führen Sie den Befehl `go get github.com/go-sql-driver/mysql` aus, um den [Go-SQL-Treiber für MySQL](https://github.com/go-sql-driver/mysql#installation) zu installieren.

   Zusammenfassung: Führen Sie die folgenden Bash-Befehle aus:
   ```bash
   sudo apt-get install golang-go
   mkdir -p ~/go/src/mysqlgo/
   cd ~/go/src/mysqlgo/
   export GOPATH=~/go/
   go get github.com/go-sql-driver/mysql
   ```

### <a name="apple-macos"></a>macOS (Apple)
1. Laden Sie Go gemäß der [Installationsanleitung](https://golang.org/doc/install) für Ihre Plattform herunter, und führen Sie die Installation durch. 
2. Starten Sie die Bash-Shell.
3. Erstellen Sie in Ihrem Basisverzeichnis einen Ordner für Ihr Projekt. Beispiel: `mkdir -p ~/go/src/mysqlgo/`.
4. Wechseln Sie zu dem Ordner. Beispiel: `cd ~/go/src/mysqlgo/`.
5. Legen Sie die GOPATH-Umgebungsvariable so fest, dass sie auf ein gültiges Quellverzeichnis verweist (beispielsweise auf den Go-Ordner Ihres aktuellen Basisverzeichnisses). Führen Sie in der Bash-Shell `export GOPATH=~/go` aus, um das Go-Verzeichnis als GOPATH für die aktuelle Shellsitzung hinzuzufügen.
6. Führen Sie den Befehl `go get github.com/go-sql-driver/mysql` aus, um den [Go-SQL-Treiber für MySQL](https://github.com/go-sql-driver/mysql#installation) zu installieren.

   Zusammenfassung: Installieren Sie Go, und führen Sie anschließend die folgenden Bash-Befehle aus:
   ```bash
   mkdir -p ~/go/src/mysqlgo/
   cd ~/go/src/mysqlgo/
   export GOPATH=~/go/
   go get github.com/go-sql-driver/mysql
   ```

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen
Rufen Sie die Verbindungsinformationen ab, die zum Herstellen einer Verbindung mit der Azure SQL-Datenbank für MySQL erforderlich sind. Sie benötigen den vollqualifizierten Servernamen und die Anmeldeinformationen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.
2. Klicken Sie im Azure-Portal im linken Menü auf **Alle Ressourcen**, und suchen Sie dann nach dem soeben erstellten Server, z.B. **mydemoserver**.
3. Klicken Sie auf den Servernamen.
4. Notieren Sie sich im Bereich **Übersicht** des Servers den **Servernamen** und den **Anmeldenamen des Serveradministrators**. Wenn Sie Ihr Kennwort vergessen haben, können Sie es in diesem Bereich auch zurücksetzen.
 :::image type="content" source="./media/connect-go/1_server-overview-name-login.png" alt-text="Servername für Azure-Datenbank für MySQL":::
   

## <a name="build-and-run-go-code"></a>Erstellen und Ausführen von Go-Code 
1. Golang-Code kann in einem einfachen Text-Editor wie dem Editor (Microsoft Windows), [vi](https://manpages.ubuntu.com/manpages/xenial/man1/nvi.1.html#contenttoc5) oder [Nano](https://www.nano-editor.org/) (Ubuntu) oder TextEdit (macOS) geschrieben werden. Falls Sie eine umfangreichere interaktive Entwicklungsumgebung (Interactive Development Environment, IDE) bevorzugen, können Sie [Gogland](https://www.jetbrains.com/go/) von Jetbrains, [Visual Studio Code](https://code.visualstudio.com/) von Microsoft oder [Atom](https://atom.io/) verwenden.
2. Fügen Sie den Go-Code aus den Abschnitten weiter unten in Textdateien ein, und speichern Sie sie in Ihrem Projektordner mit der Dateierweiterung „\*.go“ – beispielsweise `%USERPROFILE%\go\src\mysqlgo\createtable.go` (Windows-Pfad) oder `~/go/src/mysqlgo/createtable.go` (Linux-Pfad).
3. Suchen Sie im Code nach den Konstanten `HOST`, `DATABASE`, `USER` und `PASSWORD`, und ersetzen Sie dann die Beispielwerte durch Ihre eigenen Werte. 
4. Starten Sie die Eingabeaufforderung oder Bash-Shell. Wechseln Sie in das Verzeichnis Ihres Projektordners. Beispiel für Windows: `cd %USERPROFILE%\go\src\mysqlgo\`. Beispiel für Linux: `cd ~/go/src/mysqlgo/`.  Einige der genannten IDE-Editoren bieten Debug- und Laufzeitfunktionen ohne Shell-Befehle.
5. Geben Sie den Befehl `go run createtable.go` ein, um den Code auszuführen und die Anwendung zu kompilieren und auszuführen. 
6. Falls Sie aus dem Code eine native Anwendung erstellen möchten, führen Sie stattdessen `go build createtable.go` aus, und starten Sie anschließend `createtable.exe`, um die Anwendung auszuführen.

## <a name="connect-create-table-and-insert-data"></a>Herstellen der Verbindung, Erstellen der Tabelle und Einfügen von Daten
Verwenden Sie den folgenden Code, um eine Verbindung mit dem Server herzustellen, eine Tabelle zu erstellen und die Daten unter Verwendung einer SQL-Anweisung des Typs **INSERT** zu laden. 

Der Code importiert drei Pakete: das [SQL-Paket](https://golang.org/pkg/database/sql/), den [Go-SQL-Treiber für MySQL](https://github.com/go-sql-driver/mysql#installation) als Treiber für die Kommunikation mit der Azure-Datenbank für MySQL und das [FMT-Paket](https://golang.org/pkg/fmt/) für Ein- und Ausgaben in der Befehlszeile.

Der Code ruft die [sql.Open()](http://go-database-sql.org/accessing.html)-Methode auf, um eine Verbindung mit Azure Database for MySQL herzustellen, und überprüft die Verbindung mit der [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping)-Methode. Ein [Datenbankhandle](https://golang.org/pkg/database/sql/#DB) wird durchgängig für den Verbindungspool des Datenbankservers eingesetzt. Der Code ruft mehrmals die [Exec()](https://golang.org/pkg/database/sql/#DB.Exec)-Methode auf, um mehrere DDL-Befehle auszuführen. Außerdem verwendet der Code die Methoden [Prepare()](http://go-database-sql.org/prepared.html) und „Exec()“, um vorbereitete Anweisungen mit unterschiedlichen Parametern auszuführen und drei Zeilen einzufügen. Dabei wird jedes Mal mit einer benutzerdefinierten checkError()-Methode überprüft, ob ein Fehler aufgetreten ist, und der Vorgang ggf. mithilfe von „panic“ beendet.

Ersetzen Sie die Konstanten `host`, `database`, `user` und `password` durch Ihre eigenen Werte. 

```Go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "mydemoserver.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@mydemoserver"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Drop previous table of same name if one exists.
    _, err = db.Exec("DROP TABLE IF EXISTS inventory;")
    checkError(err)
    fmt.Println("Finished dropping table (if existed).")

    // Create table.
    _, err = db.Exec("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
    checkError(err)
    fmt.Println("Finished creating table.")

    // Insert some data into table.
    sqlStatement, err := db.Prepare("INSERT INTO inventory (name, quantity) VALUES (?, ?);")
    res, err := sqlStatement.Exec("banana", 150)
    checkError(err)
    rowCount, err := res.RowsAffected()
    fmt.Printf("Inserted %d row(s) of data.\n", rowCount)

    res, err = sqlStatement.Exec("orange", 154)
    checkError(err)
    rowCount, err = res.RowsAffected()
    fmt.Printf("Inserted %d row(s) of data.\n", rowCount)

    res, err = sqlStatement.Exec("apple", 100)
    checkError(err)
    rowCount, err = res.RowsAffected()
    fmt.Printf("Inserted %d row(s) of data.\n", rowCount)
    fmt.Println("Done.")
}

```

## <a name="read-data"></a>Lesen von Daten
Verwenden Sie den folgenden Code, um die Daten mit einer SQL-Anweisung des Typs **SELECT** zu verbinden und zu lesen. 

Der Code importiert drei Pakete: das [SQL-Paket](https://golang.org/pkg/database/sql/), den [Go-SQL-Treiber für MySQL](https://github.com/go-sql-driver/mysql#installation) als Treiber für die Kommunikation mit der Azure-Datenbank für MySQL und das [FMT-Paket](https://golang.org/pkg/fmt/) für Ein- und Ausgaben in der Befehlszeile.

Der Code ruft die [sql.Open()](http://go-database-sql.org/accessing.html)-Methode auf, um eine Verbindung mit Azure-Datenbank für MySQL herzustellen, und überprüft die Verbindung mit der [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping)-Methode. Ein [Datenbankhandle](https://golang.org/pkg/database/sql/#DB) wird durchgängig für den Verbindungspool des Datenbankservers eingesetzt. Der Code ruft die [Query()](https://golang.org/pkg/database/sql/#DB.Query)-Methode auf, um den select-Befehl auszuführen. Anschließend wird [Next()](https://golang.org/pkg/database/sql/#Rows.Next) ausgeführt, um das Resultset zu durchlaufen, und [Scan()](https://golang.org/pkg/database/sql/#Rows.Scan), um die Spaltenwerte zu analysieren und den Wert in Variablen zu speichern. Dabei wird jedes Mal mit einer benutzerdefinierten checkError()-Methode überprüft, ob ein Fehler aufgetreten ist, und der Vorgang ggf. mithilfe von „panic“ beendet.

Ersetzen Sie die Konstanten `host`, `database`, `user` und `password` durch Ihre eigenen Werte. 

```Go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "mydemoserver.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@mydemoserver"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Variables for printing column data when scanned.
    var (
        id       int
        name     string
        quantity int
    )

    // Read some data from the table.
    rows, err := db.Query("SELECT id, name, quantity from inventory;")
    checkError(err)
    defer rows.Close()
    fmt.Println("Reading data:")
    for rows.Next() {
        err := rows.Scan(&id, &name, &quantity)
        checkError(err)
        fmt.Printf("Data row = (%d, %s, %d)\n", id, name, quantity)
    }
    err = rows.Err()
    checkError(err)
    fmt.Println("Done.")
}
```

## <a name="update-data"></a>Aktualisieren von Daten
Verwenden Sie den folgenden Code, um eine Verbindung herzustellen und die Daten per **UPDATE**-SQL-Anweisung zu aktualisieren. 

Der Code importiert drei Pakete: das [SQL-Paket](https://golang.org/pkg/database/sql/), den [Go-SQL-Treiber für MySQL](https://github.com/go-sql-driver/mysql#installation) als Treiber für die Kommunikation mit der Azure-Datenbank für MySQL und das [FMT-Paket](https://golang.org/pkg/fmt/) für Ein- und Ausgaben in der Befehlszeile.

Der Code ruft die [sql.Open()](http://go-database-sql.org/accessing.html)-Methode auf, um eine Verbindung mit Azure-Datenbank für MySQL herzustellen, und überprüft die Verbindung mit der [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping)-Methode. Ein [Datenbankhandle](https://golang.org/pkg/database/sql/#DB) wird durchgängig für den Verbindungspool des Datenbankservers eingesetzt. Der Code ruft die [Exec()](https://golang.org/pkg/database/sql/#DB.Exec)-Methode auf, um den update-Befehl auszuführen. Dabei wird jedes Mal mit einer benutzerdefinierten checkError()-Methode überprüft, ob ein Fehler aufgetreten ist, und der Vorgang ggf. mithilfe von „panic“ beendet.

Ersetzen Sie die Konstanten `host`, `database`, `user` und `password` durch Ihre eigenen Werte. 

```Go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "mydemoserver.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@mydemoserver"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Modify some data in table.
    rows, err := db.Exec("UPDATE inventory SET quantity = ? WHERE name = ?", 200, "banana")
    checkError(err)
    rowCount, err := rows.RowsAffected()
    fmt.Printf("Updated %d row(s) of data.\n", rowCount)
    fmt.Println("Done.")
}
```

## <a name="delete-data"></a>Löschen von Daten
Verwenden Sie den folgenden Code, um eine Verbindung herzustellen und die Daten mithilfe einer SQL-Anweisung vom Typ **DELETE** zu entfernen. 

Der Code importiert drei Pakete: das [SQL-Paket](https://golang.org/pkg/database/sql/), den [Go-SQL-Treiber für MySQL](https://github.com/go-sql-driver/mysql#installation) als Treiber für die Kommunikation mit der Azure-Datenbank für MySQL und das [FMT-Paket](https://golang.org/pkg/fmt/) für Ein- und Ausgaben in der Befehlszeile.

Der Code ruft die [sql.Open()](http://go-database-sql.org/accessing.html)-Methode auf, um eine Verbindung mit Azure-Datenbank für MySQL herzustellen, und überprüft die Verbindung mit der [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping)-Methode. Ein [Datenbankhandle](https://golang.org/pkg/database/sql/#DB) wird durchgängig für den Verbindungspool des Datenbankservers eingesetzt. Der Code ruft die [Exec()](https://golang.org/pkg/database/sql/#DB.Exec)-Methode auf, um den delete-Befehl auszuführen. Dabei wird jedes Mal mit einer benutzerdefinierten checkError()-Methode überprüft, ob ein Fehler aufgetreten ist, und der Vorgang ggf. mithilfe von „panic“ beendet.

Ersetzen Sie die Konstanten `host`, `database`, `user` und `password` durch Ihre eigenen Werte. 

```Go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "mydemoserver.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@mydemoserver"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Modify some data in table.
    rows, err := db.Exec("DELETE FROM inventory WHERE name = ?", "orange")
    checkError(err)
    rowCount, err := rows.RowsAffected()
    fmt.Printf("Deleted %d row(s) of data.\n", rowCount)
    fmt.Println("Done.")
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
> [Migrieren der Datenbank mit Export und Import](./concepts-migrate-import-export.md)
