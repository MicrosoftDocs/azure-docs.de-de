---
title: Verschlüsseln der Registrierung mithilfe eines kundenseitig verwalteten Schlüssels
description: Erfahren Sie mehr über die Verschlüsselung ruhender Daten Ihrer Azure Container Registry und wie Sie Ihre Premium-Registrierung mit einem kundenseitig verwalteten Schlüssel verschlüsseln, der in Azure Key Vault gespeichert ist.
ms.topic: article
ms.date: 12/03/2020
ms.custom: ''
ms.openlocfilehash: 708a42a4f965f484060d42d89ea4f535c4365a10
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620439"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Verschlüsseln der Registrierung mithilfe eines kundenseitig verwalteten Schlüssels

Wenn Sie Images und andere Artefakte in einer Azure Container Registry speichern, verschlüsselt Azure automatisch den ruhenden Registrierungsinhalt mit [dienstseitig verwalteten Schlüsseln](../security/fundamentals/encryption-models.md). Sie können die Standardverschlüsselung durch eine zusätzliche Verschlüsselungsebene ergänzen, indem Sie einen Schlüssel verwenden, den Sie in Azure Key Vault erstellen und verwalten (kundenseitig verwalteter Schlüssel). Dieser Artikel führt Sie Schritt für Schritt durch die Verwendung der Azure CLI und des Azure-Portals.

Die serverseitige Verschlüsselung mit kundenseitig verwalteten Schlüsseln wird durch die Integration mit [Azure Key Vault](../key-vault/general/overview.md) unterstützt: 

* Sie können Ihre eigenen Verschlüsselungsschlüssel erstellen und in einem Schlüsseltresor speichern oder mit Azure Key Vault-APIs Schlüssel generieren. 
* Mit Azure Key Vault können Sie auch die Schlüsselverwendung überwachen.
* Azure Container Registry unterstützt die automatische Rotation von Registrierungsverschlüsselungsschlüsseln, wenn eine neue Schlüsselversion in Azure Key Vault verfügbar ist. Sie können Registrierungsverschlüsselungsschlüssel auch manuell rotieren.

Diese Funktion ist auf der Dienstebene **Premium** der Containerregistrierung verfügbar. Informationen zu den Tarifen des Registrierungsdiensts und zu den Einschränkungen finden Sie unter [Azure Container Registry-Tarife](container-registry-skus.md).


## <a name="things-to-know"></a>Wichtige Hinweise

