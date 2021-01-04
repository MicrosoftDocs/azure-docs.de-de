---
title: Aktivieren der Protokollierung in Azure Key Vault
description: Hier erfahren Sie, wie Sie die Protokollierung in Azure Key Vault aktivieren. Dabei werden Informationen in einem von Ihnen angegebenen Speicherkonto in Azure gespeichert.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 5e0007f3b0dad8a68e9d81cebbe9fe24b5a7db3c
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285643"
---
# <a name="how-to-enable-key-vault-logging"></a>Aktivieren der Protokollierung in Key Vault

Nachdem Sie einen oder mehrere Schlüsseltresore erstellt haben, möchten Sie vermutlich überwachen, wie, wann und von wem auf die Schlüsseltresore zugegriffen wird. Ausführliche Informationen zum Feature finden Sie unter [Key Vault-Protokollierung](logging.md).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Vorhandenen Schlüsseltresor, der von Ihnen genutzt wird  
* Die Azure CLI oder Azure PowerShell.
* Ausreichend Speicherplatz unter Azure für Ihre Schlüsseltresor-Protokolle

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie die Version 2.0.4 oder höher der Azure CLI installieren. Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli). Geben Sie Folgendes ein, um sich über die Befehlszeilenschnittstelle bei Azure anzumelden:

```azurecli-interactive
az login
```

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie mindestens Version 1.0.0 des Azure PowerShell-Moduls verwenden. Geben Sie `$PSVersionTable.PSVersion` ein, um die Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

```powershell-interactive
Connect-AzAccount
```

## <a name="connect-to-your-key-vault-subscription"></a>Herstellen einer Verbindung zu Ihrem Key Vault-Abonnement

Der erste Schritt für das Einrichten der Schlüsselprotokollierung ist das Herstellen einer Verbindung zum Abonnement, das Ihren Schlüsseltresor enthält. Dies ist besonders dann wichtig, wenn Ihrem Konto mehrere Abonnements zugeordnet sind.

Mithilfe der Azure CLI können Sie alle Abonnements anzeigen, indem Sie den Befehl [az account list](/cli/azure/account?view=azure-cli-latest#az_account_list) verwenden. Stellen Sie dann mit dem Befehl [az account set](/cli/azure/account?view=azure-cli-latest#az_account_set) eine Verbindung zu einem Abonnement her:

```azurecli-interactive
az account list

az account set --subscription "<subscriptionID>"
```

In Azure PowerShell können Sie Ihre Abonnements zunächst auflisten, indem Sie das Cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-4.7.0) verwenden. Dann können Sie mithilfe des Cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-4.7.0) eine Verbindung zu einem Abonnement herstellen: 

```powershell-interactive
Get-AzSubscription

Set-AzContext -SubscriptionId "<subscriptionID>"
```

## <a name="create-a-storage-account-for-your-logs"></a>Erstellen eines Speicherkontos für Ihre Protokolle

Sie können zwar ein vorhandenes Speicherkonto für Ihre Protokolle verwenden, aber hier soll ein neues für Key Vault-Protokolle dediziertes Speicherkonto erstellt werden. 

Um die Verwaltung noch weiter zu vereinfachen, verwenden wir auch die gleiche Ressourcengruppe wie die Gruppe, die den Schlüsseltresor enthält. Im [Azure CLI-Schnellstart](quick-create-cli.md) und im [Azure PowerShell-Schnellstart](quick-create-powershell.md) wird diese Ressourcengruppe als **myResourceGroup** (meineRessourcengruppe) bezeichnet. Der Standort ist *eastus*. Ersetzen Sie diese Werte gegebenenfalls durch Ihre eigenen. 

Geben Sie außerdem einen Speicherkontonamen an. Speicherkontonamen müssen eindeutig und zwischen drei und 24 Zeichen lang sein und dürfen nur Zahlen und Kleinbuchstaben enthalten.  Zuletzt wird ein Speicherkonto mit der SKU „Standard_LRS“ erstellt.

