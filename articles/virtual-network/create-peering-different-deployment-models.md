---
title: Erstellen eines Peerings virtueller Azure-Netzwerke mithilfe verschiedener Bereitstellungsmodelle im selben Abonnement | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein Peering zwischen virtuellen Netzwerken erstellen, die mithilfe unterschiedlicher Bereitstellungsmodelle im selben Azure-Abonnement erstellt wurden.
services: virtual-network
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: kumud;anavin
ms.openlocfilehash: 56474ee56051c3b0b7482e81b0174b7945537654
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64694714"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Erstellen eines Peerings virtueller Netzwerke mithilfe verschiedener Bereitstellungsmodelle im selben Abonnement

In diesem Tutorial erfahren Sie, wie Sie ein Peering zwischen virtuellen Netzwerken erstellen, die in verschiedenen Bereitstellungsmodellen eingerichtet wurden. Beide virtuellen Netzwerke müssen zum selben Abonnement gehören. Das Peering zweier virtueller Netzwerke ermöglicht es Ressourcen, in verschiedenen virtuellen Netzwerken untereinander mit derselben Bandbreite und Latenz zu kommunizieren, als befänden sie sich im selben virtuellen Netzwerk. Weitere Informationen hierzu finden Sie unter [Peering virtueller Netzwerke](virtual-network-peering-overview.md).

Die Schritte zum Erstellen eines Peerings virtueller Netzwerke sind je nachdem unterschiedlich, ob sich die virtuellen Netzwerke im selben oder unterschiedlichen Abonnements befinden und mit welchem [Azure-Bereitstellungsmodell](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) die virtuellen Netzwerke erstellt werden. Erfahren Sie, wie Sie ein Peering virtueller Netzwerke in anderen Szenarien erstellen, indem Sie in der folgenden Tabelle auf das gewünschte Szenario klicken:

|Azure-Bereitstellungsmodell  | Azure-Abonnement  |
|--------- |---------|
|[Beide mit Resource Manager](tutorial-connect-virtual-networks-portal.md) |Identisch|
|[Beide mit Resource Manager](create-peering-different-subscriptions.md) |Unterschiedlich|
|[Eines mit Resource Manager, das andere klassisch](create-peering-different-deployment-models-subscriptions.md) |Unterschiedlich|

