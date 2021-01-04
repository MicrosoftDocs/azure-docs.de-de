---
title: Erstellen von Hostpools für Windows Virtual Desktop-Umgebungen – Azure
description: Behandeln von Problemen mit Mandanten und Hostpools während der Einrichtung einer Windows Virtual Desktop-Umgebung
author: Heidilohr
ms.topic: troubleshooting
ms.date: 09/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 126a8e48a8db1c41299a7cb7a34f172342110667
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023121"
---
# <a name="host-pool-creation"></a>Hostpoolerstellung

>[!IMPORTANT]
>Dieser Inhalt gilt für Windows Virtual Desktop mit Windows Virtual Desktop-Objekten für Azure Resource Manager. Wenn Sie Windows Virtual Desktop (klassisch) ohne Azure Resource Manager-Objekte verwenden, finden Sie weitere Informationen in [diesem Artikel](./virtual-desktop-fall-2019/troubleshoot-set-up-issues-2019.md).

Dieser Artikel behandelt Probleme, die bei der ersten Einrichtung des Windows Virtual Desktop-Mandanten und der zugehörigen Infrastruktur für den Sitzungshostpool auftreten können.

## <a name="provide-feedback"></a>Feedback geben

In der [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) können Sie sich mit dem Produktteam und aktiven Communitymitgliedern über den Windows Virtual Desktop-Dienst austauschen.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Abrufen des Windows 10 Enterprise-Images für mehrere Sitzungen

Wenn Sie das Windows 10 Enterprise-Image für mehrere Sitzungen verwenden möchten, wechseln Sie zum Azure Marketplace, und klicken Sie auf **Get Started** (Erste Schritte)  > **Microsoft Windows 10** > [Windows 10 Enterprise multi-session, Version 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice) (Windows 10 Enterprise mit mehreren Sitzungen, Version 1809).

## <a name="issues-with-using-the-azure-portal-to-create-host-pools"></a>Probleme bei der Verwendung des Azure-Portals zum Erstellen von Hostpools

### <a name="error-create-a-free-account-appears-when-accessing-the-service"></a>Error: „Kostenloses Konto erstellen“ wird beim Zugreifen auf den Dienst angezeigt

> [!div class="mx-imgBorder"]
> ![Bild: die Meldung „Kostenloses Konto erstellen“ im Azure-Portal](media/create-new-account.png)

**Ursache:** Für das Konto, mit dem Sie sich bei Azure angemeldet haben, sind nicht genügend aktive Abonnements verfügbar, oder das verwendete Konto besitzt keine Berechtigungen zum Anzeigen der Abonnements.

**Problemlösung:** Melden Sie sich bei dem Abonnement an, über das Sie die VMs für Sitzungshosts mit einem Konto bereitstellen, das mindestens über die Zugriffsberechtigungen eines Mitwirkenden verfügt.

### <a name="error-exceeding-quota-limit"></a>Error: „Exceeding quota limit“ (Kontingentgrenze überschritten)

Wenn bei Ihrem Vorgang die Kontingentgrenze überschritten wird, können Sie eine der folgenden Aktionen ausführen:

- Erstellen Sie einen neuen Hostpool mit denselben Parametern, aber mit weniger VMs und VM-Kernen.

- Öffnen Sie den Link, der im Feld „statusMessage“ angezeigt wird, in einem Browser, um eine Anforderung zum Erhöhen des Kontingents für Ihr Azure-Abonnement für die angegebene VM-SKU zu übermitteln.

### <a name="error-cant-see-user-assignments-in-app-groups"></a>Error: Can't see user assignments in app groups. (Benutzerzuweisungen in App-Gruppen nicht sichtbar.)

Ursache: Dieser Fehler tritt meist auf, nachdem Sie das Abonnement von einem Azure Active Directory-Mandanten zu einem anderen verschoben haben. Wenn Ihre alten Zuweisungen weiterhin an den alten Azure AD-Mandanten gebunden sind, kann das Azure-Portal diese nicht nachverfolgen.

Behebung: Sie müssen die Benutzer den App-Gruppen neu zuweisen.

## <a name="azure-resource-manager-template-errors"></a>Fehler mit der Azure Resource Manager-Vorlage

Führen Sie die folgenden Schritte aus, um eine Problembehandlung bei nicht erfolgreichen Bereitstellungen von Azure Resource Manager-Vorlagen und PowerShell DSC durchzuführen.

1. Suchen Sie mithilfe der Informationen im Artikel [Anzeigen von Bereitstellungsvorgängen mit Azure Resource Manager](../azure-resource-manager/templates/deployment-history.md) nach möglichen Fehlern in der Bereitstellung.
2. Wenn keine Fehler in der Bereitstellung vorliegen, sehen Sie sich das Aktivitätsprotokoll an. Nutzen Sie dazu die Informationen unter [Anzeigen von Aktivitätsprotokollen, um Aktionen an Ressourcen zu überwachen](../azure-resource-manager/management/view-activity-logs.md).
3. Sobald der Fehler identifiziert ist, nutzen Sie die Informationen zur Fehlermeldung und zu den Ressourcen im Artikel [Beheben gängiger Azure-Bereitstellungsfehler mit Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md), um den Fehler zu beheben.
4. Löschen Sie alle Ressourcen, die während der vorherigen Bereitstellung erstellt wurden, und versuchen Sie erneut, die Vorlage bereitzustellen.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Error: Fehler bei der Bereitstellung … \<hostname>/joindomain

