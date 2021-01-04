---
title: Dienstebenen „Universell“ und „Unternehmenskritisch“
titleSuffix: Azure SQL Database & SQL Managed Instance
description: In diesem Artikel werden die Dienstebenen „Universell“ und „Unternehmenskritisch“ im vCore-basierten Kaufmodell erläutert, das von Azure SQL-Datenbank und Azure SQL Managed Instance verwendet wird.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 01/30/2020
ms.openlocfilehash: 33c63ffc4220da6d98c462039897067e4ba69491
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793159"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-service-tiers"></a>Dienstebenen für Azure SQL-Datenbank und Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL-Datenbank und Azure SQL Managed Instance basieren auf der an die Cloudumgebung angepasste Architektur der SQL Server-Datenbank-Engine, um selbst bei Infrastrukturausfällen eine Verfügbarkeit von 99,99 Prozent sicherzustellen. Azure SQL-Datenbank und Azure SQL Managed Instance umfassen zwei Dienstebenen, die jeweils unterschiedliche Architekturmodelle aufweisen. Diese Dienstebenen lauten:

- [Universell](service-tier-general-purpose.md) ist für die meisten budgetorientierten Workloads konzipiert.
- [Unternehmenskritisch](service-tier-business-critical.md) ist für Workloads mit geringer Latenz und hoher Resilienz für Ausfälle und schnelle Failover konzipiert.

Azure SQL-Datenbank umfasst eine zusätzliche Dienstebene: 

- [Hyperscale](service-tier-hyperscale.md) ist für die meisten Geschäftsworkloads konzipiert und bietet hochgradig skalierbaren Speicher, horizontale Leseskalierung und schnelle Datenbankwiederherstellungsfunktionen.

In diesem Artikel werden die Unterschiede zwischen den Dienstebenen, die Speicher- und Sicherungsaspekte für die Dienstebenen „Universell“ und „Unternehmenskritisch“ im vCore-basierten Kaufmodell beschrieben.

## <a name="service-tier-comparison"></a>Vergleich der Dienstebenen

In der folgenden Tabelle sind die wichtigsten Unterschiede zwischen den Dienstebenen für die neueste Generation (Gen5) beschrieben. Beachten Sie, dass sich die Merkmale der Dienstebenen zwischen SQL-Datenbank und SQL Managed Instance unterscheiden können.

