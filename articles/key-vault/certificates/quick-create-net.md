---
title: 'Schnellstart: Azure Key Vault-Zertifikatclientbibliothek für .NET (Version 4)'
description: Hier erfahren Sie, wie Sie mithilfe der .NET-Clientbibliothek (Version 4) Zertifikate in einem Azure-Schlüsseltresor erstellen, daraus abrufen und löschen.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b40a13b84a0191b5c454d7edac2226f8f06fadcd
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780160"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-net-sdk-v4"></a>Schnellstart: Azure Key Vault-Zertifikatclientbibliothek für .NET (SDK v4)

Hier finden Sie Informationen zu den ersten Schritten mit der Azure Key Vault-Zertifikatclientbibliothek für .NET. [Azure Key Vault](../general/overview.md) ist ein Clouddienst, der als sicherer Speicher für Zertifikate fungiert. Dadurch können Schlüssel, Kennwörter, Zertifikate und andere Geheimnisse sicher gespeichert werden. Azure Key Vault-Instanzen können über das Azure-Portal erstellt und verwaltet werden. In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der .NET-Clientbibliothek Zertifikate in einem Azure-Schlüsseltresor erstellen, daraus abrufen und löschen.

Ressourcen der Key Vault-Clientbibliothek:

[API-Referenzdokumentation](/dotnet/api/azure.security.keyvault.certificates) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Paket (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)

Weitere Informationen zu Key Vault und Zertifikaten finden Sie unter folgenden Links:
- [Übersicht über Key Vault](../general/overview.md)
- [Informationen zu Azure Key Vault-Zertifikaten](about-certificates.md)

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

Erstellen Sie eine Zugriffsrichtlinie für Ihren Schlüsseltresor, die Ihrem Benutzerkonto Zertifikatberechtigungen erteilt:

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --certificate-permissions delete get list create purge
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

Installieren Sie über die Befehlsshell die Azure Key Vault-Zertfikatclientbibliothek für .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Certificates
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

