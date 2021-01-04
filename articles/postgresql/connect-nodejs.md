---
title: 'Schnellstart: Verwenden von Node.js zum Herstellen einer Verbindung mit einem Azure Database for PostgreSQL-Einzelserver'
description: Dieser Schnellstart enthält ein Node.js-Codebeispiel, das Sie nutzen können, um eine Verbindung mit einem Azure Database for PostgreSQL-Einzelserver herzustellen und Daten daraus abzufragen.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom:
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
- devx-track-js
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 5/6/2019
ms.openlocfilehash: 7569606429740de23b56767d490b9bb14283d468
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93331691"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Schnellstart: Verwenden von Node.js zum Herstellen einer Verbindung mit einem Azure Database for PostgreSQL-Einzelserver und zum Abfragen von Daten

In dieser Schnellstartanleitung stellen Sie über eine Node.js-Anwendung eine Verbindung mit einer Azure Database for PostgreSQL-Instanz her. Es wird veranschaulicht, wie Sie SQL-Anweisungen zum Abfragen, Einfügen, Aktualisieren und Löschen von Daten in der Datenbank verwenden. Bei den Schritten in diesem Abschnitt wird davon ausgegangen, dass Sie mit der Node.js-Entwicklung vertraut sind und noch keine Erfahrung mit Azure Database for PostgreSQL haben.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Abschluss von [Schnellstart: Erstellen eines Azure Database for PostgreSQL-Servers im Azure-Portal](quickstart-create-server-database-portal.md) oder [Schnellstart: Erstellen eines Azure Database for PostgreSQL-Einzelservers mit der Azure CLI](quickstart-create-server-database-azure-cli.md) erstellt wurde.

