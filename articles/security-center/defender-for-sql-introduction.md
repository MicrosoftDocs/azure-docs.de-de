---
title: 'Azure Defender für SQL: Vorteile und Features'
description: Enthält eine Beschreibung der Vorteile und Features von Azure Defender für SQL.
author: memildin
ms.author: memildin
ms.date: 12/13/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 5d8c5eb385baff4aac450c470fb6103e2fb315f3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590412"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Einführung in Azure Defender für SQL

Azure Defender for SQL verfügt über zwei Azure Defender-Pläne als Erweiterung des [Datensicherheitspakets](../azure-sql/database/azure-defender-for-sql.md) von Azure Security Center, um Ihre Datenbanken und die zugehörigen Daten unabhängig vom Speicherort zu schützen. 

> [!VIDEO https://www.youtube.com/embed/V7RdB6RSVpc]

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|**Azure Defender für Azure SQL-Datenbank-Server**: Allgemein verfügbar (GA)<br>**Azure Defender für SQL Server-Instanzen auf Computern**: Allgemein verfügbar (GA) |
|Preise:|Die beiden Pläne, die **Azure Defender für SQL** bilden, werden gemäß den Angaben auf der [Preisseite](security-center-pricing.md) berechnet.|
|Geschützte SQL-Versionen:|[SQL auf virtuellen Azure-Computern](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)<br>[Azure Arc-fähige SQL Server-Instanzen](/sql/sql-server/azure-arc/overview)<br>Lokale SQL Server-Instanzen auf Windows-Computern ohne Azure Arc<br>Azure SQL-[Singletons](../azure-sql/database/single-database-overview.md) und [Pool für elastische Datenbanken](../azure-sql/database/elastic-pool-overview.md)<br>[Verwaltete Azure SQL-Datenbank-Instanz](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)<br>[Azure Synapse Analytics (früher SQL DW) und dedizierter SQL-Pool](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)|
|Clouds:|![Ja](./media/icons/yes-icon.png) Kommerzielle Clouds<br>![Ja](./media/icons/yes-icon.png) US Gov<br>![Ja](./media/icons/yes-icon.png) China Gov (**Teilweise**: Teilmenge von Warnungen und Sicherheitsrisikobewertungen für Computer mit SQL Server. Verhaltensbedingte Bedrohungsschutzmaßnahmen sind nicht verfügbar.)|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Was wird mit Azure Defender für SQL geschützt?

**Azure Defender für SQL** verfügt über zwei separate Azure Defender-Pläne:

- Mit **Azure Defender für Azure SQL-Datenbank-Server** wird Folgendes geschützt:
    - [Azure SQL-Datenbank](../azure-sql/database/sql-database-paas-overview.md)
    - [Verwaltete Azure SQL-Datenbank-Instanz](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
    - [Dedizierter SQL-Pool in Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- Mit **Azure Defender für SQL Server-Instanzen auf Computern** wird der Schutz für Ihre nativen Azure SQL Server-Instanzen erweitert, um Hybridumgebungen vollständig zu unterstützen und SQL Server-Instanzen (alle unterstützten Versionen), die in Azure, anderen Cloudumgebungen oder sogar auf lokalen Computern gehostet werden, schützen zu können:
    - [SQL Server auf virtuellen Computern](https://azure.microsoft.com/services/virtual-machines/sql-server/)
    - Lokale SQL Server-Instanzen:
        - [Azure Arc-fähige SQL-Server-Instanz (Vorschauversion)](https://docs.microsoft.com/sql/sql-server/azure-arc/overview)
        - [Auf Windows-Computern ohne Azure Arc ausgeführte SQL Server-Instanzen](../azure-monitor/agents/agent-windows.md)


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Welche Vorteile hat die Nutzung von Azure Defender für SQL?

Diese beiden Pläne verfügen über Funktionen zur Ermittlung und Verringerung potenzieller Datenbankschwachstellen und Erkennung ungewöhnlicher Aktivitäten, die Bedrohungen für Ihre Datenbanken darstellen können:

- [Sicherheitsrisikobewertung](../azure-sql/database/sql-vulnerability-assessment.md): der Scandienst, mit dem potenzielle Schwachstellen in der Datenbank ermittelt, nachverfolgt und behoben werden. Bewertungsscans bieten einen Überblick über den Sicherheitszustand Ihrer SQL-Computer und Einzelheiten zu allen Sicherheitsergebnissen.

- [Advanced Threat Protection](../azure-sql/database/threat-detection-overview.md): Der Erkennungsdienst, der Ihre SQL Server-Instanzen kontinuierlich auf Bedrohungen wie die Einschleusung von SQL-Befehlen, Brute-Force-Angriffe und Berechtigungsmissbrauch überwacht. Dieser Dienst bietet aktionsorientierte Sicherheitswarnungen in Azure Security Center mit Details zur verdächtigen Aktivität, Anleitungen zum Minimieren der Bedrohungen und Optionen für die Fortsetzung ihrer Untersuchungen mit Azure Sentinel. 
    > [!TIP]
    > Zeigen Sie die Liste mit den Sicherheitswarnungen für SQL-Server [auf der Referenzseite für Warnungen](alerts-reference.md#alerts-sql-db-and-warehouse) an.


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Über welche Art von Warnungen verfügt Azure Defender für SQL?

Erweiterte Threat Intelligence-Sicherheitswarnungen werden ausgelöst, wenn Folgendes vorliegt:

- **Potenzielle Angriffe durch Einschleusung von SQL-Befehlen**: Hierzu gehören auch Sicherheitsrisiken, die erkannt werden, wenn von Anwendungen in der Datenbank eine fehlerhafte SQL-Anweisung generiert wird.
- **Anomale Muster für Datenbankzugriff und -abfrage**: Ein Beispiel hierfür sind eine ungewöhnlich hohe Anzahl von fehlgeschlagenen Anmeldeversuchen mit unterschiedlichen Anmeldeinformationen (Brute-Force-Angriff).
- **Verdächtige Datenbankaktivitäten**, z. B. berechtigter Benutzer, der über einen Computer mit Sicherheitsverletzung, der mit einem C&C-Server zum Kryptografiemining kommuniziert, auf eine SQL Server-Instanz zugreift.

Warnungen enthalten Details zum Incident, durch den sie ausgelöst wurden, sowie Empfehlungen zur Untersuchung und Eindämmung von Bedrohungen.



## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mehr über Azure Defender für SQL erfahren. Gehen Sie wie folgt vor, um die beschriebenen Dienste zu nutzen:

- Verwenden Sie Azure Defender für SQL-Server auf Computern, um Ihre [SQL Server-Instanzen auf Sicherheitsrisiken zu überprüfen](defender-for-sql-usage.md).