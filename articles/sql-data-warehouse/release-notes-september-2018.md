---
title: 'Azure SQL Data Warehouse: Versionshinweise September 2018 | Microsoft-Dokumentation'
description: Versionshinweise zu Azure SQL Data Warehouse
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/08/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: 5041458fdc3c6a49c59f2c4c476db71098531419
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65912156"
---
# <a name="whats-new-in-azure-sql-data-warehouse-september-2018"></a>Neuerungen in Azure SQL Data Warehouse September 2018
Azure SQL Data Warehouse wird fortlaufend verbessert. In diesem Artikel informieren wir Sie über neue Features und Änderungen, die im September 2018 eingeführt wurden.

## <a name="new-lower-entry-point-for-sql-data-warehouse-gen2"></a>Neuer niedrigerer Einstiegspunkt für SQL Data Warehouse Gen2
Im April 2018 [kündigte Microsoft](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) Azure SQL Data Warehouse Gen2 mit 5-facher Leistung, 5-facher Computeskalierung, 4-facher Parallelität und unbegrenztem Speicher an. Laut dem [Vergleichstest für Data Warehouse in der Cloud](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/) von Gigaom **übertrifft SQL Data Warehouse Gen2 Amazon Redshift um 42 %**.

Gen2 ist nun auf der niedrigeren Einstiegsebene von DWU500c allgemein verfügbar und ermöglicht Ihnen das Ausführen eines kleineren Data Warehouse oder von Entwicklungs-/Testumgebungen mit all den neuesten Verbesserungen des Diensts. Beim neuen Einstiegspunkt werden alle Gen2-Features beibehalten, wie z.B. [adaptive Zwischenspeicherung](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/), [blitzschnelle Datenumsetzung](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/) und Unterstützung für [Data Warehouse in Echtzeit](https://azure.microsoft.com/blog/enabling-real-time-data-warehousing-with-azure-sql-data-warehouse/).

## <a name="sql-vulnerability-assessment"></a>SQL-Sicherheitsrisikobewertung
[SQL-Sicherheitsrisikobewertung (VA)](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/25/sql-vulnerability-assessment-now-supports-azure-sql-data-warehouse-and-azure-sql-database-managed-instance/) ist ein benutzerfreundlicher Dienst, der Ihr Data Warehouse kontinuierlich überwacht. Mit seiner Hilfe können Sie ein hohes Maß an Sicherheit jederzeit sicherstellen und dafür sorgen, dass Ihre Unternehmensrichtlinien eingehalten werden. Er stellt einen umfassenden Sicherheitsbericht mit handlungsrelevanten Schritten zur Bereinigung für jedes gefundene Problem bereit. Dieser Bericht erleichtert es Ihnen, die Sicherheit Ihrer Datenbank proaktiv zu verwalten und sich auf die Aktionen mit höchster Auswirkung konzentrieren – sogar, wenn Sie kein Sicherheitsexperte sind. In dynamischen Umgebungen mit häufigen Änderungen, die schwierig nachzuverfolgen sind, ist VA sehr nützlich bei der Entdeckung von Einstellungen, die Ihr Data Warehouse anfällig für Angriffe machen können.

## <a name="improved-availability-with-query-restartability"></a>Verbesserte Verfügbarkeit durch Neustartfähigkeit von Abfragen
Während der Ausführung einer Abfrage können zahlreiche Probleme auftreten, die zum Fehlschlagen der Abfrage führen. Ein Netzwerkausfall, ein Hardwarefehler oder ein anderer Grund für das Trennen der Verbindung kann eine Unterbrechung verursachen. SQL Data Warehouse unterstützt jetzt die Neustartfähigkeit von Abfragen für SELECT-Abfragen auf Schritt- oder Anweisungsebene. 

Durch diese Neustartfähigkeit wird eine Abfrage, die während der Ausführung aufgrund eines Fehlers unterbrochen wird, automatisch neu gestartet. Abhängig von der Anzahl der Schritte, der Form der Abfrage oder dem Punkt, an dem die Abfrage während der Ausführung angehalten wurde, führt die SQL Data Warehouse-Engine entweder einen Neustart der vollständigen Abfrage aus oder setzt sie ab dem letzten abgeschlossenen Abfrageschritt fort. Aus Sicht des Endbenutzers wird die Abfrage einfach fortgesetzt. 

## <a name="maintenance-scheduling-preview"></a>Wartungszeitplan (Vorschau)
Der Wartungszeitplan für Azure SQL Data Warehouse ist ab sofort als Vorschauversion verfügbar. Dieses neue Feature lässt sich nahtlos in Benachrichtigungen über eine geplante Wartung von Service Health, in Resource Health Check Monitor und den Zeitplanungsdienst für Azure SQL Data Warehouse-Wartungen integrieren. Mit einem Wartungszeitplan können Sie ein passendes Zeitfenster für den Erhalt neuer Features, Upgrades und Patches planen.

Beim Wartungszeitplan wird Azure Monitor genutzt und Kunden können bestimmen, wie sie über bevorstehende Wartungsereignisse benachrichtigt werden möchten und welche automatisierten Abläufe zur Bewältigung von Ausfallzeiten und zur Minimierung der Auswirkungen auf ihre Vorgänge ausgelöst werden sollen. Bei den Benachrichtigungen kann es sich um eine E-Mail oder SMS handeln. 

## <a name="wide-row-support-in-polybase"></a>Unterstützung großer Zeilen in Polybase
Beim Laden von Daten in SQL Data Warehouse wird allgemein empfohlen, [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading#options-for-loading-with-polybase) mit seiner Unterstützung für paralleles Laden von Daten zu verwenden. Diese Version ermöglicht die Unterstützung größerer Spalten von 32 KB bis 1 MB, sodass Tabellen mit großen Zeilen geladen werden können. Die Unterstützung für große Zeilen vereinfacht den Datenladevorgang für Tabellen mit großen Zeilen.

> [!Note]
> Die Gesamtzeilengröße darf 1 MB nicht überschreiten.

## <a name="additional-language-support"></a>Zusätzliche Sprachunterstützung
Mit dieser Version wird Unterstützung für die folgenden T-SQL-Sprachelemente bereitgestellt:

### <a name="stringsplit"></a>STRING_SPLIT
Die Funktion [STRING_SPLIT](https://docs.microsoft.com/sql/t-sql/functions/string-split-transact-sql) teilt eine Zeichenfolge mithilfe des angegebenen Trennzeichens. STRING_SPLIT ist in Datenladeszenarien nützlich, bei denen eine Spalte mehrere Werte enthalten kann, die analysiert und in eine andere Tabelle eingefügt werden sollen.

#### <a name="example"></a>Beispiel
```sql
DECLARE @tags NVARCHAR(400) = 'clothing,road,,touring,bike';

SELECT
    value
FROM
    STRING_SPLIT(@tags, ',')
WHERE
    RTRIM(value) <> '';
```

### <a name="compressdecompress-functions"></a>Funktionen COMPRESS/DECOMPRESS
Die Funktionen [COMPRESS](https://docs.microsoft.com/sql/t-sql/functions/compress-transact-sql) / [DECOMPRESS](https://docs.microsoft.com/sql/t-sql/functions/decompress-transact-sql) ermöglichen es Ihnen, eine Zeichenfolgeneingabe mithilfe des GZIP-Algorithmus zu komprimieren oder dekomprimieren.

#### <a name="example"></a>Beispiel

```sql
SELECT
    name [name_original]
    , COMPRESS(name) [name_compressed]
    , CAST(DECOMPRESS(COMPRESS(name)) AS NVARCHAR(MAX)) [name_decompressed]
FROM
    sys.objects;
```

### <a name="if-exists-clause-for-dropping-views"></a>IF EXISTS-Klausel für das Löschen von Sichten
Durch Hinzufügen der IF EXISTS-Klausel in der [DROP VIEW](https://docs.microsoft.com/sql/t-sql/statements/drop-view-transact-sql)-Anweisung wird der T-SQL-Code vereinfacht, der erforderlich ist, um eine Sicht aus dem Data Warehouse zu entfernen. Bei Anwendung der IF EXISTS-Syntax auf eine DROP VIEW-Anweisung wird die Sicht gelöscht, wenn sie vorhanden ist, oder die Anweisung wird ignoriert, wenn die Sicht nicht vorhanden ist.

#### <a name="example"></a>Beispiel
```sql
DROP VIEW IF EXISTS dbo.TestView;
```
```
Message
--------------------------------------------------
Commands completed successfully.

```

## <a name="improved-compilation-time-for-singleton-inserts-and-ddl-statements"></a>Verbesserte Kompilierungszeit für Singleton-Einfügungen und DDL-Anweisungen 
Ein herkömmliches ETL-Modell (Extrahieren, Transformieren und Laden) für das Einfügen von Daten bewirkt häufig eine Singleton-Einfügung ([INSERT-VALUES](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql)) in einer Tabelle in der Datenbank. Diese Version verbessert die Leistung von Singleton-Einfügevorgängen durch Verringern der Kompilierungszeit, die zum Ausführen dieser Art von Anweisung erforderlich ist. In einigen Fällen ist eine um das Dreifache schnellere Kompilierung zu beobachten. Durch diese Verbesserung verringert sich die Zeit, die zum Ausführen einer Singleton-Einfügeanweisung erforderlich ist. 

Die Verbesserung kommt auch Data Warehouses mit einer großen Anzahl von Objekten zugute, indem in ähnlicher Form die Kompilierungszeit für Abfragen für DDL-Anweisungen (Data Definition Language) reduziert wird, einschließlich CREATE-, ALTER- und DELETE-Vorgängen. 

Außerdem wird durch die Verbesserung die Gesamtausführungszeit von Anweisungen reduziert, die über große Tabellen ausgeführt werden, d.h. Tabellen, die eine große Anzahl von Spalten umfassen. Bei der Verbesserung handelt es ich um eine Reduzierung der Zeit im Kompilierungsschritt der Abfrage, durch die sich die Gesamtausführungsdauer für Abfragen reduziert.

## <a name="bug-fixes"></a>Fehlerbehebungen

| Titel | BESCHREIBUNG |
|:---|:---|
| **Fehlerbehebung beim Erstellen von Statistiken zu Verteilungen für UNIQUE-Einschränkungen** | Dies betrifft einen Fehler, der für Benutzer beim Ausführen von UPDATE STATISTICS mit ausschließlicher Angabe der Tabelle auftrat, wenn für die Tabelle eine UNIQUE-Einschränkung definiert war. |
| **Fehlerbehebung beim Kompilieren von Abfragen über externe Tabellen** | Dies betrifft einen Fehler, der sich auf die Kompilierungszeit für Abfragen unter Einbeziehung externer Tabellen auswirkte.|
| **Fehlerbehebung beim Ausführen einer Anweisung mit großen Typen** | Dies betrifft einen Fehler bei der Kompilierung vorbereiteter Anweisungen mit Parametern, die als einer der *großen* Typen (nvarchar(max), varchar(max) und varbinary(max)) definiert sind. |
| **Behebung bei Auftreten eines Fehlers im Fall tief geschachtelter Abfragen** | Stellt eine eindeutige Fehlermeldung bereit, wenn eine tief geschachtelte Abfrage die Systemgrenzwerte überschreitet.|
| **Behebung von Fehlern bei der Kompilierungszeit, wenn eine Anweisung eine korrelierte Unterabfrage und eine Ausführungszeitkonstante enthält** |Dies betrifft einen Fehler bei der Kompilierungszeit für Abfragen mit einer bestimmten Kombination aus korrelierten Unterabfragen und Ausführungszeitkonstanten (z.B. GETDATE()).|
| **Behebung für ein Timeout beim Abrufen von PDW-Objektsperren und Parallelitätsslot für Autostats** |Als Fehlerbehebung wird ein Timeout für die Sperre hinzugefügt, um zu verhindern, dass Autostats-Anforderungen die ursprünglichen Anforderungen über einen langen Zeitraum blockieren.|

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie sich mit den Grundlagen von SQL Data Warehouse vertraut gemacht haben, können Sie sich nun darüber informieren, wie Sie schnell [eine SQL Data Warehouse-Instanz erstellen][create a SQL Data Warehouse]. Falls Sie mit Azure noch nicht vertraut sind und auf neue Terminologie stoßen, ist das [Azure-Glossar][Azure glossary] sehr nützlich. Weitere SQL Data Warehouse-Ressourcen:  

* [Kundenerfolgsgeschichten]
* [Blogs]
* [Funktionsanfragen]
* [Videos]
* [Customer Advisory Team-Blogs]
* [Stack Overflow-Forum]
* [Twitter]


[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team-Blogs]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Kundenerfolgsgeschichten]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Funktionsanfragen]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow-Forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
