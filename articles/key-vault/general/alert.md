---
title: Überwachung und Warnungen für Azure Key Vault | Microsoft-Dokumentation
description: Erstellen Sie ein Dashboard zum Überwachen der Integrität Ihres Schlüsseltresors, und konfigurieren Sie Warnungen.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: 9195bb59264731914740e1cca902707603e3502d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018119"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Überwachung und Warnungen für Azure Key Vault

## <a name="overview"></a>Übersicht

Sobald Sie damit begonnen haben, Ihre Produktionsgeheimnisse im Schlüsseltresor zu speichern, sollten Sie durch eine Überwachung der Integrität Ihres Schlüsseltresors sicherstellen, dass Ihr Dienst wie vorgesehen funktioniert. Wenn Sie Ihren Dienst skalieren, steigt die Anzahl von Anforderungen, die an Ihren Schlüsseltresor gesendet werden. Dies kann die Latenzzeit Ihrer Anforderungen erhöhen und in extremen Fällen zu einer Drosselung Ihrer Anforderungen führen – was sich auf die Leistung Ihres Diensts auswirkt. Sie müssen außerdem benachrichtigt werden, wenn Ihr Schlüsseltresor eine ungewöhnliche Anzahl von Fehlercodes sendet, damit Sie bei Problemen mit Zugriffsrichtlinien oder Firewallkonfiguration schnell handeln können. In diesem Dokument werden die folgenden Themen behandelt:

+ Grundlegende Key Vault-Metriken für die Überwachung
+ Konfigurieren von Metriken und Erstellen eines Dashboards 
+ Erstellen von Warnungen bei angegebenen Schwellenwerten 

## <a name="basic-key-vault-metrics-to-monitor"></a>Grundlegende Key Vault-Metriken für die Überwachung

+ Tresorverfügbarkeit  
+ Tresorauslastung 
+ Wartezeit für Dienst-API 
+ Dienst-API-Treffer gesamt (Filter nach Aktivitätstyp) 
+ Fehlercodes (Filter nach Statuscode) 

**Tresorverfügbarkeit**: Diese Metrik sollte immer bei 100 % liegen. Es ist wichtig, diese Metrik zu überwachen, da sie schnell Aufschluss darüber gibt, ob Ihr Schlüsseltresor ausgefallen ist. 

**Tresorauslastung**: Die Anzahl von Anforderungen pro Sekunde, die ein Schlüsseltresor verarbeiten kann, basiert auf der Art des ausgeführten Vorgangs. Für einige Tresorvorgänge gilt ein niedrigerer Schwellenwert für die Anforderungen pro Sekunde. Diese Metrik aggregiert die Gesamtnutzung Ihres Schlüsseltresors für alle Vorgangstypen, um einen Prozentwert bereitzustellen, der die aktuelle Auslastung des Schlüsseltresors angibt. Eine vollständige Liste der Limits für den Key Vault-Dienst finden Sie im folgenden Dokument. [Grenzwerte des Azure Key Vault-Diensts](service-limits.md)

**Wartezeit für Dienst-API**: Diese Metrik zeigt die durchschnittliche Latenzzeit eines Aufrufs des Schlüsseltresors. Auch wenn Ihr Schlüsselspeicher innerhalb der Dienstlimits arbeitet, kann eine hohe Auslastung des Schlüsselspeichers Wartezeiten verursachen, die zu Fehlern bei nachgelagerten Anwendungen führen. 

**API-Treffer gesamt**: Diese Metrik zeigt alle Aufrufe an Ihren Schlüsseltresor. Anhand dieses Werts können Sie ermitteln, welche Anwendungen Ihren Schlüsseltresor aufrufen. 

