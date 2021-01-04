---
title: 'Schnellstart: Herstellen einer Verbindung mit Python: Azure Database for PostgreSQL-Einzelserver'
description: Diese Schnellstartanleitung enthält Python-Codebeispiele, die Sie verwenden können, um eine Verbindung mit einem Azure Database for PostgreSQL-Einzelserver herzustellen und Daten daraus abzufragen.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: db94a82112f2670facd4d89178f11653c5316c36
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998939"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Schnellstart: Verwenden von Python zum Herstellen einer Verbindung mit einem Azure Database for PostgreSQL-Einzelserver sowie zum Abfragen von Daten

In dieser Schnellstartanleitung erfahren Sie, wie Sie eine Verbindung mit der Datenbank auf dem Azure Database for PostgreSQL-Einzelserver herstellen und SQL-Anweisungen zum Abfragen unter Verwendung von Python unter macOS, Ubuntu Linux oder Windows ausführen.

> [!TIP]
> Wenn Sie eine Django-Anwendung mit PostgreSQL erstellen möchten, schauen Sie sich das Tutorial [Bereitstellen einer Django-Web-App mit PostgreSQL](../app-service/tutorial-python-postgresql-app.md) an.


## <a name="prerequisites"></a>Voraussetzungen
Für diese Schnellstartanleitung benötigen Sie Folgendes:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free).
- Erstellen Sie einen Azure Database for PostgreSQL-Einzelserver unter Verwendung des [Azure-Portals](./quickstart-create-server-database-portal.md) <br/> oder der [Azure CLI](./quickstart-create-server-database-azure-cli.md), wenn Sie noch keinen besitzen.
- Je nachdem, ob Sie öffentlichen oder privaten Zugriff verwenden, führen Sie **EINE** der folgenden Aktionen aus, um Konnektivität herzustellen:

  |Action| Konnektivitätsmethode|Schrittanleitung|
  |:--------- |:--------- |:--------- |
  | **Konfigurieren von Firewallregeln** | Öffentlich | [Portal](./howto-manage-firewall-using-portal.md) <br/> [BEFEHLSZEILENSCHNITTSTELLE (CLI)](./howto-manage-firewall-using-cli.md)|
  | **Konfigurieren des Dienstendpunkts** | Öffentlich | [Portal](./howto-manage-vnet-using-portal.md) <br/> [BEFEHLSZEILENSCHNITTSTELLE (CLI)](./howto-manage-vnet-using-cli.md)|
  | **Konfigurieren von Private Link** | Privat | [Portal](./howto-configure-privatelink-portal.md) <br/> [BEFEHLSZEILENSCHNITTSTELLE (CLI)](./howto-configure-privatelink-cli.md) |

- [Python](https://www.python.org/downloads/) 2.7.9+ oder 3.4+.

- Aktuelles [pip](https://pip.pypa.io/en/stable/installing/)-Paketinstallationsprogramm.
- Installieren Sie [psycopg2](https://pypi.python.org/pypi/psycopg2/) mithilfe von `pip install psycopg2` in einem Terminal- oder Eingabeaufforderungsfenster. Weitere Informationen finden Sie unter [Installieren von `psycopg2`](http://initd.org/psycopg/docs/install.html).

## <a name="get-database-connection-information"></a>Abrufen von Datenbankverbindungsinformationen
Für die Verbindungsherstellung mit einer Azure Database for PostgreSQL-Datenbank sind der vollqualifizierte Servername und Anmeldeinformationen erforderlich. Diese Informationen finden Sie im Azure-Portal.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com/) nach dem Namen Ihres Azure Database for PostgreSQL-Servers, und wählen Sie ihn aus.
1. Kopieren Sie auf der **Übersichtsseite** des Servers unter **Servername** den vollqualifizierten Servernamen und unter **Administratorbenutzername** den Administratorbenutzernamen. Der vollqualifizierte **Servername** hat immer das Format *\<my-server-name>.postgres.database.azure.com*. Der **Administratorbenutzername** hat immer das Format *\<my-admin-username>@\<my-server-name>* .

   Darüber hinaus benötigen Sie Ihr Administratorkennwort. Sollten Sie es vergessen, können Sie es auf dieser Seite zurücksetzen.

   :::image type="content" source="./media/connect-python/1-connection-string.png" alt-text="Azure Database for PostgreSQL-Servername":::

> [!IMPORTANT]
>  Ersetzen Sie die folgenden Werte:
>   - `<server-name>` und `<admin-username>` durch die Werte, die Sie aus dem Azure-Portal kopiert haben.
>   - `<admin-password>` durch Ihr Serverkennwort.
>   - `<database-name>`: Bei der Servererstellung wurde automatisch eine Standarddatenbank namens *postgres* erstellt. Sie können diese Datenbank umbenennen oder mithilfe von SQL-Befehlen [eine neue Datenbank erstellen](https://www.postgresql.org/docs/9.0/sql-createdatabase.html).

## <a name="step-1-connect-and-insert-data"></a>Schritt 1: Herstellen einer Verbindung und Einfügen von Daten
Im unten gezeigten Codebeispiel wird mithilfe der folgenden Funktion eine Verbindung mit Ihrer Azure Database for PostgreSQL-Datenbank hergestellt:
-  [psycopg2.connect](http://initd.org/psycopg/docs/connection.html), und Daten werden mit der SQL-Anweisung **INSERT** geladen.
- Die Funktion [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) führt die SQL-Abfrage für die Datenbank aus.

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string)
print("Connection established")

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")

# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")

# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")

# Clean up
conn.commit()
cursor.close()
conn.close()
```

Nach erfolgreicher Ausführung generiert der Code die folgende Ausgabe:

:::image type="content" source="media/connect-python/2-example-python-output.png" alt-text="Befehlszeilenausgabe":::


[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/postgres-doc-feedback)

## <a name="step-2-read-data"></a>Schritt 2: Lesen von Daten
Im unten gezeigten Codebeispiel wird eine Verbindung mit Ihrer Azure Database for PostgreSQL-Datenbank hergestellt. Dazu wird Folgendes verwendet:
- [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) mit der SQL-Anweisung **SELECT** zum Lesen der Daten
- [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall) akzeptiert eine Abfrage und gibt ein Resultset zurück, das mithilfe von Folgendem durchlaufen werden kann:

```Python

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))


```
[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/postgres-doc-feedback)

## <a name="step-3-update-data"></a>Schritt 3: Aktualisieren von Daten
Im folgenden Codebeispiel wird [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) mit der SQL-Anweisung **UPDATE** zum Aktualisieren der Daten verwendet.

```Python

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

```
[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/postgres-doc-feedback)

## <a name="step-5-delete-data"></a>Schritt 5: Löschen von Daten
Im folgenden Codebeispiel wird [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) mit der SQL-Anweisung **DELETE** ausgeführt, um ein zuvor eingefügtes Bestandselement zu löschen.

```Python

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

```

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe mit dem folgenden Befehl, um alle in dieser Schnellstartanleitung verwendeten Ressourcen zu bereinigen:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Verwalten eines Azure Database for MySQL-Servers mithilfe des Portals](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Verwalten eines Azure Database for MySQL-Servers mithilfe der CLI](./how-to-manage-server-cli.md)<br/>

[Können Sie die gesuchten Informationen nicht finden? Informieren Sie uns darüber.](https://aka.ms/postgres-doc-feedback)
