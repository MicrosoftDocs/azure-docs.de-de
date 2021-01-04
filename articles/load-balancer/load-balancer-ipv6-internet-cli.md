---
title: Erstellen eines öffentlichen Lastenausgleichs mit IPv6 – Azure-Befehlszeilenschnittstelle
titleSuffix: Azure Load Balancer
description: Mit diesem Lernpfad beginnen Sie mit dem Erstellen eines öffentlichen Lastenausgleichs mit IPv6 mithilfe der Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure Load Balancer, dualer Stapel, öffentliche IP, natives IPv6, mobil, IoT
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: 75226f92995794221635ced7ee0e285ac824b6e2
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696862"
---
# <a name="create-a-public-load-balancer-with-ipv6-using-azure-cli"></a>Erstellen eines öffentlichen Lastenausgleichs mit IPv6 mithilfe der Azure-Befehlszeilenschnittstelle

>[!NOTE] 
>In diesem Artikel wird eine Einführungsfunktion von IPv6 beschrieben, mit der Lastenausgleichsmodule im Basic-Tarif sowohl IPv4- als auch IPv6-Konnektivität bereitstellen können. Umfassende IPv6-Konnektivität ist jetzt mit [IPv6 für Azure VNETs](../virtual-network/ipv6-overview.md) verfügbar, das IPv6-Konnektivität in Ihre virtuellen Netzwerke integriert und wichtige Funktionen wie Regeln für IPv6-Netzwerksicherheitsgruppen, benutzerdefiniertes IPv6-Routing, IPv6-Lastenausgleich in den Tarifen Standard und Basic und mehr umfasst.  IPv6 für Azure VNETs ist der empfohlene Standard für IPv6-Anwendungen in Azure. Weitere Informationen finden Sie unter [IPv6 für Azure VNET-PowerShell-Bereitstellung](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md). 

Ein Azure Load Balancer ist ein Layer-4-Load Balancer (TCP, UDP). Lastenausgleichsmodule sorgen für Hochverfügbarkeit, indem sie eingehenden Datenverkehr zwischen funktionierenden Dienstinstanzen in Clouddiensten oder auf virtuelle Computer in einer Lastenausgleichsgruppe verteilen. Lastenausgleichsmodule können diese Dienste auch auf mehreren Ports, mehreren IP-Adressen oder beidem leisten.

## <a name="example-deployment-scenario"></a>Beispielszenario für die Bereitstellung

Das folgende Diagramm veranschaulicht die Lösung mit Lastenausgleich, die mithilfe der in diesem Artikel beschriebenen Beispielvorlage bereitgestellt wird.

