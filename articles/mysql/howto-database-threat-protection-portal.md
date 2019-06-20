---
title: 'Advanced Threat Protection: Azure Database for MySQL | Microsoft-Dokumentation'
description: Der Bedrohungsschutz erkennt anormale Datenbankaktivitäten, die auf potenzielle Sicherheitsrisiken für die Datenbank hindeuten.
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: 76f6c15fc1e186e254c4edbb53a2a0ccf7050b3e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61458945"
---
# <a name="advanced-threat-protection-for-azure-database-for-mysql"></a>Advanced Threat Protection für Azure Database for MySQL

Advanced Threat Protection für Azure Database for MySQL erkennt Anomalien bei Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Ihre Datenbanken zuzugreifen oder diese zu nutzen.

Advanced Threat Protection ist Teil des Angebots „Advanced Data Security“ (Erweiterte Datensicherheit). Dabei handelt es sich um ein vereinheitlichtes Paket für erweiterte Sicherheitsfunktionen. Über das [Azure-Portal](https://portal.azure.com) können Sie auf den Dienst Advanced Threat Protection zugreifen und diesen verwalten. Er befindet sich derzeit in der Vorschauversion.

> [!NOTE]
> Das Advanced Threat Protection-Feature ist in den folgenden Azure Government- und Sovereign Cloud-Regionen **nicht** verfügbar: „US Gov Texas“, „US Gov Arizona“, „US Gov Iowa“, „US Gov Virginia“, „US DoD, Osten“, „US DoD, Mitte“, „Deutschland, Mitte“, „Deutschland, Norden“, „China, Osten“, „China, Osten 2“. Informationen zur allgemeinen Produktverfügbarkeit finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/).
>

> [!NOTE]
> Dieses Feature steht in allen Regionen von Azure zur Verfügung, in denen Azure Database for MySQL für General Purpose- and Memory Optimized-Server bereitgestellt wird.

## <a name="set-up-threat-detection"></a>Einrichten der Bedrohungserkennung
1. Starten Sie das Azure-Portal unter [https://portal.azure.com](https://portal.azure.com).
2. Navigieren Sie zur Konfigurationsseite des Azure Database for MySQL-Servers, den Sie schützen möchten. Wählen Sie in den Sicherheitseinstellungen **Advanced Threat Protection (Vorschau)** aus.
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

* Weitere Informationen zu [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Weitere Informationen zu Tarifen finden Sie unter [Azure-Datenbank for MySQL – Preise](https://azure.microsoft.com/pricing/details/mysql/).  
