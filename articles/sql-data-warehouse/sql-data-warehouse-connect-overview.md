---
title: Verbinden mit Azure SQL Data Warehouse | Microsoft Docs
description: Verbindung mit Azure SQL Data Warehouse
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 2c937285b9c54072c6d1e1b0628c3d5fbfe786f3
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873663"
---
# <a name="connect-to-azure-sql-data-warehouse"></a>Verbinden mit Azure SQL Data Warehouse
Verbindung mit Azure SQL Data Warehouse

## <a name="find-your-server-name"></a>Suchen des Servernamens
Der Servername im folgenden Beispiel lautet etwa „samplesvr.database.windows.net“. So ermitteln Sie den vollqualifizierten Servernamen

1. Öffnen Sie das [Azure-Portal][Azure portal].
2. Klicken Sie auf **SQL Data Warehouses**.
3. Klicken Sie auf das Data Warehouse, mit dem Sie eine Verbindung herstellen möchten.
4. Suchen Sie den vollständigen Servernamen.
   
    ![Vollständiger Servername][1]

## <a name="supported-drivers-and-connection-strings"></a>Unterstützte Treiber und Verbindungszeichenfolgen
Azure SQL Data Warehouse unterstützt [ADO.NET][ADO.NET], [ODBC][ODBC], [PHP][PHP] und [JDBC][JDBC]. Um zur neuesten Version und Dokumentation zu gelangen, klicken Sie auf einen der genannten Treiber. Zur automatischen Erstellung der Verbindungszeichenfolge für den verwendeten Treiber klicken Sie im Azure-Portal auf **Datenbank-Verbindungszeichenfolgen anzeigen**, wie im vorherigen Beispiel zu sehen. Im Anschluss finden Sie auch einige Beispielverbindungszeichenfolgen für die einzelnen Treiber.

> [!NOTE]
> Es empfiehlt sich, das Verbindungstimeout auf 300 Sekunden festzulegen, damit die Verbindung bei kurzen Ausfällen bestehen bleibt.
> 
> 

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
Bei der Verbindungs- und Objekterstellung werden von SQL Data Warehouse einige Einstellungen standardisiert. Diese Einstellungen können nicht überschrieben werden:

| Datenbankeinstellung | Wert |
|:--- |:--- |
| [ANSI_NULLS][ANSI_NULLS] |EIN |
| [QUOTED_IDENTIFIERS][QUOTED_IDENTIFIERS] |EIN |
| [DATEFORMAT][DATEFORMAT] |mdy |
| [DATEFIRST][DATEFIRST] |7 |

## <a name="next-steps"></a>Nächste Schritte
Informationen zum Herstellen einer Verbindung und zum Ausführen von Abfragen mit Visual Studio finden Sie unter [Abfragen von Azure SQL Data Warehouse (Visual Studio)][Query with Visual Studio]. Weitere Informationen zu Authentifizierungsoptionen finden Sie unter [Authentifizierung in Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse].

<!--Articles-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Authentication to Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/server-connect.PNG


