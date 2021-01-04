---
title: Erste Schritte mit datenbankübergreifenden Abfragen
description: Sie erfahren, wie Sie Abfragen für elastische Datenbanken für vertikal partitionierte Datenbanken verwenden.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: dd8a30c60c81ef2761d5dc051d2dfeb300e1bac4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792122"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Erste Schritte mit datenbankübergreifenden Abfragen (vertikale Partitionierung, Vorschau)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Mit Abfragen für elastische Datenbanken (Vorschau) für Azure SQL-Datenbank können Sie T-SQL-Abfragen ausführen, die sich mit einem einzigen Verbindungspunkt über mehrere Datenbanken erstrecken. Dieser Artikel gilt für [vertikal partitionierte Datenbanken](elastic-query-vertical-partitioning.md).  

Nach Abschluss des Themas haben Sie Folgendes gelernt: Konfigurieren und Verwenden einer Azure SQL-Datenbank zum Ausführen von Abfragen, die sich über mehrere verwandte Datenbanken erstrecken.

Weitere Informationen zur Abfragefunktion für elastische Datenbanken finden Sie unter [Übersicht über die Abfrage für elastische Datenbanken in Azure SQL-Datenbank](elastic-query-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

Es ist die Berechtigung ALTER ANY EXTERNAL DATA SOURCE erforderlich. Diese Berechtigung ist in der Berechtigung ALTER DATABASE enthalten. ALTER ANY EXTERNAL DATA SOURCE-Berechtigungen sind erforderlich, um auf die zu Grunde liegende Datenquelle zu verweisen.

## <a name="create-the-sample-databases"></a>Erstellen der Beispieldatenbanken

Erstellen Sie zunächst die zwei Datenbanken **Customers** (Kunden) und **Orders** (Bestellungen), auf demselben oder auf unterschiedlichen Servern.

Führen Sie die folgenden Abfragen in der Datenbank **Orders** aus, um die Tabelle **OrderInformation** zu erstellen und die Beispieldaten einzugeben.

```tsql
CREATE TABLE [dbo].[OrderInformation](
    [OrderID] [int] NOT NULL,
    [CustomerID] [int] NOT NULL
    )
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
```

Führen Sie dann die folgende Abfrage in der Datenbank **Customers** aus, um die Tabelle **CustomerInformation** zu erstellen und die Beispieldaten einzugeben.

```tsql
CREATE TABLE [dbo].[CustomerInformation](
    [CustomerID] [int] NOT NULL,
    [CustomerName] [varchar](50) NULL,
    [Company] [varchar](50) NULL
    CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC)
)
INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC')
INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ')
INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO')
```

## <a name="create-database-objects"></a>Erstellen von Datenbankobjekten

### <a name="database-scoped-master-key-and-credentials"></a>Erstellen des Datenbankhauptschlüssels und der Anmeldeinformationen

1. Öffnen Sie SQL Server Management Studio oder SQL Server Data Tools in Visual Studio.
2. Stellen Sie eine Verbindung mit der Datenbank „Orders“ her, und führen Sie die folgenden T-SQL-Befehle aus:

    ```tsql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
    CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
    WITH IDENTITY = '<username>',
    SECRET = '<password>';  
    ```

    Als „username“ und „password“ sollten der Benutzername und das Kennwort verwendet werden, die zum Anmelden an der Datenbank „Customers“ genutzt werden.
    Die Authentifizierung mithilfe von Azure Active Directory mit elastischen Abfragen wird derzeit nicht unterstützt.

### <a name="external-data-sources"></a>Externe Datenquellen

Um eine externe Datenquelle zu erstellen, führen Sie den folgenden Befehl für die Datenbank „Orders“ aus:

```tsql
CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
    (TYPE = RDBMS,
    LOCATION = '<server_name>.database.windows.net',
    DATABASE_NAME = 'Customers',
    CREDENTIAL = ElasticDBQueryCred,
) ;
```

### <a name="external-tables"></a>Externe Tabellen

Erstellen Sie eine externe Tabelle in der Datenbank „Orders“, die der Definition der Tabelle „CustomerInformation“ entspricht:

```tsql
CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
( [CustomerID] [int] NOT NULL,
    [CustomerName] [varchar](50) NOT NULL,
    [Company] [varchar](50) NOT NULL)
WITH
( DATA_SOURCE = MyElasticDBQueryDataSrc)
```

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Ausführen einer T-SQL-Abfrage für eine elastische Beispieldatenbank

Nachdem Sie die externe Datenquelle und die externen Tabellen definiert haben, können Sie jetzt T-SQL verwenden, um externe Tabellen abzufragen. Führen Sie für die Datenbank „Orders“ diese Abfrage aus:

```tsql
SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
FROM OrderInformation
INNER JOIN CustomerInformation
ON CustomerInformation.CustomerID = OrderInformation.CustomerID
```

## <a name="cost"></a>Kosten

Derzeit ist die Abfragefunktion für elastische Datenbanken in den Kosten für Ihre Azure SQL-Datenbank enthalten.  

Preisinformationen finden Sie unter [Preise für SQL-Datenbank](https://azure.microsoft.com/pricing/details/sql-database).

## <a name="next-steps"></a>Nächste Schritte

* Eine Übersicht über elastische Abfragen finden Sie unter [Übersicht über elastische Abfragen in Azure SQL-Datenbank](elastic-query-overview.md).
* Die Syntax und Beispiele für Abfragen von vertikal partitionierten Daten finden Sie unter [Abfragen von vertikal partitionierten Daten](elastic-query-vertical-partitioning.md).
* Ein Tutorial zur horizontalen Partitionierung (Sharding) finden Sie unter [Erste Schritte mit elastischen Abfragen für horizontale Partitionierung (Sharding)](elastic-query-getting-started.md).
* Die Syntax und Beispiele für Abfragen von horizontal partitionierten Daten finden Sie unter [Abfragen von horizontal partitionierten Daten](elastic-query-horizontal-partitioning.md).
* Unter [sp\_execute \_remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) finden Sie eine gespeicherte Prozedur, mit der eine Transact-SQL-Anweisung für eine einzelne Remoteinstanz von Azure SQL-Datenbank oder für eine Gruppe von Datenbanken ausgeführt wird, die als Shards in einem Schema mit horizontaler Partitionierung dienen.