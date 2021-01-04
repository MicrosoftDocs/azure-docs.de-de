---
title: Verwalten von StorSimple-Geräten mit AzureRM PowerShell-Skripts
description: Hier erfahren Sie, wie Sie auf dem Azure Resource Manager-SDK basierende Skripts verwenden, um Ihr Gerät der StorSimple 8000-Serie zu verwalten.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: e3339f883d177366a64187e0c090752527802c8d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968773"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Verwalten von StorSimple-Geräten mit SDK-basierten Azure Resource Manager-Skripts

In diesem Artikel wird beschrieben, wie Geräte der StorSimple 8000-Serie mit SDK-basierten Azure Resource Manager-Skripts verwaltet werden können. Auch ein Beispielskript wird bereitgestellt, um Ihnen die Schritte zum Konfigurieren Ihrer Umgebung für die Ausführung dieser Skripts zu zeigen.

Dieser Artikel gilt nur für Geräte der StorSimple 8000-Serie, die im Azure-Portal ausgeführt werden.

## <a name="sample-scripts"></a>Beispielskripts

Zum Automatisieren verschiedener StorSimple-Aufträge stehen folgende Beispielskripts zur Verfügung.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Tabelle mit SDK-basierten Azure Resource Manager-Beispielskripts

| Azure Resource Manager-Skript                    | BESCHREIBUNG                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Authorize-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Mit diesem Skript können Sie Ihr StorSimple-Gerät autorisieren, um den Datenverschlüsselungsschlüssel für den Dienst zu ändern.                                                                                                           |
| [Create-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Dieses Skript erstellt ein Gerät der Serie StorSimple Cloud Appliance 8010 oder 8020. Das Cloudgerät kann dann konfiguriert und bei Ihrem StorSimple-Daten-Manager-Dienst registriert werden.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Dieses Skript erstellt oder ändert StorSimple-Volumes.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Dieses Skript listet alle Sicherungen für ein Gerät auf, das bei Ihrem StorSimple-Geräte-Manager-Dienst registriert ist.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Dieses Skript listet alle Sicherungsrichtlinien für Ihr StorSimple-Gerät auf.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Dieses Skript ruft alle StorSimple-Aufträge ab, die in Ihrem StorSimple-Geräte-Manager-Dienst ausgeführt werden.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Dieses Skript überprüft den Updateserver und informiert Sie darüber, ob Updates zur Installation auf Ihrem StorSimple-Gerät verfügbar sind.                                                                                          |
| [Install-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Dieses Skript installiert die verfügbaren Updates auf Ihrem StorSimple-Gerät.                                                                                                                                           |
| [Manage-CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Dieses Skript startet eine manuelle Cloudmomentaufnahme und löscht Cloudmomentaufnahmen, die älter sind als die angegebene Beibehaltungsdauer in Tagen.                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Dieses PowerShell-Skript des Azure Automation-Runbooks meldet den Status aller Sicherungsaufträge.                                                                                                              |
| [Remove-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Dieses Skript löscht ein einzelnes Sicherungsobjekt.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Dieses Skript startet eine manuelle Sicherung auf Ihrem StorSimple-Gerät.                                                                                                                                       |
| [Update-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Dieses Skript aktualisiert den Datenverschlüsselungsschlüssel für den Dienst für alle Geräte der Serie 8010/8020 StorSimple Cloud Appliance, die bei Ihrem StorSimple-Geräte-Manager-Dienst registriert sind.                                     |
| [Verify-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Dieses Skript gibt die fehlenden Sicherungen an, nachdem alle mit Sicherungsrichtlinien verknüpften Zeitpläne analysiert wurden. Zudem wird der Sicherungskatalog mit der Liste der verfügbaren Sicherungen überprüft.             |




## <a name="configure-and-run-a-sample-script"></a>Konfigurieren und Ausführen eines Beispielskripts

Dieser Abschnitt beschreibt anhand eines Beispielskripts die verschiedenen Schritte, die zum Ausführen des Skripts erforderlich sind.

### <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen stellen Sie sicher, dass Sie über Folgendes verfügen:

*   Azure PowerShell ist installiert. So installieren Sie Azure PowerShell-Module
    * Führen Sie in einer Windows-Umgebung die Schritte unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-az-ps) durch. Sie können Azure PowerShell auf Ihrem Windows Server-Host für Ihr StorSimple-Gerät installieren, sofern Sie eines verwenden.
    * Führen Sie in einer Linux- oder macOS-Umgebung die Schritte unter [Installieren und Konfigurieren von Azure PowerShell unter macOS oder Linux](/powershell/azure/install-az-ps) durch.

Weitere Informationen zur Verwendung von Azure PowerShell finden Sie im Artikel [Erste Schritte mit Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="run-azure-powershell-script"></a>Ausführen eines Azure PowerShell-Skripts

Das in diesem Beispiel verwendete Skript listet alle Aufträge auf einem StorSimple-Gerät auf. Hierzu zählen Aufträge, die erfolgreich oder fehlerhaft waren oder noch ausgeführt werden. Führen Sie die folgenden Schritte durch, um das Skript herunterzuladen und auszuführen.

1. Starten Sie Azure PowerShell. Erstellen Sie einen neuen Ordner, und ändern Sie das Verzeichnis in den neuen Ordner.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Laden Sie die NuGet-CLI in den Ordner herunter](https://www.nuget.org/downloads), den Sie im vorherigen Schritt erstellt haben. Es gibt verschiedene Versionen von _nuget.exe_. Wählen Sie die für Ihr SDK entsprechende Version aus. Jeder Downloadlink verweist direkt auf eine _EXE_-Datei. Führen Sie die Datei nicht über den Browser aus, sondern klicken Sie mit der rechten Maustaste auf die Datei, und speichern Sie sie auf Ihrem Computer.

    Sie können auch den folgenden Befehl ausführen, um das Skript in den zuvor erstellten Ordner herunterzuladen und zu speichern.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Laden Sie das abhängige SDK herunter.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Laden Sie das Skript aus dem GitHub-Beispielprojekt herunter.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Führen Sie das Skript aus. Geben Sie bei der Aufforderung zur Authentifizierung Ihre Azure-Anmeldeinformationen an. Dieses Skript sollte eine gefilterte Liste aller Aufträge auf Ihrem StorSimple-Gerät ausgegeben.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Beispielausgabe

Bei der Ausführung des Beispielskripts wird folgende Ausgabe angezeigt. Die Ausgabe enthält alle Aufträge, die vom 25. September 2017 bis zum 2. Oktober 2017 auf einem registrierten Gerät ausgeführt wurden.

```
-----------------------------------------
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1
PS C:\Scripts\StorSimpleSDKTools> .\Get-DeviceJobs.ps1 -SubscriptionId 1234ab5c-678d-910e-9fc4-0accc9c0166e -TenantId 12a345bc-67d8-91ef-01ab-2c7cd123ef45 -DeviceName 8600-ABC1234567D89EF -ResourceGroupName Contoso -ManagerName ContosoDeviceMgr -FilterByStartTime "09/25/2017 08:10:02" -FilterByEndTime "10/02/2017 08:10:02"


Status            : Succeeded
StartTime         : 10/2/2017 10:30:02 PM
EndTime           : 10/2/2017 10:31:36 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : ss-asr-policy1
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC/jobs/75905c48-b153-4af1-8b21-4b9a2ff9
                    825b
Name              : 75905c48-b153-4af1-8b21-4b9a2ff9825b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000
-------------------------------------------
CUT              CUT  
-------------------------------------------
Status            : Succeeded
StartTime         : 9/26/2017 5:00:02 PM
EndTime           : 9/26/2017 5:01:20 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : 8010 policy
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF/jobs/3cfd8108-db60-4e9a-a8da-6d8fe457
                    8d2b
Name              : 3cfd8108-db60-4e9a-a8da-6d8fe4578d2b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000



PS C:\Scripts\StorSimpleSDKTools>
--------------------------------------------

```


## <a name="next-steps"></a>Nächste Schritte

[Verwalten Sie Ihr StorSimple-Gerät mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manager-service-administration.md).