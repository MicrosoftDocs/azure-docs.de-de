---
title: VMs der Generation 2 (Vorschau) in Azure | Microsoft-Dokumentation
description: 'Übersicht: Azure-VMs der Generation 2'
services: virtual-machines-linux
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: lahugh
ms.openlocfilehash: e6bb947503371e379e4d4972ddfc3614e129174b
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835209"
---
# <a name="generation-2-vms-preview-on-azure"></a>VMs der Generation 2 (Vorschau) in Azure

> [!IMPORTANT]
> VMs der Generation 2 sind zurzeit als öffentliche Vorschau verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Unterstützung für virtuelle Computer (VMs) der Generation 2 ist jetzt als öffentliche Vorschau in Azure verfügbar. Die Generation eines virtuellen Computers kann nach seiner Erstellung nicht mehr geändert werden. Wir empfehlen Ihnen daher, die Überlegungen [hier](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) sowie die Informationen auf dieser Seite zu überprüfen, bevor Sie eine Generation auswählen.

VMs der Generation 2 unterstützen zahlreiche Schlüsselfunktionen (etwa mehr Speicher, Intel® Software Guard Extensions (SGX) und Virtual Persistent Memory (vPMEM)), die bei VMs der Generation 1 nicht unterstützt werden. VMs der Generation 2 haben einige Funktionen, die in Azure noch nicht unterstützt werden. Weitere Informationen finden Sie im Abschnitt [Features und Funktionen](#features-and-capabilities). 

VMs der Generation 2 verwenden die neue UEFI-basierte Startarchitektur im Vergleich zur BIOS-basierten Architektur der VMs der Generation 1. Im Vergleich zu VMs der Generation 1 können VMs der Generation 2 eine verbesserte Start- und Installationszeit aufweisen. Für einen Überblick über VMs der Generation 2 und einige der wichtigsten Unterschiede zwischen Generation 1 und Generation 2 finden Sie unter [Sollte ich einen virtuellen Computer der Generation 1 oder 2 in Hyper-V erstellen?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="generation-2-vm-sizes"></a>VM-Größen der Generation 2

VMs der Generation 1 werden von allen VM-Größen in Azure unterstützt. Azure bietet jetzt Unterstützung für Generation 2 für die folgenden ausgewählten VM-Serien in der öffentlichen Vorschau:

* [Dsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series)- und [Dsv3-Serie](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Esv3-Serie](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Fsv2-Serie](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [GS-Serie](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#gs-series)
* [Ls-Serie](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) und [Lsv2-Serie](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Mv2-Serie](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>VM-Images der Generation 2 in Azure Marketplace

VMs der Generation 2 unterstützen die folgenden Azure Marketplace-Images:

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>Lokale in Vergleich zu Azure-VMs der Generation 2

Azure unterstützt zurzeit einige der Funktionen nicht, die lokale Hyper-V-Instanzen für VMs der Generation 2 unterstützen.

| Funktionen der Generation 2                | Lokale Hyper-V-Instanz | Azure |
|-------------------------------------|---------------------|-------|
| Sicherer Start                         | :heavy_check_mark:  | :x:   |
| Abgeschirmte VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| Virtualisierungsbasierte Sicherheit (VBS) | :heavy_check_mark:  | :x:   |
| VHDX-Format                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Features und Funktionen

### <a name="generation-1-vs-generation-2-features"></a>Features der Generation 1 und 2 im Vergleich

| Feature | Generation 1 | Generation 2 |
|---------|--------------|--------------|
| Start             | PCAT                      | UEFI                               |
| Datenträgercontroller | IDE                       | SCSI                               |
| VM-Größen         | Für alle VM-Größen verfügbar | Nur durch Storage Premium unterstützte VMs |

### <a name="generation-1-vs-generation-2-capabilities"></a>Funktionen von Generation 1 und Generation 2 im Vergleich

| Funktion | Generation 1 | Generation 2 |
|------------|--------------|--------------|
| Betriebssystem-Datenträger > 2 TB                    | :x:                        | :heavy_check_mark: |
| Benutzerdefinierter Datenträger/Image/Swapbetriebssystem         | :heavy_check_mark:         | :heavy_check_mark: |
| Unterstützung für VM-Skalierungsgruppe | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/Backup                        | :heavy_check_mark:         | :x:                |
| Gemeinsamer Image-Katalog              | :heavy_check_mark:         | :x:                |
| Azure Disk Encryption             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>Erstellen einer VM der Generation 2

### <a name="marketplace-image"></a>Marketplace-Image

VMs der Generation 2 können aus einem Marketplace-Image (das UEFI-Start unterstützt) über das Azure-Portal oder die Azure CLI erstellt werden.

Das `windowsserver-gen2preview`-Angebot enthält nur Windows-Images der Generation 2. Dies vermeidet Verwirrung im Hinblick auf die Images der Generation 1 und der Generation 2. Wählen Sie zum Erstellen von VMs der Generation 2 **Images** aus diesem Angebot aus, und führen Sie den Standardprozess für die VM-Erstellung aus.

Zurzeit werden die folgenden Windows-Images der Generation 2 in Azure Marketplace veröffentlicht:

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

Im Abschnitt zu den Funktionen finden Sie eine Liste der unterstützten Marketplace-Images, da wir ständig weitere Images hinzufügen, die Generation 2 unterstützen.

### <a name="managed-image-or-managed-disk"></a>Verwaltetes Image oder verwalteter Datenträger

VMs der Generation 2 können aus einem verwalteten Image oder einem verwalteten Datenträger auf die gleiche Weise erstellt werden, wie Sie eine VM der Generation 1 erstellen würden.

### <a name="virtual-machine-scale-sets"></a>VM-Skalierungsgruppen

VMs der Generation 2 können auch mithilfe von VM-Skalierungsgruppen erstellt werden. Sie können VMs der Generation 2 mithilfe von Azure VM-Skalierungsgruppen über die Azure CLI erstellen.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

* **Unterstützen VMs der Generation 2 den beschleunigten Netzwerkbetrieb?**  
    Ja, VMs der Generation 2 unterstützen den [beschleunigten Netzwerkbetrieb](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **Wird VHDX für Generation 2 unterstützt?**  
    Nein, nur VHD wird für VMs der Generation 2 unterstützt.

* **Unterstützen VMs der Generation 2 Ultra Solid State-Laufwerke (SSD)?**  
    Ja, VMs der Generation 2 unterstützen Disk Storage Ultra.

* **Kann ich aus VMs der Generation 1 zu VMs der Generation 2 migrieren?**  
    Nein, Sie können die Generation eines virtuellen Computers nicht mehr ändern, nachdem Sie ihn erstellt haben. Wenn Sie zwischen VM-Generationen wechseln müssen, müssen Sie einen neuen virtuellen Computer einer anderen Generation erstellen.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [virtuelle Computer der Generation 2 in Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).