---
title: Bereitstellen einer Containeranwendung mit CI/CD
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe von Visual Studio und Azure DevOps Continuous Integration und Continuous Deployment für eine Azure Service Fabric-Containeranwendung einrichten.
ms.topic: tutorial
ms.date: 08/29/2018
ms.custom: mvc
ms.openlocfilehash: bb0eb9226a99f139ff10a8da12a1e22017536c67
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018850"
---
# <a name="tutorial-deploy-a-container-application-with-cicd-to-a-service-fabric-cluster"></a>Tutorial: Bereitstellen einer Containeranwendung mit CI/CD in einem Service Fabric-Cluster

Dieses Tutorial ist der zweite Teil einer Serie und beschreibt, wie mithilfe von Visual Studio und Azure DevOps Continuous Integration und Continuous Deployment für eine Azure Service Fabric-Containeranwendung eingerichtet werden.  Es ist eine vorhandene Service Fabric-Anwendung erforderlich. Die im Artikel [Erstellen einer .NET-Anwendung in einem Windows-Container für Azure Service Fabric](service-fabric-host-app-in-a-container.md) erstellte Anwendung wird als Beispiel verwendet.

Im zweiten Teil der Reihe lernen Sie Folgendes:

> [!div class="checklist"]
> * Hinzufügen der Quellcodeverwaltung zum Projekt
> * Erstellen einer Builddefinition in Visual Studio Team Explorer
> * Erstellen einer Releasedefinition in Visual Studio Team Explorer
> * Automatisches Bereitstellen und Durchführen von Upgrades einer Anwendung

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, müssen Sie Folgendes tun:

* Sie besitzen einen Cluster in Azure oder [erstellen ihn mit diesem Tutorial](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Sie stellen eine Containeranwendung dafür bereit](service-fabric-host-app-in-a-container.md)

## <a name="prepare-a-publish-profile"></a>Erstellen eines Veröffentlichungsprofils

Nachdem Sie nun [eine Containeranwendung](service-fabric-host-app-in-a-container.md) bereitgestellt haben, können Sie nun eine Continuous Integration einrichten.  Erstellen Sie zunächst in der Anwendung ein Veröffentlichungsprofil für den Bereitstellungsprozess, der in Azure DevOps ausgeführt wird.  Das Veröffentlichungsprofil muss für den Cluster konfiguriert werden, den Sie zuvor erstellt haben.  Starten Sie Visual Studio, und öffnen Sie ein vorhandenes Service Fabric-Anwendungsprojekt.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Anwendung, und wählen Sie **Veröffentlichen** aus.

Wählen Sie ein Zielprofil im Anwendungsprojekt aus, das Sie für den Continuous Integration-Workflow verwenden möchten, z.B. „Cloud“.  Geben Sie den Clusterverbindungsendpunkt an.  Aktivieren Sie das Kontrollkästchen **Upgrade der Anwendung ausführen**, damit für jede Bereitstellung in Azure DevOps ein Upgrade der Anwendung erfolgt.  Klicken Sie auf den Link **Speichern**, um die Einstellungen im Veröffentlichungsprofil zu speichern, und klicken Sie dann auf **Abbrechen**, um das Dialogfeld zu schließen.

