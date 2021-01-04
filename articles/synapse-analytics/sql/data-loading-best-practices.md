---
title: Bewährte Methoden für das Laden von Daten
description: Empfehlungen und Leistungsoptimierungen für das Laden von Daten in einen dedizierten SQL-Pool in Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 7e706f12a251cd38c3525a48553743606ed199b6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024026"
---
# <a name="best-practices-for-loading-data-into-a-dedicated-sql-pool-azure-synapse-analytics"></a>Bewährte Methoden für das Laden von Daten in einen dedizierten SQL-Pool in Azure Synapse Analytics

Dieser Artikel finden Sie Empfehlungen und Leistungsoptimierungen für das Laden von Daten.

## <a name="prepare-data-in-azure-storage"></a>Vorbereiten von Daten in Azure Storage

Zur Minimierung der Latenz sollten sich Ihre Speicherebene und Ihr dedizierter SQL-Pool an demselben Ort befinden.

Beim Exportieren von Daten in ein ORC-Dateiformat erhalten Sie unter Umständen Java-Fehler vom Typ „Nicht genügend Speicherplatz“, falls umfangreiche Textspalten vorhanden sind. Diese Einschränkung können Sie umgehen, indem Sie nur eine Teilmenge der Spalten exportieren.

Mit PolyBase können keine Zeilen geladen werden, die Daten mit mehr als einer Million Bytes enthalten. Wenn Sie Daten in Textdateien in Azure Blob Storage oder Azure Data Lake Store platzieren, müssen diese jeweils Daten mit weniger als einer Million Bytes enthalten. Dieses Bytelimit gilt unabhängig vom Tabellenschema.

Alle Dateiformate weisen unterschiedliche Leistungsmerkmale auf. Am schnellsten können Sie komprimierte, durch Trennzeichen getrennte Textdateien laden. Der Unterschied zwischen UTF-8 und UTF-16 wirkt sich nur minimal auf die Leistung aus.

Teilen Sie große komprimierte Dateien in kleinere komprimierte Dateien.

## <a name="run-loads-with-enough-compute"></a>Ausführen von Ladevorgängen mit ausreichender Computekapazität

Führen Sie immer nur einen einzelnen Ladeauftrag aus, um die bestmögliche Ladegeschwindigkeit zu erzielen. Sollte das nicht möglich sein, führen Sie eine möglichst geringe Anzahl von Ladevorgängen gleichzeitig aus. Wenn Sie einen umfangreichen Ladeauftrag erwarten, empfiehlt es sich unter Umständen, Ihren dedizierten SQL-Pool vor dem Laden zentral hochzuskalieren.

Erstellen Sie „Ladebenutzer“, die für das Ausführen von Ladevorgängen vorgesehen sind, um Ladevorgänge mit den geeigneten Computeressourcen durchzuführen. Weisen Sie jedem Ladebenutzer eine bestimmte Ressourcenklasse oder Arbeitsauslastungsgruppe zu. Melden Sie sich zum Ausführen eines Ladevorgangs als ein Ladebenutzer an, und führen Sie den Ladevorgang aus. Der Ladevorgang wird mit der Ressourcenklasse des Benutzers ausgeführt.  Diese Methode ist einfacher als der Versuch, die Ressourcenklasse eines Benutzers zu ändern, um die jeweilige Ressourcenklassenanforderung zu erfüllen.

### <a name="create-a-loading-user"></a>Erstellen eines Ladebenutzers

In diesem Beispiel wird ein Ladebenutzer für die Ressourcenklasse „staticrc20“ erstellt. Der erste Schritt umfasst das **Herstellen einer Verbindung mit dem Master** und das Erstellen einer Anmeldung.

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```

Stellen Sie eine Verbindung mit dem Data Warehouse her, und erstellen Sie einen Benutzer. Im folgenden Code wird vorausgesetzt, dass Sie über eine Verbindung mit der Datenbank „mySampleDataWarehouse“ verfügen. Es wird veranschaulicht, wie Sie den Benutzer „LoaderRC20“ erstellen und ihm die CONTROL-Berechtigung für eine Datenbank gewähren. Anschließend wird der Benutzer als Mitglied der Datenbankrolle „staticrc20“ hinzugefügt.  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```

Melden Sie sich zum Ausführen eines Ladevorgangs mit Ressourcen für die Ressourcenklasse „staticRC20“ als „LoaderRC20“ an, und führen Sie den Ladevorgang aus.

