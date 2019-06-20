---
title: Automatisierte Sicherung für virtuelle SQL Server-Computer (klassisch) | Microsoft Docs
description: 'Erläutert das Feature „Automatisierte Sicherung“ für SQL Server auf virtuellen Azure-Computern unter Verwendung des Resource Manager. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: aeb97d661d330ed6afb3ca5e5e1eb924dacc4024
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60607696"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Automatisierte Sicherung für SQL Server auf virtuellen Azure-Computern (klassisch)
> [!div class="op_single_selector"]
> * [Ressourcen-Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Klassisch](../classic/sql-automated-backup.md)
> 
> 

Die automatisierte Sicherung konfiguriert automatisch [Managed Backup für Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) für alle vorhandenen und neuen Datenbanken auf einer Azure-VM, auf der SQL Server 2014 Standard oder Enterprise ausgeführt wird. Dies bietet Ihnen die Möglichkeit, reguläre Datenbanksicherungen zu konfigurieren, die permanenten Azure Blob Storage nutzen. Die automatische Sicherung basiert auf der [Erweiterung für den SQL Server-IaaS-Agent](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager-Bereitstellungen und klassische Bereitstellungen](../../../azure-resource-manager/resource-manager-deployment-model.md). Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells. Die Resource Manager-Version dieses Artikels finden Sie unter [Automatisierte Sicherung für SQL Server auf virtuellen Azure-Computern (Resource Manager)](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Voraussetzungen
Beachten Sie bei der Verwendung der automatisierten Sicherung die folgenden Voraussetzungen:

**Betriebssystem:**

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server-Version/-Edition:**

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> Die automatisierte Sicherung für SQL Server 2016 wird von virtuellen Computern mit Resource Manager unterstützt. Weitere Informationen finden Sie unter [Automatisierte Sicherung v2 für Azure Virtual Machines mit SQL Server 2016 (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Datenbankkonfiguration:**

* Zieldatenbanken müssen das vollständige Wiederherstellungsmodell verwenden.

**Azure PowerShell:**

* [Installieren der neuesten Azure PowerShell-Befehle](/powershell/azure/overview)

**Erweiterung für SQL Server-IaaS:**

* [Installieren der Erweiterung für SQL Server-IaaS](../classic/sql-server-agent-extension.md)

## <a name="settings"></a>Einstellungen
In der folgenden Tabelle werden die Optionen beschrieben, die für die automatisierte Sicherung konfiguriert werden können. Bei klassischen virtuellen Computern muss PowerShell zum Konfigurieren dieser Einstellungen verwendet werden.

| Einstellung | Bereich (Standard) | BESCHREIBUNG |
| --- | --- | --- |
| **Automatisierte Sicherung** |Aktivieren/Deaktivieren (deaktiviert) |Aktiviert oder deaktiviert die automatisierte Sicherung für eine Azure-VM mit SQL Server 2014 Standard oder Enterprise. |
| **Aufbewahrungszeitraum** |1 bis 30 Tage (30 Tage) |Die Anzahl von Tagen, für die eine Sicherung aufbewahrt wird. |
| **Speicherkonto** |Azure-Speicher-Konto (das für die angegebene VM erstellte Speicherkonto) |Ein Azure-Speicherkonto, mit dem Dateien der automatisierten Sicherung im Blob-Speicher gespeichert werden. An diesem Speicherort wird ein Container zum Speichern aller Sicherungsdateien erstellt. Die Namenskonvention für die Sicherungsdatei enthält das Datum, die Uhrzeit und den Computernamen. |
| **Verschlüsselung** |Aktivieren/Deaktivieren (deaktiviert) |Aktiviert oder deaktiviert die Verschlüsselung. Wenn die Verschlüsselung aktiviert ist, befinden sich die Zertifikate zum Wiederherstellen der Sicherung im angegebenen Speicherkonto im gleichen automaticbackup-Container (mit derselben Namenskonvention). Wenn das Kennwort geändert wird, wird ein neues Zertifikat mit diesem Kennwort generiert, das alte Zertifikat bleibt jedoch zum Wiederherstellen vorheriger Sicherungen erhalten. |
| **Kennwort** |Kennworttext (keiner) |Ein Kennwort für Verschlüsselungsschlüssel. Ein Kennwort ist nur erforderlich, wenn die Verschlüsselung aktiviert ist. Um eine verschlüsselte Sicherung wiederherzustellen, benötigen Sie das richtige Kennwort und das zugehörige Zertifikat, das beim Erstellen der Sicherung verwendet wurde. |
| **Sichern von Systemdatenbanken** | Aktivieren/Deaktivieren (deaktiviert) | So erstellen Sie vollständige Sicherungen von Master-, Model- und MSDB-Datenbanken |
| **Configure backup schedule** (Sicherungszeitplan konfigurieren) | Manuell/Automatisiert (automatisch) | Wählen Sie **Automated** (Automatisiert), um automatisch vollständige Sicherungen und Protokollsicherungen basierend auf dem Protokolldateiwachstum zu erstellen. Wählen Sie **Manuell**, um den Zeitplan für vollständige Sicherungen und Protokollsicherungen anzugeben. |

## <a name="configuration-with-powershell"></a>Konfiguration mit PowerShell
Im folgenden PowerShell-Beispiel wird die automatisierte Sicherung für eine vorhandene SQL Server 2014-VM konfiguriert. Der Befehl **New-AzureVMSqlServerAutoBackupConfig** konfiguriert die Einstellungen der automatisierten Sicherung zum Speichern von Sicherungen in dem mit der $storageaccount-Variable angegebenen Azure-Speicherkonto. Die Sicherungen werden für 10 Tage beibehalten. Der Befehl **Set AzureVMSqlServerExtension** aktualisiert die angegebene Azure-VM mit diesen Einstellungen.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Die Installation und Konfiguration des SQL Server-IaaS-Agents kann mehrere Minuten in Anspruch nehmen.

Um die Verschlüsselung zu aktivieren, ändern Sie das vorherige Skript, um den EnableEncryption-Parameter und ein Kennwort (sichere Zeichenfolge) für den CertificatePassword-Parameter zu übergeben. Das folgende Skript aktiviert die Einstellungen der automatisierten Sicherung im vorherigen Beispiel und fügt die Verschlüsselung hinzu.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Führen Sie zum Deaktivieren der automatisierten Sicherung das gleiche Skript ohne den **-Enable**-Parameter für **New-AzureVMSqlServerAutoBackupConfig** aus. Ähnlich wie die Installation kann auch das Deaktivieren der automatisierten Sicherung mehrere Minuten dauern.

> [!NOTE]
> Durch das Deaktivieren und Deinstallieren des SQL Server-IaaS-Agents werden die zuvor konfigurierten Managed Backup-Einstellungen nicht entfernt. Die automatisierte Sicherung sollte vor dem Deaktivieren oder Deinstallieren des SQL Server-IaaS-Agents deaktiviert werden.
> 
> 

## <a name="next-steps"></a>Nächste Schritte
Die automatisierte Sicherung konfiguriert Managed Backup für Azure-VMs. Lesen Sie daher unbedingt die [Dokumentation für Managed Backup](https://msdn.microsoft.com/library/dn449496.aspx) , um sich mit dem Verhalten und den Auswirkungen vertraut zu machen.

Weitere Informationen zur Sicherung und Wiederherstellung für SQL Server auf Azure-VMs finden Sie im folgenden Thema: [Sicherung und Wiederherstellung für SQL Server auf virtuellen Azure-Computern](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Informationen zu anderen verfügbaren Automatisierungsaufgaben finden Sie unter [SQL Server-Agent-Erweiterung für virtuelle SQL Server-Computer (klassisch)](../classic/sql-server-agent-extension.md).

Ausführlichere Informationen zur Ausführung von SQL Server auf virtuellen Azure-Computern finden Sie unter [Übersicht zu SQL Server auf virtuellen Azure-Computern](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

