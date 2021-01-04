---
title: Konvertieren einer Ressourcenklasse in eine Arbeitsauslastungsgruppe
description: Erfahren Sie, wie Sie eine Workloadgruppe erstellen, die einer Ressourcenklasse in einem dedizierten SQL-Pool ähnelt.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b82342ffb76f8bb58b8f6875751601094d6131ca
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461896"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Konvertieren von Ressourcenklassen in Arbeitsauslastungsgruppen

Arbeitsauslastungsgruppen bieten einen Mechanismus zum Isolieren und Kapseln von Systemressourcen.  Außerdem können Sie mit Arbeitsauslastungsgruppen Ausführungsregeln für darin ausgeführte Anforderungen festlegen.  Mit einer Ausführungsregel für Abfragetimeout können Endlosabfragen ohne Benutzereingriff abgebrochen werden.  In diesem Artikel wird erläutert, wie anhand einer vorhandenen Ressourcenklasse eine Arbeitsauslastungsgruppe mit einer ähnlichen Konfiguration erstellt werden kann.  Außerdem wird eine optionale Regel für das Abfragetimeout hinzugefügt.

> [!NOTE]
> Der Abschnitt [Kombinieren von Ressourcenklassenzuweisungen mit Klassifizierungen](sql-data-warehouse-workload-classification.md#mixing-resource-class-assignments-with-classifiers) im Konzeptdokument [Klassifizierung der Arbeitsauslastung](sql-data-warehouse-workload-classification.md) enthält eine Anleitung zum gleichzeitigen Verwenden von Arbeitsauslastungsgruppen und Ressourcenklassen.

## <a name="understanding-the-existing-resource-class-configuration"></a>Grundlegendes zur vorhandenen Ressourcenklassenkonfiguration

Für Arbeitsauslastungsgruppen ist ein Parameter namens `REQUEST_MIN_RESOURCE_GRANT_PERCENT` erforderlich, der den Prozentsatz der Gesamtsystemressourcen angibt, die pro Anforderung zugeordnet werden.  Die Ressourcenzuordnung erfolgt für [Ressourcenklassen](resource-classes-for-workload-management.md#what-are-resource-classes) durch Zuordnen von Parallelitätsslots.  Um den für `REQUEST_MIN_RESOURCE_GRANT_PERCENT` anzugebenden Wert zu ermitteln, verwenden Sie die DMV „sys.dm_workload_management_workload_groups_stats“ <link tbd>.  Beispielsweise gibt die folgende Abfrage einen Wert zurück, der für den Parameter `REQUEST_MIN_RESOURCE_GRANT_PERCENT` verwendet werden kann, um eine Arbeitsauslastungsgruppe ähnlich wie „staticrc40“ zu erstellen.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Arbeitsauslastungsgruppen werden basierend auf dem Prozentsatz der Gesamtsystemressourcen ausgeführt.  

Da Arbeitsauslastungsgruppen basierend auf dem Prozentsatz der Gesamtsystemressourcen ausgeführt werden, ändert sich beim Hoch- und Herunterskalieren der Prozentsatz der Ressourcen, die statischen Ressourcenklassen zugeordnet sind, im Verhältnis zu den Gesamtsystemressourcen.  Beispielsweise werden mit „staticrc40“ bei DW1000c 19,2 % der Gesamtsystemressourcen zugewiesen.  Bei DW2000c werden 9,6 % zugewiesen.  Dieses Modell funktioniert ähnlich, wenn Sie für Parallelität hochskalieren möchten, anstatt mehr Ressourcen pro Anforderung zuzuweisen.

## <a name="create-workload-group"></a>Erstellen einer Arbeitsauslastungsgruppe

Wenn der Parameter `REQUEST_MIN_RESOURCE_GRANT_PERCENT` bekannt ist, können Sie die CREATE WORKLOAD GROUP<link>-Syntax zum Erstellen der Arbeitsauslastungsgruppe verwenden.  Sie können optional einen Wert größer als 0 (null) für `MIN_PERCENTAGE_RESOURCE` angeben, um Ressourcen für die Arbeitsauslastungsgruppe zu isolieren.  Außerdem können Sie optional einen Wert kleiner als 100 für `CAP_PERCENTAGE_RESOURCE` angeben, um die Menge an Ressourcen einzuschränken, die von der Arbeitsauslastungsgruppe genutzt werden kann.  

Unter Verwendung von mediumrc als Basis für ein Beispiel wird im untenstehenden Code `MIN_PERCENTAGE_RESOURCE` so festgelegt, dass zehn Prozent der Systemressourcen für `wgDataLoads` dediziert sind. Außerdem wird sichergestellt, dass eine Abfrage jederzeit ausgeführt werden kann.  Außerdem wird `CAP_PERCENTAGE_RESOURCE` auf 40 Prozent festgelegt und diese Arbeitsauslastungsgruppe auf vier gleichzeitige Anforderungen beschränkt.  Durch Festlegen des Parameters `QUERY_EXECUTION_TIMEOUT_SEC` auf 3600 werden alle Abfragen, die länger als eine Stunde ausgeführt werden, automatisch abgebrochen.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 10
 ,MIN_PERCENTAGE_RESOURCE = 10
 ,CAP_PERCENTAGE_RESOURCE = 40
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Erstellen des Klassifizierers

Zuvor erfolgte die Zuordnung von Abfragen zu Ressourcenklassen über [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Um dieselbe Funktionalität zu erreichen und Arbeitsauslastungsgruppen Anforderungen zuzuordnen, verwenden Sie die Syntax [CREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Mit „sp_addrolemember“ konnten Sie einer Anforderung nur Ressourcen basierend auf einer Anmeldung zuordnen.  Ein Klassifizierer bietet neben der Anmeldung noch weitere Optionen. Dies sind:
    - label
    - session
    - time. Im folgenden Beispiel werden Abfragen von der Anmeldung `AdfLogin` zugewiesen, bei denen außerdem die [Optionsbezeichnung](sql-data-warehouse-develop-label.md) auf `factloads` für die oben erstellte Arbeitsauslastungsgruppe `wgDataLoads` festgelegt ist.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>Testen mit einer Beispielabfrage

Nachfolgend ist eine Beispielabfrage und eine DMV-Abfrage angegeben, mit denen sichergestellt wird, dass die Arbeitsauslastungsgruppe und der Klassifizierer richtig konfiguriert sind.

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 *
  FROM nation
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command
  FROM sys.dm_pdw_exec_requests
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>Nächste Schritte

- [Workloadisolation](sql-data-warehouse-workload-isolation.md)
- [Gewusst wie: Erstellen von Arbeitsauslastungsgruppen](quickstart-configure-workload-isolation-tsql.md)
- [CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?&view=azure-sqldw-latest)
- [CREATE WORKLOAD GROUP (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
