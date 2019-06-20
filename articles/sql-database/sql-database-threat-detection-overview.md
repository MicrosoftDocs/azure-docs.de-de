---
title: Advanced Threat Protection – Azure SQL-Datenbank | Microsoft-Dokumentation
description: Advanced Threat Protection erkennt anomale Datenbankaktivitäten, die auf potenzielle Sicherheitsrisiken in Azure SQL-Datenbank hindeuten.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 03/31/2019
ms.openlocfilehash: 710a94c919f4262c3f572f28d03c79b77e658287
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60614583"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Advanced Threat Protection für Azure SQL-Datenbank

Advanced Threat Protection für [Azure SQL-Datenbank](sql-database-technical-overview.md) und [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) erkennt anomale Aktivitäten, die auf ungewöhnliche und möglicherweise schädliche Versuche hinweisen, auf Datenbanken zuzugreifen oder diese zu auszunutzen.

Advanced Threat Protection ist Teil des Angebots [Advanced Data Security](sql-database-advanced-data-security.md) (ADS), bei dem es sich um ein vereinheitlichtes Paket für erweiterte SQL-Sicherheitsfunktionen handelt. Der Zugriff auf Advanced Threat Protection und dessen Verwaltung sind über das zentrale SQL ADS-Portal möglich.

> [!NOTE]
> Dieses Thema gilt für Azure SQL-Server sowie für Datenbanken von SQL-Datenbank und SQL Data Warehouse, die auf dem Azure SQL-Server erstellt werden. Der Einfachheit halber wird nur SQL-Datenbank verwendet, wenn sowohl SQL-Datenbank als auch SQL Data Warehouse gemeint sind.

