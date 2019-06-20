---
title: Herstellen einer Verbindung mit Kafka mithilfe von virtuellen Netzwerken – Azure HDInsight
description: Erfahren Sie, wie Sie eine direkte Verbindung mit Kafka in HDInsight über ein virtuelles Azure-Netzwerk herstellen. Erfahren Sie, wie Sie über Entwicklungsclients mit einem VPN-Gateway oder über Clients in Ihrem lokalen Netzwerk mithilfe eines VPN-Gateway-Geräts eine Verbindung mit Kafka herstellen.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: ddff9ffb00f4167cb8f64a75b129711467de739d
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297063"
---
# <a name="connect-to-apache-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Herstellen einer Verbindung mit Apache Kafka in HDInsight über ein virtuelles Azure-Netzwerk

Erfahren Sie, wie Sie eine direkte Verbindung mit Apache Kafka in HDInsight über ein virtuelles Azure-Netzwerk herstellen. Dieses Dokument enthält Informationen zum Verbinden mit Kafka mithilfe der folgenden Konfigurationen:

* Aus Ressourcen in einem lokalen Netzwerk. Diese Verbindung wird mithilfe eines VPN-Geräts (Software oder Hardware) in Ihrem lokalen Netzwerk hergestellt.
* Aus einer Entwicklungsumgebung mit einem VPN-Softwareclient.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="architecture-and-planning"></a>Architektur und Planung

HDInsight erlaubt keine direkten Verbindungen zu Kafka über das öffentliche Internet. Kafka-Clients (Producer und Consumer) müssen stattdessen eine der folgenden Verbindungsmethoden verwenden:

* Führen Sie den Client im selben virtuellen Netzwerk wie Kafka auf HDInsight aus. Diese Konfiguration wird im Dokument [Erste Schritte mit Apache Kafka in HDInsight](apache-kafka-get-started.md) verwendet. Der Client wird direkt auf dem HDInsight-Clusterknoten oder auf einem anderen virtuellen Computer im selben Netzwerk ausgeführt.

