---
title: Integrieren von Azure Key Vault in die Resource Manager-Vorlagenbereitstellung | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie bei der Resource Manager-Vorlagenbereitstellung sichere Parameterwerte mithilfe von Azure Key Vault übergeben.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 0d78e6eaca708073c3a216507b320fe8783a25b6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239238"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>Tutorial: Integrieren von Azure Key Vault in die Resource Manager-Vorlagenbereitstellung

Hier erfahren Sie, wie Sie bei der Resource Manager-Bereitstellung Geheimnisse aus Azure Key Vault abrufen und als Parameter übergeben. Der Wert wird nie offengelegt, da Sie nur auf die Schlüsseltresor-ID verweisen. Weitere Informationen finden Sie unter [Verwenden von Azure Key Vault zum Übergeben eines sicheren Parameterwerts während der Bereitstellung](./resource-manager-keyvault-parameter.md).

Im Tutorial [Erstellen von Azure Resource Manager-Vorlagen mit abhängigen Ressourcen](./resource-manager-tutorial-create-templates-with-dependent-resources.md) erstellen Sie einen virtuellen Computer. Sie müssen den Administratorbenutzernamen für den virtuellen Computer und das zugehörige Kennwort eingeben. Anstatt das Kennwort einzugeben, können Sie es in Azure Key Vault vorab speichern und die Vorlage anschließend anpassen, um das Kennwort während der Bereitstellung aus dem Schlüsseltresor abzurufen.