![Load Balancer-Szenarios](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

In diesem Szenario erstellen Sie die folgenden Azure-Ressourcen:

* zwei virtuelle Computer (VMs)
* eine virtuelle Netzwerkschnittstelle für jeden virtuellen Computer mit zugewiesenen IPv4- und IPv6-Adressen
* einen öffentlichen Lastenausgleich mit je einer öffentlichen IPv4- und IPv6-IP-Adresse
* eine Verfügbarkeitsgruppe, die die zwei virtuellen Computer enthält
* zwei Lastenausgleichsregeln, um die öffentlichen virtuellen IP-Adressen den privaten Endpunkten zuzuordnen

## <a name="deploy-the-solution-by-using-azure-cli"></a>Bereitstellen der Lösung mithilfe der Azure-Befehlszeilenschnittstelle

Die folgenden Schritte zeigen, wie Sie einen öffentlichen Lastenausgleich mithilfe der Azure-Befehlszeilenschnittstelle erstellen. Mit der Befehlszeilenschnittstelle werden die einzelnen Objekt getrennt erstellt und konfiguriert und dann zusammengeführt, um eine Ressource zu erstellen.

Erstellen und konfigurieren Sie die folgenden Objekte, um einen Lastenausgleich bereitzustellen:

* **Front-End-IP-Konfiguration**: Enthält öffentliche IP-Adressen für eingehenden Netzwerkdatenverkehr.
* **Back-End-Adresspool**: Enthält Netzwerkschnittstellen (NICs), die virtuellen Computern den Empfang von Netzwerkdatenverkehr des Lastenausgleichs ermöglichen.
* **Lastenausgleichsregeln**: Enthält Regeln, die einen öffentlichen Port des Lastenausgleichs einem Port im Back-End-Adresspool zuordnen.
* **NAT-Eingangsregeln**: Enthält Netzwerkadressübersetzungs-Regeln (Network Address Translation, NAT), die einen öffentlichen Port des Lastenausgleichs einem Port für einen bestimmten virtuellen Computer im Back-End-Adresspool zuordnen.
* **Tests**: Enthält Integritätstests zum Prüfen der Verfügbarkeit von VM-Instanzen im Back-End-Adresspool.

## <a name="set-up-azure-cli"></a>Einrichten der Azure-Befehlszeilenschnittstelle

In diesem Beispiel führen Sie die Azure CLI-Tools in einem PowerShell-Befehlsfenster aus. Sie verwenden nicht die Azure PowerShell-Cmdlets, sondern die PowerShell-Skriptfunktionen, um Lesbarkeit und Wiederverwendung zu verbessern.

1. [Installieren und konfigurieren Sie die Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) anhand der Schritte im verlinkten Artikel, und melden Sie sich dann bei Ihrem Azure-Konto an.

2. Richten Sie PowerShell-Variablen für die Verwendung mit den Azure CLI-Befehlen ein:

    ```powershell
    $subscriptionid = "########-####-####-####-############"  # enter subscription id
    $location = "southcentralus"
    $rgName = "pscontosorg1southctrlus09152016"
    $vnetName = "contosoIPv4Vnet"
    $vnetPrefix = "10.0.0.0/16"
    $subnet1Name = "clicontosoIPv4Subnet1"
    $subnet1Prefix = "10.0.0.0/24"
    $subnet2Name = "clicontosoIPv4Subnet2"
    $subnet2Prefix = "10.0.1.0/24"
    $dnsLabel = "contoso09152016"
    $lbName = "myIPv4IPv6Lb"
    ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Erstellen einer Ressourcengruppe, eines Load Balancers, eines virtuellen Netzwerks und der Subnetze

1. Erstellen Sie eine Ressourcengruppe:

    ```azurecli
    az group create --name $rgName --location $location
    ```

2. Erstellen Sie einen Lastenausgleich:

    ```azurecli
    $lb = az network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Erstellen Sie ein virtuelles Netzwerk:

    ```azurecli
    $vnet = az network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. Erstellen Sie in diesem virtuellen Netzwerk zwei Subnetze:

    ```azurecli
    $subnet1 = az network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = az network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Erstellen öffentlicher IP-Adressen für den Front-End-Pool

1. Richten Sie die PowerShell-Variablen ein:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Erstellen Sie eine öffentliche IP-Adresse für den Front-End-IP-Pool:

    ```azurecli
    $publicipV4 = az network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --version IPv4 --allocation-method Dynamic --dns-name $dnsLabel
    $publicipV6 = az network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --version IPv6 --allocation-method Dynamic --dns-name $dnsLabel
    ```

    > [!IMPORTANT]
    > Der Lastenausgleich verwendet die Domänenbezeichnung der öffentlichen IP-Adresse als seinen vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN). Dies ist eine Abkehr von der klassischen Bereitstellung, bei der der Clouddienstname als FQDN des Load Balancers verwendet wird.
    >
    > In diesem Beispiel lautet der FQDN *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Erstellen von Front-End- und Back-End-Pools

In diesem Abschnitt erstellen Sie die folgenden IP-Pools:
* Front-End-IP-Pool, der den eingehenden Netzwerkdatenverkehr für den Lastenausgleich empfängt
* Back-End-IP-Pool, an den der Front-End-Pool den Netzwerkdatenverkehr sendet, für den ein Lastenausgleich durchgeführt wurde

