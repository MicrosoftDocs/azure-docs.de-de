---
title: Verwenden einer verwalteten Identität mit Azure Container Instances
description: Erfahren Sie, wie Sie eine verwaltete Identität zur Authentifizierung bei anderen Azure-Diensten über Azure Container Instances verwenden.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 10/22/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: c14c3aaf2a5d648572fdc251540264e8057a00f9
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144304"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Verwenden von verwalteten Identitäten mit Azure Container Instances

Verwenden Sie [verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md), um Code in Azure Container Instances auszuführen, der mit anderen Azure-Diensten interagiert – ohne Geheimnisse oder Anmeldeinformationen im Code verwalten zu müssen. Das Feature bietet eine Bereitstellung von Azure Container Instances mit einer automatisch verwalteten Identität in Azure Active Directory.

In diesem Artikel erfahren Sie mehr über verwaltete Identitäten in Azure Container Instances, und Sie lernen Folgendes:

> [!div class="checklist"]
> * Aktivieren einer vom Benutzer oder vom System zugewiesenen Identität in einer Containergruppe
> * Gewähren des Zugriffs auf eine Azure Key Vault-Instanz für die Identität
> * Verwenden der verwalteten Identität zum Zugreifen auf eine Key Vault-Instanz über einen ausgeführten Container

Passen Sie die Beispiele an, um Identitäten in Azure Container Instances zu aktivieren und für den Zugriff auf andere Azure-Dienste zu verwenden. Die Beispiele sind interaktiv. In der Praxis würden Ihre Containerimages Code ausführen, um auf Azure-Dienste zuzugreifen.

> [!NOTE]
> Sie können derzeit keine verwaltete Identität in einer Containergruppe verwenden, die in einem virtuellen Netzwerk bereitgestellt wird.

## <a name="why-use-a-managed-identity"></a>Gründe für die Verwendung einer verwalteten Identität

Mit einer verwalteten Identität in einem ausgeführten Container können Sie sich [bei jedem Dienst authentifizieren, der die Azure Active Directory-Authentifizierung unterstützt](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication), ohne Anmeldeinformationen im Code verwalten zu müssen. Für Dienste, die die Azure AD-Authentifizierung nicht unterstützen, können Sie Geheimnisse in Azure Key Vault speichern und mithilfe der verwalteten Identität auf Key Vault zugreifen, um Anmeldeinformationen abzurufen. Weitere Informationen zur Verwendung einer verwalteten Identität finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Diese Funktion steht derzeit als Vorschau zur Verfügung. Vorschauversionen werden Ihnen zur Verfügung gestellt, wenn Sie die [zusätzlichen Nutzungsbedingungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) akzeptieren. Einige Aspekte dieses Features werden bis zur allgemeinen Verfügbarkeit unter Umständen noch geändert. Verwaltete Identitäten werden zurzeit nur auf Linux-Containerinstanzen unterstützt.
>  

### <a name="enable-a-managed-identity"></a>Aktivieren einer verwalteten Identität

 In Azure Container Instances werden verwaltete Identitäten für Azure-Ressourcen ab REST-API-Version 2018-10-01 und in den entsprechenden SDKs und Tools unterstützt. Aktivieren Sie beim Erstellen einer Containergruppe eine oder mehrere verwaltete Identitäten, indem Sie eine [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity)-Eigenschaft festlegen. Sie können verwaltete Identitäten auch aktivieren oder aktualisieren, wenn eine Containergruppe bereits ausgeführt wird. In beiden Fällen wird die Containergruppe neu gestartet. Zum Festlegen der Identitäten für eine neue oder vorhandene Containergruppe können Sie die Azure-Befehlszeilenschnittstelle (Azure CLI), eine Resource Manager-Vorlage oder eine YAML-Datei verwenden. 

Azure Container Instances unterstützt sowohl vom Benutzer als auch vom System zugewiesene verwaltete Azure-Identitäten. In einer Containergruppe können Sie eine vom System zugewiesene Identität, eine oder mehrere vom Benutzer zugewiesene Identitäten oder beide Identitätstypen aktivieren. 

* Eine **vom Benutzer zugewiesene** verwaltete Identität wird als eigenständige Azure-Ressource in dem Azure AD-Mandanten erstellt, der vom verwendeten Abonnement als vertrauenswürdig eingestuft wird. Nachdem die Identität erstellt wurde, kann sie einer oder mehreren Azure-Ressourcen zugewiesen werden (in Azure Container Instances oder anderen Azure-Diensten). Der Lebenszyklus einer vom Benutzer zugewiesenen Identität wird getrennt vom Lebenszyklus der Containergruppen oder anderen Dienstressourcen, denen sie zugewiesen ist, verwaltet. Dieses Verhalten ist besonders nützlich in Azure Container Instances. Da die Identität über die Lebensdauer einer Containergruppe hinaus gilt, können Sie sie zusammen mit anderen Standardeinstellungen wiederverwenden, um eine hohe Wiederholbarkeit Ihrer Containergruppenbereitstellungen sicherzustellen.

