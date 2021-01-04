---
title: 'Schnellstart: Herstellen einer Verbindung unter Verwendung von Python: Azure Database for MySQL'
description: Diese Schnellstartanleitung enthält mehrere Python-Codebeispiele, mit deren Hilfe Sie eine Verbindung mit Azure-Datenbank für MySQL herstellen und Daten daraus abfragen können.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: 8aa0ea4b1e01cc7363f49d5897695c7c237b339b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535587"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql"></a>Schnellstart: Verwenden von Python zum Herstellen einer Verbindung und zum Abfragen von Daten in Azure Database for MySQL

In dieser Schnellstartanleitung stellen Sie unter Verwendung von Python eine Verbindung mit einer Azure Database for MySQL-Instanz her. Anschließend verwenden Sie SQL-Anweisungen, um Daten in der Datenbank über Mac-, Ubuntu Linux- und Windows-Plattformen abzufragen, einzufügen, zu aktualisieren und zu löschen. 

## <a name="prerequisites"></a>Voraussetzungen
Für diese Schnellstartanleitung benötigen Sie Folgendes:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free).
- Erstellen eines Azure Database for MySQL-Einzelservers mithilfe des [Azure-Portals](./quickstart-create-mysql-server-database-using-azure-portal.md) <br/> oder der [Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md), wenn Sie noch keinen besitzen.
- Je nachdem, ob Sie öffentlichen oder privaten Zugriff verwenden, führen Sie **EINE** der folgenden Aktionen aus, um Konnektivität herzustellen:

   |Action| Konnektivitätsmethode|Schrittanleitung|
   |:--------- |:--------- |:--------- |
   | **Konfigurieren von Firewallregeln** | Öffentlich | [Portal](./howto-manage-firewall-using-portal.md) <br/> [BEFEHLSZEILENSCHNITTSTELLE (CLI)](./howto-manage-firewall-using-cli.md)|
   | **Konfigurieren des Dienstendpunkts** | Öffentlich | [Portal](./howto-manage-vnet-using-portal.md) <br/> [BEFEHLSZEILENSCHNITTSTELLE (CLI)](./howto-manage-vnet-using-cli.md)| 
   | **Konfigurieren von Private Link** | Privat | [Portal](./howto-configure-privatelink-portal.md) <br/> [BEFEHLSZEILENSCHNITTSTELLE (CLI)](./howto-configure-privatelink-cli.md) | 

- [Erstellen einer Datenbank und eines Nicht-Administratorbenutzers](./howto-create-users.md)

## <a name="install-python-and-the-mysql-connector"></a>Installieren von Python und dem MySQL-Connector

Führen Sie die folgenden Schritte aus, um Python und den MySQL-Connector für Python auf Ihrem Computer zu installieren: 

