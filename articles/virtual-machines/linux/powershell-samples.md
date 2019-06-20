---
title: Virtueller Azure-Computer – PowerShell-Beispiele | Microsoft-Dokumentation
description: Virtueller Azure-Computer – PowerShell-Beispiele
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 53784c3d74f9e6af5f1e84cc098194113e81333b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60771259"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Virtueller Azure-Computer – PowerShell-Beispiele

Die folgende Tabelle enthält Links zu Beispielen von PowerShell-Skripts, die virtuelle Linux-Computer erstellen und verwalten.

| | |
|---|---|
|**Erstellen von virtuellen Computern**||
| [Erstellen einer vollständig konfigurierten VM](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Erstellt eine Ressourcengruppe, einen virtuellen Computer und alle zugehörigen Ressourcen.|
| [Erstellen eines virtuellen Computers mit Docker](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Erstellt einen virtuellen Computer, konfiguriert diesen virtuellen Computer als Docker-Host und führt einen NGINX-Container aus. |
| [Erstellen einer VM und Ausführen eines Konfigurationsskripts](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Erstellt einen virtuellen Computer und verwendet die benutzerdefinierte Azure-Skripterweiterung zum Installieren von NGINX. |
| [Erstellen eines virtuellen Computers mit WordPress](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Erstellt einen virtuellen Computer und verwendet die benutzerdefinierte Azure-Skripterweiterung zum Installieren von WordPress. |
| [Erstellen eines virtuellen Computers von einem verwalteten Betriebssystemdatenträger](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Erstellt einen virtuellen Computer durch Anfügen eines vorhandenen verwalteten Datenträgers als Betriebssystemdatenträger. |
| [Erstellen eines virtuellen Computers aus einer Momentaufnahme](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Erstellt einen virtuellen Computer auf der Grundlage einer Momentaufnahme, indem zunächst ein verwalteter Datenträger erstellt und dieser anschließend als Betriebssystemdatenträger angefügt wird. |
|**Verwalten von Speicher**||
| [Erstellen eines verwalteten Datenträgers auf der Grundlage einer VHD im gleichen oder in einem anderen Abonnement](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Erstellt einen verwalteten Datenträger auf der Grundlage einer bestimmten VHD als Betriebssystemdatenträger oder auf der Grundlage einer Daten-VHD als Datenträger für Daten (im gleichen oder in einem anderen Abonnement).  |
| [Erstellen eines verwalteten Datenträgers aus einer Momentaufnahme](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Erstellt einen verwalteten Datenträger aus einer Momentaufnahme. |
| [Exportieren einer Momentaufnahme als VHD in ein Speicherkonto](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exportiert eine verwaltete Momentaufnahme als VHD in ein Speicherkonto in einer anderen Region. |
| [Exportieren der VHD eines verwalteten Datenträgers in ein Speicherkonto](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exportiert die zugrunde liegende VHD eines verwalteten Datenträgers in ein Speicherkonto in einer anderen Region. |
| [Erstellen einer Momentaufnahme aus einer VHD](../scripts/virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Erstellt eine Momentaufnahme auf der Grundlage einer VHD und verwendet diese Momentaufnahme dann, um schnell mehrere identische verwaltete Datenträger zu erstellen.  |
| [Kopieren einer Momentaufnahme in das gleiche oder in ein anderes Abonnement](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopiert eine Momentaufnahme in das gleiche oder in ein anderes Abonnement, das sich in der gleichen Region befindet wie die übergeordnete Momentaufnahme. |
|**Überwachen virtueller Computer**||
| [Überwachen eines virtuellen Computers mit Azure Monitor-Protokollen](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Erstellt einen virtuellen Computer, installiert den Log Analytics-Agent und registriert den virtuellen Computer in einem Log Analytics-Arbeitsbereich.  |
| [Kopieren eines verwalteten Datenträgers in das gleiche oder in ein anderes Abonnement](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopiert einen verwalteten Datenträger in das gleiche oder in ein anderes Abonnement, das sich in der gleichen Region befindet wie der übergeordnete Datenträger.
| | |
