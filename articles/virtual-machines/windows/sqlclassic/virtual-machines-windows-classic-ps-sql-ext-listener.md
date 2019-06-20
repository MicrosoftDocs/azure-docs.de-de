---
title: Konfigurieren eines externen Listeners für Always On-Verfügbarkeitsgruppen | Microsoft Docs
description: Dieses Tutorial führt Sie durch die Schritte zum Erstellen eines AlwaysOn-Verfügbarkeitsgruppenlisteners in Azure, der über die öffentliche virtuelle IP-Adresse des zugeordneten Clouddiensts extern zugänglich ist.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 89623adbddce07cbc3c3ead811f5174d108c9b0e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62101624"
---
# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Konfigurieren eines externen Listeners für AlwaysOn-Verfügbarkeitsgruppen in Azure
> [!div class="op_single_selector"]
> * [Interner Listener](../classic/ps-sql-int-listener.md)
> * [Externer Listener](../classic/ps-sql-ext-listener.md)
> 
> 

In diesem Thema erfahren Sie, wie Sie einen Listener für eine AlwaysOn-Verfügbarkeitsgruppe konfigurieren, die extern über das Internet zugänglich ist. Dies wird durch das Zuordnen der **öffentlichen virtuellen IP-Adresse (VIP)** des Clouddiensts zum Listener ermöglicht.

> [!IMPORTANT] 
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager-Bereitstellungen und klassische Bereitstellungen](../../../azure-resource-manager/resource-manager-deployment-model.md). Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells.

Ihre Verfügbarkeitsgruppe kann Replikate enthalten, die ausschließlich lokal, ausschließlich in Azure oder sowohl lokal als auch in Azure verfügbar sind (Hybridkonfigurationen). Azure-Replikate können sich innerhalb derselben Region oder in mehreren Regionen befinden, wobei mehrere virtuelle Netzwerke (VNets) verwendet werden. Bei den nachfolgenden Schritten wird davon ausgegangen, dass bereits eine [Verfügbarkeitsgruppe konfiguriert wurde](../classic/portal-sql-alwayson-availability-groups.md), Sie jedoch noch keinen Listener konfiguriert haben.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Richtlinien und Einschränkungen für externe Listener
Beachten Sie die folgenden Richtlinien für den Verfügbarkeitsgruppenlistener in Azure, wenn Sie die Bereitstellung mit der öffentlichen VIP-Adresse des Clouddiensts durchführen:

* Der Verfügbarkeitsgruppenlistener wird unter Windows Server 2008 R2, Windows Server 2012 und Windows Server 2012 R2 unterstützt.
* Die Clientanwendung darf sich nicht in demselben Clouddienst befinden wie Ihre Verfügbarkeitsgruppen-VMs. Azure bietet keine Unterstützung für Direct Server Return, wenn sich Client und Server im selben Clouddienst befinden.
* Standardmäßig zeigen die Schritte in diesem Artikel, wie Sie einen Listener konfigurieren, der die virtuelle IP-Adresse (VIP) des Clouddiensts verwendet. Allerdings ist es möglich, mehrere VIP-Adressen für den Clouddienst zu reservieren und zu erstellen. Daher können Sie anhand der Schritte in diesem Artikel mehrere Listener erstellen, die jeweils einer anderen VIP-Adresse zugeordnet sind. Informationen zum Erstellen von mehreren VIP-Adressen finden Sie unter [Mehrere VIPs pro Clouddienst](../../../load-balancer/load-balancer-multivip.md).
* Wenn Sie einen Listener für eine Hybridumgebung erstellen, muss das lokale Netzwerk zusätzlich zum Standort-zu-Standort-VPN mit dem virtuellen Azure-Netzwerk auch mit dem öffentlichen Internet verbunden sein. Im Azure-Subnetz ist der Verfügbarkeitsgruppenlistener nur über die öffentliche IP-Adresse des jeweiligen Clouddiensts erreichbar.
* Nicht unterstützt wird die Erstellung eines externen Listeners in einem Clouddienst, in dem auch ein interner Listener vorhanden ist, der den internen Load Balancer (ILB) verwendet.

