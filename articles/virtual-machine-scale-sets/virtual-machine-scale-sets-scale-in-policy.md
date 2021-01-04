---
title: Verwenden benutzerdefinierter Richtlinien für horizontales Herunterskalieren mit Azure-VM-Skalierungsgruppen
description: Erfahren Sie, wie Sie benutzerdefinierte Richtlinien für das horizontale Herunterskalieren mit Azure-VM-Skalierungsgruppen verwenden, die die Autoskalierungskonfiguration zum Verwalten der Instanzenzahl verwenden.
services: virtual-machine-scale-sets
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 02/26/2020
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 9ca6310705d54d563aae746ab2dbfe6cb412e6a9
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747807"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Verwenden benutzerdefinierter Richtlinien für horizontales Herunterskalieren mit Azure-VM-Skalierungsgruppen

Eine Bereitstellung einer VM-Skalierungsgruppe kann basierend auf einem Array von Metriken einschließlich plattform- und benutzerdefinierter Metriken horizontal hoch- oder herunterskaliert werden. Während bei einer horizontalen Skalierung basierend auf dem Skalierungsgruppenmodell neue VMs erstellt werden, wirkt sich das horizontale Herunterskalieren auf die Ausführung virtueller Computer aus, die im Laufe der Entwicklung der Skalierungsgruppenworkload möglicherweise unterschiedliche Konfigurationen und/oder Funktionen aufweisen. 

Mit dem Feature „Horizontales Herunterskalieren“ können Benutzer die Reihenfolge konfigurieren, in der virtuelle Computer horizontal herunterskaliert werden, indem drei Konfigurationen für horizontales Herunterskalieren verwendet werden. 

1. Standard
2. NewestVM
3. OldestVM

### <a name="default-scale-in-policy"></a>Standardrichtlinie für das horizontale Herunterskalieren

Standardmäßig wendet die VM-Skalierungsgruppe diese Richtlinie an, um zu bestimmen, welche Instanz(en) horizontal herunterskaliert werden soll(en). Mit der *Standardrichtlinie* werden VMs für das horizontale Herunterskalieren in der folgenden Reihenfolge ausgewählt:

1. Ausgleichen virtueller Computer über Verfügbarkeitszonen hinweg (wenn die Skalierungsgruppe in zonaler Konfiguration bereitgestellt wird)
2. Ausgleichen virtueller Computer über Domänen hinweg (beste Leistung)
3. Löschen des virtuellen Computers mit der höchsten Instanz-ID

Benutzer müssen keine Richtlinie für das horizontale Herunterskalieren angeben, wenn sie nur möchten, dass die Standardreihenfolge befolgt wird.

Beachten Sie, dass der Ausgleich über Verfügbarkeitszonen oder Fehlerdomänen hinweg keine Instanzen zwischen Verfügbarkeitszonen oder Fehlerdomänen verschiebt. Der Ausgleich erfolgt durch Löschen virtueller Computer aus den unausgeglichenen Verfügbarkeitszonen oder Fehlerdomänen, bis die Verteilung virtueller Computer ausgeglichen ist.

### <a name="newestvm-scale-in-policy"></a>NewestVM-Richtlinie für das horizontale Herunterskalieren

Diese Richtlinie löscht den neuesten erstellten virtuellen Computer in der Skalierungsgruppe nach dem Ausgleich von VMs über Verfügbarkeitszonen hinweg (für zonale Bereitstellungen). Wenn Sie diese Richtlinie aktivieren, ist eine Konfigurationsänderung für das VM-Skalierungsgruppenmodell erforderlich.

### <a name="oldestvm-scale-in-policy"></a>OldestVM-Richtlinie für das horizontale Herunterskalieren

Diese Richtlinie löscht den ältesten erstellten virtuellen Computer in der Skalierungsgruppe nach dem Ausgleich von VMs über Verfügbarkeitszonen hinweg (für zonale Bereitstellungen). Wenn Sie diese Richtlinie aktivieren, ist eine Konfigurationsänderung für das VM-Skalierungsgruppenmodell erforderlich.

## <a name="enabling-scale-in-policy"></a>Aktivieren der Richtlinie für das horizontale Herunterskalieren

