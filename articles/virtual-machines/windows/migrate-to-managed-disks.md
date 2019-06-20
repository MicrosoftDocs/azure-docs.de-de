---
title: Migrieren von Azure-VMs zu Azure Managed Disks | Microsoft-Dokumentation
description: Migrieren Sie virtuelle Azure-Computer, die in Speicherkonten mit nicht verwalteten Datenträgern erstellt wurden, zu Managed Disks.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7a390c5231f715ce778c0cc267211b8bfb5934d2
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418403"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrieren von Azure-VMs zu Managed Disks in Azure

Azure Managed Disks vereinfacht die Speicherverwaltung dadurch, dass die gesonderte Verwaltung von Speicherkonten entfällt.  Sie können auch Ihre vorhandenen Azure-VMs zu Managed Disks migrieren, um von der höheren Zuverlässigkeit von VMs in einer Verfügbarkeitsgruppe zu profitieren. Es wird sichergestellt, dass die Datenträger verschiedener VMs in einer Verfügbarkeitsgruppe ausreichend voneinander isoliert sind, um Single Points of Failure zu vermeiden. Datenträger verschiedener VMs werden automatisch in einer Verfügbarkeitsgruppe in unterschiedlichen Skalierungseinheiten von Speicher (sog. „Stamps“) platziert. Dadurch werden die Auswirkungen des Ausfalls einzelner Speicherskalierungseinheiten eingedämmt, die von Hardware- und Softwarefehlern verursacht werden.
Je nach Anforderungen stehen vier Typen von Speicheroptionen zur Wahl. Weitere Informationen zu den verfügbaren Datenträgertypen finden Sie in unserem Artikel [Auswählen eines Datenträgertyps](disks-types.md).

## <a name="migration-scenarios"></a>Migrationsszenarien

Sie können in folgenden Szenarien zu Managed Disks migrieren:

|Szenario  |Artikel  |
|---------|---------|
|Konvertieren von eigenständigen virtuellen Computern und virtuellen Computern in einer Verfügbarkeitsgruppe in verwaltete Datenträger     |[Konvertieren von virtuellen Computern für die Verwendung verwalteter Datenträger](convert-unmanaged-to-managed-disks.md)         |
|Konvertieren einer einzelnen VM vom klassischen Bereitstellungsmodell in das Ressourcen-Manager-Bereitstellungsmodell auf verwalteten Datenträgern     |[Erstellen einer VM aus einer klassischen VHD](create-vm-specialized-portal.md)         |
|Konvertieren aller VMs in einem VNet vom klassischen Bereitstellungsmodell in das Ressourcen-Manager-Bereitstellungsmodell auf verwalteten Datenträgern     |[Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zum Ressourcen-Manager-Bereitstellungsmodell](migration-classic-resource-manager-ps.md) und dann [Konvertieren eines virtuellen Computers von nicht verwalteten Datenträgern in verwaltete Datenträger](convert-unmanaged-to-managed-disks.md)         |
|Upgrade von VMs mit nicht verwalteten Standarddatenträgern auf VMs mit verwalteten Premium-Datenträgern     | Zuerst [konvertieren Sie eine Windows-VM von nicht verwalteten Datenträgern in verwaltete Datenträger](convert-unmanaged-to-managed-disks.md). Dann [aktualisieren Sie den Speichertyp eines verwalteten Datenträgers](convert-disk-storage.md).         |

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Managed Disks](managed-disks-overview.md).
- Überprüfen Sie die [Preise für Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).
