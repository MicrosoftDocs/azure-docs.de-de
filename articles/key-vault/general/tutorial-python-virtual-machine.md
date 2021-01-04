---
title: 'Tutorial: Verwenden von Azure Key Vault mit einem virtuellen Computer in Python | Microsoft-Dokumentation'
description: In diesem Tutorial konfigurieren Sie einen virtuellen Computer in einer Python-Anwendung zum Lesen eines Geheimnisses aus Ihrem Schlüsseltresor.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-python, devx-track-azurecli
ms.openlocfilehash: ae62bf353f8a92c4408d4a38a91771ad60a13107
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285308"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>Tutorial: Verwenden von Azure Key Vault mit einem virtuellen Windows in Python

Azure Key Vault unterstützt Sie beim Schützen von Schlüsseln, Geheimnissen und Zertifikaten wie API-Schlüsseln und Datenbank-Verbindungszeichenfolgen.

In diesem Tutorial richten Sie eine Python-Anwendung ein, die unter Verwendung von verwalteten Identitäten für Azure-Ressourcen Informationen aus Azure Key Vault liest. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen eines Schlüsseltresors
> * Speichern eines Geheimnisses in Key Vault
> * Erstellen eines virtuellen Azure Linux-Computers
> * Aktivieren einer [verwalteten Identität](../../active-directory/managed-identities-azure-resources/overview.md) für den virtuellen Computer
> * Erteilen der erforderlichen Berechtigungen zum Lesen von Daten aus Key Vault für die Konsolenanwendung
> * Abrufen eines Geheimnisses aus Key Vault

Machen Sie sich zunächst mit den [grundlegenden Konzepten von Key Vault](basic-concepts.md) vertraut. 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Für Windows, Mac und Linux:
  * [Git-Client](https://git-scm.com/downloads)
  * Für dieses Tutorial ist es erforderlich, dass Sie die Azure CLI lokal ausführen. Hierfür muss Azure CLI Version 2.0.4 oder höher installiert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zur Installation und Aktualisierung der CLI finden Sie bei Bedarf unter [Installieren von Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Anmelden an Azure

Geben Sie Folgendes ein, um sich mithilfe der Azure-Befehlszeilenschnittstelle bei Azure anzumelden:

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>Erstellen einer Ressourcengruppe und eines Schlüsseltresors

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Einfügen eines Geheimnisses in Ihren Schlüsseltresor

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie mit einer der folgenden Methoden einen virtuellen Computer namens **myVM** :

| Linux | Windows |
|--|--|
| [Azure-Befehlszeilenschnittstelle](../../virtual-machines/linux/quick-create-cli.md) | [Azure-Befehlszeilenschnittstelle](../../virtual-machines/windows/quick-create-cli.md) |
| [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) | [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) |
| [Azure portal](../../virtual-machines/linux/quick-create-portal.md) | [Azure-Portal](../../virtual-machines/windows/quick-create-portal.md) |

Verwenden Sie zum Erstellen eines virtuellen Linux-Computers mithilfe der Azure CLI den Befehl [az vm create](/cli/azure/vm).  Im folgenden Beispiel wird ein Benutzerkonto mit dem Namen *azureuser* hinzugefügt. Der Parameter `--generate-ssh-keys` wird genutzt, um automatisch einen SSH-Schlüssel zu generieren und am Speicherort für den Standardschlüssel abzulegen ( *~/.ssh* ). 

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Beachten Sie den Wert von `publicIpAddress` in der Ausgabe.

## <a name="assign-an-identity-to-the-vm"></a>Zuweisen einer Identität zum virtuellen Computer

Erstellen Sie für den virtuellen Computer mit dem Azure CLI-Befehl [az vm identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) eine systemseitig zugewiesene Identität:

```azurecli
az vm identity assign --name "myVM" --resource-group "myResourceGroup"
```

Beachten Sie die systemseitig zugewiesene Identität, die im folgenden Code angezeigt wird. Die Ausgabe des obigen Befehls lautet etwa wie folgt: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Zuweisen von Berechtigungen für die VM-Identität

Jetzt können Sie Ihrem Schlüsseltresor die zuvor erstellten Identitätsberechtigungen zuweisen, indem Sie den folgenden Befehl ausführen:

```azurecli
az keyvault set-policy --name "<your-unique-keyvault-name>" --object-id "<systemAssignedIdentity>" --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Anmelden am virtuellen Computer

Befolgen Sie zum Anmelden beim virtuellen Computer die Anleitung unter [Vorschau: Anmelden bei einem virtuellen Linux-Computer in Azure mit der Azure Active Directory-Authentifizierung](../../virtual-machines/linux/login-using-aad.md) bzw. [Herstellen einer Verbindung mit einem virtuellen Azure-Computer unter Windows und Anmelden auf diesem Computer](../../virtual-machines/windows/connect-logon.md).


Für die Anmeldung bei einem virtuellen Linux-Computer können Sie den ssh-Befehl mit dem im Schritt [Erstellen eines virtuellen Computers](#create-a-virtual-machine) erhaltenen Wert für <publicIpAddress> verwenden:

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-libraries-on-the-vm"></a>Installieren der Python-Bibliothek auf dem virtuellen Computer

Installieren Sie auf dem virtuellen Computer die beiden Python-Bibliotheken, die im Python-Skript verwendet werden: `azure-keyvault-secrets` und `azure.identity`.  

Auf einem virtuellen Linux-Computer können Sie diese beispielsweise mithilfe von `pip3` installieren:

```bash
pip3 install azure-keyvault-secrets

pip3 install azure.identity
```

## <a name="create-and-edit-the-sample-python-script"></a>Erstellen und Bearbeiten des Python-Beispielskripts

Erstellen Sie auf dem virtuellen Computer eine Python-Datei mit dem Namen **sample.py**. Bearbeiten Sie die Datei so, dass sie den folgenden Code enthält. Ersetzen Sie dabei „<your-unique-keyvault-name>“ durch den Namen Ihres Schlüsseltresors:

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = "<your-unique-keyvault-name>"
KVUri = f"https://{keyVaultName}.vault.azure.net"
secretName = "mySecret"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
retrieved_secret = client.get_secret(secretName)

print(f"The value of secret '{secretName}' in '{keyVaultName}' is: '{retrieved_secret.value}'")
```

## <a name="run-the-sample-python-app"></a>Ausführen der Python-Beispiel-App

Führen Sie abschließend **sample.py** aus. Hat alles richtig funktioniert, sollte der Wert Ihres Geheimnisses zurückgeben werden:

```bash
python3 sample.py

The value of secret 'mySecret' in '<your-unique-keyvault-name>' is: 'Success!'
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie den virtuellen Computer und Ihren Schlüsseltresor, wenn diese nicht mehr benötigt werden.  Das geht ganz schnell, indem Sie einfach die Ressourcengruppe löschen, zu der diese Elemente gehören:

```azurecli
az group delete -g myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

[Azure Key Vault-REST-API](/rest/api/keyvault/)