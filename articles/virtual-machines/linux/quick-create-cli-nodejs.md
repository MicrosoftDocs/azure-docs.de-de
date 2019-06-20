---
title: Erstellen eines virtuellen Linux-Computers mit der klassischen Azure-Befehlszeilenschnittstelle | Microsoft-Dokumentation
description: Erstellen eines virtuellen Linux-Computers in Azure mithilfe der klassischen Azure-Befehlszeilenschnittstelle
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
ms.assetid: facb1115-2b4e-4ef3-9905-330e42beb686
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2016
ms.author: v-livech
ms.openlocfilehash: 569e90c7908ce435689a80f7917b20275703f537
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61473738"
---
# <a name="create-a-linux-vm-using-the-azure-classic-cli"></a>Erstellen eines virtuellen Linux-Computers mit der klassischen Azure-Befehlszeilenschnittstelle

In diesem Artikel wird erläutert, wie Sie mit dem Befehl `azure vm quick-create` in der Azure-Befehlszeilenschnittstelle (CLI) schnell einen virtuellen Linux-Computer (VM) bereitstellen können. Der Befehl `quick-create` stellt eine VM in einer sicheren Basisinfrastruktur bereit, mit der Sie rasch einen Prototyp erstellen oder ein Konzept testen können.

> [!NOTE]
> Wenn Sie einen virtuellen Computer mit der Azure-Befehlszeilenschnittstelle erstellen möchten, lesen Sie unter [Schnellstart: Erstellen eines virtuellen Windows-Computers per Azure CLI 2.0](../windows/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) weiter.

Sie können eine Linux-VM auch schnell über das [Azure-Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)bereitstellen.

Für den Artikel sind [Dateien mit den öffentlichen und privaten SSH-Schlüsseln](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) erforderlich.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="quick-commands"></a>Schnellbefehle

Das folgende Beispiel zeigt, wie Sie eine CoreOS-VM bereitstellen und Ihren SSH-Schlüssel (Secure Shell) anfügen (Ihre Argumente können sich von den hier verwendeten Argumenten unterscheiden):

```azurecli
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q CoreOS
```

## <a name="detailed-walkthrough"></a>Ausführliche exemplarische Vorgehensweise

In der folgenden exemplarischen Vorgehensweise wird eine UbuntuLTS-VM bereitgestellt. Dabei werden sämtliche Schritte ausführlich erläutert.

## <a name="vm-quick-create-aliases"></a>Aliase für die Schnellerstellung mit „vm quick-create“

