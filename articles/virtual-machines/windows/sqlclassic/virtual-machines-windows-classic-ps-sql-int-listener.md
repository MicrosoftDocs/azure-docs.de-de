---
title: Konfigurieren eines ILB-Listeners für AlwaysOn-Verfügbarkeitsgruppen in Azure | Microsoft-Dokumentation
description: In diesem Tutorial werden Ressourcen verwendet, die mit dem klassischen Bereitstellungsmodell erstellt wurden, und es wird mithilfe eines internen Load Balancers eine AlwaysOn-Verfügbarkeitsgruppe in Azure erstellt.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: 0e6a52ea2fdd05546a4da9f8cd1165b41ed27944
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62097688"
---
# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Konfigurieren eines ILB-Listeners für AlwaysOn-Verfügbarkeitsgruppen in Azure
> [!div class="op_single_selector"]
> * [Interner Listener](../classic/ps-sql-int-listener.md)
> * [Externer Listener](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Übersicht

> [!IMPORTANT]
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Azure Resource Manager und klassische Bereitstellung](../../../azure-resource-manager/resource-manager-deployment-model.md). Dieser Artikel gilt für das klassische Bereitstellungsmodell. Wir empfehlen für die meisten Neubereitstellungen das Resource Manager-Modell.

Informationen zum Konfigurieren eines Listeners für eine AlwaysOn-Verfügbarkeitsgruppe im Resource Manager-Modell finden Sie unter [Konfigurieren eines Load Balancers für eine AlwaysOn-Verfügbarkeitsgruppe in Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

Ihre Verfügbarkeitsgruppe kann Replikate enthalten, die ausschließlich lokal, ausschließlich in Azure oder sowohl lokal als auch in Azure verfügbar sind (Hybridkonfigurationen). Azure-Replikate können sich innerhalb derselben Region oder in mehreren Regionen befinden, wobei mehrere virtuelle Netzwerke verwendet werden. Bei den Verfahren in diesem Artikel wird davon ausgegangen, dass bereits eine [Verfügbarkeitsgruppe konfiguriert wurde](../classic/portal-sql-alwayson-availability-groups.md), Sie jedoch noch keinen Listener konfiguriert haben.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Richtlinien und Einschränkungen für interne Listener
Bei Verwendung eines internen Load Balancers (ILB) mit einem Verfügbarkeitsgruppenlistener in Azure gelten folgende Richtlinien:

* Der Verfügbarkeitsgruppenlistener wird unter Windows Server 2008 R2, Windows Server 2012 und Windows Server 2012 R2 unterstützt.
* Da der Lister mit dem internen Load Balancer (ILB) konfiguriert wird und nur ein ILB pro Clouddienst vorhanden ist, wird für jeden Clouddienst maximal ein Verfügbarkeitsgruppenlistener unterstützt. Es ist jedoch möglich, mehrere externe Listener zu erstellen. Weitere Informationen finden Sie unter [Konfigurieren eines externen Listeners für AlwaysOn-Verfügbarkeitsgruppen in Azure](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Festlegen des Zugriffs auf den Listener
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Der Schwerpunkt dieses Artikels liegt auf dem Erstellen eines Listeners, der einen ILB verwendet. Wenn Sie einen öffentlichen oder externen Listener benötigen, finden Sie die entsprechenden Schritte im Artikel zum Einrichten eines [externen Listeners](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Erstellen von VM-Endpunkten mit Lastenausgleich und Direct Server Return
Zunächst erstellen Sie einen ILB durch Ausführen des Skripts weiter unten in diesem Abschnitt.

Erstellen Sie einen Endpunkt mit Lastenausgleich für jeden virtuellen Computer mit einem Azure-Replikat. Wenn Replikate in mehreren Regionen vorhanden sind, muss sich jedes Replikat für diese Region im selben Clouddienst im selben virtuellen Azure-Netzwerk befinden. Das Erstellen von Verfügbarkeitsgruppenreplikaten, die sich über mehrere Azure-Regionen erstrecken, erfordert die Konfiguration mehrerer virtueller Netzwerke. Weitere Informationen zum Konfigurieren von Verbindungen zwischen virtuellen Netzwerken finden Sie unter [Konfigurieren der Verbindung zwischen virtuellen Netzwerken](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Wechseln Sie im Azure-Portal zu jedem virtuellen Computer, der ein Replikat hostet, um die Details anzuzeigen.

2. Klicken Sie auf die Registerkarte **Endpunkte** für jeden virtuellen Computer.

3. Stellen Sie sicher, dass die Werte für **Name** und **Öffentlicher Port** des gewünschten Listenerendpunkts nicht bereits verwendet werden. In dem Beispiel in diesem Abschnitt lautet der Name *MyEndpoint* und der Port *1433*.

4. Laden Sie auf dem lokalen Client das aktuelle [PowerShell-Modul](https://azure.microsoft.com/downloads/) herunter, und installieren Sie es.

5. Starten Sie Azure PowerShell.  
    Eine neue PowerShell-Sitzung wird geöffnet, und die Azure-Verwaltungsmodule werden geladen.

6. Führen Sie `Get-AzurePublishSettingsFile`aus. Dieses Cmdlet leitet Sie zu einem Browser weiter, damit Sie eine Datei mit Veröffentlichungseinstellungen in ein lokales Verzeichnis herunterladen können. Möglicherweise werden Sie aufgefordert, die Anmeldeinformationen für Ihr Azure-Abonnement einzugeben.

7. Führen Sie den folgenden Befehl `Import-AzurePublishSettingsFile` mit dem Pfad der Datei mit Veröffentlichungseinstellungen aus, die Sie heruntergeladen haben:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    Nachdem die Datei mit Veröffentlichungseinstellungen importiert wurde, können Sie Ihr Azure-Abonnement in der PowerShell-Sitzung verwalten.

8. Weisen Sie für *ILB* eine statische IP-Adresse zu. Untersuchen Sie das aktuelle virtuelle Netzwerk, indem Sie den folgenden Befehl ausführen:

        (Get-AzureVNetConfig).XMLConfiguration
9. Notieren Sie sich den unter *Subnetz* angegebenen Namen für das Subnetz, in dem sich die zum Hosten der Replikate verwendeten VMs befinden. Dieser Name wird für den $SubnetName-Parameter des Skripts verwendet.

10. Notieren Sie sich den für *VirtualNetworkSite* angegebenen Namen und den ersten Wert *AddressPrefix* für das Subnetz, in dem sich die zum Hosten der Replikate verwendeten VMs befinden. Suchen Sie nach einer verfügbaren IP-Adresse, indem Sie diese beiden Werte im Befehl `Test-AzureStaticVNetIP` verwenden und *AvailableAddresses* untersuchen. Wenn z.B. der Name des virtuellen Netzwerks *MyVNet* lautet und der Adressbereich des Subnetzes bei *172.16.0.128* beginnt, werden die verfügbaren Adressen mit folgendem Befehl aufgelistet:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Wählen Sie eine der verfügbaren Adressen aus, und verwenden Sie diese in dem Skript im nächsten Schritt als $ILBStaticIP-Parameter.

12. Kopieren Sie das folgende PowerShell-Skript in einen Text-Editor, und legen Sie die Variablenwerte passend für Ihre Umgebung fest. Für einige Parameter sind Standardwerte angegeben.  

    Bei vorhandenen Bereitstellungen, die Affinitätsgruppen verwenden, kann kein ILB hinzugefügt werden. Weitere Informationen zu den Voraussetzungen für den internen Lastenausgleich finden Sie unter [Interner Load Balancer (Übersicht)](../../../load-balancer/load-balancer-internal-overview.md).

    Wenn Ihre Verfügbarkeitsgruppe sich über mehrere Azure-Regionen erstreckt, müssen Sie das Skript zudem in jedem Datencenter für den Clouddienst und die Knoten ausführen, die sich in diesem Datencenter befinden.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the virtual network
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load-balanced endpoint for each node in $AGNodes by using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

13. Nachdem Sie die Variablen festgelegt haben, kopieren Sie das Skript zum Ausführen aus dem Text-Editor in Ihre PowerShell-Sitzung. Wenn die Eingabeaufforderung weiterhin **>>** anzeigt, drücken Sie erneut die EINGABETASTE, um die Skriptausführung zu starten.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Sicherstellen der Installation von KB2854082, falls erforderlich
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Öffnen der Firewallports in Verfügbarkeitsgruppenknoten
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Erstellen des Verfügbarkeitsgruppenlisteners

Erstellen Sie den Verfügbarkeitsgruppenlistener in zwei Schritten. Zunächst erstellen Sie die Clientzugriffspunkt-Clusterressource und konfigurieren Abhängigkeiten. Konfigurieren Sie als zweiten Schritt die Clusterressourcen mit PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Erstellen des Clientzugriffspunkts und Konfigurieren der Clusterabhängigkeiten
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurieren der Clusterressourcen mit PowerShell
1. Für den ILB muss die IP-Adresse des internen Load Balancers verwendet werden, der zuvor erstellt wurde. Verwenden Sie das folgende Skript, um diese IP-Adresse in PowerShell abzurufen:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Kopieren Sie in einer der VMs das unten stehende PowerShell-Skript für Ihr Betriebssystem in einen Text-Editor, und legen Sie dann die Variablen auf die zuvor notierten Werte fest.

    Verwenden Sie für Windows Server 2012 oder spätere Versionen das folgende Skript:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    Verwenden Sie für Windows Server 2008 R2 das folgende Skript:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. Nachdem Sie die Variablen festgelegt haben, öffnen Sie ein Windows PowerShell-Fenster mit erhöhten Rechten, und fügen Sie das Skript aus dem Text-Editor in Ihre PowerShell-Sitzung ein, um es auszuführen. Wenn die Eingabeaufforderung weiterhin **>>** anzeigt, drücken Sie erneut die EINGABETASTE, um die Skriptausführung zu starten.

4. Wiederholen Sie die vorherigen Schritte für jede VM.  
    Mit diesem Skript wird die IP-Adressressource mit der IP-Adresse des Clouddiensts konfiguriert, und weitere Parameter wie der Testport werden festgelegt. Wenn die IP-Adressressource online geschaltet wird, kann sie auf Abrufvorgänge am Testport reagieren, die von dem zuvor erstellten Endpunkt mit Load Balancer ausgehen.

## <a name="bring-the-listener-online"></a>Onlineschalten des Listeners
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Aufgaben nach der Erstellung
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>Testen des Verfügbarkeitsgruppenlisteners (innerhalb desselben virtuellen Netzwerks)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Nächste Schritte
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
