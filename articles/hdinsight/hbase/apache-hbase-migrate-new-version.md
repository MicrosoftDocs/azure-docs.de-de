---
title: Migrieren eines HBase-Clusters zu einer neuen Version – Azure HDInsight
description: Hier erfahren Sie, wie Sie HBase-Cluster zu einer neuen Version migrieren.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: ashishth
ms.openlocfilehash: a152b815daeefa4c199af9b159eee8e5783971e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65143316"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Migrieren eines Apache HBase-Clusters zu einer neuen Version

In diesem Artikel werden die Schritte beschrieben, die für ein Update des Apache HBase-Clusters in Azure HDInsight auf eine neuere Version erforderlich sind.

> [!NOTE]  
> Die Downtime beim Upgrade beträgt in der Regel nur wenige Minuten. Sie ist auf das Leeren sämtlicher In-Memory-Daten und auf die Konfiguration und den Neustart der Dienste im neuen Cluster zurückzuführen. Die Ergebnisse variieren je nach Kontenanzahl, Datenmenge und anderen Variablen.

## <a name="review-apache-hbase-compatibility"></a>Überprüfen der Apache HBase-Kompatibilität

Vergewissern Sie sich vor dem Apache HBase-Upgrade, dass die HBase-Versionen im Quell- und Zielcluster kompatibel sind. Weitere Informationen finden Sie unter [Welche Apache Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](../hdinsight-component-versioning.md).

