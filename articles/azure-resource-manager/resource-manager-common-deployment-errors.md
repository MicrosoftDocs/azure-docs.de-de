---
title: Problembehandlung bei häufigen Azure-Bereitstellungsfehlern | Microsoft Docs
description: Informationen zum Beheben gängiger Fehler beim Bereitstellen von Ressourcen in Azure mit Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: Bereitstellungsfehler, Azure-Bereitstellung, in Azure bereitstellen.
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2019
ms.author: tomfitz
ms.openlocfilehash: f6ebeb1d9953311ad1cb85d8ab33c83d5e92d687
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66128558"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Beheben gängiger Azure-Bereitstellungsfehler mit Azure Resource Manager

In diesem Artikel werden einige häufige Azure-Bereitstellungsfehler beschrieben. Er enthält darüber hinaus Informationen zur Behebung der Fehler. Sie können unter [Ermitteln des Fehlercodes](#find-error-code) nachsehen, falls Sie den Fehlercode für Ihren Bereitstellungsfehler nicht kennen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="error-codes"></a>Fehlercodes

| Fehlercode | Lösung | Weitere Informationen |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Beachten Sie die Einschränkungen bei der Namensgebung für Speicherkonten. | [Resolve errors for storage account names](resource-manager-storage-account-name-errors.md) (Beheben von Fehlern für Namen von Speicherkonten) |
| AccountPropertyCannotBeSet | Überprüfen Sie die verfügbaren Speicherkontoeigenschaften. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | Im Cluster oder in der Region sind keine Ressourcen verfügbar, oder die angeforderte Größe des virtuellen Computers kann nicht unterstützt werden. Wiederholen Sie die Anforderung zu einem späteren Zeitpunkt, oder fordern Sie eine andere Größe für den virtuellen Computer an. | [Bereitstellungs- und Zuteilungsprobleme bei Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md) und [Bereitstellungs- und Zuteilungsprobleme bei Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) und [Problembehandlung bei Zuordnungsfehlern](../virtual-machines/troubleshooting/allocation-failure.md)|
| AnotherOperationInProgress | Warten Sie, bis der gleichzeitige Vorgang abgeschlossen ist. | |
| AuthorizationFailed | Ihr Konto oder Dienstprinzipal verfügt nicht über ausreichende Zugriffsberechtigungen zum Durchführen der Bereitstellung. Überprüfen Sie die Rolle, zu der Ihr Konto gehört, sowie deren Zugriffsberechtigungen für den Bereitstellungsumfang.<br><br>Unter Umständen wird dieser Fehler angezeigt, wenn ein erforderlicher Ressourcenanbieter nicht registriert ist. | [Rollenbasierte Access Control in Azure](../role-based-access-control/role-assignments-portal.md)<br><br>[Lösen von Registrierungsfehlern](resource-manager-register-provider-errors.md) |
| BadRequest | Sie haben Bereitstellungswerte gesendet, die nicht den von Resource Manager erwarteten Werten entsprechen. Überprüfen Sie die interne Statusmeldung, um Hilfe zur Problembehandlung zu erhalten. | [Vorlagenreferenz](/azure/templates/) und [Unterstützte Speicherorte](resource-group-authoring-templates.md#resource-location) |
| Konflikt: | Sie fordern einen Vorgang an, der im aktuellen Zustand der Ressource nicht zulässig ist. Eine Größenänderung für den Datenträger ist beispielsweise nur zulässig, wenn ein virtueller Computer erstellt wird oder die Zuweisung des virtuellen Computers aufgehoben wurde. | |
| DeploymentActive | Warten Sie, bis die gleichzeitige Bereitstellung für diese Ressourcengruppe abgeschlossen ist. | |
| DeploymentFailed | „DeploymentFailed“ ist ein allgemeiner Fehler, der nicht die Informationen bereitstellt, die Sie zum Beheben des Fehlers benötigen. Suchen Sie in den Fehlerdetails nach einem Fehlercode, der weitere Informationen bereitstellt. | [Ermitteln des Fehlercodes](#find-error-code) |
| DeploymentQuotaExceeded | Wenn der Grenzwert von 800 Bereitstellungen pro Ressourcengruppe erreicht ist, löschen Sie nicht mehr benötigte Bereitstellungen aus dem Verlauf. Bei Verwendung der Azure CLI können Sie mit [az group deployment delete](/cli/azure/group/deployment#az-group-deployment-delete) Einträge aus dem Verlauf löschen, in PowerShell verwenden Sie [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment). Das Löschen eines Eintrags aus dem Bereitstellungsverlaufs wirkt sich nicht auf die bereitgestellten Ressourcen aus. | |
| DnsRecordInUse | Der Name des DNS-Eintrags muss eindeutig sein. Geben Sie entweder einen anderen Namen an, oder ändern Sie den vorhandenen Datensatz. | |
| ImageNotFound | Überprüfen Sie die Einstellungen für das VM-Image. |  |
| InUseSubnetCannotBeDeleted | Dieser Fehler kann ggf. bei dem Versuch auftreten, eine Ressource zu aktualisieren, aber die Anforderung wird verarbeitet, indem die Ressource gelöscht und erstellt wird. Stellen Sie sicher, dass Sie alle unveränderten Werte angeben. | [Aktualisieren von Ressourcen](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Rufen Sie das Zugriffstoken für den entsprechenden Mandanten ab. Sie können das Token nur von dem Mandanten erhalten, zu dem Ihr Konto gehört. | |
| InvalidContentLink | Sie haben wahrscheinlich versucht, eine geschachtelte Vorlage zu verknüpfen, die nicht verfügbar ist. Überprüfen Sie den URI, den Sie für die geschachtelte Vorlage angegeben haben. Wenn die Vorlage in einem Speicherkonto vorhanden ist, stellen Sie sicher, dass auf den URI zugegriffen werden kann. Möglicherweise müssen Sie ein SAS-Token übergeben. | [Verknüpfte Vorlagen](resource-group-linked-templates.md) |
| InvalidParameter | Einer der Werte, die Sie für eine Ressource angegeben haben, stimmt nicht mit dem erwarteten Wert überein. Ursache für diesen Fehler können viele unterschiedliche Bedingungen sein. Beispielsweise kann ein Kennwort unzureichend oder ein Blobname fehlerhaft sein. Überprüfen Sie die Fehlermeldung, um zu ermitteln, welcher Wert korrigiert werden muss. | |
| InvalidRequestContent | Ihre Bereitstellungswerte enthalten entweder unerwartete Werte, oder es fehlen erforderliche Werte. Bestätigen Sie die Werte für Ihren Ressourcentyp. | [Vorlagenreferenz](/azure/templates/) |
| InvalidRequestFormat | Aktivieren Sie die Debugprotokollierung, wenn Sie die Bereitstellung durchführen, und überprüfen Sie den Inhalt der Anforderung. | [Debugprotokollierung](#enable-debug-logging) |
| InvalidResourceNamespace | Überprüfen Sie den Ressourcennamespace, den Sie in der **type**-Eigenschaft angegeben haben. | [Vorlagenreferenz](/azure/templates/) |
| InvalidResourceReference | Die Ressource ist entweder noch nicht vorhanden, oder der Verweis darauf ist fehlerhaft. Überprüfen Sie, ob Sie eine Abhängigkeit hinzufügen müssen. Überprüfen Sie, ob Ihre Verwendung der Funktion **reference** die für Ihr Szenario erforderlichen Parameter umfasst. | [Auflösen von Abhängigkeiten](resource-manager-not-found-errors.md) |
| InvalidResourceType | Überprüfen Sie den Ressourcentyp, den Sie in der **type**-Eigenschaft angegeben haben. | [Vorlagenreferenz](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Registrieren Sie Ihr Abonnement beim Ressourcenanbieter. | [Lösen von Registrierungsfehlern](resource-manager-register-provider-errors.md) |
| InvalidTemplate | Überprüfen Sie die Vorlagensyntax auf Fehler. | [Beheben von Fehlern für eine ungültige Vorlage](resource-manager-invalid-template-errors.md) |
| InvalidTemplateCircularDependency | Entfernen Sie unnötige Abhängigkeiten. | [Beheben von Ringabhängigkeiten](resource-manager-invalid-template-errors.md#circular-dependency) |
| LinkedAuthorizationFailed | Überprüfen Sie, ob Ihr Konto zu demselben Mandanten wie die Ressourcengruppe gehört, für die Sie die Bereitstellung durchführen. | |
| LinkedInvalidPropertyId | Die Ressourcen-ID für eine Ressource wird nicht richtig aufgelöst. Überprüfen Sie, ob Sie alle erforderlichen Werte für die Ressourcen-ID angegeben haben, z.B. Abonnement-ID, Name der Ressourcengruppe, Ressourcentyp, Name der übergeordneten Ressource (falls erforderlich) und Ressourcenname. | |
| LocationRequired | Geben Sie einen Speicherort für die Ressource an. | [Standort festlegen](resource-group-authoring-templates.md#resource-location) |
| MismatchingResourceSegments | Stellen Sie sicher, dass die geschachtelte Ressource die richtige Anzahl an Segmenten in Name und Typ aufweist. | [Auflösen von Ressourcensegmenten](resource-manager-invalid-template-errors.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Überprüfen Sie den Registrierungsstatus des Ressourcenanbieters und die unterstützten Speicherorte. | [Lösen von Registrierungsfehlern](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Registrieren Sie Ihr Abonnement beim Ressourcenanbieter. | [Lösen von Registrierungsfehlern](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Überprüfen Sie den Registrierungsstatus des Ressourcenanbieters. | [Lösen von Registrierungsfehlern](resource-manager-register-provider-errors.md) |
| NotFound | Unter Umständen versuchen Sie, eine abhängige Ressource parallel zu einer übergeordneten Ressource bereitzustellen. Überprüfen Sie, ob Sie eine Abhängigkeit hinzufügen müssen. | [Auflösen von Abhängigkeiten](resource-manager-not-found-errors.md) |
| OperationNotAllowed | Bei der Bereitstellung wird versucht, einen Vorgang durchzuführen, bei dem das Kontingent für das Abonnement, die Ressourcengruppe oder die Region überschritten wird. Ändern Sie die Bereitstellung nach Möglichkeit so, dass die Kontingentwerte eingehalten werden. Erwägen Sie andernfalls, eine Änderung der Kontingente anzufordern. | [Beheben von Fehlern für Kontingente](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Stellen Sie sicher, dass eine übergeordnete Ressource vorhanden ist, bevor Sie die untergeordneten Ressourcen erstellen. | [Beheben von Fehlern für die übergeordnete Ressource](resource-manager-parent-resource-errors.md) |
| PasswordTooLong | Möglicherweise haben Sie ein Kennwort mit zu vielen Zeichen ausgewählt, oder Sie haben Ihr Kennwort eventuell in eine sichere Zeichenfolge konvertiert, bevor es als Parameter übergeben wurde. Wenn die Vorlage einen Parameter **Sichere Zeichenfolge** enthält, müssen Sie den Wert nicht in eine sichere Zeichenfolge konvertieren. Geben Sie den Kennwortwert als Text an. |  |
| PrivateIPAddressInReservedRange | Die angegebene IP-Adresse enthält einen Adressbereich, der für Azure benötigt wird. Ändern Sie die IP-Adresse, um die Nutzung des reservierten Bereichs zu vermeiden. | [IP-Adressen](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | Die angegebene IP-Adresse liegt außerhalb des Subnetzbereichs. Ändern Sie die IP-Adresse so, das Sie innerhalb des Subnetzbereichs liegt. | [IP-Adressen](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Einige Eigenschaften können auf einer bereitgestellten Ressource nicht geändert werden. Beschränken Sie Ihre Änderungen beim Aktualisieren einer Ressource auf die zulässigen Eigenschaften. | [Aktualisieren von Ressourcen](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Ihr Abonnement enthält eine Ressourcenrichtlinie, durch die eine Aktion verhindert wird, die Sie während der Bereitstellung ausführen möchten. Suchen Sie nach der Richtlinie, die die Aktion blockiert. Ändern Sie Ihre Bereitstellung nach Möglichkeit so, dass die Einschränkungen der Richtlinie beachtet werden. | [Beheben von Fehlern für Richtlinien](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Geben Sie einen Ressourcennamen an, der keinen reservierten Name enthält. | [Resolve reserved resource name errors](resource-manager-reserved-resource-name.md) (Beheben von Fehlern mit reservierten Ressourcennamen) |
| ResourceGroupBeingDeleted | Warten Sie, bis der Löschvorgang abgeschlossen ist. | |
| ResourceGroupNotFound | Überprüfen Sie den Namen der Zielressourcengruppe für die Bereitstellung. Er muss in Ihrem Abonnement bereits vorhanden sein. Überprüfen Sie Ihren Abonnementkontext. | [Azure CLI](/cli/azure/account?#az-account-set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | Ihre Bereitstellung verweist auf eine Ressource, die nicht aufgelöst werden kann. Überprüfen Sie, ob Ihre Verwendung der Funktion **reference** die für Ihr Szenario erforderlichen Parameter enthält. | [Beheben von Fehlern für Verweise](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | Bei der Bereitstellung wird versucht, Ressourcen zu erstellen, für die das Kontingent für das Abonnement, die Ressourcengruppe oder die Region überschritten wird. Ändern Sie Ihre Infrastruktur nach Möglichkeit so, dass die Kontingentvorgaben eingehalten werden. Erwägen Sie andernfalls, eine Änderung der Kontingente anzufordern. | [Beheben von Fehlern für Kontingente](resource-manager-quota-errors.md) |
| SkuNotAvailable | Wählen Sie die SKU (z.B. die VM-Größe) aus, die für den ausgewählten Speicherort verfügbar ist. | [Beheben von SKU-Fehlern](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Geben Sie einen eindeutigen Namen für das Speicherkonto an. | [Resolve errors for storage account names](resource-manager-storage-account-name-errors.md) (Beheben von Fehlern für Namen von Speicherkonten)  |
| StorageAccountAlreadyTaken | Geben Sie einen eindeutigen Namen für das Speicherkonto an. | [Resolve errors for storage account names](resource-manager-storage-account-name-errors.md) (Beheben von Fehlern für Namen von Speicherkonten) |
| StorageAccountNotFound | Überprüfen Sie das Abonnement, die Ressourcengruppe und den Namen des Speicherkontos, das Sie verwenden möchten. | |
| SubnetsNotInSameVnet | Ein virtueller Computer kann nur über ein virtuelles Netzwerk verfügen. Stellen Sie beim Bereitstellen von mehreren NICs sicher, dass diese demselben virtuellen Netzwerk angehören. | [Mehrere NICs](../virtual-machines/windows/multiple-nics.md) |
| TemplateResourceCircularDependency | Entfernen Sie unnötige Abhängigkeiten. | [Beheben von Ringabhängigkeiten](resource-manager-invalid-template-errors.md#circular-dependency) |
| TooManyTargetResourceGroups | Verringern Sie die Anzahl von Ressourcengruppen für eine einzelne Bereitstellung. | [Ressourcengruppenübergreifende Bereitstellung](resource-manager-cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Ermitteln des Fehlercodes

Es gibt zwei Arten von Fehlern, die auftreten können:

* Überprüfungsfehler
* Bereitstellungsfehler

Überprüfungsfehler entstehen durch Szenarien, die vor der Bereitstellung bestimmt werden können. Dazu gehören Syntaxfehler in Ihrer Vorlage oder Versuche, Ressourcen bereitzustellen, die Ihre Abonnementkontingente überschreiten würden. Bereitstellungsfehler können durch Bedingungen verursacht werden, die während des Bereitstellungsprozesses auftreten. Dazu gehören Versuche, auf eine Ressource zuzugreifen, die parallel bereitgestellt wird.

Beide Fehlertypen geben einen Fehlercode zurück, der für die Problembehandlung für die Bereitstellung genutzt werden kann. Beide Fehlertypen werden im [Aktivitätsprotokoll](resource-group-audit.md) angezeigt. Überprüfungsfehler werden allerdings nicht im Bereitstellungsverlauf festgehalten, da die Bereitstellung tatsächlich nie gestartet wurde.

### <a name="validation-errors"></a>Überprüfungsfehler

Wenn Sie Bereitstellungen über das Portal ausführen, wird ein Überprüfungsfehler nach dem Senden der Werte angezeigt.

![Anzeigen eines Überprüfungsfehlers im Portal](./media/resource-manager-common-deployment-errors/validation-error.png)

Wählen Sie die Meldung aus, um weitere Details zu sehen. In der folgenden Abbildung sehen Sie einen **InvalidTemplateDeployment**-Fehler und eine Meldung, die auf eine durch eine Richtlinie blockierte Bereitstellung hinweist.

![Anzeigen von Überprüfungsdetails](./media/resource-manager-common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Bereitstellungsfehler

Wenn der Vorgang die Überprüfung besteht, aber ein Fehler während der Bereitstellung auftritt, wird ein Bereitstellungsfehler angezeigt.

Verwenden Sie Folgendes, um Fehlercodes und -meldungen bei der Bereitstellung mit PowerShell anzuzeigen:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Verwenden Sie Folgendes, um Fehlercodes und -meldungen bei der Bereitstellung mit Azure CLI anzuzeigen:

```azurecli-interactive
az group deployment operation list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

Wählen Sie im Portal die Benachrichtigung aus.

![Benachrichtigungsfehler](./media/resource-manager-common-deployment-errors/notification.png)

Sie sehen weitere Details zur Bereitstellung. Wählen Sie die Option aus, um weitere Informationen zum Fehler zu finden.

![Fehler bei der Bereitstellung](./media/resource-manager-common-deployment-errors/deployment-failed.png)

Die Fehlermeldung und die Fehlercodes werden angezeigt. Es sind zwei Fehlercodes vorhanden. Der erste Fehlercode (**DeploymentFailed**) ist ein allgemeiner Fehler, der nicht die Informationen bereitstellt, die Sie zum Beheben des Fehlers benötigen. Der zweite Fehlercode (**StorageAccountNotFound**) enthält die Details, die Sie benötigen. 

![Fehlerdetails](./media/resource-manager-common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Debugprotokollierung aktivieren

Manchmal benötigen Sie weitere Informationen zur Anforderung und zur Antwort, um den genauen Fehler zu ermitteln. Sie können anfordern, dass während der Bereitstellung zusätzliche Informationen protokolliert werden. 

### <a name="powershell"></a>PowerShell

Legen Sie in PowerShell den Parameter **DeploymentDebugLogLevel** auf „All“, „ResponseContent“ oder „RequestContent“ fest.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Überprüfen Sie den Anforderungsinhalt mit folgendem Cmdlet:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Oder überprüfen Sie den Antwortinhalt mit:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Mithilfe dieser Informationen können Sie ermitteln, ob ein Wert in der Vorlage nicht ordnungsgemäß festgelegt wurde.

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Derzeit unterstützt Azure CLI das Aktivieren der Debugprotokollierung nicht, Sie können diese jedoch abrufen.

Untersuchen Sie die Bereitstellungsvorgänge mit folgendem Befehl:

```azurecli
az group deployment operation list \
  --resource-group examplegroup \
  --name exampledeployment
```

Überprüfen Sie den Anforderungsinhalt mit folgendem Befehl:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Überprüfen Sie den Antwortinhalt mit folgendem Befehl:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Geschachtelte Vorlage

Verwenden Sie zum Protokollieren von Debuginformationen zu einer geschachtelten Vorlage das **debugSetting**-Element.

```json
{
    "apiVersion": "2016-09-01",
    "name": "nestedTemplate",
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "{template-uri}",
            "contentVersion": "1.0.0.0"
        },
        "debugSetting": {
           "detailLevel": "requestContent, responseContent"
        }
    }
}
```

## <a name="create-a-troubleshooting-template"></a>Erstellen einer Vorlage zur Problembehandlung

Mitunter ist die einfachste Möglichkeit für die Behandlung von Problemen bei Ihrer Vorlage das Testen von Teilen davon. Sie können eine vereinfachte Vorlage erstellen, die es Ihnen ermöglicht, sich auf den Teil zu konzentrieren, der Ihrer Meinung nach den Fehler verursacht. Nehmen wir beispielsweise an, dass ein Fehler auftritt, wenn Sie auf eine Ressource verweisen. Anstatt sich mit einer gesamten Vorlage zu beschäftigen, erstellen Sie eine Vorlage, die den Teil wiedergibt, der Ihr Problem möglicherweise verursacht. So können Sie besser ermitteln, ob die richtigen Parameter übergeben, Vorlagenfunktionen ordnungsgemäß genutzt und die Ressourcen abgerufen werden, die Sie erwarten.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

Ein anderes Beispiel: Es treten Bereitstellungsfehler auf, von denen Sie annehmen, dass sie aufgrund falsch festgelegter Abhängigkeiten entstehen. Testen Sie Ihre Vorlage, indem Sie sie in einfachere Vorlagen aufteilen. Erstellen Sie zunächst eine Vorlage, mit der nur eine einzige Ressource bereitgestellt wird (z.B. eine SQL Server-Instanz). Wenn Sie sicher sind, dass die Ressource richtig definiert ist, fügen Sie eine Ressource hinzu, die davon abhängig ist (z.B. eine SQL-Datenbank). Wenn diese beiden Ressourcen richtig definiert sind, fügen Sie weitere abhängige Ressourcen hinzu (z.B. Überwachungsrichtlinien). Löschen Sie zwischen den jeweiligen Testbereitstellungen die Ressourcengruppe, um sicherzustellen, dass Sie die Abhängigkeiten angemessen testen.


## <a name="next-steps"></a>Nächste Schritte

* Ein Tutorial zur Problembehandlung finden Sie unter [Tutorial: Problembehandlung bei der Resource Manager-Vorlagenbereitstellung](./resource-manager-tutorial-troubleshoot.md).
* Informationen zur Überwachung von Aktionen finden Sie unter [Überwachen von Vorgängen mit Resource Manager](resource-group-audit.md).
* Weitere Informationen zu Aktionen zum Bestimmen von Fehlern während der Bereitstellung finden Sie unter [Anzeigen von Bereitstellungsvorgängen mit dem Azure-Portal](resource-manager-deployment-operations.md).
