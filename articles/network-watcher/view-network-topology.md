---
title: Anzeigen der Topologie des virtuellen Azure-Netzwerks | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Ressourcen in einem virtuellen Netzwerk und die Beziehungen zwischen diesen Ressourcen anzeigen.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: kumud
ms.openlocfilehash: a67f1cca54c89ead9ae7fc46ef0c9fc8c5217c74
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64682052"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Anzeigen der Topologie eines virtuellen Azure-Netzwerks

In diesem Artikel erfahren Sie, wie Sie Ressourcen in einem virtuellen Microsoft Azure-Netzwerk und die Beziehungen zwischen diesen Ressourcen anzeigen. Ein virtuelles Netzwerk enthält z.B. Subnetze. Subnetze enthalten Ressourcen, z.B. virtuelle Azure-Computer (VMs). Virtuelle Computer können eine oder mehrere Netzwerkschnittstellen aufweisen. Jedes Subnetz kann über eine Netzwerksicherheitsgruppe mit einer zugeordneten Routingtabelle verfügen. Mit der Topologiefunktion von Azure Network Watcher können Sie alle Ressourcen in einem virtuellen Netzwerk, die den Ressourcen in einem virtuellen Netzwerk zugeordneten Ressourcen und die Beziehungen zwischen den Ressourcen anzeigen.

