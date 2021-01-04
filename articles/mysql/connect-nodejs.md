---
title: 'Schnellstart: Herstellen einer Verbindung unter Verwendung von Node.js: Azure Database for MySQL'
description: Diese Schnellstartanleitung enthält mehrere Node.js-Codebeispiele, mit deren Hilfe Sie eine Verbindung mit Azure-Datenbank für MySQL herstellen und Daten daraus abfragen können.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: d1291b645e987f33bd2035580587650b843f1771
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535655"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-in-azure-database-for-mysql"></a>Schnellstart: Verwenden von Node.js zum Herstellen von Verbindungen mit Daten und Abfragen von Daten in Azure Database for MySQL

In dieser Schnellstartanleitung stellen Sie unter Verwendung von Node.js eine Verbindung mit einer Azure Database for MySQL-Instanz her. Anschließend verwenden Sie SQL-Anweisungen, um Daten in der Datenbank über Mac-, Ubuntu Linux- und Windows-Plattformen abzufragen, einzufügen, zu aktualisieren und zu löschen. 

In diesem Thema wird davon ausgegangen, dass Sie mit der Node.js-Entwicklung vertraut sind, aber noch keine Erfahrung mit Azure Database for MySQL haben.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Ein Azure Database for MySQL-Server. [Erstellen Sie einen Azure Database for MySQL-Server mithilfe des Azure-Portals](quickstart-create-mysql-server-database-using-azure-portal.md), oder [erstellen Sie einen Azure Database for MySQL-Server mithilfe der Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!IMPORTANT] 
> Sicherstellen, dass der IP-Adresse, über die Sie eine Verbindung herstellen, die Firewallregeln des Servers über das [Azure-Portal](./howto-manage-firewall-using-portal.md) oder die [Azure CLI](./howto-manage-firewall-using-cli.md) hinzugefügt wurden

## <a name="install-nodejs-and-the-mysql-connector"></a>Installation von Node.js und MySQL-Connector

Befolgen Sie zum Installieren von [Node.js](https://nodejs.org) die plattformabhängigen Anweisungen im entsprechenden Abschnitt. Verwenden Sie npm, um das Paket [mysql](https://www.npmjs.com/package/mysql) und die entsprechenden Abhängigkeiten in Ihrem Projektordner zu installieren.

### <a name="windows"></a>Windows

1. Besuchen Sie die [Node.js-Seite „Downloads“](https://nodejs.org/en/download/), und wählen Sie dann die gewünschte Windows Installer-Option aus.
2. Erstellen Sie einen lokalen Projektordner, z.B. `nodejsmysql`. 
3. Öffnen Sie die Eingabeaufforderung, und wechseln Sie zum Projektordner, z.B. mit `cd c:\nodejsmysql\`.
4. Führen Sie das NPM-Tool aus, um die mysql-Bibliothek im Projektordner zu installieren.

   ```cmd
   cd c:\nodejsmysql\
   "C:\Program Files\nodejs\npm" install mysql
   "C:\Program Files\nodejs\npm" list
   ```

5. Überprüfen Sie die Installation, indem Sie sich den Ausgabetext von `npm list` ansehen. Die Versionsnummer kann hiervon abweichen, wenn neue Patches veröffentlicht werden.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Führen Sie die folgenden Befehle zum Installieren von **Node.js** und **npm** (Paket-Manager für Node.js) aus.

   ```bash
   sudo apt-get install -y nodejs npm
   ```

2. Führen Sie die folgenden Befehle aus, um den Projektordner `mysqlnodejs` zu erstellen und das mysql-Paket in diesem Ordner zu installieren.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```
3. Überprüfen Sie die Installation, indem Sie sich den Ausgabetext von „npm list“ ansehen. Die Versionsnummer kann hiervon abweichen, wenn neue Patches veröffentlicht werden.

### <a name="macos"></a>macOS

1. Geben Sie die folgenden Befehle zum Installieren von **brew** ein, einem einfach zu verwendenden Paket-Manager für macOS und **Node.js**.

   ```bash
   ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
   brew install node
   ```
2. Führen Sie die folgenden Befehle aus, um den Projektordner `mysqlnodejs` zu erstellen und das mysql-Paket in diesem Ordner zu installieren.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```

3. Überprüfen Sie die Installation, indem Sie sich den Ausgabetext von `npm list` ansehen. Die Versionsnummer kann hiervon abweichen, wenn neue Patches veröffentlicht werden.

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen

Rufen Sie die Verbindungsinformationen ab, die zum Herstellen einer Verbindung mit der Azure SQL-Datenbank für MySQL erforderlich sind. Sie benötigen den vollqualifizierten Servernamen und die Anmeldeinformationen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.
2. Wählen Sie im Azure-Portal im linken Menü **Alle Ressourcen** aus, und suchen Sie dann nach dem Server, den Sie erstellt haben (z.B. **mydemoserver**).
3. Wählen Sie den Servernamen aus.
4. Notieren Sie sich im Bereich **Übersicht** des Servers den **Servernamen** und den **Anmeldenamen des Serveradministrators**. Wenn Sie Ihr Kennwort vergessen haben, können Sie es in diesem Bereich auch zurücksetzen.
 :::image type="content" source="./media/connect-nodejs/server-name-azure-database-mysql.png" alt-text="Servername für Azure-Datenbank für MySQL":::

## <a name="running-the-javascript-code-in-nodejs"></a>Ausführen des JavaScript-Codes in Node.js

1. Fügen Sie den JavaScript-Code in Textdateien ein, und speichern Sie ihn dann in einem Projektordner mit der Dateierweiterung „.js“, z.B. „C:\nodejsmysql\createtable.js“ oder „/home/username/nodejsmysql/createtable.js“.
2. Öffnen Sie die Eingabeaufforderung oder die Bash-Shell, und wechseln Sie zu Ihrem Projektordner: `cd nodejsmysql`.
3. Um die Anwendung auszuführen, geben Sie den node-Befehl gefolgt vom Dateinamen ein, z.B. `node createtable.js`.
4. Unter Windows müssen Sie unter Umständen den vollständigen Pfad verwenden, um die Node-Anwendung zu starten, z.B. `"C:\Program Files\nodejs\node.exe" createtable.js`, wenn sich die Node-Anwendung nicht unter dem Pfad der Umgebungsvariablen befindet.

## <a name="connect-create-table-and-insert-data"></a>Herstellen der Verbindung, Erstellen der Tabelle und Einfügen von Daten

Verwenden Sie den folgenden Code, um eine Verbindung herzustellen und die Daten zu laden, indem Sie die SQL-Anweisungen **CREATE TABLE** und **INSERT INTO** verwenden.

Die [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections)-Methode wird verwendet, um die Kommunikation mit dem MySQL-Server zu ermöglichen. Die Funktion [connect()](https://github.com/mysqljs/mysql#establishing-connections) wird verwendet, um die Verbindung mit dem Server herzustellen. Die Funktion [query()](https://github.com/mysqljs/mysql#performing-queries) wird verwendet, um die SQL-Abfrage für die MySQL-Datenbank auszuführen. 

Ersetzen Sie die Parameter `host`, `user`, `password` und `database` durch die Werte, die Sie beim Erstellen des Servers und der Datenbank angegeben haben.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
    if (err) { 
        console.log("!!! Cannot connect !!! Error:");
        throw err;
    }
    else
    {
       console.log("Connection established.");
           queryDatabase();
    }   
});

function queryDatabase(){
       conn.query('DROP TABLE IF EXISTS inventory;', function (err, results, fields) { 
            if (err) throw err; 
            console.log('Dropped inventory table if existed.');
        })
       conn.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);', 
            function (err, results, fields) {
                if (err) throw err;
            console.log('Created inventory table.');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['banana', 150], 
            function (err, results, fields) {
                if (err) throw err;
            else console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['orange', 154], 
            function (err, results, fields) {
                if (err) throw err;
            console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['apple', 100], 
        function (err, results, fields) {
                if (err) throw err;
            console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.end(function (err) { 
        if (err) throw err;
        else  console.log('Done.') 
        });
};
```

## <a name="read-data"></a>Lesen von Daten

Verwenden Sie den folgenden Code, um die Daten mit einer SQL-Anweisung des Typs **SELECT** zu verbinden und zu lesen. 

Die [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections)-Methode wird verwendet, um die Kommunikation mit dem MySQL-Server zu ermöglichen. Die [connect()](https://github.com/mysqljs/mysql#establishing-connections)-Methode wird verwendet, um die Verbindung mit dem Server herzustellen. Die [query()](https://github.com/mysqljs/mysql#performing-queries)-Methode wird verwendet, um die SQL-Abfrage für die MySQL-Datenbank auszuführen. Das results-Array wird für die Ergebnisse der Abfrage verwendet.

Ersetzen Sie die Parameter `host`, `user`, `password` und `database` durch die Werte, die Sie beim Erstellen des Servers und der Datenbank angegeben haben.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            readData();
        }   
    });

