---
title: Verwenden von Bereitstellungsskripts für Vorlagen | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie Bereitstellungsskripts in Azure Resource Manager-Vorlagen (ARM-Vorlagen) verwenden.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 08/25/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: cc19222cf1e610c6c65d7c721a54f9949bed70ae
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931434"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate-preview"></a>Tutorial: Verwenden von Bereitstellungsskripts zum Erstellen eines selbstsignierten Zertifikats (Vorschau)

Hier wird beschrieben, wie Sie Bereitstellungsskripts in Azure Resource Manager-Vorlagen (ARM-Vorlagen) verwenden. Bereitstellungsskripts können genutzt werden, um benutzerdefinierte Schritte auszuführen, die für ARM-Vorlagen nicht möglich sind. Ein Beispiel hierfür ist die Erstellung eines selbstsignierten Zertifikats.  In diesem Tutorial erstellen Sie eine Vorlage für die Bereitstellung eines Azure-Schlüsseltresors. Anschließend verwenden Sie die `Microsoft.Resources/deploymentScripts`-Ressource in derselben Vorlage, um ein Zertifikat zu erstellen, und fügen das Zertifikat dann dem Schlüsseltresor hinzu. Weitere Informationen zum Bereitstellungsskript finden Sie unter [Verwenden von Bereitstellungsskripts in ARM-Vorlagen](./deployment-script-template.md).