Mit der Azure Key Vault-Zertifikatclientbibliothek für .NET können Sie Zertifikate verwalten. Im Abschnitt [Codebeispiele](#code-examples) wird veranschaulicht, wie Sie einen Client erstellen und ein Zertifikat festlegen, abrufen und löschen.

## <a name="code-examples"></a>Codebeispiele

### <a name="add-directives"></a>Hinzufügen von Anweisungen

Fügen Sie am Anfang von *Program.cs* die folgenden Anweisungen hinzu:

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Certificates;
```

### <a name="authenticate-and-create-a-client"></a>Authentifizieren und Erstellen eines Clients

In dieser Schnellstartanleitung wird der angemeldete Benutzer zum Authentifizieren beim Schlüsseltresor verwendet. Dies ist die bevorzugte Methode für die lokale Entwicklung. Bei Anwendungen, die in Azure bereitgestellt werden, sollte die verwaltete Identität App Service oder einem virtuellen Computer zugewiesen werden. Weitere Informationen finden Sie in der [Übersicht zu verwalteten Identitäten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Im folgenden Beispiel wird der Name Ihres Schlüsseltresors in den Schlüsseltresor-URI mit dem Format „https://\<your-key-vault-name\>.vault.azure.net“ erweitert. In diesem Beispiel wird die Klasse [DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential) aus der [Azure Identity-Bibliothek](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme) verwendet, wodurch der gleiche Code in verschiedenen Umgebungen mit verschiedenen Optionen für die Identitätsbereitstellung verwendet werden kann. Weitere Informationen zur Authentifizierung beim Schlüsseltresor finden Sie im [Entwicklerhandbuch](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

```csharp
string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new CertificateClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-certificate"></a>Speichern eines Zertifikats

In diesem Beispiel können Sie der Einfachheit halber das selbstsignierte Zertifikat mit der Standardausstellungsrichtlinie verwenden. Verwenden Sie für diese Aufgabe die [StartCreateCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.startcreatecertificateasync)-Methode. Die Parameter der Methode akzeptieren einen Zertifikatnamen und die [Zertifikatrichtlinie](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.certificates.certificatepolicy).

```csharp
var operation = await client.StartCreateCertificateAsync("myCertificate", CertificatePolicy.Default);
var certificate = await operation.WaitForCompletionAsync();
```

> [!NOTE]
> Ist der Zertifikatname vorhanden, erstellt der obige Code eine neue Version dieses Zertifikats.

### <a name="retrieve-a-certificate"></a>Abrufen eines Zertifikats

Nun können Sie das zuvor erstellte Zertifikat mithilfe der [GetCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.getcertificateasync)-Methode abrufen.

```csharp
var certificate = await client.GetCertificateAsync("myCertificate");
```

### <a name="delete-a-certificate"></a>Löschen eines Zertifikats

Abschließend löschen Sie den Schlüssel mit der Methode [StartDeleteCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.startdeletecertificateasync) aus Ihrem Schlüsseltresor und bereinigen ihn mit der Methode [PurgeDeletedCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.purgedeletedcertificateasync).

```csharp
var operation = await client.StartDeleteCertificateAsync("myCertificate");

// You only need to wait for completion if you want to purge or recover the certificate.
await operation.WaitForCompletionAsync();

var certificate = operation.Value;
await client.PurgeDeletedCertificateAsync("myCertificate");
```

## <a name="sample-code"></a>Beispielcode

Führen Sie die folgenden Schritte aus, um die .NET Core-Konsolen-App so zu ändern, dass Sie mit dem Schlüsseltresor interagiert:

- Ersetzen Sie den Code in *Program.cs* durch den folgenden Code:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Certificates;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string certificateName = "myCertificate";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new CertificateClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write($"Creating a certificate in {keyVaultName} called '{certificateName}' ...");
                CertificateOperation operation = await client.StartCreateCertificateAsync(certificateName, CertificatePolicy.Default);
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
    
                Console.WriteLine($"Retrieving your certificate from {keyVaultName}.");
                var certificate = await client.GetCertificateAsync(certificateName);
                Console.WriteLine($"Your certificate version is '{certificate.Value.Properties.Version}'.");
    
                Console.Write($"Deleting your certificate from {keyVaultName} ...");
                DeleteCertificateOperation deleteOperation = await client.StartDeleteCertificateAsync(certificateName);
                // You only need to wait for completion if you want to purge or recover the certificate.
                await deleteOperation.WaitForCompletionAsync();
                Console.WriteLine(" done.");

                Console.Write($"Purging your certificate from {keyVaultName} ...");
                await client.PurgeDeletedCertificateAsync(certificateName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Testen und Überprüfen

Ausführen des folgenden Befehls zum Erstellen des Projekts

```dotnetcli
dotnet build
```

Eine Variation der folgenden Ausgabe wird angezeigt:

```console
Creating a certificate in mykeyvault called 'myCertificate' ... done.
Retrieving your certificate from mykeyvault.
Your certificate version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your certificate from mykeyvault ... done
Purging your certificate from mykeyvault ... done
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Schlüsseltresor erstellt, ein Zertifikat gespeichert und dieses Zertifikat abgerufen. 

Weitere Informationen zu Key Vault und zur Integration in Ihre Apps finden Sie in den folgenden Artikeln:

- [Was ist der Azure-Schlüsseltresor?](../general/overview.md)
- [Informationen zu Azure Key Vault-Zertifikaten](about-certificates.md)
- [Tutorial: Zugreifen auf Key Vault von einer App Service-Anwendung](../general/tutorial-net-create-vault-azure-web-app.md)
- [Tutorial: Zugreifen auf Key Vault von einem virtuellen Computer](../general/tutorial-net-virtual-machine.md)
- [Entwicklerhandbuch zu Azure-Schlüsseltresor](../general/developers-guide.md)
- [Bewährte Methoden zum Verwenden von Key Vault](../general/best-practices.md)
