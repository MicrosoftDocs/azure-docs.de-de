---
title: 'Tutorial: Verwenden von Azure Key Vault mit einem virtuellen Computer in .NET | Microsoft-Dokumentation'
description: In diesem Tutorial konfigurieren Sie einen virtuellen Computer in einer ASP.NET Core-Anwendung zum Lesen eines Geheimnisses aus Ihrem Schlüsseltresor.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 9557ada8001022d460c35a091fdac7699ac5e5ec
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289392"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-net"></a>Tutorial: Verwenden von Azure Key Vault mit einem virtuellen Windows in .NET

Azure Key Vault unterstützt Sie beim Schutz von Geheimnissen wie API-Schlüsseln und Datenbank-Verbindungszeichenfolgen, die für den Zugriff auf Ihre Anwendungen, Dienste und IT-Ressourcen benötigt werden.

In diesem Tutorial wird beschrieben, wie Sie vorgehen müssen, damit eine Konsolenanwendung Informationen aus Azure Key Vault lesen kann. Von der Anwendung wird für die Authentifizierung bei Key Vault die verwaltete Identität des virtuellen Computers verwendet. 

Das Tutorial veranschaulicht folgende Vorgehensweisen:

> [!div class="checklist"]
> * Erstellen Sie eine Ressourcengruppe.
> * Erstellen eines Schlüsseltresors
> * Hinzufügen eines Geheimnisses zum Schlüsseltresor.
> * Abrufen eines Geheimnisses aus dem Schlüsseltresor
> * Erstellen Sie einen virtuellen Azure-Computer.
> * Aktivieren einer [verwalteten Identität](../../active-directory/managed-identities-azure-resources/overview.md) für den virtuellen Computer
> * Zuweisen von Berechtigungen zur VM-Identität

Machen Sie sich zunächst mit den [grundlegenden Konzepten von Key Vault](basic-concepts.md) vertraut. 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Für Windows, Mac und Linux:
  * [Git-Client](https://git-scm.com/downloads)
  * [.NET Core 3.1 SDK oder höher](https://dotnet.microsoft.com/download/dotnet-core/3.1)
  * [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-resources-and-assign-permissions"></a>Erstellen von Ressourcen und Zuweisen von Berechtigungen

Bevor Sie mit der Programmierung beginnen, müssen Sie einige Ressourcen erstellen, ein Geheimnis in Ihren Schlüsseltresor legen und Berechtigungen zuweisen.

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

Geben Sie Folgendes ein, um sich mithilfe der Azure-Befehlszeilenschnittstelle bei Azure anzumelden:

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>Erstellen einer Ressourcengruppe und eines Schlüsseltresors

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Einfügen eines Geheimnisses in Ihren Schlüsseltresor

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers
Erstellen Sie mit einer der folgenden Methoden einen virtuellen Windows- oder Linux-Computer:

| Windows | Linux |
|--|--|
| [Azure-Befehlszeilenschnittstelle](../../virtual-machines/windows/quick-create-cli.md) | [Azure-Befehlszeilenschnittstelle](../../virtual-machines/linux/quick-create-cli.md) |  
| [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) | [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) |
| [Azure portal](../../virtual-machines/windows/quick-create-portal.md) | [Azure portal](../../virtual-machines/linux/quick-create-portal.md) |

## <a name="assign-an-identity-to-the-vm"></a>Zuweisen einer Identität zum virtuellen Computer
Erstellen Sie für den virtuellen Computer mit dem Befehl [az vm identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) eine vom System zugewiesene Identität:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Beachten Sie die systemseitig zugewiesene Identität, die im folgenden Code angezeigt wird. Die Ausgabe des obigen Befehls lautet etwa wie folgt: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Zuweisen von Berechtigungen für die VM-Identität
Weisen Sie Ihrem Schlüsseltresor mit dem Befehl [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) die zuvor erstellten Identitätsberechtigungen zu:

```azurecli
az keyvault set-policy --name '<your-unique-key-vault-name>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="sign-in-to-the-virtual-machine"></a>Anmelden beim virtuellen Computer

Befolgen Sie zum Anmelden beim virtuellen Computer die Anleitung unter [Herstellen einer Verbindung mit einem virtuellen Azure-Computer unter Windows und Anmelden auf diesem Computer](../../virtual-machines/windows/connect-logon.md) bzw. [Vorschau: Anmelden bei einem virtuellen Linux-Computer in Azure mit der Azure Active Directory-Authentifizierung](../../virtual-machines/linux/login-using-aad.md).

## <a name="set-up-the-console-app"></a>Einrichten der Konsolen-App

Erstellen Sie eine Konsolen-App, und installieren Sie die erforderlichen Pakete mit dem `dotnet`-Befehl.

### <a name="install-net-core"></a>.NET Core installieren

Wechseln Sie zur [.NET-Downloadseite](https://www.microsoft.com/net/download), um .NET Core zu installieren.

### <a name="create-and-run-a-sample-net-app"></a>Erstellen und Ausführen einer .NET-Beispiel-App

Öffnen Sie eine Eingabeaufforderung.

Sie können „Hallo Welt“ auf der Konsole ausgeben, indem Sie die folgenden Befehle ausführen:

```console
dotnet new console -n keyvault-console-app
cd keyvault-console-app
dotnet run
```

### <a name="install-the-package"></a>Installieren des Pakets

Installieren Sie über das Konsolenfenster die Azure Key Vault-Geheimnisclientbibliothek für .NET:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

In dieser Schnellstartanleitung muss für die Authentifizierung bei Azure Key Vault das folgende Identitätspaket installiert werden:

```console
dotnet add package Azure.Identity
```

## <a name="edit-the-console-app"></a>Bearbeiten der Konsolen-App

Öffnen Sie die Datei *Program.cs* , und fügen Sie die folgenden Pakete hinzu:

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

Fügen Sie diese Zeilen hinzu, und aktualisieren Sie den URI, sodass er dem Tresor-URI (`vaultUri`) Ihres Schlüsseltresors entspricht. Im folgenden Code wird ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) für die Authentifizierung beim Schlüsseltresor verwendet. Dabei wird das Token der verwalteten Anwendungsidentität zur Authentifizierung herangezogen. Darüber hinaus wird das exponentielle Backoff für Wiederholungen verwendet, falls der Schlüsseltresor gedrosselt wird.

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            var kvUri = "https://<your-key-vault-name>.vault.azure.net";
            SecretClientOptions options = new SecretClientOptions()
            {
                Retry =
                {
                    Delay= TimeSpan.FromSeconds(2),
                    MaxDelay = TimeSpan.FromSeconds(16),
                    MaxRetries = 5,
                    Mode = RetryMode.Exponential
                 }
            };

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential(),options);

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie den virtuellen Computer und Ihren Schlüsseltresor, wenn diese nicht mehr benötigt werden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Azure Key Vault-REST-API](/rest/api/keyvault/)