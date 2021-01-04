---
title: Automatische Instanzreparaturen mit Azure-VM-Skalierungsgruppen
description: Erfahren Sie, wie Sie Richtlinien für automatische Reparaturen für VM-Instanzen in einer Skalierungsgruppe konfigurieren
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: ae508754775d4eb622d8e91ef58eb0d6e1c45692
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94889013"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Automatische Instanzreparaturen für Azure-VM-Skalierungsgruppen

Durch das Aktivieren von automatischen Instanzreparaturen für Azure-VM-Skalierungsgruppen können Sie Hochverfügbarkeit für Anwendungen erzielen, indem Sie eine Reihe von fehlerfreien Instanzen verwalten. Wenn eine Instanz in der Skalierungsgruppe fehlerhaft ist, wie von der [Anwendungsintegritätserweiterung](./virtual-machine-scale-sets-health-extension.md) oder [Load Balancer-Integritätstests](../load-balancer/load-balancer-custom-probe-overview.md) gemeldet, führt diese Funktion automatisch eine Instanzreparatur aus und erstellt eine neue Instanz, um sie zu ersetzen.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Anforderungen für die Verwendung automatischer Instanzreparaturen

**Aktivieren der Anwendungsintegritätsüberwachung für eine Skalierungsgruppe**

Für die Skalierungsgruppe sollte die Anwendungsintegritätsüberwachung für Instanzen aktiviert sein. Verwenden Sie zu diesem Zweck die [Erweiterung für Anwendungsintegrität](./virtual-machine-scale-sets-health-extension.md) oder [Lastenausgleichs-Integritätstests](../load-balancer/load-balancer-custom-probe-overview.md). Nur eine dieser beiden Optionen kann gleichzeitig aktiviert werden. Die Erweiterung für Anwendungsintegrität oder der Lastenausgleich testet den für die VM-Instanzen konfigurierten Anwendungsendpunkt mit Ping, um den Integritätsstatus der Anwendung zu ermitteln. Dieser Integritätsstatus wird vom Orchestrator der Skalierungsgruppe verwendet, um die Instanzintegrität zu überwachen und bei Bedarf Reparaturen auszuführen.

**Konfigurieren des Endpunkts zum Bereitstellen des Integritätsstatus**

Vergewissern Sie sich vor dem Aktivieren der Richtlinie für automatische Instanzreparatur, dass für die Skalierungsgruppeninstanzen ein Anwendungsendpunkt konfiguriert ist, der den Integritätsstatus der Anwendung ausgeben kann. Wenn eine Instanz den Status 200 (OK) für diesen Anwendungsendpunkt zurückgibt, wird die Instanz als „Fehlerfrei“ gekennzeichnet. In allen anderen Fällen wird die Instanz als „Fehlerhaft“ gekennzeichnet, beispielsweise auch in den folgenden Szenarien:

- Wenn kein Anwendungsendpunkt innerhalb der VM-Instanzen konfiguriert ist, um den Integritätsstatus der Anwendung bereitzustellen.
- Wenn der Anwendungsendpunkt nicht ordnungsgemäß konfiguriert ist.
- Wenn der Anwendungsendpunkt nicht erreichbar ist.

Für Instanzen, die als „Fehlerhaft“ markiert sind, werden automatische Reparaturen durch die Skalierungsgruppe ausgelöst. Stellen Sie sicher, dass der Anwendungsendpunkt ordnungsgemäß konfiguriert ist, bevor Sie die Richtlinie für automatische Reparaturen aktivieren, um unbeabsichtigte Instanzreparaturen zu vermeiden, während der Endpunkt konfiguriert wird.

**Maximum number of instances in the scale set** (Maximale Anzahl von Instanzen in der Skalierungsgruppe)

Dieses Feature ist derzeit nur für Skalierungsgruppen mit maximal 200 Instanzen verfügbar. Die Skalierungsgruppe kann entweder als einzelne Platzierungsgruppe oder als mehrfache Platzierungsgruppe bereitgestellt werden. Die Anzahl der Instanzen kann jedoch nicht über 200 liegen, wenn automatische Instanzreparaturen für die Skalierungsgruppe aktiviert sind.

