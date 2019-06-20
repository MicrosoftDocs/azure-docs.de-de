---
title: Bewährte Methoden für das Laden von Daten – Azure SQL Data Warehouse | Microsoft-Dokumentation
description: Dieser Artikel enthält Empfehlungen und Leistungsoptimierungen für das Laden von Daten in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load data
ms.date: 05/31/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seoapril2019
ms.openlocfilehash: 1b5af710feb743a30ac4a2af94b6e7e8b6c56595
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479892"
---
# <a name="best-practices-for-loading-data-into-azure-sql-data-warehouse"></a>Bewährte Methoden zum Laden von Daten in Azure SQL Data Warehouse

Dieser Artikel enthält Empfehlungen und Leistungsoptimierungen für das Laden von Daten in Azure SQL Data Warehouse.

## <a name="preparing-data-in-azure-storage"></a>Vorbereiten von Daten in Azure Storage

Zur Minimierung der Wartezeit sollten sich Speicherebene und Data Warehouse am gleichen Ort befinden.

Beim Exportieren von Daten in ein ORC-Dateiformat erhalten Sie unter Umständen Java-Fehler vom Typ „Nicht genügend Speicherplatz“, falls umfangreiche Textspalten vorhanden sind. Diese Einschränkung können Sie umgehen, indem Sie nur eine Teilmenge der Spalten exportieren.

Mit PolyBase können keine Zeilen geladen werden, die Daten mit mehr als einer Million Bytes enthalten. Wenn Sie Daten in Textdateien in Azure Blob Storage oder Azure Data Lake Store platzieren, müssen diese jeweils Daten mit weniger als einer Million Bytes enthalten. Dieses Bytelimit gilt unabhängig vom Tabellenschema.

Alle Dateiformate weisen unterschiedliche Leistungsmerkmale auf. Am schnellsten können Sie komprimierte, durch Trennzeichen getrennte Textdateien laden. Der Unterschied zwischen UTF-8 und UTF-16 wirkt sich nur minimal auf die Leistung aus.

Teilen Sie große komprimierte Dateien in kleinere komprimierte Dateien.

## <a name="running-loads-with-enough-compute"></a>Ausführen von Ladevorgängen mit ausreichender Compute-Kapazität

Führen Sie immer nur einen einzelnen Ladeauftrag aus, um die bestmögliche Ladegeschwindigkeit zu erzielen. Sollte das nicht möglich sein, führen Sie eine möglichst geringe Anzahl von Ladevorgängen gleichzeitig aus. Wenn ein umfangreicher Ladeauftrag ansteht, empfiehlt es sich unter Umständen, das Data Warehouse vor dem Laden zentral hochzuskalieren.

Erstellen Sie „Ladebenutzer“, die für das Ausführen von Ladevorgängen vorgesehen sind, um Ladevorgänge mit den geeigneten Computeressourcen durchzuführen. Weisen Sie jedem Ladebenutzer eine bestimmte Ressourcenklasse zu. Melden Sie sich zum Ausführen eines Ladevorgangs als ein Ladebenutzer an, und führen Sie den Ladevorgang aus. Der Ladevorgang wird mit der Ressourcenklasse des Benutzers ausgeführt.  Diese Methode ist einfacher als der Versuch, die Ressourcenklasse eines Benutzers zu ändern, um die jeweilige Ressourcenklassenanforderung zu erfüllen.

### <a name="example-of-creating-a-loading-user"></a>Beispiel für die Erstellung eines Ladebenutzers

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

Führen Sie Ladevorgänge nicht unter dynamischen Ressourcenklassen, sondern unter statischen Ressourcenklassen aus. Wenn Sie die statischen Ressourcenklassen verwenden, ist unabhängig von Ihren [Data Warehouse-Einheiten](what-is-a-data-warehouse-unit-dwu-cdwu.md) garantiert, dass dieselben Ressourcen genutzt werden. Wenn Sie eine dynamische Ressourcenklasse verwenden, variieren die Ressourcen je nach Ihrer Dienstebene. Für dynamische Klassen bedeutet eine niedrigere Dienstebene, dass Sie für Ihren Ladebenutzer wahrscheinlich eine größere Ressourcenklasse nutzen müssen.

