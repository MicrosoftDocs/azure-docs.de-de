---
title: Überwachen der Clusterverfügbarkeit mit Ambari und Azure Monitor-Protokollen
description: Erfahren Sie, wie Sie mit Ambari und Azure Monitor-Protokollen die Integrität und Verfügbarkeit des Clusters überwachen können.
keywords: Überwachung, Ambari, Überwachen, Protokollanalyse, Warnung, Verfügbarkeit, Integrität
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: tyfox
ms.openlocfilehash: 459de569916af14b0efea0ff08b92e5c93ed2369
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64718923"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>Überwachen der Clusterverfügbarkeit mit Ambari und Azure Monitor-Protokollen

HDInsight-Cluster zeichnen sich durch sowohl Apache Ambari, das auf einen Blick Integritätsinformationen und vordefinierte Warnungen liefert, als auch die Integration von Azure Monitor-Protokollen aus, die abfragbare Metriken und Protokolle sowie konfigurierbare Warnungen zur Verfügung stellt.

Dieses Dokument zeigt, wie Sie diese Tools verwenden können, um Ihren Cluster zu überwachen. Es enthält einige Beispiele für die Konfiguration einer Ambari-Warnung, die Überwachung der Knotenverfügbarkeitsrate und die Erstellung einer Azure Monitor-Warnung, die ausgelöst wird, wenn von einem oder mehreren Knoten in fünf Stunden kein Heartbeat empfangen wurde.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Dashboard