Führen Sie Ladevorgänge nicht unter dynamischen Ressourcenklassen, sondern unter statischen Ressourcenklassen aus. Wenn Sie die statischen Ressourcenklassen verwenden, ist unabhängig von Ihren [Data Warehouse-Einheiten](resource-consumption-models.md) garantiert, dass dieselben Ressourcen genutzt werden. Wenn Sie eine dynamische Ressourcenklasse verwenden, variieren die Ressourcen je nach Ihrer Dienstebene. Für dynamische Klassen bedeutet eine niedrigere Dienstebene, dass Sie für Ihren Ladebenutzer wahrscheinlich eine größere Ressourcenklasse nutzen müssen.

## <a name="allow-multiple-users-to-load"></a>Ermöglichen von Ladevorgängen für mehrere Benutzer

Oftmals müssen mehrere Benutzer in der Lage sein, Daten in ein Data Warehouse zu laden. Das Laden mit [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) setzt CONTROL-Berechtigungen der Datenbank voraus.  Die CONTROL-Berechtigung erteilt Steuerungszugriff auf alle Schemas. Unter Umständen sollen aber nicht alle Benutzer, die Daten laden, über Steuerungszugriff auf alle Schemas verfügen. Berechtigungen können mithilfe der DENY CONTROL-Anweisung eingeschränkt werden.

Beispiel: Wenn Sie über die Datenbankschemas Schema_A für Abteilung A und Schema_B für Abteilung B verfügen, sollten Sie beachten, dass die Datenbankbenutzer Benutzer_A und Benutzer_B Benutzer von PolyBase sind und in Abteilung A bzw. B geladen werden. Beiden wurden CONTROL-Datenbankberechtigungen erteilt. Die Ersteller von Schema A und B sperren ihre Schemas jetzt mit der DENY-Anweisung:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

Benutzer_A und Benutzer_B sind für das Schema der anderen Abteilung jetzt gesperrt.

## <a name="load-to-a-staging-table"></a>Laden in eine Stagingtabelle

Laden Sie Daten in eine Stagingtabelle, um beim Verschieben von Daten in eine Data Warehouse-Tabelle die bestmögliche Ladegeschwindigkeit zu erzielen.  Definieren Sie die Stagingtabelle als Heap, und verwenden Sie Roundrobin als Verteilungsoption.

Beachten Sie, dass das Laden üblicherweise ein zweistufiger Prozess ist, bei dem die Daten zunächst in eine Stagingtabelle geladen und anschließend in eine Data Warehouse-Produktionstabelle eingefügt werden. Wenn die Produktionstabelle eine Hashverteilung verwendet, ist das Laden und Einfügen unter Umständen insgesamt schneller, wenn Sie die Stagingtabelle mit der Hashverteilung definieren. Das Laden in die Stagingtabelle dauert zwar länger, der zweite Schritt (also das Einfügen der Zeilen in die Produktionstabelle) kommt jedoch ohne verteilungsübergreifende Datenverschiebung aus.

## <a name="load-to-a-columnstore-index"></a>Laden in einen Columnstore-Index

Columnstore-Indizes erfordern große Mengen an Arbeitsspeicher, um Daten in Zeilengruppen mit hoher Qualität zu komprimieren. Um in Bezug auf die Komprimierung und den Index die höchste Effizienz zu erzielen, muss der Columnstore-Index in jeder Zeilengruppe die maximale Anzahl von Zeilen (1.048.576) komprimieren. Wenn der Arbeitsspeicher knapp ist, können für den Columnstore-Index unter Umständen nicht die maximalen Komprimierungsraten erzielt werden. Dies wirkt sich auf die Abfrageleistung aus. Ausführliche Informationen hierzu finden Sie unter [Maximieren der Zeilengruppenqualität für Columnstore](data-load-columnstore-compression.md).

- Verwenden Sie Ladebenutzer, die Mitglieder einer mittelgroßen oder großen Ressourcenklasse sind, um sicherzustellen, dass der Ladebenutzer über genügend Arbeitsspeicher zur Erreichung der maximalen Komprimierungsraten verfügt.
- Laden Sie eine ausreichende Zahl von Zeilen, um neue Zeilengruppen vollständig zu füllen. Während eines Massenladevorgangs werden alle 1.048.576 Zeilen im Columnstore direkt als vollständige Zeilengruppe komprimiert. Ladevorgänge mit weniger als 102.400 Zeilen senden die Zeilen an den Deltaspeicher, in dem Zeilen in einem B-Strukturindex gespeichert werden. Wenn Sie eine zu kleine Zahl von Zeilen laden, werden diese ggf. allesamt im Deltastore abgelegt und nicht sofort in das Columnstore-Format komprimiert.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Vergrößern von Batches bei Verwendung der SQLBulkCopy-API oder von BCP

