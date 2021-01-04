---
title: Erstellen und Verwalten von Verschlüsselungsbereichen (Vorschauversion)
description: Erfahren Sie, wie Sie einen Verschlüsselungsbereich erstellen, um Blobdaten auf Container- oder Blobebene zu isolieren.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: e77b58f7741af42f00b2a1831157405b12fa24ff
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017405"
---
# <a name="create-and-manage-encryption-scopes-preview"></a>Erstellen und Verwalten von Verschlüsselungsbereichen (Vorschauversion)

Verschlüsselungsbereiche (Vorschauversion) ermöglichen Ihnen die Verwaltung der Verschlüsselung auf der Ebene einzelner Blobs oder Container. Ein Verschlüsselungsbereich isoliert Blobdaten in einer Secure Enclave innerhalb eines Speicherkontos. Sie können Verschlüsselungsbereiche verwenden, um sichere Grenzen zwischen Daten zu erstellen, die sich im selben Speicherkonto befinden, aber zu unterschiedlichen Kunden gehören. Weitere Informationen zu Verschlüsselungsbereichen finden Sie unter [Verschlüsselungsbereiche für Blobspeicher (Vorschau)](encryption-scope-overview.md).

In diesem Artikel wird gezeigt, wie Sie einen Verschlüsselungsbereich erstellen. Darüber hinaus wird erläutert, wie Sie beim Erstellen eines Blobs oder Containers einen Verschlüsselungsbereich angeben.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-an-encryption-scope"></a>Erstellen eines Verschlüsselungsbereichs

Sie können Verschlüsselungsbereiche mit einem von Microsoft verwalteten Schlüssel oder mit einem kundenseitig verwalteten Schlüssel erstellen, der in Azure Key Vault oder in Azure Key Vault Managed Hardware Security Model (HSM) (Vorschauversion) gespeichert ist. Zum Erstellen eines Verschlüsselungsbereichs mit einem kundenseitig verwalteten Schlüssel müssen Sie zunächst einen Schlüsseltresor oder ein verwaltetes HSM erstellen und den Schlüssel hinzufügen, den Sie für den Bereich verwenden möchten. Für den Schlüsseltresor oder das verwaltete HSM muss der Löschschutz aktiviert sein. Außerdem muss sich der Schlüsseltresor oder das verwaltete HSM in derselben Region befinden wie das Speicherkonto.

Ein Verschlüsselungsbereich wird bei der Erstellung immer automatisch aktiviert. Nach der Erstellung des Verschlüsselungsbereichs können Sie diesen beim Erstellen eines Blobs angeben. Sie können beim Erstellen eines Containers auch einen Standardverschlüsselungsbereich angeben, der automatisch für alle Blobs im Container gilt.

# <a name="portal"></a>[Portal](#tab/portal)

Führen Sie die folgenden Schritte aus, um einen Verschlüsselungsbereich im Azure-Portal zu erstellen:

1. Navigieren Sie zum Speicherkonto im Azure-Portal.
1. Wählen Sie die Einstellung **Verschlüsselung** aus.
1. Wählen Sie die Registerkarte **Verschlüsselungsbereiche** aus.
1. Klicken Sie auf die Schaltfläche **Hinzufügen**, um einen neuen Verschlüsselungsbereich hinzuzufügen.
1. Geben Sie im Bereich **Verschlüsselungsbereich erstellen** einen Namen für den neuen Bereich ein.
1. Wählen Sie den Verschlüsselungstyp aus: **Von Microsoft verwaltete Schlüssel** oder **Kundenseitig verwaltete Schlüssel**.
    - Wenn Sie **Von Microsoft verwaltete Schlüssel** ausgewählt haben, klicken Sie auf **Erstellen**, um den Verschlüsselungsbereich zu erstellen.
    - Wenn Sie **Kundenseitig verwaltete Schlüssel** ausgewählt haben, geben Sie einen Schlüsseltresor oder ein verwaltetes HSM, einen Schlüssel und die Schlüsselversion an, die für diesen Verschlüsselungsbereich verwendet werden sollen, wie in der folgenden Abbildung gezeigt.

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="Screenshot: Erstellen eines Verschlüsselungsbereichs über das Azure-Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Installieren Sie zum Erstellen eines Verschlüsselungsbereichs mithilfe von PowerShell zunächst das Az.Storage-Vorschaumodul. Die Verwendung der aktuellen Vorschauversion wird empfohlen, Verschlüsselungsbereiche werden jedoch ab Version 1.13.4-preview unterstützt. Entfernen Sie alle anderen Versionen des Moduls Az.Storage.