* Eine **vom System zugewiesene verwaltete Identität** wird direkt in einer Containergruppe in Azure Container Instances aktiviert. Wenn die Identität aktiviert ist, erstellt Azure eine Identität für die Gruppe in dem Azure AD-Mandanten, der vom Abonnement der Instanz als vertrauenswürdig eingestuft wird. Nach dem Erstellen der Identität werden die Anmeldeinformationen in jedem Container in der Containergruppe bereitgestellt. Der Lebenszyklus einer vom System zugewiesenen Identität ist direkt an die Containergruppe gebunden, für die sie aktiviert wurde. Wenn die Gruppe gelöscht wird, bereinigt Azure automatisch die Anmeldeinformationen und die Identität in Azure AD.

### <a name="use-a-managed-identity"></a>Verwenden einer verwalteten Identität

Zur Verwendung einer verwalteten Identität muss der Identität zunächst der Zugriff auf mindestens eine Azure-Dienstressource (z. B. eine Web-App, eine Key Vault-Instanz oder ein Speicherkonto) im Abonnement gewährt werden. Um über einen ausgeführten Container auf die Azure-Ressourcen zuzugreifen, muss Ihr Code ein *Zugriffstoken* von einem Azure AD-Endpunkt abrufen. Danach sendet der Code das Zugriffstoken in einem Aufruf an einen Dienst, der die Azure AD-Authentifizierung unterstützt. 

