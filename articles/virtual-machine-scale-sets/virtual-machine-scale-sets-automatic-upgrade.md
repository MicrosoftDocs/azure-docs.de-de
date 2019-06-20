---
title: Automatische Betriebssystemimageupgrades mit Azure-VM-Skalierungsgruppen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie das Betriebssystemimage auf VM-Instanzen in einer Skalierungsgruppe automatisch aktualisieren.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: manayar
ms.openlocfilehash: 007f2801efed8da4964808056563418dec7f64d5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60328815"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Automatische Betriebssystemimageupgrades mit Azure-VM-Skalierungsgruppen

Das Aktivieren der automatischen Betriebssystemimage-Upgrades auf Ihrer Skalierungsgruppe vereinfacht die Updateverwaltung durch sicheres und automatisches Aktualisieren des Betriebssystem-Datenträgers für alle Instanzen in der Skalierungsgruppe.

Das automatische Betriebssystemupgrade weist folgende Merkmale auf:

- Nach der Konfiguration wird das neueste von den Imageherausgebern veröffentlichte Betriebssystemimage automatisch ohne Benutzereingriff auf die Skalierungsgruppe angewendet.
- Aktualisiert Batches von Instanzen immer parallel, wenn ein neues Plattformimage vom Herausgeber veröffentlicht wird.
- Wird in Anwendungsintegritätstests und [Anwendungsintegritätserweiterung](virtual-machine-scale-sets-health-extension.md) integriert.
- Unterstützt alle VM-Größen und sowohl Windows- als auch Linux-Plattformimages.
- Sie können automatische Upgrades jederzeit abwählen (Betriebssystemupgrades können auch manuell initiiert werden).
- Der Betriebssystemdatenträger eines virtuellen Computers wird durch den neuen, mit der neuesten Imageversion erstellten Betriebssystemdatenträger ersetzt. Konfigurierte Erweiterungen und benutzerdefinierte Datenskripts werden ausgeführt, wobei persistente Datenträger weiterhin aufbewahrt werden.
- [Erweiterungssequenzierung](virtual-machine-scale-sets-extension-sequencing.md) wird unterstützt.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Wie funktioniert das automatische Upgrade von Betriebssystemimages?

Bei einem Upgrade wird der Betriebssystemdatenträger eines virtuellen Computers durch einen neuen Datenträger ersetzt, der mit der neuesten Imageversion erstellt wird. Alle konfigurierten Erweiterungen und benutzerdefinierten Datenskripts werden auf dem Betriebssystemdatenträger ausgeführt, wobei persistente Datenträger weiterhin aufbewahrt werden. Um die Anwendungsausfallzeiten zu minimieren, erfolgen Upgrades in Batches, wobei jeweils nicht mehr als 20% der Skalierungsgruppe aktualisiert werden. Sie können auch einen Azure Load Balancer-Anwendungsintegritätstest oder eine [Anwendungsintegritätserweiterung](virtual-machine-scale-sets-health-extension.md) integrieren. Sie sollten einen Anwendungstakt integrieren und den Erfolg des Upgrades jedes Batches im Upgradeprozess überprüfen.

Der Upgradeprozess funktioniert wie folgt:
1. Bevor mit dem Upgradeprozess begonnen wird, stellt der Orchestrator sicher, dass nicht mehr als 20% der Instanzen in der gesamten Skalierungsgruppe (aus beliebigem Grund) fehlerhaft sind.
2. Der Upgradeorchestrator identifiziert den Batch zu aktualisierender VM-Instanzen, wobei ein Batch maximal 20% der gesamten Instanzenzahl aufweist.
3. Der Betriebssystem-Datenträger des ausgewählten Batches von VM-Instanzen wird durch einen neuen, aus dem aktuellen Image erstellten Betriebssystem-Datenträger ersetzt, und alle angegebenen Erweiterungen und Konfigurationen im Skalierungsgruppenmodell werden auf die aktualisierte Instanz angewendet.
4. Für Skalierungsgruppen mit konfigurierten Anwendungsintegritätstests oder Anwendungsintegritätserweiterung wartet das Upgrade bis zu 5 Minuten darauf, dass die Instanz fehlerfrei wird, bevor mit der Aktualisierung des nächsten Batches fortgefahren wird.
5. Der Upgradeorchestrator verfolgt auch den Prozentsatz der Instanzen nach, die nach einem Upgrade fehlerhaft werden. Das Upgrade wird beendet, wenn mehr als 20% der aktualisierten Instanzen während des Upgradeprozesses fehlerhaft werden.
6. Das oben beschriebene Verfahren wird fortgesetzt, bis alle Instanzen in der Skalierungsgruppe aktualisiert sind.

