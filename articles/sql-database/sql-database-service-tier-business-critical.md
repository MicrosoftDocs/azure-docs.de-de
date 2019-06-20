---
title: Tarif „Unternehmenskritisch“ – Azure SQL-Datenbank-Dienst | Microsoft-Dokumentation
description: Informationen zum Tarif „Unternehmenskritisch“ für Azure SQL-Datenbank
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 90989a9105405f1784b3be9ab59f55cd3433feaf
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479225"
---
# <a name="business-critical-tier---azure-sql-database"></a>Tarif „Unternehmenskritisch“ – Azure SQL-Datenbank

> [!NOTE]
> Der Tarif „Unternehmenskritisch“ hat im DTU-Kaufmodell den Namen „Premium“. Einen Vergleich zwischen vCore-basiertem Kaufmodell und DTU-basiertem Kaufmodell finden Sie unter [Kaufmodelle für Azure SQL-Datenbank und Ressourcen](sql-database-purchase-models.md).

Azure SQL-Datenbank basiert auf der an die Cloudumgebung angepasste Architektur der SQL Server-Datenbank-Engine, um die Verfügbarkeit von 99,99 % selbst bei Infrastrukturausfällen sicherzustellen. In Azure SQL-Datenbank werden drei Architekturmodelle verwendet:
- Universell/Standard 
- Unternehmenskritisch/Premium
- Hyperscale

Das Dienstebenenmodell des Typs „Premium/Unternehmenskritisch“ basiert auf einem Cluster von Datenbank-Engine-Prozessen. Dieses Architekturmodell beruht darauf, dass immer ein Quorum von verfügbaren Datenbank-Engine-Knoten vorhanden ist, und weist selbst während Wartungsaktivitäten eine minimale Leistungsbeeinträchtigung der Workload auf.

In Azure werden das zugrunde liegende Betriebssystem, Treiber und die SQL Server-Datenbank-Engine transparent mit minimaler Ausfallzeit für Endbenutzer aktualisiert und gepatcht. 

Die Premium-Verfügbarkeit wird in den Dienstebenen „Premium“ und „Unternehmenskritisch“ von Azure SQL-Datenbank aktiviert und ist für hohe Workloads vorgesehen, bei denen keine Leistungsbeeinträchtigung aufgrund laufender Wartungsvorgänge toleriert werden kann.

Im Premium-Modell werden in Azure SQL-Datenbank der Compute- und Speicherbereich auf dem einzelnen Knoten integriert. Die Hochverfügbarkeit in diesem Architekturmodell wird durch die Replikation auf Compute- (SQL Server-Datenbank-Engine-Prozess) und Speicherebene (lokal angefügte SSD) erreicht, die in einem Cluster mit vier Knoten anhand einer ähnlichen Technologie wie [AlwaysOn-Verfügbarkeitsgruppen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) in SQL Server bereitgestellt werden.

![Cluster von Datenbank-Engine-Knoten](media/sql-database-managed-instance/business-critical-service-tier.png)

Der SQL-Datenbank-Engine-Prozess sowie die zugrunde liegenden MDF- und LDF-Dateien werden mit lokal angefügtem SSD-Speicher auf demselben Knoten platziert und bieten eine geringe Latenz für die Workload. Hochverfügbarkeit wird anhand einer ähnlichen Technologie wie [AlwaysOn-Verfügbarkeitsgruppen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) in SQL Server implementiert. Jede Datenbank ist ein Cluster von Datenbankknoten mit einer primären Datenbank, die für die Kundenworkload zugänglich ist, und drei sekundären Prozessen, die Kopien der Daten enthalten. Der primäre Knoten überträgt die Änderungen kontinuierlich zu sekundären Knoten, um sicherzustellen, dass die Daten auf sekundären Replikaten verfügbar sind, wenn der primäre Knoten aus bestimmten Gründen ausfällt. Ein Failover wird von der SQL Server-Datenbank-Engine verarbeitet: Ein sekundäres Replikat wird zum primären Knoten, und ein neues sekundäres Replikat wird erstellt, um sicherzustellen, dass ausreichend Knoten im Cluster vorhanden sind. Die Workload wird automatisch zum neuen primären Knoten umgeleitet.

Darüber hinaus umfasst der Cluster des Typs „Unternehmenskritisch“ eine integrierte Funktion [Horizontale Leseskalierung](sql-database-read-scale-out.md), die einen gebührenfreien, integrierten und schreibgeschützten Knoten bereitstellt, der zum Ausführen von schreibgeschützten Abfragen (z.B. Berichten) verwendet werden kann, die die Leistung der primären Workload nicht beeinträchtigen sollen.

## <a name="when-to-choose-this-service-tier"></a>Wann sollte diese Dienstebene gewählt werden?

Die Dienstebene „Unternehmenskritisch“ ist für die Anwendungen gedacht, die Antworten mit geringer Latenz vom zugrunde liegenden SSD-Speicher (durchschnittlich 1 – 2 ms), schnelle Wiederherstellung bei einem Fehler der zugrunde liegenden Infrastruktur oder das Auslagern von Berichten, Analysen und schreibgeschützten Abfragen an das kostenlose lesbare sekundäre Replikat der primären Datenbank erfordern.

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die Ressourcenmerkmale (Anzahl von Kernen, E/A, Arbeitsspeicher) der Ebene „Unternehmenskritisch“ für eine [verwaltete Instanz](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), für eine Einzeldatenbank im [V-Kern-Modell](sql-database-vcore-resource-limits-single-databases.md#business-critical-service-tier-for-provisioned-compute-tier) bzw. [DTU-Modell](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier) oder für einen Pool für elastische Datenbanken im [V-Kern-Modell](sql-database-vcore-resource-limits-elastic-pools.md#business-critical-service-tier-storage-sizes-and-compute-sizes) und [DTU-Modell](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits) an.
- Erfahren Sie mehr über die Tarife [Universell](sql-database-service-tier-general-purpose.md) und [Hyperscale](sql-database-service-tier-hyperscale.md).
- Erfahren Sie mehr über [Service Fabric](../service-fabric/service-fabric-overview.md).
- Weitere Optionen zu Hochverfügbarkeit und Notfallwiederherstellung finden Sie unter [Geschäftskontinuität](sql-database-business-continuity.md).
