---
title: Bereitstellungscenter für Azure Kubernetes
description: Das Bereitstellungscenter in Azure DevOps vereinfacht die Einrichtung einer stabilen Azure DevOps-Pipeline für Ihre Anwendung.
ms.author: puagarw
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
ms.openlocfilehash: 15b0413eabcfae7e3a4b28243caf2a708260ccae
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932216"
---
# <a name="deployment-center-for-azure-kubernetes"></a>Bereitstellungscenter für Azure Kubernetes

Das Bereitstellungscenter in Azure DevOps vereinfacht die Einrichtung einer stabilen Azure DevOps-Pipeline für Ihre Anwendung. Standardmäßig wird vom Bereitstellungscenter eine Azure DevOps-Pipeline zum Bereitstellen Ihrer Anwendungsupdates im Kubernetes-Cluster konfiguriert. Sie können die standardmäßig konfigurierte Azure DevOps-Pipeline erweitern und zusätzliche Funktionen hinzufügen: Option zum Einholen der Genehmigung vor der Bereitstellung, Bereitstellung zusätzlicher Azure-Ressourcen, Ausführung von Skripts, Upgrades Ihrer Anwendung oder sogar die Ausführung zusätzlicher Validierungstests.

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> * Konfigurieren einer Azure DevOps-Pipeline zum Bereitstellen Ihrer Anwendungsupdates im Kubernetes-Cluster
> * Überprüfen der CI-Pipeline (Continuous Integration)
> * Überprüfen der CD-Pipeline (Continuous Delivery)
> * Bereinigen der Ressourcen

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Über [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) erhalten Sie ein kostenloses Abonnement.

* Einen AKS-Cluster (Azure Kubernetes Service)

## <a name="create-an-aks-cluster"></a>Erstellen eines AKS-Clusters

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Wählen Sie im Azure-Portal rechts auf der Menüleiste die Option [Cloud Shell](../cloud-shell/overview.md) aus.

