---
title: Autoskalierung von Azure HDInsight-Clustern
description: Verwenden Sie das Azure HDInsight-Features „Autoskalierung“, um Apache Hadoop-Cluster automatisch zu skalieren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperf-fy21q1
ms.date: 09/14/2020
ms.openlocfilehash: 09e4412128a3b13abfa91bf0c128372b30b3e686
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033135"
---
# <a name="autoscale-azure-hdinsight-clusters"></a>Autoskalierung von Azure HDInsight-Clustern

Mit der kostenlosen automatischen Skalierung von Azure HDInsight können Sie die Anzahl der Workerknoten in Ihrem Cluster basierend auf zuvor festgelegten Kriterien automatisch erhöhen oder verringern. Während der Clustererstellung legen Sie eine minimale und maximale Anzahl von Knoten fest und erstellen die Skalierungskriterien anhand eines Tageszeitplans oder bestimmter Leistungsmetriken. Dann führt die HDInsight-Plattform die übrigen Schritte aus.

## <a name="how-it-works"></a>Funktionsweise

Das Feature „Autoskalierung“ verwendet zwei Arten von Bedingungen zum Auslösen von Skalierungsereignissen: Schwellenwerte für verschiedene Clusterleistungsmetriken (die sogenannte *lastbasierte Skalierung*) und zeitbasierte Trigger (*zeitplanbasierte Skalierung*). Die lastbasierte Skalierung ändert die Anzahl der Knoten in Ihrem Cluster innerhalb eines von Ihnen festgelegten Bereichs, um eine optimale CPU-Auslastung zu gewährleisten und die Betriebskosten zu minimieren. Bei der zeitplanbasierten Skalierung wird die Anzahl der Knoten im Cluster aufgrund von Vorgängen geändert, die Sie bestimmten Datums- und Uhrzeitangaben zuordnen.

Das folgende Video vermittelt einen Überblick über die Herausforderungen, für die die Autoskalierung Abhilfe schafft. Außerdem wird erläutert, wie dieses Feature Ihnen bei der Steuerung von Kosten mit HDInsight helfen kann.