Eine Richtlinie für horizontales Herunterskalieren ist im VM-Skalierungsgruppenmodell definiert. Wie in den obigen Abschnitten erwähnt, ist eine Richtliniendefinition für das horizontale Herunterskalieren erforderlich, wenn die Richtlinien „NewestVM“ und „OldestVM“ verwendet werden. Die VM-Skalierungsgruppe verwendet automatisch die Standardrichtlinie zum horizontalen Herunterskalieren, wenn im Skalierungsgruppenmodell keine Richtliniendefinition für das horizontale Herunterskalieren vorhanden ist. 

Eine Richtlinie zum horizontalen Herunterskalieren kann auf folgende Weise für das VM-Skalierungsgruppenmodell definiert werden:

### <a name="azure-portal"></a>Azure-Portal
 
In den folgenden Schritten wird die Richtlinie zum horizontalen Herunterskalieren beim Erstellen einer neuen Skalierungsgruppe definiert. 
 
1. Navigieren Sie zu **VM-Skalierungsgruppen** .
1. Wählen Sie **+ Hinzufügen** aus, um eine neue Skalierungsgruppe zu erstellen.
1. Navigieren Sie zur Registerkarte **Skalierung** . 
1. Suchen Sie den Abschnitt **Richtlinie für horizontales Herunterskalieren** .
1. Wählen Sie eine Richtlinie zum horizontalen Herunterskalieren aus der Dropdownliste aus.
1. Wenn Sie die Erstellung der neuen Skalierungsgruppe abgeschlossen haben, wählen Sie die Schaltfläche **Überprüfen und erstellen** aus.

### <a name="using-api"></a>Verwenden der API

Führen Sie einen PUT-Befehl für die VM-Skalierungsgruppe mit API 2019-03-01 aus:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["OldestVM"]  
        } 
    }    
} 
```
### <a name="azure-powershell"></a>Azure PowerShell

Erstellen Sie eine Ressourcengruppe, und erstellen Sie dann eine neue Skalierungsgruppe, für die die Richtlinie für horizontales Herunterskalieren auf *OldestVM* festgelegt ist.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Im folgenden Beispiel wird eine Richtlinie zum horizontalen Herunterskalieren beim Erstellen einer neuen Skalierungsgruppe hinzugefügt. Erstellen Sie zunächst eine Ressourcengruppe, und erstellen Sie dann eine neue Skalierungsgruppe, für die die Richtlinie für horizontales Herunterskalieren auf *OldestVM* festgelegt ist. 

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Verwenden einer Vorlage

Fügen Sie in Ihrer Vorlage unter „Eigenschaften“ Folgendes hinzu:

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

Die oben genannten Blöcke geben an, dass die VM-Skalierungsgruppe den ältesten virtuellen Computer in einer Skalierungsgruppe mit Zonenausgleich löscht, wenn (durch Autoskalierung oder manuelles Löschen) ein horizontales Herunterskalieren ausgelöst wird.

Bei einer VM-Skalierungsgruppe ohne Zonenausgleich löscht die Skalierungsgruppe zuerst virtuelle Computer in der/den unausgeglichenen Zone(n). Innerhalb der unausgeglichenen Zonen verwendet die Skalierungsgruppe die oben angegebene Richtlinie für das Abskalieren, um zu bestimmen, welche VM abskaliert werden soll. In diesem Fall wählt die Skalierungsgruppe in einer unausgeglichenen Zone die älteste VM in dieser Zone zum Löschen aus.

Bei einer nicht zonalen VM-Skalierungsgruppe wählt die Richtlinie den ältesten virtuellen Computer in der Skalierungsgruppe zum Löschen aus.

Der gleiche Vorgang wird bei Verwendung von „NewestVM“ in der obigen Richtlinie zum horizontalen Herunterskalieren angewendet.

## <a name="modifying-scale-in-policies"></a>Ändern von Richtlinien für horizontales Herunterskalieren

Der Prozess zum Ändern der Richtlinie für das horizontale Herunterskalieren ähnelt dem zur Anwendung der Richtlinie für das horizontale Herunterskalieren. Wenn Sie im obigen Beispiel etwa die Richtlinie von „OldestVM“ in „NewestVM“ ändern möchten, können Sie folgendermaßen vorgehen:

### <a name="azure-portal"></a>Azure-Portal

Sie können die Richtlinie für das horizontale Herunterskalieren einer vorhandenen Skalierungsgruppe über das Azure-Portal ändern. 
 
1. Wählen Sie in einer vorhandenen VM-Skalierungsgruppe im Menü auf der linken Seite **Skalierung** aus.
1. Wählen Sie die Registerkarte **Horizontales Herunterskalieren** aus.
1. Wählen Sie eine Richtlinie zum horizontalen Herunterskalieren aus der Dropdownliste aus.
1. Wählen Sie **Speichern** aus, wenn der Vorgang abgeschlossen ist. 

### <a name="using-api"></a>Verwenden der API

Führen Sie einen PUT-Befehl für die VM-Skalierungsgruppe mit API 2019-03-01 aus:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01 

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["NewestVM"]  
        } 
    }    
}
```
### <a name="azure-powershell"></a>Azure PowerShell

