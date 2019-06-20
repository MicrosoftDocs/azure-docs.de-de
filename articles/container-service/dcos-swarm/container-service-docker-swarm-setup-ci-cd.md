---
title: (VERALTET) Continuous Integration und Continuous Deployment (CI/CD) mit Azure Container Service und Swarm
description: Verwenden Sie Azure Container Service mit Docker Swarm, einer Azure Container Registry-Instanz und Azure DevOps zum fortlaufenden Bereitstellen einer .NET Core-Anwendung mit mehreren Containern.
services: container-service
author: jcorioland
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: f28ea3dd2837a241c538057bd118409d4f5b858a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60643581"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>(VERALTET) Vollständige CI/CD-Pipeline zum Bereitstellen einer Anwendung mit mehreren Containern in Azure Container Service mit Docker Swarm mithilfe von Azure DevOps Services

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Eine der größten Herausforderungen bei der Entwicklung moderner Anwendungen für die Cloud ist die Fähigkeit, diese Anwendungen fortlaufend bereitzustellen. In diesem Artikel erfahren Sie, wie Sie eine vollständige CI/CD-Pipeline (Continuous Integration/Continuous Deployment) mithilfe von Azure Container Service mit Docker Swarm, Azure Container Registry und der Azure Pipelines-Verwaltung implementieren.

Dieser Artikel basiert auf einer einfachen Anwendung, die auf [GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs) verfügbar ist und mit ASP.NET Core entwickelt wurde. Die Anwendung besteht aus vier Diensten: drei Web-APIs und einem Web-Front-End:

