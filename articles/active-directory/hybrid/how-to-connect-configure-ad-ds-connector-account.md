---
title: 'Azure AD Connect: Konfigurieren von AD DS-Connector-Kontoberechtigungen | Microsoft-Dokumentation'
description: In diesem Dokument wird erläutert, wie Sie das AD DS-Connector-Konto mit dem neuen ADSyncConfig PowerShell-Modul konfigurieren.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 62bfc528886767bc09159ca2a2696c8c9264b307
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349938"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: Konfigurieren der Azure AD-Connector-Kontoberechtigungen 

Mit Build 1.1.880.0 (veröffentlicht im August 2018) wurde das PowerShell-Modul namens [ADSyncConfig.psm1](reference-connect-adsyncconfig.md) eingeführt, das eine Sammlung von Cmdlets enthält, die Sie beim Konfigurieren der richtigen Active Directory-Berechtigungen für Ihre Azure AD Connect-Bereitstellung unterstützen. 

## <a name="overview"></a>Übersicht 
Die folgenden PowerShell-Cmdlets können verwendet werden, um Active Directory-Berechtigungen des AD DS-Connector-Kontos für jede Funktion einzurichten, die Sie in Azure AD Connect zum Aktivieren auswählen. Um Probleme zu vermeiden, sollten Sie immer dann Active Directory-Berechtigungen im Voraus vorbereiten, wenn Sie Azure AD Connect so installieren möchten, dass es mithilfe eines benutzerdefinierten Domänenkontos eine Verbindung mit Ihrer Gesamtstruktur herstellt. Dieses ADSyncConfig-Modul kann auch verwendet werden, um Berechtigungen zu konfigurieren, nachdem Azure AD Connect bereitgestellt wurde.