> [!VIDEO https://www.youtube.com/embed/UlZcDGGFlZ0?WT.mc_id=dataexposed-c9-niner]

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Auswählen von last- oder zeitplanbasierter Skalierung

Berücksichtigen Sie bei der Auswahl eines Skalierungstyps die folgenden Faktoren:

* Lastvarianz: Folgt die Last des Clusters zu bestimmten Uhrzeiten bzw. an bestimmten Tagen einem einheitlichen Muster? Wenn nicht, ist die lastbasierte Skalierung die besser geeignete Option.
* SLA-Anforderungen: Die Autoskalierung ist nicht vorausschauend (prädiktiv), sondern reaktiv. Besteht eine ausreichende Verzögerung zwischen dem Zeitpunkt, zu dem die Zunahme der Last beginnt, und dem Zeitpunkt, zu dem der Cluster die Zielgröße erreicht haben muss? Falls strenge SLA-Anforderungen gelten und die Last einem festen bekannten Muster folgt, ist „zeitplanbasiert“ die bessere Option.

### <a name="cluster-metrics"></a>Clustermetriken

Die automatische Skalierung überwacht kontinuierlich die Cluster und sammelt die folgenden Metriken:

|Metrik|BESCHREIBUNG|
|---|---|
|CPU insgesamt für ausstehende|Die Gesamtanzahl von Kernen, die zum Starten der Ausführung aller ausstehenden Container erforderlich sind.|
|Arbeitsspeicher insgesamt für ausstehende|Die Gesamtgröße des Arbeitsspeichers (in MB), die zum Starten der Ausführung aller ausstehenden Container erforderlich ist.|
|Freie CPUs insgesamt|Die Summe aller nicht verwendeten Kerne auf den aktiven Workerknoten.|
|Freier Arbeitsspeicher insgesamt|Die Summe aller nicht verwendeten Kerne (in MB) auf den aktiven Workerknoten.|
|Verwendeter Arbeitsspeicher pro Knoten|Die Last auf einem Workerknoten. Ein Workerknoten, auf dem 10GB Arbeitsspeicher verwendet werden, ist höher ausgelastet als ein Workerknoten, auf dem 2GB verwendet werden.|
|Anzahl der Anwendungsmaster pro Knoten|Die Anzahl der Anwendungsmastercontainer (Application Master, AM), die auf einem Workerknoten ausgeführt werden. Ein Workerknoten, der 2 AM-Container hostet, gilt als wichtiger als ein Workerknoten, der 0 AM-Container hostet.|

Die oben aufgeführten Metriken werden alle 60 Sekunden überprüft. Sie können Skalierungsvorgänge für Ihren Cluster mithilfe einer dieser Metriken einrichten.

### <a name="load-based-scale-conditions"></a>Lastbasierte Skalierungsbedingungen

Wenn die folgenden Bedingungen erkannt werden, gibt die Autoskalierung eine Skalierungsanforderung aus:

|Zentrales Hochskalieren|Zentrales Herunterskalieren|
|---|---|
|„CPU insgesamt für ausstehende“ ist länger als drei Minuten größer als die Anzahl der insgesamt freien CPUs.|„CPU insgesamt für ausstehende“ ist länger als 10 Minuten kleiner als die Anzahl der insgesamt freien CPUs.|
|„Arbeitsspeicher insgesamt für ausstehende“ ist länger als drei Minuten größer als der insgesamt freie Arbeitsspeicher.|„Arbeitsspeicher insgesamt für ausstehende“ ist länger als 10 Minuten kleiner als der insgesamt freie Arbeitsspeicher.|

Beim zentralen Hochskalieren gibt die Autoskalierung eine Anforderung aus, die erforderliche Anzahl von Knoten hinzuzufügen. Das zentrale Hochskalieren basiert auf der erforderlichen Anzahl von neuen Workerknoten zum Erfüllen der aktuellen CPU- und Arbeitsspeicheranforderungen.

Beim zentralen Herunterskalieren gibt die Autoskalierung eine Anforderung aus, eine bestimmte Anzahl von Knoten zu entfernen. Das zentrale Herunterskalieren basiert auf der Anzahl der AM-Container pro Knoten und den aktuellen CPU- und Arbeitsspeicheranforderungen. Der Dienst erkennt auch, welche Knoten aufgrund der aktuellen Auftragsausführung für die Entfernung in Frage kommen. Der Vorgang des Herunterskalierens deaktiviert zunächst die Knoten und entfernt sie dann aus dem Cluster.

### <a name="cluster-compatibility"></a>Clusterkompatibilität

> [!Important]
> Die Azure HDInsight-Funktion für die automatische Skalierung wurde mit allgemeiner Verfügbarkeit am 7. November 2019 für Spark- und Hadoop-Cluster veröffentlicht und enthält Verbesserungen, die in der Vorschauversion des Features nicht verfügbar sind. Wenn Sie vor dem 7. November 2019 einen Spark-Cluster erstellt haben und die automatische Skalierung in Ihrem Cluster verwenden möchten, wird empfohlen, einen neuen Cluster zu erstellen und die automatische Skalierung für den neuen Cluster zu aktivieren.
>
> Autoskalierung für Interactive Query (LLAP) wurde am 27. August 2020 für die allgemeine Verfügbarkeit für HDI 4.0 veröffentlicht. HBase-Cluster befinden sich weiterhin in der Vorschau. Die automatische Skalierung ist nur für Spark-, Hadoop-, Interactive Query- und HBase-Cluster verfügbar.

Die folgende Tabelle beschreibt die Clustertypen und Versionen, die mit dem Feature „Autoskalierung“ kompatibel sind.

| Version | Spark | Hive | Interactive Query | hbase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 ohne ESP | Ja | Ja | Ja | Ja* | Nein | Nein | Nein |
| HDInsight 4.0 ohne ESP | Ja | Ja | Ja | Ja* | Nein | Nein | Nein |
| HDInsight 3.6 mit ESP | Ja | Ja | Ja | Ja* | Nein | Nein | Nein |
| HDInsight 4.0 mit ESP | Ja | Ja | Ja | Ja* | Nein | Nein | Nein |

\* HBase-Cluster können nur für zeitplanbasierte, aber nicht für auslastungsbasierte Skalierung konfiguriert werden.

## <a name="get-started"></a>Erste Schritte

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Erstellen eines Clusters mit lastbasierter Autoskalierung

Um das Feature „Autoskalierung“ mit lastbasierter Skalierung zu aktivieren, führen Sie als Teil des normalen Clustererstellungsvorgangs folgende Schritte aus:

1. Aktivieren Sie auf der Registerkarte **Konfiguration + Preise** das Kontrollkästchen **Automatische Skalierung aktivieren**.
1. Wählen Sie **Lastbasiert** unter **Autoskalierungstyp** aus.
1. Geben Sie die gewünschten Werte für die folgenden Eigenschaften ein:  

    * Anfängliche **Anzahl von Workerknoten** für **Workerknoten**.
    * **Minimale** Anzahl von Workerknoten.
    * **Maximale** Anzahl von Workerknoten.

    ![Aktivieren der lastbasierten Autoskalierung des Workerknotens](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

Die anfängliche Anzahl der Workerknoten kann vom Mindest- bis zum Höchstwert reichen. Dieser Wert definiert die Anfangsgröße des Clusters bei der Erstellung. Die Mindestzahl der Workerknoten sollte auf drei oder mehr festgelegt werden. Die Skalierung des Clusters auf weniger als drei Knoten kann dazu führen, dass der Cluster aufgrund unzureichender Dateireplikation im abgesicherten Modus hängen bleibt.  Weitere Informationen finden Sie unter [Hängenbleiben im abgesicherten Modus](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Erstellen eines Clusters mit zeitplanbasiert Autoskalierung

Um das Feature „Autoskalierung“ mit zeitplanbasierter Skalierung zu aktivieren, führen Sie als Teil des normalen Clustererstellungsvorgangs folgende Schritte aus:

1. Aktivieren Sie auf der Registerkarte **Konfiguration + Preise** das Kontrollkästchen **Automatische Skalierung aktivieren**.
1. Geben Sie die **Anzahl der Knoten** für **Workerknoten** ein. Damit wird begrenzt, wie hoch der Cluster skaliert werden kann.
1. Wählen Sie unter **Autoskalierungstyp** die Option **Zeitplanbasiert** aus.
1. Wählen Sie **Konfigurieren** aus, um das Fenster **Konfiguration der Autoskalierung** zu öffnen.
1. Wählen Sie Ihre Zeitzone aus, und klicken Sie dann auf **+ Bedingung hinzufügen**.
1. Wählen Sie die Tage der Woche, an denen die neue Bedingung angewendet werden soll.
1. Bearbeiten Sie die Zeit, zu der die Bedingung wirksam werden soll, und die Anzahl der Knoten, auf die der Cluster skaliert werden soll.
1. Fügen Sie gegebenenfalls weitere Bedingungen hinzu.

    ![Aktivieren der zeitplanbasierten Erstellung des Workerknotens](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Die Anzahl der Knoten muss zwischen 3 und der maximalen Anzahl der Workerknoten liegen, die Sie vor dem Hinzufügen von Bedingungen eingegeben haben.

### <a name="final-creation-steps"></a>Letzte Erstellungsschritte

Wählen Sie den VM-Typ für Workerknoten aus, indem Sie in der Dropdownliste unter **Knotengröße** eine VM auswählen. Nachdem Sie den VM-Typ für jeden Knotentyp ausgewählt haben, können Sie den Bereich der geschätzten Kosten für den gesamten Cluster sehen. Passen Sie die VM-Typen entsprechend Ihrem Budget an.

![Aktivieren der Knotengröße für die zeitplanbasierte Autoskalierung des Workerknotens](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

Ihr Abonnement verfügt für jede Region über ein Kapazitätskontingent. Die Gesamtanzahl der Kerne Ihrer Hauptknoten und der maximalen Workerknoten darf das Kapazitätskontingent nicht überschreiten. Dieses Kontingent ist jedoch eine weiche Grenze. Sie können immer ein Supportticket erstellen, um es problemlos erhöhen zu lassen.

> [!Note]  
> Wenn Sie die gesamte Kernkontingentgrenze überschreiten, informiert Sie eine Fehlermeldung darüber, dass die maximale Knotenzahl die Zahl der verfügbaren Kerne in dieser Region überschritten hat, und Sie werden aufgefordert, eine andere Region auszuwählen oder den Support um Erhöhung des Kontingents zu bitten.

Weitere Informationen zum Erstellen von HDInsight-Clustern mit dem Azure-Portal finden Sie unter [Erstellen von Linux-basierten Clustern in HDInsight mithilfe des Azure-Portals](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Erstellen eines Clusters mithilfe einer Resource Manager-Vorlage

#### <a name="load-based-autoscaling"></a>Lastbasierte Autoskalierung

Sie können einen HDInsight-Cluster mit lastbasierter Autoskalierung und einer Azure Resource Manager-Vorlage erstellen, indem Sie dem Abschnitt `computeProfile` > `workernode` einen `autoscale`-Knoten mit den Eigenschaften `minInstanceCount` und `maxInstanceCount` hinzufügen, wie im folgenden JSON-Codeausschnitt gezeigt. Eine umfassende Resource Manager-Vorlage finden Sie unter [Schnellstartvorlage: Deploy Spark Cluster with Loadbased Autoscale Enabled](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-loadbased) (Bereitstellen eines Spark-Clusters mit aktivierter lastbasierter Autoskalierung, in englischer Sprache).

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

#### <a name="schedule-based-autoscaling"></a>Zeitplanbasierte Autoskalierung

Sie können einen HDInsight-Cluster mit zeitplanbasierter Autoskalierung und einer Azure Resource Manager-Vorlage erstellen, indem Sie dem Abschnitt `computeProfile` > `workernode` einen `autoscale`-Knoten hinzufügen. Der `autoscale`-Knoten enthält ein `recurrence` mit einer `timezone` und einem `schedule`. Damit wird beschrieben, wann die Änderung erfolgen wird. Eine umfassende Resource Manager-Vorlage finden Sie unter [Deploy Spark Cluster with schedule-based Autoscale Enabled](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-schedulebased) (Bereitstellen eines Spark-Clusters mit aktivierter zeitplanbasierter Autoskalierung, in englischer Sprache).

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        }
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Aktivieren und Deaktivieren der Autoskalierung für einen aktuell ausgeführten Cluster

#### <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals

Zum Aktivieren der Autoskalierung in einem ausgeführten Cluster wählen **Clustergröße** unter **Einstellungen**. Wählen Sie dann **Automatische Skalierung aktivieren** aus. Wählen Sie die gewünschte Art der Autoskalierung, und geben Sie die Optionen für die last- oder zeitplanbasierte Skalierung ein. Klicken Sie abschließend auf **Speichern**.

![Aktivieren der zeitplanbasierte Autoskalierung des Workerknotens für einen aktiven Cluster](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>Verwenden der REST-API

Um die Autoskalierung für einen aktiven Cluster über die REST-API zu aktivieren oder zu deaktivieren, senden Sie eine POST-Anforderung an den Endpunkt der Autoskalierung:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Verwenden Sie die geeigneten Parameter in der Anforderungsnutzlast. Die nachfolgende JSON-Nutzlast könnte verwendet werden, um Autoskalierung zu aktivieren. Verwenden Sie die Nutzlast `{autoscale: null}`, um Autoskalierung zu deaktivieren.

```json
{ "autoscale": { "capacity": { "minInstanceCount": 3, "maxInstanceCount": 5 } } }
```

Eine vollständige Beschreibung aller Nutzlastparameter finden Sie im vorherigen Abschnitt über [Aktivieren von lastenbasierter Autoskalierung](#load-based-autoscaling).

## <a name="monitoring-autoscale-activities"></a>Überwachen von automatischen Skalierungsaktivitäten

### <a name="cluster-status"></a>Clusterstatus

Der im Azure-Portal aufgeführte Clusterstatus kann Ihnen helfen, die Aktivitäten der Autoskalierung zu überwachen.

![Aktivieren des Clusterstatus für die lastbasierte Autoskalierung des Workerknotens](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Alle Statusmeldungen des Clusters, die möglicherweise angezeigt werden, werden in der folgenden Liste erläutert.

| Clusterstatus | BESCHREIBUNG |
|---|---|
| Wird ausgeführt | Der Cluster wird normal ausgeführt. Alle vorherigen Autoskalierungsaktivitäten wurde erfolgreich abgeschlossen. |
| Wird aktualisiert  | Die Autoskalierungskonfiguration für den Cluster wird aktualisiert.  |
| HDInsight-Konfiguration  | Es wird ein Vorgang für das Hoch- oder Herunterskalieren des Clusters ausgeführt.  |
| Fehler beim Aktualisieren  | HDInsight hat beim Aktualisieren der Autoskalierungskonfiguration Fehler festgestellt. Kunden können wählen, ob sie den Aktualisierungsvorgang wiederholen oder die Autoskalierung deaktivieren möchten.  |
| Fehler  | Es gibt ein Problem mit dem Cluster, sodass er kann nicht verwendet werden kann. Löschen Sie diesen Cluster, und erstellen Sie einen neuen.  |

Um die aktuelle Anzahl der Knoten in Ihrem Cluster anzuzeigen, wechseln Sie zum Diagramm **Clustergröße** auf der Seite **Übersicht** für den Cluster. Sie können auch unter **Einstellungen** die Option **Clustergröße** auswählen.

### <a name="operation-history"></a>Vorgangsverlauf

Sie können den Verlauf des zentralen Hoch- und Herunterskalierens des Clusters als Teil der Clustermetriken anzeigen. Sie können auch alle Skalierungsaktionen des letzten Tags, der letzten Woche oder eines anderen Zeitraums auflisten.

Wählen Sie unter **Überwachung** **Metriken** aus. Wählen Sie dann im Dropdownfeld **Metrik** die Option **Metrik hinzufügen** und dann **Anzahl der aktiven Worker** aus. Wählen Sie die Schaltfläche in der rechten oberen Ecke aus, um den Zeitbereich zu ändern.

![Aktivieren der Metrik für die zeitplanbasierte Autoskalierung des Workerknotens](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="best-practices"></a>Bewährte Methoden

### <a name="consider-the-latency-of-scale-up-and-scale-down-operations"></a>Berücksichtigen der Latenz von Vorgängen zum Hoch- und Herunterskalieren

Es kann 10 bis 20 Minuten dauern, bis ein Skalierungsvorgang abgeschlossen ist. Sie sollten diese Verzögerung einplanen, wenn Sie einen benutzerdefinierten Zeitplan einrichten. Wenn Sie beispielsweise um 9:00 Uhr eine Clustergröße von 20 benötigen, sollten Sie den Zeitplantrigger auf einen früheren Zeitpunkt festlegen, z. B. 8:30 Uhr, damit der Skalierungsvorgang um 9:00 Uhr abgeschlossen ist.

### <a name="prepare-for-scaling-down"></a>Vorbereiten für das zentrale Herunterskalieren

Beim zentralen Herunterskalieren des Clusters werden die Knoten von Autoskalierung außer Betrieb genommen, um die Zielgröße zu erreichen. Falls auf diesen Knoten Aufgaben ausgeführt werden, wartet Autoskalierung, bis diese für Spark- und Hadoop-Cluster abgeschlossen sind. Da jeder Workerknoten auch eine Rolle im HDFS innehat, werden die temporären Daten auf die restlichen Knoten verschoben. Stellen Sie sicher, dass auf den verbleibenden Knoten genügend Speicherplatz vorhanden ist, um alle temporären Daten zu hosten.

Die ausgeführten Aufträge werden weiterhin ausgeführt. Für die ausstehenden Aufträge wird auf eine Planung mit weniger verfügbaren Workerknoten gewartet.

### <a name="be-aware-of-the-minimum-cluster-size"></a>Beachten der Clustermindestgröße

Skalieren Sie den Cluster nicht auf weniger als drei Knoten herunter. Die Skalierung des Clusters auf weniger als drei Knoten kann dazu führen, dass der Cluster aufgrund unzureichender Dateireplikation im abgesicherten Modus hängen bleibt. Weitere Informationen finden Sie unter [Hängenbleiben im abgesicherten Modus](hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="increase-the-number-of-mappers-and-reducers"></a>Erhöhen der Anzahl von Zuordnungen (Mapper) und Reducern

Autoskalierung für Hadoop-Cluster überwacht auch die HDFS-Nutzung. Ist das HDFS ausgelastet, wird davon ausgegangen, dass der Cluster weiterhin die aktuellen Ressourcen benötigt. Wenn eine große Datenmenge an der Abfrage beteiligt ist, können Sie die Anzahl der Zuordnungen und Reducer erhöhen, um die Parallelität zu erhöhen und die HDFS-Vorgänge zu beschleunigen. Auf diese Weise wird ordnungsgemäßes Herunterskalieren ausgelöst, wenn zusätzliche Ressourcen vorhanden sind. 

### <a name="set-the-hive-configuration-maximum-total-concurrent-queries-for-the-peak-usage-scenario"></a>Festlegen der Hive-Konfiguration „Maximum Total Concurrent Queries“ für das Szenario für Spitzenlast

Die Hive-Konfiguration *Maximum Total Concurrent Queries* (Maximale Anzahl gleichzeitiger Abfragen) in Ambari wird durch Autoskalierungsereignisse nicht geändert. Dies bedeutet, dass der Hive Server 2 Interactive-Dienst immer nur die angegebene Anzahl gleichzeitiger Abfragen verarbeiten kann, auch wenn die Anzahl der Interactive Query-Daemons last- oder zeitplanbasiert zentral hoch- und herunterskaliert wird. Die allgemeine Empfehlung besteht darin, diese Konfiguration entsprechend dem Spitzenlastszenario festzulegen, damit kein manueller Eingriff erforderlich wird.

Es können jedoch Hive Server 2-Neustartfehler auftreten, wenn nur wenige Workerknoten vorhanden sind und der Wert für die maximale Anzahl gleichzeitiger Abfragen zu hoch konfiguriert ist. Sie benötigen eine Mindestanzahl von Workerknoten, die die angegebene Anzahl von Tez-AMS abdecken können (entspricht der „Maximum Total Concurrent Queries“-Konfiguration). 

## <a name="limitations"></a>Einschränkungen

### <a name="node-label-file-missing"></a>Knotenbezeichnungsdatei fehlt

HDInsight Autoskalierung verwendet eine Knotenbezeichnungsdatei, um zu bestimmen, ob ein Knoten in der Lage ist, Aufgaben auszuführen. Die Knotenbezeichnungsdatei ist auf HDFS mit drei Replikaten gespeichert. Wird die Clustergröße erheblich herunterskaliert, und liegt eine große Menge temporärer Daten vor, gibt es ein kleines Risiko, dass alle drei Replikate gelöscht werden. Wenn dies passiert, wird der Cluster in einen Fehlerzustand versetzt.

### <a name="interactive-query-daemons-count"></a>Anzahl der Interactive Query-Daemons

Für Interactive Query-Cluster mit aktivierter Autoskalierung wird bei der Autoskalierung (zentrales Hochskalieren/Herunterskalieren) auch die Anzahl der Interactive Query-Daemons entsprechend der Anzahl aktiver Workerknoten zentral hochskaliert/herunterskaliert. Die Änderung der Anzahl der Daemons wird nicht in der `num_llap_nodes`-Konfiguration in Ambari gespeichert. Wenn Hive-Dienste manuell neu gestartet werden, wird die Anzahl der Interactive Query-Daemons entsprechend der Konfiguration in Ambari zurückgesetzt.

Wird der Interactive Query-Dienst manuell neu gestartet, müssen Sie die `num_llap_node`-Konfiguration (die Anzahl der Knoten, die zum Ausführen des Hive-Interactive Query-Daemons benötigt werden) unter *Advanced hive-interactive-env* entsprechend der aktuellen Anzahl aktiver Workerknoten ändern.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das manuelle Skalieren von Clustern in den [Skalierungsrichtlinien](hdinsight-scaling-best-practices.md).