1. Führen Sie die folgenden Befehle aus, um den AKS-Cluster zu erstellen:

    ```azurecli
    # Create a resource group in the South India location:

    az group create --name azooaks --location southindia

    # Create a cluster named azookubectl with one node.

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-a-kubernetes-cluster"></a>Bereitstellen von Anwendungsupdates in einem Kubernetes-Cluster

1. Navigieren Sie zu der Ressourcengruppe, die Sie im vorherigen Abschnitt erstellt haben.

1. Wählen Sie den AKS-Cluster und dann auf dem linken Blatt **Bereitstellungscenter (Vorschau)** aus. Wählen Sie **Erste Schritte** aus.

   ![Screenshot: Azure-Portal mit einem Pfeil, der auf das Bereitstellungscenter zeigt](media/deployment-center-launcher/settings.png)

1. Wählen Sie den Speicherort des Codes und dann **Weiter** aus. Wählen Sie anschließend eins der derzeit unterstützten Repository aus: **[Azure Repos](/azure/devops/repos/index)** oder **GitHub**.

    Bei Azure Repos handelt es sich um einen Satz von Tools für die Versionskontrolle, mit denen Sie Ihren Code verwalten können. Unabhängig von der Größe Ihres Softwareprojekts empfiehlt es sich, die Versionskontrolle so früh wie möglich zu verwenden.

    - **Azure Repos:** Wählen Sie ein Repository aus Ihrem vorhandenen Projekt und Ihrer Organisation aus.

        ![Azure Repos](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub:** Autorisieren Sie das Repository für Ihr GitHub-Konto, und wählen Sie es aus.

        ![Animation: Prozess in GitHub, in dessen Rahmen GitHub als Quelle und anschließend Ihr Repository ausgewählt wird](media/deployment-center-launcher/github.gif)


1. Das Bereitstellungscenter analysiert das Repository und erkennt das Dockerfile. Zur Aktualisierung des Dockerfile können Sie die ermittelte Portnummer bearbeiten.

    ![Anwendungseinstellungen](media/deployment-center-launcher/application-settings.png)

    Ist im Repository kein Dockerfile enthalten, wird vom System eine Meldung mit dem Hinweis angezeigt, dass eins committet werden muss.

    ![Screenshot: Bereitstellungscenter mit der Meldung, dass das Dockerfile nicht im Repository gefunden wurde](media/deployment-center-launcher/dockerfile.png)

1. Wählen Sie eine vorhandene Containerregistrierung aus, oder erstellen Sie eine, und wählen Sie dann **Fertig stellen** aus. Die Pipeline wird automatisch erstellt, und ein Build wird in [Azure Pipelines](/azure/devops/pipelines/index) in die Warteschlange eingereiht.

    Azure Pipelines ist ein Clouddienst, mit dem Sie Ihr Codeprojekt automatisch erstellen und testen sowie es anderen Benutzern zur Verfügung stellen können. Azure Pipelines kombiniert Continuous Integration und Continuous Delivery, um den Code kontinuierlich und konsistent zu testen und zu erstellen und auf einem beliebigen Ziel bereitzustellen.

    ![Containerregistrierung](media/deployment-center-launcher/container-registry.png)

1. Wählen Sie den Link aus, um die aktuelle Pipeline anzuzeigen.

1. Nach der Bereitstellung werden die Erfolgsprotokolle angezeigt.

    ![Screenshot: Bereitstellungscenter, in dem „Release-1“ mit einem grünen Häkchen markiert ist](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>Überprüfen der CI-Pipeline

Mit dem Bereitstellungscenter wird automatisch die CI/CD-Pipeline in Ihrer Azure DevOps-Organisation konfiguriert. Sie können die Pipeline untersuchen und anpassen.

1. Navigieren Sie zum Dashboard des Bereitstellungscenters.  

1. Wählen Sie in der Liste der Erfolgsprotokolle die Buildnummer aus, um die Buildpipeline für Ihr Projekt anzuzeigen.

1. Wählen Sie dann in der oberen rechten Ecke die Auslassungspunkte (...) aus. In einem Menü werden verschiedene Optionen angezeigt, etwa zum Einreihen eines neuen Builds in die Warteschlange, zum Beibehalten eines Builds und zum Bearbeiten der Buildpipeline. Wählen Sie **Pipeline bearbeiten** aus. 

1. In diesem Bereich können Sie sich die verschiedenen Aufgaben für Ihre Buildpipeline ansehen. Der Build führt verschiedene Aufgaben aus. Beispielsweise erfasst er Quellen aus dem Git-Repository, erstellt ein Image, überträgt ein Image per Push an die Containerregistrierung und veröffentlicht Ausgaben, die für Bereitstellungen verwendet werden.

1. Wählen Sie oben in der Pipeline den Namen der Buildpipeline aus.

1. Ersetzen Sie den Namen Ihrer Buildpipeline durch einen aussagekräftigeren Namen, und wählen Sie **Speichern und in Warteschlange einreihen** und dann **Speichern** aus.

1. Wählen Sie unter der Buildpipeline **Verlauf** aus. In diesem Bereich wird ein Überwachungsprotokoll der aktuellen Buildänderungen angezeigt. An der Buildpipeline vorgenommene Änderungen werden von Azure DevOps überwacht, sodass Sie verschiedene Versionen vergleichen können.

1. Wählen Sie **Trigger** aus. Sie können Branches in den CI-Prozess einbeziehen oder davon ausschließen.

1. Wählen Sie **Aufbewahrung** aus. Abhängig vom Szenario können Sie Richtlinien zum Aufbewahren oder Entfernen einer bestimmten Anzahl von Builds festlegen.

## <a name="examine-the-cd-pipeline"></a>Überprüfen der CD-Pipeline

Mit dem Bereitstellungscenter wird die Beziehung zwischen Ihrer Azure DevOps-Organisation und Ihrem Azure-Abonnement automatisch erstellt und konfiguriert. Die entsprechenden Schritte umfassen die Einrichtung einer Azure-Dienstverbindung zur Authentifizierung Ihres Azure-Abonnements mit Azure DevOps. Der automatisierte Prozess erstellt außerdem eine Releasepipeline, die Continuous Delivery für Azure bereitstellt.

1. Wählen Sie **Pipelines** und anschließend **Releases** aus.

1. Wählen Sie zum Bearbeiten der Releasepipeline **Bearbeiten** aus.

1. Wählen Sie in der Liste **Artefakte** die Option **Ablegen** aus. Die in den vorherigen Schritten untersuchte Pipeline erzeugt die für das Artefakt verwendete Ausgabe. 

1. Wählen Sie rechts neben der Option **Ablegen** die Option **Continuous Deployment-Trigger** aus. Diese Releasepipeline enthält einen aktivierten CD-Trigger. Jedes Mal, wenn ein neues Buildartefakt verfügbar ist, wird von diesem CD-Trigger eine Bereitstellung ausgeführt. Sie können den Trigger auch deaktivieren, sodass Ihre Bereitstellungen manuell ausgeführt werden müssen.

1. Wählen Sie **Aufgaben** aus, um alle Aufgaben für Ihre Pipeline anzuzeigen. Das Release legt die Tiller-Umgebung fest, konfiguriert den Parameter `imagePullSecrets`, installiert Helm-Tools und stellt die Helm-Diagramme im Kubernetes-Cluster bereit.

1. Wählen Sie zum Anzeigen des Releaseverlaufs **Releases anzeigen** aus.

1. Wählen Sie zum Anzeigen der Zusammenfassung **Release** aus. Wählen Sie eine beliebige Phase aus, um mehrere Menüs anzuzeigen, etwa eine Releasezusammenfassung, zugeordnete Arbeitselemente und Tests. 

1. Wählen Sie **Commits** aus. In dieser Ansicht werden Codecommits für diese Bereitstellung angezeigt. Vergleichen Sie Releases, um die Commitunterschiede zwischen den einzelnen Bereitstellungen anzuzeigen.

1. Wählen Sie **Protokolle** aus. Die Protokolle enthalten nützliche Bereitstellungsinformationen, die Sie während und nach Bereitstellungen anzeigen können.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können die erstellten zugehörigen Ressourcen löschen, wenn Sie sie nicht mehr benötigen. Verwenden Sie dazu die Funktion „Löschen“ auf dem DevOps Projects-Dashboard.

## <a name="next-steps"></a>Nächste Schritte

Diese Build- und Releasepipelines können Sie den Anforderungen Ihres Teams anpassen. Alternativ können Sie dieses CI/CD-Modell auch als Vorlage für Ihre anderen Pipelines verwenden.
