---
title: 'Erstellen eines Peerings in virtuellen Azure-Netzwerken: Resource Manager – verschiedene Abonnements'
titlesuffix: Azure Virtual Network
description: Erfahren Sie, wie Sie ein Peering zwischen virtuellen Netzwerken erstellen, die mithilfe von Ressourcen-Manager in unterschiedlichen Azure-Abonnements erstellt wurden.
services: virtual-network
documentationcenter: ''
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2019
ms.author: anavin
ms.openlocfilehash: cf414cf08771090990775d124e27222e51f786e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122013"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Erstellen eines Peerings virtueller Netzwerke gemäß dem Ressourcen-Manager-Modell in verschiedenen Abonnements

In diesem Tutorial erfahren Sie, wie Sie ein Peering zwischen virtuellen Netzwerken erstellen, die über Ressourcen-Manager eingerichtet wurden. Die virtuellen Netzwerke befinden sich in unterschiedlichen Abonnements. Das Peering zweier virtueller Netzwerke ermöglicht es Ressourcen, in verschiedenen virtuellen Netzwerken untereinander mit derselben Bandbreite und Latenz zu kommunizieren, als befänden sie sich im selben virtuellen Netzwerk. Weitere Informationen hierzu finden Sie unter [Peering virtueller Netzwerke](virtual-network-peering-overview.md).

Die Schritte zum Erstellen eines Peerings virtueller Netzwerke sind je nachdem unterschiedlich, ob sich die virtuellen Netzwerke im selben oder unterschiedlichen Abonnements befinden und mit welchem [Azure-Bereitstellungsmodell](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) die virtuellen Netzwerke erstellt werden. Erfahren Sie, wie Sie ein Peering virtueller Netzwerke in anderen Szenarien erstellen, indem Sie in der folgenden Tabelle das gewünschte Szenario auswählen:

|Azure-Bereitstellungsmodell  | Azure-Abonnement  |
|--------- |---------|
|[Beide mit Resource Manager](tutorial-connect-virtual-networks-portal.md) |identisch|
|[Eines mit Resource Manager, das andere klassisch](create-peering-different-deployment-models.md) |identisch|
|[Eines mit Resource Manager, das andere klassisch](create-peering-different-deployment-models-subscriptions.md) |Unterschiedlich|

