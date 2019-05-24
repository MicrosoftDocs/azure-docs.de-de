---
title: Überwachung der Dateiintegrität in Azure Security Center | Microsoft-Dokumentation
description: " Erfahren Sie, wie Sie die Überwachung der Dateiintegrität in Azure Security Center aktivieren. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: v-mohabe
ms.openlocfilehash: cc0c319357b39ddb3e88d515613273a6f7dc0867
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968798"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Überwachung der Dateiintegrität in Azure Security Center
In dieser exemplarischen Vorgehensweise erfahren Sie, wie Sie die Überwachung der Dateiintegrität in Azure Security Center konfigurieren.

## <a name="what-is-fim-in-security-center"></a>Was ist die Überwachung der Dateiintegrität in Security Center?
Die Überwachung der Dateiintegrität, auch als Überwachung von Änderungen bekannt, untersucht unter anderem Dateien und Registrierungen des Betriebssystems nach Änderungen, die auf einen Angriff deuten. Eine Vergleichsmethode wird verwendet, um zu bestimmen, ob der aktuelle Status der Datei sich von der letzten Überprüfung der Datei unterscheidet. Sie können diesen Vergleich nutzen, um zu bestimmen, ob gültige oder verdächtige Änderungen an Ihren Dateien vorgenommen wurden.

Die Überwachung der Dateiintegrität von Security Center überprüft die Integrität von Windows-Dateien, Windows-Registrierungen und Linux-Dateien. Sie können die zu überwachenden Dateien auswählen, indem Sie die Überwachung der Dateiintegrität aktivieren. In Security Center werden Dateien überwacht, indem die Überwachung der Dateiintegrität für folgende Aktivitäten aktiviert wird:

- Erstellen und Löschen von Dateien und Registrierungen
- Änderungen an Dateien (an der Dateigröße, der Zugriffssteuerungsliste und dem Hash des Inhalts)
- Änderungen an Registrierungen (an der Größe, der Zugriffssteuerungsliste, am Typ und am Inhalt)

In Security Center erhalten Sie Empfehlungen dafür, welche Entitäten überwacht werden sollen. Für diese können Sie die Überwachung der Dateiintegrität einfach aktivieren. Sie können ebenfalls eigene Richtlinien oder Entitäten zur Überwachung durch die Überwachung der Dateiintegrität definieren. Dies wird in der exemplarischen Vorgehensweise erläutert.