> [!NOTE]  
> Wir empfehlen dringend, die Kompatibilitätsmatrix im [HBase-Buch](https://hbase.apache.org/book.html#upgrading) zu prüfen.

Die folgende Tabelle zeigt das Beispiel einer Versionskompatibilitätsmatrix. Hierbei steht „Y“ für Kompatibilität und „N“ für eine potenzielle Inkompatibilität.

| Kompatibilitätstyp | Hauptversion| Nebenversion | Patch |
| --- | --- | --- | --- |
| Client-Server-Verbindungskompatibilität | N | J | J |
| Server-Server-Kompatibilität | N | J | J |
| Dateiformatkompatibilität | N | J | J |
| Client-API-Kompatibilität | N | J | J |
| Client-Binärkompatibilität | N | N | J |
| **Serverseitig eingeschränkte API-Kompatibilität** |  |  |  |
| Stable | N | J | J |
| In Entwicklung | N | N | J |
| Instabil | N | N | N |
| Abhängigkeitskompatibilität | N | J | J |
| Betriebskompatibilität | N | N | J |

> [!NOTE]  
> Kritische Inkompatibilitäten sollten in den Versionshinweisen für HBase-Version beschrieben sein.

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Upgraden mit der gleichen Apache HBase-Hauptversion

Führen Sie die folgenden Schritte aus, um für den Apache HBase-Cluster in Azure HDInsight ein Upgrade durchzuführen:

1. Vergewissern Sie sich anhand der HBase-Kompatibilitätsmatrix und der Versionshinweise, dass Ihre Anwendung mit der neuen Version kompatibel ist. Testen Sie Ihre Anwendung in einem Cluster mit der Zielversion von HDInsight und HBase.

2. [Richten Sie einen neuen HDInsight-Zielcluster ein](../hdinsight-hadoop-provision-linux-clusters.md), und verwenden Sie dabei das gleiche Speicherkonto, aber einen anderen Containernamen:

    ![Verwenden des gleichen Speicherkontos, aber mit einem anderen Container](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. Leeren Sie Ihren HBase-Quellcluster (den Cluster, den Sie aktualisieren möchten). HBase schreibt eingehende Daten in einen In-Memory-Speicher. Dieser wird als _Memstore_ bezeichnet. Wenn der Memstore eine bestimmte Größe erreicht, wird er von HBase zur langfristigen Speicherung im Speicherkonto des Clusters auf den Datenträger geschrieben. Wenn Sie den alten Cluster löschen, werden die Memstores recycelt, wodurch ggf. Daten verloren gehen. Führen Sie das folgende Skript aus, um den Memstore für jede Tabelle manuell auf den Datenträger zu schreiben. Die neueste Version dieses Skripts finden Sie auf [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh) für Azure.

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1 or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```
    
4. Beenden Sie die Erfassung im alten HBase-Cluster.
5. Führen Sie das vorherige Skript erneut aus, um sicherzustellen, dass alle aktuellen Daten im Memstore geleert wurden.
6. Melden Sie sich im alten Cluster (https://OLDCLUSTERNAME.azurehdidnsight.net) bei [Apache Ambari](https://ambari.apache.org/) an, und beenden Sie die HBase-Dienste. Wenn Sie zur Bestätigung aufgefordert werden, dass Sie die Dienste beenden möchten, aktivieren Sie das Kontrollkästchen, um den Wartungsmodus für HBase zu aktivieren. Weitere Informationen zur Verbindungsherstellung mit Ambari sowie zur Verwendung finden Sie unter [Verwalten von HDInsight-Clustern mithilfe der Ambari-Webbenutzeroberfläche](../hdinsight-hadoop-manage-ambari.md).

    ![Klicken Sie in Ambari unter „Dienstaktionen“ auf „Dienste > HBase > Beenden“.](./media/apache-hbase-migrate-new-version/stop-hbase-services.png)

    ![Aktivieren Sie das Kontrollkästchen zum Aktivieren des Wartungsmodus für HBase, und bestätigen Sie den Vorgang.](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. Melden Sie sich im neuen HDInsight-Cluster bei Ambari an. Ändern Sie die HDFS-Einstellung `fs.defaultFS` so, dass sie auf den Containernamen verweist, der im ursprünglichen Cluster verwendet wurde. Diese Einstellung befindet sich unter **HDFS > Configs > Advanced > Advanced core-site** (HDFS > Konfigurationen > Erweitert > core-site (erweitert)).

    ![Klicken Sie in Ambari auf „Dienste > HDFS > Konfigurationen > Erweitert“.](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![Ändern Sie den Containernamen in Ambari.](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. **Wenn Sie HBase-Cluster nicht mit der Funktion „Erweiterte Schreibvorgänge“ verwenden, überspringen Sie diesen Schritt. Dies wird nur für HBase-Cluster mit der Funktion „Erweiterte Schreibvorgänge“ benötigt.**
   
   Ändern Sie den Pfad `hbase.rootdir` so, dass er auf den Container des ursprünglichen Clusters verweist.

    ![Ändern Sie in Ambari den Containernamen für „hbase rootdir“.](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)
1. Führen Sie bei einem Upgrade von HDInsight 3.6 auf 4.0 die folgenden Schritte aus. Fahren Sie andernfalls mit Schritt 10 fort.
    1. Wählen Sie **Dienste** > **Alle erforderlichen Dienste neu starten** aus, um alle erforderlichen Dienste in Ambari neu zu starten.
    1. Beenden Sie den HBase-Dienst.
    1. Stellen Sie eine SSH-Verbindung mit dem ZooKeeper-Knoten her, und führen Sie den [ZkCli](https://github.com/go-zkcli/zkcli)-Befehl `rmr /hbase-unsecure` aus, um den Znode des HBase-Stammverzeichnisses aus ZooKeeper zu entfernen.
    1. Starten Sie HBase neu.
1. Wenn Sie ein Upgrade auf eine andere HDInsight-Version als 4.0 durchführen, gehen Sie wie folgt vor:
    1. Speichern Sie die Änderungen.
    1. Starten Sie alle erforderlichen Dienste neu, wie durch Ambari angegeben.
1. Richten Sie für Ihre Anwendung einen Verweis auf den neuen Cluster ein.

    > [!NOTE]  
    > Bei einem Upgrade ändert sich der statische DNS-Name für Ihre Anwendung. Sie können in den DNS-Einstellungen Ihres Domänennamens einen CNAME-Eintrag konfigurieren, der auf den Namen des Clusters verweist, anstatt einen hartcodierten DNS-Namen zu verwenden. Eine weitere Möglichkeit ist die Verwendung einer Konfigurationsdatei für Ihre Anwendung, die Sie ohne erneute Bereitstellung aktualisieren können.

12. Starten Sie die Erfassung, um zu prüfen, ob alles wie erwartet funktioniert.
13. Wenn der neue Cluster Ihren Vorstellungen entspricht, löschen Sie den ursprünglichen Cluster.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu [Apache HBase](https://hbase.apache.org/) und zum Upgraden von HDInsight-Clustern finden Sie in den folgenden Artikeln:

* [Aktualisieren eines HDInsight-Clusters auf eine neuere Version](../hdinsight-upgrade-cluster.md)
* [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-Webbenutzeroberfläche](../hdinsight-hadoop-manage-ambari.md)
* [Welche Apache Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](../hdinsight-component-versioning.md)
* [Optimieren von Konfigurationen mit Apache Ambari](../hdinsight-changing-configs-via-ambari.md#apache-hbase-optimization-with-the-ambari-web-ui)
