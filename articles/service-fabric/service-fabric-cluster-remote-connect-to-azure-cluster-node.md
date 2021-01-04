---
title: Herstellen einer Remoteverbindung mit einem Azure Service Fabric-Clusterknoten
description: Hier erfahren Sie, wie Sie eine Remoteverbindung mit einer Skalierungsgruppeninstanz (Service Fabric-Clusterknoten) herstellen.
ms.topic: conceptual
ms.date: 03/23/2018
ms.openlocfilehash: 98d573af4fc2026134e75d4caf24a09e57e52c87
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012543"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Herstellen einer Remoteverbindung mit einer VM-Skalierungsgruppeninstanz oder einem Clusterknoten
In einem Service Fabric-Cluster unter Azure richtet jeder Clusterknotentyp, den Sie definieren, [eine separate VM-Skalierungsgruppe](service-fabric-cluster-nodetypes.md) ein.  Sie können eine Remoteverbindung mit bestimmten Skalierungsgruppeninstanzen (Clusterknoten) herstellen.  Im Gegensatz zu Einzelinstanz-VMs besitzen Skalierungsgruppeninstanzen keine eigenen virtuellen IP-Adressen. Daher kann es schwierig sein, nach einer IP-Adresse und einem Port zum Herstellen einer Remoteverbindung mit einer bestimmten Instanz zu suchen.

Um eine IP-Adresse und einen Port für eine Remoteverbindung mit einer bestimmten Instanz zu finden, führen Sie die folgenden Schritte aus.

1. Rufen Sie die NAT-Eingangsregeln für das Remotedesktopprotokoll (RDP) ab.

    Normalerweise hat jeder Knotentyp, der in Ihrem Cluster definiert ist, eine eigene virtuelle IP-Adresse und einen dedizierten Lastenausgleich. Standardmäßig wird der Lastenausgleich für einen Knotentyp anhand des folgenden Formats benannt: *LB-{Clustername}-{Knotentyp}* , also z.B. *LB-meinCluster-FrontEnd*. 
    
    Wählen Sie im Azure-Portal auf der Seite für den Lastenausgleich **Einstellungen** > **NAT-Eingangsregeln** aus: 

    ![Screenshot einer Lastenausgleichsseite im Azure-Portal. Im Menü auf der linken Seite unter EINSTELLUNGEN ist „NAT-Regeln für eingehenden Datenverkehr“ ausgewählt.](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    Der folgende Screenshot zeigt die NAT-Eingangsregeln für den Knotentyp „FrontEnd“: 

    ![Screenshot, der die NAT-Regeln für eingehenden Datenverkehr für ein Lastenausgleichsmodul zeigt. Name, IP-Version, Ziel und Dienst werden für jede Regel aufgeführt.](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Für jeden Knoten wird die IP-Adresse in der Spalte **DESTINATION** angezeigt. Die Spalte **TARGET** gibt die Instanz der Skalierungsgruppe an, und die Spalte **SERVICE** enthält die Portnummer. Für Remoteverbindungen werden die Ports den einzelnen Knoten in aufsteigender Reihenfolge beginnend mit Port 3389 zugeordnet.

    Sie finden auch die NAT-Eingangsregeln im Abschnitt `Microsoft.Network/loadBalancers` der Resource Manager-Vorlage für den Cluster.
    
2. Um den Eingangsport als Ziel für die Portzuordnung für einen Knoten zu bestätigen, können Sie auf seine Regel klicken und den Wert **Zielport** überprüfen. Der folgende Screenshot zeigt die NAT-Eingangsregel für den Knoten **FrontEnd (Instance 1)** aus dem vorherigen Schritt. Beachten Sie, dass die (eingehende) Portnummer zwar 3390 lautet, der Zielport aber dem Port 3389, also dem Port für den RDP-Dienst auf dem Ziel, zugeordnet ist.  

    ![Zielportzuordnung](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Für Windows-Cluster lautet der Zielport standardmäßig 3389 und ist dem RDP-Dienst auf dem Zielknoten zugeordnet. Für Linux-Cluster ist der Zielport 22 und dem SSH-Dienst (Secure Shell) zugeordnet.

3. Stellen Sie eine Remoteverbindung mit dem spezifischen Knoten (Skalierungsgruppeninstanz) her. Sie können den Benutzernamen und das Kennwort, die Sie beim Erstellen des Clusters erstellt haben, oder beliebige andere Anmeldeinformationen, die Sie konfiguriert haben, verwenden. 

    Der folgende Screenshot zeigt die Verwendung einer Remotedesktopverbindung mit dem Knoten **FrontEnd (Instance 1)** in einem Windows-Cluster:
    
    ![Remotedesktopverbindung](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    Auf Linux-Knoten können Sie die Verbindung mit SSH herstellen (im folgenden Beispiel werden der Einfachheit halber dieselbe IP-Adresse und derselbe Port verwendet):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Lesen Sie als Nächstes die folgenden Artikel:
* Weitere Informationen finden Sie unter [Übersicht über das Feature „Deploy Anywhere“ (umgebungsunabhängige Bereitstellung) und ein Vergleich mit Clustern, die von Azure verwaltet werden](service-fabric-deploy-anywhere.md).
* Erfahren Sie mehr über [Clustersicherheit](service-fabric-cluster-security.md).
* [Aktualisieren der RDP-Portbereichswerte](./scripts/service-fabric-powershell-change-rdp-port-range.md) für Cluster-VMs nach der Bereitstellung
* [Ändern von Administratorbenutzername und -kennwort](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) für Cluster-VMs

