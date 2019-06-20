---
title: Einrichten von Warnungen in Azure Application Insights | Microsoft Docs
description: Legen Sie fest, dass Sie über längere Reaktionszeiten, Ausnahmen und andere Leistungs- oder Nutzungsänderungen in Ihrer Web-App informiert werden.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.reviewer: lagayhar
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: mbullwin
ms.openlocfilehash: eb8e98f66d000290ce7eb07d3d73e82fbc43514a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60693384"
---
# <a name="set-alerts-in-application-insights"></a>Einrichten von Warnungen in Application Insights
[Azure Application Insights][start] kann Sie bei Änderungen der Leistung oder von Nutzungsmetriken in Ihrer Web-App benachrichtigen. 

Application Insights überwacht die Live-App auf einer [Vielzahl von Plattformen][platforms], um Sie bei der Diagnose von Leistungsproblemen und beim Auswerten von Nutzungsmustern zu unterstützen.

Es gibt mehrere Warnungstypen:

* [**Metrikwarnungen**](../../azure-monitor/platform/alerts-metric-overview.md) informieren Sie darüber, wenn eine Metrik für einen bestimmten Zeitraum einen Schwellenwert überschreitet – z. B. in Bezug auf Reaktionszeiten, Anzahl von Ausnahmen, CPU-Nutzung oder Seitenaufrufe.
* [**Protokollwarnungen**](../../azure-monitor/platform/alerts-unified-log.md) werden dazu verwendet, Warnungen zu beschreiben, bei denen das Warnsignal auf einer benutzerdefinierten Kusto-Abfrage basiert.
* [**Webtests**][availability] informieren Sie, wenn Ihre Website im Internet nicht verfügbar ist oder langsam reagiert. [Weitere Informationen][availability].
* Die [**Proaktive Diagnose**](../../azure-monitor/app/proactive-diagnostics.md) wird automatisch konfiguriert, damit Benachrichtigungen über ungewöhnliche Leistungsmuster gesendet werden.

## <a name="set-a-metric-alert"></a>Festlegen einer Metrikwarnung
Öffnen Sie die Registerkarte „Warnungsregeln“, und verwenden Sie dann die Schaltfläche „Hinzufügen“.

![Klicken Sie auf der Registerkarte „Warnungsregeln“ auf „Warnung hinzufügen“. Legen Sie Ihre App als zu messende Ressource fest, geben Sie einen Namen für die Warnung ein, und wählen Sie eine Metrik.](./media/alerts/01-set-metric.png)

* Legen Sie die Ressource vor den anderen Eigenschaften fest. **Wählen Sie die Ressource "(Komponenten)" aus** , wenn Sie Benachrichtigungen für Leistungs- oder Nutzungsmetriken festlegen möchten.
* Der Name, den Sie der Warnung zuweisen, muss innerhalb der Ressourcengruppe (nicht nur in Ihrer Anwendung) eindeutig sein.
* Achten Sie auf die Einheiten, die beim Eingeben des Schwellenwerts gefordert sind.
* Wenn Sie das Feld „E-Mail an Besitzer...“ aktivieren, werden Warnungen per E-Mail an alle Benutzer gesendet, die Zugriff auf diese Ressourcengruppe haben. Wenn Sie diese Personengruppe erweitern möchten, fügen Sie die entsprechenden Benutzer der [Ressourcengruppe oder dem Abonnement](../../azure-monitor/app/resources-roles-access-control.md) (nicht der Ressource) hinzu.
* Wenn Sie „Weitere E-Mail-Adressen“ angeben, werden Warnungen an diese Einzelpersonen oder Gruppen gesendet (unabhängig davon, ob Sie das Kontrollkästchen „E-Mail an Besitzer...“ aktiviert haben). 
* Legen Sie eine [Webhookadresse](../../azure-monitor/platform/alerts-webhooks.md) fest, wenn Sie eine Web-App eingerichtet haben, die auf Warnungen reagiert. Der Aufruf erfolgt bei Aktivierung der Warnung und bei Auflösung der Warnung. (Beachten Sie aber, dass Abfrageparameter derzeit nicht als Webhook-Eigenschaften übergeben werden.)
* Sie können die Warnung deaktivieren oder aktivieren: Die zugehörigen Schaltflächen sehen Sie oben.

