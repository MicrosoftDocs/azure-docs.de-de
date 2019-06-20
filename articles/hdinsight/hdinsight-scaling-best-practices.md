---
title: Skalieren von Clustergrößen – Azure HDInsight
description: Flexibles Skalieren eines Azure HDInsight-Clusters zur Anpassung an Ihre Workload.
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: b85277a4238351b6448c2cf29676ae3d8c118385
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077195"
---
# <a name="scale-hdinsight-clusters"></a>Skalieren von HDInsight-Clustern

HDInsight bietet Flexibilität, indem Sie die Anzahl der Workerknoten in Ihren Clustern zentral hoch- und herunterskalieren können. Durch diese Flexibilität können Sie einen Cluster nach den Geschäftsstunden oder am Wochenende verkleinern und während der Spitzenbelastungen erweitern.

Bei einer regelmäßigen Batchverarbeitung kann der HDInsight-Cluster ein paar Minuten vor diesem Vorgang zentral hochskaliert werden, damit ausreichend Arbeitsspeicher und CPU-Rechenleistung zur Verfügung stehen.  Später, wenn nach der Verarbeitung die Nutzung wieder sinkt, können Sie den HDInsight-Cluster auf weniger Workerknoten zentral herunterskalieren.

Sie können einen Cluster mit einer der unten aufgeführten Methoden manuell skalieren oder die Optionen der [Autoskalierung](hdinsight-autoscale-clusters.md) verwenden, damit das zentrale Hoch- und Herunterskalieren als Reaktion auf CPU-Leistung, Arbeitsspeicher und andere Metriken im System automatisch erfolgt.

> [!NOTE]  
> Es werden nur Cluster mit HDInsight-Versionen ab 3.1.3 unterstützt. Überprüfen Sie ggf. auf der Seite „Eigenschaften“ die Version Ihres Clusters.

## <a name="utilities-to-scale-clusters"></a>Hilfsprogramme zum Skalieren von Clustern

Microsoft bietet die folgenden Hilfsprogramme für das Skalieren von Clustern:

