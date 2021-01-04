---
title: Aktivieren der AD DS-Authentifizierung für Dateifreigaben in Azure
description: In diesem Artikel erhalten Sie Informationen zum Aktivieren der Active Directory Domain Services-Authentifizierung über SMB für Azure-Dateifreigaben. Ihre in die Domäne eingebundenen virtuellen Windows-Computer können dann mit AD DS-Anmeldeinformationen auf Azure-Dateifreigaben zugreifen.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/13/2020
ms.author: rogarana
ms.openlocfilehash: 948b30cbf37ae5f4f357860569579d8591412414
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630395"
---
# <a name="part-one-enable-ad-ds-authentication-for-your-azure-file-shares"></a>Teil 1: Aktivieren der AD DS-Authentifizierung für Ihre Dateifreigaben in Azure 

Bevor Sie die Active Directory Domain Services-Authentifizierung (AD DS) aktivieren, sollten Sie den [Übersichtsartikel](storage-files-identity-auth-active-directory-enable.md) gelesen haben, um die unterstützten Szenarios und Anforderungen zu kennen.

In diesem Artikel wird der Prozess erläutert, der für das Aktivieren der Active Directory Domain Services-Authentifizierung (AD DS) für Ihr Speicherkonto erforderlich ist. Nachdem Sie das Feature aktiviert haben, müssen Sie Ihr Speicherkonto und Ihre AD DS-Instanz konfigurieren, damit die AD DS-Anmeldeinformationen für die Authentifizierung bei Ihrer Dateifreigabe in Azure verwendet werden. Sie müssen Ihr Speicherkonto bei AD DS registrieren und dann die erforderlichen Domäneneigenschaften für das Speicherkonto festlegen, um die AD DS-Authentifizierung über SMB für Azure-Dateifreigaben zu aktivieren.

Erstellen Sie zum Registrieren Ihres Speicherkontos bei AD DS ein Konto, das es in Ihrer AD DS-Instanz darstellt. Dieser Prozess ähnelt dem Erstellen eines Kontos, das einen lokalen Windows-Dateiserver in Ihrer AD DS-Instanz darstellt. Wenn das Feature für das Speicherkonto aktiviert ist, gilt es für alle neuen und vorhandenen Dateifreigaben im Konto.

## <a name="option-one-recommended-use-azfileshybrid-powershell-module"></a>Option 1 (empfohlen): Verwenden des AzFilesHybrid-PowerShell-Moduls

Durch die Cmdlets im AzFilesHybrid-PowerShell-Modul werden die erforderlichen Änderungen vorgenommen, und das Feature wird automatisch aktiviert. Da einige Teile der Cmdlets mit Ihrer lokalen AD DS-Instanz interagieren, wird hier erläutert, was die Cmdlets genau tun, damit sie bestimmen können, ob die Änderungen mit Ihren Compliance- und Sicherheitsrichtlinien konform sind, und dafür sorgen können, dass Sie über die erforderlichen Berechtigungen zum Ausführen der Cmdlets verfügen. Es wird zwar empfohlen, das AzFilesHybrid-Modul zu verwenden, wenn Sie dies jedoch nicht können, finden Sie hier die Schritte, die für eine manuelle Ausführung erforderlich sind.

### <a name="download-azfileshybrid-module"></a>Herunterladen des AzFilesHybrid-Moduls