**API-Version**

Die Richtlinie für automatische Reparaturen wird für die API-Computeversion 2018-10-01 oder höher unterstützt.

**Einschränkungen beim Verschieben von Ressourcen oder Abonnements**

Ressourcen- oder Abonnementverschiebungen werden derzeit nicht für Skalierungsgruppen unterstützt, wenn die Richtlinie für automatische Reparaturen aktiviert ist.

**Einschränkung für Service Fabric-Skalierungsgruppen**

Dieses Feature wird für Service Fabric-Skalierungsgruppen zurzeit nicht unterstützt.

## <a name="how-do-automatic-instance-repairs-work"></a>Wie funktionieren automatische Instanzreparaturen?

Das Feature für automatische Instannreparatur basiert auf der Integritätsüberwachung einzelner Instanzen in einer Skalierungsgruppe. VM-Instanzen in einer Skalierungsgruppe können so konfiguriert werden, dass der Integritätsstatus der Anwendung entweder mit der [Erweiterung für die Anwendungsintegrität](./virtual-machine-scale-sets-health-extension.md) oder mit [Lastenausgleichs-Integritätstests](../load-balancer/load-balancer-custom-probe-overview.md) ausgegeben wird. Wenn eine Instanz als fehlerhaft eingestuft wird, führt die Skalierungsgruppe eine Reparaturaktion aus, indem sie die fehlerhafte Instanz löscht und eine neue Instanz erstellt, um sie zu ersetzen. Das neueste VM-Skalierungsgruppenmodell wird verwendet, um die neue Instanz zu erstellen. Diese Funktion kann im VM-Skalierungsgruppenmodell mithilfe des *automaticRepairsPolicy*-Objekts aktiviert werden.

### <a name="batching"></a>Batchverarbeitung

Die automatischen Instanzreparaturvorgänge werden in Batches ausgeführt. Zu jedem Zeitpunkt werden nicht mehr als 5 % der Instanzen in der Skalierungsgruppe über die Richtlinie für automatische Reparaturen repariert. Dadurch wird verhindert, dass eine große Anzahl von Instanzen gleichzeitig gelöscht und neu erstellt wird, wenn diese gleichzeitig als fehlerhaft eingestuft werden.

### <a name="grace-period"></a>Karenzzeit

Wenn eine Instanz aufgrund einer PUT-, PATCH- oder POST-Aktion, die für die Skalierungsgruppe ausgeführt wird (z. B. Reimaging, erneute Bereitstellung, Aktualisierung usw.), einen Statusänderungsvorgang durchläuft, wird jede Reparaturaktion für diese Instanz erst ausgeführt, nachdem die Toleranzperiode abgewartet wurde. Die Toleranzperiode ist die Zeitspanne, die es der Instanz ermöglicht, in einen fehlerfreien Zustand zurückzukehren. Die Toleranzperiode startet, nachdem die Statusänderung abgeschlossen wurde. Dadurch werden vorzeitige oder versehentliche Reparaturvorgänge vermieden. Die Toleranzperiode wird für alle neu erstellten Instanzen in der Skalierungsgruppe (einschließlich der als Ergebnis eines Reparaturvorgangs erstellten Instanz) eingehalten. Die Toleranzperiode wird im ISO 8601-Format in Minuten angegeben und kann mithilfe der Eigenschaft *automaticRepairsPolicy.gracePeriod* festgelegt werden. Die Toleranzperiode kann zwischen 30 Minuten und 90 Minuten betragen und weist einen Standardwert von 30 Minuten auf.

### <a name="suspension-of-repairs"></a>Aufschieben von Reparaturen 

