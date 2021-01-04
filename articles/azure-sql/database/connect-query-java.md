---
title: Verwenden von Java und JDBC mit Azure SQL-Datenbank
description: Hier erfahren Sie, wie Sie Java und JDBC mit einer Azure SQL-Datenbank-Instanz verwenden.
services: sql-database
author: jdubois
ms.author: judubois
ms.service: sql-database
ms.subservice: development
ms.topic: quickstart
ms.devlang: java
ms.date: 06/26/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: badf6b8887c356c2a7fc7308f6aa15f551e4bb67
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95972673"
---
# <a name="use-java-and-jdbc-with--azure-sql-database"></a>Verwenden von Java und JDBC mit Azure SQL-Datenbank

In diesem Thema wird die Erstellung einer Beispielanwendung veranschaulicht, die Java und [JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) verwendet, um Informationen in [Azure SQL-Datenbank](/azure/sql-database/) zu speichern bzw. daraus abzurufen.

JDBC ist die Standard-Java-API, um eine Verbindung mit herkömmlichen relationalen Datenbanken herzustellen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto. Falls Sie noch kein Konto haben, können Sie eine [kostenlose Testversion](https://azure.microsoft.com/free/) verwenden.
- [Azure Cloud Shell](../../cloud-shell/quickstart.md) oder [Azure CLI](/cli/azure/install-azure-cli). Wir empfehlen Azure Cloud Shell, damit Sie automatisch angemeldet werden und Zugriff auf alle erforderlichen Tools erhalten.
- Ein unterstütztes [Java Development Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), Version 8 (in Azure Cloud Shell enthalten).
- Das [Apache Maven](https://maven.apache.org/)-Buildtool

## <a name="prepare-the-working-environment"></a>Vorbereiten der Arbeitsumgebung

Sie verwenden Umgebungsvariablen, um Tippfehler zu minimieren und die Anpassung der folgenden Konfiguration für Ihre spezifischen Anforderungen zu erleichtern.

Richten Sie diese Umgebungsvariablen mithilfe der folgenden Befehle ein:

```bash
AZ_RESOURCE_GROUP=database-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_SQL_SERVER_USERNAME=demo
AZ_SQL_SERVER_PASSWORD=<YOUR_AZURE_SQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Ersetzen Sie die Platzhalter durch die folgenden Werte, die in diesem Artikel verwendet werden:

- `<YOUR_DATABASE_NAME>`: Der Name für den Azure SQL-Datenbank-Server. Er muss innerhalb von Azure eindeutig sein.
- `<YOUR_AZURE_REGION>`: Die von Ihnen verwendete Azure-Region. Sie können standardmäßig `eastus` verwenden, wir empfehlen aber, eine Region zu konfigurieren, die näher an Ihrem Standort liegt. Die vollständige Liste der verfügbaren Regionen wird angezeigt, wenn Sie `az account list-locations` eingeben.
- `<AZ_SQL_SERVER_PASSWORD>`: Das Kennwort Ihres Azure SQL-Datenbank-Servers. Dieses Kennwort sollte mindestens acht Zeichen lang sein. Es muss Zeichen aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen (0-9) und nicht alphanumerische Zeichen (!, $, #, % usw.).
- `<YOUR_LOCAL_IP_ADDRESS>`: Die IP-Adresse Ihres lokalen Computers, auf dem Sie die Java-Anwendung ausführen. Sie können sie ganz einfach ermitteln, indem Sie im Browser zu [whatismyip.akamai.com](http://whatismyip.akamai.com/) navigieren.

Erstellen Sie dann mithilfe des folgenden Befehls eine Ressourcengruppe:

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
  	| jq
```

> [!NOTE]
> Wir verwenden das Hilfsprogramm `jq`, um JSON-Daten anzuzeigen und besser lesbar zu machen. Dieses Hilfsprogramm wird standardmäßig unter [Azure Cloud Shell](https://shell.azure.com/) installiert. Wenn Ihnen dieses Hilfsprogramm nicht gefällt, können Sie den Teil `| jq` aller verwendeten Befehle einfach entfernen.

## <a name="create-an-azure-sql-database-instance"></a>Erstellen einer Azure SQL-Datenbankinstanz

Sie erstellen zuerst einen verwalteten Azure SQL-Datenbank-Server.

> [!NOTE]
> Ausführlichere Informationen zum Erstellen von Azure SQL-Datenbank-Servern finden Sie unter [Schnellstart: Erstellen einer Azure SQL-Einzeldatenbank](./single-database-create-quickstart.md).

Führen Sie in [Azure Cloud Shell](https://shell.azure.com/) den folgenden Befehl aus:

```azurecli
az sql server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --admin-user $AZ_SQL_SERVER_USERNAME \
    --admin-password $AZ_SQL_SERVER_PASSWORD \
  	| jq
```

Mit diesem Befehl wird ein Azure SQL-Datenbank-Server erstellt.

### <a name="configure-a-firewall-rule-for-your-azure-sql-database-server"></a>Konfigurieren einer Firewallregel für den Azure SQL-Datenbank-Server

Azure SQL-Datenbankinstanzen sind standardmäßig gesichert. Sie besitzen eine Firewall, die keine eingehenden Verbindungen zulässt. Damit Sie die Datenbank verwenden können, müssen Sie eine Firewallregel hinzufügen, die den Zugriff der lokalen IP-Adresse auf den Datenbankserver zulässt.

Da Sie die lokale IP-Adresse zu Beginn dieses Artikels konfiguriert haben, können Sie die Firewall des Servers öffnen, indem Sie den folgenden Befehl ausführen:

```azurecli
az sql server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
  	| jq
```

### <a name="configure-a-azure-sql-database"></a>Konfigurieren einer Azure SQL-Datenbank

Der von Ihnen zuvor erstellte Azure SQL-Datenbank-Server ist leer. Er verfügt über keine Datenbank, die Sie mit der Java-Anwendung verwenden können. Erstellen Sie durch Ausführen des folgenden Befehls eine neue Datenbank namens `demo`:

```azurecli
az sql db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server $AZ_DATABASE_NAME \
  	| jq
```

### <a name="create-a-new-java-project"></a>Erstellen eines neuen Java-Projekts

Erstellen Sie mit Ihrer bevorzugten IDE ein neues Java-Projekt, und fügen Sie im Stammverzeichnis eine `pom.xml`-Datei hinzu:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>

    <properties>
        <java.version>1.8</java.version>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.sqlserver</groupId>
            <artifactId>mssql-jdbc</artifactId>
            <version>7.4.1.jre8</version>
        </dependency>
    </dependencies>
</project>
```

Dabei handelt es sich um eine [Apache Maven](https://maven.apache.org/)-Datei, die das Projekt für die Verwendung folgender Komponenten konfiguriert:

- Java 8
- aktueller SQL Server-Treiber für Java

### <a name="prepare-a-configuration-file-to-connect-to-azure-sql-database"></a>Vorbereiten einer Konfigurationsdatei für das Herstellen einer Verbindung mit Azure SQL-Datenbank

Erstellen Sie eine Datei vom Typ *src/main/resources/application.properties*, und fügen Sie Folgendes hinzu:

```properties
url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
user=demo@$AZ_DATABASE_NAME
password=$AZ_SQL_SERVER_PASSWORD
```

- Ersetzen Sie die beiden Variablen `$AZ_DATABASE_NAME` durch den Wert, den Sie zu Beginn dieses Artikels konfiguriert haben.
- Ersetzen Sie die Variable `$AZ_SQL_SERVER_PASSWORD` durch den Wert, den Sie zu Beginn dieses Artikels konfiguriert haben.

### <a name="create-an-sql-file-to-generate-the-database-schema"></a>Erstellen einer SQL-Datei zum Generieren des Datenbankschemas

Sie verwenden eine Datei vom Typ *src/main/resources/`schema.sql`* , um ein Datenbankschema zu erstellen. Erstellen Sie diese Datei mit folgendem Inhalt:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id INT PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BIT);
```

## <a name="code-the-application"></a>Codieren der Anwendung

### <a name="connect-to-the-database"></a>Herstellen der Verbindung mit der Datenbank

Fügen Sie als Nächstes den Java-Code hinzu, der JDBC zum Speichern und Abrufen von Daten in bzw. aus Ihrer Azure SQL-Datenbank nutzt.

Erstellen Sie eine Datei vom Typ *src/main/java/DemoApplication.java*, die Folgendes enthält:

```java
package com.example.demo;

import java.sql.*;
import java.util.*;
import java.util.logging.Logger;

public class DemoApplication {

    private static final Logger log;

    static {
        System.setProperty("java.util.logging.SimpleFormatter.format", "[%4$-7s] %5$s %n");
        log =Logger.getLogger(DemoApplication.class.getName());
    }

    public static void main(String[] args) throws Exception {
        log.info("Loading application properties");
        Properties properties = new Properties();
        properties.load(DemoApplication.class.getClassLoader().getResourceAsStream("application.properties"));

        log.info("Connecting to the database");
        Connection connection = DriverManager.getConnection(properties.getProperty("url"), properties);
        log.info("Database connection test: " + connection.getCatalog());

        log.info("Create database schema");
        Scanner scanner = new Scanner(DemoApplication.class.getClassLoader().getResourceAsStream("schema.sql"));
        Statement statement = connection.createStatement();
        while (scanner.hasNextLine()) {
            statement.execute(scanner.nextLine());
        }

        /*
        Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
        insertData(todo, connection);
        todo = readData(connection);
        todo.setDetails("congratulations, you have updated data!");
        updateData(todo, connection);
        deleteData(todo, connection);
        */

        log.info("Closing database connection");
        connection.close();
    }
}
```

Dieser Java-Code verwendet die weiter oben erstellten Dateien *application.properties* und *schema.sql*, um eine Verbindung mit der SQL Server-Datenbank herzustellen und ein Schema zu erstellen, in dem Ihre Daten gespeichert werden.

Sie sehen, dass in dieser Datei Methoden zum Einfügen, Lesen, Aktualisieren und Löschen von Daten kommentiert wurden: Diese Methoden werden im verbleibenden Artikel codiert, und Sie können nacheinander die Auskommentierung aufheben.

> [!NOTE]
> Die Datenbankanmeldeinformationen werden in den Eigenschaften *user* und *password* der Datei *application.properties* gespeichert. Diese Anmeldeinformationen werden bei der Ausführung von `DriverManager.getConnection(properties.getProperty("url"), properties);` verwendet, da die Eigenschaftendatei als Argument übergeben wird.

Sie können diese Hauptklasse nun mit Ihrem bevorzugten Tool ausführen:

- Bei Verwendung Ihrer IDE sollten Sie mit der rechten Maustaste auf die Klasse *DemoApplication* klicken und diese ausführen können.
- Bei Verwendung von Maven können Sie die Anwendung mithilfe von `mvn exec:java -Dexec.mainClass="com.example.demo.DemoApplication"` ausführen.

Die Anwendung sollte eine Verbindung mit Azure SQL-Datenbank herstellen, ein Datenbankschema erstellen und die Verbindung dann trennen, wie in den Konsolenprotokollen gezeigt:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Closing database connection 
```

### <a name="create-a-domain-class"></a>Erstellen einer Domänenklasse

Erstellen Sie neben der Klasse `DemoApplication` eine neue `Todo`-Java-Klasse, und fügen Sie den folgenden Code hinzu:

```java
package com.example.demo;

public class Todo {

    private Long id;
    private String description;
    private String details;
    private boolean done;

    public Todo() {
    }

    public Todo(Long id, String description, String details, boolean done) {
        this.id = id;
        this.description = description;
        this.details = details;
        this.done = done;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }

    public boolean isDone() {
        return done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }

    @Override
    public String toString() {
        return "Todo{" +
                "id=" + id +
                ", description='" + description + '\'' +
                ", details='" + details + '\'' +
                ", done=" + done +
                '}';
    }
}
```

Bei dieser Klasse handelt es sich um ein Domänenmodell, das der `todo`-Tabelle zugeordnet ist, die Sie beim Ausführen des Skripts *schema.sql* erstellt haben.

### <a name="insert-data-into-azure-sql-database"></a>Einfügen von Daten in die Azure SQL-Datenbank

Fügen Sie in der Datei *src/main/java/DemoApplication.java* nach der main-Methode die folgende Methode ein, um Daten in die Datenbank einzufügen:

```java
private static void insertData(Todo todo, Connection connection) throws SQLException {
    log.info("Insert data");
    PreparedStatement insertStatement = connection
            .prepareStatement("INSERT INTO todo (id, description, details, done) VALUES (?, ?, ?, ?);");

    insertStatement.setLong(1, todo.getId());
    insertStatement.setString(2, todo.getDescription());
    insertStatement.setString(3, todo.getDetails());
    insertStatement.setBoolean(4, todo.isDone());
    insertStatement.executeUpdate();
}
```

Sie können die Auskommentierung der beiden folgenden Zeilen in der Methode `main` aufheben:

```java
Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
insertData(todo, connection);
```

Das Ausführen der Hauptklasse sollte nun die folgende Ausgabe ergeben:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Closing database connection
```

### <a name="reading-data-from-azure-sql-database"></a>Lesen von Daten aus der Azure SQL-Datenbank

Lesen Sie die zuvor eingefügten Daten, um zu überprüfen, ob der Code ordnungsgemäß funktioniert.

Fügen Sie in der Datei *src/main/java/DemoApplication.java* nach der `insertData`-Methode die folgende Methode ein, um Daten aus der Datenbank zu lesen:

```java
private static Todo readData(Connection connection) throws SQLException {
    log.info("Read data");
    PreparedStatement readStatement = connection.prepareStatement("SELECT * FROM todo;");
    ResultSet resultSet = readStatement.executeQuery();
    if (!resultSet.next()) {
        log.info("There is no data in the database!");
        return null;
    }
    Todo todo = new Todo();
    todo.setId(resultSet.getLong("id"));
    todo.setDescription(resultSet.getString("description"));
    todo.setDetails(resultSet.getString("details"));
    todo.setDone(resultSet.getBoolean("done"));
    log.info("Data read from the database: " + todo.toString());
    return todo;
}
```

Sie können die Auskommentierung der folgenden Zeile nun in der Methode `main` aufheben:

```java
todo = readData(connection);
```

Das Ausführen der Hauptklasse sollte nun die folgende Ausgabe ergeben:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Closing database connection 
```

### <a name="updating-data-in-azure-sql-database"></a>Aktualisieren von Daten in Azure SQL-Datenbank

Aktualisieren Sie die zuvor eingefügten Daten.

Fügen Sie in der Datei *src/main/java/DemoApplication.java* nach der `readData`-Methode die folgende Methode ein, um die Daten in der Datenbank zu aktualisieren:

```java
private static void updateData(Todo todo, Connection connection) throws SQLException {
    log.info("Update data");
    PreparedStatement updateStatement = connection
            .prepareStatement("UPDATE todo SET description = ?, details = ?, done = ? WHERE id = ?;");

    updateStatement.setString(1, todo.getDescription());
    updateStatement.setString(2, todo.getDetails());
    updateStatement.setBoolean(3, todo.isDone());
    updateStatement.setLong(4, todo.getId());
    updateStatement.executeUpdate();
    readData(connection);
}
```

Sie können die Auskommentierung der beiden folgenden Zeilen in der Methode `main` aufheben:

```java
todo.setDetails("congratulations, you have updated data!");
updateData(todo, connection);
```

Das Ausführen der Hauptklasse sollte nun die folgende Ausgabe ergeben:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Update data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true} 
[INFO   ] Closing database connection 
```

### <a name="deleting-data-in-azure-sql-database"></a>Löschen von Daten in Azure SQL-Datenbank

Löschen Sie die zuvor eingefügten Daten.

Fügen Sie in der Datei *src/main/java/DemoApplication.java* nach der `updateData`-Methode die folgende Methode ein, um die Daten in der Datenbank zu löschen:

```java
private static void deleteData(Todo todo, Connection connection) throws SQLException {
    log.info("Delete data");
    PreparedStatement deleteStatement = connection.prepareStatement("DELETE FROM todo WHERE id = ?;");
    deleteStatement.setLong(1, todo.getId());
    deleteStatement.executeUpdate();
    readData(connection);
}
```

Sie können die Auskommentierung der folgenden Zeile nun in der Methode `main` aufheben:

```java
deleteData(todo, connection);
```

Das Ausführen der Hauptklasse sollte nun die folgende Ausgabe ergeben:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Update data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true} 
[INFO   ] Delete data 
[INFO   ] Read data 
[INFO   ] There is no data in the database! 
[INFO   ] Closing database connection 
```

## <a name="conclusion-and-resources-clean-up"></a>Zusammenfassung und Ressourcenbereinigung

Glückwunsch! Sie haben eine Java-Anwendung erstellt, die JDBC zum Speichern und Abrufen von Daten in bzw. aus einer Azure SQL-Datenbank verwendet.

Löschen Sie die Ressourcengruppe mit dem folgenden Befehl, um alle in dieser Schnellstartanleitung verwendeten Ressourcen zu bereinigen:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Nächste Schritte

- [Entwerfen Ihrer ersten Datenbank in Azure SQL-Datenbank](design-first-database-tutorial.md)  
- [Microsoft JDBC-Treiber für SQL Server](https://github.com/microsoft/mssql-jdbc)  
- [Melden von Problemen/Stellen von Fragen](https://github.com/microsoft/mssql-jdbc/issues)
