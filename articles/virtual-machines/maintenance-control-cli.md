---
title: Wartungssteuerung für virtuelle Azure-Computer mit der CLI
description: Es wird beschrieben, wie Sie die Wartung steuern, wenn diese mithilfe der Wartungssteuerung und der CLI auf Ihre virtuellen Azure-Computer angewendet wird.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/20/2020
ms.author: cynthn
ms.openlocfilehash: d94cd649df9da6b36ac484d4fc1e6acef7a21bb7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026164"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-cli"></a>Steuern von Updates mit der Wartungssteuerung und der Azure CLI

Mit der Wartungssteuerung können Sie entscheiden, wann Sie Plattformupdates der Hostinfrastruktur auf Ihre isolierten VMs und Ihre dedizierten Azure-Hosts anwenden. In diesem Thema werden die Azure CLI-Optionen für die Wartungssteuerung behandelt. Weitere Informationen zu den Vorteilen der Verwendung der Wartungssteuerung, ihren Einschränkungen und anderen Verwaltungsoptionen finden Sie unter [Verwalten von Plattformupdates mit der Wartungssteuerung](maintenance-control.md).

## <a name="create-a-maintenance-configuration"></a>Erstellen einer Wartungskonfiguration

Verwenden Sie `az maintenance configuration create`, um eine Wartungskonfiguration zu erstellen. In diesem Beispiel wird eine Wartungskonfiguration mit dem Namen *myConfig* erstellt, die für den Host gilt. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host\
   --location eastus
```

Kopieren Sie die Konfigurations-ID aus der Ausgabe, um sie später zu verwenden.

Durch die Verwendung von `--maintenance-scope host` wird sichergestellt, dass die Wartungskonfiguration zum Steuern von Updates für die Hostinfrastruktur verwendet wird.

Wenn Sie versuchen, eine Konfiguration mit dem gleichen Namen, aber an einem anderen Speicherort zu erstellen, erhalten Sie eine Fehlermeldung. Konfigurationsnamen müssen für Ihre Ressourcengruppe eindeutig sein.

Mithilfe von `az maintenance configuration list` können Sie verfügbare Wartungskonfigurationen abfragen.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>Erstellen einer Wartungskonfiguration mit einem geplanten Fenster
Sie können auch ein geplantes Fenster deklarieren, in dem Azure die Updates auf Ihre Ressourcen anwendet. In diesem Beispiel wird eine Wartungskonfiguration mit dem Namen myConfig mit einem geplanten Fenster von 5 Stunden am vierten Montag jeden Monats erstellt. Nachdem Sie ein geplantes Fenster erstellt haben, müssen Sie die Updates nicht mehr manuell anwenden.

```azurecli-interactive
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host \
   --location eastus \
   --maintenance-window-duration "05:00" \
   --maintenance-window-recur-every "Month Fourth Monday" \
   --maintenance-window-start-date-time "2020-12-30 08:00" \
   --maintenance-window-time-zone "Pacific Standard Time"
