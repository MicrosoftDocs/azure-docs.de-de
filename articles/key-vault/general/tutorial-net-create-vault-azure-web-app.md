---
title: 'Tutorial: Verwenden von Azure Key Vault mit einer Azure-Web-App in .NET'
description: In diesem Tutorial konfigurieren Sie eine Azure-Web-App in einer ASP.NET Core-Anwendung zum Lesen eines Geheimnisses aus Ihrem Schlüsseltresor.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 6bb1aafd942046faa77072d99af043ebd43b4a8a
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589966"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-in-net"></a>Tutorial: Verwenden einer verwalteten Identität für die Verbindungsherstellung zwischen Key Vault und einer Azure-Web-App in .NET

[Azure Key Vault](./overview.md) ermöglicht das Speichern von Anmeldeinformationen und anderen Geheimnissen mit erhöhter Sicherheit. Für Ihren Code muss aber die Authentifizierung bei Key Vault erfolgen, damit der Abruf möglich ist. Mit den [verwalteten Identitäten für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md) kann dieses Problem gelöst werden, indem für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory (Azure AD) bereitgestellt wird. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung, einschließlich Key Vault, unterstützt. Hierfür müssen im Code keine Anmeldeinformationen angezeigt werden.

In diesem Tutorial verwenden Sie eine verwaltete Identität, um eine Azure-Web-App gegenüber einer Azure Key Vault-Instanz zu authentifizieren. Sie verwenden die [Azure Key Vault-Geheimnisclientbibliothek für .NET](/dotnet/api/overview/azure/key-vault) und die [Azure CLI](/cli/azure/get-started-with-azure-cli). Die gleichen grundlegenden Prinzipien gelten auch, wenn Sie eine Entwicklungssprache Ihrer Wahl, Azure PowerShell bzw. das Azure-Portal nutzen.

## <a name="prerequisites"></a>Voraussetzungen

Für die Durchführung dieses Schnellstarts benötigen Sie Folgendes:

* Ein Azure-Abonnement. [Erstellen Sie ein kostenloses Abonnement.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [.NET Core 3.1 SDK (oder höher)](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Eine [Git](https://www.git-scm.com/downloads)-Installation.
* Die [Azure CLI](/cli/azure/install-azure-cli) oder [Azure PowerShell](/powershell/azure/).
* [Azure Key Vault:](./overview.md) Sie können eine Azure Key Vault-Instanz erstellen, indem Sie das [Azure-Portal](quick-create-portal.md), die [Azure CLI](quick-create-cli.md) oder [Azure PowerShell](quick-create-powershell.md) verwenden.
* Ein Key Vault-[Geheimnis](../secrets/about-secrets.md). Sie können ein Geheimnis erstellen, indem Sie das [Azure-Portal](../secrets/quick-create-portal.md), [PowerShell](../secrets/quick-create-powershell.md) oder die [Azure CLI](../secrets/quick-create-cli.md) verwenden.

## <a name="create-a-net-core-app"></a>Erstellen einer .NET Core-App
In diesem Schritt richten Sie das lokale .NET Core-Projekt ein.

Erstellen Sie in einem Terminalfenster auf Ihrem Computer ein Verzeichnis mit dem Namen `akvwebapp`, und wechseln Sie in dieses Verzeichnis:

```bash
mkdir akvwebapp
cd akvwebapp
```

Erstellen Sie eine .NET Core-App, indem Sie den Befehl [dotnet new web](/dotnet/core/tools/dotnet-new) verwenden:

```bash
dotnet new web
```

Führen Sie die Anwendung lokal aus, damit Sie wissen, wie sie beim Bereitstellen in Azure aussehen sollte:

```bash
dotnet run
```

Wechseln Sie in einem Webbrowser unter `http://localhost:5000` zur App.

Die Nachricht „Hello World!“ aus der Beispiel-App wird auf der Seite angezeigt.

## <a name="deploy-the-app-to-azure"></a>Bereitstellen der Anwendung in Azure

In diesem Schritt stellen Sie Ihre .NET Core-Anwendung unter Verwendung einer lokalen Git-Instanz in Azure App Service bereit. Weitere Informationen zum Erstellen und Bereitstellen von Anwendungen finden Sie unter [Schnellstart: Erstellen von ASP.NET Core-Web-Apps in Azure](../../app-service/quickstart-dotnetcore.md).

### <a name="configure-the-local-git-deployment"></a>Konfigurieren der lokalen Git-Bereitstellung

Drücken Sie im Terminalfenster **STRG+C**, um den Webserver zu schließen.  Initialisieren Sie ein Git-Repository für das .NET Core-Projekt:

```bash
git init
git add .
git commit -m "first commit"
```

Sie können für die Bereitstellung einer Azure-Web-App FTP und eine lokale Git-Instanz mit einem *Bereitstellungsbenutzer* verwenden. Nach der Konfiguration des Bereitstellungsbenutzers können Sie ihn für alle Azure-Bereitstellungen verwenden. Der Benutzername und das Kennwort für die Bereitstellung auf Kontoebene unterscheiden sich von den Anmeldeinformationen für Ihr Azure-Abonnement. 

Führen Sie zum Konfigurieren des Bereitstellungsbenutzers den Befehl [az webapp deployment user set](/cli/azure/webapp/deployment/user?#az-webapp-deployment-user-set) aus. Wählen Sie einen Benutzernamen und das zugehörige Kennwort gemäß den folgenden Richtlinien aus: 

- Der Benutzername muss in Azure eindeutig sein. Bei lokalen Git-Pushvorgängen darf er kein at-Zeichen (@) enthalten. 
- Das Kennwort muss mindestens acht Zeichen lang sein und zwei der folgenden drei Elemente enthalten: Buchstaben, Zahlen und Symbole. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

In der JSON-Ausgabe wird das Kennwort als `null` angezeigt. Wenn Sie den Fehler `'Conflict'. Details: 409` erhalten, müssen Sie den Benutzernamen ändern. Wenn Sie den Fehler `'Bad Request'. Details: 400` erhalten, müssen Sie ein sichereres Kennwort verwenden. 

Notieren Sie Ihren Benutzernamen und das zugehörige Kennwort, damit Ihnen diese Angaben für die Bereitstellung Ihrer Web-Apps vorliegen.

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist ein logischer Container, in dem Sie Azure-Ressourcen bereitstellen und verwalten. Verwenden Sie den Befehl [az group create](/cli/azure/group?#az-group-create) für die Erstellung einer Ressourcengruppe, die sowohl Ihren Schlüsseltresor als auch Ihre Web-App enthält:

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>Wie erstelle ich einen Plan?

Erstellen Sie mit dem Azure CLI-Befehl [az appservice plan create](/cli/azure/appservice/plan) einen [App Service-Plan](../../app-service/overview-hosting-plans.md). Im folgenden Beispiel wird ein App Service-Plan namens `myAppServicePlan` im Tarif `FREE` erstellt:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Wenn der App Service-Plan erstellt wird, werden von der Azure CLI Informationen der folgenden Art angezeigt:

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>

Weitere Informationen finden Sie unter [Verwalten eines App Service-Plans in Azure](../../app-service/app-service-plan-manage.md).

### <a name="create-a-web-app"></a>Erstellen einer Web-App

Erstellen Sie eine [Azure-Web-App](../../app-service/overview.md) im App Service-Plan `myAppServicePlan`. 

> [!Important]
> Eine Azure-Web-App muss genau wie ein Schlüsseltresor einen eindeutigen Namen haben. Ersetzen Sie in den folgenden Beispielen `<your-webapp-name>` durch den Namen Ihrer Web-App.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

Wenn die Web-App erstellt wird, werden von der Azure CLI in etwa die folgenden Informationen angezeigt:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>


Die URL des Git-Remotespeicherorts wird in der `deploymentLocalGitUrl`-Eigenschaft im Format `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git` angezeigt. Speichern Sie diese URL. Sie benötigen die Information später.

Navigieren Sie mit dem folgenden Befehl zu Ihrer neuen App. Ersetzen Sie `<your-webapp-name>` durch den Namen Ihrer App.

```bash
https://<your-webapp-name>.azurewebsites.net
```

Die Standardwebseite für eine neue Azure-Web-App wird angezeigt.

### <a name="deploy-your-local-app"></a>Bereitstellen Ihrer lokalen App

Kehren Sie zum lokalen Terminalfenster zurück, und fügen Sie Ihrem lokalen Git-Repository einen Azure-Remotespeicherort hinzu. Ersetzen Sie im folgenden Befehl `<deploymentLocalGitUrl-from-create-step>` durch die URL des Git-Remotespeicherorts, die Sie im Abschnitt [Erstellen einer Web-App](#create-a-web-app) gespeichert haben.

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Verwenden Sie den unten angegebenen Befehl, um zur Bereitstellung Ihrer App eine Pushübertragung zum Azure-Remotespeicherort durchzuführen. Wenn Sie von Git Credential Manager zur Eingabe von Anmeldeinformationen aufgefordert werden, verwenden Sie die Anmeldeinformationen, die Sie im Abschnitt [Konfigurieren der lokalen Git-Bereitstellung](#configure-the-local-git-deployment) erstellt haben.

```bash
git push azure main
```

Die Ausführung dieses Befehls kann einige Minuten dauern. Während der Ausführung werden Informationen der folgenden Art angezeigt:
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  main -> main
</pre>

Navigieren Sie in Ihrem Webbrowser zur bereitgestellten Anwendung, oder aktualisieren Sie die Ansicht:

```bash
http://<your-webapp-name>.azurewebsites.net
```

Die Nachricht „Hello World!“ wird angezeigt, die Sie schon vom Zugriff auf `http://localhost:5000` kennen.
 
## <a name="configure-the-web-app-to-connect-to-key-vault"></a>Konfigurieren der Web-App für die Verbindungsherstellung mit Key Vault

In diesem Abschnitt konfigurieren Sie den Webzugriff auf Key Vault und aktualisieren Ihren Anwendungscode, um ein Geheimnis aus Key Vault abzurufen.

### <a name="create-and-assign-a-managed-identity"></a>Erstellen und Zuweisen einer verwalteten Identität

In diesem Tutorial verwenden wir eine [verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) für die Key Vault-Authentifizierung. Mit der verwalteten Identität werden die Anmeldeinformationen der Anwendung automatisch verwaltet.

Führen Sie über die Azure-Befehlszeilenschnittstelle den Befehl [az webapp-identity assign](/cli/azure/webapp/identity?#az-webapp-identity-assign) aus, um die Identität für die Anwendung zu erstellen:

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

Vom Befehl wird der folgende JSON-Codeausschnitt zurückgegeben:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Übergeben Sie die `principalId` an den Azure CLI-Befehl [az keyvault set-policy](/cli/azure/keyvault?#az-keyvault-set-policy), um Ihrer Web-App die Berechtigung zum Ausführen von Abruf- und Auflistungsvorgängen (**get** und **list**) für Ihren Schlüsseltresor zu ermöglichen:

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

Sie können auch Zugriffsrichtlinien über das [Azure-Portal](./assign-access-policy-portal.md) oder per [PowerShell](./assign-access-policy-powershell.md) zuweisen.

### <a name="modify-the-app-to-access-your-key-vault"></a>Anpassen der App für den Zugriff auf Ihren Schlüsseltresor

In diesem Tutorial verwenden Sie zu Demonstrationszwecken die [Azure Key Vault-Geheimnisclientbibliothek](https://docs.microsoft.com/dotnet/api/overview/azure/security.keyvault.secrets-readme). Sie können auch die [Azure Key Vault-Zertifikatclientbibliothek](https://docs.microsoft.com/dotnet/api/overview/azure/security.keyvault.certificates-readme) oder die [Azure Key Vault-Schlüsselclientbibliothek](https://docs.microsoft.com/dotnet/api/overview/azure/security.keyvault.keys-readme) verwenden.

#### <a name="install-the-packages"></a>Installieren der Pakete

Installieren Sie über das Terminalfenster die Pakete für die Azure Key Vault-Geheimnisclientbibliothek für .NET und die Azure Identity-Clientbibliothek:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>Aktualisieren des Codes

Navigieren Sie im Projekt „akvwebapp“ zur Datei „Startup.cs“, und öffnen Sie sie. 

Fügen Sie dem Header die folgenden Zeilen hinzu:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Fügen Sie die folgenden Zeilen vor dem Aufruf von `app.UseEndpoints` hinzu, und aktualisieren Sie den URI, damit er dem Tresor-URI (`vaultUri`) Ihres Schlüsseltresors entspricht. In diesem Code wird [DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential) für die Key Vault-Authentifizierung genutzt. Hierbei wird ein Token der verwalteten Identität für Authentifizierungszwecke verwendet. Weitere Informationen zur Key Vault-Authentifizierung finden Sie im [Entwicklerhandbuch](./developers-guide.md#authenticate-to-key-vault-in-code). Darüber hinaus wird im Code das exponentielle Backoff für Wiederholungen verwendet, falls Key Vault gedrosselt wird. Weitere Informationen zu Transaktionsgrenzwerten für Key Vault finden Sie in der [Anleitung zur Drosselung von Azure Key Vault](./overview-throttling.md).

```csharp
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
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("<mySecret>");

string secretValue = secret.Value;
```

Aktualisieren Sie die Zeile `await context.Response.WriteAsync("Hello World!");` so, dass sie wie folgt aussieht:

```csharp
await context.Response.WriteAsync(secretValue);
```

Achten Sie darauf, dass Sie Ihre Änderungen speichern, bevor Sie mit dem nächsten Schritt fortfahren.

#### <a name="redeploy-your-web-app"></a>Erneutes Bereitstellen Ihrer Web-App

Nachdem Sie Ihren Code aktualisiert haben, können Sie ihn mit diesen Git-Befehlen erneut in Azure bereitstellen:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure main
```

## <a name="go-to-your-completed-web-app"></a>Navigieren zur fertigen Web-App

```bash
http://<your-webapp-name>.azurewebsites.net
```

Anstelle der zuvor angezeigten Nachricht „Hello World! sollte nun der Wert Ihres Geheimnisses angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Verwenden von Azure Key Vault mit einem virtuellen Computer in .NET](./tutorial-net-virtual-machine.md)
- Weitere Informationen zu verwalteten Identitäten für Azure-Ressourcen finden Sie [hier](../../active-directory/managed-identities-azure-resources/overview.md).
- Sehen Sie sich das [Entwicklerhandbuch](./developers-guide.md) an.
- [Sicherer Zugriff auf einen Schlüsseltresor](./secure-your-key-vault.md)