Sie können die Topologie mithilfe des [Azure-Portals](#azure-portal), der [Azure-Befehlszeilenschnittstelle](#azure-cli) oder von [PowerShell](#powershell) anzeigen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name = "azure-portal"></a>Topologie anzeigen – Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Konto an, dem die erforderlichen [Berechtigungen](required-rbac-permissions.md) zugewiesen sind.
2. Wählen Sie links oben im Portal die Option **Alle Dienste** aus.
3. Geben Sie im Filterfeld **Alle Dienste** die Zeichenfolge *Network Watcher* ein. Wenn **Network Watcher** in den Ergebnissen angezeigt wird, wählen Sie ihn aus.
4. Wählen Sie **Topologie** aus. Zum Generieren einer Topologie muss sich ein Network Watcher in derselben Region befinden wie das virtuelle Netzwerk, für das Sie die Topologie generieren möchten. Wenn in der Region, in der sich das virtuelle Netzwerk befindet, für das Sie eine Topologie generieren möchten, kein Network Watcher befindet, werden automatisch in allen Regionen Network Watcher erstellt. Die Network Watcher werden in einer Ressourcengruppe namens **NetworkWatcherRG** erstellt.
5. Wählen Sie ein Abonnement, die Ressourcengruppe eines virtuellen Netzwerks, dessen Topologie Sie anzeigen möchten, und dann das virtuelle Netzwerk aus. In der folgenden Abbildung ist eine Topologie für ein virtuelles Netzwerk mit dem Namen *myVnet* in der Ressourcengruppe mit dem Namen *MyResourceGroup* dargestellt:

    ![Topologie anzeigen](./media/view-network-topology/view-topology.png)

    Wie in der vorherigen Abbildung zu erkennen ist, enthält das virtuelle Netzwerk drei Subnetze. In einem Subnetz wurde ein virtueller Computer bereitgestellt. An den virtuellen Computer ist eine Netzwerkschnittstelle angefügt, und ihm ist eine öffentliche IP-Adresse zugeordnet. Den anderen beiden Subnetzen ist eine Routingtabelle zugeordnet. Jede Routingtabelle enthält zwei Routen. Einem Subnetz ist eine Netzwerksicherheitsgruppe zugeordnet. Informationen zur Topologie werden nur für Ressourcen angezeigt, die:
    
    - sich innerhalb der gleichen Ressourcengruppe und Region wie das virtuelle *myVnet* Netzwerk befinden. Beispiel: Eine Netzwerksicherheitsgruppe, die sich in einer anderen Ressourcengruppe als *MyResourceGroup* befindet, wird nicht angezeigt, selbst wenn sie einem Subnetz im virtuellen Netzwerk *myVnet* zugeordnet ist.
    - sich im virtuellen Netzwerk *myVnet* befinden oder Ressourcen in ihm zugeordnet sind. Beispiel: Eine Netzwerksicherheitsgruppe, die keinem Subnetz und keiner Netzwerkschnittstelle im virtuellen Netzwerk *myVnet* zugewiesen ist, wird nicht angezeigt, selbst wenn sie sich in der Ressourcengruppe *MyResourceGroup* befindet.

   Die in der Abbildung dargestellte Topologie gilt für das virtuelle Netzwerk, das nach der Bereitstellung des Beispielskripts unter **Weiterleiten von Datenverkehr über ein virtuelles Netzwerkgerät** erstellt wurde. Diese Bereitstellung kann über die [Azure-Befehlszeilenschnittstelle](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) oder [PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) erfolgen.

6. Wählen Sie **Topologie herunterladen** aus, um die Abbildung als bearbeitbare Datei im SVG-Format herunterzuladen.

Die im Diagramm dargestellten Ressourcen sind eine Teilmenge der Netzwerkkomponenten im virtuellen Netzwerk. Während eine Netzwerksicherheitsgruppe angezeigt wird, werden die Sicherheitsregeln darin beispielsweise nicht im Diagramm angezeigt. Obwohl dies im Diagramm nicht unterschieden wird, stellen die Linien eine von zwei Beziehungen dar: *Eigenständigkeit* oder *Zuordnung*. Um die vollständige Liste der Ressourcen im virtuellen Netzwerk und den Typ der Beziehung zwischen den Ressourcen anzuzeigen, generieren Sie die Topologie mit [PowerShell](#powershell) oder der [Azure-Befehlszeilenschnittstelle](#azure-cli).

## <a name = "azure-cli"></a>Topologie anzeigen – Azure-Befehlszeilenschnittstelle

Sie können die Befehle in den einzelnen Schritten folgendermaßen ausführen:
- In Azure Cloud Shell durch Auswählen von **Try It** (Ausprobieren) rechts oben vom jeweiligen Befehl. Azure Cloud Shell ist eine kostenlose interaktive Shell, in der häufig verwendete Azure-Tools vorinstalliert sind und die für die Verwendung mit Ihrem Konto konfiguriert wurde.
- Durch Ausführen der Befehlszeilenschnittstelle auf Ihrem Computer. Wenn Sie die Befehlszeilenschnittstelle auf Ihrem Computer ausführen, ist für die Schritte in diesem Artikel mindestens Version 2.0.31 von Azure CLI erforderlich. Führen Sie `az --version` aus, um die installierte Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli). Wenn Sie die Azure CLI lokal ausführen, müssen Sie auch `az login` ausführen, um eine Verbindung mit Azure herzustellen.

Das verwendete Konto muss über die erforderlichen [Berechtigungen](required-rbac-permissions.md) verfügen.

1. Wenn Sie bereits einen Network Watcher in derselben Region wie das virtuelle Netzwerk, in dem Sie eine Topologie erstellen möchten, verfügen, fahren Sie mit Schritt 3 fort. Erstellen Sie mit [az group create](/cli/azure/group) eine Ressourcengruppe, die einen Network Watcher enthalten soll. Im folgenden Beispiel wird eine Ressourcengruppe in der Region *eastus* erstellt:

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Erstellen Sie mit [az network watcher configure](/cli/azure/network/watcher#az-network-watcher-configure) einen Network Watcher. Im folgenden Beispiel wird ein Network Watcher in der Region *eastus* erstellt:

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Zeigen Sie die Topologie mit [az network watcher show-topology](/cli/azure/network/watcher#az-network-watcher-show-topology) an. Im folgenden Beispiel wird die Topologie für die Ressource *MyResourceGroup* erstellt:

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Topologieinformationen werden nur für Ressourcen in derselben Ressourcengruppe wie die Ressourcengruppe *MyResourceGroup* und derselben Region wie der Network Watcher zurückgegeben. Beispiel: Eine Netzwerksicherheitsgruppe, die sich in einer anderen Ressourcengruppe als *MyResourceGroup* befindet, wird nicht angezeigt, selbst wenn sie einem Subnetz im virtuellen Netzwerk *myVnet* zugeordnet ist.

   Erfahren Sie mehr über die Beziehungen und [Eigenschaften](#properties) in der zurückgegebenen Ausgabe. Wenn Sie nicht über ein virtuelles Netzwerk verfügen, dessen Topologie Sie anzeigen können, können Sie eines mit dem Beispielskript [Weiterleiten von Datenverkehr über ein virtuelles Netzwerkgerät](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) erstellen. Um ein Diagramm der Topologie anzuzeigen und als bearbeitbare Datei herunterzuladen, verwenden Sie das [Portal](#azure-portal).

## <a name = "powershell"></a>Topologie anzeigen – PowerShell

Sie können die Befehle in den einzelnen Schritten folgendermaßen ausführen:
- In Azure Cloud Shell durch Auswählen von **Try It** (Ausprobieren) rechts oben vom jeweiligen Befehl. Azure Cloud Shell ist eine kostenlose interaktive Shell, in der häufig verwendete Azure-Tools vorinstalliert sind und die für die Verwendung mit Ihrem Konto konfiguriert wurde.
- Durch Ausführen von PowerShell auf Ihrem Computer. Wenn Sie PowerShell auf Ihrem Computer ausführen, erfordert dieser Artikel das Azure PowerShell-Modul `Az`. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

Das verwendete Konto muss über die erforderlichen [Berechtigungen](required-rbac-permissions.md) verfügen.

1. Wenn Sie bereits einen Network Watcher in derselben Region wie das virtuelle Netzwerk, in dem Sie eine Topologie erstellen möchten, verfügen, fahren Sie mit Schritt 3 fort. Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) eine Ressourcengruppe, die einen Network Watcher enthalten soll. Im folgenden Beispiel wird eine Ressourcengruppe in der Region *eastus* erstellt:

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Erstellen Sie mit [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher) einen Network Watcher. Im folgenden Beispiel wird ein Network Watcher in der Region „eastus“ erstellt:

    ```azurepowershell-interactive
    New-AzNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Rufen Sie mit [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher) eine Network Watcher-Instanz ab. Im folgenden Beispiel wird ein Network Watcher in der Region „eastus“ (USA, Osten) abgerufen:

    ```azurepowershell-interactive
    $nw = Get-AzResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Rufen Sie eine Topologie mit [Get-AzNetworkWatcherTopology](/powershell/module/az.network/get-aznetworkwatchertopology) ab. Im folgenden Beispiel wird eine Topologie für ein virtuelles Netzwerk in der Ressourcengruppe *MyResourceGroup* abgerufen:

    ```azurepowershell-interactive
    Get-AzNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Topologieinformationen werden nur für Ressourcen in derselben Ressourcengruppe wie die Ressourcengruppe *MyResourceGroup* und derselben Region wie der Network Watcher zurückgegeben. Beispiel: Eine Netzwerksicherheitsgruppe, die sich in einer anderen Ressourcengruppe als *MyResourceGroup* befindet, wird nicht angezeigt, selbst wenn sie einem Subnetz im virtuellen Netzwerk *myVnet* zugeordnet ist.

   Erfahren Sie mehr über die Beziehungen und [Eigenschaften](#properties) in der zurückgegebenen Ausgabe. Wenn Sie nicht über ein virtuelles Netzwerk verfügen, dessen Topologie Sie anzeigen können, können Sie eines mit dem Beispielskript [Weiterleiten von Datenverkehr über ein virtuelles Netzwerkgerät](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) erstellen. Um ein Diagramm der Topologie anzuzeigen und als bearbeitbare Datei herunterzuladen, verwenden Sie das [Portal](#azure-portal).

## <a name="relationships"></a>Beziehungen

Alle in einer Topologie zurückgegebenen Ressourcen weisen einen der folgenden Typen einer Beziehung zu einer anderen Ressource auf:

| Beziehungstyp | Beispiel                                                                                                |
| ---               | ---                                                                                                    |
| Kapselung       | Ein virtuelles Netzwerk enthält ein Subnetz. Ein Subnetz enthält eine Netzwerkschnittstelle.                            |
| Zugeordnet        | Eine Netzwerkschnittstelle ist einem virtuellen Computer zugeordnet. Eine öffentliche IP-Adresse ist einer Netzwerkschnittstelle zugeordnet. |

## <a name="properties"></a>Eigenschaften

Alle in einer Topologie zurückgegebenen Ressourcen haben die folgenden Eigenschaften:

- **Name**: Der Name der Ressource
- **Id:** Der URI der Ressource
- **Standort**: Die Azure-Region, in der sich die Ressource befindet
- **Associations:** Eine Liste der Zuordnungen für das referenzierte Objekt. Jede Zuordnung weist die folgenden Eigenschaften auf:
    - **AssociationType:** Verweist auf die Beziehung zwischen dem untergeordneten und dem übergeordneten Objekt. Gültige Werte sind *Contains* oder *Associated*.
    - **Name**: Der Name der referenzierten Ressource
    - **ResourceId:** der URI der Ressource, auf die in der Zuordnung verwiesen wird

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [ein Problem mit dem Filtern des Netzwerkdatenverkehrs zu einem virtuellen Computer oder von diesem](diagnose-vm-network-traffic-filtering-problem.md) mithilfe der IP-Datenflussüberprüfungs-Funktion des Network Watcher diagnostizieren.
- Erfahren Sie, wie Sie [ein Problem mit der Weiterleitung des Netzwerkdatenverkehrs von einem virtuellen Computer](diagnose-vm-network-routing-problem.md) mithilfe der Next-Hop-Funktion des Network Watcher diagnostizieren.
