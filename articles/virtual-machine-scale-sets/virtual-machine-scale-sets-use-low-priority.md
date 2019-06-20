---
title: Erstellen einer Azure-Skalierungsgruppe, die VMs mit niedriger Priorität verwendet (Vorschauversion) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure-VM-Skalierungsgruppen erstellen, die VMs mit niedriger Priorität verwenden, um Kosten zu sparen.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mmccrory
manager: rajraj
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: memccror
ms.openlocfilehash: 861c68ae8163e0ba8c2af2a3d96153ac3e84855f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60803219"
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>VMs mit niedriger Priorität in Skalierungsgruppen (Vorschau)

Mithilfe von VMs mit niedriger Priorität in Skalierungsgruppen können Sie unsere ungenutzte Kapazität mit signifikanten Kosteneinsparungen nutzen. Wenn Azure die Kapazität wieder benötigt, erzwingt die Azure-Infrastruktur die Zurücksetzung der VMs mit niedriger Priorität. Aus diesem Grund eignen sich VMs mit niedriger Priorität hervorragend für Workloads, die Unterbrechungen verkraften können, z.B. Batchverarbeitungsaufträge, Entwicklungs- und Testumgebungen, große Berechnungsarbeitslasten etc.

Die Menge der verfügbaren ungenutzten Kapazität kann abhängig von Größe, Region, Tageszeit etc. variieren. Beim Bereitstellen von VMs mit niedriger Priorität in Skalierungsgruppen weist Azure die virtuellen Computer zu, wenn Kapazität verfügbar ist, aber es gibt keine SLA für diese virtuellen Computer. Eine Skalierungsgruppe mit niedriger Priorität wird in einer einzelnen Fehlerdomäne bereitgestellt und bietet keine Garantien für hohe Verfügbarkeit.

## <a name="eviction-policy"></a>Entfernungsrichtlinie

Wenn Sie Skalierungsgruppen mit niedriger Priorität erstellen, können Sie die Entfernungsrichtlinie auf *Zuordnung aufheben* (Standardeinstellung) oder *Löschen* festlegen. 

Die Richtlinie *Zuordnung aufheben* versetzt Ihre entfernten VMs in den Zustand „beendet/Zuordnung aufgehoben“, sodass Sie entfernte Instanzen erneut bereitstellen können. Es gibt jedoch keine Garantie dafür, dass die Zuordnung erfolgreich ist. Die virtuellen Computer, deren Zuordnung aufgehoben wurde, werden auf Ihr Kontingent an Skalierungsgruppeninstanzen angerechnet, und die zugrunde liegenden Datenträger werden Ihnen in Rechnung gestellt. 

Wenn Sie möchten, dass die virtuellen Computer in Ihrer Skalierungsgruppe mit niedriger Priorität beim Entfernen gelöscht werden, können Sie die Entfernungsrichtlinie auf *Löschen* festlegen. Wenn die Entfernungsrichtlinie zum Löschen festgelegt ist, können Sie neue VMs durch Heraufsetzen der Skalierungsgruppeninstanzenanzahl-Eigenschaft erstellen. Die entfernten VMs werden zusammen mit ihren zugrunde liegenden Datenträgern gelöscht, und darum fallen keine Kosten für ihre Speicherung an. Sie können auch die Funktion zur automatischen Skalierung von Skalierungsgruppen verwenden, um zu versuchen, entfernte VMs automatisch zu kompensieren, es gibt jedoch keine Garantie, dass die Zuordnung erfolgreich ist. Sie sollten die Funktion für automatische Skalierung nur dann auf Skalierungsgruppen mit niedriger Priorität anwenden, wenn Sie die Entfernungsrichtlinie zum Löschen festlegen, um die Kosten Ihrer Datenträger und das Erreichen von Kontingentgrenzen zu vermeiden. 

