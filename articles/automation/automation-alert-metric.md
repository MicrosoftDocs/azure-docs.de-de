---
title: Überwachen von Azure Automation-Runbooks mit Metrikwarnungen
description: In diesem Artikel erfahren Sie, wie Sie Azure Automation-Runbooks anhand von Metriken überwachen.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 11/01/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 7932d057a348957d369ba325044055ac8dfe3428
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62119893"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>Überwachen von Runbooks mit Metrikwarnungen

In diesem Artikel erfahren Sie, wie Sie Warnungen basierend auf den Abschlussstatus von Runbooks erstellen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com bei Azure an.

## <a name="create-alert"></a>Erstellen einer Warnung

Mit Warnungen können Sie eine zu überwachende Bedingung und eine auszuführende Aktion definieren, die ausgeführt wird, sobald diese Bedingung erfüllt ist.

Navigieren Sie im Azure-Portal zu Ihrem Automation-Konto. Wählen Sie unter **Überwachen** **Warnungen** aus, und klicken Sie auf **+ Neue Warnungsregel**. Der Zielbereich ist bereits in Ihrem Automation-Konto definiert.

### <a name="configure-alert-criteria"></a>Konfigurieren der Warnungskriterien

1. Klicken Sie auf **+ Kriterien hinzufügen**. Wählen Sie **Metriken** für den **Signaltyp** aus, und wählen Sie in der Tabelle **Aufträge gesamt** aus.

2. Auf der Seite **Signallogik konfigurieren** definieren Sie die Logik, die die Warnung auslöst. Unter dem Verlaufsdiagramm werden Ihnen zwei Dimensionen angezeigt, **Runbookname** und **Status**. Dimensionen sind verschiedene Eigenschaften für eine Metrik, die zum Filtern von Ergebnissen verwendet werden kann. Wählen Sie unter **Runbookname** das Runbook aus, für das eine Warnung ausgegeben werden soll, oder lassen Sie das Feld leer, um für alle Runbooks Warnungen auszugeben. Wählen Sie unter **Status** in der Dropdownliste einen Status aus, den Sie überwachen möchten. Der Name des Runbooks und die Statuswerte, die in der Dropdownliste angezeigt werden, gelten nur für Aufträge, die in der letzten Woche ausgeführt wurden.

   Wenn Sie eine Warnung zu einem Status oder einem Runbook ausgeben möchten, die nicht in der Dropdownliste angezeigt wird, klicken Sie auf **\+** neben der Dimension. Daraufhin wird ein Dialogfeld geöffnet, in dem Sie einen benutzerdefinierten Wert eingeben können, der in letzter Zeit für diese Dimension nicht ausgegeben wurde. Wenn Sie einen Wert eingeben, der für eine Eigenschaft nicht existiert, wird Ihre Warnung nicht ausgelöst.

   > [!NOTE]
   > Wenn Sie keinen Namen für die **RunbookName**-Dimension anwenden, erhalten Sie eine Warnung, wenn Runbooks vorhanden sind, die die Statuskriterien erfüllen, was auch ausgeblendete System-Runbooks umfasst.

3. Definieren Sie unter **Warnungslogik** die Bedingung und den Schwellenwert für Ihre Warnung. Unten sehen Sie eine Vorschau Ihrer definierten Bedingung.

