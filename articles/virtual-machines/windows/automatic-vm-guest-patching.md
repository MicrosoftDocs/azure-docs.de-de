---
title: Automatische VM-Gastpatches für Windows-VMs in Azure
description: Erfahren Sie, wie Sie virtuelle Windows-Computer in Azure automatisch patchen.
author: mayanknayar
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/09/2020
ms.author: manayar
ms.openlocfilehash: 8c7574daced9cec078b6e98e378212ce30d6f4f6
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744728"
---
# <a name="preview-automatic-vm-guest-patching-for-windows-vms-in-azure"></a>Vorschau: Automatische VM-Gastpatches für Windows-VMs in Azure

Durch das Aktivieren von automatischen VM-Gastpatches für Ihre Windows-VMs können Sie die Updateverwaltung vereinfachen, indem Sie virtuelle Computer sicher und automatisch patchen, um Sicherheitskonformität zu wahren.

Automatische VM-Gastpatches weisen die folgenden Merkmale auf:
- Patches, die als *Kritisch* oder *Sicherheit* klassifiziert werden, werden automatisch heruntergeladen und auf die VM angewendet.
- Patches werden außerhalb der Spitzenzeiten in der Zeitzone der VM angewendet.
- Die Patchorchestrierung wird von Azure verwaltet, und Patches werden nach den verfügbarkeitsbasierten Prinzipien angewendet.
- Die Integrität des virtuellen Computers wird anhand von Integritätssignalen der Plattform ermittelt und überwacht, um Patchfehler zu erkennen.
- Funktioniert für alle VM-Größen.

> [!IMPORTANT]
> Automatische VM-Gastpatches befinden sich derzeit in der öffentlichen Vorschau. Es ist ein Opt-in-Verfahren erforderlich, um die unten beschriebenen Funktionen der öffentlichen Vorschauversion zu nutzen.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-automatic-vm-guest-patching-work"></a>Wie funktioniert automatisches VM-Gastpatchen?

Wenn automatisches VM-Gastpatchen auf einer VM aktiviert ist, werden die verfügbaren *kritischen* und *Sicherheitspatches* heruntergeladen und automatisch auf die VM angewendet. Dieser Prozess wird jeden Monat automatisch gestartet, wenn neue Patches über Windows Update freigegeben werden. Die Patchbewertung und -installation erfolgt automatisch, und der Vorgang umfasst einen Neustart der VM bei Bedarf.

Die VM wird regelmäßig bewertet, um die entsprechenden Patches für die betreffende VM zu ermitteln. Die Patches können an jedem beliebigen Tag außerhalb der Spitzenzeiten der VM installiert werden. Diese automatische Bewertung stellt sicher, dass alle fehlenden Patches zum frühestmöglichen Zeitpunkt erkannt werden.

Patches werden innerhalb von 30 Tagen nach dem monatlichen Windows Update-Release nach der unten beschriebenen Verfügbarkeitsorchestrierung installiert. Patches werden nur außerhalb der Spitzenzeiten für die VM installiert, abhängig von der Zeitzone der VM. Die VM muss außerhalb der Spitzenzeiten ausgeführt werden, damit Patches automatisch installiert werden können. Wenn eine VM während einer periodischen Bewertung ausgeschaltet wird, wird die VM automatisch bewertet, und die anwendbaren Patches werden bei der nächsten periodischen Bewertung automatisch installiert, wenn die VM eingeschaltet wird.

