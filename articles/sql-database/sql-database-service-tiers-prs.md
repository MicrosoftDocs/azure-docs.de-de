---
title: Einstellung der Premium RS-Dienstebene in Azure SQL-Datenbank | Microsoft-Dokumentation
description: Die Premium RS-Dienstebene wird eingestellt und ihr Support beendet (siehe Migrationsoptionen).
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 7f184178343f69f522148777752c51afc5c5dcb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65790405"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Einstellung der Premium RS-Dienstebene (Vorschau) in Azure SQL-Datenbank – Optionen für die Migration

Im Februar 2018 kündigte Microsoft an, dass die Premium RS-Dienstebene in Azure SQL-Datenbank nicht für die allgemeine Verfügbarkeit freigegeben und nach dem 31. Januar 2019 nicht mehr unterstützt würde. Dieser Termin für das Supportende wurde bis zum 30. Juni 2019 verlängert. In diesem Artikel werden die Optionen für eine Migration von der Premium RS-Dienstebene zu einer anderen Dienstebene erläutert. Nach dem 30. Juni 2019 migriert Microsoft Ihre Premium RS-Datenbanken automatisch zu einer allgemein verfügbaren Dienstebene, die den Leistungsanforderungen Ihrer Premium RS-Datenbank am ehesten entspricht.

Nachfolgend finden Sie eine Auflistung der Migrationsziele und Preisoptionen, die für Premium RS-Kunden geeignet sein können:

- V-Kern-Dienstebenen

  Die Dienstebenen **Universell** und **Unternehmenskritisch** im [vCore-basierten Kaufmodell](sql-database-service-tiers-vcore.md). Diese beiden Dienstebenen sind allgemein verfügbar. Das vCore-basierte Kaufmodell umfasst auch die Dienstebene **Hyperscale**, die eine bedarfsgerechte Anpassung entsprechend den Anforderungen Ihrer Workload mit automatischer Skalierung auf bis zu 100 TB pro Datenbank ermöglicht. Die Hyperscale-Dienstebene bietet E/A-Leistung vergleichbar mit der Premium-Dienstebene im [DTU-basierten Kaufmodell](sql-database-service-tiers-dtu.md) zu einem Preis, der eher der Premium RS-Dienstebene ähnelt.