- [Node.js](https://nodejs.org)

## <a name="install-pg-client"></a>Installieren des pg-Clients
Installieren Sie [pg](https://www.npmjs.com/package/pg), den PostgreSQL-Client für Node.js.

Führen Sie hierzu den Knoten-Paket-Manager (npm) für JavaScript an der Befehlszeile aus, um den pg-Client zu installieren.
```bash
npm install pg
```

Überprüfen Sie die Installation, indem Sie die installierten Pakete auflisten.
```bash
npm list
```

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen
Rufen Sie die Verbindungsinformationen ab, die zum Herstellen einer Verbindung mit der Azure-Datenbank für PostgreSQL erforderlich sind. Sie benötigen den vollqualifizierten Servernamen und die Anmeldeinformationen.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com/) nach dem von Ihnen erstellten Server (beispielsweise **mydemoserver** ), und wählen Sie ihn aus.

1. Notieren Sie sich im Bereich **Übersicht** des Servers die Werte für **Servername** und **Administratorbenutzername**. Wenn Sie Ihr Kennwort vergessen haben, können Sie es in diesem Bereich auch zurücksetzen.

   :::image type="content" source="./media/connect-nodejs/server-details-azure-database-postgresql.png" alt-text="Azure Database for PostgreSQL-Verbindungszeichenfolge":::

## <a name="running-the-javascript-code-in-nodejs"></a>Ausführen des JavaScript-Codes in Node.js
Sie können Node.js über die Bash-Shell, das Terminal oder eine Windows-Eingabeaufforderung starten, indem Sie `node` eingeben und anschließend den JavaScript-Beispielcode interaktiv ausführen. Kopieren Sie ihn zu diesem Zweck in die Eingabeaufforderung. Alternativ können Sie den JavaScript-Code in einer Textdatei speichern und `node filename.js` mit dem Dateinamen als Parameter für die Ausführung starten.

## <a name="connect-create-table-and-insert-data"></a>Herstellen der Verbindung, Erstellen der Tabelle und Einfügen von Daten
Verwenden Sie den folgenden Code, um eine Verbindung herzustellen und die Daten zu laden, indem Sie die SQL-Anweisungen **CREATE TABLE** und **INSERT INTO** verwenden.
Das [pg.Client](https://github.com/brianc/node-postgres/wiki/Client)-Objekt wird verwendet, um die Kommunikation mit dem PostgreSQL-Server zu ermöglichen. Die Funktion [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) wird verwendet, um die Verbindung mit dem Server herzustellen. Die Funktion [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) wird verwendet, um die SQL-Abfrage für die PostgreSQL-Datenbank auszuführen. 

Ersetzen Sie die Parameter „host“, „dbname“, „user“ und „password“ durch die Werte, die Sie beim Erstellen des Servers und der Datenbank angegeben haben.

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DROP TABLE IF EXISTS inventory;
        CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
        INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
        INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
        INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    `;

    client
        .query(query)
        .then(() => {
            console.log('Table created successfully!');
            client.end(console.log('Closed client connection'));
        })
        .catch(err => console.log(err))
        .then(() => {
            console.log('Finished execution, exiting now');
            process.exit();
        });
}
```

## <a name="read-data"></a>Lesen von Daten
Verwenden Sie den folgenden Code, um die Daten mit einer **SELECT** -SQL-Anweisung zu verbinden und zu lesen. Das [pg.Client](https://github.com/brianc/node-postgres/wiki/Client)-Objekt wird verwendet, um die Kommunikation mit dem PostgreSQL-Server zu ermöglichen. Die Funktion [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) wird verwendet, um die Verbindung mit dem Server herzustellen. Die Funktion [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) wird verwendet, um die SQL-Abfrage für die PostgreSQL-Datenbank auszuführen. 

Ersetzen Sie die Parameter „host“, „dbname“, „user“ und „password“ durch die Werte, die Sie beim Erstellen des Servers und der Datenbank angegeben haben. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else { queryDatabase(); }
});

function queryDatabase() {
  
    console.log(`Running query to PostgreSQL server: ${config.host}`);

    const query = 'SELECT * FROM inventory;';

    client.query(query)
        .then(res => {
            const rows = res.rows;

            rows.map(row => {
                console.log(`Read: ${JSON.stringify(row)}`);
            });

            process.exit();
        })
        .catch(err => {
            console.log(err);
        });
}
```

## <a name="update-data"></a>Aktualisieren von Daten
Verwenden Sie den folgenden Code, um die Daten mit einer **UPDATE** -SQL-Anweisung zu verbinden und zu lesen. Das [pg.Client](https://github.com/brianc/node-postgres/wiki/Client)-Objekt wird verwendet, um die Kommunikation mit dem PostgreSQL-Server zu ermöglichen. Die Funktion [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) wird verwendet, um die Verbindung mit dem Server herzustellen. Die Funktion [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) wird verwendet, um die SQL-Abfrage für die PostgreSQL-Datenbank auszuführen. 

Ersetzen Sie die Parameter „host“, „dbname“, „user“ und „password“ durch die Werte, die Sie beim Erstellen des Servers und der Datenbank angegeben haben. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        UPDATE inventory 
        SET quantity= 1000 WHERE name='banana';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Update completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="delete-data"></a>Löschen von Daten
Verwenden Sie den folgenden Code, um die Daten mit einer **DELETE** -SQL-Anweisung zu verbinden und zu lesen. Das [pg.Client](https://github.com/brianc/node-postgres/wiki/Client)-Objekt wird verwendet, um die Kommunikation mit dem PostgreSQL-Server zu ermöglichen. Die Funktion [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) wird verwendet, um die Verbindung mit dem Server herzustellen. Die Funktion [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) wird verwendet, um die SQL-Abfrage für die PostgreSQL-Datenbank auszuführen. 

Ersetzen Sie die Parameter „host“, „dbname“, „user“ und „password“ durch die Werte, die Sie beim Erstellen des Servers und der Datenbank angegeben haben. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) {
        throw err;
    } else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DELETE FROM inventory 
        WHERE name = 'apple';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Delete completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
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
