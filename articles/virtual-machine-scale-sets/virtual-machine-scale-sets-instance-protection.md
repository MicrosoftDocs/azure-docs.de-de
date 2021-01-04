---
title: Instanzschutz für Instanzen von Azure-VM-Skalierungsgruppen
description: Es wird beschrieben, wie Sie Instanzen von Azure-VM-Skalierungsgruppen bei Vorgängen zum horizontalen Herunterskalieren und Festlegen der Skalierung schützen.
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 59720a3e89db29ba3eb0829084dd5252e27e9cc9
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746800"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Instanzschutz für Instanzen von Azure-VM-Skalierungsgruppen

Azure-VM-Skalierungsgruppen ermöglichen per [Autoskalierung](virtual-machine-scale-sets-autoscale-overview.md) eine bessere Elastizität für Ihre Workload, damit Sie die Konfiguration durchführen können, wenn Ihre Infrastruktur horizontal hoch- und herunterskaliert wird. Mit Skalierungsgruppen können Sie auch eine große Zahl von VMs zentral verwalten, konfigurieren und aktualisieren, indem Sie die entsprechenden Einstellungen der [Upgraderichtlinie](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) verwenden. Sie können ein Update des Skalierungsgruppenmodells konfigurieren. Die neue Konfiguration wird dann automatisch auf alle Skalierungsgruppeninstanzen angewendet, wenn Sie die Upgraderichtlinie auf „Automatisch“ oder „Parallel“ festgelegt haben.

Während der Verarbeitung von Datenverkehr durch Ihre Anwendung kann es zu Situationen kommen, in denen bestimmte Instanzen anders als die restlichen Skalierungsgruppeninstanzen behandelt werden sollen. Es kann beispielsweise sein, dass für bestimmte Instanzen der Skalierungsgruppe Vorgänge mit langer Ausführungsdauer durchgeführt werden und diese Instanzen erst horizontal herunterskaliert werden sollen, nachdem die Vorgänge abgeschlossen sind. Unter Umständen verfügen Sie auch über einige Instanzen in der Skalierungsgruppe, die Sie speziell für die Durchführung von zusätzlichen oder anderen Aufgaben eingerichtet haben, die nicht mit den restlichen Komponenten der Skalierungsgruppe durchgeführt werden sollen. Für Sie besteht dann die Notwendigkeit, dass diese „speziellen“ VMs nicht zusammen mit den anderen Instanzen der Skalierungsgruppe geändert werden. Mit dem Instanzschutz werden die zusätzlichen Steuerungen bereitgestellt, mit denen diese und andere Szenarien für Ihre Anwendung ermöglicht werden.

In diesem Artikel wird beschrieben, wie Sie die verschiedenen Funktionen für den Instanzschutz auf Skalierungsgruppeninstanzen anwenden und dafür nutzen können.

## <a name="types-of-instance-protection"></a>Arten des Instanzschutzes
Skalierungsgruppen umfassen zwei Arten von Funktionen für den Instanzschutz:

-   **Schutz vor horizontalem Herunterskalieren**
    - Wird über die **protectFromScaleIn** -Eigenschaft auf der Skalierungsgruppeninstanz aktiviert
    - Schützt die Instanz vor dem Vorgang zum Abskalieren, der von der Autoskalierung initiiert wird
    - Vom Benutzer initiierte Instanzvorgänge (einschließlich dem Löschen der Instanz) werden **nicht blockiert**
    - In der Skalierungsgruppe initiierte Vorgänge (Aktualisieren, Durchführen von Reimaging, Aufheben der Zuordnung usw.) werden **nicht blockiert**

-   **Schutz vor Skalierungsgruppenaktionen**
    - Wird über die **protectFromScaleSetActions** -Eigenschaft auf der Skalierungsgruppeninstanz aktiviert
    - Schützt die Instanz vor dem Vorgang zum Abskalieren, der von der Autoskalierung initiiert wird
    - Schützt die Instanz vor Vorgängen, die in der Skalierungsgruppe initiiert werden (Aktualisieren, Durchführen von Reimaging, Aufheben der Zuordnung usw.)
    - Vom Benutzer initiierte Instanzvorgänge (einschließlich dem Löschen der Instanz) werden **nicht blockiert**
    - Das Löschen der gesamten Skalierungsgruppe wird **nicht blockiert**

## <a name="protect-from-scale-in"></a>Schutz vor horizontalem Herunterskalieren
Der Instanzschutz kann auf Skalierungsgruppeninstanzen angewendet werden, nachdem die Instanzen erstellt wurden. Der Schutz wird nur auf das [Instanzmodell](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) angewendet und dort geändert – nicht für das [Skalierungsgruppenmodell](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Es gibt mehrere Möglichkeiten, den Schutz vor dem horizontalen Herunterskalieren auf Ihren Skalierungsgruppeninstanzen anzuwenden. Dies ist unten in den Beispielen beschrieben.

### <a name="azure-portal"></a>Azure-Portal

Sie können den Schutz für horizontales Herunterskalieren über das Azure-Portal auf eine Instanz in der Skalierungsgruppe anwenden. Es ist nicht möglich, mehrere Instanzen gleichzeitig anzupassen. Wiederholen Sie diese Schritte für jede Instanz, die Sie schützen möchten.
 
1. Navigieren Sie zu einer vorhandenen VM-Skalierungsgruppe.
1. Wählen Sie **Instanzen** im Menü auf der linken Seite unter **Einstellungen** aus.
1. Wählen Sie den Namen der Instanz aus, die Sie schützen möchten.
1. Wählen Sie die Registerkarte **Schutzrichtlinie** aus.
1. Wählen auf dem Blatt **Schutzrichtlinie** die Option **Vor horizontalem Herunterskalieren schützen** aus.
1. Wählen Sie **Speichern** aus. 

### <a name="rest-api"></a>REST-API

Im folgenden Beispiel wird der Schutz vor dem horizontalen Herunterskalieren auf eine Instanz der Skalierungsgruppe angewendet.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>Der Instanzschutz wird nur für API-Version 2019-03-01 und höher unterstützt.

### <a name="azure-powershell"></a>Azure PowerShell

Verwenden Sie das Cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm), um den Schutz vor dem horizontalen Herunterskalieren auf Ihre Skalierungsgruppeninstanz anzuwenden.

