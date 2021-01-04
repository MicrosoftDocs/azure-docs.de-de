---
title: PowerShell V2-Beispiele für die Verwaltung von Gruppen – Azure AD | Microsoft-Dokumentation
description: Auf dieser Seite finden Sie PowerShell-Beispiele für die Verwaltung von Gruppen in Azure Active Directory.
keywords: Azure AD, Azure Active Directory, PowerShell, Gruppen, Gruppenverwaltung
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12613362d9fc67dea14b41ece19a34febb489dda
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860642"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Azure Active Directory-Cmdlets Version 2 für die Gruppenverwaltung

> [!div class="op_single_selector"]
> - [Azure portal](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
> - [PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md)
>
>

Dieser Artikel enthält Beispiele für die Verwendung von PowerShell zur Verwaltung von Gruppen in Azure Active Directory (Azure AD).  Darüber hinaus erfahren Sie, wie das Azure AD PowerShell-Modul eingerichtet wird. Zunächst müssen Sie [das Azure AD PowerShell-Modul herunterladen](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Installieren des Azure AD PowerShell-Moduls

Verwenden Sie die folgenden Befehle, um das Azure AD PowerShell-Modul zu installieren:

```powershell
    PS C:\Windows\system32> install-module azuread
    PS C:\Windows\system32> import-module azuread
```

Überprüfen Sie mithilfe des folgenden Befehls, ob das Modul verwendet werden kann:

```powershell
    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

Sie können die Cmdlets jetzt im Modul verwenden. Eine ausführliche Beschreibung der Cmdlets im Azure AD-Modul finden Sie in der Onlinereferenzdokumentation für [Azure Active Directory PowerShell Version 2](/powershell/azure/active-directory/install-adv2).

> [!NOTE]
> Die Azure AD PowerShell-Cmdlets funktionieren nicht mit der neuen Version von PowerShell (Version 7), da diese auf .NET Core basiert. Wir arbeiten derzeit an einem Update, um dieses Problem zu beheben. In der Zwischenzeit verwenden Sie am besten das Windows PowerShell 5.x-Modul für Azure AD PowerShell-Vorgänge. 


## <a name="connect-to-the-directory"></a>Herstellen einer Verbindung mit dem Verzeichnis

Bevor Sie Gruppen mithilfe der Azure AD PowerShell-Cmdlets verwalten können, müssen Sie Ihre PowerShell-Sitzung mit dem Verzeichnis verbinden, das verwaltet werden soll. Verwenden Sie den folgenden Befehl:

```powershell
    PS C:\Windows\system32> Connect-AzureAD
```

Das Cmdlet fordert Sie zur Eingabe der Anmeldeinformationen auf, die Sie für den Zugriff auf Ihr Verzeichnis verwenden möchten. In diesem Beispiel verwenden wir „ karen@drumkit.onmicrosoft.com “ für den Zugriff auf das Demoverzeichnis. Das Cmdlet gibt eine Bestätigung zurück, die angibt, dass die Sitzung mit Ihrem Verzeichnis verbunden wurde:

```powershell
    Account                       Environment Tenant ID
    -------                       ----------- ---------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…
```

Sie können die Azure AD-Cmdlets nun zum Verwalten von Gruppen in Ihrem Verzeichnis verwenden.

## <a name="retrieve-groups"></a>Abrufen von Gruppen

Mit dem Cmdlet „Get-AzureADGroups“ können Sie vorhandene Gruppen aus Ihrem Verzeichnis abrufen. 

Um alle Gruppen innerhalb des Verzeichnisses abzurufen, verwenden Sie das Cmdlet ohne Parameter:

```powershell
    PS C:\Windows\system32> get-azureadgroup
```

Das Cmdlet gibt alle Gruppen innerhalb des verbundenen Verzeichnisses zurück.

Zum Abrufen einer bestimmten Gruppe verwenden Sie den Parameter „-objectID“ und geben die Objekt-ID der Gruppe an:

```powershell
    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b
```

Das Cmdlet gibt nun die Gruppe zurück, deren objectID-Wert mit dem eingegebenen Wert des Parameters übereinstimmt:

```powershell
    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

Mithilfe des Parameters „-filter“ können Sie nach einer bestimmten Gruppe suchen. Dieser Parameter verwendet eine ODATA-Filterklausel und gibt alle Gruppen zurück, die dem Filter entsprechen. Beispiel:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

> [!NOTE]
> In den Azure AD PowerShell-Cmdlets wird der OData-Abfragestandard verwendet. Weitere Informationen finden Sie unter **$filter** im Artikel [OData-Systemabfrageoptionen mit dem OData-Endpunkt](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)#BKMK_filter).

## <a name="create-groups"></a>Erstellen von Gruppen

Zum Erstellen einer neuen Gruppe in Ihrem Verzeichnis verwenden Sie das Cmdlet „New-AzureADGroup“. Dieses Cmdlet erstellt eine neue Sicherheitsgruppe „Marketing“:

```powershell
    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"
```

## <a name="update-groups"></a>Aktualisieren von Gruppen

Zum Aktualisieren einer vorhandenen Gruppe verwenden Sie das Cmdlet „Set-AzureADGroup“. In diesem Beispiel ändern wir die Eigenschaft „DisplayName“ der Gruppe „Intune Administrators“. Zunächst suchen wir mit dem Cmdlet „Get-AzureADGroup“ nach der Gruppe. Dabei filtern wir das Ergebnis mithilfe des Attributs „DisplayName“:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

Anschließend ändern wir die Eigenschaft „Description“ in den neuen Wert „Intune Device Administrators“:

```powershell
    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"
```

Wenn wir nun erneut nach der Gruppe suchen, sehen wir, dass die Eigenschaft „Description“ mit dem neuen Wert aktualisiert wurde:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"

    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

## <a name="delete-groups"></a>Löschen von Gruppen

Zum Löschen von Gruppen aus Ihrem Verzeichnis verwenden Sie das Cmdlet „Remove-AzureADGroup“ wie folgt:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b
```

## <a name="manage-group-membership"></a>Verwalten der Gruppenmitgliedschaft

### <a name="add-members"></a>Hinzufügen von Mitgliedern

Wenn Sie einer Gruppe neue Mitglieder hinzufügen möchten, verwenden Sie das Cmdlet „Add-AzureADGroupMember“. Mit diesem Befehl wird der Gruppe „Intune Administrators“ aus dem vorherigen Beispiel ein Mitglied hinzugefügt:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

Der Parameter „-ObjectId“ ist die Objekt-ID der Gruppe, der ein Mitglied hinzugefügt werden soll, „-RefObjectId“ ist die Objekt-ID des Benutzers, der als Gruppenmitglied hinzugefügt werden soll.

### <a name="get-members"></a>Abrufen von Mitgliedern

Zum Abrufen der vorhandenen Mitglieder einer Gruppe verwenden Sie das Cmdlet „Get-AzureADGroupMember“, wie in diesem Beispiel gezeigt:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User
```

### <a name="remove-members"></a>Entfernen von Mitgliedern

Um das Mitglied zu entfernen, das der Gruppe zuvor hinzugefügt wurde, verwenden Sie das Cmdlet „Remove-AzureADGroupMember“, wie hier gezeigt:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

### <a name="verify-members"></a>Überprüfen von Mitgliedern

Die Gruppenmitgliedschaften eines Benutzers können mithilfe des Cmdlets „Select-AzureADGroupIdsUserIsMemberOf“ überprüft werden. Dieses Cmdlet verwendet als Parameter die Objekt-ID des Benutzers, für den die Gruppenmitgliedschaften überprüft werden sollen, sowie eine Liste mit Gruppen, für die die Mitgliedschaften überprüft werden sollen. Die Liste der Gruppen muss in Form einer komplexen Variable vom Typ „Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck“ angegeben werden. Daher müssen wir zunächst eine Variable dieses Typs erstellen:

```powershell
    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck
```

Anschließend geben wir Werte für die Gruppen-IDs an, die im Attribut „GroupIds“ dieser komplexen Variable überprüft werden sollen:

```powershell
    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"
```

Wenn z.B. die Gruppenmitgliedschaften eines Benutzers mit der Objekt-ID „72cd4bbd-2594-40a2-935c-016f3cfeeeea“ für die Gruppen in „$g“ überprüft werden sollen, verwenden wir folgenden Befehl:

```powershell
    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}
```

Der zurückgegebene Wert ist eine Liste mit Gruppen, in denen dieser Benutzer Mitglied ist. Diese Methode kann auch verwendet werden, um die Mitgliedschaft in Kontaktlisten, Gruppen oder Dienstprinzipalen für eine Liste von Gruppen zu überprüfen. Verwenden Sie dazu „Select-AzureADGroupIdsContactIsMemberOf“, „Select-AzureADGroupIdsGroupIsMemberOf“ oder „Select-AzureADGroupIdsServicePrincipalIsMemberOf“.

## <a name="disable-group-creation-by-your-users"></a>Deaktivieren der Gruppenerstellung durch Benutzer

Sie können dafür sorgen, dass Benutzer ohne Administratorrechte keine Sicherheitsgruppen erstellen können. In Microsoft Online Directory Services (MSODS) sind Benutzer ohne Administratorrechte standardmäßig zum Erstellen von Gruppen berechtigt. Dabei spielt es keine Rolle, ob auch die Self-Service-Gruppenverwaltung (self-Service Group Management, SSGM) aktiviert ist. Die SSGM-Einstellung steuert nur das Verhalten im Zugriffsbereich „Meine Apps“.

So deaktivieren Sie die Gruppenerstellung für Benutzer ohne Administratorrechte:

1. Prüfen Sie, ob Benutzer ohne Administratorrechte zum Erstellen von Gruppen berechtigt sind:
   
   ```powershell
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. Wird `UsersPermissionToCreateGroupsEnabled : True` zurückgegeben, sind Benutzer ohne Administratorrechte zum Erstellen von Gruppen berechtigt. So deaktivieren Sie das Feature:
  
   ```powershell 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```
  
## <a name="manage-owners-of-groups"></a>Verwalten von Gruppenbesitzern

Zum Hinzufügen von Besitzern zu einer Gruppe verwenden Sie das Cmdlet „Add-AzureADGroupOwner“:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

Der Parameter -ObjectId ist die Objekt-ID der Gruppe, der ein Besitzer hinzugefügt werden soll, -RefObjectId ist die Objekt-ID des Benutzers oder Dienstprinzipals, der als Besitzer der Gruppe hinzugefügt werden soll.

Zum Abrufen der Besitzer einer Gruppe verwenden Sie das Cmdlet „Get-AzureADGroupOwner“:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
```

Das Cmdlet gibt die Liste der Besitzer (Benutzer und Dienstprinzipale) für die angegebene Gruppe zurück:

```powershell
    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User
```

Zum Entfernen eines Besitzers aus einer Gruppe verwenden Sie das Cmdlet „Remove-AzureADGroupOwner“:

```powershell
    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67
```

## <a name="reserved-aliases"></a>Reservierte Aliase

Wenn eine Gruppe erstellt wird, ermöglichen bestimmte Endpunkte dem Benutzer, einen mailNickname oder Alias anzugeben, der als Teil der E-Mail-Adresse der Gruppe verwendet werden soll.  Gruppen mit den folgenden weitreichend berechtigten E-Mail-Aliasen können nur von einem globalen Azure AD-Administrator erstellt werden. 
  
* abuse
* admin
* administrator
* hostmaster
* majordomo
* postmaster
* root
* secure
* security
* ssl-admin
* webmaster

## <a name="group-writeback-to-on-premises-preview"></a>Rückschreiben von Gruppen in das lokale Azure AD (Vorschau)

Heutzutage werden viele Gruppen immer noch im lokalen Active Directory verwaltet. Als Reaktion auf Anforderungen zum Synchronisieren von Cloudgruppen mit der lokalen Instanz ist das Microsoft 365-Gruppenrückschreiben für Azure AD jetzt als Vorschaufunktion verfügbar.

Microsoft 365-Gruppen werden in der Cloud erstellt und verwaltet. Mit der Funktion zum Rückschreiben können Sie Microsoft 365-Gruppen als Verteilergruppen in eine Active Directory-Gesamtstruktur bei installiertem Exchange zurückschreiben. Benutzer mit lokalen Exchange-Postfächern können E-Mails von diesen Gruppen senden und empfangen. Das Feature „Gruppenrückschreiben“ unterstützt keine Azure AD-Sicherheitsgruppen oder -Verteilergruppen.

Weitere Informationen finden Sie in der Dokumentation für den [Azure AD Connect-Synchronisierungsdienst](../hybrid/how-to-connect-syncservice-features.md).

Microsoft 365-Gruppenrückschreiben ist eine Funktion in der öffentlichen Vorschauversion von Azure Active Directory (Azure AD) und mit jedem kostenpflichtigen Azure AD-Lizenzplan verfügbar. Rechtliche Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Active Directory PowerShell finden Sie unter [Azure Active Directory-Cmdlets](/powershell/azure/active-directory/install-adv2).

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](../fundamentals/active-directory-manage-groups.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Integrieren lokaler Identitäten in Azure Active Directory](../hybrid/whatis-hybrid-identity.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