## <a name="what-is-advanced-threat-protection"></a>Was ist Advanced Threat Protection?

 Dank der neuen Sicherheitsebene von Advanced Threat Protection können Kunden potenzielle Bedrohungen erkennen und darauf reagieren, sobald diese auftreten, indem bei anomalen Aktivitäten Sicherheitswarnungen ausgegeben werden. Benutzer erhalten Warnungen zu verdächtigen Datenbankaktivitäten, potenziellen Sicherheitsrisiken sowie Angriffen durch Einschleusung von SQL-Befehlen und anomalen Datenbankzugriffs- und -abfragemustern. Advanced Threat Protection integriert Warnungen in [Azure Security Center](https://azure.microsoft.com/services/security-center/). Dabei werden Detailinformationen zu verdächtigen Aktivitäten sowie Empfehlungen bereitgestellt, wie die Bedrohung untersucht und entschärft werden kann. Advanced Threat Protection vereinfacht den Umgang mit potenziellen Bedrohungen der Datenbank, ohne das Fachwissen eines Sicherheitsexperten besitzen oder komplexe Sicherheitsüberwachungssysteme verwalten zu müssen.

Zur vollständigen Untersuchung empfiehlt es sich, die [SQL-Datenbanküberwachung](sql-database-auditing.md) zu aktivieren, bei der Datenbankereignisse in ein Überwachungsprotokoll in Ihrem Azure Storage-Konto geschrieben werden.  

## <a name="advanced-threat-protection-alerts"></a>Advanced Threat Protection-Warnungen

Advanced Threat Protection für Azure SQL-Datenbank erkennt anomale Aktivitäten, die auf ungewöhnliche und möglicherweise schädliche Versuche hinweisen, bei denen auf Datenbanken zugegriffen oder diese ausgenutzt werden sollen. Hierbei können die folgenden Warnungen ausgelöst werden:

- **Anfälligkeit für die Einschleusung von SQL-Befehlen**: Diese Warnung wird ausgelöst, wenn eine Anwendung in der Datenbank eine fehlerhafte SQL-Anweisung generiert. Diese Warnung kann auf ein mögliches Sicherheitsrisiko in Bezug auf Angriffe mit Einschleusung von SQL-Befehlen hinweisen. Es gibt zwei mögliche Gründe für die Generierung einer fehlerhaften Anweisung:

  - Ein Fehler im Anwendungscode, der zur fehlerhaften SQL-Anweisung führt
  - Anwendungscode oder gespeicherte Prozeduren führen bei der Erstellung der fehlerhaften SQL-Anweisung keine Bereinigung der Benutzereingabe durch, und dies kann für eine Einschleusung von SQL-Befehlen ausgenutzt werden
- **Potenzielle Einschleusung von SQL-Befehlen:** Diese Warnung wird ausgelöst, wenn ein aktiver Exploit für ein identifiziertes Anwendungssicherheitsrisiko in Bezug auf die Einschleusung von SQL-Befehlen besteht. Dies bedeutet, dass der Angreifer versucht, schädliche SQL-Anweisungen einzuschleusen, indem er den anfälligen Anwendungscode bzw. die gespeicherten Prozeduren verwendet.
- **Zugriff von einem ungewöhnlichen Ort:** Diese Warnung wird ausgelöst, wenn eine Änderung des Zugriffsmusters für SQL Server erfolgt ist, weil sich eine Person von einem ungewöhnlichen Ort aus bei SQL Server angemeldet hat. In einigen Fällen erkennt die Warnung eine legitime Aktion (eine neue Anwendung oder Wartungsarbeiten von Entwicklern). In anderen Fällen erkennt die Warnung eine schädliche Aktion (ehemaliger Mitarbeiter, externer Angreifer).
- **Zugriff über ein ungewöhnliches Azure-Rechenzentrum:** Diese Warnung wird ausgelöst, wenn auf diesem Server kürzlich eine Änderung des Zugriffsmusters für SQL Server erfolgt ist, weil sich eine Person über ein ungewöhnliches Azure-Rechenzentrum bei SQL Server angemeldet hat. In einigen Fällen erkennt die Warnung eine legitime Aktion (Ihre neue Anwendung in Azure, Power BI, Azure SQL-Abfrage-Editor). In anderen Fällen erkennt die Warnung ggf. eine schädliche Aktion einer Azure-Ressource bzw. eines -Diensts (ehemaliger Mitarbeiter, externer Angreifer).
- **Zugriff über einen unbekannten Prinzipal:** Diese Warnung wird ausgelöst, wenn eine Änderung des Zugriffsmusters für SQL Server erfolgt ist, weil sich eine Person über einen ungewöhnlichen Prinzipal (SQL-Benutzer) bei SQL Server angemeldet hat. In einigen Fällen erkennt die Warnung eine legitime Aktion (neue Anwendung, Wartungsarbeiten von Entwicklern). In anderen Fällen erkennt die Warnung eine schädliche Aktion (ehemaliger Mitarbeiter, externer Angreifer).
- **Zugriff über eine potenziell schädliche Anwendung:** Diese Warnung wird ausgelöst, wenn zum Zugreifen auf die Datenbank eine potenziell schädliche Anwendung verwendet wird. In einigen Fällen erkennt die Warnung aktive Eindringversuche. In anderen Fällen erkennt die Warnung einen Angriff mit allgemeinen Angriffstools.
- **Brute-Force-Angriff auf SQL-Anmeldeinformationen:** Diese Warnung wird ausgelöst, wenn eine ungewöhnlich hohe Anzahl von fehlerhaften Anmeldungen mit unterschiedlichen Anmeldeinformationen vorliegt. In einigen Fällen erkennt die Warnung aktive Eindringversuche. In anderen Fällen erkennt die Warnung einen Brute-Force-Angriff.

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Untersuchen anormaler Datenbankaktivitäten bei Erkennung eines verdächtigen Ereignisses

Bei Erkennung anormaler Datenbankaktivitäten erhalten Sie eine E-Mail-Benachrichtigung. Die E-Mail enthält Informationen zum verdächtigen Sicherheitsereignis (dazu gehören Art der anomalen Aktivitäten, Datenbankname, Servername, Anwendungsname und Zeit des Ereignisses). Darüber hinaus enthält die E-Mail Angaben zu möglichen Ursachen und empfohlenen Maßnahmen zur Untersuchung und Abwehr der potenziellen Bedrohung für die Datenbank.

![Bericht zu anomalen Aktivitäten](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Klicken Sie in der E-Mail auf den Link **View recent SQL alerts** (Aktuelle SQL-Warnungen anzeigen), um das Azure-Portal zu starten und die Azure Security Center-Seite für Warnungen zu öffnen, auf der eine Übersicht über die aktiven Bedrohungen angezeigt wird, die in der SQL-Datenbank erkannt wurden.

   ![Aktivitätsbedrohungen](./media/sql-database-threat-detection/active_threats.png)

2. Klicken Sie auf eine bestimmte Warnung, um weitere Details und Aktionen zum Untersuchen der entsprechenden Bedrohung und Abwehren zukünftiger Bedrohungen anzuzeigen.

   Beispielsweise ist die Einschleusung von SQL-Befehlen das häufigste Sicherheitsproblem für Webanwendungen im Internet, das für Angriffe auf datengesteuerte Anwendungen verwendet wird. Die Angreifer nutzen Sicherheitslücken der Anwendung, um böswillige SQL-Anweisungen in Eingabefelder der Anwendung einzuschleusen, sodass Daten in der Datenbank manipuliert oder verändert werden können. Bei Warnungen in Bezug auf die Einschleusung von SQL-Befehlen schließen die Details der Warnung die anfällige missbräuchlich genutzte SQL-Anweisung ein.

   ![Spezifische Warnung](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Auswerten von Advanced Threat Protection-Warnungen für Ihre Datenbank im Azure-Portal

Warnungen von Advanced Threat Protection sind in [Azure Security Center](https://azure.microsoft.com/services/security-center/) integriert. Auf Livekacheln von SQL Advanced Threat Protection innerhalb der Datenbank und auf den Blättern von SQL ADS im Azure-Portal lässt sich der Status aktiver Bedrohungen nachverfolgen.

Klicken Sie auf **Advanced Threat Protection-Warnung**, um die Azure Security Center-Seite für Warnungen zu öffnen und eine Übersicht über die aktiven SQL-Bedrohungen zu erhalten, die in der Datenbank oder im Data Warehouse erkannt wurden.

   ![Advanced Threat Protection-Warnung](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Advanced Threat Protection-Warnung2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Advanced Threat Protection für einzelne und in Pools zusammengefasste Datenbanken](sql-database-threat-detection.md).
- Erfahren Sie mehr über [Advanced Threat Protection in einer verwalteten Instanz](sql-database-managed-instance-threat-detection.md).
- Erfahren Sie mehr über [Advanced Data Security](sql-database-advanced-data-security.md).
- Weitere Informationen zu [Überwachung von Azure SQL-Datenbank](sql-database-auditing.md)
- Weitere Informationen zu [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
- Weitere Informationen zu den Preisen finden Sie unter [SQL-Datenbank: Preise](https://azure.microsoft.com/pricing/details/sql-database/).  