> [!IMPORTANT]
> Zwei Bereitstellungsskriptressourcen – ein Speicherkonto und eine Containerinstanz – werden für die Skriptausführung und Problembehandlung in derselben Ressourcengruppe erstellt. Diese Ressourcen werden vom Skriptdienst normalerweise gelöscht, bevor die Skriptausführung beendet wird. Die Ressourcen werden Ihnen in Rechnung gestellt, bis sie gelöscht werden. Weitere Informationen finden Sie unter [Bereinigen von Bereitstellungsskriptressourcen](./deployment-script-template.md#clean-up-deployment-script-resources).

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Öffnen einer Schnellstartvorlage
> * Bearbeiten der Vorlage
> * Bereitstellen der Vorlage
> * Debuggen des fehlerhaften Skripts
> * Bereinigen von Ressourcen

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:

* **[Visual Studio Code](https://code.visualstudio.com/) mit der Erweiterung „Azure Resource Manager-Tools“.** Weitere Informationen finden Sie unter [Schnellstart: Erstellen von ARM-Vorlagen mit Visual Studio Code](./quickstart-create-templates-use-visual-studio-code.md).

* **Vom Benutzer zugewiesene verwaltete Identität mit der Rolle „Mitwirkender“ auf Abonnementebene.** Diese Identität wird zum Ausführen von Bereitstellungsskripts verwendet. Informationen zur Erstellung finden Sie unter [Benutzerseitig zugewiesene verwaltete Identität](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). Sie benötigen die Identitäts-ID beim Bereitstellen der Vorlage. Das Format der Identität lautet:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Verwenden Sie das folgende CLI-Skript, um die ID abzurufen, indem Sie den Namen der Ressourcengruppe und den Identitätsnamen angeben.

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  az identity list -g $resourceGroupName
  ```

## <a name="open-a-quickstart-template"></a>Öffnen einer Schnellstartvorlage

Anstatt eine Vorlage von Grund auf neu zu erstellen, können Sie auch eine Vorlage aus [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/) öffnen. Azure-Schnellstartvorlagen ist ein Repository für ARM-Vorlagen.

Die in dieser Schnellstartanleitung verwendete Vorlage ist [Erstellen eines Azure-Schlüsseltresors und eines Geheimnisses](https://azure.microsoft.com/resources/templates/101-key-vault-create/). Mit der Vorlage wird ein Schlüsseltresor erstellt, und anschließend wird dafür ein Geheimnis hinzugefügt.

1. Wählen Sie in Visual Studio Code **Datei**>**Datei öffnen** aus.
2. Fügen Sie in **Dateiname** die folgende URL ein:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. Wählen Sie **Öffnen** aus, um die Datei zu öffnen.
4. Wählen Sie **Datei**>**Speichern unter** aus, um die Datei als **azuredeploy.json** auf dem lokalen Computer zu speichern.

## <a name="edit-the-template"></a>Bearbeiten der Vorlage

Nehmen Sie die folgenden Änderungen an der Vorlage vor:

### <a name="clean-up-the-template-optional"></a>Bereinigen der Vorlage (optional)

Mit der ursprünglichen Vorlage wird dem Schlüsseltresor ein Geheimnis hinzugefügt.  Entfernen Sie die folgende Ressource, um das Tutorial zu vereinfachen:

* **Microsoft.KeyVault/vaults/secrets**

Entfernen Sie die beiden folgenden Parameterdefinitionen:

* **secretName**
* **secretValue**

Wenn Sie diese Definitionen nicht entfernen möchten, müssen Sie die Parameterwerte während der Bereitstellung angeben.

### <a name="configure-the-key-vault-access-policies"></a>Konfigurieren der Zugriffsrichtlinien für den Schlüsseltresor

Vom Bereitstellungsskript wird dem Schlüsseltresor ein Zertifikat hinzugefügt. Konfigurieren Sie die Zugriffsrichtlinien für den Schlüsseltresor, um die Berechtigung für die verwaltete Identität zu erteilen:

1. Fügen Sie einen Parameter hinzu, um die ID der verwalteten Identität abzurufen:

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > Die Erweiterung für Resource Manager-Vorlagen von Visual Studio Code verfügt noch nicht über die Option zum Formatieren von Bereitstellungsskripts. Verwenden Sie nicht [UMSCHALT]+[ALT]+F, um die deploymentScripts-Ressourcen zu formatieren (wie im folgenden Fall).

1. Fügen Sie einen Parameter zum Konfigurieren der Zugriffsrichtlinien für den Schlüsseltresor hinzu, damit dem Schlüsseltresor von der verwalteten Identität Zertifikate hinzugefügt werden können.

    ```json
    "certificatesPermissions": {
      "type": "array",
      "defaultValue": [
        "get",
        "list",
        "update",
        "create"
      ],
      "metadata": {
      "description": "Specifies the permissions to certificates in the vault. Valid values are: all, get, list, update, create, import, delete, recover, backup, restore, manage contacts, manage certificate authorities, get certificate authorities, list certificate authorities, set certificate authorities, delete certificate authorities."
      }
    }
    ```

1. Aktualisieren Sie die vorhandenen Zugriffsrichtlinien für den Schlüsseltresor wie folgt:

    ```json
    "accessPolicies": [
      {
        "objectId": "[parameters('objectId')]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      },
      {
        "objectId": "[reference(parameters('identityId'), '2018-11-30').principalId]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      }
    ],
    ```

    Es sind zwei Richtlinien definiert: eine für den angemeldeten Benutzer und die andere für die verwaltete Identität.  Der angemeldete Benutzer benötigt nur die Berechtigung zum Auflisten (*list*) für die Überprüfung der Bereitstellung.  Der Einfachheit halber wird in diesem Tutorial sowohl der verwalteten Identität als auch den angemeldeten Benutzern dasselbe Zertifikat zugewiesen.

### <a name="add-the-deployment-script"></a>Hinzufügen des Bereitstellungsskripts

1. Fügen Sie drei Parameter hinzu, die vom Bereitstellungsskript verwendet werden.

    ```json
    "certificateName": {
      "type": "string",
      "defaultValue": "DeploymentScripts2019"
    },
    "subjectName": {
      "type": "string",
      "defaultValue": "CN=contoso.com"
    },
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    }
    ```

1. Fügen Sie eine Ressource vom Typ „deploymentScripts“ hinzu:

    > [!NOTE]
    > Da die Inlinebereitstellungsskripts in doppelte Anführungszeichen eingeschlossen sind, müssen Zeichenfolgen innerhalb der Bereitstellungsskripts stattdessen in einfache Anführungszeichen eingeschlossen werden. Das Escapezeichen für PowerShell ist **&#92;** .

    ```json
    {
      "type": "Microsoft.Resources/deploymentScripts",
      "apiVersion": "2019-10-01-preview",
      "name": "createAddCertificate",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[parameters('identityId')]": {
          }
        }
      },
      "kind": "AzurePowerShell",
      "properties": {
        "forceUpdateTag": "[parameters('utcValue')]",
        "azPowerShellVersion": "3.0",
        "timeout": "PT30M",
        "arguments": "[format(' -vaultName {0} -certificateName {1} -subjectName {2}', parameters('keyVaultName'), parameters('certificateName'), parameters('subjectName'))]", // can pass an argument string, double quotes must be escaped
        "scriptContent": "
          param(
            [string] [Parameter(Mandatory=$true)] $vaultName,
            [string] [Parameter(Mandatory=$true)] $certificateName,
            [string] [Parameter(Mandatory=$true)] $subjectName
          )

          $ErrorActionPreference = 'Stop'
          $DeploymentScriptOutputs = @{}

          $existingCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

          if ($existingCert -and $existingCert.Certificate.Subject -eq $subjectName) {

            Write-Host 'Certificate $certificateName in vault $vaultName is already present.'

            $DeploymentScriptOutputs['certThumbprint'] = $existingCert.Thumbprint
            $existingCert | Out-String
          }
          else {
            $policy = New-AzKeyVaultCertificatePolicy -SubjectName $subjectName -IssuerName Self -ValidityInMonths 12 -Verbose

            # private key is added as a secret that can be retrieved in the Resource Manager template
            Add-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName -CertificatePolicy $policy -Verbose

            $newCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

            # it takes a few seconds for KeyVault to finish
            $tries = 0
            do {
              Write-Host 'Waiting for certificate creation completion...'
              Start-Sleep -Seconds 10
              $operation = Get-AzKeyVaultCertificateOperation -VaultName $vaultName -Name $certificateName
              $tries++

              if ($operation.Status -eq 'failed')
              {
                throw 'Creating certificate $certificateName in vault $vaultName failed with error $($operation.ErrorMessage)'
              }

              if ($tries -gt 120)
              {
                throw 'Timed out waiting for creation of certificate $certificateName in vault $vaultName'
              }
            } while ($operation.Status -ne 'completed')

            $DeploymentScriptOutputs['certThumbprint'] = $newCert.Thumbprint
            $newCert | Out-String
          }
        ",
        "cleanupPreference": "OnSuccess",
        "retentionInterval": "P1D"
      }
    }
    ```

    Die `deploymentScripts`-Ressource ist von der Schlüsseltresorressource und der Ressource für die Rollenzuweisung abhängig.  Sie verfügt über die folgenden Eigenschaften:

    * **identity**: Im Bereitstellungsskript wird eine vom Benutzer zugewiesene verwaltete Identität genutzt, um die Skripts auszuführen.
    * **kind**: Geben Sie den Typ des Skripts an. Derzeit wird nur das PowerShell-Skript unterstützt.
    * **forceUpdateTag**: Ermitteln Sie, ob das Bereitstellungsskript auch dann ausgeführt werden sollte, wenn sich die Skriptquelle nicht geändert hat. Kann der aktuelle Zeitstempel oder eine GUID sein. Weitere Informationen finden Sie unter [Mehrmaliges Ausführen des Skripts](./deployment-script-template.md#run-script-more-than-once).
    * **azPowerShellVersion**: Gibt die zu verwendende Version des Azure PowerShell-Moduls an. Derzeit werden für das Bereitstellungsskript die Versionen 2.7.0, 2.8.0 und 3.0.0 unterstützt.
    * **timeout**: Geben Sie die maximal zulässige Ausführungsdauer für das Skript im [ISO 8601-Format](https://en.wikipedia.org/wiki/ISO_8601) an. Der Standardwert ist **P1D**.
    * **arguments**: Geben Sie die Parameterwerte an. Die Werte werden durch Leerzeichen voneinander getrennt.
    * **scriptContent**: Geben Sie den Skriptinhalt an. Verwenden Sie stattdessen **primaryScriptURI**, wenn Sie ein externes Skript ausführen möchten. Weitere Informationen finden Sie unter [Verwenden externer Skripts](./deployment-script-template.md#use-external-scripts).
        Das Deklarieren von **$DeploymentScriptOutputs** ist nur erforderlich, wenn das Skript auf einem lokalen Computer getestet wird. Durch das Deklarieren der Variablen kann das Skript auf einem lokalen Computer und einer deploymentScript-Ressource ausgeführt werden, ohne dass Änderungen vorgenommen werden müssen. Der Wert, der „$DeploymentScriptOutputs“ zugewiesen ist, ist als Ausgabe in den Bereitstellungen verfügbar. Weitere Informationen finden Sie unter [Arbeiten mit Ausgaben von PowerShell-Skripts](./deployment-script-template.md#work-with-outputs-from-powershell-script) bzw. [Arbeiten mit Ausgaben von CLI-Skripts](./deployment-script-template.md#work-with-outputs-from-cli-script).
    * **cleanupPreference**: Geben Sie an, wann die Bereitstellungsskriptressourcen gelöscht werden sollen.  Der Standardwert ist **Immer**. Dies bedeutet, dass die Bereitstellungsskriptressourcen unabhängig vom Beendigungszustand (Erfolg, Fehler, Abbruch) gelöscht werden. In diesem Tutorial wird **OnSuccess** verwendet, damit Sie die Möglichkeit haben, die Ergebnisse der Skriptausführung anzuzeigen.
    * **retentionInterval**: Geben Sie das Intervall an, das vom Dienst für die Aufbewahrung der Skriptressourcen verwendet wird, nachdem ein Beendigungszustand erreicht wurde. Ressourcen werden gelöscht, wenn dieser Zeitraum abgelaufen ist. Die Dauer basiert auf dem ISO 8601-Muster. In diesem Tutorial wird „P1D“ verwendet. Dies steht für einen Tag.  Diese Eigenschaft wird verwendet, wenn **cleanupPreference** auf **OnExpiration** festgelegt ist. Diese Eigenschaft ist derzeit nicht aktiviert.

    Für das Bereitstellungsskript sind drei Parameter erforderlich: Schlüsseltresorname, Zertifikatname und Antragstellername.  Es wird ein Zertifikat erstellt, und anschließend wird das Zertifikat dem Schlüsseltresor hinzugefügt.

    **$DeploymentScriptOutputs** wird zum Speichern des Ausgabewerts verwendet.  Weitere Informationen finden Sie unter [Arbeiten mit Ausgaben von PowerShell-Skripts](./deployment-script-template.md#work-with-outputs-from-powershell-script) bzw. [Arbeiten mit Ausgaben von CLI-Skripts](./deployment-script-template.md#work-with-outputs-from-cli-script).

    Die fertige Vorlage finden Sie [hier](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json).

1. Fügen Sie zum Veranschaulichen des Debugprozesses einen Fehler in den Code ein, indem Sie dem Bereitstellungsskript die folgende Zeile hinzufügen:

    ```powershell
    Write-Output1 $keyVaultName
    ```

    Der richtige Befehl lautet nicht **Write-Output1**, sondern **Write-Output**.

1. Wählen Sie **Datei**>**Speichern** aus, um die Datei zu speichern.

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Melden Sie sich bei [Azure Cloud Shell](https://shell.azure.com) an.

1. Wählen Sie Ihre bevorzugte Umgebung aus, indem Sie links oben **PowerShell** oder **Bash** (für die CLI) auswählen.  Bei einem Wechsel ist ein Neustart der Shell erforderlich.

    ![Azure-Portal, Cloud Shell, Datei hochladen](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Wählen Sie **Dateien hochladen/herunterladen** und dann **Hochladen** aus. Betrachten Sie hierzu den vorherigen Screenshot.  Wählen Sie die Datei aus, die Sie im vorherigen Abschnitt gespeichert haben. Nach dem Hochladen der Datei können Sie den Befehl **ls** und den Befehl **cat** verwenden, um zu überprüfen, ob die Datei hochgeladen wurde.

1. Führen Sie das folgende PowerShell-Skript aus, um die Vorlage bereitzustellen.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
    $identityId = Read-Host -Prompt "Enter the user-assigned managed identity ID"

    $adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
    $resourceGroupName = "${projectName}rg"
    $keyVaultName = "${projectName}kv"

    New-AzResourceGroup -Name $resourceGroupName -Location $location

    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -identityId $identityId -keyVaultName $keyVaultName -objectId $adUserId

    Write-Host "Press [ENTER] to continue ..."
    ```

    Vom Bereitstellungsskript-Dienst müssen zusätzliche Bereitstellungsskriptressourcen für die Skriptausführung erstellt werden. Die Vorbereitung und der Bereinigungsprozess können zusammen bis zu eine Minute in Anspruch nehmen – zusätzlich zur Ausführungsdauer des eigentlichen Skripts.

    Die Bereitstellung ist nicht erfolgreich, weil im Skript der ungültige Befehl **Write-Output1** verwendet wird. Sie erhalten die folgende Fehlermeldung:

    ```error
    The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
    program.\nCheck the spelling of the name, or if a path was included, verify that the path is correct and try again.\n
    ```

    Das Ergebnis der Ausführung des Bereitstellungsskripts wird für die Problembehandlung in den Bereitstellungsskriptressourcen gespeichert.