> [!div class="mx-imgBorder"]
> ![Screenshot: Fehler bei der Bereitstellung](media/failure-joindomain.png)

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

**Behebung 2:** Unter [Error: Der Domänenname wird nicht aufgelöst](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve) in [Sitzungshost-VM-Konfiguration](troubleshoot-vm-configuration.md) finden Sie weitere Informationen.

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
> ![Screenshot: Fehler bei der Bereitstellung – fehlerhafter Status der Terminalbereitstellung](media/failure-vmextensionprovisioning.png)

**Ursache 1:** Vorübergehender Fehler in der Windows Virtual Desktop-Umgebung.

**Ursache 2:** Vorübergehender Verbindungsfehler.

**Behebung:** Vergewissern Sie sich, ob die Windows Virtual Desktop-Umgebung fehlerfrei ausgeführt wird, indem Sie sich mithilfe von PowerShell anmelden. Schließen Sie die VM-Registrierung manuell ab. Informationen dazu finden Sie unter [Erstellen eines Hostpools mit PowerShell](create-host-pools-powershell.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Error: Der angegebene Administratorbenutzername ist unzulässig

> [!div class="mx-imgBorder"]
> ![Screenshot: Fehler bei der Bereitstellung – angegebener Administrator ist unzulässig](media/failure-username.png)

Beispiel für unformatierten Fehler:

```Error
 { …{ "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } … }
```

**Ursache:** Das angegebene Kennwort enthält unzulässige Teilzeichenfolgen (Admin, Administrator, root).

**Behebung:** Aktualisieren Sie den Benutzernamen, oder verwenden Sie einen anderen Benutzer.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Error: Die VM hat beim Verarbeiten einer Erweiterung einen Fehler gemeldet

> [!div class="mx-imgBorder"]
> ![Screenshot: Fehler bei der Bereitstellung – abgeschlossener Ressourcenvorgang mit Statusmeldung für Terminalbereitstellung](media/failure-processing.png)

Beispiel für unformatierten Fehler:

```Error
{ … "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] … }
```

**Ursache:** Die PowerShell DSC-Erweiterung konnte keine Administratorzugriff auf die VM erhalten.

**Behebung:** Überprüfen Sie, ob der verwendete Benutzer mit dem Benutzernamen und Kennwort über Administratorzugriff auf die VM verfügt, und führen Sie die Azure Resource Manager-Vorlage erneut aus.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Error: DeploymentFailed – PowerShell DSC Configuration „FirstSessionHost“ wurde mit Fehlern abgeschlossen

> [!div class="mx-imgBorder"]
> ![Screenshot: Bereitstellungsfehler – PowerShell DSC Configuration „FirstSessionHost“ wurde mit Fehlern abgeschlossen.](media/failure-dsc.png)

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

### <a name="error-cant-delete-a-session-host-from-the-host-pool-after-deleting-the-vm"></a>Error: Ein Sitzungshost kann nach dem Löschen der VM nicht aus dem Hostpool gelöscht werden

**Ursache:** Sie müssen den Sitzungshost löschen, bevor Sie die VM löschen.

**Behebung:** Setzen Sie den Sitzungshost in den Ausgleichsmodus, melden Sie alle Benutzer vom Sitzungshost ab, und löschen Sie dann den Host.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Problembehandlung von Windows Virtual Desktop und die Eskalationspfade finden Sie unter [Überblick über Problembehandlung, Feedback und Support](troubleshoot-set-up-overview.md).
- Informationen zur Problembehandlung bei der Konfiguration eines virtuellen Computers (VM) in Windows Virtual Desktop finden Sie unter [Konfiguration des virtuellen Sitzungshostcomputers](troubleshoot-vm-configuration.md).
- Informationen zur Behebung von Problemen bei Windows Virtual Desktop-Clientverbindungen finden Sie unter [Windows Virtual Desktop – Clientverbindungen](troubleshoot-service-connection.md).
- Informationen zur Behebung von Problemen bei Remotedesktop-Clients finden Sie unter [Problembehandlung für den Remotedesktop-Client](troubleshoot-client.md).
- Informationen zur Problembehandlung bei der Verwendung von PowerShell mit Windows Virtual Desktop finden Sie unter [Windows Virtual Desktop – PowerShell](troubleshoot-powershell.md).
- Weitere Informationen zum Dienst finden Sie unter [Windows Virtual Desktop-Umgebung](environment-setup.md).
- Ein Tutorial zur Problembehandlung finden Sie unter [Tutorial: Problembehandlung von Bereitstellungen der Resource Manager-Vorlage](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Informationen zur Überwachung von Aktionen finden Sie unter [Überwachen von Vorgängen mit Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Weitere Informationen zu Aktionen zum Bestimmen von Fehlern während der Bereitstellung finden Sie unter [Anzeigen von Bereitstellungsvorgängen mit dem Azure-Portal](../azure-resource-manager/templates/deployment-history.md).