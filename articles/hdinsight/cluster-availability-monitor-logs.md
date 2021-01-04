---
title: Überwachen der Clusterverfügbarkeit mit Azure Monitor-Protokollen in HDInsight
description: Erfahren Sie, wie Sie mit Azure Monitor-Protokollen die Integrität und Verfügbarkeit des Clusters überwachen können.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 08/12/2020
ms.openlocfilehash: f86b2166ea9bd2a547a29a777d6b709877036161
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542538"
---
# <a name="how-to-monitor-cluster-availability-with-azure-monitor-logs-in-hdinsight"></a>Überwachen der Clusterverfügbarkeit mit Azure Monitor-Protokollen in HDInsight

HDInsight-Cluster bieten eine Integration mit Azure Monitor-Protokollen, die abfragbare Metriken und Protokolle sowie konfigurierbare Warnungen bereitstellt. In diesem Artikel wird gezeigt, wie Sie Azure Monitor zum Überwachen Ihres Clusters verwenden.

## <a name="azure-monitor-logs-integration"></a>Integration von Azure Monitor-Protokollen

Mit Azure Monitor-Protokollen können Daten, die von mehreren Ressourcen wie z. B. HDInsight-Clustern generiert wurden, zentral gesammelt und zusammengefasst werden, um eine einheitliche Überwachungsumgebung zu schaffen.

Als Voraussetzung benötigen Sie einen Log Analytics-Arbeitsbereich, um die gesammelten Daten zu speichern. Wenn Sie noch keinen eingerichtet haben, befolgen Sie die nachstehenden Anweisungen: [Erstellen eines Log Analytics-Arbeitsbereichs](../azure-monitor/learn/quick-create-workspace.md).

## <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Aktivieren der Integration von Azure Monitor-Protokollen in HDInsight

Wählen Sie im Portal auf der Seite mit den HDInsight-Clusterressourcen **Azure Monitor** aus. Wählen Sie dann **Aktivieren** und Ihren Log Analytics-Arbeitsbereich aus der Dropdownliste aus.

![HDInsight Operations Management Suite](media/cluster-availability-monitor-logs/azure-portal-monitoring.png)

Standardmäßig wird damit der OMS-Agent auf allen Clusterknoten (mit Ausnahme der Edgeknoten) installiert. Da auf den Edgeknoten des Clusters kein OMS-Agent installiert ist, sind standardmäßig keine Telemetriedaten für Edgeknoten in Log Analytics vorhanden.

## <a name="query-metrics-and-logs-tables"></a>Abfragemetriken und Protokolltabellen

Sobald die Integration von Azure Monitor-Protokollen aktiviert ist (was einige Minuten dauern kann), navigieren Sie zu Ihrer Ressource **Log Analytics-Arbeitsbereich** und wählen **Protokolle** aus.

![Log Analytics-Arbeitsbereich – Protokolle](media/cluster-availability-monitor-logs/hdinsight-portal-logs.png)

In den Protokollen sind eine Reihe von Beispielabfragen aufgeführt, wie z. B.:

| Abfragename                      | BESCHREIBUNG                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Computer availability today (Heute verfügbare Computer)    | Diagramm der Anzahl von Computern, die Protokolle senden, stündlich                     |
| List heartbeats (Heartbeat auflisten)                 | Listet alle Heartbeats von Computern der letzten Stunde auf                           |
| Last heartbeat of each computer (Letzter Heartbeat jedes Computers) | Zeigt den letzten von jedem Computer gesendeten Heartbeat                             |
| Unavailable computers (Nicht verfügbare Computer)           | Listet alle bekannten Computer auf, die in den letzten 5 Stunden keinen Heartbeat gesendet haben |
| Availability rate (Verfügbarkeitsrate)               | Berechnet die Verfügbarkeitsrate jedes angebundenen Computers                |

Sie können die Beispielabfrage **Availability rate** (Verfügbarkeitsrate) ausführen, indem Sie für die Abfrage **Run** (Ausführen) auswählen, wie im Screenshot oben dargestellt. Dadurch wird die Verfügbarkeitsrate jedes Knotens in Ihrem Cluster als Prozentsatz angezeigt. Wenn Sie mehrere HDInsight-Cluster für das Senden von Metriken an denselben Log Analytics-Arbeitsbereich aktiviert haben, wird die Verfügbarkeitsrate für alle Knoten (mit Ausnahme der Edgeknoten) in diesen Clustern angezeigt.