VM-Skalierungsgruppen bieten die Möglichkeit, automatische Instanzreparaturen bei Bedarf vorübergehend aufzuschieben. *serviceState* für automatische Reparaturen unter der Eigenschaft *orchestrationServices* in der Instanzansicht der VM-Skalierungsgruppe zeigt den aktuellen Status automatischer Reparaturen an. Wenn für eine Skalierungsgruppe automatische Reparaturen konfiguriert werden, wird *serviceState* auf *Running* (Wird ausgeführt) festgelegt. Wenn die automatischen Reparaturen für eine Skalierungsgruppe aufgeschoben werden, wird der Parameter *serviceState* auf *Suspended* (Angehalten) festgelegt. Wenn *automaticRepairsPolicy* für eine Skalierungsgruppe definiert ist, die Funktion für automatische Reparaturen jedoch nicht aktiviert ist, hat der Parameter *serviceState* den Wert *Not Running* (Wird nicht ausgeführt).

Wenn neu erstellte Instanzen zur Ersetzung der fehlerhaften Instanzen in einer Skalierungsgruppe auch nach wiederholt durchgeführten Reparaturvorgängen fehlerhaft bleiben, aktualisiert die Plattform als Sicherheitsmaßnahme den *serviceState* für automatische Reparaturen auf *Suspended*. Sie können die automatischen Reparaturen fortsetzen, indem Sie den Wert von *serviceState* für automatische Reparaturen auf *Running* festlegen. Ausführliche Anweisungen finden Sie im Abschnitt zum [Anzeigen und Aktualisieren des Dienststatus der Richtlinie für automatische Reparaturen](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) für Ihre Skalierungsgruppe. 

Der automatische Instannreparaturvorgang funktioniert wie folgt:

1. Die [Erweiterung für Anwendungsintegrität](./virtual-machine-scale-sets-health-extension.md) oder [Lastenausgleichs-Integritätstests](../load-balancer/load-balancer-custom-probe-overview.md) pingen den Anwendungsendpunkt innerhalb der einzelnen virtuellen Computer in der Skalierungsgruppe pingen, um den Integritätsstatus der Anwendung für jede Instanz abzurufen.
2. Wenn der Endpunkt mit dem Status 200 (OK) antwortet, wird die Instanz als „Fehlerfrei“ gekennzeichnet. In allen anderen Fällen (auch wenn der Endpunkt nicht erreichbar ist) wird die Instanz als „Fehlerhaft“ markiert.
3. Wenn eine Instanz als fehlerhaft eingestuft wird, löst die Skalierungsgruppe eine Reparaturaktion aus, indem sie die fehlerhafte Instanz löscht und eine neue Instanz erstellt, um sie zu ersetzen.
4. Instanzreparaturen werden in Batches ausgeführt. Zu jedem Zeitpunkt werden nicht mehr als 5 % der Gesamtzahl der Instanzen in der Skalierungsgruppe repariert. Wenn eine Skalierungsgruppe über weniger als 20 Instanzen verfügt, werden die Reparaturen für jeweils eine fehlerhafte Instanz gleichzeitig durchgeführt.
5. Der oben beschriebene Vorgang wird fortgesetzt, bis alle fehlerhaften Instanzen in der Skalierungsgruppe repariert wurden.

## <a name="instance-protection-and-automatic-repairs"></a>Instanzschutz und automatische Reparaturen

Wenn eine Instanz in einer Skalierungsgruppe durch Anwenden einer der [Schutzrichtlinien](./virtual-machine-scale-sets-instance-protection.md) geschützt ist, werden automatische Reparaturen für diese Instanz nicht ausgeführt. Dies gilt für beide Schutzrichtlinien: *Schutz vor dem horizontalen Herunterskalieren* und *Schutz vor Skalierungsgruppenaktionen*. 

## <a name="terminatenotificationandautomaticrepairs"></a>Beendigungsbenachrichtigung und automatische Reparaturen

Wenn für eine Skalierungsgruppe das Feature [Beendigungsbenachrichtigung](./virtual-machine-scale-sets-terminate-notification.md) aktiviert ist, folgt im Rahmen automatischer Reparaturvorgänge die Löschung fehlerhafter Instanzen der Konfiguration der Beendigungsbenachrichtigung. Eine Beendigungsbenachrichtigung wird über den Azure-Metadatendienst – geplante Ereignisse – gesendet, und die Löschung von Instanzen wird für die Dauer des konfigurierten Verzögerungstimeouts verzögert. Die Erstellung einer neuen Instanz zur Ersetzung der fehlerhaften wird jedoch nicht bis nach dem Ablauf des Timeouts verschoben.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Aktivieren der Richtlinie für automatische Reparaturen beim Erstellen einer neuen Skalierungsgruppe

