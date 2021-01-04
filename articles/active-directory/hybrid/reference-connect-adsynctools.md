---
title: 'Azure AD Connect: PowerShell-Referenz zu ADSyncTools | Microsoft-Dokumentation'
description: Dieses Dokument enthält Referenzinformationen für das PowerShell-Modul „ADSyncTools.psm1“.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2dd16f89851b9376557e544b86dc5e088891e63b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446985"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect:  PowerShell-Referenz zu ADSyncTools
Die folgende Dokumentation enthält Referenzinformationen für das PowerShell-Modul „ADSyncTools.psm1“, das in Azure AD Connect enthalten ist.

## <a name="install-the-adsynctools-powershell-module"></a>Installieren des PowerShell-Moduls „ADSyncTools“
Gehen Sie folgendermaßen vor, um das PowerShell-Modul ADSyncTools zu installieren:

1.  Öffnen Sie Windows PowerShell mit Administratorrechten.
2.  Geben Sie den folgenden Befehl ein (Sie können ihn auch kopieren und einfügen): 
    ``` powershell
        [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
        Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
        Import-module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools"
    ```
3.  Drücken Sie die EINGABETASTE.
4.  Um zu überprüfen, ob das Modul installiert wurde, geben Sie folgenden Befehl ein (Sie können ihn auch kopieren und einfügen):
    ```powershell
    Get-module AdSyncTools
    ```
5.  Jetzt sollten Informationen zum Modul angezeigt werden.


## <a name="clear-adsynctoolsconsistencyguid"></a>Clear-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Löscht die mS-Ds-ConsistencyGuid von dem AD-Benutzer.

### <a name="syntax"></a>SYNTAX

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIPTION
Löscht den Wert in mS-Ds-ConsistencyGuid für den AD-Zielbenutzer.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>BEISPIEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
Festzulegender Zielbenutzer in AD.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Confirm-ADSyncToolsADModuleLoaded

### <a name="synopsis"></a>ZUSAMMENFASSUNG
{{Zusammenfassung eintragen}}

### <a name="syntax"></a>SYNTAX

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>DESCRIPTION
{{Beschreibung eintragen}}

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>Beispiel 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Hier Beispielbeschreibung hinzufügen }}

## <a name="connect-adsyncdatabase"></a>Connect-AdSyncDatabase

### <a name="synopsis"></a>ZUSAMMENFASSUNG
{{Zusammenfassung eintragen}}

### <a name="syntax"></a>SYNTAX

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>DESCRIPTION
{{Beschreibung eintragen}}

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>Beispiel 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Hier Beispielbeschreibung hinzufügen }}

### <a name="parameters"></a>PARAMETERS