|Hilfsprogramm | BESCHREIBUNG|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<Clustername> -TargetInstanceCount \<NeueGröße>|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -ClusterName \<Clustername> -TargetInstanceCount \<NeueGröße>|
|[Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --resource-group \<Ressourcengruppe> --name \<Clustername> --target-instance-count \<NeueGröße>|
|[Azure-Befehlszeilenschnittstelle](hdinsight-administer-use-command-line.md)|azure hdinsight cluster resize \<Clustername> \<Zielinstanzgröße> |
|[Azure-Portal](https://portal.azure.com)|Öffnen Sie Ihren HDInsight-Clusterbereich, wählen Sie im linken Menü **Clustergröße** aus, geben Sie dann im Bereich „Clustergröße“ die Anzahl der Workerknoten ein, und wählen Sie „Speichern“ aus.|  

![Skalieren von Clustern](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

Mit jeder dieser Methoden können Sie Ihren HDInsight-Cluster innerhalb von Minuten zentral hoch- oder herunterskalieren.

> [!IMPORTANT]  
> * Die klassische Azure-Befehlszeilenschnittstelle ist veraltet und sollte nur mit dem klassischen Bereitstellungsmodell verwendet werden. Verwenden Sie für alle anderen Bereitstellungen die [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * Das PowerShell-AzureRM-Modul ist veraltet.  Verwenden Sie wenn möglich das [Az-Modul](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0).

## <a name="impact-of-scaling-operations"></a>Auswirkungen von Skalierungsvorgängen

Wenn Sie Ihrem ausgeführten HDInsight-Cluster Knoten **hinzufügen** (zentrales Hochskalieren), sind ausstehende oder ausgeführte Aufträge nicht betroffen. Neue Aufträge können sicher übermittelt werden, während der Skalierungsprozess ausgeführt wird. Wenn bei der Skalierung aus einem beliebigen Grund ein Fehler auftritt, wird der Fehler behandelt, sodass der Cluster funktionsfähig bleibt.

Wenn Sie Knoten **entfernen** (zentrales Herunterskalieren), treten bei ausstehenden oder ausgeführten Aufträgen Fehler auf, wenn die Skalierung abgeschlossen ist. Der Grund für diese Fehler ist, dass einige Dienste während des Skalierungsvorgangs neu gestartet werden. Es besteht zudem das Risiko, dass der Cluster während eines manuellen Skalierungsvorgangs im abgesicherten Modus hängen bleibt.

Auswirkungen der Änderung der Anzahl von Datenknoten variieren für die von HDInsight unterstützten Clustertypen:

* Apache Hadoop

    Sie können die Anzahl der Workerknoten in einem aktiven Hadoop-Cluster problemlos ohne Auswirkungen auf ausstehende oder aktive Aufträge erhöhen. Neue Aufträge können auch während des Vorgangs gesendet werden. Fehler bei einer Skalierung werden ordnungsgemäß behandelt, sodass der Cluster immer in einem funktionsfähigen Zustand verbleibt.

    Wenn ein Hadoop-Cluster durch Verringern der Anzahl der Datenknoten zentral herunterskaliert wird, werden einige der Dienste im Cluster neu gestartet. Dieses Verhalten führt beim Abschluss des Skalierungsvorgangs bei allen aktiven und ausstehenden Aufträgen zu einem Fehler. Sie können die Aufträge jedoch nach Abschluss des Vorgangs erneut senden.

* Apache HBase

    Sie können Knoten reibungslos Ihrem HBase-Cluster hinzufügen oder aus diesem entfernen, während er aktiv ist. Regionale Server werden innerhalb weniger Minuten nach Abschluss des Skalierungsvorgangs automatisch ausgeglichen. Allerdings können Sie die regionalen Server auch manuell ausgleichen, indem Sie sich am Hauptknoten des Clusters anmelden und in einem Eingabeaufforderungsfenster die folgenden Befehle ausführen:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Weitere Informationen zur Verwendung von HBase-Shell finden Sie unter [Erste Schritte mit einem Apache HBase-Beispiel in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Sie können Datenknoten übergangslos zum Storm-Cluster hinzufügen oder aus diesem entfernen, während er aktiv ist. Nach erfolgreichem Abschluss des Skalierungsvorgangs müssen Sie jedoch die Topologie neu ausgleichen.

    Es stehen zwei Methoden für den erneuten Ausgleich zur Verfügung:

  * Storm-Webbenutzeroberfläche
  * Befehlszeilenschnittstelle (CLI)

    Weitere Informationen finden Sie in der [Apache Storm-Dokumentation](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

    Die Storm-Webbenutzeroberfläche ist für den HDInsight-Cluster verfügbar:

    ![Ausgleichen der HDInsight Storm-Skalierung](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Es folgt ein Beispiel-CLI-Befehl für den Neuausgleich der Storm-Topologie:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>Sicheres zentrales Herunterskalieren eines Clusters

### <a name="scale-down-a-cluster-with-running-jobs"></a>Zentrales Herunterskalieren eines Clusters mit ausgeführten Aufträgen

Um zu vermeiden, dass bei ausgeführten Aufträgen bei einem Vorgang zum zentralen Herunterskalieren Fehler auftreten, können Sie drei Möglichkeiten ausprobieren:

1. Warten Sie, bis die Aufträge abgeschlossen sind, bevor Sie den Cluster zentral herunterskalieren.
1. Beenden Sie die Aufträge manuell.
1. Übermitteln Sie die Aufträge nach Abschluss des Skalierungsvorgangs erneut.

Um eine Liste der ausstehenden und ausgeführten Aufträge anzuzeigen, können Sie mit folgenden Schritten die YARN **Resource Manager-Benutzeroberfläche** verwenden:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihren Cluster aus.  Anweisungen dazu finden Sie unter [Auflisten und Anzeigen von Clustern](./hdinsight-administer-use-portal-linux.md#showClusters). Der Cluster wird auf einer neuen Portalseite geöffnet.
2. Navigieren Sie in der Hauptansicht zu **Clusterdashboards** > **Ambari-Startseite**. Geben Sie Ihre Clusteranmeldeinformationen ein.
3. Wählen Sie auf der Benutzeroberfläche von Ambari in der Liste mit den Diensten im linken Menü **YARN** aus.  
4. Wählen Sie auf der Seite „YARN“ **Quick Links** aus, zeigen Sie auf den aktiven Hauptknoten, und wählen Sie **ResourceManager UI** (Resource Manager-Benutzeroberfläche) aus.

    ![ResourceManager UI](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

Sie können direkt mit `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster` auf die ResourceManager UI zugreifen.

Eine Liste der Aufträge wird zusammen mit ihren aktuellen Status angezeigt. Im Screenshot wird derzeit ein Auftrag ausgeführt:

![ResourceManager UI-Anwendungen](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Um die ausgeführte Anwendung manuell zu beenden, führen Sie den folgenden Befehl der SSH-Shell aus:

```bash
yarn application -kill <application_id>
```

Beispiel:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Hängenbleiben im abgesicherten Modus

Wenn Sie einen Cluster zentral herunterskalieren, werden in HDInsight Apache Ambari-Verwaltungsoberflächen verwendet, um zuerst die zusätzlichen Workerknoten außer Betrieb zu setzen, sodass deren HDFS-Blöcke auf andere Workerknoten repliziert werden, die online sind. Anschließend wird der Cluster in HDInsight sicher zentral herunterskaliert. HDFS wechselt während des Skalierungsvorgangs in den abgesicherten Modus und soll diesen nach Abschluss der Skalierung wieder verlassen. In einigen Fällen bleibt HDFS jedoch aufgrund der Unterreplikation des Dateiblocks während eines Skalierungsvorgangs im abgesicherten Modus hängen.

Standardmäßig ist HDFS mit der `dfs.replication`-Einstellung 3 konfiguriert, mit der gesteuert wird, wie viele Kopien jedes Dateiblocks verfügbar sind. Jede Kopie eines Dateiblocks wird auf einem unterschiedlichen Knoten des Clusters gespeichert.

Wenn HDFS erkennt, dass die erwartete Anzahl von Kopien der Dateiblöcke nicht verfügbar ist, wechselt HDFS in den abgesicherten Modus, und in Ambari werden Warnungen generiert. Wenn HDFS bei einem Skalierungsvorgang in den abgesicherten Modus wechselt, diesen dann jedoch nicht verlassen kann, da die erforderliche Anzahl der Knoten für die Replikation nicht erkannt wird, kann der Cluster im abgesicherten Modus hängen bleiben.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Beispiel für Fehler bei aktiviertem abgesichertem Modus

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Sie können die Namensknotenprotokolle aus dem `/var/log/hadoop/hdfs/`-Ordner überprüfen, die nahe am Zeitpunkt der Clusterskalierung liegen, um festzustellen, wann der Wechsel in den abgesicherten Modus erfolgte. Die Protokolldateien werden mit `Hadoop-hdfs-namenode-hn0-clustername.*` benannt.

Die Grundursache für die vorherigen Fehler ist, dass Hive während der Ausführung von Abfragen von temporären Dateien in HDFS abhängig ist. Wenn HDFS in den abgesicherten Modus wechselt, kann Hive keine Abfragen ausführen, weil Hive nicht in HDFS schreiben kann. Die temporären Dateien in HDFS befinden sich auf dem lokalen Laufwerk, das in einzelne Workerknoten-VMs eingebunden ist, und werden mit mindestens drei Replikaten zwischen anderen Workerknoten repliziert.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Verhindern, dass HDInsight im abgesicherten Modus hängen bleibt

Es gibt mehrere Möglichkeiten, zu verhindern, dass HDInsight im abgesicherten Modus hängen bleibt:

* Beenden Sie alle Hive-Aufträge, bevor Sie HDInsight zentral herunterskalieren. Alternativ können Sie das zentrale Herunterskalieren planen, um Konflikte mit der Ausführung von Hive-Aufträgen zu verhindern.
* Bereinigen Sie vor dem zentralen Herunterskalieren manuell die `tmp`-Scratchverzeichnisdateien von Hive in HDFS.
* Skalieren Sie HDInsight nur auf minimal drei Workerknoten zentral herunter. Vermeiden Sie es, auf nur einen Workerknoten herunter zu gehen.
* Führen Sie bei Bedarf den Befehl zum Verlassen des abgesicherten Modus aus.

In den folgenden Abschnitten werden diese Optionen beschrieben.

#### <a name="stop-all-hive-jobs"></a>Beenden aller Hive-Aufträge

Beenden Sie alle Hive-Aufträge, bevor Sie einen Workerknoten zentral herunterskalieren. Wenn Ihre Workload geplant ist, führen Sie Ihre zentrale Herunterskalierung nach Abschluss der Hive-Arbeit durch.

Durch Beenden der Hive-Aufträge vor der Skalierung kann die Anzahl der Scratchdateien im tmp-Ordner gegebenenfalls minimiert werden.

#### <a name="manually-clean-up-hives-scratch-files"></a>Manuelles Bereinigen der Hive-Scratchdateien

Wenn Hive temporäre Dateien zurückgelassen hat, können Sie diese Dateien vor dem zentralen Herunterskalieren manuell bereinigen, um den abgesicherten Modus zu vermeiden.

1. Überprüfen Sie, welcher Speicherort für temporäre Hive-Dateien verwendet wird, indem Sie die Konfigurationseigenschaft `hive.exec.scratchdir` anzeigen. Dieser Parameter wird in `/etc/hive/conf/hive-site.xml` festgelegt:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Beenden Sie die Hive-Dienste, und achten Sie darauf, dass alle Abfragen und Aufträge abgeschlossen sind.
2. Listen Sie den Inhalt des oben angegebenen Scratchverzeichnisses `hdfs://mycluster/tmp/hive/` auf, um festzustellen, ob es Dateien enthält:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Dies ist eine Beispielausgabe für den Fall, dass Dateien vorhanden sind:

    ```output
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Wenn Sie wissen, dass Hive diese Dateien nicht mehr benötigt, können Sie sie entfernen. Vergewissern Sie sich auf der ResourceManager UI-Seite von Yarn, dass keine Hive-Abfragen mehr ausgeführt werden.

    Beispielbefehlszeile zum Entfernen von Dateien aus HDFS:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Skalieren von HDInsight auf drei oder mehr Workerknoten

Wenn der Cluster beim zentralen Herunterskalieren auf weniger als drei Workerknoten häufig im abgesicherten Modus hängen bleibt und das Problem durch die vorherigen Schritte nicht behoben wird, können Sie vermeiden, dass der Cluster generell in den abgesicherten Modus wechselt, indem Sie mindestens drei Workerknoten beibehalten.

Die Beibehaltung von drei Workerknoten ist kostenaufwendiger als das zentrale Herunterskalieren auf nur einen Workerknoten, es wird jedoch verhindert, dass der Cluster im abgesicherten Modus hängen bleibt.

#### <a name="run-the-command-to-leave-safe-mode"></a>Ausführen des Befehls zum Verlassen des abgesicherten Modus

Die letzte Möglichkeit besteht darin, den Befehl zum Verlassen des abgesicherten Modus auszuführen. Wenn Sie wissen, dass der Grund für den Wechsel von HDFS in den abgesicherten Modus auf die Unterreplikation der Hive-Datei zurückzuführen ist, können Sie den folgenden Befehl ausführen, um den abgesicherten Modus zu verlassen:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Zentrales Herunterskalieren eines Apache HBase-Clusters

Regionsserver werden innerhalb weniger Minuten nach Abschluss eines Skalierungsvorgangs automatisch ausgeglichen. Um Regionsserver manuell auszugleichen, führen Sie folgende Schritte aus:

1. Stellen Sie mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster her. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Starten Sie die HBase-Shell:

    ```bash
    hbase shell
    ```

3. Um die Regionsserver manuell auszugleichen, führen Sie folgenden Befehl aus:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Automatisches Skalieren von Azure HDInsight-Clustern](hdinsight-autoscale-clusters.md)
* [Einführung in Azure HDInsight](hadoop/apache-hadoop-introduction.md)
