---
title: 'Azure Spring Cloud: CI/CD mit GitHub Actions'
description: Hier erfahren Sie, wie Sie einen CI/CD-Workflow für Azure Spring Cloud mit GitHub Actions erstellen.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 95a97d3f5104891ef5e23a03ffe1477f9b6ad036
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561612"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure Spring Cloud: CI/CD mit GitHub Actions

GitHub Actions unterstützt einen automatisierten Workflow für den Softwareentwicklungslebenszyklus. Mit GitHub Actions für Azure Spring Cloud können Sie in Ihrem Repository Workflows zum Erstellen, Testen, Verpacken, Veröffentlichen und Bereitstellen in Azure erstellen. 

## <a name="prerequisites"></a>Voraussetzungen
Für dieses Beispiel wird die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) benötigt.

::: zone pivot="programming-language-csharp"
## <a name="set-up-github-repository-and-authenticate"></a>Einrichten des GitHub-Repositorys und Durchführen der Authentifizierung
Zum Autorisieren der Azure-Anmeldeaktion sind Anmeldeinformationen für einen Azure-Dienstprinzipal erforderlich. Führen Sie auf Ihrem lokalen Computer die folgenden Befehle aus, um Azure-Anmeldeinformationen zu erhalten:

```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```

Wenn Sie auf eine bestimmte Ressourcengruppe zugreifen möchten, können Sie den Bereich einschränken:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

Der Befehl sollte ein JSON-Objekt ausgeben:

```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

In diesem Beispiel wird das [Steeltoe-Beispiel auf GitHub](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample) verwendet.  Forken Sie das Repository, öffnen Sie die GitHub-Repositoryseite für den Fork, und wählen Sie die Registerkarte **Settings** (Einstellungen) aus. Öffnen Sie das Menü **Secrets** (Geheimnisse), und wählen Sie **New secret** (Neues Geheimnis) aus:

 ![Hinzufügen eines neuen Geheimnisses](./media/github-actions/actions1.png)

Legen Sie den Namen des Geheimnisses auf `AZURE_CREDENTIALS` und den Wert auf die JSON-Zeichenfolge fest, die Sie unter *Einrichten des GitHub-Repositorys und Durchführen der Authentifizierung* gefunden haben.

 ![Festlegen der Geheimnisdaten](./media/github-actions/actions2.png)

Die Azure-Anmeldeinformationen können auch aus Key Vault in GitHub Actions abgerufen werden, wie unter [Authentifizieren von Azure Spring Cloud mit Schlüsseltresor in GitHub Actions](./spring-cloud-github-actions-key-vault.md) beschrieben.

## <a name="provision-service-instance"></a>Bereitstellen der Dienstinstanz
Führen Sie zum Bereitstellen Ihrer Azure Spring Cloud-Dienstinstanz die folgenden Befehle über die Azure-Befehlszeilenschnittstelle aus:

```azurecli
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/Azure-Spring-Cloud-Samples --label main --search-paths steeltoe-sample/config
```

## <a name="build-the-workflow"></a>Erstellen des Workflows
Der Workflow wird mithilfe folgender Optionen definiert.

### <a name="prepare-for-deployment-with-azure-cli"></a>Vorbereiten der Bereitstellung mit der Azure-Befehlszeilenschnittstelle

Der Befehl `az spring-cloud app create` ist aktuell nicht idempotent. Nachdem Sie den Befehl ein Mal ausgeführt haben, erhalten Sie eine Fehlermeldung, wenn Sie denselben Befehl erneut ausführen. Dieser Workflow wird für vorhandene Azure Spring Cloud-Apps und -Instanzen empfohlen.

Führen Sie zur Vorbereitung die folgenden Azure CLI-Befehle aus:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name planet-weather-provider
az spring-cloud app create --name solar-system-weather
```

### <a name="deploy-with-azure-cli-directly"></a>Direktes Bereitstellen über die Azure-Befehlszeilenschnittstelle

Erstellen Sie die Datei `.github/workflows/main.yml` im Repository mit folgendem Inhalt. Ersetzen Sie `<your resource group name>` und `<your service name>` durch die richtigen Werte.

```yaml
name: Steeltoe-CD

# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the main branch
on:
  push:
    branches: [ main]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    env:
      working-directory: ./steeltoe-sample
      resource-group-name: <your resource group name>
      service-name: <your service name>
    
    # Supported .NET Core version matrix.
    strategy:
      matrix:
        dotnet: [ '3.1.x' ]

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2

      # Set up .NET Core 3.1 SDK
      - uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ matrix.dotnet }}
          
      # Set credential for az login
      - uses: azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: install Azure CLI extension
        run: |
          az extension add --name spring-cloud --yes
      
      - name: Build and package planet-weather-provider app
        working-directory: ${{env.working-directory}}/src/planet-weather-provider
        run: |
          dotnet publish
          az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
      - name: Build solar-system-weather app
        working-directory: ${{env.working-directory}}/src/solar-system-weather
        run: |
          dotnet publish
          az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
```
::: zone-end

