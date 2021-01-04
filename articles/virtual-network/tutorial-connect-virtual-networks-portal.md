---
title: 'Herstellen von Verbindungen zwischen virtuellen Netzwerken mittels VNET-Peering – Tutorial: Azure-Portal'
description: In diesem Tutorial erfahren Sie, wie Sie im Azure-Portal durch Peering virtueller Netzwerke eine Verbindung zwischen virtuellen Netzwerken herstellen.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/22/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: e95441aab6c8ce7de37ba5f6b08d5f7d54e13347
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017915"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Tutorial: Herstellen einer Verbindung zwischen virtuellen Netzwerken mittels Peering über das Azure-Portal

Sie können durch Peering virtueller Netzwerke Verbindungen zwischen virtuellen Netzwerken herstellen. Diese virtuellen Netzwerke können sich in derselben oder in unterschiedlichen Regionen (auch als globales VNET-Peering bezeichnet) befinden. Sobald ein Peering zwischen virtuellen Netzwerken eingerichtet wurde, können Ressourcen in beiden virtuellen Netzwerken untereinander mit der gleichen Latenz und Bandbreite kommunizieren, als befänden sie sich im selben virtuellen Netzwerk. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen zweier virtueller Netzwerke
> * Herstellen einer Verbindung zwischen zwei virtuellen Netzwerken mit einem Peering virtueller Netzwerke
> * Bereitstellen eines virtuellen Computers (VM) in jedem virtuellen Netzwerk
> * Kommunikation zwischen VMs

Dieser Artikel kann auch mit der [Azure-Befehlszeilenschnittstelle](tutorial-connect-virtual-networks-cli.md) oder mit [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md) durchgearbeitet werden.

Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.

## <a name="log-in-to-azure"></a>Anmelden an Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-virtual-networks"></a>Erstellen virtueller Netzwerke

1. Klicken Sie im Azure-Portal auf **Ressource erstellen**.
2. Wählen Sie **Netzwerk** und anschließend **Virtuelles Netzwerk** aus.
3. Geben Sie auf der Registerkarte **Grundlagen** die folgenden Informationen ein, oder wählen Sie sie aus, und übernehmen Sie die Standardwerte für die übrigen Einstellungen:

    |Einstellung|value|
    |---|---|
    |Subscription| Wählen Sie Ihr Abonnement aus.|
    |Resource group| Klicken Sie auf **Neu erstellen**, und geben Sie *myResourceGroup* ein.|
    |Region| Wählen Sie **USA, Osten** aus.|
    |Name|myVirtualNetwork1|

4. Geben Sie auf der Registerkarte **IP-Adressen** im Feld **Adressbereich** die Adresse „10.0.0.0/16“ ein. Klicken Sie unten auf die Schaltfläche **Subnetz hinzufügen**, und geben Sie *Subnet1* für **Subnetzname** und „10.0.0.0/24“ für **Subnetzadressbereich** ein.
5. Wählen Sie **Überprüfen + erstellen** und anschließend **Erstellen** aus.
   
5. Führen Sie die Schritte 1 bis 5 mit den folgenden Änderungen erneut aus:

    |Einstellung|value|
    |---|---|
    |Name|myVirtualNetwork2|
    |Adressraum|10.1.0.0/16|
    |Resource group| Wählen Sie **Vorhandene verwenden** und dann **myResourceGroup**.|
    |Subnetzname | Subnet2|
    |Subnetzadressbereich|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>Einrichten eines Peerings von virtuellen Netzwerken

