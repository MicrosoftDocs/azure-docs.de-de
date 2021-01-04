---
title: In-Memory-Beispiel
description: Testen Sie In-Memory-Technologien von Azure SQL-Datenbank mit OLTP und dem Columnstore-Beispiel.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: b5a1035f8a213a6ce02dd3252ff7d3ddea46faf7
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92786580"
---
# <a name="in-memory-sample"></a>In-Memory-Beispiel
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

In-Memory-Technologien in Azure SQL-Datenbank können zur Verbesserung der Leistung Ihrer Anwendung beitragen und potenziell Kosten für Ihre Datenbank verringern. Durch Verwenden von In-Memory-Technologien in Azure SQL-Datenbank können Sie Leistungsverbesserungen mit verschiedenen Workloads erzielen.

In diesem Artikel finden Sie zwei Beispiele, die die Verwendung von In-Memory-OLTP und Columnstore-Indizes in Azure SQL-Datenbank veranschaulichen.

Weitere Informationen finden Sie unter

- [In-Memory-OLTP: Übersicht und Verwendungsszenarien](/sql/relational-databases/in-memory-oltp/overview-and-usage-scenarios) (einschließlich Verweisen auf Fallstudien und Informationen für Einsteiger)
- [Dokumentation für In-Memory-OLTP](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Beschreibung von Columnstore-Indizes](/sql/relational-databases/indexes/columnstore-indexes-overview)
- HTAP (Hybrid Transactional/Analytical Processing), auch als [Real-time Operational Analytics](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics) bekannt

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Installieren des Beispiels für In-Memory OLTP

