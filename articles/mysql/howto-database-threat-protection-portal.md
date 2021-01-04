---
title: Advanced Threat Protection – Azure-Portal – Azure Database for MySQL
description: Erfahren Sie, wie Advanced Threat Protection zum Identifizieren anomaler Datenbankaktivitäten konfiguriert wird, die auf potenzielle Sicherheitsrisiken für die Datenbank in einer verwalteten Instanz hinweisen.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: b30bd36dca6f866b8f3e6e8a0b133a6dd61b239b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012662"
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
  
   :::image type="content" source="./media/howto-database-threat-protection-portal/set-up-threat-protection.png" alt-text="Einrichten der Bedrohungserkennung":::

## <a name="explore-anomalous-database-activities"></a>Untersuchen anormaler Datenbankaktivitäten

Bei Erkennung anormaler Datenbankaktivitäten erhalten Sie eine E-Mail-Benachrichtigung. Die E-Mail enthält Informationen zum verdächtigen Sicherheitsereignis (dazu gehören Art der anomalen Aktivitäten, Datenbankname, Servername, Anwendungsname und Zeit des Ereignisses). Darüber hinaus enthält die E-Mail Angaben zu möglichen Ursachen und empfohlenen Maßnahmen zur Untersuchung und Abwehr der potenziellen Bedrohung für die Datenbank.
 
1. Klicken Sie in der E-Mail auf den Link **View recent alerts** (Aktuelle Warnungen anzeigen), um das Azure-Portal zu starten und die Azure Security Center-Seite für Warnungen zu öffnen, auf der eine Übersicht über die aktiven Bedrohungen angezeigt wird, die in der SQL-Datenbank erkannt wurden.
    
    :::image type="content" source="./media/howto-database-threat-protection-portal/anomalous-activity-report.png" alt-text="Bericht zu anomalen Aktivitäten":::

    Anzeigen aktiver Bedrohungen:

    :::image type="content" source="./media/howto-database-threat-protection-portal/active-threats.png" alt-text="Aktive Bedrohungen":::

2. Klicken Sie auf eine bestimmte Warnung, um weitere Details und Aktionen zum Untersuchen der entsprechenden Bedrohung und Abwehren zukünftiger Bedrohungen anzuzeigen.
    
    :::image type="content" source="./media/howto-database-threat-protection-portal/specific-alert.png" alt-text="Spezifische Warnung":::

## <a name="explore-threat-detection-alerts"></a>Untersuchen von Warnungen der Bedrohungserkennung

Die Warnungen der Bedrohungserkennung von SQL-Datenbank sind in [Azure Security Center](https://azure.microsoft.com/services/security-center/) eingebunden. Eine Livekachel für SQL-Bedrohungserkennung verfolgt den Status aktiver Bedrohungen auf den Datenbank- und SQL ATP-Seiten im Azure-Portal nach.

Klicken Sie auf **Threat detection alert** (Warnung der Bedrohungserkennung), um die Azure Security Center-Seite für Warnungen zu öffnen und eine Übersicht über die aktiven SQL-Bedrohungen zu erhalten, die in der Datenbank erkannt wurden.

   :::image type="content" source="./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png" alt-text="Warnung der Bedrohungserkennung":::
   

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu [Azure Security Center](../security-center/security-center-introduction.md)
* Weitere Informationen zu Tarifen finden Sie unter [Azure-Datenbank for MySQL – Preise](https://azure.microsoft.com/pricing/details/mysql/).