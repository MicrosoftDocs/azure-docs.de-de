---
title: Bereitstellen von Ressourcen mit PowerShell und Vorlagen | Microsoft Docs
description: Verwenden Sie Azure Resource Manager und Azure PowerShell, um Ressourcen in Azure bereitzustellen. Die Ressourcen werden in einer Resource Manager-Vorlage definiert.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tomfitz
ms.openlocfilehash: 63d729f19b0ef20d0e7a716d6857b4627095856b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476985"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell

Erfahren Sie, wie Ihre Ressourcen mit Azure PowerShell und Resource Manager-Vorlagen in Azure bereitgestellt werden. Weitere Informationen zu den Konzepten der Bereitstellung und Verwaltung Ihrer Azure-Lösungen finden Sie unter [Übersicht über den Azure Resource Manager](resource-group-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment-scope"></a>Bereitstellungsumfang

Sie können Ihre Bereitstellung entweder auf ein Azure-Abonnement oder eine Ressourcengruppe innerhalb eines Abonnements ausrichten. In den meisten Fällen wählen Sie die Bereitstellung in einer Ressourcengruppe aus. Verwenden Sie Abonnementbereitstellungen, um Richtlinien und Rollenzuweisungen im gesamten Abonnement anzuwenden. Sie verwenden Abonnementbereitstellungen auch, um eine Ressourcengruppe zu erstellen und Ressourcen für sie bereitzustellen. Abhängig vom Umfang der Bereitstellung verwenden Sie unterschiedliche Befehle.

Verwenden Sie [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment), um eine **Ressourcengruppe** bereitzustellen:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Verwenden Sie [New-AzDeployment](/powershell/module/az.resources/new-azdeployment), um ein **Abonnement** bereitzustellen:

```azurepowershell
New-AzDeployment -Location <location> -TemplateFile <path-to-template>
```

Derzeit werden Verwaltungsgruppenbereitstellungen nur über die REST-API unterstützt. Weitere Informationen finden Sie unter [Bereitstellen von Ressourcen mit Resource Manager-Vorlagen und Resource Manager-REST-API](resource-group-template-deploy-rest.md).

Die Beispiele in diesem Artikel verwenden Ressourcengruppenbereitstellungen. Weitere Informationen zu Abonnementbereitstellungen finden Sie unter [Erstellen von Ressourcengruppen und Ressourcen auf Abonnementebene](deploy-to-subscription.md).

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen eine Vorlage für die Bereitstellung. Wenn Sie noch keine besitzen, laden Sie eine [Beispielvorlage](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) aus dem Repository der Azure-Schnellstartvorlagen herunter, und speichern Sie diese. In diesem Artikel wird der lokale Dateiname **c:\MyTemplates\azuredeploy.json** verwendet.

Sofern Sie nicht Azure Cloud Shell zum Bereitstellen von Vorlagen verwenden, müssen Sie Azure PowerShell installieren und eine Verbindung mit Azure herstellen:

- **Installieren Sie Azure PowerShell-Cmdlets auf Ihrem lokalen Computer.** Weitere Informationen finden Sie unter [Erste Schritte mit Azure PowerShell](/powershell/azure/get-started-azureps).
- **Stellen Sie mithilfe von [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount) eine Verbindung mit Azure her**. Wenn Sie über mehrere Azure-Abonnements verfügen, müssen Sie möglicherweise auch [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) ausführen. Weitere Informationen finden Sie unter [Verwenden mehrerer Azure-Abonnements](/powershell/azure/manage-subscriptions-azureps).

## <a name="deploy-local-template"></a>Bereitstellen einer lokalen Vorlage

Im folgenden Beispiel wird eine Ressourcengruppe erstellt und eine Vorlage von Ihrem lokalen Computer aus bereitgestellt. Der Name einer Ressourcengruppe darf nur alphanumerische Zeichen, Punkte, Unterstriche, Bindestriche und Klammern enthalten. Der Name kann bis zu 90 Zeichen umfassen. Der Name darf nicht mit einem Punkt enden.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Die Bereitstellung kann einige Minuten dauern.

## <a name="deploy-remote-template"></a>Bereitstellen einer Remotevorlage

Anstatt Resource Manager-Vorlagen auf dem lokalen Computer zu speichern, könnten Sie sie vorzugsweise an einem externen Speicherort speichern. Sie können Vorlagen in einem Quellcodeverwaltungs-Repository (z.B. GitHub) speichern. Für den gemeinsamen Zugriff in Ihrer Organisation können Sie sie auch in einem Azure-Speicherkonto speichern.

Verwenden Sie zum Bereitstellen einer externen Vorlage den **TemplateUri**-Parameter. Verwenden Sie den URI im Beispiel, um die Beispielvorlage aus GitHub bereitzustellen.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Das obige Beispiel erfordert einen URI mit öffentlichem Zugriff für die Vorlage, was in den meisten Szenarien funktioniert, da die Vorlage keine vertraulichen Daten enthalten sollte. Wenn Sie vertrauliche Daten (z.B. ein Administratorkennwort) angeben müssen, übergeben Sie diesen Wert als sicheren Parameter. Wenn Sie jedoch keinen öffentlichen Zugriff auf Ihre Vorlage wünschen, können Sie sie schützen, indem Sie sie in einem privaten Speichercontainer speichern. Informationen zum Bereitstellen einer Vorlage, die ein SAS-Token (Shared Access Signature) erfordert, finden Sie unter [Bereitstellen einer privaten Vorlage mit SAS-Token](resource-manager-powershell-sas-token.md). Ein entsprechendes Tutorial finden Sie unter [Tutorial: Integrieren von Azure Key Vault in die Resource Manager-Vorlagenbereitstellung](./resource-manager-tutorial-use-key-vault.md).

## <a name="deploy-from-azure-cloud-shell"></a>Bereitstellen über Azure Cloud Shell

Sie können Ihre Vorlage mithilfe von [Azure Cloud Shell](https://shell.azure.com) bereitstellen. Geben Sie zum Bereitstellen einer externen Vorlage den URI der Vorlage an. Um eine lokale Vorlage bereitzustellen, müssen Sie die Vorlage zuerst in das Speicherkonto für Ihre Cloud Shell-Instanz laden. Wählen Sie zum Hochladen von Dateien in die Shell das Menüsymbol **Dateien hochladen/herunterladen** im Shell-Fenster aus.

Zum Öffnen der Cloudshell navigieren Sie zu [https://shell.azure.com](https://shell.azure.com), oder wählen Sie aus dem folgenden Codeabschnitt **Try-It** aus:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Um den Code in die Shell einzufügen, klicken Sie mit der rechten Maustaste in der Shell und wählen dann **Einfügen** aus.

## <a name="redeploy-when-deployment-fails"></a>Erneute Bereitstellung bei Bereitstellungsfehlern

Dieses Feature ist auch bekannt als *Rollback bei Fehler*. Wenn eine Bereitstellung fehlschlägt, können Sie automatisch eine frühere, erfolgreiche Bereitstellung aus Ihrem Bereitstellungsverlauf bereitstellen. Zum Angeben einer erneuten Bereitstellung verwenden Sie entweder den Parameter `-RollbackToLastDeployment` oder `-RollBackDeploymentName` im Bereitstellungsbefehl. Diese Funktionalität ist nützlich, wenn es einen bekannten guten Zustand für die Infrastrukturbereitstellung gibt, der wiederhergestellt werden soll. Es gibt eine Reihe von Vorbehalten und Einschränkungen:

- Die Bereitstellung wird genauso wie zuvor mit denselben Parametern ausgeführt. Sie können die Parameter nicht ändern.
- Die vorherige Bereitstellung wird im [vollständigen Modus](./deployment-modes.md#complete-mode) ausgeführt. Alle in der vorherigen Bereitstellung nicht enthaltenen Ressourcen werden gelöscht, und alle Ressourcenkonfigurationen werden auf ihren vorherigen Zustand zurückgesetzt. Sorgen Sie dafür, dass Sie die [Bereitstellungsmodi](./deployment-modes.md) vollständig verstehen.
- Die erneute Bereitstellung wirkt sich nur auf die Ressourcen aus. Datenänderungen sind davon nicht betroffen.
- Dieses Feature wird nur bei Bereitstellungen von Ressourcengruppen unterstützt, nicht bei Bereitstellungen auf Abonnementebene. Weitere Informationen zu Bereitstellungen auf Abonnementebene finden Sie unter [Erstellen von Ressourcengruppen und Ressourcen auf Abonnementebene](./deploy-to-subscription.md).

Zur Verwendung dieser Option müssen die Bereitstellungen eindeutige Namen aufweisen, damit sie im Verlauf identifiziert werden können. Wenn die Bereitstellungen keine eindeutigen Namen aufweisen, wird die vorherige erfolgreich ausgeführte Bereitstellung im Verlauf möglicherweise durch die aktuelle fehlerhafte Bereitstellung überschrieben. Diese Option kann nur für Bereitstellungen auf Stammebene verwendet werden. Bereitstellungen aus einer geschachtelten Vorlage können nicht erneut bereitgestellt werden.

Um die letzte erfolgreiche Bereitstellung erneut bereitzustellen, fügen Sie den Parameter `-RollbackToLastDeployment` als Flag hinzu.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Um eine bestimmte Bereitstellung erneut bereitzustellen, verwenden den Parameter `-RollBackDeploymentName` und geben den Namen der Bereitstellung an.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

Die angegebene Bereitstellung muss erfolgreich ausgeführt worden sein.

## <a name="pass-parameter-values"></a>Übergeben von Parameterwerten

Zum Übergeben von Parameterwerten können Sie entweder Inlineparameter oder eine Parameterdatei verwenden. In den obigen Beispielen in diesem Artikel werden Inlineparameter verwendet.

### <a name="inline-parameters"></a>Inlineparameter

Geben Sie zum Übergeben von Inlineparametern die Parameternamen mit dem Befehl `New-AzResourceGroupDeployment` an. Wenn Sie beispielsweise eine Zeichenfolge und ein Array an eine Vorlage in einer Vorlage übergeben möchten, verwenden Sie Folgendes:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Sie können auch den Inhalt einer Datei abrufen und als Inlineparameter übergeben.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Das Abrufen eines Parameterwerts aus einer Datei ist praktisch, wenn Sie Konfigurationswerte angeben müssen. Sie können beispielsweise [cloud-init-Werte für einen virtuellen Linux-Computer](../virtual-machines/linux/using-cloud-init.md) angeben.

Wenn Sie ein Array von Objekten übergeben müssen, erstellen Sie Hashtabellen in PowerShell, und fügen Sie sie einem Array hinzu. Übergeben Sie dieses Array während der Bereitstellung als Parameter.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```


### <a name="parameter-files"></a>Parameterdateien

Anstatt Parameter als Inlinewerte in Ihrem Skript zu übergeben, ist es wohl einfacher, eine JSON-Datei zu verwenden, die die Parameterwerte enthält. Bei der Parameterdatei kann es sich um eine lokale Datei oder eine externe Datei mit einem erreichbaren URI handeln.

Die Parameterdatei muss im folgenden Format vorliegen:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Beachten Sie, dass der Parameterabschnitt einen Parameternamen enthält, der dem in Ihrer Vorlage definierten Parameter (storageAccountType) entspricht. Die Parameterdatei enthält einen Wert für den Parameter. Dieser Wert wird der Vorlage automatisch während der Bereitstellung übergeben. Sie können mehrere Parameterdateien erstellen und dann die entsprechende Parameterdatei für das Szenario übergeben.

Kopieren Sie das obige Beispiel, und speichern Sie es unter dem Dateinamen `storage.parameters.json`.

Verwenden Sie den **TemplateParameterFile**-Parameter, um eine lokale Parameterdatei zu übergeben:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Um eine externe Parameterdatei zu übergeben, verwenden Sie den **TemplateParameterUri**-Parameter:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

### <a name="parameter-precedence"></a>Parameterrangfolge

Sie können Inlineparameter und eine lokale Parameterdatei im selben Bereitstellungsvorgang verwenden. Sie können beispielsweise einige Werte in der lokalen Parameterdatei angeben und weitere Werte während der Bereitstellung inline hinzufügen. Wenn Sie Werte für einen Parameter sowohl in der lokalen Parameterdatei als auch inline bereitstellen, haben die Inlinewerte Vorrang.

Bei Verwendung einer externen Parameterdatei können Sie jedoch keine anderen Werte (weder inline noch aus einer lokalen Datei) übergeben. Wenn Sie eine Parameterdatei im Parameter **TemplateParameterUri** angeben, werden alle Inlineparameter ignoriert. Stellen Sie alle Parameterwerte in der externen Datei bereit. Wenn Ihre Vorlage einen vertraulichen Wert enthält, der nicht in die Parameterdatei aufgenommen werden kann, fügen Sie diesen Wert einem Schlüsseltresor hinzu. Alternativ können Sie alle Parameterwerte dynamisch inline bereitstellen.

### <a name="parameter-name-conflicts"></a>Parameternamenskonflikte

Wenn Ihre Vorlage einen Parameter enthält, der den gleichen Namen besitzt wie einer der Parameter des PowerShell-Befehls, zeigt PowerShell den Parameter der Vorlage mit dem Postfix **FromTemplate** an. Beispiel: Ein Parameter namens **ResourceGroupName** in Ihrer Vorlage verursacht einen Konflikt mit dem Parameter **ResourceGroupName** im Cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Sie werden zur Eingabe eines Werts für **ResourceGroupNameFromTemplate** aufgefordert. Im Allgemeinen sollten Sie diese Verwirrung vermeiden, indem Sie Parametern nicht dieselben Namen wie Parametern für Bereitstellungsvorgänge geben.

## <a name="test-template-deployments"></a>Testen von Vorlagenbereitstellungen

Verwenden Sie [Test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment), um die Vorlage und die Parameterwerte zu testen, ohne dabei Ressourcen bereitzustellen. 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Wenn keine Fehler erkannt werden, wird der Befehl ohne Antwort abgeschlossen. Wenn ein Fehler erkannt wird, gibt der Befehl eine Fehlermeldung zurück. Beispielsweise wird beim Übergeben eines falschen Werts für die Speicherkonto-SKU der folgende Fehler zurückgegeben:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Wenn Ihre Vorlage einen Syntaxfehler aufweist, gibt der Befehl einen Fehler zurück, der angibt, dass die Vorlage nicht analysiert werden konnte. Die Meldung gibt Zeilennummer und Position des Analysefehlers an.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum sicheren Rollout Ihres Diensts in mehreren Regionen finden Sie im Artikel zum [Azure-Bereitstellungs-Manager](deployment-manager-overview.md).
- Wenn Sie angeben möchten, wie Ressourcen behandelt werden sollen, die in der Ressourcengruppe enthalten sind, aber nicht in der Vorlage definiert wurden, lesen Sie die Informationen unter [Azure Resource Manager-Bereitstellungsmodi](deployment-modes.md).
- Um zu verstehen, wie Parameter in der Vorlage definiert werden, lesen Sie [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](resource-group-authoring-templates.md).
- Informationen zum Bereitstellen einer Vorlage, die ein SAS-Token erfordert, finden Sie unter [Bereitstellen einer privaten Vorlage mit SAS-Token](resource-manager-powershell-sas-token.md).
