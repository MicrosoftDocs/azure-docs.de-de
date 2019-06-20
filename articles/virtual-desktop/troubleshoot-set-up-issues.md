---
title: Erstellung von Windows Virtual Desktop-Mandaten und -Hostpools – Azure
description: 'Vorgehensweise: Behandeln von Problemen mit Mandanten und Hostpools während der Einrichtung einer Windows Virtual Desktop-Mandantenumgebung.'
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 88e843c410a750387ecf58497dec79586e2a59d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523326"
---
# <a name="tenant-and-host-pool-creation"></a>Mandanten- und Hostpoolerstellung

Dieser Artikel behandelt Probleme, die bei der ersten Einrichtung des Windows Virtual Desktop-Mandanten und der zugehörigen Infrastruktur für den Sitzungshostpool auftreten können.

## <a name="provide-feedback"></a>Feedback geben

Solange sich Windows Virtual Desktop in der Vorschauphase befindet, werden noch keine Supportanfragen angenommen. In der [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) können Sie sich mit dem Produktteam und aktiven Communitymitgliedern über den Windows Virtual Desktop-Dienst austauschen.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Abrufen des Windows 10 Enterprise-Images für mehrere Sitzungen

Um das Windows 10 Enterprise-Image für mehrere Sitzungen zu verwenden, wechseln Sie zum Azure Marketplace, wählen Sie **Erste Schritte** > **Microsoft Windows 10** > [Windows 10 Enterprise for Virtual Desktops Preview, Version 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

![Screenshot: Auswahl von Windows 10 Enterprise for Virtual Desktops Preview, Version 1809](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Erstellen eines Windows Virtual Desktop-Mandanten

Dieser Abschnitt behandelt mögliche Probleme beim Erstellen des Windows Virtual Desktop-Mandanten.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Fehler User is not authorized to query the management service (Der Benutzer ist nicht berechtigt, den Verwaltungsdienst abzufragen)

![Screenshot: PowerShell-Fenster, das anzeigt, dass ein Benutzer nicht dazu berechtigt ist, den Verwaltungsdienst abzufragen](media/UserNotAuthorizedNewTenant.png)

Beispiel für unformatierten Fehler:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**Ursache:** Dem angemeldeten Benutzer wurde in seinem Azure Active Directory-Mandanten nicht die TenantCreator-Rolle zugewiesen.

**Behebung:** Befolgen Sie die Anweisungen unter [Zuweisen der Anwendungsrolle „TenantCreator“ für einen Benutzer in Ihrem Azure Active Directory-Mandanten](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory#assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant). Nachdem Sie die entsprechenden Schritte ausgeführt haben, ist der TenantCreator-Rolle ein Benutzer zugewiesen.

![Screenshot: Zugewiesene Rolle „TenantCreator“](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Erstellen von Windows Virtual Desktop-Sitzungshost-VMs

Sitzungshost-VMs können auf unterschiedliche Weise erstellt werden, aber die Teams für Remote Desktop Services/Windows Virtual Desktop können nur bei Problemen mit der VM-Bereitstellung in Zusammenhang mit der Azure Resource Manager-Vorlage helfen. Die Azure Resource Manager-Vorlage ist im [Azure Marketplace](https://azuremarketplace.microsoft.com/) und in [GitHub](https://github.com/) verfügbar.

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Probleme bei der Verwendung des Azure Marketplace-Angebots für das Bereitstellen eines Hostpools für Windows Virtual Desktop

Die Vorlage „Windows Virtual Desktop – Provision a host pool“ (Windows Virtual Desktop – Bereitstellen eines Hostpools“) ist im Azure Marketplace verfügbar.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Fehler Beim Verwenden eines Links von GitHub wird die Meldung „Create a free account“ (Kostenloses Konto erstellen) angezeigt

![Screenshot: Kostenloses Konto erstellen](media/be615904ace9832754f0669de28abd94.png)

**Ursache 1:** Im verwendeten Konto sind nicht genügend aktive Abonnements verfügbar, um sich bei Azure anzumelden, oder das verwendete Konto besitzt keine Berechtigungen zum Anzeigen der Abonnements.

**Behebung 1:** Melden Sie sich mit einem Konto an, das mindestens über Zugriff als Mitwirkender auf das Abonnement verfügt, in dem die Sitzungshost-VMs bereitgestellt werden sollen.

**Ursache 2:** Das verwendete Abonnement gehört zu einem Microsoft-Clouddienstanbieter-Mandanten (Cloud Service Provider, CSP).

**Behebung 2:** Wechseln Sie zum GitHub-Verzeichnis für **Create and provision new Windows Virtual Desktop host pool** (Erstellen und Bereitstellen eines neuen Windows Virtual Desktop-Hostpools), und führen Sie die folgenden Schritte aus:

1. Klicken Sie mit der rechten Maustaste auf **Deploy to Azure** (In Azure bereitstellen), und wählen Sie **Copy link address** (Linkadresse kopieren) aus.
2. Öffnen Sie den **Editor**, und fügen Sie den Link ein.
3. Fügen Sie vor dem Zeichen „#“ den Mandantennamen des CSP-Endkunden ein.
4. Öffnen Sie den neuen Link in einem Browser – das Azure-Portal lädt die Vorlage.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Fehler bei Azure Resource Manager-Vorlagen und PowerShell Desired State Configuration (DSC)

Führen Sie die folgenden Schritte aus, um eine Problembehandlung bei nicht erfolgreichen Bereitstellungen von Azure Resource Manager-Vorlagen und PowerShell DSC durchzuführen.

1. Suchen Sie mithilfe der Informationen im Artikel [Anzeigen von Bereitstellungsvorgängen mit Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations) nach möglichen Fehlern in der Bereitstellung.
2. Wenn keine Fehler in der Bereitstellung vorliegen, sehen Sie sich das Aktivitätsprotokoll an. Nutzen Sie dazu die Informationen unter [Anzeigen von Aktivitätsprotokollen, um Aktionen an Ressourcen zu überwachen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
3. Sobald der Fehler identifiziert ist, nutzen Sie die Informationen zur Fehlermeldung und zu den Ressourcen im Artikel [Beheben gängiger Azure-Bereitstellungsfehler mit Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors), um den Fehler zu beheben.
4. Löschen Sie alle Ressourcen, die während der vorherigen Bereitstellung erstellt wurden, und versuchen Sie erneut, die Vorlage bereitzustellen.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Fehler Fehler bei der Bereitstellung … <hostname>/joindomain

![Screenshot: Fehler bei der Bereitstellung](media/e72df4d5c05d390620e07f0d7328d50f.png)

Beispiel für unformatierten Fehler:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. 
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Ursache 1:** Die zum Einbinden von VMs in die Domäne bereitgestellten Anmeldeinformationen sind falsch.

**Behebung 1:** Informationen finden Sie im Artikel [Konfiguration des virtuellen Sitzungshostcomputers](troubleshoot-vm-configuration.md), Abschnitt „Fehler: Falsche Anmeldeinformationen“.

**Ursache 2:** Der Domänenname wird nicht aufgelöst.

**Behebung 2:** Informationen finden Sie im Artikel [Konfiguration des virtuellen Sitzungshostcomputers](troubleshoot-vm-configuration.md), Abschnitt „Fehler: Der Domänenname wird nicht aufgelöst“.

### <a name="error-vmextensionprovisioningerror"></a>Fehler VMExtensionProvisioningError

![Screenshot: Fehler bei der Bereitstellung – fehlerhafter Status der Terminalbereitstellung](media/7aaf15615309c18a984673be73ac969a.png)

**Ursache 1:** Vorübergehender Fehler in der Windows Virtual Desktop-Umgebung.

**Ursache 2:** Vorübergehender Verbindungsfehler.

**Behebung:** Vergewissern Sie sich, ob die Windows Virtual Desktop-Umgebung fehlerfrei ausgeführt wird, indem Sie sich mithilfe von PowerShell anmelden. Schließen Sie die VM-Registrierung manuell ab. Informationen dazu finden Sie unter [Erstellen eines Hostpools mit PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Fehler Der angegebene Administratorbenutzername ist unzulässig

![Screenshot: Fehler bei der Bereitstellung – angegebener Administrator ist unzulässig](media/f2b3d3700e9517463ef88fa41875bac9.png)

Beispiel für unformatierten Fehler:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Ursache:** Das angegebene Kennwort enthält unzulässige Teilzeichenfolgen (Admin, Administrator, root).

**Behebung:** Aktualisieren Sie den Benutzernamen, oder verwenden Sie einen anderen Benutzer.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Fehler Die VM hat beim Verarbeiten einer Erweiterung einen Fehler gemeldet

![Screenshot: Fehler bei der Bereitstellung – abgeschlossener Ressourcenvorgang mit Statusmeldung für Terminalbereitstellung](media/49c4a1836a55d91cd65125cf227f411f.png)

Beispiel für unformatierten Fehler:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-hostpool4-preview-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'. 
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few: 
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message: 
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource": 
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft. 
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**Ursache:** Die PowerShell DSC-Erweiterung konnte keine Administratorzugriff auf die VM erhalten.

**Behebung:** Überprüfen Sie, ob der verwendete Benutzer mit dem Benutzernamen und Kennwort über Administratorzugriff auf die VM verfügt, und führen Sie die Azure Resource Manager-Vorlage erneut aus.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Fehler DeploymentFailed – PowerShell DSC Configuration „FirstSessionHost“ wurde mit Fehlern abgeschlossen

![Screenshot: Bereitstellungsfehler – PowerShell DSC Configuration „FirstSessionHost“ wurde mit Fehlern abgeschlossen](media/64870370bcbe1286906f34cf0a8646ab.png)

Beispiel für unformatierten Fehler:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list 
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",  
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**Ursache:** Die PowerShell DSC-Erweiterung konnte keine Administratorzugriff auf die VM erhalten.

**Behebung:** Überprüfen Sie, ob der verwendete Benutzer mit dem Benutzernamen und Kennwort über Administratorzugriff auf die VM verfügt, und führen Sie die Azure Resource Manager-Vorlage erneut aus.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Fehler DeploymentFailed – InvalidResourceReference

Beispiel für unformatierten Fehler:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Ursache:** Ein Teil der Ressourcengruppe wird für bestimmte Ressourcen verwendet, die von der Vorlage erstellt werden. Wenn der Name vorhandenen Ressourcen entspricht, wählt die Vorlage möglicherweise eine vorhandene Ressource aus einer anderen Gruppe aus.

**Behebung:** Wenn Sie die Azure Resource Manager-Vorlage ausführen, um Sitzungshost-VMs bereitzustellen, legen Sie am Anfang des Namens Ihrer Abonnementressourcengruppe zwei eindeutige Zeichen fest.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Fehler DeploymentFailed – InvalidResourceReference

Beispiel für unformatierten Fehler:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**Ursache:** Dieser Fehler entsteht, weil die mit der Azure Resource Manager-Vorlage erstellte Netzwerkkarte den gleichen Namen aufweist wie eine andere Netzwerkkarte, die sich bereits im VNET befindet.

**Behebung:** Verwenden Sie ein anderes Hostpräfix.

### <a name="error-deploymentfailed--error-downloading"></a>Fehler DeploymentFailed – Fehler beim Herunterladen

Beispiel für unformatierten Fehler:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-hostpool-3-preview-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-private-preview-
1/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Ursache:** Dieser Fehler wird dadurch verursacht, dass eine statische Route, eine Firewallregel oder eine Netzwerksicherheitsgruppe den Download der ZIP-Datei blockiert, die mit der Azure Resource Manager-Vorlage verknüpft ist.

**Behebung:** Entfernen Sie die blockierende statische Route, Firewallregel oder Netzwerksicherheitsgruppe. Öffnen Sie optional die JSON-Datei der Azure Resource Manager-Vorlage in einem Text-Editor, kopieren Sie den Link zur ZIP-Datei, und laden Sie die Ressource in einen zulässigen Speicherort herunter.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Fehler User is not authorized to query the management service (Der Benutzer ist nicht berechtigt, den Verwaltungsdienst abzufragen)

Beispiel für unformatierten Fehler:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00", 
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing 
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s). 
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n 
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**Ursache:** Der angegebene Administrator für den Windows Virtual Desktop-Mandanten besitzt keine gültige Rollenzuweisung.

**Behebung:** Der Benutzer, der den Windows Virtual Desktop-Mandanten erstellt hat, muss sich bei Windows Virtual Desktop PowerShell anmelden und dem gewünschten Benutzer eine Rollenzuweisung zuweisen. Wenn Sie die GitHub-Azure Resource Manager-Vorlagenparameter verwenden, führen Sie folgende PowerShell-Befehle aus:

```PowerShell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName “RDS Contributor” -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Fehler Benutzer erfordert Azure Multi-Factor Authentication (MFA)

![Screenshot: Bereitstellungsfehler aufgrund fehlender Multi-Factor Authentication (MFA)](media/MFARequiredError.png)

Beispiel für unformatierten Fehler:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Ursache:** Der angegebene Administrator für den Windows Virtual Desktop-Mandanten erfordert Azure Multi-Factor Authentication (MFA) zum Anmelden.

**Behebung:** Erstellen Sie einen Dienstprinzipal, und weisen Sie diesem eine Rolle für Ihren Windows Virtual Desktop-Mandanten zu. Führen Sie dazu die Schritte in folgendem Artikel aus: [Tutorial: Erstellen von Dienstprinzipalen und Rollenzuweisungen mit PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell). Nachdem Sie überprüft haben, ob Sie sich mit dem Dienstprinzipal bei Windows Virtual Desktop anmelden können, führen Sie das Azure Marketplace-Angebot oder die GitHub-Azure Resource Manager-Vorlage erneut aus, je nachdem, welche Methode Sie verwenden. Führen Sie die folgenden Schritte aus, um die richtigen Parameter für Ihre Methode einzugeben.

Wenn Sie das Azure Marketplace-Angebot ausführen, geben Sie Werte für die folgenden Parameter an, um sich ordnungsgemäß bei Windows Virtual Desktop zu authentifizieren:

- RDS-Besitzer des Windows Virtual Desktop-Mandanten: Dienstprinzipal
- Anwendungs-ID: Der Anwendungsbezeichner des neuen, von Ihnen erstellten Dienstprinzipals
- Kennwort/Kennwort bestätigen: Das von Ihnen für den Dienstprinzipal generierte geheime Kennwort
- Azure AD-Mandanten-ID: Die Azure AD-Mandanten-ID des von Ihnen erstellten Dienstprinzipals

Wenn Sie die GitHub-Azure Resource Manager-Vorlage ausführen, geben Sie Werte für die folgenden Parameter an, um sich ordnungsgemäß bei Windows Virtual Desktop zu authentifizieren:

- Benutzerprinzipalname für den Mandantenadministrator oder Anwendungs-ID: Der Anwendungsbezeichner des neuen, von Ihnen erstellten Dienstprinzipals
- Kennwort des Mandantenadministrators: Das von Ihnen für den Dienstprinzipal generierte geheime Kennwort
- IsServicePrincipal: **true**
- AadTenantId: Die Azure AD-Mandanten-ID des von Ihnen erstellten Dienstprinzipals

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Problembehandlung von Windows Virtual Desktop und die Eskalationspfade finden Sie unter [Problembehandlung: Übersicht, Feedback und Support](troubleshoot-set-up-overview.md).
- Informationen zur Problembehandlung bei der Konfiguration eines virtuellen Computers (VM) in Windows Virtual Desktop finden Sie unter [Konfiguration des virtuellen Sitzungshostcomputers](troubleshoot-vm-configuration.md).
- Informationen zur Problembehandlung bei Problemen mit Windows Virtual Desktop-Clientverbindungen finden Sie unter [Remotedesktop-Clientverbindungen](troubleshoot-client-connection.md).
- Informationen zur Problembehandlung bei der Verwendung von PowerShell mit Windows Virtual Desktop finden Sie unter [Windows Virtual Desktop: PowerShell](troubleshoot-powershell.md).
- Weitere Informationen zum Vorschaudienst finden Sie unter [Umgebung der Windows Desktop-Vorschau](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Ein Tutorial zur Problembehandlung finden Sie unter [Tutorial: Problembehandlung von Bereitstellungen der Resource Manager-Vorlage](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Informationen zur Überwachung von Aktionen finden Sie unter [Überwachen von Vorgängen mit Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Weitere Informationen zu Aktionen zum Bestimmen von Fehlern während der Bereitstellung finden Sie unter [Anzeigen von Bereitstellungsvorgängen mit dem Azure-Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).