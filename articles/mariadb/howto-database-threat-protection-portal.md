---
title: Advanced Threat Protection – Azure-Portal – Azure Database for MariaDB
description: Der Bedrohungsschutz für Azure Database for MariaDB identifiziert anomale Datenbankaktivitäten, die auf potenzielle Sicherheitsbedrohungen für die Datenbank hinweisen.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 7734feddabb1a4a86e7932da3ef4adc57352637e
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542693"
---
# <a name="advanced-threat-protection-for-azure-database-for-mariadb"></a>Advanced Threat Protection für Azure Database for MariaDB

Advanced Threat Protection für Azure Database for MariaDB erkennt Anomalien bei Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Ihre Datenbanken zuzugreifen oder diese missbräuchlich zu nutzen.

Advanced Threat Protection ist Teil des Angebots „Advanced Data Security“ (Erweiterte Datensicherheit). Dabei handelt es sich um ein vereinheitlichtes Paket für erweiterte Sicherheitsfunktionen. Sie können über das [Azure-Portal](https://portal.azure.com) auf den Advanced Threat Protection-Dienst zugreifen und diesen verwalten.

> [!IMPORTANT]
> Advanced Threat Protection befindet sich in der Public Preview. Dieses Feature steht in allen Regionen von Azure zur Verfügung, in denen Azure Database for MariaDB für universelle und arbeitsspeicheroptimierte Server bereitgestellt wird.

> [!NOTE]
> Das Advanced Threat Protection-Feature ist in den folgenden Azure Government- und Sovereign Cloud-Regionen **nicht** verfügbar: „US Gov Texas“, „US Gov Arizona“, „US Gov Iowa“, „US Gov Virginia“, „US DoD, Osten“, „US DoD, Mitte“, „Deutschland, Mitte“, „Deutschland, Norden“, „China, Osten“, „China, Osten 2“. Informationen zur allgemeinen Produktverfügbarkeit finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/).

## <a name="set-up-threat-detection"></a>Einrichten der Bedrohungserkennung
1. Starten Sie das Azure-Portal unter [https://portal.azure.com](https://portal.azure.com).
2. Navigieren Sie zur Konfigurationsseite des Azure Database for MariaDB-Servers, den Sie schützen möchten. Wählen Sie in den Sicherheitseinstellungen **Advanced Threat Protection (Vorschau)** aus.
3. Gehen Sie auf der Konfigurationsseite **Advanced Threat Protection (Vorschau)** folgendermaßen vor:

   - Aktivieren Sie Advanced Threat Protection auf dem Server.
   - Geben Sie unter **Advanced Threat Protection Settings** (Advanced Threat Protection-Einstellungen) im Textfeld **Warnungen senden an** eine Liste von E-Mail-Adressen an, die Sicherheitswarnungen bei der Erkennung von anomalen Datenbankaktivitäten empfangen sollen.
  
   ![Einrichten der Bedrohungserkennung](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Untersuchen anormaler Datenbankaktivitäten

Bei Erkennung anormaler Datenbankaktivitäten erhalten Sie eine E-Mail-Benachrichtigung. Die E-Mail enthält Informationen zum verdächtigen Sicherheitsereignis (dazu gehören Art der anomalen Aktivitäten, Datenbankname, Servername, Anwendungsname und Zeit des Ereignisses). Darüber hinaus enthält die E-Mail Angaben zu möglichen Ursachen und empfohlenen Maßnahmen zur Untersuchung und Abwehr der potenziellen Bedrohung für die Datenbank.
 
1. Klicken Sie in der E-Mail auf den Link **View recent alerts** (Aktuelle Warnungen anzeigen), um das Azure-Portal zu starten und die Azure Security Center-Seite für Warnungen zu öffnen, auf der eine Übersicht über die aktiven Bedrohungen angezeigt wird, die in der SQL-Datenbank erkannt wurden.
    
    ![Bericht zu anomalen Aktivitäten](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Anzeigen aktiver Bedrohungen:

    ![Aktive Bedrohungen](./media/howto-database-threat-protection-portal/active-threats.png)

2. Klicken Sie auf eine bestimmte Warnung, um weitere Details und Aktionen zum Untersuchen der entsprechenden Bedrohung und Abwehren zukünftiger Bedrohungen anzuzeigen.
    
    ![Spezifische Warnung](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Untersuchen von Warnungen der Bedrohungserkennung

Die Warnungen der Bedrohungserkennung von SQL-Datenbank sind in [Azure Security Center](https://azure.microsoft.com/services/security-center/) eingebunden. Auf einer Livekachel der SQL-Bedrohungserkennung auf den Blättern der Datenbank und von SQL ATP im Azure-Portal werden die Status von aktiven Bedrohungen nachverfolgt.

Klicken Sie auf **Threat detection alert** (Warnung der Bedrohungserkennung), um die Azure Security Center-Seite für Warnungen zu öffnen und eine Übersicht über die aktiven SQL-Bedrohungen zu erhalten, die in der Datenbank erkannt wurden.

   ![Warnung der Bedrohungserkennung](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu [Azure Security Center](../security-center/security-center-introduction.md)
* Weitere Informationen zu Tarifen finden Sie unter [Azure Database for MariaDB – Preise](https://azure.microsoft.com/pricing/details/mariadb/).