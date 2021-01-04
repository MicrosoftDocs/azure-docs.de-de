---
title: Ausführen von Abfragen über Clouddatenbanken mit unterschiedlichen Schemas hinweg
description: Einrichten von datenbankübergreifenden Abfragen über vertikale Partitionen
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: c507a4c618713ba83d25b9defa918092db1a3c8e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792088"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Ausführen von Abfragen über Clouddatenbanken mit unterschiedlichen Schemas hinweg (Vorschau)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

![Abfrage über Tabellen in unterschiedlichen Datenbanken hinweg][1]

Vertikal partitionierte Datenbanken verwenden verschiedene Sätze von Tabellen in verschiedenen Datenbanken. Das bedeutet, dass das Schema bei verschiedenen Datenbanken unterschiedlich ist. Beispielsweise befinden sich alle bestandsbezogenen Tabellen in einer Datenbank, während alle Buchhaltungstabellen in einer zweiten Datenbank enthalten sind.

## <a name="prerequisites"></a>Voraussetzungen

* Der Benutzer muss über die Berechtigung ALTER ANY EXTERNAL DATA SOURCE verfügen. Diese Berechtigung ist in der Berechtigung ALTER DATABASE enthalten.
* ALTER ANY EXTERNAL DATA SOURCE-Berechtigungen sind erforderlich, um auf die zu Grunde liegende Datenquelle zu verweisen.

## <a name="overview"></a>Übersicht

> [!NOTE]
> Im Gegensatz zur horizontalen Partitionierung hängen diese DDL-Anweisungen nicht von der Festlegung einer Datenebene mit einer Shardzuordnung über die Clientbibliothek für elastische Datenbanken ab.
>