Verwenden Sie in der Azure CLI den Befehl [az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create).

```azurecli-interactive
az storage account create --name "<your-unique-storage-account-name>" -g "myResourceGroup" --sku "Standard_LRS"
```

Verwenden Sie bei Verwendung von Azure PowerShell das Cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount?view=azps-4.7.0). Sie müssen den Speicherort angeben, der dem der Ressourcengruppe entspricht.

```powershell
 New-AzStorageAccount -ResourceGroupName myResourceGroup -Name "<your-unique-storage-account-name>" -Type "Standard_LRS" -Location "eastus"
```

Notieren Sie sich in jedem Fall den id-Wert des Speicherkontos. Beim Azure CLI-Vorgang wird der id-Wert in der Ausgabe zurückgegeben. Wenn Sie Azure PowerShell verwenden, erhalten Sie den id-Wert mithilfe des Cmdlet [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-4.7.0). Weisen Sie die Ausgabe dann der Variable $sa zu. Das Speicherkonto wird dann mit $sa.id angezeigt. (Die $sa.Context-Eigenschaft wird später in diesem Artikel ebenfalls noch verwendet.)

```powershell-interactive
$sa = Get-AzStorageAccount -Name "<your-unique-storage-account-name>" -ResourceGroup "myResourceGroup"
$sa.id
```

Der id-Wert des Speicherkontos weist das folgende Format auf: /Abonnements/<Ihre-Abonnement-ID>/Ressourcengruppen/meineRessourcengruppe/Anbieter/Microsoft.Storage/Speicherkonten/<Ihr-eindeutiger-Speicherkontoname>.

> [!NOTE]
> Wenn Sie ein vorhandenes Speicherkonto verwenden möchten, muss dafür dasselbe Abonnement wie für den Schlüsseltresor verwendet werden. Außerdem muss das Azure Resource Manager-Bereitstellungsmodell genutzt werden, nicht das klassische Bereitstellungsmodell.

## <a name="obtain-your-key-vault-resource-id"></a>Abrufen der Ressourcen-ID des Schlüsseltresors

Im [CLI-Schnellstart](quick-create-cli.md) bzw. [PowerShell-Schnellstart](quick-create-powershell.md) haben Sie einen Schlüssel mit eindeutigem Namen erstellt.  Verwenden Sie diesen Namen für die Schritte unten noch mal.  Wenn Sie sich an den Namen Ihres Schlüsseltresors nicht mehr erinnern, können Sie den Azure CLI-Befehl [az keyvault list](/cli/azure/keyvault?view=azure-cli-latest#az_keyvault_list) oder das Azure PowerShell-Cmdlet [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault?view=azps-4.7.0) verwenden, um den Namen abzurufen.

Verwenden Sie den Namen Ihres Schlüsseltresors, um nach seiner Ressourcen-ID zu suchen.  Verwenden Sie bei Verwendung der Azure CLI den Befehl [az keyvault show](/cli/azure/keyvault?view=azure-cli-latest#az_keyvault_show).

```azurecli-interactive
az keyvault show --name "<your-unique-keyvault-name>"
```

In Azure PowerShell verwenden Sie das Cmdlet [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault?view=azps-4.7.0).

```powershell-interactive
Get-AzKeyVault -VaultName "<your-unique-keyvault-name>"
```

Die Ressourcen-ID für Ihren Schlüsseltresor weist das folgende Format auf: /Abonnements/<Ihre-Abonnement-ID>/Ressourcengruppen/meineRessourcengruppe/Anbieter/Microsoft.KeyVault/Tresore/<Ihr-eindeutiger-Schlüsseltresorname>. Speichern Sie ihn für den nächsten Schritt.

## <a name="enable-logging-using-azure-powershell"></a>Aktivieren der Protokollierung mithilfe von Azure PowerShell

Für die Aktivierung der Protokollierung in Azure Key Vault verwenden Sie den Azure CLI-Befehl [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest) bzw. das Cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) zusammen mit der Speicherkonto-ID und der Ressourcen-ID des Schlüsseltresors.

```azurecli-interactive
az monitor diagnostic-settings create --storage-account "<storage-account-id>" --resource "<key-vault-resource-id>" --name "Key vault logs" --logs '[{"category": "AuditEvent","enabled": true}]' --metrics '[{"category": "AllMetrics","enabled": true}]'
```

In Azure PowerShell wird das Cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) verwendet. Das **-Enabled** -Flag wird dabei auf **$true** festgelegt, und die Kategorie ist auf `AuditEvent` festgelegt. Dabei handelt es sich um die einzige Kategorie für die Protokollierung in Azure Key Vault:

