---
title: 'Tutorial: Automatisches Skalieren einer Skalierungsgruppe mit Azure-Vorlagen'
description: Es wird beschrieben, wie Sie eine VM-Skalierungsgruppe mit Azure Resource Manager-Vorlagen automatisch skalieren, wenn sich die CPU-Anforderungen erhöhen oder verringern.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 03/27/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 7e727d06670c9d07ec1aa18b92504433f6c519d6
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94518293"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-an-azure-template"></a>Tutorial: Automatisches Skalieren einer VM-Skalierungsgruppe mit einer Azure-Vorlage
Beim Erstellen einer Skalierungsgruppe definieren Sie die Anzahl von VM-Instanzen, die Sie ausführen möchten. Wenn sich die Nachfrage nach Ihrer Anwendung ändert, können Sie die Anzahl von VM-Instanzen automatisch erhöhen oder verringern lassen. Dank der Möglichkeit zum automatischen Skalieren können Sie über den gesamten Lebenszyklus Ihrer App die Kundennachfrage decken oder auf Änderungen der Anwendungsleistung reagieren. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Verwenden der automatischen Skalierung mit einer Skalierungsgruppe
> * Erstellen und Verwenden von Regeln für die automatische Skalierung
> * Durchführen eines Belastungstests für VM-Instanzen und Auslösen von Regeln für die automatische Skalierung
> * Zurückfahren der automatischen Skalierung bei einer Reduzierung des Bedarfs

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.29 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 


## <a name="define-an-autoscale-profile"></a>Definieren eines Profils für die automatische Skalierung
Sie definieren ein Profil für die automatische Skalierung in einer Azure-Vorlage mit dem Ressourcenanbieter *Microsoft.insights/autoscalesettings*. Mit einem *Profil* werden die Details zur Kapazität der Skalierungsgruppe und alle dazugehörigen Regeln angegeben. Im folgenden Beispiel wird ein Profil mit dem Namen *Autoscale by percentage based on CPU usage* (Automatische Skalierung nach Prozentsatz basierend auf der CPU-Auslastung) definiert und die Standardkapazität (und die minimale Kapazität) auf *2* VM-Instanzen und als Maximum *10* VM-Instanzen festgelegt:

```json
{
"type": "Microsoft.insights/autoscalesettings",
"name": "Autoscale",
"apiVersion": "2015-04-01",
"location": "[variables('location')]",
"scale": null,
"properties": {
  "profiles": [
    {
      "name": "Autoscale by percentage based on CPU usage",
      "capacity": {
        "minimum": "2",
        "maximum": "10",
        "default": "2"
      }
    }
  ]
}
```


## <a name="define-a-rule-to-autoscale-out"></a>Definieren einer Regel zum automatischen Aufskalieren
Wenn sich die Nachfrage für Ihre Anwendung erhöht, erhöht sich auch die Last für die VM-Instanzen in Ihrer Skalierungsgruppe. Falls es sich um eine dauerhafte Last und nicht nur um eine kurzzeitige höhere Nachfrage handelt, können Sie die Regeln für die automatische Skalierung konfigurieren, um die Anzahl von VM-Instanzen in der Skalierungsgruppe zu erhöhen. Nachdem diese VM-Instanzen erstellt und Ihre Anwendungen bereitgestellt wurden, beginnt die Skalierungsgruppe damit, über das Lastenausgleichsmodul Datenverkehr darauf zu verteilen. Sie steuern, welche Metriken überwacht werden, z.B. CPU oder Datenträger, wie lange die Anwendungslast einen bestimmten Schwellenwert einhalten muss und wie viele VM-Instanzen der Skalierungsgruppe hinzugefügt werden sollen.

Im folgenden Beispiel wird eine Regel definiert, mit der die Anzahl von VM-Instanzen in einer Skalierungsgruppe erhöht wird, wenn die durchschnittliche CPU-Last fünf Minuten lang über 70% liegt. Wenn die Regel ausgelöst wird, wird die Anzahl von VM-Instanzen um 3 erhöht.

Für diese Regel werden die folgenden Parameter verwendet:

| Parameter         | Erklärung                                                                                                         | Wert           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | Die Leistungsmetrik zum Überwachen und Anwenden von Skalierungsgruppenaktionen.                                                   | CPU in Prozent  |
| *timeGrain*       | Gibt an, wie häufig Metriken zu Analysezwecken gesammelt werden.                                                                   | 1 Minute        |
| *timeAggregation* | Definiert, wie die gesammelten Metriken zu Analysezwecken aggregiert werden sollen.                                                | Average         |
| *timeWindow*      | Der überwachte Zeitraum, bevor die Metrik und Schwellenwerte verglichen werden.                                   | 5 Minuten       |
| *operator*        | Operator zum Vergleichen der Metrikdaten mit dem Schwellenwert.                                                     | Größer als    |
| *threshold*       | Der Wert, der für die Regel für die automatische Skalierung das Auslösen einer Aktion bewirkt.                                                      | 70 %             |
| *direction*       | Definiert, ob die Skalierungsgruppe auf- oder abskaliert werden soll, wenn die Regel zutrifft.                                              | Erhöhung        |
| *type*            | Gibt an, dass die Anzahl von VM-Instanzen um einen bestimmten Wert geändert werden soll.                                    | Änderungsanzahl    |
| *value*           | Gibt an, wie viele VM-Instanzen horizontal hoch- oder herunterskaliert werden sollen, wenn die Regel zutrifft.                                             | 3               |
| *cooldown*        | Gibt an, wie lange gewartet wird, bevor die Regel erneut angewendet wird, damit die Aktionen für die automatische Skalierung wirksam werden können. | 5 Minuten       |