![Resource Manager-Vorlage: Key Vault-Integration – Diagramm](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Vorbereiten eines Schlüsseltresors
> * Öffnen einer Schnellstartvorlage
> * Bearbeiten der Parameterdatei
> * Bereitstellen der Vorlage
> * Überprüfen der Bereitstellung
> * Bereinigen von Ressourcen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:

* [Visual Studio Code](https://code.visualstudio.com/) mit der [Erweiterung „Azure Resource Manager-Tools“](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)
* Verwenden Sie aus Sicherheitsgründen ein generiertes Kennwort für das Administratorkonto des virtuellen Computers. Hier sehen Sie ein Beispiel für die Kennwortgenerierung:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Vergewissern Sie sich, dass das generierte Kennwort die VM-Kennwortanforderungen erfüllt. Jeder Azure-Dienst hat bestimmte Kennwortanforderungen. Informationen zu den Kennwortanforderungen für virtuelle Computer finden Sie unter [Welche Anforderungen an das Kennwort gelten beim Erstellen eines virtuellen Computers?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Vorbereiten eines Schlüsseltresors

In diesem Abschnitt erstellen Sie einen Schlüsseltresor und fügen ihm ein Geheimnis hinzu, damit Sie das Geheimnis beim Bereitstellen der Vorlage abrufen können. Es gibt zahlreiche Möglichkeiten zum Erstellen eines Schlüsseltresors. In diesem Tutorial verwenden Sie Azure PowerShell, um eine [Resource Manager-Vorlage](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json) bereitstellen. Diese Vorlage umfasst Folgendes:

* Erstellen eines Schlüsseltresors mit aktivierter `enabledForTemplateDeployment`-Eigenschaft. Diese Eigenschaft muss auf „true“ festgelegt sein, damit der Vorlagenbereitstellungsprozess auf die in diesem Schlüsseltresor definierten Geheimnisse zugreifen kann.
* Hinzufügen eines Geheimnisses zum Schlüsseltresor.  Das Geheimnis enthält das Administratorkennwort für den virtuellen Computer.

> [!NOTE]
> Wenn Sie (als der Benutzer, der die VM-Vorlage bereitstellt) nicht der Besitzer oder Mitwirkende für den Schlüsseltresor sind, muss der Besitzer oder ein Mitwirkender für den Schlüsseltresor Ihnen Zugriff auf die Berechtigung „Microsoft.KeyVault/vaults/deploy/action“ für den Schlüsseltresor gewähren. Weitere Informationen finden Sie unter [Verwenden von Azure Key Vault zum Übergeben eines sicheren Parameterwerts während der Bereitstellung](./resource-manager-keyvault-parameter.md).

Wählen Sie zum Ausführen des folgenden PowerShell-Skripts die Option **Testen Sie es.** aus, um Cloud Shell zu öffnen. Klicken Sie zum Einfügen des Skripts mit der rechten Maustaste auf den Shellbereich, und wählen Sie **Einfügen** aus.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue
```

Wichtige Informationen:

* Der Ressourcengruppenname ist der Projektname mit dem Zusatz **rg**. Um das [Bereinigen der in diesem Tutorial erstellten Ressourcen](#clean-up-resources) zu vereinfachen, verwenden Sie beim [Bereitstellen der nächsten Vorlage](#deploy-the-template) den gleichen Projektnamen und Ressourcengruppennamen.
* Der Standardname für das Geheimnis lautet **vmAdminPassword**. Er ist in der Vorlage hartcodiert.
* Damit die Vorlage das Geheimnis abrufen kann, müssen Sie eine Zugriffsrichtlinie namens **Zugriff auf Azure Resource Manager für Vorlagenbereitstellung aktivieren** für den Schlüsseltresor aktivieren. Diese Richtlinie ist in der Vorlage aktiviert. Weitere Informationen zu dieser Zugriffsrichtlinie finden Sie unter [Verwenden von Azure Key Vault zum Übergeben eines sicheren Parameterwerts während der Bereitstellung](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets).

Die Vorlage enthält einen Ausgabewert namens **keyVaultId**. Notieren Sie sich den Wert. Diese ID wird beim Bereitstellen des virtuellen Computers benötigt. Die Ressourcen-ID hat das folgende Format:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Beim Kopieren und Einfügen der ID wird diese möglicherweise auf mehrere Zeilen aufgeteilt. Sie müssen die Zeilen zusammenführen und die zusätzlichen Leerzeichen löschen.

Führen Sie zum Überprüfen der Bereitstellung den folgenden PowerShell-Befehl im gleichen Shellbereich aus, um das Geheimnis als Klartext abzurufen. Der Befehl funktioniert nur in der gleichen Shellsitzung, da er die Variable „$keyVaultName“ verwendet, die im vorherigen PowerShell-Skript definiert wurde.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Sie haben nun einen Schlüsseltresor und ein Geheimnis vorbereitet. In den folgenden Abschnitten wird gezeigt, wie Sie eine vorhandene Vorlage anpassen, um das Geheimnis während der Bereitstellung abzurufen.

## <a name="open-a-quickstart-template"></a>Öffnen einer Schnellstartvorlage

„Azure-Schnellstartvorlagen“ ist ein Repository für Resource Manager-Vorlagen. Statt eine Vorlage von Grund auf neu zu erstellen, können Sie eine Beispielvorlage verwenden und diese anpassen. Die in diesem Tutorial verwendete Vorlage heißt [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Bereitstellen eines einfachen virtuellen Windows-Computers).

1. Wählen Sie in Visual Studio Code **Datei**>**Datei öffnen** aus.
2. Fügen Sie in **Dateiname** die folgende URL ein:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Wählen Sie **Öffnen** aus, um die Datei zu öffnen. Hierbei handelt es sich um das gleiche Szenario, das auch im [Tutorial: Erstellen von Azure Resource Manager-Vorlagen mit abhängigen Ressourcen](./resource-manager-tutorial-create-templates-with-dependent-resources.md) verwendet wird.
4. Es gibt fünf Ressourcen, die von der Vorlage definiert werden:

   * `Microsoft.Storage/storageAccounts`. Informationen finden Sie in der [Vorlagenreferenz](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Informationen finden Sie in der [Vorlagenreferenz](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Informationen finden Sie in der [Vorlagenreferenz](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Informationen finden Sie in der [Vorlagenreferenz](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Informationen finden Sie in der [Vorlagenreferenz](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Bevor Sie die Vorlage anpassen, sollten Sie sich zunächst grundlegend damit vertraut machen.
5. Wählen Sie **Datei**>**Speichern unter** aus, um eine Kopie der Datei als **azuredeploy.json** auf dem lokalen Computer zu speichern.
6. Wiederholen Sie die Schritte 1 bis 4, um die folgende URL zu öffnen, und speichern Sie die Datei anschließend als **azuredeploy.parameters.json**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Bearbeiten der Parameterdatei

Die Vorlagendatei muss nicht geändert werden.

1. Öffnen Sie **azuredeploy.parameters.json** in Visual Studio Code, falls die Datei noch nicht geöffnet ist.
2. Aktualisieren Sie den Parameter **adminPassword** wie folgt:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    > [!IMPORTANT]
    > Ersetzen Sie den Wert der **ID** durch die Ressourcen-ID Ihres in der letzten Prozedur erstellten Schlüsseltresors.

    ![Integrieren von Key Vault und Resource Manager-Vorlage: VM-Bereitstellung – Parameterdatei](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Geben Sie Werte für Folgendes an:

    * **adminUsername**: Geben Sie das Administratorkonto des virtuellen Computers an.
    * **dnsLabelPrefix**: Geben Sie das Präfix für die DNS-Bezeichnung an.

    Ein Beispiel sehen Sie im vorherigen Screenshot.

4. Speichern Sie die Änderungen.

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Stellen Sie die Vorlage gemäß der Anleitung unter [Bereitstellen der Vorlage](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) bereit. Sie müssen sowohl **azuredeploy.json** als auch **azuredeploy.parameters.json** in Cloud Shell hochladen und anschließend die Vorlage mithilfe des folgenden PowerShell-Skripts bereitstellen:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

Verwenden Sie beim Bereitstellen der Vorlage die gleiche Ressourcengruppe wie für den Schlüsseltresor. Das erleichtert später die Bereinigung der Ressourcen. Sie müssen dann nicht zwei Ressourcengruppen löschen, sondern nur eine.

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

Testen Sie nach erfolgreicher Bereitstellung des virtuellen Computers die Anmeldung unter Verwendung des im Schlüsseltresor gespeicherten Kennworts.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Klicken Sie auf **Ressourcengruppen**/**Name Ihrer Ressourcengruppe>** /**simpleWinVM**.
3. Klicken Sie im oberen Bereich auf **Verbinden**.
4. Klicken Sie auf **RDP-Datei herunterladen**, und melden Sie sich gemäß den Anweisungen unter Verwendung des im Schlüsseltresor gespeicherten Kennworts bei dem virtuellen Computer an.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Azure-Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein Geheimnis aus Azure Key Vault abgerufen und im Rahmen Ihrer Vorlagenbereitstellung verwendet.  Informationen zum Erstellen verknüpfter Vorlagen finden Sie unter:

> [!div class="nextstepaction"]
> [Erstellen verknüpfter Vorlagen](./resource-manager-tutorial-create-linked-templates.md)
