---
title: Tutorial – Erstellen und Verwalten virtueller Linux-Computer mit der Azure-Befehlszeilenschnittstelle | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie die Azure CLI zum Erstellen und Verwalten virtueller Linux-Computer in Azure verwenden.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/23/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 270479061ad40fdda9db06571ad4ef24b00d6c4d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66171848"
---
# <a name="tutorial-create-and-manage-linux-vms-with-the-azure-cli"></a>Tutorial: Erstellen und Verwalten virtueller Linux-Computer mit der Azure-Befehlszeilenschnittstelle

Virtuelle Azure-Computer bieten eine vollständig konfigurierbare und flexible Computerumgebung. In diesem Tutorial werden grundlegende Vorgänge bei der Bereitstellung von virtuellen Azure-Computern behandelt, z.B. Auswählen einer VM-Größe, Auswählen eines VM-Images und Bereitstellen eines virtuellen Computers. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen eines virtuellen Computers und Herstellen einer Verbindung mit ihm
> * Auswählen und Verwenden von VM-Images
> * Anzeigen und Verwenden bestimmter VM-Größen
> * Ändern der Größe eines virtuellen Computers
> * Anzeigen und Verstehen des Status von virtuellen Computern

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial die Azure CLI-Version 2.0.30 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Ressourcengruppe erstellen

Erstellen Sie mit dem Befehl [az group create](https://docs.microsoft.com/cli/azure/group) eine Ressourcengruppe. 

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Vor dem virtuellen Computer muss eine Ressourcengruppe erstellt werden. In diesem Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroupVM* in der Region *eastus* erstellt. 

```azurecli-interactive 
az group create --name myResourceGroupVM --location eastus
```

Die Ressourcengruppe wird beim Erstellen oder Ändern eines virtuellen Computers angegeben und ist im gesamten Tutorial zu sehen.

## <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie mit dem Befehl [az vm create](https://docs.microsoft.com/cli/azure/vm) einen virtuellen Computer. 

Beim Erstellen eines virtuellen Computers stehen mehrere Optionen zur Verfügung – beispielsweise Betriebssystemimage, Datenträgergröße und Administratoranmeldeinformationen. Im folgenden Beispiel wird ein virtueller Computer namens *myVM* mit Ubuntu Server erstellt. Auf dem virtuellen Computer wird ein Benutzerkonto namens *azureuser* erstellt. Außerdem werden SSH-Schlüssel generiert, sofern sie am Standardspeicherort für Schlüssel ( *~/.ssh*) nicht vorhanden sind:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Die Erstellung der VM kann einige Minuten dauern. Nach der Erstellung des virtuellen Computers gibt die Azure-Befehlszeilenschnittstelle Informationen zu dem virtuellen Computer aus. Notieren Sie sich den Wert für `publicIpAddress`. Über diese Adresse kann auf den virtuellen Computer zugegriffen werden. 

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM"
}
```

## <a name="connect-to-vm"></a>Herstellen einer Verbindung mit dem virtuellen Computer

Nun können Sie mittels SSH in Azure Cloud Shell oder von Ihrem lokalen Computer aus eine Verbindung mit der VM herstellen. Ersetzen Sie die Beispiel-IP-Adresse durch den Wert für `publicIpAddress`, den Sie sich im vorherigen Schritt notiert haben.

```bash
ssh azureuser@52.174.34.95
```

Nachdem Sie sich bei der VM angemeldet haben, können Sie Anwendungen installieren und konfigurieren. Wenn Sie fertig sind, schließen Sie wie gewohnt die SSH-Sitzung:

```bash
exit
```

## <a name="understand-vm-images"></a>Allgemeines zu VM-Images

Azure Marketplace bietet zahlreiche Images, die zum Erstellen virtueller Computer verwendet werden können. In den vorherigen Schritten wurde ein virtueller Computer mit einem Ubuntu-Image erstellt. In diesem Schritt wird der Marketplace mithilfe der Azure-Befehlszeilenschnittstelle nach einem CentOS-Image durchsucht, das anschließend zum Bereitstellen eines zweiten virtuellen Computers verwendet wird. 

Eine Liste mit den am häufigsten verwendeten Images erhalten Sie mithilfe des Befehls [az vm image list](/cli/azure/vm/image).

```azurecli-interactive 
az vm image list --output table
```

Der Befehl gibt die beliebtesten VM-Images in Azure zurück.

```bash
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