![Beispielanwendung „MyShop“](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

Ziel ist die fortlaufende Bereitstellung dieser Anwendung in einem Docker Swarm-Cluster mithilfe von Azure DevOps Services. Die folgende Abbildung veranschaulicht diese Continuous Delivery-Pipeline:

![Beispielanwendung „MyShop“](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Es folgt eine kurze Erläuterung der Schritte:

1. Für Änderungen am Code erfolgt im Quellcoderepository (in diesem Fall GitHub) ein Commit. 
1. GitHub löst einen Build in Azure DevOps Services aus. 
1. Azure DevOps Services ruft die neueste Version des Quellcodes ab und erstellt alle Images, aus denen sich die Anwendung zusammensetzt. 
1. Azure DevOps Services überträgt jedes Image per Push in eine Docker-Registrierung, die mithilfe des Azure Container Registry-Diensts erstellt wurde. 
1. Azure DevOps Services löst ein neues Release aus. 
1. Das Release wendet einige Befehle über SSH auf den Masterknoten des Azure Container Service-Clusters an. 
1. Docker Swarm für den Cluster ruft die neueste Version der Images per Pull ab. 
1. Die neue Version der Anwendung wird mithilfe von Docker Compose bereitgestellt. 

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, müssen Sie die folgenden Aufgaben ausführen:

- [Erstellen eines Swarm-Clusters in Azure Container Service](container-service-deployment.md)
- [Herstellen einer Verbindung mit dem Swarm-Cluster in Azure Container Service](../container-service-connect.md)
- [Erstellen einer Azure-Containerregistrierung](../../container-registry/container-registry-get-started-portal.md)
- [Erstellen einer Azure DevOps Services-Organisation und eines Azure DevOps Services-Projekts](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Verzweigen des GitHub-Repositorys zu Ihrem GitHub-Konto](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Darüber hinaus benötigen Sie einen Computer mit Ubuntu (14.04 oder 16.04), auf dem Docker installiert ist. Dieser Computer wird in Azure DevOps Services in den Azure Pipelines-Prozessen verwendet. Eine Möglichkeit zum Erstellen dieses Computers ist die Verwendung des in [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/) verfügbaren Images. 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>Schritt 1: Konfigurieren Ihrer Azure DevOps Services-Organisation 

In diesem Abschnitt konfigurieren Sie Ihre Azure DevOps Services-Organisation.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Konfigurieren eines Linux-Build-Agents für Azure DevOps Services

Um Docker-Images zu erstellen und per Push aus einem Azure DevOps Services-Build in eine Azure Container Registry-Instanz zu übertragen, müssen Sie einen Linux-Agent registrieren. Sie haben die folgenden Installationsoptionen:

* [Bereitstellen eines Agents unter Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Verwenden von Docker zum Ausführen des Azure DevOps Services-Agents](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>Installieren der Azure DevOps Services-Erweiterung für die Docker-Integration

Microsoft bietet eine Azure DevOps Services-Erweiterung zum Arbeiten mit Docker in Azure Pipelines-Prozessen. Diese Erweiterung steht im [Azure DevOps Services Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker) zur Verfügung. Klicken Sie auf **Installieren**, um diese Erweiterung Ihrer Azure DevOps Services-Organisation hinzuzufügen:

![Installieren der Docker-Integration](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Sie werden aufgefordert, mit Ihren Anmeldeinformationen eine Verbindung mit der Azure DevOps Services-Organisation herzustellen. 

### <a name="connect-azure-devops-services-and-github"></a>Verbinden von Azure DevOps Services mit GitHub

Richten Sie eine Verbindung zwischen Ihrem Azure DevOps Services-Projekt und Ihrem GitHub-Konto ein.

1. Klicken Sie in Ihrem Azure DevOps Services-Projekt auf der Symbolleiste auf das Symbol **Einstellungen**, und wählen Sie **Dienste** aus.

    ![Azure DevOps Services: Externe Verbindung](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. Klicken Sie auf der linken Seite auf **Neuer Dienstendpunkt** > **GitHub**.

    ![Azure DevOps Services: GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Um Azure DevOps Services zur Verwendung mit Ihrem GitHub-Konto zu autorisieren, klicken Sie auf **Autorisieren** und befolgen die Anweisungen im eingeblendeten Fenster.

    ![Azure DevOps Services: Autorisieren von GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Verbinden von Azure DevOps Services mit der Azure Container Registry-Instanz und dem Azure Container Service-Cluster

Die letzten Schritte vor dem Starten mit der CI/CD-Pipeline bestehen aus dem Konfigurieren externer Verbindungen mit Ihrer Containerregistrierung und Ihrem Docker Swarm-Cluster in Azure. 

1. Fügen Sie in den Einstellungen für **Dienste** Ihres Azure DevOps Services-Projekts einen Dienstendpunkt des Typs **Docker-Registrierung** hinzu. 

1. Geben Sie im sich öffnenden Popupfenster die URL und Anmeldeinformationen Ihrer Azure-Containerregistrierung ein.

    ![Azure DevOps Services: Docker-Registrierung](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. Fügen Sie für den Docker Swarm-Cluster einen Endpunkt des Typs **SSH** hinzu. Geben Sie dann die SSH-Verbindungsinformationen Ihres Swarm-Clusters ein.

    ![Azure DevOps Services: SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Die Konfiguration ist damit abgeschlossen. Im nächsten Schritt erstellen Sie die CI-CD-Pipeline, mit der die Anwendung erstellt und im Docker Swarm-Cluster bereitgestellt wird. 

## <a name="step-2-create-the-build-pipeline"></a>Schritt 2: Erstellen der Buildpipeline

In diesem Schritt richten Sie eine Buildpipeline für Ihr Azure DevOps Services-Projekt ein und definieren den Buildworkflow für Ihre Containerimages.

### <a name="initial-pipeline-setup"></a>Anfängliche Einrichtung der Pipeline

1. Stellen Sie zum Erstellen einer Buildpipeline eine Verbindung mit Ihrem Azure DevOps Services-Projekt her, und klicken Sie auf **Build und Release**. 

1. Klicken Sie im Abschnitt **Builddefinitionen** auf **+ Neu**. Wählen Sie die Vorlage **Leer** aus.

    ![Azure DevOps: Neue Buildpipeline](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Konfigurieren Sie den neuen Build mit einer GitHub-Repositoryquelle, kreuzen Sie **Continuous Integration** an, und wählen Sie die Agent-Warteschlange aus, in der Sie Ihren Linux-Agent registriert haben. Klicken Sie zum Erstellen der Buildpipeline auf **Erstellen**.

    ![Azure DevOps Services: Erstellen der Buildpipeline](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. Öffnen Sie auf der Seite **Builddefinitionen** zuerst die Registerkarte **Repository**, und konfigurieren Sie dann den Build für die Verwendung der Verzweigung des „MyShop“-Projekts, die Sie zuvor zum Erfüllen der Voraussetzungen erstellt haben. Wählen Sie *acs-docs* als **Standardbranch** aus.

    ![Azure DevOps Services: Konfiguration des Buildrepositorys](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. Konfigurieren Sie auf der Registerkarte **Trigger** den Build, der nach jedem Commit ausgelöst werden soll. Wählen Sie **Continuous Integration** und **Batchänderungen** aus.

    ![Azure DevOps Services: Konfiguration des Buildtriggers](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Definieren des Buildworkflows
In den nächsten Schritten definieren Sie den Buildworkflow. Für die Anwendung *MyShop* müssen fünf Containerimages erstellt werden. Jedes Image wird mithilfe der Dockerfile erstellt, die sich im Projektordner befindet:

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

Sie müssen für jedes Image zwei Docker-Schritte hinzufügen: einen zum Erstellen des Image und einen zweiten zum Übertragen des Image per Push in die Azure-Containerregistrierung. 

1. Um dem Buildworkflow einen Schritt hinzuzufügen, klicken Sie auf **+ Buildschritt hinzufügen** und wählen dann **Docker** aus.

    ![Azure DevOps Services: Hinzufügen von Buildschritten](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Konfigurieren Sie für jedes Image einen Schritt, der den Befehl `docker build` verwendet.

    ![Azure DevOps Services: Docker-Build](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Wählen Sie für den Buildvorgang Ihre Azure-Containerregistrierung, die Aktion **Image erstellen** und die Dockerfile aus, die jedes Image definiert. Legen Sie **Buildkontext** auf das Dockerfile-Stammverzeichnis fest, und definieren Sie den **Imagenamen**. 
    
    Wie auf dem vorherigen Bildschirm gezeigt, setzen Sie den URI Ihrer Azure-Containerregistrierung an den Anfang des Imagenamens. (Sie können auch eine Buildvariable verwenden, um das Tag des Images zu parametrisieren, wie z.B. die Build-ID in diesem Beispiel.)

1. Konfigurieren Sie für jedes Image einen zweiten Schritt, der den Befehl `docker push` verwendet.

    ![Azure DevOps Services: Docker-Pushvorgang](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Wählen Sie für den Pushvorgang die Azure-Containerregistrierung und die Aktion zum **Übertragen eines Image per Push** aus. Geben Sie dann den **Imagenamen** ein, der im vorherigen Schritt erstellt wurde.

1. Nachdem Sie die Build- und Pushschritte für jedes der fünf Images erstellt haben, fügen Sie dem Buildworkflow zwei weitere Schritte hinzu.

    a. Eine Befehlszeilenaufgabe, die ein Bash-Skript verwendet, um *BuildNumber* in der Datei „docker-compose.yml“ durch die aktuelle Build-ID zu ersetzen. Die folgenden Abbildungen zeigen Details.

    ![Azure DevOps Services: Aktualisieren der Compose-Datei](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Eine Aufgabe, die die aktualisierte Compose-Datei als Buildartefakt ablegt, damit sie im Release verwendet werden kann. Die folgenden Abbildungen zeigen Details.

    ![Azure DevOps Services: Veröffentlichen der Compose-Datei](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Klicken Sie auf **Speichern**, und benennen Sie die Buildpipeline.

## <a name="step-3-create-the-release-pipeline"></a>Schritt 3: Erstellen der Releasepipeline

Mit Azure DevOps Services können Sie [Releases umgebungsübergreifend verwalten](https://www.visualstudio.com/team-services/release-management/). Sie können Continuous Deployment aktivieren, um sicherzustellen, dass Ihre Anwendung reibungslos in Ihren verschiedenen Umgebungen bereitgestellt wird (z.B. Entwicklungs-, Test-, Präproduktions- und Produktionsumgebung). Sie können eine neue Umgebung erstellen, die Ihren Azure Container Service Docker Swarm-Cluster darstellt.

![Azure DevOps Services: Freigabe in Azure Container Service](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Einrichtung des ersten Release

1. Um eine Releasepipeline zu erstellen, klicken Sie auf **Releases** >  **+ Freigeben**.

1. Um die Quelle des Artefakts zu konfigurieren, klicken Sie auf **Artefakte** > **Artefaktquelle verknüpfen**. Verknüpfen Sie diese neue Releasepipeline mit dem Build, den Sie im vorherigen Schritt definiert haben. Dadurch steht die Datei „docker-compose.yml“ im Releaseprozess zur Verfügung.

    ![Azure DevOps Services: Releaseartefakte](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. Um den Releasetrigger zu konfigurieren, klicken Sie auf **Trigger**, und wählen Sie **Continuous Deployment**. Legen Sie den Trigger für dieselbe Artefaktquelle fest. Diese Einstellung stellt sicher, dass ein neues Release gestartet wird, sobald der Build erfolgreich abgeschlossen wurde.

    ![Azure DevOps Services: Releasetrigger](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Definieren des Releaseworkflows

Der Releaseworkflow besteht aus zwei Aufgaben, die Sie hinzufügen.

1. Konfigurieren Sie eine Aufgabe, um eine Compose-Datei sicher in den Ordner *deploy* auf dem Docker Swarm-Masterknoten mithilfe der zuvor konfigurierten SSH-Verbindung zu kopieren. Die folgenden Abbildungen zeigen Details.

    ![Azure DevOps Services: Release mit SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Konfigurieren Sie eine zweite Aufgabe zum Ausführen eines Bash-Befehls zum Anwenden der Befehle `docker` und `docker-compose` auf den Masterknoten. Die folgenden Abbildungen zeigen Details.

    ![Azure DevOps Services: Release mit Bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    Der auf den Masterknoten angewendete Befehl verwendet die Docker CLI und Docker-Compose CLI zum Ausführen der folgenden Aufgaben:

   - Melden Sie sich bei der Azure-Containerregistrierung an (diese verwendet drei Buildvariablen, die auf der Registerkarte **Variablen** definiert sind).
   - Definieren Sie die Variable **DOCKER_HOST** für die Zusammenarbeit mit dem Swarm-Endpunkt (:2375).
   - Navigieren Sie zum Ordner *deploy*, der während der vorherigen sicheren Kopieraufgabe erstellt wurde und die Datei „docker-compose.yml“ enthält. 
   - Führen Sie `docker-compose`-Befehle aus, die die neuen Images per Pull abrufen, die Dienste beenden und entfernen sowie die Container erstellen.

     >[!IMPORTANT]
     > Lassen Sie, wie auf dem vorherigen Bildschirm gezeigt, **Fehler für STDERR** deaktiviert. Dies ist eine wichtige Einstellung, da `docker-compose` in der Standardfehlerausgabe mehrere Diagnosemeldungen ausgibt, z.B. zu beendeten und gelöschten Containern. Wenn Sie das Kontrollkästchen aktivieren, meldet Azure DevOps Services, dass im Verlauf des Release Fehler aufgetreten sind, obwohl alles wie gewünscht verlaufen ist.
     >
1. Speichern Sie diese neue Releasepipeline.


>[!NOTE]
>Diese Bereitstellung weist eine gewisse Ausfallzeit auf, da die alte Diensten beendet werden und der neue ausgeführt wird. Dies kann durch Ausführen einer Bereitstellung des Typs „Blaugrün“ vermieden werden.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Schritt 4: Testen der CI/CD-Pipeline

Nachdem Sie die Konfiguration abgeschlossen haben, ist es Zeit, diese neue CI/CD-Pipeline zu testen. Die einfachste Möglichkeit zum Testen ist das Aktualisieren des Quellcodes und Ausführen eines Commits für die Änderungen in Ihrem GitHub-Repository. Einige Sekunden nach der Übertragung des Codes per Push wird in Azure DevOps Services ein neuer Build ausgeführt. Nach erfolgreichem Abschluss wird ein neues Release ausgelöst, und die neue Version der Anwendung wird im Azure Container Service-Cluster bereitgestellt.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu CI/CD mit Azure DevOps Services finden Sie in der [Übersicht über den Azure DevOps Services-Build](https://www.visualstudio.com/docs/build/overview).
