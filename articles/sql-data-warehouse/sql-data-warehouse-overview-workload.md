---
title: Weitere Informationen zu Azure SQL Data Warehouse-Vorgängen | Microsoft-Dokumentation
description: 'Die Flexibilität von SQL Data Warehouse ermöglicht Ihnen das Vergrößern, Verkleinern oder Anhalten der Rechenleistung mithilfe eines Schiebereglers für Data Warehouse-Einheiten (DWUs). Dieser Artikel beschreibt die Data Warehouse-Metriken und deren Beziehung zu DWUs. '
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: f0489d9c018abac380cd0f8b1aa51fa5bb81707a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60531172"
---
# <a name="data-warehouse-workload"></a>Data Warehouse-Workload
Ein Data Warehouse-Workload bezieht sich auf alle Vorgänge, die für ein Data Warehouse ablaufen müssen. Der Data Warehouse-Workload umfasst den gesamten Prozess des Ladens von Daten in das Warehouse, das Durchführen der Analyse und die Berichterstellung für das Data Warehouse, das Verwalten von Daten im Data Warehouse und das Exportieren von Daten aus dem Data Warehouse. Die Tiefe und Breite dieser Komponenten entsprechen häufig dem Reifegrad des Data Warehouse.

## <a name="new-to-data-warehousing"></a>Noch keine Erfahrung mit Data Warehousing?
Ein Data Warehouse ist eine Sammlung von Daten, die aus einer oder mehreren Datenquellen geladen werden und zum Ausführen von Business Intelligence-Tasks, wie z. B. Berichterstellung und Datenanalyse, verwendet werden.

Data Warehouses werden durch Abfragen charakterisiert, die eine größere Anzahl von Zeilen und große Datenbereiche scannen und relativ große Ergebnisse für Analyse und Berichterstellung zurückgeben können. Data Warehouses werden auch durch relativ große Datenmengen im Vergleich zu kleinen Einfügungen/Aktualisierungen/Löschungen auf Transaktionsebene charakterisiert.

* Die Leistung eines Data Warehouse ist am besten, wenn die Daten auf eine Weise gespeichert werden, die Abfragen optimiert, die eine große Anzahl von Zeilen oder große Datenbereiche scannen müssen. Diese Art des Scannens funktioniert am besten, wenn die Daten nach Spalten anstelle von Zeilen durchsucht und gespeichert werden.

> [!NOTE]
> Der speicherinterne ColumnStore-Index, der Spaltenspeicher verwendet, bietet eine bis zu zehnmal so hohe Komprimierung und eine hundertfache Abfrageleistungssteigerung gegenüber herkömmlichen binären Strukturen für Berichterstattung und Analyse von Abfragen. Wir betrachten ColumnStore-Indizes als Standard für das Speichern und Scannen großer Datenmengen in einem Data Warehouse.
> 
> 

* Ein Data Warehouse hat andere Anforderungen als ein System, das für die Onlinetransaktionsverarbeitung (OLTP) optimiert ist. Im OLTP-System gibt es viele Einfüge-, Aktualisierungs- und Löschvorgänge. Diese Suchvorgänge durchsuchen bestimmte Zeilen in der Tabelle. Tabellensuchen funktionieren am besten, wenn die Daten zeilenweise gespeichert sind. Die Daten können sortiert und schnell mit einem so genannten "Divide & Conquer"-Ansatz, der auch als Binärstruktur- oder btree-Suche bezeichnet wird, durchsucht werden.

## <a name="data-loading"></a>Laden von Daten
Das Laden von Daten ist ein großer Teil des Data Warehouse-Workloads. Unternehmen haben normalerweise ein stark ausgelastetes OLTP-System, das Änderungen im Laufe des Tages verfolgt, wenn Kunden Geschäftstransaktionen generieren. In regelmäßigen Abständen (häufig in der Nacht während eines Wartungsfensters) werden die Transaktionen in das Data Warehouse verschoben oder kopiert. Sobald sich die Daten im Data Warehouse befinden, können Analysten basierend auf den Daten Geschäftsentscheidungen treffen und Analysen durchführen.

* Normalerweise wird er Prozess des Ladens als ETL bezeichnet: Extrahieren, Transformieren und Laden. Daten müssen in der Regel umgewandelt (transformiert) werden, damit sie mit anderen Daten im Data Warehouse konsistent sind. Früher haben Unternehmen dedizierte ETL-Server für Transformationen verwendet. Jetzt – mit der schnellen umfangreichen parallelen Verarbeitung – können Sie Daten zuerst in SQL Data Warehouse laden und dann die Transformationen ausführen. Dieser Prozess wird als Extrahieren, Laden und Transformieren (ELT) bezeichnet und wird ein neuer Standard für die Data Warehouse-Workloads.

