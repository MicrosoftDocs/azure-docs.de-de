---
title: Abfragen von Azure SQL-Datenbank mithilfe von Node.js | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie unter Verwendung von Node.js ein Programm erstellen, das eine Verbindung mit einer Azure SQL-Datenbank herstellt und sie mithilfe von T-SQL-Anweisungen abfragt.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 8d050fe92af7b22363b0a9207201412bc12d9082
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792183"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Schnellstart: Abfragen einer Azure SQL-Datenbank mithilfe von Node.js

In diesem Artikel erfahren Sie, wie Sie mithilfe von [Node.js](https://nodejs.org) eine Verbindung mit einer Azure SQL-Datenbank herstellen. Sie können dann T-SQL-Anweisungen zum Abfragen von Daten verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vor dem Ausführen dieses Beispiels sicher, dass die folgenden erforderlichen Komponenten vorhanden sind:

- Eine Azure SQL-Datenbank. In den folgenden Schnellstartanleitungen erfahren Sie jeweils, wie Sie eine Datenbank in Azure SQL-Datenbank erstellen und anschließend konfigurieren:

  || Einzeldatenbank | Verwaltete Instanz |
  |:--- |:--- |:---|
  | Erstellen| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [BEFEHLSZEILENSCHNITTSTELLE (CLI)](scripts/sql-database-create-and-configure-database-cli.md) | [BEFEHLSZEILENSCHNITTSTELLE (CLI)](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurieren | [IP-Firewallregel auf Serverebene](sql-database-server-level-firewall-rule.md)| [Verbindung von einem virtuellen Computer](sql-database-managed-instance-configure-vm.md)|
  |||[Verbindung von einer lokalen Ressource](sql-database-managed-instance-configure-p2s.md)
  |Laden von Daten|Laden von Adventure Works gemäß Schnellstartanleitung|[Wiederherstellen von Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Wiederherstellen oder Importieren von Adventure Works über eine [BACPAC-Datei](sql-database-import.md) von [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Die Skripts in diesem Artikel sind für die Adventure Works-Datenbank geschrieben. Bei einer verwalteten Instanz müssen Sie entweder die Adventure Works-Datenbank in eine Instanzdatenbank importieren oder die Skripts in diesem Artikel zur Verwendung der Wide World Importers-Datenbank anpassen.


- Node.js-Software für Ihr Betriebssystem:

  - **MacOS:** Installieren Sie Homebrew und Node.js und anschließend den ODBC-Treiber und SQLCMD. Weitere Informationen finden Sie unter [Schritt 1.2 und 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).
  
  - **Ubuntu:** Installieren Sie Node.js und anschließend den ODBC-Treiber und SQLCMD. Weitere Informationen finden Sie unter [Schritt 1.2 und 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).
  
  - **Windows:** Installieren Sie Chocolatey und Node.js und anschließend den ODBC-Treiber und SQLCMD. Weitere Informationen finden Sie unter [Schritt 1.2 und 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

## <a name="get-sql-server-connection-information"></a>Abrufen von SQL Server-Verbindungsinformationen

Rufen Sie die Verbindungsinformationen ab, die Sie zum Herstellen einer Verbindung mit der Azure SQL-Datenbank benötigen. In den weiteren Verfahren benötigen Sie den vollqualifizierten Server- oder Hostnamen, den Datenbanknamen und die Anmeldeinformationen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Navigieren Sie zur Seite **SQL-Datenbanken** oder **Verwaltete SQL-Instanzen**.

3. Sehen Sie sich auf der Seite **Übersicht** bei einer Einzeldatenbank den vollqualifizierten Servernamen neben **Servername** und bei einer verwalteten Instanz den vollqualifizierten Servernamen neben **Host** an. Um den Namen des Servers oder Hosts zu kopieren, zeigen Sie darauf, und wählen Sie das Symbol **Kopieren** aus. 

## <a name="create-the-project"></a>Erstellen des Projekts

Öffnen Sie eine Eingabeaufforderung, und erstellen Sie einen Ordner namens *sqltest*. Navigieren Sie zu dem erstellten Ordner, und führen Sie den folgenden Befehl aus:

  ```bash
  npm init -y
  npm install tedious@5.0.3
  npm install async@2.6.2
  ```

## <a name="add-code-to-query-database"></a>Hinzufügen von Code zum Abfragen der Datenbank

1. Erstellen Sie in Ihrem bevorzugten Text-Editor eine neue Datei namens *sqltest.js*.

1. Ersetzen Sie ihren Inhalt durch den folgenden Code. Fügen Sie anschließend die entsprechenden Werte für Server, Datenbank, Benutzer und Kennwort hinzu.

    ```js
    var Connection = require('tedious').Connection;
    var Request = require('tedious').Request;

    // Create connection to database
    var config =
    {
        authentication: {
            options: {
                userName: 'userName', // update me
                password: 'password' // update me
            },
            type: 'default'
        },
        server: 'your_server.database.windows.net', // update me
        options:
        {
            database: 'your_database', //update me
            encrypt: true
        }
    }
    var connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on('connect', function(err)
        {
            if (err)
            {
                console.log(err)
            }
            else
            {
                queryDatabase()
            }
        }
    );

    function queryDatabase()
    {
        console.log('Reading rows from the Table...');

        // Read all rows from table
        var request = new Request(
            "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc "
                + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
            function(err, rowCount, rows)
            {
                console.log(rowCount + ' row(s) returned');
                process.exit();
            }
        );

        request.on('row', function(columns) {
            columns.forEach(function(column) {
                console.log("%s\t%s", column.metadata.colName, column.value);
            });
        });
        connection.execSql(request);
    }
    ```

> [!NOTE]
> Im Codebeispiel wird die Beispieldatenbank **AdventureWorksLT** für Azure SQL verwendet.

## <a name="run-the-code"></a>Ausführen des Codes

1. Führen Sie das Programm an der Eingabeaufforderung aus.

    ```bash
    node sqltest.js
    ```

1. Vergewissern Sie sich, dass die ersten 20 Zeilen zurückgegeben werden, und schließen Sie das Anwendungsfenster.

## <a name="next-steps"></a>Nächste Schritte

- [Microsoft Node.js Driver for SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server) (Microsoft Node.js-Treiber für SQL Server)

- Herstellen einer Verbindung und Abfragen unter Windows/Linux/macOS mit [.NET Core](sql-database-connect-query-dotnet-core.md), [Visual Studio Code](sql-database-connect-query-vscode.md) oder [SSMS](sql-database-connect-query-ssms.md) (nur Windows)

- [Erste Schritte mit .NET Core unter Windows/Linux/Mac OS unter Verwendung der Befehlszeile](/dotnet/core/tutorials/using-with-xplat-cli)

- Entwerfen Ihrer ersten Azure SQL-Datenbank mit [.NET](sql-database-design-first-database-csharp.md) oder [SSMS](sql-database-design-first-database.md)