Die Verwendung einer verwalteten Identität in einem ausgeführten Container ist im Wesentlichen identisch mit der Verwendung einer Identität auf einer Azure-VM. Weitere Informationen finden Sie in der Anleitung für VMs zur Verwendung eines [Tokens](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), von [Azure PowerShell oder der Azure-Befehlszeilenschnittstelle](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) oder [Azure-SDKs](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittstelle (CLI) lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 2.0.49 der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Erstellen einer Azure Key Vault-Instanz

In den Beispielen in diesem Artikel wird eine verwaltete Identität in Azure Container Instances verwendet, um auf ein Azure Key Vault-Geheimnis zuzugreifen. 

Erstellen Sie zunächst mit dem folgenden Befehl [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) eine Ressourcengruppe namens *myResourceGroup* am Standort *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Verwenden Sie den Befehl [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create), um eine Key Vault-Instanz zu erstellen. Geben Sie hierbei einen eindeutigen Namen für die Key Vault-Instanz an. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Speichern Sie mithilfe des Befehls [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) ein Beispielgeheimnis in der Key Vault-Instanz:

```azurecli-interactive
az keyvault secret set --name SampleSecret --value "Hello Container Instances!" --description ACIsecret  --vault-name mykeyvault
```

Fahren Sie mit den folgenden Beispielen fort, um mit einer vom Benutzer oder vom System zugewiesenen verwalteten Identität in Azure Container Instances auf die Key Vault-Instanz zuzugreifen.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Beispiel 1: Verwenden einer vom Benutzer zugewiesenen Identität für den Zugriff auf Azure Key Vault

### <a name="create-an-identity"></a>Erstellen einer Identität

Erstellen Sie zunächst mit dem Befehl [az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) eine Identität in Ihrem Abonnement. Sie können die gleiche Ressourcengruppe verwenden, die Sie zum Erstellen der Key Vault-Instanz verwendet haben, oder eine andere Ressourcengruppe auswählen.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACIId
```

Um die Identität in den folgenden Schritten verwenden zu können, speichern Sie mit dem Befehl [az identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) die Dienstprinzipal-ID der Identität und die Ressourcen-ID in Variablen.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Aktivieren einer vom Benutzer zugewiesenen Identität in einer Containergruppe

Führen Sie den folgenden Befehl [az container create](/cli/azure/container?view=azure-cli-latest#az-container-create) aus, um eine auf Ubuntu Server basierende Containerinstanz zu erstellen. In diesem Beispiel wird eine einzelne Containergruppe bereitgestellt, die Sie verwenden können, um interaktiv auf andere Azure-Dienste zuzugreifen. Der `--assign-identity`-Parameter übergibt Ihre vom Benutzer zugewiesene verwaltete Identität an die Gruppe. Der Befehl mit langer Laufzeit sorgt dafür, dass der Container weiterhin ausgeführt wird. In diesem Beispiel wird die Ressourcengruppe verwendet, die zum Erstellen der Key Vault-Instanz verwendet wurde, Sie können aber auch eine andere Ressourcengruppe angeben.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity $resourceID --command-line "tail -f /dev/null"
```

Sie sollten innerhalb weniger Sekunden eine Antwort von der Azure-Befehlszeilenschnittstelle mit dem Hinweis erhalten, dass die Bereitstellung abgeschlossen wurde. Überprüfen Sie den Status mit dem Befehl [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show).

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

Der Abschnitt `identity` in der Ausgabe sieht in etwa wie folgt aus und zeigt, dass die Identität in der Containergruppe festgelegt wurde. Die `principalID` unter `userAssignedIdentities` ist der Dienstprinzipal der Identität, die Sie in Azure Active Directory erstellt haben:

```console
...
"identity": {
    "principalId": "null",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-0903-4b79-a55a-xxxxxxxxxxxx/resourcegroups/danlep1018/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACIId": {
        "clientId": "xxxxxxxx-5523-45fc-9f49-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-f25b-4895-b828-xxxxxxxxxxxx"
      }
    }
  },
...
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Gewähren des Zugriffs auf die Key Vault-Instanz für die vom Benutzer zugewiesene Identität

Führen Sie den folgenden Befehl [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) aus, um eine Zugriffsrichtlinie für die Key Vault-Instanz festzulegen. Das folgende Beispiel ermöglicht der vom Benutzer zugewiesenen Identität das Abrufen von Geheimnissen aus der Key Vault-Instanz:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Verwenden der vom Benutzer zugewiesenen Identität zum Abrufen von Geheimnissen aus der Key Vault-Instanz

Jetzt können Sie mithilfe der verwalteten Identität in der ausgeführten Containerinstanz auf die Key Vault-Instanz zugreifen. Für dieses Beispiel müssen Sie zuerst eine Bash-Shell im Container starten:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Führen Sie die folgenden Befehle in der Bash-Shell im Container aus. Rufen Sie mit dem folgenden Befehl ein Zugriffstoken ab, um Azure Active Directory für die Authentifizierung bei Key Vault zu verwenden:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Ausgabe:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Um das Zugriffstoken in einer Variablen zu speichern, die in nachfolgenden Befehlen zur Authentifizierung verwendet werden kann, führen Sie den folgenden Befehl aus:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Nun verwenden Sie das Zugriffstoken zum Durchführen der Authentifizierung bei Key Vault und Lesen eines Geheimnisses. Denken Sie daran, den Namen Ihres Schlüsseltresors in der URL zu ersetzen ( *https://mykeyvault.vault.azure.net/...* ):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Die Antwort sieht in etwa wie folgt aus und enthält das Geheimnis. In Ihrem Code würden Sie diese Ausgabe analysieren, um das Geheimnis abzurufen. Verwenden Sie das Geheimnis anschließend in einem nachfolgenden Vorgang, um auf eine andere Azure-Ressource zuzugreifen.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Beispiel 2: Verwenden einer vom System zugewiesenen Identität für den Zugriff auf Azure Key Vault

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>Aktivieren einer vom System zugewiesenen Identität in einer Containergruppe

Führen Sie den folgenden Befehl [az container create](/cli/azure/container?view=azure-cli-latest#az-container-create) aus, um eine auf Ubuntu Server basierende Containerinstanz zu erstellen. In diesem Beispiel wird eine einzelne Containergruppe bereitgestellt, die Sie verwenden können, um interaktiv auf andere Azure-Dienste zuzugreifen. Der `--assign-identity`-Parameter ohne zusätzlichen Wert aktiviert eine vom System zugewiesene verwaltete Identität in der Gruppe. Der Befehl mit langer Laufzeit sorgt dafür, dass der Container weiterhin ausgeführt wird. In diesem Beispiel wird die Ressourcengruppe verwendet, die zum Erstellen der Key Vault-Instanz verwendet wurde, Sie können aber auch eine andere Ressourcengruppe angeben.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity --command-line "tail -f /dev/null"
```

Sie sollten innerhalb weniger Sekunden eine Antwort von der Azure-Befehlszeilenschnittstelle mit dem Hinweis erhalten, dass die Bereitstellung abgeschlossen wurde. Überprüfen Sie den Status mit dem Befehl [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show).

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

Der Abschnitt `identity` in der Ausgabe sieht in etwa wie folgt aus und zeigt, dass eine vom System zugewiesene Identität in Azure Active Directory erstellt wurde:

```console
...
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
...
```

Legen Sie eine Variable auf den Wert von `principalId` (Dienstprinzipal-ID) der Identität fest, um sie in späteren Schritten zu verwenden.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Gewähren des Zugriffs auf die Key Vault-Instanz für die Containergruppe

Führen Sie den folgenden Befehl [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) aus, um eine Zugriffsrichtlinie für die Key Vault-Instanz festzulegen. Das folgende Beispiel ermöglicht der vom System verwalteten Identität das Abrufen von Geheimnissen aus der Key Vault-Instanz:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Verwenden der Identität der Containergruppe zum Abrufen von Geheimnissen aus der Key Vault-Instanz

Jetzt können Sie mithilfe der verwalteten Identität in der ausgeführten Containerinstanz auf die Key Vault-Instanz zugreifen. Für dieses Beispiel müssen Sie zuerst eine Bash-Shell im Container starten:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Führen Sie die folgenden Befehle in der Bash-Shell im Container aus. Rufen Sie mit dem folgenden Befehl ein Zugriffstoken ab, um Azure Active Directory für die Authentifizierung bei Key Vault zu verwenden:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F' -H Metadata:true -s
```

Ausgabe:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Um das Zugriffstoken in einer Variablen zu speichern, die in nachfolgenden Befehlen zur Authentifizierung verwendet werden kann, führen Sie den folgenden Befehl aus:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Nun verwenden Sie das Zugriffstoken zum Durchführen der Authentifizierung bei Key Vault und Lesen eines Geheimnisses. Denken Sie daran, den Namen Ihres Schlüsseltresors in der URL zu ersetzen (*https:\//mykeyvault.vault.azure.net/...* ):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Die Antwort sieht in etwa wie folgt aus und enthält das Geheimnis. In Ihrem Code würden Sie diese Ausgabe analysieren, um das Geheimnis abzurufen. Verwenden Sie das Geheimnis anschließend in einem nachfolgenden Vorgang, um auf eine andere Azure-Ressource zuzugreifen.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Aktivieren der verwalteten Identität mithilfe einer Resource Manager-Vorlage

Um eine verwaltete Identität in einer Containergruppe mithilfe einer [Resource Manager-Vorlage](container-instances-multi-container-group.md) zu aktivieren, legen Sie die `identity`-Eigenschaft des `Microsoft.ContainerInstance/containerGroups`-Objekts mit einem `ContainerGroupIdentity`-Objekt fest. Die folgenden Codeausschnitte zeigen die für verschiedene Szenarien konfigurierte `identity`-Eigenschaft. Weitere Informationen finden Sie unter [Resource Manager template reference](/azure/templates/microsoft.containerinstance/containergroups) (Referenz zur Resource Manager-Vorlage). Legen Sie `apiVersion` auf `2018-10-01` fest.

### <a name="user-assigned-identity"></a>Vom Benutzer zugewiesene Identität

Eine vom Benutzer zugewiesene Identität ist eine Ressourcen-ID im folgenden Format:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Sie können eine oder mehrere vom Benutzer zugewiesene Identitäten aktivieren.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Vom System zugewiesene Identität

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>Vom System und vom Benutzer zugewiesene Identitäten

Sie können in einer Containergruppe sowohl eine vom System zugewiesene Identität als auch eine oder mehrere vom Benutzer zugewiesene Identitäten aktivieren.

```json
"identity": {
    "type": "System Assigned, UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
...
```

## <a name="enable-managed-identity-using-yaml-file"></a>Aktivieren der verwalteten Identität mithilfe einer YAML-Datei

Um eine verwaltete Identität in einer mithilfe einer [YAML-Datei](container-instances-multi-container-yaml.md) bereitgestellten Containergruppe zu aktivieren, fügen Sie den folgenden YAML-Code hinzu.
Legen Sie `apiVersion` auf `2018-10-01` fest.

### <a name="user-assigned-identity"></a>Vom Benutzer zugewiesene Identität

Eine vom Benutzer zugewiesene Identität ist eine Ressourcen-ID im folgenden Format. 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Sie können eine oder mehrere vom Benutzer zugewiesene Identitäten aktivieren.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Vom System zugewiesene Identität

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>Vom System und vom Benutzer zugewiesene Identitäten

Sie können in einer Containergruppe sowohl eine vom System zugewiesene Identität als auch eine oder mehrere vom Benutzer zugewiesene Identitäten aktivieren.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mehr über verwaltete Identitäten in Azure Container Instances erfahren und Folgendes gelernt:

> [!div class="checklist"]
> * Aktivieren einer vom Benutzer oder vom System zugewiesenen Identität in einer Containergruppe
> * Gewähren des Zugriffs auf eine Azure Key Vault-Instanz für die Identität
> * Verwenden der verwalteten Identität zum Zugreifen auf eine Key Vault-Instanz über einen ausgeführten Container

* Erfahren Sie mehr über [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/).

* Sehen Sie sich ein [Azure Go SDK-Beispiel](https://medium.com/@samkreter/c98911206328) zur Verwendung einer verwalteten Identität für den Zugriff auf eine Key Vault-Instanz über Azure Container Instances an.