Mit dem folgenden Befehl wird das Modul Az.Storage [2.1.1-preview](https://www.powershellgallery.com/packages/Az.Storage/2.1.1-preview) installiert:

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.1.1-preview -AllowPrerelease
```

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Erstellen eines Verschlüsselungsbereichs, der durch von Microsoft verwaltete Schlüssel geschützt wird

Wenn Sie einen neuen Verschlüsselungsbereich erstellen möchten, der durch von Microsoft verwaltete Schlüssel geschützt wird, rufen Sie den Befehl **New-AzStorageEncryptionScope** mit dem Parameter `-StorageEncryption` auf.

Denken Sie daran, die Platzhalterwerte in diesem Beispiel durch Ihre eigenen Werte zu ersetzen:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$scopeName1 = "customer1scope"

New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -StorageEncryption
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Erstellen eines Verschlüsselungsbereichs, der mit kundenseitig verwalteten Schlüsseln geschützt wird

Wenn Sie einen neuen Verschlüsselungsbereich erstellen möchten, der mit kundenseitig verwalteten Schlüsseln geschützt wird, die in einem Schlüsseltresor oder einem verwalteten HSM gespeichert sind, konfigurieren Sie zunächst kundenseitig verwaltete Schlüssel für das Speicherkonto. Sie müssen dem Speicherkonto eine verwaltete Identität zuweisen und dann die verwaltete Identität verwenden, um die Zugriffsrichtlinie für den Schlüsseltresor oder das verwaltete HSM so zu konfigurieren, dass das Speicherkonto über Zugriffsberechtigungen verfügt.

Zum Konfigurieren von kundenseitig verwalteten Schlüsseln für die Verwendung mit einem Verschlüsselungsbereich muss der Löschschutz für den Schlüsseltresor oder das verwaltete HSM aktiviert werden. Der Schlüsseltresor oder das verwaltete HSM muss sich in derselben Region wie das Speicherkonto befinden.

Denken Sie daran, die Platzhalterwerte in diesem Beispiel durch Ihre eigenen Werte zu ersetzen:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$keyVaultName = "<key-vault>"
$keyUri = "<key-uri-with-version>"
$scopeName2 = "customer2scope"


# Assign a system managed identity to the storage account.
$storageAccount = Set-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -AssignIdentity

# Configure the access policy for the key vault.
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

Rufen Sie als Nächstes den Befehl **New-AzStorageEncryptionScope** mit dem Parameter `-KeyvaultEncryption` auf, und geben Sie den Schlüssel-URI an. Geben Sie unbedingt die Schlüsselversion im Schlüssel-URI an. Denken Sie daran, die Platzhalterwerte in diesem Beispiel durch Ihre eigenen Werte zu ersetzen:

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)

Installieren Sie zum Erstellen eines Verschlüsselungsbereichs mit der Azure CLI zunächst mindestens Version 2.4.0 der CLI.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Erstellen eines Verschlüsselungsbereichs, der durch von Microsoft verwaltete Schlüssel geschützt wird

Wenn Sie einen neuen Verschlüsselungsbereich erstellen möchten, der durch von Microsoft verwaltete Schlüssel geschützt wird, rufen Sie den Befehl [az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) auf, und geben Sie dabei `Microsoft.Storage` für den Parameter `--key-source` an. Denken Sie daran, die Platzhalterwerte durch Ihre eigenen Werte zu ersetzen:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Erstellen eines Verschlüsselungsbereichs, der mit kundenseitig verwalteten Schlüsseln geschützt wird

Wenn Sie einen neuen Verschlüsselungsbereich erstellen möchten, der durch von Microsoft verwaltete Schlüssel geschützt wird, rufen Sie den Befehl [az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) auf, und geben Sie dabei `Microsoft.Storage` für den Parameter `--key-source` an. Denken Sie daran, die Platzhalterwerte durch Ihre eigenen Werte zu ersetzen:

Wenn Sie einen neuen Verschlüsselungsbereich erstellen möchten, der mit kundenseitig verwalteten Schlüsseln in einem Schlüsseltresor oder einem verwalteten HSM geschützt wird, konfigurieren Sie zunächst kundenseitig verwaltete Schlüssel für das Speicherkonto. Sie müssen dem Speicherkonto eine verwaltete Identität zuweisen und dann die verwaltete Identität verwenden, um die Zugriffsrichtlinie für den Schlüsseltresor so zu konfigurieren, dass das Speicherkonto über Zugriffsberechtigungen verfügt. Weitere Informationen finden Sie unter [Kundenseitig verwaltete Schlüssel für die Azure Storage-Verschlüsselung](../common/customer-managed-keys-overview.md).

Zum Konfigurieren von kundenseitig verwalteten Schlüsseln für die Verwendung mit einem Verschlüsselungsbereich muss der Löschschutz für den Schlüsseltresor oder das verwaltete HSM aktiviert werden. Der Schlüsseltresor oder das verwaltete HSM muss sich in derselben Region wie das Speicherkonto befinden.

Denken Sie daran, die Platzhalterwerte in diesem Beispiel durch Ihre eigenen Werte zu ersetzen:

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity

storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group> \
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

Rufen Sie als Nächstes den Befehl **az storage account encryption-scope create** mit dem Parameter `--key-uri` auf, und geben Sie den Schlüssel-URI an. Geben Sie unbedingt die Schlüsselversion im Schlüssel-URI an. Denken Sie daran, die Platzhalterwerte in diesem Beispiel durch Ihre eigenen Werte zu ersetzen:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

Informationen zum Konfigurieren der Azure Storage-Verschlüsselung mit kundenseitig verwalteten Schlüsseln in einem Schlüsseltresor finden Sie unter [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault gespeichert sind](../common/customer-managed-keys-configure-key-vault.md). Informationen zum Konfigurieren von kundenseitig verwalteten Schlüsseln in einem verwalteten HSM finden Sie unter [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault Managed HSM (Vorschau) gespeichert sind](../common/customer-managed-keys-configure-key-vault-hsm.md).

## <a name="list-encryption-scopes-for-storage-account"></a>Auflisten der Verschlüsselungsbereiche für das Speicherkonto

# <a name="portal"></a>[Portal](#tab/portal)

Navigieren Sie zum Anzeigen der Verschlüsselungsbereiche für ein Speicherkonto im Azure-Portal zur Einstellung **Verschlüsselungsbereiche** für das Speicherkonto. In diesem Bereich können Sie einen Verschlüsselungsbereich aktivieren oder deaktivieren oder den Schlüssel für einen Verschlüsselungsbereich ändern.

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="Screenshot: Liste der Verschlüsselungsbereiche im Azure-Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Rufen Sie den Befehl **Get-AzStorageEncryptionScope** auf, um die für ein Speicherkonto verfügbaren Verschlüsselungsbereiche mit PowerShell aufzulisten. Denken Sie daran, die Platzhalterwerte in diesem Beispiel durch Ihre eigenen Werte zu ersetzen:

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

Verwenden Sie die Pipelinesyntax, um alle Verschlüsselungsbereiche in einer Ressourcengruppe nach Speicherkonto aufzulisten:

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)

Rufen Sie den Befehl [az storage account encryption-scope list](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-list) auf, um die für ein Speicherkonto verfügbaren Verschlüsselungsbereiche mit der Azure CLI aufzulisten. Denken Sie daran, die Platzhalterwerte in diesem Beispiel durch Ihre eigenen Werte zu ersetzen:

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="create-a-container-with-a-default-encryption-scope"></a>Erstellen eines Containers mit einem Standardverschlüsselungsbereich

Wenn Sie einen Container erstellen, können Sie einen standardmäßigen Verschlüsselungsbereich angeben. Blobs in diesem Container verwenden dann standardmäßig diesen Bereich.

Ein einzelnes Blob kann mit einem eigenen Verschlüsselungsbereich erstellt werden, es sei denn, der Container ist so konfiguriert, dass er die Verwendung des Standardbereichs für alle Blobs erfordert.

# <a name="portal"></a>[Portal](#tab/portal)

Wenn Sie einen Container mit einem Standardverschlüsselungsbereich im Azure-Portal erstellen möchten, erstellen Sie zunächst den Verschlüsselungsbereich wie unter [Erstellen eines Verschlüsselungsbereichs](#create-an-encryption-scope) beschrieben. Führen Sie anschließend die folgenden Schritte aus, um den Container zu erstellen:

1. Navigieren Sie zur Liste der Container in Ihrem Speicherkonto, und wählen Sie die Schaltfläche **Hinzufügen** aus, um einen neuen Container zu erstellen.
1. Erweitern Sie im Bereich **Neuer Container** die Einstellungen unter **Erweitert**.
1. Wählen Sie in der Dropdownliste **Verschlüsselungsbereich** den Standardverschlüsselungsbereich für den Container aus.
1. Wenn alle Blobs im Container den Standardverschlüsselungsbereich verwenden sollen, aktivieren Sie das Kontrollkästchen **Use this encryption scope for all blobs in the container** (Diesen Verschlüsselungsbereich für alle Blobs im Container verwenden). Ist dieses Kontrollkästchen aktiviert, kann ein einzelnes Blob im Container den Verschlüsselungsbereich nicht überschreiben.

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="Screenshot: Container mit Standardverschlüsselungsbereich":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Wenn Sie einen Container mit einem Standardverschlüsselungsbereich mit PowerShell erstellen möchten, rufen Sie den Befehl [New-AzRmStorageContainer](/powershell/module/az.storage/new-azrmstoragecontainer) auf, und geben Sie dabei den Bereich für den Parameter `-DefaultEncryptionScope` an. Mit dem Befehl **New-AzRmStorageContainer** wird ein Container unter Verwendung des Azure Storage-Ressourcenanbieters erstellt. Dadurch werden die Konfiguration von Verschlüsselungsbereichen und andere Ressourcenverwaltungsvorgänge ermöglicht.

Legen Sie den Parameter `-PreventEncryptionScopeOverride` auf `true` fest, um zu erzwingen, dass alle Blobs in einem Container den Standardbereich des Containers verwenden.

```powershell
$containerName1 = "container1"
$containerName2 = "container2"

# Create a container with a default encryption scope that cannot be overridden.
New-AzRmStorageContainer -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -Name $containerName1 `
    -DefaultEncryptionScope $scopeName1 `
    -PreventEncryptionScopeOverride $true
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)

