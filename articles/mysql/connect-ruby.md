---
title: 'Schnellstart: Herstellen einer Verbindung unter Verwendung von Ruby: Azure Database for MySQL'
description: Dieser Schnellstart enthält mehrere Ruby-Codebeispiele, die Sie nutzen können, um zu den Daten von Azure-Datenbank für MySQL eine Verbindung herzustellen und Abfragen dafür durchzuführen.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: 20e51100eba595f9261be24e0e3675aa565479cd
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535553"
---
# <a name="quickstart-use-ruby-to-connect-and-query-data-in-azure-database-for-mysql"></a>Schnellstart: Verwenden von Ruby zum Herstellen einer Verbindung und zum Abfragen von Daten in Azure Database for MySQL

In diesem Schnellstart wird gezeigt, wie Sie eine Verbindung mit einer Azure-Datenbank für MySQL per [Ruby](https://www.ruby-lang.org)-Anwendung und [mysql2](https://rubygems.org/gems/mysql2)-Gem auf Windows-, Ubuntu Linux- und Mac-Plattformen herstellen. Es wird veranschaulicht, wie Sie SQL-Anweisungen zum Abfragen, Einfügen, Aktualisieren und Löschen von Daten in der Datenbank verwenden. Bei den Schritten in diesem Artikel wird davon ausgegangen, dass Sie mit der Ruby-Entwicklung vertraut sind und noch keine Erfahrung mit Azure Database for MySQL haben.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Schnellstart werden die Ressourcen, die in den folgenden Anleitungen erstellt wurden, als Startpunkt verwendet:
- [Erstellen eines Servers für Azure-Datenbank für MySQL mithilfe des Azure-Portals](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Erstellen eines Servers für Azure-Datenbank für MySQL mithilfe der Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT]
> Sicherstellen, dass der IP-Adresse, über die Sie eine Verbindung herstellen, die Firewallregeln des Servers über das [Azure-Portal](./howto-manage-firewall-using-portal.md) oder die [Azure CLI](./howto-manage-firewall-using-cli.md) hinzugefügt wurden

## <a name="install-ruby"></a>Installieren von Ruby
Installieren Sie Ruby, Gem und die MySQL2-Bibliothek auf Ihrem eigenen Computer.

### <a name="windows"></a>Windows
1. Laden Sie Version 2.3 von [Ruby](https://rubyinstaller.org/downloads/) herunter, und installieren Sie sie.
2. Starten Sie über das Menü „Start“ eine neue Eingabeaufforderung (cmd).
3. Wechseln Sie in das Ruby-Verzeichnis für Version 2.3. `cd c:\Ruby23-x64\bin`
4. Überprüfen Sie die Ruby-Installation, indem Sie den Befehl `ruby -v` ausführen, um die installierte Version anzuzeigen.
5. Überprüfen Sie die Gem-Installation, indem Sie den Befehl `gem -v` ausführen, um die installierte Version anzuzeigen.
6. Erstellen Sie das Mysql2-Modul für Ruby, indem Sie Gem mit dem Befehl `gem install mysql2` ausführen.

### <a name="macos"></a>macOS
1. Installieren Sie Ruby per Homebrew, indem Sie den Befehl `brew install ruby` ausführen. Weitere Installationsoptionen finden Sie in der [Installationsdokumentation](https://www.ruby-lang.org/en/documentation/installation/#homebrew) zu Ruby.
2. Überprüfen Sie die Ruby-Installation, indem Sie den Befehl `ruby -v` ausführen, um die installierte Version anzuzeigen.
3. Überprüfen Sie die Gem-Installation, indem Sie den Befehl `gem -v` ausführen, um die installierte Version anzuzeigen.
4. Erstellen Sie das Mysql2-Modul für Ruby, indem Sie Gem mit dem Befehl `gem install mysql2` ausführen.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Installieren Sie Ruby, indem Sie den Befehl `sudo apt-get install ruby-full` ausführen. Weitere Installationsoptionen finden Sie in der [Installationsdokumentation](https://www.ruby-lang.org/en/documentation/installation/) zu Ruby.
2. Überprüfen Sie die Ruby-Installation, indem Sie den Befehl `ruby -v` ausführen, um die installierte Version anzuzeigen.
3. Installieren Sie die neuesten Updates für Gem, indem Sie den Befehl `sudo gem update --system` ausführen.
4. Überprüfen Sie die Gem-Installation, indem Sie den Befehl `gem -v` ausführen, um die installierte Version anzuzeigen.
5. Installieren Sie gcc, make und andere Buildtools, indem Sie den Befehl `sudo apt-get install build-essential` ausführen.
6. Installieren Sie die Entwicklerbibliotheken für den MySQL-Client, indem Sie den Befehl `sudo apt-get install libmysqlclient-dev` ausführen.
7. Erstellen Sie das mysql2-Modul für Ruby, indem Sie Gem mit dem Befehl `sudo gem install mysql2` ausführen.

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen
Rufen Sie die Verbindungsinformationen ab, die zum Herstellen einer Verbindung mit der Azure SQL-Datenbank für MySQL erforderlich sind. Sie benötigen den vollqualifizierten Servernamen und die Anmeldeinformationen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.
2. Klicken Sie im Azure-Portal im linken Menü auf **Alle Ressourcen**, und suchen Sie dann nach dem soeben erstellten Server, z.B. **mydemoserver**.
3. Klicken Sie auf den Servernamen.
4. Notieren Sie sich im Bereich **Übersicht** des Servers den **Servernamen** und den **Anmeldenamen des Serveradministrators**. Wenn Sie Ihr Kennwort vergessen haben, können Sie es in diesem Bereich auch zurücksetzen.
 :::image type="content" source="./media/connect-ruby/1_server-overview-name-login.png" alt-text="Servername für Azure-Datenbank für MySQL":::

## <a name="run-ruby-code"></a>Ausführen von Ruby-Code
1. Fügen Sie den Ruby-Code aus den Abschnitten unten in Textdateien ein, und speichern Sie dann die Dateien in einem Projektordner mit der Dateierweiterung „.rb“, z.B. `C:\rubymysql\createtable.rb` oder `/home/username/rubymysql/createtable.rb`.
2. Starten Sie zum Ausführen des Codes die Eingabeaufforderung oder die Bash-Shell. Wechseln Sie mit `cd rubymysql` in das Verzeichnis Ihres Projektordners.
3. Geben Sie anschließend den Ruby-Befehl gefolgt vom Dateinamen ein, z.B. `ruby createtable.rb`, um die Anwendung auszuführen.
4. Unter Windows müssen Sie unter Umständen den vollständigen Pfad verwenden, um die Ruby-Anwendung zu starten, z.B. `"c:\Ruby23-x64\bin\ruby.exe" createtable.rb`, wenn sich die Ruby-Anwendung nicht unter dem Pfad der Umgebungsvariablen befindet.

## <a name="connect-and-create-a-table"></a>Herstellen einer Verbindung und Erstellen einer Tabelle
Verwenden Sie den folgenden Code, um eine Verbindung herzustellen und eine Tabelle zu erstellen, indem Sie eine SQL-Anweisung des Typs **CREATE TABLE** gefolgt von SQL-Anweisungen des Typs **INSERT INTO** zum Hinzufügen von Zeilen zur Tabelle nutzen.

Im Code wird die [mysql2::client](https://www.rubydoc.info/gems/mysql2)-Klasse verwendet, um eine Verbindung mit dem MySQL-Server herzustellen. Anschließend wird die ```query()```-Methode aufgerufen, um die Befehle DROP, CREATE TABLE und INSERT INTO auszuführen. Rufen Sie zum Schluss ```close()``` auf, um die Verbindung vor dem Beenden zu schließen.

Ersetzen Sie die Zeichenfolgen `host`, `database`, `username` und `password` durch Ihre eigenen Werte.
```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Drop previous table of same name if one exists
    client.query('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    client.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    client.query("INSERT INTO inventory VALUES(1, 'banana', 150)")
    client.query("INSERT INTO inventory VALUES(2, 'orange', 154)")
    client.query("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="read-data"></a>Lesen von Daten
Verwenden Sie den folgenden Code, um die Daten mit einer SQL-Anweisung des Typs **SELECT** zu verbinden und zu lesen.

Im Code wird eine [mysql2::client](https://www.rubydoc.info/gems/mysql2)-Klasse verwendet, um mit der ```new()```-Methode eine Verbindung mit Azure Database for MySQL herzustellen. Anschließend wird die ```query()```-Methode aufgerufen, um die SELECT-Befehle auszuführen. Anschließend wird die ```close()```-Methode aufgerufen, um die Verbindung vor dem Beenden zu schließen.

Ersetzen Sie die Zeichenfolgen `host`, `database`, `username` und `password` durch Ihre eigenen Werte.

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Read data
    resultSet = client.query('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end
    puts 'Read ' + resultSet.count.to_s + ' row(s).'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="update-data"></a>Aktualisieren von Daten
Verwenden Sie den folgenden Code, um die Daten mit einer SQL-Anweisung des Typs **UPDATE** zu verbinden und zu aktualisieren.

Im Code wird eine .new()-Methode der [mysql2::client](https://www.rubydoc.info/gems/mysql2)-Klasse verwendet, um eine Verbindung mit der Azure-Datenbank für MySQL herzustellen. Dann wird die Methode ```query()``` aufgerufen, um die UPDATE-Befehle auszuführen. Anschließend wird die ```close()```-Methode aufgerufen, um die Verbindung vor dem Beenden zu schließen.

Ersetzen Sie die Zeichenfolgen `host`, `database`, `username` und `password` durch Ihre eigenen Werte.

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Update data
   client.query('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
   puts 'Updated 1 row of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```


## <a name="delete-data"></a>Löschen von Daten
Verwenden Sie den folgenden Code, um die Daten mit einer SQL-Anweisung des Typs **DELETE** zu verbinden und zu lesen.

Im Code wird die [mysql2::client](https://rubygems.org/gems/mysql2/)-Klasse verwendet, um eine Verbindung mit dem MySQL-Server herzustellen, den DELETE-Befehl auszuführen und dann die Verbindung mit dem Server zu beenden.

Ersetzen Sie die Zeichenfolgen `host`, `database`, `username` und `password` durch Ihre eigenen Werte.

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Delete data
    resultSet = client.query('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
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
> [Migrieren der Datenbank mit Export und Import](./concepts-migrate-import-export.md) <br/>

> [!div class="nextstepaction"]
> [Weitere Informationen zum MySQL2-Client](https://www.rubydoc.info/gems/mysql2) <br/>

