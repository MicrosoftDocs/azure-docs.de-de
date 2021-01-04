---
title: 'Gewusst wie: Verbessern der Leistung von Anwendungen mithilfe der Batchverarbeitung'
description: In diesem Thema wird unter Beweis gestellt, dass Sie die Geschwindigkeit und Skalierbarkeit Ihrer Azure SQL-Datenbank- und Azure SQL Managed Instance-Anwendungen durch Verwenden der Batchverarbeitung bei Datenbankvorgängen erheblich verbessern können. Die Batchverarbeitungstechniken können auf jede beliebige SQL-Datenbank angewendet werden. In diesem Artikel liegt jedoch der Schwerpunkt auf Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 07334d62cee94be8b5b8dd6188c1d6354c4d584b
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792598"
---
# <a name="how-to-use-batching-to-improve-azure-sql-database-and-azure-sql-managed-instance-application-performance"></a>Gewusst wie: Verbessern der Leistung von Azure SQL-Datenbank- und Azure SQL Managed Instance-Anwendungen mithilfe der Batchverarbeitung
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Mit Batchvorgängen für Azure SQL-Datenbank und Azure SQL Managed Instance können Sie die Leistung und Skalierbarkeit Ihrer Anwendungen erheblich verbessern. Zur Veranschaulichung der Vorteile werden im ersten Teil dieses Artikels einige Beispieltestergebnisse angeführt, in denen sequenzielle und batchbasierte Anforderungen an eine Datenbank in Azure SQL-Datenbank oder Azure SQL Managed Instance verglichen werden. Der Rest des Artikels geht auf Techniken, Szenarien und Überlegungen ein, die Sie bei der erfolgreichen Verwendung der Batchverarbeitung in Ihrer Azure-Anwendung unterstützen.

## <a name="why-is-batching-important-for-azure-sql-database-and-azure-sql-managed-instance"></a>Die Bedeutung der Batchverarbeitung für Azure SQL-Datenbank und Azure SQL Managed Instance

Die Batchverarbeitung von Aufrufen an einen Remotedienst ist eine bewährte Strategie zur Verbesserung von Leistung und Skalierbarkeit. Für jede Interaktion mit einem Remotedienst (also etwa für Serialisierung, Netzwerkübertragung und Deserialisierung) fallen feste Verarbeitungskosten an. Durch das Zusammenfassen vieler einzelner Transaktionen in einem einzigen Batch werden diese Kosten minimiert.

In diesem Artikel werden verschiedene Batchverarbeitungsstrategien und -szenarien untersucht. Diese Strategien sind zwar auch für lokale Anwendungen wichtig, die SQL Server verwenden, es gibt jedoch einige Gründe, die Verwendung der Batchverarbeitung für Azure SQL-Datenbank und Azure SQL Managed Instance hervorzuheben:

* Die Netzwerklatenz ist beim Zugriff auf Azure SQL-Datenbank und Azure SQL Managed Instance potenziell höher, insbesondere, wenn Sie von außerhalb desselben Microsoft Azure-Rechenzentrums auf Azure SQL-Datenbank oder Azure SQL Managed Instance zugreifen.
* Aufgrund der Mehrinstanzenfähigkeit von Azure SQL-Datenbank und Azure SQL Managed Instance korreliert die Effizienz der Datenzugriffsschicht mit der allgemeinen Skalierbarkeit der Datenbank. Als Reaktion auf die Überschreitung von vordefinierten Kontingenten können Azure SQL-Datenbank und Azure SQL Managed Instance den Durchsatz verringern oder Drosselungsausnahmen auslösen. Durch effizienzsteigernde Maßnahmen wie z. B. die Batchverarbeitung können Sie mehr Arbeit erledigen, bevor diese Grenzwerte erreicht werden.
* Die Batchverarbeitung eignet sich auch für Architekturen mit mehreren Datenbanken (Sharding). Die Effizienz der Interaktion mit den einzelnen Datenbank-Einheiten ist weiterhin ein Schlüsselfaktor für die Skalierbarkeit im Allgemeinen.

Einer der Vorteile der Verwendung von Azure SQL-Datenbank oder Azure SQL Managed Instance ist, dass Sie die zum Hosten der Datenbank verwendeten Server nicht verwalten müssen. Aufgrund dieser verwalteten Infrastruktur müssen Sie jedoch auch anders an Datenbankoptimierungen herangehen. Sie haben nicht mehr die Möglichkeit, die Datenbankhardware oder die Netzwerkinfrastruktur zu optimieren. Um diese Aspekte kümmert sich Microsoft Azure. Sie können in erster Linie steuern, wie Ihre Anwendung mit Azure SQL-Datenbank und Azure SQL Managed Instance interagiert. Die Batchverarbeitung ist eine der Optimierungsmöglichkeiten.

Im ersten Teil dieses Artikels werden verschiedene Batchverarbeitungstechniken für .NET-Anwendungen untersucht, die Azure SQL-Datenbank oder Azure SQL Managed Instance verwenden. In den letzten beiden Abschnitten werden Batchverarbeitungsrichtlinien und -szenarien behandelt.

## <a name="batching-strategies"></a>Batchverarbeitungsstrategien

### <a name="note-about-timing-results-in-this-article"></a>Hinweis zu den Zeitangaben in den Ergebnissen dieses Artikels

> [!NOTE]
> Die Ergebnisse sind keine Benchmarks, sondern veranschaulichen die **relative Leistung** . Die Zeitangaben basieren auf einem Durchschnittswert von mindestens zehn Testläufen. Bei den Vorgängen handelt es sich um Einfügungen in eine leere Tabelle. Die Testergebnisse wurden vor V12 ermittelt und entsprechen nicht unbedingt dem Durchsatz, der mit einer V12-Datenbank und den neuen [DTU-Dienstebenen](database/service-tiers-dtu.md) oder [Dienstebenen virtueller Kerne](database/service-tiers-vcore.md) erreicht wird. Der relative Nutzen der Batchverarbeitungstechnik dürfte jedoch ähnlich ausfallen.

### <a name="transactions"></a>Transaktionen

Es kommt Ihnen vielleicht etwas merkwürdig vor, die Erläuterung der Batchverarbeitung mit Informationen zu Transaktionen zu beginnen. Die Verwendung clientseitiger Transaktionen hat jedoch dezente Auswirkungen auf die serverseitige Batchverarbeitung, die zu einer Verbesserung der Leistung beitragen. Transaktionen können außerdem mit nur wenigen Codezeilen hinzugefügt werden. Dadurch lässt sich ohne großen Aufwand die Leistung sequenzieller Vorgänge optimieren.

