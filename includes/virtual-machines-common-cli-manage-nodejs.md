---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: a4c9ec133b3686a92cec7e7c8d4552c1302e3074
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158526"
---
Bevor Sie die Azure-Befehlszeilenschnittstelle mit Resource Manager-Befehlen und -Vorlagen zur Bereitstellung von Azure-Ressourcen und -Workloads über Ressourcengruppen verwenden können, benötigen Sie ein Azure-Konto. Wenn Sie noch kein Konto haben, erhalten Sie [hier eine kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

Wenn Sie die Azure-Befehlszeilenschnittstelle noch nicht installiert und noch keine Verbindung mit Ihrem Abonnement hergestellt haben, lesen Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](../articles/cli-install-nodejs.md) weiter, legen Sie den Modus mithilfe von `azure config mode arm` auf `arm` fest, und stellen Sie mithilfe des Befehls `azure login` eine Verbindung mit Azure her.

## <a name="cli-versions-to-complete-the-task"></a>CLI-Versionen zum Durchführen dieser Aufgabe
Führen Sie die Aufgabe mit einer der folgenden CLI-Versionen durch:

- Klassische Azure-Befehlszeilenschnittstelle: Unsere CLI für das klassische Bereitstellungsmodell und das Resource Manager-Bereitstellungsmodell (in diesem Artikel)
- [Azure-Befehlszeilenschnittstelle](../articles/virtual-machines/linux/cli-manage.md) Unsere CLI der nächsten Generation für das Resource Manager-Bereitstellungsmodell

## <a name="basic-azure-resource-manager-commands-in-azure-classic-cli"></a>Grundlegende Azure Resource Manager-Befehle in der klassischen Azure-Befehlszeilenschnittstelle

Dieser Artikel behandelt die grundlegenden Befehle, die Sie mit der klassischen Azure-Befehlszeilenschnittstelle verwenden, um Ihre Ressourcen (hauptsächlich virtuelle Computer) im Azure-Abonnement zu verwalten und mit ihnen zu interagieren.  Ausführlichere Informationen zu bestimmten Befehlszeilenschaltern und -optionen finden Sie in der Onlinehilfe zu Befehlen und Optionen, die Sie durch Eingeben von `azure <command> <subcommand> --help` oder `azure help <command> <subcommand>` aufrufen können.

> [!NOTE]
> Diese Beispiele umfassen keine vorlagenbasierten Vorgänge, die im Allgemeinen für VM-Bereitstellungen im Ressourcen-Manager empfohlen werden. Informationen finden Sie unter [Use the Azure CLI to manage Azure resources and resource groups](../articles/xplat-cli-azure-resource-manager.md) (Verwalten von Azure-Ressourcen und -Ressourcengruppen mithilfe der Befehlszeilenschnittstelle) sowie unter [Bereitstellen und Verwalten von virtuellen Computern mit Azure Resource Manager-Vorlagen und der Azure-Befehlszeilenschnittstelle](../articles/virtual-machines/linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

| Aufgabe | Ressourcen-Manager |
| --- | --- |
| Erstellen grundlegender virtueller Computer |`azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(Ermitteln Sie `image-urn` mithilfe des Befehls `azure vm image list`. Beispiele finden Sie in [diesem Artikel](../articles/virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).) |
| Erstellen eines virtuellen Linux-Computers |`azure  vm create [options] <resource-group> <name> <location> -y "Linux"` |
| Erstellen eines virtuellen Windows-Computers |`azure  vm create [options] <resource-group> <name> <location> -y "Windows"` |
| Auflisten der virtuellen Computer |`azure  vm list [options]` |
| Abrufen von Informationen zu einem virtuellen Computer |`azure  vm show [options] <resource_group> <name>` |
| Starten eines virtuellen Computers |`azure vm start [options] <resource_group> <name>` |
| Anhalten eines virtuellen Computers |`azure vm stop [options] <resource_group> <name>` |
| Aufheben der Zuordnung eines virtuellen Computers |`azure vm deallocate [options] <resource-group> <name>` |
| Neustarten eines virtuellen Computers |`azure vm restart [options] <resource_group> <name>` |
| Löschen eines virtuellen Computers |`azure vm delete [options] <resource_group> <name>` |
| Erfassen eines virtuellen Computers |`azure vm capture [options] <resource_group> <name>` |
| Erstellen eines virtuellen Computers aus einem Benutzer-Image |`azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>` |
| Erstellen eines virtuellen Computers von einem speziellen Datenträger |`azure  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>` |
| Hinzufügen eines Datenträgers zu einem virtuellen Computer |`azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]` |
| Entfernen eines Datenträgers aus einem virtuellen Computer |`azure  vm disk detach [options] <resource-group> <vm-name> <lun>` |
| Hinzufügen einer generischen Erweiterung zu einem virtuellen Computer |`azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>` |
| Hinzufügen einer VMAccess-Erweiterung zu einem virtuellen Computer |`azure vm reset-access [options] <resource-group> <name>` |
| Hinzufügen einer Docker-Erweiterung zu einem virtuellen Computer |`azure  vm docker create [options] <resource-group> <name> <location> <os-type>` |
| Entfernen einer VM-Erweiterung |`azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>` |
| Abrufen der Nutzung virtueller Computerressourcen |`azure vm list-usage [options] <location>` |
| Abrufen Sie aller verfügbaren Größen von virtuellen Computern |`azure vm sizes [options]` |

## <a name="next-steps"></a>Nächste Schritte
* Weitere Beispiele für die Befehle der CLI, die über die grundlegende Verwaltung virtueller Computer hinausgehen, finden Sie unter [Befehle der Azure-Befehlszeilenschnittstelle im Azure Resource Manager-Modus](../articles/virtual-machines/azure-cli-arm-commands.md).
