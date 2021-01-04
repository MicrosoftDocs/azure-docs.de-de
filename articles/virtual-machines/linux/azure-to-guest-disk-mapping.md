---
title: Zuordnen von Azure-Datenträgern zu Linux-VM-Gastdatenträgern
description: Erfahren Sie, wie Sie die Azure-Datenträger ermitteln, die den Gastdatenträgern einer Linux-VM zugrunde liegen.
author: timbasham
ms.service: virtual-machines-linux
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.openlocfilehash: 4f0e48bf1c14728c54d4e89f30700017b0420d7d
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523327"
---
# <a name="how-to-map-azure-disks-to-linux-vm-guest-disks"></a>Zuordnen von Azure-Datenträgern zu Linux-VM-Gastdatenträgern

Möglicherweise müssen Sie die Azure-Datenträger ermitteln, die den Gastdatenträger einer VM zugrunde liegen. In einigen Szenarien können Sie die Datenträger- oder Volumegröße mit der Größe der angefügten Azure-Datenträger vergleichen. In Szenarien, in denen an die VM mehrere Azure-Datenträger mit derselben Größe angefügt sind, müssen Sie die logische Gerätenummer (Logical Unit Number, LUN) der Datenträger verwenden. 

## <a name="what-is-a-lun"></a>Was ist eine logische Gerätenummer?

Eine logische Gerätenummer (Logical Unit Number, LUN) ist eine Zahl, mit der ein bestimmtes Speichergerät identifiziert werden kann. Jedem Speichergerät wird ein eindeutiger numerischer Bezeichner zugewiesen – beginnend bei null. Der vollständige Pfad zu einem Gerät wird durch die Busnummer, die Ziel-ID und die logische Gerätenummer (LUN) dargestellt. 

Beispiel: ***Busnummer 0, Ziel-ID 0, LUN 3** _

Für diese Übung benötigen Sie nur die LUN.

## <a name="finding-the-lun"></a>Ermitteln der logischen Gerätenummer

Im Folgenden finden Sie zwei Methoden zum Ermitteln der logischen Gerätenummer eines Datenträgers unter Linux.

### <a name="lsscsi"></a>lsscsi

1. Herstellen der Verbindung zur VM
1. `sudo lsscsi`

Die erste aufgelistete Spalte enthält die LUN. Das Format lautet: [Host:Kanal:Ziel:_*LUN**].

### <a name="listing-block-devices"></a>Auflisten der Blockgeräte

1. Herstellen der Verbindung zur VM
1. `sudo ls -l /sys/block/*/device`

Die letzte aufgelistete Spalte enthält die LUN. Das Format ist: [Host:Kanal:Ziel:**LUN**].

## <a name="finding-the-lun-for-the-azure-disks"></a>Ermitteln der logischen Gerätenummer von Azure-Datenträgern

Sie können die logische Gerätenummer eines Azure-Datenträgers mithilfe des Azure-Portals oder der Azure-Befehlszeilenschnittstelle ermitteln.

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Ermitteln der logischen Gerätenummer eines Azure-Datenträgers im Azure-Portal

1. Wählen Sie im Azure-Portal „Virtuelle Computer“ aus, um eine Liste Ihrer VMs anzuzeigen.
1. Wählen Sie den virtuellen Computer aus.
1. Wählen Sie „Datenträger“ aus.
1. Wählen Sie in der Liste der angefügten Datenträger einen aus.
1. Die logische Gerätenummer des Datenträgers wird im Detailbereich des Datenträgers angezeigt. Die hier angezeigte logische Gerätenummer entspricht den logischen Gerätenummern, die Sie im Gast mithilfe von lsscsi oder durch Auflisten der Blockgeräte ermittelt haben.

### <a name="finding-an-azure-disks-lun-using-azure-cli"></a>Ermitteln der logischen Gerätenummer eines Azure-Datenträgers mithilfe der Azure-Befehlszeilenschnittstelle

```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```