Wenn Sie einen Container mit einem Standardverschlüsselungsbereich mit der Azure CLI erstellen möchten, rufen Sie den Befehl [az storage container create](/cli/azure/storage/container#az-storage-container-create) auf, und geben Sie dabei den Bereich für den Parameter `--default-encryption-scope` an. Legen Sie den Parameter `--prevent-encryption-scope-override` auf `true` fest, um zu erzwingen, dass alle Blobs in einem Container den Standardbereich des Containers verwenden.

Im folgenden Beispiel wird Ihr Azure AD-Konto genutzt, um den Vorgang zur Erstellung des Containers zu autorisieren. Sie können auch den Kontozugriffsschlüssel verwenden. Weitere Informationen finden Sie unter [Autorisieren des Zugriffs auf Blob- oder Warteschlangendaten mit der Azure-Befehlszeilenschnittstelle](./authorize-data-operations-cli.md).

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <container> \
    --default-encryption-scope <scope> \
    --prevent-encryption-scope-override true \
    --auth-mode login
```

---

Versucht ein Client, einen Bereich anzugeben, wenn ein Blob in einen Container mit einem Standardverschlüsselungsbereich hochgeladen wird und der Container so konfiguriert ist, dass Blobs den Standardbereich nicht überschreiben können, tritt bei diesem Vorgang ein Fehler auf. Eine Meldung wird angezeigt, die angibt, dass die Anforderung durch die Containerverschlüsselungsrichtlinie untersagt wird.

## <a name="upload-a-blob-with-an-encryption-scope"></a>Hochladen eines Blobs mit einem Verschlüsselungsbereich

Beim Hochladen eines Blobs können Sie einen Verschlüsselungsbereich für das Blob angeben oder den Standardverschlüsselungsbereich für den Container verwenden, sofern einer angegeben wurde.

# <a name="portal"></a>[Portal](#tab/portal)

Wenn Sie ein Blob hochladen und den Verschlüsselungsbereich über das Azure-Portal angeben möchten, erstellen Sie zunächst den Verschlüsselungsbereich wie unter [Erstellen eines Verschlüsselungsbereichs](#create-an-encryption-scope) beschrieben. Führen Sie anschließend die folgenden Schritte aus, um das Blob zu erstellen:

1. Navigieren Sie zu dem Container, in den Sie das Blob hochladen möchten.
1. Wählen Sie die Schaltfläche **Hochladen** aus, und navigieren Sie zum hochzuladenden Blob.
1. Erweitern Sie im Bereich **Blob hochladen** die Einstellungen unter **Erweitert**.
1. Navigieren Sie zum Dropdownmenü im Abschnitt **Verschlüsselungsbereich**. Standardmäßig wird das Blob mit dem Standardverschlüsselungsbereich für den Container erstellt, sofern einer angegeben wurde. Wenn der Container erfordert, dass Blobs den Standardverschlüsselungsbereich verwenden, ist dieser Abschnitt deaktiviert.
1. Wenn Sie einen anderen Bereich für das hochzuladende Blob angeben möchten, wählen Sie **Vorhandenen Bereich auswählen** und dann in der Dropdownliste den gewünschten Bereich aus.

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="Screenshot: Hochladen eines Blobs mit einem Verschlüsselungsbereich":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Wenn Sie ein Blob hochladen und den Verschlüsselungsbereich mithilfe von PowerShell angeben möchten, rufen Sie den Befehl [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) auf, und geben Sie den Verschlüsselungsbereich für das Blob an.

```powershell
$containerName2 = "container2"
$localSrcFile = "C:\temp\helloworld.txt"
$ctx = (Get-AzStorageAccount -ResourceGroupName $rgName -StorageAccountName $accountName).Context

# Create a new container with no default scope defined.
New-AzStorageContainer -Name $containerName2 -Context $ctx
# Upload a block upload with an encryption scope specified.
Set-AzStorageBlobContent -Context $ctx -Container $containerName2 -File $localSrcFile -Blob "helloworld.txt" -BlobType Block -EncryptionScope $scopeName2
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)

