---
title: Verwalten von Speicherkontoschlüsseln mit Azure Key Vault und der Azure-Befehlszeilenschnittstelle
description: Speicherkontoschlüssel bieten nahtlose Integration zwischen Azure Key Vault und dem schlüsselbasiertem Zugriff auf ein Azure-Speicherkonto.
ms.topic: tutorial
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8473d3a19a86027b5b01af59d24833dc40cd1fe9
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95242354"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Verwalten von Speicherkontoschlüsseln mit Key Vault und der Azure-Befehlszeilenschnittstelle
> [!IMPORTANT]
> Wir empfehlen die Verwendung der Azure Storage-Integration in Azure Active Directory (Azure AD), dem cloudbasierten Identitäts- und Zugriffsverwaltungsdienst von Microsoft. Die Azure AD-Integration ist für [Azure-Blobs und -Warteschlangen](../../storage/common/storage-auth-aad.md) verfügbar und bietet tokenbasierten OAuth2-Zugriff auf Azure Storage (genau wie Azure Key Vault). Azure AD ermöglicht es Ihnen, Ihre Clientanwendung zu authentifizieren, indem Sie eine Anwendungs- oder Benutzeridentität anstelle von Speicherkontoanmeldeinformationen verwenden. Sie können eine [von Azure AD verwaltete Identität](../../active-directory/managed-identities-azure-resources/index.yml) verwenden, wenn Sie Ihre Clientanwendung in Azure ausführen. Verwaltete Identitäten machen die Clientauthentifizierung und das Speichern von Anmeldeinformationen in oder mit Ihrer Anwendung überflüssig. Verwenden Sie die folgende Lösung nur, wenn keine Azure AD Authentifizierung möglich ist.

Ein Azure-Speicherkonto verwendet Anmeldeinformationen, die sich aus einem Kontonamen und einem Schlüssel zusammensetzen. Der Schlüssel wird automatisch generiert und fungiert eher als ein Kennwort denn als ein kryptografischer Schlüssel. Key Vault verwaltet Speicherkontoschlüssel, indem sie im Speicherkonto regelmäßig neu generiert werden, und stellt SAS-Token für den delegierten Zugriff auf Ressourcen in Ihrem Speicherkonto zur Verfügung.

Sie können das Key Vault-Feature für verwaltete Speicherkontoschlüssel verwenden, um Schlüssel für ein Azure Storage-Konto aufzulisten (synchronisieren) und die Schlüssel in regelmäßigen Abständen erneut zu generieren (rotieren). Sie können Schlüssel sowohl für Speicherkonten als auch für klassische Speicherkonten verwalten.

Wenn Sie das Feature für verwaltete Speicherkontoschlüssel verwenden, sollten Sie folgende Punkte beachten:

- Schlüsselwerte werden nie als Antwort an einen Aufrufer zurückgegeben.
- Ihre Speicherkontoschlüssel sollten nur durch Key Vault verwaltet werden. Verwalten Sie die Schlüssel nicht selbst, und vermeiden Sie es, die Key Vault-Prozesse zu beeinträchtigen.
- Speicherkontoschlüssel sollten nur von einem einzigen Key Vault-Objekt verwaltet werden. Lassen Sie es nicht zu, dass die Schlüssel aus mehreren Objekten verwaltet werden.
- Generieren Sie Schlüssel nur mit Key Vault neu. Generieren Sie Ihre Speicherkontoschlüssel nicht manuell neu.

## <a name="service-principal-application-id"></a>Dienstprinzipal-Anwendungs-ID

