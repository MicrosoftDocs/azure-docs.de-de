---
title: 'Tutorial: Erstellen und Verwalten einer VM-Skalierungsgruppe – Azure CLI'
description: Hier erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle eine VM-Skalierungsgruppe erstellen und einige allgemeine Verwaltungsaufgaben ausführen, um beispielsweise eine Instanz zu starten und zu beenden oder die Kapazität der Skalierungsgruppe zu ändern.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 0f94823b958ae5f95789dd4ef9a62057bdf764a8
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517460"
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Tutorial: Erstellen und Verwalten einer VM-Skalierungsgruppe mit der Azure-Befehlszeilenschnittstelle
Mit einer VM-Skalierungsgruppe können Sie eine Gruppe identischer, automatisch skalierender virtueller Computer bereitstellen und verwalten. Während des Lebenszyklus einer Skalierungsgruppe müssen unter Umständen verschiedene Verwaltungsaufgaben durchgeführt werden. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer VM-Skalierungsgruppe und Herstellen einer Verbindung
> * Auswählen und Verwenden von VM-Images
> * Anzeigen und Verwenden bestimmter VM-Instanzgrößen
> * Manuelles Skalieren einer Skalierungsgruppe
> * Ausführen allgemeiner Verwaltungsaufgaben für Skalierungsgruppen

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.29 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 


## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Vor der VM-Skalierungsgruppe muss zunächst eine Ressourcengruppe erstellt werden. Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group) eine Ressourcengruppe. In diesem Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* in der Region *eastus* erstellt. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Der Ressourcengruppenname wird im gesamten Tutorial beim Erstellen oder Ändern einer Skalierungsgruppe angegeben.


## <a name="create-a-scale-set"></a>Erstellen einer Skalierungsgruppe
Verwenden Sie zum Erstellen einer VM-Skalierungsgruppe den Befehl [az vmss create](/cli/azure/vmss). Im folgenden Beispiel wird eine Skalierungsgruppe mit dem Namen *myScaleSet* erstellt, und es werden SSH-Schlüssel generiert, falls sie noch nicht vorhanden sind:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Die Erstellung und Konfiguration aller Ressourcen und VM-Instanzen der Skalierungsgruppe dauert einige Minuten. Um Datenverkehr an die einzelnen VM-Instanzen zu verteilen, wird auch ein Lastenausgleich erstellt.


## <a name="view-the-vm-instances-in-a-scale-set"></a>Anzeigen der VM-Instanzen in einer Skalierungsgruppe
Verwenden Sie [az vmss list-instances](/cli/azure/vmss), um eine Liste mit den VM-Instanzen in einer Skalierungsgruppe anzuzeigen:

```azurecli-interactive
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

Die folgende Beispielausgabe zeigt zwei VM-Instanzen in der Skalierungsgruppe:

```output
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  c059be0c-37a2-497a-b111-41272641533c
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUP  ec19e7a7-a4cd-4b24-9670-438f4876c1f9
```


Die erste Spalte der Ausgabe enthält eine Instanz-ID (*InstanceId*). Wenn Sie zusätzliche Informationen zu einer bestimmten VM-Instanz anzeigen möchten, fügen Sie [az vmss get-instance-view](/cli/azure/vmss) den Parameter `--instance-id` hinzu. Im folgenden Beispiel werden Informationen zur VM-Instanz *1* angezeigt:

```azurecli-interactive
az vmss get-instance-view \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --instance-id 1
```


## <a name="list-connection-information"></a>Auflisten von Verbindungsinformationen
Dem Lastenausgleich, der Datenverkehr an die einzelnen VM-Instanzen weiterleitet, wird eine öffentliche IP-Adresse zugewiesen. Standardmäßig werden dem Azure-Lastenausgleich, der den Datenverkehr von Remoteverbindungen über einen bestimmten Port an die einzelnen virtuellen Computer weiterleitet, Regeln für die Netzwerkadressübersetzung (Network Address Translation, NAT) hinzugefügt. Wenn Sie eine Verbindung mit den VM-Instanzen in einer Skalierungsgruppe herstellen möchten, erstellen Sie eine Remoteverbindung mit einer zugewiesenen öffentlichen IP-Adresse und Portnummer.

Verwenden Sie [az vmss list-instance-connection-info](/cli/azure/vmss), um die Adresse und die Ports für die Verbindungsherstellung mit VM-Instanzen in einer Skalierungsgruppe aufzulisten:

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

Die folgende Beispielausgabe enthält den Instanznamen, die öffentliche IP-Adresse des Lastenausgleichs und die Portnummer, an die Datenverkehr von den NAT-Regeln weitergeleitet wird:

```output
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

