---
title: Überwachen und Verwalten von Azure HDInsight mithilfe der Ambari-Webbenutzeroberfläche
description: Erfahren Sie, wie Sie die Apache Ambari-UI zum Überwachen und Verwalten von HDInsight-Clustern verwenden.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/16/2020
ms.openlocfilehash: f21b72cd519c1615d8273bf316a8d0ccad039672
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546159"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-Webbenutzeroberfläche

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari vereinfacht die Verwaltung und Überwachung von Apache Hadoop-Clustern. Diese Vereinfachung erfolgt durch die Bereitstellung einer benutzerfreundlichen Webbenutzeroberfläche und REST-API. Ambari ist in HDInsight-Clustern enthalten und wird verwendet, um den Cluster zu überwachen und Konfigurationsänderungen vorzunehmen.

In diesem Dokument erfahren Sie, wie Sie die Webbenutzeroberfläche Ambari mit einem HDInsight-Cluster verwenden.

## <a name="what-is-apache-ambari"></a><a id="whatis"></a>Was ist Apache Ambari?

[Apache Ambari](https://ambari.apache.org) vereinfacht die Hadoop-Verwaltung durch Bereitstellung einer einfach zu verwendenden Web-Benutzeroberfläche. Sie können Ambari verwenden, um Hadoop-Cluster zu verwalten und zu überwachen. Entwickler können diese Funktionen mithilfe der [Ambari-REST-APIs](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)in ihre Anwendungen integrieren.

## <a name="connectivity"></a>Konnektivität

Die Ambari-Webbenutzeroberfläche ist in Ihrem HDInsight-Cluster unter `https://CLUSTERNAME.azurehdinsight.net` verfügbar, wobei `CLUSTERNAME` dem Namen Ihres Clusters entspricht.

> [!IMPORTANT]  
> Zum Herstellen einer Verbindung mit Ambari in HDInsight ist HTTPS erforderlich. Wenn Sie aufgefordert werden, sich zu authentifizieren, verwenden Sie den Namen und das Kennwort des Administratorkontos, die Sie bereitgestellt haben, als der Cluster erstellt wurde. Wenn Sie nicht zur Eingabe von Anmeldeinformationen aufgefordert werden, überprüfen Sie Ihre Netzwerkeinstellungen, um zu bestätigen, dass zwischen dem Client und den Azure HDInsight-Clustern kein Verbindungsproblem vorliegt.

## <a name="ssh-tunnel-proxy"></a>SSH-Tunnel (Proxy)

Während Ambari für Ihren Cluster direkt über das Internet erreichbar ist, werden einige Links der Ambari-Webbenutzeroberfläche (z.B. JobTracker) nicht im Internet verfügbar gemacht. Um auf diesen Dienst zuzugreifen, müssen Sie einen SSH-Tunnel erstellen. Weitere Informationen finden Sie unter [Verwenden von SSH-Tunneling mit HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Ambari-Webbenutzeroberfläche

> [!WARNING]  
> Nicht alle Funktionen der Ambari-Webbenutzeroberfläche werden in HDInsight unterstützt. Weitere Informationen finden Sie im Abschnitt [Nicht unterstützte Vorgänge](#unsupported-operations) dieses Dokuments.

Wenn Sie eine Verbindung zur Webbenutzeroberfläche Ambari herstellen, werden Sie aufgefordert, sich für die Seite zu authentifizieren. Verwenden Sie den Clusteradministrator (standardmäßig „Admin“) und das Kennwort, die Sie während der Erstellung des Clusters verwendet haben.

Beachten Sie beim Öffnen der Seite die Leiste im oberen Bereich. Diese Leiste enthält die folgenden Informationen und Steuerelemente:

![Apache Ambari-Dashboard – Übersicht](./media/hdinsight-hadoop-manage-ambari/apache-ambari-dashboard.png)

|Element |BESCHREIBUNG |
|---|---|
|Ambari-Logo|Öffnet das Dashboard, mit dem der Cluster überwacht werden kann.|
|Clustername # ops|Zeigt die Anzahl der laufenden Ambari-Vorgänge an. Wenn Sie den Clusternamen oder die **(Anzahl) ops** auswählen, wird eine Liste der Hintergrundvorgänge angezeigt.|
|# alerts|Zeigt Warnungen oder kritische Warnungen für den Cluster an (falls vorhanden).|
|Dashboard|Zeigt das Dashboard an.|
|Dienste|Informationen und Konfigurationseinstellungen für die Dienste im Cluster.|
|Hosts|Informationen und Konfigurationseinstellungen für die Knoten im Cluster.|
|Alerts|Ein Protokoll von Informationen, Warnungen und kritischen Warnungen.|
|Admin|Softwarestapel/Dienste, die im Cluster installiert sind, Dienstkontoinformationen sowie die Kerberos-Sicherheit.|
|Schaltfläche „Admin“|Ambari-Verwaltung, Benutzereinstellungen und Abmeldung.|

## <a name="monitoring"></a>Überwachung

### <a name="alerts"></a>Alerts

Die folgende Liste enthält die gängigsten Warnungsstatus, die von Ambari verwendet werden:

* **OK**
* **Warning**
* **CRITICAL**
* **UNKNOWN**

Bei anderen Warnungen als **OK** wird am Seitenanfang unter dem Eintrag **(Anzahl) alerts** die Anzahl der Warnungen anzeigt. Wenn Sie diesen Eintrag auswählen, werden die Warnungen und deren Statusangaben angezeigt.

Warnungen sind in mehrere Standardgruppen angeordnet, die über die Seite **Alerts** angezeigt werden können.

![Apache Ambari – Zusammenfassung der Seite „Warnungen“](./media/hdinsight-hadoop-manage-ambari/hdinsight-alerts-page.png)

Sie können die Gruppen verwalten, indem Sie das Menü **Aktionen** und dann die Option **Warnungsgruppen verwalten** auswählen.

![Apache Ambari – Warnungsgruppen verwalten](./media/hdinsight-hadoop-manage-ambari/ambari-manage-alerts.png)

Über den Eintrag __Warnungsbenachrichtigungen verwalten__ im Menü **Aktionen** können Sie zudem Warnungsmethoden verwalten und Warnungsbenachrichtigungen erstellen. Es werden alle aktuellen Benachrichtigungen angezeigt. Erstellen Sie hier Benachrichtigungen. Die Benachrichtigungen können bei bestimmten Kombinationen aus Warnung und Schweregrad über **E-Mail** oder **SNMP** gesendet werden. Sie können z.B. eine E-Mail senden, wenn eine der Benachrichtigungen in der Gruppe **YARN Default** auf **Kritisch** festgelegt ist.

![Apache Ambari – Warnungsbenachrichtigung erstellen](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Über den Eintrag __Warnungseinstellungen verwalten__ im Menü __Aktionen__ können Sie schließlich festlegen, wie oft eine Warnung ausgegeben werden muss, bevor eine Benachrichtigung gesendet wird. Dies Einstellung kann verwendet werden, um Benachrichtigungen für vorübergehende Fehler zu verhindern.

Ein Tutorial für eine Warnmeldung mit einem kostenlosen [SendGrid-Konto](../sendgrid-dotnet-how-to-send-email.md) finden Sie unter [Konfigurieren von Apache Ambari-E-Mail-Benachrichtigungen in Azure HDInsight](./apache-ambari-email.md).

### <a name="cluster"></a>Cluster

Die Registerkarte **Metrics** im Dashboard enthält eine Reihe von Widgets, die das Überwachen des Clusterstatus auf einen Blick erleichtern. Verschiedene Widgets wie **CPU Usage** bieten zusätzliche Informationen, wenn Sie darauf klicken.

![Apache Ambari-Dashboard mit Metriken](./media/hdinsight-hadoop-manage-ambari/hdi-metrics-dashboard.png)

Die Registerkarte **Heatmaps** zeigt Metriken als farbige Wärmebilder an, die von Grün bis Rot reichen.

![Apache Ambari-Dashboard mit Heatmaps](./media/hdinsight-hadoop-manage-ambari/hdi-heatmap-dashboard.png)

Weitere Informationen zu den Knoten im Cluster finden Sie unter **Hosts** . Wählen Sie anschließend den bestimmten Knoten, für den Sie sich interessieren.

![Apache Ambari – Details der Hostzusammenfassung](./media/hdinsight-hadoop-manage-ambari/ambari-host-details1.png)

### <a name="services"></a>Dienste

Die Seitenleiste **Dienste** des Dashboards bietet einen schnellen Überblick über den Status der Dienste, die im Cluster aktiv sind. Es werden verschiedene Symbole verwendet, um den Status oder Aktionen anzugeben, die vorgenommen werden sollten. Ein gelbes Recycling-Symbol wird z.B. angezeigt, wenn ein Dienst wiederverwendet werden muss.


![Apache Ambari – Seitenleiste „Dienste“](./media/hdinsight-hadoop-manage-ambari/apache-ambari-service-bar.png)

> [!NOTE]  
> Je nach Typ und Version des HDInsight-Clusters werden unterschiedliche Dienste angezeigt. Hier werden daher möglicherweise andere Dienste angezeigt als für Ihren Cluster.

Wenn Sie einen Dienst auswählen, werden weitere ausführliche Informationen zum Dienst angezeigt.

![Apache Ambari – Zusammenfassungsinformationen zum Dienst](./media/hdinsight-hadoop-manage-ambari/ambari-service-details.png)

#### <a name="quick-links"></a>Quicklinks

Einige Dienste zeigen oben auf der Seite den Link **Quicklinks** an. Über diesen Link können Sie auf dienstspezifische Webbenutzeroberflächen zugreifen. Beispiel:

* **Job History** : MapReduce-Auftragsverlauf.
* **Resource Manager** : YARN Resource Manager-Benutzeroberfläche.
* **NameNode** : NameNode-Benutzeroberfläche im Hadoop Distributed File System (HDFS).
* **Oozie Web UI** : Oozie-Benutzeroberfläche.

Wenn Sie einen dieser Links öffnen, wird eine Registerkarte im Browser geöffnet, auf der die ausgewählte Seite angezeigt wird.

> [!NOTE]  
> Das Auswählen des Eintrags **Quicklinks** für einen Dienst kann womöglich zum Fehler „Server nicht gefunden“ führen. Wenn dieser Fehler angezeigt wird, müssen Sie einen SSH-Tunnel verwenden, wenn Sie den Eintrag **Quicklinks** für diesen Dienst nutzen. Informationen hierzu finden Sie unter [Verwenden von SSH-Tunneln mit HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="management"></a>Verwaltung

### <a name="ambari-users-groups-and-permissions"></a>Ambari-Benutzer, -Gruppen und -Berechtigungen

Das Arbeiten mit Benutzern, Gruppen und Berechtigungen wird unterstützt. Informationen zur lokalen Verwaltung finden Sie unter [Autorisieren von Benutzern für Apache Ambari-Ansichten](./hdinsight-authorize-users-to-ambari.md). Informationen zu Clustern, die in die Domäne eingebunden sind, finden Sie unter [Verwalten von in die Domäne eingebundenen HDInsight-Clustern](./domain-joined/hdinsight-security-overview.md).

> [!WARNING]  
> Ändern Sie nicht das Kennwort für den Ambari-Watchdog (hdinsightwatchdog) in Ihrem Linux-basierten HDInsight-Cluster. Durch eine Kennwortänderung wird die Möglichkeit zum Verwenden von Skriptaktionen oder zum Durchführen von Skalierungsvorgängen mit Ihren Cluster deaktiviert.

### <a name="hosts"></a>Hosts

Die Seite **Hosts** listet alle Hosts im Cluster auf. Gehen Sie folgendermaßen vor, um Hosts zu verwalten.

![Apache Ambari – Übersicht über die Seite „Hosts“](./media/hdinsight-hadoop-manage-ambari/hdinsight-hosts-page.png)

> [!NOTE]  
> Für HDInsight-Cluster sollte kein Host hinzugefügt, zurückgesetzt und wiederhergestellt werden.

1. Wählen Sie den Host aus, den Sie verwalten möchten.

2. Verwenden Sie das Menü **Actions** , um die Aktion auswählen, die Sie durchführen möchten:

    |Element |BESCHREIBUNG |
    |---|---|
    |Start all components|Startet alle Komponenten auf dem Host.|
    |Stop all components|Beendet alle Komponenten auf dem Host.|
    |Restart all components|Beendet und startet alle Komponenten auf dem Host.|
    |Turn on maintenance mode|Unterdrückt Warnungen für den Host. Dieser Modus muss aktiviert sein, wenn Sie Vorgänge ausführen, die Warnungen generieren. Zum Beispiel das Anhalten und Starten eines Diensts.|
    |Turn off maintenance mode|Versetzt den Host in den normalen Warnmodus zurück.|
    |Beenden|Beendet DataNode oder NodeManagers auf dem Host.|
    |Start|Startet DataNode oder NodeManagers auf dem Host.|
    |Neu starten|Beendet DataNode oder NodeManager auf dem Host und startet sie dann wieder.|
    |Decommission|Entfernt einen Host aus dem Cluster. **Verwenden Sie diese Aktion nicht für HDInsight-Cluster.**|
    |Recommission|Fügt einen zuvor außer Betrieb gesetzten Host dem Cluster hinzu. **Verwenden Sie diese Aktion nicht für HDInsight-Cluster.**|

### <a name="services"></a><a id="service"></a>Dienste

Verwenden Sie auf der Seite **Dashboard** oder **Dienste** am Ende der Dienstliste die Schaltfläche **Aktionen** , um alle Dienste zu beenden und zu starten.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/ambari-service-actions.png" alt-text="Apache Ambari – Liste der Dienstaktionen." border="true":::

> [!WARNING]  
> Neue Dienste sollten während der Clusterbereitstellung mit einer Skriptaktion hinzugefügt werden. Weitere Informationen zur Verwendung von Skriptaktionen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

Während die Schaltfläche **Actions** alle Dienste neu starten kann, ist es möglicherweise häufig erforderlich, dass Sie bestimmte Dienste starten, beenden oder neu starten möchten. Gehen Sie wie folgt vor, um Aktionen für einzelne Dienste auszuführen:

1. Wählen Sie auf der Seite **Dashboard** oder **Dienste** einen Dienst aus.

2. Verwenden Sie am oberen Rand der Registerkarte **Zusammenfassung** die Schaltfläche **Dienstaktionen** , und wählen Sie dann die auszuführende Aktion aus. Mit dieser Aktion wird der Dienst auf allen Knoten neu gestartet.

    ![Apache Ambari – Einzelne Dienstaktionen](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > Der Neustart einiger Dienste bei aktivem Cluster kann dazu führen, dass Warnungen generiert werden. Um Warnungen zu vermeiden, können Sie die Schaltfläche **Dienstaktionen** verwenden, um den **Wartungsmodus** für den Dienst zu aktivieren, bevor Sie den Neustart durchführen.

3. Nachdem eine Aktion ausgewählt wurde, wird der Eintrag **(Anzahl) ops** am Seitenanfang erhöht, um anzuzeigen, dass ein Hintergrundvorgang ausgeführt wird. Sofern die Anzeige konfiguriert ist, wird die Liste der Hintergrundvorgänge angezeigt.

   > [!NOTE]  
   > Wenn Sie den **Wartungsmodus** für den Dienst aktiviert haben, denken Sie daran, ihn mithilfe der Schaltfläche **Dienstaktionen** wieder zu deaktivieren, sobald der Vorgang abgeschlossen ist.

Gehen Sie wie folgt vor, um einen Dienst zu konfigurieren:

1. Wählen Sie auf der Seite **Dashboard** oder **Dienste** einen Dienst aus.

2. Wählen Sie die Registerkarte **Configs** aus. Daraufhin wird die aktuelle Konfiguration angezeigt. Zudem wird eine Liste der vorherigen Konfigurationen angezeigt.

    ![Apache Ambari – Dienstkonfiguration](./media/hdinsight-hadoop-manage-ambari/ambari-service-configs.png)

3. Verwenden Sie die angezeigten Felder, um die Konfiguration zu ändern, und klicken Sie dann auf **Save** . Sie können auch eine vorherige Konfiguration und dann **Make current** auswählen, um die vorherigen Einstellungen zu reaktivieren.

## <a name="ambari-views"></a>Ambari-Ansichten

Ambari-Ansichten ermöglichen Entwicklern das Hinzufügen von Benutzeroberflächenelementen zur Ambari-Webbenutzeroberfläche mit dem Apache Ambari Views Framework. HDInsight bietet für Hadoop-Clustertypen die folgenden Ansichten:

* Hive-Ansicht: In der Hive-Ansicht können Sie Hive-Abfragen direkt in Ihrem Webbrowser ausführen. Sie können Abfragen speichern sowie Ergebnisse anzeigen, im Clusterspeicher speichern oder auf Ihr lokales System herunterladen. Weitere Informationen zum Verwenden von Hive-Ansichten finden Sie unter [Verwenden von Apache Hive-Ansichten mit HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Tez-Ansicht: Mit der Tez-Ansicht können Sie Aufträge besser verstehen und optimieren. Sie können Informationen darüber sehen, wie Tez-Aufträge ausgeführt werden und welche Ressourcen verwendet werden.

## <a name="unsupported-operations"></a>Nicht unterstützte Vorgänge

Die folgenden Ambari-Vorgänge werden nicht in HDInsight unterstützt:

* __Verschieben des Metrikensammlungsdiensts__ . Beim Anzeigen von Informationen im Metrikensammlungsdienst ist im Menü „Dienstaktionen“ u.a. die Aktion __Metrikensammler verschieben__ vorhanden. Diese Aktion wird mit HDInsight nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

* [Apache Ambari-REST-API](hdinsight-hadoop-manage-ambari-rest-api.md) mit HDInsight.
* [Verwenden von Apache Ambari zum Optimieren von HDInsight-Clusterkonfigurationen](./hdinsight-changing-configs-via-ambari.md)
* [Skalieren von Azure HDInsight-Clustern](./hdinsight-scaling-best-practices.md)