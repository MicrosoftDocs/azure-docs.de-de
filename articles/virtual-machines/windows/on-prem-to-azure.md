---
title: Migrieren von AWS und anderen Plattformen zu Managed Disks in Azure | Microsoft-Dokumentation
description: Erstellen Sie VMs in Azure mithilfe von VHDs, die aus anderen Clouds wie AWS oder von anderen Virtualisierungsplattformen hochgeladen werden, und sichern Sie sich die Vorteile von Azure Managed Disks.
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
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 42ad7bc10cb7b93bd4db9260f950ae4ca12aba44
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61126892"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migrieren von Amazon Web Services (AWS) und anderen Plattformen zu Managed Disks in Azure

Sie können VHD-Dateien aus AWS oder lokalen Virtualisierungslösungen in Azure hochladen, um VMs zu erstellen, die Managed Disks nutzen. Dank Azure Managed Disks ist es nicht mehr erforderlich, Speicherkonten für Azure-IaaS-VMs zu verwalten. Sie müssen lediglich die Art (Premium oder Standard) und die benötigte Größe des Datenträgers angeben, und Azure erstellt und verwaltet ihn für Sie. 

Sie können sowohl generalisierte als auch spezialisierte VHDs hochladen. 
- **Generalisierte VHD**: es wurden alle Ihre persönlichen Kontoinformationen mit Sysprep entfernt. 
- **Spezialisierte VHD**: Behält die Benutzerkonten, Anwendungen und andere Zustandsdaten Ihrer ursprünglichen VM bei. 