::: zone pivot="programming-language-java"
## <a name="set-up-github-repository-and-authenticate"></a>Einrichten des GitHub-Repositorys und Durchführen der Authentifizierung
Zum Autorisieren der Azure-Anmeldeaktion sind Anmeldeinformationen für einen Azure-Dienstprinzipal erforderlich. Führen Sie auf Ihrem lokalen Computer die folgenden Befehle aus, um Azure-Anmeldeinformationen zu erhalten:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Wenn Sie auf eine bestimmte Ressourcengruppe zugreifen möchten, können Sie den Bereich einschränken:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
Der Befehl sollte ein JSON-Objekt ausgeben:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

In diesem Beispiel wird das [PiggyMetrics](https://github.com/Azure-Samples/piggymetrics)-Beispiel auf GitHub verwendet.  Forken Sie das Beispiel, öffnen Sie die GitHub-Repositoryseite, und klicken Sie auf die Registerkarte **Einstellungen**. Öffnen Sie das Menü **Geheimnisse**, und klicken Sie auf **Neues Geheimnis hinzufügen**:

 ![Hinzufügen eines neuen Geheimnisses](./media/github-actions/actions1.png)

Legen Sie den Namen des Geheimnisses auf `AZURE_CREDENTIALS` und den Wert auf die JSON-Zeichenfolge fest, die Sie unter *Einrichten des GitHub-Repositorys und Durchführen der Authentifizierung* gefunden haben.

 ![Festlegen der Geheimnisdaten](./media/github-actions/actions2.png)

Die Azure-Anmeldeinformationen können auch aus Key Vault in GitHub Actions abgerufen werden, wie unter [Authentifizieren von Azure Spring Cloud mit Schlüsseltresor in GitHub Actions](./spring-cloud-github-actions-key-vault.md) beschrieben.

## <a name="provision-service-instance"></a>Bereitstellen der Dienstinstanz
Führen Sie zum Bereitstellen Ihrer Azure Spring Cloud-Dienstinstanz die folgenden Befehle über die Azure-Befehlszeilenschnittstelle aus:
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>Erstellen des Workflows
Der Workflow wird mithilfe folgender Optionen definiert.

### <a name="prepare-for-deployment-with-azure-cli"></a>Vorbereiten der Bereitstellung mit der Azure-Befehlszeilenschnittstelle
Der Befehl `az spring-cloud app create` ist aktuell nicht idempotent.  Dieser Workflow wird für vorhandene Azure Spring Cloud-Apps und -Instanzen empfohlen.

Führen Sie zur Vorbereitung die folgenden Azure CLI-Befehle aus:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Direktes Bereitstellen über die Azure-Befehlszeilenschnittstelle
Erstellen Sie die Datei `.github/workflow/main.yml` im Repository:

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>Bereitstellen mit Azure CLI-Aktion
Der az-Befehl `run` verwendet die aktuelle Version der Azure-Befehlszeilenschnittstelle. Im Falle von Breaking Changes können Sie mithilfe von „azure/CLI `action`“ auch eine bestimmte Version der Azure-Befehlszeilenschnittstelle verwenden. 

> [!Note] 
> Da dieser Befehl in einem neuen Container ausgeführt wird, funktioniert `env` nicht, und beim aktionsübergreifenden Dateizugriff gelten möglicherweise zusätzliche Einschränkungen.

Erstellen Sie die Datei „.github/workflow/main.yml“ im Repository:
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>Bereitstellen mit Maven-Plug-In
Eine weitere Option ist die Verwendung des [Maven-Plug-Ins](./spring-cloud-quickstart.md), um die JAR-Datei bereitzustellen und die App-Einstellungen zu aktualisieren. Der Befehl `mvn azure-spring-cloud:deploy` ist idempotent und erstellt Apps bei Bedarf automatisch. Entsprechende Apps müssen nicht im Voraus erstellt werden.

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

::: zone-end

## <a name="run-the-workflow"></a>Ausführen des Workflows

GitHub **Actions** sollte nach dem Pushen von `.github/workflow/main.yml` an GitHub automatisch aktiviert werden. Die Aktion wird ausgelöst, wenn Sie einen neuen Commit pushen. Wenn Sie diese Datei im Browser erstellen, sollte Ihre Aktion bereits ausgeführt worden sein.

Klicken Sie auf der GitHub-Repositoryseite auf die Registerkarte **Aktionen**, um zu überprüfen, ob die Aktion aktiviert wurde:

![Überprüfen, ob die Aktion aktiviert wurde](./media/github-actions/actions3.png)

Im Falle eines Aktionsfehlers (beispielsweise, wenn die Azure-Anmeldeinformationen nicht festgelegt wurden) können Sie die Überprüfungen erneut ausführen, nachdem Sie den Fehler behoben haben. Klicken Sie auf der GitHub-Repositoryseite auf **Aktionen**, wählen Sie die spezifische Workflowaufgabe aus, und klicken Sie auf die Schaltfläche **Rerun checks** (Überprüfungen erneut ausführen), um die Überprüfungen erneut auszuführen:

![Erneutes Ausführen der Überprüfungen](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Nächste Schritte

* [Authentifizieren von Azure Spring Cloud mit Schlüsseltresor in GitHub Actions](./spring-cloud-github-actions-key-vault.md)
* [Azure Active Directory-Dienstprinzipale](/cli/azure/ad/sp?preserve-view=true&view=azure-cli-latest#az-ad-sp-create-for-rbac)
* [GitHub Actions für die Bereitstellung in Azure](https://github.com/Azure/actions/)
