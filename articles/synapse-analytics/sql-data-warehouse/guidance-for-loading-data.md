---
title: Bewährte Methoden zum Laden von Daten für dedizierte SQL-Pools
description: Empfehlungen und Leistungsoptimierungen für das Laden von Daten mithilfe von dedizierten SQL-Pools in Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 39625914f179dfc8d5511b9a3d386cc8332b7efa
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96456303"
---
# <a name="best-practices-for-loading-data-using-dedicated-sql-pools-in-azure-synapse-analytics"></a>Bewährte Methoden für das Laden von Daten mithilfe von dedizierten SQL-Pools in Azure Synapse Analytics

Dieser Artikel enthält Empfehlungen und Leistungsoptimierungen für das Laden von Daten mithilfe eines dedizierten SQL-Pools.

## <a name="preparing-data-in-azure-storage"></a>Vorbereiten von Daten in Azure Storage

Zur Minimierung der Latenz sollten sich Ihre Speicherebene und Ihr dedizierter SQL-Pool an demselben Ort befinden.

Beim Exportieren von Daten in ein ORC-Dateiformat erhalten Sie unter Umständen Java-Fehler vom Typ „Nicht genügend Speicherplatz“, falls umfangreiche Textspalten vorhanden sind. Diese Einschränkung können Sie umgehen, indem Sie nur eine Teilmenge der Spalten exportieren.

Alle Dateiformate weisen unterschiedliche Leistungsmerkmale auf. Am schnellsten können Sie komprimierte, durch Trennzeichen getrennte Textdateien laden. Der Unterschied zwischen UTF-8 und UTF-16 wirkt sich nur minimal auf die Leistung aus.

Teilen Sie große komprimierte Dateien in kleinere komprimierte Dateien.

## <a name="running-loads-with-enough-compute"></a>Ausführen von Ladevorgängen mit ausreichender Compute-Kapazität

Führen Sie immer nur einen einzelnen Ladeauftrag aus, um die bestmögliche Ladegeschwindigkeit zu erzielen. Sollte das nicht möglich sein, führen Sie eine möglichst geringe Anzahl von Ladevorgängen gleichzeitig aus. Wenn Sie einen umfangreichen Ladeauftrag erwarten, empfiehlt es sich unter Umständen, Ihren dedizierten SQL-Pool vor dem Laden zentral hochzuskalieren.

Erstellen Sie „Ladebenutzer“, die für das Ausführen von Ladevorgängen vorgesehen sind, um Ladevorgänge mit den geeigneten Computeressourcen durchzuführen. Klassifizieren Sie jeden Ladebenutzer mit einer bestimmten Arbeitsauslastungsgruppe. Melden Sie sich zum Ausführen eines Ladevorgangs als ein Ladebenutzer an, und führen Sie den Ladevorgang aus. Der Ladevorgang wird mit der Arbeitsauslastungsgruppe des Benutzers ausgeführt.  

### <a name="example-of-creating-a-loading-user"></a>Beispiel für die Erstellung eines Ladebenutzers

In diesem Beispiel wird ein für eine bestimmte Arbeitsauslastungsgruppe klassifizierter Ladebenutzer erstellt. Der erste Schritt umfasst das **Herstellen einer Verbindung mit dem Master** und das Erstellen einer Anmeldung.

```sql
   -- Connect to master
   CREATE LOGIN loader WITH PASSWORD = 'a123STRONGpassword!';
```