Eine vollständige Liste erhalten Sie, indem Sie das Argument `--all` hinzufügen. Die Imageliste kann auch nach `--publisher` oder `–-offer` gefiltert werden. In diesem Beispiel wird die Liste nach Images mit einem Angebot gefiltert, das *CentOS* entspricht. 

```azurecli-interactive 
az vm image list --offer CentOS --all --output table
```

Hier sehen Sie einen Teil der Ausgabe:

```azurecli-interactive 
Offer             Publisher         Sku   Urn                                     Version
----------------  ----------------  ----  --------------------------------------  -----------
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201501         6.5.201501
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201503         6.5.201503
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201506         6.5.201506
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20150904       6.5.20150904
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20160309       6.5.20160309
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20170207       6.5.20170207
```

Wenn Sie einen virtuellen Computer mit einem bestimmten Image bereitstellen möchten, notieren Sie sich den Wert aus der Spalte *Urn*. Dieser setzt sich zusammen aus dem Herausgeber, dem Angebot, der SKU und optional einer Versionsnummer zur [Identifizierung](cli-ps-findimage.md#terminology) des Images. Bei der Angabe des Images kann die Imageversionsnummer durch „latest“ ersetzt werden, um die neueste Version der Distribution auszuwählen. In diesem Beispiel wird mithilfe des Arguments `--image` die neueste Version eines CentOS 6.5-Images angeben.  

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM2 --image OpenLogic:CentOS:6.5:latest --generate-ssh-keys
```

## <a name="understand-vm-sizes"></a>Grundlegendes zu VM-Größen

Die Größe eines virtuellen Computers bestimmt die Menge an Computeressourcen (CPU, GPU, Arbeitsspeicher und Ähnliches), die für den virtuellen Computer zur Verfügung gestellt werden. Die Größe virtueller Computer muss auf die zu erwartende Workload abgestimmt werden. Bei einer Zunahme der Workload kann die Größe eines vorhandenen virtuellen Computers geändert werden.

### <a name="vm-sizes"></a>VM-Größen

In der folgenden Tabelle sind Größen in Anwendungsfällen kategorisiert.  

| Type                     | Größen           |    BESCHREIBUNG       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Allgemeiner Zweck](sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Ausgewogenes Verhältnis von CPU zu Arbeitsspeicher. Ideal für Entwicklung und Tests, kleine bis mittlere Anwendungen und Datenlösungen.  |
| [Computeoptimiert](sizes-compute.md)   | Fs, F             | Hohes Verhältnis von CPU zu Arbeitsspeicher. Geeignet für Anwendungen, Network Appliances und Batch-Prozesse mit mittlerer Auslastung.        |
| [Arbeitsspeicheroptimiert](../virtual-machines-windows-sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Hohes Verhältnis von Speicher zu Kern. Hervorragend geeignet für relationale Datenbanken, mittlere bis große Caches und In-Memory-Analysen.                 |
| [Speicheroptimiert](../virtual-machines-windows-sizes-storage.md)      | Ls                | Datenträgerdurchsatz und -E/A auf hohem Niveau. Ideal für Big Data sowie SQL- und NoSQL-Datenbanken.                                                         |
| [GPU](sizes-gpu.md)          | NV, NC            | Spezialisierte virtuelle Computer für aufwendiges Grafikrendering und aufwendige Videobearbeitung.       |
| [Hohe Leistung](sizes-hpc.md) | H, A8-11          | Unsere virtuellen Computer mit den leistungsfähigsten CPUs, die optional über Netzwerkschnittstellen mit hohem Durchsatz (RDMA) verfügen. 


### <a name="find-available-vm-sizes"></a>Ermitteln der verfügbaren VM-Größen

Eine Liste mit den verfügbaren VM-Größen einer bestimmten Region erhalten Sie mithilfe des Befehls [az vm list-sizes](/cli/azure/vm). 

```azurecli-interactive 
az vm list-sizes --location eastus --output table
```

Hier sehen Sie einen Teil der Ausgabe:

```azurecli-interactive 
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 2          3584  Standard_DS1                          1           1047552                    7168
                 4          7168  Standard_DS2                          2           1047552                   14336
                 8         14336  Standard_DS3                          4           1047552                   28672
                16         28672  Standard_DS4                          8           1047552                   57344
                 4         14336  Standard_DS11                         2           1047552                   28672
                 8         28672  Standard_DS12                         4           1047552                   57344
                16         57344  Standard_DS13                         8           1047552                  114688
                32        114688  Standard_DS14                        16           1047552                  229376
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
                 4          3584  Standard_A2                           2           1047552                  138240
                 8          7168  Standard_A3                           4           1047552                  291840
                 4         14336  Standard_A5                           2           1047552                  138240
                16         14336  Standard_A4                           8           1047552                  619520
                 8         28672  Standard_A6                           4           1047552                  291840
                16         57344  Standard_A7                           8           1047552                  619520
```

### <a name="create-vm-with-specific-size"></a>Erstellen eines virtuellen Computers mit einer bestimmten Größe

Im vorherigen Beispiel zur Erstellung eines virtuellen Computers wurde eine Standardgröße verwendet, da keine Größe angegeben wurde. Eine VM-Größe kann bei der Erstellung mit dem Befehl [az vm create](/cli/azure/vm) und dem Argument `--size` ausgewählt werden. 

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM3 \
    --image UbuntuLTS \
    --size Standard_F4s \
    --generate-ssh-keys
```

### <a name="resize-a-vm"></a>Ändern der Größe eines virtuellen Computers

Nach der Bereitstellung eines virtuellen Computers kann dessen Größe geändert werden, um die Ressourcenzuordnung zu erhöhen oder zu verringern. Mit dem Befehl [az vm show](/cli/azure/vm) können Sie die aktuelle Größe einer VM anzeigen:

```azurecli-interactive
az vm show --resource-group myResourceGroupVM --name myVM --query hardwareProfile.vmSize
```

Prüfen Sie vor der Größenänderung eines virtuellen Computers, ob die gewünschte Größe im aktuellen Azure-Cluster verfügbar ist. Der Befehl [az vm list-vm-resize-options](/cli/azure/vm) gibt die Größenliste zurück. 

```azurecli-interactive 
az vm list-vm-resize-options --resource-group myResourceGroupVM --name myVM --query [].name
```
Wenn die gewünschte Größe verfügbar ist, kann die Größe des virtuellen Computers im eingeschalteten Zustand geändert werden, er muss jedoch während des Vorgangs neu gestartet werden. Verwenden Sie zum Vornehmen der Größenänderung den Befehl [az vm resize]( /cli/azure/vm).

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_DS4_v2
```

Falls die gewünschte Größe im aktuellen Cluster nicht verfügbar ist, muss die Zuordnung des virtuellen Computers aufgehoben werden, damit die Größenänderung erfolgen kann. Verwenden Sie den Befehl [az vm deallocate]( /cli/azure/vm), um den virtuellen Computer zu beenden und die Zuordnung aufzuheben. Wenn der virtuelle Computer wieder eingeschaltet wird, werden unter Umständen sämtliche Daten auf dem temporären Datenträger entfernt. Die öffentliche IP-Adresse ändert sich ebenfalls – es sei denn, es wird eine statische IP-Adresse verwendet. 

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupVM --name myVM
```

Sobald die Zuordnung aufgehoben wurde, kann die Größe geändert werden. 

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_GS1
```

Nach dem Ändern der Größe kann der virtuelle Computer gestartet werden.

```azurecli-interactive 
az vm start --resource-group myResourceGroupVM --name myVM
```

## <a name="vm-power-states"></a>Betriebszustände von virtuellen Computern

Ein virtueller Azure-Computer kann einen von mehreren Betriebszuständen aufweisen. Dieser Zustand entspricht dem aktuellen Zustand des virtuellen Computers im Hypervisor. 

### <a name="power-states"></a>Betriebszustände

| Betriebszustand | BESCHREIBUNG
|----|----|
| Wird gestartet | Gibt an, dass der virtuelle Computer gestartet wird. |
| Wird ausgeführt | Gibt an, dass der virtuelle Computer ausgeführt wird. |
| Wird beendet | Gibt an, dass der virtuelle Computer beendet wird. | 
| Beendet | Gibt an, dass der virtuelle Computer beendet wurde. Für virtuelle Computer mit beendetem Zustand fallen weiterhin Computegebühren an.  |
| Zuordnung wird aufgehoben | Gibt an, dass die Zuordnung des virtuellen Computers aufgehoben wird. |
| Zuordnung aufgehoben | Gibt an, dass der virtuelle Computer aus dem Hypervisor entfernt wurde, auf der Steuerungsebene jedoch weiterhin verfügbar ist. Für virtuelle Computer mit aufgehobener Zuordnung fallen keine Computegebühren an. |
| - | Gibt an, dass der Betriebszustand des virtuellen Computers nicht bekannt ist. |

### <a name="find-the-power-state"></a>Ermitteln des Energiezustands

Verwenden Sie zum Abrufen des Zustands eines bestimmten virtuellen Computers den Befehl [az vm get-instance-view](/cli/azure/vm). Achten Sie darauf, dass Sie einen gültigen Namen für einen virtuellen Computer und eine Ressourcengruppe angeben. 

```azurecli-interactive 
az vm get-instance-view \
    --name myVM \
    --resource-group myResourceGroupVM \
    --query instanceView.statuses[1] --output table
```

Ausgabe:

```azurecli-interactive 
ode                DisplayStatus    Level
------------------  ---------------  -------
PowerState/running  VM running       Info
```

## <a name="management-tasks"></a>Verwaltungsaufgaben

Während der Lebensdauer eines virtuellen Computers können Sie Verwaltungsaufgaben wie das Starten, Beenden oder Löschen eines virtuellen Computers ausführen. Darüber hinaus empfiehlt es sich, Skripts zum Automatisieren von wiederkehrenden oder komplexen Aufgaben zu erstellen. Mithilfe der Azure CLI können viele allgemeine Verwaltungsaufgaben über die Befehlszeile oder in Skripts ausgeführt werden. 

### <a name="get-ip-address"></a>Abrufen der IP-Adresse

Dieser Befehl gibt die privaten und öffentlichen IP-Adressen eines virtuellen Computers zurück.  

```azurecli-interactive 
az vm list-ip-addresses --resource-group myResourceGroupVM --name myVM --output table
```

### <a name="stop-virtual-machine"></a>Beenden des virtuellen Computers

```azurecli-interactive 
az vm stop --resource-group myResourceGroupVM --name myVM
```

### <a name="start-virtual-machine"></a>Starten des virtuellen Computers

```azurecli-interactive 
az vm start --resource-group myResourceGroupVM --name myVM
```

### <a name="delete-resource-group"></a>Ressourcengruppe löschen

Beim Löschen einer Ressourcengruppe werden auch alle darin enthaltenen Ressourcen gelöscht, z.B. die VM, das virtuelle Netzwerk und der virtuelle Datenträger. Der Parameter `--no-wait` gibt die Steuerung an die Eingabeaufforderung zurück, ohne zu warten, bis der Vorgang abgeschlossen ist. Der Parameter `--yes` bestätigt ohne eine zusätzliche Aufforderung, dass Sie die Ressourcen löschen möchten.

```azurecli-interactive 
az group delete --name myResourceGroupVM --no-wait --yes
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Informationen zur grundlegenden Erstellung und Verwaltung von virtuellen Computern erhalten, darunter:

> [!div class="checklist"]
> * Erstellen eines virtuellen Computers und Herstellen einer Verbindung mit ihm
> * Auswählen und Verwenden von VM-Images
> * Anzeigen und Verwenden bestimmter VM-Größen
> * Ändern der Größe eines virtuellen Computers
> * Anzeigen und Verstehen des Status von virtuellen Computern

Im nächsten Tutorial erhalten Sie Informationen zu VM-Datenträgern.  

> [!div class="nextstepaction"]
> [Erstellen und Verwalten von VM-Datenträgern](./tutorial-manage-disks.md)