Die folgende Regel würde dem Profilabschnitt des Ressourcenanbieters *Microsoft.insights/autoscalesettings* aus dem vorherigen Abschnitt hinzugefügt werden:

```json
"rules": [
  {
    "metricTrigger": {
      "metricName": "Percentage CPU",
      "metricNamespace": "",
      "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('vmssName'))]",
      "metricResourceLocation": "[variables('location')]",
      "timeGrain": "PT1M",
      "statistic": "Average",
      "timeWindow": "PT5M",
      "timeAggregation": "Average",
      "operator": "GreaterThan",
      "threshold": 70
    },
    "scaleAction": {
      "direction": "Increase",
      "type": "ChangeCount",
      "value": "3",
      "cooldown": "PT5M"
    }
  }
]
```


## <a name="define-a-rule-to-autoscale-in"></a>Definieren einer Regel zum automatischen Abskalieren
Es kann sein, dass die Nachfrage nach Ihrer Anwendung abends oder am Wochenende abnimmt. Wenn diese Verringerung der Last für einen bestimmten Zeitraum anhält, können Sie Regeln zur automatischen Skalierung konfigurieren, um die Anzahl von VM-Instanzen in der Skalierungsgruppe zu reduzieren. Mit dieser Aktion zum horizontalen Herunterskalieren werden die Kosten für die Ausführung Ihrer Skalierungsgruppe gesenkt, da Sie nur so viele Instanzen ausführen, wie für die Erfüllung der derzeitigen Nachfrage erforderlich sind.

Im folgenden Beispiel wird eine Regel zum Abskalieren der Anzahl von VM-Instanzen um den Wert 1 definiert, wenn die durchschnittliche CPU-Last fünf Minuten lang unter 30% fällt. Diese Regel würde dem Profil für die automatische Skalierung nach der vorherigen Regel zum Aufskalieren hinzugefügt:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('vmssName'))]",
    "metricResourceLocation": "[variables('location')]",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "ChangeCount",
    "value": "1",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-an-autoscaling-scale-set"></a>Erstellen einer Skalierungsgruppe für die automatische Skalierung
