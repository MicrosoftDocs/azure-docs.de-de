---
title: 'Schnellstart: Azure Key Vault-Geheimnisclientbibliothek für .NET (Version 4)'
description: Hier Erfahren Sie, wie Sie mithilfe der .NET-Clientbibliothek (Version 4) Geheimnisse in einem Azure-Schlüsseltresor erstellen, daraus abrufen und löschen.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: dcf7c8db955b2e85ad7d1c047c714eb2c5968455
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780806"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-net-sdk-v4"></a>Schnellstart: Azure Key Vault-Geheimnisclientbibliothek für .NET (SDK v4)

Hier finden Sie Informationen zu den ersten Schritten mit der Azure Key Vault-Geheimnisclientbibliothek für .NET. [Azure Key Vault](../general/overview.md) ist ein Clouddienst, der als sicherer Geheimnisspeicher fungiert. Dadurch können Schlüssel, Kennwörter, Zertifikate und andere Geheimnisse sicher gespeichert werden. Azure Key Vault-Instanzen können über das Azure-Portal erstellt und verwaltet werden. In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der .NET-Clientbibliothek Geheimnisse in einem Azure-Schlüsseltresor erstellen, daraus abrufen und löschen.

Ressourcen der Key Vault-Clientbibliothek:

[API-Referenzdokumentation](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet&preserve-view=true) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Paket (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

Weitere Informationen zu Key Vault und Geheimnissen finden Sie unter folgenden Links:
- [Übersicht über Key Vault](../general/overview.md)
- [Informationen zu Azure Key Vault-Geheimnissen](about-secrets.md)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement: [Kostenloses Azure-Konto](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK oder höher](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)
* Eine Key Vault-Instanz. Diese kann über das [Azure-Portal](../general/quick-create-portal.md), mithilfe der [Azure CLI](../general/quick-create-cli.md) oder per [Azure PowerShell](../general/quick-create-powershell.md) erstellt werden.

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

### <a name="grant-access-to-your-key-vault"></a>Gewähren des Zugriffs auf Ihren Schlüsseltresor

Erstellen Sie eine Zugriffsrichtlinie für Ihren Schlüsseltresor, die Ihrem Benutzerkonto Geheimnisberechtigungen erteilt:

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
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

Installieren Sie über die Befehlsshell die Azure Key Vault-Geheimnisclientbibliothek für .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
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

Mit der Azure Key Vault-Geheimnisclientbibliothek für .NET können Sie Geheimnisse verwalten. Im Abschnitt [Codebeispiele](#code-examples) wird gezeigt, wie ein Client erstellt und ein Geheimnis festgelegt, abgerufen und gelöscht wird.

## <a name="code-examples"></a>Codebeispiele

### <a name="add-directives"></a>Hinzufügen von Anweisungen

Fügen Sie am Anfang von *Program.cs* die folgenden Anweisungen hinzu:

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Authentifizieren und Erstellen eines Clients

In dieser Schnellstartanleitung wird der angemeldete Benutzer zum Authentifizieren beim Schlüsseltresor verwendet. Dies ist die bevorzugte Methode für die lokale Entwicklung. Bei Anwendungen, die in Azure bereitgestellt werden, sollte die verwaltete Identität App Service oder einem virtuellen Computer zugewiesen werden. Weitere Informationen finden Sie in der [Übersicht zu verwalteten Identitäten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Im folgenden Beispiel wird der Name Ihres Schlüsseltresors in den Schlüsseltresor-URI mit dem Format „https://\<your-key-vault-name\>.vault.azure.net“ erweitert. In diesem Beispiel wird die Klasse [DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential) aus der [Azure Identity-Bibliothek](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme) verwendet, wodurch der gleiche Code in verschiedenen Umgebungen mit verschiedenen Optionen für die Identitätsbereitstellung verwendet werden kann. Weitere Informationen zur Authentifizierung beim Schlüsseltresor finden Sie im [Entwicklerhandbuch](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Speichern eines Geheimnisses

Nachdem die Konsolen-App authentifiziert wurde, fügen Sie dem Schlüsseltresor ein Geheimnis hinzu. Verwenden Sie hierfür die [SetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.setsecretasync)-Methode. Der erste Parameter der Methode akzeptiert einen Namen für das Geheimnis (in diesem Beispiel „mySecret“).

```csharp
await client.SetSecretAsync(secretName, secretValue);
```

> [!NOTE]
> Ist der Geheimnisname vorhanden, erstellt der obige Code eine neue Version dieses Geheimnisses.


### <a name="retrieve-a-secret"></a>Abrufen eines Geheimnisses

Nun können Sie den zuvor festgelegten Wert mithilfe der [GetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.getsecretasync)-Methode abrufen.

```csharp
var secret = await client.GetSecretAsync(secretName);
```

Ihr Geheimnis ist jetzt als `secret.Value` gespeichert.

### <a name="delete-a-secret"></a>Löschen eines Geheimnisses

Verwenden Sie abschließend die Methoden [StartDeleteSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.startdeletesecretasync) und [PurgeDeletedSecretAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.purgedeletedsecretasync), um das Geheimnis aus Ihrem Schlüsseltresor zu löschen.

```csharp
var operation = await client.StartDeleteSecretAsync("mySecret");
// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

await client.PurgeDeletedKeyAsync("mySecret");
```

## <a name="sample-code"></a>Beispielcode

Führen Sie die folgenden Schritte aus, um die .NET Core-Konsolen-App so zu ändern, dass Sie mit dem Schlüsseltresor interagiert:

1. Ersetzen Sie den Code in *Program.cs* durch den folgenden Code:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Secrets;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                await client.SetSecretAsync(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                var secret = await client.GetSecretAsync(secretName);
                Console.WriteLine($"Your secret is '{secret.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                DeleteSecretOperation operation = await client.StartDeleteSecretAsync(secretName);
                // You only need to wait for completion if you want to purge or recover the secret.
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
                
                Console.Write($"Purging your secret from {keyVaultName} ...");
                await client.PurgeDeletedSecretAsync(secretName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Testen und Überprüfen

1. Führen Sie die App mit dem folgenden Befehl aus.

    ```dotnetcli
    dotnet run
    ```

1. Geben Sie bei entsprechender Aufforderung einen Geheimniswert ein, z. B. „mySecretPassword“.

Eine Variation der folgenden Ausgabe wird angezeigt:

```console
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

Weitere Informationen zu Key Vault und zur Integration in Ihre Apps finden Sie in den folgenden Artikeln:

- [Was ist der Azure-Schlüsseltresor?](../general/overview.md)
- [Tutorial: Zugreifen auf Key Vault von einer App Service-Anwendung](../general/tutorial-net-create-vault-azure-web-app.md)
- [Tutorial: Zugreifen auf Key Vault von einem virtuellen Computer](../general/tutorial-net-virtual-machine.md)
- [Entwicklerhandbuch zu Azure-Schlüsseltresor](../general/developers-guide.md)
- [Bewährte Methoden zum Verwenden von Key Vault](../general/best-practices.md)