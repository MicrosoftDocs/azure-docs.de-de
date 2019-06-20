---
title: Advanced Data Security für IaaS in Azure Security Center | Microsoft-Dokumentation
description: " Erfahren Sie, wie Sie Advanced Data Security für IaaS in Azure Security Center aktivieren. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2019
ms.author: monhaber
ms.openlocfilehash: 9806f92a3f00df60cd75315eca427836348e7534
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244293"
---
# <a name="advanced-data-security-for-sql-servers-on-iaas"></a>Advanced Data Security für SQL Server-Instanzen in IaaS
Advanced Data Security für SQL Server-Instanzen in IaaS ist ein einheitliches Paket für erweiterte SQL-Sicherheitsfunktionen. Dazu zählen aktuell die Funktionen zur Ermittlung und Verringerung potenzieller Datenbankschwachstellen und Erkennung ungewöhnlicher Aktivitäten, die eine Bedrohung für Ihre Datenbank darstellen können.

Dieses Sicherheitsangebot für SQL Server-Instanzen in IaaS basiert auf der gleichen grundlegenden Technologie, die auch im [Advanced Data Security-Paket für Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) verwendet wird.


## <a name="overview"></a>Übersicht

Advanced Data Security (ADS) stellt eine Reihe erweiterter SQL-Sicherheitsfunktionen bereit, die eine Sicherheitsrisikobewertung und Advanced Threat Protection umfassen.

* [Sicherheitsrisikobewertung](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) ist ein einfach zu konfigurierender Dienst, mit dem potenzielle Schwachstellen in der Datenbank ermittelt, nachverfolgt und behoben werden können. Er bietet Einblicke in Ihren Sicherheitsstatus, enthält die Schritte zum Beheben von Sicherheitsproblemen und verbessert Ihre Datenbanksicherheit.
* [Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) erkennt Anomalien bei Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf SQL Server zuzugreifen oder diesen zu nutzen. Sie überwacht Ihre Datenbank fortlaufend auf verdächtige Aktivitäten und stellt handlungsorientierte Sicherheitswarnungen bei anomalen Datenbankzugriffsmustern bereit. Diese Warnungen enthalten Details zur verdächtigen Aktivität sowie empfohlene Maßnahmen zur Untersuchung und Abwehr der Bedrohung.

## <a name="get-started-with-ads-for-iaas"></a>Erste Schritte mit ADS für IaaS

Die folgenden Schritte bilden den Einstieg in ADS für IaaS.

### <a name="set-up-ads-for-iaas"></a>Einrichten von ADS für IaaS

**Voraussetzungen**: Sie benötigen einen Log Analytics-Arbeitsbereich zum Speichern der zu analysierenden Sicherheitsprotokolle. Wenn Sie noch nicht über einen Arbeitsbereich verfügen, können Sie ihn anhand der Erläuterungen unter [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) auf einfache Weise erstellen.