Ein Azure AD-Mandant stellt jede registrierte Anwendung mit einem [Dienstprinzipal](../../active-directory/develop/developer-glossary.md#service-principal-object) bereit. Der Dienstprinzipal dient als die Anwendungs-ID, die während der Autorisierungseinrichtung verwendet wird, um über Azure RBAC auf andere Azure-Ressourcen zuzugreifen.

Key Vault ist eine Microsoft-Anwendung, die in allen Azure AD-Mandanten vorab registriert ist. Key Vault wird unter derselben Anwendungs-ID in jeder Azure-Cloud registriert.

| Mandanten | Cloud | Anwendungs-ID |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure, öffentlich | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Andere  | Any | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Voraussetzungen

Für diesen Leitfaden müssen Sie zunächst Folgendes ausführen:

- [Installieren Sie die Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).
- [Erstellen eines Schlüsseltresors](quick-create-cli.md)
- [Erstellen Sie ein Azure-Speicherkonto](../../storage/common/storage-account-create.md?tabs=azure-cli). Der Speicherkontoname darf nur aus Kleinbuchstaben und Zahlen bestehen. Der Name muss zwischen 3 und 24 Zeichen lang sein.
      
## <a name="manage-storage-account-keys"></a>Verwalten von Speicherkontoschlüsseln

### <a name="connect-to-your-azure-account"></a>Herstellen einer Verbindung mit Ihrem Azure-Konto

Authentifizieren Sie Ihre Sitzung der Azure-Befehlszeilenschnittstelle mit den [az login](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)-Befehlen.

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Gewähren von Zugriff auf Ihr Speicherkonto für Key Vault

Verwenden Sie den Befehl [az role assignment create](/cli/azure/role/assignment) der Azure-Befehlszeilenschnittstelle, um Key Vault Zugriff auf Ihr Speicherkonto zu gewähren. Stellen Sie dem Befehl die folgenden Parameterwerte zur Verfügung:

- `--role`: Übergeben Sie die Azure-Rolle „Dienstrolle 'Speicherkonto-Schlüsseloperator'“. Diese Rolle beschränkt den Zugriffsbereich auf Ihr Speicherkonto. Für ein klassisches Speicherkonto übergeben Sie stattdessen die Rolle „Klassische Dienstrolle 'Speicherkonto-Schlüsseloperator'“.
- `--assignee`: Übergeben Sie den Wert „https://vault.azure.net“, bei dem es sich um die URL für die Key Vault-Instanz in der öffentlichen Azure-Cloud handelt. (Für die Azure Government-Cloud verwenden Sie stattdessen „--asingee-object-id“. Weitere Informationen finden Sie unter [Dienstprinzipal-Anwendungs-ID](#service-principal-application-id).)
- `--scope`: Übergeben Sie Ihre Ressourcen-ID des Speicherkonto, die in der Form `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` vorliegt. Um Ihre Abonnement-ID zu finden, verwenden Sie den Befehl [az account list](/cli/azure/account?#az-account-list) der Azure-Befehlszeilenschnittstelle. Verwenden Sie den Befehl [az storage account list](/cli/azure/storage/account?#az-storage-account-list) der Azure-Befehlszeilenschnittstelle, um Ihren Namen und die Ressourcengruppe für das Speicherkonto zu finden.

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Gewähren von Benutzerkontoberechtigungen für verwaltete Speicherkonten

Verwenden Sie das Azure CLI-Cmdlet [az keyvault-set-policy](/cli/azure/keyvault?#az-keyvault-set-policy), um die Key Vault-Zugriffsrichtlinie zu aktualisieren und Ihrem Benutzerkonto Speicherkontoberechtigungen zu gewähren.

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

Beachten Sie, dass im Azure-Portal auf der Seite „Zugriffsrichtlinien“ für das Speicherkonto keine Berechtigungen für Speicherkonten verfügbar sind.
### <a name="create-a-key-vault-managed-storage-account"></a>Erstellen eines per Key Vault verwalteten Speicherkontos

 Erstellen Sie ein von Key Vault verwaltetes Speicherkonto mit dem Befehl [az keyvault storage](/cli/azure/keyvault/storage?#az-keyvault-storage-add) der Azure-Befehlszeilenschnittstelle. Legen Sie einen Regenerationszeitraum von 90 Tagen fest. Wenn es Zeit für die Rotation ist, generiert Key Vault den inaktiven Schlüssel erneut und aktiviert den neu erstellten Schlüssel. Nur einer der Schlüssel wird verwendet, um SAS-Token zu einem beliebigen Zeitpunkt auszustellen; dies ist der aktive Schlüssel. Stellen Sie dem Befehl die folgenden Parameterwerte zur Verfügung:

- `--vault-name`: Übergeben Sie den Namen Ihres Schlüsseltresors. Um den Namen Ihres Schlüsselspeichers zu finden, verwenden Sie den Befehl [az keyvault list](/cli/azure/keyvault?#az-keyvault-list) der Azure-Befehlszeilenschnittstelle.
- `-n`: Übergeben Sie den Namen Ihres Speicherkontos. Verwenden Sie den Befehl [az storage account list](/cli/azure/storage/account?#az-storage-account-list) der Azure-Befehlszeilenschnittstelle, um den Namen Ihres Speicherkonto zu finden.
- `--resource-id`: Übergeben Sie Ihre Ressourcen-ID des Speicherkonto, die in der Form `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` vorliegt. Um Ihre Abonnement-ID zu finden, verwenden Sie den Befehl [az account list](/cli/azure/account?#az-account-list) der Azure-Befehlszeilenschnittstelle. Verwenden Sie den Befehl [az storage account list](/cli/azure/storage/account?#az-storage-account-list) der Azure-Befehlszeilenschnittstelle, um Ihren Namen und die Ressourcengruppe für das Speicherkonto zu finden.
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>SAS-Token (Shared Access Signature)

Sie können Key Vault auch anweisen, SAS-Tokens (Shared Access Signature) zu erstellen. Shared Access Signatures bieten delegierten Zugriff auf Ressourcen in Ihrem Speicherkonto. Sie können Clients Zugriff auf Ressourcen unter Ihrem Speicherkonto gewähren, ohne dafür Ihre Kontoschlüssel freigeben zu müssen. Eine SAS (Shared Access Signature) bietet Ihnen eine sichere Methode zur Freigabe Ihrer Speicherressourcen, ohne Ihre Kontoschlüssel zu gefährden.

Die Befehle in diesem Abschnitt führen Sie die folgenden Aktionen:

- Festlegen der SAS-Definition `<YourSASDefinitionName>` für ein Konto. Die Definition wird für das von Key Vault verwaltete Speicherkonto `<YourStorageAccountName>` in Ihrem Schlüsseltresor `<YourKeyVaultName>` festgelegt.
- Erstellen eines SAS-Tokens für das Konto für Blob-, Datei-, Tabellen- und Warteschlangendienst. Das Token wird für die Ressourcentypen „Dienst“, „Container“ und „Objekt“ erstellt. Das Token wird über HTTPS mit allen Berechtigungen und mit dem angegebenen Start- und Enddatum erstellt.
- Festlegen einer SAS-Definition für von Key Vault verwalteten Speicher im Tresor. Die Definition hat den Vorlagen-URI des SAS-Tokens, das erstellt wurde. Die Definition hat den SAS-Typ `account` und ist für N Tage gültig.
- Vergewissern Sie sich, dass die SAS (Shared Access Signature) in Ihrem Schlüsseltresor als Geheimnis gespeichert wurde.

### <a name="create-a-shared-access-signature-token"></a>Erstellen eines SAS-Tokens

Erstellen Sie eine SAS-Definition mit dem Befehl [az storage account generatee-sas](/cli/azure/storage/account?#az-storage-account-generate-sas) der Azure-Befehlszeilenschnittstelle. Dieser Vorgang erfordert die Berechtigungen `storage` und `setsas`.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
Nachdem der Vorgang erfolgreich ausgeführt wurde, kopieren Sie die Ausgabe.

```console
"se=2020-01-01&sp=***"
```

Diese Ausgabe wird im nächsten Schritt an den Parameter `--template-uri` übergeben.

### <a name="generate-a-shared-access-signature-definition"></a>Generieren einer SAS-Definition

Verwenden Sie den Befehl [az keyvault storage sas-definition create](/cli/azure/keyvault/storage/sas-definition?#az-keyvault-storage-sas-definition-create) der Azure-Befehlszeilenschnittstelle, indem Sie die Ausgabe des vorherigen Schritts an den Parameter `--template-uri` übergeben, um eine SAS-Definition zu erstellen.  Sie können einen Namen Ihrer Wahl für den Parameter `-n` angeben.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Überprüfen der SAS-Definition

Mithilfe des Befehls [az keyvault storage sas-definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) der Azure CLI können Sie überprüfen, ob die SAS-Definition in Ihrem Schlüsseltresor gespeichert wurde.

Sie können jetzt den Befehl [az keyvault storage sas-definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) und die Eigenschaft `id` verwenden, um den Inhalt dieses Geheimnisses anzuzeigen.

```azurecli-interactive
az keyvault storage sas-definition show --id https://<YourKeyVaultName>.vault.azure.net/storage/<YourStorageAccountName>/sas/<YourSASDefinitionName>
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Schlüssel, Geheimnisse und Zertifikate](/rest/api/keyvault/).
- Lesen Sie Artikel im [Azure Key Vault Team Blog](/archive/blogs/kv/).
- Weitere Informationen finden Sie in der Referenzdokumentation zu [az keyvault storage](/cli/azure/keyvault/storage).