*Ich sehe keine Schaltfläche zum Hinzufügen von Benachrichtigungen.*

* Verwenden Sie ein Organisationskonto? Sie können Warnungen festlegen, wenn Sie für diese Anwendungsressource über Zugriffsberechtigungen für Besitzer oder Mitwirkende verfügen. Sehen Sie sich die Registerkarte „Access Control“ an. [Erfahren Sie mehr über Access Control][roles].

> [!NOTE]
> Auf dem Blatt „Warnungen“ sehen Sie, dass bereits eine Warnung eingerichtet ist: [Proaktive Diagnose](../../azure-monitor/app/proactive-failure-diagnostics.md). Dies ist eine automatische Warnung zur Überwachung einer bestimmten Metrik: der Anforderungsfehlerrate. Sofern Sie nicht entscheiden, diese proaktive Warnung zu deaktivieren, müssen Sie also keine eigene Warnung für die Anforderungsfehlerrate festlegen.
> 
> 

## <a name="see-your-alerts"></a>Anzeigen Ihrer Warnungen
Sie erhalten eine E-Mail, wenn sich ein Warnungsstatus von "Inaktiv" in "Aktiv" ändert und umgekehrt. 

Der aktuelle Status jeder Warnung wird auf der Registerkarte „Warnungsregeln“ angezeigt.

Die Dropdownliste "Warnungen" enthält einen Überblick über die letzten Aktivitäten:

![Dropdownliste „Warnungen“](./media/alerts/010-alert-drop.png)

Der Verlauf von Statusänderungen befindet sich im Aktivitätsprotokoll:

