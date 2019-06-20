---
title: Herstellen einer Remoteverbindung mit einem Azure Service Fabric-Clusterknoten | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie eine Remoteverbindung mit einer Skalierungsgruppeninstanz (Service Fabric-Clusterknoten) herstellen.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo
ms.openlocfilehash: 4cc2d6355a0147c33048f1c2c27a3648b9223db4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62110922"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Herstellen einer Remoteverbindung mit einer VM-Skalierungsgruppeninstanz oder einem Clusterknoten
In einem Service Fabric-Cluster unter Azure richtet jeder Clusterknotentyp, den Sie definieren, [eine separate VM-Skalierungsgruppe](service-fabric-cluster-nodetypes.md) ein.  Sie können eine Remoteverbindung mit bestimmten Skalierungsgruppeninstanzen (Clusterknoten) herstellen.  Im Gegensatz zu Einzelinstanz-VMs besitzen Skalierungsgruppeninstanzen keine eigenen virtuellen IP-Adressen. Daher kann es schwierig sein, nach einer IP-Adresse und einem Port zum Herstellen einer Remoteverbindung mit einer bestimmten Instanz zu suchen.

Um eine IP-Adresse und einen Port für eine Remoteverbindung mit einer bestimmten Instanz zu finden, führen Sie die folgenden Schritte aus.

1. Rufen Sie die NAT-Eingangsregeln für das Remotedesktopprotokoll (RDP) ab.

    Normalerweise hat jeder Knotentyp, der in Ihrem Cluster definiert ist, eine eigene virtuelle IP-Adresse und einen dedizierten Lastenausgleich. Standardmäßig wird der Lastenausgleich für einen Knotentyp im folgenden Format benannt: *LB-{Clustername}-{Knotentyp}* , z.B. *LB-mycluster-FrontEnd*. 
    
    Wählen Sie im Azure-Portal auf der Seite für den Lastenausgleich **Einstellungen** > **NAT-Eingangsregeln** aus: 

    ![NAT-Eingangsregeln für den Lastenausgleich](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    Der folgende Screenshot zeigt die NAT-Eingangsregeln für den Knotentyp „FrontEnd“: 

    ![NAT-Eingangsregeln für den Lastenausgleich](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

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

