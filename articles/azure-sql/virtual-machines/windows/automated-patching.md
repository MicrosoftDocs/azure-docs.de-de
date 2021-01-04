---
title: Automatisiertes Patchen für virtuelle SQL Server-Computer (Resource Manager) | Microsoft Docs
description: Dieser Artikel erläutert die Funktion „Automatisiertes Patchen“ für virtuelle SQL Server-Computer in Azure unter Verwendung des Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 429fe39f84a54c22fa97178b85f417d76dc84a8e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359471"
---
# <a name="automated-patching-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Automatisiertes Patchen für SQL Server auf Azure-VMs (Resource Manager)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Beim automatisierten Patchen wird ein Wartungsfenster für einer Azure-VM mit SQL Server eingerichtet. Automatische Updates können nur während dieses Wartungsfensters installiert werden. Bei SQL Server stellt diese Einschränkung sicher, dass Systemupdates und alle erforderlichen Neustarts zum bestmöglichen Zeitpunkt für die Datenbank stattfinden. 

> [!IMPORTANT]
> Nur als **Wichtig** oder **Kritisch** markierte Windows- und SQL Server-Updates werden installiert. Andere SQL Server Updates (z. B. Service Packs und kumulative Updates), die nicht als **Wichtig** oder **Kritisch** gekennzeichnet sind, müssen manuell installiert werden. 

Das automatisierte Patchen basiert auf der [Erweiterung für den SQL Server-Infrastructure-as-a-Service (IaaS)-Agent](sql-server-iaas-agent-extension-automate-management.md).

## <a name="prerequisites"></a>Voraussetzungen
Beachten Sie bei der Verwendung des automatisierten Patchens die folgenden Voraussetzungen:

**Betriebssystem**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019

**SQL Server-Version:**

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Azure PowerShell:**

* [Installieren Sie die aktuellen Azure PowerShell-Befehle](/powershell/azure/) , wenn Sie das automatisierte Patchen mit PowerShell konfigurieren möchten.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> Das automatisierte Patchen basiert auf der Erweiterung für den SQL Server-IaaS-Agent. Aktuelle Katalogimages für virtuelle SQL-Computer fügen diese Erweiterung standardmäßig hinzu. Weitere Informationen finden Sie unter [Erweiterung für SQL Server-IaaS-Agent](sql-server-iaas-agent-extension-automate-management.md).
> 
> 

## <a name="settings"></a>Einstellungen
In der folgenden Tabelle werden die Optionen beschrieben, die für das automatisierte Patchen konfiguriert werden können. Die tatsächlichen Konfigurationsschritte variieren abhängig davon, ob Sie das Azure-Portal oder Azure Windows PowerShell-Befehle verwenden.

| Einstellung | Mögliche Werte | BESCHREIBUNG |
| --- | --- | --- |
| **Automatisiertes Patchen** |Aktivieren/Deaktivieren (deaktiviert) |Aktiviert oder deaktiviert das automatisierte Patchen für einen virtuellen Azure-Computer. |
| **Wartungszeitplan** |Täglich, Montag, Dienstag, Mittwoch, Donnerstag, Freitag, Samstag, Sonntag |Der Zeitplan für das Herunterladen und Installieren von Windows-, SQL Server- und Microsoft-Updates für den virtuellen Computer |
| **Wartung beginnt um** |0-24 |Die lokale Startzeit zum Aktualisieren des virtuellen Computers |
| **Dauer des Wartungsfensters** |30-180 |Der Zeitraum in Minuten, in dem das Herunterladen und Installieren von Updates abgeschlossen werden darf |
| **Patch Category** |Wichtig | Die Kategorie der Windows-Updates, die heruntergeladen und installiert werden.|

## <a name="configure-in-the-azure-portal"></a>Konfigurieren im Azure-Portal
Mit dem Azure-Portal können Sie das automatisierte Patchen während der Bereitstellung oder für vorhandene virtuelle Computer konfigurieren.

### <a name="new-vms"></a>Neue virtuelle Computer
Verwenden Sie das Azure-Portal zum Konfigurieren des automatisierten Patchens, wenn Sie einen neuen virtuelle SQL Server-Computer im Resource Manager-Bereitstellungsmodell erstellen.

