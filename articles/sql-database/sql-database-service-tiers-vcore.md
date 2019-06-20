---
title: Azure SQL-Datenbank-Dienst – virtueller Kern | Microsoft-Dokumentation
description: Mit dem V-Kern-basierten Kaufmodell können Sie Compute- und Speicherressourcen einzeln skalieren, eine Leistung wie in Ihrer lokalen Umgebung erzielen und den Preis optimieren.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 05/06/2019
ms.openlocfilehash: 520dba611e6791fca990e21173424a914c3d8e14
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693336"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>Auswählen einer V-Kern-Dienstebene und Migrieren von DTU-Dienstebenen

Mit dem V-Kern-basierten Kaufmodell können Sie Compute- und Speicherressourcen einzeln skalieren, eine Leistung wie in Ihrer lokalen Umgebung erzielen und den Preis optimieren. Darüber hinaus können Sie die Hardwaregeneration auswählen:

- **Gen4:** bis zu 24 logische CPUs, basierend auf Intel-Prozessoren vom Typ E5-2673 v3 (Haswell) mit 2,4 GHz, virtueller Kern = 1 PP (physischer Kern), 7 GB pro Kern, angeschlossene SSD
- **Gen5:** bis zu 80 logische CPUs, basierend auf Intel-Prozessoren vom Typ E5-2673 v4 (Broadwell) mit 2,3 GHz, virtueller Kern = 1 LP (Hyperthread), 5,1 GB pro Kern, schnelle eNVM-SSD

Gen4-Hardware verfügt über deutlich mehr Arbeitsspeicher pro V-Kern. Bei Gen5-Hardware können die Computeressourcen aber viel stärker zentral hochskaliert werden.

> [!NOTE]
> Informationen zu den DTU-basierten Dienstebenen finden Sie unter [Diensttarife beim DTU-basierten Kaufmodell](sql-database-service-tiers-dtu.md). Informationen zu den Unterschieden zwischen den Dienstebenen des DTU-basierten und des V-Kern-basierten Kaufmodells finden Sie unter [Wählen zwischen den Kaufmodellen „V-Kern“ und „DTU“](sql-database-purchase-models.md).

## <a name="service-tier-characteristics"></a>Dienstebenenmerkmale

Das V-Kern-basierte Kaufmodell bietet drei Dienstebenen: „Universell“, Hyperscale“ und „Unternehmenskritisch“. Diese Dienstebenen unterscheiden sich in Bezug auf Computegröße, Hochverfügbarkeitsdesign, Fehlerisolationsmethode, Speichertyp und -größe sowie E/A-Bereich.

Sie müssen den erforderlichen Speicher und den Aufbewahrungszeitraum für Sicherungen separat konfigurieren. Navigieren Sie zum Festlegen des Aufbewahrungszeitraums für Sicherungen im Azure-Portal zum Server (nicht zur Datenbank) und anschließend zu **Sicherungen verwalten** > **Richtlinie konfigurieren** > **Konfiguration der Point-in-Time-Wiederherstellung** > **7–35 Tage**.

In der folgenden Tabelle werden die Unterschiede zwischen den drei Ebenen erläutert:

