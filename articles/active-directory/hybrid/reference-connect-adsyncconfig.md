---
title: 'Azure AD Connect: PowerShell-Referenz zu ADSyncConfig | Microsoft-Dokumentation'
description: Dieses Dokument enthält Referenzinformationen für das PowerShell-Modul „ADSyncConfig.psm1“.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 554bb99121190198982f64deb6ee0674aa8831ed
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60381194"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Azure AD Connect:  PowerShell-Referenz zu ADSyncConfig
Die folgende Dokumentation enthält Referenzinformationen für das PowerShell-Modul „ADSyncConfig.psm1“, das in Azure AD Connect enthalten ist.


## <a name="get-adsyncadconnectoraccount"></a>Get-ADSyncADConnectorAccount

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Ruft den Kontonamen und die Domäne ab, die in jedem AD-Connector konfiguriert sind.

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>Beschreibung
Diese Funktion verwendet das „Get-ADSyncConnector“-Cmdlet, das in AAD Connect vorhanden ist, um aus Konnektivitätsparametern eine Tabelle mit dem Konto der AD-Connectors abzurufen.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Get-ADSyncADConnectorAccount
```

## <a name="get-adsyncobjectswithinheritancedisabled"></a>Get-ADSyncObjectsWithInheritanceDisabled

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Ruft AD-Objekte mit deaktivierter Berechtigungsvererbung ab.

### <a name="syntax"></a>SYNTAX

```
Get-ADSyncObjectsWithInheritanceDisabled [-SearchBase] <String> [[-ObjectClass] <String>] [<CommonParameters>]
```

### <a name="description"></a>Beschreibung
Sucht in AD, beginnend bei dem Parameter „SearchBase“, und gibt alle Objekte zurück, gefiltert nach dem Parameter „ObjectClass“, für die zurzeit die ACL-Vererbung deaktiviert ist.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Find objects with disabled inheritance in 'Contoso' domain (by default returns 'organizationalUnit' objects only)
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso'

#### <a name="example-2"></a>BEISPIEL 2
```
Find 'user' objects with disabled inheritance in 'Contoso' domain
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso' -ObjectClass 'user'

