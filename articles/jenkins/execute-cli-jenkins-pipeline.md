---
title: Ausführen der Azure CLI mit Jenkins
description: Erfahren Sie, wie Sie mit der Azure CLI eine Java-Web-App an Azure in der Jenkins-Pipeline bereitstellen.
ms.service: jenkins
keywords: Jenkins, Azure, DevOps, App Service, CLI
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 6/7/2017
ms.openlocfilehash: 5728a9ab70c5b7db10a123d6964b498e70f96588
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66162205"
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Bereitstellen an Azure App Service mit Jenkins und der Azure CLI
Für das Bereitstellen einer Java-Web-App in Azure können Sie die Azure CLI in der [Jenkins-Pipeline](https://jenkins.io/doc/book/pipeline/) nutzen. In diesem Tutorial erstellen Sie eine CI/CD-Pipeline auf einer Azure-VM und erfahren, wie Sie:

> [!div class="checklist"]
> * eine Jenkins-VM erstellen können
> * Konfigurieren von Jenkins
> * eine Web-App in Azure erstellen
> * ein GitHub-Repository erstellen
> * Erstellen einer Jenkins-Pipeline
> * die Pipeline ausführen und die Web-App überprüfen

Für dieses Tutorial ist mindestens Version 2.0.4 der Azure CLI erforderlich. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle]( /cli/azure/install-azure-cli) weitere Informationen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Erstellen und Konfigurieren einer Jenkins-Instanz
Wenn Sie noch nicht über einen Jenkins-Master verfügen, beginnen Sie mit der [Projektmappenvorlage](install-jenkins-solution-template.md), die das erforderliche [Azure-Anmeldeinformationen](https://plugins.jenkins.io/azure-credentials)-Plug-In standardmäßig enthält. 

Das Azure-Anmeldeinformationen-Plug-In ermöglicht Ihnen das Speichern von Anmeldeinformationen des Microsoft Azure-Dienstprinzipals in Jenkins. In Version 1.2 haben wir Support hinzugefügt, durch den die Jenkins-Pipeline die Azure-Anmeldeinformationen abrufen kann. 

Stellen Sie sicher, dass Sie über Version 1.2 oder höher verfügen:
* Klicken Sie im Jenkins-Dashboard auf **Manage Jenkins -> Plugin Manager ->** (Jenkins verwalten -> Plug-In-Manager ->), und suchen Sie nach **Azure-Anmeldeinformationen**. 
* Aktualisieren Sie das Plug-In, wenn die Version älter als 1.2 ist.

Java JDK und Maven werden auch im Jenkins-Master benötigt. Melden Sie sich zum Installieren mithilfe von SSH beim Jenkins-Master an, und führen Sie die folgenden Befehle aus:
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Hinzufügen des Azure-Dienstprinzipals zu Jenkins-Anmeldeinformationen

Eine Azure-Anmeldeinformation ist erforderlich, um die Azure CLI auszuführen.

* Klicken Sie im Jenkins-Dashboard auf **Credentials -> System ->** (Anmeldeinformationen -> System ->). Klicken Sie auf **Global credentials (unrestricted)** (Globale Anmeldeinformationen (uneingeschränkt)).
* Klicken Sie auf **Add Credentials** (Anmeldeinformationen hinzufügen), um einen [Microsoft Azure-Dienstprinzipal](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) hinzuzufügen, indem Sie die Abonnement-ID, die Client-ID, den geheimen Clientschlüssel und den OAuth 2.0-Token-Endpunkt hinzufügen. Stellen Sie eine ID zur Verwendung im nächsten Schritt bereit.

![Hinzufügen von Anmeldeinformationen](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Erstellen eines Azure App Service für die Bereitstellung der Java-Web-App

Erstellen Sie mit dem CLI-Befehl [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) einen Azure App Service-Plan mit dem Tarif **FREE**. Der App Service-Plan definiert die physischen Ressourcen, die zum Hosten Ihrer Apps verwendet werden. Alle einem App Service-Plan zugewiesenen Anwendungen teilen sich diese Ressourcen. Das spart Kosten, wenn Sie mehrere Apps hosten. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
```

Wenn der Plan fertig ist, zeigt die Azure CLI eine ähnliche Ausgabe wie im folgenden Beispiel:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Erstellen einer Azure-Web-App

 Erstellen Sie mit dem CLI-Befehl [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) eine Web-App-Definition im App Service-Plan `myAppServicePlan`. Die Web-App-Definition enthält eine URL für den Zugriff auf Ihre Anwendung und konfiguriert verschiedene Optionen zum Bereitstellen Ihres Codes in Azure. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

Ersetzen Sie den Platzhalter `<app_name>` durch Ihren eigenen eindeutigen App-Namen. Dieser eindeutige Name ist Teil des Standarddomänennamens für die Web-App. Daher muss er für alle Apps in Azure eindeutig sein. Sie können der Web-App einen benutzerdefinierten Domänennameneintrag zuordnen, bevor Sie sie für Ihre Benutzer verfügbar machen.

Wenn die Web-App-Definition fertig ist, zeigt die Azure CLI Informationen wie im folgenden Beispiel an: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Konfigurieren von Java 

Richten Sie mit dem Befehl [az appservice web config update](/cli/azure/webapp/config) die für Ihre App erforderliche Java-Laufzeitkonfiguration ein.

Der folgende Befehl konfiguriert die Web-App für die Ausführung mit einer aktuellen Java 8 JDK-Version und [Apache Tomcat](https://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>Erstellen eines GitHub-Repositorys
Öffnen Sie das Repository [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample) (Einfache Java-Web-App für Azure). Klicken Sie auf die Schaltfläche **Fork** (Verzweigen) in der rechten oberen Ecke, um das Repository in Ihr GitHub-Konto zu verzweigen.

* Öffnen Sie in der GitHub-Webbenutzeroberfläche die **Jenkinsfile**-Datei. Klicken Sie auf das Stiftsymbol, um diese Datei zu bearbeiten und die Ressourcengruppe und den Namen Ihrer Web-App in Zeile 20 bzw. 21 zu aktualisieren.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* Ändern Sie Zeile 23, um die Anmeldeinformationen-ID in Ihrer Jenkins-Instanz zu aktualisieren.

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Erstellen einer Jenkins-Pipeline
Öffnen Sie Jenkins in einem Webbrowser, und klicken Sie auf **New Item** (Neues Element). 

* Geben Sie einen Namen für den Auftrag an, und wählen Sie **Pipeline** aus. Klicken Sie auf **OK**.
* Klicken Sie dann auf die Registerkarte **Pipeline**. 
* Wählen Sie als **Definition** die Option **Pipeline script from SCM** (Pipeline-Skript von SCM) aus.
* Wählen Sie als **SCM** die Option **Git** aus.
* Geben Sie die GitHub-URL für Ihr verzweigtes Repository aus: https:\<Ihr verzweigtes Repository\>.git
* Klicken Sie unten auf der Seite auf **Speichern**.

## <a name="test-your-pipeline"></a>Testen Ihrer Pipeline
* Navigieren Sie zu der Pipeline, die Sie erstellt haben, und klicken Sie auf **Build Now** (Jetzt erstellen).
* Die Erstellung sollte nach wenigen Sekunden erfolgen. Sie können zum Build navigieren und auf **Console Output** (Konsolenausgabe) klicken, um Details anzuzeigen.

## <a name="verify-your-web-app"></a>Überprüfen Ihrer Web-App
Zur Überprüfung, ob die WAR-Datei erfolgreich an Ihre Web-App bereitgestellt wurde, öffnen Sie einen Webbrowser:

* Gehen Sie zu http://&lt;App_Name>.azurewebsites.net/api/calculator/ping  
Folgendes wird angezeigt:

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* Gehen Sie zu http://&lt;App_Name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (ersetzen Sie &lt;x> und &lt;y> durch beliebige Zahlen), um die Summe aus x und y zu erhalten.

![Rechner: Addieren](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Bereitstellen an Azure-Web-App unter Linux
Nun wissen Sie, wie Sie die Azure CLI in Ihrer Jenkins-Pipeline verwenden, und können das Skript zum Bereitstellen an eine Azure-Web-App unter Linux ändern.

Web-App unter Linux unterstützt eine andere Möglichkeit der Bereitstellung, und zwar die Verwendung von Docker. Dafür müssen Sie eine Dockerfile-Datei bereitstellen, die Ihre Web-App mit Dienstlaufzeit in ein Docker-Image verpackt. Das Plug-In erstellt dann das Image, übermittelt es per Push an eine Docker-Registrierung und stellt das Image an Ihre Web-App bereit.

* Befolgen Sie die [hier](../app-service/containers/quickstart-nodejs.md) aufgeführten Schritte, um eine Azure-Web-App zu erstellen, die unter Linux ausgeführt wird.
* Installieren Sie Docker auf Ihrer Jenkins-Instanz anhand der Anweisungen in diesem [Artikel](https://docs.docker.com/engine/installation/linux/ubuntu/).
* Erstellen Sie eine Containerregistrierung im Azure-Portal, indem Sie die [hier](/azure/container-registry/container-registry-get-started-azure-cli) aufgeführten Schritte befolgen.
* Bearbeiten Sie im Repository [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample), das Sie verzweigt haben, die **Jenkinsfile2**-Datei:
    * Aktualisieren Sie in den Zeilen 18-21 die Namen Ihrer Ressourcengruppe, Ihrer Web-App bzw. Ihres ACR. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * Aktualisieren Sie in Zeile 24 \<azsrvprincipal\> mit Ihrer Anmeldeinformationen-ID.
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Erstellen Sie eine neue Jenkins-Pipeline wie bereits bei der Bereitstellung an die Azure-Web-App unter Windows. Verwenden Sie in diesem Fall aber **Jenkinsfile2**.
* Führen Sie den neuen Auftrag aus.
* Führen Sie zur Überprüfung in der Azure CLI Folgendes aus:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    Sie erhalten folgendes Ergebnis:
    
    ```
    [
    "calculator"
    ]
    ```
    
    Gehen Sie zu http://&lt;App_Name>.azurewebsites.net/api/calculator/ping. Es wird folgende Nachricht angezeigt: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Gehen Sie zu http://&lt;App_Name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (ersetzen Sie &lt;x> und &lt;y> durch beliebige Zahlen), um die Summe aus x und y zu erhalten.
    
## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie eine Jenkins-Pipeline konfiguriert, die den Quellcode in einem GitHub-Repository auscheckt. Maven zum Erstellen einer WAR-Datei ausführt und dann die Azure CLI für die Bereitstellung an Azure App Service nutzt. Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> * eine Jenkins-VM erstellen können
> * Konfigurieren von Jenkins
> * eine Web-App in Azure erstellen
> * ein GitHub-Repository erstellen
> * Erstellen einer Jenkins-Pipeline
> * die Pipeline ausführen und die Web-App überprüfen