1. Beginnen Sie oben im Azure-Portal im Feld „Suchen“ mit der Eingabe von *MyVirtualNetwork1*. Wenn **myVirtualNetwork1** in den Suchergebnissen angezeigt wird, wählen Sie es aus.
2. Klicken Sie unter **Einstellungen** auf **Peerings**, und wählen Sie dann **Hinzufügen** aus, wie in der folgenden Abbildung gezeigt wird:

    ![Erstellen eines Peerings](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus, übernehmen Sie die Standardwerte für die übrigen Einstellungen, und klicken Sie auf **OK**.

    |Einstellung|value|
    |---|---|
    |Name des Peerings von myVirtualNetwork1 zum virtuellen Remotenetzwerk|myVirtualNetwork1-myVirtualNetwork2: Beim Laden der ersten Seite wird hier der Text „remote virtual network“ (virtuelles Remotenetzwerk) angezeigt. Wenn Sie das virtuelle Remotenetzwerk ausgewählt haben, wird der Text „remote virtual network“ durch den Namen des virtuellen Remotenetzwerks ersetzt.|
    |Subscription| Wählen Sie Ihr Abonnement aus.|
    |Virtuelles Netzwerk|myVirtualNetwork2: Wählen Sie zum Auswählen des virtuellen Netzwerks *myVirtualNetwork2* die Option **Virtuelles Netzwerk** und anschließend **myVirtualNetwork2 (myResourceGroup)** aus. Sie können ein virtuelles Netzwerk in derselben Region oder in einer anderen Region auswählen.|
    |Name des Peerings von myVirtualNetwork2 zu myVirtualNetwork1|myVirtualNetwork2-myVirtualNetwork1|

    ![Peeringeinstellungen](./media/tutorial-connect-virtual-networks-portal/peering-settings-bidirectional.png)

    Der **PEERINGSTATUS** lautet *Verbunden*, wie in der folgenden Abbildung gezeigt wird:

    ![Peeringstatus](./media/tutorial-connect-virtual-networks-portal/peering-status-connected.png)

    Wenn der Status nicht angezeigt wird, aktualisieren Sie Ihren Browser.

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Erstellen Sie eine VM in jedem virtuellen Netzwerk, damit in einem späteren Schritt die Kommunikation untereinander möglich ist.

### <a name="create-the-first-vm"></a>Erstellen des ersten virtuellen Computers

1. Klicken Sie im Azure-Portal auf **Ressource erstellen**.
2. Wählen Sie **Compute** und dann **Windows Server 2016 Datacenter**. Sie können ein anderes Betriebssystem auswählen, bei den übrigen Schritten wird jedoch davon ausgegangen, dass Sie **Windows Server 2016 Datacenter** ausgewählt haben. 
3. Geben Sie die folgenden Informationen für **Grundlagen** ein, oder wählen Sie sie aus, übernehmen Sie die Standardwerte für die übrigen Einstellungen, und klicken Sie auf **Erstellen**:

    |Einstellung|value|
    |---|---|
    |Resource group| Wählen Sie **Vorhandene verwenden** und dann **myResourceGroup**.|
    |Name|myVm1|
    |Location| Wählen Sie **USA, Osten** aus.|
    |Benutzername| Geben Sie den gewünschten Benutzernamen ein.|
    |Kennwort| Geben Sie das gewünschte Kennwort ein. Das Kennwort muss mindestens zwölf Zeichen lang sein und die [definierten Anforderungen an die Komplexität](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) erfüllen.|
   
4. Wählen Sie eine VM-Größe für die Option **Größe** aus.
5. Wählen Sie unter **Netzwerk** die folgenden Werte aus:

    |Einstellung|value|
    |---|---|
    |Virtuelles Netzwerk| myVirtualNetwork1: Falls dieses Element noch nicht ausgewählt ist, wählen Sie die Option **Virtuelles Netzwerk** und dann **myVirtualNetwork1** aus.|
    |Subnet| Subnet1: Falls dieses Element noch nicht ausgewählt ist, wählen Sie die Option **Subnetz** und dann **Subnet1** aus.|
   
6. Wählen Sie **Netzwerk** aus. Wählen Sie **Ausgewählte Ports zulassen** für die Option **Öffentliche Eingangsports** aus. Wählen Sie **RDP**  für die Option **Eingangsports auswählen** aus. 

7. Wählen Sie in der unteren linken Ecke die Schaltfläche **Review + Create** (Überprüfen + erstellen) aus, um die VM-Bereitstellung zu starten.

### <a name="create-the-second-vm"></a>Erstellen des zweiten virtuellen Computers

Führen Sie die Schritte 1 bis 6 mit den folgenden Änderungen erneut aus:

|Einstellung|value|
|---|---|
|Name | myVm2|
|Virtuelles Netzwerk | myVirtualNetwork2|

Die Erstellung der VMs kann einige Minuten dauern. Fahren Sie mit den restlichen Schritten erst fort, nachdem beide VMs erstellt wurden.

## <a name="communicate-between-vms"></a>Kommunikation zwischen VMs

1. Beginnen Sie oben im Portal im Feld *Suchen* mit der Eingabe von *myVm1*. Wenn **myVm1** in den Suchergebnissen angezeigt wird, wählen Sie diese Angabe aus.
2. Erstellen Sie eine Remotedesktopverbindung mit der VM *myVm1*, indem Sie auf **Verbinden** klicken, wie in der folgenden Abbildung gezeigt:

    ![Herstellen der Verbindung mit dem virtuellen Computer](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Öffnen Sie die heruntergeladene RDP-Datei, um eine Verbindung mit dem virtuellen Computer herzustellen. Wenn Sie dazu aufgefordert werden, wählen Sie **Verbinden** aus.
4. Geben Sie den Benutzernamen und das Kennwort ein, wie beim Erstellen des virtuellen Computers angegeben. (Unter Umständen müssen Sie auf **Weitere Optionen** und dann auf **Anderes Konto verwenden** klicken, um die Anmeldeinformationen anzugeben, die Sie beim Erstellen des virtuellen Computers eingegeben haben.) Klicken Sie anschließend auf **OK**.
5. Während des Anmeldevorgangs wird unter Umständen eine Zertifikatwarnung angezeigt. Wählen Sie **Ja** aus, um mit dem Herstellen der Verbindung fortzufahren.
6. In einem späteren Schritt wird Ping genutzt, um von der VM *myVm1* aus mit der VM *myVm2* zu kommunizieren. Ping verwendet standardmäßig das Internet Control Message-Protokoll (ICMP), das für die Windows-Firewall verweigert wird. Aktivieren Sie auf der VM *myVm1* ICMP über die Windows-Firewall, damit Sie diese VM in einem späteren Schritt mit PowerShell über *myVm2* per Ping erreichen können:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```
    
    In diesem Tutorial wird zwar Ping für die Kommunikation zwischen virtuellen Computern verwendet, für Produktionsbereitstellungen wird jedoch davon abgeraten, ICMP-Datenverkehr durch die Windows-Firewall zuzulassen.

7. Um eine Verbindung mit der VM *myVm2* herzustellen, geben Sie an einer Eingabeaufforderung auf der VM *myVm1* den folgenden Befehl ein:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Da Sie den Ping für *myVm1* aktiviert haben, können Sie jetzt anhand der IP-Adresse pingen:

    ```
    ping 10.0.0.4
    ```
    
9. Trennen Sie Ihre RDP-Sitzungen auf *myVm1* und *myVm2*.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe mit allen ihren Ressourcen, wenn Sie sie nicht mehr benötigen: 

1. Geben Sie im oben im Portal im Feld *Suche* die Zeichenfolge **myResourceGroup** ein. Wenn **myResourceGroup** in den Suchergebnissen angezeigt wird, wählen Sie diese Angabe aus.
2. Wählen Sie die Option **Ressourcengruppe löschen**.
3. Geben Sie für *Geben Sie den Ressourcengruppennamen ein:* den Namen **myResourceGroup** ein, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie mittels Peering eine Verbindung zwischen zwei Netzwerken in der gleichen Azure-Region herstellen. Sie können auch virtuelle Netzwerke in unterschiedlichen [ unterstützten Regionen](virtual-network-manage-peering.md#cross-region) und [verschiedenen Azure-Abonnements](create-peering-different-subscriptions.md#portal) durch Peering verbinden und [Netzwerke vom Typ „Nabe und Speiche“](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) mit Peering erstellen. Weitere Informationen zum Peering in virtuellen Netzwerken finden Sie unter [Peering in virtuellen Netzwerken](virtual-network-peering-overview.md) und [Erstellen, Ändern oder Löschen eines Peerings virtueller Netzwerke](virtual-network-manage-peering.md).

Wie Sie über ein VPN eine Verbindung zwischen Ihrem eigenen Computer und einem virtuellen Netzwerk herstellen und mit Ressourcen in einem virtuellen Netzwerk oder in durch Peerings verbundenen virtuellen Netzwerken interagieren, erfahren Sie unter [Konfigurieren einer Point-to-Site-Verbindung mit einem VNET unter Verwendung der nativen Azure-Zertifikatauthentifizierung: Azure-Portal](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
