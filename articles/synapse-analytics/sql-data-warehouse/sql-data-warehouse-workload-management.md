---
title: Verwalten von Arbeitsauslastungen
description: Leitfaden zum Implementieren der Workloadverwaltung in Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 761976741bf794a21182e8f962b274ae32925060
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324482"
---
# <a name="what-is-workload-management"></a>Worum geht es bei der Workloadverwaltung?

Das Ausführen gemischter Workloads kann bei ausgelasteten Systemen zu Ressourcenproblemen führen.  Lösungsarchitekten suchen nach Möglichkeiten zum Trennen klassischer Data Warehousing-Aktivitäten (z. B. Laden, Transformieren und Abfragen von Daten), um sicherzustellen, dass genügend Ressourcen zum Einhalten von SLAs vorhanden sind.  

Die Isolation physischer Server kann zu Bereichen in einer Infrastruktur führen, die nicht ausgelastet, überlastet oder in einem Zustand sind, in dem Caches ständig auf das Starten und Beenden von Hardware vorbereitet werden.  Mit einem erfolgreichen Verwaltungsschema für Workloads werden Ressourcen effektiv verwaltet, eine hochgradig effiziente Ressourcennutzung sichergestellt und die Rendite (ROI) maximiert.

Eine Data Warehouse-Workload bezieht sich auf alle Vorgänge, die in Bezug auf ein Data Warehouse ablaufen müssen. Die Tiefe und Breite dieser Komponenten hängt vom Reifegrad des Data Warehouse ab.  Die Data Warehouse-Workload umfasst Folgendes:

- Den gesamte Prozess des Ladens von Daten in das Warehouse
- Ausführen von Data Warehouse-Analyse und Berichterstellung
- Verwalten von Daten im Data Warehouse
- Exportieren von Daten aus dem Data Warehouse

Die Leistungskapazität eines Data Warehouse richtet sich nach den [Data Warehouse-Einheiten](what-is-a-data-warehouse-unit-dwu-cdwu.md).

- Informationen zum Anzeigen der zugeordneten Ressourcen für alle Leistungsprofile finden Sie unter [Speicher- und Parallelitätsgrenzwerte](memory-concurrency-limits.md).
- Für die Anpassung der Kapazität können Sie [hoch- oder herunterskalieren](quickstart-scale-compute-portal.md).

## <a name="workload-management-concepts"></a>Workloadverwaltungskonzepte

In der Vergangenheit haben Sie für Synapse SQL in Azure Synapse die Abfrageleistung über [Ressourcenklassen](resource-classes-for-workload-management.md) verwaltet.  Mithilfe von Ressourcenklassen konnte einer Abfrage Arbeitsspeicher basierend auf der Rollenmitgliedschaft zugewiesen werden.  Die größte Herausforderung bei Ressourcenklassen war, dass nach der Konfiguration keine Governance oder Fähigkeit zum Steuern der Workload bestand.  

Wenn Sie z.B. eine Ad-hoc-Benutzerrollenmitgliedschaft für „smallrc“ gewährten, konnte dieser Benutzer 100 % des Arbeitsspeichers im System belegen.  Bei Ressourcenklassen gibt es keine Möglichkeit, Ressourcen zu reservieren und sicherzustellen, dass Ressourcen für kritische Workloads verfügbar sind.

Die Workloadverwaltung für dedizierte SQL-Pools in Azure Synapse besteht aus drei grundlegenden Konzepten: [Workloadklassifizierung](sql-data-warehouse-workload-classification.md), [Workloadpriorität](sql-data-warehouse-workload-importance.md) und [Workloadisolation](sql-data-warehouse-workload-isolation.md).  Mit diesen Funktionen können Sie besser steuern, wie Systemressourcen von der Workload genutzt werden.

Die Workloadklassifizierung ist das Konzept, bei dem eine Anforderung einer Arbeitsauslastungsgruppe zugewiesen wird und Prioritätsstufen festgelegt werden.  Bisher erfolgte diese Zuweisung über die Rollenmitgliedschaft mithilfe von [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Diese Aktion ist nun über [CREATE WORKLOAD CLASSIFER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) möglich.  Die Klassifizierungsfunktion bietet einen umfassenderen Satz von Optionen, wie z.B. Bezeichnung, Sitzung und Zeit zum Klassifizieren von Anforderungen.

Die Workloadpriorität wirkt sich auf die Reihenfolge aus, in der eine Anforderung Zugriff auf Ressourcen erhält.  Auf einem ausgelasteten System hat eine Anforderung mit höherer Priorität zuerst Zugriff auf Ressourcen.  Durch die Priorität kann auch der geordnete Zugriff auf Sperren sichergestellt werden.

Mit der Workloadisolation werden Ressourcen für eine Arbeitsauslastungsgruppe reserviert.  Ressourcen, die in einer Arbeitsauslastungsgruppe reserviert sind, werden ausschließlich für diese Arbeitsauslastungsgruppe bereitgehalten, um die Ausführung zu gewährleisten.  Mithilfe von Arbeitsauslastungsgruppen können Sie auch die Menge der Ressourcen definieren, die pro Anforderung zugewiesen werden, ähnlich wie bei Ressourcenklassen.  Arbeitsauslastungsgruppen bieten Ihnen die Möglichkeit, die Menge an Ressourcen zu reservieren oder zu begrenzen, die von einer Gruppe von Anforderungen belegt werden können.  Außerdem stellen Arbeitsauslastungsgruppen einen Mechanismus zum Anwenden von Regeln (z.B. Abfragetimeout) auf Anforderungen dar.  

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Workloadklassifizierung finden Sie unter [Workloadklassifizierung](sql-data-warehouse-workload-classification.md).  
- Weitere Informationen zur Workloadisolation finden Sie unter [Workloadisolation](sql-data-warehouse-workload-isolation.md).  
- Weitere Informationen zur Workloadpriorität finden Sie unter [Workloadpriorität](sql-data-warehouse-workload-importance.md).  
- Weitere Informationen zur Überwachung der Workloadverwaltung finden Sie unter [Überwachung über das Workload-Verwaltungsportal](sql-data-warehouse-workload-management-portal-monitor.md).  