> [!NOTE]
> Während der Vorschau können Sie Ihre Entfernungsrichtlinie über das [Azure-Portal](#use-the-azure-portal) und mithilfe von [Azure Resource Manager-Vorlagen](#use-azure-resource-manager-templates) festlegen. 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>Bereitstellen von VMs mit niedriger Priorität in Skalierungsgruppen

Zum Bereitstellen von VMs mit niedriger Priorität in Skalierungsgruppen können Sie das neue *Priorität*-Flag auf *Niedrig* setzen. Für alle virtuellen Computer in Ihrer Skalierungsgruppe wird niedrige Priorität festgelegt. Verwenden Sie zum Erstellen einer Skalierungsgruppe mit niedriger Priorität eine der folgenden Methoden:
- [Azure-Portal](#use-the-azure-portal)
- Azure-Befehlszeilenschnittstelle
- [Azure PowerShell](#use-azure-powershell)
- [Azure-Ressourcen-Manager-Vorlagen](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Der Prozess zum Erstellen einer Skalierungsgruppe mit VMs mit niedriger Priorität wird in diesem [Schnellstartartikel](quick-create-portal.md) ausführlich beschrieben. Wenn Sie eine Skalierungsgruppe bereitstellen, können Sie das Flag für niedrige Priorität und die Entfernungsrichtlinie festlegen: ![Erstellen einer Skalierungsgruppe mit VMs mit niedriger Priorität](media/virtual-machine-scale-sets-use-low-priority/vmss-low-priority-portal.png)

## <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI

Der Prozess zum Erstellen einer Skalierungsgruppe mit VMs mit niedriger Priorität wird im [Artikel zum Einstieg](quick-create-cli.md) ausführlich beschrieben. Fügen Sie einfach den „--Priority“-Parameter dem CLI-Aufruf hinzu, und setzen Sie ihn auf *Low*, wie im folgenden Beispiel gezeigt:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Low
```

## <a name="use-azure-powershell"></a>Mithilfe von Azure PowerShell

Der Prozess zum Erstellen einer Skalierungsgruppe mit VMs mit niedriger Priorität wird im [Artikel zum Einstieg](quick-create-powershell.md) ausführlich beschrieben.
Fügen Sie einfach [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) den Parameter „--Priority“hinzu, und legen Sie ihn auf *Low* fest, wie im nachstehenden Beispiel gezeigt:

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Low"
```

## <a name="use-azure-resource-manager-templates"></a>Verwenden von Azure Resource Manager-Vorlagen

Der Prozess zum Erstellen einer Skalierungsgruppe mit VMs mit niedriger Priorität wird im Artikel zum Einstieg für [Linux](quick-create-template-linux.md) oder [Windows](quick-create-template-windows.md) ausführlich beschrieben. Fügen Sie die Eigenschaft „Priority“dem *Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile*-Ressourcentyp in Ihrer Vorlage hinzu, und geben Sie *Low* als Wert an. Achten Sie darauf, dass Sie die API-Version *2018-03-01* oder höher verwenden. 

Um die Entfernungsrichtlinie auf Löschen festzulegen, fügen Sie den Parameter „evictionPolicy“ hinzu, und setzen Sie ihn auf *delete*.

Das folgende Beispiel erstellt eine Linux-Skalierungsgruppe mit niedriger Priorität mit der Bezeichnung *myScaleSet* in *USA, Westen-Mitte*, die die virtuellen Computer in der Skalierungsgruppe beim Entfernen *löscht*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2018-03-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Low",
       "evictionPolicy": "delete",
       "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```
## <a name="faq"></a>Häufig gestellte Fragen

### <a name="can-i-convert-existing-scale-sets-to-low-priority-scale-sets"></a>Kann ich vorhandene Skalierungsgruppen in Skalierungsgruppe mit niedriger Priorität konvertieren?
Nein. Das Setzen des Flags für niedrige Priorität wird nur zum Zeitpunkt der Erstellung unterstützt.

### <a name="can-i-create-a-scale-set-with-both-regular-vms-and-low-priority-vms"></a>Kann ich eine Skalierungsgruppe sowohl mit regulären VMs als auch mit VMs mit niedriger Priorität erstellen?
Nein. Eine Skalierungsgruppe kann nicht mehr als einen Prioritätstyp unterstützen.

### <a name="how-is-quota-managed-for-low-priority-vms"></a>Wie werden Kontingente für VMs mit niedriger Priorität verwaltet?
VMs mit niedriger Priorität und reguläre VMs nutzen den gleichen Kontingentpool. 

### <a name="can-i-use-autoscale-with-low-priority-scale-sets"></a>Kann ich die automatische Skalierung mit Skalierungsgruppen mit niedriger Priorität verwenden?
Ja. Sie können Regeln für die automatische Skalierung in Ihrer Skalierungsgruppe mit niedriger Priorität festlegen. Wenn Ihre VMs entfernt werden, kann die automatische Skalierungsfunktion versuchen, neue VMs mit niedriger Priorität zu erstellen. Denken Sie aber daran: Dieser Kapazität ist nicht garantiert. 

### <a name="does-autoscale-work-with-both-eviction-policies-deallocate-and-delete"></a>Funktioniert die automatische Skalierung mit beiden Entfernungsrichtlinien (Zuordnung aufheben und löschen)?
Es empfiehlt sich, die Entfernungsrichtlinie auf „Löschen“ festzulegen, wenn Sie die automatische Skalierung verwenden. Der Grund hierfür ist, dass Instanzen, deren Zuordnung aufgehoben wurde, auf die Kapazität in Ihrer Skalierungsgruppe angerechnet werden. Bei Verwendung der automatischen Skalierung erreichen Sie aufgrund der entfernten Instanzen, deren Zuordnung aufgehoben wurde, die angestrebte Anzahl von Instanzen wahrscheinlich recht schnell. 

## <a name="next-steps"></a>Nächste Schritte
Da Sie nun eine Skalierungsgruppe mit virtuellen Computern mit niedriger Priorität erstellt haben, versuchen Sie, unsere [Vorlage für automatische Skalierung mit niedriger Priorität](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri) bereitzustellen.

Detaillierte Preisinformationen finden Sie auf der Seite [Linux VM-Skalierungsgruppen – Preise ](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/).