> [!IMPORTANT]
> Bevor Sie eine VHD in Azure hochladen, befolgen Sie die Anweisungen unter [Vorbereiten einer Windows-VHD oder -VHDX zum Hochladen in Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>


| Szenario                                                                                                                         | Dokumentation                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Sie verfügen über AWS EC2-Instanzen, die Sie mithilfe von verwalteten Datenträgern zu Azure-VMs migrieren möchten.                              | [Verschieben eines VM aus Amazon Web Services (AWS) zu Azure](aws-to-azure.md)                           |
| Sie verfügen über eine VM von einer anderen Virtualisierungsplattform, die Sie als Image zum Erstellen weiterer Azure-VMs nutzen möchten. | [Hochladen einer generalisierten VHD und Verwendung dieser VHD zum Erstellen einer neuen VM in Azure](upload-generalized-managed.md) |
| Sie habe eine besonders angepasste VM, die Sie in Azure neu erstellen möchten.                                                      | [Hochladen einer spezialisierten VHD in Azure und Erstellen einer neuen VM](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Managed Disks (verwaltete Datenträger): Übersicht

Azure Managed Disks vereinfacht die VM-Verwaltung dadurch, dass die Verwaltung von Speicherkonten entfällt. Managed Disks profitieren auch von der höheren Zuverlässigkeit von VMs in einer Verfügbarkeitsgruppe. Es wird sichergestellt, dass die Datenträger verschiedener VMs in einer Verfügbarkeitsgruppe ausreichend voneinander isoliert sind, um einen Single Point of Failure zu vermeiden. Datenträger verschiedener VMs werden automatisch in einer Verfügbarkeitsgruppe in unterschiedlichen Skalierungseinheiten von Speicher (sog. „Stamps“) platziert. Dadurch werden die Auswirkungen des Ausfalls einzelner Speicherskalierungseinheiten eingedämmt, die von Hardware- und Softwarefehlern verursacht werden.
Je nach Anforderungen stehen vier Typen von Speicheroptionen zur Wahl. Weitere Informationen zu den verfügbaren Datenträgertypen finden Sie in unserem Artikel [Auswählen eines Datenträgertyps](disks-types.md).

## <a name="plan-for-the-migration-to-managed-disks"></a>Planen der Migration zu Managed Disks

Dieser Abschnitt hilft Ihnen, hinsichtlich VM- und Datenträgertypen die beste Entscheidung zu treffen.

Wenn Sie eine Migration von nicht verwalteten Datenträgern zu verwalteten Datenträgern planen, müssen Sie beachten, dass Benutzer mit der Rolle [Mitwirkender von virtuellen Computern](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) danach die Größe des virtuellen Computers nicht mehr ändern können (was vor dem Wechsel möglich war). Der Grund dafür ist, dass virtuelle Computer mit verwalteten Datenträgern erfordern, dass der Benutzer über die Berechtigung „Microsoft.Compute/disks/write“ für die Betriebssystemdatenträger verfügt.

### <a name="location"></a>Location

Wählen Sie einen Standort, an dem Azure Managed Disks verfügbar sind. Wenn Sie zu Premium Managed Disks migrieren, stellen Sie außerdem sicher, dass Storage Premium in der Region verfügbar ist, in die die Migration erfolgen soll. Aktuelle Informationen zu verfügbaren Standorten finden Sie unter [Azure-Dienste nach Region](https://azure.microsoft.com/regions/#services).

### <a name="vm-sizes"></a>VM-Größen

Wenn Sie zu Premium Managed Disks migrieren, müssen Sie die Größe der VM in eine Storage Premium-fähige Größe ändern, die in der Region mit dem Standort der VM verfügbar ist. Überprüfen Sie die Storage Premium-fähigen VM-Größen. Die Größenspezifikationen der Azure-VM sind unter [Größen für virtuelle Computer](sizes.md)aufgelistet.
Sehen Sie sich die Leistungsmerkmale von virtuellen Computern für Storage Premium an, und wählen Sie die am besten für Ihre Workload geeignete VM-Größe aus. Stellen Sie sicher, dass auf Ihrem virtuellen Computer ausreichend Bandbreite zum Steuern des Datenverkehrs des Datenträgers verfügbar ist.

### <a name="disk-sizes"></a>Datenträgergrößen

**Premium Managed Disks**

Es gibt sieben verwaltete Premium-Datenträgertypen, die Sie mit Ihrer VM verwenden können. Jeder Typ weist bestimmte IOPS- und Durchsatzeinschränkungen auf. Berücksichtigen Sie diese Beschränkungen beim Auswählen des Premium-Datenträgertyps für Ihren virtuellen Computer je nach Anforderungen Ihrer Anwendung hinsichtlich Kapazität, Leistung, Skalierbarkeit und Spitzenlasten.

| Premium-Datenträgertyp  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Datenträgergröße           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS pro Datenträger       | 120   | 240   | 500   | 1100  |2\.300              | 5\.000              | 7\.500              | 7\.500              | 
| Durchsatz pro Datenträger | 25 MB pro Sekunde  | 50 MB pro Sekunde  | 100 MB pro Sekunde | 125 MB pro Sekunde |150 MB pro Sekunde | 200 MB pro Sekunde | 250 MB pro Sekunde | 250 MB pro Sekunde |

**Verwaltete Standard-Datenträger**

Es gibt sieben Arten von verwalteten Standard-Datenträgern, die mit Ihrer VM verwendet werden können. Jede davon bietet unterschiedliche Kapazität, jedoch gelten für alle dieselben IOPS- und Durchsatzgrenzwerte. Wählen Sie den Typ der verwalteten Standard-Datenträger basierend auf den Kapazitätsanforderungen Ihrer Anwendung.

| Standard-Datenträgertyp  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Datenträgergröße           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2 TB)    | 4095 GB (4 TB)   | 
| IOPS pro Datenträger       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Durchsatz pro Datenträger | 60 MB pro Sekunde | 60 MB pro Sekunde | 60 MB pro Sekunde | 60 MB pro Sekunde |60 MB pro Sekunde | 60 MB pro Sekunde | 60 MB pro Sekunde | 60 MB pro Sekunde | 

### <a name="disk-caching-policy"></a>Zwischenspeicherungsrichtlinie für Datenträger 

**Premium Managed Disks**

Standardmäßig ist die Richtlinie für das Zwischenspeichern für alle Premium-Datenträger *Schreibgeschützt* und für die Premium-Betriebssystem-Datenträger, die an den virtuellen Computer angeschlossen sind, *Lesen/Schreiben*. Diese Konfigurationseinstellung wird empfohlen, um die optimale E/A-Leistung für Ihre Anwendung zu erreichen. Für Datenträger mit hohem oder ausschließlichem Schreibzugriff (z. B. SQL Server-Protokolldateien) deaktivieren Sie das Zwischenspeichern, sodass Sie eine bessere Anwendungsleistung erzielen können.

### <a name="pricing"></a>Preise

Überprüfen Sie die [Preise für Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). Die Preise für Premium Managed Disks (verwaltete Premium-Datenträger) sind identisch mit denen für Premium Unmanaged Disks (nicht verwaltete Premium-Datenträger). Doch die Preise für verwaltete Standard-Datenträger unterscheiden sich von denen für nicht verwaltete Standard-Datenträger.


## <a name="next-steps"></a>Nächste Schritte

- Bevor Sie eine VHD in Azure hochladen, befolgen Sie die Anweisungen unter [Vorbereiten einer Windows-VHD oder -VHDX zum Hochladen in Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