## <a name="determine-the-accessibility-of-the-listener"></a>Festlegen des Zugriffs auf den Listener
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Der Schwerpunkt dieses Artikels liegt auf dem Erstellen eines Listeners, der den **externen Lastenausgleich**verwendet. Sie finden einen Listener, der privat für das virtuelle Netzwerk eingesetzt wird, in der Version dieses Artikels, in der die Schritte zum Einrichten eines [Listeners mit ILB](../classic/ps-sql-int-listener.md) erläutert werden.

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Erstellen von VM-Endpunkten mit Lastenausgleich und Direct Server Return
Der externe Lastenausgleich verwendet die öffentliche virtuelle IP-Adresse des Clouddiensts, der Ihre virtuellen Computer hostet. In diesem Fall müssen Sie den Lastenausgleich also nicht erstellen oder konfigurieren.

Sie müssen einen Endpunkt mit Lastenausgleich für jeden virtuellen Computer mit einem Azure-Replikat erstellen. Wenn Sie Replikate in mehreren Regionen haben, muss sich jedes Replikat für diese Region im selben Clouddienst im selben VNet befinden. Das Erstellen von Verfügbarkeitsgruppenreplikaten, die sich über mehrere Azure-Regionen erstrecken, erfordert die Konfiguration mehrerer VNets. Weitere Informationen zum Konfigurieren von VNet-übergreifenden Verbindungen finden Sie unter [Konfigurieren von Verbindungen zwischen VNets](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Navigieren Sie im Azure-Portal zu jedem virtuellen Computer, der ein Replikat hostet, und zeigen Sie die Details an.
2. Klicken Sie auf die Registerkarte **Endpunkte** für jeden virtuellen Computer.
3. Stellen Sie sicher, dass die Werte für **Name** und **Öffentlicher Port** des gewünschten Listenerendpunkts nicht bereits verwendet werden. Im folgenden Beispiel ist der Name "MyEndpoint" und der Port "1433".
4. Laden Sie auf dem lokalen Client [das aktuelle PowerShell-Modul](https://azure.microsoft.com/downloads/)herunter, und installieren Sie es.
5. Starten Sie **Azure PowerShell**. Eine neue PowerShell-Sitzung wird geöffnet, und die Azure-Verwaltungsmodule werden geladen.
6. Führen Sie **Get-AzurePublishSettingsFile**aus. Dieses Cmdlet leitet Sie zu einem Browser weiter, damit Sie eine Datei mit Veröffentlichungseinstellungen in ein lokales Verzeichnis herunterladen können. Möglicherweise werden Sie aufgefordert, die Anmeldeinformationen für Ihr Azure-Abonnement einzugeben.
7. Führen Sie den Befehl **Import-AzurePublishSettingsFile** mit dem Pfad der Datei mit Veröffentlichungseinstellungen aus, die Sie heruntergeladen haben:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    Nachdem die Datei mit Veröffentlichungseinstellungen importiert wurde, können Sie Ihr Azure-Abonnement in der PowerShell-Sitzung verwalten.
    
1. Kopieren Sie das unten stehende PowerShell-Skript in einen Text-Editor, und legen Sie die Variablenwerte gemäß Ihrer Umgebung fest (für einige Parameter wurden die Standardwerte angegeben). Wenn Ihre Verfügbarkeitsgruppe sich über mehrere Azure-Regionen erstreckt, müssen Sie das Skript zudem in jedem Datencenter für den Clouddienst und die Knoten ausführen, die sich in diesem Datencenter befinden.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. Nachdem Sie die Variablen festgelegt haben, kopieren Sie das Skript zur Ausführung aus dem Text-Editor in Ihre Azure PowerShell-Sitzung. Wenn die Aufforderung weiterhin >> anzeigt, geben Sie erneut ENTER ein, um die Skriptausführung zu starten.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Sicherstellen der Installation von KB2854082, falls erforderlich
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Öffnen der Firewallports in Verfügbarkeitsgruppenknoten
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Erstellen des Verfügbarkeitsgruppenlisteners

Erstellen Sie den Verfügbarkeitsgruppenlistener in zwei Schritten. Zunächst erstellen Sie die Clientzugriffspunkt-Clusterressource und konfigurieren Abhängigkeiten. Im zweiten Schritt konfigurieren Sie die Clusterressourcen mit PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Erstellen des Clientzugriffspunkts und Konfigurieren der Clusterabhängigkeiten
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurieren der Clusterressourcen mit PowerShell
1. Für den externen Lastenausgleich müssen Sie die öffentliche virtuelle IP-Adresse des Clouddiensts abrufen, der Ihre Replikate enthält. Melden Sie sich im Azure-Portal an. Navigieren Sie zu dem Clouddienst, der Ihren virtuellen Verfügbarkeitsgruppencomputer enthält. Öffnen Sie die Ansicht **Dashboard** .
2. Beachten Sie die Adresse unter **Öffentliche virtuelle IP-Adresse (VIP)** . Wenn Ihre Lösung VNets umfasst, wiederholen Sie diesen Schritt für jeden Clouddienst mit einem virtuellen Computer, der ein Replikat hostet.
3. Kopieren Sie auf einem der virtuellen Computer das unten stehende PowerShell-Skript in einen Text-Editor, und legen Sie die Variablen auf die zuvor notierten Werte fest.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Nachdem Sie die Variablen festgelegt haben, öffnen Sie ein Windows PowerShell-Fenster mit erhöhten Rechten, und kopieren Sie das Skript aus dem Text-Editor zur Ausführung in Ihre Azure PowerShell-Sitzung. Wenn die Aufforderung weiterhin >> anzeigt, geben Sie erneut ENTER ein, um die Skriptausführung zu starten.
5. Wiederholen Sie diesen Schritt für jeden virtuellen Computer. Mit diesem Skript wird die IP-Adressressource mit der IP-Adresse des Clouddienst konfiguriert. Außerdem werden weitere Parameter wie z. B. der Testport festgelegt. Wenn die IP-Adressressource online geschaltet wird, kann sie auf Abrufvorgänge am Testport reagieren, die vom Endpunkt mit Lastenausgleich ausgehen, der zuvor in diesem Tutorial erstellt wurde.

## <a name="bring-the-listener-online"></a>Onlineschalten des Listeners
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Aufgaben nach der Erstellung
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testen des Verfügbarkeitsgruppenlisteners (innerhalb desselben VNets)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Testen des Verfügbarkeitsgruppenlisteners (über das Internet)
Um von außerhalb des virtuellen Netzwerks auf den Listener zuzugreifen, müssen Sie einen externen/öffentlichen Lastenausgleich verwenden (wie in diesem Thema beschrieben) – keinen internen Lastenausgleich, auf den nur innerhalb des gleichen virtuellen Netzwerks zugegriffen werden kann. In der Verbindungszeichenfolge geben Sie den Namen des Clouddiensts an. Bei einem Clouddienst mit dem Namen *mycloudservice* lautet die sqlcmd-Anweisung beispielsweise wie folgt:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Im Gegensatz zum vorherigen Beispiel muss die SQL-Authentifizierung verwendet werden, da der Aufrufer die Windows-Authentifizierung nicht über das Internet verwenden kann. Weitere Informationen finden Sie unter [AlwaysOn-Verfügbarkeitsgruppen in Azure VM: Client-Konnektivitätsszenarien](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Stellen Sie bei Verwendung der SQL-Authentifizierung sicher, dass Sie für beide Replikate dieselbe Anmeldung erstellen. Weitere Informationen zur Problembehandlung von Anmeldungen bei Verfügbarkeitsgruppen finden Sie unter [How to map logins or use contained SQL database user to connect to other replicas and map to availability databases](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx)(in englischer Sprache).

Wenn sich die AlwaysOn-Replikate in unterschiedlichen Subnetzen befinden, müssen Clients **MultisubnetFailover=True** in der Verbindungszeichenfolge angeben. Dies führt zu parallelen Verbindungsversuchen mit Replikaten in unterschiedlichen Subnetzen. Beachten Sie, dass zu diesem Szenario eine regionsübergreifende AlwaysOn-Verfügbarkeitsgruppenbereitstellung gehört.

## <a name="next-steps"></a>Nächste Schritte
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

