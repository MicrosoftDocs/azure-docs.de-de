---
title: Verwenden von GitHub Actions zum Vornehmen von Codeaktualisierungen in Azure Functions
description: Erfahren Sie, wie Sie mit GitHub Actions einen Workflow zum Erstellen und Bereitstellen von Azure Functions-Projekten in GitHub definieren.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, github-actions-azure
ms.openlocfilehash: ae3ffb9dc387129153b684a849ba4e58274ad80c
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563278"
---
# <a name="continuous-delivery-by-using-github-action"></a>Continuous Delivery mit GitHub Actions

Verwenden Sie [GitHub Actions](https://github.com/features/actions), um einen Workflow zum automatischen Erstellen und Bereitstellen von Code in Ihrer Azure-Funktions-App zu definieren. 

In GitHub Actions ist ein [Workflow](https://help.github.com/articles/about-github-actions#workflow) ein automatisierter Prozess, den Sie in Ihrem GitHub-Repository definieren. Dieser Prozess informiert GitHub, wie Ihr Funktions-App-Projekt auf GitHub erstellt und bereitgestellt werden soll. 

Ein Workflow wird durch eine YAML-Datei im Pfad `/.github/workflows/` in Ihrem Repository definiert. Diese Definition enthält die verschiedenen Schritte und Parameter, die den Workflow bilden. 

Bei einem Azure Functions-Workflow umfasst die Datei drei Abschnitte: 

| `Section` | Aufgaben |
| ------- | ----- |
| **Authentifizierung** | Herunterladen eines Veröffentlichungsprofils<br/>Erstellen eines GitHub-Geheimnisses.|
| **Build** | Einrichten der Umgebung.<br/>Erstellen Sie die Funktions-App.|
| **Bereitstellen** | Bereitstellen der Funktions-App.|

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ein GitHub-Konto. Falls Sie noch nicht über ein Konto verfügen, können Sie sich [kostenlos](https://github.com/join) registrieren.  
- Eine funktionierende Funktions-App, die in Azure mit einem GitHub-Repository gehostet wird.   
    - [Schnellstart: Erstellen einer Funktion in Azure mit Visual Studio Code](./create-first-function-vs-code-csharp.md)

## <a name="generate-deployment-credentials"></a>Generieren von Anmeldeinformationen für die Bereitstellung

Die empfohlene Vorgehensweise für die Authentifizierung mit Azure Functions für GitHub Actions ist die Verwendung eines Veröffentlichungsprofils. Sie können sich auch mit einem Dienstprinzipal authentifizieren. Weitere Informationen finden Sie in [diesem GitHub Actions-Repository](https://github.com/Azure/functions-action). 

Nachdem Sie die Anmeldeinformationen des Veröffentlichungsprofils als [GitHub-Geheimnis](https://docs.github.com/en/actions/reference/encrypted-secrets) gespeichert haben, verwenden Sie dieses Geheimnis in Ihrem Workflow, um sich bei Azure zu authentifizieren. 

#### <a name="download-your-publish-profile"></a>Herunterladen des Veröffentlichungsprofils

So laden Sie das Veröffentlichungs Profil Ihrer Funktions-App herunter:

1. Wählen Sie die Seite **Übersicht** der Funktions-App aus, und wählen Sie dann **Veröffentlichungsprofil abrufen** aus.

   :::image type="content" source="media/functions-how-to-github-actions/get-publish-profile.png" alt-text="Herunterladen des Veröffentlichungsprofils":::

1. Speichern und kopieren Sie den Inhalt der Datei.


### <a name="add-the-github-secret"></a>Hinzufügen des GitHub-Geheimnisses

1. Navigieren Sie in [GitHub](https://github.com) zu Ihrem Repository, und wählen Sie **Einstellungen** > **Geheimnisse** > **Neues Geheimnis hinzufügen** aus.

   :::image type="content" source="media/functions-how-to-github-actions/add-secret.png" alt-text="Hinzufügen des Geheimnisses":::

1. Fügen Sie mithilfe von `AZURE_FUNCTIONAPP_PUBLISH_PROFILE` ein neues Geheimnis für **Name** sowie den Inhalt der Veröffentlichungsprofildatei für **Wert** hinzu, und wählen Sie dann **Add secret** (Geheimnis hinzufügen) aus.

GitHub kann sich jetzt bei ihrer Funktions-App in Azure authentifizieren.

## <a name="create-the-environment"></a>Erstellen der Umgebung 

Das Einrichten der Umgebung erfolgt mithilfe einer sprachspezifischen Aktionen zum Veröffentlichen des Setups.

# <a name="net"></a>[.NET](#tab/dotnet)

.NET (einschließlich ASP.NET) verwendet die Aktion `actions/setup-dotnet`.  
Das folgende Beispiel zeigt den Teil des Workflows, der die Umgebung einrichtet:

```yaml
    - name: Setup DotNet 2.2.402 Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: 2.2.402
```

# <a name="java"></a>[Java](#tab/java)

Java verwendet die Aktion `actions/setup-java`.  
Das folgende Beispiel zeigt den Teil des Workflows, der die Umgebung einrichtet:

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript (Node.js) verwendet die Aktion `actions/setup-node`.  
Das folgende Beispiel zeigt den Teil des Workflows, der die Umgebung einrichtet:

```yaml

    - name: Setup Node 12.x Environment
      uses: actions/setup-node@v1
      with:
        node-version: 12.x
```

# <a name="python"></a>[Python](#tab/python)

Python verwendet die Aktion `actions/setup-python`.  
Das folgende Beispiel zeigt den Teil des Workflows, der die Umgebung einrichtet:

```yaml
    - name: Setup Python 3.7 Environment
      uses: actions/setup-python@v1
      with:
        python-version: 3.7
```
---

## <a name="build-the-function-app"></a>Erstellen der Funktions-App

Dies hängt von der Sprache und den von Azure Functions unterstützten Sprachen ab. Dieser Abschnitt sollte die Standardbuildschritte der einzelnen Sprachen beinhalten.

Das folgende Beispiel zeigt den Teil des Workflows, der die Funktions-App erstellt, der sprachspezifisch ist:

# <a name="net"></a>[.NET](#tab/dotnet)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="java"></a>[Java](#tab/java)

```yaml
    env:
      POM_XML_DIRECTORY: '.'  # set this to the directory which contains pom.xml file

    - name: 'Restore Project Dependencies Using Mvn'
      shell: bash
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'  # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Npm'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="python"></a>[Python](#tab/python)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Pip'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/site-packages"
        popd
```
---

## <a name="deploy-the-function-app"></a>Bereitstellen der Funktionen-App
Verwenden Sie die Aktion `Azure/functions-action`, um Ihren Code in einer Funktions-App bereitzustellen. Diese Aktion umfasst drei Parameter:

|Parameter |Erklärung  |
|---------|---------|
|_**app-name**_ | (Erforderlich) Der Name Ihrer Funktions-App. |
|_**slot-name**_ | (Optional) Der Name des [Bereitstellungsslots](functions-deployment-slots.md), in dem die Bereitstellung erfolgen soll. Der Slot muss in ihrer Funktions-App bereits definiert sein. |
|_**publish-profile**_ | (Optional) Der Name des GitHub-Geheimnisses für das Veröffentlichungsprofil. |

Im folgenden Beispiel werden Version 1 von `functions-action` und `publish profile` für die Authentifizierung verwendet. 

# <a name="net"></a>[.NET](#tab/dotnet)

Richten Sie einen .NET-Linux-Workflow ein, der ein Veröffentlichungsprofil verwendet.

```yaml
name: Deploy DotNet project to Azure function app with a Linux environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '2.2.402'              # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```
Richten Sie einen .NET-Windows-Workflow ein, der ein Veröffentlichungsprofil verwendet.

```yaml
name: Deploy DotNet project to Azure function app with a Windows environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '2.2.402'              # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

# <a name="java"></a>[Java](#tab/java)

Richten Sie einen Java-Linux-Workflow ein, der ein Veröffentlichungsprofil verwendet.

```yaml
name: Deploy Java project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '1.8.x'                      # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v1
      with:
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: bash
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

Richten Sie einen Java-Windows-Workflow ein, der ein Veröffentlichungsprofil verwendet.

```yaml
name: Deploy Java project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '1.8.x'                      # set this to the java version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v1
      with:
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: pwsh
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Richten Sie einen Node.JS-Linux-Workflow ein, der ein Veröffentlichungsprofil verwendet.

```yaml
name: Deploy Node.js project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '12.x'                     # set this to the node version to use (supports 8.x, 10.x, 12.x)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

Richten Sie einen Node.JS-Windows-Workflow ein, der ein Veröffentlichungsprofil verwendet.

```yaml
name: Deploy Node.js project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '10.x'                     # set this to the node version to use (supports 8.x, 10.x, 12.x)

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}

```
# <a name="python"></a>[Python](#tab/python)

Richten Sie einen Python-Linux-Workflow ein, der ein Veröffentlichungsprofil verwendet.

```yaml
name: Deploy Python project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'   # set this to the path to your web app project, defaults to the repository root
  PYTHON_VERSION: '3.7'                 # set this to the python version to use (supports 3.6, 3.7, 3.8)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Python ${{ env.PYTHON_VERSION }} Environment
      uses: actions/setup-python@v1
      with:
        python-version: ${{ env.PYTHON_VERSION }}

    - name: 'Resolve Project Dependencies Using Pip'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/site-packages"
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

---

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zu Azure und zur GitHub-Integration](/azure/developer/github/)