## <a name="debug-the-failed-script"></a>Debuggen des fehlerhaften Skripts

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Öffnen Sie die Ressourcengruppe. Der Name setzt sich aus dem Projektnamen mit dem Zusatz **rg** zusammen. In der Ressourcengruppe werden zwei zusätzliche Ressourcen angezeigt. Diese Ressourcen werden als *Bereitstellungsskriptressourcen* bezeichnet.

    ![Resource Manager-Vorlage: Bereitstellungsskriptressourcen](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    Beide Dateien verfügen über das Suffix **azscripts**. Hierbei handelt es sich um ein Speicherkonto und eine Containerinstanz.

    Wählen Sie die Option **Ausgeblendete Typen anzeigen** aus, um die deploymentScripts-Ressource aufzulisten.

1. Wählen Sie das Speicherkonto mit dem Suffix **azscripts** aus.
1. Wählen Sie die Kachel **Dateifreigaben** aus. Der Ordner **azscripts** wird angezeigt.  Der Ordner enthält die Ausführungsdateien für die Bereitstellungsskripts.
1. Wählen Sie **azscripts** aus. Die beiden Ordner **azscriptinput** und **azscriptoutput** werden angezeigt.  Der Eingabeordner enthält eine PowerShell-Skriptdatei des Systems und die Bereitstellungsskriptdateien der Benutzer. Der Ausgabeordner enthält die Datei **executionresult.json** und die Skriptausgabedatei. Sie können die Fehlermeldung in **executionresult.json** anzeigen. Die Ausgabedatei ist nicht vorhanden, weil bei der Ausführung ein Fehler aufgetreten ist.

Entfernen Sie die Zeile **Write-Output1**, und stellen Sie die Vorlage erneut bereit.

Nachdem die zweite Bereitstellung erfolgreich ausgeführt wurde, müssen die Bereitstellungsskriptressourcen vom Skriptdienst entfernt werden, weil die **cleanupPreference**-Eigenschaft auf **OnSuccess** festgelegt ist.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Azure-Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen.

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppe** aus.
2. Geben Sie den Namen der Ressourcengruppe in das Feld **Nach Name filtern** ein.
3. Klicken Sie auf den Namen der Ressourcengruppe.  Es werden insgesamt sechs Ressourcen in der Ressourcengruppe angezeigt.
4. Wählen Sie **Ressourcengruppe löschen** aus dem Menü ganz oben aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde beschrieben, wie Sie in ARM-Vorlagen Bereitstellungsskripts verwenden. Informationen zum Bereitstellen von Azure-Ressourcen auf der Grundlage von Bedingungen finden Sie hier:

> [!div class="nextstepaction"]
> [Verwenden von Bedingungen](./template-tutorial-use-conditions.md)
