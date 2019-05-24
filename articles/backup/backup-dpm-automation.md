---
title: Azure Backup – Verwenden von PowerShell zum Sichern von DPM-Workloads
description: Erfahren Sie, wie Sie Azure Backup für Data Protection Manager (DPM) mithilfe von PowerShell bereitstellen und verwalten.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 1/23/2017
ms.author: adigan
ms.openlocfilehash: b16963265c971e604f03b51fd63f7fe411bab36e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66127760"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Bereitstellen und Verwalten der Sicherung in Azure für Data Protection Manager (DPM)-Server mit PowerShell

In diesem Artikel erfahren Sie, wie Sie PowerShell zum Einrichten von Azure Backup auf einem DPM-Server sowie zum Verwalten von Sicherungen und Wiederherstellungen verwenden.

## <a name="setting-up-the-powershell-environment"></a>Einrichten der PowerShell-Umgebung

Um mit PowerShell Sicherungen von Data Protection Manager in Azure verwalten zu können, benötigen Sie eine geeignete Umgebung in PowerShell. Zu Beginn der PowerShell-Sitzung müssen Sie den folgenden Befehl ausführen, um die richtigen Module zu importieren und korrekt auf die DPM-Cmdlets verweisen zu können:

```powershell
& "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"
```

```Output
Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Einrichtung und Registrierung

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als Erstes [laden Sie die neueste Azure PowerShell herunter](/powershell/azure/install-az-ps).

Die folgenden Installations- und Registrierungsaufgaben können mit PowerShell automatisiert werden:

* Erstellen eines Recovery Services-Tresors
* Installieren des Azure Backup-Agents
* Registrieren beim Azure Backup-Dienst
* Netzwerkeinstellungen
* Verschlüsselungseinstellungen

## <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors

Mit den folgenden Schritten können Sie einen Recovery Services-Tresor erstellen. Ein Recovery Services-Tresor unterscheidet sich von einem Sicherungstresor.

1. Falls Sie Azure Backup zum ersten Mal verwenden, müssen Sie das Cmdlet **Register-AzResourceProvider** verwenden, um den Azure Recovery Service-Anbieter für Ihr Abonnement zu registrieren.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Der Recovery Services-Tresor ist eine ARM-Ressource. Deshalb müssen Sie ihn in eine Ressourcengruppe einfügen. Sie können eine vorhandene Ressourcengruppe verwenden oder eine neue erstellen. Wenn Sie eine neue Ressourcengruppe erstellen, geben Sie den Namen und den Speicherort für die Ressourcengruppe an.

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```

3. Verwenden Sie das Cmdlet **New-AzRecoveryServicesVault** zum Erstellen eines neuen Tresors. Stellen Sie sicher, dass Sie den gleichen Speicherort für den Tresor angeben, der für die Ressourcengruppe verwendet wurde.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Geben Sie den Typ der zu verwendenden Speicherredundanz an – entweder [lokal redundanter Speicher (LRS)](../storage/common/storage-redundancy-lrs.md) oder [geografisch redundanter Speicher (GRS)](../storage/common/storage-redundancy-grs.md). Das folgende Beispiel zeigt, dass für die Option BackupStorageRedundancy für testVault der Wert auf GeoRedundant festgelegt ist.

   > [!TIP]
   > Viele Azure Backup-Cmdlets benötigen das Recovery Services-Tresorobjekt als Eingabe. Aus diesem Grund sollte das zur Sicherung verwendete Recovery Services-Tresorobjekt in einer Variablen gespeichert werden.
   >
   >

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Anzeigen von Tresoren in einem Abonnement

Verwenden Sie **Get-AzRecoveryServicesVault**, um die Liste mit allen Tresoren im aktuellen Abonnement anzuzeigen. Mithilfe dieses Befehls können Sie überprüfen, ob ein neuer Tresor erstellt wurde, oder feststellen, welche Tresore im Abonnement verfügbar sind.

Führen Sie den Befehl „Get-AzRecoveryServicesVault“ aus, damit alle Tresore im Abonnement aufgelistet werden.

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Installieren des Azure Backup-Agents auf einem DPM-Server

