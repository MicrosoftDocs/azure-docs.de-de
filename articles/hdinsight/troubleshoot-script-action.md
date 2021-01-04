---
title: Behandeln von Problemen mit Skriptaktionen in Azure HDInsight
description: Allgemeine Schritte zur Problembehandlung für Skriptaktionen in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: ef9322c17a20ab5bfcf348649a1272dd4f301c5c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000673"
---
# <a name="troubleshoot-script-actions-in-azure-hdinsight"></a>Behandeln von Problemen mit Skriptaktionen in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="viewing-logs"></a>Anzeigen von Protokollen

Über die Apache Ambari-Webbenutzeroberfläche können Sie Informationen anzeigen, die von Skriptaktionen protokolliert wurden. Wenn im Skript während der Clustererstellung ein Fehler auftritt, befinden sich die Protokolle im Standardspeicherkonto des Clusters. Dieser Abschnitt enthält Informationen zum Abrufen der Protokolle mit den beiden folgenden Optionen:

### <a name="apache-ambari-web-ui"></a>Apache Ambari-Webbenutzeroberfläche

1. Navigieren Sie in einem Webbrowser zu `https://CLUSTERNAME.azurehdinsight.net`, wobei `CLUSTERNAME` der Name Ihres Clusters ist.

1. Wählen Sie in der Leiste oben auf der Seite die Option **ops** aus. Daraufhin wird eine Liste mit aktuellen und vorherigen Vorgängen angezeigt, die über Ambari für den Cluster ausgeführt wurden.

    ![Ambari-Webbenutzeroberfläche mit ausgewählter Option "ops"](./media/troubleshoot-script-action/hdi-apache-ambari-nav.png)

1. Suchen Sie nach den Einträgen, die **run\_customscriptaction** in der Spalte **Vorgänge** enthalten. Diese Einträge werden erstellt, wenn die Skriptaktionen ausgeführt werden.

    ![Apache Ambari – Vorgänge für Skriptaktion](./media/troubleshoot-script-action/ambari-script-action.png)

    Wählen Sie den Eintrag **run\customscriptaction** aus, und navigieren Sie durch die Links, um die Ausgabe für **STDOUT** und **STDERR** anzuzeigen. Diese Ausgabe wird beim Ausführen des Skripts generiert und kann hilfreiche Informationen enthalten.

### <a name="default-storage-account"></a>Standardspeicherkonto

Für den Fall, dass die Clustererstellung aufgrund eines Fehlers in einem Skript nicht erfolgreich ist, finden Sie die Protokolle im Clusterspeicherkonto.

* Die Speicherprotokolle stehen unter `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` zur Verfügung.

    ![Skriptaktionsprotokolle](./media/troubleshoot-script-action/script-action-logs-in-storage.png)

    In diesem Verzeichnis sind die Protokolle separat nach **Hauptknoten**, **Workerknoten** und **ZooKeeper-Knoten** strukturiert. Hierzu folgende Beispiele:

    * **Hauptknoten:** `<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Workerknoten:** `<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **ZooKeeper-Knoten:** `<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* Alle Ausgaben vom Typ **stdout** und **stderr** des entsprechenden Hosts werden in das Speicherkonto hochgeladen. Für die einzelnen Skriptaktionen sind jeweils die Dateien **output-\*.txt** und **errors-\*.txt** vorhanden. Die Datei **output-*.txt** enthält Informationen zum URI des Skripts, das auf dem Host ausgeführt wurde. Der folgende Text ist ein Beispiel für diese Informationen:

    ```output
    'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'
    ```

* Es kann sein, dass Sie wiederholt eine Skriptaktion mit demselben Namen erstellen. In diesem Fall können Sie die relevanten Protokolle anhand des **Datums** im Ordnernamen unterscheiden. Die Ordnerstruktur für einen an verschiedenen Tagen erstellten Cluster namens **mycluster** ähnelt beispielsweise den folgenden Protokolleinträgen:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Wenn Sie am gleichen Tag einen Skriptaktionscluster mit demselben Namen erstellen, können Sie die relevanten Protokolldateien anhand des eindeutigen Präfixes ermitteln.

* Wenn Sie gegen 24:00 Uhr (Mitternacht) einen Cluster erstellen, umfassen die Protokolldateien unter Umständen zwei Tage. In diesem Fall sehen Sie für den gleichen Cluster zwei Ordner mit unterschiedlichen Datumsangaben.

* Das Hochladen von Protokolldateien in den Standardcontainer kann insbesondere bei großen Clustern bis zu fünf Minuten dauern. Wenn Sie also auf die Protokolle zugreifen möchten, sollten Sie nicht sofort den Cluster löschen, falls eine Skriptaktion nicht erfolgreich war.

## <a name="ambari-watchdog"></a>Ambari-Watchdog

Lassen Sie das Kennwort für die Ambari-Watchdog-Komponente (hdinsightwatchdog) in Ihrem Linux-basierten HDInsight-Cluster unverändert. Durch das Ändern des Kennworts ist es nicht mehr möglich, im HDInsight-Cluster neue Skriptaktionen auszuführen.

## <a name="cant-import-name-blobservice"></a>„Cannot import name BlobService“ (Name BlobService kann nicht importiert werden)

__Symptome:__ Bei der Skriptaktion tritt ein Fehler auf. Eine Fehlermeldung ähnlich der folgenden wird angezeigt, wenn Sie den Vorgang in Ambari anzeigen:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Ursache__. Dieser Fehler tritt auf, wenn Sie den Python Azure Storage-Client aktualisieren, der im HDInsight-Cluster enthalten ist. HDInsight erwartet Azure Storage-Client 0.20.0.

__Lösung__. Stellen Sie zur Behebung dieses Fehlers mithilfe von `ssh` manuell eine Verbindung mit den einzelnen Clusterknoten her. Führen Sie den folgenden Befehl aus, um die korrekte Storage-Clientversion neu zu installieren:

```bash
sudo pip install azure-storage==0.20.0
```

Informationen zum Herstellen einer SSH-Verbindung mit dem Cluster finden Sie unter [Herstellen einer Verbindung mit HDInsight (Apache Hadoop) per SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>Verlauf zeigt die Skripts nicht an, die bei der Clustererstellung verwendet wurden

Wenn der Cluster vor dem 15. März 2016 erstellt wurde, wird im Verlauf der Skriptaktionen unter Umständen kein Eintrag angezeigt. Durch das Ändern der Größe des Clusters werden die Skripts im Skriptaktionsverlaufs angezeigt.

Hierfür gelten zwei Ausnahmen:

* Ihr Cluster wurde vor dem 1. September 2015 erstellt. Dies ist das Datum, an dem die Skriptaktionen eingeführt wurden. Für Cluster, die vor diesem Datum erstellt wurden, können also keine Skriptaktionen für die Clustererstellung verwendet worden sein.

* Sie haben bei der Clustererstellung mehrere Skriptaktionen verwendet. Oder: Sie haben für mehrere Skripts den gleichen Namen oder den gleichen Namen und URI, aber unterschiedliche Parameter verwendet. In diesen Fällen erhalten Sie eine Fehlermeldung wie die folgende:

    ```
    No new script actions can be run on this cluster because of conflicting script names in existing scripts. Script names provided at cluster creation must be all unique. Existing scripts are run on resize.
    ```

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]