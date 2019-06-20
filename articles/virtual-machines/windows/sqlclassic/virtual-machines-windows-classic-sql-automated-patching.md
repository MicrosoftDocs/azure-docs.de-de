---
title: Automatisiertes Patchen für virtuelle SQL Server-Computer (klassisch) | Microsoft Docs
description: Erläutert das Feature „Automatisiertes Patchen“ für virtuelle SQL Server-Computer in Azure mithilfe des klassischen Bereitstellungsmodus.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: aa912e3eb76d72e7a79c83d7e51d493310bd36b3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60362134"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatisiertes Patchen für SQL Server auf virtuellen Azure-Computern (klassisch)
> [!div class="op_single_selector"]
> * [Ressourcen-Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Klassisch](../classic/sql-automated-patching.md)
> 
> 

Beim automatisierten Patchen wird ein Wartungsfenster für einen virtuellen Azure-Computer mit SQL Server eingerichtet. Automatische Updates können nur während dieses Wartungsfensters installiert werden. Dadurch wird bei SQL Server sichergestellt, dass Systemupdates und alle erforderlichen Neustarts zum bestmöglichen Zeitpunkt für die Datenbank stattfinden. 

> [!IMPORTANT]
> Nur als **Wichtig** markierte Windows-Updates werden installiert. Andere SQL Server-Updates wie kumulative Updates müssen manuell installiert werden. 

Das automatisierte Patchen basiert auf der [Erweiterung für den SQL Server-IaaS-Agent](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager-Bereitstellungen und klassische Bereitstellungen](../../../azure-resource-manager/resource-manager-deployment-model.md). Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells. Die Resource Manager-Version dieses Artikels finden Sie unter [Automatisiertes Patchen für SQL Server auf virtuellen Azure-Computern (Resource Manager)](../sql/virtual-machines-windows-sql-automated-patching.md).

## <a name="prerequisites"></a>Voraussetzungen
Beachten Sie bei der Verwendung des automatisierten Patchens die folgenden Voraussetzungen:

**Betriebssystem:**

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server-Version:**

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell:**

* [Installieren der neuesten Azure PowerShell-Befehle](/powershell/azure/overview)

**Erweiterung für SQL Server-IaaS:**

* [Installieren der Erweiterung für SQL Server-IaaS](../classic/sql-server-agent-extension.md)

## <a name="settings"></a>Einstellungen
In der folgenden Tabelle werden die Optionen beschrieben, die für das automatisierte Patchen konfiguriert werden können. Bei klassischen virtuellen Computern muss PowerShell zum Konfigurieren dieser Einstellungen verwendet werden.

| Einstellung | Mögliche Werte | BESCHREIBUNG |
| --- | --- | --- |
| **Automatisiertes Patchen** |Aktivieren/Deaktivieren (deaktiviert) |Aktiviert oder deaktiviert das automatisierte Patchen für einen virtuellen Azure-Computer. |
| **Wartungszeitplan** |Täglich, Montag, Dienstag, Mittwoch, Donnerstag, Freitag, Samstag, Sonntag |Der Zeitplan für das Herunterladen und Installieren von Windows-, SQL Server- und Microsoft-Updates für den virtuellen Computer |
| **Wartung beginnt um** |0-24 |Die lokale Startzeit zum Aktualisieren des virtuellen Computers |
| **Dauer des Wartungsfensters** |30-180 |Der Zeitraum in Minuten, in dem das Herunterladen und Installieren von Updates abgeschlossen werden darf |
| **Patch Category** |Wichtig |Die Kategorie der Updates, die heruntergeladen und installiert werden. |

## <a name="configuration-with-powershell"></a>Konfiguration mit PowerShell
Im folgenden Beispiel wird PowerShell zum Konfigurieren des automatisierten Patchens auf einem vorhandenen virtuellen SQL Server-Computer verwendet. Der Befehl **New-AzureVMSqlServerAutoPatchingConfig** konfiguriert ein neues Wartungsfenster für automatische Updates.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

In der folgenden Tabelle wird basierend auf diesem Beispiel die tatsächliche Auswirkung auf den virtuellen Azure-Zielcomputer beschrieben:

| Parameter | Wirkung |
| --- | --- |
| **DayOfWeek** |Patches werden jeden Donnerstag installiert. |
| **MaintenanceWindowStartingHour** |Updates werden um 11:00 Uhr gestartet. |
| **MaintenanceWindowDuration** |Patches müssen innerhalb von 120 Minuten installiert werden. Auf der Grundlage der Startzeit müssen sie um 13:00 Uhr abgeschlossen sein. |
| **PatchCategory** |Die einzig mögliche Einstellung für diesen Parameter lautet „Wichtig“. |

Die Installation und Konfiguration des SQL Server-IaaS-Agents kann mehrere Minuten in Anspruch nehmen.

Führen Sie zum Deaktivieren des automatisierten Patchens das gleiche Skript ohne den Parameter „-Enable“ für „New-AzureVMSqlServerAutoPatchingConfig“ aus. Wie bei der Installation kann auch das Deaktivieren des automatisierten Patchens mehrere Minuten dauern.

## <a name="next-steps"></a>Nächste Schritte
Informationen zu anderen verfügbaren Automatisierungsaufgaben finden Sie unter [SQL Server-Agent-Erweiterung für virtuelle SQL Server-Computer (klassisch)](../classic/sql-server-agent-extension.md).

Ausführlichere Informationen zur Ausführung von SQL Server auf virtuellen Azure-Computern finden Sie unter [Übersicht zu SQL Server auf virtuellen Azure-Computern](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

