---
title: Verwalten von Speicherkontoschlüsseln mit Azure Key Vault und der Azure-Befehlszeilenschnittstelle
description: Speicherkontoschlüssel bieten nahtlose Integration zwischen Azure Key Vault und dem schlüsselbasiertem Zugriff auf ein Azure-Speicherkonto.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 91cc3f96f9cdd231c38232c972c2628d12b9f4b3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476154"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>Verwalten von Speicherkontoschlüsseln mit Azure Key Vault und der Azure-Befehlszeilenschnittstelle 

Azure Key Vault verwaltet Schlüssel für Azure-Speicherkonten und klassische Speicherkonten. Sie können das Feature für ein in Key Vault verwaltetes Speicherkonto verwenden, um verschiedene Schlüsselverwaltungsfunktionen für Sie auszuführen.

Ein [Azure-Speicherkonto](/azure/storage/storage-create-storage-account) verwendet Anmeldeinformationen, die sich aus einem Kontonamen und einem Schlüssel zusammensetzen. Der Schlüssel wird automatisch generiert und fungiert eher als ein Kennwort denn als ein kryptografischer Schlüssel. Key Vault verwaltet diese Speicherkontoschlüssel, indem sie als [Key Vault-Geheimnisse](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets) gespeichert werden. Schlüssel werden mit einem Azure-Speicherkonto aufgelistet (synchronisiert) und in regelmäßigen Abständen erneut generiert oder _rotiert_. 

Wenn Sie das Feature für verwaltete Speicherkontoschlüssel verwenden, sollten Sie folgende Punkte beachten:

- Schlüsselwerte werden nie als Antwort an einen Aufrufer zurückgegeben.
- Ihre Speicherkontoschlüssel sollten nur durch Key Vault verwaltet werden. Verwalten Sie die Schlüssel nicht selbst, und vermeiden Sie es, die Key Vault-Prozesse zu beeinträchtigen.
- Speicherkontoschlüssel sollten nur von einem einzigen Key Vault-Objekt verwaltet werden. Lassen Sie es nicht zu, dass die Schlüssel aus mehreren Objekten verwaltet werden.
- Sie können Key Vault anweisen, Ihr Speicherkonto mit einem Benutzerprinzipal zu verwalten, aber nicht mit einem Dienstprinzipal.
- Generieren Sie Schlüssel nur mit Key Vault neu. Generieren Sie Ihre Speicherkontoschlüssel nicht manuell neu. 

> [!NOTE]
> Azure Storage-Integration in Azure Active Directory (Azure AD) ist der cloudbasierte Identitäts- und Zugriffsverwaltungsdienst von Microsoft.
> Azure AD-Integration ist für [Azure-Blobs und -Warteschlangen](https://docs.microsoft.com/azure/storage/common/storage-auth-aad) verfügbar.
> Verwenden Sie Azure AD für Authentifizierung und Autorisierung.
> Azure AD bietet auf OAuth2-Token basierenden Zugriff auf Azure Storage, so wie dies für Azure Key Vault der Fall ist.
>
> Azure AD ermöglicht es Ihnen, Ihre Clientanwendung zu authentifizieren, indem Sie eine Anwendungs- oder Benutzeridentität anstelle von Speicherkontoanmeldeinformationen verwenden.
> Sie können eine [von Azure AD verwaltete Identität](/azure/active-directory/managed-identities-azure-resources/) verwenden, wenn Sie Ihre Clientanwendung in Azure ausführen. Verwaltete Identitäten machen die Clientauthentifizierung und das Speichern von Anmeldeinformationen in oder mit Ihrer Anwendung überflüssig.
> Azure AD verwendet rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC), um die Autorisierung zu verwalten. Dies wird auch von Key Vault unterstützt.

### <a name="service-principal-application-id"></a>Dienstprinzipal-Anwendungs-ID