#### <a name="-database"></a>-Database
{{Datenbankbeschreibung eintragen}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-instance"></a>-Instance
{{Instanzbeschreibung eintragen}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-password"></a>-Password
{{Kennwortbeschreibung eintragen}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
{{Serverbeschreibung eintragen}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName
{{UserName-Beschreibung eintragen}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Exportiert den ConsistencyGuid-Bericht.

### <a name="syntax"></a>SYNTAX

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>DESCRIPTION
Generiert einen ConsistencyGuid-Bericht, basierend auf eine CSV-Importdatei von Import-ADSyncToolsImmutableIdMigration.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsConsistencyGuidMigration -Output ".\AllSyncUsers-Report"
```

#### <a name="example-2"></a>BEISPIEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-alternativeloginid"></a>-AlternativeLoginId
Alternative Anmelde-ID (E-Mail) verwenden.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-userprincipalname"></a>-UserPrincipalName
UserPrincipalName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Output
Ausgabedateiname für CSV- und LOG-Dateien.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>ZUSAMMENFASSUNG
{{Zusammenfassung eintragen}}

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>DESCRIPTION
{{Beschreibung eintragen}}

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>Beispiel 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Hier Beispielbeschreibung hinzufügen }}

### <a name="parameters"></a>PARAMETERS

#### <a name="-hostname"></a>-hostName
{{hostName-Beschreibung eintragen}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="get-adsynctoolsaduser"></a>Get-ADSyncToolsADuser

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Ruft Benutzer aus AD ab.

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIPTION
Gibt ein AD-Objekt zurück. AUFGABE: Unterstützung für mehrere Gesamtstrukturen

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>BEISPIEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
Zielbenutzer in AD zum Festlegen von ConsistencyGuid.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Ruft die mS-Ds-ConsistencyGuid von dem AD-Benutzer ab.

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIPTION
Gibt den Wert im Attribut „mS-Ds-ConsistencyGuid“ des AD-Zielbenutzers im GUID-Format zurück.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>BEISPIEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
Festzulegender Zielbenutzer in AD.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjectGuid

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Ruft die ObjectGuid vom AD-Benutzer ab.

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIPTION
Gibt den Wert im Attribut „ObjectGUID“ des AD-Zielbenutzers im GUID-Format zurück.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>BEISPIEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
Festzulegender Zielbenutzer in AD.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Ruft den Ausführungsverlauf von AAD Connect ab.

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>DESCRIPTION
Eine Funktion, die den Ausführungsverlauf von AAD Connect im XML-Format zurückgibt

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Get-ADSyncToolsRunHistory
```

#### <a name="example-2"></a>BEISPIEL 2
```
Get-ADSyncToolsRunHistory -Days 1
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-days"></a>-Days
{{Tagebeschreibung eintragen}}

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 3
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorChanged

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Ruft Benutzer mit „SourceAnchor geändert“-Fehlern ab.

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIPTION
Eine Funktion, die den Ausführungsverlauf von AAD Connect abfragt und alle Benutzer exportiert, für die der Fehler „Attribut „SourceAnchor“ wurde geändert“ gemeldet wird.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
#Required Parameters
```

$sourcePath = Read-Host -Prompt "Geben Sie Ihren Protokolldateipfad mit Dateinamen an." #"\<Source_Path\>" $outputPath = Read-Host -Prompt "Geben Sie Ihren Ausgabedateipfad mit Dateinamen an." #"\<Out_Path\>"
 
 Get-ADSyncToolsUsersSourceAnchorChanged -sourcePath $sourcePath -outputPath $outputPath

#### <a name="example-2"></a>BEISPIEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-sourcepath"></a>-sourcePath
{{sourcePath-Beschreibung eintragen}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-outputpath"></a>-outputPath
{{out-Beschreibung eintragen}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Importiert ImmutableID aus AAD.

### <a name="syntax"></a>SYNTAX

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>DESCRIPTION
Generiert eine Datei mit allen in Azure AD synchronisierten Benutzern, die den Wert „ImmutableID“ im GUID-Format enthalten. Voraussetzungen: PowerShell-Modul „MSOnline“

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Import-ADSyncToolsImmutableIdMigration -OutputFile '.\AllSyncUsers.csv'
```

#### <a name="example-2"></a>BEISPIEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-output"></a>-Output
CSV-Ausgabedatei

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecycleBin
Ruft synchronisierte Benutzer aus dem Azure AD-Papierkorb ab.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).


## <a name="invoke-adsyncdatabasequery"></a>Invoke-AdSyncDatabaseQuery

### <a name="synopsis"></a>ZUSAMMENFASSUNG
{{Zusammenfassung eintragen}}

### <a name="syntax"></a>SYNTAX

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>DESCRIPTION
{{Beschreibung eintragen}}

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>Beispiel 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Hier Beispielbeschreibung hinzufügen }}

### <a name="parameters"></a>PARAMETERS

#### <a name="-query"></a>-Query
{{Abfragebeschreibung eintragen}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-sqlconnection"></a>-SqlConnection
{{SqlConnection-Beschreibung eintragen}}

```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Skript zum Entfernen abgelaufener Zertifikate aus dem Attribut „UserCertificate“.

### <a name="syntax"></a>SYNTAX

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>DESCRIPTION
Dieses Skript akzeptiert alle Objekte von einer Zielorganisationseinheit in Ihrer Active Directory-Domäne – gefiltert nach Objektklasse (Benutzer/Computer), und löscht alle abgelaufene Zertifikate, die im Attribut „UserCertificate“ vorhanden sind.
Standardmäßig (BackupOnly-Modus) werden nur abgelaufene Zertifikate in einer Datei gesichert und keine Änderungen in Active Directory vorgenommen.
Wenn Sie „-BackupOnly $false“ verwenden, werden alle für diese Objekte im Attribut „UserCertificate“ vorhandenen abgelaufenen Zertifikate aus AD entfernt, nachdem sie in eine Datei kopiert wurden.
Jedes Zertifikat wird unter einem gesonderten Dateinamen gesichert: ObjectClass_ObjectGUID_CertThumprint.cer. Das Skript erstellt außerdem eine Protokolldatei im CSV-Format mit allen Benutzern, die Zertifikate besitzen, die entweder gültig oder abgelaufen sind, inklusive der tatsächlich ausgeführten Aktion (Übersprungen/Exportiert/Gelöscht).

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Users,OU=Corp,DC=Contoso,DC=com" -ObjectClass user

#### <a name="example-2"></a>BEISPIEL 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Computers,OU=Corp,DC=Contoso,DC=com" -ObjectClass computer -BackupOnly $false

### <a name="parameters"></a>PARAMETERS

#### <a name="-targetou"></a>-TargetOU
Ziel-OE, in der nach AD-Objekten gesucht werden soll.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-backuponly"></a>-BackupOnly
Bei „BackupOnly“ werden keine Zertifikate aus AD gelöscht.

```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-ObjectClass
Objektklassenfilter

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="repair-adsynctoolsautoupgradestate"></a>Repair-ADSyncToolsAutoUpgradeState

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Kurze Beschreibung

### <a name="syntax"></a>SYNTAX

```
Repair-ADSyncToolsAutoUpgradeState
```

### <a name="description"></a>DESCRIPTION
Lange Beschreibung

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>BEISPIEL 2
```
Another example of how to use this cmdlet
```

## <a name="resolve-adsynchostaddress"></a>Resolve-ADSyncHostAddress

### <a name="synopsis"></a>ZUSAMMENFASSUNG
{{Zusammenfassung eintragen}}

### <a name="syntax"></a>SYNTAX

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>DESCRIPTION
{{Beschreibung eintragen}}

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>Beispiel 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Hier Beispielbeschreibung hinzufügen }}

### <a name="parameters"></a>PARAMETERS

#### <a name="-hostname"></a>-hostName
{{hostName-Beschreibung eintragen}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="restore-adsynctoolsexpiredcertificates"></a>Restore-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>ZUSAMMENFASSUNG
(AUFGABE) Stellt das AD-Attribut „UserCertificate“ aus einer Zertifikatdatei wieder her

### <a name="syntax"></a>SYNTAX

```
Restore-ADSyncToolsExpiredCertificates
```

### <a name="description"></a>DESCRIPTION
Lange Beschreibung

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>BEISPIEL 2
```
Another example of how to use this cmdlet
```

## <a name="set-adsynctoolsconsistencyguid"></a>Set-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Legt „ms-DS-ConsistencyGuid“ für einen AD-Benutzer fest.

### <a name="syntax"></a>SYNTAX

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>DESCRIPTION
Legt einen Wert im Attribut „mS-Ds-ConsistencyGuid“ für den AD-Zielbenutzer fest.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>BEISPIEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
Zielbenutzer in AD zum Festlegen von ConsistencyGuid.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-value"></a>-Value
ImmutableId (Byte-Array, GUID, GUID-Zeichenfolge oder Base64-Zeichenfolge)

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="test-adsyncnetworkport"></a>Test-ADSyncNetworkPort

### <a name="synopsis"></a>ZUSAMMENFASSUNG
{{Zusammenfassung eintragen}}

### <a name="syntax"></a>SYNTAX

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>DESCRIPTION
{{Beschreibung eintragen}}

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>Beispiel 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Hier Beispielbeschreibung hinzufügen }}

### <a name="parameters"></a>PARAMETERS

#### <a name="-hostname"></a>-hostName
{{hostName-Beschreibung eintragen}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-port
{{Portbeschreibung eintragen}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="trace-adsynctoolsadimport"></a>Trace-ADSyncToolsADImport

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Erstellt eine Ablaufverfolgungsdatei aus einem AD-Importschritt.

### <a name="syntax"></a>SYNTAX

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>DESCRIPTION
Erstellt eine Ablaufverfolgung für alle LDAP-Abfragen einer AD-Importausführung von AAD Connect von einem bestimmten AD-Wasserzeichen-Prüfpunkt (Partitionscookie). Erstellt eine Ablaufverfolgungsdatei „.\ADimportTrace_yyyyMMddHHmmss.log“ im aktuellen Ordner.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>BEISPIEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectorxml"></a>-ADConnectorXML
{{ADConnectorXML-Beschreibung eintragen}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dc"></a>-dc
XML-Datei eines AD-Connector-Exports.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: DC1.domain.contoso.com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-rootdn"></a>-rootDN
Zieldomänencontroller

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: DC=Domain,DC=Contoso,DC=com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-filter
Gesamtstrukturstamm-DN

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipcredentials"></a>-SkipCredentials
Typen von AD-Objekten, für die eine Ablaufverfolgung erstellt werden soll \> * = alle Objekttypen.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adwatermark"></a>-ADwatermark
Wenn die Ausführung bereits als Domänenadministrator erfolgt, besteht keine Notwendigkeit, AD-Anmeldeinformationen einzugeben.
Manuelle Eingabe des Wasserzeichens anstelle einer XML-Datei. Beispiel: $ADwatermark = "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA(...)"

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Kurze Beschreibung

### <a name="syntax"></a>SYNTAX

```
Trace-ADSyncToolsLdapQuery [-Context] <String> [-Server] <String> [-Port] <Int32> [-Filter] <String>
 [<CommonParameters>]
```

### <a name="description"></a>DESCRIPTION
Lange Beschreibung

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>BEISPIEL 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-context"></a>-Context
Hilfebeschreibung zu Param1.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
Hilfebeschreibung zu Param2.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: Localhost
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-Port
Hilfebeschreibung zu Param2.

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: 389
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-Filter
Hilfebeschreibung zu Param2.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Update-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Aktualisiert Benutzer mit der neuen „ConsistencyGuid“ (ImmutableId).

### <a name="syntax"></a>SYNTAX

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>DESCRIPTION
Aktualisiert Benutzer mit dem neuen Wert von „ConsistencyGuid“ (ImmutableId), der dem ConsistencyGuid-Bericht entnommen wurde. Diese Funktion unterstützt den WhatIf-Schalter. Hinweis: Der ConsistencyGuid-Bericht muss mit Tabstopps als Trennzeichen importiert werden.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2 -WhatIf
```

#### <a name="example-2"></a>BEISPIEL 2
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-distinguishedname"></a>-DistinguishedName
DistinguishedName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-action"></a>-Action
Aktion

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Output
Ausgabedateiname für LOG-Dateien.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Zeigt, was geschieht, wenn das Cmdlet ausgeführt wird.
Das Cmdlet wird nicht ausgeführt.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Hiermit werden Sie vor der Ausführung des Cmdlets zur Bestätigung aufgefordert.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).