Aktualisieren Sie die Richtlinie für das horizontale Herunterskalieren einer vorhandenen Skalierungsgruppe:

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Im folgenden finden Sie ein Beispiel für das Aktualisieren der Richtlinie für das horizontale Herunterskalieren einer vorhandenen Skalierungsgruppe: 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Verwenden einer Vorlage

Ändern Sie die Vorlage unter „Eigenschaften“ wie unten dargestellt, und stellen Sie sie erneut bereit: 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

Der gleiche Vorgang wird angewendet, wenn Sie „NewestVM“ in „Standard“ oder „OldestVM“ ändern möchten.

## <a name="instance-protection-and-scale-in-policy"></a>Instanzschutz und Richtlinie zum horizontalen Herunterskalieren

VM-Skalierungsgruppen umfassen zwei Arten von [Instanzschutz](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection):

1. Schutz vor horizontalem Herunterskalieren
2. Schutz vor Skalierungsgruppenaktionen

Ein geschützter virtueller Computer wird unabhängig von der angewendeten Richtlinie zum horizontalen Herunterskalieren nicht durch eine Aktion zum horizontalen Herunterskalieren gelöscht. Wenn z. B. VM_0 (die älteste VM in der Skalierungsgruppe) vor dem horizontalen Herunterskalieren geschützt ist und für die Skalierungsgruppe die Richtlinie „OldestVM“ zum horizontalen Herunterskalieren aktiviert ist, wird VM_0 nicht beim horizontalen Herunterskalieren berücksichtigt, obwohl es sich um die älteste VM in der Skalierungsgruppe handelt. 

Ein geschützter virtueller Computer kann unabhängig von der in der Skalierungsgruppe aktivierten Richtlinie zum horizontalen Herunterskalieren jederzeit vom Benutzer manuell gelöscht werden. 

## <a name="usage-examples"></a>Anwendungsbeispiele 

Die folgenden Beispiele veranschaulichen, wie eine VM-Skalierungsgruppe VMs zum Löschen auswählt, wenn ein horizontales Herunterskalieren ausgelöst wird. Bei virtuellen Computern mit den höchsten Instanz-IDs wird davon ausgegangen, dass es sich um die neuesten VMs in der Skalierungsgruppe handelt, und die virtuellen Computer mit den kleinsten Instanz-IDs werden als älteste VMs in der Skalierungsgruppe angesehen. 

### <a name="oldestvm-scale-in-policy"></a>OldestVM-Richtlinie für das horizontale Herunterskalieren

| Ereignis                 | Instanz-IDs in Zone 1  | Instanz-IDs in Zone 2  | Instanz-IDs in Zone 3  | Auswahl für horizontales Herunterskalieren                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Horizontales Herunterskalieren              | 3, 4, 5, 10            | **_2_* _, 6, 9, 11      | 1, 7, 8                | Auswahl zwischen Zone 1 und 2, auch wenn Zone 3 über die älteste VM verfügt. Löschen von VM2 in Zone 2, da es sich um die älteste VM in dieser Zone handelt.   |
| Horizontales Herunterskalieren              | _*_3_*_ , 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | Auswahl von Zone 1, obwohl Zone 3 über die älteste VM verfügt. Löschen von VM3 in Zone 1, da es sich um die älteste VM in dieser Zone handelt.                  |
| Horizontales Herunterskalieren              | 4, 5, 10               | 6, 9, 11               | _*_1_*_ , 7, 8          | Zonen sind ausgeglichen. Löschen von VM1 in Zone 3, da es sich um die älteste VM in der Skalierungsgruppe handelt.                                               |
| Horizontales Herunterskalieren              | _*_4_*_ , 5, 10         | 6, 9, 11               | 7, 8                   | Auswahl zwischen Zone 1 und 2. Löschen von VM4 in Zone 1, da es sich um die älteste VM in den beiden Zonen handelt.                              |
| Horizontales Herunterskalieren              | 5, 10                  | _*_6_*_ , 9, 11         | 7, 8                   | Auswahl von Zone 2, obwohl Zone 1 über die älteste VM verfügt. Löschen von VM6 in Zone 1, da es sich um die älteste VM in dieser Zone handelt.                    |
| Horizontales Herunterskalieren              | _*_5_*_ , 10            | 9, 11                  | 7, 8                   | Zonen sind ausgeglichen. Löschen von VM5 in Zone 1, da es sich um die älteste VM in der Skalierungsgruppe handelt.                                                |