Ein Azure AD-Mandant stellt jede registrierte Anwendung mit einem [Dienstprinzipal](/azure/active-directory/develop/developer-glossary#service-principal-object) bereit. Der Dienstprinzipal fungiert als Anwendungsidentität (ID). Die Anwendungs-ID wird während der Autorisierungseinrichtung verwendet, um über RBAC auf andere Azure-Ressourcen zuzugreifen.

Key Vault ist eine Microsoft-Anwendung, die in allen Azure AD-Mandanten vorab registriert ist. Key Vault wird unter derselben Anwendungs-ID und in jeder Azure-Cloud registriert.

| Mandanten | Cloud | Anwendungs-ID |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure, öffentlich | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Andere  | Beliebig | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie Ihren Speicherkontoschlüssel mit Key Vault verwalten, überprüfen Sie die Voraussetzungen:

- Installieren Sie die [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
- Erstellen Sie ein [Azure-Speicherkonto](https://azure.microsoft.com/services/storage/). Führen Sie [diese Schritte](https://docs.microsoft.com/azure/storage/) aus.
- Der Speicherkontoname darf nur aus Kleinbuchstaben und Zahlen bestehen. Der Name muss zwischen 3 und 24 Zeichen lang sein.        
      
## <a name="manage-storage-account-keys"></a>Verwalten von Speicherkontoschlüsseln

Es gibt vier grundlegende Schritte, nach denen Speicherkontoschlüssel mit Key Vault verwaltet werden können:

1. Besorgen Sie sich Zugriff auf ein vorhandenes Speicherkonto.
1. Rufen Sie einen vorhandenen Schlüsseltresor ab.
1. Fügen Sie dem Tresor ein von Key Vault verwaltetes Speicherkonto hinzu. Legen Sie `key1` als aktiven Schlüssel mit einem Regenerierungszeitraum von 180 Tagen fest.
1. Verwenden Sie `key1`, um einen Speicherkontext für das angegebene Speicherkonto festzulegen.

> [!NOTE]
> Key Vault als Dienst werden Bedienerberechtigungen für Ihr Speicherkonto zugewiesen.
> 
> Nachdem Sie von Azure Key Vault verwaltete Speicherkontoschlüssel eingerichtet haben, ändern Sie die Schlüssel nur über Key Vault.
> Für verwaltete Speicherkontoschlüssel verwaltet Key Vault die Rotation des Speicherkontoschlüssels.

1. Führen Sie, nachdem Sie ein Speicherkonto erstellt haben, den folgenden Befehl aus, um die Ressourcen-ID des zu verwaltenden Speicherkontos abzurufen:
    ```
    az storage account show -n storageaccountname
    ```

    Kopieren Sie den Ressourcen-ID-Wert aus der Ausgabe des Befehls:
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    Beispielausgabe:
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. Weisen Sie Key Vault die RBAC-Rolle „Dienstrolle "Speicherkonto-Schlüsseloperator"“ (Storage Account Key Operator Service Role) zu. Diese Rolle beschränkt den Zugriffsbereich auf Ihr Speicherkonto. Für ein klassisches Speicherkonto verwenden Sie die Rolle „Klassische Dienstrolle "Speicherkonto-Schlüsseloperator"“ (Classic Storage Account Key Operator Service Role).

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope 93c27d83-f79b-4cb2-8dd4-4aa716542e74
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74` ist die Objekt-ID für Key Vault in der öffentlichen Azure-Cloud. Informationen, wie die Objekt-ID für Key Vault in der Azure Government-Cloud abgerufen wird, finden Sie unter [Dienstprinzipal-Anwendungs-ID](#service-principal-application-id).
    
1. Erstellen Sie ein per Key Vault verwaltetes Speicherkonto:

    Legen Sie einen Regenerationszeitraum von 90 Tagen fest. Nach 90 Tagen wird `key1` von Key Vault neu generiert, und Key Vault tauscht den aktiven Schlüssel von `key2` in `key1`. `key1` ist dann als der aktive Schlüssel markiert. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>Erstellen von Tokens

Sie können Key Vault auch anweisen, SAS-Tokens (Shared Access Signature) zu erstellen. Shared Access Signatures bieten delegierten Zugriff auf Ressourcen in Ihrem Speicherkonto. Sie können Clients Zugriff auf Ressourcen unter Ihrem Speicherkonto gewähren, ohne dafür Ihre Kontoschlüssel freigeben zu müssen. Eine SAS (Shared Access Signature) bietet Ihnen eine sichere Methode zur Freigabe Ihrer Speicherressourcen, ohne Ihre Kontoschlüssel zu gefährden.

Die Befehle in diesem Abschnitt führen Sie die folgenden Aktionen:

- Festlegen der SAS-Definition `<YourSASDefinitionName>` für ein Konto. Die Definition wird für das von Key Vault verwaltete Speicherkonto `<YourStorageAccountName>` in Ihrem Schlüsseltresor `<VaultName>` festgelegt.
- Erstellen eines SAS-Tokens für das Konto für Blob-, Datei-, Tabellen- und Warteschlangendienst. Das Token wird für die Ressourcentypen „Dienst“, „Container“ und „Objekt“ erstellt. Das Token wird über HTTPS mit allen Berechtigungen und mit dem angegebenen Start- und Enddatum erstellt.
- Festlegen einer SAS-Definition für von Key Vault verwalteten Speicher im Tresor. Die Definition hat den Vorlagen-URI des SAS-Tokens, das erstellt wurde. Die Definition hat den SAS-Typ `account` und ist für N Tage gültig.
- Rufen Sie das tatsächliche Zugriffstoken aus dem Key Vault-Geheimnis ab, das der SAS-Definition entspricht.

Nachdem Sie die Schritte im vorherigen Abschnitt abgeschlossen haben, führen Sie die folgenden Befehle aus, um Key Vault anzuweisen, SAS-Tokens zu generieren. 

1. Erstellen Sie eine SAS-Definition. Nachdem die SAS-Definition erstellt ist, weisen Sie Key Vault an, weitere SAS-Tokens zu generieren. Dieser Vorgang erfordert die Berechtigungen `storage` und `setsas`.
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    Informationen zu diesem Vorgang finden Sie in der Referenzdokumentation zu [az storage account generate-sas](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas).

    Nachdem der Vorgang erfolgreich ausgeführt wurde, kopieren Sie die Ausgabe.
    ```console
       "se=2020-01-01&sp=***"
    ```

1. Verwenden Sie das im vorherigen Befehl generierte `$sasToken`, und erstellen Sie eine SAS-Definition. Weitere Informationen zu den Parametern finden Sie in der Referenzdokumentation zu [az keyvault storage sas-definition create](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters).
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    Hat der Benutzer keine Berechtigungen für das Speicherkonto, rufen Sie zuerst die Objekt-ID des Benutzers ab:
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>Abrufen von Tokens in Code

Führen Sie Vorgänge für Ihr Speicherkonto aus, indem Sie [Shared Access Signature-Tokens](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS-Tokens) aus Key Vault abrufen.

Es gibt drei Möglichkeiten für die Authentifizierung bei Key Vault:

- Verwenden Sie eine verwaltete Dienstidentität. Dieser Ansatz wird unbedingt empfohlen.
- Verwenden Sie einen Dienstprinzipal und ein Zertifikat. 
- Verwenden Sie einen Dienstprinzipal und ein Kennwort. Dieser Ansatz wird nicht empfohlen.

Weitere Informationen finden Sie unter [Azure-Schlüsseltresor: Grundlegende Konzepte](key-vault-whatis.md#basic-concepts).

Im folgenden Beispiel wird veranschaulicht, wie SAS-Tokens abgerufen werden. Sie rufen die Tokens ab, nachdem Sie eine SAS-Definition erstellt haben. 

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Sollte Ihr SAS-Token bald ablaufen, rufen Sie das SAS-Token erneut aus Key Vault ab, und aktualisieren Sie den Code.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Azure-CLI-Befehle

Weitere Informationen über die Azure CLI-Befehle, die für verwaltete Speicherkonten relevant sind, finden Sie in der Referenzdokumentation von [az keyvault storage](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Schlüssel, Geheimnisse und Zertifikate](https://docs.microsoft.com/rest/api/keyvault/).
- Lesen Sie Artikel im [Azure Key Vault Team Blog](https://blogs.technet.microsoft.com/kv/).
