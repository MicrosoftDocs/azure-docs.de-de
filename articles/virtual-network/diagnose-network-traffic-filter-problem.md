---
title: Diagnostizieren von Problemen mit dem Filter für Netzwerkdatenverkehr eines virtuellen Computers | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein Problem mit dem Filter für Netzwerkdatenverkehr eines virtuellen Computers durch Anzeigen der effektiven Sicherheitsregeln für einen virtuellen Computer diagnostizieren.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: kumud
ms.openlocfilehash: ec70f7820994898cb8e552dab66547cc9c9f10ec
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042880"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Diagnostizieren von Problemen mit dem Filter für Netzwerkdatenverkehr eines virtuellen Computers

In diesem Artikel erfahren Sie, wie ein Problem mit einem Filter für Netzwerkdatenverkehr diagnostiziert werden kann, indem Sie die für einen virtuellen Computer (VM) geltenden NSG-Sicherheitsregeln (Netzwerksicherheitsgruppe) anzeigen.

Über NSGs können Sie die Typen des Datenverkehrs steuern, der bei einer VM ein- und ausgeht. Sie können einem Subnetz in einem virtuellen Azure-Network, einer Netzwerkschnittstelle, die an eine VM angefügt ist, oder beidem eine NSG zuordnen. Die geltenden Sicherheitsregeln, die auf eine Netzwerkschnittstelle angewendet werden, stellen eine Aggregation der Regeln, die in der einer Netzwerkschnittstelle zugeordneten NSG vorhanden sind, und des Subnetzes der Netzwerkschnittstelle dar. Die Regeln in den verschiedenen NSGs können manchmal in Konflikt miteinander stehen und sich auf die Netzwerkkonnektivität einer VM auswirken. Sie können alle effektiven Sicherheitsregeln der NSGs anzeigen, die auf die Netzwerkschnittstellen Ihrer VM angewendet werden. Wenn Sie nicht mit virtuellen Netzwerken, Netzwerkschnittstellen oder NSG-Konzepten vertraut sind, lesen Sie [Was ist Azure Virtual Network?](virtual-networks-overview.md), [Erstellen, Ändern oder Löschen von Netzwerkschnittstellen](virtual-network-network-interface.md) und [Netzwerksicherheit](security-overview.md).

## <a name="scenario"></a>Szenario

