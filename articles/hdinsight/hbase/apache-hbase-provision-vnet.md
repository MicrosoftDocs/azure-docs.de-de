---
title: Erstellen von HBase-Clustern in einem virtuellen Netzwerk – Azure
description: Erste Schritte mit HBase in Azure HDInsight. Erfahren Sie, wie Sie HDInsight HBase-Cluster in Azure Virtual Network erstellen können.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: 85eaa81a0cfd7ccfe8ad3ae818f89966280d279e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64730364"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Erstellen von Apache HBase-Clustern in HDInsight in Azure Virtual Network
Hier erfahren Sie, wie Sie Apache HBase-Cluster in Azure HDInsight in einem [virtuellen Azure-Netzwerk][1] erstellen.

Mit der Integration in virtuelle Netzwerke können Apache HBase-Cluster im selben virtuellen Netzwerk bereitgestellt werden wie Ihre Anwendungen, sodass Anwendungen direkt mit HBase kommunizieren können. Es ergeben sich folgende Vorteile:

* Direkte Konnektivität der Webanwendung mit den Knoten des HBase-Clusters, wodurch die Kommunikation über HBase Java-RPC-APIs (Remoteprozeduraufrufe) ermöglicht wird.
* Optimierte Leistung, da Datenverkehr nicht über mehrere Gateways und Lastenausgleichsmodule fließt.
* Möglichkeit einer höheren Sicherheit bei der Verarbeitung sensibler Informationen ohne exponierten öffentlichen Endpunkt.