||**Universell**|**Unternehmenskritisch**|**Hyperscale**|
|---|---|---|---|
|Am besten geeignet für:|Die meisten geschäftlichen Workloads. Bietet budgetorientierte, ausgewogene und skalierbare Compute- und Speicheroptionen.|Geschäftsanwendungen mit hohen E/A-Anforderungen. Bietet dank mehrerer isolierter Replikate höchste Resilienz gegenüber Ausfällen.|Die meisten geschäftlichen Workloads mit hohen Anforderungen an skalierbaren Speicher und Leseskalierung.|
|Compute|**Bereitgestelltes Computing**:<br/>Gen4: 1 bis 24 V-Kerne<br/>Gen5: 2 bis 80 V-Kerne<br/>**Serverloses Computing**:<br/>Gen5: 0,5 bis 4 V-Kerne|**Bereitgestelltes Computing**:<br/>Gen4: 1 bis 24 V-Kerne<br/>Gen5: 2 bis 80 V-Kerne|**Bereitgestelltes Computing**:<br/>Gen4: 1 bis 24 V-Kerne<br/>Gen5: 2 bis 80 V-Kerne|
|Arbeitsspeicher|**Bereitgestelltes Computing**:<br/>Gen4: 7 GB pro V-Kern<br/>Gen5: 5,1 GB pro virtuellem Kern<br/>**Serverloses Computing**:<br/>Gen5: 3GB pro virtuellem Kern|**Bereitgestelltes Computing**:<br/>Gen4: 7 GB pro V-Kern<br/>Gen5: 5,1 GB pro virtuellem Kern |**Bereitgestelltes Computing**:<br/>Gen4: 7 GB pro V-Kern<br/>Gen5: 5,1 GB pro virtuellem Kern|
|Storage|Verwendet Remotespeicher.<br/>**Bereitgestelltes Einzeldatenbank-Computing**:<br/>5 GB – 4 TB<br/>**Serverloses Einzeldatenbank-Computing**:<br/>5 GB bis 1 TB<br/>**Verwaltete Instanz**: 32 GB – 8 TB |Verwendet lokalen SSD-Speicher.<br/>**Bereitgestelltes Einzeldatenbank-Computing**:<br/>5 GB – 4 TB<br/>**Verwaltete Instanz**:<br/>32 GB – 4 TB |Flexible automatische Speichervergrößerung nach Bedarf. Unterstützt bis zu 100 TB Speicher. Verwendet lokalen SSD-Speicher für den lokalen Pufferpoolcache und den lokalen Datenspeicher. Verwendet Azure-Remotespeicher als endgültigen langfristigen Datenspeicher. |
|E/A-Durchsatz (ungefähr)|**Einzeldatenbank**: 500 IOPS pro V-Kern mit maximal 7.000 IOPS.<br/>**Verwaltete Instanz**: Abhängig von der [Größe der Datei](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes).|5000 IOPS pro Kern mit maximal 200.000 IOPS|Hyperscale ist eine mehrstufige Architektur mit Caching auf mehreren Ebenen. Die tatsächlichen IOPs hängen von der Workload ab.|
|Verfügbarkeit|Einzelnes Replikat, keine Replikate mit Leseskalierung|3 Replikate, 1 [Replikat, Leseskalierung](sql-database-read-scale-out.md),<br/>zonenredundante Hochverfügbarkeit (High Availability, HA)|Einzelnes Replikat mit Lese-/Schreibzugriff sowie bis zu vier [Replikate mit Leseskalierung](sql-database-read-scale-out.md)|
|Backups|[Georedundanter Speicher mit Lesezugriff (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), sieben bis 35 Tage (standardmäßig sieben Tage)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 Tage (standardmäßig 7 Tage)|Auf Momentaufnahmen basierende Sicherungen in Azure-Remotespeicher. Bei Wiederherstellungen werden diese Momentaufnahmen zur schnellen Wiederherstellung verwendet. Sicherungen werden sofort ausgeführt und haben keine Auswirkungen auf die E/A-Computeleistung. Wiederherstellungen sind schnell und nicht datenintensiv (dauern also nicht Stunden oder Tage, sondern nur Minuten).|
|In-Memory|Nicht unterstützt|Unterstützt|Nicht unterstützt|
|||

> [!NOTE]
> Im Rahmen eines kostenlosen Azure-Kontos können Sie eine kostenlose Azure SQL-Datenbank mit der Dienstebene „Basic“ erhalten. Weitere Informationen finden Sie unter [Mit dem kostenlosen Azure-Konto eine verwaltete Clouddatenbank erstellen](https://azure.microsoft.com/free/services/sql-database/).

- Weitere Informationen zu V-Kern-Ressourcengrenzwerten finden Sie unter [V-Kern-Ressourcengrenzwerte in einer Einzeldatenbank](sql-database-vcore-resource-limits-single-databases.md) sowie unter [V-Kern-Ressourcengrenzwerte in einer verwalteten Instanz](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Weitere Informationen zu den Dienstebenen „Universell“ und „Unternehmenskritisch“ finden Sie unter [Dienstebenen „Universell“ und „Unternehmenskritisch“](sql-database-service-tiers-general-purpose-business-critical.md).
- Weitere Informationen zur Dienstebene „Hyperscale“ im V-Kern-basierten Kaufmodell finden Sie unter [Dienstebene „Hyperscale“](sql-database-service-tier-hyperscale.md).  

## <a name="azure-hybrid-benefit"></a>Azure-Hybridvorteil

Bei der bereitgestellten Computeebene des V-Kern-basierten Kaufmodells können Sie Ihre vorhandenen Lizenzen gegen Rabattpreise für SQL-Datenbank tauschen, indem Sie den [Azure-Hybridvorteil für SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) verwenden. Mit diesem Azure-Vorteil können Sie bei Azure SQL-Datenbank bis zu 30 Prozent sparen, indem Sie Ihre lokalen SQL Server-Lizenzen mit Software Assurance verwenden.

![Preise](./media/sql-database-service-tiers/pricing.png)

Mit dem Azure-Hybridvorteil können Sie wahlweise nur die zugrunde liegende Azure-Infrastruktur bezahlen und Ihre vorhandene SQL Server-Lizenz für die eigentliche SQL-Datenbank-Engine verwenden (Grundpreis für Computeleistung), oder Sie bezahlen für die zugrunde liegende Infrastruktur sowie für die SQL Server-Lizenz (Preismodell mit enthaltener Lizenz).

Sie können Ihr Lizenzierungsmodell im Azure-Portal oder mithilfe einer der folgenden APIs auswählen oder ändern:

- Festlegen oder Aktualisieren des Lizenztyps mithilfe von PowerShell:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Festlegen oder Aktualisieren des Lizenztyps mithilfe der Azure-Befehlszeilenschnittstelle:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Festlegen oder Aktualisieren des Lizenztyps mithilfe der REST-API:

  - [Datenbanken – Erstellen oder Aktualisieren](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Datenbanken – Aktualisieren](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Verwaltete Instanzen – Erstellen oder Aktualisieren](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Verwaltete Instanzen – Aktualisieren](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrieren vom DTU-basierten Modell zum V-Kern-basierten Modell

### <a name="migrate-a-database"></a>Migrieren einer Datenbank

Die Migration einer Datenbank vom DTU-basierten zum V-Kern-basierten Kaufmodell ist vergleichbar mit einem Up- oder Downgrade zwischen der Standard- und Premium-Dienstebene im DTU-basierten Kaufmodell.

### <a name="migrate-databases-with-geo-replication-links"></a>Migrieren von Datenbanken mit Georeplikationsverknüpfungen

Die Migration vom DTU-basierten zum V-Kern-basierten Modell ähnelt dem Up- oder Downgrade der Georeplikationsbeziehungen zwischen Datenbanken auf der Standard- und Premium-Dienstebene. Sie müssen die Georeplikation während der Migration nicht beenden, es müssen jedoch die folgenden Sequenzierungsregeln eingehalten werden:

- Bei einem Upgrade müssen Sie zuerst das Upgrade für die sekundäre Datenbank und anschließend das Upgrade für die primäre Datenbank durchführen.
- Drehen Sie bei einem Downgrade die Reihenfolge um: Führen Sie zuerst das Downgrade für die primäre und anschließend das Downgrade für die sekundäre Datenbank durch.

Bei Verwendung der Georeplikation zwischen zwei Pools für elastische Datenbanken empfiehlt es sich, einen Pool als primäres Element und den anderen als sekundäres Element festzulegen. In diesem Fall sollten Sie sich beim Migrieren von Pools für elastische Datenbanken an die gleiche Sequenzierungsanleitung halten. Falls Sie jedoch über Pools für elastische Datenbanken verfügen, die sowohl primäre als auch sekundäre Datenbanken enthalten, müssen Sie den Pool mit der höheren Auslastung als primäres Element behandeln und die Sequenzierungsregeln entsprechend befolgen.  

Die folgende Tabelle enthält eine Anleitung für spezifische Migrationsszenarien:

|Aktuelle Dienstebene|Zieldienstebene|Migrationstyp|Benutzeraktionen|
|---|---|---|---|
|Standard|Allgemeiner Zweck|Seitwärts|Die Migration ist in einer beliebigen Reihenfolge möglich, aber Sie müssen für eine geeignete V-Kern-Größe sorgen*|
|Premium|Unternehmenskritisch|Seitwärts|Die Migration ist in einer beliebigen Reihenfolge möglich, aber Sie müssen für eine geeignete V-Kern-Größe sorgen*|
|Standard|Unternehmenskritisch|Upgrade|Sekundäre Einheit muss zuerst migriert werden|
|Unternehmenskritisch|Standard|Downgrade|Primäre Einheit muss zuerst migriert werden|
|Premium|Allgemeiner Zweck|Downgrade|Primäre Einheit muss zuerst migriert werden|
|Allgemeiner Zweck|Premium|Upgrade|Sekundäre Einheit muss zuerst migriert werden|
|Unternehmenskritisch|Allgemeiner Zweck|Downgrade|Primäre Einheit muss zuerst migriert werden|
|Allgemeiner Zweck|Unternehmenskritisch|Upgrade|Sekundäre Einheit muss zuerst migriert werden|
||||

\* Auf der Standardebene wird für jeweils 100 DTUs mindestens ein V-Kern benötigt. Auf der Premiumebene wird für jeweils 125 DTUs mindestens ein V-Kern benötigt.

### <a name="migrate-failover-groups"></a>Migrieren von Failovergruppen

Für die Migration von Failovergruppen mit mehreren Datenbanken ist eine individuelle Migration der primären und sekundären Datenbank erforderlich. Während dieses Prozesses gelten die gleichen Aspekte und Sequenzierungsregeln. Nachdem die Datenbanken auf das V-Kern-basierte Kaufmodell umgestellt wurden, bleibt die Failovergruppe mit den gleichen Richtlinieneinstellungen wirksam.

### <a name="create-a-geo-replication-secondary-database"></a>Erstellen einer sekundären Datenbank für die Georeplikation

Eine sekundäre Datenbank für die Georeplikation (Geo-Sekundärdatenbank) kann nur mit der gleichen Dienstebene erstellt werden, die auch für die primäre Datenbank verwendet wurde. Bei Datenbanken mit hoher Protokollgenerierungsrate empfiehlt es sich, die Geo-Sekundärdatenbank mit der gleichen Computegröße zu erstellen wie die primäre Datenbank.

Wenn Sie eine Geo-Sekundärdatenbank im Pool für elastische Datenbanken für eine einzelne primäre Datenbank erstellen, muss die Einstellung `maxVCore` für den Pool der Computegröße der primären Datenbank entsprechen. Wenn Sie eine Geo-Sekundärdatenbank für eine primäre Datenbank in einem anderen Pool für elastische Datenbanken erstellen, sollte die Einstellung `maxVCore` der Pools gleich sein.

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Verwenden einer Datenbankkopie zum Konvertieren einer DTU-basierten Datenbank in eine V-Kern-basierte Datenbank

Sie können eine beliebige Datenbank mit einer DTU-basierten Computegröße in eine Datenbank mit einer V-Kern-basierten Computegröße kopieren, ohne dass hierfür Einschränkungen oder spezielle Sequenzierungen gelten, solange die Zielcomputegröße die maximale Datenbankgröße der Quelldatenbank unterstützt. Die Datenbankkopie erstellt eine Momentaufnahme der Daten zum Startzeitpunkt des Kopiervorgangs und synchronisiert keine Daten zwischen Quelle und Ziel.

## <a name="next-steps"></a>Nächste Schritte

- Die spezifischen Computegrößen und Speichergrößenoptionen für Einzeldatenbanken finden Sie unter [V-Kern-basierte Ressourceneinschränkungen in SQL-Datenbank für Einzeldatenbanken](sql-database-vcore-resource-limits-single-databases.md).
- Die spezifischen Computegrößen und Speichergrößenoptionen für Pools für elastische Datenbanken finden Sie unter [V-Kern-basierte Ressourceneinschränkungen in SQL-Datenbank für Pools für elastische Datenbanken](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
