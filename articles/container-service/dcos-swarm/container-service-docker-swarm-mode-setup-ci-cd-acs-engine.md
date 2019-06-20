---
title: (VERALTET) CI/CD mit Azure Container Service-Engine und Swarm-Modus
description: Verwenden Sie Azure Container Service-Engine mit Docker Swarm-Modus, einer Azure Container Registry-Instanz und Azure DevOps zum fortlaufenden Bereitstellen einer .NET Core-Anwendung mit mehreren Containern.
services: container-service
author: diegomrtnzg
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/27/2017
ms.author: diegomrtnzg
ms.custom: mvc
ms.openlocfilehash: 8aa62e4ed65f8223071786ac165f8343cb6901d5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60430422"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-azure-devops"></a>(VERALTET) Vollständige CI/CD-Pipeline zum Bereitstellen einer Anwendung mit mehreren Containern in Azure Container Service mit ACS-Engine und Docker Swarm-Modus mithilfe von Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

*Dieser Artikel basiert auf der Dokumentation [Vollständige CI/CD-Pipeline zum Bereitstellen einer Anwendung mit mehreren Containern in Azure Container Service mit Docker Swarm mithilfe von Azure DevOps](container-service-docker-swarm-setup-ci-cd.md).*

Heutzutage ist eine der größten Herausforderungen bei der Entwicklung moderner Anwendungen für die Cloud die Fähigkeit, diese Anwendungen fortlaufend bereitzustellen. In diesem Artikel erfahren Sie, wie Sie eine vollständige CI/CD-Pipeline (Continuous Integration und Continuous Deployment) mithilfe folgender Komponenten implementieren können: 
* Azure Container Service-Engine mit Docker Swarm-Modus
* Azure Container Registry
* Azure DevOps

Dieser Artikel basiert auf einer einfachen Anwendung, die auf [GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux) verfügbar ist und mit ASP.NET Core entwickelt wurde. Die Anwendung besteht aus vier Diensten: drei Web-APIs und einem Web-Front-End:

![Beispielanwendung „MyShop“](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

Ziel ist die fortlaufende Bereitstellung dieser Anwendung in einem Cluster im Docker Swarm-Modus mithilfe von Azure DevOps Services. Die folgende Abbildung veranschaulicht diese Continuous Delivery-Pipeline:

![Beispielanwendung „MyShop“](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Es folgt eine kurze Erläuterung der Schritte:

1. Für Änderungen am Code erfolgt im Quellcoderepository (in diesem Fall GitHub) ein Commit. 
2. GitHub löst einen Build in Azure DevOps aus. 
3. Azure DevOps ruft die neueste Version des Quellcodes ab und erstellt alle Images, aus denen sich die Anwendung zusammensetzt. 
4. Azure DevOps überträgt jedes Image per Push in eine Docker-Registrierung, die mithilfe des Azure Container Registry-Diensts erstellt wurde. 
5. Azure DevOps löst ein neues Release aus. 
6. Das Release wendet einige Befehle über SSH auf den Masterknoten des Azure Container Service-Clusters an. 
7. Der Docker Swarm-Modus für den Cluster ruft die neueste Version der Images per Pull ab. 
8. Die neue Version der Anwendung wird mithilfe von Docker Stack bereitgestellt. 

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, müssen Sie die folgenden Aufgaben ausführen:

- [Erstellen eines Clusters im Swarm-Modus in Azure Container Service mit ACS-Engine](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Verbinden mit einem Azure Container Service-Cluster](../container-service-connect.md)
- [Erstellen einer Azure-Containerregistrierung](../../container-registry/container-registry-get-started-portal.md)
- [Erstellen einer Azure DevOps-Organisation und eines Azure DevOps-Projekts](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Verzweigen des GitHub-Repositorys zu Ihrem GitHub-Konto](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Der Docker Swarm-Orchestrator in Azure Container Service nutzt eine ältere eigenständige Swarm-Version. Derzeit ist der integrierte [Swarm-Modus](https://docs.docker.com/engine/swarm/) (in Docker 1.12 und höher) kein unterstützter Orchestrator in Azure Container Service. Aus diesem Grund verwenden wir die [ACS-Engine](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), eine von der Community bereitgestellte [Schnellstartvorlage](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/), oder eine Docker-Lösung im [Azure Marketplace](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-azure-devops-organization"></a>Schritt 1: Konfigurieren Ihrer Azure DevOps-Organisation 

In diesem Abschnitt konfigurieren Sie Ihre Azure DevOps-Organisation. Klicken Sie zum Konfigurieren von Azure DevOps Services-Dienstendpunkten in Ihrem Azure DevOps-Projekt auf der Symbolleiste auf das Symbol **Einstellungen**, und wählen Sie **Dienste** aus.

![Öffnen des Dienstendpunkts](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-azure-devops-and-azure-account"></a>Verbinden von Azure DevOps mit dem Azure-Konto

Richten Sie eine Verbindung zwischen Ihrem Azure DevOps-Projekt und Ihrem Azure-Konto ein.

1. Klicken Sie auf der linken Seite auf **Neuer Dienstendpunkt** > **Azure Resource Manager**.
2. Um Azure DevOps zur Verwendung mit Ihrem Azure-Konto zu autorisieren, wählen Sie Ihr **Abonnement** aus, und klicken Sie auf **OK**.

    ![Azure DevOps: Autorisieren des Azure-Kontos](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-azure-devops-and-github"></a>Verbinden von Azure DevOps mit GitHub

Richten Sie eine Verbindung zwischen Ihrem Azure DevOps-Projekt und Ihrem GitHub-Konto ein.

1. Klicken Sie auf der linken Seite auf **Neuer Dienstendpunkt** > **GitHub**.
2. Um Azure DevOps zur Verwendung mit Ihrem GitHub-Konto zu autorisieren, klicken Sie auf **Autorisieren** und befolgen die Anweisungen im eingeblendeten Fenster.

    ![Azure DevOps: Autorisieren von GitHub](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-azure-devops-to-your-azure-container-service-cluster"></a>Verbinden von Azure DevOps mit Ihrem Azure Container Service-Cluster

Die letzten Schritte vor dem Start mit der CI/CD-Pipeline bestehen aus dem Konfigurieren externer Verbindungen mit Ihrem Docker Swarm-Cluster in Azure. 

1. Fügen Sie für den Docker Swarm-Cluster einen Endpunkt des Typs **SSH** hinzu. Geben Sie dann die SSH-Verbindungsinformationen des Swarm-Clusters (Masterknoten) ein.

    ![Azure DevOps: SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Die Konfiguration ist damit abgeschlossen. Im nächsten Schritt erstellen Sie die CI-CD-Pipeline, mit der die Anwendung erstellt und im Docker Swarm-Cluster bereitgestellt wird. 

## <a name="step-2-create-the-build-pipeline"></a>Schritt 2: Erstellen der Buildpipeline

In diesem Schritt richten Sie eine Buildpipeline für Ihr Azure DevOps-Projekt ein und definieren den Buildworkflow für Ihre Containerimages.

### <a name="initial-pipeline-setup"></a>Anfängliche Einrichtung der Pipeline

1. Stellen Sie zum Erstellen einer Buildpipeline eine Verbindung mit Ihrem Azure DevOps-Projekt her, und klicken Sie auf **Build und Release**. Klicken Sie im Abschnitt **Builddefinitionen** auf **+ Neu**. 

    ![Azure DevOps: Neue Buildpipeline](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Wählen Sie den **leeren Prozess** aus.

    ![Azure DevOps: Neue leere Buildpipeline](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Klicken Sie dann auf die Registerkarte **Variablen**, und erstellen Sie zwei neue Variablen: **RegistryURL** und **AgentURL**. Fügen Sie die Werte des DNS der Registrierung und der Cluster-Agents ein.

    ![Azure DevOps: Konfiguration der Buildvariablen](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. Öffnen Sie auf der Seite **Builddefinitionen** die Registerkarte **Trigger**, und konfigurieren Sie den Build für die Verwendung der Continuous Integration mit der Verzweigung des „MyShop“-Projekts, die Sie zuvor zum Erfüllen der Voraussetzungen erstellt haben. Wählen Sie dann **Batchänderungen** aus. Stellen Sie sicher, dass Sie *docker-linux* als **Branchspezifikation** auswählen.

    ![Azure DevOps: Konfiguration des Buildrepositorys](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Klicken Sie schließlich auf die Registerkarte **Optionen**, und legen Sie die Agent-Standardwarteschlange auf **Gehostete Linux-Vorschau** fest.

    ![Azure DevOps: Konfiguration des Host-Agents](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Definieren des Buildworkflows
In den nächsten Schritten definieren Sie den Buildworkflow. Zunächst müssen Sie die Quelle des Codes konfigurieren. Wählen Sie dazu **GitHub** sowie Ihr **Repository** und die **Verzweigung** (docker-linux) aus.

![Azure DevOps: Konfigurieren der Codequelle](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Für die Anwendung *MyShop* müssen fünf Containerimages erstellt werden. Jedes Image wird mithilfe der Dockerfile erstellt, die sich im Projektordner befindet:

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

Für jedes Image benötigen Sie zwei Docker-Schritte: einen zum Erstellen des Images und einen zweiten zum Übertragen des Images per Push in die Azure Container Registry-Instanz. 

1. Um dem Buildworkflow einen Schritt hinzuzufügen, klicken Sie auf **+ Buildschritt hinzufügen** und wählen dann **Docker** aus.

    ![Azure DevOps: Hinzufügen von Buildschritten](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Konfigurieren Sie für jedes Image einen Schritt, der den Befehl `docker build` verwendet.

    ![Azure DevOps: Docker-Build](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Wählen Sie für den Buildvorgang Ihre Azure Container Registry-Instanz, die Aktion **Image erstellen** und die Dockerfile aus, die jedes Image definiert. Legen Sie das **Arbeitsverzeichnis** als Dockerfile-Stammverzeichnis fest, definieren Sie den **Imagenamen**, und wählen Sie **Aktuelles Tag einschließen** aus.
    
    Der Imagename muss dieses Format aufweisen: ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Ersetzen Sie **[NAME]** durch den Imagenamen:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Konfigurieren Sie für jedes Image einen zweiten Schritt, der den Befehl `docker push` verwendet.

    ![Azure DevOps: Docker-Pushvorgang](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Wählen Sie für den Pushvorgang die Azure Container Registry-Instanz und die Aktion zum **Übertragen eines Images per Push** aus. Geben Sie den **Imagenamen** ein, der im vorherigen Schritt erstellt wurde, und wählen Sie **Aktuelles Tag einschließen** aus.

4. Nachdem Sie die Build- und Pushschritte für jedes der fünf Images erstellt haben, fügen Sie dem Buildworkflow drei weitere Schritte hinzu.

   ![Azure DevOps: Hinzufügen einer Befehlszeilenaufgabe](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

   1. Eine Befehlszeilenaufgabe, die ein Bash-Skript verwendet, um *RegistryURL* in der Datei „docker-compose.yml“ durch die RegistryURL-Variable zu ersetzen. 
    
       ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

       ![Azure DevOps: Aktualisieren der Compose-Datei mit Registrierungs-URL](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

   2. Eine Befehlszeilenaufgabe, die ein Bash-Skript verwendet, um *AgentURL* in der Datei „docker-compose.yml“ durch die AgentURL-Variable zu ersetzen.
  
       ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

      1. Eine Aufgabe, die die aktualisierte Compose-Datei als Buildartefakt ablegt, damit sie im Release verwendet werden kann. Die folgenden Abbildungen zeigen Details.

      ![Azure DevOps: Veröffentlichen des Artefakts](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

      ![Azure DevOps: Veröffentlichen der Compose-Datei](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Klicken Sie auf **Speichern und in Warteschlange einreihen**, um die Buildpipeline zu testen.

   ![Azure DevOps: Speichern und Einreihen in die Warteschlange](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Azure DevOps: Neue Warteschlange](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Wenn der **Build** erfolgreich erstellt wurde, wird dieser Bildschirm angezeigt:

   ![Azure DevOps: Buildvorgang erfolgreich](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-pipeline"></a>Schritt 3: Erstellen der Releasepipeline

Mit Azure DevOps können Sie [Releases umgebungsübergreifend verwalten](https://www.visualstudio.com/team-services/release-management/). Sie können Continuous Deployment aktivieren, um sicherzustellen, dass Ihre Anwendung reibungslos in Ihren verschiedenen Umgebungen bereitgestellt wird (z.B. Entwicklungs-, Test-, Präproduktions- und Produktionsumgebung). Sie können eine Umgebung erstellen, die Ihren Azure Container Service-Cluster im Docker Swarm-Modus darstellt.

![Azure DevOps: Freigabe in Azure Container Service](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Einrichtung des ersten Release

1. Um eine Releasepipeline zu erstellen, klicken Sie auf **Releases** >  **+ Freigeben**.

2. Um die Quelle des Artefakts zu konfigurieren, klicken Sie auf **Artefakte** > **Artefaktquelle verknüpfen**. Verknüpfen Sie diese neue Releasepipeline mit dem Build, den Sie im vorherigen Schritt definiert haben. Anschließend steht die Datei „docker-compose.yml“ im Releaseprozess zur Verfügung.

    ![Azure DevOps: Releaseartefakte](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Um den Releasetrigger zu konfigurieren, klicken Sie auf **Trigger**, und wählen Sie **Continuous Deployment**. Legen Sie den Trigger für dieselbe Artefaktquelle fest. Diese Einstellung stellt sicher, dass ein neues Release gestartet wird, wenn der Build erfolgreich abgeschlossen wurde.

    ![Azure DevOps: Releasetrigger](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Um die Releasevariablen zu konfigurieren, klicken Sie auf **Variablen**, und wählen Sie **+ Variable** aus, um drei neue Variablen mit den Informationen der Registrierung zu erstellen: **docker.username**, **docker.password** und **docker.registry**. Fügen Sie die Werte des DNS der Registrierung und der Cluster-Agents ein.

    ![Azure DevOps: Konfiguration des Buildrepositorys](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Klicken Sie, wie in der Abbildung oben dargestellt, auf das Kontrollkästchen **Sperre** in docker.password. Diese Einstellung ist wichtig, um das Kennwort zu beschränken.
    >

### <a name="define-the-release-workflow"></a>Definieren des Releaseworkflows

Der Releaseworkflow besteht aus zwei Aufgaben, die Sie hinzufügen.

1. Konfigurieren Sie eine Aufgabe, um eine Compose-Datei sicher in den Ordner *deploy* auf dem Docker Swarm-Masterknoten mithilfe der zuvor konfigurierten SSH-Verbindung zu kopieren. Die folgenden Abbildungen zeigen Details.
    
    Quellordner: ```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Azure DevOps: Release mit SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Konfigurieren Sie eine zweite Aufgabe zum Ausführen eines Bash-Befehls zum Anwenden der Befehle `docker` und `docker stack deploy` auf den Masterknoten. Die folgenden Abbildungen zeigen Details.

    ```
    docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth
    ```

    ![Azure DevOps: Release mit Bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    Der auf den Masterknoten angewendete Befehl verwendet die Docker-Befehlszeilenschnittstelle und die Docker-Compose-Befehlszeilenschnittstelle zum Ausführen der folgenden Aufgaben:

   - Melden Sie sich bei Azure Container Registry an (diese verwendet drei Buildvariablen, die auf der Registerkarte **Variablen** definiert sind).
   - Definieren Sie die Variable **DOCKER_HOST** für die Zusammenarbeit mit dem Swarm-Endpunkt (:2375).
   - Navigieren Sie zum Ordner *deploy*, der während der vorherigen sicheren Kopieraufgabe erstellt wurde und die Datei „docker-compose.yml“ enthält. 
   - Führen Sie `docker stack deploy`-Befehle aus, die die neuen Images per Pull übertragen und die Container erstellen.

     >[!IMPORTANT]
     > Lassen Sie, wie in der Abbildung oben gezeigt, das Kontrollkästchen **Fehler für STDERR** deaktiviert. Mit dieser Einstellung können Sie den Releaseprozess abschließen, da `docker-compose` in der Standardfehlerausgabe mehrere Diagnosemeldungen ausgibt, z.B. zu beendeten oder gelöschten Containern. Wenn Sie das Kontrollkästchen aktivieren, meldet Azure DevOps, dass im Verlauf des Release Fehler aufgetreten sind, obwohl alles wie gewünscht verlaufen ist.
     >
3. Speichern Sie diese neue Releasepipeline.

## <a name="step-4-test-the-cicd-pipeline"></a>Schritt 4: Testen der CI/CD-Pipeline

Nachdem Sie die Konfiguration abgeschlossen haben, ist es Zeit, diese neue CI/CD-Pipeline zu testen. Die einfachste Möglichkeit zum Testen ist das Aktualisieren des Quellcodes und Ausführen eines Commits für die Änderungen in Ihrem GitHub-Repository. Einige Sekunden nach der Übertragung des Codes per Push wird in Azure DevOps ein neuer Build ausgeführt. Nach erfolgreichem Abschluss wird ein neues Release ausgelöst, und die neue Version der Anwendung wird im Azure Container Service-Cluster bereitgestellt.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu CI/CD mit Azure DevOps finden Sie in der [Übersicht über den Azure DevOps-Build](https://www.visualstudio.com/docs/build/overview).
* Weitere Informationen zur ACS-Engine finden Sie im [GitHub-Repository zum ACS-Engine](https://github.com/Azure/acs-engine).
* Weitere Informationen zum Docker Swarm-Modus finden Sie unter [Docker Swarm mode overview](https://docs.docker.com/engine/swarm/) (Übersicht über Docker Swarm-Modus).
