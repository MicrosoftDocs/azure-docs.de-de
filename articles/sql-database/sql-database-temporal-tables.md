---
title: Erste Schritte mit temporalen Tabellen in der Azure SQL-Datenbank | Microsoft Docs
description: Lernen Sie die ersten Schritte mit temporalen Tabellen in der Azure SQL-Datenbank.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: fbb2458e73330a09124c00cebe3eb7bcaba5408d
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65951500"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Erste Schritte mit temporalen Tabellen in der Azure SQL-Datenbank

Temporale Tabellen sind eine neue Programmierfunktion der Azure SQL-Datenbank, mit der Sie den vollständigen Verlauf von Änderungen in Ihren Daten ohne benutzerdefinierte Codierung nachverfolgen und analysieren können. Temporale Tabellen enthalten Daten, die eng mit dem zeitlichen Kontext verbunden sind, sodass gespeicherte Fakten nur im angegebenen Zeitraum als gültig interpretiert werden können. Diese Eigenschaft von temporalen Tabellen ermöglicht eine effiziente zeitbasierte Analyse und Einblicke in die Datenentwicklung.

## <a name="temporal-scenario"></a>Temporales Szenario

Dieser Artikel beschreibt die Schritte zur Nutzung temporaler Tabellen in einem Anwendungsszenario. Stellen Sie sich vor, Sie möchten die Benutzeraktivität auf einer neuen Website nachverfolgen, die von Grund auf neu entwickelt wird, oder auf einer vorhandenen Website, die Sie mit der Benutzeraktivitätsanalyse erweitern möchten. In diesem vereinfachten Beispiel setzen wir voraus, dass die Anzahl der besuchten Webseiten während einer Zeitspanne ein Indikator ist, der in der Websitedatenbank aufgezeichnet und überwacht werden muss, die in der Azure SQL-Datenbank gehostet wird. Die Verlaufsanalyse der Benutzeraktivität soll Anregungen zum Neuentwurf der Website und bessere Erkenntnisse über die Besucher liefern.

Das Datenbankmodell für dieses Szenario ist sehr einfach – die Metrik der Benutzeraktivität wird nur mit dem Ganzzahlfeld **PageVisited** dargestellt und zusammen mit grundlegenden Informationen im Benutzerprofil erfasst. Außerdem verwalten Sie für die zeitbasierte Analyse eine Reihe von Zeilen für jeden Benutzer, wobei jede Zeile die Anzahl von Seiten darstellt, die ein bestimmter Benutzer innerhalb einer bestimmten Zeitspanne besucht.

![Schema](./media/sql-database-temporal-tables/AzureTemporal1.png)

Glücklicherweise erfordert die Verwaltung dieser Aktivitätsinformationen von Ihnen keinerlei Aufwand zur Bearbeitung Ihrer App. Mit temporalen Tabellen ist dieser Prozess automatisiert – Sie genießen volle Flexibilität bei der Gestaltung der Website und haben mehr Zeit, um sich auf die eigentliche Datenanalyse zu konzentrieren. Sie müssen einzig und allein sicherstellen, dass die Tabelle **WebSiteInfo** als [temporal mit Systemversionsverwaltung](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0)konfiguriert ist. Die genauen Schritte zum Verwenden temporaler Tabellen in diesem Szenario werden unten beschrieben.

## <a name="step-1-configure-tables-as-temporal"></a>Schritt 1: Konfigurieren von Tabellen als temporal
Je nachdem, ob Sie eine neue Entwicklung beginnen oder eine vorhandene Anwendung aktualisieren, werden Sie entweder temporale Tabellen erstellen oder vorhandene ändern, indem Sie temporale Attribute hinzufügen. Im Allgemeinen kann Ihr Szenario eine Kombination dieser beiden Optionen sein. Führen Sie diese Aktion mit [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) oder einem anderen Tool zur Transact-SQL-Entwicklung aus.