- Preise für Dev/Test

  [Preise für Dev/Test](https://azure.microsoft.com/pricing/dev-test/) bieten Einsparungen von bis zu 55 % gegenüber Tarifen mit enthaltenen Lizenzen für Ihr Visual Studio-Abonnement.
- Azure-Hybridvorteil und Preise für reservierte Kapazität

  [Azure-Hybridvorteil und Preise für reservierte Kapazität](https://azure.microsoft.com/pricing/details/sql-database/) bieten Einsparungen von bis zu 80 % gegenüber Tarifen mit enthaltenen Lizenzen. Weitere Informationen zu diesen Optionen finden Sie unter [Azure-Hybridvorteil für SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) und [Reservierte Azure SQL-Datenbank-Kapazität](sql-database-reserved-capacity.md).

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Nehmen Sie jetzt eine Migration Ihrer Premium RS-Datenbanken zu alternativen SQL-Datenbank-Dienstebenen vor

Sehen Sie sich die Informationen in diesem Artikel zusammen mit unseren Preismodellen und der Dokumentation an, um die richtigen Migrationsziele für Ihre Premium RS-Workloads zu ermitteln.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Migrieren und Sparen bei rechenintensiven Workloads

Für Ihre rechenintensiven Premium RS-Workloads wird eine Migration zu unserer allgemein verfügbaren V-Kern-basierten Dienstebene „Universell“ empfohlen, die durch Angebote mit Azure-Hybridvorteil für SQL Server und reservierter Kapazität größere Einsparungen gegenüber Tarifen mit enthaltenen Lizenzen bietet. Wenn Sie stattdessen bei einer DTU-basierten Kaufoption bleiben möchten, können Sie Ihre rechenintensiven Premium RS-Datenbanken zu einer Standard-Dienstebene migrieren und dennoch gegenüber den Preismodellen für Premium RS mit allgemeiner Verfügbarkeit (wenn die Phase der allgemeinen Verfügbarkeit erreicht ist) Einsparungen erzielen.

> [!WARNING]
> Durch das Migrieren Ihrer Premium RS-Workloads zu DTU-basierten Premium-Dienstebenen können sich die monatlichen Kosten gegenüber den aktuellen Premium RS-Preismodellen erhöhen. Es wird empfohlen, die Dienstebene „Hyperscale“ oder „Unternehmenskritisch“ mit Azure-Hybridvorteil und Preisen für reservierte Kapazität in Betracht zu ziehen, um vergleichbare oder niedrigere Kosten als bei Premium RS zu erzielen.

### <a name="premium-rs-databases"></a>Premium RS-Datenbanken

|**Derzeitige Dienstebene**|**Migration zu vergleichbarer V-Kern-basierter Dienstebene**|**Migration zu vergleichbarer DTU-basierter Dienstebene**|
|---|---|---|
|Premium RS 1|Universell, 1 virtueller Kern (Gen 4)|Standard 3|
|Premium RS 2|Universell, 2 virtuelle Kerne (Gen 4)|Standard 4|
|Premium RS 4|Universell, 4 virtuelle Kerne (Gen 4)|Standard 6|
|Premium RS 6|Universell, 6 virtuelle Kerne (Gen 4)|Standard 7|

### <a name="premium-rs-pools"></a>Premium RS-Pools

|**Derzeitige Dienstebene**|**Migration zu vergleichbarer V-Kern-basierter Dienstebene**|**Migration zu vergleichbarer DTU-basierter Dienstebene**|
|---|---|---|
|Premium RS-Pool, 125 DTUs|Universell, 1 virtueller Kern (Gen 4)|Standard-Pool, 100 eDTUs|
|Premium RS-Pool, 250 DTUs|Universell, 2 virtuelle Kerne (Gen 4)|Standard-Pool, 250 eDTUs|
|Premium RS-Pool, 500 DTUs|Universell, 4 virtuelle Kerne (Gen 4)|Standard-Pool, 500 eDTUs|
|Premium RS-Pool, 1000 DTUs|Universell, 8 virtuelle Kerne (Gen 4)|Standard-Pool, 1000 eDTUs|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Optimieren von Einsparungen und Leistung für E/A-intensive Workloads

Es wird empfohlen, E/A-intensive Einzeldatenbanken zur V-Kern-basierten Dienstebene „Hyperscale“ (derzeit in der Vorschauphase) und E/A-intensive Datenbankpools zur allgemein verfügbaren Dienstebene „Unternehmenskritisch“ zu migrieren, um eine optimale Kombination von Leistung und Kosten zu erzielen.  Mit den folgenden V-Kern-basierten Optionen wird die aktuelle Leistung beibehalten oder verbessert, und in Kombination mit dem Azure-Hybridvorteil und Preisen für reservierte Kapazität können Kosten gespart werden.

|**Derzeitige Dienstebene**|**Migration zu vergleichbarer V-Kern-basierter Dienstebene**|**Migration zu vergleichbarer DTU-basierter Dienstebene**|
|---|---|---|
|Premium RS 1| Hyperscale, 1 virtueller Kern (Gen 4) oder Unternehmenskritisch, 1 virtueller Kern (Gen 4)|Premium 1|
|Premium RS 2| Hyperscale, 2 virtuelle Kerne (Gen 4) oder Unternehmenskritisch, 2 virtuelle Kerne (Gen 4)|Premium 2|
|Premium RS 4| Hyperscale, 4 virtuelle Kerne (Gen 4) oder Unternehmenskritisch, 4 virtuelle Kerne (Gen 4)|Premium 4
|Premium RS 6| Hyperscale, 6 virtuelle Kerne (Gen 4) oder Unternehmenskritisch, 6 virtuelle Kerne (Gen 4)|Premium 6|

|**Derzeitige Dienstebene**|**Migration zu vergleichbarer V-Kern-basierter Dienstebene**|**Migration zu vergleichbarer DTU-basierter Dienstebene**|
|---|---|---|
|Premium RS-Pool, 125 DTUs|Unternehmenskritisch, 2 virtuelle Kerne (Gen 4)|Premium-Pool, 125 eDTUs|
|Premium RS-Pool, 250 DTUs|Unternehmenskritisch, 2 virtuelle Kerne (Gen 4)|Premium-Pool, 250 eDTUs|
|Premium RS-Pool, 500 DTUs|Unternehmenskritisch, 4 virtuelle Kerne (Gen 4)|Premium-Pool, 500 eDTUs|
|Premium RS-Pool, 1000 DTUs|Unternehmenskritisch, 8 virtuelle Kerne (Gen 4)|Premium-Pool, 1000 eDTUs|

## <a name="take-advantage-of-our-new-offers"></a>Nutzen Sie unsere neuen Angebote

Für unsere Dienstebenen im vCore-basierten Kaufmodell gelten spezielle Angebote, mit denen Sie bis zu 80 Prozent gegenüber Preismodellen mit enthaltenen Lizenzen sparen können. Verwenden Sie Ihre Lizenzen für die SQL Server Standard oder Enterprise Edition mit aktiver Software Assurance, um mit dem [Azure-Hybridvorteil für SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) bis zu 55 % gegenüber Preismodellen mit enthaltenen Lizenzen zu sparen. Sie können den Hybridvorteil mit Preisen für [reservierte Azure SQL-Datenbank-Kapazität](sql-database-reserved-capacity.md) kombinieren und bis zu 80 % sparen, wenn Sie sich im Voraus für einen Zeitraum von einem oder drei Jahren festlegen.  Aktivieren Sie beide Vorteile noch heute über das Azure-Portal.

Wenn Sie Fragen oder Bedenken bezüglich dieser Änderung haben oder Unterstützung bei der Migration benötigen, wenden Sie sich an [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Migration von einer Premium RS-Dienstebene zu einer Dienstebene im DTU- oder V-Kern-Modell

### <a name="migration-of-a-database"></a>Migrieren einer Datenbank

Das Migrieren einer Datenbank von einer Premium RS-Dienstebene zu einer Dienstebene im DTU- oder V-Kern-Modell ähnelt einem Upgrade oder Downgrade zwischen Dienstebenen in der Premium RS-Dienstebene.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Verwenden einer Datenbankkopie zum Konvertieren einer Premium RS-Datenbank in eine DTU-basierte oder V-Kern-basierte Datenbank

Sie können eine beliebige Datenbank mit einer Premium RS-Computegröße in eine Datenbank mit einer DTU-basierten oder V-Kern-basierten Computegröße kopieren, ohne dass hierfür Einschränkungen oder spezielle Sequenzierungen gelten, solange die Zielcomputegröße die maximale Datenbankgröße der Quelldatenbank unterstützt. Die Datenbankkopie erstellt eine Momentaufnahme der Daten zum Startzeitpunkt des Kopiervorgangs und führt keine Datensynchronisierung zwischen Quelle und Ziel durch.

## <a name="next-steps"></a>Nächste Schritte

- Ausführliche Informationen zu bestimmten Computegrößen und Speichergrößen für Einzeldatenbanken finden Sie unter [V-Kern-basierte Ressourceneinschränkungen in SQL-Datenbank für Einzeldatenbanken](sql-database-vcore-resource-limits-single-databases.md).
- Ausführliche Informationen zu Auswahlmöglichkeiten bestimmter Compute- und Speichergrößen für Pools für elastische Datenbanken finden Sie unter [V-Kern-basierte Ressourceneinschränkungen in SQL-Datenbank für Pools für elastische Datenbanken](sql-database-vcore-resource-limits-elastic-pools.md).
