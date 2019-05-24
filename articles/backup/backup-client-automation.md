---
title: Verwenden von PowerShell zum Sichern von Windows Server in Azure
description: Erfahren Sie, wie Sie Azure Backup mit PowerShell bereitstellen und verwalten.
services: backup
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 5/24/2018
ms.author: pvrk
ms.openlocfilehash: 6280ca55023fc604e70b62cabdc30cca6409d9e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66127805"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Bereitstellen und Verwalten der Sicherung in Azure für Windows Server-/Windows-Clientcomputer mit PowerShell

In diesem Artikel erfahren Sie, wie Sie PowerShell zum Einrichten von Azure Backup auf einem Windows-Server oder Windows-Client sowie zum Verwalten von Sicherungen und Wiederherstellungen verwenden.

## <a name="install-azure-powershell"></a>Installieren von Azure Powershell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als Erstes [installieren Sie die neueste PowerShell-Version](/powershell/azure/install-az-ps).

## <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors

Mit den folgenden Schritten können Sie einen Recovery Services-Tresor erstellen. Ein Recovery Services-Tresor unterscheidet sich von einem Sicherungstresor.

1. Falls Sie Azure Backup zum ersten Mal verwenden, müssen Sie das Cmdlet **Register-AzResourceProvider** verwenden, um den Azure Recovery Service-Anbieter für Ihr Abonnement zu registrieren.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Der Recovery Services-Tresor ist eine ARM-Ressource. Deshalb müssen Sie ihn in eine Ressourcengruppe einfügen. Sie können eine vorhandene Ressourcengruppe verwenden oder eine neue erstellen. Wenn Sie eine neue Ressourcengruppe erstellen, geben Sie den Namen und den Speicherort für die Ressourcengruppe an.  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. Verwenden Sie das Cmdlet **New-AzRecoveryServicesVault** zum Erstellen des neuen Tresors. Stellen Sie sicher, dass Sie den gleichen Speicherort für den Tresor angeben, der für die Ressourcengruppe verwendet wurde.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. Geben Sie den Typ der zu verwendenden Speicherredundanz an – entweder [lokal redundanter Speicher (LRS)](../storage/common/storage-redundancy-lrs.md) oder [geografisch redundanter Speicher (GRS)](../storage/common/storage-redundancy-grs.md). Das folgende Beispiel zeigt, dass für die Option BackupStorageRedundancy für testVault der Wert auf GeoRedundant festgelegt ist.

   > [!TIP]
   > Viele Azure Backup-Cmdlets benötigen das Recovery Services-Tresorobjekt als Eingabe. Aus diesem Grund sollte das zur Sicherung verwendete Recovery Services-Tresorobjekt in einer Variablen gespeichert werden.
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Anzeigen von Tresoren in einem Abonnement

Verwenden Sie **Get-AzRecoveryServicesVault**, um die Liste mit allen Tresoren im aktuellen Abonnement anzuzeigen. Mithilfe dieses Befehls können Sie überprüfen, ob ein neuer Tresor erstellt wurde, oder um festzustellen, welche Tresore im Abonnement verfügbar sind.

Führen Sie den Befehl **Get-AzRecoveryServicesVault** aus, damit alle Tresore im Abonnement aufgelistet werden.

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