![Klicken Sie auf der Registerkarte „Übersicht“ auf „Einstellungen“ > „Überwachungsprotokolle“.](./media/alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Funktionsweise von Warnungen
* Eine Warnung kann drei Zustände annehmen: „Nie aktiviert“, „Aktiviert“ und „Aufgelöst“. „Aktiviert“ bedeutet, dass die angegebene Bedingung bei der letzten Auswertung erfüllt wurde.
* Wenn sich der Status einer Warnung ändert, wird eine Benachrichtigung generiert. (Wenn die Bedingung für die Warnung bereits erfüllt war, als Sie die Warnung erstellt haben, erhalten Sie möglicherweise erst dann eine Benachrichtigung, wenn die Bedingung nicht mehr erfüllt wird.)
* Jede Benachrichtigung generiert eine E-Mail-Nachricht, wenn Sie das E-Mail-Feld aktiviert oder E-Mail-Adressen angegeben haben. Sie können auch die Dropdownliste „Benachrichtigungen“ überprüfen.
* Eine Warnung wird jedes Mal ausgewertet, wenn eine Metrik empfangen wird, sonst aber aus keinem anderen Grund.
* Durch die Auswertung wird die Metrik über den vorangegangenen Zeitraum aggregiert und mit dem Schwellenwert verglichen, um den neuen Zustand zu ermitteln.
* Der von Ihnen ausgewählte Zeitraum gibt das Intervall an, über das Metriken aggregiert werden. Der Zeitraum hat keinen Einfluss darauf, wie häufig die Warnung ausgewertet wird: Dies hängt davon ab, wie häufig Metriken empfangen werden.
* Wenn eine Zeit lang kein Daten für eine bestimmte Metrik empfangen wurden, wirkt sich diese Lücke unterschiedlich auf die Auswertung von Warnungen und die Diagramme im Metrik-Explorer aus. Wenn länger als durch das Samplingintervall des Diagramms vorgegeben keine Daten empfangen werden, hat das Diagramm im Metrik-Explorer den Wert 0. Eine auf der gleichen Metrik basierende Warnung wird aber nicht erneut ausgewertet, und der Zustand der Warnung bleibt unverändert. 
  
    Sobald dann Daten empfangen werden, springt das Diagramm auf einen Wert ungleich 0 (null) zurück. Die Warnung wird auf Grundlage der Daten ausgewertet, die für den angegebenen Zeitraum zur Verfügung stehen. Wenn der neue Datenpunkt der einzige in diesem Zeitraum verfügbare Datenpunkt ist, basiert das Aggregat auf diesem Datenpunkt.
* Eine Warnung kann häufig zwischen den Zuständen "Warnung" und "Fehlerfrei" hin und her wechseln, auch wenn Sie einen langen Zeitraum festlegen. Dies kann vorkommen, wenn sich der metrische Wert um den Schwellenwert herum bewegt. Der Schwellenwert weist keine Hysterese auf: Der Übergang zur Warnung erfolgt beim selben Wert wie der Übergang zum fehlerfreien Zustand.

## <a name="what-are-good-alerts-to-set"></a>Welche Warnungen sollten festgelegt werden?
Das hängt von Ihrer Anwendung ab. Zunächst einmal sollten Sie nicht zu viele Metriken festlegen. Beobachten Sie Ihre Metrikdiagramme eine Zeitlang, während die App ausgeführt wird, um ein Gefühl für das Normalverhalten zu erhalten. Diese Übung unterstützt Sie bei der Verbesserung der Leistung. Richten Sie dann Warnungen ein, die Ihnen mitteilen, wenn die Metriken den Normalbereich verlassen. 

Zu den gängigen Warnungen zählen Folgende:

* [Browsermetriken][client], insbesondere **Browser-Seitenladezeiten**, eignen sich für Webanwendungen. Wenn Ihre Seite über viele Skripts verfügt, suchen Sie nach **Browserausnahmen**. Um diese Metriken und Warnungen zu erhalten, müssen Sie die [Webseitenüberwachung][client] einrichten.
* **Serverantwortzeit** für die Serverseite von Webanwendungen. Achten Sie neben der Einrichtung von Warnungen auf diese Metrik, um festzustellen, ob sie bei hohen Anforderungsraten unverhältnismäßig stark variiert: Dies kann darauf hindeuten, dass für Ihre App nicht genügend Systemressourcen vorhanden sind. 
* **Serverausnahmen** erfordern ein [zusätzliches Setup](../../azure-monitor/app/asp-net-exceptions.md), damit sie angezeigt werden.

Vergessen Sie nicht, dass bei der [proaktiven Fehlerquotendiagnose](../../azure-monitor/app/proactive-failure-diagnostics.md) automatisch die Rate überwacht wird, mit der Ihre App auf Anforderungen mit Fehlercodes reagiert.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Festlegen einer Ausnahmewarnung mithilfe von benutzerdefinierter Protokollsuche

In diesem Abschnitt erfahren Sie, wie eine abfragebasierte Ausnahmewarnung festgelegt wird. Nehmen wir für dieses Beispiel an, dass eine Warnung ausgegeben werden soll, wenn die Fehlerrate innerhalb der letzten 24 Stunden 10 % übersteigt.

1. Navigieren Sie im Azure-Portal zu Ihrer Application Insights-Ressource.
2. Klicken Sie links unter „Konfigurieren“ auf **Warnung**.

    ![Klicken Sie links unter „Konfigurieren“ auf „Warnung“.](./media/alerts/1appinsightalert.png)

3. Wählen Sie oben auf der Registerkarte „Warnung“ die Option **Neue Warnungsregel** aus.

     ![Wählen Sie oben auf der Registerkarte „Warnung“ die Option „Neue Warnungsregel“ aus.](./media/alerts/2createalert.png)

4. Ihre Ressource sollte automatisch ausgewählt werden. Klicken Sie auf **Bedingung hinzufügen**, um eine Bedingung festzulegen.

    ![Klicken Sie auf „Bedingung hinzufügen“.](./media/alerts/3addcondition.png)

5. Klicken Sie auf der Registerkarte „Signallogik konfigurieren“ auf **Benutzerdefinierte Protokollsuche**.

    ![Klicken Sie auf „Benutzerdefinierte Protokollsuche“.](./media/alerts/4customlogsearch.png)

6. Geben Sie Ihre Abfrage auf der Registerkarte „Benutzerdefinierte Protokollsuche“ im Feld „Suchabfrage“ ein. In diesem Beispiel verwenden wir die nachfolgende Kusto-Abfrage.
    ```kusto
    let percentthreshold = 10;
    let period = 24h;
    requests
    | where timestamp >ago(period)
    | summarize requestsCount = sum(itemCount)
    | project requestsCount, exceptionsCount = toscalar(exceptions | where timestamp >ago(period) | summarize sum(itemCount))
    | extend exceptionsRate = toreal(exceptionsCount)/toreal(requestsCount) * 100
    | where exceptionsRate > percentthreshold

    ```

    ![Eingeben einer Abfrage in das Suchabfragefeld](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Sie können diese Schritte auch für andere abfragebasierte Warnungstypen durchführen. Weitere Informationen zur Kusto-Abfragesprache finden Sie in der Dokumentation [Erste Schritte mit Kusto](https://docs.microsoft.com/azure/kusto/concepts/) oder im Spickzettel [SQL to Kusto](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet).

7. Wählen Sie unter „Warnungslogik“ aus, ob diese auf der Anzahl von Ergebnissen oder metrischer Maßeinheit basiert. Wählen Sie dann die Bedingung (größer als, gleich, kleiner als) und einen Schwellenwert aus. Während Sie diese Werte ändern, stellen Sie möglicherweise eine Änderung des Satzes für die Bedingungsvorschau fest. In diesem Beispiel verwenden wir „gleich“.

    ![Wählen Sie unter „Warnungslogik“ aus den vorhandenen Optionen für „Basierend auf“ und „Bedingung“ aus, und geben Sie dann einen Schwellenwert ein.](./media/alerts/6alertlogic.png)

8. Legen Sie unter „Auswertung basierend auf“ den Zeitraum und die Häufigkeit fest. Der Zeitraum muss hier mit dem Wert übereinstimmen, den wir in der Abfrage oben für den Zeitraum festgelegt haben. Klicken Sie anschließend auf **Fertig**.

    ![Legen Sie unten den Zeitraum und die Häufigkeit fest, und klicken Sie dann auf „Fertig“.](./media/alerts/7evaluate.png)

9. Wir sehen jetzt die Bedingung, die wir mit den voraussichtlichen monatlichen Kosten erstellt haben. Unter [Aktionsgruppen](../platform/action-groups.md) können Sie eine neue Gruppe erstellen oder eine vorhandene Gruppe auswählen. Wenn Sie möchten, können Sie die Aktionen anpassen.

    ![Klicken Sie unter „Aktionsgruppe“ auf die Schaltflächen „Auswählen“ oder „Erstellen“.](./media/alerts/8actiongroup.png)

10. Fügen Sie anschließend Ihre Warnungsdetails (Name der Warnungsregel, Beschreibung, Schweregrad) hinzu. Klicken Sie zuletzt unten auf **Warnungsregel erstellen**.

    ![Geben Sie unter „Warnungsdetails“ Ihren Namen für die Warnungsregel und eine Beschreibung ein, und legen Sie einen Schweregrad fest.](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Abbestellen von E-Mail-Benachrichtigungen zu klassischen Warnungen

Dieser Abschnitt bezieht sich auf **klassische Verfügbarkeitswarnungen**, **klassische Application Insights-Metrikwarnungen** und **klassische Warnungen zu Fehleranomalien**.

Sie erhalten E-Mail-Benachrichtigungen für diese klassischen Warnungen, wenn Folgendes zutrifft:

* Ihre E-Mail-Adresse ist im Feld „Empfänger von E-Mail-Benachrichtigungen“ in den Einstellungen für Warnungsregeln aufgeführt.

* Die Option zum Senden von E-Mail-Benachrichtigungen an Benutzer mit bestimmten Rollen für das Abonnement ist aktiviert, und Sie haben eine entsprechende Rolle für dieses bestimmte Azure-Abonnement.

![Screenshot für Warnungsbenachrichtigungen](./media/alerts/alert-notification.png)

Um Sicherheit und Datenschutz besser steuern zu können, wird generell empfohlen, dass Sie im Feld **Empfänger von E-Mail-Benachrichtigungen** die Benachrichtigungsempfänger für die klassischen Warnungen explizit angeben. Die Option zum Benachrichtigen aller Benutzer mit bestimmten Rollen ist für die Abwärtskompatibilität vorgesehen.

Wenn Sie die über eine bestimmte Warnungsregel generierten E-Mail-Benachrichtigungen abbestellen möchten, entfernen Sie Ihre E-Mail-Adresse im Feld **Empfänger von E-Mail-Benachrichtigungen**.

Wenn Ihre E-Mail-Adresse nicht explizit aufgeführt ist, wird empfohlen, dass Sie die Option zur automatischen Benachrichtigung aller Benutzer mit bestimmten Rollen deaktivieren und stattdessen im Feld „Empfänger von E-Mail-Benachrichtigungen“ die E-Mail-Adressen aller Benutzer aufführen, die Benachrichtigungen für die Warnungsregel erhalten sollen.

## <a name="who-receives-the-classic-alert-notifications"></a>Wer erhält die (klassischen) Warnungsbenachrichtigungen?

Dieser Abschnitt gilt nur für klassische Benachrichtigungen und hilft Ihnen, Ihre Warnungsbenachrichtigungen zu optimieren, um sicherzustellen, dass nur die gewünschten Empfänger Benachrichtigungen erhalten. Lesen Sie den [Übersichtsartikel zu Warnungen](../platform/alerts-overview.md), um mehr über den Unterschied zwischen [klassischen Warnungen](../platform/alerts-classic.overview.md) und den neuen Warnungen zu erfahren. Verwenden Sie [Aktionsgruppen](../platform/action-groups.md), um die Warnungsbenachrichtigung in der neuen Benutzeroberfläche für Warnungen zu steuern.

* Wir empfehlen die Verwendung bestimmter Empfänger für klassische Warnungsbenachrichtigungen.

* Für Warnungen zu allen Application Insights-Metriken (einschließlich Verfügbarkeitsmetriken) sendet die Option **Masse/Gruppe**, falls aktiviert, Benachrichtigungen an Benutzer mit der Rolle „Besitzer“, „Mitwirkender“ oder „Leser“ im Abonnement. Tatsächliche sind _alle_ Benutzer mit Zugriff auf das Abonnement der Application Insights-Ressource im Umfang enthalten und erhalten Benachrichtigungen.

> [!NOTE]
> Wenn Sie aktuell die Option **Massenversand/Gruppe** verwenden und diese deaktivieren, können Sie die Änderung nicht rückgängig machen.

Verwenden Sie die neue Benutzeroberfläche für Warnungen/Warnungen nahezu in Echtzeit, wenn Sie Benutzer basierend auf ihren Rollen benachrichtigen müssen. Mit [Aktionsgruppen](../platform/action-groups.md) können Sie E-Mail-Benachrichtigungen für Benutzer mit einer der „Mitwirkender/Besitzer/Leser“ konfigurieren (nicht zusammen als eine einzige Option).

## <a name="automation"></a>Automation
* [Verwenden von PowerShell zum Automatisieren der Einrichtung von Warnungen](../../azure-monitor/app/powershell-alerts.md)
* [Verwenden von Webhooks zum Automatisieren der Reaktion auf Warnungen](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Weitere Informationen
* [Verfügbarkeitswebtests](../../azure-monitor/app/monitor-web-app-availability.md)
* [Automatisieren der Einrichtung von Warnungen](../../azure-monitor/app/powershell-alerts.md)
* [Proaktive Diagnose](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

