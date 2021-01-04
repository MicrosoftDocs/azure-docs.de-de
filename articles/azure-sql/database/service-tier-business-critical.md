---
title: Dienstebene „Unternehmenskritisch“
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Erfahren Sie mehr über die Dienstebene „Unternehmenskritisch“ für Azure SQL-Datenbank und Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 830ecc44d0def13e51cb06704bef429bb8860cd6
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780222"
---
# <a name="business-critical-tier---azure-sql-database-and-azure-sql-managed-instance"></a>Tarif „Unternehmenskritisch“ – Azure SQL-Datenbank und Azure SQL Managed Instance 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> Der Tarif „Unternehmenskritisch“ trägt im DTU-Kaufmodell den Namen „Premium“. Einen Vergleich zwischen vCore-basiertem Kaufmodell und DTU-basiertem Kaufmodell finden Sie unter [Kaufmodelle für Azure SQL-Datenbank und Ressourcen](purchasing-models.md).

Azure SQL-Datenbank und Azure SQL Managed Instance basieren beide auf der an die Cloudumgebung angepasste Architektur der SQL Server-Datenbank-Engine, um Verfügbarkeit von 99,99 % selbst bei Infrastrukturausfällen sicherzustellen. Es werden drei Architekturmodelle verwendet:
- Universell/Standard 
- Unternehmenskritisch/Premium
- Hyperscale

Das Dienstebenenmodell des Typs „Premium/Unternehmenskritisch“ basiert auf einem Cluster von Datenbank-Engine-Prozessen. Dieses Architekturmodell beruht darauf, dass immer ein Quorum von verfügbaren Datenbank-Engine-Knoten vorhanden ist, und weist selbst während Wartungsaktivitäten eine minimale Leistungsbeeinträchtigung der Workload auf. Die Dienstebene „Hyperscale“ ist derzeit nur für Azure SQL-Datenbank (und nicht für SQL Managed Instance) verfügbar. Diese Dienstebene bietet eine hochgradig skalierbare Speicher- und Computeleistung, mit der die Speicher- und Computeressourcen für eine Datenbank in Azure SQL-Datenbank mithilfe der Azure-Architektur weit über die Limits der Dienstebenen „Universell“ und „Unternehmenskritisch“ hinaus aufskaliert werden können.

In Azure werden das zugrunde liegende Betriebssystem, Treiber und die SQL Server-Datenbank-Engine transparent mit minimaler Ausfallzeit für Endbenutzer aktualisiert und gepatcht. 

Die Premium-Verfügbarkeit wird in den Dienstebenen „Premium“ und „Unternehmenskritisch“ aktiviert und ist für hohe Workloads vorgesehen, bei denen keine Leistungsbeeinträchtigung aufgrund laufender Wartungsvorgänge toleriert werden kann.

Compute und Speicher sind in den einzelnen Knoten im Premium-Modell integriert. Die Hochverfügbarkeit in diesem Architekturmodell wird durch die Replikation auf Compute- (SQL Server-Datenbank-Engine-Prozess) und Speicherebene (lokal angefügte SSD) erreicht, die in einem Cluster mit vier Knoten anhand einer ähnlichen Technologie wie [AlwaysOn-Verfügbarkeitsgruppen](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) in SQL Server bereitgestellt werden.

![Cluster von Datenbank-Engine-Knoten](./media/service-tier-business-critical/business-critical-service-tier.png)

Der SQL Server-Datenbank-Engine-Prozess sowie die zugrunde liegenden MDF- und LDF-Dateien werden mit lokal angefügtem SSD-Speicher auf demselben Knoten platziert und bieten eine geringe Latenz für die Workload. Hochverfügbarkeit wird anhand einer ähnlichen Technologie wie [AlwaysOn-Verfügbarkeitsgruppen](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) in SQL Server implementiert. Jede Datenbank ist ein Cluster von Datenbankknoten mit einer primären Datenbank, die für Kundenworkloads zugänglich ist, und drei sekundären Prozessen, die Kopien der Daten enthalten. Der primäre Knoten überträgt die Änderungen kontinuierlich an sekundären Knoten, um sicherzustellen, dass die Daten auf sekundären Replikaten verfügbar sind, wenn der primäre Knoten aus bestimmten Gründen ausfällt. Ein Failover wird von der SQL Server-Datenbank-Engine verarbeitet: Ein sekundäres Replikat wird zum primären Knoten, und ein neues sekundäres Replikat wird erstellt, um sicherzustellen, dass ausreichend Knoten im Cluster vorhanden sind. Die Workload wird automatisch zum neuen primären Knoten umgeleitet.

Darüber hinaus umfasst der Cluster des Typs „Unternehmenskritisch“ eine integrierte Funktion [Horizontale Leseskalierung](read-scale-out.md), die einen gebührenfreien, integrierten und schreibgeschützten Knoten bereitstellt, der zum Ausführen von schreibgeschützten Abfragen (z.B. Berichten) verwendet werden kann, die die Leistung der primären Workload nicht beeinträchtigen sollen.

## <a name="when-to-choose-this-service-tier"></a>Wann sollte diese Dienstebene gewählt werden?

Die Dienstebene „Unternehmenskritisch“ ist für Anwendungen gedacht, die Antworten mit geringer Latenz vom zugrunde liegenden SSD-Speicher (durchschnittlich 1 – 2 ms), schnelle Wiederherstellung bei einem Fehler der zugrunde liegenden Infrastruktur oder das Auslagern von Berichten, Analysen und schreibgeschützten Abfragen an das kostenlose lesbare sekundäre Replikat der primären Datenbank erfordern.