```powershell-interactive
Set-AzDiagnosticSetting -ResourceId "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category "AuditEvent"
```

Optional können Sie eine Aufbewahrungsrichtlinie für Ihre Protokolle festlegen, mit der ältere Protokolle nach einer angegeben Dauer automatisch gelöscht werden. Sie könnten beispielsweise eine Aufbewahrungsrichtlinie so festlegen, dass alle Protokolle, die älter als 90 Tage sind, automatisch gelöscht werden.

<!-- With the Azure CLI, use the [az monitor diagnostic-settings update](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az_monitor_diagnostic_settings_update) command. 

```azurecli-interactive
az monitor diagnostic-settings update 
```
-->

In Azure PowerShell verwenden Sie das Cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0). 

```powershell-interactive
Set-AzDiagnosticSetting "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Protokollierte Inhalte

* Alle authentifizierten REST-API-Anforderungen, z. B. auch Anforderungen, die aufgrund von Zugriffsberechtigungen, Systemfehlern oder fehlerhaften Anforderungen nicht erfolgreich sind.
* Vorgänge im Schlüsseltresor selbst, z. B. Erstellung, Löschung und Festlegung von Schlüsseltresor-Zugriffsrichtlinien und Aktualisierung von Schlüsseltresor-Attributen wie Tags.
* Vorgänge mit Schlüsseln und Geheimnissen im Schlüsseltresor, einschließlich:
  * Erstellen, Ändern oder Löschen dieser Schlüssel oder Geheimnisse.
  * Signieren, Verifizieren, Verschlüsseln, Entschlüsseln, Ver- und Entpacken von Schlüsseln, Erhalten von Geheimnissen und Auflisten von Schlüsseln und Geheimnissen (und deren Versionen).
* Bei nicht authentifizierten Anforderungen wird eine 401-Antwort zurückgegeben. Beispiele sind Anforderungen ohne Bearertoken, falsch formatierte oder abgelaufene Anforderungen oder Anforderungen, deren Token ungültig ist.  
* Event Grid-Benachrichtigungsereignisse für „Läuft demnächst ab“, „Abgelaufen“ und „Tresorzugriffsrichtlinie geändert“ (neues Versionsereignis wird nicht protokolliert). Ereignisse werden unabhängig davon protokolliert, ob im Schlüsseltresor ein Ereignisabonnement erstellt wurde. Weitere Informationen finden Sie unter [Event Grid-Ereignisschema für Schlüsseltresor](../../event-grid/event-schema-key-vault.md).

## <a name="access-your-logs"></a>Zugreifen auf Ihre Protokolle

Key Vault-Protokolle werden im Container „insights-logs-auditevent“ im von Ihnen angegebenen Speicherkonto gespeichert. Zur Anzeige der Protokolle müssen Sie Blobs herunterladen.

Rufen Sie zunächst alle Blobs im Container ab.  Verwenden Sie in der Azure CLI den Befehl [az storage blob list](/cli/azure/storage/blob?view=azure-cli-latest#az_storage_blob_list).

```azurecli-interactive
az storage blob list --account-name "<your-unique-storage-account-name>" --container-name "insights-logs-auditevent"
```

In Azure PowerShell können Sie das Cmdlet [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob?view=azps-4.7.0) nutzen, um alle Blobs im Container aufzulisten. Geben Sie dazu Folgendes ein:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Wie an der Ausgabe entweder des Azure CLI-Befehls oder des Azure PowerShell-Cmdlet ersichtlich wird, weisen die Blobnamen das folgende Format auf: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`. Für die Werte für Datum und Uhrzeit wird UTC verwendet.

