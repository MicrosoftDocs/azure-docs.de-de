---
title: Erstellen eines virtuellen Azure-Computers mit beschleunigtem Netzwerkbetrieb mithilfe der Azure CLI
description: Weitere Informationen finden Sie unter „Erstellen eines virtuellen Linux-Computers mit beschleunigtem Netzwerkbetrieb“.
services: virtual-network
documentationcenter: na
author: gsilva5
manager: gedegrac
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: bccbfed96dd6cd87bdfe986baf4b52817a160ac0
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95533360"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking-using-azure-cli"></a>Erstellen eines virtuellen Linux-Computers mit beschleunigtem Netzwerkbetrieb mithilfe der Azure CLI

In diesem Tutorial erfahren Sie, wie Sie einen virtuellen Linux-Computer (VM) mit beschleunigtem Netzwerkbetrieb erstellen. Informationen zum Erstellen einer Windows-VM mit beschleunigtem Netzwerkbetrieb finden Sie unter [Erstellen eines virtuellen Windows-Computers mit beschleunigtem Netzwerkbetrieb](create-vm-accelerated-networking-powershell.md). Der beschleunigte Netzwerkbetrieb ermöglicht die E/A-Virtualisierung mit Einzelstamm (Single Root I/O Virtualization, SR-IOV) in einer VM und somit eine erhebliche Steigerung der Netzwerkleistung. Über diesen für Hochleistung konzipierten Pfad wird der Host des Datenpfads umgangen, um Latenzen, Jitter und CPU-Auslastung zu verringern. So können mit unterstützten VM-Typen die anspruchsvollsten Netzwerkworkloads genutzt werden. Die folgende Abbildung zeigt die Kommunikation zwischen zwei VMs mit und ohne beschleunigten Netzwerkbetrieb:

![Vergleich](./media/create-vm-accelerated-networking/accelerated-networking.png)

