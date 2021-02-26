---
title: Zulassen oder Blockieren von Einladungen für bestimmte Organisationen – Azure AD
description: In diesem Artikel wird beschrieben, wie ein Administrator über das Azure-Portal oder mithilfe von PowerShell eine Zugriffs- oder Verweigerungsliste zum Zulassen oder Blockieren von B2B-Benutzern von bestimmten Domänen festlegen kann.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb5a785cba4a560fc15963363d4e5f6b811af97a
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99548830"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Zulassen oder Blockieren von Einladungen für B2B-Benutzer von bestimmten Organisationen

Sie können eine Zulassungsliste oder eine Verweigerungsliste verwenden, um Einladungen für B2B-Benutzer von bestimmten Organisationen zuzulassen bzw. zu blockieren. Wenn Sie beispielsweise Domänen persönlicher E-Mail-Adressen blockieren möchten, können Sie eine Verweigerungsliste einrichten, die Domänen wie Gmail.com und Outlook.com enthält. Oder wenn zwischen Ihrem Unternehmen und anderen Unternehmen (z.B. Contoso.com, Fabrikam.com, und Litware.com) eine Partnerschaft besteht und Sie Einladungen nur auf diese Unternehmen beschränken möchten, können Sie Ihrer Zulassungsliste Contoso.com, Fabrikam.com, und Litware.com hinzufügen.
  
## <a name="important-considerations"></a>Wichtige Hinweise

