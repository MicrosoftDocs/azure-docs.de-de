---
title: Überwachen und Verbessern der Leistung in Azure SQL-Datenbank | Microsoft-Dokumentation
description: Die Azure SQL-Datenbank bietet Leistungstools, mit denen Sie Bereiche identifizieren können, in denen die aktuelle Abfrageleistung verbessert werden kann.
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: adbccd5f9cfd5ddd1912e304f800f3ebe04912c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60584799"
---
# <a name="monitor-and-improve-performance"></a>Überwachen und Verbessern der Leistung

Azure SQL-Datenbank stellt intelligente Aktionen zur Optimierung und Empfehlungen bereit, mit denen potenzielle Probleme in Ihrer Datenbank identifiziert werden und Aktionen zur Leistungsverbesserung Ihrer Workload vorgeschlagen werden.

Verwenden Sie die Kachel **Leistung** auf der Seite „Übersicht“, um die Leistung Ihrer Datenbank zu überprüfen, oder navigieren Sie zum Abschnitt „Support und Problembehandlung“ herunter:

   ![Anzeigen der Leistung](./media/sql-database-performance/entries.png)

Im Abschnitt „Support und Problembehandlung“ können Sie die folgenden Seiten verwenden:


1. [Leistungsübersicht](#performance-overview) zum Überwachen der Leistung Ihrer Datenbank. 
2. [Empfehlungen zur Leistung](#performance-recommendations) zum Suchen von Empfehlungen, die die Leistung Ihrer Workload verbessern können.
3. [Statistik zur Abfrageleistung](#query-performance-insight) zum Identifizieren der Abfragen mit dem größten Ressourcenverbrauch.
4. [Automatische Optimierung](#automatic-tuning) zum Aktivieren der automatischen Optimierung Ihrer Datenbank durch Azure SQL-Datenbank.

## <a name="performance-overview"></a>Leistungsübersicht

Diese Ansicht bietet eine Übersicht über die Leistung der Datenbank und unterstützt Sie bei der Leistungsoptimierung und Fehlerbehebung. 

![Leistung](./media/sql-database-performance/performance.png)

* Die Kachel **Empfehlungen** bietet eine Aufschlüsselung der Optimierungsempfehlungen für Ihre Datenbank (bei mehreren Empfehlungen werden die wichtigsten drei angezeigt). Durch Klicken auf diese Kachel gelangen Sie zu **[Empfehlungen zur Leistung](#performance-recommendations)** . 
* Die Kachel **Optimierungsaktivität** bietet eine Zusammenfassung der laufenden und abgeschlossenen Optimierungsaktionen für die Datenbank, sodass Sie einen schnellen Überblick über den Verlauf der Optimierungsaktivitäten erhalten. Wenn Sie auf diese Kachel klicken, gelangen Sie zur Ansicht des vollständigen Optimierungsverlaufs für Ihre Datenbank.
* Die Kachel **Automatische Optimierung** zeigt die Konfiguration der [automatischen Optimierung für Ihre Datenbank](sql-database-automatic-tuning-enable.md) an (die Optimierungsaktionen, die automatisch auf die Datenbank angewendet werden). Durch Klicken auf diese Kachel öffnen Sie das Dialogfeld zur Automatisierungskonfiguration.
* Die Kachel **Datenbankabfragen** zeigt die Zusammenfassung der Abfrageleistung für Ihre Datenbank (DTU-Nutzung insgesamt und die Abfragen mit dem größten Ressourcenverbrauch). Durch Klicken auf diese Kachel gelangen Sie zur **[Statistik zur Abfrageleistung](#query-performance-insight)** .

## <a name="performance-recommendations"></a>Empfehlungen zur Leistung

Diese Seite bietet intelligente [Empfehlungen zur Optimierung](sql-database-advisor.md), mit denen Sie die Leistung Ihrer Datenbank verbessern können. Auf dieser Seite finden Sie die folgenden Arten von Empfehlungen:

* Empfehlungen, welche Indizes erstellt oder abgelegt werden sollen.
* Empfehlungen bei Schemaproblemen in der Datenbank.
* Empfehlungen, wenn Abfragen von parametrisierten Abfragen profitieren können.

![Leistung](./media/sql-database-performance/recommendations.png)

Sie können auch den vollständigen Verlauf der Aktionen zur Leistungsoptimierung suchen, die in der Vergangenheit angewendet wurden.

Im Artikel [Find and apply performance recommendations (Suchen und Anwenden von Empfehlungen zur Leistung)](sql-database-advisor-portal.md) finden Sie Informationen zur Anwendung von Empfehlungen zur Leistung.

## <a name="automatic-tuning"></a>Automatische Optimierung

Azure SQL-Datenbanken können automatisch die Leistung der Datenbank optimieren, indem sie [Empfehlungen zur Leistung](sql-database-advisor.md) anwenden. Weitere Informationen finden Sie unter [Automatische Optimierung](sql-database-automatic-tuning.md). Lesen Sie zum Aktivieren den Artikel [Aktivieren der automatischen Optimierung](sql-database-automatic-tuning-enable.md).

## <a name="query-performance-insight"></a>Query Performance Insight

Mithilfe von [Query Performance Insight](sql-database-query-performance.md) können Sie den Zeitaufwand für die Problembehandlung der Datenbankleistung reduzieren. Dieses Tool stellt Folgendes bereit:

* Tiefere Einblicke in den Verbrauch von Datenbankressourcen (DTU). 
* Informationen zu Abfragen mit der höchsten CPU-Auslastung, für die unter Umständen eine Leistungssteigerung erzielt werden kann. 
* Die Möglichkeit zum Durchführen eines Drilldowns in die Details einer Abfrage.​ 

  ![Leistungsdashboard](./media/sql-database-query-performance/performance.png)

Weitere Informationen zu dieser Seite finden Sie im Artikel **[How to use Query Performance Insight (Anleitung zur Verwendung der Statistik zur Abfrageleistung)](sql-database-query-performance.md)** .

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Leitfaden zur Azure SQL-Datenbankleistung für Einzeldatenbanken](sql-database-performance-guidance.md)
* [Wann sollte ein Pool für elastische Datenbanken verwendet werden?](sql-database-elastic-pool-guidance.md)