Da dasselbe Speicherkonto zum Erfassen von Protokollen für mehrere Ressourcen verwendet werden kann, ist die vollständige Ressourcen-ID im Blobnamen sehr hilfreich, um nur auf die benötigten Blobs zuzugreifen bzw. diese herunterzuladen. Zuerst wird aber beschrieben, wie Sie alle Blobs herunterladen.

Wenn Sie die Azure CLI verwenden, nutzen Sie den Befehl [az storage blob download](/cli/azure/storage/blob?view=azure-cli-latest#az_storage_blob_download). Übergeben Sie dabei die Blobnamen sowie den Pfad zur Datei, in der die Ergebnisse gespeichert werden sollen.

```azurecli-interactive
az storage blob download --container-name "insights-logs-auditevent" --file <path-to-file> --name "<blob-name>" --account-name "<your-unique-storage-account-name>"
```

In Azure PowerShell verwenden Sie das Cmdlet [Gt-AzStorageBlobs](/powershell/module/az.storage/get-azstorageblob?view=azps-4.7.0), um eine Liste der Blobs abzurufen, und übergeben diese über eine Pipeline an das Cmdlet [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent?view=azps-4.7.0), um die Protokolle in den von Ihnen ausgewählten Pfad herunterzuladen.

```powershell-interactive
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context | Get-AzStorageBlobContent -Destination "<path-to-file>"
```

Beim Ausführen dieses zweiten Cmdlet in PowerShell wird mit dem Trennzeichen **/** in den Blobnamen eine vollständige Ordnerstruktur unter dem Zielordner erstellt. Sie verwenden diese Struktur, um die Blobs herunterzuladen und als Dateien zu speichern.

Verwenden Sie Platzhalter, um Blobs selektiv herunterzuladen. Beispiel:

* Bei Verwendung mehrerer Schlüsseltresore und einem Download von Protokollen nur für einen Schlüsseltresor mit dem Namen CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Wenn Sie über mehrere Ressourcengruppen verfügen und nur Protokolle für eine Ressourcengruppe herunterladen möchten, verwenden Sie `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Wenn Sie alle Protokolle für den Monat Januar 2019 herunterladen möchten, verwenden Sie `-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Sie können sich nun ansehen, was in den Protokollen enthalten ist. Aber bevor wir damit fortfahren, sollten Sie noch zwei weitere Befehle kennen:

Informationen zum Lesen der Protokolle finden Sie unter [Key Vault-Protokollierung: Interpretieren Ihrer Key Vault-Protokolle](logging.md#interpret-your-key-vault-logs).

## <a name="use-azure-monitor-logs"></a>Verwenden von Azure Monitor-Protokollen

Sie können die Key Vault-Lösung in Azure Monitor verwenden, um `AuditEvent`-Protokolle von Key Vault zu überprüfen. In Azure Monitor-Protokollen verwenden Sie Protokollabfragen, um Daten zu analysieren und die benötigten Informationen zu erhalten.

Weitere Informationen, z. B. zur Einrichtung, finden Sie im Artikel zu [Azure Key Vault in Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Nächste Schritte

- Konzeptionelle Informationen einschließlich dazu, wie Protokolle in Key Vault interpretiert werden, finden Sie unter [Key Vault-Protokollierung](logging.md).
- Ein Tutorial zur Verwendung von Azure Key Vault in einer .NET-Webanwendung finden Sie unter [Verwenden von Azure Key Vault aus einer Webanwendung](tutorial-net-create-vault-azure-web-app.md).
- Eine Referenz zur Programmierung finden Sie im [Entwicklerhandbuch für den Azure-Schlüsseltresor](developers-guide.md).