> [!IMPORTANT]
> Es wird empfohlen, immer die neueste Version von Management Studio zu verwenden, damit Sie mit Updates von Microsoft Azure und SQL-Datenbank synchron sind. [Aktualisieren Sie SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Erstellen einer neuen Tabelle
Verwenden Sie das Kontextmenüelement „Neue Tabelle mit Systemversionsverwaltung“ im SSMS-Objekt-Explorer, um den Abfrage-Editor mit einem Vorlagenskript für eine temporale Tabelle zu öffnen, und füllen Sie die Vorlage dann mit „Werte für Vorlagenparameter angeben“ (STRG+UMSCHALT+M):

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

Wählen Sie in SSDT beim Hinzufügen von neuen Elementen zum Datenbankprojekt die Vorlage „Temporale Tabelle (mit Systemversionsverwaltung)“ aus. Damit öffnen Sie den Tabellen-Designer, sodass Sie mühelos das Tabellenlayout festlegen können:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Sie können eine temporale Tabelle auch durch Angeben von Transact-SQL-Anweisungen direkt erstellen, wie im folgenden Beispiel gezeigt. Beachten Sie, dass die obligatorischen Elemente aller temporalen Tabellen die Definition von PERIOD und die SYSTEM_VERSIONING-Klausel mit einem Verweis auf eine andere Benutzertabelle sind, die Verlaufszeilenversionen speichert:

```
CREATE TABLE WebsiteUserInfo 
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED 
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL 
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
```

Wenn Sie eine temporale Tabelle mit Systemversionsverwaltung erstellen, wird die zugehörige Verlaufstabelle mit der Standardkonfiguration automatisch erstellt. Die Standardverlaufstabelle enthält einen gruppierten B-Strukturindex für die Periodenspalten (Anfang, Ende) mit aktivierter Seitenkomprimierung. Diese Konfiguration ist optimal für die Mehrzahl der Szenarien, in denen temporale Tabellen insbesondere für die [Datenüberwachung](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0)verwendet werden. 

In diesem Fall soll über einen längeren Datenverlauf hinweg und mit größeren Datasets eine zeitbasierte Trendanalyse ausgeführt werden, darum wird als Speicher für die Verlaufstabelle ein gruppierter Columnstore-Index gewählt. Ein gruppierter Columnstore bietet sehr gute Komprimierung und Leistung für analytische Abfragen. Temporale Tabellen geben Ihnen die Flexibilität zum vollständig unabhängigen Konfigurieren von Indizes für aktuelle und temporalen Tabellen. 

> [!NOTE]
> Columnstore-Indizes sind in der Premium-Ebene und in der Standardebene, in S3 und höher verfügbar.
>

Das folgende Skript zeigt, wie der Standardindex für die Verlaufstabelle in den gruppierten Columnstore geändert werden kann:

```
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

Temporale Tabellen werden im Objekt-Explorer zur leichteren Erkennung mit einem bestimmten Symbol dargestellt, während die zugehörige Verlaufstabelle als untergeordneter Knoten angezeigt wird.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Ändern einer vorhandenen Tabelle in temporal
Wir behandeln nun das alternative Szenario, in dem die WebsiteUserInfo-Tabelle bereits vorhanden ist, jedoch nicht zum Speichern eines Änderungsverlaufs konzipiert wurde. In diesem Fall können Sie die vorhandene Tabelle, wie im folgenden Beispiel gezeigt, einfach zu einer temporalen erweitern:

```
ALTER TABLE WebsiteUserInfo 
ADD 
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN   
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo); 

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

## <a name="step-2-run-your-workload-regularly"></a>Schritt 2: Reguläres Ausführen Ihrer Workload
Der Hauptvorteil von temporalen Tabellen ist, dass Sie Ihre Website zum Nachverfolgen von Änderungen in keiner Weise ändern oder anpassen müssen. Nach der Erstellung behalten temporale Tabellen jedes Mal transparent vorherige Zeilenversionen bei, wenn Sie Änderungen an Ihren Daten ausführen. 

Um die automatische Änderungsnachverfolgung für dieses spezielle Szenario nutzen zu können, richten wir es ein, dass die Spalte **PagesVisited** jedes Mal aktualisiert wird, wenn ein Benutzer seine Sitzung auf der Website beendet:

```
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
```

