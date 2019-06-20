---
title: 'Tutorial: Verwenden von Azure Key Vault mit einer Azure-Web-App in .NET | Microsoft-Dokumentation'
description: In diesem Tutorial konfigurieren Sie eine ASP.NET Core-Anwendung zum Lesen eines Geheimnisses aus Ihrem Schlüsseltresor.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 5129af1f34f8a2604e7b70c9638b370c7cad029a
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66015540"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Tutorial: Verwenden von Azure Key Vault mit einer Azure-Web-App in .NET

Azure Key Vault unterstützt Sie beim Schützen von Geheimnissen wie API-Schlüsseln und Datenbank-Verbindungszeichenfolgen. Die Lösung ermöglicht Ihnen den Zugriff auf Ihre Anwendungen, Dienste und IT-Ressourcen.

In diesem Tutorial erfahren Sie, wie Sie eine Azure-Webanwendung erstellen, die Informationen aus einer Azure Key Vault-Instanz lesen kann. Dabei werden verwaltete Identitäten für Azure-Ressourcen verwendet. Weitere Informationen zu Azure-Webanwendungen finden Sie unter [Azure App Service](../app-service/overview.md).

Das Tutorial veranschaulicht folgende Vorgehensweisen:

> [!div class="checklist"]
> * Erstellen eines Schlüsseltresors
> * Hinzufügen eines Geheimnisses zum Schlüsseltresor.
> * Abrufen eines Geheimnisses aus dem Schlüsseltresor
> * Erstellen einer Azure-Web-App
> * Aktivieren einer verwalteten Identität für die Web-App
> * Zuweisen von Berechtigungen für die Web-App
> * Ausführen der Web-App in Azure