> [!NOTE]
> In diesem Schnellstart wird das [MySQL-Connector-/Python-Entwicklerhandbuch](https://dev.mysql.com/doc/connector-python/en/) verwendet.

1. Laden Sie [Python 3.7 oder höher](https://www.python.org/downloads/) für Ihr Betriebssystem herunter, und führen Sie die Installation durch. Stellen Sie sicher, dass Sie Python unter `PATH` hinzufügen, da dies für den MySQL-Connector erforderlich ist.
   
2. Öffnen Sie eine Eingabeaufforderung oder eine `bash`-Shell, und überprüfen Sie Ihre Python-Version, indem Sie `python -V` ausführen (Switch „V“ als Großbuchstabe).
   
3. Das Installationsprogramm für das `pip`-Paket ist in den aktuellen Versionen von Python enthalten. Führen Sie für `pip` das Update auf die aktuelle Version durch, indem Sie `pip install -U pip` ausführen. 
   
   Wenn `pip` nicht installiert ist, können Sie `get-pip.py` zum Herunterladen und Installieren verwenden. Weitere Informationen finden Sie unter [Installation](https://pip.pypa.io/en/stable/installing/). 
   
4. Verwenden Sie `pip`, um den MySQL-Connector für Python und die Abhängigkeiten zu installieren:
   
   ```bash
   pip install mysql-connector-python
   ```
   
[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/mysql-doc-feedback)

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen

Sie können die Verbindungsinformationen, die Sie zum Herstellen einer Verbindung mit Azure Database for MySQL benötigen, über das Azure-Portal ermitteln. Sie benötigen den Servernamen, den Datenbanknamen und die Anmeldeinformationen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
   
1. Suchen Sie im Portal in der Suchleiste nach dem von Ihnen erstellten Azure Database for MySQL-Server, z. B. **mydemoserver**, und wählen Sie ihn aus.
   
   :::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Servername für Azure-Datenbank für MySQL":::
   
1. Notieren Sie sich auf der Seite **Übersicht** des Servers den **Servernamen** und die **Anmelde-ID des Serveradministrators**. Falls Sie Ihr Kennwort vergessen haben, können Sie es auf dieser Seite auch zurücksetzen.
   
   :::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Servername 2 für Azure-Datenbank für MySQL":::

## <a name="step-1-create-a-table-and-insert-data"></a>Schritt 1: Erstellen einer Tabelle und Einfügen von Daten

Verwenden Sie den folgenden Code, um eine Verbindung mit dem Server und der Datenbank herzustellen, eine Tabelle zu erstellen und die Daten mit einer SQL-Anweisung vom Typ **INSERT** zu laden. Durch den Code wird die Bibliothek „mysql.connector“ importiert und die folgende Methode verwendet:
- Die Funktion [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) wird verwendet, um unter Verwendung der [Argumente](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) in der CONFIG-Sammlung eine Verbindung mit Azure Database for MySQL herzustellen. 
- Durch die Methode [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) wird die SQL-Abfrage für die MySQL-Datenbank ausgeführt. 
- [cursor.close()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-close.html) wird nach der Verwendung eines Cursors ausgeführt.
- [conn.close()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlconnection-close.html) wird zum Schließen der Verbindung ausgeführt.

> [!IMPORTANT]
> - SSL ist standardmäßig aktiviert. Möglicherweise müssen Sie das [SSL-Zertifikat „DigiCertGlobalRootG2“](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) herunterladen, um eine Verbindung von der lokalen Umgebung herzustellen.
> - Ersetzen Sie die Platzhalter `<mydemoserver>`, `<myadmin>`, `<mypassword>` und `<mydatabase>` durch die Werte für Ihren MySQL-Server und die Datenbank.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>',
  'client_flags': [ClientFlag.SSL],
  'ssl_cert': '/var/wwww/html/DigiCertGlobalRootG2.crt.pem'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-read-data"></a>Schritt 2: Lesen von Daten

Verwenden Sie den folgenden Code, um die Daten mit einer SQL-Anweisung des Typs **SELECT** zu verbinden und zu lesen. Durch den Code werden die Bibliothek „mysql.connector“ importiert und die SQL-Abfrage mithilfe der Methode [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) für die MySQL-Datenbank ausgeführt. 

Der Code liest die Datenzeilen mit der [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html)-Methode, legt die Ergebnisse in einer Zeile der Auflistung ab und verwendet einen `for`-Iterator, um einen Schleifenvorgang für die Zeilen durchzuführen.

```python
  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

```

## <a name="step-3-update-data"></a>Schritt 3: Aktualisieren von Daten

Verwenden Sie den folgenden Code, um die Daten mit einer SQL-Anweisung des Typs **UPDATE** zu verbinden und zu aktualisieren. Durch den Code werden die Bibliothek „mysql.connector“ importiert und die SQL-Abfrage mithilfe der Methode [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) für die MySQL-Datenbank ausgeführt. 

```python
  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")
```

## <a name="step-4-delete-data"></a>Schritt 4: Löschen von Daten

Verwenden Sie den folgenden Code, um eine Verbindung herzustellen und die Daten mithilfe einer SQL-Anweisung des Typs **DELETE** zu entfernen. Durch den Code werden die Bibliothek „mysql.connector“ importiert und die SQL-Abfrage mithilfe der Methode [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) für die MySQL-Datenbank ausgeführt. 

```python

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")
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
> [Verwalten eines Azure Database for MySQL-Servers mithilfe des Portals](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Verwalten eines Azure Database for MySQL-Servers mithilfe der CLI](./how-to-manage-single-server-cli.md)

[Können Sie die gesuchten Informationen nicht finden? Informieren Sie uns darüber.](https://aka.ms/mysql-doc-feedback)