> [!NOTE]
> Das Feature „Überwachung der Dateiintegrität“ funktioniert auf physischen und virtuellen Windows- und Linux-Computern und ist im Standard-Tarif von Security Center enthalten. Weitere Informationen zu den Tarifen von Security Center finden Sie unter [Preise](security-center-pricing.md). Die Überwachung der Dateiintegrität lädt Daten in den Log Analytics-Arbeitsbereich hoch. Die Gebühren für diese Daten hängen von der hochgeladenen Datenmenge ab. Weitere Informationen erhalten Sie unter [Log Analytics – Preise](https://azure.microsoft.com/pricing/details/log-analytics/).

Die Überwachung der Dateiintegrität verwendet die Azure-Lösung „Änderungsnachverfolgung“, um Änderungen in Ihrer Umgebung nachzuverfolgen und zu identifizieren. Wenn die Überwachung der Dateiintegrität aktiviert ist, besitzen Sie die Ressource **Änderungsnachverfolgung** vom Typ **Lösung**. Informationen zur Häufigkeit der Datensammlung für die Azure-Änderungsnachverfolgung finden Sie unter [Details zur Datensammlung für die Änderungsnachverfolgung](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details).

> [!NOTE]
> Wenn Sie die Ressource **Änderungsnachverfolgung** entfernen, deaktivieren Sie damit auch das Feature „Überwachung der Dateiintegrität“ in Security Center.

## <a name="which-files-should-i-monitor"></a>Welche Dateien sollten überwacht werden?
Sie sollten sich überlegen, welche Dateien für Ihr System und Ihre Anwendungen entscheidend sind, wenn Sie auswählen, welche Dateien überwacht werden sollen. Sie sollten Dateien auswählen, von denen Sie keine ungeplanten Änderungen erwarten. Wenn Sie Dateien auswählen, die häufig von Anwendungen oder dem Betriebssystem geändert werden (z.B. Protokoll- und Textdateien), werden viele Aktionen generiert, die das Identifizieren eines Angriffs erschweren.

In Security Center erhalten Sie Empfehlungen, welche Dateien überwacht werden sollten. Diese basieren standardmäßig auf bekannten Angriffsmustern, die Änderungen an Dateien und Registrierungen beinhalten.

## <a name="using-file-integrity-monitoring"></a>Verwenden der Überwachung der Dateiintegrität
1. Öffnen Sie das Dashboard **Security Center**.
2. Klicken Sie im linken Bereich unter **Erweiterter Cloudschutz** auf **Überwachung der Dateiintegrität**.
![Security Center-Dashboard][1]

Die **Überwachung der Dateiintegrität** wird geöffnet.
  ![Security Center-Dashboard][2]

Folgende Informationen werden für jeden Arbeitsbereich angegeben:

- Die Gesamtanzahl der Änderungen, die in der letzten Woche aufgetreten sind (wenn die Überwachung der Dateiintegrität im Arbeitsbereich deaktiviert ist, wird „–“ angezeigt)
- Die Gesamtanzahl der physischen und virtuellen Computer, die Berichte an den Arbeitsbereich senden
- Der geografische Standort des Arbeitsbereichs
- Das Azure-Abonnement, dem der Arbeitsbereich unterliegt

Außerdem können folgende Schaltflächen für einen Arbeitsbereich angezeigt werden:

- ![Symbol „Aktivieren“][3] Gibt an, dass die Überwachung der Dateiintegrität nicht für den Arbeitsbereich aktiviert ist. Durch das Auswählen des Arbeitsbereichs können Sie die Überwachung der Dateiintegrität auf allen Computern im Arbeitsbereich aktivieren.
- ![Symbol „Upgradeplan“][4] Gibt an, dass der Arbeitsbereich oder das Abonnement nicht im Standard-Tarif von Security Center ausgeführt wird. Ihr Abonnement muss sich im Standard-Tarif befinden, um das Feature „Überwachung der Dateiintegrität“ zu verwenden.  Wenn Sie den Arbeitsbereich auswählen, können Sie ein Upgrade auf den Standard-Tarif durchführen. Weitere Informationen zum Standard-Tarif und zum Upgradevorgang finden Sie unter [Upgrade to Security Center's Standard tier for enhanced security (Upgrade auf den Standard-Tarif von Azure Security Center für erhöhte Sicherheit)](security-center-pricing.md).
- Wenn keine Schaltfläche vorhanden ist, bedeutet das, dass die Überwachung der Dateiintegrität für den Arbeitsbereich bereits aktiviert ist.

Unter **Überwachung der Dateiintegrität** können Sie einen Arbeitsbereich auswählen, für den diese aktiviert werden soll, das Dashboard „Überwachung der Dateiintegrität“ für diesen Arbeitsbereich anzeigen oder den Arbeitsbereich auf den Standard-Tarif [upgraden](security-center-pricing.md).

## <a name="enable-fim"></a>Aktivieren der Überwachung der Dateiintegrität
So aktivieren Sie die Überwachung der Dateiintegrität in einem Arbeitsbereich:

1. Wählen Sie unter **Überwachung der Dateiintegrität** einen Arbeitsbereich mithilfe der Schaltfläche **Aktivieren** aus.
2. Mithilfe von **Überwachung der Dateiintegrität aktivieren** wird die Anzahl von Windows- und Linux-Computern im Arbeitsbereich angezeigt.

   ![Aktivieren der Überwachung der Dateiintegrität][5]

   Die empfohlenen Einstellungen für Windows und Linux werden ebenfalls aufgeführt.  Erweitern Sie **Windows-Dateien**, **Registrierung** und **Linux-Dateien**, um die vollständige Liste der empfohlenen Elemente anzuzeigen.

3. Deaktivieren Sie die Kontrollkästchen für alle empfohlenen Entitäten, auf die Sie die Überwachung der Dateiintegrität nicht anwenden möchten.
4. Klicken Sie auf **Überwachung der Dateiintegrität anwenden**, um die Überwachung der Dateiintegrität zu aktivieren.

> [!NOTE]
> Sie können die Einstellungen jederzeit ändern. Weitere Informationen finden Sie im Folgenden unter „Edit monitored entities“ (Bearbeiten überwachter Entitäten).
>
>

## <a name="view-the-fim-dashboard"></a>Anzeigen des Dashboards „Überwachung der Dateiintegrität“
Das Dashboard **Überwachung der Dateiintegrität** zeigt die Arbeitsbereiche an, für den die Überwachung der Dateiintegrität aktiviert ist. Das Dashboard „Überwachung der Dateiintegrität“ wird geöffnet, nachdem Sie die Überwachung der Dateiintegrität für einen Arbeitsbereich aktiviert haben oder wenn Sie einen Arbeitsbereich im Fenster **Überwachung der Dateiintegrität** auswählen, für den diese bereits aktiviert ist.

![Dashboard „Überwachung der Dateiintegrität“][6]

Im Dashboard „Überwachung der Dateiintegrität“ für einen Arbeitsbereich wird Folgendes angezeigt:

- Die Gesamtanzahl der mit dem Arbeitsbereich verbundenen Computer
- Die Gesamtanzahl der Änderungen, die im ausgewählten Zeitraum aufgetreten sind
- Eine Aufschlüsselung des Änderungstyps (Dateien, Registrierungen)
- Eine Aufschlüsselung der Änderungskategorie (Geändert, Hinzugefügt, Entfernt)

Wenn Sie im oberen Bereich des Dashboards auf „Filter“ klicken, können Sie den Zeitraum auswählen, für den die Änderungen angezeigt werden sollen.

![Filter „Zeitraum“][7]

Die Registerkarte **Computer** (siehe oben) führt alle Computer auf, die Berichte an diesen Arbeitsbereich senden. Für jeden Computer führt das Dashboard Folgendes auf:

- Die Gesamtanzahl der Änderungen, die im ausgewählten Zeitraum aufgetreten sind
- Eine Aufschlüsselung der Gesamtanzahl der Änderungen in Änderungen von Dateien oder Registrierungen

Die **Protokollsuche** wird geöffnet, wenn Sie den Namen eines Computers in das Suchfeld eingeben oder einen Computer auswählen, der auf der Registerkarte „Computer“ aufgeführt ist. Die Protokollsuche zeigt alle Änderungen an, die im ausgewählten Zeitraum am Computer durchgeführt wurden. Sie können eine Änderung erweitern, um weitere Informationen zu erhalten.

![Protokollsuche][8]

Die Registerkarte **Änderungen** (siehe unten) führt alle Änderungen auf, die im ausgewählten Zeitraum am Arbeitsbereich durchgeführt wurden. Für jede geänderte Entität führt das Dashboard Folgendes auf:

- Den Computer, auf dem die Änderung aufgetreten ist
- Den Änderungstyp (Registrierung oder Datei)
- Die Änderungskategorie (Geändert, Hinzugefügt, Entfernt)
- Das Datum und die Uhrzeit der Änderung

![Änderungen am Arbeitsbereich][9]

**Änderungsdetails** wird geöffnet, wenn Sie eine Änderung in das Suchfeld eingeben oder eine Entität auswählen, die in der Registerkarte **Änderungen** aufgeführt ist.

![Änderungsdetails][10]

## <a name="edit-monitored-entities"></a>Bearbeiten von überwachten Entitäten

1. Kehren Sie zum Dashboard **Überwachung der Dateiintegrität** zurück, und klicken Sie auf **Einstellungen**.

   ![Einstellungen][11]

   **Arbeitsbereichskonfiguration** wird geöffnet, und diese drei Registerkarten werden angezeigt: **Windows-Registrierung**, **Windows-Dateien** und **Linux-Dateien**. Jede Registerkarte führt die Entitäten auf, die Sie in dieser Kategorie bearbeiten können. Für jede aufgeführte Entität identifiziert Security Center, ob die Überwachung der Dateiintegrität aktiviert (true) (wahr) oder deaktiviert (false) (falsch) ist.  Wenn Sie eine Entität bearbeiten, können Sie die Überwachung der Dateiintegrität aktivieren oder deaktivieren.

   ![Arbeitsbereichskonfiguration][12]

2. Wählen Sie einen Identitätsschutz aus. In diesem Beispiel wurde ein Element unter „Windows-Registrierung“ ausgewählt. Das Fenster **Edit for Change Tracking** (Für die Änderungsnachverfolgung bearbeiten) wird geöffnet.

   ![Für die Änderungsnachverfolgung bearbeiten][13]

Unter **Edit for Change Tracking** (Für die Änderungsnachverfolgung bearbeiten) können Sie Folgendes durchführen:

- Aktivieren (True) (Wahr) oder deaktivieren (False) (Falsch) Sie die Überwachung der Dateiintegrität.
- Geben Sie den Namen der Entität an, oder ändern Sie diesen.
- Geben Sie den Wert oder Pfad an, oder ändern Sie diesen.
- Löschen Sie die Entität, verwerfen Sie die Änderung, oder speichern Sie die Änderung.

## <a name="add-a-new-entity-to-monitor"></a>Hinzufügen einer neuen zu überwachenden Entität
1. Kehren Sie zum Dashboard **Überwachung der Dateiintegrität** zurück, und wählen Sie im oberen Bereich **Einstellungen** aus. Die **Arbeitsbereichskonfiguration** wird geöffnet.
2. Klicken Sie unter **Arbeitsbereichskonfiguration** auf die Registerkarte für den Typ der Entität, die Sie hinzufügen möchten: Windows-Registrierung, Windows-Dateien oder Linux-Dateien. In diesem Beispiel wurde **Linux-Dateien** ausgewählt.

   ![Hinzufügen eines neuen zu überwachenden Elements][14]

3. Wählen Sie **Hinzufügen**. Das Fenster **Add for Change Tracking** (Für die Änderungsnachverfolgung hinzufügen) wird geöffnet.

   ![Eingabe der angeforderten Informationen][15]

4. Geben Sie auf der Seite **Hinzufügen** die angeforderten Informationen ein, und klicken Sie auf **Speichern**.

## <a name="disable-monitored-entities"></a>Deaktivieren von überwachten Entitäten
1. Kehren Sie zum Dashboard **Überwachung der Dateiintegrität** zurück.
2. Wählen Sie einen Arbeitsbereich aus, für den die Überwachung der Dateiintegrität derzeit aktiviert ist. Die Überwachung der Dateiintegrität ist für einen Arbeitsbereich aktiviert, wenn die Schaltflächen „Aktivieren“ und „Upgradeplan“ nicht vorhanden sind.

   ![Auswählen eines Arbeitsbereichs, bei dem die Überwachung der Dateiintegrität aktiviert ist][16]

3. Klicken Sie unter „Überwachung der Dateiintegrität“ auf **Einstellungen**.

   ![Klicken auf „Einstellungen“][17]

4. Klicken Sie unter **Arbeitsbereichskonfiguration** auf eine Gruppe, für die **Aktiviert** auf „true“ (wahr) festgelegt ist.

   ![Arbeitsbereichskonfiguration][18]

5. Legen Sie **Aktiviert** im Fenster **Edit for Change Tracking** (Für Änderungsnachverfolgung bearbeiten) auf „False“ (Falsch) fest.

   ![Festlegen von „Aktiviert“ auf „Falsch“][19]

6. Wählen Sie **Speichern** aus.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Ordner- und Pfadüberwachung mithilfe von Platzhaltern

Verwenden Sie Platzhalter, um die verzeichnisübergreifende Nachverfolgung zu vereinfachen. Beim Konfigurieren der Ordnerüberwachung mithilfe von Platzhaltern gelten die folgenden Regeln:
-   Platzhalter werden zum Nachverfolgen mehrerer Dateien benötigt.
-   Platzhalter können nur im letzten Segment eines Pfads verwendet werden, z.B. „C:\Ordner\Datei“ oder „/etc/*.conf“.
-   Wenn eine Umgebungsvariable einen ungültigen Pfad enthält, ist die Überprüfung zwar erfolgreich, bei der Ausführung der Inventur tritt jedoch ein Fehler für den Pfad auf.
-   Vermeiden Sie beim Festlegen des Pfads die Verwendung allgemeiner Pfade wie „c:\*.*“, da in diesem Fall zu viele Ordner durchlaufen werden müssen.

## <a name="disable-fim"></a>Deaktivieren der Überwachung der Dateiintegrität
Sie können die Überwachung der Dateiintegrität deaktivieren. Die Überwachung der Dateiintegrität verwendet die Azure-Lösung „Änderungsnachverfolgung“, um Änderungen in Ihrer Umgebung nachzuverfolgen und zu identifizieren. Wenn Sie die Überwachung der Dateiintegrität deaktivieren, wird die Lösung für die Änderungsnachverfolgung aus dem ausgewählten Arbeitsbereich entfernt.

1. Kehren Sie zum Dashboard **Überwachung der Dateiintegrität** zurück, um die Überwachung der Dateiintegrität zu deaktivieren.
2. Wählen Sie einen Arbeitsbereich aus.
3. Klicken Sie unter **Überwachung der Dateiintegrität** auf **Deaktivieren**.

   ![Deaktivieren der Überwachung der Dateiintegrität][20]

4. Klicken Sie auf **Entfernen**, um sie zu deaktivieren.

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie mehr darüber erfahren, wie die Überwachung der Dateiintegrität in Security Center verwendet wird. Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

* [Festlegen von Sicherheitsrichtlinien:](tutorial-security-policy.md) Erfahren Sie, wie Sie Sicherheitsrichtlinien für Ihre Azure-Abonnements und -Ressourcengruppen konfigurieren.
* [Verwalten von Sicherheitsempfehlungen:](security-center-recommendations.md) Erfahren Sie, wie Empfehlungen Ihnen beim Schutz Ihrer Azure-Ressourcen helfen.
* [Überwachen der Sicherheitsintegrität:](security-center-monitoring.md) Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
* [Verwalten von und Reagieren auf Sicherheitswarnungen:](security-center-managing-and-responding-alerts.md) Erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
* [Überwachen von Partnerlösungen:](security-center-partner-solutions.md) Erfahren Sie, wie der Integritätsstatus Ihrer Partnerlösungen überwacht wird.
* [Security Center – Häufig gestellte Fragen:](security-center-faq.md) Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/)(Blog zur Azure-Sicherheit): Hier finden Sie Neuigkeiten und Informationen zur Azure-Sicherheit.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[2]: ./media/security-center-file-integrity-monitoring/file-integrity-monitoring.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[6]: ./media/security-center-file-integrity-monitoring/fim-dashboard.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png