1. Verbinden Sie den virtuellen Computer, der SQL Server hostet, mit dem Log Analytics-Arbeitsbereich. Anweisungen dazu finden Sie unter [Verbinden von Windows-Computern mit Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

1. Navigieren Sie im Azure Marketplace zur [SQL Advanced Data Security-Lösung](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity).
(Sie können mithilfe der Marketplace-Suchoption danach suchen, wie es in der folgenden Abbildung gezeigt ist.) Die Seite **SQL Advanced Data Security** wird geöffnet.

    ![Advanced Data Security für IaaS](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. Klicken Sie auf **Create**. Die Arbeitsbereiche werden angezeigt.

    ![Advanced Data Security – Erstellen](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. Wählen Sie den zu verwendenden Arbeitsbereich aus, und klicken Sie auf **Erstellen**.

   ![Arbeitsbereich auswählen](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. Starten Sie [SQL Server auf dem virtuellen Computer](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017) neu.


## <a name="explore-and-investigate-security-alerts"></a>Erkunden und Untersuchen von Sicherheitswarnungen

Sie können die aktuellen Sicherheitswarnungen anzeigen und verwalten.

1. Klicken Sie auf **Security Center** > **Sicherheitswarnungen**, und klicken Sie dann auf eine Warnung.

    ![Warnung suchen](./media/security-center-advanced-iaas-data/find-alert.png)

1. Klicken Sie in der Spalte **Angegriffene Ressource** auf eine Ressource, die angegriffen wurde.

1. Um Warnungsdetails und Aktionen zum Untersuchen der aktuellen Bedrohung sowie zum Begegnen zukünftiger Bedrohungen anzuzeigen, scrollen Sie nach unten zur Seite **Allgemeine Informationen**, und klicken Sie im Abschnitt **Schritte zur Bereinigung** auf den Link **UNTERSUCHUNGSSCHRITTE**.

    ![Schritte zur Bereinigung](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. Zum Anzeigen der Protokolle, die dem Auslösen der Warnung zugeordnet sind, wechseln Sie zu **Log Analytics-Arbeitsbereiche**, und führen Sie die folgenden Schritte aus:

     > [!NOTE]
     > Wenn **Log Analytics-Arbeitsbereiche** nicht im linken Menü angezeigt wird, klicken Sie auf **Alle Dienste**, und suchen Sie nach **Log Analytics-Arbeitsbereiche**.

    1. Vergewissern Sie sich, dass die Spalten **Tarif** und **Arbeitsbereichs-ID** angezeigt werden. (**Log Analytics-Arbeitsbereiche** > **Spalten bearbeiten**, **Tarif** und **Arbeitsbereichs-ID** hinzufügen.)

     ![Spalten bearbeiten](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. Klicken Sie auf den Arbeitsbereich mit den Warnungsprotokollen.

    1. Klicken Sie im Menü **Allgemein** auf **Protokolle**.

    1. Klicken Sie auf das Auge neben der Tabelle **SQLAdvancedThreatProtection**. Die Protokolle werden aufgelistet.

     ![Protokolle anzeigen](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>Einrichten einer E-Mail-Benachrichtigung für ATP-Warnungen 

Sie können eine Liste von Empfängern festlegen, die eine E-Mail-Benachrichtigung erhalten sollen, wenn ASC-Warnungen generiert werden. Die E-Mail enthält einen direkten Link zur Warnung in Azure Security Center mit allen relevanten Details. 

1. Wechseln Sie zu **Security Center** > **Sicherheitsrichtlinie**, und klicken Sie in der Zeile des jeweiligen Abonnements auf **Einstellungen bearbeiten >** .

    ![Abonnementeinstellungen](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. Klicken Sie im Menü **Einstellungen** auf **E-Mail-Benachrichtigungen**. 
1. Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adressen ein, die Benachrichtigungen erhalten sollen. Sie können mehrere E-Mail-Adresse eingeben, indem Sie die einzelnen Adressen durch ein Komma (,) trennen.  Beispiel: admin1@mycompany.com,admin2@mycompany.com,admin3@mycompany.com

      ![E-Mail-Einstellungen](./media/security-center-advanced-iaas-data/email-settings.png)

1. Legen Sie unter den **Einstellungen für die E-Mail-Benachrichtigung** die folgenden Optionen fest:
  
    * **E-Mail-Benachrichtigung für Warnungen mit hohem Schweregrad senden**: Statt E-Mails für alle Warnungen zu senden, werden nur welche für Warnungen mit hohem Schweregrad gesendet.
    * **E-Mail-Benachrichtigung auch an Abonnementbesitzer senden**:  Es werden auch Benachrichtigungen an die Abonnementbesitzer gesendet.

1. Klicken Sie im oberen Bereich des Bildschirms **E-Mail-Benachrichtigungen** auf **Speichern**.

  > [!NOTE]
  > Achten Sie darauf, dass Sie vor dem Schließen des Fensters auf **Speichern** klicken, da sonst die neuen **Einstellungen für die E-Mail-Benachrichtigung** nicht gespeichert werden.

## <a name="explore-vulnerability-assessment-reports"></a>Untersuchen der Berichte zur Sicherheitsrisikobewertung

Das Dashboard zur Sicherheitsrisikobewertung bietet eine Übersicht über die Bewertungsergebnisse für alle Datenbanken. Sie können die Verteilung von Datenbanken nach SQL Server-Version sowie eine Zusammenfassung der fehlerhaften gegenüber einwandfreien Datenbanken und eine allgemeine Übersicht über fehlerhafte Prüfungen nach Risikoverteilung anzeigen.

Sie können die Ergebnisse und Berichte zur Sicherheitsrisikobewertung direkt aus Log Analytics heraus anzeigen.

1. Navigieren Sie zum Log Analytics-Arbeitsbereich mit der ADS-Lösung.
1. Navigieren Sie zu **Lösungen**, und wählen Sie die Lösung **SQL-Sicherheitsrisikobewertung** aus.
1. Klicken Sie im Bereich **Zusammenfassung** auf **Zusammenfassung anzeigen**, und wählen Sie Ihren **Bericht zur SQL-Sicherheitsrisikobewertung** aus.

    ![SQL-Bewertungsbericht](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    Das Berichtsdashboard wird geladen. Stellen Sie sicher, dass das Zeitfenster mindestens auf **Letzte 7 Tage** festgelegt ist, da Sicherheitsrisikoüberprüfungen für Ihre Datenbanken nach einem festen Zeitplan einmal alle 7 Tage ausgeführt werden.

    ![Letzte 7 Tage festlegen](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. Um weitere Details anzuzeigen, klicken Sie auf eines der Dashboardelemente. Beispiel: 

   1. Klicken Sie im Abschnitt mit der Zusammenfassung zu Überprüfungen mit Fehlern auf eine Sicherheitsüberprüfung, um eine Log Analytics-Tabelle mit den Ergebnissen für diese Überprüfung bei allen Datenbanken anzuzeigen **.** Die Überprüfungen mit Ergebnissen werden zuerst aufgeführt.

   1. Klicken Sie dann durch die Liste, um Details zu jedem Sicherheitsrisiko anzuzeigen. Dazu gehören die Beschreibung des Sicherheitsrisikos sowie Auswirkung, Status, verbundenes Risiko und tatsächliche Ergebnisse für diese Datenbank. Sie können auch die eigentliche Abfrage, die für diese Überprüfung ausgeführt wurde, und Informationen zur Beseitigung dieses Sicherheitsrisikos anzeigen.

    ![Arbeitsbereich auswählen](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![Arbeitsbereich auswählen](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. Sie können beliebige Log Analytics-Abfragen für die Ergebnisdaten Ihrer Sicherheitsrisikobewertung ausführen, um die Daten entsprechend Ihren Anforderungen aufzuteilen.

## <a name="advanced-threat-protection-for-sql-servers-on-iaas-alerts"></a>Warnung der Advanced Threat Protection für SQL Server-Instanzen in IaaS
Warnungen werden bei ungewöhnlichen und potenziell schädlichen Zugriffsversuchen oder Exploit-Vorgängen für SQL Server-Instanzen generiert. Diese Ereignisse können die folgenden Warnungen auslösen:

### <a name="anomalous-access-pattern-alerts"></a>Warnungen zu ungewöhnlichen Zugriffsmustern

* **Zugriff von einem ungewöhnlichen Ort**: Diese Warnung wird ausgelöst, wenn eine Änderung des Zugriffsmusters für SQL Server erfolgt ist, weil sich eine Person von einem ungewöhnlichen Ort aus bei SQL Server angemeldet hat. Mögliche Ursachen:
     * Ein Angreifer oder böswilliger ehemaliger Mitarbeiter hat auf Ihren SQL Server zugegriffen.
     * Ein berechtigter Benutzer hat von einem neuen Ort aus auf Ihren SQL Server zugegriffen.
* **Zugriff über eine potenziell schädliche Anwendung**: Diese Warnung wird ausgelöst, wenn zum Zugreifen auf die Datenbank eine potenziell schädliche Anwendung verwendet wird. Mögliche Ursachen:
     * Ein Angreifer versucht, mit gebräuchlichen Angriffstools Zugriff auf Ihren SQL Server zu erhalten.
     * Ein legitimer Penetrationstest wird ausgeführt.
* **Zugriff über einen unbekannten Prinzipal:** Diese Warnung wird ausgelöst, wenn eine Änderung des Zugriffsmusters für SQL Server erfolgt ist, weil sich eine Person über einen ungewöhnlichen Prinzipal (SQL-Benutzer) bei SQL Server angemeldet hat. Mögliche Ursachen:
     * Ein Angreifer oder böswilliger ehemaliger Mitarbeiter hat auf Ihren SQL Server zugegriffen. 
     * Ein berechtigter Benutzer hat mit einem neuen Prinzipal auf Ihren SQL Server zugegriffen.
* **Brute-Force-Angriff auf SQL-Anmeldeinformationen:** Diese Warnung wird ausgelöst, wenn eine ungewöhnlich hohe Anzahl von fehlerhaften Anmeldungen mit unterschiedlichen Anmeldeinformationen vorliegt. Mögliche Ursachen:
     * Ein Angreifer versucht, mit einem Brute-Force-Angriff Zugriff auf Ihren SQL Server zu erhalten.
     * Ein legitimer Penetrationstest wird ausgeführt.

### <a name="potential-sql-injection-attacks-coming"></a>Potenzielle Angriffe durch Einschleusung von SQL-Befehlen (zukünftig)

* **Anfälligkeit für die Einschleusung von SQL-Befehlen**: Diese Warnung wird ausgelöst, wenn eine Anwendung in der Datenbank eine fehlerhafte SQL-Anweisung generiert. Diese Warnung kann auf ein mögliches Sicherheitsrisiko in Bezug auf Angriffe mit Einschleusung von SQL-Befehlen hinweisen. Mögliche Ursachen:
     * Ein Fehler im Anwendungscode, der zur fehlerhaften SQL-Anweisung führt
     * Anwendungscode oder gespeicherte Prozeduren führen bei der Erstellung der fehlerhaften SQL-Anweisung keine Bereinigung der Benutzereingabe durch, und dies kann für eine Einschleusung von SQL-Befehlen ausgenutzt werden
* **Potenzielle Einschleusung von SQL-Befehlen:** Diese Warnung wird ausgelöst, wenn ein aktiver Exploit für ein identifiziertes Anwendungssicherheitsrisiko in Bezug auf die Einschleusung von SQL-Befehlen besteht. Dies bedeutet, dass der Angreifer versucht, schädliche SQL-Anweisungen einzuschleusen, indem er den anfälligen Anwendungscode bzw. die gespeicherten Prozeduren verwendet.