function readData(){
        conn.query('SELECT * FROM inventory', 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Selected ' + results.length + ' row(s).');
                for (i = 0; i < results.length; i++) {
                    console.log('Row: ' + JSON.stringify(results[i]));
                }
                console.log('Done.');
            })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Closing connection.') 
        });
};
```

## <a name="update-data"></a>Aktualisieren von Daten

Verwenden Sie den folgenden Code, um die Daten mit einer SQL-Anweisung des Typs **UPDATE** zu verbinden und zu lesen. 

Die [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections)-Methode wird verwendet, um die Kommunikation mit dem MySQL-Server zu ermöglichen. Die [connect()](https://github.com/mysqljs/mysql#establishing-connections)-Methode wird verwendet, um die Verbindung mit dem Server herzustellen. Die [query()](https://github.com/mysqljs/mysql#performing-queries)-Methode wird verwendet, um die SQL-Abfrage für die MySQL-Datenbank auszuführen. 

Ersetzen Sie die Parameter `host`, `user`, `password` und `database` durch die Werte, die Sie beim Erstellen des Servers und der Datenbank angegeben haben.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            updateData();
        }   
    });

function updateData(){
       conn.query('UPDATE inventory SET quantity = ? WHERE name = ?', [200, 'banana'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Updated ' + results.affectedRows + ' row(s).');
        })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="delete-data"></a>Löschen von Daten

Verwenden Sie den folgenden Code, um die Daten mit einer SQL-Anweisung des Typs **DELETE** zu verbinden und zu lesen. 

Die [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections)-Methode wird verwendet, um die Kommunikation mit dem MySQL-Server zu ermöglichen. Die [connect()](https://github.com/mysqljs/mysql#establishing-connections)-Methode wird verwendet, um die Verbindung mit dem Server herzustellen. Die [query()](https://github.com/mysqljs/mysql#performing-queries)-Methode wird verwendet, um die SQL-Abfrage für die MySQL-Datenbank auszuführen. 

Ersetzen Sie die Parameter `host`, `user`, `password` und `database` durch die Werte, die Sie beim Erstellen des Servers und der Datenbank angegeben haben.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            deleteData();
        }   
    });

function deleteData(){
       conn.query('DELETE FROM inventory WHERE name = ?', ['orange'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Deleted ' + results.affectedRows + ' row(s).');
        })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
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
