---
title: 'Schnellstart: Herstellen einer Verbindung unter Verwendung von C++: Azure Database for MySQL'
description: Diese Schnellstartanleitung enthält ein C++-Codebeispiel, mit dem Sie eine Verbindung mit den Daten aus Azure-Datenbank für MySQL herstellen und Daten abfragen können.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.devlang: cpp
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: e8b2842300e43a9de88cc0b9b3ae9ce6cd754612
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535842"
---
# <a name="quickstart-use-connectorc-to-connect-and-query-data-in-azure-database-for-mysql"></a>Schnellstart: Verwenden von Connector/C++ zum Herstellen einer Verbindung und zum Abfragen von Daten in Azure Database for MySQL

Dieser Schnellstart zeigt, wie Sie mit einer C#-Anwendung eine Verbindung mit einer Azure Database for MySQL-Instanz herstellen. Es wird veranschaulicht, wie Sie SQL-Anweisungen zum Abfragen, Einfügen, Aktualisieren und Löschen von Daten in der Datenbank verwenden. In diesem Thema wird davon ausgegangen, dass Sie mit der C++-Entwicklung vertraut sind, aber noch keine Erfahrung mit Azure Database for MySQL haben.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Schnellstart werden die Ressourcen, die in den folgenden Anleitungen erstellt wurden, als Ausgangspunkt verwendet:
- [Erstellen eines Servers für Azure-Datenbank für MySQL mithilfe des Azure-Portals](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Erstellen eines Servers für Azure-Datenbank für MySQL mithilfe der Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

Außerdem benötigen Sie Folgendes:
- Installieren von [.NET Framework](https://www.microsoft.com/net/download)
- Installieren von [Visual Studio](https://www.visualstudio.com/downloads/)
- Installieren von [MySQL-Connector/C++](https://dev.mysql.com/downloads/connector/cpp/) 
- Installieren von [Boost](https://www.boost.org/)

> [!IMPORTANT] 
> Sicherstellen, dass der IP-Adresse, über die Sie eine Verbindung herstellen, die Firewallregeln des Servers über das [Azure-Portal](./howto-manage-firewall-using-portal.md) oder die [Azure CLI](./howto-manage-firewall-using-cli.md) hinzugefügt wurden

## <a name="install-visual-studio-and-net"></a>Installieren von Visual Studio Code und .NET
Bei den Schritten in diesem Abschnitt wird davon ausgegangen, dass Sie mit der .NET-Entwicklung vertraut sind.

### <a name="windows"></a>**Windows**
- Installieren Sie Visual Studio 2019 Community. Visual Studio 2019 Community ist eine erweiterbare IDE mit vollem Funktionsumfang, die kostenlos bereitgestellt wird. Mit dieser IDE können Sie moderne Anwendungen für Android, iOS und Windows, Web- und Datenanwendungen sowie Clouddienste entwickeln. Sie können entweder das vollständige .NET Framework oder nur .NET Core installieren. Die Codeausschnitte in der Schnellstartanleitung können mit beiden Lösungen verwendet werden. Falls Visual Studio bereits auf Ihrem Computer installiert ist, können Sie die nächsten beiden Schritte überspringen.
   1. Laden Sie das [Visual Studio 2019-Installationsprogramm](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15) herunter. 
   2. Führen Sie das Installationsprogramm aus, und befolgen Sie die Installationsanweisungen, um die Installation abzuschließen.

### <a name="configure-visual-studio"></a>**Konfigurieren von Visual Studio**
1. Fügen Sie in Visual Studio unter „Projekt“ > „Eigenschaften“ > „Linker“ > „Allgemein“ > „Zusätzliche Bibliotheksverzeichnisse“ das Verzeichnis „\lib\opt“ (Beispiel: „C:\Programme (x86)\MySQL\MySQL Connector C++ 1.1.9\lib\opt“) des C++-Connectors hinzu.
2. Gehen Sie in Visual Studio unter „Projekt“ > „Eigenschaften“ > „C/C++“ > „Allgemein“ > „Zusätzliche Includeverzeichnisse“ wie folgt vor:
   - Fügen Sie das Verzeichnis „\include“ des C++-Connectors (Beispiel: „C:\Programme (x86)\MySQL\MySQL Connector C++ 1.1.9\include“\) hinzu.
   - Fügen Sie das Stammverzeichnis der Boost-Bibliothek hinzu (Beispiel: „C:\boost_1_64_0“\).
3. Fügen Sie in Visual Studio unter „Projekt“ > „Eigenschaften“ > „Linker“ > „Eingabe“ > „Zusätzliche Abhängigkeiten“ dem Textfeld die Bibliothek **mysqlcppconn.lib** hinzu.
4. Kopieren Sie die Datei **mysqlcppconn.dll** aus dem Bibliotheksordner des C++-Connectors in Schritt 3 in dasselbe Verzeichnis wie die ausführbare Datei der Anwendung, oder fügen Sie sie der Umgebungsvariablen hinzu, damit sie von Ihrer Anwendung gefunden werden kann.

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen
Rufen Sie die Verbindungsinformationen ab, die zum Herstellen einer Verbindung mit der Azure SQL-Datenbank für MySQL erforderlich sind. Sie benötigen den vollqualifizierten Servernamen und die Anmeldeinformationen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Klicken Sie im Azure-Portal im linken Menü auf **Alle Ressourcen**, und suchen Sie dann nach dem soeben erstellten Server, z.B. **mydemoserver**.
3. Klicken Sie auf den Servernamen.
4. Notieren Sie sich im Bereich **Übersicht** des Servers den **Servernamen** und den **Anmeldenamen des Serveradministrators**. Wenn Sie Ihr Kennwort vergessen haben, können Sie es in diesem Bereich auch zurücksetzen.
 :::image type="content" source="./media/connect-cpp/1_server-overview-name-login.png" alt-text="Servername für Azure-Datenbank für MySQL":::

## <a name="connect-create-table-and-insert-data"></a>Herstellen der Verbindung, Erstellen der Tabelle und Einfügen von Daten
Verwenden Sie den folgenden Code, um eine Verbindung herzustellen und die Daten zu laden, indem Sie die SQL-Anweisungen **CREATE TABLE** und **INSERT INTO** verwenden. Im Code wird die sql::Driver-Klasse mit der connect()-Methode verwendet, um eine Verbindung mit MySQL herzustellen. Anschließend werden im Code die Methoden „createStatement()“ und „execute()“ verwendet, um die Datenbankbefehle auszuführen. 

Ersetzen Sie die Parameter für Host, DBName, Benutzer und Kennwort. Sie können die Parameter durch die Werte ersetzen, die Sie beim Erstellen des Servers und der Datenbank angegeben haben. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::Statement *stmt;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }

    //please create database "quickstartdb" ahead of time
    con->setSchema("quickstartdb");

    stmt = con->createStatement();
    stmt->execute("DROP TABLE IF EXISTS inventory");
    cout << "Finished dropping table (if existed)" << endl;
    stmt->execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);");
    cout << "Finished creating table" << endl;
    delete stmt;

    pstmt = con->prepareStatement("INSERT INTO inventory(name, quantity) VALUES(?,?)");
    pstmt->setString(1, "banana");
    pstmt->setInt(2, 150);
    pstmt->execute();
    cout << "One row inserted." << endl;

    pstmt->setString(1, "orange");
    pstmt->setInt(2, 154);
    pstmt->execute();
    cout << "One row inserted." << endl;

    pstmt->setString(1, "apple");
    pstmt->setInt(2, 100);
    pstmt->execute();
    cout << "One row inserted." << endl;

    delete pstmt;
    delete con;
    system("pause");
    return 0;
}
```

## <a name="read-data"></a>Lesen von Daten

Verwenden Sie den folgenden Code, um die Daten mit einer SQL-Anweisung des Typs **SELECT** zu verbinden und zu lesen. Im Code wird die sql::Driver-Klasse mit der connect()-Methode verwendet, um eine Verbindung mit MySQL herzustellen. Anschließend werden im Code die Methoden „prepareStatement()“ und „executeQuery()“ verwendet, um die Auswahlbefehle auszuführen. Als Nächstes wird im Code „next()“ verwendet, um zu den Datensätzen in den Ergebnissen zu gelangen. Schließlich werden im Code „getInt()“ und „getString()“ verwendet, um die Werte im Datensatz zu analysieren.

Ersetzen Sie die Parameter für Host, DBName, Benutzer und Kennwort. Sie können die Parameter durch die Werte ersetzen, die Sie beim Erstellen des Servers und der Datenbank angegeben haben. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }

    con->setSchema("quickstartdb");

    //select  
    pstmt = con->prepareStatement("SELECT * FROM inventory;");
    result = pstmt->executeQuery();

    while (result->next())
        printf("Reading from table=(%d, %s, %d)\n", result->getInt(1), result->getString(2).c_str(), result->getInt(3));

    delete result;
    delete pstmt;
    delete con;
    system("pause");
    return 0;
}
```

## <a name="update-data"></a>Aktualisieren von Daten
Verwenden Sie den folgenden Code, um die Daten mit einer SQL-Anweisung des Typs **UPDATE** zu verbinden und zu lesen. Im Code wird die sql::Driver-Klasse mit der connect()-Methode verwendet, um eine Verbindung mit MySQL herzustellen. Anschließend werden im Code die Methoden „prepareStatement()“ und „executeQuery()“ verwendet, um die Aktualisierungsbefehle auszuführen. 

Ersetzen Sie die Parameter für Host, DBName, Benutzer und Kennwort. Sie können die Parameter durch die Werte ersetzen, die Sie beim Erstellen des Servers und der Datenbank angegeben haben. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }
    
    con->setSchema("quickstartdb");

    //update
    pstmt = con->prepareStatement("UPDATE inventory SET quantity = ? WHERE name = ?");
    pstmt->setInt(1, 200);
    pstmt->setString(2, "banana");
    pstmt->executeQuery();
    printf("Row updated\n");

    delete con;
    delete pstmt;
    system("pause");
    return 0;
}
```


## <a name="delete-data"></a>Löschen von Daten
Verwenden Sie den folgenden Code, um die Daten mit einer SQL-Anweisung des Typs **DELETE** zu verbinden und zu lesen. Im Code wird die sql::Driver-Klasse mit der connect()-Methode verwendet, um eine Verbindung mit MySQL herzustellen. Anschließend werden im Code die Methoden „prepareStatement()“ und „executeQuery()“ verwendet, um die Löschbefehle auszuführen.

Ersetzen Sie die Parameter für Host, DBName, Benutzer und Kennwort. Sie können die Parameter durch die Werte ersetzen, die Sie beim Erstellen des Servers und der Datenbank angegeben haben. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }
    
    con->setSchema("quickstartdb");
        
    //delete
    pstmt = con->prepareStatement("DELETE FROM inventory WHERE name = ?");
    pstmt->setString(1, "orange");
    result = pstmt->executeQuery();
    printf("Row deleted\n");    
    
    delete pstmt;
    delete con;
    delete result;
    system("pause");
    return 0;
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
> [Migrieren der MySQL-Datenbank auf Azure-Datenbank für MySQL durch Sicherungen und Wiederherstellungen](concepts-migrate-dump-restore.md)