#### <a name="example-3"></a>BEISPIEL 3
```
Find all types of objects with disabled inheritance in a OU
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase OU=AzureAD,DC=Contoso,DC=com -ObjectClass '*'

### <a name="parameters"></a>PARAMETER

#### <a name="-searchbase"></a>-SearchBase
Die „SearchBase“ für die LDAP-Abfrage, die der „DistinguishedName“ oder „FQDN“ einer AD-Domäne sein kann.

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

#### <a name="-objectclass"></a>-ObjectClass
Die Klasse der zu suchenden Objekte, bei denen es sich um „*“ (für alle Objektklassen), „User“ (Benutzer), „Group“ (Gruppe), „Container“ usw. handeln kann. Standardmäßig sucht diese Funktion nach der Objektklasse „organizationalUnit“ (Organisationseinheit).

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: OrganizationalUnit
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="set-adsyncbasicreadpermissions"></a>Set-ADSyncBasicReadPermissions

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Initialisiert Ihre Active Directory-Gesamtstruktur und Domäne für grundlegende Leseberechtigungen.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>Beschreibung
Die Funktion „Set-ADSyncBasicReadPermissions“ gewährt dem AD-Synchronisierungskonto die erforderlichen Berechtigungen, darunter die folgenden:
1.
„Eigenschaft lesen“-Zugriff auf alle Attribute für alle Nachfolger-Computerobjekte
2.
„Eigenschaft lesen“-Zugriff auf alle Attribute für alle Nachfolger-Geräteobjekte
3.
„Eigenschaft lesen“-Zugriff auf alle Attribute für alle Nachfolger-foreignsecurityprincipal-Objekte
5.
„Eigenschaft lesen“-Zugriff auf alle Attribute für alle Nachfolger-Benutzerobjekte
6.
„Eigenschaft lesen“-Zugriff auf alle Attribute für alle Nachfolger-inetorgperson-Objekte
7.
„Eigenschaft lesen“-Zugriff auf alle Attribute für alle Nachfolger-Gruppenobjekte
8.
„Eigenschaft lesen“-Zugriff auf alle Attribute für alle Nachfolger-Kontaktobjekte

Diese Berechtigungen gelten für alle Domänen in der Gesamtstruktur.
Optional können Sie einen „DistinguishedName“ im Parameter „ADobjectDN“ bereitstellen, um diese Berechtigungen nur für das AD-Objekt (einschließlich Vererbung an Unterobjekte) festzulegen.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>BEISPIEL 2
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>BEISPIEL 3
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>BEISPIEL 4
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETER

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Der Name des Active Directory-Kontos, das von der Azure AD Connect-Synchronisierung zum Verwalten von Objekten im Verzeichnis verwendet wird.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Die Domäne des Active Directory-Kontos, das von der Azure AD Connect-Synchronisierung zum Verwalten von Objekten im Verzeichnis verwendet wird.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Der DistinguishedName des Active Directory-Kontos, das von der Azure AD Connect-Synchronisierung zum Verwalten von Objekten im Verzeichnis verwendet wird.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
Der „DistinguishedName“ des-AD-Zielobjekts zum Festlegen von Berechtigungen (optional).

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Optionaler Parameter, um anzuzeigen, ob der AdminSDHolder-Container nicht mit diesen Berechtigungen aktualisiert werden sollte.

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

## <a name="set-adsyncexchangehybridpermissions"></a>Set-ADSyncExchangeHybridPermissions

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Initialisiert Ihre Active Directory-Gesamtstruktur und Domäne für die Exchange-Hybridfunktion.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>Beschreibung
Die Funktion „Set-ADSyncExchangeHybridPermissions“ gewährt dem AD-Synchronisierungskonto die erforderlichen Berechtigungen, darunter die folgenden:
1.
„Eigenschaft lesen/schreiben“-Zugriff auf alle Attribute für alle Nachfolger-Benutzerobjekte
2.
„Eigenschaft lesen/schreiben“-Zugriff auf alle Attribute für alle Nachfolger-inetorgperson-Objekte
3.
„Eigenschaft lesen/schreiben“-Zugriff auf alle Attribute für alle Nachfolger-Gruppenobjekte
4.
„Eigenschaft lesen/schreiben“-Zugriff auf alle Attribute für alle Nachfolger-Kontaktobjekte

Diese Berechtigungen gelten für alle Domänen in der Gesamtstruktur.
Optional können Sie einen „DistinguishedName“ im Parameter „ADobjectDN“ bereitstellen, um diese Berechtigungen nur für das AD-Objekt (einschließlich Vererbung an Unterobjekte) festzulegen.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>BEISPIEL 2
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>BEISPIEL 3
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>BEISPIEL 4
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETER

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Der Name des Active Directory-Kontos, das von der Azure AD Connect-Synchronisierung zum Verwalten von Objekten im Verzeichnis verwendet wird.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Die Domäne des Active Directory-Kontos, das von der Azure AD Connect-Synchronisierung zum Verwalten von Objekten im Verzeichnis verwendet wird.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Der DistinguishedName des Active Directory-Kontos, das von der Azure AD Connect-Synchronisierung zum Verwalten von Objekten im Verzeichnis verwendet wird.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
Der „DistinguishedName“ des-AD-Zielobjekts zum Festlegen von Berechtigungen (optional).

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Optionaler Parameter, um anzuzeigen, ob der AdminSDHolder-Container nicht mit diesen Berechtigungen aktualisiert werden sollte.

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

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Set-ADSyncExchangeMailPublicFolderPermissions

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Initialisiert Ihre Active Directory-Gesamtstruktur und Domäne für die Funktion „Öffentliche Ordner in Exchange-E-Mail“.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String>
 -ADConnectorAccountDomain <String> [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm]
 [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>Beschreibung
Die Funktion „Set-ADSyncExchangeMailPublicFolderPermissions“ gewährt dem AD-Synchronisierungskonto die erforderlichen Berechtigungen, darunter die folgenden:
1.
„Eigenschaft lesen“-Zugriff auf alle Attribute für alle Nachfolger-publicfolder-Objekte

Diese Berechtigungen gelten für alle Domänen in der Gesamtstruktur.
Optional können Sie einen „DistinguishedName“ im Parameter „ADobjectDN“ bereitstellen, um diese Berechtigungen nur für das AD-Objekt (einschließlich Vererbung an Unterobjekte) festzulegen.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>BEISPIEL 2
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>BEISPIEL 3
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>BEISPIEL 4
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETER

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Der Name des Active Directory-Kontos, das von der Azure AD Connect-Synchronisierung zum Verwalten von Objekten im Verzeichnis verwendet wird.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Die Domäne des Active Directory-Kontos, das von der Azure AD Connect-Synchronisierung zum Verwalten von Objekten im Verzeichnis verwendet wird.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Der DistinguishedName des Active Directory-Kontos, das von der Azure AD Connect-Synchronisierung zum Verwalten von Objekten im Verzeichnis verwendet wird.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
Der „DistinguishedName“ des-AD-Zielobjekts zum Festlegen von Berechtigungen (optional).

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Optionaler Parameter, um anzuzeigen, ob der AdminSDHolder-Container nicht mit diesen Berechtigungen aktualisiert werden sollte.

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

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Set-ADSyncMsDsConsistencyGuidPermissions

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Initialisiert Ihre Active Directory-Gesamtstruktur und Domäne für die mS-DS-ConsistencyGuid-Funktion.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>Beschreibung
Die Funktion „Set-ADSyncMsDsConsistencyGuidPermissions“ gewährt dem AD-Synchronisierungskonto die erforderlichen Berechtigungen, darunter die folgenden:
1.
„Eigenschaft lesen/schreiben“-Zugriff auf das mS-DS-ConsistencyGuid-Attribut für alle Nachfolger-Benutzerobjekte

Diese Berechtigungen gelten für alle Domänen in der Gesamtstruktur.
Optional können Sie einen „DistinguishedName“ im Parameter „ADobjectDN“ bereitstellen, um diese Berechtigungen nur für das AD-Objekt (einschließlich Vererbung an Unterobjekte) festzulegen.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>BEISPIEL 2
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>BEISPIEL 3
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>BEISPIEL 4
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETER

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Der Name des Active Directory-Kontos, das von der Azure AD Connect-Synchronisierung zum Verwalten von Objekten im Verzeichnis verwendet wird.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Die Domäne des Active Directory-Kontos, das von der Azure AD Connect-Synchronisierung zum Verwalten von Objekten im Verzeichnis verwendet wird.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Der DistinguishedName des Active Directory-Kontos, das von der Azure AD Connect-Synchronisierung zum Verwalten von Objekten im Verzeichnis verwendet wird.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
Der „DistinguishedName“ des-AD-Zielobjekts zum Festlegen von Berechtigungen (optional).

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Optionaler Parameter, um anzuzeigen, ob der AdminSDHolder-Container nicht mit diesen Berechtigungen aktualisiert werden sollte.

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

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Initialisiert Ihre Active Directory-Gesamtstruktur und Domäne für die Kennworthashsynchronisierung.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>Beschreibung
Die Funktion „Set-ADSyncPasswordHashSyncPermissions“ gewährt dem AD-Synchronisierungskonto die erforderlichen Berechtigungen, darunter die folgenden:
1.
Replizieren von Verzeichnisänderungen
2.
Replizieren von Verzeichnisänderungen: Alle

Diese Berechtigungen werden allen Domänen in der Gesamtstruktur erteilt.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>BEISPIEL 2
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETER

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Der Name des Active Directory-Kontos, das von der Azure AD Connect-Synchronisierung zum Verwalten von Objekten im Verzeichnis verwendet wird.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Die Domäne des Active Directory-Kontos, das von der Azure AD Connect-Synchronisierung zum Verwalten von Objekten im Verzeichnis verwendet wird.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Der DistinguishedName des Active Directory-Kontos, das von der Azure AD Connect-Synchronisierung zum Verwalten von Objekten im Verzeichnis verwendet wird.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
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

## <a name="set-adsyncpasswordwritebackpermissions"></a>Set-ADSyncPasswordWritebackPermissions

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Initialisiert Ihre Active Directory-Gesamtstruktur und Domäne für das Kennwortzurückschreiben aus Azure AD.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>Beschreibung
Die Funktion „Set-ADSyncPasswordWritebackPermissions“ gewährt dem AD-Synchronisierungskonto die erforderlichen Berechtigungen, darunter die folgenden:
1.
„Kennwort zurücksetzen“ für Nachfolger-Benutzerobjekte
2.
„Eigenschaft schreiben“-Zugriff auf das lockoutTime-Attribut für alle Nachfolger-Benutzerobjekte
3.
„Eigenschaft schreiben“-Zugriff auf das pwdLastSet-Attribut für alle Nachfolger-Benutzerobjekte

Diese Berechtigungen gelten für alle Domänen in der Gesamtstruktur.
Optional können Sie einen „DistinguishedName“ im Parameter „ADobjectDN“ bereitstellen, um diese Berechtigungen nur für das AD-Objekt (einschließlich Vererbung an Unterobjekte) festzulegen.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>BEISPIEL 2
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>BEISPIEL 3
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>BEISPIEL 4
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETER

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Der Name des Active Directory-Kontos, das von der Azure AD Connect-Synchronisierung zum Verwalten von Objekten im Verzeichnis verwendet wird.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Die Domäne des Active Directory-Kontos, das von der Azure AD Connect-Synchronisierung zum Verwalten von Objekten im Verzeichnis verwendet wird.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Der DistinguishedName des Active Directory-Kontos, das von der Azure AD Connect-Synchronisierung zum Verwalten von Objekten im Verzeichnis verwendet wird.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
Der „DistinguishedName“ des-AD-Zielobjekts zum Festlegen von Berechtigungen (optional).

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Optionaler Parameter, um anzuzeigen, ob der AdminSDHolder-Container nicht mit diesen Berechtigungen aktualisiert werden sollte.

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

## <a name="set-adsyncrestrictedpermissions"></a>Set-ADSyncRestrictedPermissions

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Verschärft Berechtigungen für ein AD-Objekt, das sonst in keiner geschützten AD-Sicherheitsgruppe enthalten ist.
Ein typisches Beispiel ist das AD Connect-Konto (MSOL), das automatisch von AAD Connect erstellt wird.
Dieses Konto verfügt über Berechtigungen zum Replizieren für alle Domänen, lässt sich aber leicht kompromittieren, weil es nicht geschützt ist.

### <a name="syntax"></a>SYNTAX

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>Beschreibung
Die Funktion „Set-ADSyncRestrictedPermissions“ verschärft die Berechtigungen des bereitgestellten Kontos.
Das Verschärfen von Berechtigungen umfasst die folgenden Schritte:
1.
Deaktivieren der Vererbung für das angegebene Objekt
2.
Entfernen aller ACEs für das angegebene Objekt, mit Ausnahme von ACEs für SELF
Wir möchten die Standardberechtigungen für SELF beibehalten.
3.
Weisen Sie diese speziellen Berechtigungen zu:

        Type    Name                                        Access              Applies To
        =============================================================================================
        Allow   SYSTEM                                      Full Control        This object
        Allow   Enterprise Admins                           Full Control        This object
        Allow   Domain Admins                               Full Control        This object
        Allow   Administrators                              Full Control        This object

        Allow   Enterprise Domain Controllers               List Contents
                                                            Read All Properties
                                                            Read Permissions    This object

        Allow   Authenticated Users                         List Contents
                                                            Read All Properties
                                                            Read Permissions    This object

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN "CN=TestAccount1,CN=Users,DC=Contoso,DC=com" -Credential $(Get-Credential)
```

