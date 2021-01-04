---
title: 'Schnellstart: Azure Key Vault-Geheimnisclientbibliothek für JavaScript (Version 4)'
description: Es wird beschrieben, wie Sie mit der JavaScript-Clientbibliothek Geheimnisse in einer Azure Key Vault-Instanz erstellen, daraus abrufen und löschen.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 8e04fcea53869fe15ebbeb3c7709cff842893931
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780789"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-javascript-version-4"></a>Schnellstart: Azure Key Vault-Geheimnisclientbibliothek für JavaScript (Version 4)

Hier finden Sie Informationen zu den ersten Schritten mit der Azure Key Vault-Geheimnisclientbibliothek für JavaScript. [Azure Key Vault](../general/overview.md) ist ein Clouddienst, der als sicherer Geheimnisspeicher fungiert. Dadurch können Schlüssel, Kennwörter, Zertifikate und andere Geheimnisse sicher gespeichert werden. Azure Key Vault-Instanzen können über das Azure-Portal erstellt und verwaltet werden. In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der JavaScript-Clientbibliothek Geheimnisse in einem Azure-Schlüsseltresor erstellen, daraus abrufen und löschen.

Ressourcen der Key Vault-Clientbibliothek:

[API-Referenzdokumentation](/javascript/api/overview/azure/key-vault-index) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [Paket (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

Weitere Informationen zu Key Vault und Geheimnissen finden Sie unter folgenden Links:
- [Übersicht über Key Vault](../general/overview.md)
- [Informationen zu Azure Key Vault-Geheimnissen](about-secrets.md)

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement ([kostenloses Abonnement erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
- Aktuelle [Node.js](https://nodejs.org) für Ihr Betriebssystem.
- [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)
- Eine Key Vault-Instanz. Diese kann über das [Azure-Portal](../general/quick-create-portal.md), mithilfe der [Azure CLI](../general/quick-create-cli.md) oder per [Azure PowerShell](../general/quick-create-powershell.md) erstellt werden.

In dieser Schnellstartanleitung wird davon ausgegangen, dass Sie die [Azure CLI](/cli/azure/install-azure-cli) verwenden.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

1. Führen Sie den Befehl `login` aus.

    ```azurecli-interactive
    az login
    ```

    Die CLI öffnet Ihren Standardbrowser, sofern sie dazu in der Lage ist, und lädt eine Azure-Anmeldeseite.

    Öffnen Sie andernfalls die Browserseite [https://aka.ms/devicelogin](https://aka.ms/devicelogin), und geben Sie den in Ihrem Terminal angezeigten Autorisierungscode ein.

2. Melden Sie sich im Browser mit Ihren Anmeldeinformationen an.

## <a name="create-new-nodejs-application"></a>Erstellen einer neuen Node.js-Anwendung

Als Nächstes erstellen Sie eine Node.js-Anwendung, die in der Cloud bereitgestellt werden kann. 

1. Erstellen Sie in einer Befehlsshell einen Ordner mit dem Namen `key-vault-node-app`:

```azurecli
mkdir key-vault-node-app
```

1. Wechseln Sie zum neu erstellten Verzeichnis *key-vault-node-app*, und führen Sie den Befehl „init“ aus, um das Knotenprojekt zu initialisieren:

```azurecli
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Installieren von Key Vault-Paketen

Installieren Sie über das Konsolenfenster die Azure Key Vault-[Geheimnisbibliothek](https://www.npmjs.com/package/@azure/keyvault-secrets) für Node.js.

```azurecli
npm install @azure/keyvault-secrets
```

Installieren Sie das Paket [azure.identity](https://www.npmjs.com/package/@azure/identity) für die Authentifizierung bei Key Vault:

```azurecli
npm install @azure/identity
```

## <a name="set-environment-variables"></a>Festlegen von Umgebungsvariablen

Diese Anwendung verwendet den Namen des Schlüsseltresors als Umgebungsvariable namens `KEY_VAULT_NAME`.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS oder Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="grant-access-to-your-key-vault"></a>Gewähren des Zugriffs auf Ihren Schlüsseltresor

Erstellen Sie eine Zugriffsrichtlinie für Ihren Schlüsseltresor, die Ihrem Benutzerkonto Geheimnisberechtigungen erteilt:

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

## <a name="code-examples"></a>Codebeispiele

In den folgenden Codebeispielen wird gezeigt, wie Sie einen Client erstellen und ein Geheimnis festlegen, abrufen und löschen. 

### <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

1. Erstellen Sie eine neue Textdatei, und speichern Sie sie als „index.js“.

1. Fügen Sie require-Aufrufe zum Laden von Azure- und Node.js-Modulen hinzu.

1. Erstellen der Struktur für das Programm, einschließlich einer einfachen Ausnahmebehandlung

```javascript
const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {
    
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
```

### <a name="add-directives"></a>Hinzufügen von Anweisungen

Fügen Sie am Anfang Ihres Codes die folgenden Anweisungen hinzu:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Authentifizieren und Erstellen eines Clients

In dieser Schnellstartanleitung wird der angemeldete Benutzer zum Authentifizieren beim Schlüsseltresor verwendet. Dies ist die bevorzugte Methode für die lokale Entwicklung. Bei Anwendungen, die in Azure bereitgestellt werden, sollte die verwaltete Identität App Service oder einem virtuellen Computer zugewiesen werden. Weitere Informationen finden Sie in der [Übersicht zu verwalteten Identitäten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Im folgenden Beispiel wird der Name Ihres Schlüsseltresors in den Schlüsseltresor-URI mit dem Format „https://\<your-key-vault-name\>.vault.azure.net“ erweitert. In diesem Beispiel wird die Klasse [DefaultAzureCredential()](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) aus der [Azure Identity-Bibliothek](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme) verwendet, wodurch der gleiche Code in verschiedenen Umgebungen mit verschiedenen Optionen für die Identitätsbereitstellung verwendet werden kann. Weitere Informationen zur Authentifizierung beim Schlüsseltresor finden Sie im [Entwicklerhandbuch](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

Fügen Sie der Funktion „main()“ den folgenden Code hinzu:

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Speichern eines Geheimnisses

Nachdem Ihre Anwendung nun authentifiziert ist, können Sie mithilfe der [Methode „setSecret“](/javascript/api/@azure/keyvault-secrets/secretclient?#setsecret-string--string--setsecretoptions-) ein Geheimnis in Ihrem Schlüsseltresor platzieren. Hierzu benötigen Sie einen Namen für das Geheimnis. In diesem Beispiel wird „mySecret“ verwendet.  

```javascript
await client.setSecret(secretName, secretValue);
```

### <a name="retrieve-a-secret"></a>Abrufen eines Geheimnisses

Nun können Sie den zuvor festgelegten Wert mithilfe der [Methode „getSecret“](/javascript/api/@azure/keyvault-secrets/secretclient?#getsecret-string--getsecretoptions-) abrufen.

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

Ihr Geheimnis ist jetzt als `retrievedSecret.value` gespeichert.

### <a name="delete-a-secret"></a>Löschen eines Geheimnisses

Verwenden Sie abschließend die Methode [beginDeleteSecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#beginDeleteSecret_string__BeginDeleteSecretOptions_), um das Geheimnis aus Ihrem Schlüsseltresor zu löschen, und die Methode [purgeDeletedSecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#purgeDeletedSecret_string__PurgeDeletedSecretOptions_), um es endgültig zu löschen.

```javascript
const deletePoller = await client.beginDeleteSecret(secretName);
await deletePoller.pollUntilDone();
await client.purgeDeletedSecret(secretName);
```

## <a name="sample-code"></a>Beispielcode

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new SecretClient(KVUri, credential);

  const secretName = "mySecret";
  var secretValue = await askQuestion("Input the value of your secret > ");

  console.log("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");
  await client.setSecret(secretName, secretValue);

  console.log("Done.");

  console.log("Forgetting your secret.");
  secretValue = "";
  console.log("Your secret is '" + secretValue + "'.");

  console.log("Retrieving your secret from " + keyVaultName + ".");

  const retrievedSecret = await client.getSecret(secretName);

  console.log("Your secret is '" + retrievedSecret.value + "'.");

  console.log("Deleting your secret from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteSecret(secretName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your secret from {keyVaultName} ...");
  await client.purgeDeletedSecret(secretName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Testen und Überprüfen

1. Führen Sie die folgenden Befehle aus, um die App auszuführen:

    ```azurecli
    npm install
    npm index.js
    ```

1. Geben Sie bei entsprechender Aufforderung einen Geheimniswert ein, z. B. „mySecretPassword“.

    Eine Variation der folgenden Ausgabe wird angezeigt:

    ```azurecli
    Input the value of your secret > mySecretPassword
    Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
    Forgetting your secret.
    Your secret is ''.
    Retrieving your secret from <your-unique-keyvault-name>.
    Your secret is 'mySecretPassword'.
    Deleting your secret from <your-unique-keyvault-name> ... done.  
    Purging your secret from <your-unique-keyvault-name> ... done.   
    ```


## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Schlüsseltresor erstellt, ein Geheimnis gespeichert und dieses Geheimnis abgerufen. Weitere Informationen zu Key Vault und zur Integration in Ihre Anwendungen finden Sie in den folgenden Artikeln:

- [Was ist der Azure-Schlüsseltresor?](../general/overview.md)
- [Informationen zu Azure Key Vault-Geheimnissen](about-secrets.md)
- [Sicherer Zugriff auf einen Schlüsseltresor](../general/secure-your-key-vault.md)
- [Entwicklerhandbuch zu Azure-Schlüsseltresor](../general/developers-guide.md)
- [Bewährte Methoden zum Verwenden von Key Vault](../general/best-practices.md)
