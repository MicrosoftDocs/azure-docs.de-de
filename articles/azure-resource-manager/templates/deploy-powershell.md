---
title: Bereitstellen von Ressourcen mit PowerShell und Vorlagen
description: Verwenden Sie Azure Resource Manager und Azure PowerShell, um Ressourcen in Azure bereitzustellen. Die Ressourcen werden in einer Resource Manager-Vorlage definiert.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 5266aa51422dce6dfa4b82238e905f4f630ccf48
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92668558"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Bereitstellen von Ressourcen mit ARM-Vorlagen und Azure PowerShell

In diesem Artikel wird erläutert, wie Ihre Anwendung mit Azure PowerShell und Azure Resource Manager-Vorlagen (ARM) in Azure bereitgestellt wird. Wenn Sie nicht mit den Konzepten der Bereitstellung und Verwaltung Ihrer Azure-Lösungen vertraut sind, informieren Sie sich unter [Übersicht über die Vorlagenbereitstellung](overview.md).

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen eine Vorlage für die Bereitstellung. Wenn Sie noch keine besitzen, laden Sie eine [Beispielvorlage](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) aus dem Repository der Azure-Schnellstartvorlagen herunter, und speichern Sie diese. In diesem Artikel wird der lokale Dateiname **c:\MyTemplates\azuredeploy.json** verwendet.

Sie müssen Azure PowerShell installieren und eine Verbindung zu Azure herstellen:

- **Installieren Sie Azure PowerShell-Cmdlets auf Ihrem lokalen Computer.** Weitere Informationen finden Sie unter [Erste Schritte mit Azure PowerShell](/powershell/azure/get-started-azureps).
- **Stellen Sie mithilfe von [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount) eine Verbindung mit Azure her**. Wenn Sie über mehrere Azure-Abonnements verfügen, müssen Sie möglicherweise auch [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) ausführen. Weitere Informationen finden Sie unter [Verwenden mehrerer Azure-Abonnements](/powershell/azure/manage-subscriptions-azureps).

Wenn PowerShell nicht installiert ist, können Sie Cloud Shell verwenden. Weitere Informationen finden Sie unter [Bereitstellen von ARM-Vorlagen über Cloud Shell](deploy-cloud-shell.md).

## <a name="deployment-scope"></a>Bereitstellungsumfang

Sie können als Ziel für Ihre Bereitstellung eine Ressourcengruppe, ein Abonnement, eine Verwaltungsgruppe oder einen Mandanten verwenden. Abhängig vom Umfang der Bereitstellung verwenden Sie unterschiedliche Befehle.

* Verwenden Sie [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment), um eine **Ressourcengruppe** bereitzustellen:

  ```azurepowershell
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
  ```

* Verwenden Sie „New-AzSubscriptionDeployment“, um ein **Abonnement** bereitzustellen:

  ```azurepowershell
  New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Weitere Informationen zu Bereitstellungen auf Abonnementebene finden Sie unter [Erstellen von Ressourcengruppen und Ressourcen auf Abonnementebene](deploy-to-subscription.md).

* Verwenden Sie zum Bereitstellen in einer **Verwaltungsgruppe** das Cmdlet [New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment).

  ```azurepowershell
  New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Weitere Informationen zu Bereitstellungen auf Verwaltungsgruppenebene finden Sie unter [Erstellen von Ressourcen auf der Verwaltungsgruppenebene](deploy-to-management-group.md).

