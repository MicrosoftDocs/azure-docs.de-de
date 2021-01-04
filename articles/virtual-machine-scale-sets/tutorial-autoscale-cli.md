---
title: 'Tutorial: Automatisches Skalieren einer Skalierungsgruppe mit der Azure CLI'
description: Hier wird beschrieben, wie Sie eine VM-Skalierungsgruppe mit der Azure CLI automatisch skalieren, wenn sich die CPU-Anforderungen erhöhen oder verringern.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 05/18/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 68f311a949d6c7663c5602c444d1b7b9af09dcad
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016725"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli"></a>Tutorial: Automatisches Skalieren einer VM-Skalierungsgruppe mit Azure CLI

Beim Erstellen einer Skalierungsgruppe definieren Sie die Anzahl von VM-Instanzen, die Sie ausführen möchten. Wenn sich die Nachfrage nach Ihrer Anwendung ändert, können Sie die Anzahl von VM-Instanzen automatisch erhöhen oder verringern lassen. Dank der Möglichkeit zum automatischen Skalieren können Sie über den gesamten Lebenszyklus Ihrer App die Kundennachfrage decken oder auf Änderungen der Anwendungsleistung reagieren. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Verwenden der automatischen Skalierung mit einer Skalierungsgruppe
> * Erstellen und Verwenden von Regeln für die automatische Skalierung
> * Durchführen eines Belastungstests für VM-Instanzen und Auslösen von Regeln für die automatische Skalierung
> * Zurückfahren der automatischen Skalierung bei einer Reduzierung des Bedarfs

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für dieses Tutorial ist mindestens Version 2.0.32 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-scale-set"></a>Erstellen einer Skalierungsgruppe

Erstellen Sie mit [az group create](/cli/azure/group) wie folgt eine Ressourcengruppe:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Erstellen Sie dann mit [az vmss create](/cli/azure/vmss) eine VM-Skalierungsgruppe. Im folgenden Beispiel wird eine Skalierungsgruppe mit der Instanzanzahl *2* erstellt, und es werden SSH-Schlüssel generiert, falls sie noch nicht vorhanden sind:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --instance-count 2 \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="define-an-autoscale-profile"></a>Definieren eines Profils für die automatische Skalierung