Auf das Ambari-Dashboard kann zugegriffen werden, indem Sie im Azure-Portal auf dem Blatt „Übersicht“ im Abschnitt **Clusterdashboards** auf den Link zur **Ambari-Homepage**  klicken. Alternativ ist der Zugriff möglich, indem in einem Browser die folgende URL eingegeben wird: [https://\<Clustername\>.azurehdinsight.net](https://clustername.azurehdinsight.net/)

![Ansicht des HDInsight-Ressourcenportals](media/hdinsight-cluster-availability/portal-overview.png)

Sie werden aufgefordert, einen Anmeldebenutzernamen und ein Kennwort für den Cluster einzugeben. Geben Sie die Anmeldeinformationen ein, die Sie beim Erstellen des Clusters festgelegt haben.

Sie werden dann zum Ambari-Dashboard weitergeleitet, das Widgets mit einer Reihe von Metriken enthält, die Ihnen einen schnellen Überblick über die Integrität Ihres HDInsight-Clusters geben. Diese Widgets zeigen Metriken wie z.B. die Anzahl der aktiven DataNodes (Workerknoten) und JournalNodes (Zookeeper-Knoten), Betriebszeit von NameNodes (Hauptknoten) sowie Metriken, die spezifisch für bestimmte Clustertypen sind, wie die Betriebszeit von YARN ResourceManager für Spark- und Hadoop-Cluster.

![Ambari-Dashboard](media/hdinsight-cluster-availability/ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Hosts – Anzeigen des Status einzelner Knoten

Sie können auch Statusinformationen für einzelne Knoten anzeigen. Klicken Sie auf die Registerkarte **Hosts**, um eine Liste aller Knoten in Ihrem Cluster und grundlegende Informationen zu jedem Knoten anzuzeigen. Das grüne Häkchen links neben einem Knotennamen gibt an, dass alle Komponenten auf dem Knoten betriebsbereit sind. Wenn eine Komponente auf einem Knoten ausgefallen ist, wird ein rotes Warndreieck anstelle des grünen Häkchens angezeigt.

![Anzeigen der Ambari-Hosts](media/hdinsight-cluster-availability/ambari-hosts.png)

Sie können dann auf den **Namen** eines Knotens klicken, um detailliertere Hostmetriken für diesen bestimmten Knoten anzuzeigen. Diese Ansicht zeigt den Status bzw. die Verfügbarkeit jeder einzelnen Komponente.

![Ambari-Hosts: Ansicht für einzelnen Knoten](media/hdinsight-cluster-availability/ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari-Warnungen

Ambari bietet außerdem mehrere konfigurierbare Warnungen, die Benutzer bei bestimmten Ereignissen benachrichtigen. Wenn Warnungen ausgelöst werden, werden sie in der linken oberen Ecke von Ambari in einem roten Badge mit der Anzahl der Warnungen angezeigt. Bei Klicken auf diesen Badge wird eine Liste der aktuellen Warnungen angezeigt.

![Anzahl der Ambari-Warnungen](media/hdinsight-cluster-availability/ambari-alerts.png)

Um eine Liste der Warnungsdefinitionen und deren Status anzuzeigen, klicken Sie auf die Registerkarte **Alerts (Warnungen)** (siehe unten).

![Ambari-Warnungen: Ansicht der Definitionen](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari bietet viele vordefinierte Warnungen in Bezug auf Verfügbarkeit, wie z.B.:

| Name der Warnung                        | BESCHREIBUNG                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DataNode Health Summary (DataNode-Integritätszusammenfassung)           | Diese Warnung auf Dienstebene wird ausgelöst, wenn es fehlerhafte DataNodes gibt.                                                                                                                |
| NameNode High Availability Health (NameNode-Hochverfügbarkeitsintegrität) | Diese Warnung auf Dienstebene wird ausgelöst, wenn entweder der aktive NameNode oder der Standby-NameNode nicht ausgeführt wird.                                                                              |
| Percent JournalNodes Available (Prozentsatz verfügbarer JournalNodes)    | Diese Warnung wird ausgelöst, wenn die Anzahl der ausgefallenen JournalNodes im Cluster höher ist als der konfigurierte kritische Schwellenwert. Sie aggregiert die Ergebnisse der JournalNode-Prozessüberprüfungen. |
| Percent DataNodes Available (Prozentsatz verfügbarer DataNodes)       | Diese Warnung wird ausgelöst, wenn die Anzahl der ausgefallenen DataNodes im Cluster höher ist als der konfigurierte kritische Schwellenwert. Sie aggregiert die Ergebnisse der DataNode-Prozessüberprüfungen.       |

Eine vollständige Liste der Ambari-Warnungen, die helfen, die Verfügbarkeit eines Clusters zu überwachen, finden Sie [hier](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui).

Um Details zu einer Warnung anzuzeigen oder Kriterien zu ändern, klicken Sie auf den **Namen** der Warnung. Nehmen Sie **DataNode Health Summary** (DataNode-Integritätszusammenfassung) als Beispiel. Sie sehen eine Beschreibung der Warnung sowie die spezifischen Kriterien, die eine „Warnung“ oder „kritische“ Warnung auslösen, und das Überprüfungsintervall für die Kriterien. Um die Konfiguration zu bearbeiten, klicken Sie rechts oben im Konfigurationsfeld auf die Schaltfläche **Edit** (Bearbeiten).

![Konfiguration von Ambari-Warnungen](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Hier können Sie die Beschreibung und vor allem das Überprüfungsintervall und die Schwellenwerte für Warnungen bzw. kritische Warnungen bearbeiten.

![Bearbeitungsansicht der Konfiguration von Ambari-Warnungen](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

Bei diesem Beispiel könnten Sie veranlassen, dass zwei fehlerhafte DataNodes eine kritische Warnung auslösen und ein fehlerhafter DataNode nur eine Warnung auslöst. Wenn Sie die Bearbeitung fertig sind, klicken Sie auf **Save** (Speichern).

### <a name="email-notifications"></a>E-Mail-Benachrichtigungen

Sie können optional auch E-Mail-Benachrichtigungen für Ambari-Warnungen konfigurieren. Klicken Sie dazu auf der Registerkarte **Alerts** (Warnungen) links oben auf die Schaltfläche **Actions** (Aktionen) und dann auf **Manage Notifications** (Benachrichtigungen verwalten).

![Ambari: Verwalten der Benachrichtigungsaktion](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Ein Dialogfeld für die Verwaltung von Benachrichtigungen zu Warnungen wird geöffnet. Klicken Sie unten im Dialogfeld auf **+** , und füllen Sie die Pflichtfelder aus, um Ambari die Details des E-Mail-Servers anzugeben, von dem E-Mails gesendet werden sollen.

> [!TIP]
> Die Einrichtung von Ambari-E-Mail-Benachrichtigungen kann eine gute Möglichkeit sein, bei Verwaltung vieler HDInsight-Cluster Benachrichtigungen zentral zu erhalten.

## <a name="azure-monitor-logs-integration"></a>Integration von Azure Monitor-Protokollen

Azure Monitor-Protokolle ermöglichen, dass Daten, die von mehreren Ressourcen, wie z.B. HDInsight-Clustern, generiert werden, gesammelt und zentral zusammengefasst werden können, um eine einheitliche Überwachungsumgebung zu schaffen.

Als Voraussetzung benötigen Sie einen Log Analytics-Arbeitsbereich, um die gesammelten Daten zu speichern. Wenn Sie noch keinen erstellt haben, befolgen Sie die Anweisungen hier: [Erstellen eines Log Analytics-Arbeitsbereichs](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Aktivieren der Integration von Azure Monitor-Protokollen in HDInsight

Klicken Sie im Portal auf der Seite mit den Ressourcen des HDInsight-Clusters auf das Blatt **Operations Management Suite**. Klicken Sie dann auf **Aktivieren**, und wählen Sie Ihren Log Analytics-Arbeitsbereich in der Dropdownliste aus.

![HDInsight-Blatt „Operations Management Suite“](media/hdinsight-cluster-availability/portal-enable-oms.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>Abfragemetriken und Protokolltabellen auf dem Blatt „Protokolle“

Sobald die Azure Monitor-Protokollintegration aktiviert ist (was einige Minuten dauern kann), navigieren Sie zu Ihrer Ressource **Log Analytics-Arbeitsbereich** und klicken auf der Blatt **Protokolle**.

![Blatt „Protokolle“ im Log Analytics-Arbeitsbereich](media/hdinsight-cluster-availability/portal-logs.png)

Auf dem Blatt **Protokolle** ist eine Reihe von Beispielabfragen aufgeführt, wie z.B.:

| Abfragename                      | BESCHREIBUNG                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Computer availability today (Heute verfügbare Computer)    | Diagramm der Anzahl von Computern, die Protokolle senden, stündlich                     |
| List heartbeats (Heartbeat auflisten)                 | Listet alle Heartbeats von Computern der letzten Stunde auf                           |
| Last heartbeat of each computer (Letzter Heartbeat jedes Computers) | Zeigt den letzten von jedem Computer gesendeten Heartbeat                             |
| Unavailable computers (Nicht verfügbare Computer)           | Listet alle bekannten Computer auf, die in den letzten 5 Stunden keinen Heartbeat gesendet haben |
| Availability rate (Verfügbarkeitsrate)               | Berechnet die Verfügbarkeitsrate jedes angebundenen Computers                |

Führen Sie beispielsweise die Beispielabfrage **Availability rate** aus, indem Sie auf **Run** (Ausführen) für diese Abfrage klicken, wie im Screenshot oben gezeigt. Dadurch wird die Verfügbarkeitsrate jedes Knotens in Ihrem Cluster als Prozentsatz angezeigt. Wenn Sie mehrere HDInsight-Cluster für das Senden von Metriken an den gleichen Log Analytics-Arbeitsbereich aktiviert haben, wird die Verfügbarkeitsrate für alle Knoten in diesen Clustern angezeigt.

![Blatt „Protokolle“ im Log Analytics-Arbeitsbereich mit Beispielabfrage zur Verfügbarkeitsrate](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> Die Verfügbarkeitsrate wird über einen Zeitraum von 24 Stunden gemessen, sodass Ihr Cluster mindestens 24 Stunden ausgeführt werden muss, bevor Sie genaue Verfügbarkeitsraten sehen.

Sie können diese Tabelle an ein freigegebenes Dashboard anheften, indem Sie rechts oben auf **Anheften** klicken. Wenn Sie über kein beschreibbares freigegebenes Dashboard verfügen, finden Sie Informationen zu dessen Erstellung hier: [Erstellen und Freigeben von Dashboards im Azure-Portal](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-a-dashboard-and-manage-access-control).

### <a name="azure-monitor-alerts"></a>Azure Monitor-Warnungen

Sie können auch Azure Monitor-Warnungen einrichten, die ausgelöst werden, wenn der Wert einer Metrik oder die Ergebnisse einer Abfrage bestimmte Bedingungen erfüllen. Als Beispiel erstellen wir eine Warnung zum Senden einer E-Mail, wenn ein oder mehrere Knoten innerhalb von 5 Stunden keinen Heartbeat gesendet haben (d.h. es wird angenommen, dass sie nicht verfügbar sind).

Führen Sie auf dem Blatt **Logs** (Protokolle) die Beispielabfrage **Unavailable computers** (Nicht verfügbare Computer) aus, indem Sie auf **Run** (Ausführen) für diese Abfrage klicken, wie im Screenshot oben gezeigt.

![Blatt „Protokolle“ im Log Analytics-Arbeitsbereich mit Beispielabfrage zu nicht verfügbaren Computern](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Wenn alle Knoten verfügbar sind, sollte diese Abfrage vorerst 0 Ergebnisse zurückgeben. Klicken Sie auf **Neue Warnungsregel**, um mit der Konfiguration Ihrer Warnung für diese Abfrage zu beginnen.

![Log Analytics-Arbeitsbereich mit neuer Warnungsregel](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Eine Warnung besteht aus drei Komponenten: der *Ressource*, für die die Regel erstellt werden soll (in diesem Fall der Log Analytics-Arbeitsbereich), der *Bedingung* zum Auslösen der Warnung und den *Aktionsgruppen*, die bestimmen, was passieren soll, wenn die Warnung ausgelöst wird.

Klicken Sie auf den **Titel der Bedingung**, wie unten gezeigt, um die Konfiguration der Signallogik abzuschließen.

![Warnungsregelbedingung](media/hdinsight-cluster-availability/portal-condition-title.png)

Das Blatt **Signallogik konfigurieren** wird geöffnet.

Legen Sie den Abschnitt **Warnungslogik** wie folgt fest:

*Basierend auf: Anzahl von Ergebnissen, Bedingung: Größer als, Schwellenwert: 0.*

Da diese Abfrage nur nicht verfügbare Knoten als Ergebnis zurückgibt, sollte die Warnung immer ausgelöst werden, wenn die Anzahl der Ergebnisse größer als 0 ist.

Legen Sie im Abschnitt **Auswertung basierend auf** den **Zeitraum** und die **Häufigkeit** basierend darauf fest, wie oft Sie die Prüfung auf nicht verfügbare Knoten erfolgen soll.

Beachten Sie, dass Sie für die Zwecke dieser Warnung sicherstellen möchten, dass **Zeitraum=Häufigkeit**. Weitere Informationen zu Zeitraum, Häufigkeit und anderen Warnungsparametern finden Sie [hier](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Klicken Sie auf **Fertig**, wenn Sie mit dem Konfigurieren der Signallogik fertig sind.

![Konfigurieren der Signallogik für Warnungsregel](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Wenn Sie noch keine Aktionsgruppe haben, klicken Sie im Abschnitt **Aktionsgruppen** auf **Neu erstellen**.

![Neue Aktionsgruppe für Warnungsregel](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Dadurch wird das Blatt **Aktionsgruppe hinzufügen** geöffnet. Wählen Sie einen **Aktionsgruppennamen**, **Kurznamen**, ein **Abonnement** und eine **Ressourcengruppe**. Wählen Sie im Abschnitt **Aktionen** einen **Aktionsnamen** und **E-Mail/SMS/Push/Sprachanruf** als **Aktionstyp** aus.

> [!NOTE]
> Es gibt mehrere andere Aktionen, die neben „E-Mail/SMS/Push/Sprachanruf“ eine Warnung auslösen können, wie beispielsweise eine Azure-Funktion, LogicApp, Webhook, ITSM und Automation Runbook. [Weitere Informationen](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information).

Das Blatt **E-Mail/SMS/Push/Sprachanruf** wird geöffnet. Wählen Sie einen **Namen** für den Empfänger, **aktivieren** Sie das Feld **E-Mail**, und geben Sie eine E-Mail-Adresse ein, an die die Warnung gesendet werden soll. Klicken Sie auf dem Blatt **E-Mail/SMS/Push/Sprachanruf** und dann auf dem Blatt **Aktionsgruppe hinzufügen** auf **OK**, um die Konfiguration Ihrer Aktionsgruppe abzuschließen.

![Hinzufügen einer Aktionsgruppe zur Warnungsregel](media/hdinsight-cluster-availability/portal-add-action-group.png)

Nachdem diese Blätter geschlossen wurden, sollte Ihre Aktionsgruppe unter dem Abschnitt **Aktionsgruppen** aufgeführt sein. Geben Sie abschließend im Abschnitt **Warnungsdetails** den **Namen der Warnungsregel** und eine **Beschreibung** ein, und wählen Sie einen **Schweregrad**.
Klicken Sie im letzten Schritt auf **Warnungsregel erstellen**.

![Fertigstellung der Warnungsregel](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> Die Möglichkeit, einen **Schweregrad** anzugeben, ist ein leistungsstarkes Instrument, das bei der Erstellung mehrerer Benachrichtigungen verwendet werden kann. Sie können beispielsweise eine Warnung mit Schweregrad 1 auszulösen, wenn ein einzelner Hauptknoten ausfällt, und eine weitere kritische Warnung mit Schweregrad 0 für den unwahrscheinlichen Fall erstellen, dass beide Hauptknoten ausfallen.

Wenn die Bedingung für diese Warnung erfüllt ist, wird die Warnung ausgelöst, und Sie erhalten eine E-Mail mit den entsprechenden Details:

![Azure Monitor-Warnungs-E-Mail](media/hdinsight-cluster-availability/alert-email.png)

Sie können auch alle ausgelösten Warnungen gruppiert nach Schweregrad anzeigen, indem Sie in Ihrem **Log Analytics-Arbeitsbereich** zum Blatt **Warnungen** wechseln.

![Warnungen im Log Analytics-Arbeitsbereich](media/hdinsight-cluster-availability/portal-alerts.png)

Wenn Sie auf eine Schweregradgruppierung klicken (z.B. **Sev 1**, wie oben hervorgehoben), werden Datensätze für alle Warnungen dieses Schweregrads angezeigt, die ausgelöst wurden (siehe unten):

![Log Analytics-Arbeitsbereich mit Warnungen des Schweregrads 1](media/hdinsight-cluster-availability/portal-alerts-sev-1.png)

## <a name="next-steps"></a>Nächste Schritte
- [Verfügbarkeit und Zuverlässigkeit von Apache Hadoop-Clustern in HDInsight](hdinsight-high-availability-linux.md)
