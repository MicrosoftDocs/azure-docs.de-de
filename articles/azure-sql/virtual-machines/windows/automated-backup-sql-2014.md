---
title: Automatisierte Sicherung für Azure-VMs mit SQL Server 2014
description: Erläutert das Feature „Automatisierte Sicherung“ für SQL Server 2014-VMs, die in Azure ausgeführt werden. Dieser Artikel bezieht sich speziell auf VMs, die das Resource Manager-Modell verwenden.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 41add54ce767413982ab0503f7263c58aed4d4e2
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359284"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Automatisierte Sicherung für SQL Server 2014-VMs (Resource Manager)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [SQL Server 2014](automated-backup-sql-2014.md)
> * [SQL Server 2016/2017](automated-backup.md)

Die automatisierte Sicherung konfiguriert automatisch [Managed Backup für Microsoft Azure](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure) für alle vorhandenen und neuen Datenbanken auf einer Azure-VM, auf der SQL Server 2014 Standard oder Enterprise ausgeführt wird. Dies bietet Ihnen die Möglichkeit, reguläre Datenbanksicherungen zu konfigurieren, die permanenten Azure Blob Storage nutzen. Das automatisierte Sichern basiert auf der [Erweiterung für den SQL Server-Infrastructure-as-a-Service (IaaS)-Agent](sql-server-iaas-agent-extension-automate-management.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Voraussetzungen
Beachten Sie bei der Verwendung der automatisierten Sicherung die folgenden Voraussetzungen:


**Betriebssystem**:

- Windows Server 2012 und höher 

**SQL Server-Version/-Edition:**

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!NOTE]
> Informationen zu SQL 2016 und höher finden Sie unter [Automatisierte Sicherung v2 für virtuelle Azure-Computer (Resource Manager)](automated-backup.md).

**Datenbankkonfiguration:**

- _Zielbenutzerdatenbanken_ müssen das vollständige Wiederherstellungsmodell verwenden. Für Systemdatenbanken muss nicht das vollständige Wiederherstellungsmodell verwendet werden. Wenn Sie allerdings Protokollsicherungen für das Modell oder für msdb benötigen, müssen Sie das vollständige Wiederherstellungsmodell verwenden. Weitere Informationen zu den Auswirkungen des vollständigen Wiederherstellungsmodells auf Sicherungen finden Sie unter [Sichern beim vollständigen Wiederherstellungsmodell](/previous-versions/sql/sql-server-2008-r2/ms190217(v=sql.105)). 
- Die SQL Server-VM wurde mit der SQL-IaaS-Agent-Erweiterung im [Verwaltungsmodus „Vollständig“](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full) registriert. 
-  Die automatisierte Sicherung basiert auf der vollständigen [Erweiterung für den SQL Server-IaaS-Agent](sql-server-iaas-agent-extension-automate-management.md). Daher wird die automatisierte Sicherung nur für Zieldatenbanken von der Standardinstanz oder eine einzelne benannte Instanz unterstützt. Wenn keine Standardinstanz und mehrere benannte Instanzen vorhanden sind, kann die SQL-IaaS-Erweiterung nicht ausgeführt werden, und die automatisierte Sicherung funktioniert nicht. 

## <a name="settings"></a>Einstellungen

In der folgenden Tabelle werden die Optionen beschrieben, die für die automatisierte Sicherung konfiguriert werden können. Die tatsächlichen Konfigurationsschritte variieren abhängig davon, ob Sie das Azure-Portal oder Azure Windows PowerShell-Befehle verwenden.

| Einstellung | Bereich (Standard) | BESCHREIBUNG |
| --- | --- | --- |
| **Automatisierte Sicherung** | Aktivieren/Deaktivieren (deaktiviert) | Aktiviert oder deaktiviert die automatisierte Sicherung für eine Azure-VM mit SQL Server 2014 Standard oder Enterprise. |
| **Aufbewahrungszeitraum** | 1 bis 30 Tage (30 Tage) | Die Anzahl von Tagen, für die eine Sicherung aufbewahrt wird. |
| **Speicherkonto** | Azure-Speicherkonto | Ein Azure-Speicherkonto, mit dem Dateien der automatisierten Sicherung im Blob-Speicher gespeichert werden. An diesem Speicherort wird ein Container zum Speichern aller Sicherungsdateien erstellt. Die Namenskonvention für die Sicherungsdatei enthält das Datum, die Uhrzeit und den Computernamen. |
| **Verschlüsselung** | Aktivieren/Deaktivieren (deaktiviert) | Aktiviert oder deaktiviert die Verschlüsselung. Wenn die Verschlüsselung aktiviert ist, befinden sich die Zertifikate zum Wiederherstellen der Sicherung im angegebenen Speicherkonto im gleichen `automaticbackup`-Container (mit der gleichen Namenskonvention). Wenn das Kennwort geändert wird, wird ein neues Zertifikat mit diesem Kennwort generiert, das alte Zertifikat bleibt jedoch zum Wiederherstellen vorheriger Sicherungen erhalten. |
| **Kennwort** | Kennworttext | Ein Kennwort für Verschlüsselungsschlüssel. Ein Kennwort ist nur erforderlich, wenn die Verschlüsselung aktiviert ist. Um eine verschlüsselte Sicherung wiederherzustellen, benötigen Sie das richtige Kennwort und das zugehörige Zertifikat, das beim Erstellen der Sicherung verwendet wurde. |


