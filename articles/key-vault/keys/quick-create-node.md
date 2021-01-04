---
title: 'Schnellstart: Azure Key Vault-Schlüsselclientbibliothek für JavaScript (Version 4)'
description: Hier erfahren Sie, wie Sie mithilfe der JavaScript-Clientbibliothek Schlüssel in einem Azure-Schlüsseltresor erstellen, daraus abrufen und löschen.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 1d842a4eaf0ff4afbc61ca58847747c3da57b1da
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841636"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-javascript-version-4"></a>Schnellstart: Azure Key Vault-Schlüsselclientbibliothek für JavaScript (Version 4)

Hier finden Sie Informationen zu den ersten Schritten mit der Azure Key Vault-Schlüsselclientbibliothek für JavaScript. [Azure Key Vault](../general/overview.md) ist ein Clouddienst, der als sicherer Speicher für kryptografische Schlüssel fungiert. Dadurch können Schlüssel, Kennwörter, Zertifikate und andere Geheimnisse sicher gespeichert werden. Azure Key Vault-Instanzen können über das Azure-Portal erstellt und verwaltet werden. In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der JavaScript-Schlüsselclientbibliothek Schlüssel in einem Azure-Schlüsseltresor erstellen, daraus abrufen und löschen.

Ressourcen der Key Vault-Clientbibliothek:

[API-Referenzdokumentation](/javascript/api/overview/azure/key-vault-index) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [Paket (npm)](https://www.npmjs.com/package/@azure/keyvault-keys)

Weitere Informationen zu Key Vault und Schlüsseln finden Sie unter folgenden Links:
- [Übersicht über Key Vault](../general/overview.md)
- [Informationen zu Schlüsseln](about-keys.md)

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

Installieren Sie über das Konsolenfenster die Azure Key Vault-[Schlüsselbibliothek](https://www.npmjs.com/package/@azure/keyvault-keys) für Node.js.

```azurecli
npm install @azure/keyvault-keys
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

Erstellen Sie eine Zugriffsrichtlinie für Ihren Schlüsseltresor, die Ihrem Benutzerkonto Schlüsselberechtigungen erteilt:

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --key-permissions delete get list create purge
```

## <a name="code-examples"></a>Codebeispiele

In den folgenden Codebeispielen wird gezeigt, wie Sie einen Client erstellen, einen Schlüssel festlegen, einen Schlüssel abrufen und einen Schlüssel löschen. 

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
const { KeyClient } = require("@azure/keyvault-keys");
```

### <a name="authenticate-and-create-a-client"></a>Authentifizieren und Erstellen eines Clients

In dieser Schnellstartanleitung wird der angemeldete Benutzer zum Authentifizieren beim Schlüsseltresor verwendet. Dies ist die bevorzugte Methode für die lokale Entwicklung. Bei Anwendungen, die in Azure bereitgestellt werden, sollte die verwaltete Identität App Service oder einem virtuellen Computer zugewiesen werden. Weitere Informationen finden Sie in der [Übersicht zu verwalteten Identitäten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Im folgenden Beispiel wird der Name Ihres Schlüsseltresors in den Schlüsseltresor-URI mit dem Format „https://\<your-key-vault-name\>.vault.azure.net“ erweitert. In diesem Beispiel wird die Klasse [DefaultAzureCredential()](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) aus der [Azure Identity-Bibliothek](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme) verwendet, wodurch der gleiche Code in verschiedenen Umgebungen mit verschiedenen Optionen für die Identitätsbereitstellung verwendet werden kann. Weitere Informationen zur Authentifizierung beim Schlüsseltresor finden Sie im [Entwicklerhandbuch](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

Fügen Sie der Funktion „main()“ den folgenden Code hinzu:

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new KeyClient(KVUri, credential);
```

### <a name="save-a-key"></a>Speichern eines Schlüssels

Nachdem Ihre Anwendung nun authentifiziert ist, können Sie mithilfe der [Methode „createKey“](/javascript/api/@azure/keyvault-keys/keyclient?#createKey_string__KeyType__CreateKeyOptions_) einen Schlüssel in Ihrem Schlüsseltresor platzieren. Von den Parametern der Methode werden ein Schlüsselname und der [Schlüsseltyp](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keytype) akzeptiert.

```javascript
await client.createKey(keyName, keyType);
```

### <a name="retrieve-a-key"></a>Abrufen eines Schlüssels

Nun können Sie den zuvor festgelegten Wert mithilfe der [Methode „getKey“](/javascript/api/@azure/keyvault-keys/keyclient?#getKey_string__GetKeyOptions_) abrufen.

```javascript
const retrievedKey = await client.getKey(keyName);
 ```

### <a name="delete-a-key"></a>Löschen eines Schlüssels

Verwenden Sie abschließend die Methode [beginDeleteKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#beginDeleteKey_string__BeginDeleteKeyOptions_), um den Schlüssel aus Ihrem Schlüsseltresor zu löschen, und die Methode [purgeDeletedKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#purgeDeletedKey_string__PurgeDeletedKeyOptions_), um ihn endgültig zu löschen.

```javascript
const deletePoller = await client.beginDeleteKey(keyName);
await deletePoller.pollUntilDone();
await client.purgeDeletedKey(keyName);
```

## <a name="sample-code"></a>Beispielcode

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { KeyClient } = require("@azure/keyvault-keys");

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
  const client = new KeyClient(KVUri, credential);

  const keyName = "myKey";
  
  console.log("Creating a key in " + keyVaultName + " called '" + keyName + "` ...");
  await client.createKey(keyName, "RSA");

  console.log("Done.");

  console.log("Retrieving your key from " + keyVaultName + ".");

  const retrievedKey = await client.getKey(keyName);

  console.log("Your key version is '" + retrievedKey.properties.version + "'.");

  console.log("Deleting your key from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteKey(keyName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your key from {keyVaultName} ...");
  await client.purgeDeletedKey(keyName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Testen und Überprüfen

Führen Sie die folgenden Befehle aus, um die App auszuführen:

```azurecli
npm install
npm index.js
```

Eine Variation der folgenden Ausgabe wird angezeigt:

```azurecli
Creating a key in <your-unique-keyvault-name> called 'myKey' ... done.
Retrieving your key from mykeyvault.
Your key version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your key from <your-unique-keyvault-name> ... done.  
Purging your key from <your-unique-keyvault-name> ... done.   
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Schlüsseltresor erstellt, einen Schlüssel gespeichert und diesen Schlüssel abgerufen. Weitere Informationen zu Key Vault und zur Integration in Ihre Anwendungen finden Sie in den folgenden Artikeln:

- [Was ist der Azure-Schlüsseltresor?](../general/overview.md)
- [Informationen zu Schlüsseln](about-keys.md)
- [Sicherer Zugriff auf einen Schlüsseltresor](../general/secure-your-key-vault.md)
- [Entwicklerhandbuch zu Azure-Schlüsseltresor](../general/developers-guide.md)
- [Bewährte Methoden zum Verwenden von Key Vault](../general/best-practices.md)
