---
title: Überwachen und Verwalten von Hadoop mit der Ambari-REST-API – Azure HDInsight
description: Erfahren Sie, wie Sie Ambari zum Überwachen und Verwalten von Hadoop-Clustern in Azure HDInsight verwenden. In diesem Dokument erfahren Sie, wie Sie die in HDInsight-Clustern enthaltene Ambari-REST-API verwenden.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 6ba1d1e15b1dbb3efb24219b6c09a6827e701d46
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546074"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-REST-API

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Erfahren Sie, wie Sie die Apache Ambari-REST-API zum Verwalten und Überwachen von Apache Hadoop-Clustern in Azure HDInsight verwenden.

## <a name="what-is-apache-ambari"></a>Was ist Apache Ambari?

Apache Ambari vereinfacht die Verwaltung und Überwachung von Hadoop-Clustern durch die Bereitstellung einer benutzerfreundlichen Webbenutzeroberfläche, die auf den zugehörigen [REST-APIs](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md) basiert.  Ambari wird standardmäßig mit Linux-basierten Clustern bereitgestellt.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Hadoop-Cluster in HDInsight. Weitere Informationen finden Sie unter [Erste Schritte mit HDInsight unter Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* Bash unter Ubuntu unter Windows 10.  In den Beispielen dieses Artikels wird die Bash-Shell unter Windows 10 verwendet. Die Installationsschritte finden Sie unter [Windows Subsystem for Linux Installation Guide for Windows 10](/windows/wsl/install-win10) (Windows-Subsystem für Linux: Installationshandbuch für Windows 10).  Es funktionieren auch andere [Unix-Shells](https://www.gnu.org/software/bash/).  Die Beispiele können mit einigen geringfügigen Änderungen auch an einer Windows-Eingabeaufforderung verwendet werden.  Oder Sie können Windows PowerShell verwenden.

* jq, ein JSON-Befehlszeilenprozessor.  Siehe [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Windows PowerShell.  Oder Sie können Bash verwenden.

## <a name="base-uniform-resource-identifier-for-ambari-rest-api"></a>Basis-URI für die Ambari-REST-API

 Der Basis-URI (Uniform Resource Identifier) für die Ambari-REST-API unter HDInsight lautet `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, wobei `CLUSTERNAME` der Name Ihres Clusters ist.  Bei Clusternamen in URIs wird die **Groß-/Kleinschreibung** beachtet.  Beim Clusternamen im vollqualifizierten Domänennamen (FQDN) innerhalb des URI (`CLUSTERNAME.azurehdinsight.net`) wird die Groß-/Kleinschreibung nicht beachtet, bei anderen Vorkommen im URI dagegen schon.

## <a name="authentication"></a>Authentifizierung

Zum Herstellen einer Verbindung mit Ambari in HDInsight ist HTTPS erforderlich. Verwenden Sie den Administratorkontonamen (Standardname: **Administrator** ) und das Kennwort, die Sie während der Clustererstellung bereitgestellt haben.

Verwenden Sie für Enterprise-Sicherheitspaketcluster anstelle von `admin` einen vollqualifizierten Benutzernamen wie `username@domain.onmicrosoft.com`.

## <a name="examples"></a>Beispiele

### <a name="setup-preserve-credentials"></a>Setup (Anmeldeinformationen speichern)

Speichern Sie Ihre Anmeldeinformationen, um zu vermeiden, dass Sie sie für jedes Beispiel neu eingeben müssen.  Der Clustername wird in einem separaten Schritt gespeichert.

**A. Bash**  
Bearbeiten Sie das unten angegebene Skript, indem Sie `PASSWORD` durch Ihr Kennwort ersetzen.  Geben Sie anschließend den Befehl ein.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identifizieren von Clusternamen mit richtiger Groß-/Kleinschreibung

Die tatsächliche Schreibweise des Clusternamens kann anders sein als erwartet.  Mit den hier angegebenen Schritten wird die tatsächliche Schreibweise angezeigt und dann für alle späteren Beispiele in einer Variable gespeichert.

Bearbeiten Sie die unten angegebenen Skripts, um `CLUSTERNAME` durch den Namen Ihres Clusters zu ersetzen. Geben Sie anschließend den Befehl ein. (Beim Clusternamen für den FQDN wird die Groß-/Kleinschreibung nicht beachtet.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

### <a name="parsing-json-data"></a>Analyse von JSON-Daten

Im folgenden Beispiel wird [jq](https://stedolan.github.io/jq/) oder [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json) verwendet, um das JSON-Antwortdokument zu analysieren und nur die `health_report`-Informationen aus den Ergebnissen anzuzeigen.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" \
| jq '.Clusters.health_report'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

### <a name="get-the-fqdn-of-cluster-nodes"></a>Abrufen des FQDN von Clusterknoten

Sie müssen möglicherweise den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) eines Clusterknotens kennen. Sie können den FQDN für die verschiedenen Knoten im Cluster einfach wie folgt abrufen:

**Alle Knoten**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**Hauptknoten**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Workerknoten**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" \
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Zookeeper-Knoten**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq -r ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="get-the-internal-ip-address-of-cluster-nodes"></a>Abrufen der internen IP-Adresse der Clusterknoten

Auf die von den Beispielen in diesem Abschnitt zurückgegebenen IP-Adressen kann nicht direkt über das Internet zugegriffen werden. Auf sie kann nur innerhalb des virtuellen Azure-Netzwerks, das den HDInsight-Cluster enthält, zugegriffen werden.

Weitere Informationen zur Verwendung von HDInsight und virtuellen Netzwerken finden Sie unter [Plan a virtual network for Azure HDInsight](hdinsight-plan-virtual-network-deployment.md) (Planen eines virtuellen Netzwerks für Azure HDInsight).

Zum Ermitteln der IP-Adresse muss Ihnen der interne vollqualifizierte Domänenname (Fully Qualified Domain Name, FQDN) der Clusterknoten bekannt sein. Sobald Sie den FQDN kennen, können Sie die IP-Adresse des Hosts abrufen. Die folgenden Beispiele fragen Ambari zuerst nach dem FQDN aller Hostknoten ab. Danach fragt Ambari die IP-Adresse der einzelnen Hosts ab.

```bash
for HOSTNAME in $(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```  

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" 
$resp = Invoke-WebRequest -Uri $uri -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds -UseBasicParsing
    $hostInfoObj = ConvertFrom-Json $hostInfoResp
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

### <a name="get-the-default-storage"></a>Abrufen des Standardspeicher

HDInsight-Cluster müssen ein Azure Storage-Konto oder Data Lake Storage als Standardspeicher verwenden. Sie können diese Informationen mit Ambari abrufen, nachdem der Cluster erstellt wurde. Sie möchten z.B. Daten außerhalb HDInsight aus dem Container lesen bzw. in ihn hineinschreiben.

Die folgenden Beispiele rufen die Standardspeicherkonfiguration aus dem Cluster ab:

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq -r '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> Diese Beispiele geben die erste auf den Server angewendete Konfiguration (`service_config_version=1`) zurück, die diese Informationen enthält. Wenn Sie einen Wert abrufen, der nach der Erstellung des Clusters geändert wurde, müssen Sie möglicherweise die Konfigurationsversionen auflisten und die neueste Version abrufen.

Der Rückgabewert ähnelt einem der folgenden Beispiele:

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net` – dieser Wert gibt an, dass der Cluster ein Azure Storage-Konto als Standardspeicher verwendet. Der Wert `ACCOUNTNAME` ist der Name des Speicherkontos. Der Teil `CONTAINER` ist der Name des Blobcontainers im Speicherkonto. Der Container ist der Stamm des HDFS-kompatiblen Speichers für den Cluster.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net`: Dieser Wert gibt an, dass der Cluster Azure Data Lake Storage Gen2 als Standardspeicher verwendet. Die Werte `ACCOUNTNAME` und `CONTAINER` haben die gleichen Bedeutung wie für Azure Storage (siehe oben).

* `adl://home`: Dieser Wert gibt an, dass der Cluster Azure Data Lake Storage Gen1 als Standardspeicher verwendet.

    Um den Data Lake Storage-Kontonamen zu ermitteln, verwenden Sie die folgenden Beispiele:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    Der Rückgabewert ähnelt `ACCOUNTNAME.azuredatalakestore.net`, wobei `ACCOUNTNAME` der Name des Data Lake Storage-Kontos ist.

    Um das Verzeichnis in der Data Lake Storage-Instanz zu suchen, die den Speicher für den Cluster enthält, verwenden Sie die folgenden Beispiele:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    Der Rückgabewert ähnelt `/clusters/CLUSTERNAME/`. Dieser Wert ist ein Pfad im Data Lake Storage-Konto. Dieser Pfad ist der Stamm des HDFS-kompatiblen Dateisystems für den Cluster.  

> [!NOTE]  
> Das Cmdlet [Get-AzHDInsightCluster](/powershell/module/az.hdinsight/get-azhdinsightcluster) von [Azure PowerShell](/powershell/azure/) gibt auch die Speicherinformationen für den Cluster zurück.

### <a name="get-all-configurations"></a>Abrufen aller Konfigurationen

Rufen Sie die verfügbaren Konfigurationen für Ihren Cluster ab.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

In diesem Beispiel wird ein JSON-Dokument zurückgegeben, das die aktuelle Konfiguration für installierte Komponenten enthält. Siehe den Wert *tag* . Das folgende Beispiel ist ein Datenauszug aus der Rückgabe eines Spark-Clustertyps:

```json
"jupyter-site" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-client-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
```

### <a name="get-configuration-for-specific-component"></a>Abrufen der Konfiguration für eine bestimmte Komponente

Rufen Sie die Konfiguration für die Komponente ab, die Sie interessiert. Ersetzen Sie im folgenden Beispiel `INITIAL` mit dem Tagwert, der von der vorherigen Anforderung zurückgegeben wurde.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

In diesem Beispiel wird ein JSON-Dokument zurückgegeben, das die aktuelle Konfiguration für die `livy2-conf`-Komponente enthält.

### <a name="update-configuration"></a>Aktualisieren der Konfiguration

1. Erstellen Sie `newconfig.json`.  
   Ändern Sie die unten angegebenen Befehle, und geben Sie sie dann ein:

   * Ersetzen Sie `livy2-conf` durch die neue Komponente.
   * Ersetzen Sie `INITIAL` durch den tatsächlichen Wert, der für `tag` unter [Abrufen aller Konfigurationen](#get-all-configurations) abgerufen wurde.

     **A. Bash**

     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     **B. PowerShell**  
     Für das PowerShell-Skript wird [jq](https://stedolan.github.io/jq/) verwendet.  Bearbeiten Sie unten `C:\HD\jq\jq-win64`, um Ihren Pfad und die Version von[jq](https://stedolan.github.io/jq/) anzugeben.

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     Jq wird verwendet, um die aus HDInsight abgerufenen Daten in eine neue Konfigurationsvorlage umzuwandeln. Im Einzelnen führen diese Beispiele folgende Aktionen aus:

   * Es wird ein eindeutiger Wert erstellt, der die Zeichenfolge „version“ sowie das Datum enthält und in `newtag` gespeichert wird.

   * Ein Stammdokument für die neue Konfiguration wird erstellt.

   * Der Inhalt des `.items[]`-Arrays wird abgerufen und unter dem **desired_config** -Element hinzugefügt.

   * Die Elemente `href`, `version` und `Config` werden gelöscht, da sie zum Übermitteln einer neuen Konfiguration nicht benötigt werden.

   * Ein `tag`-Element mit dem Wert `version#################` wird hinzugefügt. Der numerische Teil basiert auf dem aktuellen Datum. Jede Konfiguration muss über ein eindeutiges Tag verfügen.

     Schließlich werden die Daten im Dokument `newconfig.json` gespeichert. Die Dokumentstruktur sollte in etwa wie folgt aussehen:

     ```json
     {
       "Clusters": {
         "desired_config": {
           "tag": "version1552064778014",
           "type": "livy2-conf",
           "properties": {
             "livy.environment": "production",
             "livy.impersonation.enabled": "true",
             "livy.repl.enableHiveContext": "true",
             "livy.server.csrf_protection.enabled": "true",
               ....
           },
         },
       }
     }
     ```

2. Bearbeiten Sie `newconfig.json`.  
   Öffnen Sie das Dokument `newconfig.json` und ändern Sie Werte im `properties`-Objekt, bzw. fügen Sie sie hinzu. Im folgenden Beispiel wird der Wert von `"livy.server.csrf_protection.enabled"` von `"true"` in `"false"` geändert.

    ```json
    "livy.server.csrf_protection.enabled": "false",
    ```

    Speichern Sie die Datei, nachdem Sie alle Änderungen vorgenommen haben.

3. Übermitteln Sie `newconfig.json`.  
   Verwenden Sie die folgenden Befehle, um die aktualisierte Konfiguration an Ambari zu übermitteln.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```  

    Diese Befehle übermitteln den Inhalt der Datei **newconfig.json** als neue Konfiguration an den Cluster. Die Anforderung gibt ein JSON-Dokument zurück. Das **versionTag** -Element in diesem Dokument sollte mit der von Ihnen übermittelten Version übereinstimmen, und das Objekt **configs** enthält die Konfigurationsänderungen, die Sie angefordert haben.

### <a name="restart-a-service-component"></a>Neustarten einer Dienstkomponente

An diesem Punkt zeigt die Ambari-Webbenutzeroberfläche an, dass der Spark-Dienst neu gestartet werden muss, damit die neue Konfiguration wirksam wird. Führen Sie die unten angegebenen Schritte aus, um den Dienst neu zu starten.

1. Verwenden Sie Folgendes, um den Wartungsmodus für den Spark2-Dienst zu aktivieren:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    ```

2. Überprüfen des Wartungsmodus  

    Diese Befehle senden ein JSON-Dokument an den Server, der den Wartungsmodus aktiviert. Mit der folgenden Anforderung können Sie überprüfen, ob sich der Dienst nun im Wartungsmodus befindet:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```

    Der Rückgabewert ist `ON`.

3. Deaktivieren Sie als Nächstes den Spark2-Dienst wie folgt:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```

    Die Antwort ähnelt dem folgenden Beispiel:

    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```

    > [!IMPORTANT]  
    > Im Wert `href`, der von diesem URI zurückgegeben wird, wird die interne IP-Adresse des Clusterknotens verwendet. Ersetzen Sie für die Nutzung von außerhalb des Clusters den Teil `10.0.0.18:8080` durch den FQDN des Clusters.  

4. Überprüfen Sie die Anforderung.  
    Bearbeiten Sie den unten angegebenen Befehl, indem Sie `29` durch den tatsächlichen Wert für `id` ersetzen, der im vorherigen Schritt zurückgegeben wurde.  Die folgenden Befehle rufen den Status der Anforderung ab:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    Die Antwort `COMPLETED` gibt an, dass die Anforderung abgeschlossen ist.

5. Verwenden Sie Folgendes, um den Spark2-Dienst zu starten, nachdem die vorherige Anforderung abgeschlossen wurde:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    $resp.Content
    ```

    Der Dienst verwendet jetzt die neue Konfiguration.

6. Verwenden Sie abschließend Folgendes, um den Wartungsmodus zu deaktivieren:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>Nächste Schritte

Eine vollständige Referenz der REST-API finden Sie unter [Referenz zur Apache Ambari-API V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Weitere Informationen finden Sie unter [Autorisieren von Benutzern für Apache Ambari-Ansichten](./hdinsight-authorize-users-to-ambari.md)