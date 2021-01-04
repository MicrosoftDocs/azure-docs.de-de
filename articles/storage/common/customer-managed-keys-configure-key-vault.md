---
title: Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault gespeichert sind
titleSuffix: Azure Storage
description: Erfahren Sie, wie die Azure Storage-Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault gespeichert sind, über das Azure-Portal, PowerShell oder die Azure CLI konfiguriert wird.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 02661c9c2a581ab21a2ae9dc31e5da95426c0edd
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843382"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault"></a>Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault gespeichert sind

Azure Storage verschlüsselt alle Daten in einem ruhenden Speicherkonto. Standardmäßig werden Daten mit von Microsoft verwalteten Schlüsseln verschlüsselt. Um zusätzliche Kontrolle über die Verschlüsselungsschlüssel zu erhalten, können Sie eigene Schlüssel verwalten. Kundenseitig verwaltete Schlüssel müssen in Azure Key Vault oder im Key Vault Managed Hardware Security Model (HSM, Vorschau) gespeichert werden.

In diesem Artikel wird erläutert, wie die Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in einem Schlüsseltresor gespeichert sind, über das Azure-Portal, PowerShell oder die Azure CLI konfiguriert wird. Informationen zum Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in einem verwalteten HSM gespeichert sind, finden Sie unter [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault Managed HSM (Vorschau) gespeichert sind](customer-managed-keys-configure-key-vault-hsm.md).

> [!NOTE]
> Azure Key Vault und Azure Key Vault Managed HSM unterstützen dieselben APIs und Verwaltungsschnittstellen für die Konfiguration.

## <a name="configure-a-key-vault"></a>Konfigurieren eines Schlüsseltresors

Sie können einen neuen oder vorhandenen Schlüsseltresor verwenden, um kundenseitig verwaltete Schlüssel zu speichern. Das Speicherkonto und der Schlüsseltresor müssen sich in derselben Region befinden, dürfen aber zu verschiedenen Abonnements gehören.

Bei Verwendung kundenseitig verwalteter Schlüssel mit der Azure Storage-Verschlüsselung muss der Schlüsseltresor vor vorläufigem und endgültigem Löschen geschützt sein. Das vorläufige Löschen ist standardmäßig aktiviert, wenn Sie einen neuen Schlüsseltresor erstellen, und kann nicht deaktiviert werden. Sie können den Löschschutz aktivieren, wenn Sie den Schlüsseltresor erstellen oder nachdem er erstellt wurde.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Informationen zum Erstellen eines Schlüsseltresors über das Azure-Portal finden Sie unter [Schnellstart: Erstellen eines Schlüsseltresors über das Azure-Portal](../../key-vault/general/quick-create-portal.md). Wenn Sie den Schlüsseltresor erstellen, wählen Sie **Löschschutz aktivieren** aus, wie in der folgenden Abbildung dargestellt.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/configure-key-vault-portal.png" alt-text="Screenshot, der veranschaulicht, wie der Löschschutz beim Erstellen eines Schlüsseltresors aktiviert wird":::

Führen Sie die folgenden Schritte aus, um den Löschschutz für einen vorhandenen Schlüsseltresor zu aktivieren:

1. Navigieren Sie im Azure-Portal zu Ihrem Schlüsseltresor.
1. Wählen Sie unter **Einstellungen** die Option **Eigenschaften** aus.
1. Wählen Sie im Abschnitt **Löschschutz** die Option **Löschschutz aktivieren** aus.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Um einen neuen Schlüsseltresor mit PowerShell zu erstellen, installieren Sie Version 2.0.0 oder höher des PowerShell-Moduls [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0). Rufen Sie dann [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) auf, um einen neuen Schlüsseltresor zu erstellen. Ab Version 2.0.0 des Az.KeyVault-Moduls ist das vorläufige Löschen standardmäßig aktiviert, wenn Sie einen neuen Schlüsseltresor erstellen.

Im folgenden Beispiel wird ein neuer Schlüsseltresor erstellt, der sowohl vor vorläufigen als auch vor endgültigen Löschvorgängen geschützt ist. Denken Sie daran, die Platzhalterwerte in den spitzen Klammern durch Ihre eigenen Werte zu ersetzen.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

Informationen zum Aktivieren des Löschschutzes für einen vorhandenen Schlüsseltresor mit PowerShell finden Sie unter [Verwenden des vorläufigen Löschens mit PowerShell](../../key-vault/general/key-vault-recovery.md).

