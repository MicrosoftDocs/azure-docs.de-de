---
title: Erstellung eines Mandanten- und Hostpools in Windows Virtual Desktop (klassisch) – Azure
description: Hier erfahren Sie, wie Sie Probleme mit Mandanten und Hostpools während der Einrichtung einer Mandantenumgebung in Windows Virtual Desktop (klassisch) behandeln.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8c6d26de62364b6aca671d1e4283a01c1b78c397
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014831"
---
# <a name="tenant-and-host-pool-creation-in-windows-virtual-desktop-classic"></a>Mandanten- und Hostpoolerstellung in Windows Virtual Desktop (klassisch)

>[!IMPORTANT]
>Dieser Inhalt gilt für den Windows Virtual Desktop-Dienst (klassisch), der keine Windows Virtual Desktop-Objekte in Azure Resource Manager unterstützt. Wenn Sie Windows Virtual Desktop-Objekte in Azure Resource Manager verwalten möchten, helfen Ihnen die Informationen in [diesem Artikel](../troubleshoot-set-up-issues.md) weiter.

Dieser Artikel behandelt Probleme, die bei der ersten Einrichtung des Windows Virtual Desktop-Mandanten und der zugehörigen Infrastruktur für den Sitzungshostpool auftreten können.

## <a name="provide-feedback"></a>Feedback geben

In der [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) können Sie sich mit dem Produktteam und aktiven Communitymitgliedern über den Windows Virtual Desktop-Dienst austauschen.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Abrufen des Windows 10 Enterprise-Images für mehrere Sitzungen

Um das Windows 10 Enterprise-Image für mehrere Sitzungen zu verwenden, wechseln Sie zu Azure Marketplace, und wählen Sie **Erste Schritte** > **Microsoft Windows 10** und [Windows 10 Enterprise for Virtual Desktops, Version 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice) aus.