- Sie können entweder eine Zulassungsliste oder eine Verweigerungsliste erstellen. Sie können jedoch nicht beide Listentypen einrichten. Standardmäßig sind alle Domänen, die nicht in der Zulassungsliste aufgeführt sind, in der Verweigerungsliste enthalten (und umgekehrt). 
- Sie können nur jeweils eine Richtlinie pro Organisation erstellen. Sie können die Richtlinie so aktualisieren, dass sie weitere Domänen enthält, oder Sie können die Richtlinie löschen und eine neue erstellen. 
- Die Anzahl der Domänen, die Sie einer Zulassungs- oder Verweigerungsliste hinzufügen können, wird nur durch die Richtliniengröße eingeschränkt. Dieser Grenzwert bezieht sich auf die Anzahl der Zeichen, sodass Sie mehrere kurze oder wenigere lange Domänen angeben können. Die Maximalgröße der gesamten Richtlinie beträgt 25 KB (25.000 Zeichen) einschließlich der Zulassungs- oder Verweigerungsliste sowie aller weiteren Parameter, die für andere Features konfiguriert sind.
- Diese Liste ist unabhängig von Zulassungs- oder Blockierungslisten für OneDrive for Business und SharePoint Online. Wenn Sie einzelne Dateifreigaben in SharePoint Online einschränken möchten, müssen Sie eine Zulassungs- oder Verweigerungsliste für OneDrive for Business und SharePoint Online einrichten. Weitere Informationen finden Sie unter [Eingeschränkte Domänenfreigabe in SharePoint Online und OneDrive for Business](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- Diese Liste gilt nicht für externe Benutzer, die die Einladung bereits eingelöst haben. Die Liste wird nach dem Einrichten erzwungen. Wenn eine Benutzereinladung ausstehend ist und Sie eine Richtlinie festlegen, die die Domäne des Benutzers blockiert, kann der Benutzer die Einladung nicht einlösen.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Festlegen der Richtlinie für die Zulassungs- oder Verweigerungsliste im Portal

Standardmäßig ist die Einstellung **Allow invitations to be sent to any domain (most inclusive)** (Senden von Einladungen an jede Domäne zulassen (am umfassendsten)) aktiviert. In diesem Fall können Sie B2B-Benutzer von jeder Organisation einladen.

### <a name="add-a-deny-list"></a>Hinzufügen einer Verweigerungsliste

Hierbei handelt es sich um das typische Szenario, bei dem Ihre Organisation mit nahezu allen Organisationen arbeiten möchte, jedoch verhindern möchte, dass Benutzer von bestimmten Domänen als B2B-Benutzer eingeladen werden.

So fügen Sie eine Verweigerungsliste hinzu

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **Azure Active Directory** > **Benutzer** > **Benutzereinstellungen** aus.
3. Wählen Sie unter **Externe Benutzer** die Option **Externe Einstellungen zur Zusammenarbeit verwalten** aus.
4. Wählen Sie unter **Collaboration restrictions** (Zusammenarbeitseinschränkungen) die Option **Deny invitations to the specified domains** (Einladungen an die angegebene Domäne verweigern) aus.
5. Geben Sie unter **ZIELDOMÄNEN** den Namen einer der Domänen ein, die Sie blockieren möchten. Geben Sie für mehrere Domänen jede Domäne in einer neuen Zeile ein. Beispiel:

   ![Abbildung der Option zum Verweigern mit hinzugefügten Domänen](./media/allow-deny-list/DenyListSettings.png)
 
6. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

Wenn Sie nach dem Festlegen der Richtlinie versuchen, einen Benutzer von einer blockierten Domäne einzuladen, wird eine Meldung angezeigt, dass die Domäne des Benutzers derzeit durch die Einladungsrichtlinie blockiert ist.
 
### <a name="add-an-allow-list"></a>Hinzufügen einer Zulassungsliste

Hierbei handelt es sich um eine restriktivere Konfiguration, in der Sie bestimmte Domänen in der Zulassungsliste festlegen und Einladungen auf andere nicht aufgeführte Organisationen oder Domänen einschränken können. 

Wenn Sie eine Zulassungsliste verwenden möchten, stellen Sie sicher, dass Sie Ihre Geschäftsanforderungen umfassend evaluieren. Wenn Sie diese Richtlinie zu restriktiv festlegen, senden die Benutzer möglicherweise Dokumente per E-Mail oder finden andere Möglichkeiten der nicht durch die IT-Abteilung genehmigten Zusammenarbeit.


So fügen Sie eine Zulassungsliste hinzu

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **Azure Active Directory** > **Benutzer** > **Benutzereinstellungen** aus.
3. Wählen Sie unter **Externe Benutzer** die Option **Externe Einstellungen zur Zusammenarbeit verwalten** aus.
4. Wählen Sie unter **Einschränkungen für die Zusammenarbeit** die Option **Einladungen nur für die angegebenen Domänen zulassen (restriktivste Einstellung)** aus.
5. Geben Sie unter **ZIELDOMÄNEN** den Namen einer der Domänen ein, die Sie zulassen möchten. Geben Sie für mehrere Domänen jede Domäne in einer neuen Zeile ein. Beispiel:

   ![Abbildung der Option zum Zulassen mit hinzugefügten Domänen](./media/allow-deny-list/AllowListSettings.png)
 
6. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

Wenn Sie nach dem Festlegen der Richtlinie versuchen, einen Benutzer einzuladen, der nicht in der Zulassungsliste ist, wird eine Meldung angezeigt, dass die Domäne des Benutzers derzeit durch die Einladungsrichtlinie blockiert ist.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Wechseln von einer Zulassungs- zu einer Verweigerungsliste und umgekehrt 

Wenn Sie zwischen Richtlinien wechseln, wird jeweils die vorhandene Richtlinienkonfiguration verworfen. Sichern Sie daher vor dem Wechseln zur anderen Liste die Details der jeweiligen Konfiguration. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Festlegen der Richtlinie für die Zulassungs- oder Verweigerungsliste mithilfe von PowerShell

### <a name="prerequisite"></a>Voraussetzungen

> [!Note]
> Das AzureADPreview-Modul ist kein vollständig unterstütztes Modul, da es sich in der Vorschauphase befindet. 

Zum Festlegen der Zulassungs- oder Verweigerungsliste mithilfe von PowerShell müssen Sie die Vorschauversion des Azure Active Directory-Moduls für Windows PowerShell installieren. Genauer gesagt müssen Sie die AzureADPreview-Modulversion 2.0.0.98 oder höher installieren.

So überprüfen Sie die Version des Moduls (und ob es installiert ist)
 
1. Öffnen Sie Windows PowerShell als Benutzer mit erhöhten Rechten („Als Administrator ausführen“). 
2. Führen Sie den folgenden Befehl aus, um festzustellen, ob auf Ihrem Computer Versionen des Azure Active Directory-Moduls für Windows PowerShell installiert sind:

   ```powershell  
   Get-Module -ListAvailable AzureAD*
   ```

Wenn das Modul nicht installiert oder nicht die erforderliche Version installiert ist, gehen Sie folgendermaßen vor:

- Wenn keine Ergebnisse zurückgegeben werden, führen Sie den folgenden Befehl aus, um die neueste Version des AzureADPreview-Moduls zu installieren:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Wenn nur das AzureAD-Modul in den Ergebnissen angezeigt wird, führen Sie die folgenden Befehle aus, um das AzureADPreview-Modul zu installieren: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Wenn nur das AzureADPreview-Modul in den Ergebnissen angezeigt wird, es sich jedoch um eine frühere Version als 2.0.0.98 handelt, führen Sie die folgenden Befehle aus, um die Version zu aktualisieren: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ```

- Wenn das AzureAD- und das AzureADPreview-Modul in den Ergebnissen angezeigt werden, es sich jedoch um eine frühere Version des AzureADPreview-Moduls als 2.0.0.98 handelt, führen Sie die folgenden Befehle aus, um die Version zu aktualisieren: 

   ```powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ```

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>Konfigurieren der Richtlinie mithilfe der AzureADPolicy-Cmdlets

Verwenden Sie zum Erstellen einer Zulassungs- oder Verweigerungsliste das Cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true). Im folgenden Beispiel wird gezeigt, wie Sie eine Verweigerungsliste festlegen, mit der die Domäne live.com blockiert wird.

```powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Nachstehend sehen Sie das gleiche Beispiel, jedoch mit einer Inline-Richtliniendefinition.

```powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Verwenden Sie zum Festlegen der Richtlinie für die Zulassungs- oder Verweigerungsliste das Cmdlet [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true). Beispiel:

```powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
```

Verwenden Sie zum Abrufen der Richtlinie das Cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true). Beispiel:

```powershell
$currentpolicy = Get-AzureADPolicy -All $true | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
```

Verwenden Sie zum Entfernen der Richtlinie das Cmdlet [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true). Beispiel:

```powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
```

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über Azure AD B2B finden Sie unter [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)
- Informationen zum bedingten Zugriff und zur B2B-Zusammenarbeit finden Sie unter [Bedingter Zugriff für Benutzer von B2B-Zusammenarbeit](conditional-access.md).
