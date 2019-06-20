---
title: include file
description: include file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/10/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7515c061467419412608bb8103136791845ae093
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133170"
---
# <a name="enable-and-deploy-azure-ultra-ssds-preview"></a>Aktivieren und Bereitstellen von SSD Ultra-Azure-Datenträgern (Vorschauversion)

SSD Ultra-Azure-Datenträger (Solid State Drive) (Vorschauversion) verfügen über hohen Durchsatz, einen hohen IOPS-Wert und einen einheitlichen Datenträgerspeicher mit geringer Latenz für virtuelle Azure IaaS-Computer (VMs). Dieses neue Angebot bietet Spitzenleistung auf den gleichen Verfügbarkeitsebenen wie unsere vorhandenen Datenträgerangebote. Ein Hauptvorteil von SSD Ultra-Datenträgern ist die Möglichkeit zum dynamischen Ändern der SSD-Leistung zusammen mit Ihren Workloads, ohne dass Sie Ihre VMs neu starten müssen. SSD Ultra eignet sich für datenintensive Workloads wie SAP HANA, führende Datenbanken und Workloads mit vielen Transaktionen.

Aktuell befindet sich SSD Ultra in der Vorschauphase, und Sie müssen sich für die Vorschauversion [registrieren](https://aka.ms/UltraSSDPreviewSignUp), um darauf zuzugreifen.

## <a name="determine-your-availability-zone"></a>Ermitteln Ihrer Verfügbarkeitszone

Nach dem Erhalt der Genehmigung müssen Sie ermitteln, in welcher Verfügbarkeitszone Sie sich befinden, um SSD Ultra zu verwenden. Führen Sie einen der folgenden Befehle aus, um zu bestimmen, in welcher Zone in „USA, Osten 2“ Ihr SSD Ultra-Datenträger bereitgestellt werden soll:

PowerShell: `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

CLI: `az vm list-skus --resource-type disks --query "[?name=='UltraSSD_LRS'].locationInfo"`

Die Antwort ähnelt dem unten gezeigten Formular, wobei X für die Zone steht, die für die Bereitstellung in „USA, Osten 2“ verwendet werden soll. X kann entweder 1, 2 oder 3 sein.

Behalten Sie den Wert für **Zonen** bei. Er steht für Ihre Verfügbarkeitszone, und Sie benötigen ihn zum Bereitstellen eines SSD Ultra-Datenträgers.

|ResourceType  |NAME  |Location  |Zones  |Einschränkung  |Funktion  |Wert  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Wenn der Befehl keine Antwort zurückgegeben hat, steht Ihre Registrierung beim Feature entweder noch aus oder ist noch nicht genehmigt.

Nachdem Sie nun wissen, in welcher Zone bereitgestellt werden soll, können Sie die Bereitstellungsschritte in diesem Artikel ausführen, um Ihre ersten virtuellen Computer mit SSD Ultra bereitzustellen.

## <a name="deploy-an-ultra-ssd-using-azure-resource-manager"></a>Bereitstellen eines SSD Ultra-Datenträgers mit Azure Resource Manager

Bestimmen Sie zuerst die Größe des bereitzustellenden virtuellen Computers. Im Rahmen dieser Preview werden nur VMs der Familien DsV3 und EsV3 unterstützt. Zusätzliche Details zu diesen VM-Größen finden Sie in der zweiten Tabelle in diesem [Blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

Wenn Sie eine VM mit mehreren SSD Ultra-Datenträgern erstellen möchten, hilft Ihnen das Beispiel zum [Erstellen einer VM mit mehreren SSD Ultra-Datenträgern](https://aka.ms/UltraSSDTemplate) weiter.

Falls Sie Ihre eigene Vorlage verwenden möchten, sollten Sie sicherstellen, dass **apiVersion** für `Microsoft.Compute/virtualMachines` und `Microsoft.Compute/Disks` auf `2018-06-01` (oder höher) festgelegt ist.

Legen Sie die Datenträger-SKU auf **UltraSSD_LRS** fest, und geben Sie anschließend die Datenträgerkapazität, den IOPS-Wert, die Verfügbarkeitszone und den Durchsatz in MBit/s an, um einen Ultra-Datenträger zu erstellen.

Nachdem der virtuelle Computer bereitgestellt wurde, können Sie die Datenträger für Daten partitionieren und formatieren sowie für Ihre Workloads konfigurieren.

## <a name="deploy-an-ultra-ssd-using-cli"></a>Bereitstellen eines SSD Ultra-Datenträgers per CLI

Bestimmen Sie zuerst die Größe des bereitzustellenden virtuellen Computers. Im Rahmen dieser Preview werden nur VMs der Familien DsV3 und EsV3 unterstützt. Zusätzliche Details zu diesen VM-Größen finden Sie in der zweiten Tabelle in diesem [Blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

Zum Verwenden von SSD Ultra-Datenträgern müssen Sie eine VM erstellen, für die diese Art von Datenträgern genutzt werden kann.

Ersetzen Sie die Variablen **$vmname**, **$rgname**, **$diskname**, **$location**, **$password** und **$user** durch Ihre eigenen Werte, bzw. legen Sie sie fest. Legen Sie **$zone** auf den Wert Ihrer Verfügbarkeitszone fest, den Sie am [Anfang dieses Artikels](#determine-your-availability-zone) ermittelt haben. Führen Sie anschließend den folgenden CLI-Befehl aus, um eine Ultra-fähige VM zu erstellen:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled --zone $zone --authentication-type password --admin-password $password --admin-username $user --attach-data-disks $diskname --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-ssd-using-cli"></a>Erstellen eines SSD Ultra-Datenträgers per CLI

Nachdem Sie nun über eine VM verfügen, für die SSD Ultra-Datenträger genutzt werden können, können Sie einen entsprechenden Datenträger erstellen und anfügen.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an Ultra SSD disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

### <a name="adjust-the-performance-of-an-ultra-ssd-using-cli"></a>Anpassen der Leistung eines SSD Ultra-Datenträgers per CLI

SSD Ultra-Datenträger verfügen über eine einzigartige Funktion, mit der Sie die Leistung anpassen können. Mit dem folgenden Befehl wird die Verwendung dieses Features veranschaulicht:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-ssd-using-powershell"></a>Bereitstellen eines SSD Ultra-Datenträgers mit PowerShell

Bestimmen Sie zuerst die Größe des bereitzustellenden virtuellen Computers. Im Rahmen dieser Preview werden nur VMs der Familien DsV3 und EsV3 unterstützt. Zusätzliche Details zu diesen VM-Größen finden Sie in der zweiten Tabelle in diesem [Blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

Zum Verwenden von SSD Ultra-Datenträgern müssen Sie eine VM erstellen, für die diese Art von Datenträgern genutzt werden kann. Ersetzen Sie die Variablen **$resourcegroup** und **$vmName** durch Ihre eigenen Werte. Legen Sie **$zone** auf den Wert Ihrer Verfügbarkeitszone fest, den Sie am [Anfang dieses Artikels](#determine-your-availability-zone) ermittelt haben. Führen Sie anschließend den folgenden [New-AzVm](/powershell/module/az.compute/new-azvm)-Befehl aus, um eine Ultra-fähige VM zu erstellen:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-an-ultra-ssd-using-powershell"></a>Erstellen eines SSD Ultra-Datenträgers mit PowerShell

Nachdem Sie nun über eine VM verfügen, für die SSD Ultra-Datenträger genutzt werden können, können Sie einen entsprechenden Datenträger erstellen und anfügen:

```powershell
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName 'Disk02' `
-Disk $diskconfig;
```

### <a name="adjust-the-performance-of-an-ultra-ssd-using-powershell"></a>Anpassen der Leistung eines SSD Ultra-Datenträgers mit PowerShell

SSD Ultra-Datenträger verfügen über eine einzigartige Funktion, mit der Sie die Leistung anpassen können. Der folgende Befehl ist ein Beispiel für die Anpassung der Leistung, ohne dass der Datenträger getrennt werden muss:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>Nächste Schritte

Falls Sie den neuen Datenträgertyp ausprobieren möchten, müssen Sie den [Zugriff auf die Vorschauversion über diese Umfrage anfordern](https://aka.ms/UltraSSDPreviewSignUp).