> [!div class="mx-imgBorder"]
> ![Screenshot: Auswählen von Windows 10 Enterprise for Virtual Desktops, Version 1809.](../media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Erstellen eines Windows Virtual Desktop-Mandanten

Dieser Abschnitt behandelt mögliche Probleme beim Erstellen des Windows Virtual Desktop-Mandanten.

### <a name="error-aadsts650052-the-app-needs-access-to-a-service"></a>Error: AADSTS650052 The app needs access to a service (Die APP benötigt Zugriff auf einen Dienst).

Beispiel für unformatierten Fehler:

```Error
AADSTS650052 Message The app needs access to a service(\"{name}\") that your organization
\"{organization}\" has not subscribed to or enabled. Contact your IT Admin to review the
configuration of your service subscriptions.650052 Message The app needs access to a service
(\"{name}\") that your organization \"{organization}\" has not subscribed to or enabled.
Contact your IT Admin to review the configuration of your service subscriptions.
```

**Ursache:** Windows Virtual Desktop in der Azure Active Directory-Instanz wurde keine Erlaubnis erteilt.

**Behebung:** [Befolgen Sie diese Anleitung](./tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop), um die Erlaubnis zu erteilen.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Error: User is not authorized to query the management service (Der Benutzer ist nicht berechtigt, den Verwaltungsdienst abzufragen)

> [!div class="mx-imgBorder"]
> ![Screenshot: PowerShell-Fenster, das anzeigt, dass ein Benutzer nicht dazu berechtigt ist, den Verwaltungsdienst abzufragen](../media/UserNotAuthorizedNewTenant.png)

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

**Behebung:** Befolgen Sie die Anweisungen unter [Zuweisen der Anwendungsrolle „TenantCreator“ für einen Benutzer in Ihrem Azure Active Directory-Mandanten](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role). Nachdem Sie die entsprechenden Schritte ausgeführt haben, ist der TenantCreator-Rolle ein Benutzer zugewiesen.

> [!div class="mx-imgBorder"]
> ![Screenshot: Zugewiesene Rolle „TenantCreator“](../media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Erstellen von Windows Virtual Desktop-Sitzungshost-VMs

Sitzungshost-VMs können auf unterschiedliche Weise erstellt werden. Das Windows Virtual Desktop-Team hilft aber nur bei VM-Bereitstellungsproblemen in Zusammenhang mit dem [Azure Marketplace](https://azuremarketplace.microsoft.com/)-Angebot. Weitere Informationen finden Sie unter [Probleme bei der Verwendung des Azure Marketplace-Angebots für das Bereitstellen eines Hostpools für Windows Virtual Desktop](#issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Probleme bei der Verwendung des Azure Marketplace-Angebots für das Bereitstellen eines Hostpools für Windows Virtual Desktop

Die Vorlage „Windows Virtual Desktop – Provision a host pool“ (Windows Virtual Desktop – Bereitstellen eines Hostpools“) ist im Azure Marketplace verfügbar.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Error: Beim Verwenden eines Links von GitHub wird die Meldung „Create a free account“ (Kostenloses Konto erstellen) angezeigt.

> [!div class="mx-imgBorder"]
> ![Screenshot: Kostenloses Konto erstellen](../media/be615904ace9832754f0669de28abd94.png)

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

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>Error: Sie erhalten die Fehlermeldung, dass die Vorlagenbereitstellung nicht gültig ist.

> [!div class="mx-imgBorder"]
> ![Screenshot mit der Fehlermeldung, dass die Vorlagenbereitstellung nicht gültig ist](../media/troubleshooting-marketplace-validation-error-generic.png)

Bevor Sie eine bestimmte Aktion ausführen, müssen Sie das Aktivitätsprotokoll überprüfen, um den detaillierten Fehler für die fehlgeschlagene Bereitstellungsüberprüfung anzuzeigen.

So zeigen Sie den Fehler im Aktivitätsprotokoll an:

1. Beenden Sie das aktuelle Azure Marketplace-Bereitstellungsangebot.
2. Suchen Sie in der oberen Suchleiste nach **Aktivitätsprotokoll** und wählen Sie es aus.
3. Suchen Sie eine Aktivität mit dem Namen **Bereitstellung überprüfen** mit dem Status **Fehler** und wählen Sie die Aktivität aus.

   > [!div class="mx-imgBorder"]
   > ![Screenshot der einzelnen **Bereitstellung überprüfen**-Aktivität mit dem Status **Fehler**](../media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. Wählen Sie "JSON" aus und scrollen Sie zum unteren Rand des Bildschirms, bis das Feld "statusMessage" angezeigt wird.

   > [!div class="mx-imgBorder"]
   > ![Screenshot der fehlgeschlagenen Aktivität mit einem roten Feld um die statusMessage-Eigenschaft des JSON-Texts](../media/troubleshooting-marketplace-validation-error-json-boxed.png)

Wenn die Vorgangsvorlage das Kontingentlimit überschreitet, können Sie eine der folgenden Aktionen ausführen, um dies zu beheben:

 - Führen Sie Azure Marketplace mit den Parametern aus, die Sie zum ersten Mal verwendet haben, aber dieses Mal mit weniger VMs und VM-Kernen.
 - Öffnen Sie den Link, der im Feld **statusMessage** angezeigt wird, in einem Browser, um eine Anforderung zum Erhöhen des Kontingents für Ihr Azure-Abonnement für die angegebene VM-SKU zu übermitteln.

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Fehler bei Azure Resource Manager-Vorlagen und PowerShell Desired State Configuration (DSC)

Führen Sie die folgenden Schritte aus, um eine Problembehandlung bei nicht erfolgreichen Bereitstellungen von Azure Resource Manager-Vorlagen und PowerShell DSC durchzuführen.

1. Suchen Sie mithilfe der Informationen im Artikel [Anzeigen von Bereitstellungsvorgängen mit Azure Resource Manager](../../azure-resource-manager/templates/deployment-history.md) nach möglichen Fehlern in der Bereitstellung.
2. Wenn keine Fehler in der Bereitstellung vorliegen, sehen Sie sich das Aktivitätsprotokoll an. Nutzen Sie dazu die Informationen unter [Anzeigen von Aktivitätsprotokollen, um Aktionen an Ressourcen zu überwachen](../../azure-resource-manager/management/view-activity-logs.md).
3. Sobald der Fehler identifiziert ist, nutzen Sie die Informationen zur Fehlermeldung und zu den Ressourcen im Artikel [Beheben gängiger Azure-Bereitstellungsfehler mit Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md), um den Fehler zu beheben.
4. Löschen Sie alle Ressourcen, die während der vorherigen Bereitstellung erstellt wurden, und versuchen Sie erneut, die Vorlage bereitzustellen.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Error: Fehler bei der Bereitstellung … \<hostname>/joindomain

> [!div class="mx-imgBorder"]
> ![Screenshot: Fehler bei der Bereitstellung](../media/e72df4d5c05d390620e07f0d7328d50f.png)

Beispiel für unformatierten Fehler:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Ursache 1:** Die zum Einbinden von VMs in die Domäne bereitgestellten Anmeldeinformationen sind falsch.

**Behebung 1:** Informationen finden Sie im Artikel [Konfiguration des virtuellen Sitzungshostcomputers](troubleshoot-vm-configuration-2019.md), Abschnitt „Fehler: Falsche Anmeldeinformationen“.

**Ursache 2:** Der Domänenname wird nicht aufgelöst.

**Behebung 2:** Unter [Error: Der Domänenname wird nicht aufgelöst](troubleshoot-vm-configuration-2019.md#error-domain-name-doesnt-resolve) in [Sitzungshost-VM-Konfiguration](troubleshoot-vm-configuration-2019.md) finden Sie weitere Informationen.

**Ursache 3:** Die DNS-Konfiguration Ihres virtuellen Netzwerks (VNET) ist auf **Standard** festgelegt.

Führen Sie folgende Schritte aus, um dieses Problem zu beheben:

1. Öffnen Sie das Azure-Portal, und wechseln Sie zur Registerkarte **Virtuelle Netzwerke**.
2. Suchen Sie Ihr VNET, und wählen Sie dann **DNS-Server** aus.
3. Das Menü für DNS-Server sollte auf der rechten Seite des Bildschirms angezeigt werden. Wählen Sie in diesem Menü **Benutzerdefiniert** aus.
4. Stellen Sie sicher, dass die unter „Benutzerdefiniert“ aufgelisteten DNS-Server mit Ihrem Domänencontroller oder Ihrer Active Directory-Domäne übereinstimmen. Wenn Ihr DNS-Server nicht angezeigt wird, können Sie ihn hinzufügen, indem Sie den entsprechenden Wert in das Feld **DNS-Server hinzufügen** eingeben.

### <a name="error-your-deployment-failedunauthorized"></a>Error: Fehler bei der Bereitstellung...\Nicht autorisiert

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Ursache:** Bei dem von Ihnen verwendeten Abonnement handelt es sich um einen Typ, der nicht auf erforderliche Features in der Region zugreifen kann, in der vom Kunden eine Bereitstellung vorgenommen wird. Dieser Fehler kann beispielsweise bei Abonnements des Typs „MSDN“, „Free“ oder „Education“ angezeigt werden.

**Behebung:** Ändern Sie den Abonnementtyp oder die Region so, dass auf die erforderlichen Features zugegriffen werden kann.

### <a name="error-vmextensionprovisioningerror"></a>Error: VMExtensionProvisioningError

> [!div class="mx-imgBorder"]
> ![Screenshot: Fehler bei der Bereitstellung – fehlerhafter Status der Terminalbereitstellung](../media/7aaf15615309c18a984673be73ac969a.png)

**Ursache 1:** Vorübergehender Fehler in der Windows Virtual Desktop-Umgebung.

**Ursache 2:** Vorübergehender Verbindungsfehler.

**Behebung:** Vergewissern Sie sich, ob die Windows Virtual Desktop-Umgebung fehlerfrei ausgeführt wird, indem Sie sich mithilfe von PowerShell anmelden. Schließen Sie die VM-Registrierung manuell ab. Informationen dazu finden Sie unter [Erstellen eines Hostpools mit PowerShell](create-host-pools-powershell-2019.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Error: Der angegebene Administratorbenutzername ist unzulässig

> [!div class="mx-imgBorder"]
> ![Screenshot: Fehler bei der Bereitstellung – angegebener Administrator ist unzulässig](../media/f2b3d3700e9517463ef88fa41875bac9.png)

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

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Error: Die VM hat beim Verarbeiten einer Erweiterung einen Fehler gemeldet

> [!div class="mx-imgBorder"]
> ![Screenshot: Fehler bei der Bereitstellung – abgeschlossener Ressourcenvorgang mit Statusmeldung für Terminalbereitstellung](../media/49c4a1836a55d91cd65125cf227f411f.png)

Beispiel für unformatierten Fehler:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-provision-host-pool-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
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

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Error: DeploymentFailed – PowerShell DSC Configuration „FirstSessionHost“ wurde mit Fehlern abgeschlossen

> [!div class="mx-imgBorder"]
> ![Screenshot: Bereitstellungsfehler – PowerShell DSC Configuration „FirstSessionHost“ wurde mit Fehlern abgeschlossen.](../media/64870370bcbe1286906f34cf0a8646ab.png)

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

### <a name="error-deploymentfailed--invalidresourcereference"></a>Error: DeploymentFailed – InvalidResourceReference

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

### <a name="error-deploymentfailed--invalidresourcereference"></a>Error: DeploymentFailed – InvalidResourceReference

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

### <a name="error-deploymentfailed--error-downloading"></a>Error: DeploymentFailed – Fehler beim Herunterladen

Beispiel für unformatierten Fehler:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Ursache:** Dieser Fehler wird dadurch verursacht, dass eine statische Route, eine Firewallregel oder eine Netzwerksicherheitsgruppe den Download der ZIP-Datei blockiert, die mit der Azure Resource Manager-Vorlage verknüpft ist.

**Behebung:** Entfernen Sie die blockierende statische Route, Firewallregel oder Netzwerksicherheitsgruppe. Öffnen Sie optional die JSON-Datei der Azure Resource Manager-Vorlage in einem Text-Editor, kopieren Sie den Link zur ZIP-Datei, und laden Sie die Ressource in einen zulässigen Speicherort herunter.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Error: User is not authorized to query the management service (Der Benutzer ist nicht berechtigt, den Verwaltungsdienst abzufragen)

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
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-ad-multi-factor-authentication-mfa"></a>Error: Benutzer erfordert Azure AD Multi-Factor Authentication (MFA)

> [!div class="mx-imgBorder"]
> ![Screenshot: Bereitstellungsfehler aufgrund fehlender Multi-Factor Authentication (MFA)](../media/MFARequiredError.png)

Beispiel für unformatierten Fehler:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Ursache:** Der angegebene Administrator für den Windows Virtual Desktop-Mandanten erfordert Azure AD Multi-Factor Authentication (MFA) zum Anmelden.

**Behebung:** Erstellen Sie einen Dienstprinzipal, und weisen Sie diesem eine Rolle für Ihren Windows Virtual Desktop-Mandanten zu. Führen Sie dazu die Schritte in folgendem Artikel aus: [Tutorial: Erstellen von Dienstprinzipalen und Rollenzuweisungen mit PowerShell](create-service-principal-role-powershell.md). Nachdem Sie überprüft haben, ob Sie sich mit dem Dienstprinzipal bei Windows Virtual Desktop anmelden können, führen Sie das Azure Marketplace-Angebot oder die GitHub-Azure Resource Manager-Vorlage erneut aus, je nachdem, welche Methode Sie verwenden. Führen Sie die folgenden Schritte aus, um die richtigen Parameter für Ihre Methode einzugeben.

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

### <a name="error-vmsubnet-not-available-when-configuring-virtual-networks"></a>Fehler: vmSubnet nicht verfügbar, wenn virtuelle Netzwerke konfiguriert werden

**Ursache:** In der WVD-Marketplace-Vorlage werden in der Benutzeroberfläche nur Subnetze angezeigt, in denen mindestens so viele IP-Adressen verfügbar sind wie die Gesamtzahl der VMs, die in der Vorlage angegeben sind. Die tatsächliche Anzahl der im Subnetz verfügbaren IP-Adressen muss nur mit der Anzahl der neuen VMs identisch sein, die bereitgestellt werden. Dies kann jedoch von der aktuellen Benutzeroberfläche nicht berechnet werden.

**Behebung:** Sie können ein Subnetz angeben, in dem mindestens so viele IP-Adressen verfügbar sind wie die Anzahl der VMs, die hinzugefügt werden. Dazu gehen Sie so vor, dass Sie nicht die Marketplace-Benutzeroberfläche verwenden, sondern den Subnetznamen im Parameter **existingSubnetName** angeben, wenn Sie [eine vorhandene Bereitstellung erneut bereitstellen](expand-existing-host-pool-2019.md#redeploy-from-azure) oder [über die zugrunde liegende ARM-Vorlage aus GitHub bereitstellen](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool).

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Problembehandlung von Windows Virtual Desktop und die Eskalationspfade finden Sie unter [Überblick über Problembehandlung, Feedback und Support](troubleshoot-set-up-overview-2019.md).
- Informationen zur Problembehandlung bei der Konfiguration eines virtuellen Computers (VM) in Windows Virtual Desktop finden Sie unter [Konfiguration des virtuellen Sitzungshostcomputers](troubleshoot-vm-configuration-2019.md).
- Informationen zur Behebung von Problemen bei Windows Virtual Desktop-Clientverbindungen finden Sie unter [Windows Virtual Desktop – Clientverbindungen](troubleshoot-service-connection-2019.md).
- Informationen zur Behebung von Problemen bei Remotedesktop-Clients finden Sie unter [Problembehandlung für den Remotedesktop-Client](../troubleshoot-client.md).
- Informationen zur Problembehandlung bei der Verwendung von PowerShell mit Windows Virtual Desktop finden Sie unter [Windows Virtual Desktop – PowerShell](troubleshoot-powershell-2019.md).
- Weitere Informationen zum Dienst finden Sie unter [Windows Virtual Desktop-Umgebung](environment-setup-2019.md).
- Ein Tutorial zur Problembehandlung finden Sie unter [Tutorial: Problembehandlung von Bereitstellungen der Resource Manager-Vorlage](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Informationen zur Überwachung von Aktionen finden Sie unter [Überwachen von Vorgängen mit Resource Manager](../../azure-resource-manager/management/view-activity-logs.md).
- Weitere Informationen zu Aktionen zum Bestimmen von Fehlern während der Bereitstellung finden Sie unter [Anzeigen von Bereitstellungsvorgängen mit dem Azure-Portal](../../azure-resource-manager/templates/deployment-history.md).