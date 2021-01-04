---
title: Modell mit gemeinsam genutzten Metadaten
description: Azure Synapse Analytics ermöglicht den verschiedenen Berechnungsengines von Arbeitsbereichen die gemeinsame Nutzung von Datenbanken und Tabellen zwischen serverlosen Apache Spark-Pools, dem serverlosen SQL-Pool und dedizierten SQL-Pools.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: b10b6f011fa7daee4094f0cc7b819d36127fedcd
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460352"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Azure Synapse Analytics: Gemeinsam genutzte Metadaten

Azure Synapse Analytics ermöglicht den verschiedenen Berechnungsengines von Arbeitsbereichen die gemeinsame Nutzung von Datenbanken und Tabellen zwischen serverlosen Apache Spark-Pools und dem serverlosen SQL-Pool.

Die gemeinsame Nutzung unterstützt das sogenannte moderne Data Warehouse und ermöglicht den Arbeitsbereich-SQL-Engines den Zugriff auf mit Spark erstellte Datenbanken und Tabellen. Außerdem ermöglicht es den SQL-Engines die Erstellung eigener Objekte, die nicht gemeinsam mit anderen Engines genutzt werden.

## <a name="support-the-modern-data-warehouse"></a>Unterstützen des modernen Data Warehouse

Das Modell mit gemeinsam genutzten Metadaten unterstützt das moderne Data Warehouse wie folgt:

1. Daten aus dem Data Lake werden mit Spark effizient aufbereitet und strukturiert. Hierzu werden die aufbereiteten Daten in (ggf. partitionierten) Parquet-basierten Tabellen gespeichert, die in mehreren Datenbanken enthalten sein können.

2. Die von Spark erstellten Datenbanken und alle zugehörigen Tabellen sind in allen Spark-Poolinstanzen des Azure Synapse-Arbeitsbereichs sichtbar und können von beliebigen Spark-Aufträgen verwendet werden. Diese Funktion ist abhängig von den [Berechtigungen](#security-model-at-a-glance), da allen Spark-Pools in einem Arbeitsbereich der gleiche Katalogmetaspeicher zugrunde liegt.

3. Die von Spark erstellten Datenbanken und ihre Parquet-basierten Tabellen werden im serverlosen SQL-Pool des Arbeitsbereichs sichtbar. [Datenbanken](database.md) werden automatisch in den Metadaten des serverlosen SQL-Pools erstellt, und [sowohl die externen als auch die verwalteten Tabellen](table.md), die von einem Spark-Auftrag erstellt werden, werden als externe Tabellen in den Metadaten des serverlosen SQL-Pools im Schema `dbo` der entsprechenden Datenbank zugänglich gemacht. 

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

Die Objektsynchronisierung erfolgt asynchron. Daher dauert es ein paar Sekunden, bis Objekte im SQL-Kontext angezeigt werden. Sobald sie angezeigt werden, können sie von SQL-Engines mit entsprechendem Zugriff abgefragt (aber nicht aktualisiert oder geändert) werden.

## <a name="shared-metadata-objects"></a>Freigegebene Metadatenobjekte

Spark ermöglicht das Erstellen von Datenbanken, externen Tabellen, verwalteten Tabellen und Sichten. Da Spark-Sichten eine Spark-Engine für die Verarbeitung der definierenden Spark-SQL-Anweisung erfordern und nicht von einer SQL-Engine verarbeitet werden können, werden nur Datenbanken und die darin enthaltenen externen und verwalteten Tabellen im Parquet-Speicherformat gemeinsam mit der SQL-Engine des Arbeitsbereichs genutzt. Spark-Sichten werden nur von den Spark-Poolinstanzen gemeinsam genutzt.

## <a name="security-model-at-a-glance"></a>Sicherheitsmodell auf einen Blick

Die Spark-Datenbanken und -Tabellen sowie deren synchronisierte Darstellungen in der SQL-Engine werden auf der zugrunde liegenden Speicherebene geschützt. Wenn die Tabelle von einer der Engines abgefragt wird, zu deren Verwendung der Abfrageübermittler berechtigt ist, wird der Sicherheitsprinzipal des Abfrageübermittlers an die zugrunde liegenden Dateien übergeben. Die Berechtigungen werden auf der Dateisystemebene überprüft.

Weitere Informationen finden Sie unter [Gemeinsam genutzte Azure Synapse Analytics-Datenbank](database.md).

## <a name="change-maintenance"></a>Änderungsverwaltung

Wenn ein Metadatenobjekt mit Spark gelöscht oder geändert wird, werden die Änderungen übernommen und an den serverlosen SQL-Pool weitergegeben. Da die Synchronisierung asynchron erfolgt, kommt es bei der Implementierung der Änderungen in der SQL-Engine zu einer kurzen Verzögerung.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu gemeinsam genutzten Azure Synapse Analytics-Datenbanken finden Sie [hier](database.md).
- Weitere Informationen zu gemeinsam genutzten Metadatentabellen von Azure Synapse Analytics finden Sie [hier](table.md).