Der folgende C#-Code enthält eine Sequenz von Einfüge- und Aktualisierungsvorgängen für eine einfache Tabelle:

```csharp
List<string> dbOperations = new List<string>();
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
dbOperations.Add("insert MyTable values ('new value',1)");
dbOperations.Add("insert MyTable values ('new value',2)");
dbOperations.Add("insert MyTable values ('new value',3)");
```

Der folgende ADO.NET-Code führt diese Vorgänge nacheinander aus:

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();

    foreach(string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn);
        cmd.ExecuteNonQuery();
    }
}
```

Dieser Code lässt sich am besten durch die Implementierung einer clientseitigen Batchverarbeitung der Aufrufe optimieren. Die Leistung dieses Codes lässt sich jedoch ganz einfach erhöhen, indem Sie die Aufrufsequenz in eine Transaktion einschließen. Hier sehen Sie den gleichen Code mit einer Transaktion:

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();
    SqlTransaction transaction = conn.BeginTransaction();

    foreach (string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
        cmd.ExecuteNonQuery();
    }

    transaction.Commit();
}
```

Transaktionen werden genau genommen in beiden Beispielen verwendet. Im ersten Beispiel ist jeder einzelne Aufruf eine implizite Transaktion. Im zweiten Beispiel umschließt eine explizite Transaktion alle Aufrufe. Gemäß der Dokumentation für das [Write-Ahead-Transaktionsprotokoll](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide?view=sql-server-ver15#WAL)werden Protokolldatensätze auf die Festplatte geleert, wenn für die Transaktion ein Commit ausgeführt wird. Wenn also mehr Aufrufe in eine Transaktion eingeschlossen werden, kann der Schreibvorgang für das Transaktionsprotokoll bis zum Commit der Transaktion hinausgezögert werden. Dadurch ermöglichen Sie im Endeffekt eine Batchverarbeitung der Schreibvorgänge für das Transaktionsprotokoll des Servers.

Die folgende Tabelle zeigt einige Ad-hoc-Testergebnisse. Bei den Tests wurden jeweils die gleichen sequenziellen Einfügevorgänge mit und ohne Transaktionen ausgeführt. Zur Verbesserung der Aussagekraft wurde der erste Testsatz remote auf einem Laptop ausgeführt und an die Datenbank in Microsoft Azure gerichtet. Der zweite Testsatz wurde über einen Clouddienst und eine Clouddatenbank ausgeführt, die sich beide im selben Microsoft Azure-Datencenter (USA, Westen) befanden. Die folgende Tabelle zeigt die Dauer sequenzieller Einfügevorgänge mit und ohne Transaktionen (in Millisekunden).

**Lokal zu Azure:**

| Operationen (Operations) | Keine Transaktion (ms) | Transaktion (ms) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure zu Azure (gleiches Datencenter)** :

| Operationen (Operations) | Keine Transaktion (ms) | Transaktion (ms) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Die Ergebnisse sind keine Benchmarks. Weitere Informationen finden Sie im [Hinweis zu den Zeitangaben in den Ergebnissen dieses Artikels](#note-about-timing-results-in-this-article).

Wie Sie den Testergebnissen entnehmen können, verschlechtert sich die Leistung sogar, wenn ein einzelner Vorgang in eine Transaktion eingeschlossen wird. Wenn Sie aber die Anzahl der Vorgänge in einer einzelnen Transaktion erhöhen, ergibt sich eine Verbesserung der Leistung. Der Leistungsunterschied ist außerdem ausgeprägter, wenn alle Vorgänge innerhalb des gleichen Microsoft Azure-Datencenters abgewickelt werden. Die höhere Latenz bei Verwendung von Azure SQL-Datenbank oder Azure SQL Managed Instance außerhalb des Microsoft Azure-Rechenzentrums beeinträchtigt den Leistungszuwachs, der sich durch die Verwendung von Transaktionen erzielen lässt.

Die Verwendung von Transaktionen kann zwar zur Verbesserung der Leistung beitragen, es empfiehlt sich jedoch, sich weiterhin mit den [bewährten Methoden für Transaktionen und Verbindungen](/previous-versions/sql/sql-server-2008-r2/ms187484(v=sql.105))auseinanderzusetzen. Halten Sie die Transaktion so kurz wie möglich, und trennen Sie die Datenbankverbindung nach Abschluss der Arbeit. Die using-Anweisung im vorherigen Beispiel stellt sicher, dass die Verbindung nach Abschluss des nachfolgenden Codeblocks getrennt wird.

Das vorherige Beispiel zeigt, dass Sie jedem ADO.NET-Code mit nur zwei Zeilen eine lokale Transaktion hinzufügen können. Mit Transaktionen können Sie schnell die Leistung von Code für sequenzielle Einfüge-, Aktualisierungs- und Löschvorgänge verbessern. Die bestmögliche Leistung erzielen Sie jedoch, wenn Sie den Code noch weiter anpassen, um von clientseitiger Batchverarbeitung (etwa von Tabellenwertparametern) zu profitieren.

Weitere Informationen zu Transaktionen in ADO.NET finden Sie unter [Lokale Transaktionen in ADO.NET](/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>Tabellenwertparameter

Tabellenwertparameter unterstützen benutzerdefinierte Tabellentypen als Parameter in Transact-SQL-Anweisungen, gespeicherten Prozeduren und Funktionen. Mithilfe dieser clientseitigen Batchverarbeitungstechnik können Sie innerhalb des Tabellenwertparameters mehrere Datenzeilen senden. Zur Verwendung von Tabellenwertparametern müssen Sie zunächst einen Tabellentyp definieren. Die folgende Transact-SQL-Anweisung erstellt einen Tabellentyp namens **MyTableType** :

```sql
    CREATE TYPE MyTableType AS TABLE
    ( mytext TEXT,
      num INT );
```

Erstellen Sie innerhalb des Codes ein Objekt vom Typ **DataTable** mit exakt den gleichen Namen und Typen wie beim Tabellentyp. Übergeben Sie das Objekt vom Typ **DataTable** in einem Parameter in einer Textabfrage oder im Aufruf einer gespeicherten Prozedur. Das folgende Beispiel zeigt diese Technik:

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    DataTable table = new DataTable();
    // Add columns and rows. The following is a simple example.
    table.Columns.Add("mytext", typeof(string));
    table.Columns.Add("num", typeof(int));
    for (var i = 0; i < 10; i++)
    {
        table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
    }

    SqlCommand cmd = new SqlCommand(
        "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
        connection);

    cmd.Parameters.Add(
        new SqlParameter()
        {
            ParameterName = "@TestTvp",
            SqlDbType = SqlDbType.Structured,
            TypeName = "MyTableType",
            Value = table,
        });

    cmd.ExecuteNonQuery();
}
```

Im vorherigen Beispiel fügt das Objekt **SqlCommand** Zeilen aus dem Tabellenwertparameter **\@TestTvp** ein. Das zuvor erstellte Objekt vom Typ **DataTable** wird diesem Parameter mithilfe der Methode **SqlCommand.Parameters.Add** zugewiesen. Die Batchverarbeitung der Einfügevorgänge in einem einzigen Aufruf führt im Vergleich zu sequenziellen Einfügevorgängen zu einer erheblichen Leistungssteigerung.

Verwenden Sie zur weiteren Optimierung des vorherigen Beispiels eine gespeicherte Prozedur anstelle eines textbasierten Befehls. Der folgende Transact-SQL-Befehl erstellt eine gespeicherte Prozedur, die den Tabellenwertparameter **SimpleTestTableType** annimmt:

```sql
CREATE PROCEDURE [dbo].[sp_InsertRows]
@TestTvp as MyTableType READONLY
AS
BEGIN
INSERT INTO MyTable(mytext, num)
SELECT mytext, num FROM @TestTvp
END
GO
```

Ändern Sie anschließend die Objektdeklaration für **SqlCommand** aus dem vorherigen Codebeispiel wie folgt:

```csharp
SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
cmd.CommandType = CommandType.StoredProcedure;
```

In den meisten Fällen erzielen Sie mit Tabellenwertparametern mindestens die gleiche Leistung wie mit anderen Batchverarbeitungstechniken. Aufgrund ihrer Flexibilität sind Tabellenwertparameter häufig anderen Optionen vorzuziehen. Andere Techniken (etwa SQL-Massenkopieren) ermöglichen beispielsweise nur das Einfügen neuer Zeilen. Mit Tabellenwertparametern können Sie dagegen über die in der gespeicherten Prozedur enthaltene Logik bestimmen, welche Zeilen Aktualisierungen und welche Zeilen Einfügungen sind. Der Tabellentyp kann außerdem modifiziert werden, sodass eine Vorgangsspalte Aufschluss darüber gibt, ob die angegebene Zeile eingefügt, aktualisiert oder gelöscht werden soll.

Die folgende Tabelle zeigt Ad-hoc-Testergebnisse für die Verwendung von Tabellenwertparametern (in Millisekunden):

| Operationen (Operations) | Lokal zu Azure (ms) | Azure, gleiches Datencenter (ms) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> Die Ergebnisse sind keine Benchmarks. Weitere Informationen finden Sie im [Hinweis zu den Zeitangaben in den Ergebnissen dieses Artikels](#note-about-timing-results-in-this-article).

Der durch die Batchverarbeitung bedingte Leistungsgewinn ist sofort ersichtlich. Im vorherigen sequenziellen Test dauerten 1000 Vorgänge 129 Sekunden (außerhalb des Datencenters) bzw. 21 Sekunden (innerhalb des Datencenters). Mit Tabellenwertparametern dauern 1000 Vorgänge dagegen nur 2,6 Sekunden (außerhalb des Datencenters) bzw. 0,4 Sekunden (innerhalb des Datencenters).

Weitere Informationen zu Tabellenwertparametern finden Sie unter [Tabellenwertparameter](/sql/relational-databases/tables/use-table-valued-parameters-database-engine).

### <a name="sql-bulk-copy"></a>SQL-Massenkopieren

SQL-Massenkopieren ist eine weitere Möglichkeit, um große Datenmengen in eine Zieldatenbank einzufügen. .NET-Anwendungen können für Masseneinfügevorgänge die Klasse **SqlBulkCopy** verwenden. **SqlBulkCopy** funktioniert ähnlich wie das Befehlszeilentool **Bcp.exe** oder die Transact-SQL-Anweisung **BULK INSERT** . Im folgenden Codebeispiel ist das Massenkopieren der Zeilen aus der Quelltabelle **DataTable** in die Zieltabelle „MyTable“ dargestellt.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
    {
        bulkCopy.DestinationTableName = "MyTable";
        bulkCopy.ColumnMappings.Add("mytext", "mytext");
        bulkCopy.ColumnMappings.Add("num", "num");
        bulkCopy.WriteToServer(table);
    }
}
```

In bestimmten Fällen ist Massenkopieren den Tabellenwertparametern vorzuziehen. Weitere Informationen finden Sie im Artikel [Tabellenwertparameter](/sql/relational-databases/tables/use-table-valued-parameters-database-engine) in der Vergleichstabelle für Tabellenwertparameter und BULK INSERT-Vorgänge.

Die folgenden Ad-hoc-Testergebnisse zeigen die Leistung der Batchverarbeitung mit **SqlBulkCopy** (in Millisekunden):

| Operationen (Operations) | Lokal zu Azure (ms) | Azure, gleiches Datencenter (ms) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> Die Ergebnisse sind keine Benchmarks. Weitere Informationen finden Sie im [Hinweis zu den Zeitangaben in den Ergebnissen dieses Artikels](#note-about-timing-results-in-this-article).

Bei kleineren Batches wurde mit Tabellenwertparametern eine bessere Leistung erzielt als mit der Klasse **SqlBulkCopy** . Bei Tests mit 1.000 und 10.000 Zeilen war **SqlBulkCopy** jedoch um 12–31 % schneller als die Tabellenwertparameter. **SqlBulkCopy** ist (genau wie Tabellenwertparameter) eine gute Option für Batcheinfügevorgänge – insbesondere verglichen mit der Leistung von Vorgängen ohne Batchverarbeitung.

Weitere Informationen zum Massenkopieren in ADO.NET finden Sie unter [Massenkopiervorgänge](/dotnet/framework/data/adonet/sql/bulk-copy-operations-in-sql-server).

### <a name="multiple-row-parameterized-insert-statements"></a>Mehrzeilige parametrisierte INSERT-Anweisungen

Eine Alternative für kleine Batches ist die Erstellung einer großen parametrisierten INSERT-Anweisung, die mehrere Zeilen eingefügt. Diese Technik wird im folgenden Codebeispiel veranschaulicht.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
        "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";

    SqlCommand cmd = new SqlCommand(insertCommand, connection);

    for (int i = 1; i <= 10; i += 2)
    {
        cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
        cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
    }

    cmd.ExecuteNonQuery();
}
```

Dieses Beispiel zeigt das grundlegende Konzept. In einem realistischeren Szenario werden die erforderlichen Entitäten durchlaufen, um gleichzeitig die Abfragezeichenfolge und die Befehlsparameter zu erstellen. Die Anzahl der Abfrageparameter ist auf 2100 begrenzt, was auch die Gesamtzahl der Zeilen begrenzt, die auf diese Weise verarbeitet werden können.

Die folgenden Ad-hoc-Testergebnisse zeigen die Leistung dieser Art von INSERT-Anweisung (in Millisekunden):

| Operationen (Operations) | Tabellenwertparameter (ms) | Einzelne INSERT-Anweisung (ms) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Die Ergebnisse sind keine Benchmarks. Weitere Informationen finden Sie im [Hinweis zu den Zeitangaben in den Ergebnissen dieses Artikels](#note-about-timing-results-in-this-article).

Für Batches mit weniger als 100 Zeilen kann dieser Ansatz etwas schneller sein. Auch wenn die Verbesserung eher überschaubar ausfällt, ist diese Technik dennoch eine weitere Option für Ihr individuelles Anwendungsszenario.

### <a name="dataadapter"></a>DataAdapter

Mithilfe der Klasse **DataAdapter** können Sie ein Objekt vom Typ **DataSet** ändern und die Änderungen anschließend als INSERT-, UPDATE- und DELETE-Vorgang übermitteln. Beachten Sie bei einer solchen Verwendung des Objekts **DataAdapter** , dass für jeden einzelnen Vorgang separate Aufrufe ausgeführt werden. Verwenden Sie zur Verbesserung der Leistung die Eigenschaft **UpdateBatchSize** mit der Anzahl von Vorgängen, für die eine gleichzeitige Batchverarbeitung durchgeführt werden soll. Weitere Informationen finden Sie unter [Ausführen von Batchoperationen mit DataAdapters](/dotnet/framework/data/adonet/performing-batch-operations-using-dataadapters).

### <a name="entity-framework"></a>Entity Framework

[Entity Framework Core](/ef/efcore-and-ef6/#saving-data) unterstützt die Batchverarbeitung.

### <a name="xml"></a>XML

Der Vollständigkeit halber sollten wir auch auf XML als Batchverarbeitungsstrategie eingehen. Allerdings bietet die Verwendung von XML gegenüber anderen Methoden keinerlei Vorteile und sogar einige Nachteile. Der Ansatz ist vergleichbar mit Tabellenwertparametern, eine XML-Datei oder eine Zeichenfolge wird jedoch nicht an eine benutzerdefinierte Tabelle, sondern an eine gespeicherte Prozedur übergeben. Die gespeicherte Prozedur analysiert die Befehle in der gespeicherten Prozedur.

Dieser Ansatz bringt einige Nachteile mit sich:

* Die Verwendung von XML ist unter Umständen umständlich und fehleranfällig.
* Die Analyse des XML-Codes für die Datenbank ist unter Umständen mit einer hohen CPU-Auslastung verbunden.
* In den meisten Fällen ist diese Methode langsamer als die Verwendung von Tabellenwertparametern.

Aus diesen Gründen wird die Verwendung von XML für Batchabfragen nicht empfohlen.

## <a name="batching-considerations"></a>Überlegungen zur Batchverarbeitung

In den folgenden Abschnitten finden Sie weitere Hinweise zur Verwendung der Batchverarbeitung in Azure SQL-Datenbank- und Azure SQL Managed Instance-Anwendungen.

### <a name="tradeoffs"></a>Kompromisse

Je nach Architektur muss bei der Batchverarbeitung unter Umständen zwischen Leistung und Stabilität abgewogen werden. Nehmen wir als Beispiel einen unerwarteten Ausfall Ihrer Rolle: Wenn Sie eine einzelne Zeile mit Daten verlieren, sind die Auswirkungen natürlich geringer als beim Verlust eines großen Batches nicht übermittelter Zeilen. Wenn Sie Zeilen puffern und erst innerhalb eines bestimmten Zeitfensters an die Datenbank senden, besteht ein höheres Risiko.

Überlegen Sie sich aufgrund dieses Umstands, welche Vorgänge Sie zu einem Batch zusammenfassen. Bei weniger wichtigen Daten kann eine offensivere Batchverarbeitung (größere Batches und großzügigere Zeitfenster) gewählt werden.

### <a name="batch-size"></a>Batchgröße

In unseren Tests hatte die Aufspaltung großer Batches in kleinere Blöcke in der Regel keinerlei Vorteile. Tatsächlich wurde durch diese Aufspaltung häufig sogar ein schlechteres Ergebnis erzielt als bei der Übermittlung eines einzelnen großen Batches. Nehmen wir beispielsweise an, Sie möchten 1000 Zeilen einfügen. Die folgende Tabelle zeigt, wie lange es dauert, mithilfe von Tabellenwertparametern 1000 in kleinere Batches aufgeteilte Zeilen einzufügen:

| Batchgröße | Iterationen | Tabellenwertparameter (ms) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Die Ergebnisse sind keine Benchmarks. Weitere Informationen finden Sie im [Hinweis zu den Zeitangaben in den Ergebnissen dieses Artikels](#note-about-timing-results-in-this-article).

Wie Sie sehen, erzielen Sie bei 1000 Zeilen ein optimales Ergebnis, wenn Sie alle auf einmal übermitteln. In anderen (hier nicht gezeigten) Tests wurde bei einem Batch mit 10.000 Zeilen ein geringer Leistungszuwachs erzielt, indem er in zwei Batches mit jeweils 5.000 Zeilen aufgeteilt wurde. Das Tabellenschema für diese Tests ist verhältnismäßig einfach. Führen Sie daher am besten Tests mit Ihren spezifischen Daten und Batchgrößen durch, um diese Ergebnisse zu überprüfen.

Ein weiterer zu beachtender Faktor ist, dass Azure SQL-Datenbank oder Azure SQL Managed Instance möglicherweise mit einer Drosselung reagiert und den Commit für den Batch verweigert, wenn der gesamte Batch zu groß wird. Testen Sie Ihr jeweiliges Szenario, um die ideale Batchgröße zu ermitteln und optimale Ergebnisse zu erzielen. Ermöglichen Sie die Konfiguration der Batchgröße zur Laufzeit, um sie auf der Grundlage von Leistung oder Fehlern schnell anpassen zu können.

Wägen Sie schließlich die Batchgröße gegen die mit der Batchverarbeitung verbundenen Risiken ab. Überlegen Sie, welche Auswirkungen eine Wiederholung des Vorgangs oder der Verlust der Daten im Batch bei vorübergehenden Fehlern oder einem Ausfall der Rolle hat.

### <a name="parallel-processing"></a>Parallelverarbeitung

Was, wenn Sie die Batchgröße verringern, aber mehrere Threads verwenden, um die Vorgänge auszuführen? Auch hier ergaben unsere Tests, dass bei Verwendung mehrerer kleinerer Multithread-Batches in der Regel schlechtere Ergebnisse erzielt werden als mit einem einzelnen größeren Batch. Im folgenden Test wurden 1000 Zeilen in einem bzw. in mehreren parallelen Batches eingefügt. Dieser Test zeigt, dass die gleichzeitige Verwendung mehrerer Batches die Leistung beeinträchtigt.

| Batchgröße [Iterationen] | Zwei Threads (ms) | Vier Threads (ms) | Sechs Threads (ms) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Die Ergebnisse sind keine Benchmarks. Weitere Informationen finden Sie im [Hinweis zu den Zeitangaben in den Ergebnissen dieses Artikels](#note-about-timing-results-in-this-article).

Für die parallelitätsbedingte Beeinträchtigung der Leistung gibt es mehrere mögliche Ursachen:

* Es gibt mehrere gleichzeitige Netzwerkaufrufe (nicht nur einen).
* Mehrere Vorgänge für eine einzelne Tabelle können zu Konflikten und Blockierungen führen.
* Multithreading ist mit einem gewissen Mehraufwand verbunden.
* Die Nachteile des Öffnens mehrerer Verbindungen überwiegen die Vorteile der parallelen Verarbeitung.

Wenn Sie verschiedene Tabellen oder Datenbanken als Ziel verwenden, lassen sich mit dieser Strategie unter Umständen Leistungssteigerungen erzielen. Datenbanksharding oder -verbünde wären etwa ein Szenario für diesen Ansatz. Beim Sharding werden mehrere Datenbanken verwendet und unterschiedliche Daten an die einzelnen Datenbanken weitergeleitet. Wenn jeder kleine Batch an eine andere Datenbank übermittelt wird, kann sich die parallele Ausführung der Vorgänge als effizienter erweisen. Der Leistungsgewinn ist jedoch nicht groß genug, um sich allein auf dieser Grundlage für die Verwendung von Datenbanksharding in Ihrer Lösung zu entscheiden.

Bei einigen Designs kann die parallele Ausführung von kleineren Batches den Anforderungsdurchsatz in einem System unter Last verbessern. In diesem Fall ist die parallele Verarbeitung mehrerer Batches unter Umständen effizienter, auch wenn die Verarbeitung eines einzelnen größeren Batches schneller ist.

Bei Verwendung der parallelen Ausführung empfiehlt es sich, die maximale Anzahl von Arbeitsthreads festzulegen. Eine geringere Anzahl sorgt möglicherweise für weniger Konflikte und eine schnelleren Ausführung. Berücksichtigen Sie auch die dadurch bedingte zusätzliche Last für die Zieldatenbank (sowohl bei Verbindungen als auch bei Transaktionen).

### <a name="related-performance-factors"></a>Verwandte Leistungsfaktoren

Die Hinweise zur Datenbankleistung wirken sich üblicherweise auch auf die Batchverarbeitung aus. So verschlechtert sich etwa bei Tabellen mit einem großen Primärschlüssel oder vielen nicht gruppierten Indizes die Einfügeleistung.

Wenn Tabellenwertparameter eine gespeicherte Prozedur verwenden, können Sie am Anfang der Prozedur den Befehl **SET NOCOUNT ON** verwenden. Mit dieser Anweisung wird die Rückgabe der Anzahl betroffener Zeilen in der Prozedur unterdrückt. In unseren Tests hatte die Verwendung von **SET NOCOUNT ON** allerdings keinerlei Auswirkung oder führte sogar zu Leistungseinbußen. Im Test wurde eine einfache gespeicherte Prozedur mit einem einzelnen Befehl vom Typ **INSERT** aus dem Tabellenwertparameter verwendet. Es mag sein, dass komplexere gespeicherte Prozeduren von dieser Anweisung profitieren. Erwarten Sie jedoch nicht, dass das Hinzufügen von **SET NOCOUNT ON** zu Ihrer gespeicherten Prozedur automatisch zu einer Leistungsverbesserung führt. Testen Sie Ihre gespeicherte Prozedur mit und ohne die Anweisung **SET NOCOUNT ON** , um die Wirkung zu ermitteln.

## <a name="batching-scenarios"></a>Batchverarbeitungsszenarien

In den folgenden Abschnitten wird das Verwenden von Tabellenwertparametern in drei Anwendungsszenarien beschrieben. Das erste Szenario zeigt, wie Pufferung und Batchverarbeitung kombiniert werden können. Das zweite Szenario verbessert die Leistung durch Ausführen von Master/Detail-Vorgängen in einem einzelnen gespeicherten Prozeduraufruf. Das letzte Szenario veranschaulicht die Verwendung von Tabellenwertparametern in einem UPSERT-Vorgang.

### <a name="buffering"></a>Pufferung

Einige Szenarien bieten sich zwar ganz offensichtlich für die Batchverarbeitung an, es gibt aber auch Szenarien, die von einer verzögerten Verarbeitung durch die Batchverarbeitung profitieren können. Im Falle eines unerwarteten Fehlers birgt eine verzögerte Verarbeitung allerdings ein höheres Datenverlustrisiko. Über dieses Risiko müssen Sie sich im Klaren sein und die Konsequenzen abwägen.

Nehmen wir zum Beispiel eine Anwendung, die den Navigationsverlauf der einzelnen Benutzer nachverfolgt. Die Anwendung könnte nun bei jeder Seitenanforderung die Datenbank aufrufen und den Seitenaufruf des Benutzers dokumentieren. Eine bessere Leistung und Skalierbarkeit wird jedoch erzielt, wenn die Navigationsaktivitäten des Benutzers gepuffert und dann in Batches an die Datenbank übermittelt werden. Die Aktualisierung der Datenbank kann auf der Grundlage der verstrichenen Zeit und/oder der Puffergröße ausgelöst werden. So können Sie beispielsweise mithilfe einer Regel festlegen, dass der Batch nach 20 Sekunden oder bei Erreichen eines Pufferinhalts von 1000 Elementen verarbeitet werden soll.

Im folgenden Codebeispiel werden gepufferte Ereignisse, die von einer Überwachungsklasse ausgelöst wurden, mithilfe von [Reactive Extensions - Rx](/previous-versions/dotnet/reactive-extensions/hh242985(v=vs.103)) verarbeitet. Wenn der Puffer voll ist oder ein Timeout erreicht wurde, wird der Batch mit den Benutzerdaten unter Verwendung eines Tabellenwertparameters an die Datenbank gesendet.

Die folgende NavHistoryData-Klasse modelliert die Benutzernavigationsdetails. Sie enthält grundlegende Informationen wie Benutzer-ID, aufgerufene URL und Zugriffszeit.

```csharp
public class NavHistoryData
{
    public NavHistoryData(int userId, string url, DateTime accessTime)
    { UserId = userId; URL = url; AccessTime = accessTime; }
    public int UserId { get; set; }
    public string URL { get; set; }
    public DateTime AccessTime { get; set; }
}
```

Die NavHistoryDataMonitor-Klasse ist zuständig für die Pufferung der Benutzernavigationsdaten in der Datenbank. Die enthaltene RecordUserNavigationEntry-Methode dient zum Auslösen eines Ereignisses vom Typ **OnAdded** . Der folgende Code zeigt die Konstruktorlogik, die mithilfe von „Rx“ ein auf dem Ereignis basierendes ObservableCollection-Objekt erstellt. Anschließend wird das ObservableCollection-Objekt mit der Buffer-Methode abonniert. Die Überladung gibt an, dass der Puffer alle 20 Sekunden oder jeweils bei Erreichen von 1.000 Einträgen gesendet werden soll.

```csharp
public NavHistoryDataMonitor()
{
    var observableData =
        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
}
```

Der Handler konvertiert alle gepufferten Elemente in einen Tabellenwerttyp und übergibt diesen dann an eine gespeicherte Prozedur, die den Batch verarbeitet. Der folgende Code zeigt die vollständige Definition für die NavHistoryDataEventArgs- und die NavHistoryDataMonitor-Klasse:

```csharp
public class NavHistoryDataEventArgs : System.EventArgs
{
    public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
    public NavHistoryData Data { get; set; }
}

public class NavHistoryDataMonitor
{
    public event EventHandler<NavHistoryDataEventArgs> OnAdded;

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
    }
```

Der Handler konvertiert alle gepufferten Elemente in einen Tabellenwerttyp und übergibt diesen dann an eine gespeicherte Prozedur, die den Batch verarbeitet. Der folgende Code zeigt die vollständige Definition für die NavHistoryDataEventArgs- und die NavHistoryDataMonitor-Klasse:

```csharp
    public class NavHistoryDataEventArgs : System.EventArgs
    {
        if (OnAdded != null)
            OnAdded(this, new NavHistoryDataEventArgs(data));
    }

    protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
    {
        DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
        navHistoryBatch.Columns.Add("UserId", typeof(int));
        navHistoryBatch.Columns.Add("URL", typeof(string));
        navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
        foreach (EventPattern<NavHistoryDataEventArgs> item in items)
        {
            NavHistoryData data = item.EventArgs.Data;
            navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
        }

        using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
        {
            connection.Open();

            SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
            cmd.CommandType = CommandType.StoredProcedure;

            cmd.Parameters.Add(
                new SqlParameter()
                {
                    ParameterName = "@NavHistoryBatch",
                    SqlDbType = SqlDbType.Structured,
                    TypeName = "NavigationHistoryTableType",
                    Value = navHistoryBatch,
                });

            cmd.ExecuteNonQuery();
        }
    }
}
```

Zur Verwendung dieser Pufferungsklasse erstellt die Anwendung ein statisches NavHistoryDataMonitor-Objekt. Bei jedem Seitenaufruf eines Benutzers ruft die Anwendung die NavHistoryDataMonitor.RecordUserNavigationEntry-Methode auf. Die Pufferungslogik sendet die Einträge in Batches an die Datenbank.

### <a name="master-detail"></a>Master/Detail

Tabellenwertparameter eignen sich für einfache INSERT-Szenarien. Bei Einfügevorgängen mit mehreren Tabellen kann sich die Batchverarbeitung jedoch als etwas schwieriger erweisen. Ein gutes Beispiel ist das Master/Detail-Szenario. Die Mastertabelle gibt die primäre Entität an. In mindestens einer Detailtabelle werden Daten zur Entität gespeichert. Im vorliegenden Szenario erzwingen Fremdschlüsselbeziehungen die Beziehung zwischen Details und einer eindeutigen Masterentität. Stellen Sie sich eine vereinfachte Version einer PurchaseOrder-Tabelle und die dazugehörige OrderDetail-Tabelle vor. Die folgende Transact-SQL-Anweisung erstellt die PurchaseOrder-Tabelle mit vier Spalten: „OrderID“, „OrderDate“, „CustomerID“ und „Status“.

```sql
CREATE TABLE [dbo].[PurchaseOrder](
[OrderID] [int] IDENTITY(1,1) NOT NULL,
[OrderDate] [datetime] NOT NULL,
[CustomerID] [int] NOT NULL,
[Status] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_PurchaseOrder]
PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

Jeder Auftrag enthält mindestens einen Produktkauf. Die entsprechenden Informationen werden in der PurchaseOrderDetail-Tabelle erfasst. Die folgende Transact-SQL-Anweisung erstellt die PurchaseOrderDetail-Tabelle mit fünf Spalten: „OrderID“, „OrderDetailID“, „ProductID“, „UnitPrice“ und „OrderQty“.

```sql
CREATE TABLE [dbo].[PurchaseOrderDetail](
[OrderID] [int] NOT NULL,
[OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
[ProductID] [int] NOT NULL,
[UnitPrice] [money] NULL,
[OrderQty] [smallint] NULL,
CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED
( [OrderID] ASC, [OrderDetailID] ASC ))
```

Die OrderID-Spalte in der PurchaseOrderDetail-Tabelle muss auf einen Auftrag aus der PurchaseOrder-Tabelle verweisen. Diese Einschränkung wird durch die folgende Definition eines Fremdschlüssels erzwungen:

```sql
ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD
CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

Zur Verwendung von Tabellenwertparametern muss für jede Zieltabelle ein benutzerdefinierter Tabellentyp vorhanden sein:

```sql
CREATE TYPE PurchaseOrderTableType AS TABLE
( OrderID INT,
    OrderDate DATETIME,
    CustomerID INT,
    Status NVARCHAR(50) );
GO

CREATE TYPE PurchaseOrderDetailTableType AS TABLE
( OrderID INT,
    ProductID INT,
    UnitPrice MONEY,
    OrderQty SMALLINT );
GO
```

Definieren Sie anschließend eine gespeicherte Prozedur, die Tabellen dieser Art akzeptiert. Mit der Prozedur kann eine Anwendung einen Satz von Aufträgen und Auftragsdetails lokal in einem einzigen Aufruf als Batch verarbeiten. Der folgende Transact-SQL-Code stellt die vollständige Deklaration der gespeicherten Prozedur für dieses Beispiel bereit:

```sql
CREATE PROCEDURE sp_InsertOrdersBatch (
@orders as PurchaseOrderTableType READONLY,
@details as PurchaseOrderDetailTableType READONLY )
AS
SET NOCOUNT ON;

-- Table that connects the order identifiers in the @orders
-- table with the actual order identifiers in the PurchaseOrder table
DECLARE @IdentityLink AS TABLE (
SubmittedKey int,
ActualKey int,
RowNumber int identity(1,1)
);

-- Add new orders to the PurchaseOrder table, storing the actual
-- order identifiers in the @IdentityLink table
INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;

-- Match the passed-in order identifiers with the actual identifiers
-- and complete the @IdentityLink table for use with inserting the details
WITH OrderedRows As (
SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber
FROM @orders
)
UPDATE @IdentityLink SET SubmittedKey = M.OrderID
FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;

-- Insert the order details into the PurchaseOrderDetail table,
-- using the actual order identifiers of the master table, PurchaseOrder
INSERT INTO PurchaseOrderDetail (
[OrderID],
[ProductID],
[UnitPrice],
[OrderQty] )
SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
FROM @details D
JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
GO
```

In diesem Beispiel werden die tatsächlichen OrderID-Werte aus den neu eingefügten Zeilen in der lokal definierten @IdentityLink-Tabelle gespeichert. Diese Auftrags-IDs unterscheiden sich von den temporären OrderID-Werten in den Tabellenwertparametern @orders und @details. Aus diesem Grund verbindet die @IdentityLink-Tabelle anschließend die OrderID-Werte aus dem @orders-Parameter mit den tatsächlichen OrderID-Werten für die neuen Zeilen in der PurchaseOrder-Tabelle. Nach diesem Schritt kann die @IdentityLink-Tabelle das Einfügen der Auftragsdetails mit der tatsächlichen Auftrags-ID vereinfachen, die die Fremdschlüsseleinschränkung erfüllt.

Diese gespeicherte Prozedur kann vom Code oder von anderen Transact-SQL-Aufrufen verwendet werden. Ein entsprechendes Codebeispiel finden Sie in diesem Artikel im Abschnitt „Tabellenwertparameter“. Der folgende Transact-SQL-Code zeigt, wie „Sp_InsertOrdersBatch“ aufgerufen wird:

```sql
declare @orders as PurchaseOrderTableType
declare @details as PurchaseOrderDetailTableType

INSERT @orders
([OrderID], [OrderDate], [CustomerID], [Status])
VALUES(1, '1/1/2013', 1125, 'Complete'),
(2, '1/13/2013', 348, 'Processing'),
(3, '1/12/2013', 2504, 'Shipped')

INSERT @details
([OrderID], [ProductID], [UnitPrice], [OrderQty])
VALUES(1, 10, $11.50, 1),
(1, 12, $1.58, 1),
(2, 23, $2.57, 2),
(3, 4, $10.00, 1)

exec sp_InsertOrdersBatch @orders, @details
```

Bei dieser Lösung kann jeder Batch eine Gruppe von OrderID-Werten verwenden, die bei 1 beginnen. Diese temporären OrderID-Werte beschreiben die Beziehungen im Batch. Die tatsächlichen OrderID-Werte werden zum Zeitpunkt des Einfügevorgangs bestimmt. Sie können die Anweisungen aus dem vorherigen Beispiel wiederholt ausführen und eindeutige Aufträge in der Datenbank generieren. Fügen Sie daher ggf. weiteren Code oder weitere Datenbanklogik hinzu, um die Generierung doppelter Aufträge zu verhindern, wenn Sie diese Batchverarbeitungstechnik verwenden.

Dieses Beispiel zeigt, dass mithilfe von Tabellenwertparametern auch komplexere Datenbankvorgänge (wie etwa Master/Detail-Vorgänge) als Batch verarbeitet werden können.

### <a name="upsert"></a>UPSERT

In einem anderen Batchverarbeitungsszenario werden gleichzeitig vorhandene Zeilen aktualisiert und neue Zeilen eingefügt. Dieser Vorgang wird auch als „UPSERT“ („Update + Insert“; „Aktualisieren und Einfügen“) bezeichnet. Dabei werden aber nicht etwa INSERT und UPDATE separat aufgerufen. Stattdessen kommt die MERGE-Anweisung zum Einsatz. Die MERGE-Anweisung kann in einem einzelnen Aufruf sowohl Einfüge- als auch Aktualisierungsvorgänge ausführen.

Tabellenwertparameter können mit der MERGE-Anweisung verwendet werden, um Aktualisierungen und Einfügevorgänge durchzuführen. Nehmen wir beispielsweise eine vereinfachte Mitarbeitertabelle mit den Spalten „EmployeeID“, „FirstName“, „LastName“ und „SocialSecurityNumber“:

```sql
CREATE TABLE [dbo].[Employee](
[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[SocialSecurityNumber] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED
([EmployeeID] ASC ))
```

In diesem Beispiel können Sie den Umstand, dass es sich bei „SocialSecurityNumber“ um einen eindeutigen Wert handelt, zum Ausführen eines MERGE-Vorgangs für mehrere Mitarbeiter nutzen. Erstellen Sie zunächst den benutzerdefinierten Tabellentyp:

```sql
CREATE TYPE EmployeeTableType AS TABLE
( Employee_ID INT,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    SocialSecurityNumber NVARCHAR(50) );
GO
```

Erstellen Sie als Nächstes eine gespeicherte Prozedur, die mithilfe der MERGE-Anweisung die Aktualisierung und Einfügung vornimmt, oder schreiben Sie entsprechenden Code. Im folgenden Beispiel wird die MERGE-Anweisung für den Tabellenwertparameter @employees vom Typ „EmployeeTableType“ verwendet. Der Inhalt der @employees-Tabelle wird hier nicht angezeigt.

```sql
MERGE Employee AS target
USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees)
AS source ([FirstName], [LastName], [SocialSecurityNumber])
ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
WHEN MATCHED THEN
UPDATE SET
target.FirstName = source.FirstName,
target.LastName = source.LastName
WHEN NOT MATCHED THEN
    INSERT ([FirstName], [LastName], [SocialSecurityNumber])
    VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);