Sie versuchen, eine Verbindung mit einer VM über Port 80 über das Internet herzustellen, doch ein Fehler tritt auf. Um festzustellen, warum Sie nicht auf Port 80 über das Internet zugreifen können, können Sie die effektiven Sicherheitsregeln für eine Netzwerkschnittstelle mit dem [Azure-Portal](#diagnose-using-azure-portal), mit [PowerShell](#diagnose-using-powershell) oder mit der [Azure CLI](#diagnose-using-azure-cli) anzeigen.

Die folgenden Schritte setzen voraus, dass Sie über eine VM verfügen, deren effektive Sicherheitsregeln Sie anzeigen können. Wenn Sie nicht über eine VM verfügen, stellen Sie zuerst eine [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- oder [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-VM zum Ausführen der Aufgaben in diesem Artikel bereit. Die Beispiele in diesem Artikel beziehen sich auf eine VM mit dem Namen *myVM* mit einer Netzwerkschnittstelle namens *myVMVMNic*. VM und Netzwerkschnittstelle gehören zu einer Ressourcengruppe mit dem Namen *myResourceGroup* in der Region *USA, Osten*. Ändern Sie die Werte in den Schritten nach Bedarf für die VM, deren Problem Sie diagnostizieren.

## <a name="diagnose-using-azure-portal"></a>Diagnostizieren über das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Azure-Konto an, dem die [erforderlichen Berechtigungen](virtual-network-network-interface.md#permissions) zugewiesen sind.
2. Geben Sie oben im Azure-Portal in das Suchfeld den Namen der VM ein. Wenn der Name der VM in den Suchergebnissen angezeigt wird, wählen Sie sie aus.
3. Klicken Sie unter **EINSTELLUNGEN** auf **Netzwerk** , wie in der folgenden Abbildung gezeigt wird:

   ![Screenshot: Azure-Portal mit Netzwerkeinstellungen für myVMVMNic](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   Die Regeln, die in der vorherigen Abbildung aufgeführt sind, beziehen sich auf eine Netzwerkschnittstelle mit dem Namen **myVMVMNic**. Sie können **REGELN FÜR EINGEHENDE PORTS** für die Netzwerkschnittstelle aus zwei verschiedenen Netzwerksicherheitsgruppen sehen:
   
   - **mySubnetNSG** : Ist dem Subnetz zugeordnet, in dem sich die Netzwerkschnittstelle befindet.
   - **myVMNSG** : Ist der Netzwerkschnittstelle in der VM mit dem Namen **myVMVMNic** zugeordnet.

   Die Regel mit dem Namen **DenyAllInBound** verhindert eingehende Kommunikation mit der VM über Port 80 über das Internet, wie im [Szenario](#scenario) beschrieben wird. Die Regel gibt *0.0.0.0/0* für **QUELLE** an, die das Internet einschließt. Keine andere Regel mit einer höheren Priorität (niedrigere Zahl) lässt den Port 80 als eingehenden Port zu. Um den Port 80 als eingehenden Port für die VM über das Internet zuzulassen, lesen Sie den Artikel [Beheben eines Problems](#resolve-a-problem). Weitere Informationen zu Sicherheitsregeln und zu ihrer Anwendung in Azure finden Sie unter [Netzwerksicherheitsgruppen](security-overview.md).

   Unten in der Abbildung sehen Sie darüber hinaus **REGELN FÜR AUSGEHENDE PORTS**. Darunter befinden sich die Regeln für ausgehende Ports für die Netzwerkschnittstelle. Obwohl in der Abbildung nur vier Eingangsregeln für jede NSG gezeigt werden, verfügen Ihre NSGs möglicherweise über mehr als vier Regeln. In der Abbildung finden Sie **VirtualNetwork** unter **QUELLE** und **ZIEL** und **AzureLoadBalancer** unter **QUELLE**. Bei **VirtualNetwork** und **AzureLoadBalancer** handelt es sich um [Diensttags](security-overview.md#service-tags). Diensttags stellen eine Gruppe von IP-Adressen dar und haben die Aufgabe, bei der Erstellung von Sicherheitsregeln die Komplexität zu verringern.

4. Um die in der folgenden Abbildung gezeigten effektiven Sicherheitsregeln anzuzeigen, stellen Sie sicher, dass sich die VM im Ausführungsstatus befindet, und wählen Sie dann **Effektive Sicherheitsregeln** aus:

   ![Screenshot des Bereichs „Effektive Sicherheitsregeln“ mit ausgewählter Downloadoption und ausgewählter Eingangsregel „AllowAzureLoadBalancerInbound“](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   Die aufgeführten Regeln sind mit denen aus Schritt 3 identisch, wobei es jedoch verschiedene Registerkarten für die NSG gibt, die der Netzwerkschnittstelle und dem Subnetz zugeordnet ist. Wie Sie in der Abbildung sehen können, werden nur die ersten 50 Regeln angezeigt. Klicken Sie zum Herunterladen einer CSV-Datei, die alle Regeln enthält, auf **Herunterladen**.

   Um anzuzeigen, welche Präfixe jedes Diensttag darstellt, wählen Sie eine Regel aus, z.B. die Regel mit dem Namen **AllowAzureLoadBalancerInbound**. In der folgenden Abbildung werden die Präfixe für das Diensttag **AzureLoadBalancer** gezeigt:

   ![Screenshot mit eingegebenen Adresspräfixen für „AllowAzureLoadBalancerInbound“](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   Das Diensttag **AzureLoadBalancer** stellt zwar nur ein Präfix dar, andere Diensttags stehen jedoch für mehrere Präfixe.

5. In den vorherigen Schritten wurden die Sicherheitsregeln für eine Netzwerkschnittstelle mit dem Namen **myVMVMNic** veranschaulicht, doch in einigen der vorherigen Abbildungen haben Sie auch eine Netzwerkschnittstelle mit dem Namen **myVMVMNic2** gesehen. Bei der VM in diesem Beispiel sind zwei Netzwerkschnittstellen angefügt. Die effektiven Sicherheitsregeln können für jede Netzwerkschnittstelle unterschiedlich sein.

   Um die Regeln für die Netzwerkschnittstelle **myVMVMNic2** anzuzeigen, wählen Sie sie aus. Wie in der nachfolgenden Abbildung gezeigt wird, sind bei der Netzwerkschnittstelle dem Subnetz die gleichen Regeln wie denen der Netzwerkschnittstelle **myVMVMNic** zugeordnet, da sich beide Netzwerkschnittstellen im gleichen Subnetz befinden. Wenn Sie einem Subnetz eine NSG zuordnen, werden die zugehörigen Regeln auf alle Netzwerkschnittstellen im Subnetz angewendet.

   ![Screenshot: Azure-Portal mit Netzwerkeinstellungen für myVMVMNic2](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   Im Gegensatz zur Netzwerkschnittstelle **myVMVMNic** ist die Netzwerkschnittstelle **myVMVMNic2** nicht einer Netzwerksicherheitsgruppe zugeordnet. Jede Netzwerkschnittstelle und jedes Subnetz können optional einer NSG zugeordnet werden. Den einzelnen Netzwerkschnittstellen oder Subnetzen kann die gleiche oder eine andere NSG zugeordnet werden. Sie können dieselbe Netzwerksicherheitsgruppe beliebig vielen Netzwerkschnittstellen und Subnetzen zuordnen.

Effektive Sicherheitsregeln können nicht nur über die VM angezeigt werden, sondern auch jeweils über Folgendes:
- **Netzwerkschnittstelle** : Erfahren Sie, wie [eine Netzwerkschnittstelle angezeigt wird](virtual-network-network-interface.md#view-network-interface-settings).
- **NSG** : Erfahren Sie, wie [eine NSG angezeigt wird](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Diagnose mit PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Sie können die nachfolgenden Befehle in [Azure Cloud Shell](https://shell.azure.com/powershell) oder über PowerShell auf Ihrem Computer ausführen. Azure Cloud Shell ist eine kostenlose interaktive Shell. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. Wenn Sie PowerShell auf Ihrem Computer ausführen, müssen Sie das Azure PowerShell-Modul Version 1.0.0 oder höher ausführen. Führen Sie `Get-Module -ListAvailable Az` auf Ihrem Computer aus, um nach der installierten Version zu suchen. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um sich bei Azure mit einem Konto anzumelden, das über die [erforderlichen Berechtigungen](virtual-network-network-interface.md#permissions) verfügt.

Rufen Sie mit [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup) die effektiven Sicherheitsregeln für eine Netzwerkschnittstelle ab. Im folgenden Beispiel werden die effektiven Sicherheitsregeln für eine Netzwerkschnittstelle mit dem Namen *myVMVMNic* abgerufen, die sich in einer Ressourcengruppe mit dem Namen *myResourceGroup* befindet:

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

Die Ausgabe wird im JSON-Format zurückgegeben. Informationen zur Ausgabe finden Sie unter [Interpretieren der Befehlsausgabe](#interpret-command-output).
Die Ausgabe wird nur dann zurückgegeben, wenn einer NSG die Netzwerkschnittstelle, das Subnetz, in dem sich die Netzwerkschnittstelle befindet, oder beides zugeordnet ist. Die VM muss sich im Ausführungsstatus befinden. Eine VM kann mehrere Netzwerkschnittstellen aufweisen, auf die verschiedene NSGs angewendet sind. Führen Sie den Befehl bei der Problembehandlung für jede Netzwerkschnittstelle aus.

Wenn das Konnektivitätsproblem weiterhin besteht, lesen Sie die Abschnitte [Zusätzliche Diagnose](#additional-diagnosis) und [Überlegungen](#considerations).

Wenn Sie den Namen einer Netzwerkschnittstelle nicht kennen, aber den Namen der VM, der die Netzwerkschnittstelle angefügt ist, geben die folgenden Befehle die IDs aller Netzwerkschnittstellen zurück, die einer VM angefügt sind:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Eine Ausgabe ähnlich wie im folgenden Beispiel wird angezeigt:

```output
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

In der vorherigen Ausgabe lautete der Netzwerkschnittstellenname *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnose über die Azure CLI

Wenn Sie Befehle der Azure-Befehlszeilenschnittstelle (CLI) zum Durchführen von Aufgaben in diesem Artikel verwenden, führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/bash) oder durch Ausführen der CLI auf Ihrem Computer aus. Für diesen Artikel ist die Azure CLI-Version 2.0.32 oder höher erforderlich. Führen Sie `az --version` aus, um die installierte Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli). Wenn Sie die Azure CLI lokal ausführen, müssen Sie auch `az login` ausführen, um sich bei Azure mit einem Konto anzumelden, das über die [erforderlichen Berechtigungen](virtual-network-network-interface.md#permissions) verfügt.

Rufen Sie mit [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg) die effektiven Sicherheitsregeln für eine Netzwerkschnittstelle ab. Im folgenden Beispiel werden die effektiven Sicherheitsregeln für eine Netzwerkschnittstelle mit dem Namen *myVMVMNic* abgerufen, die sich in einer Ressourcengruppe mit dem Namen *myResourceGroup* befindet:

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Die Ausgabe wird im JSON-Format zurückgegeben. Informationen zur Ausgabe finden Sie unter [Interpretieren der Befehlsausgabe](#interpret-command-output).
Die Ausgabe wird nur dann zurückgegeben, wenn einer NSG die Netzwerkschnittstelle, das Subnetz, in dem sich die Netzwerkschnittstelle befindet, oder beides zugeordnet ist. Die VM muss sich im Ausführungsstatus befinden. Eine VM kann mehrere Netzwerkschnittstellen aufweisen, auf die verschiedene NSGs angewendet sind. Führen Sie den Befehl bei der Problembehandlung für jede Netzwerkschnittstelle aus.

Wenn das Konnektivitätsproblem weiterhin besteht, lesen Sie die Abschnitte [Zusätzliche Diagnose](#additional-diagnosis) und [Überlegungen](#considerations).

Wenn Sie den Namen einer Netzwerkschnittstelle nicht kennen, aber den Namen der VM, der die Netzwerkschnittstelle angefügt ist, geben die folgenden Befehle die IDs aller Netzwerkschnittstellen zurück, die einer VM angefügt sind:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

In der zurückgegebenen Ausgabe finden Sie Informationen wie etwa im folgenden Beispiel:

```output
"networkProfile": {
    "additionalProperties": {},
    "networkInterfaces": [
      {
        "additionalProperties": {},
        "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
        "primary": true,
        "resourceGroup": "myResourceGroup"
      },
```

In der vorangehenden Ausgabe lautet der Netzwerkschnittstellenname *myVMVMNic interface*.

## <a name="interpret-command-output"></a>Interpretieren der Befehlsausgabe

Unabhängig davon, ob Sie [PowerShell](#diagnose-using-powershell) oder die [Azure CLI](#diagnose-using-azure-cli) zum Diagnostizieren eines Problems verwendet haben, erhalten Sie eine Ausgabe mit den folgenden Informationen:

- **NetworkSecurityGroup** : Die ID der Netzwerksicherheitsgruppe.
- **Zuordnung** : Gibt an, ob die Netzwerksicherheitsgruppe *NetworkInterface* oder *Subnet* zugeordnet ist. Wenn eine NSG beidem zugeordnet ist, wird die Ausgabe mit **NetworkSecurityGroup** , **Association** und **EffectiveSecurityRules** für jede NSG zurückgegeben. Wenn die NSG unmittelbar vor dem Ausführen dieses Befehls zum Anzeigen der effektiven Sicherheitsregeln zugeordnet oder ihre Zuordnung aufgehoben wird, müssen Sie möglicherweise einen Moment warten, damit die Änderung in der Befehlsausgabe angezeigt wird.
- **EffectiveSecurityRules** : Eine Erläuterung der einzelnen Eigenschaften finden Sie unter [Erstellen einer Sicherheitsregel](manage-network-security-group.md#create-a-security-rule). Regelnamen mit vorangestellten *defaultSecurityRules/* sind Standardsicherheitsregeln, die in jede NSG vorhanden sind. Regelnamen mit vorangestellten *securityRules/* sind Regeln, die Sie erstellt haben. Regeln, die ein [Diensttag](security-overview.md#service-tags) angeben (z.B. **Internet** , **VirtualNetwork** und **AzureLoadBalancer** für die Eigenschaften **destinationAddressPrefix** oder **sourceAddressPrefix** ), weisen auch Werte für die Eigenschaft **expandedDestinationAddressPrefix** auf. Die Eigenschaft **expandedDestinationAddressPrefix** listet alle Adresspräfixe auf, die von dem Diensttag dargestellt werden.

Wenn doppelte Regeln in der Ausgabe aufgeführt werden, so ist dies darauf zurückzuführen, dass eine NSG sowohl der Netzwerkschnittstelle als auch dem Subnetz zugeordnet ist. Beide NSGs weisen die gleichen Standardregeln und möglicherweise weitere doppelte Regeln auf, wenn Sie eigene Regeln erstellt haben, die in beiden NSGs identisch sind.

Die Regel mit dem Namen **defaultSecurityRules/DenyAllInBound** verhindert eingehende Kommunikation mit der VM über Port 80 über das Internet, wie im [Szenario](#scenario) beschrieben wird. Keine andere Regel mit einer höheren Priorität (niedrigere Zahl) lässt den eingehenden Port 80 über das Internet zu.

## <a name="resolve-a-problem"></a>Beheben eines Problems

Unabhängig davon, ob Sie das im [Szenario](#scenario) dieses Artikels dargestellte Problem über das [Azure-Portal](#diagnose-using-azure-portal), über [PowerShell](#diagnose-using-powershell) oder über die [Azure CLI](#diagnose-using-azure-cli) diagnostizieren, die Lösung besteht darin, eine Netzwerksicherheitsregel mit den folgenden Eigenschaften zu erstellen:

| Eigenschaft                | value                                                                              |
|---------                |---------                                                                           |
| `Source`                  | Any                                                                                |
| Source port ranges      | Any                                                                                |
| Destination             | Die IP-Adresse der VM, ein Bereich von IP-Adressen oder alle Adressen im Subnetz. |
| Zielportbereiche | 80                                                                                 |
| Protocol                | TCP                                                                                |
| Action                  | Allow                                                                              |
| Priority                | 100                                                                                |
| Name                    | Allow-HTTP-All                                                                     |

Nachdem Sie die Regel erstellt haben, wird Port 80 eingehender Datenverkehr aus dem Internet erlaubt, da die Priorität der Regel höher ist als die Standardsicherheitsregel mit dem Namen *DenyAllInBound* , die den Datenverkehr ablehnt. Weitere Informationen finden Sie unter [Erstellen einer Sicherheitsregel](manage-network-security-group.md#create-a-security-rule). Wenn verschiedene NSGs sowohl der Netzwerkschnittstelle als auch dem Subnetz zugeordnet sind, müssen Sie die gleiche Regel in beiden NSGs erstellen.

Wenn eingehender Datenverkehr in Azure verarbeitet wird, werden die Regeln in der NSG, die dem Subnetz zugeordnet sind (sofern eine zugeordnete NSG vorhanden ist), und dann die Regeln in der NSG verarbeitet, die der Netzwerkschnittstelle zugeordnet sind. Ist eine NSG der Netzwerkschnittstelle und dem Subnetz zugeordnet, muss der Port in beiden NSGs geöffnet sein, damit der Datenverkehr die VM erreicht. Um Probleme mit der Verwaltung und der Kommunikation einfacher beheben zu können, wird empfohlen, eine NSG einem Subnetz zuzuordnen statt einzelnen Netzwerkschnittstellen. Wenn VMs in einem Subnetz verschiedene Sicherheitsregeln erfordern, können Sie die Netzwerkschnittstellen zu Mitgliedern einer Anwendungssicherheitsgruppe (ASG) machen und eine ASG als Quelle und Ziel einer Sicherheitsregel angeben. Weitere Informationen finden Sie unter [Anwendungssicherheitsgruppen](security-overview.md#application-security-groups).

Falls weiterhin Probleme mit der Kommunikation bestehen, lesen Sie die Abschnitte [Überlegungen](#considerations) und „Zusätzliche Diagnose“.

## <a name="considerations"></a>Überlegungen

Beachten Sie bei der Problembehandlung von Verbindungsproblemen die folgenden Punkte:

* Die Standardsicherheitsregeln blockieren den eingehenden Zugriff über das Internet und lassen nur eingehenden Datenverkehr über das virtuelle Netzwerk zu. Um eingehenden Datenverkehr aus dem Internet zuzulassen, fügen Sie Sicherheitsregeln mit einer höheren Priorität als der der Standardregeln hinzu. Erfahren Sie mehr über [Standardsicherheitsregeln](security-overview.md#default-security-rules), oder das [Erstellen einer Sicherheitsregel](manage-network-security-group.md#create-a-security-rule).
* Beim Peering virtueller Netzwerke wird das Diensttag **VIRTUAL_NETWORK** standardmäßig automatisch erweitert, sodass Präfixe für die per Peering verknüpften virtuellen Netzwerke einbezogen werden. Um Probleme im Hinblick auf Peerings virtueller Netzwerke zu beheben, können Sie diese Präfixe in der Liste **ExpandedAddressPrefix** anzeigen. Weitere Informationen hierzu finden Sie unter [Peering virtueller Netzwerke](virtual-network-peering-overview.md) und [Diensttags](security-overview.md#service-tags).
* Effektive Sicherheitsregeln werden nur dann für eine Netzwerkschnittstelle angezeigt, wenn der Netzwerkschnittstelle und/oder dem Subnetz der VM eine NSG zugeordnet ist und die VM den Ausführungsstatus aufweist.
* Wenn der Netzwerkschnittstelle oder dem Subnetz keine NSGs zugeordnet sind und der VM eine [öffentliche IP-Adresse](virtual-network-public-ip-address.md) zugewiesen ist, sind alle Ports für den eingehenden und ausgehenden Zugriff auf beliebige Ziele geöffnet. Wenn die VM eine öffentliche IP-Adresse aufweist, sollten Sie eine NSG auf das Subnetz der Netzwerkschnittstelle anwenden.

## <a name="additional-diagnosis"></a>Zusätzliche Diagnose

* Um anhand eines Schnelltests festzustellen, ob Datenverkehr an eine oder von einer VM zugelassen wird, verwenden Sie die Funktion [IP-Fluss überprüfen](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) von Azure Network Watcher. Die Überprüfung des IP-Flusses gibt Aufschluss darüber, ob Datenverkehr zugelassen oder abgelehnt wird. Falls dieser verweigert wird, können Sie mit der Überprüfung des IP-Flusses feststellen, durch welche Sicherheitsregel der Datenverkehr abgelehnt wird.
* Wenn bei der Netzwerkkonnektivität einer VM nicht aufgrund von Sicherheitsregeln Fehler auftreten, kann das Problem folgende Ursachen haben:
  * Firewallsoftware, die im Betriebssystem des virtuellen Computers ausgeführt wird
  * Es sind Routen für virtuelle Geräte oder den lokalen Datenverkehr konfiguriert. Der Internetdatenverkehr kann über die [Tunnelerzwingung](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) an Ihr lokales Netzwerk umgeleitet werden. Wenn Sie die Tunnelerzwingung für den Internetdatenverkehr auf eine virtuelle Appliance oder einem lokalen Gerät erzwingen, können Sie möglicherweise keine Verbindung mit der VM über das Internet herstellen. Um zu erfahren, wie Sie Routingprobleme diagnostizieren, die möglicherweise den ausgehenden Datenverkehrsfluss der VM beeinträchtigen, lesen Sie [Diagnostizieren eines Problems mit der Weiterleitung von Netzwerkdatenverkehr virtueller Computer](diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über alle Aufgaben, Eigenschaften und Einstellungen für eine [Netzwerksicherheitsgruppe](manage-network-security-group.md#work-with-network-security-groups) und für [Sicherheitsregeln](manage-network-security-group.md#work-with-security-rules).
- Erfahren Sie mehr über [Standardsicherheitsregeln](security-overview.md#default-security-rules), [Diensttags](security-overview.md#service-tags) und die [Verarbeitung von Sicherheitsregeln für eingehenden und ausgehenden Datenverkehr in Azure](security-overview.md#network-security-groups) für eine VM.
