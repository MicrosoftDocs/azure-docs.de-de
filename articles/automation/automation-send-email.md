---
title: Senden einer E-Mail aus einem Azure Automation-Runbook
description: Dieser Artikel beschreibt, wie Sie eine E-Mail aus einem Runbook heraus versenden können.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: conceptual
ms.openlocfilehash: a81ae680a5f04eca0a6cc01ee24b474cc5daabea
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97005204"
---
# <a name="send-an-email-from-a-runbook"></a>Senden einer E-Mail aus einem Runbook

Sie können unter Verwendung von PowerShell und [SendGrid](https://sendgrid.com/solutions) eine E-Mail aus einem Runbook senden. 

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oder sich für ein [kostenloses Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Ein SendGrid-Konto](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account).
* [Automation-Konto](./index.yml) mit **Az**-Modulen.
* [Ausführendes Konto](./manage-runas-account.md) zum Speichern und Ausführen des Runbooks.

## <a name="create-an-azure-key-vault"></a>Erstellen einer Azure Key Vault-Instanz

Sie können eine Azure Key Vault-Instanz mit dem folgenden PowerShell-Skript erstellen. Ersetzen Sie die Variablenwerte durch die Werte für Ihre Umgebung. Verwenden Sie die eingebettete Azure Cloud Shell-Instanz, indem Sie die Schaltfläche **Ausprobieren** in der oberen rechten Ecke des Codeblocks nutzen. Sie können den Code auch lokal kopieren und ausführen, wenn die [Az-Module](/powershell/azure/install-az-ps) auf dem lokalen Computer installiert sind.

> [!NOTE]
> Führen Sie zum Abrufen Ihres API-Schlüssels die Schritte unter [Senden von E-Mails in Azure mit SendGrid](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key) aus.

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Weitere Möglichkeiten zum Erstellen einer Azure Key Vault-Instanz und zum Speichern eines Geheimnisses finden Sie unter [Schnellstarts zu Key Vault](../key-vault/index.yml).

## <a name="import-required-modules-into-your-automation-account"></a>Importieren erforderlicher Module in Ihr Automation-Konto

Um Azure Key Vault innerhalb eines Runbooks zu verwenden, müssen Sie die folgenden Module in Ihr Automation-Konto importieren:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Anweisungen finden Sie unter [Importieren von Az-Modulen](shared-resources/modules.md#import-az-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Erstellen des Runbooks zum Senden einer E-Mail

Wenn Sie einen Schlüsseltresor erstellt und Ihren `SendGrid`-API-Schlüssel gespeichert haben, können Sie das Runbook erstellen, das den API-Schlüssel abruft und eine E-Mail sendet. Lassen Sie uns ein Runbook verwenden, das `AzureRunAsConnection` als [ausführendes Konto](./manage-runas-account.md) für die Authentifizierung bei Azure verwendet, um das Geheimnis aus Azure Key Vault abzurufen. Wir nennen das Runbook **Send-GridMailMessage**. Sie können das für Beispielzwecke verwendete PowerShell-Skript ändern und für verschiedene Szenarien wiederverwenden.

1. Wechseln Sie zu Ihrem Azure Automation-Konto.
2. Wählen Sie unter **Prozessautomatisierung** die Option **Runbooks** aus.
3. Wählen Sie oben in der Liste mit den Runbooks die Option **+ Runbook erstellen** aus.
4. Geben Sie auf der Seite „Runbook hinzufügen“ den Namen **Send-GridMailMessage** für das Runbook ein. Wählen Sie als Runbooktyp **PowerShell** aus. Wählen Sie anschließend **Erstellen**.
   ![Erstellen eines Runbooks](./media/automation-send-email/automation-send-email-runbook.png)
5. Das Runbook wird erstellt, und die Seite „PowerShell-Runbook bearbeiten“ wird geöffnet.
   ![Bearbeiten des Runbooks](./media/automation-send-email/automation-send-email-edit.png)
6. Kopieren Sie das folgende PowerShell-Beispiel auf die Seite „Bearbeiten“. Stellen Sie sicher, dass der `VaultName` den Namen angibt, den Sie für Ihren Schlüsseltresor ausgewählt haben.

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValue
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. Wählen Sie die Option **Veröffentlichen**, um das Runbook zu speichern und zu veröffentlichen.

Führen Sie die Schritte unter [Verwalten von Runbooks in Azure Automation](manage-runbooks.md#test-a-runbook) oder [Starten eines Runbooks in Azure Automation](start-runbooks.md) aus, um zu überprüfen, ob das Runbook ausgeführt wird.

Wird die Test-E-Mail zuerst nicht angezeigt, überprüfen Sie den **Junk-** und **Spam**-Ordner.

## <a name="clean-up-resources-after-the-email-operation"></a>Bereinigen von Ressourcen nach dem E-Mail-Vorgang

1. Wenn das Runbook nicht mehr benötigt wird, wählen Sie es in der Runbookliste aus, und klicken Sie auf **Löschen**.

2. Löschen Sie den Schlüsseltresor mithilfe des Cmdlets [Remove-AzKeyVault](/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0).

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Senden von Runbookauftragsdaten an Ihren Log Analytics-Arbeitsbereich finden Sie unter [Weiterleiten von Azure Automation-Auftragsdaten an Azure Monitor-Protokolle](automation-manage-send-joblogs-log-analytics.md).
* Informationen zur Überwachung von Metriken und Protokollen auf Basisebene finden Sie unter [Verwenden einer Warnung zum Auslösen eines Azure Automation-Runbooks](automation-create-alert-triggered-runbook.md).
* Informationen zur Behebung von Problemen, die während der Runbookausführung auftreten, finden Sie unter [Beheben von Runbookproblemen](./troubleshoot/runbooks.md).