```

Weitere Informationen finden Sie in der Dokumentation und in den Beispielen für die MERGE-Anweisung. Die gleiche Aufgabe könnte zwar auch durch Aufrufen einer mehrstufigen gespeicherten Prozedur mit separaten INSERT- und UPDATE-Vorgängen durchgeführt werden, die MERGE-Anweisung ist jedoch effizienter. Datenbankcode kann auch Transact-SQL-Aufrufe erstellen, die die MERGE-Anweisung direkt (also ohne zwei Datenbankaufrufe für INSERT und UPDATE) verwenden.

## <a name="recommendation-summary"></a>Zusammenfassung

Die folgende Liste enthält eine Zusammenfassung der in diesem Artikel behandelten Empfehlungen für die Batchverarbeitung:

* Verwenden Sie Pufferung und Batchverarbeitung, um die Leistung und Skalierbarkeit von Azure SQL-Datenbank- und Azure SQL Managed Instance-Anwendungen zu steigern.
* Machen Sie sich mit den Kompromissen zwischen Batchverarbeitung/Pufferung und Stabilität vertraut. Bei einem Rollenausfall überwiegen unter Umständen die Nachteile, die sich durch den Verlust eines nicht verarbeiteten Batchs mit unternehmenskritischen Daten ergeben, die durch die Batchverarbeitung bedingten Leistungsvorteile.
* Wickeln Sie nach Möglichkeit alle an die Datenbank gerichteten Aufrufe innerhalb des gleichen Datencenters ab, um die Latenz zu verringern.
* Wenn Sie sich für eine einzelne Batchverarbeitungstechnik entscheiden: Tabellenwertparameter bieten die beste Leistung und Flexibilität.
* Halten Sie sich an die folgenden allgemeinen Richtlinien, um die bestmögliche Leistung bei Einfügevorgängen zu erzielen (vergessen Sie aber nicht, Ihr Szenario zu testen):
  * Weniger als 100 Zeilen: Verwenden Sie einen einzelnen parametrisierten INSERT-Befehl.
  * Weniger als 1000 Zeilen: Verwenden Sie Tabellenwertparameter.
  * Ab 1000 Zeilen: Verwenden Sie „SqlBulkCopy“.
* Verwenden Sie für Aktualisierungs- und Löschvorgänge Tabellenwertparameter mit gespeicherter Prozedurlogik, die für die einzelnen Zeilen im Tabellenparameter jeweils den korrekten Vorgang bestimmt.
* Richtlinien für die Batchgröße:
  * Verwenden Sie möglichst umfangreiche Batches mit einer Größe, die für Ihre individuelle Anwendung und Ihre geschäftlichen Anforderungen sinnvoll ist.
  * Sorgen Sie für ein ausgeglichenes Verhältnis zwischen dem durch große Batches erzielten Leistungsgewinn und den Risiken, die mit temporären oder schwer wiegenden Fehlern einhergehen. Was ist die Folge von Wiederholungsversuchen oder dem Verlust der im Batch enthaltenen Daten?
  * Testen Sie die größte Batchgröße, um sicherzustellen, dass der Batch von Azure SQL-Datenbank oder Azure SQL Managed Instance nicht abgelehnt wird.
  * Erstellen Sie Konfigurationseinstellungen, um bestimmte Aspekte der Batchverarbeitung (etwa die Batchgröße oder das Pufferungszeitfenster) steuern zu können. Diese Einstellungen sorgen für Flexibilität: Mit ihnen können sie das Batchverarbeitungsverhalten in der Produktionsumgebung ohne erneute Bereitstellung des Clouddiensts ändern.
* Vermeiden Sie die parallele Ausführung von Batches für eine einzelne Tabelle in einer einzelnen Datenbank. Sollten Sie sich doch dafür entscheiden, einen einzelnen Batch auf mehrere Arbeitsthreads aufzuteilen, ermitteln Sie mithilfe von Tests die ideale Threadanzahl. Nach Überschreitung eines nicht näher spezifizierten Schwellenwerts wird die Leistung durch die Erhöhung der Threadanzahl nicht mehr verbessert, sondern sogar beeinträchtigt.
* Erwägen Sie für weitere Szenarien eine größen- und zeitabhängige Pufferung als Implementierungsoption für die Batchverarbeitung.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel ging es in erster Linie darum, wie Datenbankdesign und Programmiertechniken im Zusammenhang mit der Batchverarbeitung die Leistung und Skalierbarkeit Ihrer Anwendung verbessern können. Dies ist jedoch nur einer von vielen Faktoren einer Gesamtstrategie. Weitere Methoden zur Verbesserung der Leistung und Skalierbarkeit finden Sie unter [Leitfaden zur Anwendungs- und Datenbankoptimierung](database/performance-guidance.md) und [Überlegungen zum Preis und zur Leistung eines Pools für elastische Datenbanken](database/elastic-pool-overview.md).