## <a name="allowing-multiple-users-to-load"></a>Ermöglichen von Ladevorgängen für mehrere Benutzer

Oftmals müssen mehrere Benutzer in der Lage sein, Daten in ein Data Warehouse zu laden. Das Laden mit [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) setzt CONTROL-Berechtigungen der Datenbank voraus.  Die CONTROL-Berechtigung erteilt Steuerungszugriff auf alle Schemas. Unter Umständen sollen aber nicht alle Benutzer, die Daten laden, über Steuerungszugriff auf alle Schemas verfügen. Berechtigungen können mithilfe der DENY CONTROL-Anweisung eingeschränkt werden.

Beispiel: Wenn Sie über die Datenbankschemas Schema_A für Abteilung A und Schema_B für Abteilung B verfügen, sollten Sie beachten, dass die Datenbankbenutzer Benutzer_A und Benutzer_B Benutzer von PolyBase sind und in Abteilung A bzw. B geladen werden. Beiden wurden CONTROL-Datenbankberechtigungen erteilt. Die Ersteller von Schema A und B sperren ihre Schemas jetzt mit der DENY-Anweisung:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

Benutzer_A und Benutzer_B sind für das Schema der anderen Abteilung jetzt gesperrt.

## <a name="loading-to-a-staging-table"></a>Laden in eine Stagingtabelle

Laden Sie Daten in eine Stagingtabelle, um beim Verschieben von Daten in eine Data Warehouse-Tabelle die bestmögliche Ladegeschwindigkeit zu erzielen.  Definieren Sie die Stagingtabelle als Heap, und verwenden Sie Roundrobin als Verteilungsoption. 

Beachten Sie, dass das Laden üblicherweise ein zweistufiger Prozess ist, bei dem die Daten zunächst in eine Stagingtabelle geladen und anschließend in eine Data Warehouse-Produktionstabelle eingefügt werden. Wenn die Produktionstabelle eine Hashverteilung verwendet, ist das Laden und Einfügen unter Umständen insgesamt schneller, wenn Sie die Stagingtabelle mit der Hashverteilung definieren. Das Laden in die Stagingtabelle dauert zwar länger, der zweite Schritt (also das Einfügen der Zeilen in die Produktionstabelle) kommt jedoch ohne verteilungsübergreifende Datenverschiebung aus.

## <a name="loading-to-a-columnstore-index"></a>Laden in einen Columnstore-Index