|-| Ressourcentyp | Universell |  Hyperscale | Unternehmenskritisch |
|:---:|:---:|:---:|:---:|:---:|
| **Am besten geeignet für** | |  Bietet budgetorientierte ausgewogene Compute- und Speicheroptionen. | Die meisten geschäftlichen Workloads. Automatische Skalierung der Speichergröße bis zu 100 TB, fluide vertikale und horizontale Computeskalierung, schnelle Datenbankwiederherstellung. | OLTP-Anwendungen mit hoher Transaktionsrate und geringen Latenzen bei E/A-Vorgängen. Bietet mit mehreren synchron aktualisierten Replikaten höchste Resilienz gegenüber Fehlern und schnelle Failover.|
|  **Verfügbar in Ressourcentyp:** ||SQL-Datenbank/SQL Managed Instance | Azure SQL-Einzeldatenbank | SQL-Datenbank/SQL Managed Instance |
| **Computegröße**| SQL-Datenbank | 1 bis 80 virtuelle Kerne | 1 bis 80 virtuelle Kerne | 1 bis 80 virtuelle Kerne |
| | Verwaltete SQL-Instanz | 4, 8, 16, 24, 32, 40, 64, 80 virtuelle Kerne | – | 4, 8, 16, 24, 32, 40, 64, 80 virtuelle Kerne |
| | SQL Managed Instance-Pools | 2, 4, 8, 16, 24, 32, 40, 64, 80 virtuelle Kerne | – | – |
| **Speichertyp** | All | Storage Premium (remote, pro Instanz) | Entkoppelter Speicher mit lokalem SSD-Cache (pro Instanz) | Äußerst schneller lokaler SSD-Speicher (pro Instanz) |
| **Datenbankgröße** | SQL-Datenbank | 5 GB – 4 TB | Bis zu 100 TB | 5 GB – 4 TB |
| | Verwaltete SQL-Instanz  | 32 GB – 8 TB | – | 32 GB – 4 TB |
| **Speichergröße** | SQL-Datenbank | 5 GB – 4 TB | Bis zu 100 TB | 5 GB – 4 TB |
| | Verwaltete SQL-Instanz  | 32 GB – 8 TB | – | 32 GB – 4 TB |
| **TempDB-Größe** | SQL-Datenbank | [32 GB pro virtuellem Kern](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB pro virtuellem Kern](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB pro virtuellem Kern](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Verwaltete SQL-Instanz  | [24 GB pro virtuellem Kern](../managed-instance/resource-limits.md#service-tier-characteristics) | – | Bis zu 4 TB – [begrenzt durch Speichergröße](../managed-instance/resource-limits.md#service-tier-characteristics) |
| **Protokollschreibdurchsatz** | SQL-Datenbank | [1.875 MB/Sek. pro virtuellem Kern (max. 30 MB/s)](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/Sek. pro virtuellem Kern (max. 96 MB/s)](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Verwaltete SQL-Instanz | [3 MB/Sek. pro virtuellem Kern (max. 22 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) | – | [4 MB/Sek. pro virtuellem Kern (max. 48 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) |
|**Verfügbarkeit**|All| 99,99 % |  [99,95 % mit einem sekundären Replikat, 99,99 % mit weiteren Replikaten](service-tier-hyperscale-frequently-asked-questions-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99 % <br/> [99,995 % mit zonenredundantem Singleton](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Sicherungen**|All|RA-GRS, 7 - 35 Tage (standardmäßig 7 Tage)| RA-GRS, 7 Tage, konstante Zeitpunktwiederherstellung (Point-in-Time Recovery, PITR) | RA-GRS, 7 - 35 Tage (standardmäßig 7 Tage) |
|**In-Memory-OLTP** | | – | – | Verfügbar |
|**Schreibgeschützte Replikate**| | 0: Integriert <br> 0–4: Verwendung von [Georeplikation](active-geo-replication-overview.md) | 0–4: Integriert | 1: Integriert, im Preis inbegriffen <br> 0–4: Verwendung von [Georeplikation](active-geo-replication-overview.md) |
|**Preise/Abrechnung** | SQL-Datenbank | [Virtueller Kern, reservierter Speicher und Sicherungsspeicher](https://azure.microsoft.com/pricing/details/sql-database/single/) werden in Rechnung gestellt. <br/>IOPS werden nicht in Rechnung gestellt. | [Virtueller Kern für jedes Replikat und den verwendeten Speicher](https://azure.microsoft.com/pricing/details/sql-database/single/) wird in Rechnung gestellt. <br/>IOPS noch nicht in Rechnung gestellt. | [Virtueller Kern, reservierter Speicher und Sicherungsspeicher](https://azure.microsoft.com/pricing/details/sql-database/single/) werden in Rechnung gestellt. <br/>IOPS werden nicht in Rechnung gestellt. |
|| Verwaltete SQL-Instanz | [Virtueller Kern, reservierter Speicher und Sicherungsspeicher](https://azure.microsoft.com/pricing/details/sql-database/managed/) werden in Rechnung gestellt. <br/>IOPS werden nicht in Rechnung gestellt.| – | [Virtueller Kern, reservierter Speicher und Sicherungsspeicher](https://azure.microsoft.com/pricing/details/sql-database/managed/) werden in Rechnung gestellt. <br/>IOPS werden nicht in Rechnung gestellt.| 
|**Rabattmodelle**| | [Reservierte Instanzen](reserved-capacity-overview.md)<br/>[Azure-Hybridvorteil](../azure-hybrid-benefit.md) (nicht verfügbar für Dev/Test-Abonnements)<br/>[Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)- und [Pay-as-you-Go](https://azure.microsoft.com/offers/ms-azr-0023p/)-Dev/Test-Abonnements| [Azure-Hybridvorteil](../azure-hybrid-benefit.md) (nicht verfügbar für Dev/Test-Abonnements)<br/>[Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)- und [Pay-as-you-Go](https://azure.microsoft.com/offers/ms-azr-0023p/)-Dev/Test-Abonnements| [Reservierte Instanzen](reserved-capacity-overview.md)<br/>[Azure-Hybridvorteil](../azure-hybrid-benefit.md) (nicht verfügbar für Dev/Test-Abonnements)<br/>[Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)- und [Pay-as-you-Go](https://azure.microsoft.com/offers/ms-azr-0023p/)-Dev/Test-Abonnements|

Weitere Informationen finden Sie auf den Seiten zu den ausführlichen Unterschieden zwischen den Dienstebenen unter [Azure SQL-Datenbank (virtueller Kern)](resource-limits-vcore-single-databases.md), [Azure SQL-Einzeldatenbank (DTU, Datenbankdurchsatzeinheit)](resource-limits-dtu-single-databases.md), [Azure SQL-Datenbank (DTU) in einem Pool](resource-limits-dtu-single-databases.md) und [Azure SQL Managed Instance](../managed-instance/resource-limits.md).

> [!NOTE]
> Informationen zur Dienstebene „Hyperscale“ im vCore-basierten Kaufmodell finden Sie unter [Dienstebene „Hyperscale“](service-tier-hyperscale.md). Einen Vergleich zwischen vCore-basiertem Kaufmodell und DTU-basiertem Kaufmodell finden Sie unter [Entscheiden zwischen dem vCore-basierten und dem DTU-basierten Kaufmodell – Azure SQL-Datenbank und SQL Managed Instance](purchasing-models.md).

## <a name="data-and-log-storage"></a>Daten- und Protokollspeicher

Die folgenden Faktoren wirken sich darauf aus, wie viel Speicherplatz für Daten und Protokolldateien verwendet wird. Dies gilt für die Dienstebenen „Universell“ und „Unternehmenskritisch“. Ausführliche Informationen zu Daten und zum Protokollspeicher in Hyperscale finden Sie unter [Hyperscale-Dienstebene](service-tier-hyperscale.md).

- Der zugeordnete Speicher wird für Datendateien (MDF) und Protokolldateien (LDF) verwendet.
- Jede Einzeldatenbank-Computegröße unterstützt eine maximale Datenbankgröße, die standardmäßig bei 32 GB liegt.
- Wenn Sie die erforderliche Einzeldatenbankgröße (die Größe der MDF-Datei) konfigurieren, werden automatisch 30 Prozent zusätzlicher Speicher hinzugefügt, um LDF-Dateien zu unterstützen.
- Sie können eine beliebige Einzeldatenbankgröße zwischen 10 GB und dem unterstützten Maximum auswählen.
  - In den Dienstebenen „Standard“ oder „Universell“ erhöhen oder verringern Sie die Speichergröße in Schritten von 10 GB.
  - In den Dienstebenen „Premium“ oder „Unternehmenskritisch“ erhöhen oder verringern Sie die Speichergröße in Schritten von 250 GB.
- In der Dienstebene „Universell“ wird für `tempdb` eine angefügte SSD verwendet, und diese Speicherkosten sind im V-Kern-Preis enthalten.
- In der Dienstebene „Unternehmenskritisch“ wird für `tempdb` die angefügte SSD für MDF- und LDF-Dateien gemeinsam genutzt, und die `tempdb`-Speicherkosten sind im V-Kern-Preis enthalten.
- Die Speichergröße für eine Instanz von SQL Managed Instance muss als Vielfaches von 32 GB angegeben werden.


> [!IMPORTANT]
> Ihnen wird der gesamte Speicher berechnet, der für MDF- und LDF-Dateien zugeordnet ist.

Verwenden Sie [sp_spaceused](/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql) zum Überwachen der aktuellen Gesamtgröße Ihrer MDF- und LDF-Dateien. Verwenden Sie [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql), um die aktuelle Größe der einzelnen MDF- und LDF-Dateien zu überwachen.

> [!IMPORTANT]
> Unter bestimmten Umständen müssen Sie ggf. eine Datenbank verkleinern, um ungenutzten Speicherplatz freizugeben. Weitere Informationen finden Sie unter [Verwalten von Dateispeicherplatz in Azure SQL-Datenbank](file-space-manage.md).

## <a name="backups-and-storage"></a>Sicherungen und Speicher

Der Speicher für Datenbanksicherungen wird zugeordnet, um die Funktionen „Point-in-Time-Wiederherstellung“ (Point in Time Restore, PITR) und [Langzeitaufbewahrung](long-term-retention-overview.md) (Long Term Retention, LTR) von SQL-Datenbank und SQL Managed Instance zu unterstützen. Dieser Speicher wird für jede Datenbank separat zugeordnet und als zwei Arten von getrennten Datenbankgebühren berechnet.

- **PITR** : Einzelne Datenbanksicherungen werden automatisch in den [georedundanten Speicher mit Lesezugriff](../../storage/common/geo-redundant-design.md) (Read-Access Geo-Redundant, RA-GRS) kopiert. Die Speichergröße wird dynamisch erhöht, wenn neue Sicherungen erstellt werden. Der Speicher wird für wöchentliche vollständige Sicherungen, tägliche differenzielle Sicherungen und im 5-Minuten-Takt kopierte Sicherungen von Transaktionsprotokollen verwendet. Der Speicherverbrauch richtet sich nach der Änderungsrate der Datenbank und nach dem Aufbewahrungszeitraum für Sicherungen. Sie können für jede Datenbank eine separate Aufbewahrungsdauer konfigurieren, die zwischen 7 und 35 Tagen liegt. Eine Mindestspeichermenge, die 100 Prozent (1x) der Datenbankgröße entspricht, wird kostenlos zur Verfügung gestellt. Für die meisten Datenbanken reicht diese Menge aus, um Sicherungen für sieben Tage aufzubewahren.
- **LTR** : Sie haben auch die Möglichkeit, die Langzeitaufbewahrung von vollständigen Sicherungen für bis zu 10 Jahre zu konfigurieren (dieses Feature ist [in der eingeschränkten öffentlichen Vorschauversion von SQL Managed Instance enthalten](long-term-retention-overview.md#sql-managed-instance-support)). Wenn Sie eine LTR-Richtlinie einrichten, werden diese Sicherungen automatisch im RA-GRS-Speicher gespeichert. Sie können jedoch steuern, wie häufig die Sicherungen kopiert werden. Zur Einhaltung unterschiedlicher Konformitätsanforderungen können Sie verschiedene Aufbewahrungszeiträume für wöchentliche, monatliche und/oder jährliche Sicherungen auswählen. Wie viel Speicher für LTR-Sicherungen verwendet wird, richtet sich nach der ausgewählten Konfiguration. Sie können den LTR-Preisrechner verwenden, um die Kosten für LTR-Speicher zu schätzen. Weitere Informationen finden Sie unter dem Thema zur [Langzeitaufbewahrung von SQL-Datenbank](long-term-retention-overview.md).

## <a name="next-steps"></a>Nächste Schritte

Ausführliche Informationen zu den spezifischen Compute- und Speichergrößen der Dienstebenen „Universell“ und „Unternehmenskritisch“ finden Sie unter: 

- [Ressourcenlimits des auf virtuellen Kernen basierenden Kaufmodells für Azure SQL-Datenbank](resource-limits-vcore-single-databases.md)
- [Limits des auf virtuellen Kernen basierenden Kaufmodells für Pooldatenbanken in Azure SQL-Datenbank](resource-limits-vcore-elastic-pools.md)
- [Ressourcenlimits des auf virtuellen Kernen basierenden Kaufmodells für Azure SQL Managed Instance](../managed-instance/resource-limits.md)