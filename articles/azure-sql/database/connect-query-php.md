---
title: Verwenden von PHP für Abfragen
description: Erfahren Sie, wie Sie unter Verwendung von PHP ein Programm erstellen, das eine Verbindung mit einer Datenbank in Azure SQL-Datenbank oder Azure SQL Managed Instance herstellt und diese mithilfe von T-SQL-Anweisungen abfragt.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: sqldbrb=2
ms.openlocfilehash: e0ad8e9e71a0ee2c23412d535fe79955edb39ba8
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576649"
---
# <a name="quickstart-use-php-to-query-a-database-in-azure-sql-database"></a>Schnellstart: Abfragen einer Datenbank in Azure SQL-Datenbank mithilfe von PHP
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

In diesem Artikel wird veranschaulicht, wie Sie mithilfe von [PHP](https://php.net/manual/en/intro-whatis.php) eine Verbindung mit einer Datenbank in Azure SQL-Datenbank oder Azure SQL Managed Instance herstellen. Sie können dann T-SQL-Anweisungen zum Abfragen von Daten verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Für die Durchführung dieses Schnellstarts benötigen Sie Folgendes:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Eine Datenbank in Azure SQL-Datenbank oder Azure SQL Managed Instance In den folgenden Schnellstartanleitungen erfahren Sie jeweils, wie Sie eine Datenbank erstellen und anschließend konfigurieren:

  | Aktion | SQL-Datenbank | Verwaltete SQL-Instanz | SQL Server auf Azure-VMs |
  |:--- |:--- |:---|:---|
  | Erstellen| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [BEFEHLSZEILENSCHNITTSTELLE (CLI)](scripts/create-and-configure-database-cli.md) | [BEFEHLSZEILENSCHNITTSTELLE (CLI)](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurieren | [IP-Firewallregel auf Serverebene](firewall-create-server-level-portal-quickstart.md)| [Verbindung von einem virtuellen Computer](../managed-instance/connect-vm-instance-configure.md)|
  |||[Verbindungen von lokalen Computern](../managed-instance/point-to-site-p2s-configure.md) | [Herstellen einer Verbindung mit einer SQL Server-Instanz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Laden der Daten|Laden von Adventure Works gemäß Schnellstartanleitung|[Wiederherstellen von Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [Wiederherstellen von Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||Wiederherstellen oder Importieren von Adventure Works über eine [BACPAC-Datei](database-import.md) von [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Wiederherstellen oder Importieren von Adventure Works über eine [BACPAC-Datei](database-import.md) von [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||




  > [!IMPORTANT]
  > Die Skripts in diesem Artikel sind für die Adventure Works-Datenbank geschrieben. Bei einer verwalteten SQL-Instanz müssen Sie entweder die Adventure Works-Datenbank in eine Instanzdatenbank importieren oder die Skripts in diesem Artikel zur Verwendung der Wide World Importers-Datenbank anpassen.

- PHP-Software für Ihr Betriebssystem:

  - **macOS**: Installieren Sie PHP, den ODBC-Treiber und dann den PHP-Treiber für SQL Server. Siehe [Schritt 1, 2 und 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Linux**: Installieren Sie PHP, den ODBC-Treiber und dann den PHP-Treiber für SQL Server. Siehe [Schritt 1, 2 und 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Windows**: Installieren Sie PHP, PHP-Treiber und anschließend den ODBC-Treiber und SQLCMD. Weitere Informationen finden Sie unter [Schritt 1.2 und 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-server-connection-information"></a>Ermitteln von Serververbindungsinformationen

Ermitteln Sie die Verbindungsinformationen, die Sie zum Herstellen einer Verbindung mit der Datenbank in Azure SQL-Datenbank benötigen. In den weiteren Verfahren benötigen Sie den vollqualifizierten Server- oder Hostnamen, den Datenbanknamen und die Anmeldeinformationen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Navigieren Sie zur Seite **SQL-Datenbanken** oder **Verwaltete SQL-Instanzen**.

3. Auf der Seite **Übersicht** finden Sie den vollqualifizierten Servernamen für eine Datenbank in Azure SQL-Datenbank neben **Servername** oder den vollqualifizierten Servernamen (oder die IP-Adresse) für Azure SQL Managed Instance bzw. für SQL Server auf einem virtuellen Azure-Computer neben **Host**. Um den Namen des Servers oder Hosts zu kopieren, zeigen Sie darauf, und wählen Sie das Symbol **Kopieren** aus.

> [!NOTE]
> Verbindungsinformationen für SQL Server auf einem virtuellen Azure-Computer finden Sie unter [Herstellen einer Verbindung mit einer SQL Server-Instanz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="add-code-to-query-the-database"></a>Hinzufügen von Code zum Abfragen der Datenbank

1. Erstellen Sie in Ihrem bevorzugten Text-Editor eine neue Datei namens *sqltest.php*.  

1. Ersetzen Sie ihren Inhalt durch den folgenden Code. Fügen Sie anschließend die entsprechenden Werte für Server, Datenbank, Benutzer und Kennwort hinzu.

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
       );
       //Establishes the connection
       $conn = sqlsrv_connect($serverName, $connectionOptions);
       $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
            FROM [SalesLT].[ProductCategory] pc
            JOIN [SalesLT].[Product] p
            ON pc.productcategoryid = p.productcategoryid";
       $getResults= sqlsrv_query($conn, $tsql);
       echo ("Reading data from table" . PHP_EOL);
       if ($getResults == FALSE)
           echo (sqlsrv_errors());
       while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
        echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
       }
       sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>Ausführen des Codes

1. Führen Sie die App an der Eingabeaufforderung aus.

   ```bash
   php sqltest.php
   ```

1. Vergewissern Sie sich, dass die ersten 20 Zeilen zurückgegeben werden, und schließen Sie das App-Fenster.

## <a name="next-steps"></a>Nächste Schritte

- [Entwerfen Ihrer ersten Datenbank in Azure SQL-Datenbank](design-first-database-tutorial.md)
- [Microsoft PHP Drivers for SQL Server](https://github.com/Microsoft/msphpsql/) (Microsoft-PHP-Treiber für SQL Server)
- [Seite für Problemmeldungen und Fragen](https://github.com/Microsoft/msphpsql/issues)
- [Beispiel für eine Wiederholungslogik: Herstellen resilienter Azure SQL-Verbindungen mit PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