* Für die Bereitstellung in einem **Mandanten** verwenden Sie [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

  ```azurepowershell
  New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Weitere Informationen zu Bereitstellungen auf Mandantenebene finden Sie unter [Erstellen von Ressourcen auf der Mandantenebene](deploy-to-tenant.md).

Der Benutzer, der die Vorlage bereitstellt, muss für jeden Bereich über die erforderlichen Berechtigungen zum Erstellen von Ressourcen verfügen.

## <a name="deploy-local-template"></a>Bereitstellen einer lokalen Vorlage

Sie können eine Vorlage bereitstellen, die auf Ihrem lokalen Computer oder extern gespeichert ist. In diesem Abschnitt wird die Bereitstellung einer lokalen Vorlage beschrieben.

Wenn eine Bereitstellung in einer Ressourcengruppe erfolgen soll, die nicht vorhanden ist, erstellen Sie zunächst die Ressourcengruppe. Der Name einer Ressourcengruppe darf nur alphanumerische Zeichen, Punkte, Unterstriche, Bindestriche und Klammern enthalten. Der Name kann bis zu 90 Zeichen umfassen. Der Name darf nicht mit einem Punkt enden.

```azurepowershell
New-AzResourceGroup -Name ExampleGroup -Location "Central US"
```

Verwenden Sie zum Bereitstellen einer lokalen Vorlage im Bereitstellungsbefehl den Parameter `-TemplateFile`. Das folgende Beispiel zeigt auch, wie ein Parameterwert festgelegt wird, der aus der Vorlage stammt.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Die Bereitstellung kann einige Minuten dauern.

## <a name="deploy-remote-template"></a>Bereitstellen einer Remotevorlage

Anstatt ARM-Vorlagen auf dem lokalen Computer zu speichern, könnten Sie sie auch an einem externen Speicherort speichern. Sie können Vorlagen in einem Quellcodeverwaltungs-Repository (z.B. GitHub) speichern. Für den gemeinsamen Zugriff in Ihrer Organisation können Sie sie auch in einem Azure-Speicherkonto speichern.

Wenn eine Bereitstellung in einer Ressourcengruppe erfolgen soll, die nicht vorhanden ist, erstellen Sie zunächst die Ressourcengruppe. Der Name einer Ressourcengruppe darf nur alphanumerische Zeichen, Punkte, Unterstriche, Bindestriche und Klammern enthalten. Der Name kann bis zu 90 Zeichen umfassen. Der Name darf nicht mit einem Punkt enden.

```azurepowershell
New-AzResourceGroup -Name ExampleGroup -Location "Central US"
```

Verwenden Sie zum Bereitstellen einer externen Vorlage den `-TemplateUri`-Parameter.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Das obige Beispiel erfordert einen URI mit öffentlichem Zugriff für die Vorlage, was in den meisten Szenarien funktioniert, da die Vorlage keine vertraulichen Daten enthalten sollte. Wenn Sie vertrauliche Daten (z.B. ein Administratorkennwort) angeben müssen, übergeben Sie diesen Wert als sicheren Parameter. Wenn Sie jedoch den Zugriff auf die Vorlage verwalten möchten, erwägen Sie den Einsatz von [Vorlagenspezifikationen](#deploy-template-spec).

## <a name="deployment-name"></a>„Deployment name“ (Bereitstellungsname)

Wenn Sie eine ARM-Vorlage bereitstellen, können Sie der Bereitstellung einen Namen geben. Dieser Name kann das Abrufen der Bereitstellung aus dem Bereitstellungsverlauf vereinfachen. Wenn Sie keinen Namen für die Bereitstellung angeben, wird der Name der Vorlagendatei verwendet. Wenn Sie beispielsweise eine Vorlage mit dem Namen `azuredeploy.json` bereitstellen und keinen Bereitstellungsnamen angeben, erhält die Bereitstellung den Namen `azuredeploy`.

Bei jedem Ausführen einer Bereitstellung wird dem Bereitstellungsverlauf der Ressourcengruppe ein Eintrag mit dem Bereitstellungsnamen hinzugefügt. Wenn Sie eine andere Bereitstellung ausführen und denselben Namen vergeben, wird der vorherige Eintrag durch die aktuelle Bereitstellung ersetzt. Wenn Sie eindeutige Einträge im Bereitstellungsverlauf beibehalten möchten, müssen Sie jeder Bereitstellung einen eindeutigen Namen geben.

Um einen eindeutigen Namen zu erstellen, können Sie eine Zufallszahl zuweisen.

```azurepowershell-interactive
$suffix = Get-Random -Maximum 1000
$deploymentName = "ExampleDeployment" + $suffix
```

Sie können auch einen Datumswert hinzufügen.

```azurepowershell-interactive
$today=Get-Date -Format "MM-dd-yyyy"
$deploymentName="ExampleDeployment"+"$today"
```

Wenn Sie gleichzeitige Bereitstellungen in derselben Ressourcengruppe mit dem gleichen Bereitstellungsnamen ausführen, wird nur die letzte Bereitstellung abgeschlossen. Alle Bereitstellungen mit dem gleichen Namen, die noch nicht abgeschlossen wurden, werden durch die letzte Bereitstellung ersetzt. Wenn Sie z. B. eine Bereitstellung mit dem Namen `newStorage` ausführen, die ein Speicherkonto mit dem Namen `storage1` bereitstellt, und gleichzeitig eine andere Bereitstellung mit dem Namen `newStorage` ausführen, die ein Speicherkonto mit dem Namen `storage2` bereitstellt, wird nur ein Speicherkonto bereitgestellt. Das resultierende Speicherkonto hat den Namen `storage2`.

Führen Sie jedoch eine Bereitstellung mit dem Namen `newStorage` aus, die ein Speicherkonto mit dem Namen `storage1` bereitstellt, und führen Sie direkt nach dem Abschluss eine andere Bereitstellung mit dem Namen `newStorage` aus, die ein Speicherkonto mit dem Namen `storage2` bereitstellt, erhalten Sie zwei Speicherkonten. Eines hat den Namen `storage1` und das andere den Namen `storage2`. Es ist jedoch nur ein Eintrag im Bereitstellungsverlauf vorhanden.

Wenn Sie für jede Bereitstellung einen eindeutigen Namen angeben, können Sie diese ohne Konflikt gleichzeitig ausführen. Wenn Sie eine Bereitstellung namens `newStorage1` ausführen, die ein Speicherkonto namens `storage1` bereitstellt, und gleichzeitig eine andere Bereitstellung namens `newStorage2` ausführen, die ein Speicherkonto namens `storage2` bereitstellt, erhalten Sie zwei Speicherkonten und zwei Einträge im Bereitstellungsverlauf.

Um Konflikte mit gleichzeitigen Bereitstellungen zu vermeiden und eindeutige Einträge im Bereitstellungsverlauf zu gewährleisten, geben Sie jeder Bereitstellung einen eindeutigen Namen.

## <a name="deploy-template-spec"></a>Bereitstellen der Vorlagenspezifikationen

Anstatt eine lokale oder Remotevorlage bereitzustellen, können Sie eine [Vorlagenspezifikation](template-specs.md) erstellen. Bei der Vorlagenspezifikation handelt es sich um eine Ressource im Azure-Abonnement, die eine ARM-Vorlage enthält. Sie vereinfacht die sichere Freigabe der Vorlage für Benutzer in Ihrer Organisation. Mit der rollenbasierten Zugriffssteuerung von Azure (Role-Based Access Control, Azure RBAC) können Sie Zugriff auf die Vorlagenspezifikation gewähren. Diese Funktion steht derzeit als Vorschau zur Verfügung.

In den folgenden Beispielen wird das Erstellen und Bereitstellen einer Vorlagenspezifikation veranschaulicht. Diese Befehle sind nur verfügbar, wenn Sie sich für die [Vorschau registriert haben](https://aka.ms/templateSpecOnboarding).

Als Erstes erstellen Sie die Vorlagenspezifikation, indem Sie die ARM-Vorlage angeben.

```azurepowershell
New-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0 `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateJsonFile ./mainTemplate.json
```

Rufen Sie anschließend die ID der Vorlagenspezifikation ab, und stellen Sie sie bereit.

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

Weitere Informationen finden Sie unter [Azure Resource Manager-Vorlagenspezifikationen (Vorschau)](template-specs.md).

## <a name="preview-changes"></a>Vorschau der Änderungen

Vor dem Bereitstellen der Vorlage können Sie die Änderungen, die von der Vorlage an Ihrer Umgebung vorgenommen werden, in der Vorschau anzeigen. Überprüfen Sie anhand des [„Was-wäre-wenn“-Vorgangs](template-deploy-what-if.md), ob die Vorlage die erwarteten Änderungen vornimmt. „Was-wäre-wenn“ überprüft auch die Vorlage auf Fehler.

## <a name="pass-parameter-values"></a>Übergeben von Parameterwerten

Zum Übergeben von Parameterwerten können Sie entweder Inlineparameter oder eine Parameterdatei verwenden.

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

Das Abrufen eines Parameterwerts aus einer Datei ist praktisch, wenn Sie Konfigurationswerte angeben müssen. Sie können beispielsweise [cloud-init-Werte für einen virtuellen Linux-Computer](../../virtual-machines/linux/using-cloud-init.md) angeben.

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

Weitere Informationen zur Parameterdatei finden Sie unter [Erstellen einer Resource Manager-Parameterdatei](parameter-files.md).

Verwenden Sie den **TemplateParameterFile** -Parameter, um eine lokale Parameterdatei zu übergeben:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Um eine externe Parameterdatei zu übergeben, verwenden Sie den **TemplateParameterUri** -Parameter:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Rollback zu einer erfolgreiche Bereitstellung, wenn ein Fehler auftritt, finden Sie unter [Rollback bei Fehler zu erfolgreicher Bereitstellung](rollback-on-error.md).
- Wenn Sie angeben möchten, wie Ressourcen behandelt werden sollen, die in der Ressourcengruppe enthalten sind, aber nicht in der Vorlage definiert wurden, lesen Sie die Informationen unter [Azure Resource Manager-Bereitstellungsmodi](deployment-modes.md).
- Um zu verstehen, wie Parameter in der Vorlage definiert werden, lesen Sie [Verstehen der Struktur und Syntax von ARM-Vorlagen](template-syntax.md).
- Informationen zum Bereitstellen einer Vorlage, die ein SAS-Token erfordert, finden Sie unter [Bereitstellen einer privaten Vorlage mit SAS-Token](secure-template-with-sas-token.md).
