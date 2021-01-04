---
title: Erstellen von virtuellen Netzwerken für Azure HDInsight-Cluster
description: Erfahren Sie, wie Sie ein virtuelles Azure-Netzwerk erstellen, um HDInsight mit anderen Cloudressourcen oder Ressourcen in Ihrem Rechenzentrum zu verbinden.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/16/2020
ms.openlocfilehash: 4f604ba2edcc63a245a5e87d3dcb6fdd16a9d050
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92741443"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>Erstellen von virtuellen Netzwerken für Azure HDInsight-Cluster

Dieser Artikel enthält Beispiele und Beispielcode für das Erstellen und Konfigurieren von [virtuellen Azure-Netzwerken](../virtual-network/virtual-networks-overview.md) für die Verwendung mit Azure HDInsight-Clustern. Es werden ausführliche Beispiele zum Erstellen von Netzwerksicherheitsgruppen (NSG) und der DNS-Konfiguration gegeben.

Hintergrundinformationen für die Verwendung von virtuellen Netzwerken mit Azure HDInsight finden Sie unter [Plan a virtual network for Azure HDInsight (Planen eines virtuellen Netzwerks für Azure HDInsight)](hdinsight-plan-virtual-network-deployment.md).

## <a name="prerequisites-for-code-samples-and-examples"></a>Voraussetzungen für die Codebeispiele und Beispiele

Vor dem Ausführen der in diesem Artikel beschriebenen Beispielcodes sollten Sie über Grundkenntnisse im Umgang mit TCP/IP-Netzwerken verfügen. Falls Sie nicht mit TCP/IP-Netzwerken vertraut sind, sollten Sie sich an eine Person mit den entsprechenden Kenntnissen wenden, bevor Sie Änderungen an Produktionsnetzwerken vornehmen.

Weitere Voraussetzungen für die Beispiele in diesem Artikel sind die folgenden:

* Sie müssen das [AZ-Modul](/powershell/azure/) installieren, wenn Sie PowerShell verwenden.
* Navigieren Sie zu [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli), wenn Sie die Azure-Befehlszeilenschnittstelle verwenden möchten, diese aber noch nicht installiert haben.

> [!IMPORTANT]  
> Eine ausführliche Anleitung zum Herstellen einer Verbindung von HDInsight mit Ihrem lokalen Netzwerk mithilfe eines virtuellen Azure-Netzwerks finden Sie im Dokument [Verbinden von HDInsight mit Ihrem lokalen Netzwerk](connect-on-premises-network.md).

## <a name="example-network-security-groups-with-hdinsight"></a><a id="hdinsight-nsg"></a>Beispiel: Netzwerksicherheitsgruppen mit HDInsight

Die Beispiele in diesem Abschnitt veranschaulichen, wie Sie Netzwerksicherheitsgruppen-Regeln erstellen, die HDInsight die Kommunikation mit den Azure-Verwaltungsdiensten ermöglichen. Passen Sie vor dem Verwenden der Beispiele die IP-Adressen an die IP-Adressen der genutzten Azure-Region an. Sie finden diese Informationen unter [HDInsight management IP addresses (IP-Adressen für die HDInsight-Verwaltung)](hdinsight-management-ip-addresses.md).

### <a name="azure-resource-management-template"></a>Azure Resource Manager-Vorlage

Mit der folgenden Resource Manager-Vorlage wird ein virtuelles Netzwerk erstellt, mit dem eingehender Datenverkehr eingeschränkt wird, während Datenverkehr von den IP-Adressen zugelassen wird, die für HDInsight benötigt werden. Mit dieser Vorlage wird auch ein HDInsight-Cluster im virtuellen Netzwerk erstellt.

* [Deploy a secured Azure Virtual Network and an HDInsight Hadoop cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/) (Bereitstellen eines geschützten Azure Virtual Network und eines HDInsight Hadoop-Clusters)