Wie bereits erwähnt erzielt das Laden mit PolyBase den höchsten Durchsatz mit dem Synapse SQL-Pool. Wenn Sie PolyBase nicht zum Laden verwenden können und die SqlBulkCopy-API (oder BCP) verwenden müssen, sollten Sie eine Erhöhung der Batchgröße für einen besseren Durchsatz in Betracht ziehen. Eine gute Faustregel ist eine Batchgröße zwischen 100 KB und 1 Mio. Zeilen.

## <a name="manage-loading-failures"></a>Verwalten von Ladefehlern

Das Laden von Daten mithilfe einer externen Tabelle kann folgenden Fehler auslösen: *Abfrage abgebrochen – der maximale Ablehnungsgrenzwert wurde beim Lesen aus einer externen Quelle erreicht*. Diese Meldung weist darauf hin, dass die externen Daten fehlerhafte Datensätze enthalten. Ein Datensatz gilt als fehlerhaft, wenn die Datentypen und die Spaltenanzahl nicht den Spaltendefinitionen der externen Tabelle entsprechen oder wenn die Daten nicht im angegebenen externen Dateiformat vorliegen.

Vergewissern Sie sich zur Behebung dieses Problems, dass die Formatdefinitionen der externen Tabelle und Datei korrekt sind und Ihre externen Daten diesen Definitionen entsprechen. Sollte ein Teil der externen Datensätze fehlerhaft sein, können Sie diese Datensätze für Ihre Abfragen mithilfe der Ablehnungsoptionen in „CREATE EXTERNAL TABLE“ ablehnen.

## <a name="insert-data-into-a-production-table"></a>Einfügen von Daten in eine Produktionstabelle

Bei einem einmaligen Ladevorgang für eine kleine Tabelle mit einer [INSERT-Anweisung](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) oder beim periodischen erneuten Laden eines Suchvorgangs wird unter Umständen eine ausreichende Leistung für Ihre Zwecke erzielt, wenn Sie eine Anweisung wie `INSERT INTO MyLookup VALUES (1, 'Type 1')` verwenden.  Singleton-Einfügungen sind jedoch nicht so effizient wie ein Massenladevorgang.

Wenn Sie im Laufe eines Tages über Tausende oder mehr einzelne Einfügungen verfügen, sollten Sie sie zu einem Batch zusammenfassen, um das Massenladen zu ermöglichen.  Entwickeln Sie Ihre Prozesse so, dass die einzelnen Einfügungen an eine Datei angefügt werden, und erstellen Sie dann einen weiteren Prozess, mit dem die Datei regelmäßig geladen wird.

## <a name="create-statistics-after-the-load"></a>Erstellen von Statistiken nach dem Laden

Zur Verbesserung der Abfrageleistung ist es wichtig, nach dem ersten Laden oder nach Datenänderungen Statistiken für alle Spalten sämtlicher Tabellen zu erstellen, ansonsten treten wesentliche Änderungen in den Daten auf. Das Erstellen von Statistiken kann manuell ausgeführt werden, oder Sie können [auto-create statistics](../sql-data-warehouse/sql-data-warehouse-tables-statistics.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) aktivieren.

Eine ausführliche Erläuterung von Statistiken finden Sie unter [Statistiken](develop-tables-statistics.md). Im folgenden Beispiel wird gezeigt, wie Statistiken für fünf Spalten der Tabelle „Customer_Speed“ manuell erstellt werden.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Durchführen einer Rotation von Speicherschlüsseln

Eine bewährte Sicherheitsmethode besteht darin, den Zugriffsschlüssel für Ihren Blobspeicher regelmäßig zu ändern. Sie verfügen über zwei Speicherschlüssel für Ihr Blobspeicherkonto und haben somit die Möglichkeit, einen Schlüsselübergang durchzuführen.

Gehen Sie wie folgt vor, um Schlüssel für Azure Storage-Konten zu rotieren:

Führen Sie für jedes Speicherkonto, dessen Schlüssel sich geändert hat, [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) aus.

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

- Weitere Informationen zu PolyBase und zum Entwerfen eines ELT-Prozesses (Extrahieren, Laden und Transformieren) finden Sie unter [Entwerfen von ELT für Azure Synapse Analytics](../sql-data-warehouse/design-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
- Ein Tutorial zum Ladevorgang finden Sie unter [Verwenden von PolyBase zum Laden von Daten aus Azure Blob Storage in Azure Synapse Analytics](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
- Informationen zum Überwachen von Datenladevorgängen finden Sie unter [Überwachen Ihrer Workload mit dynamischen Verwaltungssichten](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