Für nicht zonale VM-Skalierungsgruppen wählt die Richtlinie den ältesten virtuellen Computer in der Skalierungsgruppe zum Löschen aus. Jede „geschützte“ VM wird beim Löschen übersprungen.

### <a name="newestvm-scale-in-policy"></a>NewestVM-Richtlinie für das horizontale Herunterskalieren

| Ereignis                 | Instanz-IDs in Zone 1  | Instanz-IDs in Zone 2  | Instanz-IDs in Zone 3  | Auswahl für horizontales Herunterskalieren                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Horizontales Herunterskalieren              | 3, 4, 5, 10            | 2, 6, 9, _*_11_*_      | 1, 7, 8                | Auswahl zwischen Zone 1 und 2. Löschen von VM11 aus Zone 2, da es sich um die neueste VM in den beiden Zonen handelt.                                |
| Horizontales Herunterskalieren              | 3, 4, 5, _*_10_*_      | 2, 6, 9                | 1, 7, 8                | Auswahl von Zone 1, da sie mehr VMS als die anderen beiden Zonen aufweist. Löschen von VM10 in Zone 1, da es sich um die neueste VM in dieser Zone handelt.          |
| Horizontales Herunterskalieren              | 3, 4, 5                | 2, 6, _*_9_*_          | 1, 7, 8                | Zonen sind ausgeglichen. Löschen von VM9 in Zone 2, da es sich um die neueste VM in der Skalierungsgruppe handelt.                                                |
| Horizontales Herunterskalieren              | 3, 4, 5                | 2, 6                   | 1, 7, _*_8_*_          | Auswahl zwischen Zone 1 und 3. Löschen von VM8 in Zone 3, da es sich um die neueste VM in dieser Zone handelt.                                      |
| Horizontales Herunterskalieren              | 3, 4, _*_5_*_          | 2, 6                   | 1, 7                   | Auswahl von Zone 1, obwohl Zone 3 über den neuesten virtuellen Computer verfügt. Löschen von VM5 in Zone 1, da es sich um die neueste VM in dieser Zone handelt.                    |
| Horizontales Herunterskalieren              | 3, 4                   | 2, 6                   | 1, _ *_7_**             | Zonen sind ausgeglichen. Löschen von VM7 in Zone 3, da es sich um die neueste VM in der Skalierungsgruppe handelt.                                                |

Für nicht zonale VM-Skalierungsgruppen wählt die Richtlinie den neuesten virtuellen Computer in der Skalierungsgruppe zum Löschen aus. Jede „geschützte“ VM wird beim Löschen übersprungen. 

## <a name="troubleshoot"></a>Problembehandlung

1. Fehler beim Aktivieren von scaleInPolicy: Wenn Ihnen mit der Fehlermeldung „Member 'scaleInPolicy' für Objekt vom Typ 'properties' nicht gefunden“ ein BadRequest-Fehler gemeldet wird, überprüfen Sie die für die Skalierungsgruppe verwendete API-Version. Für dieses Feature ist API-Version 2019-03-01 oder höher erforderlich.

2. Falsche Auswahl von VMs für das horizontale Herunterskalieren: Beachten Sie die obigen Beispiele. Wenn die VM-Skalierungsgruppe eine zonale Bereitstellung ist, wird die Richtlinie für horizontales Herunterskalieren zuerst auf die unausgeglichenen Zonen und dann, sobald die Zonen ausgeglichen sind, auf die Skalierungsgruppe angewendet. Wenn die Reihenfolge des horizontalen Herunterskalierens nicht mit den obigen Beispielen konsistent ist, wenden Sie sich zur Problembehandlung an das VM-Skalierungsgruppenteam.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das [Bereitstellen Ihrer Anwendung](virtual-machine-scale-sets-deploy-app.md) in VM-Skalierungsgruppen.