Wählen Sie auf der Registerkarte **SQL Server-Einstellungen** die Option **Konfiguration ändern** unter **Automatisiertes Patchen** aus. Auf dem folgenden Screenshot des Azure-Portals sehen Sie das Blatt **Automatisiertes SQL-Patchen** .

![Automatisiertes SQL-Patchen im Azure-Portal](./media/automated-patching/azure-sql-arm-patching.png)

Weitere Informationen finden Sie unter [Bereitstellen eines virtuelle SQL Server-Computers in Azure](create-sql-vm-portal.md).

### <a name="existing-vms"></a>Vorhandene virtuelle Computer

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Für vorhandene virtuelle SQL Server-Computer öffnen Sie die Ressource [Virtueller SQL-Computer](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) und wählen dann **Patchen** unter **Einstellungen** aus. 

![Automatisiertes Patchen für SQL für vorhandene virtuelle Computer](./media/automated-patching/azure-sql-rm-patching-existing-vms.png)


Klicken Sie abschließend unten auf dem Blatt **SQL Server-Konfiguration** auf die Schaltfläche **OK**, um die Änderungen zu speichern.

Falls Sie das automatisierte Patchen zum ersten Mal aktivieren, konfiguriert Azure den SQL Server-IaaS-Agent im Hintergrund. Im Azure-Portal wird währenddessen u.U. nicht angezeigt, dass das automatisierte Patchen konfiguriert wird. Warten Sie einige Minuten, bis der Agent installiert und konfiguriert wurde. Danach werden die neuen Einstellungen im Azure-Portal angezeigt.

## <a name="configure-with-powershell"></a>Konfigurieren mithilfe von PowerShell
Nach der Bereitstellung des virtuellen SQL-Computers können Sie das automatisierte Patchen mithilfe von PowerShell konfigurieren.

Im folgenden Beispiel wird PowerShell zum Konfigurieren des automatisierten Patchens auf einem vorhandenen virtuellen SQL Server-Computer verwendet. Der Befehl **New-AzVMSqlServerAutoPatchingConfig** konfiguriert ein neues Wartungsfenster für automatische Updates.

```azurepowershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"
$aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s
Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname
```

> [!IMPORTANT]
> Wenn die Erweiterung noch nicht installiert ist, wird der SQL Server durch ihre Installation neu gestartet.

In der folgenden Tabelle wird basierend auf diesem Beispiel die tatsächliche Auswirkung auf den virtuellen Azure-Zielcomputer beschrieben:

| Parameter | Wirkung |
| --- | --- |
| **DayOfWeek** |Patches werden jeden Donnerstag installiert. |
| **MaintenanceWindowStartingHour** |Updates werden um 11:00 Uhr gestartet. |
| **MaintenanceWindowsDuration** |Patches müssen innerhalb von 120 Minuten installiert werden. Auf der Grundlage der Startzeit müssen sie um 13:00 Uhr abgeschlossen sein. |
| **PatchCategory** |Die einzig mögliche Einstellung für diesen Parameter lautet **Wichtig**. Hiermit werden als „Wichtig“ markierte Windows-Updates installiert; SQL Server-Updates, die nicht in dieser Kategorie enthalten sind, werden nicht installiert. |

Die Installation und Konfiguration des SQL Server-IaaS-Agents kann mehrere Minuten in Anspruch nehmen.

Führen Sie zum Deaktivieren des automatisierten Patchens das gleiche Skript ohne den Parameter **-Enable** für **New-AzVMSqlServerAutoPatchingConfig** aus. Das Fehlen des Parameters **-Enable** signalisiert dem Befehl, die Funktion zu deaktivieren.

## <a name="next-steps"></a>Nächste Schritte
Informationen zu anderen verfügbaren Automatisierungsaufgaben finden Sie unter [SQL Server-Agent-Erweiterung für virtuelle SQL Server-Computer (klassisch)](sql-server-iaas-agent-extension-automate-management.md).

Ausführlichere Informationen zur Ausführung von SQL Server auf virtuellen Azure-Computern finden Sie unter [Übersicht zu SQL Server auf Azure-VMs](sql-server-on-azure-vm-iaas-what-is-overview.md).