Bevor Sie den Azure Backup-Agent installieren, müssen Sie das Installationsprogramm herunterladen, damit es auf dem Windows-Server verfügbar ist. Die neueste Version des Installationsprogramms erhalten Sie im [Microsoft Download Center](https://aka.ms/azurebackup_agent) oder im Dashboard des Recovery Services-Tresors. Speichern Sie das Installationsprogramm an einem leicht zugänglichen Speicherort wie *C:\Downloads\*.

Um den Agent zu installieren, führen Sie den folgenden Befehl **auf dem DPM-Server**in einer PowerShell-Konsole mit erhöhten Rechten aus:

```powershell
MARSAgentInstaller.exe /q
```

Dadurch wird der Agent mit allen Standardoptionen installiert. Der Installationsvorgang im Hintergrund dauert einige Minuten. Wenn Sie die */nu* -Option nicht angeben, wird am Ende der Installation das Fenster **Windows Update** geöffnet, um nach Updates zu suchen.

Der Agent wird in der Liste mit den installierten Programmen angezeigt. Um die Liste der installierten Programme anzuzeigen, wechseln Sie zu **Systemsteuerung** > **Programme** > **Programme und Funktionen**.

![Agent installiert](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Installationsoptionen

Verwenden Sie den folgenden Befehl, um alle über die Befehlszeile verfügbaren Optionen anzuzeigen:

```powershell
MARSAgentInstaller.exe /?
```

Die verfügbaren Optionen umfassen:

| Option | Details | Standard |
| --- | --- | --- |
| /q |Installation im Hintergrund |- |
| /p:"location" |Pfad zum Installationsordner für den Azure Backup-Agent |C:\Programme\Microsoft Azure Recovery Services Agent |
| /s:"location" |Pfad zum Cacheordner für den Azure Backup-Agent |C:\Programme\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Microsoft Update abonnieren |- |
| /nu |Nach Abschluss der Installation nicht nach Updates suchen |- |
| /d |Microsoft Azure Recovery Services-Agent deinstallieren |- |
| /ph |Proxyhostadresse |- |
| /po |Proxyhost-Portnummer |- |
| /pu |Proxyhost-Benutzername |- |
| /pw |Proxykennwort |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Registrieren von DPM bei einem Recovery Services-Tresor

Laden Sie nach dem Erstellen des Recovery Services-Tresors den neuesten Agent sowie die Tresoranmeldeinformationen herunter, und speichern Sie sie an einem zentralen Ort wie C:\Downloads.

```powershell
$credspath = "C:\downloads"
$credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
$credsfilename
```

```Output
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

Führen Sie auf dem DPM-Server das Cmdlet [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) aus, um den Computer beim Tresor zu registrieren.

```powershell
$cred = $credspath + $credsfilename
Start-OBRegistration-VaultCredentials $cred -Confirm:$false
```

```Output
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Anfängliche Konfigurationseinstellungen

Wenn der DPM-Server im Recovery Services-Tresor registriert ist, wird er mit den standardmäßigen Abonnementeinstellungen gestartet. Diese Abonnementeinstellungen umfassen Netzwerk, Verschlüsselung und den Stagingbereich. Wenn Sie die Abonnementeinstellungen ändern möchten, müssen Sie zunächst mithilfe des Cmdlets [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793) ein Handle für die vorhandenen (standardmäßigen) Einstellungen abrufen:

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Alle Änderungen werden an diesem lokalen PowerShell-Objekt ```$setting``` vorgenommen, und anschließend wird das vollständige Objekt zum Speichern der Änderungen mit dem Cmdlet [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) per Commit in DPM und Azure Backup übernommen. Sie müssen das ```–Commit``` -Kennzeichen verwenden, um sicherzustellen, dass die Änderungen gespeichert werden. Die Einstellungen werden erst angewendet und von Azure Backup verwendet, wenn ein Commit ausgeführt wurde.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Netzwerk

Wenn die Verbindung des DPM-Computers mit dem Azure Backup-Dienst im Internet über einen Proxyserver hergestellt wird, müssen die Proxyservereinstellungen angegeben werden, damit die Sicherungen erfolgreich erstellt werden. Dazu werden die Parameter ```-ProxyServer```, ```-ProxyPort```, ```-ProxyUsername``` und ```ProxyPassword``` mit dem Cmdlet [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) verwendet. In diesem Beispiel gibt es keinen Proxyserver, daher werden alle Proxy-bezogenen Informationen explizit gelöscht.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Die Bandbreitennutzung kann auch mit Optionen für ```-WorkHourBandwidth``` und ```-NonWorkHourBandwidth``` für eine bestimmte Anzahl von Tagen der Woche gesteuert werden. In diesem Beispiel wird keine Drosselung festgelegt.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Konfigurieren des Stagingbereichs

Der Azure Backup-Agent auf dem DPM-Server benötigt temporären Speicher für Daten, die aus der Cloud wiederhergestellt werden (Stagingbereich). Konfigurieren Sie den Stagingbereich mit dem [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791)-Cmdlet und dem Parameter ```-StagingAreaPath```.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

Im obigen Beispiel wird der Stagingbereich im PowerShell-Objekt ```$setting``` auf *C:\StagingArea* festgelegt. Stellen Sie sicher, dass der angegebene Ordner bereits vorhanden ist. Andernfalls schlägt der endgültige Commit für die Abonnementeinstellungen fehl.

### <a name="encryption-settings"></a>Verschlüsselungseinstellungen

Die Sicherungsdaten, die an Azure Backup gesendet werden, werden verschlüsselt, um die Vertraulichkeit der Daten zu schützen. Die Verschlüsselungspassphrase ist das "Kennwort" zum Entschlüsseln der Daten zum Zeitpunkt der Wiederherstellung. Es ist wichtig, diese Informationen nach dem Festlegen zu sichern.

Im folgenden Beispiel konvertiert der erste Befehl die Zeichenfolge ```passphrase123456789``` in eine sichere Zeichenfolge und weist die sichere Zeichenfolge der Variablen namens ```$Passphrase``` zu. Der zweite Befehl legt die sichere Zeichenfolge in ```$Passphrase``` als Kennwort für die Verschlüsselung von Sicherungen fest.

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Sichern Sie die Passphraseninformationen, nachdem Sie sie festgelegt haben. Es ist nicht möglich, Daten aus Azure ohne diese Passphrase wiederherzustellen.
>
>

Jetzt sollten Sie alle erforderlichen Änderungen am ```$setting``` -Objekt vorgenommen haben. Denken Sie daran, einen Commit für die Änderungen auszuführen:

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Schützen von Daten auf Azure Backup

In diesem Abschnitt fügen Sie DPM einen Produktionsserver hinzu, und anschließend schützen Sie die Daten im lokalen DPM-Speicher und auf Azure Backup. In den Beispielen wird das Sichern von Dateien und Ordnern veranschaulicht. Die Logik kann problemlos erweitert werden, um beliebige von DPM unterstützte Datenquellen zu sichern. Alle DPM-Sicherungen werden durch eine Schutzgruppe (Protection Group, PG) mit vier Teilen gesteuert:

1. **Gruppenmitglieder** ist eine Liste der zu schützenden Objekte (in DPM auch als *Datenquellen* bezeichnet), die in der gleichen Schutzgruppe geschützt werden sollen. Sie können aufgrund der unterschiedlichen Sicherungsanforderungen z. B. Produktions-VMs in einer Schutzgruppe und SQL-Serverdatenbanken in einer anderen Schutzgruppe schützen. Bevor Sie eine Datenquelle auf einem Produktionsserver sichern können, müssen Sie sicherstellen, dass der DPM-Agent auf dem Server installiert ist und von DPM verwaltet wird. Führen Sie die Schritte zum [Installieren des DPM-Agents](https://technet.microsoft.com/library/bb870935.aspx) und Verknüpfen des DPM-Agents mit dem entsprechenden DPM-Server aus.
2. Die **Datenschutzmethode** gibt die Zielspeicherorte für Sicherungen an, d.h. Band, Datenträger und Cloud. In diesem Beispiel werden Daten auf dem lokalen Datenträger und in der Cloud geschützt.
3. Ein **Sicherungszeitplan** gibt an, wann Sicherungen ausgeführt und wie oft die Daten zwischen dem DPM-Server und dem Produktionsserver synchronisiert werden müssen.
4. Ein **Aufbewahrungszeitplan** , der angibt, wie lange die Wiederherstellungspunkte in Azure beibehalten werden sollen.

### <a name="creating-a-protection-group"></a>Erstellen einer Schutzgruppe

Erstellen Sie zunächst mithilfe des [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722) -Cmdlets eine neue Schutzgruppe.

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Das obige Cmdlet erstellt eine Schutzgruppe mit dem Namen *ProtectGroup01*. Eine vorhandene Schutzgruppe kann später auch geändert werden, um die Sicherung in der Azure Cloud hinzuzufügen. Um Änderungen an einer (neuen oder vorhandenen) Schutzgruppe vorzunehmen, müssen wir jedoch ein Handle für ein *änderbares* Objekt mit dem [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713) -Cmdlet abrufen.

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Hinzufügen von Gruppenmitgliedern zur Schutzgruppe

Jeder DPM-Agent kennt die Liste der Datenquellen auf dem Server, auf dem er installiert ist. Um der Schutzgruppe eine Datenquelle hinzuzufügen, muss der DPM-Agent zunächst eine Liste mit den Datenquellen zurück an den DPM-Server senden. Anschließend werden eine oder mehrere Datenquellen ausgewählt und der Schutzgruppe hinzugefügt. Hierzu müssen in PowerShell die folgenden Schritte ausgeführt werden:

1. Rufen Sie eine Liste aller von DPM verwalteten Server über den DPM-Agent ab.
2. Wählen Sie einen Server aus.
3. Rufen Sie eine Liste aller Datenquellen auf dem Server ab.
4. Wählen Sie eine oder mehrere Datenquellen aus, und fügen Sie sie der Schutzgruppe hinzu.

Die Liste der Server, auf denen der DPM-Agent installiert ist und vom DPM-Server verwaltet wird, wird mit dem [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600) -Cmdlet abgerufen. In diesem Beispiel filtern wir, um nur den Produktionsserver mit dem Namen *productionserver01* für die Sicherung zu konfigurieren.

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains “productionserver01”}
```

Rufen Sie jetzt die Liste der Datenquellen auf ```$server``` mit dem [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605)-Cmdlet ab. In diesem Beispiel filtern wir nach Volume *D:\\*, das wir für die Sicherung konfigurieren möchten. Diese Datenquelle wird der Schutzgruppe dann mithilfe des [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732)-Cmdlets hinzugefügt. Denken Sie daran, beim Hinzufügen das *änderbare* Schutzgruppenobjekt ```$MPG``` zu verwenden.

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains “D:\” }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Wiederholen Sie diesen Schritt, bis Sie der Schutzgruppe alle ausgewählten Datenquellen hinzugefügt haben. Sie können auch mit einer Datenquelle beginnen, die Schritte zum Erstellen der Schutzgruppe durchführen und der Schutzgruppe später weitere Datenquellen hinzufügen.

### <a name="selecting-the-data-protection-method"></a>Auswählen der Datenschutzmethode

Nachdem die Datenquellen der Schutzgruppe hinzugefügt wurden, legen Sie als Nächstes mithilfe des [Set-DPMProtectionType](https://technet.microsoft.com/library/hh881725) -Cmdlets die Schutzmethode fest. In diesem Beispiel wird die Schutzgruppe für die Sicherung auf dem lokalen Datenträger und in der Cloud eingerichtet. Sie müssen auch die zu schützende Datenquelle mit dem Cmdlet [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx) und dem "-Online"-Flag angeben.

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Festlegen der Beibehaltungsdauer

Legen Sie die Beibehaltungsdauer für die Sicherungspunkte mithilfe des [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) -Cmdlets fest. Es erscheint ungewöhnlich, die Beibehaltungsdauer vor dem Definieren des Sicherungszeitplans festzulegen, durch die Verwendung des ```Set-DPMPolicyObjective``` -Cmdlets wird jedoch automatisch ein Standardsicherungszeitplan festgelegt, der anschließend geändert werden kann. Es ist immer möglich, zuerst den Sicherungszeitplan und danach die Aufbewahrungsrichtlinie festzulegen.

Im folgenden Beispiel legt das Cmdlet die Aufbewahrungsparameter für Sicherungen auf Datenträger fest. Durch diese Einstellungen werden Sicherungen 10 Tage beibehalten und Daten alle 6 Stunden zwischen dem Produktionsserver und dem DPM-Server synchronisiert. ```SynchronizationFrequencyMinutes``` definiert nicht, wie oft ein Sicherungspunkt erstellt wird, sondern wie häufig Daten auf den DPM-Server kopiert werden.  Dadurch wird verhindert, dass Sicherungen zu groß werden.

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Für Sicherungen in Azure (in DPM werden diese als Onlinesicherungen bezeichnet) kann die Beibehaltungsdauer für eine [langfristige Aufbewahrung nach GFS-Schema (Grandfather-Father-Son)](backup-azure-backup-cloud-as-tape.md)konfiguriert werden. Sie können also eine kombinierte Aufbewahrungsrichtlinie mit täglichen, wöchentlichen, monatlichen und jährlichen Aufbewahrungsrichtlinien definieren. In diesem Beispiel erstellen wir ein Array, das das gewünschte komplexe Aufbewahrungsschema darstellt, und anschließend konfigurieren wir die Beibehaltungsdauer mit dem [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) -Cmdlet.

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Festlegen des Sicherungszeitplans

DPM legt automatisch einen Standardzeitplan fest, wenn Sie das Schutzziel mithilfe des ```Set-DPMPolicyObjective``` -Cmdlets angeben. Um die Standardzeitpläne zu ändern, verwenden Sie das [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749)-Cmdlet gefolgt vom [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723)-Cmdlet.

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

Im obigen Beispiel ist ```$onlineSch``` ein Array mit vier Elementen, das den vorhandenen Onlineschutzzeitplan für die Schutzgruppe im GFS-Schema enthält:

1. ```$onlineSch[0]``` enthält den täglichen Zeitplan.
2. ```$onlineSch[1]``` enthält den wöchentlichen Zeitplan.
3. ```$onlineSch[2]``` enthält den monatlichen Zeitplan.
4. ```$onlineSch[3]``` enthält den jährlichen Zeitplan.

Wenn Sie den wöchentlichen Zeitplan ändern möchten, müssen Sie folglich auf ```$onlineSch[1]```verweisen.

### <a name="initial-backup"></a>Erste Sicherung

Wenn Sie eine Datenquelle zum ersten Mal sichern, erstellt DPM ein erstes Replikat, das eine vollständige Kopie der zu schützenden Datenquelle auf dem DPM-Replikatvolume erstellt. Diese Aktivität kann entweder für einen bestimmten Zeitpunkt geplant oder mit dem [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715)-Cmdlet mit dem Parameter ```-NOW``` manuell ausgelöst werden.

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Ändern der Größe des DPM-Replikat- und des Wiederherstellungspunktvolumes

Sie können auch die Größe des DPM-Replikatvolumes und Schattenkopievolumes mithilfe des Cmdlets [Set-DPMDatasourceDiskAllocation](https://technet.microsoft.com/library/hh881618.aspx) ändern. Beispiel: Get-DatasourceDiskAllocation -Datasource $DS Set-DatasourceDiskAllocation -Datasource $DS -ProtectionGroup $MPG -manual -ReplicaArea (2gb) -ShadowCopyArea (2gb)

### <a name="committing-the-changes-to-the-protection-group"></a>Commit für Änderungen an der Schutzgruppe

Zum Schluss muss für die Änderungen ein Commit ausgeführt werden, bevor DPM die Sicherung für die neue Schutzgruppenkonfiguration durchführen kann. Hierzu können Sie das Cmdlet [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758) verwenden.

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>Anzeigen der Sicherungspunkte

Sie können das [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746) -Cmdlet verwenden, um eine Liste aller Wiederherstellungspunkte für eine Datenquelle abzurufen. In diesem Beispiel werden wir:

* alle Schutzgruppen auf dem DPM-Server aufrufen, die in einem Array vom Typ ```$PG```
* die Datenquellen abrufen, die ```$PG[0]```
* alle Wiederherstellungspunkte für eine Datenquelle abrufen.

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Wiederherstellen von Daten, die auf Azure geschützt werden

Zum Wiederherstellen von Daten wird eine Kombination aus einem ```RecoverableItem```-Objekt und einem ```RecoveryOption```-Objekt verwendet. Im vorherigen Abschnitt haben wir eine Liste der Sicherungspunkte für eine Datenquelle abgerufen.

Das folgende Beispiel zeigt, wie Sie eine virtuelle Hyper-V-Maschine in Azure Backup wiederherstellen, indem Sie Sicherungspunkte mit dem Wiederherstellungsziel kombinieren. Dieses Beispiel umfasst Folgendes:

* Erstellen einer Wiederherstellungsoption mit dem [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592)-Cmdlet
* Abrufen des Arrays von Sicherungspunkten mit dem ```Get-DPMRecoveryPoint```-Cmdlet
* Auswählen eines Sicherungspunkts für die Wiederherstellung

```powershell
$RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “C:\VMRecovery”

$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Die Befehle können mühelos für beliebige Datenquellentypen erweitert werden.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Verwendung von DPM mit Azure Backup finden Sie unter [Einführung in DPM Backup](backup-azure-dpm-introduction.md)
