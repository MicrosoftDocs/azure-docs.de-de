---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 276ddf0a70fa450451cd3ddc78c7610c4ab1edc1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178048"
---
Der Verfügbarkeitsgruppenlistener umfasst eine IP-Adresse und einen Netzwerknamen, über die die SQL Server-Verfügbarkeitsgruppe lauscht. Gehen Sie wie folgt vor, um den Verfügbarkeitsgruppenlistener zu erstellen:

1. <a name="getnet"></a>Rufen Sie den Namen der Clusternetzwerk-Ressource ab.

    a. Stellen Sie eine RDP-Verbindung mit dem virtuellen Azure-Computer her, der das primäre Replikat hostet. 

    b. Öffnen Sie den Failovercluster-Manager.

    c. Wählen Sie den Knoten **Netzwerke** aus, und notieren Sie den Netzwerknamen des Clusters. Verwenden Sie diesen Namen im PowerShell-Skript in der Variablen `$ClusterNetworkName` . In der folgenden Abbildung lautet der Name des Clusternetzwerks **Cluster Network 1**:

   ![Name des Clusternetzwerks](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>Fügen Sie den Clientzugriffspunkt hinzu.  
    Der Clientzugriffspunkt ist der Netzwerkname, der von den Anwendungen zum Herstellen der Verbindung mit den Datenbanken einer Verfügbarkeitsgruppe verwendet wird. Erstellen Sie den Clientzugriffspunkt im Failovercluster-Manager.

    a. Erweitern Sie den Clusternamen, und klicken Sie dann auf **Rollen**.

    b. Klicken Sie im Bereich **Rollen** mit der rechten Maustaste auf den Verfügbarkeitsgruppennamen, und wählen Sie dann **Ressource hinzufügen** > **Clientzugriffspunkt** aus.

   ![Clientzugriffspunkt](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. Erstellen Sie im Feld **Name** einen Namen für diesen neuen Listener. 
   Der Name für den neuen Listener ist der Netzwerkname, den Anwendungen beim Herstellen einer Verbindung mit Datenbanken in der SQL Server-Verfügbarkeitsgruppe verwenden.

    d. Klicken Sie zum Abschließen der Listenererstellung zweimal auf **Weiter** und dann auf **Fertig stellen**. Schalten Sie den Listener oder die Ressource jetzt noch nicht online.

1. Schalten Sie die Clusterrolle der Verfügbarkeitsgruppe offline. Klicken Sie im **Failovercluster-Manager** unter **Rollen** mit der rechten Maustaste auf die Rolle, und klicken Sie dann auf **Rolle beenden**.

1. <a name="congroup"></a>Konfigurieren Sie die IP-Ressource für die Verfügbarkeitsgruppe.

    a. Klicken Sie auf die Registerkarte **Ressourcen**, und erweitern Sie dann den erstellten Clientzugriffspunkt.  
    Der Clientzugriffspunkt ist offline.

   ![Clientzugriffspunkt](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Klicken Sie mit der rechten Maustaste auf die IP-Ressource, und klicken Sie dann auf „Eigenschaften“. Notieren Sie den Namen der IP-Adresse, und verwenden Sie ihn in der Variablen `$IPResourceName` im PowerShell-Skript.

    c. Klicken Sie unter **IP-Adresse** auf **Statische IP-Adresse**. Legen Sie die IP-Adresse auf die gleiche Adresse fest, die Sie beim Festlegen der Adresse für den Lastenausgleich im Azure-Portal verwendet haben.

   ![IP-Ressource](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>Richten Sie die Abhängigkeit der SQL Server-Verfügbarkeitsgruppenressource vom Clientzugriffspunkt ein.

    a. Klicken Sie im Failovercluster-Manager auf **Rollen** und dann auf Ihre Verfügbarkeitsgruppe.

    b. Klicken Sie auf der Registerkarte **Ressourcen** unter **Sonstige Ressourcen** mit der rechten Maustaste auf die Verfügbarkeitsressourcengruppe, und klicken Sie dann auf **Eigenschaften**. 

    c. Fügen Sie auf der Registerkarte „Abhängigkeiten“ den Namen der Clientzugriffspunkt-Ressource (Listener) hinzu.

   ![IP-Ressource](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Klicken Sie auf **OK**.

1. <a name="listname"></a>Legen Sie fest, dass die Clientzugriffspunkt-Ressource von der IP-Adresse abhängig ist.

    a. Klicken Sie im Failovercluster-Manager auf **Rollen** und dann auf Ihre Verfügbarkeitsgruppe. 

    b. Klicken Sie auf der Registerkarte **Ressourcen** unter **Servername** mit der rechten Maustaste auf den Clientzugriffspunkt, und klicken Sie dann auf **Eigenschaften**. 

   ![IP-Ressource](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Klicken Sie auf die Registerkarte **Abhängigkeiten** . Stellen Sie sicher, dass die IP-Adresse eine Abhängigkeit ist. Wenn dies nicht der Fall ist, legen Sie eine Abhängigkeit für die IP-Adresse fest. Wenn mehrere Ressourcen aufgeführt sind, überprüfen Sie, ob die IP-Adressen OR-Abhängigkeiten aufweisen (statt AND-Abhängigkeiten). Klicken Sie auf **OK**. 

   ![IP-Ressource](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >Sie können überprüfen, ob die Abhängigkeiten ordnungsgemäß konfiguriert sind. Wechseln Sie im Failovercluster-Manager zu „Rollen“, klicken Sie mit der rechten Maustaste auf die Verfügbarkeitsgruppe, klicken Sie auf **Weitere Aktionen**, und klicken Sie dann auf **Abhängigkeitsbericht anzeigen**. Wenn die Abhängigkeiten ordnungsgemäß konfiguriert sind, ist die Verfügbarkeitsgruppe vom Netzwerknamen und der Netzwerkname von der IP-Adresse abhängig. 


1. <a name="setparam"></a>Legen Sie die Clusterparameter in PowerShell fest.

   a. Kopieren Sie das folgende PowerShell-Skript in eine Ihrer SQL Server-Instanzen. Aktualisieren Sie die Variablen für Ihre Umgebung.

   - `$ListenerILBIP` ist die IP-Adresse, die Sie im Azure Load Balancer für den Verfügbarkeitsgruppenlistener angelegt haben.
    
   - `$ListenerProbePort` ist der Port, den Sie im Azure Load Balancer für den Verfügbarkeitsgruppenlistener konfiguriert haben.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>
  
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Legen Sie die Clusterparameter fest, indem Sie das PowerShell-Skript auf einem der Clusterknoten ausführen.  

   > [!NOTE]
   > Falls sich Ihre SQL Server-Instanzen in unterschiedlichen Regionen befinden, muss das PowerShell-Skript zweimal ausgeführt werden. Verwenden Sie beim ersten Mal die Elemente `$ListenerILBIP` und `$ListenerProbePort` der ersten Region. Verwenden Sie beim zweiten Mal die Elemente `$ListenerILBIP` und `$ListenerProbePort` der zweiten Region. Der Name des Clusternetzwerks und der Cluster-IP-Ressourcenname sind auch in jeder Region unterschiedlich.

1. Schalten Sie die Clusterrolle der Verfügbarkeitsgruppe online. Klicken Sie im **Failovercluster-Manager** unter **Rollen** mit der rechten Maustaste auf die Rolle, und klicken Sie dann auf **Rolle starten**.

Wiederholen Sie bei Bedarf die oben aufgeführten Schritte, um die Clusterparameter für die IP-Adresse des WSFC-Clusters festzulegen.

1. Rufen Sie den Namen der IP-Adresse des WSFC-Clusters ab. Suchen Sie den **Servernamen** im **Failovercluster-Manager** unter **Hauptressourcen des Clusters**.

1. Klicken Sie erst mit der rechten Maustaste auf **IP-Adresse** und anschließend mit der linken auf **Eigenschaften**.

1. Kopieren Sie den **Namen** der IP-Adresse. Z.B. `Cluster IP Address`. 

1. <a name="setwsfcparam"></a>Legen Sie die Clusterparameter in PowerShell fest.
  
   a. Kopieren Sie das folgende PowerShell-Skript in eine Ihrer SQL Server-Instanzen. Aktualisieren Sie die Variablen für Ihre Umgebung.

   - `$ClusterCoreIP` ist die IP-Adresse, die Sie im Azure Load Balancer für die WSFC-Hauptclusterressource angelegt haben. Sie unterscheidet sich von der IP-Adresse des Verfügbarkeitsgruppenlisteners.

   - `$ClusterProbePort` ist der Port, den Sie im Azure Load Balancer für den WSFC-Integritätstest konfiguriert haben. Er unterscheidet sich vom Test des Verfügbarkeitsgruppenlisteners.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.
  
   Import-Module FailoverClusters
  
   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Legen Sie die Clusterparameter fest, indem Sie das PowerShell-Skript auf einem der Clusterknoten ausführen.  

>[!WARNING]
>Der Port für den Integritätstest des Verfügbarkeitsgruppenlisteners muss sich vom Port für den Integritätstest der IP-Adresse des Hauptclusters unterscheiden. In diesen Beispielen ist der Listenerport 59999 und IP-Adresse des Hauptclusters 58888. Für beide Ports ist eine eingehende Firewallregel erforderlich.