```

> [!IMPORTANT]
> Die **Dauer** der Wartung muss *2 Stunden* oder länger sein. Die Wartung muss mindestens ein Mal in 35 Tagen **wiederholt** werden.

Die Wiederholung der Wartung kann täglich, wöchentlich oder monatlich ausgedrückt werden. Beispiele:
- **daily**- maintenance-window-recur-every: „Day“ **oder** „3Days“
- **weekly**- maintenance-window-recur-every: „3Weeks“ **oder** „Week Saturday,Sunday“
- **monthly**- maintenance-window-recur-every: „Month day23,day24“ **oder** „Month Last Sunday“ **oder** „Month Fourth Monday“


## <a name="assign-the-configuration"></a>Zuweisen der Konfiguration

Verwenden Sie `az maintenance assignment create`, um die Konfiguration Ihrer isolierten VM oder Azure Dedicated Host zuzuweisen.

### <a name="isolated-vm"></a>Isolierte VM

Wenden Sie die Konfiguration auf einen virtuellen Computer an, indem Sie die ID der Konfiguration verwenden. Geben Sie `--resource-type virtualMachines` an, und geben Sie den Namen der VM für `--resource-name`, die Ressourcengruppe für die VM in `--resource-group` und den Speicherort der VM für `--location` an. 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>Dedicated Host

Wenn Sie eine Konfiguration auf einen dedizierten Host anwenden möchten, müssen Sie `--resource-type hosts`, `--resource-parent-name` mit dem Namen der Hostgruppe und `--resource-parent-type hostGroups` einschließen. 

Der `--resource-id`-Parameter ist die ID des Hosts. Sie können [az vm host get-instance-view](/cli/azure/vm/host#az-vm-host-get-instance-view) verwenden, um die ID Ihres dedizierten Hosts zu erhalten.

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>Überprüfen der Konfiguration

Sie können mithilfe von `az maintenance assignment list` überprüfen, ob die Konfiguration ordnungsgemäß angewendet wurde oder welche Konfiguration zurzeit angewendet wird.

### <a name="isolated-vm"></a>Isolierte VM

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>Dedicated Host 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>Prüfen auf ausstehende Updates

Verwenden Sie `az maintenance update list`, um zu überprüfen, ob noch Updates ausstehen. Aktualisieren Sie den Befehl --subscription, damit dieser zur ID für das Abonnement wird, das die VM enthält.

Wenn keine Updates vorhanden sind, gibt der Befehl eine Fehlermeldung zurück, die folgenden Text enthält: `Resource not found...StatusCode: 404`.

Wenn Updates vorhanden sind, wird nur eines zurückgegeben, auch wenn mehrere Updates ausstehend sind. Die Daten für dieses Update werden in einem Objekt zurückgegeben:

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>Isolierte VM

Überprüfen Sie, ob für eine isolierte VM ausstehende Updates angezeigt werden. In diesem Beispiel ist die Ausgabe zur besseren Lesbarkeit als Tabelle formatiert.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Dedicated Host

Überprüfen Sie, ob für einen dedizierten Host ausstehende Updates angezeigt werden. In diesem Beispiel ist die Ausgabe zur besseren Lesbarkeit als Tabelle formatiert. Ersetzen Sie die Werte für die Ressourcen durch Ihre eigenen.

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>Anwenden von Updates

Verwenden Sie `az maintenance apply update`, um ausstehende Updates anzuwenden. Bei Erfolg gibt dieser Befehl JSON-Code zurück, der die Details zum Update enthält.

### <a name="isolated-vm"></a>Isolierte VM

Erstellen Sie eine Anforderung, um Updates auf eine isolierte VM anzuwenden.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Dedicated Host

Wenden Sie Updates auf einen dedizierten Host an.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>Überprüfen Sie den Status für das Anwenden von Updates. 

Sie können den Fortschritt der Updates mithilfe von `az maintenance applyupdate get` überprüfen. 

Sie können `default` als Updatenamen verwenden, um die Ergebnisse des letzten Updates anzuzeigen, oder `myUpdateName` durch den Namen des Updates ersetzen, der beim Ausführen von `az maintenance applyupdate create` zurückgegeben wurde.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime ist die Uhrzeit, zu der das Update vollständig durchgeführt wurde. Dies kann durch Sie initiiert werden oder durch die Plattform, falls das Selbstwartungsfenster nicht genutzt wurde. Wenn noch nie über die Wartungssteuerung ein Update angewandt wurde, wird der Standardwert angezeigt.

### <a name="isolated-vm"></a>Isolierte VM

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>Dedicated Host

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>Löschen einer Wartungskonfiguration

Verwenden Sie `az maintenance configuration delete`, um eine Wartungskonfiguration zu löschen. Durch das Löschen der Konfiguration wird die Wartungssteuerung aus den zugeordneten Ressourcen entfernt.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --resource-name myConfig
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [Wartung und Updates](maintenance-and-updates.md).
