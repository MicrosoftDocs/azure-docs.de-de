---
title: Spark-Connector mit Azure SQL-Datenbank und SQL Server| Microsoft-Dokumentation
description: Erfahren Sie mehr über den Einsatz des Spark-Connectors für Azure SQL-Datenbank und SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 8e531de34302ef8aee571c960955d33a4832aa11
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60331508"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Beschleunigen der Big Data-Echtzeitanalyse mit dem Spark-Connector für Azure SQL-Datenbank und SQL Server

Der Spark-Connector für Azure SQL-Datenbank und SQL Server ermöglicht es, SQL-Datenbanken – Azure SQL-Datenbank und SQL Server eingeschlossen – als Eingabedatenquelle oder Ausgabedatensenke für Spark-Aufträge zu verwenden. So können transaktionale Echtzeitdaten in der Big Data-Analyse genutzt und Ergebnisse für Ad-Hoc-Abfragen oder Berichterstellung dauerhaft gespeichert werden. Im Vergleich zum integrierten JDBC-Connector bietet dieser Connector die Möglichkeit, Daten per Massenvorgang in SQL-Datenbanken einzufügen. Dies führt zu einer erheblichen Leistungssteigerung: Daten können gegenüber einer zeilenweisen Einfügung 10- bis 20-Mal schneller eingefügt werden. Der Spark-Connector für Azure SQL-Datenbank und SQL Server bietet außerdem Unterstützung für die AAD-Authentifizierung. Dies ermöglicht es Ihnen, unter Verwendung Ihres AAD-Kontos aus Azure Databricks eine sichere Verbindung mit Azure SQL-Datenbank herzustellen. Mit dem integrierten JDBC-Connector werden ähnliche Schnittstellen bereitgestellt. Die Migration Ihrer vorhandenen Spark-Aufträge zu diesem neuen Connector ist sehr einfach durchzuführen.

## <a name="download"></a>Download
Laden Sie zunächst den Spark-Connector für SQL DB aus dem GitHub-Repository [azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) herunter.

## <a name="official-supported-versions"></a>Offiziell unterstützte Versionen

| Komponente                            |Version                  |
| :----------------------------------- | :---------------------- |
| Apache Spark                         |2.0.2 oder höher           |
| Scala                                |2.10 oder höher            |
| Microsoft JDBC-Treiber für SQL Server |6.2 oder höher             |
| Microsoft SQL Server                 |SQL Server 2008 oder höher |
| Azure SQL-Datenbank                   |Unterstützt                |

Der Spark-Connector für Azure SQL-Datenbank und SQL Server verwendet den Microsoft JDBC-Treiber für SQL Server, um Daten zwischen Spark-Workerknoten und SQL-Datenbanken zu verschieben:
 
Der Datenfluss ist wie folgt:
1. Der Spark-Masterknoten stellt eine Verbindung mit SQL Server oder Azure SQL-Datenbank her und lädt Daten aus einer spezifischen Tabelle oder unter Verwendung einer bestimmten SQL-Abfrage.
2. Der Spark-Masterknoten verteilt die Daten zur Transformation auf die Workerknoten. 
3. Der Workerknoten stellt eine Verbindung mit SQL Server oder Azure SQL-Datenbank her und schreibt Daten in die Datenbank. Benutzer könne auswählen, ob sie die Daten Zeile für Zeile oder in einem Massenvorgang einfügen möchten.

Das folgende Diagramm veranschaulicht den Datenfluss.

   ![Architektur](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Erstellen des Spark-Connectors für SQL DB
Derzeit wird für das Connectorprojekt Maven verwendet. Sie können Folgendes ausführen, um den Connector ohne Abhängigkeiten zu erstellen:

- „mvn clean package“
- Download der aktuellen JAR-Versionen aus dem release-Ordner
- Einschließen der Spark-JAR für SQL DB

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Herstellen einer Verbindung zwischen Spark und SQL DB per Connector
Sie können über Spark-Aufträge eine Verbindung mit Azure SQL-Datenbank oder SQL Server herstellen und Daten lesen oder schreiben. Sie können alternativ auch eine DML- oder DDL-Abfrage in Azure SQL-Datenbank-Instanz oder einer SQL Server-Datenbank ausführen.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Lesen von Daten aus Azure SQL-Datenbank oder SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients"
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Das Lesen von Daten aus Azure SQL-Datenbank oder SQL Server mit einer angegebenen SQL-Abfrage
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDb(config)
collection.show()
```

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Schreiben von Daten nach Azure SQL-Datenbank oder SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
 
// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients"
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Ausführen einer DML- oder DDL-Abfrage in Azure SQL-Datenbank oder SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City= 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.SqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>Herstellen einer Verbindung zwischen Spark und Azure SQL-Datenbank per AAD-Authentifizierung
Sie können unter Verwendung der Azure Active Directory-Authentifizierung (AAD) eine Verbindung mit Azure SQL-Datenbank herstellen. Verwenden Sie die AAD-Authentifizierung für eine zentrale Verwaltung der Identitäten der Datenbankbenutzer und als Alternative zur SQL Server-Authentifizierung.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Verbindungsherstellung im Authentifizierungsmodus „ActiveDirectoryPassword“
#### <a name="setup-requirement"></a>Anforderungen für die Einrichtung
Wenn Sie den Authentifizierungsmodus „ActiveDirectoryPassword“ verwenden, müssen Sie [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) und zugehörige Abhängigkeiten herunterladen und in den Java-Buildpfad einschließen.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username ",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>Verbindungsherstellung per Zugriffstoken
#### <a name="setup-requirement"></a>Anforderungen für die Einrichtung
Wenn Sie den auf einem Zugriffstoken basierenden Authentifizierungsmodus verwenden, müssen Sie [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) und zugehörige Abhängigkeiten herunterladen und in den Java-Buildpfad einschließen.

Unter [Verwenden der Azure Active Directory-Authentifizierung für die Authentifizierung bei SQL-Datenbank](sql-database-aad-authentication.md) finden Sie Informationen dazu, wie Sie ein Zugriffstoken für Ihre Azure SQL-Datenbank-Instanz abrufen.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token ",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Schreiben von Daten nach Azure SQL-Datenbank oder SQL Server per Masseneinfügevorgang
Der herkömmliche JDBC-Connector schreibt Daten Zeile für Zeile in Azure SQL-Datenbank oder SQL Server. Mit dem Spark-Connector für SQL DB können Sie Daten per Massenvorgang in eine SQL-Datenbank einfügen. Dies führt zu einer erheblich verbesserten Schreibleistung beim Laden großer Datasets oder beim Laden von Daten in Tabellen, in denen ein ColumStore-Index verwenden wird.

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/** 
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "databaseName"      -> "zeqisql",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Nächste Schritte
Laden Sie – sofern noch nicht geschehen – den Spark-Connector für Azure SQL-Datenbank und SQL Server aus dem GitHub-Repository [azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) herunter, und sehen Sie sich die zusätzlichen Ressourcen im Repository an:

-   [Azure Databricks-Beispielnotebooks](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Beispielskripts (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Es kann auch nützlich sein, den [Leitfaden zu Apache Spark SQL, DataFrames und Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html) (in englischer Sprache) und die [Azure Databricks-Dokumentation](https://docs.microsoft.com/azure/azure-databricks/) zu lesen.