Weisen Sie als Nächstes dem Speicherkonto eine systemseitig zugewiesene verwaltete Identität zu. Sie verwenden diese Identität, um dem Speicherkonto die Zugriffsberechtigungen für den Schlüsseltresor zu gewähren. Weitere Informationen zu systemseitig zugewiesenen verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../active-directory/managed-identities-azure-resources/overview.md)

Rufen Sie für das Zuweisen einer verwalteten Identität mithilfe von PowerShell [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) auf:

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Konfigurieren Sie zuletzt die Zugriffsrichtlinie für den Schlüsseltresor, sodass das Speicherkonto über die Berechtigungen für den Zugriff auf diesen verfügt. In diesem Schritt verwenden Sie die verwaltete Identität, die Sie zuvor dem Speicherkonto zugewiesen haben.

Rufen Sie zum Festlegen der Zugriffsrichtlinie für den Schlüsseltresor [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) auf:

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Rufen Sie [az keyvault create](/cli/azure/keyvault#az-keyvault-create) auf, um einen neuen Schlüsseltresor mit der Azure CLI zu erstellen. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-purge-protection
```

Informationen zum Aktivieren des Löschschutzes für einen vorhandenen Schlüsseltresor mit der Azure CLI finden Sie unter [Verwenden des vorläufigen Löschens mit der CLI](../../key-vault/general/key-vault-recovery.md).

Weisen Sie als Nächstes dem Speicherkonto eine systemseitig zugewiesene verwaltete Identität zu. Sie verwenden diese Identität, um dem Speicherkonto die Zugriffsberechtigungen für den Schlüsseltresor zu gewähren. Weitere Informationen zu systemseitig zugewiesenen verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../active-directory/managed-identities-azure-resources/overview.md)

Rufen Sie [az storage account update](/cli/azure/storage/account#az-storage-account-update) auf, um eine verwaltete Identität mit der Azure CLI zuzuweisen.

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Konfigurieren Sie zuletzt die Zugriffsrichtlinie für den Schlüsseltresor, sodass das Speicherkonto über die Berechtigungen für den Zugriff auf diesen verfügt. In diesem Schritt verwenden Sie die verwaltete Identität, die Sie zuvor dem Speicherkonto zugewiesen haben.

Rufen Sie [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) auf, um die Zugriffsrichtlinie für den Schlüsseltresor festzulegen:

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

---

## <a name="add-a-key"></a>Hinzufügen eines Schlüssels

Als Nächstes fügen Sie im Schlüsseltresor einen Schlüssel hinzu.

Die Azure Storage-Verschlüsselung unterstützt RSA- und RSA-HSM-Schlüssel mit einer Größe von 2.048, 3.072 und 4.096 Bit. Weitere Informationen über Schlüssel finden Sie unter [Informationen zu Schlüsseln](../../key-vault/keys/about-keys.md).

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Informationen zum Hinzufügen eines Schlüssels über das Azure-Portal finden Sie unter [Schnellstart: Festlegen und Abrufen eines Schlüssels aus Azure Key Vault mit dem Azure-Portal](../../key-vault/keys/quick-create-portal.md).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Um einen Schlüssel mit PowerShell hinzuzufügen, rufen Sie [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) auf. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen und die in den vorherigen Beispielen definierten Variablen zu verwenden.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName `
    -Name <key> `
    -Destination 'Software'
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um einen Schlüssel mit der Azure CLI hinzuzufügen, rufen Sie [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create) auf. Denken Sie daran, die Platzhalterwerte in den spitzen Klammern durch Ihre eigenen Werte zu ersetzen.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

---

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurieren der Verschlüsselung mit von Kunden verwalteten Schlüsseln

Als Nächstes konfigurieren Sie Ihr Azure Storage-Konto für kundenseitig verwaltete Schlüssel mit Azure Key Vault. Legen Sie dann den Schlüssel fest, der dem Speicherkonto zugeordnet werden soll.

Beim Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln können Sie festlegen, dass die für die Azure Storage-Verschlüsselung verwendete Schlüsselversion automatisch aktualisiert wird, wenn im zugeordneten Schlüsseltresor eine neue Version verfügbar ist. Alternativ können Sie explizit eine Schlüsselversion angeben, die für die Verschlüsselung verwendet werden soll, bis die Schlüsselversion manuell aktualisiert wird.

> [!NOTE]
> Um einen Schlüssel zu drehen, erstellen Sie eine neue Version des Schlüssels in Azure Key Vault. Azure Storage handhabt die Rotation des Schlüssels nicht in Azure Key Vault, sodass Sie Ihren Schlüssel manuell drehen oder eine Funktion erstellen müssen, um ihn nach einem Zeitplan zu drehen.

### <a name="configure-encryption-for-automatic-updating-of-key-versions"></a>Konfigurieren der Verschlüsselung für die automatische Aktualisierung von Schlüsselversionen

Azure Storage kann den kundenseitig verwalteten Schlüssel, der für die Verschlüsselung verwendet wird, automatisch aktualisieren, um die neueste Schlüsselversion zu verwenden. Wenn der kundenseitig verwaltete Schlüssel in Azure Key Vault gedreht wird, beginnt Azure Storage automatisch, die neueste Version des Schlüssels zur Verschlüsselung zu verwenden.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Führen Sie die folgenden Schritte aus, um kundenseitig verwaltete Schlüssel mit der automatischen Aktualisierung der Schlüsselversion im Azure-Portal zu konfigurieren:

1. Navigieren Sie zum Speicherkonto.
1. Klicken Sie auf dem Blatt **Einstellungen** Blatt für das Speicherkonto auf **Verschlüsselung**. Wählen Sie die Option **Von Kunden verwaltete Schlüssel** wie in der folgenden Abbildung gezeigt aus.

    ![Portal-Screenshot mit der Verschlüsselungsoption](./media/customer-managed-keys-configure-key-vault/portal-configure-encryption-keys.png)

1. Wählen Sie die Option **Select from Key Vault** (Aus Schlüsseltresor wählen).
1. Wählen Sie **Schlüsseltresor und Schlüssel auswählen** aus.
1. Wählen Sie den Schlüsseltresor mit dem Schlüssel aus, den Sie verwenden möchten.
1. Wählen Sie den Schlüssel aus dem Schlüsseltresor aus.

   ![Screenshot: Auswählen von Schlüsseltresor und Schlüssel](./media/customer-managed-keys-configure-key-vault/portal-select-key-from-key-vault.png)

1. Speichern Sie die Änderungen.

Nachdem Sie den Schlüssel angegeben haben, sehen Sie im Azure-Portal, dass die automatische Aktualisierung der Schlüsselversion aktiviert ist. Außerdem wird die Schlüsselversion angezeigt, die derzeit für die Verschlüsselung verwendet wird.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/portal-auto-rotation-enabled.png" alt-text="Screenshot, der die automatische Aktualisierung der aktivierten Schlüsselversion anzeigt":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Um kundenseitig verwaltete Schlüssel mit der automatischen Aktualisierung der Schlüsselversion mit PowerShell zu konfigurieren, installieren Sie das Modul [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage), Version 2.0.0 oder höher.

Um die Schlüsselversion für einen kundenseitig verwalteten Schlüssel automatisch zu aktualisieren, lassen Sie die Schlüsselversion aus, wenn Sie die Verschlüsselung mit kundenseitig verwalteten Schlüsseln für das Speicherkonto konfigurieren. Rufen Sie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) auf, um die Verschlüsselungseinstellungen des Speicherkontos zu aktualisieren, wie im folgenden Beispiel gezeigt, und fügen Sie die Option **-KeyvaultEncryption** ein, um kundenseitig verwaltete Schlüssel für das Speicherkonto zu aktivieren.

Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen und die in den vorherigen Beispielen definierten Variablen zu verwenden.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um kundenseitig verwaltete Schlüssel mit der automatischen Aktualisierung der Schlüsselversion mit der Azure CLI zu konfigurieren, installieren Sie [Azure CLI, Version 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) oder höher. Weitere Informationen finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).

Um die Schlüsselversion für einen kundenseitig verwalteten Schlüssel automatisch zu aktualisieren, lassen Sie die Schlüsselversion aus, wenn Sie die Verschlüsselung mit kundenseitig verwalteten Schlüsseln für das Speicherkonto konfigurieren. Rufen Sie [az storage account update](/cli/azure/storage/account#az-storage-account-update) wie im folgenden Beispiel gezeigt auf, um die Verschlüsselungseinstellungen des Speicherkontos zu aktualisieren. Fügen Sie den Parameter `--encryption-key-source` ein, und legen Sie ihn auf `Microsoft.Keyvault` fest, um kundenseitig verwaltete Schlüssel für das Speicherkonto zu aktivieren.

Denken Sie daran, die Platzhalterwerte in den spitzen Klammern durch Ihre eigenen Werte zu ersetzen.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

---

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>Konfigurieren der Verschlüsselung für die manuelle Aktualisierung von Schlüsselversionen

Wenn Sie die Schlüsselversion manuell aktualisieren möchten, geben Sie die Version explizit an, wenn Sie die Verschlüsselung mit kundenseitig verwalteten Schlüsseln konfigurieren. In diesem Fall wird die Schlüsselversion von Azure Storage nicht automatisch aktualisiert, wenn eine neue Version im Schlüsseltresor erstellt wird. Um eine neue Schlüsselversion zu verwenden, müssen Sie die Version, die für die Azure Storage-Verschlüsselung verwendet wird, manuell aktualisieren.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Um kundenseitig verwaltete Schlüssel mit der manuellen Aktualisierung der Schlüsselversion im Azure-Portal zu konfigurieren, geben Sie den Schlüssel-URI einschließlich der Version an. Gehen Sie wie folgt vor, um einen Schlüssel als URI anzugeben:

1. Um den Schlüssel-URI im Azure-Portal anzuzeigen, navigieren Sie zu Ihrem Schlüsseltresor, und wählen die Einstellung **Schlüssel** aus. Wählen Sie den gewünschten Schlüssel aus, und klicken Sie darauf, um dessen Versionen anzuzeigen. Wählen Sie eine Schlüsselversion aus, um die Einstellungen für diese Version anzuzeigen.
1. Kopieren Sie den Wert im Feld **Schlüsselbezeichner**, das den URI enthält.

    ![Screenshot mit Schlüssel-URI des Schlüsseltresors](media/customer-managed-keys-configure-key-vault/portal-copy-key-identifier.png)

1. Wählen Sie in den Einstellungen für den **Verschlüsselungsschlüssel** für Ihr Speicherkonto die Option **Schlüssel-URI eingeben** aus.
1. Fügen Sie den kopierten URI in das Feld **Schlüssel-URI** ein. Lassen Sie die Schlüsselversion aus dem URI aus, um eine automatische Aktualisierung der Schlüsselversion zu ermöglichen.

   ![Screenshot zur Eingabe des Schlüssel-URI](./media/customer-managed-keys-configure-key-vault/portal-specify-key-uri.png)

1. Geben Sie das Abonnement an, das den Schlüsseltresor enthält.
1. Speichern Sie die Änderungen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Um kundenseitig verwaltete Schlüssel mit der manuellen Aktualisierung der Schlüsselversion zu konfigurieren, geben Sie die Schlüsselversion explizit an, wenn Sie die Verschlüsselung für das Speicherkonto konfigurieren. Rufen Sie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) auf, um die Verschlüsselungseinstellungen des Speicherkontos zu aktualisieren, wie im folgenden Beispiel gezeigt, und fügen Sie die Option **-KeyvaultEncryption** ein, um kundenseitig verwaltete Schlüssel für das Speicherkonto zu aktivieren.

Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen und die in den vorherigen Beispielen definierten Variablen zu verwenden.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

Wenn Sie die Schlüsselversion manuell aktualisieren, müssen Sie die Verschlüsselungseinstellungen des Speicherkontos aktualisieren, damit die neue Version verwendet wird. Rufen Sie zunächst [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) auf, um die neueste Version des Schlüssels abzurufen. Rufen Sie dann [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) auf, um die Verschlüsselungseinstellungen des Speicherkontos zu aktualisieren, damit wie im vorherigen Beispiel gezeigt die neue Version des Schlüssels verwendet wird.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um kundenseitig verwaltete Schlüssel mit der manuellen Aktualisierung der Schlüsselversion zu konfigurieren, geben Sie die Schlüsselversion explizit an, wenn Sie die Verschlüsselung für das Speicherkonto konfigurieren. Rufen Sie [az storage account update](/cli/azure/storage/account#az-storage-account-update) wie im folgenden Beispiel gezeigt auf, um die Verschlüsselungseinstellungen des Speicherkontos zu aktualisieren. Fügen Sie den Parameter `--encryption-key-source` ein, und legen Sie ihn auf `Microsoft.Keyvault` fest, um kundenseitig verwaltete Schlüssel für das Speicherkonto zu aktivieren.

Denken Sie daran, die Platzhalterwerte in den spitzen Klammern durch Ihre eigenen Werte zu ersetzen.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

Wenn Sie die Schlüsselversion manuell aktualisieren, müssen Sie die Verschlüsselungseinstellungen des Speicherkontos aktualisieren, damit die neue Version verwendet wird. Rufen Sie zunächst [az keyvault show](/cli/azure/keyvault#az-keyvault-show) zum Abfragen des Schlüsseltresor-URIs und [az keyvault key list-versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions) zum Abfragen der Schlüsselversion auf. Rufen Sie dann [az storage account update](/cli/azure/storage/account#az-storage-account-update) auf, um die Verschlüsselungseinstellungen des Speicherkontos zu aktualisieren, damit wie im vorherigen Beispiel gezeigt die neue Version des Schlüssels verwendet wird.

---

## <a name="change-the-key"></a>Ändern des Schlüssels

Sie können den Schlüssel, den Sie für die Azure Storage-Verschlüsselung verwenden, jederzeit ändern.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Führen Sie die folgenden Schritte aus, um den Schlüssel im Azure-Portal zu ändern:

1. Navigieren Sie zu Ihrem Speicherkonto, und zeigen Sie die Einstellungen zur **Verschlüsselung** an.
1. Wählen Sie den Schlüsseltresor und dann einen neuen Schlüssel aus.
1. Speichern Sie die Änderungen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Um den Schlüssel mit PowerShell zu ändern, rufen Sie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) wie unter [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln](#configure-encryption-with-customer-managed-keys) beschrieben auf, und geben Sie den neuen Schlüsselnamen und die neue Version an. Wenn sich der neue Schlüssel in einem anderen Schlüsseltresor befindet, müssen Sie auch den Schlüsseltresor-URI aktualisieren.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um den Schlüssel mit der Azure CLI zu ändern, rufen Sie [az storage account update](/cli/azure/storage/account#az-storage-account-update) wie unter [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln](#configure-encryption-with-customer-managed-keys) beschrieben auf, und geben Sie den neuen Schlüsselnamen und die neue Version an. Wenn sich der neue Schlüssel in einem anderen Schlüsseltresor befindet, müssen Sie auch den Schlüsseltresor-URI aktualisieren.

---

## <a name="revoke-customer-managed-keys"></a>Widerrufen von kundenseitig verwalteten Schlüsseln

Wenn Sie einen kundenseitig verwalteten Schlüssel widerrufen, wird die Zuordnung zwischen dem Speicherkonto und dem Schlüsseltresor entfernt.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Um kundenseitig verwaltete Schlüssel über das Azure-Portal zu widerrufen, deaktivieren Sie den Schlüssel, wie unter [Deaktivieren der vom Kunden verwalteten Schlüssel](#disable-customer-managed-keys) beschrieben.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Sie können kundenseitig verwaltete Schlüssel widerrufen, indem Sie die Schlüsseltresor-Zugriffsrichtlinie entfernen. Zum Widerrufen eines kundenseitig verwalteten Schlüssels mit PowerShell rufen Sie den Befehl [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) auf, wie im folgenden Beispiel gezeigt. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen und die in den vorherigen Beispielen definierten Variablen zu verwenden.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Sie können kundenseitig verwaltete Schlüssel widerrufen, indem Sie die Schlüsseltresor-Zugriffsrichtlinie entfernen. Zum Widerrufen eines kundenseitig verwalteten Schlüssels mit der Azure CLI rufen Sie den Befehl [az keyvault delete-policy](/cli/azure/keyvault#az-keyvault-delete-policy) auf, wie im folgenden Beispiel gezeigt. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen und die in den vorherigen Beispielen definierten Variablen zu verwenden.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

---

## <a name="disable-customer-managed-keys"></a>Deaktivieren von vom Kunden verwalteten Schlüsseln

Wenn Sie kundenseitig verwaltete Schlüssel deaktivieren, wird Ihr Speicherkonto wieder mit von Microsoft verwalteten Schlüsseln verschlüsselt.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Um kundenseitig verwaltete Schlüssel im Azure-Portal zu deaktivieren, gehen Sie folgendermaßen vor:

1. Navigieren Sie zu Ihrem Speicherkonto, und zeigen Sie die Einstellungen zur **Verschlüsselung** an.
1. Deaktivieren Sie das Kontrollkästchen neben der Einstellung **Eigenen Schlüssel verwenden**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Um kundenseitig verwaltete Schlüssel mit PowerShell zu deaktivieren, rufen Sie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) mit der Option `-StorageEncryption` auf, wie im folgenden Beispiel gezeigt. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen und die in den vorherigen Beispielen definierten Variablen zu verwenden.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um kundenseitig verwaltete Schlüssel mit der Azure CLI zu deaktivieren, rufen Sie [az storage account update](/cli/azure/storage/account#az-storage-account-update) auf, und legen Sie `--encryption-key-source parameter` auf `Microsoft.Storage` fest, wie im folgenden Beispiel gezeigt. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen und die in den vorherigen Beispielen definierten Variablen zu verwenden.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

---

## <a name="next-steps"></a>Nächste Schritte

- [Azure Storage encryption for data at rest (Azure Storage-Verschlüsselung für ruhende Daten)](storage-service-encryption.md)
- [Kundenseitig verwaltete Schlüssel für die Azure Storage-Verschlüsselung](customer-managed-keys-overview.md)
- [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault Managed HSM (Vorschau) gespeichert sind](customer-managed-keys-configure-key-vault-hsm.md)