### <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit diesem Tutorial beginnen können, benötigen Sie Folgendes:

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Install and use Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)(Installieren und Verwenden von Azure PowerShell, in englischer Sprache).

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Erstellen von Apache HBase-Clustern in einem virtuellen Netzwerk
In diesem Abschnitt erstellen Sie mit einer [Azure Resource Manager-Vorlage](../../azure-resource-manager/resource-group-template-deploy.md) einen Linux-basierten Apache HBase-Cluster mit dem abhängigen Azure Storage-Konto in einem virtuellen Azure-Netzwerk. Andere Methoden zur Erstellung von Clustern und Informationen zu den Einstellungen finden Sie unter [Erstellen von Linux-basierten Hadoop-Clustern in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Weitere Informationen zur Verwendung einer Vorlage zum Erstellen von Apache Hadoop-Clustern in HDInsight finden Sie unter [Erstellen Linux-basierter Apache Hadoop-Cluster in HDInsight mithilfe von Azure Resource Manager-Vorlagen](../hdinsight-hadoop-create-linux-clusters-arm-templates.md).

> [!NOTE]  
> Einige Eigenschaften sind in der Vorlage hartcodiert. Beispiel:
>
> * **Standort:** USA (Ost) 2
> * **Clusterversion:** 3.6
> * **Anzahl der Workerknoten im Cluster:** 2
> * **Standard-Speicherkonto**: eine eindeutige Zeichenfolge
> * **Name des virtuellen Netzwerks:** &lt;Clustername>-vnet
> * **Adressraum des virtuellen Netzwerks:** 10.0.0.0/16
> * **Subnetzname**: subnet1
> * **Subnetzadressbereich:** 10.0.0.0/24
>
> &lt;Clustername&gt; wird durch den Clusternamen ersetzt, den Sie bei Verwendung der Vorlage angeben.
>
>

1. Klicken Sie auf die folgende Abbildung, um die Vorlage im Azure-Portal zu öffnen: Die Vorlage finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Geben Sie auf dem Blatt **Benutzerdefinierte Bereitstellung** die folgenden Eigenschaften ein:

   * **Abonnement**: Wählen Sie ein Azure-Abonnement aus, um den HDInsight-Cluster, das abhängige Speicherkonto und das virtuelle Azure-Netzwerk zu erstellen.
   * **Ressourcengruppe:** Wählen Sie **Neu erstellen** aus, und geben Sie einen neuen Ressourcengruppennamen ein.
   * **Standort:** Wählen Sie einen Speicherort für die Ressourcengruppe aus.
   * **ClusterName:** Geben Sie einen Namen für den zu erstellenden Hadoop-Cluster ein.
   * **Clusteranmeldename und Kennwort:** Der Standardanmeldename lautet **admin**.
   * **SSH-Benutzername und Kennwort**: Der Standardbenutzername lautet **sshuser**.  Sie können auch einen anderen Namen festlegen.
   * **Ich stimme den oben genannten Geschäftsbedingungen zu:** (Auswählen)
3. Klicken Sie auf **Kaufen**. Das Erstellen eines Clusters dauert ca. 20 Minuten. Wenn der Cluster erstellt wurde, öffnen Sie ihn, indem Sie im Portal auf das Clusterblatt klicken.

Löschen Sie den Cluster, wenn Sie das Tutorial beendet haben. Mit HDInsight werden Ihre Daten im Azure-Speicher gespeichert, sodass Sie einen Cluster problemlos löschen können, wenn er nicht verwendet wird. Für einen HDInsight-Cluster fallen auch dann Gebühren an, wenn er nicht verwendet wird. Da die Gebühren für den Cluster erheblich höher sind als die Kosten für den Speicher, ist es sinnvoll, nicht verwendete Cluster zu löschen. Anweisungen zum Löschen eines Clusters finden Sie unter [Verwalten von Windows-basierten Apache Hadoop-Clustern in HDInsight mit dem Azure-Portal](../hdinsight-administer-use-portal-linux.md#delete-clusters).

Führen Sie die Schritte unter [Erste Schritte mit einem Apache HBase-Beispiel in HDInsight](./apache-hbase-tutorial-get-started-linux.md) aus, um mit Ihrem neu erstellten HBase-Cluster zu arbeiten.

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Herstellen einer Verbindung mit dem Apache HBase-Cluster mithilfe von Apache HBase-Java-RPC-APIs
1. Erstellen Sie einen virtuellen IaaS-Computer (Infrastructure-as-a-Service) im gleichen virtuellen Azure-Netzwerk und im gleichen Subnetz. Anweisungen zum Erstellen eines neuen virtuellen IaaS-Computers finden Sie im Abschnitt zum [Erstellen eines virtuellen Computers unter Windows Server](../../virtual-machines/windows/quick-create-portal.md). Beim Ausführen der in diesem Dokument beschriebenen Schritte müssen Sie die folgenden Werte für die Netzwerkkonfiguration verwenden:

   * **Virtuelles Netzwerk:** &lt;Clustername>-vnet
   * **Subnetz**: subnet1

   > [!IMPORTANT]  
   > Ersetzen Sie &lt;Clustername> durch den Namen, den Sie beim Erstellen des HDInsight-Clusters in den vorherigen Schritten verwendet haben.

   Mit diesen Werten wird der virtuelle Computer im selben virtuellen Netzwerk und Subnetz platziert wie der HDInsight-Cluster. Diese Konfiguration ermöglicht eine direkte Kommunikation zwischen virtuellem Computer und Cluster. Es gibt eine Möglichkeit zum Erstellen eines HDInsight-Clusters mit einem leeren Edgeknoten. Der Edgeknoten kann zum Verwalten des Clusters verwendet werden.  Weitere Informationen finden Sie unter [Use empty edge nodes in HDInsight](../hdinsight-apps-use-edge-node.md)(Verwenden leerer Edgeknoten in HDInsight).

2. Für Remote-Verbindungen zwischen Java-Anwendungen und HBase müssen Sie den vollqualifizierten Domänennamen (FQDN) verwenden. Rufen Sie hierzu das verbindungsspezifische DNS-Suffix des HBase-Clusters ab. Dazu können Sie eine der folgenden Methoden verwenden:

   * Ausführen eines [Apache Ambari](https://ambari.apache.org/)-Aufrufs mithilfe eines Webbrowsers:

     Wechseln Sie zu „https://&lt;Clustername>.azurehdinsight.net/api/v1/clusters/&lt;Clustername>/hosts?minimal_response=true“. Es wird eine JSON-Datei mit den DNS-Suffixen zurückgegeben.
   * Verwenden der Ambari-Website:

     1. Wechseln Sie zu „https://&lt;Clustername>.azurehdinsight.net“.
     2. Klicken Sie im Menü am oberen Rand auf **Hosts** .
   * Durchführen von REST-Aufrufen mithilfe von Curl:

     ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
     ```

     Suchen Sie in den zurückgegebenen JSON-Daten (JavaScript Object Notation) den Eintrag "host_name". Er enthält den vollqualifizierten Domänennamen (FQDN) für die Knoten im Cluster. Beispiel:

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     Der Teil des Domänennamens, der mit dem Clusternamen beginnt, ist das DNS-Suffix. Zum Beispiel: mycluster.b1.cloudapp.net.
   * Mithilfe von Azure PowerShell

     Verwenden Sie das folgende Azure PowerShell-Skript, um die **Get-ClusterDetail** -Funktion zu registrieren, mit der Sie das DNS-Suffix zurückgeben können:

     ```powershell
        function Get-ClusterDetail(
            [String]
            [Parameter( Position=0, Mandatory=$true )]
            $ClusterDnsName,
            [String]
            [Parameter( Position=1, Mandatory=$true )]
            $Username,
            [String]
            [Parameter( Position=2, Mandatory=$true )]
            $Password,
            [String]
            [Parameter( Position=3, Mandatory=$true )]
            $PropertyName
            )
        {
        <#
            .SYNOPSIS
            Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
            .Description
            This command shows the following 4 properties of an HDInsight cluster:
            1. ZookeeperQuorum (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.quorum".
            2. ZookeeperClientPort (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.property.clientPort".
            3. HBaseRestServers (supports only HBase type cluster)
                Shows a list of host FQDNs that run the HBase REST server.
            4. FQDNSuffix (supports all cluster types)
                Shows the FQDN suffix of hosts in the cluster.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
            This command shows the value of HBase property "hbase.zookeeper.quorum".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
            This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
            This command shows a list of host FQDNs that run the HBase REST server.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
            This command shows the FQDN suffix of hosts in the cluster.
        #>

            $DnsSuffix = ".azurehdinsight.net"

            $ClusterFQDN = $ClusterDnsName + $DnsSuffix
            $webclient = new-object System.Net.WebClient
            $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

            if($PropertyName -eq "ZookeeperQuorum")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
            }
            if($PropertyName -eq "ZookeeperClientPort")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
            }
            if($PropertyName -eq "HBaseRestServers")
            {
                $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                $Response1 = $webclient.DownloadString($Url1)
                $JsonObject1 = $Response1 | ConvertFrom-Json
                $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                $Response2 = $webclient.DownloadString($Url2)
                $JsonObject2 = $Response2 | ConvertFrom-Json
                foreach ($host_component in $JsonObject2.host_components)
                {
                    $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                    Write-host $ConnectionString
                }
            }
            if($PropertyName -eq "FQDNSuffix")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                $pos = $FQDN.IndexOf(".")
                $Suffix = $FQDN.Substring($pos + 1)
                Write-host $Suffix
            }
        }
     ```

     Verwenden Sie nach der Ausführung des Azure PowerShell-Skripts den folgenden Befehl, um das DNS-Suffix mithilfe der **Get-ClusterDetail** -Funktion zurückzugeben. Geben Sie den Namen Ihres HDInsight HBase-Clusters sowie den Admin-Namen und das Admin-Kennwort an, wenn Sie diesen Befehl ausführen.

     ```powershell
        Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>
     ```

     Dieser Befehl gibt das DNS-Suffix zurück. Zum Beispiel: **yourclustername.b4.internal.cloudapp.net**.


<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/PrimaryDNSSuffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Führen Sie den Befehl `ping headnode0.<dns suffix>` auf dem virtuellen Computer aus, um zu überprüfen, ob der virtuelle Computer mit dem HBase-Cluster kommunizieren kann. Beispiel: ping headnode0.mycluster.b1.cloudapp.net.

Führen Sie die unter [Erstellen von Java-Anwendungen für die Apache-HBase](./apache-hbase-build-java-maven-linux.md) beschriebenen Schritte aus, um diese Informationen in einer Java-Anwendung zu verwenden. Wenn die Anwendung eine Verbindung mit einem HBase-Remoteserver herstellen soll, müssen Sie den FQDN für Zookeeper in der Datei **hbase-site.xml** eintragen. Beispiel:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> Weitere Informationen zur Namensauflösung in virtuellen Azure-Netzwerken und Verwendung eigener DNS-Server finden Sie unter [Namensauflösung (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie gelernt, wie Sie einen Apache HBase-Cluster erstellen. Weitere Informationen finden Sie unter:

* [Erste Schritte mit HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Verwenden leerer Edgeknoten in HDInsight](../hdinsight-apps-use-edge-node.md)
* [Konfigurieren der Apache HBase-Replikation in HDInsight](apache-hbase-replication.md)
* [Erstellen von Apache Hadoop-Clustern in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Erste Schritte mit einem Apache HBase-Beispiel in HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Virtuelle Netzwerke im Überblick](../../virtual-network/virtual-networks-overview.md)

[1]: https://azure.microsoft.com/services/virtual-network/
[2]: https://technet.microsoft.com/library/ee176961.aspx
[3]: https://technet.microsoft.com/library/hh847889.aspx