Wenn Sie ein Blob hochladen und den Verschlüsselungsbereich mithilfe der Azure CLI angeben möchten, rufen Sie den Befehl [az storage blob upload](/cli/azure/storage/blob#az-storage-blob-upload) auf, und geben Sie den Verschlüsselungsbereich für das Blob an.

Wenn Sie Azure Cloud Shell verwenden, führen Sie die unter [Hochladen eines Blobs](storage-quickstart-blobs-cli.md#upload-a-blob) beschriebenen Schritte aus, um eine Datei im Stammverzeichnis zu erstellen. Anschließend können Sie diese Datei mithilfe des folgenden Beispiels in ein Blob hochladen:

```azurecli-interactive
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --file <file> \
    --name <file> \
    --encryption-scope <scope>
```

---

## <a name="change-the-encryption-key-for-a-scope"></a>Ändern des Verschlüsselungsschlüssels für einen Bereich

Wenn Sie den Schlüssel, der einen Verschlüsselungsbereich schützt, von einem von Microsoft verwalteten Schlüssel in einen kundenseitig verwalteten Schlüssel ändern möchten, vergewissern Sie sich zunächst, dass Sie kundenseitig verwaltete Schlüssel mit Azure Key Vault oder Key Vault HSM für das Speicherkonto aktiviert haben. Weitere Informationen finden Sie unter [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault gespeichert sind](../common/customer-managed-keys-configure-key-vault.md) oder [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault Managed HSM gespeichert sind](../common/customer-managed-keys-configure-key-vault.md).

# <a name="portal"></a>[Portal](#tab/portal)

Führen Sie die folgenden Schritte aus, um den Schlüssel zu ändern, der einen Bereich im Azure-Portal schützt:

1. Navigieren Sie zur Registerkarte **Verschlüsselungsbereiche**, um die Liste der Verschlüsselungsbereiche für das Speicherkonto anzuzeigen.
1. Wählen Sie neben dem Bereich, den Sie ändern möchten, die Schaltfläche **Mehr** aus.
1. Im Bereich **Verschlüsselungsbereich bearbeiten** können Sie den Verschlüsselungstyp von „Von Microsoft verwaltete Schlüssel“ in „Kundenseitig verwaltete Schlüssel“ ändern und umgekehrt.
1. Wählen Sie zum Auswählen eines neuen kundenseitig verwalteten Schlüssels die Option **Neuen Schlüssel verwenden** aus, und geben Sie den Schlüsseltresor, den Schlüssel und die Schlüsselversion an.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Wenn Sie mit PowerShell den Schlüssel, der einen Verschlüsselungsbereich schützt, von einem kundenseitig verwalteten Schlüssel in einen von Microsoft verwalteten Schlüssel ändern möchten, rufen Sie den Befehl **Update-AzStorageEncryptionScope** auf, und übergeben Sie den Parameter `-StorageEncryption`:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

Rufen Sie als Nächstes den Befehl **Update-AzStorageEncryptionScope** auf, und übergeben Sie die Parameter `-KeyUri` und `-KeyvaultEncryption`:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)

Wenn Sie mit der Azure CLI den Schlüssel, der einen Verschlüsselungsbereich schützt, von einem kundenseitig verwalteten Schlüssel in einen von Microsoft verwalteten Schlüssel ändern möchten, rufen Sie den Befehl [az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) auf, und übergeben Sie den Parameter `--key-source` mit dem Wert `Microsoft.Storage`:

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

Rufen Sie als Nächstes den Befehl **az storage account encryption-scope update** auf, und übergeben Sie den Parameter `--key-uri` sowie den Parameter `--key-source` mit dem Wert `Microsoft.KeyVault`:

```powershell
az storage account encryption-scope update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="disable-an-encryption-scope"></a>Deaktivieren eines Verschlüsselungsbereichs

Wenn ein Verschlüsselungsbereich deaktiviert ist, wird Ihnen dieser nicht mehr in Rechnung gestellt. Deaktivieren Sie alle Verschlüsselungsbereiche, die nicht benötigt werden, um unnötige Gebühren zu vermeiden. Weitere Informationen finden Sie unter [Azure Storage-Verschlüsselung für ruhende Daten](../common/storage-service-encryption.md).

# <a name="portal"></a>[Portal](#tab/portal)

Navigieren Sie zum Deaktivieren eines Verschlüsselungsbereichs im Azure-Portal zur Einstellung **Verschlüsselungsbereiche** für das Speicherkonto, und wählen Sie den gewünschten Verschlüsselungsbereich und dann **Deaktivieren** aus.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Rufen Sie zum Deaktivieren eines Verschlüsselungsbereichs mit PowerShell den Befehl „Update-AzStorageEncryptionScope“ auf, und fügen Sie den Parameter `-State` mit dem Wert `disabled` ein, wie im folgenden Beispiel gezeigt. Wenn Sie einen Verschlüsselungsbereich wieder aktivieren möchten, rufen Sie den gleichen Befehl auf. Legen Sie dabei den Parameter `-State` auf `enabled` fest. Denken Sie daran, die Platzhalterwerte in diesem Beispiel durch Ihre eigenen Werte zu ersetzen:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)

Rufen Sie zum Deaktivieren eines Verschlüsselungsbereichs mit der Azure CLI den Befehl [az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) auf, und fügen Sie den Parameter `--state` mit dem Wert `Disabled` ein, wie im folgenden Beispiel gezeigt. Wenn Sie einen Verschlüsselungsbereich wieder aktivieren möchten, rufen Sie den gleichen Befehl auf. Legen Sie dabei den Parameter `--state` auf `Enabled` fest. Denken Sie daran, die Platzhalterwerte in diesem Beispiel durch Ihre eigenen Werte zu ersetzen:

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <scope> \
    --state Disabled
```

---

## <a name="next-steps"></a>Nächste Schritte

- [Azure Storage encryption for data at rest (Azure Storage-Verschlüsselung für ruhende Daten)](../common/storage-service-encryption.md)
- [Verschlüsselungsbereiche für Blobspeicher (Vorschau)](encryption-scope-overview.md)
- [Kundenseitig verwaltete Schlüssel für die Azure Storage-Verschlüsselung](../common/customer-managed-keys-overview.md)