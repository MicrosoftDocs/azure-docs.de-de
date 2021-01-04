---
title: 'Schnellstart: Verwenden von Azure Key Vault-Geheimnissen in GitHub Actions-Workflows'
description: Verwenden von Key Vault-Geheimnissen in GitHub Actions zum Automatisieren Ihrer Workflows für die Softwareentwicklung
author: juliakm
ms.custom: github-actions-azure
ms.author: jukullam
ms.date: 11/24/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 9509f84b14a42180189a529282b5db348deab279
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95920231"
---
# <a name="use-key-vault-secrets-in-github-actions-workflows"></a>Verwenden von Key Vault-Geheimnissen in GitHub Actions-Workflows

Verwenden Sie Key Vault-Geheimnisse auf Ihrer [GitHub Actions](https://help.github.com/en/articles/about-github-actions)-Instanz, und nutzen Sie die sichere Speicherung von Kennwörtern und anderen Geheimnissen in einem Azure-Schlüsseltresor. Informieren Sie sich über [Key Vault](../general/overview.md). 

Bei Key Vault und GitHub Actions profitieren Sie von einem zentralisierten Tool für die Geheimnisverwaltung und von allen weiteren GitHub Actions-Vorteilen. GitHub Actions ist eine Suite mit Features in GitHub, mit denen Sie Ihre Workflows für die Softwareentwicklung automatisieren können. Sie können Workflows an demselben Ort bereitstellen, an dem Sie Code speichern und an Pull Requests und Problemen zusammenarbeiten. 


## <a name="prerequisites"></a>Voraussetzungen 
- Ein GitHub-Konto. Falls Sie noch nicht über ein Konto verfügen, können Sie sich [kostenlos](https://github.com/join) registrieren.  
- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine Azure-App mit Verbindung mit einem GitHub-Repository. In diesem Beispiel wird [Bereitstellen von Containern in Azure App Service](https://docs.microsoft.com/azure/developer/javascript/tutorial-vscode-docker-node-01) verwendet. 
- Ein Azure-Schlüsseltresor.  Sie können eine Azure Key Vault-Instanz im Azure-Portal, mit der Azure-Befehlszeilenschnittstelle oder mit Azure PowerShell erstellen.

## <a name="workflow-file-overview"></a>Übersicht über die Workflowdatei

Ein Workflow wird durch eine YAML-Datei im Pfad `/.github/workflows/` in Ihrem Repository definiert. Diese Definition enthält die verschiedenen Schritte und Parameter, die den Workflow bilden.

Die Datei enthält zwei Abschnitte für die Authentifizierung mit GitHub Actions:

|`Section`  |Aufgaben  |
|---------|---------|
|**Authentifizierung** | 1. Definieren eines Dienstprinzipals. <br /> 2. Erstellen eines GitHub-Geheimnisses. <br /> 3. Hinzufügen einer Rollenzuweisung. |
|**Schlüsseltresor** | 1. Hinzufügen der Schlüsseltresoraktion. <br /> 2. Verweisen auf das Schlüsseltresorgeheimnis. |

## <a name="authentication"></a>Authentifizierung

Sie können mit dem Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) in der [Azure CLI](/cli/azure/) einen [Dienstprinzipal](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) erstellen. Führen Sie diesen Befehl mit [Azure Cloud Shell](https://shell.azure.com/) im Azure-Portal oder durch Klicken auf die Schaltfläche **Ausprobieren** aus.

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

Ersetzen Sie im obigen Beispiel die Platzhalter durch Ihre Abonnement-ID und den Ressourcengruppennamen. Ersetzen Sie den Platzhalter `myApp` durch den Namen Ihrer Anwendung. Die Ausgabe ist ein JSON-Objekt mit den Anmeldeinformationen für die Rollenzuweisung, die ähnlich wie unten Zugriff auf Ihre App Service-App gewähren. Kopieren Sie dieses JSON-Objekt zur späteren Verwendung. Sie benötigen nur die Abschnitte mit den Werten `clientId`, `clientSecret`, `subscriptionId` und `tenantId`. 

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

### <a name="configure-the-github-secret"></a>Konfigurieren des GitHub-Geheimnisses

Erstellen Sie Geheimnisse für Ihre Azure-Anmeldeinformationen, Ressourcengruppe und Abonnements. 

1. Suchen Sie auf [GitHub](https://github.com/) nach Ihrem Repository.

1. Wählen Sie **Settings > Secrets > New secret** (Einstellungen > Geheimnisse > Neues Geheimnis) aus.

1. Fügen Sie die gesamte JSON-Ausgabe aus dem Azure CLI-Befehl in das Wertfeld des Geheimnisses ein. Geben Sie dem Geheimnis den Namen `AZURE_CREDENTIALS`.

1. Kopieren Sie den Wert von `clientId` zur späteren Verwendung. 

### <a name="add-a-role-assignment"></a>Hinzufügen einer Rollenzuweisung 
 
Sie müssen Zugriff auf den Azure-Dienstprinzipal gewähren, damit Sie für `get`- und `list`-Vorgänge auf Ihren Schlüsseltresor zugreifen können. Wenn Sie dies nicht tun, können Sie den Dienstprinzipal nicht verwenden. 

Ersetzen Sie `keyVaultName` durch den Namen Ihres Schlüsseltresors und `clientIdGUID` durch den Wert Ihrer `clientId`. 

```azurecli-interactive
    az keyvault set-policy -n {keyVaultName} --secret-permissions get list --spn {clientIdGUID}
```

## <a name="use-the-azure-key-vault-action"></a>Verwenden der Azure-Schlüsseltresoraktion

Mit der [Azure-Schlüsseltresoraktion](https://github.com/marketplace/actions/azure-key-vault-get-secrets) können Sie ein oder mehrere Geheimnisse von einer Azure-Schlüsseltresorinstanz abrufen und in Ihren GitHub Actions-Workflows nutzen.

Abgerufene Geheimnisse werden als Ausgaben und auch als Umgebungsvariablen festgelegt. Variablen werden automatisch maskiert, wenn sie in der Konsole oder in Protokollen ausgegeben werden.

```yaml
    - uses: Azure/get-keyvault-secrets@v1
      with:
        keyvault: "my Vault" # name of key vault in Azure portal
        secrets: 'mySecret'  # comma separated list of secret keys to fetch from key vault 
      id: myGetSecretAction # ID for secrets that you will reference
```

Zum Verwenden eines Schlüsseltresors in Ihrem Workflow benötigen Sie sowohl die Schlüsseltresoraktion als auch einen Verweis auf die Aktion. 

In diesem Beispiel hat der Schlüsseltresor den Namen `containervault`. Der Umgebung werden mit den Schlüsseltresoraktionen `containerPassword` und `containerUsername` zwei Schlüsseltresorgeheimnisse hinzugefügt. 

Auf die Schlüsseltresorwerte wird später in der Docker-Anmeldeaufgabe mit dem Präfix `steps.myGetSecretAction.outputs` verwiesen. 

```yaml
name: Example key vault flow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - uses: actions/checkout@v2
    - uses: Azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - uses: Azure/get-keyvault-secrets@v1
      with: 
        keyvault: "containervault"
        secrets: 'containerPassword, containerUsername'
      id: myGetSecretAction
    - uses: azure/docker-login@v1
      with:
        login-server: myregistry.azurecr.io
        username: ${{ steps.myGetSecretAction.outputs.containerUsername }}
        password: ${{ steps.myGetSecretAction.outputs.containerPassword }}
    - run: |
        docker build . -t myregistry.azurecr.io/myapp:${{ github.sha }}
        docker push myregistry.azurecr.io/myapp:${{ github.sha }}     
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'myregistry.azurecr.io/myapp:${{ github.sha }}'
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie Ihre Azure-App, das GitHub-Repository und den Schlüsseltresor nicht mehr benötigen, sollten Sie die bereitgestellten Ressourcen bereinigen, indem Sie die Ressourcengruppe für die App, das GitHub-Repository und den Schlüsseltresor löschen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zu Azure Key Vault](../general/overview.md)