Eine schnelle Möglichkeit zum Auswählen einer Distribution ist die Verwendung der entsprechenden Azure-CLI-Aliase für die am häufigsten verwendeten OS-Distributionen. Die folgende Tabelle enthält eine Liste der Aliase (ab Azure-CLI Version 0.10). Bei allen Bereitstellungen mit `quick-create` werden standardmäßig VMs mit SSD-Speicher (Solid State Drive) erstellt, die eine schnellere Bereitstellung und hohe Leistung für den Datenträgerzugriff bieten. (Diese Aliase stellen einen kleinen Teil der in Azure verfügbaren Distributionen dar. Sie können im Azure Marketplace auf weitere Images zugreifen, indem Sie [in PowerShell](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) oder [im Internet nach einem Image suchen](https://azure.microsoft.com/marketplace/virtual-machines/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Sie können auch ein eigenes [benutzerdefiniertes Image hochladen](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).)

| Alias | Herausgeber | Angebot | SKU | Version |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |CentOS |7.2 |neueste |
| CoreOS |CoreOS |CoreOS |Stable |neueste |
| Debian |credativ |Debian |8 |neueste |
| openSUSE |SUSE |openSUSE |13.2 |neueste |
| RHEL |Red Hat |RHEL |7.2 |neueste |
| UbuntuLTS |Canonical |Ubuntu Server |14.04.4-LTS |neueste |

In den folgenden Abschnitten wird der Alias `UbuntuLTS` für die Option **ImageURN** (`-Q`) verwendet, um einen Server mit Ubuntu 14.04.4 LTS bereitzustellen.

Im vorherigen Beispiel für `quick-create` wurde nur das Flag `-M` aufgerufen, um den hochzuladenden öffentlichen SSH-Schlüssel zu identifizieren, während SSH-Kennwörter deaktiviert wurden. Sie werden daher zum Angeben der folgenden Argumente aufgefordert:

* Ressourcengruppenname (für Ihre erste Azure-Ressourcengruppe ist in der Regel eine beliebige Zeichenfolge ausreichend)
* Name des virtuellen Computers
* Standort (`westus` oder `westeurope` sind gute Standardwerte)
* Linux (um Azure das zu verwendende Betriebssystem mitzuteilen)
* userName

Da im folgenden Beispiel alle Werte angegeben werden, sind keine weiteren Eingabeaufforderungen erforderlich. Sofern Sie über eine Datei `~/.ssh/id_rsa.pub` als öffentliche Schlüsseldatei im ssh-rsa-Format verfügen, sind keine Änderungen erforderlich:

```azurecli
azure vm quick-create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --admin-username myAdminUser \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --image-urn UbuntuLTS
```

Die Ausgabe sollte dem folgenden Ausgabeblock ähneln:

```azurecli
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "myVM"
info:    Verifying the public key SSH file: /Users/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli16330708391032639673
+ Looking up the NIC "examp-westu-1633070839-nic"
info:    An nic with given name "examp-westu-1633070839-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-1633070839-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "examp-westu-1633070839-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-1633070839-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-1633070839-vnet"
+ Looking up the subnet "examp-westu-1633070839-snet" under the virtual network "examp-westu-1633070839-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-1633070839-pip"
info:    PublicIP with given name "examp-westu-1633070839-pip" not found, creating a new one
+ Creating public ip "examp-westu-1633070839-pip"
+ Looking up the public ip "examp-westu-1633070839-pip"
+ Creating NIC "examp-westu-1633070839-nic"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the storage account clisto1710997031examplev
+ Creating VM "myVM"
+ Looking up the VM "myVM"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the public ip "examp-westu-1633070839-pip"
data:    Id                              :/subscriptions/2<--snip-->d/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMName
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMName
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic7fadb847357e9cf-os-1473374894359
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli16330708391032639673.blob.core.windows.net/vhds/clic7fadb847357e9cf-os-1473374894359.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM
data:      User Name                     :myAdminUser
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-42-FB
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-1633070839-nic
data:          Location                  :westus
data:            Public IP address       :138.91.247.29
data:            FQDN                    :examp-westu-1633070839-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto1710997031examplev.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

## <a name="log-in-to-the-new-vm"></a>Anmelden auf dem neuen virtuellen Computer
Melden Sie sich mit der in der Ausgabe aufgeführten öffentlichen IP-Adresse bei Ihrer VM an. Sie können auch den vollständig qualifizierten Domänennamen (FQDN) verwenden, der in der Ausgabe angegeben ist:

```bash
ssh -i ~/.ssh/id_rsa.pub ahmet@138.91.247.29
```

Der Anmeldeprozess sollte etwa wie der folgende Ausgabeblock aussehen:

```bash
Warning: Permanently added '138.91.247.29' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Sep  8 22:50:57 UTC 2016

  System load: 0.63              Memory usage: 2%   Processes:       81
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

myAdminUser@myVM:~$
```

## <a name="next-steps"></a>Nächste Schritte
Der Befehl `azure vm quick-create` ermöglicht die schnelle Bereitstellung einer VM – Sie können sich bei einer Bash-Shell anmelden und mit der Arbeit beginnen. Bei der Verwendung von `vm quick-create` haben Sie jedoch weder umfassende Kontrolle, noch können Sie eine komplexere Umgebung erstellen.  Informationen zum Bereitstellen einer für Ihre Infrastruktur angepassten Linux-VM finden Sie in den Artikeln zu folgenden Themen:

* [Direktes Erstellen einer benutzerdefinierten Umgebung für einen virtuellen Linux-Computer über Azure-CLI-Befehle](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Erstellen einer SSH-geschützten Linux-VM mit einer Azure-Vorlage](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Sie können auch den [Azure-Treiber `docker-machine` mit verschiedenen Befehlen verwenden, um eine Linux-VM schnell als Docker-Host zu erstellen](docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
