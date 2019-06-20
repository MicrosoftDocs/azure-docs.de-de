---
title: Hinzufügen zusätzlicher Azure-Speicherkonten zu HDInsight
description: Erfahren Sie, wie Sie einem vorhandenen HDInsight-Cluster zusätzliche Azure-Speicherkonten hinzufügen.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: hrasheed
ms.openlocfilehash: 6b9577bcf8b527abb0cb7b8720ed83ec8321655b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64724465"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Hinzufügen zusätzlicher Speicherkonten zu HDInsight

Erfahren Sie, wie Sie Skriptaktionen verwenden, um in HDInsight zusätzliche Azure Storage-*Konten* hinzuzufügen. Mit den Schritten in diesem Dokument fügen Sie einem vorhandenen Linux-basierten HDInsight-Cluster ein Storage-*Konto* hinzu. Dieser Artikel bezieht sich auf Storage-*Konten* (nicht auf das standardmäßige Clusterspeicherkonto) sowie auf nicht zusätzlichen Speicher wie z. B. [Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) und [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> Die Informationen in diesem Dokument beziehen sich auf das Hinzufügen zusätzlichen Speichers zu einem Cluster, nachdem es erstellt wurde. Informationen zum Hinzufügen von Speicherkonten während der Clustererstellung finden Sie unter [Einrichten von Clustern in HDInsight mit Apache Hadoop, Apache Spark, Apache Kafka und anderen](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Hadoop-Cluster in HDInsight. Weitere Informationen finden Sie unter [Erste Schritte mit HDInsight unter Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Speicherkontoname und -schlüssel. Siehe [Verwalten von Speicherkontoeinstellungen im Azure-Portal](../storage/common/storage-account-manage.md).
* [Clustername mit korrekter Groß-/Kleinschreibung](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name)
* Bei Verwendung von PowerShell benötigen Sie das Az-Modul.  Siehe [Übersicht über Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* Wenn Sie die Azure-Befehlszeilenschnittstelle (Azure CLI) nicht installiert haben, finden Sie alle erforderlichen Informationen unter [Azure-Befehlszeilenschnittstelle (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).
* Bei Verwendung der Bash oder einer Windows-Eingabeaufforderung benötigen Sie außerdem den JSON-Befehlszeilenprozessor **jq**.  Siehe [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/). Informationen für Bash unter Ubuntu oder Windows 10 finden Sie unter [Windows Subsystem for Linux Installation Guide for Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) (Windows-Subsystem für Linux: Installationshandbuch für Windows 10).

## <a name="how-it-works"></a>So funktioniert's

Diesem Skript werden die folgenden Parameter übergeben:

* __Azure-Speicherkontoname__: Der Name des Speicherkontos, das dem HDInsight-Cluster hinzugefügt werden soll. Nach dem Ausführen des Skripts kann HDInsight Daten lesen und schreiben, die in diesem Speicherkonto gespeichert werden.

* __Azure-Speicherkontoschlüssel__: Ein Schlüssel, der Zugriff auf das Speicherkonto gewährt.

* __-p__ (optional): Bei Angabe wird der Schlüssel nicht verschlüsselt und wird in der core-site.xml-Datei als Nur-Text gespeichert.

Während der Verarbeitung führt dieses Skript folgende Aktionen aus:

* Wenn das Speicherkonto bereits in der core-site.xml-Konfiguration für den Cluster vorhanden ist, wird das Skript beendet, und keine weiteren Aktionen werden ausgeführt.

* Es wird überprüft, ob das Speicherkonto vorhanden ist und mit dem Schlüssel darauf zugegriffen werden kann.

* Der Schlüssel wird mit den Clusteranmeldeinformationen verschlüsselt.

* Das Speicherkonto wird der core-site.xml-Datei hinzugefügt.

* Beendet die Dienste [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) und [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) und startet sie neu. Durch das Beenden und Starten dieser Dienste wird ihnen die Verwendung des neuen Speicherkontos ermöglicht.

> [!WARNING]  
> Die Verwendung eines Speicherkontos an einem anderen Ort als dem HDInsight-Cluster wird nicht unterstützt.

## <a name="the-script"></a>Das Skript

__Skriptspeicherort__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Anforderungen__:  Das Skript muss auf die __Hauptknoten__ angewendet werden. Sie müssen dieses Skript nicht als __Persistent__ markieren, da es die Ambari-Konfiguration für den Cluster direkt aktualisiert.

## <a name="to-use-the-script"></a>So verwenden Sie das Skript

Das Skript kann über Azure PowerShell, die Azure-Befehlszeilenschnittstelle oder das Azure-Portal verwendet werden.

### <a name="powershell"></a>PowerShell

Verwenden Sie [Submit-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction). Ersetzen Sie `CLUSTERNAME`, `ACCOUNTNAME` und `ACCOUNTKEY` durch die entsprechenden Werte.

```powershell
# Update these parameters
$clusterName = "CLUSTERNAME"
$parameters = "ACCOUNTNAME ACCOUNTKEY"

$scriptActionName = "addStorage"
$scriptActionUri = "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh"

# Execute script
Submit-AzHDInsightScriptAction `
    -ClusterName $clusterName `
    -Name $scriptActionName `
    -Uri $scriptActionUri `
    -NodeTypes "headnode" `
    -Parameters $parameters
```

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Verwenden Sie [az hdinsight script-action execute](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute).  Ersetzen Sie `CLUSTERNAME`, `RESOURCEGROUP`, `ACCOUNTNAME` und `ACCOUNTKEY` durch die entsprechenden Werte.

```cli
az hdinsight script-action execute ^
    --name CLUSTERNAME ^
    --resource-group RESOURCEGROUP ^
    --roles headnode ^
    --script-action-name addStorage ^
    --script-uri "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh" ^
    --script-parameters "ACCOUNTNAME ACCOUNTKEY"
```

### <a name="azure-portal"></a>Azure-Portal

Siehe [Anwenden einer Skriptaktion auf einen ausgeführten Cluster](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

## <a name="known-issues"></a>Bekannte Probleme

### <a name="storage-firewall"></a>Speicherfirewall

Wenn Sie sich dazu entscheiden, Ihr Speicherkonto mit den **Firewalls und virtuelle Netzwerke**-Einschränkungen auf **Ausgewählte Netzwerke** zu schützen, denken Sie daran, die Ausnahme **Vertrauenswürdige Microsoft-Dienste zulassen…** zu aktivieren, damit HDInsight auf Ihr Speicherkonto zugreifen kann.

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Speicherkonten werden im Azure-Portal oder in Tools nicht angezeigt

Wenn Sie beim Anzeigen des HDInsight-Clusters im Azure-Portal den Eintrag __Speicherkonten__ unter __Eigenschaften__ auswählen, werden keine Speicherkonten angezeigt, die über diese Skriptaktion hinzugefügt wurden. Azure PowerShell und Azure CLI zeigen das zusätzliche Speicherkonto ebenfalls nicht an.

Die Speicherinformationen werden nicht angezeigt, da das Skript nur die core-site.xml-Konfiguration für den Cluster ändert. Diese Informationen werden nicht verwendet, wenn Sie die Clusterinformationen mit Azure-Verwaltungs-APIs abrufen.

Verwenden Sie die Ambari-REST-API, um Speicherkontoinformationen anzuzeigen, die dem Cluster mit diesem Skript hinzugefügt wurden. Verwenden Sie die folgenden Befehle, um diese Informationen für Ihren Cluster abzurufen:

### <a name="powershell"></a>PowerShell

Ersetzen Sie `CLUSTERNAME` durch den Clusternamen mit korrekter Groß-/Kleinschreibung. Geben Sie den folgenden Befehl ein, um zunächst die verwendete Konfigurationsversion des Diensts zu identifizieren:

```powershell
# getting service_config_version
$clusterName = "CLUSTERNAME"

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version
```

Ersetzen Sie `ACCOUNTNAME` durch die tatsächlichen Namen. Ersetzen Sie dann `4` durch die tatsächliche Konfigurationsversion des Diensts, und geben Sie den Befehl ein. Geben Sie bei entsprechender Aufforderung das Kennwort für die Clusteranmeldung ein.

```powershell
# Update values
$accountName = "ACCOUNTNAME"
$version = 4

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$version" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>Bash
Ersetzen Sie `myCluster` durch den Clusternamen mit korrekter Groß-/Kleinschreibung.

```bash
export CLUSTERNAME='myCluster'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

Ersetzen Sie `myAccount` durch den tatsächlichen Namen des Speicherkontos. Ersetzen Sie `4` dann durch die tatsächliche Konfigurationsversion des Diensts, und geben Sie den Befehl ein:

```bash
export ACCOUNTNAME='"fs.azure.account.key.myAccount.blob.core.windows.net"'
export VERSION='4'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=$VERSION" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd

Ersetzen Sie `CLUSTERNAME` in beiden Skripts durch den Clusternamen mit korrekter Groß-/Kleinschreibung. Geben Sie den folgenden Befehl ein, um zunächst die verwendete Konfigurationsversion des Diensts zu identifizieren:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

Ersetzen Sie `ACCOUNTNAME` durch den tatsächlichen Namen des Speicherkontos. Ersetzen Sie `4` dann durch die tatsächliche Konfigurationsversion des Diensts, und geben Sie den Befehl ein:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=4" | ^
jq-win64 ".items[].configurations[].properties["""fs.azure.account.key.ACCOUNTNAME.blob.core.windows.net"""] | select(. != null)"
```
---

 Die Ausgabe dieses Befehls entspricht in etwa dem folgenden Text:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Dieser Text ist ein Beispiel für einen verschlüsselten Schlüssel, der zum Zugriff auf das Speicherkonto verwendet wird.

### <a name="unable-to-access-storage-after-changing-key"></a>Zugriff auf den Speicher ist nach dem Ändern des Schlüssels nicht möglich

Wenn Sie den Schlüssel für ein Speicherkonto ändern, kann HDInsight nicht mehr auf das Speicherkonto zugreifen. HDInsight verwendet eine zwischengespeicherte Kopie des Schlüssels in der Datei „core-site.xml“ für den Cluster. Diese zwischengespeicherte Kopie muss mit dem neuen Schlüssel aktualisiert werden.

Das erneute Ausführen der Skriptaktion aktualisiert den Schlüssel __nicht__, da das Skript überprüft, ob bereits ein Eintrag für das Speicherkonto vorhanden ist. Wenn bereits ein Eintrag vorhanden ist, werden keine Änderungen vorgenommen.

Um dieses Problem zu umgehen, müssen Sie den vorhandenen Eintrag für das Speicherkonto entfernen. Führen Sie die folgenden Schritte aus, um den vorhandenen Eintrag zu entfernen:

1. Öffnen Sie in einem Webbrowser die Ambari-Webbenutzeroberfläche für Ihren HDInsight-Cluster. Der URI ist `https://CLUSTERNAME.azurehdinsight.net`. Ersetzen Sie `CLUSTERNAME` durch den Namen Ihres Clusters.

    Geben Sie bei der entsprechenden Aufforderung den HTTP-Anmeldenamen und das Kennwort für den Cluster ein.

2. Wählen Sie aus der Liste der Dienste auf der linken Seite den Dienst __HDFS__ aus. Wählen Sie in der Mitte der Seite die Registerkarte __Configs__ aus.

3. Geben Sie im Feld __Filter...__ den Wert __fs.azure.account__ ein. Damit werden Einträge für alle zusätzlichen Speicherkonten zurückgegeben, die dem Cluster hinzugefügt wurden. Es gibt zwei Arten von Einträgen: __keyprovider__ und __key__. Beide enthalten den Namen des Speicherkontos als Teil des Schlüsselnamens.

    Im Folgenden sehen Sie Beispieleinträge für ein Speicherkonto namens __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Nachdem Sie die Schlüssel für das Speicherkonto identifiziert haben, das Sie entfernen müssen, verwenden Sie das rote "-"-Symbol rechts neben den Eintrag, um es zu löschen. Klicken Sie dann auf die Schaltfläche __Speichern__, um die Änderungen zu speichern.

5. Nachdem die Änderungen gespeichert wurden, fügen Sie das Speicherkonto und den neuen Schlüsselwert mit der Skriptaktion dem Cluster hinzu.

### <a name="poor-performance"></a>Schlechte Leistung

Wenn das Speicherkonto sich in einer anderen Region als der HDInsight-Cluster befindet, könnten Sie eine schlechte Leistung feststellen. Der Zugriff auf Daten in einer anderen Region ist mit Netzwerkdatenverkehr außerhalb des regionalen Azure-Rechenzentrums und über das öffentliche Internet verbunden, was zu Latenz führen kann.

### <a name="additional-charges"></a>Zusätzliche Gebühren

Wenn das Speicherkonto sich in einer anderen Region als der HDInsight-Cluster befindet, fallen Ihnen möglicherweise zusätzliche Ausgangsgebühren in Ihrer Azure-Abrechnung auf. Eine Ausgangsgebühr wird fällig, wenn Daten ein regionales Rechenzentrum verlassen. Diese Gebühr fällt auch dann an, wenn das Ziel des Datenverkehrs ein anderes Azure-Rechenzentrum in einer anderen Region ist.

## <a name="next-steps"></a>Nächste Schritte

Sie haben gelernt, wie Sie einem vorhandenen HDInsight-Cluster zusätzliche Speicherkonten hinzufügen. Weitere Informationen zu Skriptaktionen finden Sie unter [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).
