---
title: Hinzufügen zusätzlicher Azure-Speicherkonten zu HDInsight
description: Erfahren Sie, wie Sie einem vorhandenen HDInsight-Cluster zusätzliche Azure-Speicherkonten hinzufügen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: 51977c00dc8c9932def89d54ec1b6ec34afad652
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92541994"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Hinzufügen zusätzlicher Speicherkonten zu HDInsight

Erfahren Sie, wie Sie Skriptaktionen verwenden, um in HDInsight zusätzliche Azure Storage- *Konten* hinzuzufügen. Mit den Schritten in diesem Dokument fügen Sie einem vorhandenen HDInsight-Cluster ein Azure Storage- *Konto* hinzu. Dieser Artikel bezieht sich auf *Speicherkonten* und nicht auf das Standardclusterspeicherkonto oder weitere Speicher wie [`Azure Data Lake Storage Gen1`](hdinsight-hadoop-use-data-lake-storage-gen1.md) und [`Azure Data Lake Storage Gen2`](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> Die Informationen in diesem Dokument beziehen sich auf das Hinzufügen zusätzlicher Speicherkonten zu einem Cluster, nachdem dieser erstellt wurde. Informationen zum Hinzufügen von Speicherkonten während der Clustererstellung finden Sie unter [Einrichten von Clustern in HDInsight mit Apache Hadoop, Apache Spark, Apache Kafka und anderen](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Hadoop-Cluster in HDInsight. Weitere Informationen finden Sie unter [Erste Schritte mit HDInsight unter Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Speicherkontoname und -schlüssel. Weitere Informationen finden Sie unter [Verwalten von Speicherkonto-Zugriffsschlüsseln](../storage/common/storage-account-keys-manage.md).
* Bei Verwendung von PowerShell benötigen Sie das Az-Modul.  Siehe [Übersicht über Azure PowerShell](/powershell/azure/).

## <a name="how-it-works"></a>Funktionsweise

Während der Verarbeitung führt dieses Skript folgende Aktionen aus:

* Wenn das Speicherkonto bereits in der core-site.xml-Konfiguration für den Cluster vorhanden ist, wird das Skript beendet, und keine weiteren Aktionen werden ausgeführt.

* Es wird überprüft, ob das Speicherkonto vorhanden ist und mit dem Schlüssel darauf zugegriffen werden kann.

* Der Schlüssel wird mit den Clusteranmeldeinformationen verschlüsselt.

* Das Speicherkonto wird der core-site.xml-Datei hinzugefügt.

* Die Dienste Apache Oozie, Apache Hadoop YARN, Apache Hadoop MapReduce2 und Apache Hadoop HDFS werden beendet. Durch das Beenden und Starten dieser Dienste wird ihnen die Verwendung des neuen Speicherkontos ermöglicht.

> [!WARNING]  
> Die Verwendung eines Speicherkontos an einem anderen Ort als dem HDInsight-Cluster wird nicht unterstützt.

## <a name="add-storage-account"></a>Hinzufügen von Speicherkonten

Verwenden Sie eine [Skriptaktion](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster), um die Änderungen anzuwenden. Beachten Sie Folgendes:

|Eigenschaft | Wert |
|---|---|
|Bash-Skript-URI|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|Knotentyp(en)|Head|
|Parameter|`ACCOUNTNAME` `ACCOUNTKEY` `-p` (optional)|

* `ACCOUNTNAME` ist der Name des Speicherkontos, das dem HDInsight-Cluster hinzugefügt werden soll.
* `ACCOUNTKEY` ist der Zugriffsschlüssel für `ACCOUNTNAME`.
* `-p` ist optional. Bei Angabe wird der Schlüssel nicht verschlüsselt und im Klartext in der Datei „core-site.xml“ gespeichert.

## <a name="verification"></a>Überprüfung

Wenn Sie beim Anzeigen des HDInsight-Clusters im Azure-Portal die Option __Speicherkonten__ unter __Eigenschaften__ auswählen, werden Speicherkonten, die über diese Skriptaktion hinzugefügt wurden, nicht angezeigt. Azure PowerShell und die Azure-Befehlszeilenschnittstelle zeigen das zusätzliche Speicherkonto ebenfalls nicht an. Die Speicherinformationen werden nicht angezeigt, da vom Skript nur die `core-site.xml`-Konfiguration für den Cluster geändert wird. Diese Informationen werden nicht verwendet, wenn Sie die Clusterinformationen mit Azure-Verwaltungs-APIs abrufen.

Um den zusätzlichen Speicherplatz zu überprüfen, verwenden Sie eine der folgenden Methoden:

### <a name="powershell"></a>PowerShell

Das Skript gibt die Namen der dem angegebenen Cluster zugeordneten Speicherkonten zurück. Ersetzen Sie `CLUSTERNAME` durch den tatsächlichen Clusternamen, und führen Sie dann das Skript aus.

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. Navigieren Sie in einem Webbrowser zu `https://CLUSTERNAME.azurehdinsight.net`, wobei `CLUSTERNAME` der Name Ihres Clusters ist.

1. Navigieren Sie zu **HDFS** > **Configs** > **Advanced** > **Custom core-site** (HDFS > Konfigurationen > Erweitert > Benutzerdefinierte core-site).

1. Beachten Sie die Schlüssel, die mit `fs.azure.account.key` beginnen. Der Kontoname ist Teil des Schlüssels, wie in diesem Beispielbild veranschaulicht:

   ![Überprüfung durch Apache Ambari](./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png)

## <a name="remove-storage-account"></a>Entfernen des Speicherkontos

1. Navigieren Sie in einem Webbrowser zu `https://CLUSTERNAME.azurehdinsight.net`, wobei `CLUSTERNAME` der Name Ihres Clusters ist.

1. Navigieren Sie zu **HDFS** > **Configs** > **Advanced** > **Custom core-site** (HDFS > Konfigurationen > Erweitert > Benutzerdefinierte core-site).

1. Entfernen Sie die folgenden Schlüssel:
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

Nachdem Sie diese Schlüssel entfernt und die Konfiguration gespeichert haben, müssen Sie Oozie, Yarn, MapReduce2, HDFS und Hive nacheinander neu starten.

## <a name="known-issues"></a>Bekannte Probleme

### <a name="storage-firewall"></a>Speicherfirewall

Denken Sie daran, die Ausnahme **Vertrauenswürdige Microsoft-Dienste zulassen** zu aktivieren, damit HDInsight auf Ihr Speicherkonto zugreifen kann, wenn Sie Ihr Speicherkonto mit den **Firewalls und virtuelle Netzwerke** -Einschränkungen für **Ausgewählte Netzwerke** schützen.

### <a name="unable-to-access-storage-after-changing-key"></a>Zugriff auf den Speicher ist nach dem Ändern des Schlüssels nicht möglich

Wenn Sie den Schlüssel für ein Speicherkonto ändern, kann HDInsight nicht mehr auf das Speicherkonto zugreifen. HDInsight verwendet eine zwischengespeicherte Kopie des Schlüssels in der Datei „core-site.xml“ für den Cluster. Diese zwischengespeicherte Kopie muss mit dem neuen Schlüssel aktualisiert werden.

Das wiederholte Ausführen der Skriptaktion aktualisiert den Schlüssel **nicht** , da das Skript überprüft, ob bereits ein Eintrag für das Speicherkonto vorhanden ist. Wenn bereits ein Eintrag vorhanden ist, werden keine Änderungen vorgenommen.

So umgehen Sie dieses Problem:  
1. Entfernen Sie das Speicherkonto.
1. Fügen Sie das Speicherkonto hinzu.

> [!IMPORTANT]  
> Das Rotieren des Speicherschlüssels für das primäre Speicherkonto, das an einen Cluster gekoppelt ist, wird nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Sie haben gelernt, wie Sie einem vorhandenen HDInsight-Cluster zusätzliche Speicherkonten hinzufügen. Weitere Informationen zu Skriptaktionen finden Sie unter [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).