Stellen Sie eine SSH-Verbindung mit Ihrer ersten VM-Instanz her. Geben Sie mithilfe des Parameters `-p` Ihre öffentliche IP-Adresse und die Portnummer an, wie im vorherigen Befehl gezeigt:

```console
ssh azureuser@13.92.224.66 -p 50001
```

Nach der Anmeldung bei der VM-Instanz können Sie nach Bedarf einige manuelle Konfigurationsänderungen vornehmen. Schließen Sie die SSH-Sitzung vorerst ganz normal:

```console
exit
```


## <a name="understand-vm-instance-images"></a>Grundlegendes zu VM-Instanzimages
Bei der Erstellung der Skalierungsgruppe zu Beginn des Tutorials wurde als `--image` für die VM-Instanzen *UbuntuLTS* angegeben. Im Azure Marketplace stehen zahlreiche Images zur Verfügung, die Sie zum Erstellen von VM-Instanzen verwenden können. Eine Liste mit den am häufigsten verwendeten Images erhalten Sie mithilfe des Befehls [az vm image list](/cli/azure/vm/image).

```azurecli-interactive
az vm image list --output table
```

Die folgende Beispielausgabe zeigt die gängigsten VM-Images in Azure. Mit *UrnAlias* können Sie bei der Erstellung einer Skalierungsgruppe eines dieser gängigen Images angeben.

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

Eine vollständige Liste erhalten Sie durch Hinzufügen des Arguments `--all`. Die Imageliste kann auch nach `--publisher` oder `–-offer` gefiltert werden. Im folgenden Beispiel wird die Liste nach Images mit einem Angebot gefiltert, das *CentOS* entspricht:

```azurecli-interactive
az vm image list --offer CentOS --all --output table
```

Die folgende verkürzte Ausgabe zeigt einige der verfügbaren CentOS 7.3-Images:

```output
Offer    Publisher   Sku   Urn                                 Version
-------  ----------  ----  ----------------------------------  -------------
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20161221   7.3.20161221
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170421   7.3.20170421
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170517   7.3.20170517
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170612   7.3.20170612
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170707   7.3.20170707
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170925   7.3.20170925
```

Wenn Sie eine Skalierungsgruppe mit einem bestimmten Image bereitstellen möchten, verwenden Sie den Wert aus der Spalte *Urn*. Wenn Sie das Image angeben, können Sie die Imageversionsnummer durch *latest* ersetzen, um die neueste Version der Distribution auszuwählen. Im folgenden Beispiel wird mithilfe des Arguments `--image` die neueste Version eines CentOS 7.3-Images angegeben.

> [!IMPORTANT]
> Wir empfehlen die Verwendung der *neuesten* Imageversion. Geben Sie „latest“ an, damit Sie die neueste Version eines Images verwenden können, das zum Zeitpunkt der Bereitstellung verfügbar ist. Bitte beachten Sie: Sollten Sie „latest“ verwenden, wird das VM-Image nach diesem Zeitpunkt selbst dann nicht automatisch aktualisiert, wenn eine neue Version verfügbar wird.

Da die Erstellung und Konfiguration aller Ressourcen und VM-Instanzen der Skalierungsgruppe einige Minuten dauert, müssen Sie die folgende Skalierungsgruppe nicht bereitstellen:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetCentOS \
  --image OpenLogic:CentOS:7.3:latest \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="understand-vm-instance-sizes"></a>Grundlegendes zu VM-Instanzgrößen
Eine VM-Instanzgröße (oder *SKU*) bestimmt die Menge an Computeressourcen (CPU, GPU, Arbeitsspeicher und Ähnliches), die für die VM-Instanz zur Verfügung stehen. Die Größe der VM-Instanzen in einer Skalierungsgruppe muss auf die zu erwartende Workload abgestimmt werden.

### <a name="vm-instance-sizes"></a>VM-Instanzgrößen
In der folgenden Tabelle sind gängige VM-Größen nach Anwendungsfall kategorisiert:

| type                     | Gängige Größen           |    BESCHREIBUNG       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Allgemeiner Zweck](../virtual-machines/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Ausgewogenes Verhältnis von CPU zu Arbeitsspeicher. Ideal für Entwicklung und Tests, kleine bis mittlere Anwendungen und Datenlösungen.  |
| [Computeoptimiert](../virtual-machines/sizes-compute.md)   | Fs, F             | Hohes Verhältnis von CPU zu Arbeitsspeicher. Geeignet für Anwendungen, Network Appliances und Batch-Prozesse mit mittlerer Auslastung.        |
| [Arbeitsspeicheroptimiert](../virtual-machines/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Hohes Verhältnis von Speicher zu Kern. Hervorragend geeignet für relationale Datenbanken, mittlere bis große Caches und In-Memory-Analysen.                 |
| [Speicheroptimiert](../virtual-machines/sizes-storage.md)      | Ls                | Datenträgerdurchsatz und -E/A auf hohem Niveau. Ideal für Big Data sowie SQL- und NoSQL-Datenbanken.                                                         |
| [GPU](../virtual-machines/sizes-gpu.md)          | NV, NC            | Spezialisierte virtuelle Computer für aufwendiges Grafikrendering und aufwendige Videobearbeitung.       |
| [Hohe Leistung](../virtual-machines/sizes-hpc.md) | H, A8-11          | Unsere virtuellen Computer mit den leistungsfähigsten CPUs, die optional über Netzwerkschnittstellen mit hohem Durchsatz (RDMA) verfügen. 

### <a name="find-available-vm-instance-sizes"></a>Suchen nach verfügbaren VM-Instanzgrößen
Eine Liste mit verfügbaren VM-Instanzgrößen einer bestimmten Region erhalten Sie mithilfe des Befehls [az vm list-sizes](/cli/azure/vm).

```azurecli-interactive
az vm list-sizes --location eastus --output table
```

Die Ausgabe ähnelt dem folgenden verkürzten Beispiel, das die Ressourcen zeigt, die den einzelnen VM-Größen zugewiesen sind:

```output
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 4          3584  Standard_DS1_v2                       1           1047552                    7168
                 8          7168  Standard_DS2_v2                       2           1047552                   14336
[...]
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
[...]
                 4          2048  Standard_F1                           1           1047552                   16384
                 8          4096  Standard_F2                           2           1047552                   32768
[...]
                24         57344  Standard_NV6                          6           1047552                   38912
                48        114688  Standard_NV12                        12           1047552                  696320
```

### <a name="create-a-scale-set-with-a-specific-vm-instance-size"></a>Erstellen einer Skalierungsgruppe mit einer bestimmten VM-Instanzgröße
Bei der Erstellung der Skalierungsgruppe zu Beginn des Tutorials wurde für die VM-Instanzen die VM-Standard-SKU *Standard_D1_v2* angegeben. Auf der Grundlage der Ausgabe von [az vm list-sizes](/cli/azure/vm) können Sie eine andere VM-Instanzgröße angeben. Im folgenden Beispiel wird eine Skalierungsgruppe mit dem Parameter `--vm-sku` erstellt, um die VM-Instanzgröße *Standard_F1* anzugeben. Da die Erstellung und Konfiguration aller Ressourcen und VM-Instanzen der Skalierungsgruppe einige Minuten dauert, müssen Sie die folgende Skalierungsgruppe nicht bereitstellen:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetF1Sku \
  --image UbuntuLTS \
  --vm-sku Standard_F1 \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="change-the-capacity-of-a-scale-set"></a>Ändern der Kapazität einer Skalierungsgruppe
Bei der Erstellung der Skalierungsgruppe zu Beginn des Tutorials wurden standardmäßig zwei VM-Instanzen bereitgestellt. Sie können [az vmss create](/cli/azure/vmss) zusammen mit dem Parameter `--instance-count` angeben, um die Anzahl von Instanzen zu ändern, die mit einer Skalierungsgruppe erstellt werden. Wenn Sie die Anzahl von VM-Instanzen in Ihrer bereits vorhandenen Skalierungsgruppe erhöhen oder verringern möchten, können Sie manuell die Kapazität ändern. Die Skalierungsgruppe erstellt oder entfernt die erforderliche Anzahl von VM-Instanzen und konfiguriert anschließend den Lastenausgleich für die Verteilung des Datenverkehrs.

Die Anzahl von VM-Instanzen in der Skalierungsgruppe kann mit [az vmss scale](/cli/azure/vmss) manuell erhöht oder verringert werden. Im folgenden Beispiel wird die Anzahl von VM-Instanzen in der Skalierungsgruppe auf *3* festgelegt:

```azurecli-interactive
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 3
```

Die Aktualisierung der Skalierungsgruppenkapazität dauert ein paar Minuten. Verwenden Sie [az vmss show](/cli/azure/vmss), und fragen Sie *sku.capacity* ab, um die Anzahl von Instanzen anzuzeigen, die momentan in der Skalierungsgruppe enthalten sind:

```azurecli-interactive
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```


## <a name="common-management-tasks"></a>Allgemeine Verwaltungsaufgaben
Sie können nun eine Skalierungsgruppe erstellen, Verbindungsinformationen auflisten und eine Verbindung mit VM-Instanzen herstellen. Sie haben gelernt, wie Sie ein anderes Betriebssystemimage für Ihre VM-Instanzen verwenden, eine andere VM-Größe auswählen oder manuell die Instanzenanzahl skalieren. Im Rahmen der alltäglichen Verwaltung kann es vorkommen, dass Sie die VM-Instanzen in der Skalierungsgruppe beenden, starten oder neu starten müssen.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>Beenden von VM-Instanzen in einer Skalierungsgruppe und Aufheben ihrer Zuordnung
Verwenden Sie [az vmss stop](/cli/azure/vmss), um VM-Instanzen in einer Skalierungsgruppe zu beenden. Mit dem Parameter `--instance-ids` können Sie die zu beendenden VM-Instanzen angeben. Wenn Sie keine Instanz-ID angeben, werden alle VM-Instanzen in der Skalierungsgruppe beendet. Im folgenden Beispiel wird die Instanz *1* beendet:

```azurecli-interactive
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

Beendete VM-Instanzen bleiben weiter zugeordnet, und es fallen weiterhin Computegebühren für sie an. Wenn Sie stattdessen die Zuordnung der VM-Instanzen aufheben möchten, sodass nur noch Speichergebühren anfallen, verwenden Sie [az vmss deallocate](/cli/azure/vmss). Im folgenden Beispiel wird die Instanz *1* beendet und ihre Zuordnung aufgehoben:

```azurecli-interactive
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="start-vm-instances-in-a-scale-set"></a>Starten von VM-Instanzen in einer Skalierungsgruppe
Verwenden Sie [az vmss start](/cli/azure/vmss), um VM-Instanzen in einer Skalierungsgruppe zu starten. Mit dem Parameter `--instance-ids` können Sie die zu startenden VM-Instanzen angeben. Wenn Sie keine Instanz-ID angeben, werden alle VM-Instanzen in der Skalierungsgruppe gestartet. Im folgenden Beispiel wird die Instanz *1* gestartet:

```azurecli-interactive
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="restart-vm-instances-in-a-scale-set"></a>Neustarten von VM-Instanzen in einer Skalierungsgruppe
Verwenden Sie [az vmss restart](/cli/azure/vmss), um VM-Instanzen in einer Skalierungsgruppe neu zu starten. Mit dem Parameter `--instance-ids` können Sie die neu zu startenden VM-Instanzen angeben. Wenn Sie keine Instanz-ID angeben, werden alle VM-Instanzen in der Skalierungsgruppe neu gestartet. Im folgenden Beispiel wird die Instanz *1* neu gestartet:

```azurecli-interactive
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie eine Ressourcengruppe löschen, werden auch alle darin enthaltenen Ressourcen (wie VM-Instanzen, das virtuelle Netzwerk und die Datenträger) gelöscht. Der Parameter `--no-wait` gibt die Steuerung an die Eingabeaufforderung zurück, ohne zu warten, bis der Vorgang abgeschlossen ist. Der Parameter `--yes` bestätigt ohne eine zusätzliche Aufforderung, dass Sie die Ressourcen löschen möchten.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie gelernt, wie Sie mit der Azure-Befehlszeilenschnittstelle einige grundlegende Erstellungs- und Verwaltungsaufgaben für Skalierungsgruppen ausführen:

> [!div class="checklist"]
> * Erstellen einer VM-Skalierungsgruppe und Herstellen einer Verbindung
> * Auswählen und Verwenden von VM-Images
> * Anzeigen und Verwenden bestimmter VM-Größen
> * Manuelles Skalieren einer Skalierungsgruppe
> * Ausführen allgemeiner Verwaltungsaufgaben für Skalierungsgruppen

Im nächsten Tutorial erhalten Sie Informationen zu Datenträgern für Skalierungsgruppen.

> [!div class="nextstepaction"]
> [Verwenden von Datenträgern mit Skalierungsgruppen](tutorial-use-disks-cli.md)
