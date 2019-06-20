---
title: Virtueller Azure-Computer – PowerShell-Beispiele | Microsoft-Dokumentation
description: Virtueller Azure-Computer – PowerShell-Beispiele
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 576fe268bec12c16c7c2e2076dfa066c908693d5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60583688"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Virtueller Azure-Computer – PowerShell-Beispiele

Die folgende Tabelle enthält Links zu exemplarischen PowerShell-Skripts für die Erstellung und Verwaltung virtueller Windows-Computer (VMs).

| | |
|---|---|
|**Erstellen von virtuellen Computern**||
| [Schnelles Erstellen eines virtuellen Computers](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Erstellt eine Ressourcengruppe, einen virtuellen Computer und alle dazugehörigen Ressourcen mit möglichst wenigen Eingabeaufforderungen.|
| [Erstellen einer vollständig konfigurierten VM](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Erstellt eine Ressourcengruppe, einen virtuellen Computer und alle dazugehörigen Ressourcen.|
| [Erstellen hoch verfügbarer VMs](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Erstellt mehrere virtuelle Computer in einer Konfiguration mit hoher Verfügbarkeit und Lastenausgleich.|
| [Erstellen einer VM und Ausführen eines Konfigurationsskripts](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Erstellt einen virtuellen Computer und verwendet die benutzerdefinierte Azure-Skripterweiterung zum Installieren von IIS. |
| [Erstellen einer VM und Ausführen einer DSC-Konfiguration](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Erstellt einen virtuellen Computer und verwendet die Azure-DSC-Erweiterung (Desired State Configuration) zum Installieren von IIS. |
| [Hochladen einer virtuellen Festplatte und Erstellen von virtuellen Computern](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Lädt eine lokale VHD-Datei in Azure hoch, erstellt ein Image auf der Grundlage der VHD und erstellt dann auf der Grundlage dieses Images einen virtuellen Computer. |
| [Erstellen eines virtuellen Computers von einem verwalteten Betriebssystemdatenträger](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Erstellt einen virtuellen Computer durch Anfügen eines vorhandenen verwalteten Datenträgers als Betriebssystemdatenträger. |
| [Erstellen eines virtuellen Computers aus einer Momentaufnahme](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Erstellt einen virtuellen Computer auf der Grundlage einer Momentaufnahme, indem zunächst ein verwalteter Datenträger erstellt und dieser anschließend als Betriebssystemdatenträger angefügt wird. |
|**Verwalten von Speicher**||
| [Erstellen eines verwalteten Datenträgers auf der Grundlage einer VHD im gleichen oder in einem anderen Abonnement](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Erstellt einen verwalteten Datenträger auf der Grundlage einer bestimmten VHD als Betriebssystemdatenträger oder auf der Grundlage einer Daten-VHD als Datenträger für Daten (im gleichen oder in einem anderen Abonnement).  |
| [Erstellen eines verwalteten Datenträgers aus einer Momentaufnahme](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Erstellt einen verwalteten Datenträger aus einer Momentaufnahme. |
| [Kopieren eines verwalteten Datenträgers in das gleiche oder in ein anderes Abonnement](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Kopiert einen verwalteten Datenträger in das gleiche oder in ein anderes Abonnement, das sich in der gleichen Region befindet wie der übergeordnete Datenträger.
| [Exportieren einer Momentaufnahme als VHD in ein Speicherkonto](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exportiert eine verwaltete Momentaufnahme als VHD in ein Speicherkonto in einer anderen Region. |
| [Exportieren der VHD eines verwalteten Datenträgers in ein Speicherkonto](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exportiert die zugrunde liegende VHD eines verwalteten Datenträgers in ein Speicherkonto in einer anderen Region. |
| [Erstellen einer Momentaufnahme aus einer VHD](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Erstellt eine Momentaufnahme auf der Grundlage einer VHD und verwendet diese Momentaufnahme dann, um schnell mehrere identische verwaltete Datenträger zu erstellen.  |
| [Kopieren einer Momentaufnahme in das gleiche oder in ein anderes Abonnement](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Kopiert eine Momentaufnahme in das gleiche oder in ein anderes Abonnement, das sich in der gleichen Region befindet wie die übergeordnete Momentaufnahme. |
|**Sichern von virtuellen Computern**||
| [Verschlüsseln eines virtuellen Computers und der Datenträger](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Erstellt einen Azure-Schlüsseltresor, einen Verschlüsselungsschlüssel und einen Dienstprinzipal und verschlüsselt dann einen virtuellen Computer. |
|**Überwachen virtueller Computer**||
| [Überwachen eines virtuellen Computers mit Azure Monitor](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Erstellt einen virtuellen Computer, installiert den Azure Log Analytics-Agent und registriert den virtuellen Computer in einem Log Analytics-Arbeitsbereich.  |
| | |