## <a name="configure-new-vms"></a>Konfigurieren neuer VMs

Verwenden Sie das Azure-Portal zum Konfigurieren der automatisierten Sicherung, wenn Sie einen neuen virtuellen Computer mit SQL Server 2014 im Resource Manager-Bereitstellungsmodell erstellen.

Scrollen Sie auf der Registerkarte **SQL Server-Einstellungen** nach unten zu **Automatisierte Sicherung**, und wählen Sie **Aktivieren** aus. Auf dem folgenden Screenshot des Azure-Portals sehen Sie die Einstellungen für **Automatisierte SQL-Sicherung**.

![Konfigurieren der automatisierten SQL-Sicherung im Azure-Portal](./media/automated-backup-sql-2014/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Konfigurieren vorhandener VMs

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Für vorhandene SQL Server-VMs können Sie automatisierte Sicherungen aktivieren und deaktivieren, die Beibehaltungsdauer ändern, das Speicherkonto angeben und die Verschlüsselung über das Azure-Portal aktivieren. 

Navigieren Sie für Ihren virtuellen SQL Server 2014-Computer zur [Ressource für virtuelle SQL-Computer](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource), und wählen Sie dann **Sicherungen** aus. 

![Automatisierte SQL-Sicherung für vorhandene virtuelle Computer](./media/automated-backup-sql-2014/azure-sql-rm-autobackup-existing-vms.png)

Wählen Sie abschließend die Schaltfläche **Übernehmen** am unteren Rand der Seite **Sicherungen** aus, um Ihre Änderungen zu speichern.

Falls Sie die automatisierte Sicherung zum ersten Mal aktivieren, konfiguriert Azure den SQL Server-IaaS-Agent im Hintergrund. Im Azure-Portal wird währenddessen u.U. nicht angezeigt, dass die automatisierte Sicherung konfiguriert wird. Warten Sie einige Minuten, bis der Agent installiert und konfiguriert wurde. Danach werden die neuen Einstellungen im Azure-Portal angezeigt.

> [!NOTE]
> Sie können die automatisierte Sicherung auch mithilfe einer Vorlage konfigurieren. Weitere Informationen finden Sie unter [Azure quickstart template for Automated Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update)(Azure-Schnellstartvorlage für die automatisierte Sicherung).

## <a name="configure-with-powershell"></a>Konfigurieren mithilfe von PowerShell

Die automatisierte Sicherung kann mithilfe von PowerShell konfiguriert werden. Führen Sie zur Vorbereitung folgende Schritte aus:

- [Laden Sie die aktuelle Version von Azure PowerShell herunter, und installieren Sie sie.](https://aka.ms/webpi-azps)
- Öffnen Sie Windows PowerShell, und stellen Sie mit dem Befehl **Connect-AzAccount** eine Verknüpfung mit Ihrem Konto her. 

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-server-iaas-extension"></a>Installieren der Erweiterung für SQL Server-IaaS
Wenn Sie eine SQL Server-VM über das Azure-Portal bereitgestellt haben, müsste die SQL Server-IaaS-Erweiterung bereits installiert sein. Rufen Sie den Befehl **Get-AzVM** auf, und sehen Sie sich die Eigenschaft **Extensions** an, um zu ermitteln, ob sie für Ihren virtuellen Computer installiert ist.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

Wenn die SQL Server-IaaS-Agent-Erweiterung installiert ist, wird „SqlIaaSAgent“ oder „SQLIaaSExtension“ aufgeführt. Außerdem muss für die Erweiterung unter **ProvisioningState** der Zustand „Succeeded“ angezeigt werden.

Falls die Erweiterung nicht installiert oder nicht erfolgreich bereitgestellt wurde, können Sie sie mithilfe des folgenden Befehls installieren. Neben dem Namen des virtuellen Computers und der Ressourcengruppe müssen Sie auch die Region ( **$region**) angeben, in der sich Ihr virtueller Computer befindet. Geben Sie den Lizenztyp für Ihre SQL Server-VM an, und wählen Sie über den [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) eine Lizenz mit nutzungsbasierter Bezahlung oder eine BYOL-Lizenz (Bring Your Own License) aus. Weitere Informationen zur Lizenzierung finden Sie unter [Lizenzierungsmodell](licensing-model-azure-hybrid-benefit-ahb-change.md). 

```powershell
New-AzSqlVM  -Name $vmname `
    -ResourceGroupName $resourcegroupname `
    -Location $region -LicenseType <PAYG/AHUB>
```

> [!IMPORTANT]
> Wenn die Erweiterung noch nicht installiert ist, wird der SQL Server-Dienst durch deren Installation neu gestartet.

### <a name="verify-current-settings"></a><a id="verifysettings"></a> Überprüfen der aktuellen Einstellungen

Wenn Sie die automatisierte Sicherung während der Bereitstellung aktiviert haben, können Sie mithilfe von PowerShell Ihre aktuelle Konfiguration überprüfen. Führen Sie den Befehl **Get-AzVMSqlServerExtension** aus, und sehen Sie sich die Eigenschaft **AutoBackupSettings** an:

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Die Ausgabe sollte in etwa wie folgt aussehen:

```
Enable                      : False
EnableEncryption            : False
RetentionPeriod             : -1
StorageUrl                  : NOTSET
StorageAccessKey            : 
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : 
FullBackupFrequency         : 
FullBackupStartTime         : 
FullBackupWindowHours       : 
LogBackupFrequency          : 
```

Falls Sie in der Ausgabe sehen, dass **Enable** auf **False** festgelegt ist, müssen Sie die automatisierte Sicherung aktivieren. Die gute Nachricht: Die Vorgehensweise zum Aktivieren der automatisierten Sicherung unterscheidet sich nicht von der Vorgehensweise zum Konfigurieren. Entsprechende Informationen finden Sie im nächsten Abschnitt.

> [!NOTE] 
> Wenn Sie eine Änderung vorgenommen haben und die Einstellungen direkt danach überprüfen, werden unter Umständen noch die alten Konfigurationswerte zurückgegeben. Warten Sie einige Minuten, und überprüfen Sie die Einstellungen dann erneut, um sich zu vergewissern, dass die Änderungen angewendet wurden.

### <a name="configure-automated-backup"></a>Konfigurieren der automatisierten Sicherung
Mithilfe von PowerShell können Sie die automatisierte Sicherung nicht nur aktivieren, sondern auch jederzeit ihre Konfiguration und ihr Verhalten ändern.

Wählen Sie zunächst ein Speicherkonto für die Sicherungsdateien aus, oder erstellen Sie eines. Das folgende Skript wählt ein Speicherkonto aus. Ist kein Speicherkonto vorhanden, wird eines erstellt.

```powershell
$storage_accountname = "yourstorageaccount"
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }
```

> [!NOTE]
> Die automatisierte Sicherung unterstützt zwar nicht das Speichern von Sicherungen in Storage Premium, kann aber Sicherungen von VM-Datenträgern erstellen, die Storage Premium verwenden.

Verwenden Sie dann den Befehl **New-AzVMSqlServerAutoBackupConfig**, um die Einstellungen der automatisierten Sicherung so zu konfigurieren, dass Sicherungen in dem Azure Storage-Konto gespeichert werden. In diesem Beispiel werden die Sicherungen zehn Tage lang aufbewahrt. Der zweite Befehl, **Set-AzVMSqlServerExtension**, aktualisiert den angegebenen virtuellen Azure-Computer mit diesen Einstellungen.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Die Installation und Konfiguration des SQL Server-IaaS-Agents kann mehrere Minuten in Anspruch nehmen.

> [!NOTE]
> Es gibt andere Einstellungen für **New-AzVMSqlServerAutoBackupConfig**, die nur für SQL Server 2016 und die automatisierte Sicherung v2 gelten. Die folgenden Einstellungen werden von SQL Server 2014 nicht unterstützt: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours** und **LogBackupFrequencyInMinutes**. Wenn Sie versuchen, diese Einstellungen auf einem virtuellen Computer mit SQL Server 2014 zu konfigurieren, wird zwar kein Fehler angezeigt, doch die Einstellungen werden nicht angewendet. Wenn Sie diese Einstellungen auf einem virtuellen Computer mit SQL Server 2016verwenden möchten, siehe [Automatisierte Sicherung v2 für Azure-VMs mit SQL Server 2016](automated-backup.md).

Um die Verschlüsselung zu aktivieren, ändern Sie das vorherige Skript, um den **EnableEncryption**-Parameter und ein Kennwort (sichere Zeichenfolge) für den **CertificatePassword**-Parameter zu übergeben. Das folgende Skript aktiviert die Einstellungen der automatisierten Sicherung im vorherigen Beispiel und fügt die Verschlüsselung hinzu.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

[Überprüfen Sie die Konfiguration der automatisierten Sicherung](#verifysettings), um sich zu vergewissern, dass Ihre Einstellungen angewendet wurden.

### <a name="disable-automated-backup"></a>Deaktivieren der automatisierten Sicherung

Führen Sie zum Deaktivieren der automatisierten Sicherung das gleiche Skript ohne den Parameter **-Enable** für den Befehl **New-AzVMSqlServerAutoBackupConfig** aus. Das Fehlen des Parameters **-Enable** signalisiert dem Befehl, die Funktion zu deaktivieren. Ähnlich wie die Installation kann auch das Deaktivieren der automatisierten Sicherung mehrere Minuten dauern.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Beispielskript

Das folgende Skript stellt einen Satz von Variablen bereit, die Sie anpassen können, um die automatisierte Sicherung für Ihren virtuellen Computer zu aktivieren und zu konfigurieren. Unter Umständen müssen Sie das Skript an Ihre individuellen Anforderungen anpassen. So müssen Sie das Skript beispielsweise ändern, wenn Sie die Sicherung von Systemdatenbanken deaktivieren oder die Verschlüsselung aktivieren möchten.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = "Azure region name such as EASTUS2"
$storage_accountname = "storageaccountname"
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL Server IaaS Extension

Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Überwachung

Zur Überwachung der automatisierten Sicherung in SQL Server 2014 stehen Ihnen im Wesentlichen zwei Optionen zur Verfügung. Da die automatisierte Sicherung das SQL Server-Feature Managed Backup verwendet, gelten für beide Optionen die gleichen Überwachungstechniken.

Zum einen können Sie den Status durch Aufruf von [msdb.smart_admin.sp_get_backup_diagnostics](/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql) abrufen. Die andere Möglichkeit besteht darin, die Tabellenwertfunktion [msdb.smart_admin.fn_get_health_status](/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) abzufragen.

> [!NOTE]
> Das Schema für Managed Backup in SQL Server 2014 lautet **msdb.smart_admin**. In SQL Server 2016 wurde dies zu **msdb.managed_backup** geändert, und die Referenzthemen verweisen dieses neuere Schema. Für SQL Server 2014 müssen Sie jedoch für alle Managed Backup-Objekte weiterhin das **smart_admin**-Schema verwenden.

Eine andere Option besteht darin, das integrierte Datenbank-E-Mail-Feature für Benachrichtigungen zu verwenden.

1. Rufen Sie die gespeicherte Prozedur [msdb.smart_admin.sp_set_parameter](/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) auf, um dem Parameter **SSMBackup2WANotificationEmailIds** eine E-Mail-Adresse zuzuweisen. 
1. Aktivieren Sie [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md), um die E-Mails von der Azure-VM zu senden.
1. Verwenden Sie den SMTP-Server und den Benutzernamen, um das Datenbank-E-Mail-Feature zu konfigurieren. Sie können das Datenbank-E-Mail-Feature in SQL Server Management Studio oder mithilfe von Transact-SQL-Befehlen konfigurieren. Weitere Informationen finden Sie unter [Datenbank-E-Mail](/sql/relational-databases/database-mail/database-mail).
1. [Konfigurieren Sie den SQL Server-Agent für die Verwendung von Datenbank-E-Mail](/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Stellen Sie sicher, dass der SMTP-Port sowohl in der lokalen VM-Firewall als auch in der Netzwerksicherheitsgruppe für die VM zugelassen ist.

## <a name="next-steps"></a>Nächste Schritte

Die automatisierte Sicherung konfiguriert Managed Backup für Azure-VMs. Lesen Sie daher unbedingt die [Dokumentation für Managed Backup in SQL Server 2014](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure).

Weitere Informationen zur Sicherung und Wiederherstellung für SQL Server auf Azure-VMs finden Sie im folgenden Artikel: [Sicherung und Wiederherstellung für SQL Server auf Azure-VMs](backup-restore.md).

Informationen zu anderen verfügbaren Automatisierungsaufgaben finden Sie unter [SQL Server-Agent-Erweiterung für virtuelle SQL Server-Computer (klassisch)](sql-server-iaas-agent-extension-automate-management.md).

Ausführlichere Informationen zur Ausführung von SQL Server auf virtuellen Azure-Computern finden Sie unter [Übersicht zu SQL Server auf Azure-VMs](sql-server-on-azure-vm-iaas-what-is-overview.md).