Wir verwenden eine Beispielvorlage, um eine Skalierungsgruppe zu erstellen und Regeln für die automatische Skalierung anzuwenden. Sie können sich [die vollständige Vorlage ansehen](https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/autoscale.json) oder [den Abschnitt mit dem Ressourcenanbieter *Microsoft.insights/autoscalesettings*](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/autoscale.json#L220) der Vorlage anzeigen.

Erstellen Sie zunächst mit [az group create](/cli/azure/group) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Sie erstellen nun mit [az group deployment create](/cli/azure/group/deployment) eine VM-Skalierungsgruppe. Geben Sie bei entsprechender Aufforderung Ihren eigenen Benutzernamen, z.B. *azureuser*, mit dem dazugehörigen Kennwort jeweils als Anmeldeinformationen für eine VM-Instanz an:

```azurecli-interactive
az group deployment create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/autoscale.json
```

Die Erstellung und Konfiguration aller Ressourcen und virtuellen Computer der Skalierungsgruppe dauert einige Minuten.


## <a name="generate-cpu-load-on-scale-set"></a>Generieren einer CPU-Last in einer Skalierungsgruppe
Generieren Sie zum Testen der Regeln für die automatische Skalierung eine CPU-Last auf den VM-Instanzen in der Skalierungsgruppe. Diese simulierte CPU-Last bewirkt über die Regeln für die automatische Skalierung das Aufskalieren und eine Erhöhung der Anzahl von VM-Instanzen. Wenn die simulierte CPU-Last dann verringert wird, wird über die Regeln für das automatische Skalieren das Abskalieren durchgeführt und die Anzahl von VM-Instanzen reduziert.

Listen Sie mit [az vmss list-instance-connection-info](/cli/azure/vmss) zuerst die Adresse und die Ports für die Verbindungsherstellung mit VM-Instanzen in einer Skalierungsgruppe auf:

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

Die folgende Beispielausgabe enthält den Instanznamen, die öffentliche IP-Adresse des Lastenausgleichs und die Portnummer, an die Datenverkehr durch die Regeln für die Netzwerkadressübersetzung (Network Address Translation, NAT) weitergeleitet wird:

```json
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

Stellen Sie eine SSH-Verbindung mit Ihrer ersten VM-Instanz her. Geben Sie mithilfe des Parameters `-p` Ihre eigene öffentliche IP-Adresse und die Portnummer an, wie im vorherigen Befehl gezeigt:

```console
ssh azureuser@13.92.224.66 -p 50001
```

Installieren Sie nach dem Anmelden das Hilfsprogramm **stress**. Starten Sie *zehn* Worker vom Typ **stress**, um CPU-Last zu generieren. Diese Worker werden *420* Sekunden lang ausgeführt. Dieser Zeitraum reicht aus, um über die Regeln der automatischen Skalierung die gewünschte Aktion zu implementieren.

```console
sudo apt-get update
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Wenn für **stress** eine Ausgabe wie *stress: info: [2688] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd* angezeigt wird, können Sie die *EINGABETASTE* drücken, um zur Eingabeaufforderung zurückzukehren.

Um zu bestätigen, dass **stress** CPU-Last generiert, können Sie die aktive Systemauslastung mit dem Hilfsprogramm **top** untersuchen:

```console
top
```

Beenden Sie **top**, und schließen Sie dann Ihre Verbindung mit der VM-Instanz. **stress** wird auf der VM-Instanz weiterhin ausgeführt.

```console
Ctrl-c
exit
```

Stellen Sie über die Portnummer, die mit dem obigen Befehl [az vmss list-instance-connection-info](/cli/azure/vmss) aufgeführt wurde, eine Verbindung mit der zweiten VM-Instanz her:

```console
ssh azureuser@13.92.224.66 -p 50003
```

Installieren Sie **stress**, und führen Sie das Hilfsprogramm aus. Starten Sie auf dieser zweiten VM-Instanz anschließend zehn Worker.

```console
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Auch hier gilt wieder: Wenn für **stress** eine Ausgabe wie *stress: info: [2713] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd* angezeigt wird, können Sie die *EINGABETASTE* drücken, um zur Eingabeaufforderung zurückzukehren.

Schließen Sie die Verbindung mit der zweiten VM-Instanz. **stress** wird auf der VM-Instanz weiterhin ausgeführt.

```console
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>Überwachen der aktiven Regeln für die automatische Skalierung
Verwenden Sie **watch**, um die Anzahl von VM-Instanzen in Ihrer Skalierungsgruppe zu überwachen. Es dauert fünf Minuten, bis die Regeln für die automatische Skalierung als Reaktion auf die CPU-Last, die mit **stress** auf den einzelnen VM-Instanzen generiert wurde, mit dem Aufskalieren beginnen:

```azurecli-interactive
watch az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

Nachdem der CPU-Schwellenwert erreicht wurde, wird mit den Regeln für die automatische Skalierung die Anzahl von VM-Instanzen in der Skalierungsgruppe erhöht. In der folgenden Ausgabe werden drei VMs angezeigt, die erstellt werden, wenn für die Skalierungsgruppe das automatische horizontale Hochskalieren durchgeführt wird:

```output
Every 2.0s: az vmss list-instances --resource-group myResourceGroup --name myScaleSet --output table

  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            MYRESOURCEGROUP  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             MYRESOURCEGROUP  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             MYRESOURCEGROUP  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Nachdem die Ausführung von **stress** auf den ersten VM-Instanzen angehalten wurde, sinkt die CPU-Last wieder auf normale Werte. Nach weiteren fünf Minuten wird über die Regeln für die automatische Skalierung dann das Abskalieren für die Anzahl von VM-Instanzen durchgeführt. Bei Aktionen zum Abskalieren werden die VM-Instanzen mit den höchsten IDs zuerst entfernt. Wenn eine Skalierungsgruppe Verfügbarkeitsgruppen oder Verfügbarkeitszonen verwendet, werden Vorgänge zum Abskalieren gleichmäßig auf diese VM-Instanzen verteilt. In der folgenden Beispielausgabe ist zu sehen, dass beim automatischen horizontalen Herunterskalieren der Skalierungsgruppe eine VM-Instanz gelöscht wurde:

```output
           6  True                  eastus      myScaleSet_6  Deleting             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Beenden Sie *watch* mit `Ctrl-c`. Für die Skalierungsgruppe wird alle fünf Minuten das Abskalieren durchgeführt und eine VM-Instanz entfernt, bis die Mindestanzahl von zwei Instanzen erreicht ist.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Löschen Sie die Ressourcengruppe und alle dazugehörigen Ressourcen mit [az group delete](/cli/azure/group), um Ihre Skalierungsgruppe und die weiteren Ressourcen zu entfernen:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial wurde beschrieben, wie Sie mit der Azure CLI eine Skalierungsgruppe automatisch ab- oder aufskalieren:

> [!div class="checklist"]
> * Verwenden der automatischen Skalierung mit einer Skalierungsgruppe
> * Erstellen und Verwenden von Regeln für die automatische Skalierung
> * Durchführen eines Belastungstests für VM-Instanzen und Auslösen von Regeln für die automatische Skalierung
> * Zurückfahren der automatischen Skalierung bei einer Reduzierung des Bedarfs
