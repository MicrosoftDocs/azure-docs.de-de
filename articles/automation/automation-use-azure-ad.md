---
title: Verwenden von Azure AD in Azure Automation für die Authentifizierung bei Azure
description: In diesem Artikel wird beschrieben, wie Sie Azure AD in Azure Automation als Anbieter für die Authentifizierung bei Azure verwenden.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 336c0387ac9febcc517c2ce358d0b04c80d10678
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576802"
---
# <a name="use-azure-ad-to-authenticate-to-azure"></a>Authentifizieren bei Azure mithilfe von Azure AD

Der [Azure Active Directory (AD)](../active-directory/fundamentals/active-directory-whatis.md)-Dienst ermöglicht eine Reihe von Verwaltungsaufgaben wie Benutzerverwaltung, Domänenverwaltung und Konfiguration des einmaligen Anmeldens. In diesem Artikel wird beschrieben, wie Sie Azure AD in Azure Automation als Anbieter für die Authentifizierung bei Azure verwenden. 

## <a name="install-azure-ad-modules"></a>Installieren von Azure AD-Modulen

Sie können Azure AD mithilfe der folgenden PowerShell-Module aktivieren:

* Azure Active Directory PowerShell für Graph (AzureRM und AZ-Module). Azure Automation enthält im Lieferumfang das AzureRM-Modul und sein jüngstes Upgrade, das Az-Modul. Die Funktionalität umfasst die nicht interaktive Authentifizierung bei Azure mithilfe der Azure auf den Anmeldeinformationen des AD-Benutzers (OrgId) basierenden Authentifizierung. Siehe [Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Active Directory für Windows PowerShell (MSOnline-Modul). Dieses Modul ermöglicht Interaktionen mit Microsoft Online, einschließlich Microsoft 365.

>[!NOTE]
>PowerShell Core unterstützt das MSOnline-Modul nicht. Um die Module-Cmdlets verwenden zu können, müssen Sie sie über Windows PowerShell ausführen. Es wird empfohlen, dass Sie die neuere Azure Active Directory PowerShell für Graph-Module anstelle des MSOnline-Moduls verwenden. 

### <a name="preinstallation"></a>Vorinstallation

Vor dem Installieren der Azure AD-Module auf Ihrem Computer:

* Deinstallieren Sie alle früheren Versionen des AzureRM/Az-Moduls und des MSOnline-Moduls. 

* Deinstallieren Sie den Microsoft Online Services-Anmelde-Assistenten, um den ordnungsgemäßen Betrieb der neuen PowerShell-Module sicherzustellen.  

### <a name="install-the-azurerm-and-az-modules"></a>Installieren der AzureRM- und Az-Module

>[!NOTE]
>Um mit diesen Modulen arbeiten zu können, müssen Sie PowerShell, Version 5.1 oder höher, mit einer 64-Bit-Version von Windows verwenden. 

1. Installieren Sie Windows Management Framework 5.1 (WMF). Siehe [Installieren und Konfigurieren von WMF 5.1](/powershell/scripting/wmf/setup/install-configure).

2. Verwenden Sie zum Installieren von AzureRM und/oder Az die Anweisungen unter [Installieren von Azure PowerShell unter Windows mit PowerShellGet](/powershell/azure/azurerm/install-azurerm-ps).

### <a name="install-the-msonline-module"></a>Installieren des MSOnline-Moduls

>[!NOTE]
>Um das MSOnline-Modul zu installieren, müssen Sie Mitglied einer Administratorrolle sein. Siehe [Informationen zu Administratorrollen](/microsoft-365/admin/add-users/about-admin-roles).

1. Stellen Sie sicher, dass die Microsoft .NET Framework 3.5.x-Funktion auf Ihrem Computer aktiviert ist. Es ist wahrscheinlich, dass auf Ihrem Computer eine neuere Version installiert ist, aber die Abwärtskompatibilität mit älteren Versionen des .NET Frameworks kann aktiviert oder deaktiviert werden. 

2. Installieren Sie die 64-Bit-Version des [Microsoft Online Services-Anmelde-Assistenten](https://www.microsoft.com/Download/details.aspx?id=28177).

3. Führen Sie Windows PowerShell als Administrator aus, um eine Windows PowerShell-Eingabeaufforderung mit erhöhten Rechten zu erstellen.

4. Stellen Sie Azure Active Directory aus [MSOnline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0) bereit.

5. Wenn Sie zum Installieren des NuGet-Anbieters aufgefordert werden, wählen Sie „Y“ aus, und drücken Sie die EINGABETASTE.

6. Wenn Sie zum Installieren des Moduls aus der [PSgallery](https://www.powershellgallery.com/) aufgefordert werden, geben Sie „Y“ ein, und drücken Sie die EINGABETASTE.

### <a name="install-support-for-pscredential"></a>Installieren der Unterstützung für „PSCredential“"

Azure Automation verwendet die [PSCredential](/dotnet/api/system.management.automation.pscredential)-Klasse, um ein Anmeldeinformationsobjekt darzustellen. Ihr Skripts rufen `PSCredential`-Objekte mithilfe des `Get-AutomationPSCredential`-Cmdlets ab. Weitere Informationen finden Sie unter [Anmeldeinformationsobjekte in Azure Automation](shared-resources/credentials.md).

## <a name="assign-a-subscription-administrator"></a>Zuweisen eines Abonnementadministrators

Sie müssen einen Administrator für das Azure-Abonnement zuweisen. Diese Person verfügt über die Rolle „Besitzer“" für den Abonnementbereich. Siehe [Rollenbasierte Zugriffssteuerung in Azure Automation](automation-role-based-access-control.md). 

## <a name="change-the-azure-ad-users-password"></a>Ändern des Kennworts des Azure AD-Benutzers

So ändern Sie das Kennwort des Azure AD-Benutzers

1. Melden Sie sich bei Azure ab.

2. Lassen Sie den Administrator sich bei Azure als der gerade erstellte Azure AD-Benutzer anmelden, wobei der vollständige Benutzername (einschließlich Domäne) und ein temporäres Kennwort verwendet werden. 

3. Bitten Sie den Administrator, das Kennwort bei Aufforderung dazu zu ändern.

## <a name="configure-azure-automation-to-manage-the-azure-subscription"></a>Konfigurieren von Azure Automation für die Verwaltung des Azure-Abonnements

Damit Azure Automation mit Azure AD kommunizieren kann, müssen Sie die Anmeldeinformationen abrufen, die der Azure-Verbindung mit Azure AD zugeordnet sind. Beispiele für diese Anmeldeinformationen sind Mandanten-ID, Abonnement-ID und Ähnliches. Weitere Informationen zu der Verbindung zwischen Azure und Azure AD finden Sie unter [Verbinden Ihrer Organisation mit Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="create-a-credential-asset"></a>Erstellen eines Anmeldeinformationsobjekts

Da die Azure-Anmeldeinformationen für Azure AD verfügbar sind, ist es jetzt an der Zeit, ein Azure Automation-Anmeldeinformationsobjekt zu erstellen, um die Azure AD-Anmeldeinformationen sicher zu speichern, damit Runbooks und DSC-Skripts (Desire State Configuration) darauf zugreifen können. Dies können Sie mithilfe des Azure-Portals oder mit PowerShell-Cmdlets durchführen.

### <a name="create-the-credential-asset-in-azure-portal"></a>Erstellen des Anmeldeinformationsobjekts im Azure-Portal

Sie können das Azure-Portal zum Erstellen des Anmeldeinformationsobjekts verwenden. Führen Sie diesen Vorgang in Ihrem Automation-Konto unter **Freigegebene Ressourcen** mithilfe der Option **Anmeldeinformationen** aus. Siehe [Anmeldeinformationsobjekte in Azure Automation](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>Erstellen des Anmeldeinformationsobjekts mithilfe von Windows PowerShell

Zum Vorbereiten eines neuen Anmeldeinformationsobjekts in Windows PowerShell erstellt Ihr Skript zunächst ein `PSCredential`-Objekt mithilfe des zugewiesenen Benutzernamens und Kennworts. Das Skript verwendet dann dieses Objekt, um das Objekt durch einen Aufruf des Cmdlets [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) zu erstellen. Alternativ kann das Skript das Cmdlet [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) aufrufen, um den Benutzer zur Eingabe eines Namens und Kennworts aufzufordern. Siehe [Anmeldeinformationsobjekte in Azure Automation](shared-resources/credentials.md). 

## <a name="manage-azure-resources-from-an-azure-automation-runbook"></a>Verwalten von Azure-Ressourcen über ein Azure Automation-Runbook

Sie können Azure-Ressourcen unter Verwendung des Anmeldeinformationsobjekts aus Azure Automation-Runbooks heraus verwalten. Im Folgenden finden Sie ein PowerShell-Beispielrunbook, das Anmeldeinformationsobjekt erfasst, um es zum Beenden und Starten virtueller Computer in einem Azure-Abonnement zu verwenden. Dieses Runbook verwendet zunächst `Get-AutomationPSCredential`, um die Anmeldeinformationen abzurufen, die für die Authentifizierung bei Azure verwendet werden sollen. Danach ruft es das Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) auf, um eine Verbindung mit Azure mit diesen Anmeldeinformationen herzustellen. Das Skript verwendet das Cmdlet [Select-AzureSubscription](/powershell/module/servicemanagement/azure.service/select-azuresubscription), um das Abonnement auszuwählen, mit dem Sie arbeiten möchten. 

```azurepowershell
Workflow Stop-Start-AzureVM 
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    $credential = Get-AutomationPSCredential -Name 'AzureCredential' 
    Connect-AzAccount -Credential $credential 
    Select-AzureSubscription -SubscriptionId $AzureSubscriptionId 
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Stop-AzVM -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Start-AzVM 
        } 
    } 
}
```  

## <a name="next-steps"></a>Nächste Schritte

* Ausführliche Informationen zur Verwendung von Anmeldeinformationen finden Sie unter [Verwalten von Anmeldeinformationen in Azure Automation](shared-resources/credentials.md).
* Weitere Informationen zu Modulen finden Sie unter [Verwalten von Modulen in Azure Automation](shared-resources/modules.md).
* Wenn Sie Informationen zum Starten eines Runbooks benötigen, lesen Sie den Artikel [Starten eines Runbooks in Azure Automation](start-runbooks.md).
* Details zu PowerShell finden Sie in der [PowerShell-Dokumentation](/powershell/scripting/overview).