1. [CREATE MASTER KEY](/sql/t-sql/statements/create-master-key-transact-sql)
2. [CREATE DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
3. [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql)
4. [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)

## <a name="create-database-scoped-master-key-and-credentials"></a>Erstellen des Datenbankhauptschlüssels und der Anmeldeinformationen

Die Anmeldeinformationen werden von der elastischen Abfrage für die Verbindung mit Ihren Remotedatenbanken verwendet.  

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
SECRET = '<password>'
[;]
```

> [!NOTE]
> Stellen Sie sicher, dass `<username>` kein **„\@Servername“** -Suffix enthält.

## <a name="create-external-data-sources"></a>Erstellen externer Datenquellen

Syntax:

<External_Data_Source> ::= CREATE EXTERNAL DATA SOURCE <data_source_name> WITH (TYPE = RDBMS, LOCATION = ’<fully_qualified_server_name>’, DATABASE_NAME = ‘<remote_database_name>’,  
    CREDENTIAL = <credential_name> ) [;]

> [!IMPORTANT]
> Der TYPE-Parameter muss auf **RDBMS** festgelegt werden.

### <a name="example"></a>Beispiel

Das folgende Beispiel veranschaulicht die Verwendung der CREATE-Anweisung für externe Datenquellen.

```sql
CREATE EXTERNAL DATA SOURCE RemoteReferenceData
   WITH
      (
         TYPE=RDBMS,
         LOCATION='myserver.database.windows.net',
         DATABASE_NAME='ReferenceData',
         CREDENTIAL= SqlUser
      );
```

So rufen Sie die Liste der aktuellen externen Datenquellen ab

```sql
select * from sys.external_data_sources;
```

### <a name="external-tables"></a>Externe Tabellen

Syntax:

CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ]) { WITH ( <rdbms_external_table_options> ) } )[;]

<rdbms_external_table_options> ::= DATA_SOURCE = <External_Data_Source>, [ SCHEMA_NAME = N'nonescaped_schema_name',] [ OBJECT_NAME = N'nonescaped_object_name',]

### <a name="example"></a>Beispiel

```sql
CREATE EXTERNAL TABLE [dbo].[customer]
   (
      [c_id] int NOT NULL,
      [c_firstname] nvarchar(256) NULL,
      [c_lastname] nvarchar(256) NOT NULL,
      [street] nvarchar(256) NOT NULL,
      [city] nvarchar(256) NOT NULL,
      [state] nvarchar(20) NULL,
      DATA_SOURCE = RemoteReferenceData
   );
```

Das folgende Beispiel zeigt, wie Sie die Liste der externen Tabellen aus der aktuellen Datenbank abrufen:

```sql
select * from sys.external_tables;
```

### <a name="remarks"></a>Bemerkungen

Die Abfrage für elastische Datenbanken erweitert die vorhandene externe Tabellensyntax zum Definieren von externen Tabellen, die externe Datenquellen vom Typ RDBMS verwenden. Eine externe Tabellendefinition für die vertikale Partitionierung behandelt die folgenden Aspekte:

* **Schema** : In der DDL für externe Tabellen ist ein Schema definiert, das Ihre Abfragen verwenden können. Das in Ihrer Definition für externe Tabellen angegebene Schema muss mit dem Schema der Tabellen in der Remotedatenbank übereinstimmen, in der die eigentlichen Daten gespeichert werden.
* **Remotedatenbankverweis:** Die DDL für externe Tabellen verweist auf eine externe Datenquelle. Die externe Datenquelle gibt den Servernamen und Datenbanknamen der Remotedatenbank an, in der die eigentlichen Tabellendaten gespeichert sind.

Wenn Sie eine externe Datenquelle gemäß der Beschreibung im vorherigen Abschnitt verwenden, lautet die Syntax zum Erstellen externer Tabellen wie folgt:

Die DATA_SOURCE-Klausel definiert die externe Datenquelle (d. h. die Remotedatenbank bei vertikaler Partitionierung), die für die externe Tabelle verwendet wird.  

Die Klauseln SCHEMA_NAME und OBJECT_NAME bieten die Möglichkeit, die Definition der externen Tabelle einer Tabelle in einem anderen Schema in der Remotedatenkbank bzw. einer Tabelle mit einem anderen Namen zuzuordnen. Das ist nützlich, wenn Sie eine externe Tabelle für eine Katalogsicht oder DMV in Ihrer Remotedatenbank definieren möchten oder wenn der Name der Remotetabelle bereits lokal verwendet wird.  

Die folgende DDL-Anweisung löscht eine vorhandene Definition für eine externe Tabelle aus dem lokalen Katalog. Das wirkt sich nicht auf die Remotedatenbank aus.

```sql
DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  
```

**Berechtigungen für CREATE/DROP EXTERNAL TABLE:** Berechtigungen vom Typ ALTER ANY EXTERNAL DATA SOURCE sind für die DDL für externe Tabellen erforderlich, die auch für den Verweis auf die zugrunde liegende Datenquelle benötigt wird.  

## <a name="security-considerations"></a>Sicherheitshinweise

Benutzer mit Zugriff auf die externe Tabelle erhalten automatisch Zugriff auf die zugrunde liegenden Remotetabellen gemäß den Anmeldeinformationen, die in der externen Datenquellendefinition angegeben sind. Sie müssen den Zugriff auf die externe Tabelle sorgfältig verwalten, um eine unerwünschte Erhöhung von Berechtigungen über die Anmeldeinformationen für die externe Datenquelle zu vermeiden. Herkömmliche SQL-Berechtigungen können zum Gewähren (GRANT) oder Widerrufen (REVOKE) des Zugriffs auf eine externe Tabelle wie bei einer normalen Tabelle verwendet werden.  

## <a name="example-querying-vertically-partitioned-databases"></a>Beispiel: Abfragen von vertikal partitionierten Datenbanken

Die folgende Abfrage führt eine Dreiwegeverknüpfung zwischen den zwei lokalen Tabellen für Aufträge und Auftragspositionen und der Remotetabelle für Kunden aus. Hier sehen Sie ein Beispiel für den Verweisdatenanwendungsfall für die Abfrage elastischer Datenbanken:

```sql
    SELECT
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline,
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer
    JOIN orders
    ON c_id = o_c_id
    JOIN  order_line
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Gespeicherte Prozedur für T-SQL-Remoteausführung: sp\_execute_remote

Mit der elastischen Abfrage wurde auch eine gespeicherte Prozedur eingeführt, die einen Direktzugriff auf die Remotedatenbank bietet. Die gespeicherte Prozedur heißt [sp\_execute \_remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) und kann verwendet werden, um gespeicherte Remoteprozeduren oder T-SQL-Code in der Remotedatenbank auszuführen. Hierfür werden die folgenden Parameter verwendet:

* Datenquellenname (nvarchar): Name der externen Datenquelle vom Typ RDBMS.
* Abfrage (nvarchar): T-SQL-Abfrage, die für die Remotedatenbank ausgeführt werden soll.
* Parameterdeklaration (nvarchar) – optional: Zeichenfolge mit Datentypdefinitionen für die Parameter, die im Query-Parameter verwendet werden (wie sp_executesql).
* Parameterwertliste – optional: Durch Trennzeichen getrennte Liste von Parameterwerten (wie sp_executesql).

Die Prozedur „sp\_execute\_remote“ verwendet die externe Datenquelle, die in den Aufrufparametern angegeben ist, um die jeweilige T-SQL-Anweisung in der Remotedatenbank auszuführen. Dabei werden die Anmeldeinformationen der externen Datenquelle verwendet, um die Verbindung mit der Remotedatenbank herzustellen.  

Beispiel:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Konnektivität für Tools

Sie können herkömmliche SQL Server-Verbindungszeichenfolgen verwenden, um Ihre BI- und Datenintegrationstools mit Datenbanken auf dem Server zu verbinden, für die elastische Abfragen aktiviert und externe Tabellen definiert sind. Stellen Sie sicher, dass SQL Server als Datenquelle für das Tool unterstützt wird. Verweisen Sie dann auf die elastische Abfragedatenbank und die externen Tabellen wie auf jede andere SQL Server-Datenbank, zu der Sie mit dem Tool eine Verbindung herstellen würden.

## <a name="best-practices"></a>Bewährte Methoden

* Stellen Sie sicher, dass die Endpunktdatenbank für elastische Abfragen Zugriff auf die Remotedatenbank hat, indem Sie den Zugriff für Azure-Dienste in der Firewallkonfiguration für Azure SQL-Datenbank aktivieren. Stellen Sie darüber hinaus sicher, dass mit den in der externen Datenquellendefinition angegebenen Anmeldeinformationen die Anmeldung bei der Remotedatenbank möglich ist und für die Anmeldeinformationen Berechtigungen zum Zugriff auf die Remotetabelle festgelegt wurden.  
* Eine Abfrage für elastische Datenbanken funktioniert am besten für Abfragen, in denen der größte Teil der Berechnung in den Remotedatenbanken erfolgt. In der Regel erhalten Sie optimale Abfrageleistung mit benutzerdefinierten Filterprädikaten, die in den Remotedatenbanken oder Verknüpfungen ausgewertet werden können, die vollständig in der Remotedatenbank ausgeführt werden können. Für andere Abfragemuster müssen möglicherweise große Mengen von Daten aus der Remotedatenbank geladen werden, wodurch Leistungseinbußen auftreten.

## <a name="next-steps"></a>Nächste Schritte

* Eine Übersicht über elastische Abfragen finden Sie unter [Übersicht über elastische Abfragen in Azure SQL-Datenbank](elastic-query-overview.md).
* Ein Tutorial zur vertikalen Partitionierung finden Sie unter [Erste Schritte mit datenbankübergreifenden Abfragen (vertikale Partitionierung)](elastic-query-getting-started-vertical.md).
* Ein Tutorial zur horizontalen Partitionierung (Sharding) finden Sie unter [Erste Schritte mit elastischen Abfragen für horizontale Partitionierung (Sharding)](elastic-query-getting-started.md).
* Die Syntax und Beispiele für Abfragen von horizontal partitionierten Daten finden Sie unter [Abfragen von horizontal partitionierten Daten](elastic-query-horizontal-partitioning.md).
* Unter [sp\_execute \_remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) finden Sie eine gespeicherte Prozedur, mit der eine Transact-SQL-Anweisung für eine einzelne Remoteinstanz von Azure SQL-Datenbank oder für eine Gruppe von Datenbanken ausgeführt wird, die als Shards in einem Schema mit horizontaler Partitionierung dienen.

<!--Image references-->
[1]: ./media/elastic-query-vertical-partitioning/verticalpartitioning.png

<!--anchors-->