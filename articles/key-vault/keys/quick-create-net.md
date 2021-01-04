---
title: 'Schnellstart: Azure Key Vault-Schlüsselclientbibliothek für .NET (Version 4)'
description: Hier erfahren Sie, wie Sie mithilfe der .NET-Clientbibliothek (Version 4) Schlüssel in einem Azure-Schlüsseltresor erstellen, daraus abrufen und löschen.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 77907b6e901ae074c879b4911a8ee755224a7948
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780415"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-net-sdk-v4"></a>Schnellstart: Azure Key Vault-Schlüsselclientbibliothek für .NET (SDK v4)

Hier finden Sie Informationen zu den ersten Schritten mit der Azure Key Vault-Schlüsselclientbibliothek für .NET. [Azure Key Vault](../general/overview.md) ist ein Clouddienst, der als sicherer Speicher für kryptografische Schlüssel fungiert. Dadurch können kryptografische Schlüssel, Kennwörter, Zertifikate und andere Geheimnisse sicher gespeichert werden. Azure Key Vault-Instanzen können über das Azure-Portal erstellt und verwaltet werden. In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der .NET-Schlüsselclientbibliothek Schlüssel in einem Azure-Schlüsseltresor erstellen, daraus abrufen und löschen.

Ressourcen der Key Vault-Schlüsselclientbibliothek:

[API-Referenzdokumentation](/dotnet/api/azure.security.keyvault.keys) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Paket (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.keys/)

Weitere Informationen zu Key Vault und Schlüsseln finden Sie unter folgenden Links:
- [Übersicht über Key Vault](../general/overview.md)
- [Informationen zu Schlüsseln](about-keys.md)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement: [Kostenloses Azure-Konto](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK oder höher](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)
* Eine Key Vault-Instanz. Diese kann über das [Azure-Portal](../general/quick-create-portal.md), die [Azure CLI](../general/quick-create-cli.md) oder mithilfe von [Azure PowerShell](../general/quick-create-powershell.md) erstellt werden.

In dieser Schnellstartanleitung werden `dotnet` und die Azure CLI verwendet.

## <a name="setup"></a>Einrichten

In dieser Schnellstartanleitung wird die Azure Identity-Bibliothek mit der Azure CLI verwendet, um den Benutzer bei Azure-Diensten zu authentifizieren. Entwickler können auch Visual Studio oder Visual Studio Code verwenden, um ihre Aufrufe zu authentifizieren. Weitere Informationen finden Sie unter [Authentifizieren des Clients mit der Azure Identity-Clientbibliothek](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true).

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

1. Führen Sie den Befehl `login` aus.

    ```azurecli-interactive
    az login
    ```

    Die CLI öffnet Ihren Standardbrowser, sofern sie dazu in der Lage ist, und lädt eine Azure-Anmeldeseite.

    Öffnen Sie andernfalls die Browserseite [https://aka.ms/devicelogin](https://aka.ms/devicelogin), und geben Sie den in Ihrem Terminal angezeigten Autorisierungscode ein.

2. Melden Sie sich im Browser mit Ihren Anmeldeinformationen an.

#### <a name="grant-access-to-your-key-vault"></a>Gewähren des Zugriffs auf Ihren Schlüsseltresor

Erstellen Sie eine Zugriffsrichtlinie für Ihren Schlüsseltresor, die Ihrem Benutzerkonto Schlüsselberechtigungen erteilt:

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
```

### <a name="create-new-net-console-app"></a>Erstellen einer neuen .NET-Konsolen-App

1. Führen Sie in einer Befehlsshell den folgenden Befehl aus, um ein Projekt namens `key-vault-console-app` zu erstellen:

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. Wechseln Sie zum neu erstellten Verzeichnis *key-vault-console-app*, und führen Sie den folgenden Befehl aus, um das Projekt zu erstellen:

    ```dotnetcli
    dotnet build
    ```

    Die Buildausgabe sollte keine Warnungen oder Fehler enthalten.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>Installieren der Pakete

Installieren Sie über die Befehlsshell die Azure Key Vault-Schlüsselclientbibliothek für .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Keys
```

Für diese Schnellstartanleitung müssen Sie auch die Azure SDK-Clientbibliothek für Azure Identity installieren:

```dotnetcli
dotnet add package Azure.Identity
```

#### <a name="set-environment-variables"></a>Festlegen von Umgebungsvariablen

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
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Objektmodell

Mit der Azure Key Vault-Schlüsselclientbibliothek für .NET können Sie Schlüssel verwalten. Im Abschnitt [Codebeispiele](#code-examples) wird gezeigt, wie ein Client erstellt und ein Schlüssel festgelegt, abgerufen und gelöscht wird.

## <a name="code-examples"></a>Codebeispiele

### <a name="add-directives"></a>Hinzufügen von Anweisungen

Fügen Sie am Anfang von *Program.cs* die folgenden Anweisungen hinzu:

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Keys;
```

### <a name="authenticate-and-create-a-client"></a>Authentifizieren und Erstellen eines Clients

In dieser Schnellstartanleitung wird der angemeldete Benutzer zum Authentifizieren beim Schlüsseltresor verwendet. Dies ist die bevorzugte Methode für die lokale Entwicklung. Bei Anwendungen, die in Azure bereitgestellt werden, sollte die verwaltete Identität App Service oder einem virtuellen Computer zugewiesen werden. Weitere Informationen finden Sie in der [Übersicht zu verwalteten Identitäten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Im folgenden Beispiel wird der Name Ihres Schlüsseltresors in den Schlüsseltresor-URI mit dem Format „https://\<your-key-vault-name\>.vault.azure.net“ erweitert. In diesem Beispiel wird die Klasse [DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential) aus der [Azure Identity-Bibliothek](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme) verwendet, wodurch der gleiche Code in verschiedenen Umgebungen mit verschiedenen Optionen für die Identitätsbereitstellung verwendet werden kann. Weitere Informationen zur Authentifizierung beim Schlüsseltresor finden Sie im [Entwicklerhandbuch](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

```csharp
var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-key"></a>Speichern eines Schlüssels

Verwenden Sie hierfür die [CreateKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.createkeyasync)-Methode. Die Parameter der Methode akzeptieren einen Schlüsselnamen und den [Schlüsseltyp](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.keys.keytype).

```csharp
var key = await client.CreateKeyAsync("myKey", KeyType.Rsa);
```

> [!NOTE]
> Ist der Schlüsselname vorhanden, erstellt der obige Code eine neue Version dieses Schlüssels.

### <a name="retrieve-a-key"></a>Abrufen eines Schlüssels

Nun können Sie den zuvor erstellten Schlüssel mithilfe der [CreateKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.getkeyasync)-Methode abrufen.

```csharp
var key = await client.GetKeyAsync("myKey");
```

### <a name="delete-a-key"></a>Löschen eines Schlüssels

Abschließend löschen Sie den Schlüssel mit der Methode [StartDeleteKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.startdeletekeyasync) aus Ihrem Schlüsseltresor und bereinigen ihn mit der Methode [PurgeDeletedKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.purgedeletedkeyasync).

```csharp
var operation = await client.StartDeleteKeyAsync("myKey");

// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

var key = operation.Value;
await client.PurgeDeletedKeyAsync("myKey");
```

## <a name="sample-code"></a>Beispielcode

Führen Sie die folgenden Schritte aus, um die .NET Core-Konsolen-App so zu ändern, dass Sie mit dem Schlüsseltresor interagiert:

- Ersetzen Sie den Code in *Program.cs* durch den folgenden Code:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Keys;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string keyName = "myKey";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write($"Creating a key in {keyVaultName} called '{keyName}' ...");
                var createdKey = await client.CreateKeyAsync(keyName, KeyType.Rsa);
                Console.WriteLine("done.");
    
                Console.WriteLine($"Retrieving your key from {keyVaultName}.");
                var key = await client.GetKeyAsync(keyName);
                Console.WriteLine($"Your key version is '{key.Value.Properties.Version}'.");
    
                Console.Write($"Deleting your key from {keyVaultName} ...");
                var deleteOperation = await client.StartDeleteKeyAsync(keyName);
                // You only need to wait for completion if you want to purge or recover the key.
                await deleteOperation.WaitForCompletionAsync();
                Console.WriteLine("done.");

                Console.Write($"Purging your key from {keyVaultName} ...");
                await client.PurgeDeletedKeyAsync(keyName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Testen und Überprüfen

1.  Ausführen des folgenden Befehls zum Erstellen des Projekts

    ```dotnetcli
    dotnet build
    ```

1. Führen Sie die App mit dem folgenden Befehl aus.

    ```dotnetcli
    dotnet run
    ```

1. Geben Sie bei entsprechender Aufforderung einen Geheimniswert ein, z. B. „mySecretPassword“.

    Eine Variation der folgenden Ausgabe wird angezeigt:

    ```console
    Creating a key in mykeyvault called 'myKey' ... done.
    Retrieving your key from mykeyvault.
    Your key version is '8532359bced24e4bb2525f2d2050738a'.
    Deleting your key from jl-kv ... done
    Purging your key from <your-unique-keyvault-name> ... done.   
    ```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Schlüsseltresor erstellt, einen Schlüssel gespeichert und diesen Schlüssel abgerufen. 

Weitere Informationen zu Key Vault und zur Integration in Ihre Apps finden Sie in den folgenden Artikeln:

- [Was ist der Azure-Schlüsseltresor?](../general/overview.md)
- [Informationen zu Schlüsseln](about-keys.md)
- [Tutorial: Zugreifen auf Key Vault von einer App Service-Anwendung](../general/tutorial-net-create-vault-azure-web-app.md)
- [Tutorial: Zugreifen auf Key Vault von einem virtuellen Computer](../general/tutorial-net-virtual-machine.md)
- [Entwicklerhandbuch zu Azure-Schlüsseltresor](../general/developers-guide.md)
- [Bewährte Methoden zum Verwenden von Key Vault](../general/best-practices.md)
