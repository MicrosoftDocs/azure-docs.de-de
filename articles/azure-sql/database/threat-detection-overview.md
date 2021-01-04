---
title: Erweiterter Schutz vor Bedrohungen
titleSuffix: Azure SQL Database, SQL Managed Instance, & Azure Synapse Analytics
description: Advanced Threat Protection erkennt anomale Datenbankaktivitäten, die auf potenzielle Sicherheitsrisiken in Azure SQL-Datenbank, Azure SQL Managed Instance und Azure Synapse Analytics hindeuten.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, sstein
ms.date: 12/01/2020
tags: azure-synapse
ms.openlocfilehash: 931e914cd3c184136395a9bb9a7e148a90e9fb91
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461946"
---
# <a name="advanced-threat-protection-for-azure-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Advanced Threat Protection für Azure SQL-Datenbank, SQL Managed Instance und Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Advanced Threat Protection für [Azure SQL-Datenbank](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) und [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) erkennt anomale Aktivitäten, die auf ungewöhnliche und möglicherweise schädigende Zugriffs- oder Exploitversuche auf Datenbanken hinweisen.

Advanced Threat Protection ist Teil des [Azure Defender für SQL](azure-defender-for-sql.md)-Angebots. Dabei handelt es sich um ein vereinheitlichtes Paket für erweiterte SQL-Sicherheitsfunktionen. Der Zugriff auf Advanced Threat Protection und dessen Verwaltung sind über das zentrale Azure Defender für SQL-Portal möglich.

## <a name="overview"></a>Übersicht

Dank der neuen Sicherheitsebene von Advanced Threat Protection können Kunden potenzielle Bedrohungen erkennen und darauf reagieren, sobald diese auftreten, indem bei anomalen Aktivitäten Sicherheitswarnungen ausgegeben werden. Benutzer erhalten Warnungen zu verdächtigen Datenbankaktivitäten, potenziellen Sicherheitsrisiken sowie Angriffen durch Einschleusung von SQL-Befehlen und anomalen Datenbankzugriffs- und -abfragemustern. Advanced Threat Protection integriert Warnungen in [Azure Security Center](https://azure.microsoft.com/services/security-center/). Dabei werden Detailinformationen zu verdächtigen Aktivitäten sowie Empfehlungen bereitgestellt, wie die Bedrohung untersucht und entschärft werden kann. Advanced Threat Protection vereinfacht den Umgang mit potenziellen Bedrohungen der Datenbank, ohne das Fachwissen eines Sicherheitsexperten besitzen oder komplexe Sicherheitsüberwachungssysteme verwalten zu müssen.

Zur vollständigen Untersuchung empfiehlt es sich, die Datenbanküberwachung zu aktivieren, bei der Datenbankereignisse in ein Überwachungsprotokoll in Ihrem Azure Storage-Konto geschrieben werden.  Informationen zum Aktivieren der Überwachung finden Sie unter [Überwachung für Azure SQL-Datenbank und Azure-Synapse](../../azure-sql/database/auditing-overview.md) oder [Überwachung für Azure SQL Managed Instance](../managed-instance/auditing-configure.md).

## <a name="alerts"></a>Alerts

Advanced Threat Protection für Azure SQL-Datenbank erkennt Anomalien bei Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Ihre Datenbanken zuzugreifen oder diese zu nutzen. Eine Liste der Warnungen für Azure SQL-Datenbank finden Sie in den [Warnungen für SQL-Datenbank und Azure Synapse Analytics in Azure Security Center](../../security-center/alerts-reference.md#alerts-sql-db-and-warehouse).

## <a name="explore-detection-of-a-suspicious-event"></a>Erkennen eines verdächtigen Ereignisses

Bei Erkennung anormaler Datenbankaktivitäten erhalten Sie eine E-Mail-Benachrichtigung. Die E-Mail enthält Informationen zum verdächtigen Sicherheitsereignis (dazu gehören Art der anomalen Aktivitäten, Datenbankname, Servername, Anwendungsname und Zeit des Ereignisses). Darüber hinaus enthält die E-Mail Angaben zu möglichen Ursachen und empfohlenen Maßnahmen zur Untersuchung und Abwehr der potenziellen Bedrohung für die Datenbank.

![Bericht zu anomalen Aktivitäten](./media/threat-detection-overview/anomalous_activity_report.png)

1. Klicken Sie in der E-Mail auf den Link **View recent SQL alerts** (Aktuelle SQL-Warnungen anzeigen), um das Azure-Portal zu starten und die Azure Security Center-Seite für Warnungen zu öffnen, auf der eine Übersicht über die aktiven Bedrohungen angezeigt wird, die in der Datenbank erkannt wurden.

   ![Aktivitätsbedrohungen](./media/threat-detection-overview/active_threats.png)

1. Klicken Sie auf eine bestimmte Warnung, um weitere Details und Aktionen zum Untersuchen der entsprechenden Bedrohung und Abwehren zukünftiger Bedrohungen anzuzeigen.

   Beispielsweise ist die Einschleusung von SQL-Befehlen das häufigste Sicherheitsproblem für Webanwendungen im Internet, das für Angriffe auf datengesteuerte Anwendungen verwendet wird. Die Angreifer nutzen Sicherheitslücken der Anwendung, um böswillige SQL-Anweisungen in Eingabefelder der Anwendung einzuschleusen, sodass Daten in der Datenbank manipuliert oder verändert werden können. Bei Warnungen in Bezug auf die Einschleusung von SQL-Befehlen schließen die Details der Warnung die anfällige missbräuchlich genutzte SQL-Anweisung ein.

   ![Spezifische Warnung](./media/threat-detection-overview/specific_alert.png)

## <a name="explore-alerts-in-the-azure-portal"></a>Warnungen im Azure-Portal

Warnungen von Advanced Threat Protection sind in [Azure Security Center](https://azure.microsoft.com/services/security-center/) integriert. Auf Livekacheln von SQL Advanced Threat Protection innerhalb der Datenbank und auf den Blättern von SQL Azure Defender im Azure-Portal lässt sich der Status aktiver Bedrohungen nachverfolgen.

Klicken Sie auf **Advanced Threat Protection-Warnung**, um die Azure Security Center-Seite für Warnungen zu öffnen und eine Übersicht über die aktiven SQL-Bedrohungen zu erhalten, die in der Datenbank erkannt wurden.

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection-alerts.png" alt-text="Advanced Threat Protection-Warnungen in der Datenbankübersicht":::

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection.png" alt-text="Advanced Threat Protection in Security Center":::

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Advanced Threat Protection in Azure SQL-Datenbank und Azure Synapse](threat-detection-configure.md).
- Weitere Informationen finden Sie unter [Advanced Threat Protection in Azure SQL Managed Instance](../managed-instance/threat-detection-configure.md).
- Weitere Informationen zu [Azure Defender für SQL](azure-defender-for-sql.md).
- Weitere Informationen zu [Überwachung von Azure SQL-Datenbank](../../azure-sql/database/auditing-overview.md)
- Weitere Informationen zu [Azure Security Center](../../security-center/security-center-introduction.md).
- Weitere Informationen zu den Preisen finden Sie unter [Azure SQL-Datenbank: Preise](https://azure.microsoft.com/pricing/details/sql-database/).