---
title: Benutzerdefinierte Container-CI/CD aus GitHub-Aktionen
description: Erfahren Sie, wie Sie mithilfe von GitHub-Aktionen Ihren benutzerdefinierten Linux-Container aus einer CI/CD-Pipeline in App Service bereitstellen.
ms.devlang: na
ms.topic: article
ms.date: 12/04/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: ae587b9501c9c68600ff880744d311ba966923ed
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008026"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Bereitstellen eines benutzerdefinierten Containers in App Service mithilfe von GitHub-Aktionen

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) bietet Ihnen die Flexibilität, einen automatisierten Softwareentwicklungsworkflow zu erstellen. Mit der [Azure Web Deploy-Aktion](https://github.com/Azure/webapps-deploy) können Sie Ihren Workflow automatisieren, um mithilfe von GitHub Actions benutzerdefinierte [App Service](overview.md)-Container bereitzustellen.

Ein Workflow wird durch eine YAML-Datei im Pfad `/.github/workflows/` in Ihrem Repository definiert. Diese Definition enthält die verschiedenen Schritte und Parameter im Workflow.

Bei einem Azure App Service-Containerworkflow umfasst die Datei drei Abschnitte:

|`Section`  |Aufgaben  |
|---------|---------|
|**Authentifizierung** | 1. Abrufen eines Dienstprinzipals oder Veröffentlichungsprofils. <br /> 2. Erstellen eines GitHub-Geheimnisses. |
|**Build** | 1. Erstellen Sie die Umgebung. <br /> 2. Erstellen des Containerimages |
|**Bereitstellen** | 1. Bereitstellen des Containerimages |

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [Kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Ein GitHub-Konto. Falls Sie noch nicht über ein Konto verfügen, können Sie sich [kostenlos](https://github.com/join) registrieren. Für die Bereitstellung in Azure App Service benötigen Sie Code in einem GitHub-Repository. 
- Eine funktionierende Containerregistrierung und eine Azure App Service-App für Container. In diesem Beispiel wird Azure Container Registry verwendet. Führen Sie die vollständige Bereitstellung in Azure App Service für Container durch. Im Unterschied zu regulären Web-Apps verfügen Web-Apps für Container nicht über eine standardmäßige Landing Page. Veröffentlichen Sie den Container, und erhalten Sie so ein funktionsfähiges Beispiel.
    - [Hier erfahren Sie, wie Sie eine Node.js-Containeranwendung mithilfe von Docker zu erstellen, das Containerimage in eine Registrierung pushen und das Image dann in Azure App Service bereitzustellen.](/azure/developer/javascript/tutorial-vscode-docker-node-01)
        
## <a name="generate-deployment-credentials"></a>Generieren von Anmeldeinformationen für die Bereitstellung

Die empfohlene Vorgehensweise für die Authentifizierung mit Azure App Services für GitHub Actions verwendet ein Veröffentlichungsprofil. Sie können sich auch mit einem Dienstprinzipal authentifizieren, für diesen Vorgang sind jedoch weitere Schritte erforderlich. 

Speichern Sie die Anmeldeinformationen des Veröffentlichungsprofils oder den Dienstprinzipal als [GitHub-Geheimnis](https://docs.github.com/en/actions/reference/encrypted-secrets), um sich bei Azure zu authentifizieren. In Ihrem Workflow greifen Sie auf das Geheimnis zu. 

# <a name="publish-profile"></a>[Veröffentlichungsprofil](#tab/publish-profile)

Ein Veröffentlichungsprofil stellt Anmeldeinformationen auf App-Ebene dar. Richten Sie Ihr Veröffentlichungsprofil als GitHub-Geheimnis ein. 

1. Wechseln Sie im Azure-Portal zu Ihrem App Service. 

1. Wählen Sie auf der Seite **Übersicht** die Option **Veröffentlichungsprofil abrufen**.

    > [!NOTE]
    > Ab Oktober 2020 muss für Linux-Web-Apps die App-Einstellung `WEBSITE_WEBDEPLOY_USE_SCM` auf `true` festgelegt werden, **ehe die Datei heruntergeladen wird**. Diese Anforderung wird künftig entfallen. Informationen zum Konfigurieren allgemeiner Web-App-Einstellungen finden Sie unter [Konfigurieren einer App Service-App im Azure-Portal](/azure/app-service/configure-common).  

1. Speichern Sie die heruntergeladene Datei. Zum Erstellen eines GitHub-Geheimnisses verwenden Sie den Inhalt der Datei.

# <a name="service-principal"></a>[Dienstprinzipal](#tab/service-principal)

Sie können mit dem Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) in der [Azure CLI](/cli/azure/) einen [Dienstprinzipal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) erstellen. Führen Sie diesen Befehl mit [Azure Cloud Shell](https://shell.azure.com/) im Azure-Portal oder durch Auswählen der Schaltfläche **Ausprobieren** aus.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

Ersetzen Sie im Beispiel die Platzhalter durch Ihre Abonnement-ID, den Ressourcengruppennamen und den App-Namen. Die Ausgabe ist ein JSON-Objekt mit den Anmeldeinformationen für die Rollenzuweisung, die Zugriff auf Ihre App Service-App gewähren. Kopieren Sie dieses JSON-Objekt zur späteren Verwendung.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Es ist immer empfehlenswert, den minimalen Zugriff zu gewähren. Der Bereich im vorherigen Beispiel ist auf die spezifische App Service-App und nicht auf die gesamte Ressourcengruppe beschränkt.

---
## <a name="configure-the-github-secret-for-authentication"></a>Konfigurieren des GitHub-Geheimnisses für die Authentifizierung

# <a name="publish-profile"></a>[Veröffentlichungsprofil](#tab/publish-profile)

Navigieren Sie in [GitHub](https://github.com/) zu Ihrem Repository, und wählen Sie **Einstellungen > Geheimnisse > Neues Geheimnis hinzufügen** aus.

Um [Anmeldeinformationen auf App-Ebene](#generate-deployment-credentials) zu verwenden, fügen Sie den Inhalt der heruntergeladenen Veröffentlichungsprofildatei in das Wertfeld des Geheimnisses ein. Geben Sie dem Geheimnis den Namen `AZURE_WEBAPP_PUBLISH_PROFILE`.

Wenn Sie Ihren GitHub-Workflow konfigurieren, verwenden Sie `AZURE_WEBAPP_PUBLISH_PROFILE` in der Bereitstellungsaktion für die Azure-Web-App. Beispiel:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Dienstprinzipal](#tab/service-principal)

Navigieren Sie in [GitHub](https://github.com/) zu Ihrem Repository, und wählen Sie **Einstellungen > Geheimnisse > Neues Geheimnis hinzufügen** aus.

Um [Anmeldeinformationen auf Benutzerebene](#generate-deployment-credentials) zu verwenden, fügen Sie die gesamte JSON-Ausgabe aus dem Azure CLI-Befehl in das Wertfeld des Geheimnisses ein. Geben Sie dem Geheimnis einen Namen wie `AZURE_CREDENTIALS`.

Wenn Sie die Workflowdatei später konfigurieren, verwenden Sie das Geheimnis für die Eingabe `creds` der Azure-Anmeldeaktion. Beispiel:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>Konfigurieren von GitHub-Geheimnissen für die Registrierung

Definieren Sie Geheimnisse für die Docker-Anmeldeaktion. Im Beispiel in diesem Dokument wird Azure Container Registry für die Containerregistrierung verwendet. 

1. Wechseln Sie im Azure-Portal oder in Docker zu Ihrem Container, und kopieren Sie den Benutzernamen und das Kennwort. Den Benutzernamen und das Kennwort für Azure Container Registry finden Sie im Azure-Portal unter **Einstellungen** > **Zugriffsschlüssel** für Ihre Registrierung. 

2. Definieren Sie ein neues Geheimnis für den Registrierungsbenutzer mit dem Namen `REGISTRY_USERNAME`. 

3. Definieren Sie ein neues Geheimnis für das Registrierungskennwort mit dem Namen `REGISTRY_PASSWORD`. 

## <a name="build-the-container-image"></a>Erstellen des Containerimages

Das folgende Beispiel zeigt einen Teil des Workflows zum Erstellen eines Node.js-Docker-Images. Melden Sie sich über die [Docker-Anmeldung](https://github.com/azure/docker-login) bei einer privaten Containerregistrierung an. In diesem Beispiel wird Azure Container Registry verwendet, die gleiche Aktion funktioniert jedoch auch mit anderen Registrierungen. 


```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

Sie können auch die [Docker-Anmeldung](https://github.com/azure/docker-login) verwenden, um sich gleichzeitig bei mehreren Containerregistrierungen anzumelden. Dieses Beispiel enthält zwei neue GitHub-Geheimnisse für die Authentifizierung mit docker.io. Im Beispiel wird davon ausgegangen, dass ein Dockerfile auf der Stammebene der Registrierung vorhanden ist. 

```yml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - uses: azure/docker-login@v1
      with:
        login-server: index.docker.io
        username: ${{ secrets.DOCKERIO_USERNAME }}
        password: ${{ secrets.DOCKERIO_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

## <a name="deploy-to-an-app-service-container"></a>Bereitstellung in einem App Service-Container

Um Ihr Image in einem benutzerdefinierten Container in App Service bereitzustellen, verwenden Sie die Aktion `azure/webapps-deploy@v2`. Diese Aktion umfasst sieben Parameter:

| **Parameter**  | **Erklärung**  |
|---------|---------|
| **app-name** | (Erforderlich): Name der App Service-App. | 
| **publish-profile** | (Optional) Gilt für Web-Apps(Windows und Linux) und Web-App-Container (Linux). Szenarien mit mehreren Containern werden nicht unterstützt. Veröffentlichungsprofil-Dateiinhalte (\*.publishsettings) mit Web Deploy-Geheimnissen | 
| **slot-name** | (Optional): Geben Sie einen vorhandene Slot ein, bei dem es sich nicht um den Produktionsslot handelt. |
| **package** | (Optional) Gilt nur für Web-App: Pfad zum Paket oder Ordner. \*.zip, \*.war, \*.jar oder ein Ordner für die Bereitstellung |
| **images** | (Erforderlich) Gilt nur für Web-App-Container: Geben Sie den vollqualifizierten Namen des bzw. der Containerimages an. Beispiel: „myregistry.azurecr.io/nginx:latest“ oder „python:3.7.2-alpine/“. Für eine App mit mehreren Containern können mehrere Namen von Containerimages angegeben werden (mehrere getrennte Zeilen). |
| **configuration-file** | (Optional) Gilt nur für Web-App-Container: Der Pfad der Docker-Compose-Datei. Es muss sich um einen vollqualifizierten Pfad oder einen Pfad relativ zum Standardarbeitsverzeichnis handeln. Dieser Parameter ist beim Konfigurieren von Apps mit mehreren Containern erforderlich. |
| **startup-command** | (Optional): Geben Sie den Startbefehl ein. Beispiel: „dotnet run“ oder „dotnet filename.dll“. |

# <a name="publish-profile"></a>[Veröffentlichungsprofil](#tab/publish-profile)

```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     

    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
```
# <a name="service-principal"></a>[Dienstprinzipal](#tab/service-principal)

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@main
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
      
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="next-steps"></a>Nächste Schritte

Die verfügbaren Aktionen sind auf verschiedene GitHub-Repositorys verteilt, jeweils mit Dokumentation und Beispielen, um Sie bei der Verwendung von GitHub für CI/CD und der Bereitstellung Ihrer Apps in Azure zu unterstützen.

- [Aktionsworkflows für das Bereitstellen in Azure](https://github.com/Azure/actions-workflow-samples)

- [Azure-Anmeldung](https://github.com/Azure/login)

- [Azure-Web-App](https://github.com/Azure/webapps-deploy)

- [Docker-Anmeldung/-Abmeldung](https://github.com/Azure/docker-login)

- [Ereignisse zum Auslösen von Workflows](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Kubernetes-Bereitstellung](https://github.com/Azure/k8s-deploy)

- [Workflows zum Einstieg](https://github.com/actions/starter-workflows)
