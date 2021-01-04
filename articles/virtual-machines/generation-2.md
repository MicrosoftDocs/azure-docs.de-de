---
title: Azure-Support für VMs der Generation 2
description: 'Übersicht: Azure-Support für VMs der Generation 2'
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/28/2020
ms.author: jushiman
ms.openlocfilehash: 56a360f461c89d7c60e973bf2d6258d63113ab47
ms.sourcegitcommit: 6b16e7cc62b29968ad9f3a58f1ea5f0baa568f02
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/28/2020
ms.locfileid: "96303780"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Unterstützung für VMs der Generation 2 in Azure

Die Unterstützung für virtuelle Computer (VMs) der Generation 2 ist jetzt in Azure verfügbar. Sie können die Generation eines virtuellen Computers nach der Erstellung nicht mehr ändern, deshalb sollten Sie die Aspekte auf dieser Seite genauestens prüfen, bevor Sie eine Generation auswählen.

VMs der Generation 2 unterstützen wichtige Features, die bei VMs der Generation 1 nicht unterstützt werden. Zu diesen Features gehören mehr Speicher, Intel Software Guard Extensions (Intel SGX) und virtualisierter persistenter Speicher (vPMEM). VMs der Generation 2, die lokal ausgeführt werden, weisen auch einige Features auf, die in Azure noch nicht unterstützt werden. Weitere Informationen finden Sie im Abschnitt [Features und Funktionen](#features-and-capabilities).

VMs der Generation 2 verwenden die neue UEFI-basierte Startarchitektur und nicht mehr die BIOS-basierte Architektur von VMs der Generation 1. Im Vergleich zu VMs der Generation 1 können VMs der Generation 2 eine verbesserte Start- und Installationszeit aufweisen. Einen Überblick über VMs der Generation 2 und einige der Unterschiede zwischen Generation 1 und Generation 2 finden Sie unter [Sollte ich einen virtuellen Computer der Generation 1 oder 2 in Hyper-V erstellen?](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>VM-Größen der Generation 2

VMs der Generation 1 werden von allen VM-Größen in Azure unterstützt (Ausnahme: VMs der Mv2-Serie). Azure bietet jetzt Unterstützung für die Generation 2 der folgenden ausgewählten VM-Serien:


* [B-Serie](sizes-b-series-burstable.md)
* [DCsv2-Serie](dcv2-series.md)
* [DSv2-Serie](dv2-dsv2-series.md)
* [Dsv3-Serie](dv3-dsv3-series.md)
* [Dsv4-Serie](dv4-dsv4-series.md)
* [Dasv4-Serie](dav4-dasv4-series.md)
* [Ddsv4-Serie](ddv4-ddsv4-series.md)
* [Esv3-Serie](ev3-esv3-series.md)
* [Easv4-Serie](eav4-easv4-series.md)
* [Fsv2-Serie](fsv2-series.md)
* [GS-Serie](sizes-previous-gen.md#gs-series)
* [HB-Serie](hb-series.md)
* [HC-Serie](hc-series.md)
* [Ls-Serie](sizes-previous-gen.md#ls-series) 
* [Lsv2-Serie](lsv2-series.md)
* [M-Serie](m-series.md)
* [Mv2-Serie](mv2-series.md)<sup>1</sup>
* [NCv2-Serie](ncv2-series.md) 
* [NCv3-Serie](ncv3-series.md)
* [ND-Serie](nd-series.md)
* [NVv3-Serie](nvv3-series.md)
* [NVv4-Serie](nvv4-series.md)
* [NCasT4_v3-Serie](nct4-v3-series.md)

<sup>1</sup> Die Mv2-Serie unterstützt keine VM-Images der Generation 1 und nur eine Teilmenge von Images der Generation 2. Ausführliche Informationen finden Sie in der [Dokumentation zur Mv2-Serie](mv2-series.md).


## <a name="generation-2-vm-images-in-azure-marketplace"></a>VM-Images der Generation 2 in Azure Marketplace

VMs der Generation 2 unterstützen die folgenden Marketplace-Images:

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10 Pro, Windows 10 Enterprise
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16.04, 18.04, 19.04, 19.10 
* RHEL 8.1, 8.0, 7.7, 7.6, 7.5, 7.4, 7.0
* Cent OS 8.1, 8.0, 7.7, 7.6, 7.5, 7.4
* Oracle Linux 7.7, 7.7-CI

> [!NOTE]
> Bestimmte VM-Größen wie die Mv2-Serie unterstützen möglicherweise nur eine Teilmenge dieser Images. Vollständige Informationen finden Sie in der Dokumentation zur entsprechenden VM-Größe.

## <a name="on-premises-vs-azure-generation-2-vms"></a>Lokale VMs und Azure-VMs der Generation 2 im Vergleich

Azure unterstützt zurzeit einige der Funktionen nicht, die lokale Hyper-V-Instanzen für VMs der Generation 2 unterstützen.

| Funktionen der Generation 2                | Lokale Hyper-V-Instanz | Azure |
|-------------------------------------|---------------------|-------|
| Sicherer Start                         | :heavy_check_mark:  | :x:   |
| Abgeschirmte VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| Virtualisierungsbasierte Sicherheit (VBS) | :heavy_check_mark:  | :x:   |
| VHDX-Format                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Features und Funktionen

### <a name="generation-1-vs-generation-2-features"></a>Features der Generationen 1 und 2 im Vergleich

| Funktion | Generation 1 | Generation 2 |
|---------|--------------|--------------|
| Start             | PCAT                      | UEFI                               |
| Datenträgercontroller | IDE                       | SCSI                               |
| VM-Größen         | Alle VM-Größen | [Verfügbare Größen anzeigen](#generation-2-vm-sizes) |

### <a name="generation-1-vs-generation-2-capabilities"></a>Funktionen der Generationen 1 und 2 im Vergleich

| Funktion | Generation 1 | Generation 2 |
|------------|--------------|--------------|
| Betriebssystem-Datenträger > 2 TB                    | :x:                | :heavy_check_mark: |
| Benutzerdefinierter Datenträger/Image/Swapbetriebssystem         | :heavy_check_mark: | :heavy_check_mark: |
| Unterstützung für VM-Skalierungsgruppe | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Sichern/Wiederherstellen                    | :heavy_check_mark: | :heavy_check_mark: |
| Shared Image Gallery              | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)             | :heavy_check_mark: | :x:                |
| [Serverseitige Verschlüsselung](disk-encryption.md)            | :heavy_check_mark: | :heavy_check_mark: |

## <a name="creating-a-generation-2-vm"></a>Erstellen einer VM der Generation 2

### <a name="marketplace-image"></a>Marketplace-Image

VMs der Generation 2 können aus einem Marketplace-Image, das den UEFI-Start unterstützt, über das Azure-Portal oder die Azure-Befehlszeilenschnittstelle erstellt werden.

#### <a name="azure-portal"></a>Azure-Portal

Im Folgenden finden Sie die Schritte zum Erstellen eines virtuellen Computers der Generation 2 (Gen2) im Azure-Portal.

1. Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.
1. Wählen Sie **Ressource erstellen**.
1. Klicken Sie im Azure Marketplace links auf **Alle anzeigen**.
1. Wählen Sie ein Image aus, das Gen2 unterstützt.
1. Klicken Sie auf **Erstellen**.
1. Wählen Sie auf der Registerkarte **Erweitert** im Abschnitt **VM-Generation** die Option **Gen 2** aus.
1. Wechseln Sie auf der Registerkarte **Grundlagen** unter **Instanzdetails** zu **Größe**, und öffnen Sie das Blatt **VM-Größe auswählen**.
1. Wählen Sie eine [unterstützte VM der Generation 2](#generation-2-vm-sizes) aus.
1. Durchlaufen Sie die restlichen Seiten, um die Erstellung der VM abzuschließen.

![Virtuellen Computer der Gen 1 oder Gen 2 auswählen](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

Sie können auch PowerShell verwenden, um einen virtuellen Computer zu erstellen, indem Sie direkt auf die SKU der Generation 1 oder der Generation 2 verweisen.

Verwenden Sie beispielsweise das folgende PowerShell-Cmdlet, um eine Liste der SKUs im `WindowsServer`-Angebot abzurufen.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Wenn Sie einen virtuellen Computer mit Windows Server 2012 als Betriebssystem erstellen, wählen Sie entweder die VM-SKU der Generation 1 (BIOS) oder der Generation 2 (UEFI) aus, die wie folgt aussieht:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Im Abschnitt [Features und Funktionen](#features-and-capabilities) finden Sie eine aktuelle Liste der unterstützten Marketplace-Images.

#### <a name="azure-cli"></a>Azure CLI

Alternativ können Sie über die Azure-Befehlszeilenschnittstelle alle verfügbaren Images der Generation 2 anzeigen, die von **Publisher** aufgelistet werden.

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

### <a name="managed-image-or-managed-disk"></a>Verwaltetes Image oder verwalteter Datenträger

Sie können VMs der Generation 2 aus einem verwalteten Image oder einem verwalteten Datenträger auf die gleiche Weise erstellen, wie Sie eine VM der Generation 1 erstellen würden.

### <a name="virtual-machine-scale-sets"></a>VM-Skalierungsgruppen

VMs der Generation 2 können auch mithilfe von VM-Skalierungsgruppen erstellt werden. Verwenden Sie an der Azure-Befehlszeilenschnittstelle Azure-Skalierungsgruppen, um VMs der Generation 2 zu erstellen.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

* **Sind VMs der Generation 2 in allen Azure-Regionen verfügbar?**  
    Ja. Allerdings stehen nicht alle [VM-Größen der Generation 2](#generation-2-vm-sizes) in allen Regionen zur Verfügung. Die Verfügbarkeit von VMs der Generation 2 richtet sich nach der Verfügbarkeit der VM-Größe.

* **Gibt es einen Preisunterschied zwischen VMs der 1. und 2. Generation?**  
   Nein.

* **Ich verfüge über eine VHD-Datei aus meiner lokalen VM der Generation 2. Kann ich die VHD-Datei verwenden, um einen virtuellen Computer der Generation 2 in Azure zu erstellen?**
  Ja, Sie können die VHD-Datei der Generation 2 in Azure einbringen und zum Erstellen eines virtuellen Computers der Generation 2 verwenden. Führen Sie dazu die folgenden Schritte aus:
    1. Laden Sie die VHD-Datei in ein Speicherkonto in derselben Region hoch, in der Sie Ihre VM erstellen möchten.
    1. Erstellen Sie einen verwalteten Datenträger aus der VHD-Datei. Legen Sie die Eigenschaft „Hyper-V-Generation“ auf „V2“ fest. Die folgenden PowerShell-Befehle legen die Eigenschaft „Hyper-V-Generation“ beim Erstellen eines verwalteten Datenträgers fest.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Nachdem der Datenträger verfügbar ist, erstellen Sie einen virtuellen Computer durch Anfügen dieses Datenträgers. Der erstellte virtuelle Computer ist eine VM der Generation 2.
    Wenn der virtuelle Computer der Generation 2 erstellt ist, können Sie optional das Image dieser VM verallgemeinern. Wenn Sie das Image verallgemeinern, können Sie es verwenden, um mehrere VMs zu erstellen.

* **Wie erhöhe ich die Größe des Betriebssystemdatenträgers?**  

  Betriebssystemdatenträger mit einer Größe von mehr als 2 TiB sind neu bei VMs der Generation 2. Standardmäßig sind Betriebssystemdatenträger für VMs der Generation 2 kleiner als 2 TiB. Sie können die Größe des Datenträgers bis zu einer empfohlenen maximalen Größe von 4 TiB erhöhen. Verwenden Sie zum Erhöhen der Größe von Betriebssystemdatenträgern die Azure-Befehlszeilenschnittstelle oder das Azure-Portal. Weitere Informationen zum programmgesteuerten Erweitern von Datenträgern finden Sie unter **Ändern der Größe eines Datenträgers** für [Windows](./windows/expand-os-disk.md) bzw. [Linux](./linux/resize-os-disk-gpt-partition.md).

  So erhöhen Sie die Größe für Betriebssystemdatenträger über das Azure-Portal:

  1. Navigieren Sie im Azure-Portal zur Seite mit den VM-Eigenschaften.
  1. Verwenden Sie die Schaltfläche **Beenden**, um die VM herunterzufahren und ihre Zuordnung aufzuheben.
  1. Wählen Sie im Abschnitt **Datenträger** den Betriebssystemdatenträger aus, den Sie vergrößern möchten.
  1. Wählen Sie im Abschnitt **Datenträger** die Option **Konfiguration** aus, und ändern Sie die **Größe** auf den gewünschten Wert.
  1. Navigieren Sie zurück zur Seite mit den Eigenschaften der VM, und **starten** Sie die VM.
  
  Für Betriebssystemdatenträger mit einer Größe von mehr als 2 TiB wird möglicherweise eine Warnmeldung angezeigt. Die Warnung gilt nicht für VMs der Generation 2. Größen für Betriebssystemdatenträger von mehr als 4 TiB werden jedoch nicht unterstützt.

* **Unterstützen VMs der Generation 2 den beschleunigten Netzwerkbetrieb?**  
    Ja. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Computers mit beschleunigtem Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md).

* **Wird von virtuellen Computern der zweiten Generation der sichere Start oder vTPM in Azure unterstützt?**
    Sicherer Start und vTPM werden weder von virtuellen Computern der ersten Generation noch von virtuellen Computern der zweiten Generation unterstützt. 
    
* **Wird VHDX für Generation 2 unterstützt?**  
    Nein, VMs der Generation 2 unterstützen nur VHD.

* **Wird für VMs der Generation 2 Azure Disk Storage Ultra unterstützt?**  
    Ja.

* **Kann ich VMs der Generation 1 zu Generation 2 migrieren?**  
    Nein, Sie können die Generation eines virtuellen Computers nicht mehr ändern, nachdem Sie ihn erstellt haben. Wenn Sie zwischen VM-Generationen wechseln möchten, müssen Sie einen neuen virtuellen Computer einer anderen Generation erstellen.

* **Warum ist meine VM-Größe in der Größenauswahl nicht aktiviert, wenn ich versuche, eine Gen2-VM zu erstellen?**

    Dies kann eventuell wie folgt gelöst werden:

    1. Vergewissern Sie sich, dass die Eigenschaft **VM-Generation** auf der Registerkarte **Erweitert** auf **Gen 2** festgelegt ist.
    1. Vergewissern Sie sich, dass Sie nach einer [VM-Größe suchen, die Gen2-VMs unterstützt](#generation-2-vm-sizes).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über [virtuelle Computer der Generation 2 in Hyper-V](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