Machen Sie sich zunächst mit den [grundlegenden Konzepten von Key Vault](key-vault-whatis.md#basic-concepts) vertraut. 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen.

## <a name="prerequisites"></a>Voraussetzungen

* Für Windows: [.NET Core 2.1 SDK oder höher](https://www.microsoft.com/net/download/windows)
* Für Mac: [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/)
* Für Windows, Mac und Linux:
  * [Git-Client](https://git-scm.com/downloads)
  * Für dieses Tutorial ist es erforderlich, dass Sie die Azure CLI lokal ausführen. Hierfür muss Azure CLI Version 2.0.4 oder höher installiert sein. Führen Sie `az --version` aus, um die Version zu finden. Informationen zur Installation und Aktualisierung der CLI finden Sie bei Bedarf unter [Installieren von Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>Informationen zur verwalteten Dienstidentität

Azure Key Vault dient zum sicheren Speichern von Anmeldeinformationen, damit diese nicht in Ihrem Code erscheinen. Sie müssen sich jedoch bei Azure Key Vault authentifizieren, um Ihre Schlüssel abzurufen. Und für die Authentifizierung bei Key Vault benötigen Sie wiederum Anmeldeinformationen. Ein klassisches Bootstrap-Dilemma. Die verwaltete Dienstidentität (Managed Service Identity, MSI) löst dieses Problem, indem sie eine _Bootstrap-Identität_ bereitstellt, die den Prozess vereinfacht.

Wenn Sie MSI für einen Azure-Dienst aktivieren (beispielsweise für Azure Virtual Machines, Azure App Service oder Azure Functions), erstellt Azure einen [Dienstprinzipal](key-vault-whatis.md#basic-concepts). MSI führt diesen Schritt für die Instanz des Diensts in Azure Active Directory (Azure AD) aus und fügt die Anmeldeinformationen des Dienstprinzipals in diese Instanz ein.

![MSI-Diagramm](media/MSI.png)

Als Nächstes ruft Ihr Code einen lokalen, für die Azure-Ressource verfügbaren Metadatendienst auf, um ein Zugriffstoken zu erhalten. Ihr Code verwendet das vom lokalen MSI-Endpunkt erhaltene Zugriffstoken für die Authentifizierung bei einem Azure Key Vault-Dienst.

## <a name="log-in-to-azure"></a>Anmelden an Azure

Geben Sie Folgendes ein, um sich mithilfe der Azure-Befehlszeilenschnittstelle bei Azure anzumelden:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe.

Wählen Sie anschließend einen Namen für die Ressourcengruppe aus, und fügen Sie einen Wert für den Platzhalter ein. Im folgenden Beispiel wird eine Ressourcengruppe am Standort „USA, Westen“ erstellt:

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Diese Ressourcengruppe wird im gesamten Tutorial verwendet.

## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

Geben Sie die folgenden Informationen an, um einen Schlüsseltresor in Ihrer Ressourcengruppe zu erstellen:

* Name des Schlüsseltresors: eine Zeichenfolge mit 3 bis 24 Zeichen, die nur Zahlen (0 - 9), Buchstaben (a - z, A - Z) und Bindestriche (-) enthalten darf.
* Ressourcengruppenname
* Standort: **USA, Westen**

Geben Sie in der Azure-Befehlszeilenschnittstelle den folgenden Befehl ein:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

An diesem Punkt ist nur Ihr Azure-Konto zum Ausführen von Vorgängen für den neuen Tresor autorisiert.

## <a name="add-a-secret-to-the-key-vault"></a>Hinzufügen eines Geheimnisses zum Schlüsseltresor

Nun können Sie ein Geheimnis hinzufügen. Dabei kann es sich beispielsweise um eine SQL-Verbindungszeichenfolge oder um beliebige andere Informationen handeln, die sowohl sicher aufbewahrt werden als auch für Ihre Anwendung verfügbar sein müssen.

Geben Sie den folgenden Befehl ein, um im Schlüsseltresor ein Geheimnis namens **AppSecret** zu erstellen: 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Dieses Geheimnis speichert den Wert **MySecret**.

Geben Sie den folgenden Befehl ein, um den im Geheimnis enthaltenen Wert als Klartext anzuzeigen:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Mit diesem Befehl werden die Geheimnisinformationen einschließlich des URI angezeigt. 

Nach der Ausführung dieser Schritte sollten Sie einen URI zu einem Geheimnis in einem Schlüsseltresor besitzen. Notieren Sie sich diese Informationen zur späteren Verwendung in diesem Tutorial. 

## <a name="create-a-net-core-web-app"></a>Erstellen einer .NET Core-Web-App

Befolgen Sie die Anleitung unter [Erstellen von ASP.NET Core-Web-Apps in Azure](../app-service/app-service-web-get-started-dotnet.md), um eine .NET Core-Web-App zu erstellen und in Azure zu veröffentlichen. 

Empfehlenswert ist auch folgendes Video:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Öffnen und Bearbeiten der Lösung

1. Navigieren Sie zur Datei **Seiten** > **About.cshtml.cs**.

1. Installieren Sie die folgenden NuGet-Pakete:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. Importieren Sie den folgenden Code in die Datei *About.cshtml.cs*:

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

   Der Code in der AboutModel-Klasse sollte wie folgt aussehen:

   ```csharp
    public class AboutModel : PageModel
    {
        public string Message { get; set; }

        public async Task OnGetAsync()
        {
            Message = "Your application description page.";
            int retries = 0;
            bool retry = false;
            try
            {
                /* The next four lines of code show you how to use AppAuthentication library to fetch secrets from your key vault */
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;
            }
            /* If you have throttling errors see this tutorial https://docs.microsoft.com/azure/key-vault/tutorial-net-create-vault-azure-web-app */
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
            }
        }

        // This method implements exponential backoff if there are 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory, which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-web-app"></a>Ausführen der Web-App

1. Wählen Sie im Hauptmenü von Visual Studio 2019 **Debuggen** > **Starten** (mit oder ohne Debuggen) aus. 
1. Navigieren Sie im Browser zur Seite **Info**.  
    Der Wert für **AppSecret** wird angezeigt.

## <a name="enable-a-managed-identity"></a>Aktivieren einer verwalteten Identität

Azure Key Vault ermöglicht das sichere Speichern von Anmeldeinformationen und anderen Geheimnissen. Um diese abrufen zu können, muss sich Ihr Code jedoch bei Key Vault authentifizieren. Dieses Problem können Sie anhand der Informationen unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md) lösen, indem Sie Azure-Diensten eine automatisch verwaltete Identität in Azure AD bereitstellen. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung, einschließlich Key Vault, unterstützt. Hierfür müssen im Code keine Anmeldeinformationen angezeigt werden.

Führen Sie über die Azure-Befehlszeilenschnittstelle den Befehl „assign-identity“ aus, um die Identität für diese Anwendung zu erstellen:

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

Ersetzen Sie „\<YourAppName>“ durch den Namen der veröffentlichten App in Azure.  
    Wenn Ihre veröffentlichte App also beispielsweise **MyAwesomeapp.azurewebsites.net** heißt, ersetzen Sie „\<YourAppName>“ durch **MyAwesomeapp**.

Notieren Sie sich den Wert von `PrincipalId`, wenn Sie die Anwendung in Azure veröffentlichen. Die Ausgabe des Befehls in Schritt 1 hat folgendes Format:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>Der Befehl in dieser Vorgehensweise ist identisch mit dem Aufrufen des [Azure-Portals](https://portal.azure.com) und dem anschließenden Festlegen der Einstellung **Identität/Vom System zugewiesen** auf **Ein** (in den Webanwendungseigenschaften).

## <a name="assign-permissions-to-your-app"></a>Zuweisen von Berechtigungen für Ihre App

Ersetzen Sie im folgenden Befehl „\<YourKeyVaultName>“ durch den Namen Ihres Schlüsseltresors und „\<PrincipalId>“ durch den Wert von **PrincipalId**:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Dieser Befehl erteilt der Identität (MSI) des App-Diensts die Berechtigung zum Ausführen von **get**- und **list**-Vorgängen für Ihren Schlüsseltresor.

## <a name="publish-the-web-app-to-azure"></a>Veröffentlichen der Web-App in Azure

Veröffentlichen Sie Ihre Web-App erneut in Azure, um sich zu vergewissern, dass Ihre Live-Web-App den Geheimniswert abrufen kann.

1. Wählen Sie in Visual Studio das Projekt **key-vault-dotnet-core-quickstart** aus.
2. Klicken Sie auf **Veröffentlichen** > **Starten**.
3. Klicken Sie auf **Erstellen**.

Wenn Sie die Anwendung ausführen, sollte sie den Wert des Geheimnisses abrufen können.

Sie haben nun erfolgreich eine Web-App in .NET erstellt, die ihre Geheimnisse aus Ihrem Schlüsseltresor speichert und daraus abruft.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Sie können den virtuellen Computer und Ihren Schlüsseltresor löschen, wenn diese Komponenten nicht mehr benötigt werden.

## <a name="next-steps"></a>Nächste Schritte

>[!div class="nextstepaction"]
>[Entwicklerhandbuch zu Azure-Schlüsseltresor](https://docs.microsoft.com/azure/key-vault/key-vault-developers-guide)
