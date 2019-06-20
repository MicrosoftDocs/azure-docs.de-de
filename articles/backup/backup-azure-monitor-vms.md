---
title: Überwachen von Sicherungswarnungen für virtuelle Azure-Computer
description: Überwachen Sie Ereignisse und Warnungen aus Sicherungsaufträgen virtueller Azure-Computer. Senden Sie E-Mails basierend auf Warnungen.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: raynew
ms.openlocfilehash: 350e13a4b1c01329bef1ec270af5ba007cd788aa
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399747"
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Überwachen von Warnungen für Sicherungen von virtuellen Azure-Computern

Warnungen sind Antworten des Diensts mit dem Hinweis, dass ein Ereignisschwellenwert erreicht oder überschritten wurde. Informationen zur zeitlichen Entstehung von Problemen können sehr wichtig sein, um die Geschäftskosten gering zu halten. Auftretende Warnungen halten sich normalerweise nicht an einen Zeitplan, und daher ist es wichtig informiert zu werden, sobald die Warnungen auftreten. Beispiel: Wenn bei einem Sicherungs- oder Wiederherstellungsauftrag ein Fehler auftritt, wird innerhalb von fünf Minuten nach Auftreten des Fehlers eine Warnung gesendet. Im Dashboard des Tresors werden auf der Kachel „Sicherungswarnungen“ Ereignisse der Ebenen „Kritisch“ und „Warnung“ angezeigt. In den Einstellungen der Sicherungswarnungen können Sie alle Ereignisse anzeigen. Aber was ist zu tun, wenn eine Warnung bei der Arbeit an einem anderen Problem auftritt? Wenn Sie nicht wissen, wann die Warnung auftritt, kann dies nur ein unbedeutender Vorfall sein, oder es kann zu einer Kompromittierung von Daten kommen. Stellen Sie wie folgt sicher, dass die richtigen Personen über eine Warnung benachrichtigt werden: Konfigurieren Sie den Dienst so, dass beim Auftreten Warnungsbenachrichtigungen per E-Mail gesendet werden. Weitere Informationen zum Einrichten von E-Mail-Benachrichtigungen finden Sie unter [Konfigurieren von Benachrichtigungen](backup-azure-monitor-vms.md#configure-notifications).

## <a name="how-do-i-find-information-about-the-alerts"></a>Wie finde ich Informationen zu den Warnungen?

Sie müssen den Abschnitt „Sicherungswarnungen“ öffnen, um Informationen zu dem Ereignis anzuzeigen, das eine Warnung ausgelöst hat. Es gibt zwei Möglichkeiten, den Abschnitt „Sicherungswarnungen“ zu öffnen: über die Kachel „Sicherungswarnungen“ im Dashboard des Tresors oder über den Abschnitt „Warnungen und Ereignisse“.

Gehen Sie wie folgt vor, um das Blatt „Sicherungswarnungen“ über die Kachel „Sicherungswarnungen“ zu öffnen:

* Klicken Sie im Dashboard des Tresors auf der Kachel **Sicherungswarnungen** auf **Kritisch** oder **Warnung**, um die Vorgangsereignisse für diesen Schweregrad anzuzeigen.

    ![Kachel „Sicherungswarnungen“](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

Gehen Sie wie folgt vor, um das Blatt „Sicherungswarnungen“ über den Abschnitt „Warnungen und Ereignisse“ zu öffnen:

1. Klicken Sie im Dashboard des Tresors auf **Alle Einstellungen**. ![Schaltfläche „Alle Einstellungen“](./media/backup-azure-monitor-vms/all-settings-button.png)
2. Klicken Sie auf dem Blatt **Einstellungen** auf **Warnungen und Ereignisse**. ![Schaltfläche „Warnungen und Ereignisse“](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. Klicken Sie auf dem Blatt **Warnungen und Ereignisse** auf **Sicherungswarnungen**. ![Schaltfläche „Sicherungswarnungen“](./media/backup-azure-monitor-vms/backup-alerts.png)

    Der Abschnitt **Sicherungswarnungen** wird geöffnet, und die gefilterten Warnungen werden angezeigt.

    ![Kachel „Sicherungswarnungen“](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. Klicken Sie in der Liste mit den Ereignissen auf eine Warnung, um den dazugehörigen Abschnitt **Details** zu öffnen, wenn Sie ausführliche Informationen zu einer bestimmten Warnung anzeigen möchten.

    ![Ereignisdetails](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    Informationen zum Anpassen der Attribute, die in der Liste angezeigt werden, finden Sie unter [Anzeigen von weiteren Ereignisattributen](backup-azure-monitor-vms.md)

## <a name="configure-notifications"></a>Konfigurieren von Benachrichtigungen

 Sie können den Dienst so konfigurieren, dass E-Mail-Benachrichtigungen für die Warnungen gesendet werden, die im Verlauf der letzten Stunde aufgetreten sind oder wenn bestimmte Arten von Ereignissen eintreten.

So richten Sie E-Mail-Benachrichtigungen für Warnungen ein

1. Klicken Sie im Menü „Sicherungswarnungen“ auf **Konfigurieren von Benachrichtigungen**

    ![Menü „Sicherungswarnungen“](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    Der Abschnitt „Benachrichtigungen konfigurieren“ wird geöffnet.

    ![Blatt „Benachrichtigungen konfigurieren“](./media/backup-azure-monitor-vms/configure-notifications.png)
2. Klicken Sie im Abschnitt „Benachrichtigungen konfigurieren“ für E-Mail-Benachrichtigungen auf **Ein**.

    Die Bereiche „Empfänger“ und „Schweregrad“ sind mit einem Sternchen gekennzeichnet, weil die Angabe dieser Informationen erforderlich ist. Geben Sie mindestens eine E-Mail-Adresse ein, und wählen Sie mindestens einen Schweregrad aus.
3. Geben Sie unter **Empfänger (E-Mail)** die E-Mail-Adressen für die Personen ein, die die Benachrichtigungen erhalten sollen. Verwenden Sie das folgende Format: username@domainname.com. Trennen Sie mehrere E-Mail-Adressen per Semikolon (;).
4. Wählen Sie im Bereich **Benachrichtigen** die Option **Pro Warnung**, damit eine Benachrichtigung gesendet wird, wenn die angegebene Warnung auftritt. Wählen Sie die Option **Stündliche Übersicht**, wenn eine Übersicht für die letzte Stunde gesendet werden soll.
5. Wählen Sie unter **Schweregrad** mindestens eine Ebene aus, bei der die E-Mail-Benachrichtigung ausgelöst werden soll.
6. Klicken Sie auf **Speichern**.

### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Welche Warnungstypen für die Azure IaaS-VM-Sicherung verfügbar sind

   | Warnstufe | Gesendete Warnungen |
   | --- | --- |
   | Kritisch | Für Sicherungsfehler, Wiederherstellungsfehler |
   | Warnung | Derzeit sind keine Warnungen für Azure-VM-Sicherungen verfügbar (z. B. einige fehlerhafte Schreibvorgänge beim Erstellen einer Momentaufnahme). |
   | Information | Aktuell sind keine Informationsmeldungen für die Azure-VM-Sicherung verfügbar. |

### <a name="situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>Fälle, in denen keine E-Mail gesendet wird, auch wenn Benachrichtigungen konfiguriert wurden

Es gibt Situationen, in denen keine Warnung gesendet wird, obwohl die Benachrichtigungen richtig konfiguriert wurden. In den folgenden Situationen werden keine E-Mail-Benachrichtigungen gesendet, um unnötige Warnungen zu vermeiden:

* Für Benachrichtigungen wurde die stündliche Übersicht konfiguriert, und eine Warnung wird ausgelöst und innerhalb dieser Stunde gelöst.
* Der Auftrag wird abgebrochen.
* Ein Sicherungsauftrag wird ausgelöst und schlägt dann fehl, und ein weiterer Sicherungsauftrag wird ausgeführt.
* Ein geplanter Sicherungsauftrag für einen im Resource Manager-Modus aktivierten virtuellen Computer wird gestartet, der virtuelle Computer ist jedoch nicht mehr vorhanden.

## <a name="using-activity-logs-to-get-notifications-for-successful-backups"></a>Verwenden von Aktivitätsprotokollen, um Benachrichtigungen für erfolgreiche Sicherungen zu erhalten

> [!NOTE]
> Wir haben jetzt ein neues Modell eingesetzt, um Aktivitätsprotokolle aus Azure Backup in Recovery Services-Tresore zu verschieben. Leider hat dies die Generierung von Aktivitätsprotokollen in Azure-Sovereign Clouds beeinträchtigt. Wenn Benutzer von Azure-Sovereign Clouds Warnungen aus den Aktivitätsprotokollen über Azure Monitor wie hier beschrieben erstellt/konfiguriert haben, würden sie nicht ausgelöst werden. In diesem Fall empfehlen wir solchen Benutzern, Diagnoseeinstellungen und LA-Arbeitsbereich oder [Power BI-Berichterstellung](backup-azure-configure-reports.md) zu verwenden, um die relevanten Informationen zu erhalten. Außerdem würden in allen öffentlichen Azure-Regionen, wenn ein Benutzer Recovery Services-Aktivitätsprotokolle wie [hier](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity) erwähnt in einem Log Analytics-Arbeitsbereich erfasst, auch diese Protokolle nicht angezeigt.

Wenn Sie nach erfolgreichen Sicherungen benachrichtigt werden möchten, können Sie Warnungen verwenden, die auf den [Aktivitätsprotokollen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) des Tresors basieren.

### <a name="login-into-azure-portal"></a>Anmelden beim Azure-Portal

Melden Sie sich beim Azure-Portal an, rufen Sie den relevanten Azure Recovery Services-Tresor auf, und klicken Sie in den Eigenschaften auf den Abschnitt „Aktivitätsprotokoll“.

### <a name="identify-appropriate-log"></a>Identifizieren des geeigneten Protokolls

Wenden Sie die in der folgenden Abbildung gezeigten Filter an, um zu überprüfen, ob Sie Aktivitätsprotokolle für erfolgreiche Sicherungen empfangen. Ändern Sie den Zeitraum zum Anzeigen von Datensätzen entsprechend.

![Aktivitätsprotokolle](./media/backup-azure-monitor-vms/activity-logs-identify.png)

Sie können auf das Segment „JSON“ klicken, um weitere Details abzurufen und diese durch Kopieren und Einfügen in einen Text-Editor anzeigen. Es sollten die Tresordetails und das Element angezeigt werden, welches das Aktivitätsprotokoll ausgelöst hat, d.h. das Sicherungselement.

Klicken Sie dann auf „Aktivitätsprotokollwarnung hinzufügen“, um Warnungen für alle solchen Protokolle zu generieren.

### <a name="add-activity-log-alert"></a>Aktivitätsprotokollwarnung hinzufügen

Wenn Sie auf „Aktivitätsprotokollwarnung hinzufügen“ klicken, wird ein Bildschirm wie der folgende angezeigt

![Aktivitätsprotokollwarnung](./media/backup-azure-monitor-vms/activity-logs-alerts-successful.png): Das Abonnement und die Ressourcengruppe werden zum Speichern der Warnung verwendet. Die Kriterien werden vorab ausgefüllt. Stellen Sie sicher, dass alle Werte für Ihre Anforderungen relevant sind.

Für erfolgreiche Sicherungen wird die Ebene als „Information“ und der Status als „Erfolgreich“ angegeben.

Wenn Sie oben eine „Ressource“ auswählen, wird die Warnung generiert, wenn für diese Ressource bzw. diesen Tresor Aktivitätsprotokolle aufgezeichnet werden. Wenn die Regel für alle Tresore gelten soll, lassen Sie „Ressource“ leer.

### <a name="define-action-on-alert-firing"></a>Definieren der Aktion bei Warnungsauslösung

Verwenden Sie die „Aktionsgruppe“ zum Definieren der Aktion nach dem Generieren einer Warnung. Sie können auf „Aktionstyp“ klicken, um mehr zu verfügbaren Aktionen wie E-Mail/SMS/Integration in ITSM usw. zu erfahren.

![Aktionsgruppe des Aktivitätsprotokolls](./media/backup-azure-monitor-vms/activity-logs-alerts-action-group.png)

Sobald Sie auf „OK“ klicken, wird eine Aktivitätsprotokollwarnung generiert, und nachfolgende für erfolgreiche Sicherungen aufgezeichnete Aktivitätsprotokolle lösen die in der Aktionsgruppe definierte Aktion aus.

### <a name="limitations-on-alerts"></a>Einschränkungen für Warnungen

Ereignisbasierte Warnungen unterliegen den folgenden Einschränkungen:

1. Warnungen werden auf allen virtuellen Computern im Recovery Services-Tresor ausgelöst. Es ist nicht möglich, die Warnung für eine Teilmenge von virtuellen Computern in einem Recovery Services-Tresor anzupassen.
2. Warnungen werden von alerts-noreply@mail.windowsazure.com gesendet. Sie können den E-Mail-Absender derzeit nicht ändern.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum erneuten Erstellen eines virtuellen Computers über einen Wiederherstellungspunkt finden Sie unter [Wiederherstellen virtueller Azure-Computer](backup-azure-arm-restore-vms.md).

Informationen zum Schutz Ihrer virtuellen Computer finden Sie unter [Einführung: Sichern virtueller Azure-Computer in Recovery Services-Tresor](backup-azure-vms-first-look-arm.md).

Weitere Informationen zu den Verwaltungsaufgaben für VM-Sicherungen finden Sie im Artikel [Verwalten der Sicherungen von virtuellen Azure-Computern](backup-azure-manage-vms.md).