Sie können im [Azure-Portal](https://portal.azure.com/) die Beispieldatenbank „AdventureWorksLT“ mit wenigen Klicks erstellen. Anhand der Schritte in diesem Abschnitt wird erläutert, wie Sie anschließend Ihre AdventureWorksLT-Datenbank mit In-Memory-OLTP-Objekten erweitern können, und es werden Leistungsvorteile demonstriert.

Eine einfachere, aber visuell ansprechendere Demo der Leistung von In-Memory-OLTP finden Sie hier:

- Version: [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Quellcode: [in-memory-oltp-demo-source-code](https://github.com/microsoft/sql-server-samples/tree/master/samples/features/in-memory-database)

### <a name="installation-steps"></a>Installationsschritte

1. Erstellen Sie im [Azure-Portal](https://portal.azure.com/) auf einem Server eine Datenbank vom Typ „Premium“ oder „Unternehmenskritisch“. Legen Sie die Beispieldatenbank „AdventureWorksLT“ als **Quelle** fest. Ausführliche Anweisungen finden Sie unter [Erstellen Ihrer ersten Datenbank in Azure SQL-Datenbank](database/single-database-create-quickstart.md).

2. Stellen Sie eine Verbindung mit der Datenbank mit SQL Server Management Studio [(SSMS.exe)](/sql/ssms/download-sql-server-management-studio-ssms)her.

3. Kopieren Sie das [In-Memory OLTP Transact-SQL-Skript](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_oltp_sample.sql) in die Zwischenablage. Das T-SQL-Skript erstellt die erforderlichen In-Memory-Objekte in der in Schritt 1 erstellten Beispieldatenbank „AdventureWorksLT“.

4. Fügen Sie das T-SQL-Skript in SSMS ein, und führen Sie es aus. Unerlässlich ist die `MEMORY_OPTIMIZED = ON`-Klausel in den CREATE TABLE-Anweisungen. Beispiel:

```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```

### <a name="error-40536"></a>Fehler 40536

Wenn bei Ausführen des T-SQL-Skripts der Fehler 40536 angezeigt wird, führen Sie das folgende T-SQL-Skript aus, um zu prüfen, ob die Datenbank In-Memory unterstützt:

```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```

Das Ergebnis **0** bedeutet, dass In-Memory nicht unterstützt wird. **1** bedeutet Unterstützung. Stellen Sie für die Problemdiagnose sicher, dass für die Datenbank die Dienstebene „Premium“ gilt.

### <a name="about-the-created-memory-optimized-items"></a>Informationen zu speicheroptimierten Elementen

**Tabellen** : Das Beispiel enthält die folgenden speicheroptimierten Tabellen:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed

Sie können im **Objekt-Explorer** in SSMS speicheroptimierte Tabellen untersuchen. Klicken Sie mit der rechten Maustaste auf **Tabellen** > **Filter** > **Filtereinstellungen** > **Speicheroptimiert** . Der Wert ist gleich 1.

Oder Sie können die Katalogsichten abfragen wie z. B.:

```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```

**Nativ kompilierte gespeicherte Prozedur** : Sie können „SalesLT.usp_InsertSalesOrder_inmem“ über eine Katalogsichtabfrage untersuchen:

```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```

&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Ausführen der OLTP-Beispielworkload

Der einzige Unterschied zwischen den beiden folgenden *gespeicherten Prozeduren* ist, dass die erste Prozedur speicheroptimierte Versionen der Tabellen verwendet, während die zweite Prozedur die herkömmlichen Tabellen auf dem Datenträger nutzt:

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**

In diesem Abschnitt wird veranschaulicht, wie Sie das praktische Hilfsprogramm **ostress.exe** zum Ausführen der beiden gespeicherten Prozeduren bei hohen Belastungsgraden verwenden. Sie können vergleichen, wie lange es dauert, bis die beiden Belastungstests abgeschlossen sind.

Für das Ausführen von „ostress.exe“ wird empfohlen, dass Sie Parameterwerte übergeben, die für beide der folgenden ausgelegt sind:

- Führen Sie eine große Anzahl gleichzeitiger Verbindungen mit „-n100“ aus.
- Lassen Sie jede Verbindung eine Schleife Hunderte Male durchlaufen, indem Sie „-r500“ angeben.

Allerdings möchten Sie möglicherweise mit wesentlich kleineren Werten wie „-n10“ und „-r50“ starten, um sicherzustellen, dass alles funktioniert.

### <a name="script-for-ostressexe"></a>Skript für „ostress.exe“

Dieser Abschnitt zeigt das T-SQL-Skript, das in unsere „ostress.exe“-Befehlszeile eingebettet ist. Das Skript verwendet Elemente, die von dem zuvor von Ihnen installierten T-SQL-Skript erstellt wurden.

Das folgende Skript fügt einen Beispielauftrag mit fünf Positionen in die folgenden speicheroptimierten *Tabellen* ein:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem

```sql
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```

Um die *_ondisk* -Version des vorherigen T-SQL-Skripts für „ostress.exe“ zu erstellen, müssen Sie beide Vorkommen der Teilzeichenfolge *_inmem* durch *_ondisk* ersetzen. Diese Austauschvorgänge wirken sich auf die Namen von Tabellen und gespeicherten Prozeduren aus.

#### <a name="install-rml-utilities-and-ostress"></a>Installieren von RML-Hilfsprogrammen und `ostress`

Im Idealfall planen Sie die Ausführung von „ostress.exe“ auf einer Azure-VM. Dazu erstellen Sie eine [Azure-VM](https://azure.microsoft.com/documentation/services/virtual-machines/) in der gleichen Azure-Region, in der sich die AdventureWorksLT-Datenbank befindet. Sie können „ostress.exe“ aber auch auf Ihrem Laptop ausführen.

Installieren Sie auf der VM oder dem gewählten Host die RML-Hilfsprogramme (Replay Markup Language). Dazu zählt auch „ostress.exe“.

Weitere Informationen finden Sie unter

- Diskussion zu „ostress.exe“ unter [Beispieldatenbank für In-Memory-OLTP](/sql/relational-databases/in-memory-oltp/sample-database-for-in-memory-oltp).
- [Beispieldatenbank für In-Memory-OLTP](/sql/relational-databases/in-memory-oltp/sample-database-for-in-memory-oltp).
- [Blog zur Installation von „ostress.exe“](https://techcommunity.microsoft.com/t5/sql-server-support/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql/ba-p/317910).

<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->

#### <a name="run-the-_inmem-stress-workload-first"></a>Zuerst Belastungsworkload *_inmem* ausführen

Sie können ein Fenster mit einer *RML-Eingabeaufforderung* zum Ausführen der „ostress.exe“-Befehlszeile verwenden. Die Befehlszeilenparameter weisen `ostress` an, die folgenden Aktionen auszuführen:

- Ausführen von 100 Verbindungen gleichzeitig (-n100).
- 50-maliges Ausführen des T-SQL-Skripts auf jeder Verbindung (-r50).

```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```

So führen Sie die vorherige „ostress.exe“-Befehlszeile aus:

1. Setzen Sie den Dateninhalt der Datenbank durch Ausführen des folgenden Befehls in SSMS zurück, um alle Daten zu löschen, die bei vorherigen Ausführungen eingefügt wurden:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Kopieren Sie den Text der vorhergehenden „ostress.exe“-Befehlszeile in die Zwischenablage.

3. Ersetzen Sie `<placeholders>` für die Parameter „-S“, „-U“, „-P“ und „-d“ durch die ordnungsgemäßen tatsächlichen Werte.

4. Führen Sie die bearbeitete Befehlszeile in einem RML-Befehlsfenster aus.

#### <a name="result-is-a-duration"></a>Das Ergebnis ist eine Dauer

Wenn `ostress.exe` abgeschlossen ist, wird die Testlaufdauer im RML-Befehlsfenster als letzte Zeile ausgegeben. Ein kürzerer Testlauf dauerte beispielsweise ca. 1,5 Minuten:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`

#### <a name="reset-edit-for-_ondisk-then-rerun"></a>Zurücksetzen, für *_ondisk* bearbeiten, danach erneut ausführen

Sobald Sie über das Ergebnis der Ausführung von *_inmem* verfügen, führen Sie die folgenden Schritte für die Ausführung von *_ondisk* durch:

1. Setzen Sie die Datenbank durch Ausführen des folgenden Befehls in SSMS zurück, um alle Daten zu löschen, die bei der vorherigen Ausführung eingefügt wurden:

   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. Bearbeiten Sie die ostress.exe-Befehlszeile so, dass alle Vorkommen von *_inmem* durch *_ondisk* ersetzt werden.

3. Führen Sie „ostress.exe“ ein zweites Mal aus, und erfassen Sie die Dauer.

4. Setzen Sie die Datenbank erneut zurück, um auf verantwortliche Weise eine u.U. große Menge an Testdaten zu löschen.

#### <a name="expected-comparison-results"></a>Erwartete Vergleichsergebnisse

Unsere In-Memory-Tests haben für diese einfache Workload eine **neunmal** höhere Leistung ergeben, wenn `ostress` auf einem virtuellen Azure-Computer ausgeführt wird, der sich in der gleichen Azure-Region wie die Datenbank befindet.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Installieren des In-Memory Analytics-Beispiels

In diesem Abschnitt vergleichen Sie die E/A- und Statistikergebnisse mit dem Verwenden eines Columnstore-Indexes im Vergleich mit einem herkömmlichen B-Struktur-Index.

Für Echtzeitanalysen einer OLTP-Workload empfiehlt es sich häufig, einen nicht gruppierten Columnstore-Index zu verwenden. Weitere Informationen finden Sie unter [Beschreibung von Columnstore-Indizes](/sql/relational-databases/indexes/columnstore-indexes-overview).

### <a name="prepare-the-columnstore-analytics-test"></a>Vorbereiten des Columnstore-Analysetests

1. Erstellen Sie im Azure-Portal anhand des Beispiels eine neue AdventureWorksLT-Datenbank.
   - Verwenden Sie denselben Namen.
   - Wählen Sie die Dienstebene „Premium“ aus.

2. Kopieren Sie [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_analytics_sample.sql) in die Zwischenablage.
   - Das T-SQL-Skript erstellt die erforderlichen In-Memory-Objekte in der in Schritt 1 erstellten Beispieldatenbank „AdventureWorksLT“.
   - Das Skript erstellt die Dimensionstabelle und zwei Faktentabellen. Beide Faktentabellen werden mit 3,5 Mio. Zeilen aufgefüllt.
   - Die Ausführung des Skripts kann 15 dauern.

3. Fügen Sie das T-SQL-Skript in SSMS ein, und führen Sie es aus. Unerlässlich ist das Schlüsselwort **COLUMNSTORE** in einer **CREATE INDEX** -Anweisung wie in:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Legen Sie „AdventureWorksLT“ auf den Kompatibilitätsgrad 130 fest:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Der Kompatibilitätsgrad 130 steht nicht in direkter Beziehung mit den In-Memory-Features Doch der Grad 130 bietet im Allgemeinen eine höhere Abfrageleistung als 120.

#### <a name="key-tables-and-columnstore-indexes"></a>Wichtige Tabellen und Columnstore-Indizes

- „dbo.FactResellerSalesXL_CCI“ ist eine Tabelle mit einem gruppierten Columnstore-Index, der auf *Datenebene* eine erweiterte Komprimierung aufweist.

- „dbo.FactResellerSalesXL_PageCompressed“ ist eine Tabelle mit einem vergleichbaren herkömmlichen gruppierten Index, der nur auf *Seitenebene* komprimiert ist.

#### <a name="key-queries-to-compare-the-columnstore-index"></a>Wichtige Abfragen zum Vergleichen des Columnstore-Indexes

[Hier](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/clustered_columnstore_sample_queries.sql) finden Sie mehrere T-SQL-Abfragetypen, die Sie ausführen können, um Leistungssteigerungen zu erkennen. Achten Sie in Schritt 2 im T-SQL-Skript auf dieses Paar von Abfragen. Die beiden Abfragen unterscheiden sich nur in einer Zeile:

- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`

Für die Tabelle „FactResellerSalesXL\_CCI“ ist ein gruppierter Columnstore-Index vorhanden.

Der folgende T-SQL-Skriptauszug gibt Statistiken für E/A und Dauer der Abfrage der einzelnen Tabellen zurück.

```sql
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

In einer Datenbank mit dem P2-Tarif können Sie etwa einen neunfachen Leistungszuwachs für diese Abfrage bei Verwenden des gruppierten Columnstore-Indexes im Vergleich zum traditionellen Index erwarten. Mit P15 kann bei Verwenden des Columnstore-Indexes ein Leistungszuwachs um das 57-fache erwartet werden.

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart 1: In-Memory-OLTP-Technologien für höhere T-SQL-Leistung](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp)

- [Verwenden von In-Memory-OLTP in einer vorhandenen Azure SQL-Anwendung](in-memory-oltp-configure.md)

- [Überwachen des In-Memory-OLTP-Speichers](in-memory-oltp-monitor-space.md) für In-Memory-OLTP

## <a name="additional-resources"></a>Zusätzliche Ressourcen

### <a name="deeper-information"></a>Weiterführende Informationen

- [Erfahren Sie, wie Quorum die Workload der wichtigen Datenbank verdoppelt, während es die DTU mit In-Memory-OLTP in Azure SQL-Datenbank um 70 % reduziert](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Blogbeitrag „In-Memory OLTP in Azure SQL Database“](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/) (In-Memory OLTP in Azure SQL-Datenbank)

- [In-Memory OLTP (Arbeitsspeicheroptimierung)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)

- [Beschreibung von Columnstore-Indizes](/sql/relational-databases/indexes/columnstore-indexes-overview)

- [Erste Schritte mit Columnstore für operative Echtzeitanalyse](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

- Das [Whitepaper zu allgemeinen Workloadmustern und Überlegungen zur Migration](/previous-versions/dn673538(v=msdn.10))beschreibt Workloadmuster, bei denen In-Memory-OLTP im Allgemeinen erhebliche Leistungssteigerungen bietet.

#### <a name="application-design"></a>Anwendungsentwurf

- [In-Memory OLTP (In-Memory Optimization)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)

- [Verwenden von In-Memory-OLTP in einer vorhandenen Azure SQL-Anwendung](in-memory-oltp-configure.md)

#### <a name="tools"></a>Tools

- [Azure portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)

- [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt)