---
title: Bereitstellen einer Service Fabric-App mit Continuous Integration und Azure Pipelines in Azure | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie Continuous Integration und Continuous Deployment für eine Service Fabric-Anwendung mithilfe von Azure Pipelines einrichten.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/02/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: ba0975486039546d5be0f704fb617beb1a9e0908
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306888"
---
# <a name="tutorial-deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Tutorial: Bereitstellen einer Anwendung mit CI/CD in einem Service Fabric-Cluster

Dieses Tutorial ist der vierte Teil einer Reihe und beschreibt die Einrichtung von Continuous Integration und Continuous Deployment für eine Azure Service Fabric-Anwendung mithilfe von Azure Pipelines.  Es ist eine vorhandene Service Fabric-Anwendung erforderlich. Die im Artikel [Erstellen einer .NET-Anwendung](service-fabric-tutorial-create-dotnet-app.md) erstellte Anwendung wird als Beispiel verwendet.

Im dritten Teil der Serie lernen Sie Folgendes:

> [!div class="checklist"]
> * Hinzufügen der Quellcodeverwaltung zum Projekt
> * Erstellen einer Buildpipeline in Azure-Pipelines
> * Erstellen einer Releasepipeline in Azure-Pipelines
> * Automatisches Bereitstellen und Durchführen von Upgrades einer Anwendung

In dieser Tutorialserie lernen Sie Folgendes:
> [!div class="checklist"]
> * [Erstellen einer .NET Service Fabric-Anwendung](service-fabric-tutorial-create-dotnet-app.md)
> * [Bereitstellen der Anwendung in einem Remotecluster](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Hinzufügen eines HTTPS-Endpunkts zu einem ASP.NET Core-Front-End-Dienst](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * Konfigurieren von CI/CD mit Azure Pipelines
> * [Einrichten der Überwachung und Diagnose für die Anwendung](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, müssen Sie Folgendes tun:

* Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Installieren Sie Visual Studio 2019](https://www.visualstudio.com/) und die Workloads **Azure-Entwicklung** und **ASP.NET und Webentwicklung**.
* [Installieren Sie das Service Fabric SDK](service-fabric-get-started.md).
* Erstellen Sie einen Windows Service Fabric-Cluster in Azure, z.B. durch das [Ausführen der Schritte in diesem Tutorial](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
* Erstellen Sie eine [Azure DevOps-Organisation](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student). Dadurch können Sie ein Projekt in Azure DevOps erstellen und Azure Pipelines verwenden.

## <a name="download-the-voting-sample-application"></a>Laden Sie die Beispielanwendung „Voting“ herunter.

Falls Sie die Beispielanwendung „Voting“ aus [Teil 1 dieser Tutorialreihe](service-fabric-tutorial-create-dotnet-app.md) nicht erstellt haben, können Sie sie herunterladen. Führen Sie in einem Befehlsfenster den folgenden Befehl aus, um das Beispiel-App-Repository auf Ihren lokalen Computer zu klonen.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>Erstellen eines Veröffentlichungsprofils

Nachdem Sie [eine Anwendung erstellt](service-fabric-tutorial-create-dotnet-app.md) und [die Anwendung in Azure bereitgestellt](service-fabric-tutorial-deploy-app-to-party-cluster.md) haben, können Sie Continuous Integration einrichten.  Erstellen Sie zunächst in der Anwendung ein Veröffentlichungsprofil für den Bereitstellungsprozess, der in Azure Pipelines ausgeführt wird.  Das Veröffentlichungsprofil muss für den Cluster konfiguriert werden, den Sie zuvor erstellt haben.  Starten Sie Visual Studio, und öffnen Sie ein vorhandenes Service Fabric-Anwendungsprojekt.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Anwendung, und wählen Sie **Veröffentlichen** aus.

Wählen Sie ein Zielprofil im Anwendungsprojekt aus, das Sie für den Continuous Integration-Workflow verwenden möchten, z.B. „Cloud“.  Geben Sie den Clusterverbindungsendpunkt an.  Aktivieren Sie das Kontrollkästchen **Upgrade der Anwendung ausführen**, damit für jede Bereitstellung in Azure DevOps ein Upgrade der Anwendung erfolgt.  Klicken Sie auf den Link **Speichern**, um die Einstellungen im Veröffentlichungsprofil zu speichern, und klicken Sie dann auf **Abbrechen**, um das Dialogfeld zu schließen.

![Profil pushen][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>Freigeben der Visual Studio-Projektmappe in einem neuen Git-Repository von Azure DevOps

Teilen Sie Ihre Anwendungsquelldateien für ein Projekt in Azure DevOps, damit Sie Builds erstellen können.

Erstellen Sie ein neues lokales Git-Repository für das Projekt, indem Sie auf der Statusleiste in der unteren rechten Ecke von Visual Studio **Zur Quellcodeverwaltung hinzufügen** -> **Git** auswählen.

Klicken Sie in der Ansicht **Push** in **Team Explorer** unter **Per Push in Azure DevOps übertragen** auf die Schaltfläche **Git-Repository veröffentlichen**.

![Git-Repository pushen][push-git-repo]

Überprüfen Sie Ihre E-Mail-Adresse, und wählen Sie in der Dropdownliste **Azure DevOps-Domäne** Ihr Konto aus. Geben Sie den Repositorynamen ein, und wählen Sie **Repository veröffentlichen** aus.

![Git-Repository pushen][publish-code]

Durch das Veröffentlichen des Repositorys wird in Ihrem Konto ein neues Projekt mit dem gleichen Namen wie das lokale Repository erstellt. Um das Repository in einem vorhandenen Projekt zu erstellen, klicken Sie neben **Repositoryname** auf **Erweitert**, und wählen Sie ein Projekt aus. Sie können den Code im Web anzeigen, indem Sie **Im Web anzeigen** auswählen.

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Konfigurieren von Continuous Delivery mit Azure Pipelines

Eine Azure Pipelines-Buildpipeline beschreibt einen Workflow, der aus verschiedenen Buildschritten besteht, die nacheinander ausgeführt werden. Erstellen Sie eine Buildpipeline, die ein Service Fabric-Anwendungspaket und andere Elemente erzeugt, um sie in einem Service Fabric-Cluster bereitzustellen. Erfahren Sie mehr über [Azure Pipelines-Buildpipelines](https://www.visualstudio.com/docs/build/define/create). 

Eine Azure Pipelines-Releasepipeline beschreibt einen Workflow, der ein Anwendungspaket in einem Cluster bereitstellt. Bei gemeinsamer Verwendung führen die Buildpipeline und Releasepipeline den gesamten Workflow aus, und zwar beginnend mit den Quelldateien und endend mit einer im Cluster ausgeführten Anwendung. Weitere Informationen zu Azure Pipelines-Releasepipelines finden Sie [hier](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition).

### <a name="create-a-build-pipeline"></a>Erstellen einer Buildpipeline

Navigieren Sie in einem Webbrowser zu Ihrem neuen Projekt: [https://&lt;IhrKonto&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting).

Wählen Sie die Registerkarte **Pipelines** und dann **Builds** aus, und klicken Sie auf **Neue Pipeline**.

![Neue Pipeline][new-pipeline]

Wählen Sie **Azure Repos Git** als Quelle, das Teamprojekt **Voting**, das Repository **Voting** und den Standardbranch **master** für manuelle und geplante Builds aus.  Klicken Sie anschließend auf **Weiter**.

![Auswählen des Repositorys][select-repo]

Wählen Sie unter **Vorlage auswählen** die Vorlage **Azure Service Fabric-Anwendung** aus, und klicken Sie anschließend auf **Anwenden**.

![Auswählen der Buildvorlage][select-build-template]

Geben Sie unter **Aufgaben** für **Agentpool** die Zeichenfolge „Hosted VS2017“ ein.

![Auswählen von Aufgaben][save-and-queue]

Aktivieren Sie unter **Trigger** Continuous Integration, indem Sie die Option **Continuous Integration aktivieren** auswählen. Unter **Branchfilter** ist **Branchspezifikation** standardmäßig auf **master** festgelegt. Klicken Sie auf **Speichern und in Warteschlange einreihen**, um manuell einen Buildvorgang zu starten.

![Auswählen von Triggern][save-and-queue2]

Buildvorgänge werden auch bei Push- oder Eincheckvorgängen ausgelöst. Wechseln Sie zum Überprüfen des Buildstatus zur Registerkarte **Builds**.  Nachdem Sie überprüft haben, ob der Build erfolgreich ausgeführt wird, müssen Sie eine Releasepipeline festlegen, mit der die Anwendung in einem Cluster bereitgestellt wird.

### <a name="create-a-release-pipeline"></a>Erstellen einer Releasepipeline

Wählen Sie die Registerkarte **Pipelines** und dann **Releases** aus, und klicken Sie auf **+ Neue Pipeline**.  Wählen Sie in der Liste unter **Vorlage auswählen** die Vorlage **Azure Service Fabric-Bereitstellung** aus, und klicken Sie auf **Anwenden**.

![Auswählen der Releasevorlage][select-release-template]

Klicken Sie auf **Aufgaben**->**Umgebung 1** und anschließend auf **+Neu**, um eine neue Clusterverbindung hinzuzufügen.

![Hinzufügen der Clusterverbindung][add-cluster-connection]

Wählen Sie in der Ansicht **Add new Service Fabric Connection** (Neue Service Fabric-Verbindung hinzufügen) die Authentifizierung **Zertifikatbasiert** oder **Azure Active Directory** aus.  Nennen Sie die Verbindung „mysftestcluster“, und geben Sie als Clusterendpunkt entweder „tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000“ (oder den Endpunkt des Zielclusters Ihrer Bereitstellung) an.

Fügen Sie bei Verwendung der zertifikatbasierten Authentifizierung den **Zertifikatfingerabdruck des Servers** für das Serverzertifikat hinzu, das bei der Erstellung des Clusters verwendet wurde.  Fügen Sie unter **Clientzertifikat** die Base64-Codierung der Clientzertifikatdatei hinzu. Die Vorgehensweise zum Ermitteln der Base64-codierten Darstellung des Zertifikats wird in der kontextbezogenen Hilfe zu diesem Feld beschrieben. Fügen Sie auch das **Kennwort** für das Zertifikat hinzu.  Sollten Sie über kein separates Clientzertifikat verfügen, können Sie auch das Cluster- oder Serverzertifikat verwenden.

Fügen Sie bei Verwendung von Azure Active Directory-Anmeldeinformationen den **Zertifikatfingerabdruck des Servers** für das Serverzertifikat hinzu, das bei der Erstellung des Clusters verwendet wurde, und geben Sie in den Feldern **Benutzername** und **Kennwort** die Anmeldeinformationen an, die beim Herstellen der Clusterverbindung verwendet werden sollen.

Klicken Sie auf **Hinzufügen**, um die Clusterverbindung zu speichern.

Fügen Sie als Nächstes der Pipeline ein Buildartefakt hinzu, damit die Releasepipeline die Ausgabe des Builds findet. Klicken Sie auf **Pipeline** und anschließend auf **Artefakte**-> **+Hinzufügen**.  Wählen Sie unter **Quelle (Builddefinition)** die zuvor erstellte Buildpipeline aus.  Klicken Sie auf **Hinzufügen**, um das Buildartefakt zu speichern.

![Hinzufügen des Artefakts][add-artifact]

Aktivieren Sie einen Continuous Deployment-Trigger, damit nach Abschluss des Buildvorgangs automatisch ein Release erstellt wird. Klicken Sie im Artefakt auf das Blitzsymbol, aktivieren Sie den Trigger, und klicken Sie anschließend auf **Speichern**, um die Releasepipeline zu speichern.

![Aktivieren des Triggers][enable-trigger]

Wählen Sie **+ Release** -> **Release erstellen** -> **Erstellen** aus, um manuell ein Release zu erstellen. Sie können den Releasestatus auf der Registerkarte **Releases** überwachen.

Überprüfen Sie, ob die Bereitstellung erfolgreich war und die Anwendung im Cluster ausgeführt wird.  Öffnen Sie einen Webbrowser, und navigieren Sie zu [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Beachten Sie, dass die Anwendungsversion in diesem Beispiel „1.0.0.20170616.3“ lautet.

## <a name="commit-and-push-changes-trigger-a-release"></a>Committen und Pushen von Änderungen, Auslösen eines Release

Um die Funktionsfähigkeit Continuous Integration-Pipeline zu überprüfen, checken Sie einige Codeänderungen bei Azure DevOps ein.

Während Sie den Code schreiben, werden Ihre Änderungen automatisch von Visual Studio nachverfolgt. Führen Sie den Commit der Änderungen in das lokale Git-Repository aus, indem Sie auf der Statusleiste unten rechts das Symbol „Ausstehende Änderungen“ (![Ausstehend][pending]) auswählen.

Fügen Sie in der Ansicht **Änderungen** in Team Explorer eine Nachricht hinzu, die die Aktualisierung beschreibt, und führen Sie den Commit der Änderungen aus.

![Commit für alle][changes]

Wählen Sie das Statusleistensymbol für nicht veröffentlichte Änderungen (![nicht unveröffentlichte Änderungen][unpublished-changes]) oder die Synchronisierungsansicht in Team Explorer aus. Wählen Sie **Push** aus, um den Code in Azure Pipelines zu aktualisieren.

![Änderungen pushen][push]

Durch das Pushen der Änderungen an Azure Pipelines wird automatisch ein Buildvorgang ausgelöst.  Wenn die Buildpipeline erfolgreich abgeschlossen ist, wird automatisch ein Release erstellt, das mit dem Upgrade der Anwendung auf dem Cluster beginnt.

Um den Buildstatus zu überprüfen, wechseln Sie in Visual Studio in **Team Explorer** zur Registerkarte **Builds**.  Nachdem Sie überprüft haben, ob der Build erfolgreich ausgeführt wird, müssen Sie eine Releasepipeline festlegen, mit der die Anwendung in einem Cluster bereitgestellt wird.

Überprüfen Sie, ob die Bereitstellung erfolgreich war und die Anwendung im Cluster ausgeführt wird.  Öffnen Sie einen Webbrowser, und navigieren Sie zu [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Beachten Sie, dass die Anwendungsversion in diesem Beispiel 1.0.0.20170815.3 lautet.

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>Aktualisieren der Anwendung

Nehmen Sie in der Anwendung Codeänderungen vor.  Speichern und committen Sie die Änderungen, indem Sie die vorherigen Schritte ausführen.

Sobald das Upgrade der Anwendung gestartet wird, können Sie in Service Fabric Explorer den Upgradestatus beobachten:

![Service Fabric Explorer][sfx2]

Das Anwendungsupgrade kann mehrere Minuten dauern. Wenn das Upgrade abgeschlossen ist, wird die nächste Version der Anwendung ausgeführt.  In diesem Beispiel: 1.0.0.20170815.4

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Hinzufügen der Quellcodeverwaltung zum Projekt
> * Erstellen einer Buildpipeline
> * Erstellen einer Releasepipeline
> * Automatisches Bereitstellen und Durchführen von Upgrades einer Anwendung

Fahren Sie mit dem nächsten Tutorial fort:
> [!div class="nextstepaction"]
> [Einrichten der Überwachung und Diagnose für die Anwendung](service-fabric-tutorial-monitoring-aspnet.md)

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewPipeline.png
[select-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectRepo.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-AzureDevOpsServices]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