* Sie können einen benutzerseitig verwalteten Schlüssel zurzeit nur aktivieren, wenn Sie eine Registrierung erstellen. Beim Aktivieren des Schlüssels konfigurieren Sie eine *benutzerseitig zugewiesene* verwaltete Identität für den Zugriff auf den Schlüsseltresor.
* Nachdem die Verschlüsselung mit einem kundenseitig verwalteten Schlüssel in einer Registrierung aktiviert wurde, können Sie die Verschlüsselung nicht mehr deaktivieren.  
* Azure Container Registry unterstützt nur RSA- oder RSA-HSM-Schlüssel. Elliptic Curve-Schlüssel werden derzeit nicht unterstützt.
* Die [Inhaltsvertrauensstellung](container-registry-content-trust.md) wird derzeit nicht in einer Registrierung unterstützt, die mit einem kundenseitig verwalteten Schlüssel verschlüsselt ist.
* Führen Sie in einer Registrierung, die mit einem kundenseitig verwalteten Schlüssel verschlüsselt ist, Protokolle für [ACR Tasks](container-registry-tasks-overview.md) aus, die derzeit nur für 24 Stunden aufbewahrt werden. Wenn Sie Protokolle für einen längeren Zeitraum aufbewahren müssen, finden Sie weitere Informationen in dieser Anleitung zum [Exportieren und Speichern von Task-Ausführungsprotokollen](container-registry-tasks-logs.md#alternative-log-storage).


> [!NOTE]
> Wenn der Zugriff auf Ihren Azure-Schlüsseltresor mithilfe eines virtuellen Netzwerks mit einer [Key Vault-Firewall](../key-vault/general/network-security.md) eingeschränkt ist, sind zusätzliche Konfigurationsschritte erforderlich. Nachdem Sie die Registrierung erstellt und den kundenseitig verwalteten Schlüssel aktiviert haben, richten Sie den Zugriff auf den Schlüssel mithilfe der *systemseitig zugewiesenen* verwalteten Identität der Registrierung ein, und konfigurieren Sie die Registrierung so, dass die Key Vault-Firewall umgangen wird. Führen Sie die Schritte in diesem Artikel zuerst aus, um die Verschlüsselung mit einem kundenseitig verwalteten Schlüssel zu aktivieren, und sehen Sie sich dann die Anleitung für [Erweitertes Szenario: Key Vault-Firewall](#advanced-scenario-key-vault-firewall) weiter unten in diesem Artikel an.

## <a name="automatic-or-manual-update-of-key-versions"></a>Automatisches oder manuelles Aktualisieren von Schlüsselversionen

Ein wichtiger Aspekt für die Sicherheit einer Registrierung, die mit einem kundenseitig verwalteten Schlüssel verschlüsselt ist, betrifft die Häufigkeit, mit der Sie den Verschlüsselungsschlüssel aktualisieren (rotieren). Ihre Organisation hat möglicherweise Compliancerichtlinien definiert, die eine regelmäßige Aktualisierung der [Versionen](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) der Schlüssel erfordern, die in Azure Key Vault gespeichert sind, wenn sie als kundenseitig verwaltete Schlüssel verwendet werden. 

Wenn Sie die Registrierungsverschlüsselung mit einem kundenseitig verwalteten Schlüssel konfigurieren, haben Sie zwei Möglichkeiten, die zur Verschlüsselung verwendete Schlüsselversion zu aktualisieren:

* **Automatisches Aktualisieren der Schlüsselversion:** Um einen kundenseitig verwalteten Schlüssel automatisch zu aktualisieren, wenn eine neue Version in Azure Key Vault verfügbar ist, lassen Sie die Schlüsselversion weg, wenn Sie die Registrierungsverschlüsselung mit einem kundenseitig verwalteten Schlüssel konfigurieren. Wenn eine Registrierung mit einem nicht versionierten Schlüssel verschlüsselt ist, wird in Azure Container Registry regelmäßig der Schlüsseltresor auf eine neue Schlüsselversion überprüft und der kundenseitig verwaltete Schlüssel innerhalb von einer Stunde aktualisiert. In Azure Container Registry wird automatisch die neueste Version des Schlüssels verwendet.

* **Manuelles Aktualisieren der Schlüsselversion:** Zur Verwendung einer bestimmten Version eines Schlüssels zur Registrierungsverschlüsselung geben Sie die entsprechende Schlüsselversion an, wenn Sie die Registrierungsverschlüsselung mit einem kundenseitig verwalteten Schlüssel aktivieren. Wenn eine Registrierung mit einer bestimmten Schlüsselversion verschlüsselt ist, wird diese Version in Azure Container Registry zur Verschlüsselung verwendet, bis Sie den kundenseitig verwalteten Schlüssel manuell rotieren.

Weitere Informationen finden Sie unter [Auswählen der Schlüssel-ID mit oder ohne Schlüsselversion](#choose-key-id-with-or-without-key-version) und [Aktualisieren der Schlüsselversion](#update-key-version) weiter unten in diesem Artikel.

## <a name="prerequisites"></a>Voraussetzungen

Um die in diesem Artikel genannten Schritte in der Azure-Befehlszeilenschnittstelle auszuführen, benötigen Sie Version 2.2.0 oder höher der Azure-Befehlszeilenschnittstelle oder Azure Cloud Shell. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Aktivieren kundenseitig verwalteter Schlüssel: CLI

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Führen Sie bei Bedarf den Befehl [az group create][az-group-create] aus, um eine Ressourcengruppe zum Erstellen des Schlüsseltresors, der Containerregistrierung und anderer erforderlicher Ressourcen zu erstellen.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Erstellen einer benutzerseitig zugewiesenen verwalteten Identität

Erstellen Sie eine benutzerseitig zugewiesene [verwaltete Identität für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md) mit dem Befehl [az identity create][az-identity-create]. Diese Identität wird von Ihrer Registrierung verwendet, um auf den Key Vault-Dienst zuzugreifen.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

Notieren Sie sich die folgenden Werte in der Befehlsausgabe: `id` und `principalId`. Sie benötigen diese Werte in späteren Schritten, um den Registrierungszugriff auf den Schlüsseltresor zu konfigurieren.

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Speichern Sie diese Werte aus Gründen der Bequemlichkeit in Umgebungsvariablen:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

Erstellen Sie einen Schlüsseltresor mit [az keyvault create][az-keyvault-create], um einen kundenseitig verwalteten Schlüssel für die Registrierungsverschlüsselung zu speichern.

Standardmäßig ist die Einstellung **Vorläufiges Löschen** in einem neuen Schlüsseltresor automatisch aktiviert. Aktivieren Sie außerdem die Einstellung **Löschschutz**, um Datenverluste aufgrund von versehentlich gelöschten Schlüsseln oder Schlüsseltresoren zu vermeiden:

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-purge-protection
```

Rufen Sie die Ressourcen-ID des Schlüsseltresors zur Verwendung in einem späteren Schritt ab:

```azurecli
keyvaultID=$(az keyvault show --resource-group <resource-group-name> --name <key-vault-name> --query 'id' --output tsv)
```

### <a name="enable-key-vault-access"></a>Aktivieren des Zugriffs auf den Schlüsseltresor

Konfigurieren Sie eine Richtlinie für den Schlüsseltresor, damit die Identität darauf zugreifen kann. In dem folgenden Befehl [az keyvault set-policy][az-keyvault-set-policy] übergeben Sie die zuvor in einer Umgebungsvariablen gespeicherte Prinzipal-ID der verwalteten Identität, die Sie zuvor erstellt hatten. Legen Sie Schlüsselberechtigungen auf **get**, **unwrapkey** und **wrapkey** fest.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey
```

Alternativ können Sie [Azure RBAC für Key Vault](../key-vault/general/rbac-guide.md) (Vorschau) verwenden, um der Identität Zugriffsberechtigungen für den Schlüsseltresor zuzuweisen. Weisen Sie der Identität über den Befehl [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) z. B.die Rolle „Key Vault Crypto Service Encryption“ (Key Vault-Kryptografiedienstverschlüsselung) zu:

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption (preview)" \
  --scope $keyvaultID
```

### <a name="create-key-and-get-key-id"></a>Erstellen eines Schlüssels und Abrufen der Schlüssel-ID

Führen Sie den Befehl [az keyvault key create][az-keyvault-key-create] aus, um einen Schlüssel im Schlüsseltresor zu erstellen.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Notieren Sie sich die ID des Schlüssels (`kid`) in der Befehlsausgabe. Sie verwenden diese ID im nächsten Schritt:

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/<version>",
    "kty": "RSA",
[...]
```

### <a name="choose-key-id-with-or-without-key-version"></a>Auswählen der Schlüssel-ID mit oder ohne Schlüsselversion

Speichern Sie der Einfachheit halber das für die Schlüssel-ID ausgewählte Format in der Umgebungsvariable „$keyID“. Sie können eine Schlüssel-ID mit oder ohne Version verwenden.

#### <a name="manual-key-rotation---key-id-with-version"></a>Manuelle Schlüsselrotation: Schlüssel-ID mit Version

Bei Verwendung zum Verschlüsseln einer Registrierung mit einem kundenseitig verwalteten Schlüssel ermöglicht dieser Schlüssel nur die manuelle Schlüsselrotation in Azure Container Registry.

In diesem Beispiel wird die `kid`-Eigenschaft des Schlüssels gespeichert:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

#### <a name="automatic-key-rotation---key-id-omitting-version"></a>Automatische Schlüsselrotation: Schlüssel-ID ohne Version 

Bei Verwendung zum Verschlüsseln einer Registrierung mit einem kundenseitig verwalteten Schlüssel ermöglicht dieser Schlüssel die automatische Schlüsselrotation, wenn eine neue Schlüsselversion in Azure Key Vault erkannt wird.

In diesem Beispiel wird die Version aus der `kid`-Eigenschaft des Schlüssels entfernt:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)

keyID=$(echo $keyID | sed -e "s/\/[^/]*$//")
```

### <a name="create-a-registry-with-customer-managed-key"></a>Erstellen einer Registrierung mit kundenseitig verwaltetem Schlüssel

Führen Sie den Befehl [az acr create][az-acr-create] aus, um eine Registrierung im Premium-Tarif zu erstellen und den kundenseitig verwalteten Schlüssel zu aktivieren. Übergeben Sie die ID der verwalteten Identität und die Schlüssel-ID, die Sie zuvor in Umgebungsvariablen gespeichert haben:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Anzeigen des Verschlüsselungsstatus

Um anzuzeigen, ob die Registrierungsverschlüsselung mit einem kundenseitig verwalteten Schlüssel aktiviert ist, führen Sie den Befehl [az acr encryption show][az-acr-encryption-show] aus:

```azurecli
az acr encryption show --name <registry-name>
```

Abhängig vom Schlüssel, der zum Verschlüsseln der Registrierung verwendet wird, sieht die Ausgabe etwa wie folgt aus:

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "keyRotationEnabled": true,
    "lastKeyRotationTimestamp": xxxxxxxx
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>Aktivieren kundenseitig verwalteter Schlüssel: Portal

### <a name="create-a-managed-identity"></a>Erstellen einer verwalteten Identität

Erstellen Sie eine benutzerseitig zugewiesene [verwaltete Identität für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md) im Azure-Portal. Eine schrittweise Anleitung hierfür finden Sie unter [Erstellen einer benutzerseitig zugewiesenen Identität](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Sie verwenden den Namen der Identität in späteren Schritten.

:::image type="content" source="media/container-registry-customer-managed-keys/create-managed-identity.png" alt-text="Erstellen einer benutzerseitig zugewiesenen Identität im Azure-Portal":::

### <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

Schritte zum Erstellen eines Schlüsseltresors finden Sie unter [Schnellstart: Erstellen eines Schlüsseltresors über das Azure-Portal](../key-vault/general/quick-create-portal.md).

Wenn Sie einen Schlüsseltresor für einen kundenseitig verwalteten Schlüssel erstellen, aktivieren Sie auf der Registerkarte **Grundlagen** die Einstellung **Löschschutz**. Diese Einstellung hilft bei der Verhinderung von Datenverlusten aufgrund versehentlich gelöschter Schlüssel oder Schlüsseltresore.

:::image type="content" source="media/container-registry-customer-managed-keys/create-key-vault.png" alt-text="Erstellen eines Schlüsseltresors im Azure-Portal":::

### <a name="enable-key-vault-access"></a>Aktivieren des Zugriffs auf den Schlüsseltresor

Konfigurieren Sie eine Richtlinie für den Schlüsseltresor, damit die Identität darauf zugreifen kann.

1. Navigieren Sie zu Ihrem Schlüsseltresor.
1. Wählen Sie **Einstellungen** > **Zugriffsrichtlinien > +Zugriffsrichtlinie hinzufügen** aus.
1. Wählen Sie **Schlüsselberechtigungen** aus, und aktivieren Sie **Abrufen**, **Schlüssel entpacken** und **Schlüssel packen**.
1. Wählen Sie unter **Prinzipal auswählen** den Ressourcennamen der benutzerseitig zugewiesenen verwalteten Identität aus.  
1. Wählen Sie **Hinzufügen** und dann **Speichern** aus.

:::image type="content" source="media/container-registry-customer-managed-keys/add-key-vault-access-policy.png" alt-text="Erstellen einer Schlüsseltresor-Zugriffsrichtlinie":::

Alternativ können Sie [Azure RBAC für Key Vault](../key-vault/general/rbac-guide.md) (Vorschau) verwenden, um der Identität Zugriffsberechtigungen für den Schlüsseltresor zuzuweisen. Weisen Sie der Identität z. B.die Rolle „Key Vault Crypto Service Encryption“ (Key Vault-Kryptografiedienstverschlüsselung) zu.

1. Navigieren Sie zu Ihrem Schlüsseltresor.
1. Klicken Sie auf **Zugriffssteuerung (IAM)**  >  **+ Hinzufügen** > **Rollenzuweisung hinzufügen**.
1. Führen Sie im Fenster **Rollenzuweisung hinzufügen** die folgenden Aktionen aus:
    1. Wählen Sie die Rolle **Key Vault Crypto Service Encryption (Preview)** (Key Vault-Kryptografiedienstverschlüsselung (Vorschau)) aus. 
    1. Weisen Sie der **benutzerseitig zugewiesenen verwalteten Identität** Zugriff zu.
    1. Wählen Sie den Ressourcennamen Ihrer benutzerseitig zugewiesenen verwalteten Identität aus, und klicken Sie auf **Speichern**.

### <a name="create-key-optional"></a>Erstellen eines Schlüssels (optional)

Erstellen Sie optional einen Schlüssel im Schlüsseltresor für das Verschlüsseln der Registrierung. Führen Sie die folgenden Schritte aus, wenn Sie eine bestimmte Schlüsselversion als kundenseitig verwalteten Schlüssel auswählen möchten. 

1. Navigieren Sie zu Ihrem Schlüsseltresor.
1. Wählen Sie **Einstellungen** > **Schlüssel** aus.
1. Wählen Sie **+Generieren/Importieren** aus, und geben Sie einen eindeutigen Namen für den Schlüssel ein.
1. Akzeptieren Sie die verbleibenden Standardwerte, und wählen Sie **Erstellen** aus.
1. Wählen Sie nach der Erstellung den Schlüssel aus, und wählen Sie dann die aktuelle Version aus. Kopieren Sie den **Schlüsselbezeichner** für die Schlüsselversion.

### <a name="create-azure-container-registry"></a>Erstellen einer Azure-Containerregistrierung

1. Klicken Sie auf **Ressource erstellen** > **Container** > **Container Registry**.
1. Wählen Sie auf der Registerkarte **Grundlagen** eine Ressourcengruppe aus, oder erstellen Sie eine, und geben Sie einen Registrierungsnamen ein. Wählen Sie unter **SKU** die Option **Premium** aus.
1. Wählen Sie auf der Registerkarte **Verschlüsselung** unter **Kundenseitig verwalteter Schlüssel** die Option **Aktiviert** aus.
1. Wählen Sie unter **Identität** die verwaltete Identität aus, die Sie erstellt haben.
1. Wählen Sie unter **Verschlüsselung** eine der folgenden Optionen aus:
    * Wählen Sie **Aus Schlüsseltresor auswählen** und dann einen vorhandenen Schlüsseltresor und Schlüssel aus, oder wählen Sie **Neu erstellen** aus. Der ausgewählte Schlüssel ist nicht versioniert und ermöglicht automatische Schlüsselrotation.
    * Wählen Sie **Schlüssel-URI eingeben** aus, und geben Sie direkt einen Schlüsselbezeichner an. Sie können den URI eines versionierten Schlüssels (der Schlüssel muss manuell rotiert werden) oder eines Schlüssels ohne Versionsangabe (aktiviert die automatische Schlüsselrotation) angeben. 
1. Wählen Sie auf der Registerkarte **Verschlüsselung** die Option **Überprüfen + erstellen** aus.
1. Wählen Sie **Erstellen**, um die Registrierungsinstanz bereitzustellen.

:::image type="content" source="media/container-registry-customer-managed-keys/create-encrypted-registry.png" alt-text="Erstellen einer verschlüsselten Registrierung im Azure-Portal":::

Navigieren Sie zu Ihrer Registrierung, um den Verschlüsselungsstatus Ihrer Registrierung im Portal anzuzeigen. Wählen Sie unter **Einstellungen** die Option **Verschlüsselung** aus.

## <a name="enable-customer-managed-key---template"></a>Aktivieren kundenseitig verwalteter Schlüssel: Vorlage

Sie können auch eine Resource Manager-Vorlage verwenden, um eine Registrierung zu erstellen und die Verschlüsselung mit einem kundenseitig verwalteten Schlüssel zu aktivieren.

Die folgende Vorlage erstellt eine neue Containerregistrierung und eine benutzerseitig zugewiesene verwaltete Identität. Kopieren Sie den folgenden Inhalt in eine neue Datei, und speichern Sie sie unter einem Dateinamen, z. B. `CMKtemplate.json`.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}
```

Führen Sie zum Erstellen der folgenden Ressourcen die Schritte aus den vorherigen Abschnitten durch:

* Schlüsseltresor, identifiziert anhand des Namens.
* Schlüsseltresorschlüssel, identifiziert anhand der Schlüssel-ID.

Führen Sie den folgenden Befehl [az deployment group create][az-deployment-group-create] aus, um die Registrierung mithilfe der vorherigen Vorlagendatei zu erstellen. Geben Sie, wo angegeben, einen neuen Registrierungsnamen und den Namen der verwalteten Identität an sowie den Namen des Schlüsseltresors und die Schlüssel-ID, die Sie erstellt haben.

```bash
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>Anzeigen des Verschlüsselungsstatus

Um den Status der Registrierungsverschlüsselung anzuzeigen, führen Sie den Befehl [az acr encryption show][az-acr-encryption-show] aus:

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>Verwenden der Registrierung

Nachdem Sie einen kundenseitig verwalteten Schlüssel in einer Registrierung aktiviert haben, können Sie dieselben Registrierungsvorgänge ausführen, die Sie auch in einer Registrierung ausführen, die nicht mit einem kundenseitig verwalteten Schlüssel verschlüsselt ist. Beispielsweise können Sie sich mit der Registrierung authentifizieren und Docker-Images per Push übertragen. Beispielbefehle finden Sie unter [Push- und Pull-Vorgänge für Images](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Rotieren von Schlüsseln

Aktualisieren Sie die Schlüsselversion in Azure Key Vault, oder erstellen Sie einen neuen Schlüssel, und aktualisieren Sie dann die Registrierung, damit Daten mit dem Schlüssel verschlüsselt werden. Sie können diese Schritte auch mit der Azure CLI oder im Portal ausführen.

Beim Rotieren eines Schlüssels geben Sie in der Regel dieselbe Identität an, die beim Erstellen der Registrierung verwendet wurde. Optional konfigurieren Sie eine neue benutzerseitig zugewiesene Identität für den Schlüsselzugriff, oder Sie aktivieren die systemseitig zugewiesene Identität der Registrierung und geben diese an.

> [!NOTE]
> Stellen Sie sicher, dass der erforderliche [Schlüsseltresorzugriff](#enable-key-vault-access) für die Identität festgelegt ist, die Sie für den Schlüsselzugriff konfigurieren.

### <a name="update-key-version"></a>Aktualisieren der Schlüsselversion

Ein verbreitetes Szenario ist das Aktualisieren der Version des Schlüssels, der als kundenseitig verwalteter Schlüssel verwendet wird. Je nach Konfiguration der Registrierungsverschlüsselung wird der kundenseitig verwaltete Schlüssel in Azure Container Registry automatisch aktualisiert, oder er muss manuell aktualisiert werden.

### <a name="azure-cli"></a>Azure CLI

Verwenden Sie [az keyvault key][az-keyvault-key]-Befehle, um Ihre Schlüsseltresorschlüssel zu erstellen oder zu verwalten. Um eine neue Schlüsselversion zu erstellen, führen Sie den Befehl [az keyvault key create][az-keyvault-key-create] aus:

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>
```

Der nächste Schritt hängt von der Konfiguration der Registrierungsverschlüsselung ab:

* Wenn die Registrierung so konfiguriert ist, dass Aktualisierungen der Schlüsselversion erkannt werden, wird der kundenseitig verwaltete Schlüssel automatisch innerhalb von einer Stunde aktualisiert.

* Wenn die Registrierung so konfiguriert ist, dass eine neue Schlüsselversion manuell aktualisiert werden muss, führen Sie den Befehl [az acr encryption rotate-key][az-acr-encryption-rotate-key] aus, und übergeben Sie dabei die neue Schlüssel-ID und die zu konfigurierende Identität:

So aktualisieren Sie die Version des kundenseitig verwalteten Schlüssels manuell

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

> [!TIP]
> Wenn Sie `az acr encryption rotate-key` ausführen, können Sie entweder eine versionierte Schlüssel-ID oder eine nicht versionierte Schlüssel-ID übergeben. Wenn Sie eine nicht versionierte Schlüssel-ID verwenden, wird die Registrierung so konfiguriert, dass spätere Aktualisierungen der Schlüsselversion automatisch erkannt werden.

### <a name="portal"></a>Portal

Verwenden Sie die Einstellungen für die **Verschlüsselung** der Registrierung, um die Einstellungen für den Schlüsseltresor, den Schlüssel oder die Identität zu aktualisieren, die für den kundenseitig verwalteten Schlüssel verwendet werden.

Gehen Sie zum Konfigurieren eines neuen Schlüssels z. B. wie folgt vor:

1. Navigieren Sie im Portal zu Ihrer Registrierung.
1. Wählen Sie unter **Einstellungen** die Option **Verschlüsselung** > **Schlüssel ändern** aus.

    :::image type="content" source="media/container-registry-customer-managed-keys/rotate-key.png" alt-text="Rotieren des Schlüssels im Azure-Portal":::
1. Wählen Sie in **Verschlüsselung** eine der folgenden Optionen aus:
    * Wählen Sie **Aus Schlüsseltresor auswählen** und dann einen vorhandenen Schlüsseltresor und Schlüssel aus, oder wählen Sie **Neu erstellen** aus. Der ausgewählte Schlüssel ist nicht versioniert und ermöglicht automatische Schlüsselrotation.
    * Wählen Sie **Schlüssel-URI eingeben** aus, und geben Sie direkt einen Schlüsselbezeichner an. Sie können den URI eines versionierten Schlüssels (der Schlüssel muss manuell rotiert werden) oder eines Schlüssels ohne Versionsangabe (aktiviert die automatische Schlüsselrotation) angeben.
1. Vervollständigen Sie die Schlüsselauswahl, und wählen Sie **Speichern** aus.

## <a name="revoke-key"></a>Widerrufen eines Schlüssels

Widerrufen Sie den kundenseitig verwalteten Verschlüsselungsschlüssel, indem Sie die Zugriffsrichtlinie oder die Berechtigungen für den Schlüsseltresor ändern oder den Schlüssel löschen. Verwenden Sie z. B. den Befehl [az keyvault delete-policy][az-keyvault-delete-policy], um die Zugriffsrichtlinie für die verwaltete Identität zu ändern, die von Ihrer Registrierung verwendet wird:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Das Widerrufen des Schlüssels blockiert praktisch den Zugriff auf alle Registrierungsdaten, da die Registrierung nicht auf den Verschlüsselungsschlüssel zugreifen kann. Wenn der Zugriff auf den Schlüssel aktiviert oder der gelöschte Schlüssel wiederhergestellt wird, wählt Ihre Registrierung den Schlüssel aus, sodass Sie wieder auf die verschlüsselten Registrierungsdaten zugreifen können.

## <a name="advanced-scenario-key-vault-firewall"></a>Erweitertes Szenario: Key Vault-Firewall

Wenn Ihr Azure-Schlüsseltresor in einem virtuellen Netzwerk mit einer Key Vault-Firewall bereitgestellt ist, führen Sie die folgenden zusätzlichen Schritte aus, nachdem Sie die Verschlüsselung mit kundenseitig verwaltetem Schlüssel in Ihrer Registrierung aktiviert haben.

1. Konfigurieren der Registrierungsverschlüsselung für die Verwendung der systemseitig zugewiesenen Identität
1. Aktivieren der Registrierung für die Umgehung der Key Vault-Firewall
1. Rotieren des kundenseitig verwalteten Schlüssels

### <a name="configure-system-assigned-identity"></a>Konfigurieren der systemseitig zugewiesenen Identität

Sie können die systemseitig zugewiesene verwaltete Identität einer Registrierung für den Zugriff auf den Schlüsseltresor für Verschlüsselungsschlüssel konfigurieren. Wenn Sie mit den verschiedenen verwalteten Identitäten für Azure-Ressourcen nicht vertraut sind, sehen Sie sich die [Übersicht](../active-directory/managed-identities-azure-resources/overview.md) an.

So aktivieren Sie die systemseitig zugewiesene Identität im Portal

1. Navigieren Sie im Portal zu Ihrer Registrierung.
1. Wählen Sie **Einstellungen** >  **Identität** aus.
1. Legen Sie unter **Systemseitig zugewiesen** **Status** auf **Ein** fest. Wählen Sie **Speichern** aus.
1. Kopieren Sie die **Objekt-ID** der Identität.

So gewähren Sie der Identität Zugriff auf Ihren Schlüsseltresor

1. Navigieren Sie zu Ihrem Schlüsseltresor.
1. Wählen Sie **Einstellungen** > **Zugriffsrichtlinien > +Zugriffsrichtlinie hinzufügen** aus.
1. Wählen Sie **Schlüsselberechtigungen** aus, und aktivieren Sie **Abrufen**, **Schlüssel entpacken** und **Schlüssel packen**.
1. Wählen Sie **Prinzipal auswählen** aus, und suchen Sie nach der Objekt-ID der systemseitig zugewiesenen verwalteten Identität oder dem Namen Ihrer Registrierung.  
1. Wählen Sie **Hinzufügen** und dann **Speichern** aus.

So aktualisieren Sie die Verschlüsselungseinstellungen der Registrierung für die Verwendung der Identität

1. Navigieren Sie im Portal zu Ihrer Registrierung.
1. Wählen Sie unter **Einstellungen** die Option **Verschlüsselung** > **Schlüssel ändern** aus.
1. Wählen Sie in **Identität** zuerst **Systemseitig zugewiesen** und dann **Speichern** aus.

### <a name="enable-key-vault-bypass"></a>Aktivieren der Key Vault-Umgehung

Um auf einen mit einer Key Vault-Firewall konfigurierten Schlüsseltresor zuzugreifen, muss die Registrierung die Firewall umgehen. Stellen Sie sicher, dass der Schlüsseltresor so konfiguriert ist, dass der Zugriff durch jeden [vertrauenswürdigen Dienst](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services) zugelassen wird. Azure Container Registry ist einer der vertrauenswürdigen Dienste.

1. Navigieren Sie im Portal zu Ihrem Schlüsseltresor.
1. Wählen Sie **Einstellungen** > **Netzwerk** aus.
1. Bestätigen, aktualisieren oder fügen Sie Einstellungen des virtuellen Netzwerks hinzu. Detaillierte Schritte finden Sie unter [Konfigurieren von Azure Key Vault-Firewalls und virtuellen Netzwerken](../key-vault/general/network-security.md).
1. Wählen Sie in **Vertrauenswürdigen Microsoft-Diensten die Umgehung dieser Firewall erlauben** die Option **Ja** aus. 

### <a name="rotate-the-customer-managed-key"></a>Rotieren des kundenseitig verwalteten Schlüssels

Nachdem Sie die vorangehenden Schritte ausgeführt haben, rotieren Sie den Schlüssel in einen neuen Schlüssel im Schlüsseltresor hinter einer Firewall. Weitere Informationen finden Sie unter [Rotieren von Schlüsseln](#rotate-key) in diesem Artikel.

## <a name="troubleshoot"></a>Problembehandlung

### <a name="removing-user-assigned-identity"></a>Entfernen der benutzerseitig zugewiesenen Identität

Wenn Sie versuchen, eine benutzerseitig zugewiesene Identität aus einer Registrierung zu entfernen, die für die Verschlüsselung verwendet wird, wird möglicherweise eine Fehlermeldung ähnlich der folgenden angezeigt:
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
Darüber hinaus können Sie den Verschlüsselungsschlüssel nicht ändern (rotieren). Wenn dieses Problem auftritt, weisen Sie zunächst die Identität mit der in der Fehlermeldung angegebenen GUID neu zu. Beispiel:

```azurecli
az acr identity assign -n myRegistry --identities xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx
```
        
Nach dem Ändern des Schlüssels und dem Zuweisen einer anderen Identität können Sie dann die ursprüngliche benutzerseitig zugewiesene Identität entfernen.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur [Verschlüsselung ruhender Daten in Azure](../security/fundamentals/encryption-atrest.md).
* Unter [Sicherer Zugriff auf einen Schlüsseltresor](../key-vault/general/secure-your-key-vault.md) erfahren Sie mehr über Zugriffsrichtlinien und das Sichern Ihres Schlüsseltresors.


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