> [!NOTE]
> Mit SQL Server 2016 können Sie jetzt Analysen in Echtzeit für eine OLTP-Tabelle ausführen. Dies ist kein Ersatz für ein Data Warehouse zum Speichern und Analysieren von Daten, stellt aber eine Möglichkeit für die Analyse in Echtzeit bereit.
> 
> 

### <a name="reporting-and-analysis-queries"></a>Berichterstellung und Analyseabfragen
Berichterstellung und Analyseabfragen werden häufig in die Kategorien klein, mittel und groß unterteilt – basierend auf einer Reihe von Kriterien, aber in der Regel zeitbasiert. In den meisten Data Warehouse gibt es gemischte Workloads mit kurzen und lang andauernden Abfragen. In jedem Fall ist es wichtig, bestimmen diese Mischung und ihre Häufigkeit (stündlich, täglich, am Monatsende, am Quartalsende usw.) zu bestimmen. Es ist wichtig zu verstehen, dass gemischte Abfrageworkloads in Verbindung mit der Parallelität zu einer ordnungsgemäßen Kapazitätsplanung für ein Data Warehouse führt.

* Kapazitätsplanung kann für gemischte Abfrageworkloads eine komplexe Aufgabe sein, besonders wenn Sie eine längere Durchlaufzeit benötigen, um Kapazität zum Data Warehouse hinzuzufügen. Die Dringlichkeit der Kapazitätsplanung wird durch SQL Data Warehouse entfernt, da Sie die Compute-Kapazität jederzeit vergrößern und verkleinern können und da Speicher-und Compute-Kapazität unabhängig voneinander angepasst werden.

### <a name="data-management"></a>Datenverwaltung
Das Verwalten von Daten ist wichtig, besonders wenn Sie wissen, dass zukünftig möglicherweise nicht genügend Speicherplatz zur Verfügung steht. Data Warehouses unterteilen die Daten in der Regel in sinnvolle Bereiche, die als Partitionen in einer Tabelle gespeichert werden. Alle SQL Server-basierten Produkte ermöglichen Ihnen das Verschieben von Partitionen in die und aus der Tabelle. Durch diesen Partitionswechsel können Sie ältere Daten auf kostengünstigeren Speicher verschieben und die neuesten Daten auf dem Onlinespeicher verfügbar halten.

* ColumnStore-Indizes unterstützen partitionierte Tabellen. Für ColumnStore-Indizes werden partitionierte Tabellen für die Datenverwaltung und -archivierung verwendet. Bei zeilenweise gespeicherten Tabellen spielen Partitionen eine größere Rolle bei der Abfrageleistung.  
* PolyBase spielt eine wichtige Rolle bei der Verwaltung von Daten. Mit PolyBase können Sie ältere Daten in Hadoop oder Azure Blob Storage archivieren.  Dies bietet viele Möglichkeiten, da die Daten noch immer online sind.  Es dauert u. U. länger, Daten aus Hadoop abzurufen, aber der Nachteil der Abrufzeit gleicht möglicherweise die Speicherkosten aus.

### <a name="exporting-data"></a>Exportieren von Daten
Eine Möglichkeit, Daten für Berichte und Analysen verfügbar zu machen, ist das Senden von Daten aus dem Data Warehouse an Server, die speziell zum Ausführen von Berichten und Analysen verwendet werden. Diese Server werden als Data Marts bezeichnet. Beispielsweise könnten Sie Berichtsdaten vorab verarbeiten und dann aus dem Data Warehouse auf viele Server auf der ganzen Welt exportieren, um sie für Kunden und Analysten allgemein verfügbar zu machen.

* Zum Generieren von Berichten könnten Sie jede Nacht schreibgeschützte Berichtsserver mit einer Momentaufnahme der täglichen Daten füllen. Dies ermöglicht höhere Bandbreiten für Kunden bei gleichzeitiger Senkung der erforderlichen Compute-Ressourcen für das Data Warehouse. Vom Sicherheitsaspekt aus betrachtet ermöglichen Ihnen Data Marts das Reduzieren der Anzahl von Benutzern mit Zugriff auf das Data Warehouse.
* Für Analysten können Sie einen Analysecube im Data Warehouse erstellen und Analysen für das Data Warehouse ausführen oder Daten vorab verarbeiten und zur weiteren Analyse auf den Analyseserver exportieren.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie sich mit den Grundlagen von SQL Data Warehouse vertraut gemacht haben, können Sie sich nun darüber informieren, wie Sie schnell [eine SQL Data Warehouse-Instanz erstellen][create a SQL Data Warehouse] und [Beispieldaten][load sample data] laden.

<!--Image references-->

<!--Article references-->
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other web references-->
