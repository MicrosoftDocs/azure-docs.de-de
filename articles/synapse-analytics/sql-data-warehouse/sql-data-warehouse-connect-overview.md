---
title: Herstellen einer Verbindung mit einem SQL-Pool in Azure Synapse
description: Stellen Sie eine Verbindung mit dem SQL-Pool her.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: azure-synapse, seo-lt-2019, devx-track-csharp
ms.openlocfilehash: 3483fa03e8c05101b88bde5c964b74b382d32a52
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462849"
---
# <a name="connect-to-a-sql-pool-in-azure-synapse"></a>Herstellen einer Verbindung mit einem SQL-Pool in Azure Synapse 

Stellen Sie Verbindung mit einem SQL-Pool in Azure Synapse her.

## <a name="find-your-server-name"></a>Suchen des Servernamens

Der Servername im folgenden Beispiel lautet „sqlpoolservername.database.windows.net“. So ermitteln Sie den vollqualifizierten Servernamen

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Klicken Sie auf **Azure Synapse Analytics**.
3. Klicken Sie auf den SQL-Pool, mit dem Sie eine Verbindung herstellen möchten.
4. Suchen Sie den vollständigen Servernamen.

   ![Vollständiger Servername](media/sql-data-warehouse-connect-overview/server-connect.PNG)

## <a name="supported-drivers-and-connection-strings"></a>Unterstützte Treiber und Verbindungszeichenfolgen

Der SQL-Pool unterstützt [ADO.NET](/dotnet/framework/data/adonet?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), [ODBC](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [PHP](/sql/connect/php/overview-of-the-php-sql-driver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) und [JDBC](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Um zur neuesten Version und Dokumentation zu gelangen, klicken Sie auf einen der genannten Treiber.

Zur automatischen Erstellung der Verbindungszeichenfolge für den verwendeten Treiber klicken Sie im Azure-Portal auf **Datenbank-Verbindungszeichenfolgen anzeigen**, wie im vorherigen Beispiel zu sehen. Im Anschluss finden Sie auch einige Beispielverbindungszeichenfolgen für die einzelnen Treiber.

> [!NOTE]
> Es empfiehlt sich, das Verbindungstimeout auf 300 Sekunden festzulegen, damit die Verbindung bei kurzen Ausfällen bestehen bleibt.

### <a name="adonet-connection-string-example"></a>Beispielverbindungszeichenfolge für ADO.NET

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Beispielverbindungszeichenfolge für ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Beispielverbindungszeichenfolge für PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Beispielverbindungszeichenfolge für JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Verbindungseinstellungen

Bei der Verbindungs- und Objekterstellung werden vom SQL-Pool einige Einstellungen standardisiert. Diese Einstellungen können nicht überschrieben werden:

| Einstellung für den SQL-Pool | Wert |
|:--- |:--- |
| [ANSI_NULLS](/sql/t-sql/statements/set-ansi-nulls-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |EIN |
| [QUOTED_IDENTIFIERS](/sql/t-sql/statements/set-quoted-identifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |EIN |
| [DATEFORMAT](/sql/t-sql/statements/set-dateformat-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |dmy |
| [DATEFIRST](/sql/t-sql/statements/set-datefirst-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |7 |

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Herstellen einer Verbindung und zum Durchführen von Abfragen mit Visual Studio finden Sie unter [Abfragen von Azure SQL Data Warehouse (Visual Studio)](sql-data-warehouse-query-visual-studio.md). Weitere Informationen zu Authentifizierungsoptionen finden Sie unter [Authentifizierung in Azure Synapse Analytics](sql-data-warehouse-authentication.md).
