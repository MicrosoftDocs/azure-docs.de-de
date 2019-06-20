---
title: SQL Data Warehouse-Empfehlungen – Konzepte | Microsoft-Dokumentation
description: Erfahren Sie mehr über SQL Data Warehouse-Empfehlungen und wie sie erzeugt werden
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/05/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b275f23209979e1a8068ecd99465f7b52392bc6c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61421221"
---
# <a name="sql-data-warehouse-recommendations"></a>SQL Data Warehouse-Empfehlungen

Dieser Artikel beschreibt die Empfehlungen, die SQL Data Warehouse mit dem Azure Advisor unterbreitet.  

SQL Data Warehouse bietet Empfehlungen, um sicherzustellen, dass Ihr Data Warehouse konsistent für Leistung optimiert ist. Data Warehouse-Empfehlungen sind eng in den [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) integriert, um Sie direkt im [Azure-Portal](https://aka.ms/Azureadvisor) mit bewährten Methoden vertraut zu machen. Auf täglicher Basis analysiert SQL Data Warehouse den aktuellen Zustand Ihres Data Warehouse, sammelt Telemetriedaten und unterbreitet Empfehlungen für Ihre aktive Workload. Die unterstützten Data Warehouse-Empfehlungsszenarien werden im Folgenden zusammen mit der Anwendung empfohlener Aktionen beschrieben.

Wenn Sie uns Feedback zum SQL Data Warehouse Advisor geben möchten oder Probleme auftreten, wenden Sie sich an [sqldwadvisor@service.microsoft.com](mailto:sqldwadvisor@service.microsoft.com).   

Klicken Sie [hier](https://aka.ms/Azureadvisor), um zu erfahren, welche Empfehlungen Sie heute erhalten! Dieses Feature ist zurzeit nur auf Gen2-Data Warehouses anwendbar. 

## <a name="data-skew"></a>Datenschiefe

Datenschiefe kann eine zusätzliche Datenverschiebung oder Ressourcenengpässe beim Ausführen Ihrer Workload verursachen. Die folgende Dokumentation beschreibt, wie Datenschiefe identifiziert und ihr Auftreten durch Auswahl eines Schlüssels für die optimale Verteilung verhindert wird.

- [Identifizieren und Entfernen von Datenschiefe](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Keine oder veraltete Statistiken

Suboptimale Statistiken können die Abfrageleistung erheblich beeinträchtigen, weil sie dazu führen können, dass der Abfrageoptimierer von SQL Data Warehouse suboptimale Abfragepläne generiert. Die folgende Dokumentation beschreibt die bewährten Methoden zum Erstellen und Aktualisieren von Statistiken:

- [Erstellen und Aktualisieren von Tabellenstatistiken](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

Führen Sie zum Anzeigen der Liste der Tabellen, auf die sich diese Empfehlungen auswirken, [dieses T-SQL-Skript](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables) aus. Der Advisor führt kontinuierlich das gleiche T-SQL-Skript aus, um diese Empfehlungen zu generieren.

## <a name="replicate-tables"></a>Replizieren von Tabellen

Zur Generierung von Empfehlungen für replizierte Tabellen ermittelt der Advisor Tabellenkandidaten auf der Grundlage folgender physischer Merkmale:

- Größe der replizierten Tabelle
- Anzahl von Spalten
- Tabellenverteilungstyp
- Anzahl von Partitionen

Der Advisor nutzt kontinuierlich workloadbasierte Heuristik wie etwa Tabellenzugriffshäufigkeit, durchschnittlich zurückgegebene Zeilen sowie Schwellenwerte für Data Warehouse-Größe und -Aktivität, um hochwertige Empfehlungen zu generieren. 

Im Anschluss wird die workloadbasierte Heuristik beschrieben, die ggf. im Azure-Portal für die einzelnen Empfehlungen für replizierte Tabellen verfügbar ist:

- Scan avg (Scandurchschnitt): Der durchschnittliche Prozentsatz von Zeilen, die aus der Tabelle zurückgegeben wurden (für jeden Tabellenzugriff in den letzten sieben Tagen).
- Frequent read, no update (Häufig lesen, keine Aktualisierung): Gibt an, dass die Tabelle in den letzten sieben Tagen nicht aktualisiert wurde, aber Zugriffsaktivitäten festgestellt wurden.
- Read/update ratio (Verhältnis zwischen Lesen und Aktualisieren): Das Verhältnis zwischen Zugriffs- und Aktualisierungsvorgängen für die Tabelle in den letzten sieben Tagen.
- Aktivität: Ermittelt die Nutzung anhand der Zugriffsaktivität. Die Tabellenzugriffsaktivität wird dabei mit der durchschnittlichen Tabellenzugriffsaktivität innerhalb des gesamten Data Warehouse während der letzten sieben Tage verglichen. 

Derzeit zeigt der Advisor maximal vier Kandidaten für replizierte Tabellen auf einmal an – mit gruppierten Columnstore-Indizes und unter Priorisierung der höchsten Aktivität.

> [!IMPORTANT]
> Die Empfehlung für replizierte Tabellen ist nicht narrensicher und berücksichtigt keine Datenverschiebungsvorgänge. Wir arbeiten daran, dies als Heuristik hinzuzufügen, bis dahin empfiehlt es sich jedoch, die Workload nach Anwendung der Empfehlung zu überprüfen. Wenden Sie sich an sqldwadvisor@service.microsoft.com, wenn Ihnen Empfehlungen für replizierte Tabellen auffallen, die zu einer Verschlechterung Ihrer Workload führen. Weitere Informationen zu replizierten Tabellen finden Sie in [dieser Dokumentation](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table).