- [Herunterladen und Entpacken des AzFilesHybrid-Moduls (GA-Modul: v0.2.0+)](https://github.com/Azure-Samples/azure-files-samples/releases) Beachten Sie, dass die AES 256-Kerberos-Verschlüsselung ab Version 0.2.2 unterstützt wird. Wenn Sie die Funktion mit einer älteren AzFilesHybrid-Version als v0.2.2 aktiviert haben und ein Update zur Unterstützung der AES 256 Kerberos-Verschlüsselung durchführen möchten, lesen Sie [diesen Artikel](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption). 
- Installieren Sie das Modul auf einem Gerät mit Domäneneinbindung in die lokale AD DS-Umgebung mithilfe von AD DS-Anmeldeinformationen, die zum Erstellen eines Dienstanmeldekontos oder eines Computerkontos in der Ziel-AD-Umgebungen berechtigt sind, und führen Sie das Modul dann aus.
-  Führen Sie das Skript mit lokalen AD DS-Anmeldeinformationen aus, die mit Ihrer Azure AD-Umgebung synchronisiert sind. Die lokalen AD DS-Anmeldeinformationen müssen über die Rolle „Speicherkontobesitzer“ oder „Mitwirkender“ für Azure-Rollen verfügen.

### <a name="run-join-azstorageaccountforauth"></a>Ausführen von Join-AzStorageAccountForAuth

Mit dem Cmdlet `Join-AzStorageAccountForAuth` wird die Entsprechung eines Offlinedomänenbeitritts im Auftrag des angegebenen Speicherkontos durchgeführt. Das Skript verwendet das Cmdlet, um ein [Computerkonto](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) in Ihrer AD-Domäne zu erstellen. Wenn Sie aus irgendeinem Grund kein Computerkonto verwenden können, können Sie stattdessen das Skript ändern, um ein [Dienstanmeldekonto](/windows/win32/ad/about-service-logon-accounts) zu erstellen. Wenn Sie diesen Befehl manuell ausführen möchten, sollten Sie das Konto auswählen, das am besten für Ihre Umgebung geeignet ist.

Das vom Cmdlet erstellte AD DS-Konto repräsentiert das Speicherkonto. Wenn das AD DS-Konto unter einer AD-Organisationseinheit (OE) erstellt wird, die einen Kennwortablauf erzwingt, müssen Sie das Kennwort vor Erreichen des maximalen Kennwortalters aktualisieren. Wenn Sie das Kontokennwort nicht vor diesem Datum aktualisieren, führt dies beim Zugriff auf Azure-Dateifreigaben zu Authentifizierungsfehlern. Weitere Informationen zum Aktualisieren des Kennworts finden Sie unter [Aktualisieren des Kennworts für das AD DS-Konto](storage-files-identity-ad-ds-update-password.md).

Ersetzen Sie die Platzhalterwerte in den Parametern unten durch ihre eigenen Werte, bevor Sie das Skript in PowerShell ausführen.
> [!IMPORTANT]
> Mit dem Cmdlet für den Domänenbeitritt wird ein AD-Konto erstellt, das das Speicherkonto (die Dateifreigabe) in AD repräsentiert. Sie können sich wahlweise als Computerkonto oder Dienstanmeldekonto registrieren. Einzelheiten finden Sie unter [Häufig gestellte Fragen](./storage-files-faq.md#security-authentication-and-access-control). Für Computerkonten gilt eine Standardfrist für den Ablauf des Kennworts, die in AD auf 30 Tage festgelegt ist. Analog dazu kann für das Dienstanmeldekonto in der AD-Domäne oder Organisationseinheit (OU) eine Standardablauffrist für das Kennwort festgelegt sein.
> Für beide Kontotypen wir empfohlen, das in Ihrer AD-Umgebung konfigurierte Kenntwortablaufalter zu überprüfen und eine [Aktualisierung des Kennworts Ihrer Speicherkontoidentität](storage-files-identity-ad-ds-update-password.md) für das AD-Konto zu planen, bevor das maximale Kennwortalter erreicht ist. Sie können in Erwägung ziehen, [eine neue AD-Organisationseinheit (OE) in AD](/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) zu erstellen und die Richtlinie zum Ablauf des Kennworts für [Computerkonten](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)) oder Dienstanmeldekonten entsprechend zu deaktivieren. 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer Azure role assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account (default parameter value), depends on the AD permission you have and preference. 
# Run Get-Help Join-AzStorageAccountForAuth for more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -StorageAccountName $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" <# Default is set as ComputerAccount #> `
        -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" <# If you don't provide the OU name as an input parameter, the AD identity that represents the storage account is created under the root directory. #> `
        -EncryptionType "<AES256/RC4/AES256,RC4>" <# Specify the encryption agorithm used for Kerberos authentication. Default is configured as "'RC4','AES256'" which supports both 'RC4' and 'AES256' encryption. #>

#Run the command below if you want to enable AES 256 authentication. If you plan to use RC4, you can skip this step.
Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, see Azure Files Windows troubleshooting guide.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="option-2-manually-perform-the-enablement-actions"></a>Option 2: Manuelle Ausführung der Aktivierungsaktionen

Wenn Sie das oben beschriebene Skript `Join-AzStorageAccountForAuth` bereits erfolgreich ausgeführt haben, fahren Sie mit dem nächsten Abschnitt [Überprüfen der Aktivierung des Features](#confirm-the-feature-is-enabled) fort. Die folgenden manuellen Schritte müssen Sie nicht ausführen.

### <a name="checking-environment"></a>Überprüfen der Umgebung

Sie müssen als ersten Schritt den Zustand der Umgebung überprüfen. Insbesondere müssen Sie überprüfen, ob [Active Directory PowerShell](/powershell/module/addsadministration/?view=win10-ps) installiert ist und die Shell mit Administratorrechten ausgeführt wird. Überprüfen Sie dann, ob das [Modul „Az.Storage 2.0“](https://www.powershellgallery.com/packages/Az.Storage/2.0.0) installiert ist. Ist dies nicht der Fall, installieren Sie das Modul. Wenn Sie diese beiden Überprüfungen durchgeführt haben, überprüfen Sie, ob Ihre AD DS-Umgebung ein bereits vorhandenes [Computerkonto](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (Standardwert) oder ein [Dienstanmeldekonto](/windows/win32/ad/about-service-logon-accounts) mit dem SPN/UPN „cifs/ihr-speicherkontoname.file.core.windows.net“ aufweist. Wenn das Konto nicht vorhanden ist, erstellen Sie wie im folgenden Abschnitt beschrieben ein Konto.

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>Manuelles Erstellen einer Identität, die das Speicherkonto in Ihrem AD darstellt

Um dieses Konto manuell zu erstellen, erstellen Sie einen neuen Kerberos-Schlüssel für Ihr Speicherkonto. Verwenden Sie den Kerberos-Schlüssel dann als Kennwort für Ihr Konto mit den PowerShell-Cmdlets unten. Dieser Schlüssel wird nur während der Einrichtung verwendet und kann nicht für Vorgänge auf Steuerungs- oder Datenebene für das Speicherkonto verwendet werden. 

```PowerShell
# Create the Kerberos key on the storage account and get the Kerb1 key as the password for the AD identity to represent the storage account
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

New-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -KeyName kerb1
Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -ListKerbKey | where-object{$_.Keyname -contains "kerb1"}
```

Sobald Sie über diesen Schlüssel verfügen, erstellen Sie unter Ihrer Organisationseinheit ein Dienst- oder ein Computerkonto. Verwenden Sie die folgende Spezifikation, und denken Sie dabei daran, den Beispieltext durch den Namen Ihres Speicherkontos zu ersetzen:

SPN: „cifs/ihr-speicherkontoname.file.core.windows.net“ Kennwort: Kerberos-Schlüssel für Ihr Speicherkonto.

Wenn Ihre Organisationseinheit den Ablauf von Kennwörtern erzwingt, müssen Sie das Kennwort vor Erreichen des maximalen Kennwortalters aktualisieren, um Authentifizierungsfehler beim Zugriff auf Azure-Dateifreigaben zu verhindern. Weitere Informationen finden Sie unter [Aktualisieren des Kennworts für Ihre Speicherkontoidentität in Active Directory](storage-files-identity-ad-ds-update-password.md).

Notieren Sie sich die SID für die neu erstellte Identität. Sie benötigen sie im nächsten Schritt. Die soeben erstellte Identität, die das Speicherkonto repräsentiert, muss nicht mit Azure AD synchronisiert werden.

### <a name="enable-the-feature-on-your-storage-account"></a>Aktivieren des Features für Ihr Speicherkonto

Sie können nun das Feature für Ihr Speicherkonto aktivieren. Geben Sie einige Konfigurationsdetails für die Domäneneigenschaften im folgenden Befehl ein, und führen Sie ihn dann aus. Die im folgenden Befehl erforderliche SID für das Speicherkonto ist die SID der Identität, die Sie im [vorherigen Abschnitt](#creating-an-identity-representing-the-storage-account-in-your-ad-manually) in Ihrer AD DS-Umgebung erstellt haben.

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```

### <a name="debugging"></a>Debuggen

Sie können das Cmdlet Debug-AzStorageAccountAuth ausführen, um eine Reihe grundlegender Überprüfungen Ihrer AD-Konfiguration mit dem angemeldeten AD-Benutzer durchzuführen. Dieses Cmdlet wird von der AzFilesHybrid-Version 0.1.2 und höheren Versionen unterstützt. Weitere Informationen zu den in diesem Cmdlet ausgeführten Überprüfungen finden Sie im Problembehandlungsleitfaden für Windows unter [Azure Files mit AD-Anmeldeinformationen kann nicht eingebunden werden](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials).

```PowerShell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="confirm-the-feature-is-enabled"></a>Überprüfen der Aktivierung des Features

Sie können überprüfen, ob das Feature für Ihr Speicherkonto aktiviert ist, indem Sie das folgende Skript verwenden:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Wurde das Skript erfolgreich ausgeführt, sollte die Ausgabe wie folgt aussehen:

```PowerShell
DomainName:<yourDomainHere>
NetBiosDomainName:<yourNetBiosDomainNameHere>
ForestName:<yourForestNameHere>
DomainGuid:<yourGUIDHere>
DomainSid:<yourSIDHere>
AzureStorageID:<yourStorageSIDHere>
```
## <a name="next-steps"></a>Nächste Schritte

Sie haben das Feature nun für Ihr Speicherkonto erfolgreich aktiviert. Zum Verwenden des Features müssen Sie Berechtigungen auf Freigabeebene zuweisen. Sie können nun mit folgendem Artikel fortfahren:

[Teil 2: Zuweisen von Berechtigungen auf Freigabeebene für eine Identität](storage-files-identity-ad-ds-assign-permissions.md)