Ohne beschleunigten Netzwerkbetrieb muss sämtlicher Netzwerkdatenverkehr zur und von der VM den Host und virtuellen Switch passieren. Der virtuelle Switch setzt alle Richtlinien für den Netzwerkdatenverkehr um, z.B. Netzwerksicherheitsgruppen, Zugriffssteuerungslisten, Isolation und andere virtualisierte Netzwerkdienste. Weitere Informationen zu virtuellen Switches finden Sie im Artikel mit einer [Übersicht über Hyper-V-Netzwerkvirtualisierung und virtuellen Switch](https://technet.microsoft.com/library/jj945275.aspx).

Mit beschleunigtem Netzwerkbetrieb gelangt der Netzwerkdatenverkehr zur Netzwerkkarte (NIC) der VM und wird dann an die VM weitergeleitet. Alle Netzwerkrichtlinien, die vom virtuellen Switch angewendet werden, werden ab sofort ausgelagert und in der Hardware angewendet. Durch das Anwenden von Richtlinien in der Hardware kann die NIC Netzwerkdatenverkehr direkt an den virtuellen Computer weiterleiten und dabei den Host und den virtuellen Switch umgehen, während gleichzeitig sämtliche Richtlinien umgesetzt werden, die sonst auf dem Host angewendet worden wären.

Die Vorteile des beschleunigten Netzwerkbetriebs gelten nur für die VM, auf der dieses Feature aktiviert ist. Die besten Ergebnisse erzielen Sie, wenn Sie dieses Feature auf mindestens zwei VMs aktivieren, die mit demselben virtuellen Azure-Netzwerk (VNET) verbunden sind. Bei der Kommunikation über VNets oder bei lokalen Verbindungen hat dieses Feature nur minimale Auswirkungen auf die Wartezeit.

## <a name="benefits"></a>Vorteile
* **Niedrigere Latenz / mehr Pakete pro Sekunde (pps):** Durch das Entfernen des virtuellen Switch aus dem Datenpfad wird die Zeit reduziert, die Pakete auf dem Host für die Verarbeitung von Richtlinien warten müssen. Gleichzeitig wird die Anzahl von Paketen erhöht, die im virtuellen Computer verarbeitet werden können.
* **Reduzierte Jitter:** Die Verarbeitung im virtuellen Switch hängt vom Umfang der anzuwendenden Richtlinien und von der Workload der CPU ab, die die Verarbeitung durchführt. Das Auslagern der Richtlinienerzwingung auf die Hardware entfernt diesen Umweg, indem die Pakete direkt an den virtuellen Computer gesendet werden. Damit werden der Host und alle Softwareinterrupts und Kontextwechsel aus der VM-Kommunikation entfernt.
* **Verringerte CPU-Auslastung:** Das Umgehen des virtuellen Switch auf dem Host führt zu weniger CPU-Auslastung für die Verarbeitung des Netzwerkdatenverkehrs.

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme
Die folgenden Distributionen werden standardmäßig aus dem Azure-Katalog unterstützt: 
* **Ubuntu 14.04 mit dem Azure-Linux-Kernel**
* **Ubuntu 16.04 oder höher** 
* **SLES12 SP3 oder höher** 
* **RHEL 7.4 oder höher**
* **CentOS 7.4 oder höher**
* **CoreOS Linux**
* **Debian „Stretch“ mit Backports-Kernel, Debian „Buster“ oder höher**
* **Oracle Linux 7.4 und höher mit Red Hat Compatible Kernel (RHCK)**
* **Oracle Linux 7.5 und höher mit UEK-Version 5**
* **FreeBSD 10.4, 11.1 und 12.0 oder höher**

## <a name="limitations-and-constraints"></a>Einschränkungen

### <a name="supported-vm-instances"></a>Unterstützte VM-Instanzen
Der beschleunigte Netzwerkbetrieb wird in den meisten universellen, computeoptimierten Instanzgrößen mit mindestens 2 vCPUs unterstützt.  Folgende Reihen werden unterstützt: D/DSv2 und F/Fs

Bei Instanzen, die Hyperthreading unterstützen, wird der beschleunigte Netzwerkbetrieb auf VM-Instanzen mit mindestens 4 vCPUs unterstützt. Folgende Reihen werden unterstützt: D/Dsv3, D/Dsv4, Dd/Ddv4, Da/Dasv4, E/Esv3, E/Esv4, Ed/Edsv4, Ea/Easv4, Fsv2, Lsv2, Ms/Mms und Ms/Mmsv2.

Weitere Informationen zu VM-Instanzen finden Sie unter [Größen für virtuelle Linux-Computer in Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="custom-images"></a>Custom Images
Wenn Sie ein benutzerdefiniertes Image verwenden und dieses den beschleunigten Netzwerkbetrieb unterstützt, stellen Sie sicher, dass Sie über die erforderlichen Treiber verfügen, damit Mellanox ConnectX-3- und ConnectX-4 LX-Netzwerkadapter in Azure unterstützt werden.

### <a name="regions"></a>Regions
Verfügbar in allen öffentlichen Azure-Regionen und in Azure Government-Clouds

<!-- ### Network interface creation 
Accelerated networking can only be enabled for a new NIC. It cannot be enabled for an existing NIC.
removed per issue https://github.com/MicrosoftDocs/azure-docs/issues/9772 -->
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Aktivieren des beschleunigten Netzwerkbetriebs auf einer ausgeführten VM
Bei einer unterstützten VM-Größe ohne aktivierten beschleunigten Netzwerkbetrieb kann das Feature nur aktiviert werden, wenn die VM beendet und ihre Zuordnung aufgehoben wird.  
### <a name="deployment-through-azure-resource-manager"></a>Bereitstellung über Azure Resource Manager
Virtuelle Computer (klassisch) können nicht mit beschleunigtem Netzwerkbetrieb bereitgestellt werden.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Erstellen eines virtuellen Linux-Computers mit beschleunigtem Netzwerkbetrieb
## <a name="portal-creation"></a>Erstellung über das Portal
In diesem Artikel werden die Schritte zum Erstellen eines virtuellen Computers mit beschleunigtem Netzwerkbetrieb mithilfe der Azure-Befehlszeilenschnittstelle dargestellt, Sie können jedoch auch [über das Azure-Portal einen virtuellen Computer mit beschleunigtem Netzwerkbetrieb erstellen](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Wenn Sie einen virtuellen Computer im Portal erstellen, wählen Sie im Blatt **Virtuellen Computer erstellen** die Registerkarte **Netzwerkbetrieb** aus.  Auf dieser Registerkarte gibt es eine Option für **Beschleunigter Netzwerkbetrieb**.  Wenn Sie ein [unterstütztes Betriebssystem](#supported-operating-systems) und eine [unterstützte VM-Größe](#supported-vm-instances) ausgewählt haben, wird diese Option automatisch auf „Ein“ gesetzt.  Wenn nicht, wird die Option für „Beschleunigter Netzwerkbetrieb“ auf „Aus“ festgelegt, und dem Benutzer wird der Grund angezeigt, warum der beschleunigte Netzwerkbetrieb nicht aktiviert wird.   

* *Hinweis:* Nur unterstützte Betriebssysteme können über das Portal aktiviert werden.  Wenn Sie ein benutzerdefiniertes Image verwenden und Ihr Image den beschleunigten Netzwerkbetrieb unterstützt, erstellen Sie Ihre VM mit CLI oder PowerShell. 

Nach dem Erstellen des virtuellen Computers können Sie sich vergewissern, dass der beschleunigte Netzwerkbetrieb aktiviert ist, indem Sie den Anweisungen unter [Bestätigen der Aktivierung des beschleunigten Netzwerkbetriebs](#confirm-that-accelerated-networking-is-enabled) folgen.

## <a name="cli-creation"></a>CLI-Erstellung
### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Installieren Sie die neueste Version der [Azure CLI](/cli/azure/install-azure-cli), und melden Sie sich mit [az login](/cli/azure/reference-index) bei einem Azure-Konto an. Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen durch Ihre eigenen Werte. Zu Parameternamen zählen z.B. *myResourceGroup*, *myNic* und *myVm*.

Erstellen Sie mit [az group create](/cli/azure/group) eine Ressourcengruppe. Im folgenden Beispiel wird am Standort *centralus* eine Ressourcengruppe namens *myResourceGroup* erstellt:

```azurecli
az group create --name myResourceGroup --location centralus
```

Wählen Sie aus der Auflistung unter [Beschleunigter Netzwerkbetrieb für Linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview) eine unterstützte Linux-Region aus.

Erstellen Sie mit [az network vnet create](/cli/azure/network/vnet) ein virtuelles Netzwerk. Im folgenden Beispiel wird ein virtuelles Netzwerk namens *myVnet* mit einem Subnetz erstellt:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe
Erstellen Sie mit [az network nsg create](/cli/azure/network/nsg) eine Netzwerksicherheitsgruppe. Im folgenden Beispiel wird eine Netzwerksicherheitsgruppe namens *myNetworkSecurityGroup* erstellt:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Die Netzwerksicherheitsgruppe enthält mehrere Standardregeln, von denen eine sämtlichen eingehenden Zugriff über das Internet deaktiviert. Öffnen Sie mit [az network nsg rule create](/cli/azure/network/nsg/rule) einen Port, um SSH-Zugriff auf den virtuellen Computer zuzulassen:

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Erstellen einer Netzwerkschnittstelle mit beschleunigtem Netzwerkbetrieb

Erstellen Sie mit [az network public-ip create](/cli/azure/network/public-ip) eine öffentliche IP-Adresse. Wenn Sie nicht vorhaben, über das Internet auf den virtuellen Computer zuzugreifen, ist keine öffentliche IP-Adresse erforderlich – diese wird jedoch für die Durchführung der Schritte in diesem Artikel benötigt.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Erstellen Sie mit [az network nic create](/cli/azure/network/nic) eine Netzwerkschnittstelle mit aktiviertem beschleunigtem Netzwerkbetrieb. Im folgenden Beispiel wird eine Netzwerkschnittstelle mit dem Namen *myNic* im Subnetz *mySubnet* des virtuellen Netzwerks *myVnet* erstellt und die Netzwerksicherheitsgruppe  *myNetworkSecurityGroup* der Netzwerkschnittstelle zugeordnet:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

### <a name="create-a-vm-and-attach-the-nic"></a>Erstellen einer VM und Anfügen der NIC
Geben Sie bei der Erstellung der VM mit `--nics` die NIC an, die Sie erstellt haben. Wählen Sie aus der Auflistung unter [Beschleunigter Netzwerkbetrieb für Linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview) eine Größe und eine Distribution aus. 

Erstellen Sie mit [az vm create](/cli/azure/vm) einen virtuellen Computer. Im folgenden Beispiel wird eine VM namens *myVM* mit dem UbuntuLTS-Image und einer Größe erstellt, die beschleunigten Netzwerkbetrieb unterstützt (*Standard_DS4_v2*):

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

Eine Liste aller VM-Größen und -Eigenschaften finden Sie unter [Größen für virtuelle Linux-Computer in Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Nachdem die VM erstellt wurde, wird eine ähnliche Ausgabe wie die folgende Beispielausgabe zurückgegeben. Notieren Sie sich **publicIpAddress**. Diese Adresse wird später verwendet, um auf die VM zuzugreifen.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Bestätigen der Aktivierung des beschleunigten Netzwerkbetriebs

Erstellen Sie mit dem folgenden Befehl eine SSH-Sitzung mit dem virtuellen Computer. Ersetzen Sie `<your-public-ip-address>` durch die öffentliche IP-Adresse, die dem von Ihnen erstellten virtuellen Computer zugewiesen wurde, und ersetzen Sie *azureuser*, wenn Sie für `--admin-username` einen anderen Wert als bei der VM-Erstellung verwendet haben.

```bash
ssh azureuser@<your-public-ip-address>
```

Geben Sie in der Bash-Shell `uname -r` ein, und vergewissern Sie sich, dass es sich bei der Kernelversion um eine der folgenden Versionen oder eine höhere Version handelt:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Vergewissern Sie sich, dass mithilfe des Befehls `lspci` das Mellanox VF-Gerät für die VM verfügbar gemacht wurde. Die zurückgegebene Ausgabe sieht in etwa wie folgt aus:

```output
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Suchen Sie mithilfe des Befehl `ethtool -S eth0 | grep vf_` nach der Aktivität für die VF (virtuelle Funktion). Wenn Sie eine Ausgabe ähnlich wie die folgende Beispielausgabe erhalten, ist der beschleunigte Netzwerkbetrieb aktiviert und funktioniert.

```output
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Der beschleunigte Netzwerkbetrieb ist nun für Ihre VM aktiviert.

## <a name="handle-dynamic-binding-and-revocation-of-virtual-function"></a>Verarbeiten dynamischer Bindung und Sperrung der virtuellen Funktion 
Anwendungen müssen über die synthetische NIC ausgeführt werden, die in der VM bereitgestellt wird. Wenn die Anwendung direkt über die VF-NIC ausgeführt wird, empfängt sie nicht **alle** Pakete, die an die VM gerichtet sind, da einige Pakete über die synthetische Schnittstelle angezeigt werden.
Wenn Sie eine Anwendung über die synthetische NIC ausführen, ist garantiert, dass die Anwendung **alle** Pakete empfängt, die für sie bestimmt sind. Es wird außerdem sichergestellt, dass die Anwendung weiterhin ausgeführt wird, auch wenn VF während der Wartung des Hosts aufgehoben wird. Anwendungen, die eine Bindung an die synthetische NIC verwenden, sind eine **obligatorische** Voraussetzung für alle Anwendungen, die **beschleunigten Netzwerkbetrieb** nutzen.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Aktivieren des beschleunigten Netzwerkbetriebs auf vorhandenen VMs
Wenn Sie eine VM ohne beschleunigten Netzwerkbetrieb erstellt haben, können Sie dieses Feature auf dieser VM aktivieren.  Die VM muss folgende Voraussetzungen erfüllen, um den beschleunigten Netzwerkbetrieb zu unterstützen (siehe auch oben):

* Die VM muss eine unterstützte Größe für den beschleunigten Netzwerkbetrieb aufweisen.
* Bei der VM muss es sich um ein unterstütztes Image aus dem Azure-Katalog (und eine unterstützte Kernelversion für Linux) handeln.
* Alle VMs in einer Verfügbarkeitsgruppe oder VM-Skalierungsgruppe müssen beendet und ihre Zuordnung muss aufgehoben werden, bevor Sie den beschleunigten Netzwerkbetrieb in einer Netzwerkschnittstelle aktivieren können.

### <a name="individual-vms--vms-in-an-availability-set"></a>Einzelne VMs und VMs in einer Verfügbarkeitsgruppe
Beenden Sie die VM, und heben Sie ihre Zuordnung auf. Falls es sich um eine Verfügbarkeitsgruppe handelt, führen Sie diese Vorgänge für alle VMs in der Gruppe aus:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Wichtiger Hinweis: Wenn Ihre VM einzeln erstellt wurde (ohne Verfügbarkeitsgruppe), müssen Sie nur diese eine VM beenden und ihre Zuordnung aufheben, um den beschleunigten Netzwerkbetrieb zu aktivieren.  Wenn Ihre VM mit einer Verfügbarkeitsgruppe erstellt wurde, müssen alle VMs, die in dieser Verfügbarkeitsgruppe enthalten sind, beendet und deren Zuordnungen aufgehoben werden, bevor Sie den beschleunigten Netzwerkbetrieb in einer der Netzwerkschnittstellen aktivieren können. 

Aktivieren Sie dann den beschleunigten Netzwerkbetrieb in der Netzwerkschnittstelle Ihrer VM:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Starten Sie Ihre VM bzw. im Fall einer Verfügbarkeitsgruppe alle VMs in der Gruppe neu, und überprüfen Sie, ob der beschleunigte Netzwerkbetrieb aktiviert wurde: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VM-Skalierungssgruppe
Eine VM-Skalierungsgruppe funktioniert etwas anders, folgt aber dem gleichen Workflow.  Beenden Sie zuerst die VMs:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

Sobald alle VMs beendet sind, aktualisieren Sie die Eigenschaft für den beschleunigten Netzwerkbetrieb in der Netzwerkschnittstelle:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Beachten Sie, dass eine VM-Skalierungsgruppe VM-Upgrades hat, die Aktualisierungen entsprechend drei unterschiedlichen Einstellungen anwenden: „Automatisch“, „Parallel“ (Rolling) und „Manuell“.  In diesen Anweisungen wird die Richtlinie auf „Automatisch“ festgelegt, sodass die VM-Skalierungsgruppe die Änderungen sofort nach dem Neustart übernimmt.  So legen Sie die Einstellung auf „Automatisch“ fest, damit die Änderungen sofort übernommen werden: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

Starten Sie schließlich die VM-Skalierungsgruppe neu:

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

Warten Sie, bis die Upgrades nach dem Neustart vollständig abgeschlossen sind. Nach dem Abschluss wird das Feature in der VM angezeigt.  (Stellen Sie sicher, dass Sie ein unterstütztes Betriebssystem und eine unterstützte VM-Größe verwenden.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Ändern der Größe vorhandener VMs mit beschleunigtem Netzwerkbetrieb

VMs mit aktiviertem beschleunigtem Netzwerkbetrieb können nur zu VM-Größen geändert werden, die den beschleunigten Netzwerkbetrieb unterstützen.  

Eine VM mit aktiviertem beschleunigtem Netzwerkbetrieb kann nicht in eine VM-Instanz geändert werden, die den beschleunigten Netzwerkbetrieb nicht unterstützt.  Ändern Sie die VM-Größe stattdessen in eine der folgenden VMs: 

* Beenden Sie die VM, und heben Sie ihre Zuordnung auf. Falls es sich um eine VM-Skalierungsgruppe handelt, führen Sie diese Vorgänge für alle VMs in der Gruppe aus.
* Der beschleunigte Netzwerkbetrieb muss in der Netzwerkschnittstelle der VM bzw. im Fall einer Verfügbarkeits- oder VM-Skalierungsgruppe in den Netzwerkschnittstellen aller VMs deaktiviert werden.
* Sobald der beschleunigte Netzwerkbetrieb deaktiviert ist, kann die VM bzw. die Verfügbarkeits- oder VM-Skalierungsgruppe in eine neue Größe ohne Unterstützung des beschleunigten Netzwerkbetriebs geändert und neu gestartet werden.  