Im folgenden Beispiel wird der Schutz vor dem horizontalen Herunterskalieren auf eine Instanz der Skalierungsgruppe angewendet (Instanz-ID 0).

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Verwenden Sie [az vmss update](/cli/azure/vmss#az-vmss-update), um den Schutz vor dem horizontalen Herunterskalieren auf Ihre Skalierungsgruppeninstanz anzuwenden.

Im folgenden Beispiel wird der Schutz vor dem horizontalen Herunterskalieren auf eine Instanz der Skalierungsgruppe angewendet (Instanz-ID 0).

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Schutz vor Skalierungsgruppenaktionen
Der Instanzschutz kann auf Skalierungsgruppeninstanzen angewendet werden, nachdem die Instanzen erstellt wurden. Der Schutz wird nur auf das [Instanzmodell](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) angewendet und dort geändert – nicht für das [Skalierungsgruppenmodell](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Durch das Schützen einer Instanz vor Skalierungsgruppenaktionen wird die Instanz auch vor dem Vorgang des Abskalierens geschützt, der von der Autoskalierung initiiert wird.

Es gibt mehrere Möglichkeiten, den Schutz vor Skalierungsgruppenaktionen auf Ihren Skalierungsgruppeninstanzen anzuwenden. Dies ist unten in den Beispielen beschrieben.

### <a name="azure-portal"></a>Azure-Portal

Sie können den Schutz über Skalierungsgruppenaktionen über das Azure-Portal auf eine Instanz in der Skalierungsgruppe anwenden. Es ist nicht möglich, mehrere Instanzen gleichzeitig anzupassen. Wiederholen Sie diese Schritte für jede Instanz, die Sie schützen möchten.
 
1. Navigieren Sie zu einer vorhandenen VM-Skalierungsgruppe.
1. Wählen Sie **Instanzen** im Menü auf der linken Seite unter **Einstellungen** aus.
1. Wählen Sie den Namen der Instanz aus, die Sie schützen möchten.
1. Wählen Sie die Registerkarte **Schutzrichtlinie** aus.
1. Wählen auf dem Blatt **Schutzrichtlinie** die Option **Vor Aktionen für Skalierungsgruppen schützen** aus.
1. Wählen Sie **Speichern** aus. 

### <a name="rest-api"></a>REST-API

Im folgenden Beispiel wird der Schutz vor Skalierungsgruppenaktionen auf eine Instanz der Skalierungsgruppe angewendet.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>Der Instanzschutz wird nur für API-Version 2019-03-01 und höher unterstützt.</br>
Durch das Schützen einer Instanz vor Skalierungsgruppenaktionen wird die Instanz auch vor dem Vorgang des Abskalierens geschützt, der von der Autoskalierung initiiert wird. Es ist nicht möglich, für „protectFromScaleIn“ die Einstellung „false“ anzugeben, wenn „protectFromScaleSetActions“ auf „true“ festgelegt ist.

### <a name="azure-powershell"></a>Azure PowerShell

Verwenden Sie das Cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm), um den Schutz vor Skalierungsgruppenaktionen auf Ihre Skalierungsgruppeninstanz anzuwenden.

Im folgenden Beispiel wird der Schutz vor Skalierungsgruppenaktionen auf eine Instanz der Skalierungsgruppe angewendet (Instanz-ID 0).

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Verwenden Sie [az vmss update](/cli/azure/vmss#az-vmss-update), um den Schutz vor Skalierungsgruppenaktionen auf Ihre Skalierungsgruppeninstanz anzuwenden.

Im folgenden Beispiel wird der Schutz vor Skalierungsgruppenaktionen auf eine Instanz der Skalierungsgruppe angewendet (Instanz-ID 0).

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Problembehandlung
### <a name="no-protectionpolicy-on-scale-set-model"></a>Keine „protectionPolicy“ für Skalierungsgruppenmodell
Instanzschutz gilt nur für Skalierungsgruppeninstanzen und nicht für das Skalierungsgruppenmodell.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Keine „protectionPolicy“ für Skalierungsgruppeninstanz-Modell
Standardmäßig wird die Schutzrichtlinie bei der Erstellung einer Instanz nicht angewendet.

Sie können den Instanzschutz auf Skalierungsgruppeninstanzen anwenden, nachdem die Instanzen erstellt wurden.

### <a name="not-able-to-apply-instance-protection"></a>Anwenden des Instanzschutzes nicht möglich
Der Instanzschutz wird nur für API-Version 2019-03-01 und höher unterstützt. Überprüfen Sie die verwendete API-Version, und aktualisieren Sie sie je nach Bedarf. Unter Umständen müssen Sie auch PowerShell oder die CLI auf die aktuelle Version aktualisieren.

## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich über das [Bereitstellen Ihrer Anwendung](virtual-machine-scale-sets-deploy-app.md) in VM-Skalierungsgruppen.