Der Upgradeorchestrator für das Skalierungsgruppen-Betriebssystem überprüft die allgemeine Skalierungsgruppenintegrität, bevor die einzelnen Batches aktualisiert werden. Beim Aktualisieren eines Batches finden eventuell andere gleichzeitige geplante oder nicht geplante Wartungsaktivitäten statt, die die Integrität Ihrer Skalierungsgruppeninstanzen beeinträchtigen könnten. Wenn in solchen Fällen mehr als 20% der Instanzen der Skalierungsgruppe fehlerhaft werden, endet das Upgrade der Skalierungsgruppe am Ende des aktuellen Batches.

## <a name="supported-os-images"></a>Unterstützte Betriebssystemimages
Derzeit werden nur bestimmte Betriebssystemplattform-Images unterstützt. Benutzerdefinierte Images werden derzeit nicht unterstützt.

Derzeit werden die folgenden SKUs unterstützt (und weitere werden regelmäßig hinzugefügt):

| Herausgeber               | Betriebssystemangebot      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| Rogue Wave (OpenLogic)  | CentOS        | 7,5                |
| CoreOS                  | CoreOS        | Stable             |
| Microsoft Corporation   | Windows Server | 2012-R2-Datacenter |
| Microsoft Corporation   | Windows Server | 2016-Datacenter    |
| Microsoft Corporation   | Windows Server | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | Windows Server | 2016-Datacenter-with-Containers |
| Microsoft Corporation   | Windows Server | 2019-Datacenter |
| Microsoft Corporation   | Windows Server | 2019-Datacenter-Smalldisk |
| Microsoft Corporation   | Windows Server | 2019-Datacenter-with-Containers |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Anforderungen für das Konfigurieren des automatischen Upgrades von Betriebssystemimages

- Die *version*-Eigenschaft des Plattformimages muss auf *latest* festgelegt werden.
- Verwenden Sie Anwendungsintegritätstests oder [Anwendungsintegritätserweiterung](virtual-machine-scale-sets-health-extension.md) für Nicht-Service Fabric-Skalierungsgruppen.
- Stellen Sie sicher, dass im Skalierungsgruppenmodell angegebene externe Ressourcen verfügbar und aktualisiert sind. Zu den Beispielen zählen SAS-URI für die Bootstrap-Nutzlast in VM-Erweiterungseigenschaften, Nutzlast im Speicherkonto, Verweis auf Geheimnisse im Modell und Sonstiges.

## <a name="configure-automatic-os-image-upgrade"></a>Konfigurieren des automatischen Upgrades von Betriebssystemimages
Stellen Sie zum Konfigurieren des automatischen Upgrades von Betriebssystemimages sicher, dass die Eigenschaft *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* in der Definition des Skalierungsgruppenmodells auf *true* festgelegt ist.