Um die Richtlinie für automatische Reparaturen beim Erstellen einer neuen Skalierungsgruppe zu aktivieren, stellen Sie sicher, dass alle [Anforderungen](#requirements-for-using-automatic-instance-repairs) erfüllt sind, die für das Abonnieren dieses Features erforderlich sind. Der Anwendungsendpunkt sollte für Skalierungsgruppeninstanzen ordnungsgemäß konfiguriert werden, um zu vermeiden, dass unbeabsichtigte Reparaturen ausgelöst werden, während der Endpunkt konfiguriert wird. Bei neu erstellten Skalierungsgruppen werden alle Instanzreparaturen ausgeführt, nachdem für die Dauer der Toleranzperiode gewartet wurde. Um die automatische Instanzreparatur in einer Skalierungsgruppe zu aktivieren, verwenden Sie das *automaticRepairsPolicy*-Objekt im VM-Skalierungsgruppenmodell.

Sie können diese [Schnellstartvorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) auch verwenden, um eine VM-Skalierungsgruppe mit dem Integritätstest für den Lastenausgleich und aktivierten automatischen Instanzreparaturen mit einer Karenzzeit von 30 Minuten bereitzustellen.

### <a name="azure-portal"></a>Azure-Portal
 
Mit den folgenden Schritten wird die Richtlinie für automatische Reparaturen beim Erstellen einer neuen Skalierungsgruppe aktiviert.
 
1. Navigieren Sie zu **VM-Skalierungsgruppen**.
1. Wählen Sie **+ Hinzufügen** aus, um eine neue Skalierungsgruppe zu erstellen.
1. Wechseln Sie zur Registerkarte **Integrität**. 
1. Suchen Sie den Abschnitt **Integrität**.
1. Aktivieren Sie die Option **Anwendungsintegrität überwachen**.
1. Suchen Sie den Abschnitt **Automatische Reparaturrichtlinie**.
1. **Aktivieren** Sie die Option **automatische Reparaturen**.
1. Geben Sie in **Grace period (min)** (Karenzzeit (Min.)) die Karenzzeit in Minuten ein, die zulässigen Werte liegen zwischen 30 und 90 Minuten. 
1. Wenn Sie die Erstellung der neuen Skalierungsgruppe abgeschlossen haben, wählen Sie die Schaltfläche **Überprüfen und erstellen** aus.

### <a name="rest-api"></a>REST-API

Im folgenden Beispiel wird gezeigt, wie die automatische Instanzreparatur in einem Skalierungsgruppenmodell aktiviert wird. Verwenden Sie die API-Version 2018-10-01 oder höher.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Die Funktion für automatische Instanzreparatur kann beim Erstellen einer neuen Skalierungsgruppe mithilfe des Cmdlets [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) aktiviert werden. Dieses Beispielskript führt Sie schrittweise durch die Erstellung einer Skalierungsgruppe und zugehöriger Ressourcen mithilfe der Konfigurationsdatei: [Erstellen einer vollständigen VM-Skalierungsgruppe](./scripts/powershell-sample-create-complete-scale-set.md). Sie können die Richtlinie für automatische Instanzreparaturen konfigurieren, indem Sie die Parameter *EnableAutomaticRepair* und *AutomaticRepairGracePeriod* dem Konfigurationsobjekt hinzufügen, um die Skalierungsgruppe zu erstellen. Das folgende Beispiel aktiviert das Feature mit einer Toleranzperiode von 30 Minuten.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Im folgenden Beispiel wird die Richtlinie für automatische Reparaturen aktiviert, während mithilfe von *[az vmss create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)* eine neue Skalierungsgruppe erstellt wird. Erstellen Sie zunächst eine Ressourcengruppe, und erstellen Sie dann eine neue Skalierungsgruppe mit einer auf 30 Minuten festgelegten Karenzzeit für die Richtlinie für automatische Reparaturen.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-grace-period 30
```

Im Beispiel oben werden ein vorhandener Load Balancer und ein vorhandener Integritätstest zum Überwachen der Anwendungsintegrität von Instanzen verwendet. Wenn Sie stattdessen die Verwendung einer Application Health-Erweiterung zur Überwachung vorziehen, können Sie eine Skalierungsgruppe erstellen, die Application Health-Erweiterung konfigurieren und dann die Richtlinie für automatische Reparaturen mithilfe von *az vmss update* aktivieren, wie im nächsten Abschnitt beschrieben.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Aktivieren der Richtlinie für automatische Reparaturen beim Aktualisieren einer vorhandenen Skalierungsgruppe

Bevor Sie die Richtlinie für automatische Reparaturen in einer vorhandenen Skalierungsgruppe aktivieren, stellen Sie sicher, dass alle [Anforderungen](#requirements-for-using-automatic-instance-repairs) erfüllt sind, die für das Abonnieren dieses Features erforderlich sind. Der Anwendungsendpunkt sollte für Skalierungsgruppeninstanzen ordnungsgemäß konfiguriert werden, um zu vermeiden, dass unbeabsichtigte Reparaturen ausgelöst werden, während der Endpunkt konfiguriert wird. Um die automatische Instanzreparatur in einer Skalierungsgruppe zu aktivieren, verwenden Sie das *automaticRepairsPolicy*-Objekt im VM-Skalierungsgruppenmodell.

Vergewissern Sie sich nach dem Aktualisieren des Modells einer vorhandenen Skalierungsgruppe, dass auf alle Instanzen der Skalierung das neueste Modell angewendet wird. Sie können sich dabei auf die Anweisung zum [Aktualisieren von VMs auf das neueste Skalierungsgruppenmodell](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) beziehen.

### <a name="azure-portal"></a>Azure-Portal

Sie können die Richtlinie für automatische Reparaturen einer vorhandenen Skalierungsgruppe über das Azure-Portal ändern. 
 
1. Navigieren Sie zu einer vorhandenen VM-Skalierungsgruppe.
1. Wählen Sie unter **Einstellungen** im Menü auf der linken Seite **Integrität und Reparatur** aus.
1. Aktivieren Sie die Option **Anwendungsintegrität überwachen**.
1. Suchen Sie den Abschnitt **Automatische Reparaturrichtlinie**.
1. **Aktivieren** Sie die Option **automatische Reparaturen**.
1. Geben Sie in **Grace period (min)** (Karenzzeit (Min.)) die Karenzzeit in Minuten ein, die zulässigen Werte liegen zwischen 30 und 90 Minuten. 
1. Wählen Sie **Speichern** aus, wenn der Vorgang abgeschlossen ist. 

### <a name="rest-api"></a>REST-API

Das folgende Beispiel aktiviert die Richtlinie mit einer Toleranzperiode von 40 Minuten. Verwenden Sie die API-Version 2018-10-01 oder höher.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Verwenden Sie das Cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss), um die Konfiguration des automatischen Instanzreparaturfeatures in einer vorhandenen Skalierungsgruppe zu ändern. Im folgenden Beispiel wird die Toleranzperiode auf 40 Minuten aktualisiert.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Nachfolgend finden Sie ein Beispiel zum Aktualisieren der Richtlinie für automatische Instanzreparaturen einer vorhandenen Skalierungsgruppe mithilfe von *[az vmss update](/cli/azure/vmss?view=azure-cli-latest#az-vmss-update)* .

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>Anzeigen und Aktualisieren des Dienststatus einer Richtlinie für automatische Instanzreparaturen

### <a name="rest-api"></a>REST-API 

Verwenden Sie [Get Instance View](/rest/api/compute/virtualmachinescalesets/getinstanceview) (Instanzansicht abrufen) mit API-Version 2019-12-01 oder höher für eine VM-Skalierungsgruppe, um den *serviceState* für automatische Reparaturen unter der Eigenschaft *orchestrationServices* anzuzeigen. 

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

Verwenden Sie die *setOrchestrationServiceState*-API mit API-Version 2019-12-01 oder höher für eine VM-Skalierungsgruppe, um den Status automatischer Reparaturen festzulegen. Nachdem die Funktion für automatische Reparaturen für die Skalierungsgruppe festgelegt wurde, können Sie diese API verwenden, um automatische Reparaturen für Ihre Skalierungsgruppe aufzuschieben oder fortzusetzen. 

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>Azure CLI 

Verwenden Sie das Cmdlet [get-instance-view](/cli/azure/vmss?view=azure-cli-latest#az-vmss-get-instance-view), um den *serviceState* für automatische Instanzreparaturen anzuzeigen. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

Verwenden Sie das [set-orchestration-service-state](/cli/azure/vmss?view=azure-cli-latest#az-vmss-set-orchestration-service-state)-Cmdlet, um den *serviceState* für automatische Instanzreparaturen zu aktualisieren. Nachdem die Funktion für automatische Reparaturen für die Skalierungsgruppe festgelegt wurde, können Sie dieses Cmdlet verwenden, um automatische Reparaturen für Ihre Skalierungsgruppe aufzuschieben oder fortzusetzen. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Verwenden Sie das Cmdlet [Get-AzVmss](/powershell/module/az.compute/get-azvmss?view=azps-3.7.0) mit dem Parameter *InstanceView*, um den *ServiceState* für automatische Instanzreparaturen anzuzeigen.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Verwenden Sie das Set-AzVmssOrchestrationServiceState-Cmdlet, um den *serviceState* für automatische Instanzreparaturen zu aktualisieren. Nachdem die Funktion für automatische Reparaturen für die Skalierungsgruppe festgelegt wurde, können Sie dieses Cmdlet verwenden, um automatische Reparaturen für Ihre Skalierungsgruppe aufzuschieben oder fortzusetzen.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Problembehandlung

**Fehler beim Aktivieren der Richtlinie für automatische Reparaturen**

Wenn Sie einen Fehler „BadRequest“ mit der Meldung „Could not find member 'automaticRepairsPolicy' on object of type 'properties'“ (Member „automaticRepairsPolicy“ für Objekt vom Typ „properties“ wurde nicht gefunden) erhalten, überprüfen Sie die API-Version, die für die VM-Skalierungsgruppe verwendet wird. Für dieses Feature ist API-Version 2018-10-01 oder höher erforderlich.

**Instanz wird auch bei aktivierter Richtlinie nicht repariert**

Die Instanz kann sich in der Toleranzperiode befinden. Dies ist die Zeitspanne, die gewartet werden soll, nachdem eine beliebige Zustandsänderung der Instanz erfolgt ist, bevor Reparaturen durchgeführt werden. Dadurch werden vorzeitige oder versehentliche Reparaturvorgänge vermieden. Die Reparaturaktion sollte durchgeführt werden, sobald die Toleranzperiode für die Instanz abgeschlossen ist.

**Anzeigen des Integritätsstatus der Anwendung für Skalierungsgruppeninstanzen**

Zum Anzeigen des Integritätsstatus der Anwendung können Sie die [API zum Abrufen der Instanzenansicht](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) für Instanzen in einer VM-Skalierungsgruppe verwenden. Mit Azure PowerShell können Sie das Cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) mit dem Flag *-InstanceView* verwenden. Der Integritätsstatus der Anwendung wird unter der Eigenschaft *vmHealth* bereitgestellt.

Im Azure-Portal können Sie den Integritätsstatus ebenfalls anzeigen. Wechseln Sie zu einer vorhandenen Skalierungsgruppe, wählen Sie im Menü auf der linken Seite **Instanzen** aus, und lesen Sie in der Spalte **Health state** (Integritätsstatus) den Integritätsstatus jeder Skalierungsgruppeninstanz ab. 

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie die [Erweiterung für Anwendungsintegrität](./virtual-machine-scale-sets-health-extension.md) oder [Lastenausgleichs-Integritätstests](../load-balancer/load-balancer-custom-probe-overview.md) für Ihre Skalierungsgruppen konfiguriert werden.