### <a name="azure-powershell"></a>Azure PowerShell

Verwenden Sie das folgende PowerShell-Skript, um ein virtuelles Netzwerk zu erstellen, bei dem eingehender Datenverkehr eingeschränkt und Datenverkehr von den IP-Adressen für die Region „Europa, Norden“ zugelassen wird.

> [!IMPORTANT]  
> Ändern Sie die IP-Adressen, die in diesem Beispiel für `hdirule1` und `hdirule2` verwendet werden, damit sie der von Ihnen genutzten Azure-Region entsprechen. Sie finden diese Informationen unter [HDInsight management IP addresses (IP-Adressen für die HDInsight-Verwaltung)](hdinsight-management-ip-addresses.md).

```azurepowershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"

# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName

# Get the region the Virtual network is in.
$location = $vnet.Location

# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName

# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `

# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg

# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

In diesem Beispiel wird veranschaulicht, wie Sie Regeln hinzufügen, um eingehenden Datenverkehr für die erforderlichen IP-Adressen zuzulassen. Es enthält keine Regel zum Einschränken des Zugriffs in eingehender Richtung von anderen Quellen. Mit dem folgenden Code wird veranschaulicht, wie der SSH-Zugriff über das Internet aktiviert wird:

```azurepowershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Verwenden Sie die folgenden Schritte, um ein virtuelles Netzwerk zu erstellen, mit dem eingehender Datenverkehr eingeschränkt wird, während Datenverkehr von den IP-Adressen zugelassen wird, die für HDInsight benötigt werden.

1. Verwenden Sie den folgenden Befehl, um eine neue Netzwerksicherheitsgruppe namens `hdisecure`zu erstellen. Ersetzen Sie `RESOURCEGROUP` durch die Ressourcengruppe, die das virtuelle Azure-Netzwerk enthält. Ersetzen Sie `LOCATION` durch den Standort (Region), in dem die Gruppe erstellt wurde.

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    Nachdem die Gruppe erstellt wurde, erhalten Sie Informationen über die neue Gruppe.

2. Gehen Sie wie folgt vor, um der neuen Netzwerksicherheitsgruppe Regeln hinzuzufügen, die eingehende Kommunikation auf Port 443 aus dem Azure HDInsight-Integritäts- und Verwaltungsdienst ermöglichen. Ersetzen Sie `RESOURCEGROUP` durch den Namen der Ressourcengruppe, die das virtuelle Azure-Netzwerk enthält.

    > [!IMPORTANT]  
    > Ändern Sie die IP-Adressen, die in diesem Beispiel für `hdirule1` und `hdirule2` verwendet werden, damit sie der von Ihnen genutzten Azure-Region entsprechen. Sie finden diese Informationen unter [HDInsight management IP addresses (IP-Adressen für die HDInsight-Verwaltung)](hdinsight-management-ip-addresses.md).

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Verwenden Sie den folgenden Befehl, um den eindeutigen Bezeichner für diese Netzwerksicherheitsgruppe abzurufen:

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query "id"
    ```

    Dieser Befehl gibt einen Wert zurück, der in etwa wie folgt aussieht:

    ```output
    "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

4. Verwenden Sie den folgenden Befehl, um die Netzwerksicherheitsgruppe auf ein Subnetz anzuwenden. Ersetzen Sie die Werte `GUID` und `RESOURCEGROUP` durch die Rückgabewerte aus dem vorherigen Schritt. Ersetzen Sie `VNETNAME` und `SUBNETNAME` durch den Namen des zu erstellenden virtuellen Netzwerks bzw. durch den Namen des Subnetzes.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Nachdem die Ausführung dieses Befehls abgeschlossen ist, können Sie HDInsight im virtuellen Netzwerk installieren.

Mit diesen Schritten wird nur der Zugriff auf den HDInsight-Integritäts- und -Verwaltungsdienst in der Azure-Cloud ermöglicht. Jeder andere Zugriff auf den HDInsight-Cluster von außerhalb des virtuellen Netzwerks wird blockiert. Wenn Sie den Zugriff von außerhalb des virtuellen Netzwerks ermöglichen möchten, müssen Sie zusätzliche Netzwerksicherheitsgruppen-Regeln hinzufügen.

Mit dem folgenden Code wird veranschaulicht, wie der SSH-Zugriff über das Internet aktiviert wird:

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a name="example-dns-configuration"></a><a id="example-dns"></a> Beispiel: DNS-Konfiguration

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Namensauflösung zwischen einem virtuellen Netzwerk und einem verbundenen lokalen Netzwerk

Für dieses Beispiel werden die folgenden Annahmen getroffen:

* Sie verfügen über ein Azure Virtual Network, das per VPN-Gateway mit einem lokalen Netzwerk verbunden ist.

* Auf dem benutzerdefinierten DNS-Server im virtuellen Netzwerk wird als Betriebssystem Linux oder Unix ausgeführt.

* Auf dem benutzerdefinierten DNS-Server ist [Bind](https://www.isc.org/downloads/bind/) installiert.

Auf dem benutzerdefinierten DNS-Server im virtuellen Netzwerk:

1. Verwenden Sie entweder Azure PowerShell oder Azure CLI, um nach dem DNS-Suffix des virtuellen Netzwerks zu suchen:

    Ersetzen Sie `RESOURCEGROUP` durch den Namen der Ressourcengruppe, die das virtuelle Netzwerk enthält, und geben Sie dann diesen Befehl ein:

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

1. Verwenden Sie auf dem benutzerdefinierten DNS-Server für das virtuelle Netzwerk den folgenden Text als Inhalt der Datei `/etc/bind/named.conf.local`:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Ersetzen Sie den Wert `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` durch das DNS-Suffix Ihres virtuellen Netzwerks.

    Bei dieser Konfiguration werden alle DNS-Anforderungen für das DNS-Suffix des virtuellen Netzwerks an den rekursiven Azure-Resolver geleitet.

1. Verwenden Sie auf dem benutzerdefinierten DNS-Server für das virtuelle Netzwerk den folgenden Text als Inhalt der Datei `/etc/bind/named.conf.options`:

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Ersetzen Sie den Wert `10.0.0.0/16` durch den IP-Adressbereich Ihres virtuellen Netzwerks. Für diesen Eintrag sind Adressen für Namensauflösungsanforderungen innerhalb dieses Bereichs zulässig.

    * Fügen Sie den IP-Adressbereich des lokalen Netzwerks dem Abschnitt `acl goodclients { ... }` hinzu.  Für den Eintrag sind Namensauflösungsanforderungen von Ressourcen im lokalen Netzwerk zulässig.
    
    * Ersetzen Sie den Wert `192.168.0.1` durch die IP-Adresse Ihres lokalen DNS-Servers. Bei diesem Eintrag werden alle anderen DNS-Anforderungen an den lokalen DNS-Server geleitet.

1. Starten Sie Bind neu, um die Konfiguration zu verwenden. Beispiel: `sudo service bind9 restart`.

1. Fügen Sie dem lokalen DNS-Server eine bedingte Weiterleitung hinzu. Konfigurieren Sie die bedingte Weiterleitung, um Anforderungen für das DNS-Suffix aus Schritt 1 an den benutzerdefinierten DNS-Server zu senden.

    > [!NOTE]  
    > Die Dokumentation Ihrer DNS-Software enthält ausführliche Informationen zum Hinzufügen einer bedingten Weiterleitung.

Nach Ausführung dieser Schritte können Sie mit den Ressourcen in beiden Netzwerken eine Verbindung herstellen, indem Sie vollqualifizierte Domänennamen (FQDNs) verwenden. Sie können HDInsight jetzt im virtuellen Netzwerk installieren.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Namensauflösung zwischen zwei verbundenen virtuellen Netzwerken

Für dieses Beispiel werden die folgenden Annahmen getroffen:

* Sie verfügen über zwei Azure Virtual Networks, die per VPN-Gateway oder Peering verbunden sind.

* Auf dem benutzerdefinierten DNS-Server in beiden Netzwerken wird als Betriebssystem Linux oder Unix ausgeführt.

* Auf den benutzerdefinierten DNS-Servern ist [Bind](https://www.isc.org/downloads/bind/) installiert.

1. Verwenden Sie entweder Azure PowerShell oder Azure CLI, um nach dem DNS-Suffix der beiden virtuellen Netzwerke zu suchen:

    Ersetzen Sie `RESOURCEGROUP` durch den Namen der Ressourcengruppe, die das virtuelle Netzwerk enthält, und geben Sie dann diesen Befehl ein:

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Verwenden Sie den folgenden Text als Inhalt der Datei `/etc/bind/named.config.local` auf dem benutzerdefinierten DNS-Server. Nehmen Sie diese Änderung auf dem benutzerdefinierten DNS-Server in beiden virtuellen Netzwerken vor.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Ersetzen Sie den Wert `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` durch das DNS-Suffix des __anderen__ virtuellen Netzwerks. Mit diesem Eintrag werden Anforderungen für das DNS-Suffix des Remotenetzwerks an das benutzerdefinierte DNS in diesem Netzwerk geleitet.

3. Verwenden Sie auf den benutzerdefinierten DNS-Servern der beiden virtuellen Netzwerke den folgenden Text als Inhalt der Datei `/etc/bind/named.conf.options`:

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

   Ersetzen Sie die Werte `10.0.0.0/16` und `10.1.0.0/16` durch die IP-Adressbereiche Ihrer virtuellen Netzwerke. Bei diesem Eintrag ist es für Ressourcen in beiden Netzwerken zulässig, Anforderungen an die DNS-Server zu senden.

    Alle Anforderungen, die nicht für die DNS-Suffixe der virtuellen Netzwerke bestimmt sind (z.B. „microsoft.com“), werden vom rekursiven Azure-Resolver verarbeitet.

4. Starten Sie Bind neu, um die Konfiguration zu verwenden. Beispiel: `sudo service bind9 restart` auf beiden DNS-Servern.

Nach Ausführung dieser Schritte können Sie mit den Ressourcen im virtuellen Netzwerk eine Verbindung herstellen, indem Sie vollqualifizierte Domänennamen (FQDNs) verwenden. Sie können HDInsight jetzt im virtuellen Netzwerk installieren.

## <a name="next-steps"></a>Nächste Schritte

* Ein umfassendes Beispiel für die Konfiguration von HDInsight zum Herstellen einer Verbindung mit einem lokalen Netzwerk finden Sie unter [Verbinden von HDInsight mit einem lokalen Netzwerk](./connect-on-premises-network.md) ().
* Informationen zum Konfigurieren von Apache HBase-Clustern in virtuellen Azure-Netzwerken finden Sie unter [Erstellen von Apache HBase-Clustern in HDInsight in Azure Virtual Network](hbase/apache-hbase-provision-vnet.md).
* Informationen zum Konfigurieren der Apache HBase-Georeplikation finden Sie unter [Einrichten der Apache HBase-Clusterreplikation in virtuellen Azure-Netzwerken](hbase/apache-hbase-replication.md).
* Weitere Informationen zu virtuellen Azure-Netzwerken finden Sie in der [Übersicht zu virtuellen Azure-Netzwerken](../virtual-network/virtual-networks-overview.md).

* Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie in der [Übersicht zu Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md).

* Weitere Informationen zu benutzerdefinierten Routen finden Sie unter [Benutzerdefinierte Routen und IP-Weiterleitung](../virtual-network/virtual-networks-udr-overview.md).