![Protokolle im Log Analytics-Arbeitsbereich mit Beispielabfrage zur Verfügbarkeitsrate](media/cluster-availability-monitor-logs/portal-availability-rate.png)

> [!NOTE]  
> Die Verfügbarkeitsrate wird über einen Zeitraum von 24 Stunden gemessen, sodass Ihr Cluster mindestens 24 Stunden ausgeführt werden muss, bevor Sie genaue Verfügbarkeitsraten sehen.

Sie können diese Tabelle an ein freigegebenes Dashboard anheften, indem Sie rechts oben auf **Anheften** klicken. Wenn Sie über kein beschreibbares freigegebenes Dashboard verfügen, erfahren Sie hier, wie Sie ein Dashboard erstellen: [Erstellen und Freigeben von Dashboards im Azure-Portal](../azure-portal/azure-portal-dashboards.md#publish-and-share-a-dashboard).

## <a name="azure-monitor-alerts"></a>Azure Monitor-Warnungen

Sie können auch Azure Monitor-Warnungen einrichten, die ausgelöst werden, wenn der Wert einer Metrik oder die Ergebnisse einer Abfrage bestimmte Bedingungen erfüllen. Als Beispiel erstellen wir eine Warnung zum Senden einer E-Mail, wenn mindestens von einem Knoten innerhalb von 5 Stunden kein Heartbeat gesendet wurde (d. h. es wird angenommen, dass er nicht verfügbar ist).

Führen Sie unter **Logs** (Protokolle) die Beispielabfrage **Unavailable computers** (Nicht verfügbare Computer) aus, indem Sie für diese Abfrage **Run** (Ausführen) auswählen, wie unten dargestellt.

![Protokolle im Log Analytics-Arbeitsbereich mit Beispiel zu nicht verfügbaren Computern](media/cluster-availability-monitor-logs/portal-unavailable-computers.png)

Wenn alle Knoten verfügbar sind, sollte diese Abfrage vorerst 0 (null) Ergebnisse zurückgeben. Klicken Sie auf **Neue Warnungsregel** , um mit der Konfiguration Ihrer Warnung für diese Abfrage zu beginnen.

![Log Analytics-Arbeitsbereich mit neuer Warnungsregel](media/cluster-availability-monitor-logs/portal-logs-new-alert-rule.png)

Eine Warnung besteht aus drei Komponenten: der *Ressource* , für die die Regel erstellt werden soll (in diesem Fall der Log Analytics-Arbeitsbereich), der *Bedingung* zum Auslösen der Warnung und den *Aktionsgruppen* , die bestimmen, was passieren soll, wenn die Warnung ausgelöst wird.
Klicken Sie auf den **Titel der Bedingung** , wie unten gezeigt, um die Konfiguration der Signallogik abzuschließen.

![Portalwarnung – Erstellen einer Regelbedingung](media/cluster-availability-monitor-logs/portal-condition-title.png)

Dadurch wird **Signallogik konfigurieren** geöffnet.

Legen Sie den Abschnitt **Warnungslogik** wie folgt fest:

*Basierend auf: Anzahl von Ergebnissen, Bedingung: Größer als, Schwellenwert: 0.*

Da diese Abfrage nur nicht verfügbare Knoten als Ergebnis zurückgibt, sollte die Warnung immer ausgelöst werden, wenn die Anzahl der Ergebnisse größer als 0 ist.

Legen Sie im Abschnitt **Auswertung basierend auf** den **Zeitraum** und die **Häufigkeit** basierend darauf fest, wie oft Sie die Prüfung auf nicht verfügbare Knoten erfolgen soll.

Bei dieser Warnung möchten Sie sicherstellen, dass sich **Zeitraum und Häufigkeit** entsprechen. Weitere Informationen zu Zeitraum, Häufigkeit und anderen Warnungsparametern finden Sie [hier](../azure-monitor/platform/alerts-unified-log.md#alert-logic-definition).

Wählen Sie **Fertig** aus, wenn die Konfiguration der Signallogik abgeschlossen ist.

![Warnungsregel zur Konfiguration von Signallogik](media/cluster-availability-monitor-logs/portal-configure-signal-logic.png)

Wenn Sie noch nicht über eine Aktionsgruppe verfügen, klicken Sie im Abschnitt **Aktionsgruppen** auf **Neu erstellen** .

![Warnungsregel zur Erstellung einer neuen Aktionsgruppe](media/cluster-availability-monitor-logs/portal-create-new-action-group.png)

Dadurch wird **Aktionsgruppe hinzufügen** geöffnet. Wählen Sie einen **Aktionsgruppennamen** , **Kurznamen** , ein **Abonnement** und eine **Ressourcengruppe** . Wählen Sie im Abschnitt **Aktionen** einen **Aktionsnamen** und **E-Mail/SMS/Push/Sprachanruf** als **Aktionstyp** aus.

> [!NOTE]
> Es gibt mehrere andere Aktionen, die neben „E-Mail/SMS/Push/Sprachanruf“ eine Warnung auslösen können, wie beispielsweise eine Azure-Funktion, LogicApp, Webhook, ITSM und Automation Runbook. [Weitere Informationen](../azure-monitor/platform/action-groups.md#action-specific-information).

Dadurch wird **E-Mail/SMS/Push/Sprachanruf** geöffnet. Wählen Sie einen **Namen** für den Empfänger, **aktivieren** Sie das Feld **E-Mail** , und geben Sie eine E-Mail-Adresse ein, an die die Warnung gesendet werden soll. Wählen Sie unter **E-Mail/SMS/Push/Sprachanruf** und unter **Aktionsgruppe hinzufügen** die Option **OK** aus, um die Konfiguration der Aktionsgruppe abzuschließen.

![Erstellen einer Warnungsregel – Hinzufügen einer Aktionsgruppe](media/cluster-availability-monitor-logs/portal-add-action-group.png)

Nachdem diese Blätter geschlossen wurden, sollte Ihre Aktionsgruppe unter dem Abschnitt **Aktionsgruppen** aufgeführt sein. Geben Sie abschließend im Abschnitt **Warnungsdetails** den **Namen der Warnungsregel** und eine **Beschreibung** ein, und wählen Sie einen **Schweregrad** . Klicken Sie im letzten Schritt auf **Warnungsregel erstellen** .

![Portal – Fertigstellen der Warnungsregel](media/cluster-availability-monitor-logs/portal-create-alert-rule-finish.png)

> [!TIP]
> Die Möglichkeit, einen **Schweregrad** anzugeben, ist ein leistungsstarkes Instrument, das bei der Erstellung mehrerer Benachrichtigungen verwendet werden kann. Sie können beispielsweise eine Warnung mit Schweregrad 1 auszulösen, wenn ein einzelner Hauptknoten ausfällt, und eine weitere kritische Warnung mit Schweregrad 0 für den unwahrscheinlichen Fall erstellen, dass beide Hauptknoten ausfallen.

Wenn die Bedingung für diese Warnung erfüllt ist, wird die Warnung ausgelöst, und Sie erhalten eine E-Mail mit den entsprechenden Details:

![Beispiel für eine Azure Monitor-Warnungs-E-Mail](media/cluster-availability-monitor-logs/portal-oms-alert-email.png)

Sie können auch alle ausgelösten Warnungen gruppiert nach Schweregrad anzeigen, indem Sie im **Log Analytics-Arbeitsbereich** zu **Warnungen** wechseln.

![Warnungen im Log Analytics-Arbeitsbereich](media/cluster-availability-monitor-logs/hdi-portal-oms-alerts.png)

Wenn Sie eine Gruppierung nach Schweregrad auswählen (z. B. **Sev 1** , wie oben hervorgehoben), werden Einträge für alle Warnungen dieses Schweregrads angezeigt, die ausgelöst wurden (siehe unten):

![Log Analytics-Arbeitsbereich mit Warnungen des Schweregrads 1](media/cluster-availability-monitor-logs/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Nächste Schritte

* [Clusterverfügbarkeit: Apache Ambari](./hdinsight-cluster-availability.md)
* [Verwenden von Azure Monitor-Protokollen](hdinsight-hadoop-oms-log-analytics-tutorial.md)