1. Richten Sie die PowerShell-Variablen ein:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Erstellen Sie einen Front-End-IP-Adresspool, und ordnen Sie ihn der im vorherigen Schritt erstellten öffentlichen IP-Adresse und dem Lastenausgleich zu.

    ```azurecli
    $frontendV4 = az network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-address $publicIpv4Name --lb-name $lbName
    $frontendV6 = az network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-address $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>Erstellen des Tests, der NAT-Regeln und der Lastenausgleichsregeln

In diesem Beispiel werden die folgenden Elemente erstellt:

* Testregel, um die Verbindung mit TCP-Port 80 zu überprüfen
* NAT-Regel, um sämtlichen eingehenden Datenverkehr an Port 3389 für Port 3389 für RDP zu übersetzen\*
* NAT-Regel, um sämtlichen eingehenden Datenverkehr an Port 3391 für Port 3389 für Remotedesktopprotokoll (RDP) zu übersetzen\*
* Lastenausgleichsregel für die gleichmäßige Verteilung des gesamten an Port 80 eingehenden Datenverkehrs an Port 80 der Adressen im Back-End-Pool

\* NAT-Regeln sind einer bestimmten Instanz eines virtuellen Computers hinter dem Lastenausgleichsmodul zugeordnet. Der an Port 3389 eingehende Netzwerkdatenverkehr wird an einen bestimmten virtuellen Computer und einen Port gesendet, die mit dieser NAT-Regel verknüpft sind. Sie müssen ein Protokoll (UDP oder TCP) für eine NAT-Regel angeben. Sie können nicht beide Protokolle dem gleichen Port zuweisen.

1. Richten Sie die PowerShell-Variablen ein:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Erstellen Sie den Test.

    Das folgende Beispiel erstellt einen TCP-Test, der alle 15 Sekunden die Verbindung mit Back-End-TCP-Port 80 überprüft. Nach zwei aufeinanderfolgenden Fehlern wird die Back-End-Ressource als nicht verfügbar markiert.

    ```azurecli
    $probeV4V6 = az network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --threshold 2 --lb-name $lbName
    ```

3. Erstellen Sie eingehende NAT-Regeln, die RDP-Verbindungen mit Back-End-Ressourcen zulassen:

    ```azurecli
    $inboundNatRuleRdp1 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Erstellen Sie Lastenausgleichsregeln, die Datenverkehr abhängig davon, welches Front-End die Anforderung empfangen hat, an verschiedene Back-End-Ports senden.

    ```azurecli
    $lbruleIPv4 = az network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = az network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Überprüfen Sie Ihre Einstellungen:

    ```azurecli
    az network lb show --resource-group $rgName --name $lbName
    ```

    Erwartete Ausgabe:

    ```output
    info:    Executing command network lb show
    info:    Looking up the load balancer "myIPv4IPv6Lb"
    data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
    data:    Name                            : myIPv4IPv6Lb
    data:    Type                            : Microsoft.Network/loadBalancers
    data:    Location                        : southcentralus
    data:    Provisioning state              : Succeeded
    data:
    data:    Frontend IP configurations:
    data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
    data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
    data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
    data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
    data:
    data:    Probes:
    data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
    data:    -------------------  ------------------  --------  ----  ----  --------  -----
    data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
    data:
    data:    Backend Address Pools:
    data:    Name             Provisioning state
    data:    ---------------  ------------------
    data:    BackendPoolIPv4  Succeeded
    data:    BackendPoolIPv6  Succeeded
    data:
    data:    Load Balancing Rules:
    data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
    data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
    data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
    data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
    data:
    data:    Inbound NAT Rules:
    data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
    data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
    data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
    data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
    info:    network lb show
    ```

## <a name="create-nics"></a>Erstellen von NICs

Erstellen Sie NICs, und ordnen Sie diese NAT-Regeln, Lastenausgleichsregeln und Tests zu.

1. Richten Sie die PowerShell-Variablen ein:

    ```powershell
    $nic1Name = "myIPv4IPv6Nic1"
    $nic2Name = "myIPv4IPv6Nic2"
    $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
    $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
    $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
    $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
    $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
    $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
    ```

2. Erstellen Sie eine NIC für jedes Back-End, und fügen Sie eine IPv6-Konfiguration hinzu:

    ```azurecli
    $nic1 = az network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet1Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule1V4Id
    $nic1IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = az network nic create --name $nic2Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet2Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule2V4Id
    $nic2IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Erstellen der Back-End-VM-Ressourcen und Anfügen der NICs

Um VMs zu erstellen, benötigen Sie ein Speicherkonto. Für den Lastenausgleich müssen die VMs zu einer Verfügbarkeitsgruppe gehören. Weitere Informationen zum Erstellen von VMs finden Sie unter [Erstellen eines virtuellen Windows-Computers mit PowerShell](../virtual-machines/windows/quick-create-powershell.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. Richten Sie die PowerShell-Variablen ein:

    ```powershell
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $imageurn = "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > Dieses Beispiel verwendet den Benutzernamen und das Kennwort für die virtuellen Computer in Klartext. Bei Anmeldeinformationen in Klartext müssen Sie entsprechend vorsichtig vorgehen. Eine sicherere Methode zur Handhabung der Anmeldeinformationen in PowerShell finden Sie in den Informationen zum Cmdlet [`Get-Credential`](/powershell/module/microsoft.powershell.security/get-credential).

2. Erstellen Sie die Verfügbarkeitsgruppe:

    ```azurecli
    $availabilitySet = az vm availability-set create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. Erstellen Sie die virtuellen Computer mit den zugehörigen NICs:

    ```azurecli
    az vm create --resource-group $rgname --name $vm1Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic1Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 

    az vm create --resource-group $rgname --name $vm2Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic2Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 
    ```