4. Wählen Sie unter **Auswertung basierend auf** den Zeitraum und die Ausführungshäufigkeit für die Abfrage aus. Wenn Sie beispielsweise **In den letzten 5 Minuten** für **Zeitraum** und **Jede Minute** für **Häufigkeit** wählen, sucht die Warnung nach der Anzahl der Runbooks, die Ihre Kriterien in den letzten 5 Minuten erfüllt haben. Diese Abfrage wird jede Minute ausgeführt, und sobald die von Ihnen definierten Warnungskriterien nicht mehr in einem 5-Minuten-Fenster gefunden werden, löst sich die Warnung von selbst auf. Klicken Sie abschließend auf **Fertig**.

   ![Auswählen einer Ressource für die Warnung](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Definieren von Warnungsdetails

1. Wählen Sie unter **2. Warnungsdetails definieren** einen Anzeigenamen und eine Beschreibung für die Warnung aus. Stellen Sie den **Schweregrad** entsprechend Ihrer Warnungsbedingung ein. Es gibt fünf Schweregrade von 0 bis 5. Die Warnungen werden unabhängig vom Schweregrad gleich behandelt. Sie können den Schweregrad an Ihre Geschäftslogik anpassen.

1. Am Ende des Abschnitts befindet sich eine Schaltfläche, mit der Sie die Regel nach Abschluss aktivieren können. Standardmäßig sind die Regeln bei der Erstellung aktiviert. Wenn Sie „Nein“ auswählen, können Sie die Warnung im Status **Deaktiviert** erstellen. Wenn Sie die Warnung aktivieren möchten, wählen Sie diese in Azure Monitor auf der Seite **Regeln** aus, und klicken Sie auf **Aktivieren**.

### <a name="define-the-action-to-take"></a>Definieren der auszuführenden Aktion

1. Klicken Sie unter **3. Aktionsgruppe definieren** auf **+ Neue Aktionsgruppe**. Eine Aktionsgruppe ist eine Gruppe von Aktionen, die Sie für mehrere Warnungen verwenden können. Dies können beispielsweise E-Mail-Benachrichtigungen, Runbooks und Webhooks sein. Weitere Informationen zu Aktionsgruppen finden Sie unter [Erstellen und Verwalten von Aktionsgruppen im Azure-Portal](../azure-monitor/platform/action-groups.md).

1. Geben Sie im Feld **Name der Aktionsgruppe** einen Anzeigenamen und einen Kurznamen an. Der Kurzname wird anstelle eines vollständigen Aktionsgruppennamens verwendet, wenn Benachrichtigungen mithilfe dieser Gruppe gesendet werden.

1. Wählen Sie im Abschnitt **Aktionen** unter **AKTIONSTYP** die Option **E-Mail/SMS/Push/Sprachanruf** aus.

1. Vergeben Sie auf der Seite **E-Mail/SMS/Push/Sprachanruf** einen Namen. Aktivieren Sie das Kontrollkästchen **E-Mail**, und geben Sie eine gültige E-Mail-Adresse ein.

   ![Konfigurieren der E-Mail-Aktionsgruppe](./media/automation-alert-activity-log/add-action-group.png)

1. Klicken Sie auf der Seite **E-Mail/SMS/Push/Sprachanruf** auf **OK**, um sie zu schließen, und anschließend erneut auf **OK**, um die Seite **Aktionsgruppe hinzuzufügen** zu schließen. Der auf dieser Seite angegebene Name wird als **AKTIONSNAME** gespeichert.

1. Klicken Sie auf **Speichern**, wenn Sie fertig sind. Dadurch wird die Regel erstellt, die Sie benachrichtigt, wenn ein Runbook mit einem bestimmten Status abgeschlossen wird.

> [!NOTE]
> Beim Hinzufügen einer E-Mail-Adresse zu einer Aktionsgruppe wird eine Benachrichtigungs-E-Mail gesendet, die angibt, dass die Adresse zu einer Aktionsgruppe hinzugefügt wurde.

## <a name="notification"></a>Benachrichtigung

Wenn die Warnungskriterien erfüllt sind, führt die Aktionsgruppe die definierte Aktion aus. Im Beispiel dieses Artikels wird eine E-Mail verschickt. In der folgenden Abbildung sehen Sie ein Beispiel für eine E-Mail, die Sie nach dem Auslösen der Warnung empfangen:

![E-Mail-Warnung](./media/automation-alert-activity-log/alert-email.png)

Wenn die Metrik nicht mehr außerhalb des definierten Schwellenwerts liegt, wird die Warnung deaktiviert und die Aktionsgruppe führt die definierte Aktion aus. Wenn ein E-Mail-Aktionstyp ausgewählt ist, wird eine Auflösungs-E-Mail gesendet, die angibt, dass sie aufgelöst wurde.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem folgenden Artikel fort, um mehr über andere Möglichkeiten zu erfahren, wie Sie Benachrichtigungen in Ihr Automation-Konto integrieren können.

> [!div class="nextstepaction"]
> [Verwenden einer Warnung zum Auslösen eines Azure Automation-Runbooks](automation-create-alert-triggered-runbook.md)
