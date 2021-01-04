---
title: Automatisierte Sicherung v2 für virtuelle Azure-Computer mit SQL Server 2016/2017 | Microsoft-Dokumentation
description: In diesem Artikel wird das Feature „Automatisierte Sicherung“ für in Azure ausgeführte VMs mit SQL Server 2016/2017 erläutert. Dieser Artikel bezieht sich speziell auf VMs, die das Resource Manager-Modell verwenden.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f41614d54dc4320f683f406b2882a7b388bb4c3d
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358417"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Automatisierte Sicherung v2 für virtuelle Azure-Computer (Resource Manager)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [SQL Server 2014](automated-backup-sql-2014.md)
> * [SQL Server 2016 und höher](automated-backup.md)

Die automatisierte Sicherung (v2) konfiguriert automatisch [SQL Server Managed Backup für Microsoft Azure](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure) für alle vorhandenen und neuen Datenbanken auf einer Azure-VM, auf der die Standard-, Enterprise- oder Developer-Version von SQL Server 2016 oder höher ausgeführt wird. Dies bietet Ihnen die Möglichkeit, reguläre Datenbanksicherungen zu konfigurieren, die permanenten Azure Blob Storage nutzen. Die automatisierte Sicherung v2 basiert auf der [SQL Server-IaaS-Agent-Erweiterung (Infrastructure-as-a-Service)](sql-server-iaas-agent-extension-automate-management.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Voraussetzungen
Um die automatisierte Sicherung v2 verwenden zu können, müssen die folgenden Voraussetzungen erfüllt sein:

**Betriebssystem**:

- Windows Server 2012 R2 oder höher

**SQL Server-Version/-Edition:**

- SQL Server 2016 oder höher: Developer, Standard oder Enterprise

> [!NOTE]
> Für SQL Server 2014 finden Sie die Informationen unter [Automatisierte Sicherung für SQL Server 2014-VMs (Resource Manager)](automated-backup-sql-2014.md).

**Datenbankkonfiguration:**

- _Zielbenutzerdatenbanken_ müssen das vollständige Wiederherstellungsmodell verwenden. Für Systemdatenbanken muss nicht das vollständige Wiederherstellungsmodell verwendet werden. Wenn Sie allerdings Protokollsicherungen für das Modell oder für msdb benötigen, müssen Sie das vollständige Wiederherstellungsmodell verwenden. Weitere Informationen zu den Auswirkungen des vollständigen Wiederherstellungsmodells auf Sicherungen finden Sie unter [Sichern beim vollständigen Wiederherstellungsmodell](/previous-versions/sql/sql-server-2008-r2/ms190217(v=sql.105)). 
- Die SQL Server-VM wurde mit der SQL-IaaS-Agent-Erweiterung im [Verwaltungsmodus „Vollständig“](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full) registriert. 
-  Die automatisierte Sicherung basiert auf der vollständigen [Erweiterung für den SQL Server-IaaS-Agent](sql-server-iaas-agent-extension-automate-management.md). Daher wird die automatisierte Sicherung nur für Zieldatenbanken von der Standardinstanz oder eine einzelne benannte Instanz unterstützt. Wenn keine Standardinstanz und mehrere benannte Instanzen vorhanden sind, kann die SQL-IaaS-Erweiterung nicht ausgeführt werden, und die automatisierte Sicherung funktioniert nicht. 

## <a name="settings"></a>Einstellungen
In der folgenden Tabelle werden die Optionen beschrieben, die für die automatisierte Sicherung v2 konfiguriert werden können. Die tatsächlichen Konfigurationsschritte variieren abhängig davon, ob Sie das Azure-Portal oder Azure Windows PowerShell-Befehle verwenden.

### <a name="basic-settings"></a>Basic Settings

| Einstellung | Bereich (Standard) | BESCHREIBUNG |
| --- | --- | --- |
| **Automatisierte Sicherung** | Aktivieren/Deaktivieren (deaktiviert) | Aktiviert oder deaktiviert die automatisierte Sicherung für einen virtuellen Azure-Computer mit SQL Server 2016/2017 Developer, Standard oder Enterprise. |
| **Aufbewahrungszeitraum** | 1 bis 30 Tage (30 Tage) | Die Anzahl von Tagen, für die Sicherungen aufbewahrt werden. |
| **Speicherkonto** | Azure-Speicherkonto | Ein Azure-Speicherkonto, mit dem Dateien der automatisierten Sicherung im Blob-Speicher gespeichert werden. An diesem Speicherort wird ein Container zum Speichern aller Sicherungsdateien erstellt. Die Namenskonvention für die Sicherungsdatei enthält das Datum, die Uhrzeit sowie die Datenbank-GUID. |
| **Verschlüsselung** |Aktivieren/Deaktivieren (deaktiviert) | Aktiviert oder deaktiviert die Verschlüsselung. Wenn die Verschlüsselung aktiviert ist, befinden sich die Zertifikate zum Wiederherstellen der Sicherung im angegebenen Speicherkonto. Dabei wird derselbe **Automatische Sicherung**-Container mit der gleichen Namenskonvention verwendet. Wenn das Kennwort geändert wird, wird ein neues Zertifikat mit diesem Kennwort generiert, das alte Zertifikat bleibt jedoch zum Wiederherstellen vorheriger Sicherungen erhalten. |
| **Kennwort** |Kennworttext | Ein Kennwort für Verschlüsselungsschlüssel. Dieses Kennwort ist nur erforderlich, wenn die Verschlüsselung aktiviert ist. Um eine verschlüsselte Sicherung wiederherzustellen, benötigen Sie das richtige Kennwort und das zugehörige Zertifikat, das beim Erstellen der Sicherung verwendet wurde. |

### <a name="advanced-settings"></a>Erweiterte Einstellungen

| Einstellung | Bereich (Standard) | BESCHREIBUNG |
| --- | --- | --- |
| **System Database Backups** (Systemdatenbanksicherungen) | Aktivieren/Deaktivieren (deaktiviert) | Ist dieses Feature aktiviert, werden auch die Systemdatenbanken gesichert: Master, MSDB und Modell. Vergewissern Sie sich bei der MSDB- und der Modelldatenbank, dass sie sich im vollständigen Wiederherstellungsmodus befinden, wenn Sie Protokollsicherungen erstellen möchten. Für die Masterdatenbank werden niemals Protokollsicherungen erstellt. Und für die TempDB-Datenbank werden gar keine Sicherungen ausgeführt. |
| **Sicherungszeitplan** | Manuell/Automatisiert (automatisch) | Der Sicherungszeitplan wird standardmäßig automatisch auf der Grundlage des Protokollwachstums festgelegt. Bei einem manuellen Sicherungszeitplan kann der Benutzer das gewünschte Zeitfenster für Sicherungen angeben. In diesem Fall werden Sicherungen ausschließlich gemäß dem angegebenen Intervall und während des angegebenen Zeitfensters eines bestimmten Tags durchgeführt. |
| **Full backup frequency** (Intervall für vollständige Sicherungen) | Täglich/Wöchentlich | Intervall für vollständige Sicherungen. In beiden Fällen werden vollständige Sicherungen während des nächsten geplanten Zeitfensters gestartet. Bei Verwendung der wöchentlichen Option können sich die Sicherungen über mehrere Tage erstrecken, bis alle Datenbanken erfolgreich gesichert wurden. |
| **Full backup start time** (Startzeit für vollständige Sicherungen) | 00:00–23:00 (01:00) | Die Startzeit eines bestimmten Tags, an dem eine vollständige Sicherung stattfinden kann. |
| **Full backup time window** (Zeitfenster für vollständige Sicherungen) | 1–23 Stunden (1 Stunde) | Das Zeitfenster eines bestimmten Tags, an dem eine vollständige Sicherung stattfinden kann. |
| **Log backup frequency** (Intervall für Protokollsicherungen) | 5–60 Minuten (60 Minuten) | Intervall für Protokollsicherungen. |

## <a name="understanding-full-backup-frequency"></a>Grundlegendes zum Intervall für vollständige Sicherungen
Es ist wichtig, den Unterschied zwischen täglichen und wöchentlichen vollständigen Sicherungen zu verstehen. Betrachten Sie die folgenden beiden Beispielszenarien.

### <a name="scenario-1-weekly-backups"></a>Szenario 1: Wöchentliche Sicherungen
Sie verfügen über einen virtuellen SQL Server-Computer mit einer Reihe großer Datenbanken.

Am Montag aktivieren Sie die automatisierte Sicherung v2 mit den folgenden Einstellungen:

- Sicherungszeitplan: **Manuell**
- Häufigkeit der vollständigen Sicherung: **Wöchentlich**
- Startzeit für vollständige Sicherung: **01:00**
- Zeitfenster für vollständige Sicherung: **1 Stunde**

Das nächste verfügbare Sicherungszeitfenster ist also am Dienstag ab 1 Uhr für eine Stunde. Zu dieser Zeit beginnt die automatisierte Sicherung damit, Ihre Datenbanken nacheinander zu sichern. In diesem Szenario sind Ihre Datenbanken so groß, dass die vollständige Sicherung nur für die ersten Datenbanken abgeschlossen werden kann. Nach einer Stunde wurden also noch nicht alle Datenbanken gesichert.

In diesem Fall beginnt die automatisierte Sicherung am nächsten Tag (Mittwoch, ab 1 Uhr für eine Stunde) damit, die restlichen Datenbanken zu sichern. Sollten dabei nicht alle Datenbanken gesichert werden können, wird am nächsten Tag zur gleichen Zeit ein erneuter Versuch gestartet. Dies wird fortgesetzt, bis alle Datenbanken erfolgreich gesichert wurden.

Am nächsten Dienstag beginnt die automatisierte Sicherung dann wieder mit der Sicherung aller Datenbanken.

Dieses Szenario zeigt, dass sich die Aktivität der automatisierten Sicherung auf das angegebene Zeitfenster beschränkt und jede Datenbank einmal pro Woche gesichert wird. Es zeigt auch, dass sich Sicherungen über mehrere Tage erstrecken können, falls nicht alle Sicherungen an einem einzelnen Tag abgeschlossen werden können.

### <a name="scenario-2-daily-backups"></a>Szenario 2: Tägliche Sicherungen
Sie verfügen über einen virtuellen SQL Server-Computer mit einer Reihe großer Datenbanken.

Am Montag aktivieren Sie die automatisierte Sicherung v2 mit den folgenden Einstellungen:

- Sicherungszeitplan: Manuell
- Häufigkeit der vollständigen Sicherung: Täglich
- Startzeit für vollständige Sicherung: 22:00
- Zeitfenster für vollständige Sicherung: 6 Stunden

Das nächste verfügbare Sicherungszeitfenster ist also am Montag ab 10 Uhr für sechs Stunden. Zu dieser Zeit beginnt die automatisierte Sicherung damit, Ihre Datenbanken nacheinander zu sichern.

Am Dienstag wird dann ab 10 Uhr erneut für sechs Stunden eine vollständige Sicherung aller Datenbanken durchgeführt.

> [!IMPORTANT]
> Bei der Planung täglicher Sicherungen empfiehlt sich die Verwendung eines großzügigen Zeitfensters, um sicherzustellen, dass alle Datenbanken gesichert werden können. Dies ist besonders wichtig, wenn große Datenmengen gesichert werden müssen.

## <a name="configure-new-vms"></a>Konfigurieren neuer VMs

Verwenden Sie das Azure-Portal zum Konfigurieren der automatisierten Sicherung v2, wenn Sie einen neuen virtuellen Computer mit SQL Server 2016 oder 2017 im Resource Manager-Bereitstellungsmodell erstellen.

Wählen Sie auf der Registerkarte **SQL Server-Einstellungen** unter **Automatisierte Sicherung** die Option **Aktivieren** aus. Auf dem folgenden Screenshot des Azure-Portals sehen Sie die Einstellungen für **Automatisierte SQL-Sicherung**.

![Konfigurieren der automatisierten SQL-Sicherung im Azure-Portal](./media/automated-backup/automated-backup-blade.png)

> [!NOTE]
> Die automatisierte Sicherung v2 ist standardmäßig deaktiviert.

## <a name="configure-existing-vms"></a>Konfigurieren vorhandener VMs

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Für vorhandene virtuelle SQL Server-Computer navigieren Sie zur Ressource [Virtuelle SQL-Computer](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) und klicken dann auf **Sicherungen**, um Ihre automatisierten Sicherungen zu konfigurieren.

![Automatisierte SQL-Sicherung für vorhandene virtuelle Computer](./media/automated-backup/sql-server-configuration.png)


Klicken Sie abschließend auf die Schaltfläche **Übernehmen** am unteren Rand der Einstellungenseite **Sicherungen**, um Ihre Änderungen zu speichern.

Falls Sie die automatisierte Sicherung zum ersten Mal aktivieren, konfiguriert Azure den SQL Server-IaaS-Agent im Hintergrund. Im Azure-Portal wird währenddessen u.U. nicht angezeigt, dass die automatisierte Sicherung konfiguriert wird. Warten Sie einige Minuten, bis der Agent installiert und konfiguriert wurde. Danach werden die neuen Einstellungen im Azure-Portal angezeigt.

## <a name="configure-with-powershell"></a>Konfigurieren mithilfe von PowerShell

Die automatisierte Sicherung v2 kann mithilfe von PowerShell konfiguriert werden. Führen Sie zur Vorbereitung folgende Schritte aus:

- [Laden Sie die aktuelle Version von Azure PowerShell herunter, und installieren Sie sie.](https://aka.ms/webpi-azps)
- Öffnen Sie Windows PowerShell, und stellen Sie mit dem Befehl **Connect-AzAccount** eine Verknüpfung mit Ihrem Konto her.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-server-iaas-extension"></a>Installieren der Erweiterung für SQL Server-IaaS
Wenn Sie einen virtuellen SQL Server-Computer über das Azure-Portal bereitgestellt haben, müsste die SQL Server-IaaS-Erweiterung bereits installiert sein. Indem Sie den Befehl **Get-AzVM** ausführen und sich die Eigenschaft **Extensions** ansehen, können Sie ermitteln, ob dies für Ihre VM bereits der Fall ist.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

Wenn die SQL Server-IaaS-Agent-Erweiterung installiert ist, wird „SqlIaaSAgent“ oder „SQLIaaSExtension“ aufgeführt. Außerdem muss für die Erweiterung unter **ProvisioningState** der Zustand „Succeeded“ angezeigt werden. 

Falls die Erweiterung nicht installiert oder nicht erfolgreich bereitgestellt wurde, können Sie sie mithilfe des folgenden Befehls installieren. Neben dem Namen des virtuellen Computers und der Ressourcengruppe müssen Sie auch die Region ( **$region**) angeben, in der sich Ihr virtueller Computer befindet.

```powershell
$region = "EASTUS2"
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region 
```

### <a name="verify-current-settings"></a><a id="verifysettings"></a> Überprüfen der aktuellen Einstellungen
Wenn Sie die automatisierte Sicherung bei der Bereitstellung aktiviert haben, können Sie über PowerShell Ihre aktuelle Konfiguration überprüfen. Führen Sie den Befehl **Get-AzVMSqlServerExtension** aus, und sehen Sie sich die Eigenschaft **AutoBackupSettings** an:

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Die Ausgabe sollte in etwa wie folgt aussehen:

```
Enable                      : True
EnableEncryption            : False
RetentionPeriod             : 30
StorageUrl                  : https://test.blob.core.windows.net/
StorageAccessKey            :  
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : Manual
FullBackupFrequency         : WEEKLY
FullBackupStartTime         : 2
FullBackupWindowHours       : 2
LogBackupFrequency          : 60
```

Wenn laut der Ausgabe **Enable** auf **False** festgelegt ist, müssen Sie die automatisierte Sicherung aktivieren. Die gute Nachricht: Die Vorgehensweise zum Aktivieren der automatisierten Sicherung unterscheidet sich nicht von der Vorgehensweise zum Konfigurieren. Entsprechende Informationen finden Sie im nächsten Abschnitt.

> [!NOTE] 
> Wenn Sie eine Änderung vorgenommen haben und die Einstellungen direkt danach überprüfen, werden unter Umständen noch die alten Konfigurationswerte zurückgegeben. Warten Sie einige Minuten, und überprüfen Sie die Einstellungen dann erneut, um sich zu vergewissern, dass die Änderungen angewendet wurden.

### <a name="configure-automated-backup-v2"></a>Konfigurieren der automatisierten Sicherung v2
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

Verwenden Sie dann den Befehl **New-AzVMSqlServerAutoBackupConfig**, um die Einstellungen der automatisierten Sicherung v2 so zu konfigurieren, dass Sicherungen in dem Azure Storage-Konto gespeichert werden. In diesem Beispiel ist für die Sicherungen eine Aufbewahrungsdauer von zehn Tagen festgelegt. Systemdatenbanksicherungen sind aktiviert. Vollständige Sicherungen werden wöchentlich ab 20:00 Uhr innerhalb eines zweistündigen Zeitfensters durchgeführt. Für Protokollsicherungen ist ein 30-minütiges Intervall geplant. Der zweite Befehl, **Set-AzVMSqlServerExtension**, aktualisiert den angegebenen virtuellen Azure-Computer mit diesen Einstellungen.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname 
```

Die Installation und Konfiguration des SQL Server-IaaS-Agents kann mehrere Minuten in Anspruch nehmen. 

Um die Verschlüsselung zu aktivieren, ändern Sie das vorherige Skript, um den **EnableEncryption**-Parameter und ein Kennwort (sichere Zeichenfolge) für den **CertificatePassword**-Parameter zu übergeben. Das folgende Skript aktiviert die Einstellungen der automatisierten Sicherung im vorherigen Beispiel und fügt die Verschlüsselung hinzu.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

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
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

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
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Überwachung

Zum Überwachen der automatisierten Sicherung in SQL Server 2016/2017 stehen Ihnen im Wesentlichen zwei Optionen zur Verfügung. Da die automatisierte Sicherung das SQL Server-Feature Managed Backup verwendet, gelten für beide Optionen die gleichen Überwachungstechniken.

Zum einen können Sie den Status durch Aufruf von [msdb.managed_backup.sp_get_backup_diagnostics](/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql) abrufen. Die andere Möglichkeit besteht darin, die Tabellenwertfunktion [msdb.managed_backup.fn_get_health_status](/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) abzufragen.

Eine andere Option besteht darin, das integrierte Datenbank-E-Mail-Feature für Benachrichtigungen zu verwenden.

1. Rufen Sie die gespeicherte Prozedur [msdb.managed_backup.sp_set_parameter](/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) auf, um dem Parameter **SSMBackup2WANotificationEmailIds** eine E-Mail-Adresse zuzuweisen. 
1. Aktivieren Sie [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md), um die E-Mails von der Azure-VM zu senden.
1. Verwenden Sie den SMTP-Server und den Benutzernamen, um das Datenbank-E-Mail-Feature zu konfigurieren. Sie können das Datenbank-E-Mail-Feature in SQL Server Management Studio oder mithilfe von Transact-SQL-Befehlen konfigurieren. Weitere Informationen finden Sie unter [Datenbank-E-Mail](/sql/relational-databases/database-mail/database-mail).
1. [Konfigurieren Sie den SQL Server-Agent für die Verwendung von Datenbank-E-Mail](/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Stellen Sie sicher, dass der SMTP-Port sowohl in der lokalen VM-Firewall als auch in der Netzwerksicherheitsgruppe für die VM zugelassen ist.

## <a name="next-steps"></a>Nächste Schritte
Die automatisierte Sicherung v2 konfiguriert Managed Backup für virtuelle Azure-Computer. Lesen Sie daher unbedingt die [Dokumentation für Managed Backup](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure) , um sich mit dem Verhalten und den Auswirkungen vertraut zu machen.

Weitere Informationen zur Sicherung und Wiederherstellung für SQL Server auf Azure-VMs finden Sie im folgenden Artikel: [Sicherung und Wiederherstellung für SQL Server auf Azure-VMs](backup-restore.md).

Informationen zu anderen verfügbaren Automatisierungsaufgaben finden Sie unter [SQL Server-Agent-Erweiterung für virtuelle SQL Server-Computer (klassisch)](sql-server-iaas-agent-extension-automate-management.md).

Ausführlichere Informationen zur Ausführung von SQL Server auf virtuellen Azure-Computern finden Sie unter [Übersicht zu SQL Server auf Azure-VMs](sql-server-on-azure-vm-iaas-what-is-overview.md).