Ein Peering zweier virtueller Netzwerke, die über das klassische Bereitstellungsmodell bereitgestellt wurden, ist nicht möglich. Wenn Sie eine Verbindung zwischen virtuellen Netzwerken herstellen möchten, die beide über das klassische Bereitstellungsmodell erstellt wurden, können Sie dazu eine Azure [VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-Instanz verwenden.

In diesem Tutorial wird ein Peering für virtuelle Netzwerke in der gleichen Region durchgeführt. Sie können virtuelle Netzwerke auch in verschiedenen [unterstützten Regionen](virtual-network-manage-peering.md#cross-region) per Peering verknüpfen. Es wird empfohlen, sich mit den [Peeringanforderungen und -einschränkungen](virtual-network-manage-peering.md#requirements-and-constraints) vertraut zu machen, bevor Sie das Peering von virtuellen Netzwerken nutzen.

Ein Peering in Netzwerken lässt sich mithilfe des [Azure-Portals](#portal), mit der [Azure-Befehlszeilenschnittstelle](#cli) (CLI), mit [Azure PowerShell](#powershell) oder mit der [Azure Resource Manager-Vorlage](#template) erstellen. Klicken Sie auf einen der vorherigen Toollinks. So gelangen Sie direkt zu den Anleitungen zum Erstellen von Peerings virtueller Netzwerke mit dem Tool Ihrer Wahl.

## <a name="portal"></a>Erstellen eines Peerings: Azure-Portal

Befolgen Sie die Schritte im Abschnitt zur CLI und zu PowerShell in diesem Artikel, wenn sich die virtuellen Netzwerke, für die Sie das Peering herstellen möchten, in Abonnements befinden, die verschiedenen Azure Active Directory-Mandanten zugeordnet sind. Das Portal verfügt nicht über die Unterstützung für das Peering von virtuellen Netzwerken, die zu Abonnements aus unterschiedlichen Active Directory-Mandanten gehören. 

Beachten Sie, dass es in Cloud Shell Einschränkungen hinsichtlich des Wechselns von Abonnements und Mandanten gibt, wegen denen VNet-Peering oder globales VNet-Peering zwischen virtuellen Netzwerken, die zu Abonnements in verschiedenen Azure Active Directory-Mandanten gehören, nicht funktioniert. Verwenden Sie PowerShell oder die CLI.

In den folgenden Schritten werden für jedes Abonnement unterschiedliche Konten genutzt. Wenn Sie ein Konto verwenden, das über Berechtigungen für beide Abonnements verfügt, können Sie dasselbe Konto für alle Schritte verwenden. Überspringen Sie die Schritte zum Abmelden vom Portal und zum Zuweisen weiterer Benutzerberechtigungen für die virtuellen Netzwerke.

1. Melden Sie sich als *UserA* beim [Azure-Portal](https://portal.azure.com) an. Das Konto, mit dem Sie sich anmelden, muss über die Berechtigungen verfügen, die zum Erstellen eines Peerings virtueller Netzwerke erforderlich sind. Eine Liste der Berechtigungen finden Sie im Abschnitt „Berechtigungen“ unter [Erstellen, Ändern oder Löschen eines Peerings virtueller Netzwerke](virtual-network-manage-peering.md#permissions).
2. Klicken Sie auf **+ Ressource erstellen**, auf **Netzwerk** und dann auf **Virtuelles Netzwerk**.
3. Wählen Sie die nachfolgenden Beispielwerte für die folgenden Einstellungen aus, oder geben Sie sie ein, und klicken Sie dann auf **Erstellen**:
    - **Name**: *myVnetA*
    - **Adressraum:** *10.0.0.0/16*
    - **Subnetzname**: *Standard*
    - **Subnetzadressbereich:** *10.0.0.0/24*
    - **Abonnement**: Wählen Sie Abonnement A aus.
    - **Ressourcengruppe:** Wählen Sie **Neue erstellen** aus, und geben Sie *myResourceGroupA* ein.
    - **Standort:** *USA, Osten*
4. Geben Sie im oben im Portal *myVnetA* im Feld **Ressourcen suchen** ein. Klicken Sie auf **myVnetA**, wenn es in den Suchergebnissen angezeigt wird. 
5. Klicken Sie in der vertikalen Liste der Optionen auf der linken Seite auf **Zugriffssteuerung (IAM)** .
6. Wählen Sie unter **myVnetA – Zugriffssteuerung (IAM)** die Option **+ Rollenzuweisung hinzufügen** aus.
7. Wählen Sie im Feld **Rolle** die Option **Netzwerkmitwirkender** aus.
8. Wählen Sie im Feld **Auswählen** die Option *UserB* aus, oder geben Sie die E-Mail-Adresse von UserB ein, um ihn zu suchen.
9. Wählen Sie **Speichern** aus.
10. Klicken Sie unter **myVnetA – Zugriffssteuerung (IAM)** in der vertikalen Liste der Optionen auf der linken Seite auf **Eigenschaften**. Kopieren Sie die **Ressourcen-ID**, die in einem späteren Schritt verwendet wird. Die Ressourcen-ID Ausgabe sieht in etwa wie das folgende Beispiel aus: `/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA`.
11. Melden Sie sich als UserA vom Portal ab, und melden Sie sich als UserB an.
12. Führen Sie die Schritte 2-3 erneut aus, und geben Sie bei Schritt 3 die folgenden Werte an:

    - **Name**: *myVnetB*
    - **Adressraum:** *10.1.0.0/16*
    - **Subnetzname**: *Standard*
    - **Subnetzadressbereich:** *10.1.0.0/24*
    - **Abonnement**: Wählen Sie Abonnement B aus.
    - **Ressourcengruppe:** Wählen Sie **Neue erstellen** aus, und geben Sie *myResourceGroupB* ein.
    - **Standort:** *USA, Osten*

13. Geben Sie im oben im Portal *myVnetB* im Feld **Ressourcen suchen** ein. Klicken Sie auf **myVnetB**, wenn es in den Suchergebnissen angezeigt wird.
14. Klicken Sie unter **myVnetB** in der vertikalen Liste der Optionen auf der linken Seite auf **Eigenschaften**. Kopieren Sie die **Ressourcen-ID**, die in einem späteren Schritt verwendet wird. Die Ressourcen-ID Ausgabe sieht in etwa wie das folgende Beispiel aus: `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`.
15. Klicken Sie unter **myVnetB** auf **Zugriffssteuerung (IAM)** , und führen Sie dann die Schritte 5 bis 10 für myVnetB aus. Geben Sie dabei in Schritt 8 **UserA** ein.
16. Melden Sie sich als UserB vom Portal ab, und melden Sie sich als UserA an.
17. Geben Sie im oben im Portal *myVnetA* im Feld **Ressourcen suchen** ein. Klicken Sie auf **myVnetA**, wenn es in den Suchergebnissen angezeigt wird.
18. Klicken Sie auf **myVnetA**.
19. Wählen Sie unter **EINSTELLUNGEN** die Option **Peerings** aus.
20. Klicken Sie unter **myVnetA – Peerings** auf **+ Hinzufügen**
21. Geben Sie unter **Peering hinzufügen** die folgenden Optionen ein, oder wählen Sie sie aus, und klicken Sie dann auf **OK**:
     - **Name**: *myVnetAToMyVnetB*
     - **Bereitstellungsmodell für das virtuelle Netzwerk:**  Wählen Sie **Ressourcen-Manager**.
     - **I know my resource ID** (Ich kenne meine Ressourcen-ID): Aktivieren Sie dieses Kontrollkästchen.
     - **Ressourcen-ID:** Geben Sie die Ressourcen-ID aus Schritt 14 ein.
     - **Zugriff auf virtuelles Netzwerk zulassen:** Stellen Sie sicher, dass **Aktiviert** ausgewählt ist.
    In diesem Tutorial werden keine anderen Einstellungen verwendet. Weitere Informationen zu allen Peeringeinstellungen finden Sie unter [Create a peering (Erstellen eines Peerings)](virtual-network-manage-peering.md#create-a-peering).
22. Das von Ihnen erstellte Peering wird kurz nach dem Klicken auf **OK** im vorherigen Schritt angezeigt. In der Spalte **Peeringstatus** wird für das Peering **myVnetAToMyVnetB** der Status **Initiiert** angezeigt. Sie haben ein Peering von myVnetA mit myVnetB eingerichtet, doch nun müssen Sie ein Peering von myVnetB mit myVnetA erstellen. Das Peering muss für beide Richtungen erstellt werden, damit die Ressourcen in den virtuellen Netzwerken miteinander kommunizieren können.
23. Melden Sie sich als UserA vom Portal ab, und melden Sie sich als UserB an.
24. Führen Sie die Schritte 17-21 für myVnetB erneut aus. Geben Sie in Schritt 21 dem Peering den Namen *myVnetBToMyVnetA*. Wählen Sie *myVnetA* für **Virtuelles Netzwerk** aus, und geben Sie die ID aus Schritt 10 in das Feld **Ressourcen-ID** ein.
25. Einige Sekunden nach dem Klicken auf **OK** zum Erstellen des Peerings für myVnetB wird das zuvor erstellte Peering **myVnetBToMyVnetA** in der Spalte **Peeringstatus** als **Verbunden** angezeigt.
26. Melden Sie sich als UserB vom Portal ab, und melden Sie sich als UserA an.
27. Führen Sie die Schritte 17-19 erneut aus. Der **Peeringstatus** für **myVnetAToVNetB** lautet jetzt auch **Verbunden**. Wenn in der Spalte **Peeringstatus** für beide virtuellen Netzwerke der Status **Verbunden** angezeigt wird, wurde das Peering erfolgreich erstellt. Alle Azure-Ressourcen, die Sie in einem der virtuellen Netzwerke erstellen, sind in der Lage, miteinander über ihre IP-Adressen zu kommunizieren. Wenn Sie die standardmäßige Azure-Namensauflösung für virtuelle Netzwerke verwenden, können die Ressourcen in den virtuellen Netzwerken Namen nicht netzwerkübergreifend auflösen. Wenn Sie Namen netzwerkübergreifend in einem Peering auflösen möchten, müssen Sie einen eigenen DNS-Server erstellen. Weitere Informationen finden Sie im Artikel [Namensauflösung mithilfe eines eigenen DNS-Servers](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Optional:** Obwohl das Erstellen virtueller Computer in diesem Tutorial nicht behandelt wird, können Sie in jedem virtuellen Netzwerk virtuelle Computer erstellen und eine Verbindung zwischen ihnen herstellen, um die Konnektivität zu überprüfen.
29. **Optional:** Zum Löschen der Ressourcen, die Sie in diesem Tutorial erstellt haben, führen Sie die Schritte im Abschnitt [Löschen von Ressourcen](#delete-portal) dieses Artikels aus.

## <a name="cli"></a>Erstellen eines Peerings: Azure CLI

In diesem Tutorial werden unterschiedliche Konten für jedes Abonnement verwendet. Wenn Sie ein Konto verwenden, das über Berechtigungen für beide Abonnements verfügt, können Sie dasselbe Konto für alle Schritte verwenden. Überspringen Sie die Schritte zum Abmelden von Azure und zum Entfernen der Skriptzeilen zum Erstellen von Benutzerrollenzuweisungen. Ersetzen Sie UserA@azure.com und UserB@azure.com in allen folgenden Skripts durch die Benutzernamen, die Sie für UserA und UserB verwenden. Wenn sich die virtuellen Netzwerke in unterschiedlichen Abonnements befinden und die Abonnements unterschiedlichen Azure Active Directory-Mandanten zugeordnet sind, sollten Sie die folgenden Schritte ausführen, bevor Sie fortfahren:
 - Fügen Sie den Benutzer aus jedem Active Directory-Mandanten dem entsprechenden anderen Azure Active Directory-Mandanten als [Gastbenutzer](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) hinzu.
 - Jeder Benutzer muss die Einladung als Gastbenutzer vom entsprechenden Azure Active Directory-Mandanten akzeptieren.

Für das Skript gilt Folgendes:

- Es erfordert mindestens Version 2.0.4 der Azure CLI. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) weitere Informationen.
- Es wird in einer Bash-Shell ausgeführt. Optionen zum Ausführen von Azure CLI-Skripts auf einem Windows-Client finden Sie unter [Installieren der Azure CLI 2.0 unter Windows](/cli/azure/install-azure-cli-windows).

Anstatt die CLI und ihre Abhängigkeiten zu installieren, können Sie die Azure Cloud Shell verwenden. Azure Cloud Shell ist eine kostenlose Bash-Shell, die Sie direkt im Azure-Portal ausführen können. Die Azure CLI ist vorinstalliert und für die Verwendung mit Ihrem Konto konfiguriert. Klicken Sie im folgenden Skript auf die Schaltfläche **Ausprobieren**, um eine Cloud Shell-Instanz aufzurufen, über die Sie sich bei Ihrem Azure-Konto anmelden können.

1. Starten Sie eine CLI-Sitzung, und melden Sie sich mit dem Befehl `azure login` als UserA bei Azure an. Das Konto, mit dem Sie sich anmelden, muss über die Berechtigungen verfügen, die zum Erstellen eines Peerings virtueller Netzwerke erforderlich sind. Eine Liste der Berechtigungen finden Sie im Abschnitt „Berechtigungen“ unter [Erstellen, Ändern oder Löschen eines Peerings virtueller Netzwerke](virtual-network-manage-peering.md#permissions).
2. Kopieren Sie das folgende Skript in einen Texteditor auf Ihrem PC. Ersetzen Sie `<SubscriptionA-Id>` durch die ID von „SubscriptionA“. Kopieren Sie das geänderte Skript, fügen Sie es in die CLI-Sitzung ein, und drücken Sie `Enter`. Wenn Sie Ihre Abonnement-ID nicht kennen, geben Sie den Befehl `az account show` ein. Der Wert für **id** in der Ausgabe ist Ihre Abonnement-ID.

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

3. Melden Sie sich von Azure als UserA mit dem Befehl `az logout` ab, und melden Sie sich bei Azure als UserB an. Das Konto, mit dem Sie sich anmelden, muss über die Berechtigungen verfügen, die zum Erstellen eines Peerings virtueller Netzwerke erforderlich sind. Eine Liste der Berechtigungen finden Sie im Abschnitt „Berechtigungen“ unter [Erstellen, Ändern oder Löschen eines Peerings virtueller Netzwerke](virtual-network-manage-peering.md#permissions).
4. Erstellen Sie myVnetB. Kopieren Sie den Skriptinhalt in Schritt 2 in einen Texteditor auf Ihrem PC. Ersetzen Sie `<SubscriptionA-Id>` durch die ID von SubscriptionB. Ändern Sie 10.0.0.0/16 in 10.1.0.0/16, alle As in B und alle Bs in A. Kopieren Sie das geänderte Skript, fügen Sie es in Ihre CLI-Sitzung ein, und drücken Sie die `Enter`.
5. Melden Sie sich als UserB von Azure ab, und melden Sie sich als UserA bei Azure an.
6. Erstellen Sie ein Peering virtueller Netzwerke von myVnetA mit myVnetB. Kopieren Sie den folgenden Skriptinhalt in einen Texteditor auf Ihrem PC. Ersetzen Sie `<SubscriptionB-Id>` durch die ID von SubscriptionB. Kopieren Sie zum Ausführen des Skripts das geänderte Skript, fügen Sie es in Ihre CLI-Sitzung ein, und drücken Sie die EINGABETASTE.

    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)

        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet-id /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. Zeigen Sie den Peeringstatus von myVnetA an.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    Der Status ist **Initiiert**. Er ändert sich in **Verbunden**, sobald Sie das Peering von myVnetA mit myVnetB eingerichtet haben.

8. Melden Sie sich als UserA von Azure ab, und melden Sie sich als UserB bei Azure an.
9. Erstellen Sie das Peering von myVnetB mit myVnetA. Kopieren Sie den Skriptinhalt in Schritt 6 in einen Texteditor auf Ihrem PC. Ersetzen Sie `<SubscriptionB-Id>` durch die ID für „SubscriptionA“, und ändern Sie alle As in B und alle Bs in A. Kopieren Sie anschließend das geänderte Skript, fügen Sie es in Ihre CLI-Sitzung ein, und drücken Sie die `Enter`.
10. Zeigen Sie den Peeringstatus von myVnetB an. Kopieren Sie den Skriptinhalt in Schritt 7 in einen Texteditor auf Ihrem PC. Ändern Sie bei den Namen der Ressourcengruppe und des virtuellen Netzwerks A in B. Kopieren Sie das Skript. Fügen Sie das geänderte Skript in Ihre CLI-Sitzung ein, und drücken Sie dann `Enter`. Der Peeringstatus ist **Verbunden**. Der Peeringstatus von myVnetA ändert sich in **Verbunden**, nachdem Sie das Peering von myVnetB mit myVnetA eingerichtet haben. Sie können sich wieder als Benutzer A bei Azure anmelden und Schritt 7 erneut ausführen, um den Peeringstatus von myVnetA zu überprüfen. 

    > [!NOTE]
    > Das Peering ist erst dann erfolgreich erstellt, sobald der Peeringstatus für beide virtuellen Netzwerke **Verbunden** lautet.

11. **Optional:** Obwohl das Erstellen virtueller Computer in diesem Tutorial nicht behandelt wird, können Sie in jedem virtuellen Netzwerk virtuelle Computer erstellen und eine Verbindung zwischen ihnen herstellen, um die Konnektivität zu überprüfen.
12. **Optional:** Zum Löschen der Ressourcen, die Sie in diesem Tutorial erstellt haben, führen Sie die Schritte im Abschnitt [Löschen von Ressourcen](#delete-cli) in diesem Artikel aus.

Alle Azure-Ressourcen, die Sie in einem der virtuellen Netzwerke erstellen, sind in der Lage, miteinander über ihre IP-Adressen zu kommunizieren. Wenn Sie die standardmäßige Azure-Namensauflösung für virtuelle Netzwerke verwenden, können die Ressourcen in den virtuellen Netzwerken Namen nicht netzwerkübergreifend auflösen. Wenn Sie Namen netzwerkübergreifend in einem Peering auflösen möchten, müssen Sie einen eigenen DNS-Server erstellen. Weitere Informationen finden Sie im Artikel [Namensauflösung mithilfe eines eigenen DNS-Servers](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="powershell"></a>Erstellen eines Peerings: PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In diesem Tutorial werden unterschiedliche Konten für jedes Abonnement verwendet. Wenn Sie ein Konto verwenden, das über Berechtigungen für beide Abonnements verfügt, können Sie dasselbe Konto für alle Schritte verwenden. Überspringen Sie die Schritte zum Abmelden von Azure und zum Entfernen der Skriptzeilen zum Erstellen von Benutzerrollenzuweisungen. Ersetzen Sie UserA@azure.com und UserB@azure.com in allen folgenden Skripts durch die Benutzernamen, die Sie für UserA und UserB verwenden.
Wenn sich die virtuellen Netzwerke in unterschiedlichen Abonnements befinden und die Abonnements unterschiedlichen Azure Active Directory-Mandanten zugeordnet sind, sollten Sie die folgenden Schritte ausführen, bevor Sie fortfahren:
 - Fügen Sie den Benutzer aus jedem Active Directory-Mandanten dem entsprechenden anderen Azure Active Directory-Mandanten als [Gastbenutzer](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) hinzu.
 - Jeder Benutzer muss die Einladung als Gastbenutzer vom entsprechenden Active Directory-Mandanten akzeptieren.

1. Vergewissern Sie sich, dass Sie über Azure PowerShell-Version 1.0.0 oder höher verfügen. Zu diesem Zweck können Sie `Get-Module -Name Az` ausführen. Wir empfehlen Ihnen, die aktuelle Version des PowerShell-[Az](/powershell/azure/install-az-ps)-Moduls zu installieren. Wenn Sie noch nicht mit Azure PowerShell vertraut sind, lesen Sie die [Übersicht über Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json). 
2. Starten Sie eine PowerShell-Sitzung.
3. Melden Sie sich in PowerShell als UserA bei Azure an, indem Sie den Befehl `Connect-AzAccount` eingeben. Das Konto, mit dem Sie sich anmelden, muss über die Berechtigungen verfügen, die zum Erstellen eines Peerings virtueller Netzwerke erforderlich sind. Eine Liste der Berechtigungen finden Sie im Abschnitt „Berechtigungen“ unter [Erstellen, Ändern oder Löschen eines Peerings virtueller Netzwerke](virtual-network-manage-peering.md#permissions).
4. Erstellen Sie eine Ressourcengruppe und das virtuelle Netzwerk A. Kopieren Sie das folgende Skript in einen Text-Editor auf Ihrem PC. Ersetzen Sie `<SubscriptionA-Id>` durch die ID von SubscriptionA. Wenn Sie Ihre Abonnement-ID nicht kennen, geben Sie den Befehl `Get-AzSubscription` ein. Der Wert für **Id** in der Ausgabe ist Ihre Abonnement-ID. Um das Skript auszuführen, kopieren Sie das geänderte Skript. Fügen Sie es in PowerShell ein, und drücken Sie dann `Enter`.

    ```powershell
    # Create a resource group.
    New-AzResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Melden Sie sich als UserA von Azure ab, und melden Sie sich als UserB an. Das Konto, mit dem Sie sich anmelden, muss über die Berechtigungen verfügen, die zum Erstellen eines Peerings virtueller Netzwerke erforderlich sind. Eine Liste der Berechtigungen finden Sie im Abschnitt „Berechtigungen“ unter [Erstellen, Ändern oder Löschen eines Peerings virtueller Netzwerke](virtual-network-manage-peering.md#permissions).
6. Kopieren Sie den Skriptinhalt in Schritt 4 in einen Texteditor auf Ihrem PC. Ersetzen Sie `<SubscriptionA-Id>` durch die ID für Abonnement B. Ändern Sie 10.0.0.0/16 in 10.1.0.0/16. Ändern Sie alle As in B und alle Bs in A. Kopieren Sie das geänderte Skript, fügen Sie es in PowerShell ein, und drücken Sie anschließend die `Enter`, um das Skript auszuführen.
7. Melden Sie sich als UserB von Azure ab, und melden Sie sich als UserA an.
8. Erstellen Sie das Peering von myVnetA mit myVnetB. Kopieren Sie das folgende Skript in einen Texteditor auf Ihrem PC. Ersetzen Sie `<SubscriptionB-Id>` durch die ID für Abonnement B. Kopieren Sie das geänderte Skript, fügen Sie es in PowerShell ein, und drücken Sie anschließend die `Enter`, um das Skript auszuführen.

   ```powershell
   # Peer myVnetA to myVnetB.
   $vNetA=Get-AzVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
   Add-AzVirtualNetworkPeering `
     -Name 'myVnetAToMyVnetB' `
     -VirtualNetwork $vNetA `
     -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
   ```

9. Zeigen Sie den Peeringstatus von myVnetA an.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Der Status ist **Initiiert**. Er wird in **Verbunden** geändert, sobald Sie das Peering von myVnetA mit myVnetB eingerichtet haben.

10. Melden Sie sich als UserA von Azure ab, und melden Sie sich als UserB an.
11. Erstellen Sie das Peering von myVnetB mit myVnetA. Kopieren Sie den Skriptinhalt in Schritt 8 in einen Texteditor auf Ihrem PC. Ersetzen Sie `<SubscriptionB-Id>` durch die ID für Abonnement A, und ändern Sie alle As in B und alle Bs in A. Kopieren Sie das geänderte Skript, fügen Sie es in PowerShell ein, und drücken Sie anschließend die `Enter`, um das Skript auszuführen.
12. Zeigen Sie den Peeringstatus von myVnetB an. Kopieren Sie den Skriptinhalt in Schritt 9 in einen Texteditor auf Ihrem PC. Ändern Sie bei den Namen der Ressourcengruppe und des virtuellen Netzwerks A in B. Um das Skript auszuführen, fügen Sie das geänderte Skript in PowerShell ein, und drücken Sie dann `Enter`. Der Status ist **Verbunden**. Der Peeringstatus von **myVnetA** ändert sich in **Verbunden**, nachdem Sie das Peering von **myVnetB** mit **myVnetA** eingerichtet haben. Sie können sich wieder als Benutzer A bei Azure anmelden und Schritt 9 erneut ausführen, um den Peeringstatus von myVnetA zu überprüfen.

    > [!NOTE]
    > Das Peering ist erst dann erfolgreich erstellt, sobald der Peeringstatus für beide virtuellen Netzwerke **Verbunden** lautet.

    Alle Azure-Ressourcen, die Sie in einem der virtuellen Netzwerke erstellen, sind in der Lage, miteinander über ihre IP-Adressen zu kommunizieren. Wenn Sie die standardmäßige Azure-Namensauflösung für virtuelle Netzwerke verwenden, können die Ressourcen in den virtuellen Netzwerken Namen nicht netzwerkübergreifend auflösen. Wenn Sie Namen netzwerkübergreifend in einem Peering auflösen möchten, müssen Sie einen eigenen DNS-Server erstellen. Weitere Informationen finden Sie im Artikel [Namensauflösung mithilfe eines eigenen DNS-Servers](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Optional:** Obwohl das Erstellen virtueller Computer in diesem Tutorial nicht behandelt wird, können Sie in jedem virtuellen Netzwerk virtuelle Computer erstellen und eine Verbindung zwischen ihnen herstellen, um die Konnektivität zu überprüfen.
14. **Optional:** Zum Löschen der Ressourcen, die Sie in diesem Tutorial erstellt haben, führen Sie die Schritte im Abschnitt [Löschen von Ressourcen](#delete-powershell) in diesem Artikel aus.

## <a name="template"></a>Erstellen eines Peerings: Resource Manager-Vorlage

Wenn sich die virtuellen Netzwerke in unterschiedlichen Abonnements befinden und die Abonnements unterschiedlichen Azure Active Directory-Mandanten zugeordnet sind, sollten Sie die folgenden Schritte ausführen, bevor Sie fortfahren:
 - Fügen Sie den Benutzer aus jedem Active Directory-Mandanten dem entsprechenden anderen Azure Active Directory-Mandanten als [Gastbenutzer](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) hinzu.
 - Jeder Benutzer muss die Einladung als Gastbenutzer vom entsprechenden Active Directory-Mandanten akzeptieren.

1. Führen Sie die Schritte in den Abschnitten [Portal](virtual-network-manage-peering.md#permissions), [Azure CLI](#portal) oder [PowerShell](#cli) dieses Artikels durch, um ein virtuelles Netzwerk zu erstellen und die passenden [Berechtigungen](#powershell) zuzuweisen.
2. Speichern Sie den darauf folgenden Text in einer Datei auf dem lokalen Computer. Ersetzen Sie `<subscription ID>` durch die Abonnement-ID von UserA. Sie können die Datei z.B. unter dem Namen „vnetpeeringA.json“ speichern.

   ```json
   {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
   }
   ```

3. Melden Sie sich bei Azure als BenutzerA an, und stellen Sie die Vorlage über das [Portal](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template) oder die [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template) bereit. Geben Sie den Dateinamen ein, unter dem Sie in Schritt 2 den Beispiel-Json-Text gespeichert haben.
4. Kopieren Sie den Beispiel-Json-Code aus Schritt 2 in einer Datei auf Ihrem Computer, und nehmen Sie Änderungen an den Zeilen vor, die wie folgt beginnen:
   - **name:** Ändern Sie *myVnetA/myVnetAToMyVnetB* in *myVnetB/myVnetBToMyVnetA*.
   - **id:** Ersetzen Sie `<subscription ID>` durch die Abonnement-ID von UserB, und ändern Sie *myVnetB* in *myVnetA*.
5. Führen Sie Schritt 3 erneut durch, während Sie in Azure als BenutzerB angemeldet sind.
6. **Optional:** Obwohl das Erstellen virtueller Computer in diesem Tutorial nicht behandelt wird, können Sie in jedem virtuellen Netzwerk virtuelle Computer erstellen und eine Verbindung zwischen ihnen herstellen, um die Konnektivität zu überprüfen.
7. **Optional:** Zum Löschen der Ressourcen, die Sie in diesem Tutorial erstellt haben, führen Sie mithilfe des Azure-Portals, von PowerShell oder der Azure-Befehlszeilenschnittstelle die Schritte im Abschnitt [Löschen von Ressourcen](#delete) dieses Artikels aus.

## <a name="delete"></a>Löschen von Ressourcen
Wenn Sie dieses Tutorial abgeschlossen haben, möchten Sie die Ressourcen, die Sie in diesem Tutorial erstellt haben, möglicherweise wieder löschen, damit keine Nutzungsgebühren anfallen. Durch das Löschen einer Ressourcengruppe werden auch alle Ressourcen in dieser Ressourcengruppe gelöscht.

### <a name="delete-portal"></a>Azure-Portal

1. Melden Sie sich als UserA beim Azure-Portal an.
2. Geben Sie in das Suchfeld im Portal **myResourceGroupA** ein. Klicken Sie in den Suchergebnissen auf **myResourceGroupA**.
3. Klicken Sie auf **Löschen**.
4. Um den Löschvorgang zu bestätigen, geben Sie in das Feld **Geben Sie den Ressourcengruppennamen ein** den Namen **myResourceGroupA** ein, und klicken Sie dann auf **Löschen**.
5. Melden Sie sich als UserA vom Portal ab, und melden Sie sich als UserB an.
6. Führen Sie die Schritte 2-4 für myResourceGroupB aus.

### <a name="delete-cli"></a>Azure-Befehlszeilenschnittstelle

1. Melden Sie sich als UserA bei Azure an, und führen Sie den folgenden Befehl aus:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Melden Sie sich als UserA bei Azure ab, und melden Sie sich als UserB an.
3. Führen Sie den folgenden Befehl aus:

   ```azurecli-interactive
   az group delete --name myResourceGroupB --yes
   ```

### <a name="delete-powershell"></a>PowerShell

1. Melden Sie sich als UserA bei Azure an, und führen Sie den folgenden Befehl aus:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -force
   ```

2. Melden Sie sich als UserA bei Azure ab, und melden Sie sich als UserB an.
3. Führen Sie den folgenden Befehl aus:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupB -force
   ```

## <a name="next-steps"></a>Nächste Schritte

- Machen Sie sich eingehend mit den wichtigen [Einschränkungen und Verhalten eines Peerings in virtuellen Netzwerken](virtual-network-manage-peering.md#requirements-and-constraints) vertraut, bevor Sie ein Peering in virtuellen Netzwerken für die Produktion erstellen.
- Erfahren Sie alles über [Peering-Einstellungen in virtuellen Netzwerken](virtual-network-manage-peering.md#create-a-peering).
- Erfahren Sie, wie Sie eine [Hub-Spoke-Netzwerktopologie ](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) mit einem Peering in virtuellen Netzwerken erstellen.
