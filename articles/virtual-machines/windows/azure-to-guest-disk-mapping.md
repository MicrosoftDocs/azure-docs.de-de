---
title: Zuordnen von Azure-Datenträgern zu Windows-VM-Gastdatenträgern
description: Erfahren Sie, wie Sie die Azure-Datenträger ermitteln, die den Gastdatenträgern einer Windows-VM zugrunde liegen.
author: timbasham
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.openlocfilehash: f16e34f372016f284d4af79443e84d9d5cdea957
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523320"
---
# <a name="how-to-map-azure-disks-to-windows-vm-guest-disks"></a>Zuordnen von Azure-Datenträgern zu Windows-VM-Gastdatenträgern

Möglicherweise müssen Sie die Azure-Datenträger ermitteln, die den Gastdatenträger einer VM zugrunde liegen. In einigen Szenarien können Sie die Datenträger- oder Volumegröße mit der Größe der angefügten Azure-Datenträger vergleichen. In Szenarien, in denen an die VM mehrere Azure-Datenträger mit derselben Größe angefügt sind, müssen Sie die logische Gerätenummer (Logical Unit Number, LUN) der Datenträger verwenden. 

## <a name="what-is-a-lun"></a>Was ist eine logische Gerätenummer?

Eine logische Gerätenummer (Logical Unit Number, LUN) ist eine Zahl, mit der ein bestimmtes Speichergerät identifiziert werden kann. Jedem Speichergerät wird ein eindeutiger numerischer Bezeichner zugewiesen – beginnend bei null. Der vollständige Pfad zu einem Gerät wird durch die Busnummer, die Ziel-ID und die logische Gerätenummer (LUN) dargestellt. 

Beispiel: ***Busnummer 0, Ziel-ID 0, LUN 3***

Für diese Übung benötigen Sie nur die LUN.

## <a name="finding-the-lun"></a>Ermitteln der logischen Gerätenummer

Es gibt zwei Methoden zum Ermitteln der logischen Gerätenummer, deren Auswahl davon abhängt, ob Sie [Speicherplätze](https://docs.microsoft.com/windows-server/storage/storage-spaces/overview) verwenden oder nicht.

### <a name="disk-management"></a>Datenträgerverwaltung

Wenn Sie keine Speicherpools verwenden, können Sie die logische Gerätenummer mithilfe der [Datenträgerverwaltung](https://docs.microsoft.com/windows-server/storage/disk-management/overview-of-disk-management) ermitteln.

1. Stellen Sie eine Verbindung mit der VM her, und öffnen Sie die Datenträgerverwaltung. Klicken Sie mit der rechten Maustaste auf die Schaltfläche „Start“, und wählen Sie „Datenträgerverwaltung“ aus. Sie können auch `diskmgmt.msc` in das Suchfeld im Startmenü eingeben.
1. Klicken Sie im unteren Bereich mit der rechten Maustaste auf einen der Datenträger, und wählen Sie „Eigenschaften“ aus.
1. Die logische Gerätenummer wird unter der Location-Eigenschaft auf der Registerkarte „Allgemein“ aufgeführt.

### <a name="storage-pools"></a>Speicherpools

1. Stellen Sie eine Verbindung mit der VM her, und öffnen Sie Server-Manager.
1. Auswählen von „Datei- und Speicherdienste“, „Volumes“, „Speicherpools“
1. In der rechten unteren Ecke von Server-Manager wird der Abschnitt „Physische Datenträger“ angezeigt. Die Datenträger, aus denen sich der Speicherpool zusammensetzt, werden hier ebenfalls mit der logischen Gerätenummer für die einzelnen Datenträger aufgelistet.

## <a name="finding-the-lun-for-the-azure-disks"></a>Ermitteln der logischen Gerätenummer von Azure-Datenträgern

Sie können die logische Gerätenummer eines Azure-Datenträgers mithilfe des Azure-Portals, der Azure-Befehlszeilenschnittstelle oder von Azure PowerShell ermitteln.

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Ermitteln der logischen Gerätenummer eines Azure-Datenträgers im Azure-Portal

1. Wählen Sie im Azure-Portal „Virtuelle Computer“ aus, um eine Liste Ihrer VMs anzuzeigen.
1. Wählen Sie den virtuellen Computer aus.
1. Wählen Sie „Datenträger“ aus.
1. Wählen Sie in der Liste der angefügten Datenträger einen aus.
1. Die logische Gerätenummer des Datenträgers wird im Detailbereich des Datenträgers angezeigt. Die hier angezeigte logische Gerätenummer entspricht den logischen Gerätenummern, die Sie im Gast mithilfe von Geräte-Manager oder Server-Manager ermittelt haben.

### <a name="finding-an-azure-disks-lun-using-azure-cli-or-azure-powershell"></a>Ermitteln der logischen Gerätenummer eines Azure-Datenträgers mithilfe der Azure-Befehlszeilenschnittstelle oder von Azure PowerShell

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
$vm.StorageProfile.DataDisks | ft
```
---
