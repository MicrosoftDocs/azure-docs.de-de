---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: beece95164f0d82b1aa7f22d56f4dce02f4bb38c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66117258"
---
Mit Azure CLI können Sie Ihre Azure-Ressourcen unter macOS, Linux und Windows erstellen und verwalten. In diesem Artikel werden einige der am häufigsten verwendeten Befehle zum Erstellen und Verwalten von virtuellen Computern (VMs) erläutert.

Für diesen Artikel ist mindestens Version 2.0.4 der Azure CLI erforderlich. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) weitere Informationen. Sie können auch [Cloud Shell](/azure/cloud-shell/quickstart) in Ihrem Browser verwenden.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Grundlegende Azure Resource Manager-Befehle in der Azure-Befehlszeilenschnittstelle
Ausführlichere Informationen zu bestimmten Befehlszeilenschaltern und -optionen finden Sie in der Onlinehilfe zu Befehlen und Optionen, die Sie durch Eingeben von `az <command> <subcommand> --help` aufrufen können.

### <a name="create-vms"></a>Virtuelle Computer erstellen
| Aufgabe | Azure-CLI-Befehle |
| --- | --- |
| Erstellen einer Ressourcengruppe | `az group create --name myResourceGroup --location eastus` |
| Erstellen eines virtuellen Linux-Computers | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| Erstellen eines virtuellen Windows-Computers | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>Verwalten des VM-Status
| Aufgabe | Azure-CLI-Befehle |
| --- | --- |
| Starten eines virtuellen Computers | `az vm start --resource-group myResourceGroup --name myVM` |
| Anhalten eines virtuellen Computers | `az vm stop --resource-group myResourceGroup --name myVM` |
| Aufheben der Zuordnung eines virtuellen Computers | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| Neustarten eines virtuellen Computers | `az vm restart --resource-group myResourceGroup --name myVM` |
| Erneutes Bereitstellen eines virtuellen Computers | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| Löschen eines virtuellen Computers | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>Abrufen von VM-Informationen
| Aufgabe | Azure-CLI-Befehle |
| --- | --- |
| Auflisten der virtuellen Computer | `az vm list` |
| Abrufen von Informationen zu einem virtuellen Computer | `az vm show --resource-group myResourceGroup --name myVM` |
| Abrufen der Nutzung virtueller Computerressourcen | `az vm list-usage --location eastus` |
| Abrufen Sie aller verfügbaren Größen von virtuellen Computern | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>Datenträger und Images
| Aufgabe | Azure-CLI-Befehle |
| --- | --- |
| Hinzufügen eines Datenträgers zu einem virtuellen Computer | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new` |
| Entfernen eines Datenträgers aus einem virtuellen Computer | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| Ändern der Größe eines Datenträgers | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| Erstellen einer Momentaufnahme eines Datenträgers | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| Erstellen eines Images einer VM | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| Erstellen einer VM aus einem Image | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>Nächste Schritte
Weitere Beispiele für CLI-Befehle finden Sie im Tutorial [Erstellen und Verwalten virtueller Linux-Computer mit der Azure-Befehlszeilenschnittstelle](../articles/virtual-machines/linux/tutorial-manage-vm.md).