* Verbinden Sie ein privates Netzwerk, z.B. Ihr lokalen Netzwerk, mit dem virtuellen Netzwerk. Diese Konfiguration ermöglicht es Clients, in Ihrem lokalen Netzwerk direkt mit Kafka zu arbeiten. Um diese Konfiguration zu aktivieren, führen Sie die folgenden Aufgaben aus:

  1. Erstellen Sie ein virtuelles Netzwerk.
  2. Erstellen Sie ein VPN-Gateway, das eine Standort-zu-Standort-Konfiguration verwendet. Die in diesem Dokument verwendete Konfiguration stellt eine Verbindung zu einem VPN-Gateway-Gerät in Ihrem lokalen Netzwerk her.
  3. Erstellen Sie einen DNS-Server im virtuellen Netzwerk.
  4. Konfigurieren Sie die Weiterleitung zwischen den DNS-Servern in jedem Netzwerk.
  5. Erstellen Sie einen Kafka-Cluster in HDInsight-Cluster im virtuellen Netzwerk.

     Weitere Informationen finden Sie im Abschnitt [Herstellen einer Verbindung mit Apache Kafka aus einem lokalen Netzwerk](#on-premises). 

* Verbinden Sie die einzelnen Computer mithilfe des VPN-Gateways und dem VPN-Client mit dem Netzwerk. Um diese Konfiguration zu aktivieren, führen Sie die folgenden Aufgaben aus:

  1. Erstellen Sie ein virtuelles Netzwerk.
  2. Erstellen Sie ein VPN-Gateway, das eine Punkt-zu-Standort-Konfiguration verwendet. Diese Konfiguration kann sowohl für Windows- als auch macOS-Clients verwendet werden.
  3. Erstellen Sie einen Kafka-Cluster in HDInsight-Cluster im virtuellen Netzwerk.
  4. Konfigurieren Sie Kafka zum Ankündigen der IP-Adresse. Diese Konfiguration ermöglicht dem Client das Herstellen einer Verbindung mithilfe von Broker-IP-Adressen anstelle von Domänennamen.
  5. Laden Sie den VPN-Client auf dem Entwicklungssystem herunter, und verwenden Sie ihn.

     Weitere Informationen finden Sie im Abschnitt [Herstellen einer Verbindung mit Apache Kafka über einen VPN-Client](#vpnclient).

     > [!WARNING]  
     > Diese Konfiguration wird aufgrund der folgenden Einschränkungen nur für Entwicklungszwecke empfohlen:
     >
     > * Jeder Client muss eine Verbindung mithilfe eines VPN-Software-Clients herstellen.
     > * Der VPN-Client übergibt die Anforderungen zur Namensauflösung nicht an das virtuelle Netzwerk. Sie müssen also IP-Adressen für die Kommunikation mit Kafka verwenden. Die IP-Kommunikation erfordert eine zusätzliche Konfiguration auf dem Kafka-Cluster.

Weitere Informationen zur Verwendung von HDInsight in einem virtuellen Netzwerk finden Sie unter [Erweitern der HDInsight-Funktionen mit Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md).

## <a id="on-premises"></a> Herstellen einer Verbindung mit Apache Kafka aus einem lokalen Netzwerk

Um ein Kafka-Cluster zu erstellen, das mit Ihrem lokalen Netzwerk kommuniziert, führen Sie die Schritte im Dokument [Connect HDInsight to your on-premises network (Verbinden von HDInsight mit Ihrem lokalen Netzwerk)](./../connect-on-premises-network.md).

> [!IMPORTANT]  
> Wenn Sie das HDInsight-Cluster erstellen, wählen Sie den Clustertyp __Kafka__ aus.

Mit diesen Schritten erstellen Sie die folgende Konfiguration:

* Virtuelles Azure-Netzwerk
* Standort-zu-Standort-VPN-Gateway
* Azure-Storage-Konto (von HDInsight verwendet)
* Kafka in HDInsight

Um sicherzustellen, dass ein Kafka-Client eine Verbindung von einem lokalen Standort zu einem Cluster herstellen kann, verwenden Sie die Schritte im Abschnitt [Beispiel: Python-Client](#python-client).

## <a id="vpnclient"></a> Herstellen einer Verbindung mit Apache Kafka über einen VPN-Client

Führen Sie die Schritte in diesem Abschnitt aus, um die folgende Konfiguration zu erstellen:

* Virtuelles Azure-Netzwerk
* Point-to-Site-VPN-Gateway
* Azure-Speicherkonto (von HDInsight verwendet)
* Kafka in HDInsight

1. Führen Sie die Schritte im Dokument [Generieren und Exportieren von Zertifikaten für Punkt-zu-Site-Verbindungen mithilfe von PowerShell unter Windows 10](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) aus. In diesem Dokument werden die für das Gateway benötigten Zertifikate erstellt.

2. Öffnen Sie eine PowerShell-Eingabeaufforderung, und verwenden Sie den folgenden Code, um sich bei Ihrem Azure-Abonnement anzumelden:

    ```powershell
    Connect-AzAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzSubscription -SubscriptionName "name of your subscription"
    ```

3. Verwenden Sie den folgenden Code zum Erstellen von Variablen, die Konfigurationsinformationen enthalten:

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.6"
    $hdiType = "Kafka"
    ```

4. Verwenden Sie den folgenden Code, um die Azure-Ressourcengruppe und das virtuelle Netzwerk zu erstellen:

    ```powershell
    # Create the resource group that contains everything
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]  
    > Dieser Prozess kann mehrere Minuten dauern.

5. Verwenden Sie den folgenden Code, um das Azure-Speicherkonto und Blobcontainer zu erstellen:

    ```powershell
    # Create the storage account
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -SkuName Standard_GRS `
        -Location $location `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. Verwenden Sie den folgenden Code zum Erstellen des HDInsight-Clusters:

    ```powershell
    # Create the HDInsight cluster
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -DisksPerWorkerNode 2 `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

   > [!WARNING]  
   > Für diesen Prozess werden etwa 15 Minuten benötigt.

### <a name="configure-kafka-for-ip-advertising"></a>Konfigurieren von Kafka zum Ankündigen der IP-Adresse

Standardmäßig gibt Apache ZooKeeper den Domänennamen der Kafka-Broker an Clients zurück. Diese Konfiguration funktioniert für den VPN-Client nicht, da er die Namensauflösung nicht für Entitäten im virtuellen Netzwerk verwenden kann. Verwenden Sie für diese Konfiguration die folgenden Schritte, um Kafka zum Ankündigen von IP-Adressen anstelle von Domänennamen zu konfigurieren:

1. Wechseln Sie in Ihrem Webbrowsers zu `https://CLUSTERNAME.azurehdinsight.net`. Ersetzen Sie `CLUSTERNAME` durch den Namen von Kafka im HDInsight-Cluster.

    Geben Sie bei entsprechender Aufforderung den HTTPS-Benutzernamen und das Kennwort für den Cluster ein. Die Ambari-Webbenutzeroberfläche für den Cluster wird angezeigt.

2. Wählen Sie zum Anzeigen von Informationen über Kafka aus der Liste auf der linken Seite __Kafka__ aus.

    ![Dienstliste, in der Kafka hervorgehoben ist](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Wählen Sie zum Anzeigen der Kafka-Konfiguration oben in der Mitte __Konfigurationen__ aus.

    ![Konfigurationslinks für Kafka](./media/apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. Geben Sie zum Suchen der Konfiguration __kafka-env__ oben rechts in das Feld __Filter__ die Zeichenfolge `kafka-env` ein.

    ![Kafka-Konfiguration für kafka-env](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Um Kafka zum Ankündigen von IP-Adressen zu konfigurieren, fügen Sie den folgenden Text am Ende des Felds __Vorlage für kafka-env__ hinzu:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Geben Sie zum Konfigurieren der Schnittstelle, an der Kafka lauscht, oben rechts in das Feld __Filter__ die Zeichenfolge `listeners` ein.

7. Um Kafka zum Lauschen an allen Netzwerkschnittstellen zu konfigurieren, ändern Sie den Wert im Feld __Listener__ in `PLAINTEXT://0.0.0.0:9092`.

8. Klicken Sie auf die Schaltfläche __Speichern__, um die Konfigurationsänderungen zu speichern. Geben Sie eine Textnachricht ein, die die Änderungen beschreibt. Wählen Sie __OK__ aus, nachdem die Änderungen gespeichert wurden.

    ![Schaltfläche zum Speichern der Konfiguration](./media/apache-kafka-connect-vpn-gateway/save-button.png)

9. Um Fehler beim Neustart von Kafka zu vermeiden, verwenden Sie die Schaltfläche __Dienstaktionen__, und wählen Sie __Wartungsmodus aktivieren__ aus. Wählen Sie „OK“ aus, um diesen Vorgang abzuschließen.

    ![Dienstaktionen, „Wartungsmodus aktivieren“ hervorgehoben](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Verwenden Sie zum Neustarten von Kafka die Schaltfläche __Neu starten__, und wählen Sie __Alle betroffenen Instanzen neu starten__ aus. Bestätigen Sie den Neustart, und verwenden Sie dann die Schaltfläche __OK__, wenn der Vorgang abgeschlossen ist.

    ![Schaltfläche „Neu starten“, Funktion zum Neustarten aller betroffenen Instanzen hervorgehoben](./media/apache-kafka-connect-vpn-gateway/restart-button.png)

11. Um den Wartungsmodus zu deaktivieren, verwenden die Schaltfläche __Dienstaktionen__, und wählen Sie __Wartungsmodus deaktivieren__ aus. Wählen Sie **OK** aus, um diesen Vorgang abzuschließen.

### <a name="connect-to-the-vpn-gateway"></a>Herstellen einer Verbindung mit dem VPN-Gateway

Verwenden Sie zum Herstellen einer Verbindung mit dem VPN-Gateway den Abschnitt __Herstellen einer Verbindung mit Azure__ im Dokument [Konfigurieren einer Point-to-Site-Verbindung](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect).

## <a id="python-client"></a> Beispiel: Python-Client

Um die Konnektivität mit Kafka zu überprüfen, führen Sie die folgenden Schritte aus, um einen Python-Producer und -Consumer zu erstellen und auszuführen:

1. Verwenden Sie eine der folgenden Methoden, um den vollqualifizierten Domänennamen (FQDN) und die IP-Adressen der Knoten im Kafka-Cluster abzurufen:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    In diesem Skript wird vorausgesetzt, dass `$resourceGroupName` der Name der Azure-Ressourcengruppe ist, die das virtuelle Netzwerk enthält.

    Speichern Sie die zurückgegebenen Informationen, um sie in den nächsten Schritten zu verwenden.

2. Verwenden Sie Folgendes zum Installieren des [kafka-python](https://kafka-python.readthedocs.io/)-Clients:

    ```bash
    pip install kafka-python
    ```

3. Verwenden Sie zum Senden von Daten an Kafka den folgenden Python-Code:

   ```python
   from kafka import KafkaProducer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # NOTE: you don't need the full list of worker nodes, just one or two.
   producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
   for _ in range(50):
      producer.send('testtopic', b'test message')
   ```

    Ersetzen Sie die `'kafka_broker'`-Einträge durch die Adressen, die in Schritt 1 in diesem Abschnitt zurückgegeben wurden:

   * Wenn Sie einen __Software-VPN-Client__ verwenden, ersetzen Sie die `kafka_broker`-Einträge durch die IP-Adresse Ihrer Workerknoten.

   * Wenn Sie die __Namensauflösung über einen benutzerdefinierten DNS-Server aktiviert haben__, ersetzen die `kafka_broker`-Einträge durch den FQDN der Workerknoten.

     > [!NOTE]
     > Dieser Code sendet die Zeichenfolge `test message` an das Thema `testtopic`. Die Standardkonfiguration von Kafka in HDInsight ist, dass das Thema erstellt wird, wenn es nicht vorhanden ist.

4. Verwenden Sie zum Abrufen von Nachrichten von Kafka den folgenden Python-Code:

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Again, you only need one or two, not the full list.
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    Ersetzen Sie die `'kafka_broker'`-Einträge durch die Adressen, die in Schritt 1 in diesem Abschnitt zurückgegeben wurden:

    * Wenn Sie einen __Software-VPN-Client__ verwenden, ersetzen Sie die `kafka_broker`-Einträge durch die IP-Adresse Ihrer Workerknoten.

    * Wenn Sie die __Namensauflösung über einen benutzerdefinierten DNS-Server aktiviert haben__, ersetzen die `kafka_broker`-Einträge durch den FQDN der Workerknoten.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von HDInsight mit einem virtuellen Netzwerk finden Sie im Dokument [Erweitern von Azure HDInsight per Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md).

Weitere Informationen zum Erstellen eines virtuellen Azure-Netzwerks mit Point-to-Site-VPN-Gateway finden Sie in den folgenden Dokumenten:

* [Konfigurieren einer Point-to-Site-Verbindung mit einem VNET über das Azure-Portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Konfigurieren einer Point-to-Site-VPN-Verbindung (P2S) mit einem VNET mithilfe von PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Weitere Informationen zur Verwendung von Apache Kafka in HDInsight finden Sie in den folgenden Dokumenten:

* [Erste Schritte mit Apache Kafka in HDInsight](apache-kafka-get-started.md)
* [Verwenden von Spiegelung mit Apache Kafka in HDInsight](apache-kafka-mirroring.md)