Es ist wichtig, zu beachten, dass die Aktualisierungsabfrage nicht die genaue Zeit kennen muss, zu der der eigentliche Vorgang aufgetreten ist, noch wie die Verlaufsdaten zur späteren Analyse beibehalten werden. Beide Aspekte werden automatisch von der Azure SQL-Datenbank behandelt. Das folgende Diagramm veranschaulicht, wie Verlaufsdaten bei jedem Update generiert werden.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Schritt 3: Ausführen der Verlaufsdatenanalyse
Da nun die temporale Systemversionsverwaltung aktiviert ist, ist die Analyse der Verlaufsdaten nur eine Abfrage von Ihnen entfernt. In diesem Artikel zeigen wir einige Beispiele, die allgemeine Analyseszenarien behandeln. Um alle Details zu erfahren, können Sie sich mit den verschiedenen Optionen vertraut machen, die mit der [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3)-Klausel eingeführt werden.

Um die Top 10-Benutzer geordnet nach der Anzahl der besuchten Webseiten mit Stand vor einer Stunde anzuzeigen, führen Sie diese Abfrage aus:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

Sie können diese Abfrage problemlos ändern, um die Websitebesuche vor einem Tag, einem Monat oder zu einem beliebigen Zeitpunkt in der Vergangenheit zu analysieren.

Um eine grundlegende statistische Analyse für den vorherigen Tag auszuführen, verwenden Sie das folgende Beispiel:

```
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
```

Um nach Aktivitäten eines bestimmten Benutzers in einer Zeitspanne zu suchen, verwenden Sie die CONTAINED IN-Klausel:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

Grafische Visualisierung ist besonders geeignet für temporale Abfragen, da Sie Trends und Verwendungsmuster unkompliziert in intuitiver Weise anzeigen können:

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Entwicklung des Tabellenschemas
In der Regel müssen Sie das Schema der temporalen Tabelle während der App-Entwicklung ändern. Führen Sie dafür einfach die regulären ALTER TABLE-Anweisungen aus, und die Azure SQL-Datenbank leitet Änderungen entsprechend an die Verlaufstabelle weiter. Das folgende Skript zeigt, wie Sie ein zusätzliches Attribut für die Nachverfolgung hinzufügen können:

```
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

Auf ähnliche Weise können Sie die Spaltendefinition ändern, während Ihre Workload aktiv ist:

```
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

Schließlich können Sie eine Spalte entfernen, die Sie nicht mehr benötigen.

```
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
```

Alternativ verwenden Sie die aktuellen [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) , um das Schema der temporalen Tabelle zu ändern, während Sie mit der Datenbank verbunden sind (Onlinemodus), oder als Teil des Datenbankprojekts (Offlinemodus).

## <a name="controlling-retention-of-historical-data"></a>Steuern der Aufbewahrung von Verlaufsdaten
Mit temporalen Tabellen mit Systemversionsverwaltung kann die Verlaufstabelle die Datenbankgröße stärker steigern als reguläre Tabellen. Eine große und stetig wachsende Verlaufstabelle kann ein Problem darstellen, sowohl aufgrund der reinen Speicherkosten als auch aufgrund der Tatsache, dass temporale Abfragen eine zusätzliche Belastung mit sich bringen. Daher ist die Entwicklung einer Aufbewahrungsrichtlinie für die Verwaltung von Daten in der Verlaufstabelle ein wichtiger Aspekt der Planung und Verwaltung des Lebenszyklus jeder temporalen Tabelle. Die Azure SQL-Datenbank bietet Ihnen die folgenden Methoden zum Verwalten von Verlaufsdaten in der temporalen Tabelle:

* [Tabellenpartitionierung](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Benutzerdefiniertes Bereinigungsskript](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>Nächste Schritte
Ausführliche Informationen zu temporalen Tabellen finden Sie in der [MSDN-Dokumentation](https://msdn.microsoft.com/library/dn935015.aspx).
Auf Channel 9 können Sie sich einen [Kundenerfahrungsbericht zur Temporal-Implementierung](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) anhören und eine [Temporal-Live-Demo](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016) ansehen.