Um Patches mit anderen Patchklassifizierungen zu installieren oder die Patchinstallation innerhalb Ihres eigenen benutzerdefinierten Wartungsfensters zu planen, können Sie die [Updateverwaltung](tutorial-config-management.md#manage-windows-updates) verwenden.

### <a name="availability-first-patching"></a>Patches nach Verfügbarkeit

Der Patchinstallationsvorgang wird global von Azure für alle VMs orchestriert, für die automatisches VM-Gastpatchen aktiviert ist. Diese Orchestrierung folgt den Verfügbarkeitsprinzipien auf unterschiedlichen Verfügbarkeitsstufen, die von Azure bereitgestellt werden.

Für eine Gruppe von virtuellen Computern, die ein Update durchlaufen, orchestriert die Azure-Plattform Updates wie folgt:

**Über Regionen hinweg:**
- Ein monatliches Update wird in Azure global phasenweise orchestriert, um globale Bereitstellungsfehler zu verhindern.
- Eine Phase kann sich über eine oder mehrere Regionen erstrecken, und ein Update geht nur dann in die nächsten Phasen über, wenn die in Betracht kommenden VMs in einer Phase erfolgreich aktualisiert wurden.
- Georegionspaare werden nicht gleichzeitig aktualisiert und können sich nicht in der gleichen Regionsphase befinden.
- Der Erfolg eines Updates wird durch die Nachverfolgung der Integrität nach dem Update der VM gemessen. Die VM-Integrität wird durch Integritätsindikatoren der Plattform für die VM nachverfolgt.

**Innerhalb einer Region:**
- VMs in verschiedenen Verfügbarkeitszonen werden nicht gleichzeitig aktualisiert.
- VMs, die nicht Teil einer Verfügbarkeitsgruppe sind, werden nach bestem Wissen in einem Batch zusammengefasst, um gleichzeitige Updates für alle VMs in einem Abonnement zu vermeiden.

**Innerhalb einer Verfügbarkeitsgruppe:**
- Alle VMs in einer gemeinsamen Verfügbarkeitsgruppe werden nicht gleichzeitig aktualisiert.
-   VMs in einer gemeinsamen Verfügbarkeitsgruppe werden innerhalb der Grenzen der Updatedomäne aktualisiert, und VMs in mehreren Updatedomänen werden nicht gleichzeitig aktualisiert.

Das Patchinstallationsdatum für eine bestimmte VM variiert ggf. von Monat zu Monat, da eine bestimmte VM in einem anderen Batch zwischen monatlichen Patchzyklen ausgewählt werden kann.

## <a name="supported-os-images"></a>Unterstützte Betriebssystemimages
Zurzeit werden nur VMs, die aus bestimmten Betriebssystem-Plattformimages erstellt wurden, in der Vorschau unterstützt. Benutzerdefinierte Images werden in der Vorschau zurzeit nicht unterstützt.

Derzeit werden die folgenden Plattform-SKUs unterstützt (weitere werden regelmäßig hinzugefügt):

| Herausgeber               | Betriebssystemangebot      |  Sku               |
|-------------------------|---------------|--------------------|
| Microsoft Corporation   | Windows Server | 2012-R2-Datacenter |
| Microsoft Corporation   | Windows Server | 2016-Datacenter    |
| Microsoft Corporation   | Windows Server | 2016-Datacenter-Server-Core |
| Microsoft Corporation   | Windows Server | 2019-Datacenter |
| Microsoft Corporation   | Windows Server | 2019-Datacenter-Server-Core |

## <a name="patch-orchestration-modes"></a>Patchorchestrierungsmodi
Windows-VMs in Azure unterstützen nun die folgenden Patchorchestrierungsmodi:

**AutomaticByPlatform:**
- Dieser Modus ermöglicht automatisches VM-Gastpatchen für den virtuellen Windows-Computer, und die nachfolgende Patchinstallation wird von Azure orchestriert.
- Dieser Modus ist erforderlich für Patches nach Verfügbarkeit.
- Wenn Sie diesen Modus festlegen, werden die nativen automatischen Updates auf dem virtuellen Windows-Computer deaktiviert, um Duplizierung zu vermeiden.
- Dieser Modus wird nur für VMs unterstützt, die mithilfe der oben genannten unterstützten Betriebssystem-Plattformimages erstellt wurden.
- Um diesen Modus zu verwenden, legen Sie die-Eigenschaft `osProfile.windowsConfiguration.enableAutomaticUpdates=true` fest, und legen Sie die Eigenschaft `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatfom` in der VM-Vorlage fest.

**AutomaticByOS:**
- Dieser Modus ermöglicht automatische Updates auf dem virtuellen Windows-Computer, und Patches werden über automatische Updates auf der VM installiert.
- Dieser Modus unterstützt keine Patches nach Verfügbarkeit.
- Dieser Modus wird standardmäßig festgelegt, wenn kein anderer Patchmodus angegeben wird.
- Wenn Sie diesen Modus verwenden möchten, legen Sie die Eigenschaft `osProfile.windowsConfiguration.enableAutomaticUpdates=true` fest, und legen Sie die Eigenschaft `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` in der VM-Vorlage fest.

**Manual (Manuell):**
- In diesem Modus werden automatische Updates auf dem virtuellen Windows-Computer deaktiviert.
- Dieser Modus unterstützt keine Patches nach Verfügbarkeit.
- Dieser Modus sollte festgelegt werden, wenn Sie benutzerdefinierte Patchlösungen verwenden.
- Um diesen Modus zu verwenden, legen Sie die-Eigenschaft `osProfile.windowsConfiguration.enableAutomaticUpdates=false` fest, und legen Sie die Eigenschaft `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` in der VM-Vorlage fest.

> [!NOTE]
>Die Eigenschaft `osProfile.windowsConfiguration.enableAutomaticUpdates` kann derzeit nur bei der Erstellung der VM festgelegt werden. Der Wechsel von einem manuellen in einen automatischen Modus oder von einem der beiden automatischen Modi in den manuellen Modus wird derzeit nicht unterstützt. Der Wechsel vom AutomaticByOS-Modus in den AutomaticByPlatfom-Modus wird unterstützt.

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>Anforderungen zum Aktivieren von automatischen VM-Gastpatches

- Auf dem virtuellen Computer muss der [Azure-VM-Agent](../extensions/agent-windows.md) installiert sein.
- Der Windows Update-Dienst muss auf dem virtuellen Computer ausgeführt werden.
- Der virtuelle Computer muss auf Windows Update-Endpunkte zugreifen können. Wenn Ihr virtueller Computer für die Verwendung von Windows Server Update Services (WSUS) konfiguriert ist, muss auf die relevanten WSUS-Serverendpunkte zugegriffen werden können.
- Verwenden Sie Compute-API-Version 2020-06-01 oder höher.

Zum Aktivieren der Vorschaufunktion ist eine einmalige Aktivierung des Features *InGuestAutoPatchVMPreview* pro Abonnement erforderlich, wie unten beschrieben.

### <a name="rest-api"></a>REST-API
Im folgenden Beispiel wird beschrieben, wie Sie die Vorschauversion für Ihr Abonnement aktivieren:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```

Die Featureregistrierung kann bis zu 15 Minuten dauern. So überprüfen Sie den Registrierungsstatus:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```

Schließen Sie den Aktivierungsvorgang ab, nachdem das Feature für Ihr Abonnement registriert wurde, indem Sie die Änderung an den Computeressourcenanbieter weitergeben.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Verwenden Sie das Cmdlet [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature), um die Vorschauversion für Ihr Abonnement zu aktivieren.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Die Featureregistrierung kann bis zu 15 Minuten dauern. So überprüfen Sie den Registrierungsstatus:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Schließen Sie den Aktivierungsvorgang ab, nachdem das Feature für Ihr Abonnement registriert wurde, indem Sie die Änderung an den Computeressourcenanbieter weitergeben.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Verwenden Sie [az feature register](/cli/azure/feature#az-feature-register), um die Vorschauversion für Ihr Abonnement zu aktivieren.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
```

Die Featureregistrierung kann bis zu 15 Minuten dauern. So überprüfen Sie den Registrierungsstatus:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
```

Schließen Sie den Aktivierungsvorgang ab, nachdem das Feature für Ihr Abonnement registriert wurde, indem Sie die Änderung an den Computeressourcenanbieter weitergeben.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```
## <a name="enable-automatic-vm-guest-patching"></a>Aktivieren automatischer VM-Gastpatches
Um automatische VM-Gastpatches zu aktivieren, stellen Sie sicher, dass die Eigenschaft *osProfile.windowsConfiguration.enableAutomaticUpdates* in der VM-Vorlagendefinition auf *true* festgelegt ist. Diese Eigenschaft kann nur beim Erstellen der VM festgelegt werden.

### <a name="rest-api"></a>REST-API
Im folgenden Beispiel wird das Aktivieren von automatischen VM-Gastpatches beschrieben:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-06-01`
```

```json
{
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Verwenden Sie das Cmdlet [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem), um beim Erstellen oder Aktualisieren einer VM automatische VM-Gastpatches zu aktivieren.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Verwenden Sie [az vm create](/cli/azure/vm#az-vm-create), um beim Erstellen einer neuen VM automatische VM-Gastpatches zu aktivieren. Im folgenden Beispiel werden automatische VM-Gastpatches für eine VM mit dem Namen *myVM* in der Ressourcengruppe mit dem Namen *myResourceGroup* konfiguriert:

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

Verwenden Sie [az vm update](/cli/azure/vm#az-vm-update), um eine vorhandene VM zu ändern.

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>Aktivierung und Bewertung

> [!NOTE]
>Es kann mehr als drei Stunden dauern, bis automatische VM-Gastupdates auf einer VM aktiviert werden, da die Aktivierung außerhalb der Spitzenzeiten der VM abgeschlossen wird. Da Bewertung und Patchinstallation nur außerhalb der Spitzenzeiten stattfinden, muss Ihre VM auch außerhalb der Spitzenzeiten ausgeführt werden, damit Patches angewendet werden können.

Wenn automatische VM-Gastpatches für eine VM aktiviert sind, wird auf der VM eine VM-Erweiterung vom Typ `Microsoft.CPlat.Core.WindowsPatchExtension` installiert. Diese Erweiterung muss nicht manuell installiert oder aktualisiert werden, da diese Erweiterung von der Azure-Plattform im Rahmen des automatischen VM-Gastpatchprozesses verwaltet wird.

Es kann mehr als drei Stunden dauern, bis automatische VM-Gastupdates auf einer VM aktiviert werden, da die Aktivierung außerhalb der Spitzenzeiten der VM abgeschlossen wird. Die Erweiterung wird ebenfalls außerhalb der Spitzenzeiten für die VM installiert und aktualisiert. Wenn der Zeitraum mit schwacher Auslastung der VM endet, bevor die Aktivierung abgeschlossen werden kann, wird der Aktivierungsprozess während des nächsten verfügbaren Zeitraums mit schwacher Auslastung fortgesetzt. Wenn für die VM zuvor automatische Windows-Updates im AutomaticByOS-Patchmodus aktiviert waren, werden automatische Windows-Updates für die VM deaktiviert, wenn die Erweiterung installiert wird.

Überprüfen Sie die Instanzansicht der VM, um zu ermitteln, ob automatische VM-Gastpatches abgeschlossen wurden und die Patcherweiterung auf der VM installiert wurde. Nachdem der Aktivierungsvorgang abgeschlossen wurde, wird die Erweiterung installiert, und die Bewertungsergebnisse für die VM sind unter `patchStatus` verfügbar. Der Zugriff auf die Instanzansicht der VM kann auf verschiedene Weise erfolgen, wie unten beschrieben.

### <a name="rest-api"></a>REST-API

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-06-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
Verwenden Sie das Cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) mit dem Parameter `-Status`, um auf die Instanzansicht Ihrer VM zuzugreifen.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

PowerShell stellt zurzeit nur Informationen zur Patcherweiterung zur Verfügung. Informationen zu `patchStatus` werden demnächst ebenfalls über PowerShell verfügbar sein.

### <a name="azure-cli-20"></a>Azure CLI 2.0
Verwenden Sie [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view), um auf die Instanzansicht für Ihre VM zuzugreifen.

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>Grundlegendes zum Patchstatus für Ihre VM

Der Abschnitt `patchStatus` der Antwort der Instanzansicht enthält Details zur neuesten Bewertung und zur letzten Patchinstallation für Ihre VM.

Die Bewertungsergebnisse für Ihre VM können im Abschnitt `availablePatchSummary` überprüft werden. Eine Bewertung wird regelmäßig für eine VM durchgeführt, für die automatische VM-Gastpatches aktiviert sind. Die Anzahl der verfügbaren Patches nach einer Bewertung wird unter den `criticalAndSecurityPatchCount`- und `otherPatchCount`-Ergebnissen bereitgestellt. Mit automatischen VM-Gastpatches werden alle Patches installiert, die mit den Patchklassifizierungen *Kritisch* und *Sicherheit* bewertet werden. Alle anderen bewerteten Patches werden übersprungen.

Die Ergebnisse der Patchinstallation für Ihre VM können im Abschnitt `lastPatchInstallationSummary` überprüft werden. Dieser Abschnitt enthält Details zum letzten Patchinstallationsversuch auf der VM, einschließlich der Anzahl der Patches, die installiert wurden, ausstehend bzw. fehlgeschlagen sind oder übersprungen wurden. Patches werden nur während des Wartungsfensters außerhalb der Spitzenzeiten für die VM installiert. Ausstehende und fehlerhafte Patches werden während des nächsten Wartungsfensters außerhalb der Spitzenzeiten automatisch wiederholt.

## <a name="on-demand-patch-assessment"></a>Bedarfsgesteuerte Patchbewertung
Wenn automatische VM-Gastpatches bereits für Ihre VM aktiviert sind, wird eine regelmäßige Patchbewertung für die VM außerhalb der Spitzenzeiten der VM durchgeführt. Dieser Prozess erfolgt automatisch, und die Ergebnisse der aktuellen Bewertung können wie zuvor in diesem Dokument beschrieben über die Instanzansicht der VM überprüft werden. Sie können auch jederzeit eine bedarfsgesteuerte Patchbewertung für Ihren virtuellen Computer auslösen. Die Patchbewertung kann einige Minuten dauern, und der Status der aktuellen Bewertung wird in der Instanzansicht der VM aktualisiert.

Zum Aktivieren der Vorschaufunktion ist eine einmalige Aktivierung des Features *InGuestPatchVMPreview* pro Abonnement erforderlich. Die Funktionsvorschau für die Patchbewertung auf Anforderung kann durch Befolgen des [Prozesses zum Aktivieren der Vorschau](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching) aktiviert werden, der weiter oben für automatische VM-Gastpatches beschrieben wurde.

> [!NOTE]
>Bei der bedarfsgesteuerten Patchbewertung wird die Patchinstallation nicht automatisch auslöst. Bewertete und anwendbare Patches für die VM werden nur außerhalb der Spitzenzeiten der VM installiert. Dies erfolgt nach dem zuvor in diesem Dokument beschriebenen Vorgang zur Patchverfügbarkeit.

### <a name="rest-api"></a>REST-API
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Verwenden Sie das Cmdlet [Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment), um die verfügbaren Patches für Ihren virtuellen Computer zu bewerten.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Verwenden Sie [az vm assess-patches](/cli/azure/vm#az-vm-assess-patches), um verfügbare Patches für Ihren virtuellen Computer zu bewerten.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Weitere Informationen zum Erstellen und Verwalten virtueller Windows-Computer](tutorial-manage-vm.md)
