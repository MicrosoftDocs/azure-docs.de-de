---
title: Mit Azure Key Vault verwaltetes Speicherkonto – PowerShell-Version
description: Das Feature für verwaltete Speicherkonten bietet eine nahtlose Integration zwischen Azure Key Vault und einem Azure-Speicherkonto.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 9b6089aa828b5667f100c1a8cbff3e69345e4512
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66150421"
---
# <a name="azure-key-vault-managed-storage-account---powershell"></a>Mit Azure Key Vault verwaltetes Speicherkonto – PowerShell

> [!NOTE]
> [Die Azure-Speicherintegration in Azure Active Directory (Azure AD) befindet sich jetzt in der Vorschau](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Wir empfehlen die Verwendung von Azure AD für die Authentifizierung und Autorisierung, da es wie Azure Key Vault einen auf OAuth2-Token basierenden Zugriff auf Azure-Speicher bietet. Dies ermöglicht Ihnen Folgendes:
> - Authentifizieren Sie Ihre Clientanwendung mithilfe einer Anwendungs- oder Benutzeridentität, statt Speicherkontoanmeldeinformationen angeben zu müssen. 
> - Verwenden Sie für die Ausführung in Azure eine [von Azure AD verwaltete Identität](/azure/active-directory/managed-identities-azure-resources/). Verwaltete Identitäten machen die Clientauthentifizierung und das Speichern von Anmeldeinformationen in oder mit Ihrer Anwendung überflüssig.
> - Verwenden Sie die rollenbasierte Zugriffssteuerung (RBAC) zum Verwalten der Autorisierung (wird auch von Key Vault unterstützt).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ein [Azure-Speicherkonto](/azure/storage/storage-create-storage-account) verwendet Anmeldeinformationen, die sich aus einem Kontonamen und einem Schlüssel zusammensetzen. Der Schlüssel wird automatisch generiert und dient eher als „Kennwort“ denn als kryptografischer Schlüssel. Key Vault kann diese Speicherkontoschlüssel verwalten, indem sie als [Key Vault-Geheimnisse](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets) gespeichert werden. 

## <a name="overview"></a>Übersicht

Das Feature für ein in Key Vault verwaltetes Speicherkonto führt verschiedene Verwaltungsfunktionen in Ihrem Auftrag aus:

- Auflisten (Synchronisieren) der Schlüssel mit einem Azure-Speicherkonto
- Erneutes Generieren (Rotieren) der Schlüssel in regelmäßigen Abständen
- Verwalten der Schlüssel sowohl für Speicherkonten als auch für klassische Speicherkonten
- Schlüsselwerte werden nie als Antwort an den Aufrufer zurückgegeben.

Beachten Sie bei Verwendung des Features für verwaltete Speicherkontoschlüssel Folgendes:

- **Lassen Sie Ihre Speicherkontoschlüssel nur durch Key Vault verwalten.** Versuchen Sie nicht, die Schlüssel selbst zu verwalten, da dies zu Konflikten mit den Key Vault-Prozessen führt.
- **Lassen Sie Speicherkontoschlüssel nicht durch mehrere Key Vault-Objekte verwalten.**
- **Generieren Sie Ihre Speicherkontoschlüssel nicht manuell neu.** Es wird empfohlen, die Schlüssel über Key Vault neu zu generieren.

Das folgende Beispiel zeigt, wie Sie Key Vault das Verwalten Ihrer Speicherkontoschlüssel ermöglichen.

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>Autorisieren von Key Vault für den Zugriff auf Ihr Speicherkonto

> [!IMPORTANT]
> Ein Azure AD-Mandant stellt jede registrierte Anwendung mit einem **[Dienstprinzipal](/azure/active-directory/develop/developer-glossary#service-principal-object)** bereit, der als Anwendungsidentität fungiert. Anhand der Anwendungs-ID des Dienstprinzipals kann über die rollenbasierte Zugriffssteuerung (RBAC) Zugriff auf andere Ressourcen erteilt werden. Key Vault ist eine Microsoft-Anwendung, deshalb ist sie in allen Azure AD-Mandanten über dieselbe Anwendungs-ID in jeder Azure-Cloud vorab registriert:
> - Azure AD-Mandanten in der Azure Government-Cloud verwenden die Anwendungs-ID `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - Azure AD-Mandanten in der öffentlichen Azure-Cloud und allen weiteren verwenden die Anwendungs-ID `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

Damit Key Vault auf Ihre Speicherkontoschlüssel zugreifen und diese verwalten kann, müssen Sie Key Vault zum Zugriff auf Ihr Speicherkonto autorisieren. Die Key Vault-Anwendung benötigt die Berechtigungen zum *Auflisten* und *Regenerieren* von Schlüsseln für Ihr Speicherkonto. Diese Berechtigungen werden über die integrierte RBAC-Dienstrolle [Speicherkonto-Schlüsseloperator](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role) aktiviert. 

Führen Sie die folgenden Schritte aus, um dem Key Vault-Dienstprinzipal diese Rolle zuzuweisen und den Bereich auf Ihr Speicherkonto einzuschränken. Achten Sie darauf, vor dem Ausführen des Skripts die Variablen `$resourceGroupName`, `$storageAccountName`, `$storageAccountKey` und `$keyVaultName` zu aktualisieren:

```azurepowershell-interactive
# TODO: Update with the resource group where your storage account resides, your storage account name, the name of your active storage account key, and your Key Vault instance name
$resourceGroupName = "rgContoso"
$storageAccountName = "sacontoso"
$storageAccountKey = "key1"
$keyVaultName = "kvContoso"
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093" # See "IMPORTANT" block above for information on Key Vault Application IDs

# Authenticate your PowerShell session with Azure AD, for use with Azure Resource Manager cmdlets
$azureProfile = Connect-AzAccount

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Nach erfolgreicher Rollenzuweisung sollten eine Ausgabe ähnlich wie im folgenden Beispiel angezeigt werden:

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

Wenn Key Vault bereits der Rolle für Ihr Speicherkonto zugewiesen wurde, wird die Fehlermeldung *„Die Rollenzuweisung ist bereits vorhanden.“* angezeigt. Sie können die Rollenzuweisung auch im Azure-Portal über die Seite „Zugriffssteuerung (IAM)“ für das Speicherkonto überprüfen.  

## <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Gewähren von Benutzerkontoberechtigungen für verwaltete Speicherkonten

>[!TIP] 
> Ebenso wie Azure AD einen **Dienstprinzipal** für eine Anwendungsidentität bereitstellt, wird ein **Benutzerprinzipal** für eine Benutzeridentität bereitgestellt. Der Benutzerprinzipal kann anschließend über Key Vault-Zugriffsrichtlinienberechtigungen zum Zugriff auf Key Vault autorisiert werden.

Aktualisieren Sie in derselben PowerShell-Sitzung die Key Vault-Zugriffsrichtlinie für verwaltete Speicherkonten. Durch diesen Schritt werden Speicherkontoberechtigungen für Ihr Benutzerkonto angewendet, damit sichergestellt ist, dass Sie auf das Feature für verwaltete Speicherkonten zugreifen können: 

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $azureProfile.Context.Account.Id -PermissionsToStorage get, list, listsas, delete, set, update, regeneratekey, recover, backup, restore, purge
```

Beachten Sie, dass im Azure-Portal auf der Seite „Zugriffsrichtlinien“ für das Speicherkonto keine Berechtigungen für Speicherkonten verfügbar sind.

## <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Hinzufügen eines verwalteten Speicherkontos zu Ihrer Key Vault-Instanz

Erstellen Sie in derselben PowerShell-Sitzung ein verwaltetes Speicherkonto in Ihrer Key Vault-Instanz. Mit dem Schalter `-DisableAutoRegenerateKey` wird angegeben, dass die Speicherkontoschlüssel NICHT neu generiert werden.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Nach dem erfolgreichen Hinzufügen des Speicherkontos ohne erneute Generierung der Schlüssel sollte eine ähnliche Ausgabe wie die im folgenden Beispiel angezeigt werden:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

### <a name="enable-key-regeneration"></a>Aktivieren der erneuten Schlüsselgenerierung

Wenn Sie möchten, dass Key Vault die Speicherkontoschlüssel regelmäßig neu generiert, können Sie einen Zeitraum für die erneute Generierung festlegen. Im folgenden Beispiel wird der Zeitraum auf drei Tage festgelegt. Nach 3 Tagen generiert Key Vault „key1“ neu und wechselt für den aktiven Schlüssel von „key2“ zu „key1“.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Nach dem erfolgreichen Hinzufügen des Speicherkontos mit erneuter Generierung der Schlüssel sollte eine ähnliche Ausgabe wie die im folgenden Beispiel angezeigt werden:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

## <a name="next-steps"></a>Nächste Schritte

- [Beispiele für verwaltete Speicherkontoschlüssel](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](about-keys-secrets-and-certificates.md)
- [PowerShell-Referenz zu Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