[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="installing-the-azure-backup-agent"></a>Installieren des Azure Backup-Agents

Bevor Sie den Azure Backup-Agent installieren, müssen Sie das Installationsprogramm herunterladen, damit es auf dem Windows-Server verfügbar ist. Die neueste Version des Installationsprogramms erhalten Sie im [Microsoft Download Center](https://aka.ms/azurebackup_agent) oder im Dashboard des Recovery Services-Tresors. Speichern Sie das Installationsprogramm an einem leicht zugänglichen Speicherort wie *C:\Downloads\*.

Alternativ können Sie das Downloadprogramm mithilfe von PowerShell abrufen:
 
 ```powershell
 $MarsAURL = 'https://aka.ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Um den Agent zu installieren, führen Sie den folgenden Befehl in einer PowerShell-Konsole mit erhöhten Rechten aus:

```powershell
MARSAgentInstaller.exe /q
```

Dadurch wird der Agent mit allen Standardoptionen installiert. Der Installationsvorgang im Hintergrund dauert einige Minuten. Wenn Sie die */nu* -Option nicht angeben, wird das Fenster **Windows Update** am Ende geöffnet, um nach Updates zu suchen. Nach der Installation wird der Agent in der Liste der installierten Programme angezeigt.

Um die Liste der installierten Programme anzuzeigen, wechseln Sie zu **Systemsteuerung** > **Programme** > **Programme und Funktionen**.

![Agent installiert](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Installationsoptionen

Um alle über die Befehlszeile verfügbaren Optionen anzuzeigen, verwenden Sie den folgenden Befehl:

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

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Registrieren von Windows-Server oder Windows-Client bei einem Recovery Services-Tresor

Laden Sie nach dem Erstellen des Recovery Services-Tresors den neuesten Agent sowie die Tresoranmeldeinformationen herunter, und speichern Sie sie an einem zentralen Ort wie C:\Downloads.

```powershell
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
```

Führen Sie auf dem Windows-Server oder Windows-Clientcomputer das Cmdlet [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) aus, um den Computer beim Tresor zu registrieren.
Diese und andere Cmdlets, die für die Sicherung verwendet werden, stammen aus dem MSONLINE-Modul, das das Installationsprogramm für den Mars-Agent während des Installationsvorgangs hinzugefügt hat. 

Das Agent-Installationsprogramm aktualisiert nicht die $Env:PSModulePath-Variable. Dies bedeutet, dass automatisch Ladevorgänge für das Modul zu Fehlern führen. Führen Sie folgende Schritte aus, um dieses Problem zu beheben:

```powershell
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

Alternativ können Sie das Modul wie folgt manuell in Ihrem Skript laden:

```powershell
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
```

Nachdem Sie die Onlinesicherungs-Cmdlets geladen haben, registrieren Sie die Tresoranmeldeinformationen:

```powershell
Start-OBRegistration -VaultCredentials $CredsFilename.FilePath -Confirm:$false
```

```Output
CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : testvault
Region              : WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> Verwenden Sie keine relativen Pfade, um die Tresoranmeldedatendatei anzugeben. Sie müssen einen absoluten Pfad als Eingabe für das Cmdlet angeben.
>
>

## <a name="networking-settings"></a>Netzwerkeinstellungen

Wenn die Konnektivität des Windows-Computers mit dem Internet über einen Proxyserver hergestellt wird, können die Proxyeinstellungen auch dem Agent bereitgestellt werden. Da es in diesem Beispiel keinen Proxyserver gibt, löschen wir explizit alle Proxy-bezogenen Informationen.

Die Bandbreitennutzung kann auch mit den Optionen für `work hour bandwidth` und `non-work hour bandwidth` für eine bestimmte Anzahl von Tagen der Woche gesteuert werden.

Das Festlegen von Proxy- und Bandbreitendetails erfolgt mithilfe des [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) -Cmdlets:

```powershell
Set-OBMachineSetting -NoProxy
```

```Output
Server properties updated successfully.
```

```powershell
Set-OBMachineSetting -NoThrottle
```

```Output
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Verschlüsselungseinstellungen

Die Sicherungsdaten, die an Azure Backup gesendet werden, werden verschlüsselt, um die Vertraulichkeit der Daten zu schützen. Die Verschlüsselungspassphrase ist das "Kennwort" zum Entschlüsseln der Daten zum Zeitpunkt der Wiederherstellung.

Sie müssen eine Sicherheits-PIN generieren, indem Sie im Azure-Portal im Abschnitt **Recovery Services-Tresor** unter **Einstellungen** > **Eigenschaften** > **Sicherheits-PIN** auf **Generieren** klicken. Verwenden Sie diese PIN anschließend im folgenden Befehl als `generatedPIN`:

```powershell
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>"
```

```Output
Server properties updated successfully
```

> [!IMPORTANT]
> Sichern Sie die Passphraseninformationen, nachdem Sie sie festgelegt haben. Das Wiederherstellen von Daten aus Azure ohne diese Passphrase ist nicht möglich.
>
>

## <a name="back-up-files-and-folders"></a>Sichern von Dateien und Ordnern

Alle Sicherungen von Windows-Servern und -Clients in Azure Backup werden durch eine Richtlinie gesteuert. Die Richtlinie besteht aus drei Teilen:

1. Ein **Sicherungszeitplan** gibt an, wann Sicherungen erstellt und mit dem Dienst synchronisiert werden müssen.
2. Ein **Aufbewahrungszeitplan** , der angibt, wie lange die Wiederherstellungspunkte in Azure beibehalten werden sollen.
3. Eine **Spezifikation für den Ein-/Ausschluss von Dateien** bestimmt, welche Dateien gesichert werden sollen.

Da wir die Sicherung automatisieren, gehen wir hier davon aus, dass nichts konfiguriert wurde. Als Erstes erstellen wir eine neue Sicherungsrichtlinie mithilfe des Cmdlets [New-OBPolicy](https://technet.microsoft.com/library/hh770416.aspx) .

```powershell
$NewPolicy = New-OBPolicy
```

Die Richtlinie ist jetzt noch leer. Um zu definieren, welche Elemente ein- oder ausgeschlossen, wann Sicherungen ausgeführt und wo die Sicherungen gespeichert werden, müssen andere Cmdlets verwendet werden.

### <a name="configuring-the-backup-schedule"></a>Konfigurieren des Sicherungszeitplans

Der erste der drei Teile einer Richtlinie ist der Sicherungszeitplan, der mit dem [New-OBSchedule](https://technet.microsoft.com/library/hh770401) Cmdlet erstellt wird. Der Sicherungszeitplan definiert, wann Sicherungen ausgeführt werden müssen. Beim Erstellen eines Zeitplans müssen Sie zwei Eingabeparameter angeben:

* **Tage der Woche** , an denen die Sicherung ausgeführt werden soll. Sie können den Sicherungsauftrag nur an einem Tag, an jedem Tag der Woche oder an einer beliebigen Kombination von Tagen ausführen.
* **Tageszeiten** , zu denen die Sicherung ausgeführt werden soll. Sie können bis zu drei verschiedene Tageszeiten definieren, zu denen die Sicherung ausgelöst wird.

Sie können z. B. eine Sicherungsrichtlinie konfigurieren, die jeden Samstag und Sonntag um 16:00 Uhr ausgeführt wird.

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

Der Sicherungszeitplan muss einer Richtlinie zugeordnet werden. Dazu kann das [Set-OBSchedule](https://technet.microsoft.com/library/hh770407)-Cmdlet verwendet werden.

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```
### <a name="configuring-a-retention-policy"></a>Konfigurieren einer Aufbewahrungsrichtlinie

Die Aufbewahrungsrichtlinie definiert, wie lange durch Sicherungsaufträge erstellte Wiederherstellungspunkte beibehalten werden. Beim Erstellen einer neuen Aufbewahrungsrichtlinie mit dem [New-OBRetentionPolicy](https://technet.microsoft.com/library/hh770425) -Cmdlet können Sie die Anzahl von Tagen angeben, für die die Wiederherstellungspunkte der Sicherung in Azure Backup beibehalten werden müssen. Im folgenden Beispiel wird eine Aufbewahrungsrichtlinie von 7 Tagen festgelegt.

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

Die Aufbewahrungsrichtlinie muss der Hauptrichtlinie mithilfe des [Set-OBRetentionPolicy](https://technet.microsoft.com/library/hh770405)-Cmdlets zugeordnet werden:

```powershell
Set-OBRetentionPolicy -Policy $NewPolicy -RetentionPolicy $RetentionPolicy
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```
### <a name="including-and-excluding-files-to-be-backed-up"></a>Einschließen und Ausschließen zu sichernder Dateien

Ein `OBFileSpec` -Objekt definiert die Dateien, die in eine Sicherung eingeschlossen oder von ihr ausgeschlossen werden. Es handelt sich dabei um einen Satz von Regeln, die die geschützten Dateien und Ordner auf einem Computer festlegen. Sie können beliebig viele Einschluss- oder Ausschlussregeln für Dateien festlegen und sie einer Richtlinie zuordnen. Beim Erstellen eines neuen OBFileSpec-Objekts können Sie:

* die einzuschließenden Dateien und Ordner angeben,
* die auszuschließenden Dateien und Ordner angeben und
* eine rekursive Sicherung der Daten in einem Ordner festlegen (oder) angeben, ob nur die Dateien der obersten Ebene im angegebenen Ordner gesichert werden sollen.

Letzteres wird durch Verwendung des -NonRecursive-Kennzeichens im New-OBFileSpec-Befehl erreicht.

Im folgenden Beispiel sichern wir Volume „C:“ und „D:“ und schließen die Binärdateien des Betriebssystems im Windows-Ordner sowie alle temporären Ordner aus. Dazu erstellen wir zwei Dateispezifikationen mithilfe des [New-OBFileSpec](https://technet.microsoft.com/library/hh770408) -Cmdlets – eine Spezifikation für Einschluss und eine für Ausschluss. Nachdem die Dateispezifikationen erstellt wurden, werden sie mithilfe des [Add-OBFileSpec](https://technet.microsoft.com/library/hh770424) -Cmdlets mit der Richtlinie verknüpft.

```powershell
$Inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")
$Exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Inclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

```powershell
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Exclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="applying-the-policy"></a>Anwenden der Richtlinie

Das Richtlinienobjekt ist jetzt fertig und verfügt über einen zugeordneten Sicherungszeitplan, eine Aufbewahrungsrichtlinie sowie eine Ein-/Ausschlussliste für Dateien. Für diese Richtlinie kann jetzt ein Commit ausgeführt werden, um sie in Azure Backup zu speichern. Bevor Sie die neue Richtlinie anwenden, müssen Sie mithilfe des [Remove-OBPolicy](https://technet.microsoft.com/library/hh770415) -Cmdlets sicherstellen, dass dem Server keine vorhandenen Sicherungsrichtlinien zugeordnet sind. Beim Entfernen der Richtlinie werden Sie zur Bestätigung aufgefordert. Sie können die Bestätigung überspringen, indem Sie das `-Confirm:$false` -Kennzeichen mit dem Cmdlet verwenden.

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

Zum Ausführen eines Commits für das Richtlinienobjekt wird das [Set-OBPolicy](https://technet.microsoft.com/library/hh770421) -Cmdlet verwendet. Auch dabei werden Sie zur Bestätigung aufgefordert. Sie können die Bestätigung überspringen, indem Sie das `-Confirm:$false` -Kennzeichen mit dem Cmdlet verwenden.

```powershell
Set-OBPolicy -Policy $NewPolicy
```

```Output
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
    }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

Sie können die Details der vorhandenen Sicherungsrichtlinie mit dem [Get-OBPolicy](https://technet.microsoft.com/library/hh770406) -Cmdlet anzeigen. Sie können mit dem [Get-OBSchedule](https://technet.microsoft.com/library/hh770423)-Cmdlet einen Drilldown für den Sicherungszeitplan und mit dem [Get-OBRetentionPolicy](https://technet.microsoft.com/library/hh770427)-Cmdlet einen Drilldown für die Aufbewahrungsrichtlinien ausführen.

```powershell
Get-OBPolicy | Get-OBSchedule
```

```Output
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing
```

```powershell
Get-OBPolicy | Get-OBRetentionPolicy
```

```Output
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing
```

```powershell
Get-OBPolicy | Get-OBFileSpec
```

```Output
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### <a name="performing-an-ad-hoc-backup"></a>Durchführen einer Ad-hoc-Sicherung

Nachdem eine Sicherungsrichtlinie festgelegt wurde, werden die Sicherungen entsprechend dem Zeitplan ausgeführt. Mit dem [Start-OBBackup](https://technet.microsoft.com/library/hh770426)-Cmdlet kann auch eine Ad-hoc-Sicherung ausgelöst werden:

```powershell
Get-OBPolicy | Start-OBBackup
```

```Output
Initializing
Taking snapshot of volumes...
Preparing storage...
Generating backup metadata information and preparing the metadata VHD...
Data transfer is in progress. It might take longer since it is the first backup and all data needs to be transferred...
Data transfer completed and all backed up data is in the cloud. Verifying data integrity...
Data transfer completed
In progress...
Job completed.
The backup operation completed successfully.
```

## <a name="restore-data-from-azure-backup"></a>Wiederherstellen von Daten aus Azure Backup

In diesem Abschnitt werden die Schritte zum Automatisieren der Wiederherstellung von Daten aus Azure Backup erläutert. Dieser Vorgang umfasst die folgenden Schritte:

1. Auswählen des Quellvolumes
2. Auswählen eines Sicherungspunkts für die Wiederherstellung
3. Auswählen eines wiederherzustellenden Elements
4. Auslösen des Wiederherstellungsvorgangs

### <a name="picking-the-source-volume"></a>Auswählen des Quellvolumes

Um ein Element aus Azure Backup wiederherzustellen, müssen Sie zunächst die Quelle des Elements identifizieren. Da wir die Befehle im Kontext eines Windows-Servers oder Windows-Clients ausführen, ist der Computer bereits identifiziert. Der nächste Schritt beim Auswählen der Quelle besteht darin, das zugehörige Volume zu identifizieren. Eine Liste der gesicherten Volumes oder Quellen des Computers kann mit dem [Get-OBRecoverableSource](https://technet.microsoft.com/library/hh770410) -Cmdlet abgerufen werden. Dieser Befehl gibt ein Array aller Quellen zurück, die von diesem Server/Client gesichert werden.

```powershell
$Source = Get-OBRecoverableSource
$Source
```

```Output
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Auswählen eines Sicherungspunkts für die Wiederherstellung

Die Liste der Sicherungspunkte kann durch Ausführen des Cmdlets [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) mit entsprechenden Parametern abgerufen werden. In unserem Beispiel wählen wir den neuesten Sicherungspunkt für das Quellvolume *D:* aus und verwenden ihn zum Wiederherstellen einer bestimmten Datei.

```powershell
$Rps = Get-OBRecoverableItem -Source $Source[1]
```

```Output
IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :

IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 17-Jun-15 6:31:31 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :
```

Das `$Rps`-Objekt ist ein Array von Sicherungspunkten. Das erste Element ist der neueste Punkt und das n. Element der älteste Punkte. Um den neuesten Punkt auszuwählen, verwenden wir `$Rps[0]`.

### <a name="choosing-an-item-to-restore"></a>Auswählen eines wiederherzustellenden Elements

Um die Datei oder den Ordner zu identifizieren, die bzw. der wiederhergestellt werden soll, verwenden Sie das [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx)-Cmdlet rekursiv. Auf diese Weise kann die Ordnerhierarchie ausschließlich mit `Get-OBRecoverableItem` durchsucht werden.

Wenn wir in diesem Beispiel die Datei *finances.xls* wiederherstellen möchten, können wir mit dem `$FilesFolders[1]`-Objekt auf sie verweisen.

```powershell
$FilesFolders = Get-OBRecoverableItem $Rps[0]
$FilesFolders
```

```Output
IsDir : True
ItemNameFriendly : D:\MyData\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\
LocalMountPoint : D:\
MountPointName : D:\
Name : MyData
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime : 15-Jun-15 8:49:29 AM
```

```powershell
$FilesFolders = Get-OBRecoverableItem $FilesFolders[0]
$FilesFolders
```

```Output
IsDir : False
ItemNameFriendly : D:\MyData\screenshot.oxps
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\screenshot.oxps
LocalMountPoint : D:\
MountPointName : D:\
Name : screenshot.oxps
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 228313
ItemLastModifiedTime : 21-Jun-14 6:45:09 AM

IsDir : False
ItemNameFriendly : D:\MyData\finances.xls
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\finances.xls
LocalMountPoint : D:\
MountPointName : D:\
Name : finances.xls
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 96256
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM
```

Sie können auch mit dem ```Get-OBRecoverableItem``` -Cmdlet nach wiederherzustellenden Elementen suchen. In unserem Beispiel können wir zum Suchen nach *finances.xls* den folgenden Befehl ausführen, um ein Handle für die Datei abzurufen:

```powershell
$Item = Get-OBRecoverableItem -RecoveryPoint $Rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### <a name="triggering-the-restore-process"></a>Auslösen des Wiederherstellungsvorgangs

Um den Wiederherstellungsvorgang auszulösen, müssen wir zunächst die Wiederherstellungsoptionen angeben. Dazu kann das [New-OBRecoveryOption](https://technet.microsoft.com/library/hh770417.aspx) -Cmdlet verwendet werden. Angenommen, wir möchten die Dateien in *C:\temp* wiederherstellen. Außerdem sollen Dateien, die bereits im Zielordner *C:\temp* vorhanden sind, übersprungen werden. Um eine solche Wiederherstellungsoption zu erstellen, verwenden Sie den folgenden Befehl:

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Jetzt lösen Sie die Wiederherstellung mithilfe des Befehls [Start-OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) für das ausgewählte `$Item` in der Ausgabe des Cmdlets `Get-OBRecoverableItem` aus:

```powershell
Start-OBRecovery -RecoverableItem $Item -RecoveryOption $RecoveryOption
```

```Output
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```

## <a name="uninstalling-the-azure-backup-agent"></a>Deinstallieren des Azure Backup-Agents

Das Deinstallieren des Azure Backup-Agents kann mithilfe des folgenden Befehls erfolgen:

```powershell
.\MARSAgentInstaller.exe /d /q
```

Die Deinstallation der Binärdateien des Agents vom Computer hat einige Folgen, die berücksichtigt werden müssen:

* Der Dateifilter wird vom Computer entfernt, und die Nachverfolgung von Änderungen wird beendet.
* Alle Richtlinieninformationen werden vom Computer entfernt, aber weiterhin im Dienst gespeichert.
* Alle Sicherungszeitpläne werden entfernt, und es werden keine weiteren Sicherungen erstellt.

Die in Azure gespeicherten Daten bleiben jedoch erhalten und werden gemäß der von Ihnen eingerichteten Aufbewahrungsrichtlinie beibehalten. Ältere Punkte werden automatisch ersetzt.

## <a name="remote-management"></a>Remoteverwaltung

Die gesamte Verwaltung des Azure Backup-Agents, der Richtlinien und Datenquellen kann remote über PowerShell durchgeführt werden. Der Computer, der remote verwaltet wird, muss ordnungsgemäß vorbereitet werden.

Standardmäßig ist der WinRM-Dienst für den manuellen Start konfiguriert. Der Starttyp muss auf *Automatisch* festgelegt werden, und der Dienst sollte gestartet werden. Um sicherzustellen, dass der WinRM-Dienst ausgeführt wird, muss der Wert der Statuseigenschaft *Wird ausgeführt*lauten.

```powershell
Get-Service -Name WinRM
```

```Output
Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

PowerShell sollte für Remoting konfiguriert sein.

```powershell
Enable-PSRemoting -Force
```

```Output
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.
```

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force
```

Der Computer kann jetzt remote verwaltet werden - beginnend mit der Installation des Agents. Beispielsweise kopiert das folgende Skript den Agent auf den Remotecomputer und installiert ihn.

```powershell
$DLoc = "\\REMOTESERVER01\c$\Windows\Temp"
$Agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
$Args = "/q"
Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $DLoc -Force

$Session = New-PSSession -ComputerName REMOTESERVER01
Invoke-Command -Session $Session -Script { param($D, $A) Start-Process -FilePath $D $A -Wait } -ArgumentList $Agent, $Args
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Backup für Windows-Server und -Clients finden Sie unter

* [Einführung in Azure Backup](backup-introduction-to-azure-backup.md)
* [Sichern von Windows-Servern](backup-configure-vault.md)