Stellen Sie eine Verbindung mit dem dedizierten SQL-Pool her, und erstellen Sie einen Benutzer. Beim folgenden Code wird vorausgesetzt, dass Sie über eine Verbindung mit der Datenbank „mySampleDataWarehouse“ verfügen. Es wird gezeigt, wie ein Benutzer namens „loader“ erstellt wird, und er erhält Berechtigungen zum Erstellen von Tabellen und Laden mithilfe der [COPY-Anweisung](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Anschließend wird der Benutzer für die Arbeitsauslastungsgruppe DataLoads mit maximalen Ressourcen klassifiziert. 

```sql
   -- Connect to the dedicated SQL pool
   CREATE USER loader FOR LOGIN loader;
   GRANT ADMINISTER DATABASE BULK OPERATIONS TO loader;
   GRANT INSERT ON <yourtablename> TO loader;
   GRANT SELECT ON <yourtablename> TO loader;
   GRANT CREATE TABLE TO loader;
   GRANT ALTER ON SCHEMA::dbo TO loader;
   
   CREATE WORKLOAD GROUP DataLoads
   WITH ( 
      MIN_PERCENTAGE_RESOURCE = 100
       ,CAP_PERCENTAGE_RESOURCE = 100
       ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 100
    );

   CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
   WITH (
         WORKLOAD_GROUP = 'DataLoads'
       ,MEMBERNAME = 'loader'
   );
```

Um einen Ladevorgang mit Ressourcen für die Arbeitsauslastungsgruppe auszuführen, melden Sie sich als „loader“ an, und führen Sie den Ladevorgang aus.

## <a name="allowing-multiple-users-to-load-polybase"></a>Ermöglichen von Ladevorgängen für mehrere Benutzer (PolyBase)

Oftmals müssen mehrere Benutzer in der Lage sein, Daten in einen dedizierten SQL-Pool zu laden. Das Laden mit [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (PolyBase) setzt CONTROL-Berechtigungen der Datenbank voraus.  Die CONTROL-Berechtigung erteilt Steuerungszugriff auf alle Schemas.

Unter Umständen sollen aber nicht alle Benutzer, die Daten laden, über Steuerungszugriff auf alle Schemas verfügen. Berechtigungen können mithilfe der DENY CONTROL-Anweisung eingeschränkt werden.

Beispiel: Wenn Sie über die Datenbankschemas Schema_A für Abteilung A und Schema_B für Abteilung B verfügen, sollten Sie beachten, dass die Datenbankbenutzer Benutzer_A und Benutzer_B Benutzer von PolyBase sind und in Abteilung A bzw. B geladen werden. Beiden wurden CONTROL-Datenbankberechtigungen erteilt. Die Ersteller von Schema A und B sperren ihre Schemas jetzt mit der DENY-Anweisung:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

Benutzer_A und Benutzer_B sind für das Schema der anderen Abteilung jetzt gesperrt.

## <a name="loading-to-a-staging-table"></a>Laden in eine Stagingtabelle

Laden Sie Daten in eine Stagingtabelle, um beim Verschieben von Daten in eine Tabelle des dedizierten SQL-Pools die bestmögliche Ladegeschwindigkeit zu erzielen.  Definieren Sie die Stagingtabelle als Heap, und verwenden Sie Roundrobin als Verteilungsoption.

Beachten Sie, dass das Laden normalerweise ein zweistufiger Prozess ist, bei dem die Daten zunächst in eine Stagingtabelle geladen und anschließend in eine Produktionstabelle des dedizierten SQL-Pools eingefügt werden. Wenn die Produktionstabelle eine Hashverteilung verwendet, ist das Laden und Einfügen unter Umständen insgesamt schneller, wenn Sie die Stagingtabelle mit der Hashverteilung definieren.

Das Laden in die Stagingtabelle dauert zwar länger, der zweite Schritt (also das Einfügen der Zeilen in die Produktionstabelle) kommt jedoch ohne verteilungsübergreifende Datenverschiebung aus.

## <a name="loading-to-a-columnstore-index"></a>Laden in einen Columnstore-Index

Columnstore-Indizes erfordern große Mengen an Arbeitsspeicher, um Daten in Zeilengruppen mit hoher Qualität zu komprimieren. Um in Bezug auf die Komprimierung und den Index die höchste Effizienz zu erzielen, muss der Columnstore-Index in jeder Zeilengruppe die maximale Anzahl von Zeilen (1.048.576) komprimieren.

Wenn der Arbeitsspeicher knapp ist, können für den Columnstore-Index unter Umständen nicht die maximalen Komprimierungsraten erzielt werden. Dieses Szenario wirkt sich wiederum auf die Abfrageleistung aus. Ausführliche Informationen hierzu finden Sie unter [Maximieren der Zeilengruppenqualität für Columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Verwenden Sie Ladebenutzer, die Mitglieder einer mittelgroßen oder großen Ressourcenklasse sind, um sicherzustellen, dass der Ladebenutzer über genügend Arbeitsspeicher zur Erreichung der maximalen Komprimierungsraten verfügt.
- Laden Sie eine ausreichende Zahl von Zeilen, um neue Zeilengruppen vollständig zu füllen. Während eines Massenladevorgangs werden alle 1.048.576 Zeilen im Columnstore direkt als vollständige Zeilengruppe komprimiert. Ladevorgänge mit weniger als 102.400 Zeilen senden die Zeilen an den Deltaspeicher, in dem Zeilen in einem B-Strukturindex gespeichert werden.

> [!NOTE]
> Wenn Sie eine zu kleine Zahl von Zeilen laden, werden diese ggf. allesamt an den Deltastore weitergeleitet und nicht sofort in das Columnstore-Format komprimiert.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Vergrößern von Batches bei Verwendung der SqLBulkCopy-API oder von bcp

Beim Laden mit der COPY-Anweisung wird der höchste Durchsatz für dedizierte SQL-Pools erzielt. Falls Sie zum Laden nicht die COPY-Anweisung verwenden können, sondern die [SqLBulkCopy-API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) oder [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) nutzen müssen, sollten Sie für einen höheren Durchsatz den Batch vergrößern.

> [!TIP]
> Eine Batchgröße zwischen 100.000 und einer Million Zeilen ist der empfohlene Ausgangswert zum Ermitteln der optimalen Batchkapazität.

## <a name="handling-loading-failures"></a>Behandeln von Ladefehlern

Das Laden von Daten mithilfe einer externen Tabelle kann folgenden Fehler auslösen: *Abfrage abgebrochen – der maximale Ablehnungsgrenzwert wurde beim Lesen aus einer externen Quelle erreicht*. Diese Meldung weist darauf hin, dass die externen Daten fehlerhafte Datensätze enthalten.

Ein Datensatz wird als fehlerhaft angesehen, wenn eine der folgenden Bedingungen zutrifft:

- Die Datentypen und die Anzahl der Spalten stimmen nicht mit den Spaltendefinitionen der externen Tabelle überein.
- Die Daten entsprechen nicht dem angegebenen externen Dateiformat.

Vergewissern Sie sich zur Behebung dieses Problems, dass die Formatdefinitionen der externen Tabelle und Datei korrekt sind und Ihre externen Daten diesen Definitionen entsprechen.

Wenn eine Teilmenge der externen Datensätze fehlerhaft ist, können Sie diese Datensätze für Ihre Abfragen mit den Ablehnungsoptionen in [CREATE EXTERNAL TABLE (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) ablehnen.

## <a name="inserting-data-into-a-production-table"></a>Einfügen von Daten in eine Produktionstabelle

Bei einem einmaligen Ladevorgang für eine kleine Tabelle mit einer [INSERT-Anweisung](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) oder beim periodischen erneuten Laden eines Suchvorgangs wird unter Umständen eine ausreichende Leistung für Ihre Zwecke erzielt, wenn Sie eine Anweisung wie `INSERT INTO MyLookup VALUES (1, 'Type 1')` verwenden.  Singleton-Einfügungen sind jedoch nicht so effizient wie ein Massenladevorgang.

Wenn Sie im Laufe eines Tages über Tausende oder mehr einzelne Einfügungen verfügen, sollten Sie sie zu einem Batch zusammenfassen, um das Massenladen zu ermöglichen.  Entwickeln Sie Ihre Prozesse so, dass die einzelnen Einfügungen an eine Datei angefügt werden, und erstellen Sie dann einen weiteren Prozess, mit dem die Datei regelmäßig geladen wird.

## <a name="creating-statistics-after-the-load"></a>Erstellen von Statistiken nach dem Laden

Zur Verbesserung der Abfrageleistung ist es wichtig, nach dem ersten Laden oder nach Datenänderungen Statistiken für alle Spalten sämtlicher Tabellen zu erstellen. Das Erstellen von Statistiken kann manuell ausgeführt werden, oder Sie können [AUTO_CREATE_STATISTICS](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic) aktivieren.

Eine ausführliche Erläuterung von Statistiken finden Sie unter [Statistiken](sql-data-warehouse-tables-statistics.md). Im folgenden Beispiel wird gezeigt, wie Statistiken für fünf Spalten der Tabelle „Customer_Speed“ manuell erstellt werden.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys-polybase"></a>Durchführen einer Rotation von Speicherschlüsseln (PolyBase)

Eine bewährte Sicherheitsmethode besteht darin, den Zugriffsschlüssel für Ihren Blobspeicher regelmäßig zu ändern. Sie verfügen über zwei Speicherschlüssel für Ihr Blobspeicherkonto und haben somit die Möglichkeit, einen Schlüsselübergang durchzuführen.

Gehen Sie wie folgt vor, um Schlüssel für Azure Storage-Konten zu rotieren:

Führen Sie für jedes Speicherkonto, dessen Schlüssel sich geändert hat, [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) aus.

Beispiel:

Originalschlüssel wird erstellt

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

Für den Schlüssel wird von Schlüssel 1 zu Schlüssel 2 rotiert

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

Es sind keine weiteren Änderungen an zugrunde liegenden externen Datenquellen erforderlich.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur COPY-Anweisung oder zu PolyBase beim Entwerfen eines ELT-Prozesses (Extrahieren, Laden und Transformieren) finden Sie unter [Datenladestrategien für Synapse SQL-Pools](design-elt-data-loading.md).
- Ein Tutorial zum Ladevorgang finden Sie unter [Tutorial: Laden des New York Taxicab-Datasets](load-data-from-azure-blob-storage-using-polybase.md).
- Informationen zum Überwachen von Datenladevorgängen finden Sie unter [Überwachen Ihrer Workload mit dynamischen Verwaltungssichten](sql-data-warehouse-manage-monitor.md).