### <a name="rest-api"></a>REST-API
Im folgenden Beispiel wird beschrieben, wie automatische Betriebssystemupgrades auf einem Skalierungsgruppenmodell festgelegt werden:

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2018-10-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Überprüfen Sie mit dem [Update-AzVmss](/powershell/module/az.compute/update-azvmss)-Cmdlet den Verlauf des Betriebssystemupgrades für Ihre Skalierungsgruppe. Im folgenden Beispiel werden automatische Upgrades für die Skalierungsgruppe *myVMSS* in der Ressourcengruppe *myResourceGroup* konfiguriert:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Überprüfen Sie mit [az vmss update](/cli/azure/vmss#az-vmss-update) den Verlauf des Betriebssystemupgrades für Ihre Skalierungsgruppe. Verwenden Sie Azure CLI 2.0.47 oder höher. Im folgenden Beispiel werden automatische Upgrades für die Skalierungsgruppe *myVMSS* in der Ressourcengruppe *myResourceGroup* konfiguriert:

```azurecli-interactive
az vmss update --name myVMSS --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

## <a name="using-application-health-probes"></a>Verwenden von Anwendungsintegritätstests

Während eines Betriebssystemupgrades werden VM-Instanzen in einer Skalierungsgruppe batchweise nacheinander aktualisiert. Das Upgrade sollte nur fortgesetzt werden, wenn die Kundenanwendung auf den aktualisierten VM-Instanzen fehlerfrei ist. Es wird empfohlen, dass die Anwendung der Upgrade-Engine für das Skalierungsgruppen-Betriebssystem Integritätssignale zur Verfügung stellt. Standardmäßig wertet die Plattform während Betriebssystemupgrades den VM-Energiezustand und den Status der Erweiterungsbereitstellung aus, um festzustellen, ob eine VM-Instanz nach einem Upgrade fehlerfrei ist. Während des Betriebssystemupgrades einer VM-Instanz wird ihr Betriebssystemdatenträger durch einen neuen Datenträger ersetzt, der auf der neuesten Imageversion basiert. Nachdem das Betriebssystemupgrade abgeschlossen wurde, werden die konfigurierten Erweiterungen auf diesen VMs ausgeführt. Die Anwendung wird erst dann als fehlerfrei angesehen, wenn alle Erweiterungen erfolgreich auf der Instanz bereitgestellt wurden.

Eine Skalierungsgruppe kann optional mit Anwendungsintegritätstests konfiguriert werden, um der Plattform genaue Informationen zum fortlaufenden Status der Anwendung bereitzustellen. Anwendungsintegritätstests sind benutzerdefinierte Lastenausgleichs-Prüfpunkte, die als Integritätssignal verwendet werden. Die auf einer Skalierungsgruppen-VM-Instanz ausgeführte Anwendung kann auf externe HTTP- oder TCP-Anforderungen reagieren und dadurch anzeigen, dass sie fehlerfrei ist. Weitere Informationen zur Funktionsweise von benutzerdefinierten Lastenausgleichstests finden Sie unter [Grundlegendes zu Lastenausgleichstests](../load-balancer/load-balancer-custom-probe-overview.md). Ein Anwendungsintegritätstest ist für Service Fabric-Skalierungsgruppen nicht erforderlich, wird jedoch empfohlen. Nicht-Service Fabric-Skalierungsgruppen erfordern entweder Load Balancer-Anwendungsintegritätstests oder eine [Anwendungsintegritätserweiterung](virtual-machine-scale-sets-health-extension.md).

Wenn die Skalierungsgruppe für die Verwendung mehrerer Platzierungsgruppen konfiguriert ist, müssen Tests mit einem [Standardlastenausgleich](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) verwendet werden.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurieren eines benutzerdefinierten Lastenausgleichstests als Anwendungsintegritätstest für eine Skalierungsgruppe
Erstellen Sie als bewährte Methode einen Lastenausgleichstest explizit für die Skalierungsgruppenintegrität. Sie können denselben Endpunkt für einen vorhandenen HTTP-Test oder TCP-Test verwenden. Für einen Integritätstest ist jedoch möglicherweise ein anderes Verhalten als bei einem herkömmlichen Lastenausgleichstest erforderlich. Beispielsweise könnte ein herkömmlicher Lastenausgleichstest den Status „Fehlerhaft“ zurückgeben, wenn die Last der Instanz zu hoch ist, aber zum Ermitteln der Instanzintegrität bei einem automatischen Betriebssystemupgrade wäre dies nicht angemessen. Konfigurieren Sie den Test so, dass eine hohe Stichprobenrate von unter zwei Minuten gilt.

Der Lastenausgleichstest kann im *networkProfile* der Skalierungsgruppe referenziert werden und wie folgt einem internen oder einem öffentlich zugänglichen Lastenausgleich zugeordnet werden:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> Wenn Sie automatische Betriebssystemupgrades mit Service Fabric verwenden, wird das neue Betriebssystemimage nacheinander in einzelnen Updatedomänen bereitgestellt. So wird gewährleistet, dass die in Service Fabric ausgeführten Dienste hochverfügbar bleiben. Um automatische Betriebssystemupgrades in Service Fabric nutzen zu können, muss Ihr Cluster zur Verwendung der Dauerhaftigkeitsstufe „Silber“ oder höher konfiguriert sein. Weitere Informationen zu den Dauerhaftigkeitsmerkmalen von Service Fabric-Clustern finden Sie in [dieser Dokumentation](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Die Anmeldeinformationen müssen aktuell sein
Wenn Ihre Skalierungsgruppe Anmeldeinformationen zum Zugreifen auf externe Ressourcen verwendet – wenn z.B. eine VM-Erweiterung konfiguriert wurde, die ein SAS-Token für das Speicherkonto verwendet – stellen Sie sicher, dass die Anmeldeinformationen aktuell sind. Wenn die verwendeten Anmeldeinformationen (Zertifikate und Token eingeschlossen) abgelaufen sind, kommt es beim Upgrade zu einem Fehler, und der erste VM-Batch wechselt in einen fehlerhaften Zustand.

Führen Sie die folgenden Schritte aus, um nach einem Fehler bei der Ressourcenauthentifizierung die VMs wiederherzustellen und automatische Betriebssystemupgrades erneut zu aktivieren:

* Generieren Sie das Token (oder andere Anmeldeinformationen) neu, die an Ihre Erweiterungen übergeben werden.
* Stellen Sie sicher, dass auf dem virtuellen Computer verwendete Anmeldeinformationen für die Kommunikation mit externen Entitäten aktuell sind.
* Aktualisieren Sie Erweiterungen im Skalierungsgruppenmodell mit den neuen Token.
* Stellen Sie die aktualisierte Skalierungsgruppe bereit, um alle VM-Instanzen – einschließlich der fehlerhaften – zu aktualisieren.

## <a name="using-application-health-extension"></a>Verwenden der Anwendungsintegritätserweiterung
Die Application Health-Erweiterung wird in einer VM-Skalierungsgruppeninstanz bereitgestellt und berichtet von der Skalierungsgruppeninstanz aus über die VM-Integrität. Sie können die Erweiterung konfigurieren, um einen Anwendungsendpunkt zu prüfen und den Status der Anwendung in dieser Instanz zu aktualisieren. Dieser Instanzstatus wird von Azure geprüft, um festzustellen, ob eine Instanz für Upgradevorgänge geeignet ist.

Da die Erweiterung von einem virtuellen Computer aus über die Integrität berichtet, kann die Erweiterung in Situationen verwendet werden, in denen externe Tests wie z.B. Anwendungsintegritätstests (die benutzerdefinierte Azure Load Balancer-[Tests](../load-balancer/load-balancer-custom-probe-overview.md) verwenden) nicht genutzt werden können.

Es gibt mehrere Möglichkeiten, die Application Health-Erweiterung für Ihre Skalierungsgruppen bereitzustellen, wie in den Beispielen in [diesem Artikel](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension) beschrieben.

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Abrufen des Verlaufs automatischer Betriebssystemimageupgrades
Sie können den Verlauf des letzten für Ihre Skalierungsgruppe durchgeführten Betriebssystemupgrades mit Azure PowerShell, Azure CLI 2.0 oder der REST-API überprüfen. Sie können den Verlauf für die letzten fünf Betriebssystemupgradeversuche innerhalb der vergangenen zwei Monate abrufen.

### <a name="rest-api"></a>REST-API
Im folgenden Beispiel wird der Status der Skalierungsgruppe *myVMSS* in der Ressourcengruppe *myResourceGroup* mit der [REST-API](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) überprüft:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```

Der GET-Aufruf gibt Eigenschaften ähnlich der folgenden Beispielausgabe zurück:

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Überprüfen Sie mit dem [Get-AzVmss](/powershell/module/az.compute/get-azvmss)-Cmdlet den Verlauf des Betriebssystemupgrades für Ihre Skalierungsgruppe. Das folgende Beispiel zeigt, wie der Status des Betriebssystemupgrades für eine Skalierungsgruppe namens *myVMSS* in der Ressourcengruppe *myResourceGroup* überprüft wird:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Überprüfen Sie mit [az vmss get-os-upgrade-history](/cli/azure/vmss#az-vmss-get-os-upgrade-history) den Verlauf des Betriebssystemupgrades für Ihre Skalierungsgruppe. Verwenden Sie Azure CLI 2.0.47 oder höher. Das folgende Beispiel zeigt, wie der Status des Betriebssystemupgrades für eine Skalierungsgruppe namens *myVMSS* in der Ressourcengruppe *myResourceGroup* überprüft wird:

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myVMSS
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Wie erhalte ich die neueste Version eines Plattformimages?

Sie können die Imageversionen für automatische Betriebssystemupgrades von unterstützten SKUs mithilfe der folgenden Beispiele erhalten:

### <a name="rest-api"></a>REST-API
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="deploy-with-a-template"></a>Bereitstellen mit einer Vorlage

Sie können Vorlagen zum Bereitstellen einer Skalierungsgruppe mit automatischen Betriebssystemupgrades für unterstützte Images wie z.B. [Ubuntu 16.04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json) verwenden.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

## <a name="next-steps"></a>Nächste Schritte
Weitere Beispiele für die Verwendung automatischer Betriebssystemupgrades mit Skalierungsgruppen finden Sie im [GitHub-Repository](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