![Übersicht über AD DS-Konten](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

Für eine Azure AD Connect-Expressinstallation wird ein automatisch generiertes Konto (MSOL_nnnnnnnnnn) in Active Directory mit allen notwendigen Berechtigungen erstellt, weshalb Sie dieses ADSyncConfig-Modul nur verwenden müssen, wenn Sie die Vererbung von Berechtigungen für Organisationseinheiten oder für bestimmte Active Directory-Objekte gesperrt haben, die Sie mit Azure AD synchronisieren möchten. 
 
### <a name="permissions-summary"></a>Zusammenfassung der Berechtigungen 
Die folgende Tabelle bietet eine Zusammenfassung der für AD-Objekte erforderlichen Berechtigungen: 

| Funktion | Berechtigungen |
| --- | --- |
| ms-DS-ConsistencyGuid |Lese- und Schreibberechtigungen für das Attribut „ms-DS-ConsistencyGuid“, das unter [Entwurfskonzepte – Verwendung von „ms-DS-ConsistencyGuid“ als „sourceAnchor“](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) dokumentiert ist. | 
| Kennworthashsynchronisierung |<li>Verzeichnisänderungen replizieren</li>  <li>Verzeichnisänderungen replizieren: Alle |
| Exchange-Hybridbereitstellung |Lese- und Schreibberechtigungen für die Attribute, die in [Exchange-Hybridrückschreiben](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) für Benutzer, Gruppen und Kontakte dokumentiert sind. |
| Öffentlicher Exchange-E-Mail-Ordner |Leseberechtigungen für die Attribute, die im [öffentlichen Exchange-E-Mail-Ordner](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) für öffentliche Ordner dokumentiert sind. | 
| Kennwortrückschreiben |Lese- und Schreibberechtigungen für die Attribute, die in [Erste Schritte mit der Kennwortverwaltung](../authentication/tutorial-enable-sspr-writeback.md) für Benutzer dokumentiert sind. |
| Geräterückschreiben |Lese- und Schreibberechtigungen für Geräteobjekte und Container, die in [Geräterückschreiben](how-to-connect-device-writeback.md) dokumentiert sind. |
| Gruppenrückschreiben |Lesen, Erstellen, Aktualisieren und Löschen von Gruppenobjekten für synchronisierte **Office 365-Gruppen**.|

## <a name="using-the-adsyncconfig-powershell-module"></a>Verwenden des ADSyncConfig PowerShell-Moduls 
Das ADSyncConfig-Modul erfordert die [Remoteserver-Verwaltungstools (RSAT) für AD DS](/windows-server/remote/remote-server-administration-tools), weil es vom AD DS PowerShell-Modul und den Tools abhängig ist. Um RSAT für AD DS zu installieren, öffnen Sie ein Windows PowerShell-Fenster mit „Als Administrator ausführen“, und führen Sie Folgendes aus: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Konfigurieren](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>Sie können auch die Datei **C:\Programme\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** in einen Domänencontroller kopieren, auf dem bereits RSAT für AD DS installiert ist, und dieses PowerShell-Modul von dort aus verwenden.

Um mit der Verwendung von ADSyncConfig zu beginnen, müssen Sie das Modul in ein Windows PowerShell-Fenster laden: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

Um alle in diesem Modul enthaltenen Cmdlets zu überprüfen, können Sie Folgendes eingeben:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![Prüfen](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Jedes Cmdlet hat dieselben Eingabeparameter, das AD DS-Connector-Konto und einen AdminSDHolder-Parameter. Um Ihre AD DS-Connector-Konto anzugeben, können Sie den Kontonamen und die Domäne angeben oder nur den DN des Kontos (Distinguished Name),

z. B.:

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

Oder

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

Stellen Sie sicher, dass Sie `<ADAccountName>`, `<ADDomainName>` und `<ADAccountDN>` durch die entsprechenden Werte für Ihre Umgebung ersetzen.

Für den Fall, dass Sie keine Berechtigungen des AdminSDHolder-Containers ändern möchten, verwenden Sie den Parameter `-SkipAdminSdHolders`. 

Standardmäßig versuchen alle Cmdlets zum Festlegen von Berechtigungen AD DS-Berechtigungen im Stamm jeder Domäne in der Gesamtstruktur festzulegen, was bedeutet, dass der Benutzer, der die PowerShell-Sitzung ausführt, Domänenadministratorberechtigungen für jede Domäne in der Gesamtstruktur benötigt.  Aufgrund dieser Anforderung wird empfohlen, einen Unternehmensadministrator aus dem Gesamtstrukturstamm zu verwenden. Wenn Ihre Azure AD Connect-Bereitstellung mehrere AD DS-Connectors besitzt, ist es erforderlich, dasselbe Cmdlet in jeder Gesamtstruktur auszuführen, die über einen AD DS-Connector verfügt. 

Sie können auch Berechtigungen für eine bestimmte Organisationseinheit oder ein bestimmtes AD DS-Objekt festlegen, indem Sie den Parameter `-ADobjectDN` verwenden, gefolgt vom DN des Zielobjekts, für das Sie die Berechtigungen festlegen möchten. Wenn Sie einen Ziel-ADobjectDN verwenden, legt das Cmdlet Berechtigungen nur für dieses Objekt und nicht für den Domänenstamm- oder AdminSDHolder-Container fest. Dieser Parameter kann nützlich sein, wenn Sie bestimmte Organisationseinheiten oder AD DS-Objekte haben, für die die Berechtigungsvererbung deaktiviert ist (siehe „Auffinden von AD DS-Objekten mit deaktivierter Berechtigungsvererbung“). 

Ausnahmen von diesen allgemeinen Parametern sind das Cmdlet `Set-ADSyncRestrictedPermissions`, das verwendet wird, um die Berechtigungen für das AD DS-Connector-Konto selbst festzulegen, und das Cmldet `Set-ADSyncPasswordHashSyncPermissions` Cmdlet, da die für die Kennworthashsynchronisierung erforderlichen Berechtigungen nur auf Ebene des Domänenstamms festgelegt werden, weshalb dieses Cmdlet die Parameter `-ObjectDN` oder `-SkipAdminSdHolders` nicht einschließt.

### <a name="determine-your-ad-ds-connector-account"></a>Bestimmen Ihres AD DS-Connector-Kontos 
Falls Azure AD Connect bereits installiert ist, und Sie überprüfen möchten, welches das aktuell von Azure AD Connect verwendete AD DS-Connector-Konto ist, können Sie das Cmdlet ausführen: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>Auffinden von AD DS-Objekten mit deaktivierter Berechtigungsvererbung 
Falls Sie überprüfen möchten, ob AD DS-Objekte vorhanden sind, für die die Vererbung von Berechtigungen deaktiviert ist, können Sie Folgendes ausführen: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Standardmäßig sucht dieses Cmdlet nur nach Organisationseinheiten mit deaktivierter Vererbung, aber Sie können weitere AD DS-Objektklassen über den Parameter `-ObjectClass` angeben, oder Sie verwenden „*“ für alle Objektklassen wie folgt: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>Anzeigen der AD DS-Berechtigungen eines Objekts 
Sie können das unten angegebene Cmdlet verwenden, um die Liste der aktuell für ein Active Directory-Objekt festgelegten Berechtigungen anzuzeigen, indem Sie seinen DN (DistinguishedName) angeben: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>Konfigurieren der Azure AD-Connector-Kontoberechtigungen 
 
### <a name="configure-basic-read-only-permissions"></a>Konfigurieren grundlegender Nur-Lesen-Berechtigungen 
Um grundlegende Nur-Lesen-Berechtigungen für das AD DS-Connector-Konto festzulegen, wenn keine Azure AD Connect-Funktionen verwendet werden, führen Sie Folgendes aus: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


oder 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


Dieses Cmdlet legt die folgenden Berechtigungen fest: 
 

|type |Name |Zugriff |Gilt für| 
|-----|-----|-----|-----|
|Allow |AD DS-Connector-Konto |Alle Eigenschaften lesen |Nachfolger-Geräteobjekte| 
|Allow |AD DS-Connector-Konto|Alle Eigenschaften lesen |Nachfolger-InetOrgPerson-Objekte| 
|Allow |AD DS-Connector-Konto |Alle Eigenschaften lesen |Nachfolger-Computerobjekte| 
|Allow |AD DS-Connector-Konto |Alle Eigenschaften lesen |Nachfolger-foreignSecurityPrincipal-Objekte| 
|Allow |AD DS-Connector-Konto |Alle Eigenschaften lesen |Nachfolger-Gruppenobjekte| 
|Allow |AD DS-Connector-Konto |Alle Eigenschaften lesen |Nachfolger-Benutzerobjekte| 
|Allow |AD DS-Connector-Konto |Alle Eigenschaften lesen |Nachfolger-Kontaktobjekte| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>Konfigurieren von MS-DS-Consistency-Guid-Berechtigungen 
Um Berechtigungen für das AD DS-Connector-Konto festzulegen, wenn Sie das Attribut „ms-Ds-Consistency-Guid“ als Quellanker verwenden (auch bekannt als Option „Ich möchte den Quellanker durch Azure verwalten lassen“), führen Sie Folgendes aus: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

oder 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Dieses Cmdlet legt die folgenden Berechtigungen fest: 

|type |Name |Zugriff |Gilt für|
|-----|-----|-----|-----| 
|Allow|AD DS-Connector-Konto|Lese-/Schreibeigenschaft|Nachfolger-Benutzerobjekte|

### <a name="permissions-for-password-hash-synchronization"></a>Berechtigungen für die Kennworthashsynchronisierung 
Um Berechtigungen für das AD DS-Connector-Konto festzulegen, wenn Kennworthashsynchronisierung verwendet wird, führen Sie Folgendes aus: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


oder 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

Dieses Cmdlet legt die folgenden Berechtigungen fest: 

|type |Name |Zugriff |Gilt für|
|-----|-----|-----|-----| 
|Allow |AD DS-Connector-Konto |Replizieren von Verzeichnisänderungen |Nur dieses Objekt (Domänenstamm)| 
|Allow |AD DS-Connector-Konto |Replizieren von Verzeichnisänderungen: Alle |Nur dieses Objekt (Domänenstamm)| 
  
### <a name="permissions-for-password-writeback"></a>Berechtigungen für das Kennwortrückschreiben 
Um Berechtigungen für das AD DS-Connector-Konto festzulegen, wenn Kennwortrückschreiben verwendet wird, führen Sie Folgendes aus: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


oder

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Dieses Cmdlet legt die folgenden Berechtigungen fest: 

|type |Name |Zugriff |Gilt für|
|-----|-----|-----|-----| 
|Allow |AD DS-Connector-Konto |Kennwort zurücksetzen |Nachfolger-Benutzerobjekte| 
|Allow |AD DS-Connector-Konto |Schreiben für Eigenschaft „LockoutTime“ |Nachfolger-Benutzerobjekte| 
|Allow |AD DS-Connector-Konto |Schreiben für Eigenschaft „pwdLastSet“ |Nachfolger-Benutzerobjekte| 

### <a name="permissions-for-group-writeback"></a>Berechtigungen für das Gruppenrückschreiben 
Um Berechtigungen für das AD DS-Connector-Konto festzulegen, wenn Gruppenrückschreiben verwendet wird, führen Sie Folgendes aus: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
oder 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
Dieses Cmdlet legt die folgenden Berechtigungen fest: 

|type |Name |Zugriff |Gilt für|
|-----|-----|-----|-----| 
|Allow |AD DS-Connector-Konto |Generisches Lesen/Schreiben |Alle Attribute einer Objekttypgruppe und von Unterobjekten| 
|Allow |AD DS-Connector-Konto |Erstellen/Löschen von untergeordneten Objekten |Alle Attribute einer Objekttypgruppe und von Unterobjekten| 
|Allow |AD DS-Connector-Konto |Löschen/Löschen von Strukturobjekten|Alle Attribute einer Objekttypgruppe und von Unterobjekten|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Berechtigungen für eine Exchange-Hybridbereitstellung 
Um Berechtigungen für das AD DS-Connector-Konto festzulegen, wenn eine Exchange-Hybridbereitstellung verwendet wird, führen Sie Folgendes aus: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


oder 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Dieses Cmdlet legt die folgenden Berechtigungen fest:  
 

|type |Name |Zugriff |Gilt für|
|-----|-----|-----|-----| 
|Allow |AD DS-Connector-Konto |Lesen/Schreiben für alle Eigenschaften |Nachfolger-Benutzerobjekte| 
|Allow |AD DS-Connector-Konto |Lesen/Schreiben für alle Eigenschaften |Nachfolger-InetOrgPerson-Objekte| 
|Allow |AD DS-Connector-Konto |Lesen/Schreiben für alle Eigenschaften |Nachfolger-Gruppenobjekte| 
|Allow |AD DS-Connector-Konto |Lesen/Schreiben für alle Eigenschaften |Nachfolger-Kontaktobjekte| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Berechtigungen für öffentliche Exchange-E-Mail-Ordner (Vorschau) 
Um Berechtigungen für das AD DS-Connector-Konto festzulegen, wenn die Funktion für öffentliche Exchange-E-Mail-Ordner verwendet wird, führen Sie Folgendes aus: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


oder 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Dieses Cmdlet legt die folgenden Berechtigungen fest: 

|type |Name |Zugriff |Gilt für|
|-----|-----|-----|-----| 
|Allow |AD DS-Connector-Konto |Alle Eigenschaften lesen |Nachfolger-PublicFolder-Objekte| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>Einschränken der Berechtigungen für das AD DS-Connector-Konto 
Dieses PowerShell-Skript verschärft die Berechtigungen für das AD-Connector-Konto, das als Parameter angegeben wird. Das Verschärfen von Berechtigungen umfasst die folgenden Schritte: 

- Deaktivieren der Vererbung für das angegebene Objekt 
- Entfernen aller ACEs für das angegebene Objekt, mit Ausnahme von für SELF spezifischen ACEs, da wir die Standardberechtigungen bezüglich SELF intakt lassen möchten. 
 
  Der Parameter „-ADConnectorAccountDN“ ist das AD-Konto, dessen Berechtigungen verschärft werden sollen. Dies ist normalerweise das Domänenkonto „MSOL_nnnnnnnnnnnn“, das im AD DS-Connector konfiguriert ist (siehe „Bestimmen Ihres AD DS-Connector-Kontos“). Der Parameter „-Credential“ ist erforderlich, um das Administratorkonto anzugeben, das die erforderlichen Berechtigungen besitzt, um Active Directory-Berechtigungen für das AD-Zielobjekt einzuschränken. Dies ist normalerweise der Unternehmens- oder Domänenadministrator.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Beispiel: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

Dieses Cmdlet legt die folgenden Berechtigungen fest: 

|type |Name |Zugriff |Gilt für|
|-----|-----|-----|-----| 
|Allow |SYSTEM |Vollzugriff |Dieses Objekt 
|Allow |Organisationsadministratoren |Vollzugriff |Dieses Objekt 
|Allow |Domänenadministratoren |Vollzugriff |Dieses Objekt 
|Allow |Administratoren |Vollzugriff |Dieses Objekt 
|Allow |Domänencontroller des Unternehmens |Inhalt auflisten |Dieses Objekt 
|Allow |Domänencontroller des Unternehmens |Alle Eigenschaften lesen |Dieses Objekt 
|Allow |Domänencontroller des Unternehmens |Leseberechtigungen |Dieses Objekt 
|Allow |Authentifizierte Benutzer |Inhalt auflisten |Dieses Objekt 
|Allow |Authentifizierte Benutzer |Alle Eigenschaften lesen |Dieses Objekt 
|Allow |Authentifizierte Benutzer |Leseberechtigungen |Dieses Objekt 

## <a name="next-steps"></a>Nächste Schritte
- [Azure AD Connect: Konten und Berechtigungen](reference-connect-accounts-permissions.md)
- [Expressinstallation](how-to-connect-install-express.md)
- [Benutzerdefinierte Installation](how-to-connect-install-custom.md)
- [Referenz zu ADSyncConfig](reference-connect-adsyncconfig.md)