Zwischen zwei virtuellen Netzwerken, die über das klassische Bereitstellungsmodell bereitgestellt wurden, kann kein Peering eingerichtet werden. Wenn Sie eine Verbindung zwischen virtuellen Netzwerken herstellen möchten, die beide über das klassische Bereitstellungsmodell erstellt wurden, können Sie dazu eine Azure [VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-Instanz verwenden.

In diesem Tutorial wird ein Peering für virtuelle Netzwerke in der gleichen Region durchgeführt. Sie können virtuelle Netzwerke auch in verschiedenen [unterstützten Regionen](virtual-network-manage-peering.md#cross-region) per Peering verknüpfen. Es wird empfohlen, sich mit den [Peeringanforderungen und -einschränkungen](virtual-network-manage-peering.md#requirements-and-constraints) vertraut zu machen, bevor Sie das Peering von virtuellen Netzwerken nutzen.

Ein Peering in Netzwerken lässt sich mithilfe des Azure-Portals, mit der Azure-[Befehlszeilenschnittstelle](#cli) (CLI), mit Azure [PowerShell](#powershell) oder mit einer Azure Resource Manager-Vorlage erstellen. Klicken Sie auf einen der vorherigen Tool-Links. So gelangen Sie direkt zu den Anleitungen zum Erstellen von Peerings in virtuellen Netzwerken mit dem Tool Ihrer Wahl.

## <a name="create-peering---azure-portal"></a>Erstellen eines Peerings: Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Das Konto, mit dem Sie sich anmelden, braucht die erforderlichen Berechtigungen, um ein Peering in virtuellen Netzwerken zu erstellen. Eine Liste der Berechtigungen finden Sie im Abschnitt „Berechtigungen“ unter [Erstellen, Ändern oder Löschen eines Peerings virtueller Netzwerke](virtual-network-manage-peering.md#requirements-and-constraints).
2. Klicken Sie auf **+ Neu**, dann auf **Network**, und klicken Sie zuletzt auf **Virtuelles Netzwerk**.
3. Geben Sie auf dem Blatt **Virtuelles Netzwerk erstellen** die Werte für die folgenden Einstellungen an, oder wählen Sie sie aus, und klicken Sie anschließend auf **Erstellen**:
    - **Name**: *myVNet1*
    - **Adressraum:** *10.0.0.0/16*
    - **Subnetzname**: *Standard*
    - **Subnetzadressbereich:** *10.0.0.0/24*
    - **Abonnement**: Wählen Sie Ihr Abonnement aus.
    - **Ressourcengruppe**: Klicken Sie auf **Neue erstellen**, und geben Sie *myResourceGroup* ein.
    - **Standort**: *USA, Osten*
4. Klicken Sie auf **+ NEU**. Geben Sie im Feld **Marketplace durchsuchen** *Virtuelles Netzwerk* ein. Wenn **Virtuelle Netzwerke** in den Suchergebnissen angezeigt wird, klicken Sie darauf.
5. Wählen Sie auf dem Blatt **Virtuelles Netzwerk** im Feld **Bereitstellungsmodell auswählen** die Option **Klassisch** aus, und klicken Sie dann auf **Erstellen**.
6. Geben Sie auf dem Blatt **Virtuelles Netzwerk erstellen** die Werte für die folgenden Einstellungen an, oder wählen Sie sie aus, und klicken Sie anschließend auf **Erstellen**:
    - **Name**: *myVnet2*
    - **Adressraum:** *10.1.0.0/16*
    - **Subnetzname**: *Standard*
    - **Subnetzadressbereich:** *10.1.0.0/24*
    - **Abonnement**: Wählen Sie Ihr Abonnement aus.
    - **Ressourcengruppe**: Wählen Sie **Vorhandene verwenden** und dann *myResourceGroup* aus.
    - **Standort**: *USA, Osten*
7. Geben Sie im oben im Portal im Feld **Ressourcen suchen** *myResourceGroup* ein. Klicken Sie auf **myResourceGroup**, wenn sie in den Suchergebnissen angezeigt wird. Es öffnet sich ein Blatt für die Ressourcengruppe **myResourceGroup**. Die Ressourcengruppe enthält die beiden virtuellen Netzwerke, die in den vorherigen Schritten erstellt wurden.
8. Klicken Sie auf **myVNet1**.
9. Klicken Sie auf dem sich öffnenden Blatt **myVnet1** links in der vertikalen Liste der Optionen auf **Peerings**.
10. Klicken Sie auf dem sich öffnenden Blatt **myVnet1 – Peerings** auf **+ Hinzufügen**
11. Geben Sie auf dem sich öffnenden Blatt **Peering hinzufügen** die folgenden Optionen ein, oder wählen Sie sie aus, und klicken Sie auf **OK**:
     - **Name**: *myVnet1ToMyVnet2*
     - **Bereitstellungsmodell für das virtuelle Netzwerk:**  Wählen Sie **Klassisch** aus.
     - **Abonnement**: Wählen Sie Ihr Abonnement aus.
     - **Virtuelles Netzwerk:**  Klicken Sie auf **Virtuelles Netzwerk auswählen**, und klicken Sie dann auf **myVnet2**.
     - **Zugriff auf virtuelles Netzwerk zulassen:** Stellen Sie sicher, dass **Aktiviert** ausgewählt ist.
    In diesem Tutorial werden keine anderen Einstellungen verwendet. Weitere Informationen zu allen Peeringeinstellungen finden Sie unter [Create a peering (Erstellen eines Peerings)](virtual-network-manage-peering.md#create-a-peering).
12. Nachdem Sie zuvor auf **OK** geklickt haben, wird das Blatt **Peering hinzufügen** geschlossen, und Sie gelangen wieder zum Blatt **myVnet1 – Peerings**. Nach wenigen Sekunden wird das von Ihnen erstellte Peering auf dem Blatt angezeigt. In der Spalte **Peeringstatus** wird für das Peering **myVnet1ToMyVnet2** der Status **Verbunden** angezeigt.

    Das Peering ist nun hergestellt. Alle Azure-Ressourcen, die Sie in einem der virtuellen Netzwerke erstellen, sind in der Lage, miteinander über ihre IP-Adressen zu kommunizieren. Wenn Sie die standardmäßige Azure-Namensauflösung für virtuelle Netzwerke verwenden, können die Ressourcen in den virtuellen Netzwerken Namen nicht netzwerkübergreifend auflösen. Wenn Sie Namen netzwerkübergreifend in einem Peering auflösen möchten, müssen Sie einen eigenen DNS-Server erstellen. Weitere Informationen finden Sie im Artikel [Namensauflösung mithilfe eines eigenen DNS-Servers](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
13. **Optional:** Obwohl das Erstellen virtueller Computer in diesem Tutorial nicht behandelt wird, können Sie in jedem virtuellen Netzwerk virtuelle Computer erstellen und eine Verbindung zwischen ihnen herstellen, um die Konnektivität zu überprüfen.
14. **Optional:** Zum Löschen der Ressourcen, die Sie in diesem Tutorial erstellt haben, führen Sie die Schritte im Abschnitt [Löschen von Ressourcen](#delete-portal) dieses Artikels aus.

## <a name="cli"></a>Erstellen eines Peerings: Azure CLI

Führen Sie die folgenden Schritte mithilfe der klassischen Azure CLI und der Azure CLI aus. Sie können die Schritte von Azure Cloud Shell aus ausführen, indem Sie einfach die Schaltfläche **Ausprobieren** in einem der folgenden Schritte auswählen oder indem Sie die [klassischen CLI](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) und [CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) installieren und die Befehle auf Ihrem lokalen Computer ausführen.

1. Wenn Sie Cloud Shell verwenden, fahren Sie mit Schritt 2 fort, da Cloud Shell Sie automatisch bei Azure anmeldet. Starten Sie eine Befehlssitzung, und melden Sie sich mit dem Befehl `azure login` bei Azure an.
2. Führen Sie die CLI in Dienstverwaltungsmodus aus, indem Sie den Befehl `azure config mode asm` eingeben.
3. Geben Sie den folgenden Befehl ein, um das virtuelle Netzwerk (klassisch) zu erstellen:

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. Führen Sie das folgende Bash-CLI-Skript mit der CLI aus (nicht mit der klassischen CLI). Optionen zum Ausführen von Bash-CLI-Skripts auf einem Windows-Computer finden Sie unter [Installieren der Azure CLI unter Windows](/cli/azure/install-azure-cli-windows).

   ```azurecli-interactive
   #!/bin/bash

   # Create a resource group.
   az group create \
     --name myResourceGroup \
     --location eastus

   # Create the virtual network (Resource Manager).
   az network vnet create \
     --name myVnet1 \
     --resource-group myResourceGroup \
     --location eastus \
     --address-prefix 10.0.0.0/16
   ```

5. Erstellen Sie über die CLI ein Peering zwischen zwei virtuellen Netzwerken, die mit verschiedenen Bereitstellungsmodellen erstellt wurden. Kopieren Sie das folgende Skript in einen Texteditor auf Ihrem PC. Ersetzen Sie `<subscription id>` durch Ihre Abonnement-ID. Sollten Sie Ihre Abonnement-ID nicht kennen, geben Sie den Befehl `az account show` ein. Der Wert für **id** in der Ausgabe ist Ihre Abonnement-ID. Fügen Sie das geänderte Skript in Ihre CLI-Sitzung ein, und drücken Sie dann `Enter`.

   ```azurecli-interactive
   # Get the ID for VNet1.
   vnet1Id=$(az network vnet show \
     --resource-group myResourceGroup \
     --name myVnet1 \
     --query id --out tsv)

   # Peer VNet1 to VNet2.
   az network vnet peering create \
     --name myVnet1ToMyVnet2 \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
     --allow-vnet-access
   ```

6. Nachdem das Skript ausgeführt wurde, überprüfen Sie das Peering für das virtuelle Netzwerk (Ressourcen-Manager). Kopieren Sie den folgenden Befehl, fügen Sie ihn in Ihre CLI-Sitzung ein, und drücken Sie dann `Enter`:

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   Die Ausgabe zeigt in der Spalte **PeeringState** den Status **Connected**.

   Alle Azure-Ressourcen, die Sie in einem der virtuellen Netzwerke erstellen, sind in der Lage, miteinander über ihre IP-Adressen zu kommunizieren. Wenn Sie die standardmäßige Azure-Namensauflösung für virtuelle Netzwerke verwenden, können die Ressourcen in den virtuellen Netzwerken Namen nicht netzwerkübergreifend auflösen. Wenn Sie Namen netzwerkübergreifend in einem Peering auflösen möchten, müssen Sie einen eigenen DNS-Server erstellen. Weitere Informationen finden Sie im Artikel [Namensauflösung mithilfe eines eigenen DNS-Servers](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
7. **Optional:** Obwohl das Erstellen virtueller Computer in diesem Tutorial nicht behandelt wird, können Sie in jedem virtuellen Netzwerk virtuelle Computer erstellen und eine Verbindung zwischen ihnen herstellen, um die Konnektivität zu überprüfen.
8. **Optional:** Zum Löschen der Ressourcen, die Sie in diesem Tutorial erstellt haben, führen Sie die Schritte im Abschnitt [Löschen von Ressourcen](#delete-cli) in diesem Artikel aus.

## <a name="powershell"></a>Erstellen eines Peerings: PowerShell

1. Installieren Sie die neueste Version der PowerShell-Module [Azure](https://www.powershellgallery.com/packages/Azure) und [Az](https://www.powershellgallery.com/packages/Az/). Wenn Sie noch nicht mit Azure PowerShell vertraut sind, lesen Sie die [Übersicht über Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Starten Sie eine PowerShell-Sitzung.
3. Melden Sie sich in PowerShell bei Azure an, indem Sie den Befehl `Add-AzureAccount` eingeben. Das Konto, mit dem Sie sich anmelden, braucht die erforderlichen Berechtigungen, um ein Peering in virtuellen Netzwerken zu erstellen. Eine Liste der Berechtigungen finden Sie im Abschnitt „Berechtigungen“ unter [Erstellen, Ändern oder Löschen eines Peerings virtueller Netzwerke](virtual-network-manage-peering.md#requirements-and-constraints).
4. Um ein virtuelles Netzwerk (klassisch) mit PowerShell zu erstellen, müssen Sie eine neue Netzwerkkonfigurationsdatei erstellen oder eine vorhandene ändern. Erfahren Sie, wie Sie [Netzwerkkonfigurationsdateien exportieren, aktualisieren und importieren](virtual-networks-using-network-configuration-file.md). Die Datei muss das folgende **VirtualNetworkSite**-Element für das in diesem Tutorial verwendete virtuelle Netzwerk enthalten:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Das Importieren einer geänderten Netzwerkkonfigurationsdatei kann zu Änderungen an vorhandenen virtuellen Netzwerken (klassisch) in Ihrem Abonnement führen. Vergewissern Sie sich, dass Sie nur das vorherige virtuelle Netzwerk hinzufügen und keine in Ihrem Abonnement vorhandenen virtuellen Netzwerke ändern oder entfernen.
5. Melden Sie sich bei Azure an, um mithilfe des Befehls `Connect-AzAccount` das virtuelle Netzwerk (Resource Manager) zu erstellen. Das Konto, mit dem Sie sich anmelden, braucht die erforderlichen Berechtigungen, um ein Peering in virtuellen Netzwerken zu erstellen. Eine Liste der Berechtigungen finden Sie im Abschnitt „Berechtigungen“ unter [Erstellen, Ändern oder Löschen eines Peerings virtueller Netzwerke](virtual-network-manage-peering.md#requirements-and-constraints).
6. Erstellen Sie eine Ressourcengruppe und ein virtuelles Netzwerk (Ressourcen-Manager). Kopieren Sie das Skript, fügen Sie es in PowerShell ein, und drücken Sie dann `Enter`.

    ```powershell
    # Create a resource group.
      New-AzResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. Erstellen Sie ein Peering zwischen zwei virtuellen Netzwerken, die mit verschiedenen Bereitstellungsmodellen erstellt wurden. Kopieren Sie das folgende Skript in einen Texteditor auf Ihrem PC. Ersetzen Sie `<subscription id>` durch Ihre Abonnement-ID. Sollten Sie Ihre Abonnement-ID nicht kennen, geben Sie den Befehl `Get-AzSubscription` ein, um sie anzuzeigen. Der Wert für **Id** in der Ausgabe ist Ihre Abonnement-ID. Um das Skript auszuführen, kopieren Sie das geänderte Skript aus Ihrem Texteditor. Klicken Sie dann in der PowerShell-Sitzung mit der rechten Maustaste, und drücken Sie anschließend `Enter`.

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Nachdem das Skript ausgeführt wurde, überprüfen Sie das Peering für das virtuelle Netzwerk (Ressourcen-Manager). Kopieren Sie den folgenden Befehl, fügen Sie ihn in Ihre PowerShell-Sitzung ein, und drücken Sie dann `Enter`:

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Die Ausgabe zeigt in der Spalte **PeeringState** den Status **Connected**.

    Alle Azure-Ressourcen, die Sie in einem der virtuellen Netzwerke erstellen, sind in der Lage, miteinander über ihre IP-Adressen zu kommunizieren. Wenn Sie die standardmäßige Azure-Namensauflösung für virtuelle Netzwerke verwenden, können die Ressourcen in den virtuellen Netzwerken Namen nicht netzwerkübergreifend auflösen. Wenn Sie Namen netzwerkübergreifend in einem Peering auflösen möchten, müssen Sie einen eigenen DNS-Server erstellen. Weitere Informationen finden Sie im Artikel [Namensauflösung mithilfe eines eigenen DNS-Servers](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

9. **Optional:** Obwohl das Erstellen virtueller Computer in diesem Tutorial nicht behandelt wird, können Sie in jedem virtuellen Netzwerk virtuelle Computer erstellen und eine Verbindung zwischen ihnen herstellen, um die Konnektivität zu überprüfen.
10. **Optional:** Zum Löschen der Ressourcen, die Sie in diesem Tutorial erstellt haben, führen Sie die Schritte im Abschnitt [Löschen von Ressourcen](#delete-powershell) in diesem Artikel aus.

## <a name="delete"></a>Löschen von Ressourcen

Wenn Sie dieses Tutorial abgeschlossen haben, möchten Sie die Ressourcen, die Sie in diesem Tutorial erstellt haben, möglicherweise wieder löschen, damit keine Nutzungsgebühren anfallen. Durch das Löschen einer Ressourcengruppe werden auch alle Ressourcen in dieser Ressourcengruppe gelöscht.

### <a name="delete-portal"></a>Azure-Portal

1. Geben Sie in das Suchfeld im Portal **myResourceGroup** ein. Klicken Sie in den Suchergebnissen auf **myResourceGroup**.
2. Klicken Sie auf dem Blatt **myResourceGroup** auf das **Löschsymbol**.
3. Um den Löschvorgang zu bestätigen, geben Sie in das Feld **TYPE THE RESOURCE GROUP NAME** (Ressourcengruppennamen eingeben) **myResourceGroup** ein, und klicken Sie dann auf **Löschen**.

### <a name="delete-cli"></a>Azure-Befehlszeilenschnittstelle

1. Löschen Sie das virtuelle Netzwerk (Resource Manager) mit der Azure CLI und dem folgenden Befehl:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Löschen Sie das virtuelle Netzwerk (klassisch) mit der klassischen Befehlszeilenschnittstelle und den folgenden Befehlen:

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. Geben Sie den folgenden Befehl zum Löschen des virtuellen Netzwerks (Ressourcen-Manager) ein:

    ```powershell
    Remove-AzResourceGroup -Name myResourceGroup -Force
    ```

2. Um ein virtuelles Netzwerk (klassisch) mit PowerShell zu löschen, müssen Sie eine vorhandene Netzwerkkonfigurationsdatei ändern. Erfahren Sie, wie Sie [Netzwerkkonfigurationsdateien exportieren, aktualisieren und importieren](virtual-networks-using-network-configuration-file.md). Entfernen Sie das folgende „VirtualNetworkSite“-Element für das in diesem Tutorial verwendete virtuelle Netzwerk:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Das Importieren einer geänderten Netzwerkkonfigurationsdatei kann zu Änderungen an vorhandenen virtuellen Netzwerken (klassisch) in Ihrem Abonnement führen. Vergewissern Sie sich, dass Sie nur das vorherige virtuelle Netzwerk entfernen und keine anderen in Ihrem Abonnement vorhandenen virtuellen Netzwerke ändern oder entfernen.

## <a name="next-steps"></a>Nächste Schritte

- Machen Sie sich eingehend mit den wichtigen [Einschränkungen und Verhalten eines Peerings in virtuellen Netzwerken](virtual-network-manage-peering.md#requirements-and-constraints) vertraut, bevor Sie ein Peering in virtuellen Netzwerken für die Produktion erstellen.
- Erfahren Sie alles über [Peering-Einstellungen in virtuellen Netzwerken](virtual-network-manage-peering.md#create-a-peering).
- Erfahren Sie, wie Sie eine [Hub-Spoke-Netzwerktopologie ](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) mit einem Peering in virtuellen Netzwerken erstellen.