Columnstore-Indizes erfordern große Mengen an Arbeitsspeicher, um Daten in Zeilengruppen mit hoher Qualität zu komprimieren. Um in Bezug auf die Komprimierung und den Index die höchste Effizienz zu erzielen, muss der Columnstore-Index in jeder Zeilengruppe die maximale Anzahl von Zeilen (1.048.576) komprimieren. Wenn der Arbeitsspeicher knapp ist, können für den Columnstore-Index unter Umständen nicht die maximalen Komprimierungsraten erzielt werden. Dies wirkt sich wiederum auf die Abfrageleistung aus. Ausführliche Informationen hierzu finden Sie unter [Maximieren der Zeilengruppenqualität für Columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Verwenden Sie Ladebenutzer, die Mitglieder einer mittelgroßen oder großen Ressourcenklasse sind, um sicherzustellen, dass der Ladebenutzer über genügend Arbeitsspeicher zur Erreichung der maximalen Komprimierungsraten verfügt. 
- Laden Sie eine ausreichende Zahl von Zeilen, um neue Zeilengruppen vollständig zu füllen. Während eines Massenladevorgangs werden alle 1.048.576 Zeilen im Columnstore direkt als vollständige Zeilengruppe komprimiert. Ladevorgänge mit weniger als 102.400 Zeilen senden die Zeilen an den Deltaspeicher, in dem Zeilen in einem B-Strukturindex gespeichert werden. Wenn Sie eine zu kleine Zahl von Zeilen laden, werden diese ggf. allesamt im Deltastore abgelegt und nicht sofort in das Columnstore-Format komprimiert.

## <a name="handling-loading-failures"></a>Behandeln von Ladefehlern

Das Laden von Daten mithilfe einer externen Tabelle kann folgenden Fehler auslösen: *Abfrage abgebrochen – der maximale Ablehnungsgrenzwert wurde beim Lesen aus einer externen Quelle erreicht*. Diese Meldung weist darauf hin, dass die externen Daten fehlerhafte Datensätze enthalten. Ein Datensatz gilt als fehlerhaft, wenn die Datentypen und die Spaltenanzahl nicht den Spaltendefinitionen der externen Tabelle entsprechen oder wenn die Daten nicht im angegebenen externen Dateiformat vorliegen. 

Vergewissern Sie sich zur Behebung dieses Problems, dass die Formatdefinitionen der externen Tabelle und Datei korrekt sind und Ihre externen Daten diesen Definitionen entsprechen. Sollte ein Teil der externen Datensätze fehlerhaft sein, können Sie diese Datensätze für Ihre Abfragen mithilfe der Ablehnungsoptionen in „CREATE EXTERNAL TABLE“ ablehnen.

## <a name="inserting-data-into-a-production-table"></a>Einfügen von Daten in eine Produktionstabelle

Bei einem einmaligen Ladevorgang für eine kleine Tabelle mit einer [INSERT-Anweisung](/sql/t-sql/statements/insert-transact-sql) oder beim periodischen erneuten Laden eines Suchvorgangs wird unter Umständen eine ausreichende Leistung für Ihre Zwecke erzielt, wenn Sie eine Anweisung wie `INSERT INTO MyLookup VALUES (1, 'Type 1')` verwenden.  Singleton-Einfügungen sind jedoch nicht so effizient wie ein Massenladevorgang. 

Wenn Sie im Laufe eines Tages über Tausende oder mehr einzelne Einfügungen verfügen, sollten Sie sie zu einem Batch zusammenfassen, um das Massenladen zu ermöglichen.  Entwickeln Sie Ihre Prozesse so, dass die einzelnen Einfügungen an eine Datei angefügt werden, und erstellen Sie dann einen weiteren Prozess, mit dem die Datei regelmäßig geladen wird.

## <a name="creating-statistics-after-the-load"></a>Erstellen von Statistiken nach dem Laden

Zur Verbesserung der Abfrageleistung ist es wichtig, nach dem ersten Laden oder nach Datenänderungen Statistiken für alle Spalten sämtlicher Tabellen zu erstellen.  Dies kann manuell ausgeführt werden, oder Sie können das [automatische Erstellen von Statistiken](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistic) aktivieren.

Eine ausführliche Erläuterung von Statistiken finden Sie unter [Statistiken](sql-data-warehouse-tables-statistics.md). Im folgenden Beispiel wird gezeigt, wie Statistiken für fünf Spalten der Tabelle „Customer_Speed“ manuell erstellt werden.

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

Führen Sie für jedes Speicherkonto, dessen Schlüssel sich geändert hat, [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql) aus.

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

- Weitere Informationen zu PolyBase und zum Entwerfen eines ELT-Prozesses (Extrahieren, Laden und Transformieren) finden Sie unter [Designing Extract, Load, and Transform (ELT) for Azure SQL Data Warehouse](design-elt-data-loading.md) (ELT-Entwurf (Extrahieren, Laden und Transformieren) für Azure SQL Data Warehouse).
- Ein Tutorial zum Ladevorgang finden Sie unter [Verwenden von PolyBase zum Laden von Daten aus Azure Blob Storage in Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).
- Informationen zum Überwachen von Datenladevorgängen finden Sie unter [Überwachen Ihrer Workload mit dynamischen Verwaltungssichten](sql-data-warehouse-manage-monitor.md).



