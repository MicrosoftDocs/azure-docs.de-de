---
title: Bereitstellen in Azure App Service mit dem Jenkins-Plug-In
description: Erfahren Sie, wie Sie das Jenkins-Plug-In für Azure App Service zum Bereitstellen einer Java-Web-App für Azure in Jenkins verwenden.
ms.service: jenkins
keywords: Jenkins, Azure, DevOps, App Service
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 29a842f7dfcf720f29fcff80d2e736893c824f5a
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65949558"
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Bereitstellen in Azure App Service mit dem Jenkins-Plug-In 

Zum Bereitstellen einer Java-Web-App für Azure können Sie die Azure-Befehlszeilenschnittstelle in der [Jenkins-Pipeline](/azure/jenkins/execute-cli-jenkins-pipeline) oder das [Jenkins-Plug-In für Azure App Service](https://plugins.jenkins.io/azure-app-service) verwenden. Version 1.0 des Jenkins-Plug-Ins unterstützt die kontinuierliche Bereitstellung mithilfe der Web-Apps-Funktion von Azure App Service über:
* Dateiupload.
* Docker für Web-Apps unter Linux

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Konfigurieren von Jenkins zum Bereitstellen von Web-Apps über den Dateiupload.
> * Konfigurieren von Jenkins zum Bereitstellen von Web-App für Container

## <a name="create-and-configure-a-jenkins-instance"></a>Erstellen und Konfigurieren einer Jenkins-Instanz

Wenn Sie noch nicht über einen Jenkins-Master verfügen, sollten Sie mit der [Projektmappenvorlage](install-jenkins-solution-template.md) beginnen, in der Java Development Kit (JDK) Version 8 und die folgenden erforderlichen Jenkins-Plug-Ins enthalten sind:

* [Jenkins-Git-Client-Plug-In](https://plugins.jenkins.io/git-client) Version 2.4.6 
* [Docker-Commons-Plug-In](https://plugins.jenkins.io/docker-commons) Version 1.4.0
* [Azure-Anmeldeinformationen](https://plugins.jenkins.io/azure-credentials) Version 1.2
* [Azure App Service](https://plugins.jenkins.io/azure-app-service) Version 0.1

Mithilfe des Jenkins-Plug-Ins können Sie eine Web-App in einer beliebigen Sprache bereitstellen, die von Web-Apps unterstützt wird, wie beispielsweise C#, PHP, Java und Node.js. In diesem Tutorial wird eine [einfache Java-Web-App für Azure](https://github.com/azure-devops/javawebappsample) verwendet. Wählen Sie die Schaltfläche **Fork** (Verzweigen) in der rechten oberen Ecke der GitHub-Benutzeroberfläche aus, um das Repository in Ihr GitHub-Konto zu verzweigen.  

> [!NOTE]
> Das Java JDK und Maven sind zum Erstellen des Java-Projekts erforderlich. Installieren Sie diese Komponenten auf dem Jenkins-Master oder dem VM-Agent, wenn Sie diesen für Continuous Integration nutzen. Wenn Sie eine Java SE-Anwendung bereitstellen, wird ZIP auch auf dem Buildserver benötigt.
>

Melden Sie sich zum Installieren der Komponenten mithilfe von SSH bei der Jenkins-Instanz an, und führen Sie die folgenden Befehle aus:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Installieren Sie Docker auf dem Jenkins-Master oder dem VM-Agent, der für den Build verwendet wird, um in Web-App für Container bereitzustellen. Eine Anleitung hierzu finden Sie unter [Installieren von Docker unter Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntu/).

## <a name="service-principal"></a> Hinzufügen eines Azure-Dienstprinzipals zu den Jenkins-Anmeldeinformationen

Ein Azure-Dienstprinzipal ist für die Bereitstellung in Azure erforderlich. 


1. Verwenden Sie die [Azure-Befehlszeilenschnittstelle](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) oder das [Azure-Portal](/azure/azure-resource-manager/resource-group-create-service-principal-portal), um einen Azure-Dienstprinzipal zu erstellen.
2. Wählen Sie im Jenkins-Dashboard die Option **Anmeldeinformationen** > **System** aus. Wählen Sie anschließend **Globale Anmeldeinformationen (uneingeschränkt)** aus.
3. Wählen Sie zum Hinzufügen eines Microsoft Azure-Dienstprinzipals die Option **Anmeldeinformationen hinzufügen** aus. Füllen Sie die Felder **Abonnement-ID**, **Client-ID**, **Geheimer Clientschlüssel** und **OAuth 2.0-Token-Endpunkt** aus. Legen Sie das Feld **ID** auf **mySp** fest. Diese ID wird für spätere Aktionen in diesem Artikel verwendet.


## <a name="configure-jenkins-to-deploy-web-apps-by-uploading-files"></a>Konfigurieren von Jenkins zum Bereitstellen von Web-Apps durch das Hochladen von Dateien

Um Ihr Projekt in Web Apps bereitzustellen, können Sie Ihre Buildartefakte per Dateiupload hochladen. Azure App Service unterstützt mehrere Bereitstellungsoptionen. Durch das Jenkins-Plug-In von Azure App Service können sie die Bereitstellungsoption einfach basierend auf dem Dateityp ableiten. 

* Für Java EE-Anwendungen wird die [WAR-Bereitstellung](/azure/app-service/deploy-zip#deploy-war-file) verwendet.
* Für Java SE-Anwendungen wird die [ZIP-Bereitstellung](/azure/app-service/deploy-zip#deploy-zip-file) verwendet.
* Für andere Sprachen wir die [Git-Bereitstellung](/azure/app-service/deploy-local-git) verwendet.

Vor dem Einrichten des Auftrags in Jenkins benötigen Sie einen Azure App Service-Plan und eine Web-App zum Ausführen der Java-App.


1. Erstellen Sie mit dem [Azure CLI-Befehl](/cli/azure/appservice/plan#az-appservice-plan-create) `az appservice plan create` einen Azure App Service-Plan mit dem Tarif **FREE**. Der App Service-Plan definiert die physischen Ressourcen, die zum Hosten Ihrer Apps verwendet werden. Alle einem App Service-Plan zugewiesenen Anwendungen teilen sich diese Ressourcen. Mit gemeinsamen Ressourcen können Sie beim Hosten mehrerer Apps Kosten sparen.
2. Erstellen Sie eine Web-App. Sie können dafür entweder das [Azure-Portal](/azure/app-service/configure-common) oder den folgenden Azure CLI-Befehl `az` verwenden:
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. Richten Sie die Java-Runtimekonfiguration ein, die für Ihre App erforderlich ist. Mit dem folgenden Azure CLI-Befehl wird die Web-App für die Ausführung mit einem aktuellen JDK Version 8 und [Apache Tomcat](https://tomcat.apache.org/) Version 8.0 konfiguriert:
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>Einrichten des Jenkins-Auftrags

1. Erstellen Sie im Jenkins-Dashboard ein neues **Freestyle**-Projekt.
2. Konfigurieren Sie das Feld **Quellcodeverwaltung** so, dass Ihre lokale Verzweigung der [einfachen Java-Web-App für Azure](https://github.com/azure-devops/javawebappsample) verwendet wird. Geben Sie die **Repository-URL** an. Beispiel: http:\//github.com/&lt;Ihre_ID>/javawebappsample.
3. Fügen Sie zum Erstellen des Projekts mithilfe von Maven einen Schritt hinzu, indem Sie den Befehl **Execute shell** (Shell ausführen) hinzufügen. Für dieses Beispiel wird ein zusätzlicher Schritt benötigt, um die \*.WAR-Datei im Zielordner in **ROOT.war** umzubenennen:   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. Fügen Sie eine Postbuildaktion hinzu, indem Sie **Publish an Azure Web App** (Azure-Web-App veröffentlichen) wählen.
5. Geben Sie **mySp** als Azure-Dienstprinzipal an. Dieser Prinzipal wurde in einem vorherigen Schritt unter [Azure-Anmeldeinformationen](#service-principal) gespeichert.
6. Wählen Sie im Abschnitt **App-Konfiguration** die Ressourcengruppe und die Web-App Ihres Abonnements aus. Das Jenkins-Plug-In erkennt automatisch, ob die Web-App auf Windows oder Linux basiert. Für eine Windows-basierte Web-App wird die Option **Publish Files** (Dateien veröffentlichen) angezeigt.
7. Fügen Sie die Dateien ein, die Sie bereitstellen möchten. Geben Sie z.B. das WAR-Paket an, wenn Sie Java verwenden. Verwenden Sie die optionalen Parameter **Quellverzeichnis** und **Zielverzeichnis**, um die Quell- und Zielordner für den Dateiupload anzugeben. Die Java-Web-App in Azure wird auf einem Tomcat-Server ausgeführt. Laden Sie für Java Ihr WAR-Paket in den Ordner „Webapps“ hoch. Legen Sie für dieses Beispiel das **Quellverzeichnis** auf **Ziel** und das **Zielverzeichnis** auf **Webapps** fest.
8. Wenn Sie die Bereitstellung für einen anderen Slot als den Produktionsslot durchführen möchten, können Sie auch den **Slot**-Namen festlegen.
9. Speichern Sie das Projekt, und führen Sie den Buildvorgang durch. Ihre Web-App wird in Azure bereitgestellt, wenn der Buildvorgang abgeschlossen ist.

### <a name="deploy-web-apps-by-uploading-files-using-jenkins-pipeline"></a>Bereitstellen von Web-Apps durch das Hochladen von Dateien unter Verwendung der Jenkins-Pipeline

Das Jenkins-Plug-In für Azure App Service ist für die Pipeline vorbereitet. Das folgende Beispiel finden Sie im GitHub-Repository.

1. Öffnen Sie in der GitHub-Benutzeroberfläche die Datei **Jenkinsfile_ftp_plugin**. Klicken Sie zum Bearbeiten der Datei auf das Stiftsymbol. Aktualisieren Sie jeweils die Definitionen **resourceGroup** und **webAppName** in Zeile 11 und 12 für Ihre Web-App:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Legen Sie die Definition **withCredentials** in Zeile 14 auf die Anmeldeinformationen-ID Ihrer Jenkins-Instanz fest:
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Erstellen einer Jenkins-Pipeline

1. Öffnen Sie Jenkins in einem Webbrowser. Wählen Sie **Neues Element** aus.
2. Geben Sie einen Namen für den Auftrag an, und wählen Sie **Pipeline** aus. Klicken Sie auf **OK**.
3. Wählen Sie die Registerkarte **Pipeline** aus.
4. Wählen Sie als Wert für die **Definition** die Option **Pipeline script from SCM** (Pipeline-Skript von SCM) aus.
5. Wählen Sie als Wert für **SCM** die Option **Git** aus. Geben Sie die GitHub-URL für Ihr verzweigtes Repository ein. Zum Beispiel: „https://&lt;Ihr_verzweigtes_Repository>.git“.
6. Aktualisieren Sie den **Skriptpfad** in „**Jenkinsfile_ftp_plugin**“.
7. Klicken Sie auf **Speichern**, und führen Sie den Auftrag aus.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>Konfigurieren von Jenkins zum Bereitstellen von Web-App für Container

Web-Apps unter Linux unterstützen die Bereitstellung mithilfe von Docker. Für die Bereitstellung Ihrer Web-App mit Docker müssen Sie eine Dockerfile-Datei angeben, mit der Ihre Web-App mit einer Dienstruntime in einem Docker-Image verpackt wird. Das Jenkins-Plug-In erstellt dann das Image, übermittelt es mittels Push an eine Docker-Registrierung und stellt das Image für Ihre Web-App bereit.

Für Web-Apps unter Linux werden auch herkömmliche Bereitstellungsmethoden wie Git und Dateiupload unterstützt. Dies gilt jedoch nur für integrierte Sprachen (.NET Core, Node.js, PHP und Ruby). Für andere Sprachen müssen Sie Ihren Anwendungscode und die Dienstruntime gemeinsam in ein Docker-Image packen und Docker zum Bereitstellen verwenden.

Vor dem Einrichten des Auftrags in Jenkins benötigen Sie eine Web-App unter Linux. Außerdem ist eine Containerregistrierung zum Speichern und Verwalten Ihrer privaten Docker-Containerimages erforderlich. Sie können DockerHub zum Erstellen der Containerregistrierung verwenden. In diesem Beispiel wird Azure Container Registry verwendet.

* [Erstellen einer Web-App unter Linux](../app-service/containers/quickstart-nodejs.md).
* Azure Container Registry ist ein verwalteter Dienst vom Typ [Docker-Registrierung](https://docs.docker.com/registry/), der auf Version 2.0 der Open Source-Docker-Registrierung basiert. [Erstellen einer Azure-Containerregistrierung](/azure/container-registry/container-registry-get-started-azure-cli). Sie können auch DockerHub verwenden.

### <a name="set-up-the-jenkins-job-for-docker"></a>Einrichten des Jenkins-Auftrags für Docker

1. Erstellen Sie im Jenkins-Dashboard ein neues **Freestyle**-Projekt.
2. Konfigurieren Sie das Feld **Quellcodeverwaltung** so, dass Ihre lokale Verzweigung der [einfachen Java-Web-App für Azure](https://github.com/azure-devops/javawebappsample) verwendet wird. Geben Sie die **Repository-URL** an. Beispiel: http:\//github.com/&lt;Ihre_ID>/javawebappsample.
3. Fügen Sie zum Erstellen des Projekts mithilfe von Maven einen Schritt hinzu, indem Sie den Befehl **Execute shell** (Shell ausführen) hinzufügen. Fügen Sie dem Befehl die folgende Zeile hinzu:
    ```bash
    mvn clean package
    ```

4. Fügen Sie eine Postbuildaktion hinzu, indem Sie **Publish an Azure Web App** (Azure-Web-App veröffentlichen) wählen.
5. Geben Sie **mySp** als Azure-Dienstprinzipal an. Dieser Prinzipal wurde in einem vorherigen Schritt unter [Azure-Anmeldeinformationen](#service-principal) gespeichert.
6. Wählen Sie im Abschnitt **App-Konfiguration** die Ressourcengruppe und eine Linux-Web-App Ihres Abonnements aus.
7. Wählen Sie die Option **Publish via Docker** (Über Docker veröffentlichen) aus.
8. Geben Sie den Pfadwert der **Dockerfile**-Datei ein. Sie können die Standardeinstellung „/Dockerfile“ beibehalten.
Geben Sie als **Docker registry URL** (URL für die Docker-Registrierung) die URL im Format „https://&lt;IhreRegistrierung>.azurecr.io“ an, wenn Sie Azure Container Registry verwenden. Lassen Sie das Feld leer, wenn Sie DockerHub verwenden.
9. Fügen Sie als Wert für **Registry credentials** (Anmeldeinformationen für die Registrierung) die Anmeldeinformationen für die Containerregistrierung hinzu. Sie können die Benutzer-ID und das Kennwort abrufen, indem Sie in der Azure-Befehlszeilenschnittstelle die folgenden Befehle ausführen. Mit dem ersten Befehl wird das Administratorkonto aktiviert:
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. Der Name und der Tagwert des Docker-Images auf der Registerkarte **Erweitert** sind optional. Der Wert für den Imagenamen wird standardmäßig von dem Imagenamen abgeleitet, den Sie im Azure-Portal in der **Docker-Container**-Einstellung konfiguriert haben. Das Tag wird mithilfe von $BUILD_NUMBER generiert.
    > [!NOTE]
    > Stellen Sie sicher, dass Sie entweder den Imagenamen im Azure-Portal angeben oder auf der Registerkarte **Erweitert** einen Wert für das **Docker-Image** bereitstellen. Legen Sie in diesem Beispiel den Wert für das **Docker-Image** auf „&lt;Ihre_Registrierung >.azurecr.io/calculator“ fest, und lassen Sie das Feld für das **Docker Image Tag** (Docker-Imagetag) leer.

11. Die Bereitstellung schlägt fehl, wenn Sie eine integrierte Docker-Imageeinstellung verwenden. Ändern Sie die Docker-Konfiguration so, dass das benutzerdefinierte Image in der **Docker-Container**-Einstellung im Azure-Portal verwendet wird. Verwenden Sie für ein integriertes Image den Dateiuploadansatz, um die Bereitstellung durchzuführen.
12. Ähnlich wie beim Dateiuploadansatz können Sie einen anderen **Slot**-Namen als den **Produktionsslot** auswählen.
13. Speichern und erstellen Sie das Projekt. Ihr Containerimage wird per Pushvorgang in Ihre Registrierung übermittelt und die Web-App bereitgestellt.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>Bereitstellen von Web-App für Container mithilfe der Jenkins-Pipeline

1. Öffnen Sie in der GitHub-Benutzeroberfläche die Datei **Jenkinsfile_container_plugin**. Klicken Sie zum Bearbeiten der Datei auf das Stiftsymbol. Aktualisieren Sie jeweils die Definitionen **resourceGroup** und **webAppName** in Zeile 11 und 12 für Ihre Web-App:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Ändern Sie Zeile 13, um Ihren Server für die Containerregistrierung anzugeben:
    ```java
    def registryServer = '<registryURL>'
    ```

3. Ändern Sie Zeile 16, um die Anmeldeinformationen-ID in Ihrer Jenkins-Instanz zu verwenden:
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Erstellen einer Jenkins-Pipeline    

1. Öffnen Sie Jenkins in einem Webbrowser. Wählen Sie **Neues Element** aus.
2. Geben Sie einen Namen für den Auftrag an, und wählen Sie **Pipeline** aus. Klicken Sie auf **OK**.
3. Wählen Sie die Registerkarte **Pipeline** aus.
4. Wählen Sie als Wert für die **Definition** die Option **Pipeline script from SCM** (Pipeline-Skript von SCM) aus.
5. Wählen Sie als Wert für **SCM** die Option **Git** aus. Geben Sie die GitHub-URL für Ihr verzweigtes Repository ein. Zum Beispiel: „https://&lt;Ihr_verzweigtes_Repository>.git“.
7. Aktualisieren Sie den **Skriptpfad** in „**Jenkinsfile_container_plugin**“.
8. Klicken Sie auf **Speichern**, und führen Sie den Auftrag aus.

## <a name="verify-your-web-app"></a>Überprüfen Ihrer Web-App

1. Öffnen Sie einen Webbrowser, um zu überprüfen, ob die WAR-Datei erfolgreich für Ihre Web-App bereitgestellt wurde.
2. Wechseln Sie zu „http://&lt;Ihr_App_Name>.azurewebsites.net/api/calculator/ping“. Ersetzen Sie „&lt;Ihr_App_Name>“ durch den Namen Ihrer Web-App. Es wird folgende Nachricht angezeigt:
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. Wechseln Sie zu „http://&lt;Ihr_App_Name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>“. Ersetzen Sie „&lt;x>“ und „&lt;y>“ durch beliebige Zahlen, um die Summe aus x + y zu erhalten. Der Rechner zeigt die Summe an: ![Rechner: Addieren](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>Für Azure App Service unter Linux

1. Führen Sie den folgenden Befehl in der Azure-Befehlszeilenschnittstelle aus, um Ihre Web-App zu überprüfen:
    ```CLI
    az acr repository list -n <myRegistry> -o json
    ```
    Die folgende Meldung wird angezeigt:
    ```CLI
    ["calculator"]
    ```
    
2. Wechseln Sie zu „http://&lt;Ihr_App_Name>.azurewebsites.net/api/calculator/ping“. Ersetzen Sie „&lt;Ihr_App_Name>“ durch den Namen Ihrer Web-App. Es wird folgende Nachricht angezeigt: 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. Wechseln Sie zu „http://&lt;Ihr_App_Name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>“. Ersetzen Sie „&lt;x>“ und „&lt;y>“ durch beliebige Zahlen, um die Summe aus x + y zu erhalten.
    
## <a name="troubleshooting-the-jenkins-plugin"></a>Problembehandlung beim Jenkins-Plug-In

Wenn bei den Jenkins-Plug-Ins Fehler auftreten, melden Sie das Problem auf der [Jenkins-JIRA-Seite](https://issues.jenkins-ci.org/) für die jeweilige Komponente.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie das Jenkins-Plug-In für Azure App Service für die Bereitstellung in Azure verwendet.

Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Konfigurieren von Jenkins zum Bereitstellen in Azure App Service per Dateiupload 
> * Konfigurieren von Jenkins zum Bereitstellen in Web-App für Container 