### <a name="parameters"></a>PARAMETER

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Der „DistinguishedName“ des Active Directory-Kontos, dessen Berechtigungen verschärft werden müssen.
Dies ist normalerweise das Konto „MSOL_nnnnnnnnnn“ oder ein benutzerdefiniertes Domänenkonto, das in Ihrem AD-Connector konfiguriert ist.

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

#### <a name="-credential"></a>-Credential
Anmeldeinformationen des Administrators, der über die erforderlichen Berechtigungen zum Einschränken der Berechtigungen des Kontos „ADConnectorAccountDN“ verfügt. Dies ist meist der Unternehmens- oder Domänenadministrator. Verwenden Sie den vollqualifizierten Domänennamen des Administratorkontos, um Fehler bei der Kontosuche zu vermeiden.
Beispiel: CONTOSO\admin

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-disablecredentialvalidation"></a>-DisableCredentialValidation
Wenn „DisableCredentialValidation“ verwendet wird, überprüft die Funktion weder, ob die in „-Credential“ bereitgestellten Anmeldeinformationen in AD gültig sind, noch ob das angegebene Konto die erforderlichen Berechtigungen zum Einschränken der Berechtigungen für das Konto „ADConnectorAccountDN“ besitzt.

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

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Set-ADSyncUnifiedGroupWritebackPermissions

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Initialisiert Ihre Active Directory-Gesamtstruktur und Domäne für das Gruppenzurückschreiben aus Azure AD.