Zum Aktivieren der automatischen Skalierung für eine Skalierungsgruppe legen Sie zuerst ein Profil für die automatische Skalierung fest. Dieses Profil definiert die standardmäßige, minimale und maximale Kapazität der Skalierungsgruppe. Diese Grenzwerte sind hilfreich bei der Kostenkontrolle und sorgen nicht nur dafür, dass nicht fortlaufend VM-Instanzen erstellt werden, sondern gewährleisten auch ein ausgewogenes Verhältnis zwischen angemessener Leistung und einer minimalen Anzahl von Instanzen, die beim horizontalen Herunterskalieren erhalten bleiben. Erstellen Sie mit [az monitor autoscale create](/cli/azure/monitor/autoscale#az-monitor-autoscale-create) ein Profil für die automatische Skalierung. Im folgenden Beispiel werden als Standard- und Minimalwert *2* und als Maximalwert *10* VM-Instanzen festgelegt:

```azurecli-interactive
az monitor autoscale create \
  --resource-group myResourceGroup \
  --resource myScaleSet \
  --resource-type Microsoft.Compute/virtualMachineScaleSets \
  --name autoscale \
  --min-count 2 \
  --max-count 10 \
  --count 2
```

## <a name="create-a-rule-to-autoscale-out"></a>Erstellen einer Regel zum automatischen Aufskalieren

Wenn sich die Nachfrage für Ihre Anwendung erhöht, erhöht sich auch die Last für die VM-Instanzen in Ihrer Skalierungsgruppe. Falls es sich um eine dauerhafte Last und nicht nur um eine kurzzeitige höhere Nachfrage handelt, können Sie die Regeln für die automatische Skalierung konfigurieren, um die Anzahl von VM-Instanzen in der Skalierungsgruppe zu erhöhen. Nachdem diese VM-Instanzen erstellt und Ihre Anwendungen bereitgestellt wurden, beginnt die Skalierungsgruppe damit, über das Lastenausgleichsmodul Datenverkehr darauf zu verteilen. Sie steuern, welche Metriken überwacht werden, z.B. CPU oder Datenträger, wie lange die Anwendungslast einen bestimmten Schwellenwert einhalten muss und wie viele VM-Instanzen der Skalierungsgruppe hinzugefügt werden sollen.

Wir erstellen mit [az monitor autoscale rule create](/cli/azure/monitor/autoscale/rule#az-monitor-autoscale-rule-create) eine Regel, mit der die Anzahl von VM-Instanzen in einer Skalierungsgruppe erhöht wird, wenn die durchschnittliche CPU-Last für einen Zeitraum von 5 Minuten über 70 Prozent liegt. Wenn die Regel ausgelöst wird, wird die Anzahl von VM-Instanzen um 3 erhöht.

```azurecli-interactive
az monitor autoscale rule create \
  --resource-group myResourceGroup \
  --autoscale-name autoscale \
  --condition "Percentage CPU > 70 avg 5m" \
  --scale out 3
```

## <a name="create-a-rule-to-autoscale-in"></a>Erstellen einer Regel zum automatischen Abskalieren

Es kann sein, dass die Nachfrage nach Ihrer Anwendung abends oder am Wochenende abnimmt. Wenn diese Verringerung der Last für einen bestimmten Zeitraum anhält, können Sie Regeln zur automatischen Skalierung konfigurieren, um die Anzahl von VM-Instanzen in der Skalierungsgruppe zu reduzieren. Mit dieser Aktion zum horizontalen Herunterskalieren werden die Kosten für die Ausführung Ihrer Skalierungsgruppe gesenkt, da Sie nur so viele Instanzen ausführen, wie für die Erfüllung der derzeitigen Nachfrage erforderlich sind.

Erstellen Sie mit [az monitor autoscale rule create](/cli/azure/monitor/autoscale/rule#az-monitor-autoscale-rule-create) eine weitere Regel, mit der die Anzahl von VM-Instanzen in einer Skalierungsgruppe verringert wird, wenn die durchschnittliche CPU-Last für einen Zeitraum von 5 Minuten unter 30 Prozent liegt. Im folgenden Beispiel wird die Regel zum Abskalieren der Anzahl von VM-Instanzen um 1 definiert:

```azurecli-interactive
az monitor autoscale rule create \
  --resource-group myResourceGroup \
  --autoscale-name autoscale \
  --condition "Percentage CPU < 30 avg 5m" \
  --scale in 1
```

## <a name="generate-cpu-load-on-scale-set"></a>Generieren einer CPU-Last in einer Skalierungsgruppe

Generieren Sie zum Testen der Regeln für die automatische Skalierung eine CPU-Last auf den VM-Instanzen in der Skalierungsgruppe. Diese simulierte CPU-Last bewirkt über die automatische Skalierung das Aufskalieren und eine Erhöhung der Anzahl von VM-Instanzen. Wenn die simulierte CPU-Last dann verringert wird, wird über die Regeln für das automatische Skalieren das Abskalieren durchgeführt und die Anzahl von VM-Instanzen reduziert.

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

Verwenden Sie **watch**, um die Anzahl von VM-Instanzen in Ihrer Skalierungsgruppe zu überwachen. Es dauert fünf Minuten, bis die Regeln für die automatische Skalierung als Reaktion auf die CPU-Last, die mit **stress** auf den einzelnen VM-Instanzen generiert wurde, mit dem horizontalen Skalieren beginnen:

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
           1  True                  eastus      myScaleSet_1  Succeeded            myResourceGroup  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            myResourceGroup  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             myResourceGroup  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             myResourceGroup  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             myResourceGroup  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Nachdem die Ausführung von **stress** auf den ersten VM-Instanzen angehalten wurde, sinkt die CPU-Last wieder auf normale Werte. Nach weiteren fünf Minuten wird über die Regeln für die automatische Skalierung dann das Abskalieren für die Anzahl von VM-Instanzen durchgeführt. Bei Aktionen zum Abskalieren werden die VM-Instanzen mit den höchsten IDs zuerst entfernt. Wenn eine Skalierungsgruppe Verfügbarkeitsgruppen oder Verfügbarkeitszonen verwendet, werden Vorgänge zum Abskalieren gleichmäßig auf diese VM-Instanzen verteilt. In der folgenden Beispielausgabe ist zu sehen, dass beim automatischen horizontalen Herunterskalieren der Skalierungsgruppe eine VM-Instanz gelöscht wurde:

```output
           6  True                  eastus      myScaleSet_6  Deleting             myResourceGroup  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Beenden Sie *watch* mit `Ctrl-c`. Für die Skalierungsgruppe wird alle fünf Minuten das Abskalieren durchgeführt und eine VM-Instanz entfernt, bis die Mindestanzahl von zwei Instanzen erreicht ist.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe und alle dazugehörigen Ressourcen mit [az group delete](/cli/azure/group), um Ihre Skalierungsgruppe und die weiteren Ressourcen zu entfernen. Der Parameter `--no-wait` gibt die Steuerung an die Eingabeaufforderung zurück, ohne zu warten, bis der Vorgang abgeschlossen ist. Der Parameter `--yes` bestätigt ohne eine zusätzliche Aufforderung, dass Sie die Ressourcen löschen möchten.

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
