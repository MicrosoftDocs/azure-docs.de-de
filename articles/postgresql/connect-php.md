---
title: 'Schnellstart: Herstellen einer Verbindung mit PHP: Azure Database for PostgreSQL-Einzelserver'
description: Dieser Schnellstart enthält ein PHP-Codebeispiel, das Sie nutzen können, um eine Verbindung mit einem Azure Database for PostgreSQL-Einzelserver herzustellen und Daten daraus abzufragen.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.devlang: php
ms.topic: quickstart
ms.date: 2/28/2018
ms.openlocfilehash: 4e82c8402584f694cb32bb37ae3e6eae9366eaf7
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341432"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Schnellstart: Verwenden von PHP zum Herstellen einer Verbindung mit einem Azure Database for PostgreSQL-Einzelserver sowie zum Abfragen von Daten

Dieser Schnellstart zeigt, wie Sie mit einer [PHP](https://secure.php.net/manual/intro-whatis.php)-Anwendung eine Verbindung mit einer Azure-Datenbank für PostgreSQL herstellen. Es wird veranschaulicht, wie Sie SQL-Anweisungen zum Abfragen, Einfügen, Aktualisieren und Löschen von Daten in der Datenbank verwenden. Bei den Schritten in diesem Abschnitt wird davon ausgegangen, dass Sie mit der PHP-Entwicklung vertraut sind und noch keine Erfahrung mit Azure Database for PostgreSQL haben.

## <a name="prerequisites"></a>Voraussetzungen
In diesem Schnellstart werden die Ressourcen, die in den folgenden Anleitungen erstellt wurden, als Startpunkt verwendet:
- [Erstellen einer Datenbank – Portal](quickstart-create-server-database-portal.md)
- [Erstellen einer Datenbank – Azure-CLI](quickstart-create-server-database-azure-cli.md)

## <a name="install-php"></a>Installieren von PHP
Installieren Sie PHP auf Ihrem eigenen Server, oder erstellen Sie eine Azure-[Web-App](../app-service/overview.md), die PHP enthält.

### <a name="windows"></a>Windows
- Herunterladen von [Version PHP 7.1.4 Non-Thread Safe (x64)](https://windows.php.net/download#php-7.1)
- Installieren von PHP (Informationen zur weiteren Konfiguration finden Sie im [PHP-Handbuch](https://secure.php.net/manual/install.windows.php))
- Im Code wird die **pgsql** -Klasse (ext/php_pgsql.dll) verwendet, die in der PHP-Installation enthalten ist. 
- Aktivieren Sie die **pgsql** -Erweiterung, indem Sie die Konfigurationsdatei „php.ini“ bearbeiten, die sich normalerweise unter `C:\Program Files\PHP\v7.1\php.ini` befindet. Die Konfigurationsdatei sollte eine Zeile mit dem Text `extension=php_pgsql.so` enthalten. Fügen Sie den Text hinzu, und speichern Sie die Datei, falls der Text nicht bereits angezeigt wird. Wenn der Text vorhanden ist, aber mit einem Semikolon als Präfix als Kommentar gekennzeichnet ist, können Sie die Kommentierung aufheben, indem Sie das Semikolon entfernen.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
- Herunterladen von [Version PHP 7.1.4 Non-Thread Safe (x64)](https://secure.php.net/downloads.php) 
- Installieren von PHP (Informationen zur weiteren Konfiguration finden Sie im [PHP-Handbuch](https://secure.php.net/manual/install.unix.php))
- Im Code wird die **pgsql** -Klasse (php_pgsql.so) verwendet. Installieren Sie sie, indem Sie `sudo apt-get install php-pgsql` ausführen.
- Aktivieren Sie die **pgsql** -Erweiterung, indem Sie die Konfigurationsdatei `/etc/php/7.0/mods-available/pgsql.ini` bearbeiten. Die Konfigurationsdatei sollte eine Zeile mit dem Text `extension=php_pgsql.so` enthalten. Fügen Sie den Text hinzu, und speichern Sie die Datei, falls der Text nicht bereits angezeigt wird. Wenn der Text vorhanden ist, aber mit einem Semikolon als Präfix als Kommentar gekennzeichnet ist, können Sie die Kommentierung aufheben, indem Sie das Semikolon entfernen.

### <a name="macos"></a>MacOS
- Herunterladen der [Version PHP 7.1.4](https://secure.php.net/downloads.php)
- Installieren von PHP (Informationen zur weiteren Konfiguration finden Sie im [PHP-Handbuch](https://secure.php.net/manual/install.macosx.php))

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen
Rufen Sie die Verbindungsinformationen ab, die zum Herstellen einer Verbindung mit der Azure-Datenbank für PostgreSQL erforderlich sind. Sie benötigen den vollqualifizierten Servernamen und die Anmeldeinformationen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.
2. Klicken Sie im Azure-Portal im linken Menü auf **Alle Ressourcen** , und suchen Sie dann nach dem soeben erstellten Server, z.B. **mydemoserver**.
3. Klicken Sie auf den Servernamen.
4. Notieren Sie sich im Bereich **Übersicht** des Servers den **Servernamen** und den **Anmeldenamen des Serveradministrators**. Wenn Sie Ihr Kennwort vergessen haben, können Sie es in diesem Bereich auch zurücksetzen.
 :::image type="content" source="./media/connect-php/1-connection-string.png" alt-text="Azure Database for PostgreSQL-Servername":::

## <a name="connect-and-create-a-table"></a>Herstellen einer Verbindung und Erstellen einer Tabelle
Verwenden Sie den folgenden Code, um eine Verbindung herzustellen und eine Tabelle zu erstellen, indem Sie eine **CREATE TABLE** -SQL-Anweisung gefolgt von **INSERT INTO** -SQL-Anweisungen zum Hinzufügen von Zeilen zur Tabelle nutzen.

Im Code wird die [pg_connect()](https://secure.php.net/manual/en/function.pg-connect.php)-Methode aufgerufen, um eine Verbindung mit Azure-Datenbank für PostgreSQL herzustellen. Anschließend wird mehrfach die [pg_query()](https://secure.php.net/manual/en/function.pg-query.php)-Methode aufgerufen, um mehrere Befehle auszuführen, und mit [pg_last_error()](https://secure.php.net/manual/en/function.pg-last-error.php) werden jeweils die Details überprüft, falls ein Fehler auftritt. Als Nächstes wird die [pg_close()](https://secure.php.net/manual/en/function.pg-close.php)-Methode aufgerufen, um die Verbindung zu schließen.

Ersetzen Sie die Parameter `$host`, `$database`, `$user` und `$password` durch Ihre eigenen Werte. 

```php
<?php
    // Initialize connection variables.
    $host = "mydemoserver.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mydemoserver";
    $password = "<server_admin_password>";

    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password") 
        or die("Failed to create connection to database: ". pg_last_error(). "<br/>");
    print "Successfully created connection to database.<br/>";

    // Drop previous table of same name if one exists.
    $query = "DROP TABLE IF EXISTS inventory;";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");
    print "Finished dropping table (if existed).<br/>";

    // Create table.
    $query = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");
    print "Finished creating table.<br/>";

    // Insert some data into table.
    $name = '\'banana\'';
    $quantity = 150;
    $query = "INSERT INTO inventory (name, quantity) VALUES ($1, $2);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");

    $name = '\'orange\'';
    $quantity = 154;
    $query = "INSERT INTO inventory (name, quantity) VALUES ($name, $quantity);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");

    $name = '\'apple\'';
    $quantity = 100;
    $query = "INSERT INTO inventory (name, quantity) VALUES ($name, $quantity);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error()). "<br/>";

    print "Inserted 3 rows of data.<br/>";

    // Closing connection
    pg_close($connection);
?>
```

## <a name="read-data"></a>Lesen von Daten
Verwenden Sie den folgenden Code, um die Daten mit einer **SELECT** -SQL-Anweisung zu verbinden und zu lesen. 

 Im Code wird die [pg_connect()](https://secure.php.net/manual/en/function.pg-connect.php)-Methode aufgerufen, um eine Verbindung mit Azure-Datenbank für PostgreSQL herzustellen. Anschließend wird die [pg_query()](https://secure.php.net/manual/en/function.pg-query.php)-Methode aufgerufen, um den SELECT-Befehl auszuführen, und die Ergebnisse werden in einem Resultset zusammengefasst. Mit [pg_last_error()](https://secure.php.net/manual/en/function.pg-last-error.php) werden die Details überprüft, falls ein Fehler auftritt.  Zum Lesen des Resultsets wird die [pg_fetch_row()](https://secure.php.net/manual/en/function.pg-fetch-row.php)-Methode in einer Schleife aufgerufen (einmal pro Zeile), und die Zeilendaten werden im Array `$row` abgerufen, das für jede Arrayposition einen Datenwert pro Spalte enthält.  Zum Freigeben des Resultsets wird die [pg_free_result()](https://secure.php.net/manual/en/function.pg-free-result.php)-Methode aufgerufen. Als Nächstes wird die [pg_close()](https://secure.php.net/manual/en/function.pg-close.php)-Methode aufgerufen, um die Verbindung zu schließen.

Ersetzen Sie die Parameter `$host`, `$database`, `$user` und `$password` durch Ihre eigenen Werte. 

```php
<?php
    // Initialize connection variables.
    $host = "mydemoserver.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mydemoserver";
    $password = "<server_admin_password>";
    
    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password")
                or die("Failed to create connection to database: ". pg_last_error(). "<br/>");

    print "Successfully created connection to database. <br/>";

    // Perform some SQL queries over the connection.
    $query = "SELECT * from inventory";
    $result_set = pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");
    while ($row = pg_fetch_row($result_set))
    {
        print "Data row = ($row[0], $row[1], $row[2]). <br/>";
    }

    // Free result_set
    pg_free_result($result_set);

    // Closing connection
    pg_close($connection);
?>
```

## <a name="update-data"></a>Aktualisieren von Daten
Verwenden Sie den folgenden Code, um eine Verbindung herzustellen und die Daten per **UPDATE** -SQL-Anweisung zu aktualisieren.

Im Code wird die [pg_connect()](https://secure.php.net/manual/en/function.pg-connect.php)-Methode aufgerufen, um eine Verbindung mit Azure-Datenbank für PostgreSQL herzustellen. Anschließend wird die [pg_query()](https://secure.php.net/manual/en/function.pg-query.php)-Methode aufgerufen, um einen Befehl auszuführen, und [pg_last_error()](https://secure.php.net/manual/en/function.pg-last-error.php), um beim Auftreten eines Fehlers die Details zu überprüfen. Als Nächstes wird die [pg_close()](https://secure.php.net/manual/en/function.pg-close.php)-Methode aufgerufen, um die Verbindung zu schließen.

Ersetzen Sie die Parameter `$host`, `$database`, `$user` und `$password` durch Ihre eigenen Werte. 

```php
<?php
    // Initialize connection variables.
    $host = "mydemoserver.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mydemoserver";
    $password = "<server_admin_password>";

    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password")
                or die("Failed to create connection to database: ". pg_last_error(). ".<br/>");

    print "Successfully created connection to database. <br/>";

    // Modify some data in table.
    $new_quantity = 200;
    $name = '\'banana\'';
    $query = "UPDATE inventory SET quantity = $new_quantity WHERE name = $name;";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). ".<br/>");
    print "Updated 1 row of data. </br>";

    // Closing connection
    pg_close($connection);
?>
```


## <a name="delete-data"></a>Löschen von Daten
Verwenden Sie den folgenden Code, um die Daten mit einer **DELETE** -SQL-Anweisung zu verbinden und zu lesen. 

 Im Code wird die [pg_connect()](https://secure.php.net/manual/en/function.pg-connect.php)-Methode aufgerufen, um eine Verbindung mit Azure-Datenbank für PostgreSQL herzustellen. Anschließend wird die [pg_query()](https://secure.php.net/manual/en/function.pg-query.php)-Methode aufgerufen, um einen Befehl auszuführen, und [pg_last_error()](https://secure.php.net/manual/en/function.pg-last-error.php), um beim Auftreten eines Fehlers die Details zu überprüfen. Als Nächstes wird die [pg_close()](https://secure.php.net/manual/en/function.pg-close.php)-Methode aufgerufen, um die Verbindung zu schließen.

Ersetzen Sie die Parameter `$host`, `$database`, `$user` und `$password` durch Ihre eigenen Werte. 

```php
<?php
    // Initialize connection variables.
    $host = "mydemoserver.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mydemoserver";
    $password = "<server_admin_password>";

    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password")
            or die("Failed to create connection to database: ". pg_last_error(). ". </br>");

    print "Successfully created connection to database. <br/>";

    // Delete some data from table.
    $name = '\'orange\'';
    $query = "DELETE FROM inventory WHERE name = $name;";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). ". <br/>");
    print "Deleted 1 row of data. <br/>";

    // Closing connection
    pg_close($connection);
?>
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