Die wichtigsten Gründe dafür, dass Sie die Dienstebene „Unternehmenskritisch“ anstelle der Ebene „Universell“ wählen sollten, sind folgende:
-   **Niedrige E/A-Latenzanforderungen** : Für Workloads, die eine schnelle Reaktion der Speicherebene (durchschnittlich 1 bis 2 Millisekunden) erfordern, sollte die Ebene „Unternehmenskritisch“ verwendet werden. 
-   **Häufige Kommunikation zwischen der Anwendung und der Datenbank** . Anwendungen, die eine Zwischenspeicherung auf Anwendungsebene oder die [Batchverarbeitung von Anforderungen](../performance-improve-use-batching.md) nicht nutzen können und viele SQL-Abfragen senden müssen, für die eine schnelle Verarbeitung erforderlich ist, sind gute Kandidaten für die Ebene „Unternehmenskritisch“.
-   **Große Anzahl von Aktualisierungen** : Einfüge-, Aktualisierungs- und Löschvorgänge verändern die Datenseiten im Arbeitsspeicher (modifizierte Seite), die mit dem Vorgang `CHECKPOINT` in Datendateien gespeichert werden müssen. Ein potenzieller Prozessabsturz der Datenbank-Engine oder ein Failover der Datenbank mit einer großen Anzahl von modifizierten Seiten erhöht unter Umständen die Wiederherstellungszeit in der Ebene „Universell“. Verwenden Sie die Ebene „Unternehmenskritisch“, wenn Sie über eine Workload verfügen, die zu vielen In-Memory-Änderungen führt. 
-   **Zeitintensive Transaktionen, die Daten ändern** . Transaktionen, die für einen längeren Zeitraum geöffnet sind, verhindern das Abschneiden von Protokolldateien, was die Protokollgröße und die Anzahl von [virtuellen Protokolldateien](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch) (Virtual Log File, VLF) erhöhen kann. Eine große Anzahl von VLFs kann die Wiederherstellung der Datenbank nach einem Failover verlangsamen.
-   **Workload mit Berichterstellungs- und Analyseabfragen** , die zum kostenlosen sekundären schreibgeschützten Replikat umgeleitet werden können.
- **Höhere Resilienz und schnellere Wiederherstellung nach Fehlern** . Bei einem Systemausfall wird die Datenbank auf der primären Instanz deaktiviert, und eines der sekundären Replikate wird sofort zur neuen primären Datenbank mit Lese-/Schreibzugriff, die Abfragen verarbeiten kann. Es ist nicht erforderlich, dass die Datenbank-Engine Transaktionen aus der Protokolldatei analysiert und wiederholt und alle Daten im Speicherpuffer lädt.
- **Erweiterter Schutz vor Datenbeschädigung** : Die Ebene „Unternehmenskritisch“ nutzt im Hintergrund Datenbankreplikate, um die Geschäftskontinuität sicherzustellen, und der Dienst nutzt auch die automatische Seitenreparatur. Dabei handelt es sich um dieselbe Technologie, die für die [SQL Server-Datenbankspiegelung und Verfügbarkeitsgruppen](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) genutzt wird. Wenn ein Replikat eine Seite aufgrund eines Datenintegritätsproblems nicht lesen kann, wird eine neue Kopie der Seite von einem anderen Replikat abgerufen, die die nicht lesbare Seite ohne Datenverluste oder Ausfallzeiten für den Kunden ersetzt. Diese Funktionalität ist in der Ebene „Universell“ verfügbar, wenn die Datenbank über ein georedundantes Replikat verfügt.
- **Höhere Verfügbarkeit** : Die Ebene „Unternehmenskritisch“ in einer Konfiguration mit mehreren Verfügbarkeitszonen garantiert eine Verfügbarkeit von 99,995 %, die Ebene „Universell“ im Vergleich dazu 99,99 %.
- **Schnelle geografische Wiederherstellung** : Die mit Georeplikation konfigurierte Ebene „Unternehmenskritisch“ bietet eine garantierte Recovery Point Objective (RPO) von fünf Sekunden und eine Recovery Time Objective (RTO) von 30 Sekunden für 100 % der bereitgestellten Stunden.

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die Ressourcenmerkmale (Anzahl von Kernen, E/A, Arbeitsspeicher) der Ebene „Unternehmenskritisch“ in [SQL Managed Instance](../managed-instance/resource-limits.md#service-tier-characteristics), für eine Einzeldatenbank im [vCore-basierten Modell](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) oder im [DTU-Modell](resource-limits-dtu-single-databases.md#premium-service-tier) oder für einen Pool für elastische Datenbanken im [vCore-basierten Modell](resource-limits-vcore-elastic-pools.md#business-critical---provisioned-compute---gen4) und [DTU-Modell](resource-limits-dtu-elastic-pools.md#premium-elastic-pool-limits) an.
- Erfahren Sie mehr über die Tarife [Universell](service-tier-general-purpose.md) und [Hyperscale](service-tier-hyperscale.md).
- Informationen zu [Service Fabric](../../service-fabric/service-fabric-overview.md)
- Weitere Optionen zu Hochverfügbarkeit und Notfallwiederherstellung finden Sie unter [Geschäftskontinuität](business-continuity-high-availability-disaster-recover-hadr-overview.md).