### <a name="syntax"></a>SYNTAX

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>Beschreibung
Die Funktion „Set-ADSyncUnifiedGroupWritebackPermissions“ gewährt dem AD-Synchronisierungskonto die erforderlichen Berechtigungen, darunter die folgenden:
1.
„Generisches Lesen/Schreiben“, „Löschen“, „Struktur löschen“ und „Untergeordnetes Objekt erstellen/löschen“ für alle Gruppenobjekttypen und Unterobjekte

Diese Berechtigungen gelten für alle Domänen in der Gesamtstruktur.
Optional können Sie einen „DistinguishedName“ im Parameter „ADobjectDN“ bereitstellen, um diese Berechtigungen nur für das AD-Objekt (einschließlich Vererbung an Unterobjekte) festzulegen.
In diesem Fall ist „ADobjectDN“ der Distinguished Name des Containers, den Sie mit der GroupWriteback-Funktion verknüpfen möchten.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>BEISPIEL 2
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>BEISPIEL 3
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>BEISPIEL 4
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETER

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Der Name des Active Directory-Kontos, das von der Azure AD Connect-Synchronisierung zum Verwalten von Objekten im Verzeichnis verwendet wird.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Die Domäne des Active Directory-Kontos, das von der Azure AD Connect-Synchronisierung zum Verwalten von Objekten im Verzeichnis verwendet wird.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Der DistinguishedName des Active Directory-Kontos, das von der Azure AD Connect-Synchronisierung zum Verwalten von Objekten im Verzeichnis verwendet wird.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
Der „DistinguishedName“ des-AD-Zielobjekts zum Festlegen von Berechtigungen (optional).

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Optionaler Parameter, um anzuzeigen, ob der AdminSDHolder-Container nicht mit diesen Berechtigungen aktualisiert werden sollte.

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

## <a name="show-adsyncadobjectpermissions"></a>Show-ADSyncADObjectPermissions

### <a name="synopsis"></a>ZUSAMMENFASSUNG
Zeigt Berechtigungen eines angegebenen AD-Objekts an.

### <a name="syntax"></a>SYNTAX

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>Beschreibung
Diese Funktion gibt alle aktuell für ein bestimmtes, im Parameter „-ADobjectDN“ angegebenes AD-Objekt festgelegten AD-Berechtigungen zurück.
Der „ADobjectDN“ muss in einem „DistinguishedName“-Format angegeben werden.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1
```
Show-ADSyncADObjectPermissions -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETER

#### <a name="-adobjectdn"></a>-ADobjectDN
{{ADobjectDN-Beschreibung eintragen}}

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

#### <a name="commonparameters"></a>CommonParameters
Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).
