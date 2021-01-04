---
title: Verbindungszeichenfolgen für Synapse SQL
description: Verbindungszeichenfolgen für Synapse SQL
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 6859d0582997ee861713090ccb4c22ed58ec4ca7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462340"
---
# <a name="connection-strings-for-synapse-sql"></a>Verbindungszeichenfolgen für Synapse SQL

Sie können über verschiedene Anwendungsprotokolle eine Verbindung mit Synapse SQL herstellen, etwa über [ADO.NET](https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx), [ODBC](https://msdn.microsoft.com/library/jj730314.aspx), [PHP](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396) und [JDBC](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx). Im Folgenden sind einige Beispiele für Verbindungszeichenfolgen für die einzelnen Protokolle aufgeführt. 

Sie können auch das Azure-Portal zum Einrichten Ihrer Verbindungszeichenfolge verwenden.  Um die Verbindungszeichenfolge über das Azure-Portal zu erstellen, navigieren Sie zum Blatt Ihrer Datenbank, und wählen Sie unter *Zusammenfassung* die Option *Datenbank-Verbindungszeichenfolgen anzeigen* aus.

## <a name="sample-adonet-connection-string"></a>Beispiel-Verbindungszeichenfolge für ADO.NET

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Beispiel-Verbindungszeichenfolge für ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>Beispiel-Verbindungszeichenfolge für PHP

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>Beispiel-Verbindungszeichenfolge für JDBC

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

> [!NOTE]
> Es empfiehlt sich, das Verbindungstimeout auf 300 Sekunden festzulegen, damit die Verbindung bei kurzen Ausfällen bestehen bleibt.

## <a name="recommendations"></a>Empfehlungen

Für die Ausführung von Abfragen eines **serverlosen SQL-Pools** werden [Azure Data Studio](get-started-azure-data-studio.md) und Azure Synapse Studio empfohlen.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Abfragen Ihrer Analysen mit Visual Studio und anderen Anwendungen finden Sie unter [Herstellen einer Verbindung mit Azure Synapse Analytics mithilfe von Visual Studio und SSDT](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