**Fehlercodes**: Diese Metrik zeigt, ob für Ihren Schlüsseltresor eine ungewöhnliche Anzahl von Fehlern auftritt. Eine vollständige Liste der Fehlercodes und einen Leitfaden zur Problembehandlung finden Sie im folgenden Dokument. [Azure Key Vault: REST-API-Fehlercodes](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Konfigurieren von Metriken und Erstellen eines Dashboards

1. Melden Sie sich beim Azure-Portal an.
2. Navigieren Sie zu Ihrem Schlüsseltresor.
3. Klicken Sie unter **Überwachung** auf **Metriken**. 

> [!div class="mx-imgBorder"]
> ![Screenshot: Abschnitt „Überwachung“ mit hervorgehobener Option „Metriken“](../media/alert-1.png)

4. Aktualisieren Sie den Titel des Diagramms in den Namen, den Sie für Ihr Dashboard anzeigen möchten. 
5. Wählen Sie den Bereich aus. In diesem Beispiel wählen wir einen einzelnen Schlüsseltresor aus. 
6. Wählen Sie die Metrik **Tresorverfügbarkeit gesamt** aus, und legen Sie als Aggregation **Durchschnitt** fest. 
7. Aktualisieren Sie den Zeitbereich auf „Letzte 24 Stunden“, und legen Sie die Granularität auf 1 Minute fest. 

> [!div class="mx-imgBorder"]
> ![Screenshot: Metrik „Tresorverfügbarkeit gesamt“](../media/alert-2.png)

8. Wiederholen Sie die Schritte oben für die Metriken „Tresorauslastung“ und „Wartezeit für Dienst-API“. Klicken Sie auf **An Dashboard anheften**, um Ihre Metriken in einem Dashboard zu speichern. 

> [!IMPORTANT]
> Auswählen von „An Dashboard anheften“ und Speichern der konfigurierten Metriken Wenn Sie die Seite verlassen und ohne Speicherung zur Seite zurückkehren, gehen Ihre Konfigurationsänderungen verloren. 

9. Um alle Vorgangstypen für den Schlüsseltresor zu überwachen, verwenden Sie die Metrik **Dienst-API-Treffer gesamt**, und wählen Sie **Teilung anwenden: nach Aktivitätstyp** aus.

> [!div class="mx-imgBorder"]
> ![Screenshot: Schaltfläche „Teilung anwenden“](../media/alert-3.png)

10. Um auf Fehlercodes für den Schlüsseltresor zu überwachen, verwenden Sie die Metrik **Dienst-API-Ergebnisse gesamt**, und wählen Sie **Teilung anwenden: nach Aktivitätstyp** aus.

> [!div class="mx-imgBorder"]
> ![Screenshot: Ausgewählte Metrik „Dienst-API-Ergebnisse gesamt“](../media/alert-4.png)

Jetzt sieht Ihr Dashboard wie dieses aus. Sie können auf die 3 Punkte oben rechts neben jeder Kachel klicken und die Kacheln nach Bedarf neu anordnen und ihre Größe ändern. 

Nachdem Sie das Dashboard gespeichert und veröffentlicht haben, wird eine neue Ressource in Ihrem Azure-Abonnement erstellt. Sie können diese jederzeit anzeigen, indem Sie nach „freigegebenes Dashboard“ suchen. 

> [!div class="mx-imgBorder"]
> ![Screenshot: Veröffentlichtes Dashboard](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Konfigurieren von Warnungen für Ihren Schlüsseltresor 

In diesem Abschnitt wird gezeigt, wie Sie Warnungen für Ihren Schlüsseltresor konfigurieren, damit Ihr Team sofort benachrichtigt wird, wenn sich Ihr Schlüsseltresor in einem fehlerhaften Zustand befindet. Sie können Warnungen konfigurieren, mit denen eine E-Mail gesendet (vorzugsweise an eine Verteilerliste für ein Team), eine Event Grid-Benachrichtigung ausgelöst, eine Telefonnummer angerufen oder eine SMS gesendet wird. Es ist auch möglich, statische Warnungen basierend auf einem festen Wert auszuwählen oder dynamische Warnungen zu konfigurieren, die Sie benachrichtigen, wenn für eine überwachte Metrik der Durchschnittsgrenzwert für Ihren Schlüsseltresor innerhalb eines definierten Zeitraums mit einer festgelegten Häufigkeit überschritten wird. 

> [!IMPORTANT]
> Beachten Sie, dass es bis zu 10 Minuten dauern kann, bis neu konfigurierte Warnungen damit beginnen, Benachrichtigungen zu versenden. 

### <a name="configure-an-action-group"></a>Konfigurieren einer Aktionsgruppe 

Eine Aktionsgruppe ist eine konfigurierbare Liste mit Benachrichtigungen und Eigenschaften.

1. Melden Sie sich beim Azure-Portal an.
2. Suchen Sie im Suchfeld nach **Warnungen**.
3. Wählen Sie **Aktionen verwalten** aus.

> [!div class="mx-imgBorder"]
> ![Screenshot: Hervorgehobene Schaltfläche „Aktionen verwalten“](../media/alert-6.png)

4. Klicken Sie auf **+ Aktionsgruppe hinzufügen**.

> [!div class="mx-imgBorder"]
> ![Screenshot: Hervorgehobene Schaltfläche „+ Aktionsgruppe hinzufügen“](../media/alert-7.png)

5. Wählen Sie den **Aktionstyp** für Ihre Aktionsgruppe aus. In diesem Beispiel erstellen wir eine neue E-Mail-Benachrichtigung.

> [!div class="mx-imgBorder"]
> ![Screenshot: Hervorgehobene Felder, die zum Hinzufügen einer Aktionsgruppe erforderlich sind](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Screenshot: Erforderliche Elemente zum Hinzufügen einer E-Mail- oder SMS-Nachrichtenwarnung](../media/alert-9.png)

6. Klicken Sie unten auf der Seite auf **OK**. Sie haben erfolgreich einen Aktionsgruppe erstellt. 

Nun, da Sie eine Aktionsgruppe konfiguriert haben, konfigurieren wir die Warnungsschwellenwerte für den Schlüsseltresor. 

### <a name="configure-alert-thresholds"></a>Konfigurieren von Warnungsschwellenwerten 

1. Wählen Sie Ihre Key Vault-Ressource im Azure-Portal aus, und klicken Sie unter **Überwachung** auf **Warnungen**.

> [!div class="mx-imgBorder"]
> ![Screenshot: Abschnitt „Überwachung“ mit der Menüoption „Warnungen“](../media/alert-10.png)

2. Wählen Sie **Neue Warnungsregel** aus.

> [!div class="mx-imgBorder"]
> ![Screenshot: Schaltfläche „+ Neue Warnungsregel“](../media/alert-11.png)

3. Legen Sie den Bereich für Ihre Warnungsregel fest. Sie können einen einzelnen Tresor oder mehrere Tresore auswählen. 

> [!IMPORTANT]
> Beachten Sie, dass sich bei Auswahl mehrerer Tresore als Bereich für Ihre Warnungen alle ausgewählten Tresore in derselben Region befinden müssen. Sie müssen für Tresore in unterschiedlichen Regionen separate Warnungsregeln konfigurieren. 

> [!div class="mx-imgBorder"]
> ![Screenshot: Auswählen eines Tresors](../media/alert-12.png)

4. Wählen Sie die Bedingungen für Ihre Warnungen aus. Sie können beliebige der folgenden Signale auswählen und Ihre Warnungslogik definieren. Das Key Vault-Team empfiehlt eine Konfiguration der folgenden Warnungsschwellenwerte. 

    + Key Vault-Verfügbarkeit fällt unter 100 % (statischer Schwellenwert)
    + Key Vault-Wartezeit übersteigt 500 ms (statischer Schwellenwert) 
    + „Tresorauslastung insgesamt“ übersteigt 75 % (statischer Schwellenwert) 
    + „Tresorauslastung insgesamt“ überschreitet den Durchschnitt (dynamischer Schwellenwert)
    + „Fehlercodes gesamt“ liegt über dem Durchschnitt (dynamischer Schwellenwert) 

> [!div class="mx-imgBorder"]
> ![Screenshot: Auswählen von Bedingungen für Warnungen](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Beispiel 1: Konfigurieren eines statischen Warnungsschwellenwerts für die Latenzzeit

Wählen Sie als Signalname **Wartezeit für Dienst-API gesamt** aus.
> [!div class="mx-imgBorder"]
> ![Screenshot: Signalname „Wartezeit für Dienst-API gesamt“](../media/alert-14.png)

Beachten Sie die folgenden Konfigurationsparameter.

+ Legen Sie den Schwellenwert auf **Statisch** fest. 
+ Legen Sie den Operator auf **Größer als** fest.
+ Legen Sie den Aggregationstyp auf **Durchschnitt** fest.
+ Legen Sie den Wert des Schwellenwerts auf **500** fest.
+ Legen Sie den Aggregationszeitraum auf **5 Minuten** fest.
+ Legen Sie die Auswertungshäufigkeit auf **1 Minute** fest.
+ Wählen Sie **Fertig** aus.  

> [!div class="mx-imgBorder"]
> ![Screenshot: Hervorgehobene konfigurierte Warnungslogik](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Beispiel 2: Konfigurieren eines dynamischen Warnungsschwellenwerts für die Tresorauslastung 

Wenn Sie eine dynamische Warnung verwenden, können Sie historische Daten zum ausgewählten Schlüsseltresor anzeigen. Der blaue Bereich stellt die durchschnittliche Auslastung Ihres Schlüsseltresors dar. Der rote Bereich zeigt Spitzen, die zum Auslösen einer Warnung führen würden, wenn weitere Kriterien in der Warnungskonfiguration erfüllt sind. Die roten Punkte zeigen Verstöße, bei denen die Kriterien für die Warnung innerhalb des aggregierten Zeitfensters erfüllt waren. Sie können eine Warnung so konfigurieren, dass sie nach einer bestimmten Anzahl von Verstößen innerhalb eines festgelegten Zeitraums ausgelöst wird. Wenn Sie vorherige Daten nicht einbeziehen möchten, können Sie alte Daten über eine Option in den erweiterten Einstellungen ausschließen. 

> [!div class="mx-imgBorder"]
> ![Screenshot: Graph der Gesamttresorauslastung](../media/alert-16.png)

Beachten Sie die folgenden Konfigurationsparameter.

+ Legen Sie den Schwellenwert auf **Dynamisch** fest. 
+ Legen Sie den Operator auf **Größer als** fest.
+ Legen Sie den Aggregationstyp auf **Durchschnitt** fest.
+ Legen Sie die Schwellenwertempfindlichkeit auf **Mittel** fest.
+ Legen Sie den Aggregationszeitraum auf **5 Minuten** fest.
+ Legen Sie die Auswertungshäufigkeit auf **1 Minute** fest.
+ **Optional**: Konfigurieren Sie erweiterte Einstellungen. 
+ Wählen Sie **Fertig** aus.

> [!div class="mx-imgBorder"]
> ![Screenshot des Azure-Portals](../media/alert-17.png)

5. Hinzufügen der erstellten Aktionsgruppe

> [!div class="mx-imgBorder"]
> ![Screenshot: Hinzufügen einer Aktionsgruppe](../media/alert-18.png)

6. Aktivieren der Warnung und Zuweisen eines Schweregrads

> [!div class="mx-imgBorder"]
> ![Screenshot: Aktivieren der Warnung und Zuweisen eines Schweregrads](../media/alert-19.png)

7. Erstellen der Warnung 


## <a name="next-steps"></a>Nächste Schritte

Glückwunsch! Sie haben erfolgreich ein Überwachungsdashboard erstellt und Warnungen für Ihren Schlüsseltresor konfiguriert! Nachdem Sie alle oben aufgeführten Schritte ausgeführt haben, sollten Sie jetzt E-Mail-Benachrichtigungen erhalten, wenn Ihr Schlüsseltresor die konfigurierten Warnungskriterien erfüllt. Ein entsprechendes Beispiel ist nachfolgend dargestellt. Verwenden Sie die in diesem Artikel eingerichteten Tools, um die Integrität Ihres Schlüsseltresors zu überwachen. 

### <a name="example-email-alert"></a>Beispiel-E-Mail-Warnung 

> [!div class="mx-imgBorder"]
> ![Screenshot: Hervorgehobene Informationen, die zum Konfigurieren einer E-Mail-Warnung erforderlich sind](../media/alert-20.png)
