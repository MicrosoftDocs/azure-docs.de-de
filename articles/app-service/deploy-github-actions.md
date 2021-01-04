---
title: Konfigurieren von CI/CD mit GitHub-Aktionen
description: Erfahren Sie, wie Sie Azure App Service Codes über eine CI/CD-Pipeline mit GitHub-Aktionen bereitstellen. Passen Sie die Buildaufgaben an, und führen Sie komplexe Bereitstellungen aus.
ms.devlang: na
ms.topic: article
ms.date: 09/14/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: b94e35f504a4c4d6e934ec01b06105f749031e35
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007380"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Bereitstellen in App Service mithilfe von GitHub Actions

Verwenden Sie [GitHub Actions](https://help.github.com/en/articles/about-github-actions) zum Automatisieren Ihres Workflows und zum Bereitstellen im [Azure App Service](overview.md) aus GitHub. 

## <a name="prerequisites"></a>Voraussetzungen 

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ein GitHub-Konto. Falls Sie noch nicht über ein Konto verfügen, können Sie sich [kostenlos](https://github.com/join) registrieren.  
- Eine funktionierende Azure App Service-App. 
    - .NET: [Erstellen von ASP.NET Core-Web-Apps in Azure](quickstart-dotnetcore.md)
    - ASP.NET: [Erstellen einer ASP.NET Framework-Web-App in Azure](quickstart-dotnet-framework.md)
    - JavaScript: [Erstellen einer Node.js-Web-App in Azure App Service](quickstart-nodejs.md)  
    - Java: [Erstellen einer Java-App in Azure App Service](quickstart-java.md)
    - Python: [Erstellen einer Python-App in Azure App Service](quickstart-python.md)

## <a name="workflow-file-overview"></a>Übersicht über die Workflowdatei

Ein Workflow wird durch eine YAML-Datei im Pfad `/.github/workflows/` in Ihrem Repository definiert. Diese Definition enthält die verschiedenen Schritte und Parameter, die den Workflow bilden.

Die Datei besteht aus drei Abschnitten:

|`Section`  |Aufgaben  |
|---------|---------|
|**Authentifizierung** | 1. Definieren eines Dienstprinzipals oder Veröffentlichungsprofils. <br /> 2. Erstellen eines GitHub-Geheimnisses. |
|**Build** | 1. Einrichten der Umgebung. <br /> 2. Erstellen der Web-App. |
|**Bereitstellen** | 1. Bereitstellen der Web-App. |

## <a name="use-the-deployment-center"></a>Verwenden des Bereitstellungscenters

Mit dem App Service Deployment Center ist die Verwendung von GitHub Actions ganz einfach. Dort wird automatisch eine Workflowdatei generiert, die auf Ihrem Anwendungsstapel basiert, und ein Commit dieser Datei im richtigen Verzeichnis Ihres GitHub-Repositorys durchgeführt.

1. Navigieren Sie im Azure-Portal zu Ihrer Web-App.
1. Klicken Sie auf der linken Seite auf **Deployment Center**.
1. Wählen Sie unter **Continuous Deployment (CI/CD)** die Option **GitHub** aus.
1. Wählen Sie als Nächstes **GitHub Actions** aus.
1. Verwenden Sie die Dropdownlisten, um Ihr GitHub-Repository, den Branch und den Anwendungsstapel auszuwählen.
    - Auch wenn der ausgewählte Branch geschützt ist, können Sie die Workflowdatei hinzufügen. Stellen Sie sicher, dass Sie Ihren Branchschutz überprüfen, bevor Sie den Vorgang fortsetzen.
1. Auf dem letzten Bildschirm können Sie Ihre Auswahl überprüfen und eine Vorschau der Workflowdatei anzeigen, für die ein Commit an das Repository ausgeführt wird. Wenn die Auswahl richtig ist, klicken Sie auf **Finish** (Fertig stellen).

Dadurch wird ein Commit der Workflowdatei an das Repository ausgeführt. Der Workflow zum Erstellen und Bereitstellen Ihrer App wird sofort gestartet.

## <a name="set-up-a-workflow-manually"></a>Manuelles Einrichten eines Workflows

Sie können einen Workflow auch ohne das Deployment Center bereitstellen. Dazu müssen Sie die Anmeldeinformationen für die Bereitstellung generieren. 

## <a name="generate-deployment-credentials"></a>Generieren von Anmeldeinformationen für die Bereitstellung

Die empfohlene Vorgehensweise für die Authentifizierung mit Azure App Services für GitHub Actions verwendet ein Veröffentlichungsprofil. Sie können sich auch mit einem Dienstprinzipal authentifizieren, für diesen Vorgang sind jedoch weitere Schritte erforderlich. 

Speichern Sie die Anmeldeinformationen des Veröffentlichungsprofils oder den Dienstprinzipal als [GitHub-Geheimnis](https://docs.github.com/en/actions/reference/encrypted-secrets), um sich bei Azure zu authentifizieren. In Ihrem Workflow greifen Sie auf das Geheimnis zu. 

# <a name="publish-profile"></a>[Veröffentlichungsprofil](#tab/applevel)

Ein Veröffentlichungsprofil stellt Anmeldeinformationen auf App-Ebene dar. Richten Sie Ihr Veröffentlichungsprofil als GitHub-Geheimnis ein. 

1. Wechseln Sie im Azure-Portal zu Ihrem App Service. 

1. Wählen Sie auf der Seite **Übersicht** die Option **Veröffentlichungsprofil abrufen**.

1. Speichern Sie die heruntergeladene Datei. Zum Erstellen eines GitHub-Geheimnisses verwenden Sie den Inhalt der Datei.

> [!NOTE]
> Ab Oktober 2020 muss für Linux-Web-Apps die App-Einstellung `WEBSITE_WEBDEPLOY_USE_SCM` auf `true` festgelegt werden, **bevor das Veröffentlichungsprofil heruntergeladen wird**. Diese Anforderung wird künftig entfallen.

# <a name="service-principal"></a>[Dienstprinzipal](#tab/userlevel)

Sie können mit dem Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) in der [Azure CLI](/cli/azure/) einen [Dienstprinzipal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) erstellen. Führen Sie diesen Befehl mit [Azure Cloud Shell](https://shell.azure.com/) im Azure-Portal oder durch Auswählen der Schaltfläche **Ausprobieren** aus.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

Ersetzen Sie im obigen Beispiel die Platzhalter durch Ihre Abonnement-ID, den Ressourcengruppennamen und den App-Namen. Die Ausgabe ist ein JSON-Objekt mit den Anmeldeinformationen für die Rollenzuweisung, die ähnlich wie unten Zugriff auf Ihre App Service-App gewähren. Kopieren Sie dieses JSON-Objekt zur späteren Verwendung.

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

## <a name="configure-the-github-secret"></a>Konfigurieren des GitHub-Geheimnisses


# <a name="publish-profile"></a>[Veröffentlichungsprofil](#tab/applevel)

Navigieren Sie in [GitHub](https://github.com/) zu Ihrem Repository, und wählen Sie **Einstellungen > Geheimnisse > Neues Geheimnis hinzufügen** aus.

Um [Anmeldeinformationen auf App-Ebene](#generate-deployment-credentials) zu verwenden, fügen Sie den Inhalt der heruntergeladenen Veröffentlichungsprofildatei in das Wertfeld des Geheimnisses ein. Geben Sie dem Geheimnis den Namen `AZURE_WEBAPP_PUBLISH_PROFILE`.

Wenn Sie Ihren GitHub-Workflow konfigurieren, verwenden Sie `AZURE_WEBAPP_PUBLISH_PROFILE` in der Bereitstellungsaktion für die Azure-Web-App. Beispiel:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Dienstprinzipal](#tab/userlevel)

Navigieren Sie in [GitHub](https://github.com/) zu Ihrem Repository, und wählen Sie **Einstellungen > Geheimnisse > Neues Geheimnis hinzufügen** aus.

Um [Anmeldeinformationen auf Benutzerebene](#generate-deployment-credentials) zu verwenden, fügen Sie die gesamte JSON-Ausgabe aus dem Azure CLI-Befehl in das Wertfeld des Geheimnisses ein. Geben Sie dem Geheimnis den Namen `AZURE_CREDENTIALS`.

Wenn Sie die Workflowdatei später konfigurieren, verwenden Sie das Geheimnis für die Eingabe `creds` der Azure-Anmeldeaktion. Beispiel:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="set-up-the-environment"></a>Einrichten der Umgebung

Das Einrichten der Umgebung kann mithilfe einer der Setupaktionen erfolgen.

|**Sprache**  |**Setupaktion**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**ASP.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

In den folgenden Beispielen wird gezeigt, wie Sie die Umgebung für die verschiedenen unterstützten Sprachen einrichten:

**.NET**

```yaml
    - name: Setup Dotnet 3.3.x
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '3.3.x'
```

**ASP.NET**

```yaml
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x,
        # change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

**JavaScript**

```yaml
env:
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
```
**Python**

```yaml
    - name: Setup Python 3.x 
      uses: actions/setup-python@v1
      with:
        python-version: 3.x
```

## <a name="build-the-web-app"></a>Erstellen der Web-App.

Der Prozess zum Erstellen einer Web-App und zum Bereitstellen im Azure App Service hängt von der verwendeten Sprache ab. 

Die folgenden Beispiele zeigen den Teil des Workflows, in dem die Web-App in den verschiedenen unterstützten Sprachen erstellt wird.

Bei allen Sprachen können Sie das Stammverzeichnis der Web-App mit `working-directory` festlegen. 

**.NET**

Die Umgebungsvariable `AZURE_WEBAPP_PACKAGE_PATH` gibt den Pfad zum Web-App-Projekt an. 

```yaml
- name: dotnet build and publish
  run: |
    dotnet restore
    dotnet build --configuration Release
    dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
```
**ASP.NET**

Mit `run` können Sie NuGet-Abhängigkeiten wiederherstellen und MSBuild ausführen. 

```yaml
- name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
  run: nuget restore

- name: Add msbuild to PATH
  uses: microsoft/setup-msbuild@v1.0.0

- name: Run msbuild
  run: msbuild .\SampleWebApplication.sln
```

**Java**

```yaml
- name: Build with Maven
  run: mvn package --file pom.xml
```

**JavaScript**

Für Node.js können Sie `working-directory` festlegen oder in `pushd` zum Verzeichnis „npm“ wechseln. 

```yaml
- name: npm install, build, and test
  run: |
    npm install
    npm run build --if-present
    npm run test --if-present
  working-directory: my-app-folder # set to the folder with your app if it is not the root directory
```

**Python**

```yaml
- name: Install dependencies
  run: |
    python -m pip install --upgrade pip
    pip install -r requirements.txt
```


## <a name="deploy-to-app-service"></a>Bereitstellen für App Service

Verwenden Sie die Aktion `azure/webapps-deploy@v2`, um Ihren Code in einer App Service-App bereitzustellen. Diese Aktion umfasst vier Parameter:

| **Parameter**  | **Erklärung**  |
|---------|---------|
| **app-name** | (Erforderlich): Name der App Service-App. | 
| **publish-profile** | (Optional) Veröffentlichungsprofil-Dateiinhalte mit Web Deploy-Geheimnissen. |
| **package** | (Optional) Pfad zum Paket oder Ordner. Der Pfad darf „*.zip“, „*.war“, „*.jar“ oder einen Ordner für die Bereitstellung enthalten. |
| **slot-name** | (Optional): Geben Sie einen vorhandenen Slot ein, bei dem es sich nicht um den Produktions[slot](deploy-staging-slots.md) handelt. |


# <a name="publish-profile"></a>[Veröffentlichungsprofil](#tab/applevel)

### <a name="net-core"></a>.NET Core

Erstellen Sie eine .NET Core-App, und stellen Sie sie mithilfe eines Azure-Veröffentlichungsprofils in Azure bereit. Die `publish-profile`-Eingabe verweist auf das `AZURE_WEBAPP_PUBLISH_PROFILE`-Geheimnis, das Sie zuvor erstellt haben.

```yaml
name: .NET Core CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@master
      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
```

### <a name="aspnet"></a>ASP.NET

Erstellen Sie eine ASP.NET MVC-App, die NuGet und `publish-profile` für die Authentifizierung verwendet. 


```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    - uses: actions/checkout@master  
    
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
```

### <a name="java"></a>Java

Erstellen Sie eine Java Spring-App, und stellen Sie sie mithilfe eines Azure-Veröffentlichungsprofils in Azure bereit. Die `publish-profile`-Eingabe verweist auf das `AZURE_WEBAPP_PUBLISH_PROFILE`-Geheimnis, das Sie zuvor erstellt haben.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: my-app-path
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.jar
```

Um eine `war`-Datei anstelle einer `jar`-Datei bereitzustellen, ändern Sie den `package`-Wert. 


```yaml
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.war
```

### <a name="javascript"></a>JavaScript 

Erstellen Sie eine Node.js-App, und stellen Sie sie mithilfe des Veröffentlichungsprofils der App in Azure bereit. Die `publish-profile`-Eingabe verweist auf das `AZURE_WEBAPP_PUBLISH_PROFILE`-Geheimnis, das Sie zuvor erstellt haben.

```yaml
# File: .github/workflows/workflow.yml
name: JavaScript CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name   # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: 'my-app-path'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    - name: npm install, build, and test
      run: |
        # Build and test the project, then
        # deploy to Azure Web App.
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory: my-app-path
    - name: 'Deploy to Azure WebApp'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

### <a name="python"></a>Python 

Erstellen Sie eine Python-App, und stellen Sie sie mithilfe des Veröffentlichungsprofils der App in Azure bereit. Beachten Sie, wie die `publish-profile`-Eingabe auf das `AZURE_WEBAPP_PUBLISH_PROFILE`-Geheimnis verweist, das Sie zuvor erstellt haben.

```yaml
name: Python CI

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-web-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Building web app
      uses: azure/appservice-build@v2
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

# <a name="service-principal"></a>[Dienstprinzipal](#tab/userlevel)

### <a name="net-core"></a>.NET Core 

Erstellen Sie eine .NET Core-App, und stellen Sie sie mithilfe eines Azure-Dienstprinzipals in Azure bereit. Beachten Sie, wie die `creds`-Eingabe auf das `AZURE_CREDENTIALS`-Geheimnis verweist, das Sie zuvor erstellt haben.


```yaml
name: .NET Core

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@master
      - uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
      
      - name: logout
        run: |
          az logout
```

### <a name="aspnet"></a>ASP.NET

Erstellen Sie eine ASP.NET MVC-App, und stellen Sie sie mithilfe eines Azure-Dienstprinzipals in Azure bereit. Beachten Sie, wie die `creds`-Eingabe auf das `AZURE_CREDENTIALS`-Geheimnis verweist, das Sie zuvor erstellt haben.

```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    # checkout the repo
    - uses: actions/checkout@master  
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
  
    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="java"></a>Java 

Erstellen Sie eine Java Spring-App, und stellen Sie sie mithilfe eines Azure-Dienstprinzipals in Azure bereit. Beachten Sie, wie die `creds`-Eingabe auf das `AZURE_CREDENTIALS`-Geheimnis verweist, das Sie zuvor erstellt haben.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: complete
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        package: my/target/*.jar

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="javascript"></a>JavaScript 

Erstellen Sie eine Node.js-App, und stellen Sie sie mithilfe eines Azure-Dienstprinzipals in Azure bereit. Beachten Sie, wie die `creds`-Eingabe auf das `AZURE_CREDENTIALS`-Geheimnis verweist, das Sie zuvor erstellt haben.

```yaml
name: JavaScript CI

on: [push]

name: Node.js

env:
  AZURE_WEBAPP_NAME: my-app   # set this to your application's name
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory:  my-app-path
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="python"></a>Python 

Erstellen Sie eine Python-App, und stellen Sie sie mithilfe eines Azure-Dienstprinzipals in Azure bereit. Beachten Sie, wie die `creds`-Eingabe auf das `AZURE_CREDENTIALS`-Geheimnis verweist, das Sie zuvor erstellt haben.

```yaml
name: Python application

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
    - name: logout
      run: |
        az logout
```


---

## <a name="next-steps"></a>Nächste Schritte

Die verfügbaren Aktionen sind auf verschiedene GitHub-Repositorys verteilt, jeweils mit Dokumentation und Beispielen, um Sie bei der Verwendung von GitHub für CI/CD und der Bereitstellung Ihrer Apps in Azure zu unterstützen.

- [Aktionsworkflows für das Bereitstellen in Azure](https://github.com/Azure/actions-workflow-samples)

- [Azure-Anmeldung](https://github.com/Azure/login)

- [Azure-Web-App](https://github.com/Azure/webapps-deploy)

- [Azure-Web-App für Container](https://github.com/Azure/webapps-container-deploy)

- [Docker-Anmeldung/-Abmeldung](https://github.com/Azure/docker-login)

- [Ereignisse zum Auslösen von Workflows](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Kubernetes-Bereitstellung](https://github.com/Azure/k8s-deploy)

- [Workflows zum Einstieg](https://github.com/actions/starter-workflows)
