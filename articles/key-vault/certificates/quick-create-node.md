---
title: 'Schnellstart: Azure Key Vault-Zertifikatclientbibliothek für JavaScript (Version 4)'
description: Hier erfahren Sie, wie Sie mithilfe der JavaScript-Clientbibliothek Zertifikate in einem Azure-Schlüsseltresor erstellen, daraus abrufen und löschen.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 3854b7491bf068bf7130180f483905531f053f7c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841630"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-javascript-version-4"></a>Schnellstart: Azure Key Vault-Zertifikatclientbibliothek für JavaScript (Version 4)

Hier finden Sie Informationen zu den ersten Schritten mit der Azure Key Vault-Zertifikatclientbibliothek für JavaScript. [Azure Key Vault](../general/overview.md) ist ein Clouddienst, der als sicherer Speicher für Zertifikate fungiert. Dadurch können Schlüssel, Kennwörter, Zertifikate und andere Geheimnisse sicher gespeichert werden. Azure Key Vault-Instanzen können über das Azure-Portal erstellt und verwaltet werden. In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der JavaScript-Clientbibliothek Zertifikate in einem Azure-Schlüsseltresor erstellen, daraus abrufen und löschen.

Ressourcen der Key Vault-Clientbibliothek:

[API-Referenzdokumentation](/javascript/api/overview/azure/key-vault-index) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [Paket (npm)](https://www.npmjs.com/package/@azure/keyvault-certificates)

Weitere Informationen zu Key Vault und Zertifikaten finden Sie unter folgenden Links:
- [Übersicht über Key Vault](../general/overview.md)
- [Informationen zu Azure Key Vault-Zertifikaten](about-certificates.md)

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

Installieren Sie über das Konsolenfenster die Azure Key Vault-[Zertifikatbibliothek](https://www.npmjs.com/package/@azure/keyvault-certificates) für Node.js:

```azurecli
npm install @azure/keyvault-certificates
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

Erstellen Sie eine Zugriffsrichtlinie für Ihren Schlüsseltresor, die Ihrem Benutzerkonto Zertifikatberechtigungen erteilt:

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create purge
```

## <a name="code-examples"></a>Codebeispiele

In den folgenden Codebeispielen wird gezeigt, wie Sie einen Client erstellen, ein Zertifikat festlegen, ein Zertifikat abrufen und ein Zertifikat löschen. 

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
const { CertificateClient } = require("@azure/keyvault-certificates");
```

### <a name="authenticate-and-create-a-client"></a>Authentifizieren und Erstellen eines Clients

In dieser Schnellstartanleitung wird der angemeldete Benutzer zum Authentifizieren beim Schlüsseltresor verwendet. Dies ist die bevorzugte Methode für die lokale Entwicklung. Bei Anwendungen, die in Azure bereitgestellt werden, sollte die verwaltete Identität App Service oder einem virtuellen Computer zugewiesen werden. Weitere Informationen finden Sie in der [Übersicht zu verwalteten Identitäten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Im folgenden Beispiel wird der Name Ihres Schlüsseltresors in den Schlüsseltresor-URI mit dem Format „https://\<your-key-vault-name\>.vault.azure.net“ erweitert. In diesem Beispiel wird die Klasse [DefaultAzureCredential()](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) aus der [Azure Identity-Bibliothek](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme) verwendet, wodurch der gleiche Code in verschiedenen Umgebungen mit verschiedenen Optionen für die Identitätsbereitstellung verwendet werden kann. Weitere Informationen zur Authentifizierung beim Schlüsseltresor finden Sie im [Entwicklerhandbuch](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

Fügen Sie der Funktion „main()“ den folgenden Code hinzu:

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new Certificate(KVUri, credential);
```

### <a name="save-a-certificate"></a>Speichern eines Zertifikats

Nachdem Ihre Anwendung nun authentifiziert ist, können Sie mithilfe der [Methode „beginCreateCertificate“](/javascript/api/@azure/keyvault-certificates/certificateclient?#beginCreateCertificate_string__CertificatePolicy__BeginCreateCertificateOptions_) ein Zertifikat in Ihrem Schlüsseltresor platzieren. Hierfür sind ein Name für das Zertifikat und die [Zertifikatrichtlinie](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicy) mit [Zertifikatrichtlinieneigenschaften](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicyproperties) erforderlich.

```javascript
const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
};
const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
const certificate = await poller.pollUntilDone();
```

> [!NOTE]
> Ist der Zertifikatname vorhanden, erstellt der obige Code eine neue Version dieses Zertifikats.
### <a name="retrieve-a-certificate"></a>Abrufen eines Zertifikats

Nun können Sie mithilfe der [Methode „getCertificate“](/javascript/api/@azure/keyvault-certificates/certificateclient?#getCertificate_string__GetCertificateOption) den zuvor festgelegten Wert abrufen.

```javascript
const retrievedCertificate = await client.getCertificate(certificateName);
 ```

### <a name="delete-a-certificate"></a>Löschen eines Zertifikats

Verwenden Sie abschließend die Methode [beginDeleteCertificate]https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#beginDeleteCertificate_string__BeginDeleteCertificateOptions_), um das Zertifikat aus Ihrem Schlüsseltresor zu löschen, und die Methode [purgeDeletedCertificate](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#purgeDeletedCertificate_string__PurgeDeletedCertificateOptions_), um es endgültig zu löschen.

```javascript
const deletePoller = await client.beginDeleteCertificate(certificateName);
await deletePoller.pollUntilDone();
await client.purgeDeletedCertificate(certificateName);
```

## <a name="sample-code"></a>Beispielcode

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { CertificateClient } = require("@azure/keyvault-certificates");

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

  const string certificateName = "myCertificate";
  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new CertificateClient(KVUri, credential);

  console.log("Creating a certificate in " + keyVaultName + " called '" + certificateName +  "` ...");
  const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
  };
  const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
  const certificate = await poller.pollUntilDone();

  console.log("Done.");

  console.log("Retrieving your certificate from " + keyVaultName + ".");

  const retrievedCertificate = await client.getCertificate(certificateName);

  console.log("Your certificate version is '" + retrievedCertificate.properties.version + "'.");

  console.log("Deleting your certificate from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteCertificate(certificateName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your certificate from {keyVaultName} ...");
  await client.purgeDeletedCertificate(certificateName);
  
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
Creating a certificate in mykeyvault called 'myCertificate' ... done.
Retrieving your certificate from mykeyvault.
Your certificate version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your certificate from mykeyvault ... done
Purging your certificate from mykeyvault ... done 
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Schlüsseltresor erstellt, ein Zertifikat gespeichert und dieses Zertifikat abgerufen. Weitere Informationen zu Key Vault und zur Integration in Ihre Anwendungen finden Sie in den folgenden Artikeln:

- [Was ist der Azure-Schlüsseltresor?](../general/overview.md)
- [Informationen zu Azure Key Vault-Zertifikaten](about-certificates.md)
- [Tutorial: Zugreifen auf Key Vault von einer App Service-Anwendung](../general/tutorial-net-create-vault-azure-web-app.md)
- [Tutorial: Zugreifen auf Key Vault von einem virtuellen Computer](../general/tutorial-net-virtual-machine.md)
- [Entwicklerhandbuch zu Azure-Schlüsseltresor](../general/developers-guide.md)
- [Bewährte Methoden zum Verwenden von Key Vault](../general/best-practices.md)