![Profil pushen][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>Freigeben der Visual Studio-Projektmappe in einem neuen Git-Repository von Azure DevOps

Teilen Sie Ihre Anwendungsquelldateien für ein Teamprojekt in Azure DevOps, damit Sie Builds erstellen können.

Erstellen Sie ein neues lokales Git-Repository für das Projekt, indem Sie auf der Statusleiste in der unteren rechten Ecke von Visual Studio **Zur Quellcodeverwaltung hinzufügen** -> **Git** auswählen.

Klicken Sie in der Ansicht **Push** in **Team Explorer** unter **Per Push in Azure DevOps übertragen** auf die Schaltfläche **Git-Repository veröffentlichen**.

![Screenshot: Team Explorer-Synchronisierungsfenster in Visual Studio. Unter „Per Push an Azure DevOps übertragen“ ist die Schaltfläche „Git-Repository veröffentlichen“ hervorgehoben.][push-git-repo]

Überprüfen Sie Ihre E-Mail-Adresse, und wählen Sie in der Dropdownliste **Konto** Ihre Organisation aus. Unter Umständen müssen Sie eine Organisation einrichten, falls Sie noch keine besitzen. Geben Sie den Repositorynamen ein, und wählen Sie **Repository veröffentlichen** aus.

![Screenshot: Fenster „Per Push an Azure DevOps übertragen“. Die Einstellungen für „E-Mail“, „Konto“ und „Repositoryname“ sowie die Schaltfläche „Repository veröffentlichen“ sind hervorgehoben.][publish-code]

Durch das Veröffentlichen des Repositorys wird in Ihrem Konto ein neues Teamprojekt mit dem gleichen Namen wie das lokale Repository erstellt. Um das Repository in einem vorhandenen Teamprojekt zu erstellen, klicken Sie neben **Repositoryname** auf **Erweitert**, und wählen Sie ein Teamprojekt aus. Sie können den Code im Web anzeigen, indem Sie **Im Web anzeigen** auswählen.

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Konfigurieren von Continuous Delivery mit Azure Pipelines

Eine Azure DevOps-Builddefinition beschreibt einen Workflow, der aus verschiedenen Buildschritten besteht, die nacheinander ausgeführt werden. Erstellen Sie eine Builddefinition, die ein Service Fabric-Anwendungspaket und andere Elemente erzeugt, um sie in einem Service Fabric-Cluster bereitzustellen. Erfahren Sie mehr über Azure DevOps-[Builddefinitionen](https://www.visualstudio.com/docs/build/define/create). 

Eine Azure DevOps-Releasedefinition beschreibt einen Workflow, der ein Anwendungspaket in einem Cluster bereitstellt. Bei gemeinsamer Verwendung führen die Builddefinition und Releasedefinition den gesamten Workflow aus, und zwar beginnend mit den Quelldateien und endend mit einer im Cluster ausgeführten Anwendung. Erfahren Sie mehr über Azure DevOps-[Releasedefinitionen](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition).

### <a name="create-a-build-definition"></a>Erstellen einer Builddefinition

Navigieren Sie zum Öffnen Ihres neuen Teamprojekts in einem Webbrowser zu https://dev.azure.com, und wählen Sie Ihre Organisation und dann das neue Projekt aus. 

Wählen Sie im linken Bereich die Option **Pipelines** aus, und klicken Sie auf **Neue Pipeline**.

>[!NOTE]
>Wird die Builddefinitionsvorlage nicht angezeigt, vergewissern Sie sich, dass das Feature **Neue Benutzeroberfläche zum Erstellen einer YAML-Pipeline** deaktiviert ist. Dieses Feature wird in Ihrem DevOps-Konto im Abschnitt **Featurevorschau** konfiguriert.

![Neue Pipeline][new-pipeline]

Wählen Sie **Azure Repos Git** als Quelle, den Namen Ihres Teamprojekts, Ihr Projektrepository und den Standardbranch **master** für manuelle und geplante Builds aus.  Klicken Sie anschließend auf **Weiter**.

Wählen Sie unter **Vorlage auswählen** die Vorlage **Azure Service Fabric-Anwendung mit Docker-Unterstützung** aus, und klicken Sie anschließend auf **Anwenden**.

![Auswählen der Buildvorlage][select-build-template]

Wählen Sie unter **Aufgaben****Hosted VS2017** als den **Agent-Pool** aus.

![Auswählen von Aufgaben][task-agent-pool]

Klicken Sie auf **Images markieren**.

Wählen Sie **Containerregistrierungstyp****Azure Container Registry**. Wählen Sie ein **Azure-Abonnement**, und klicken Sie dann auf **Autorisieren**. Wählen Sie eine **Azure Container Registry**.

![Auswählen von Images mit Docker-Tag][select-tag-images]

Klicken Sie auf **Übertragen von Images per Push**.

Wählen Sie **Containerregistrierungstyp****Azure Container Registry**. Wählen Sie ein **Azure-Abonnement**, und klicken Sie dann auf **Autorisieren**. Wählen Sie eine **Azure Container Registry**.

![Pushen von Docker-Images auswählen][select-push-images]

Aktivieren Sie auf der Registerkarte **Trigger** Continuous Integration, indem Sie die Option **Continuous Integration aktivieren** auswählen. Klicken Sie in **Branchfilter** auf **+ Hinzufügen**, und die **Branchspezifikation** wird standardmäßig auf **master** festgelegt.

Klicken Sie im Dialogfeld **Buildpipeline speichern und in Warteschlange stellen** auf **Speichern & in Warteschlange stellen**, um die Erstellung des Builds manuell zu starten.

![Auswählen von Triggern][save-and-queue]

Buildvorgänge werden auch bei Push- oder Eincheckvorgängen ausgelöst. Wechseln Sie zum Überprüfen des Buildstatus zur Registerkarte **Builds**.  Nachdem Sie überprüft haben, ob der Build erfolgreich ausgeführt wird, legen Sie eine Releasedefinition fest, mit der die Anwendung in einem Cluster bereitgestellt wird.

### <a name="create-a-release-definition"></a>Erstellen einer Releasedefinition

Wählen Sie im linken Bereich die Option **Pipelines** aus, und klicken Sie auf **Releases** und dann auf **+ Neue Pipeline**.  Wählen Sie in der Liste unter **Vorlage auswählen** die Vorlage **Azure Service Fabric-Bereitstellung** aus, und klicken Sie auf **Anwenden**.

![Auswählen der Releasevorlage][select-release-template]

Klicken Sie auf **Aufgaben****Umgebung1** und anschließend auf **+Neu**, um eine neue Clusterverbindung hinzuzufügen.

![Hinzufügen der Clusterverbindung][add-cluster-connection]

Wählen Sie in der Ansicht **Add new Service Fabric Connection** (Neue Service Fabric-Verbindung hinzufügen) die Authentifizierung **Zertifikatbasiert** oder **Azure Active Directory** aus.  Nennen Sie die Verbindung „mysftestcluster“, und geben Sie als Clusterendpunkt entweder „tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000“ (oder den Endpunkt des Zielclusters Ihrer Bereitstellung) an.

Fügen Sie bei Verwendung der zertifikatbasierten Authentifizierung den **Zertifikatfingerabdruck des Servers** für das Serverzertifikat hinzu, das bei der Erstellung des Clusters verwendet wurde.  Fügen Sie unter **Clientzertifikat** die Base64-Codierung der Clientzertifikatdatei hinzu. Die Vorgehensweise zum Ermitteln der Base64-codierten Darstellung des Zertifikats wird in der kontextbezogenen Hilfe zu diesem Feld beschrieben. Fügen Sie auch das **Kennwort** für das Zertifikat hinzu.  Sollten Sie über kein separates Clientzertifikat verfügen, können Sie auch das Cluster- oder Serverzertifikat verwenden.

Fügen Sie bei Verwendung von Azure Active Directory-Anmeldeinformationen den **Zertifikatfingerabdruck des Servers** für das Serverzertifikat hinzu, das bei der Erstellung des Clusters verwendet wurde, und geben Sie in den Feldern **Benutzername** und **Kennwort** die Anmeldeinformationen an, die beim Herstellen der Clusterverbindung verwendet werden sollen.

Klicken Sie auf **Hinzufügen**, um die Clusterverbindung zu speichern.



Klicken Sie unter Agent-Phase auf **Service Fabric-Anwendung bereitstellen**.
Klicken Sie auf **Docker-Einstellungen** und dann auf **Docker-Einstellungen konfigurieren**. Unter **Quelle der Registrierungsanmeldeinformationen** wählen Sie die Option **Azure Resource Manager-Dienstverbindung**. Wählen Sie dann Ihr **Azure-Abonnement**.

![Releasepipeline-Agent][release-pipeline-agent]

Fügen Sie als Nächstes der Pipeline ein Buildartefakt hinzu, damit die Releasedefinition die Ausgabe des Builds findet. Klicken Sie auf **Pipeline** und anschließend auf **Artefakte**->**+Hinzufügen**.  Wählen Sie unter **Quelle (Builddefinition)** die zuvor erstellte Builddefinition aus.  Klicken Sie auf **Hinzufügen**, um das Buildartefakt zu speichern.

![Hinzufügen des Artefakts][add-artifact]

Aktivieren Sie einen Continuous Deployment-Trigger, damit nach Abschluss des Buildvorgangs automatisch ein Release erstellt wird. Klicken Sie im Artefakt auf das Blitzsymbol, aktivieren Sie den Trigger, und klicken Sie anschließend auf **Speichern**, um die Releasedefinition zu speichern.

![Aktivieren des Triggers][enable-trigger]

Wählen Sie **+ Release** -> **Release erstellen** -> **Erstellen** aus, um manuell ein Release zu erstellen. Sie können den Releasestatus auf der Registerkarte **Releases** überwachen.

Überprüfen Sie, ob die Bereitstellung erfolgreich war und die Anwendung im Cluster ausgeführt wird.  Öffnen Sie einen Webbrowser, und navigieren Sie zu `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`.  Beachten Sie, dass die Anwendungsversion in diesem Beispiel „1.0.0.20170616.3“ lautet.

## <a name="commit-and-push-changes-trigger-a-release"></a>Committen und Pushen von Änderungen, Auslösen eines Release

Um die Funktionsfähigkeit Continuous Integration-Pipeline zu überprüfen, checken Sie einige Codeänderungen bei Azure DevOps ein.

Während Sie den Code schreiben, werden Ihre Änderungen automatisch von Visual Studio nachverfolgt. Führen Sie den Commit der Änderungen in das lokale Git-Repository aus, indem Sie auf der Statusleiste unten rechts das Symbol „Ausstehende Änderungen“ (![Für das Symbol „Ausstehende Änderungen“ werden ein Stift und eine Zahl angezeigt.][pending]) auswählen.

Fügen Sie in der Ansicht **Änderungen** in Team Explorer eine Nachricht hinzu, die die Aktualisierung beschreibt, und führen Sie den Commit der Änderungen aus.

![Commit für alle][changes]

Wählen Sie das Statusleistensymbol für nicht veröffentlichte Änderungen (![nicht unveröffentlichte Änderungen][unpublished-changes]) oder die Synchronisierungsansicht in Team Explorer aus. Wählen Sie **Push** aus, um den Code in Azure DevOps zu aktualisieren.

![Änderungen pushen][push]

Durch das Pushen der Änderungen zu Azure DevOps wird automatisch ein Build ausgelöst.  Nach erfolgreichem Abschluss der Builddefinition wird automatisch ein Release erstellt, und es wird ein Upgrade der Anwendung im Cluster gestartet.

Um den Buildstatus zu überprüfen, wechseln Sie in Visual Studio in **Team Explorer** zur Registerkarte **Builds**.  Nachdem Sie überprüft haben, ob der Build erfolgreich ausgeführt wird, legen Sie eine Releasedefinition fest, mit der die Anwendung in einem Cluster bereitgestellt wird.

Überprüfen Sie, ob die Bereitstellung erfolgreich war und die Anwendung im Cluster ausgeführt wird.  Öffnen Sie einen Webbrowser, und navigieren Sie zu `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`.  Beachten Sie, dass die Anwendungsversion in diesem Beispiel 1.0.0.20170815.3 lautet.

![Screenshot: Voting-App in Service Fabric Explorer. Auf der Registerkarte „Zusammenfassung“ ist die App-Version 1.0.0.20170815.3 hervorgehoben.][sfx1]

## <a name="update-the-application"></a>Aktualisieren der Anwendung

Nehmen Sie in der Anwendung Codeänderungen vor.  Speichern und committen Sie die Änderungen, indem Sie die vorherigen Schritte ausführen.

Sobald das Upgrade der Anwendung gestartet wird, können Sie in Service Fabric Explorer den Upgradestatus beobachten:

![Screenshot: Voting-App in Service Fabric Explorer. Eine Meldung vom Typ „Das Upgrade wird ausgeführt...“ ist hervorgehoben, und der Status lautet „Upgrade wird durchgeführt“.][sfx2]

Das Anwendungsupgrade kann mehrere Minuten dauern. Wenn das Upgrade abgeschlossen ist, wird die nächste Version der Anwendung ausgeführt.  In diesem Beispiel: 1.0.0.20170815.4

![Screenshot: Voting-App in Service Fabric Explorer. Auf der Registerkarte „Zusammenfassung“ ist die aktualisierte App-Version 1.0.0.20170815.4 hervorgehoben.][sfx3]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Hinzufügen der Quellcodeverwaltung zum Projekt
> * Erstellen einer Builddefinition
> * Erstellen einer Releasedefinition
> * Automatisches Bereitstellen und Durchführen von Upgrades einer Anwendung

Im nächsten Teil des Tutorials erfahren Sie, wie Sie die [Überwachung für Ihren Container](service-fabric-tutorial-monitoring-wincontainers.md) einrichten.

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewPipeline.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectBuildTemplate.png
[task-agent-pool]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/TaskAgentPool.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SaveAndQueue.png
[select-tag-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerTagImages.png
[select-push-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerPushImages.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddClusterConnection.png
[release-pipeline-agent]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/ReleasePipelineAgent.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpointDialog.png
