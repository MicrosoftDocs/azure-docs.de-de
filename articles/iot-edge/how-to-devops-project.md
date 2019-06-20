---
title: CI/CD-Pipeline mit Azure DevOps Projects – Azure IoT Edge | Microsoft-Dokumentation
description: Azure DevOps Projects erleichtert die ersten Schritte mit Azure. Damit können Sie eine Azure IoT Edge-App Ihrer Wahl in einigen wenigen Schritten starten.
author: shizn
manager: ''
ms.author: xshi
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 02977032c5975de4098600ddbebccfcbb9b0fafd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60595496"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects-preview"></a>Erstellen einer CI/CD-Pipeline für IoT Edge mit Azure DevOps Projects (Vorschauversion)

Konfigurieren Sie mit DevOps Projects Continuous Integration (CI) und Continuous Delivery (CD) für Ihre IoT Edge-Anwendung. DevOps Projects erleichtert die Erstkonfiguration einer Build- und Releasepipeline in Azure Pipelines.

Wenn Sie über kein aktives Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Mit DevOps Projects wird eine CI/CD-Pipeline in Azure DevOps erstellt. Sie können eine neue Azure DevOps-Organisation erstellen oder eine bestehende Organisation verwenden. Ferner werden mit DevOps Projects Azure-Ressourcen im Azure-Abonnement Ihrer Wahl erstellt.

1. Melden Sie sich beim [Microsoft Azure-Portal](https://portal.azure.com)an.

1. Wählen Sie im linken Bereich die Option **Ressource erstellen** aus, und suchen Sie nach **DevOps Projects**.  

1.  Klicken Sie auf **Erstellen**.

## <a name="create-a-new-application-pipeline"></a>Erstellen einer neuen Anwendungspipeline 

1. Ihre Azure IoT Edge-Module können in [C#](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) und [Java](tutorial-java-module.md) geschrieben werden. Wählen Sie Ihre bevorzugte Sprache aus, um eine neue Anwendung zu starten: **.NET**, **Node.js**, **Python**, **C** oder **Java**. Klicken Sie auf **Weiter**, um fortzufahren.

   ![Sprache auswählen, um eine neue Anwendung zu erstellen](./media/how-to-devops-project/select-language.png)

2. Wählen Sie **Einfaches IoT (Vorschau)** als Anwendungsframework und dann **Weiter** aus.

   ![Einfaches IoT-Framework auswählen](media/how-to-devops-project/select-iot.png)

3. Wählen Sie **IoT Edge** als Azure-Dienst aus, der die Anwendung bereitstellt, und wählen Sie dann **Weiter** aus.

   ![Dienst „IoT Edge“ auswählen](media/how-to-devops-project/select-iot-edge.png)

4. Sie können eine neue kostenlose Azure DevOps-Organisation erstellen oder eine bestehende Organisation auswählen.

   1. Geben Sie einen Namen für Ihr Projekt an. 

   2. Wählen Sie Ihre Azure DevOps-Organisation aus. Wenn keine Organisation vorhanden ist, wählen Sie **Zusätzliche Einstellungen** aus, um eine neue zu erstellen. 

   3. Wählen Sie Ihr Azure-Abonnement.

   4. Verwenden Sie den Namen der durch Ihren Projektnamen generierten IoT Hub-Instanz, oder geben Sie einen eigenen an.

   5. Wählen Sie **Zusätzliche Einstellungen** aus, um die Azure-Ressourcen zu konfigurieren, die DevOps Projects in Ihrem Namen erstellt.

   6. Wählen Sie **Fertig** aus, um das Erstellen Ihres Projekts abzuschließen. 

   ![Anwendung benennen und erstellen](media/how-to-devops-project/select-devops.png)

Nach wenigen Minuten wird das DevOps Projects-Dashboard im Azure-Portal angezeigt. Wählen Sie Ihren Projektnamen aus, um den Status anzuzeigen. Möglicherweise müssen Sie die Seite aktualisieren. Eine Beispiel-IoT Edge-Anwendung wird in einem Repository in Ihrer Azure DevOps-Organisation eingerichtet, ein Build wird ausgeführt, und Ihre Anwendung wird auf dem IoT Edge-Gerät bereitgestellt. Dieses Dashboard bietet Einblick in Ihr Coderepository, in Ihre CI/CD-Pipeline und in Ihre Anwendung in Azure.

   ![Anwendung im DevOps-Portal anzeigen](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>Ausführen eines Commits für Codeänderungen und Ausführen von CI/CD

Von DevOps Projects wurde ein Git-Repository für Ihr Projekt in Azure Repos erstellt. In diesem Abschnitt zeigen Sie das Repository an und nehmen Codeänderungen an Ihrer Anwendung vor.

1. Navigieren Sie zu dem für Ihr Projekt erstellten Repository, und wählen Sie **Repositorys** im Menü Ihres Projektdashboards aus.  

   ![Ansicht des in Azure Repos generierten Repositorys](./media/how-to-devops-project/view-repositories.png)

2. Die folgenden Schritte führen Sie durch die Verwendung des Webbrowsers zum Ändern von Code. Wenn Sie stattdessen Ihr Repository lokal klonen möchten, wählen Sie **Klonen** oben rechts im Fenster aus. Verwenden Sie die angegebene URL zum Klonen Ihres Git-Repositorys in Visual Studio Code oder Ihrem bevorzugten Entwicklungstool. 

3. Das Repository enthält bereits Code für ein Modul namens **SampleModule**, basierend auf der Anwendungssprache, die Sie bei der Erstellung ausgewählt haben. Öffnen Sie die Datei **modules/SampleModule/module.json**.

   ![Öffnen der module.json-Datei in Azure Repos](./media/how-to-devops-project/open-module-json.png)

4. Wählen Sie **Bearbeiten** aus, und nehmen Sie unter `"tag"` eine Änderung für `"version"` vor. Sie können die Version z. B. auf `"version": "${BUILD_BUILDID}"` aktualisieren, sodass [Azure DevOps-Buildvariablen](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) als Teil Ihres Azure IoT Edge-Modulimagetags verwendet werden.

   ![Version bearbeiten, sodass Buildvariablen akzeptiert werden](media/how-to-devops-project/update-module-json.png)

5. Wählen Sie **Commit** aus, und speichern Sie anschließend die Änderungen.

6. Gehen Sie in Ihrem Browser zurück zu Ihrem DevOps Projects-Dashboard im Azure-Portal. Nun wird angezeigt, dass ein Build erstellt wird. Die von Ihnen vorgenommenen Änderungen werden automatisch erstellt und über eine CI/CD-Pipeline bereitgestellt.

    ![Status „Wird ausgeführt“ anzeigen](media/how-to-devops-project/ci-cd-in-progress.png)

## <a name="examine-the-cicd-pipeline"></a>Überprüfen der CI/CD-Pipeline

In den vorherigen Abschnitten wurde von Azure DevOps Projects automatisch eine vollständige CI/CD-Pipeline für Ihre IoT Edge-Anwendung konfiguriert. Dann haben Sie die Buildpipeline getestet, indem Sie Änderungen für eine der Dateien committet haben. Untersuchen Sie nun die Pipeline, und passen Sie sie bei Bedarf an. Führen Sie die folgenden Schritte aus, um sich mit den Build- und Releasepipelines von Azure DevOps vertraut zu machen:

1. Wählen Sie zum Anzeigen der Buildpipelines in Ihrem DevOps-Projekt **Buildpipelines** im Menü Ihres Projektdashboards aus. Über diesen Link werden eine Browserregisterkarte und die Azure DevOps-Buildpipeline für Ihr neues Projekt geöffnet.

   ![Anzeigen von Buildpipelines in Azure Pipelines](./media/how-to-devops-project/view-build-pipelines.png)

2. Wählen Sie **Bearbeiten** aus.

    ![Bearbeiten der Buildpipeline](media/how-to-devops-project/click-edit-button.png)

3. In dem Bereich, der geöffnet wird, können Sie die Aufgaben überprüfen, die auftreten, wenn Ihre Buildpipeline ausgeführt wird. Die Buildpipeline führt verschiedene Aufgaben durch. Beispielsweise werden Quellen aus dem Git-Repository abgerufen, IoT Edge-Modulimages erstellt und IoT Edge-Module zu einer Containerregistrierung gepusht sowie für Bereitstellungen verwendete Ausgaben veröffentlicht. Weitere Informationen zu Azure IoT Edge-Aufgaben in Azure DevOps finden Sie unter [Konfigurieren von Azure Pipelines für Continuous Integration](how-to-ci-cd.md#configure-continuous-integration).

4. Wählen Sie die **Pipeline**-Kopfzeile oben in der Buildpipeline aus, um die Pipelinedetails aufzurufen. Ersetzen Sie den Namen Ihrer Buildpipeline durch einen aussagekräftigeren Namen.

   ![Bearbeiten der Pipelinedetails](./media/how-to-devops-project/edit-build-pipeline.png)

5. Wählen Sie **Speichern und in Warteschlange einreihen** und dann **Speichern** aus.

6. Wählen Sie im Menü für die Buildpipeline **Trigger** aus. Mit DevOps Projects wurde automatisch ein CI-Trigger erstellt, und mit jedem für das Repository ausgeführten Commit wird ein neuer Build gestartet.  Optional können Sie Branches aus dem CI-Prozess einbeziehen oder ausschließen.

7. Wählen Sie **Aufbewahrung** aus. Abhängig vom Szenario können Sie Richtlinien zum Aufbewahren oder Entfernen einer bestimmten Anzahl von Builds festlegen.

8. Wählen Sie **Verlauf** aus. Der Verlaufsbereich enthält ein Überwachungsprotokoll mit den letzten Änderungen des Builds. An der Buildpipeline vorgenommene Änderungen werden von Azure Pipelines nachverfolgt, sodass Sie verschiedene Versionen vergleichen können.

9. Wenn Sie die Buildpipeline untersucht haben, navigieren Sie zu der entsprechenden Releasepipeline. Wählen Sie **Releases** unter **Pipelines** und dann **Bearbeiten** aus, um die Pipelinedetails anzuzeigen.

    ![Releasepipeline anzeigen](media/how-to-devops-project/release-pipeline.png)

10. Wählen Sie unter **Artefakte** die Option **Ablegen** aus. Die Quelle, die dieses Artefakt überwacht, ist die Ausgabe der Buildpipeline, die Sie in den vorherigen Schritten untersucht haben. 

11. Wählen Sie neben dem Symbol **Ablegen** das Symbol **Continuous Deployment-Trigger** aus, das wie ein Blitz aussieht. Diese Releasepipeline hat den Trigger aktiviert, der jedes Mal eine Bereitstellung ausführt, wenn ein neues Buildartefakt verfügbar ist. Optional können Sie den Trigger deaktivieren, sodass Ihre Bereitstellungen manuell ausgeführt werden müssen.  

12. Wählen Sie im Menü für Ihre Releasepipeline **Aufgaben** aus, und wählen Sie dann die Stufe **dev** in der Dropdownliste aus. DevOps Projects erstellte eine Releasestufe für Sie, die eine IoT Hub-Instanz und ein IoT Edge-Gerät in dieser IoT Hub-Instanz erstellt, das Beispielmodul aus der Buildpipeline bereitstellt sowie einen virtuellen Computer, der als Ihr IoT Edge-Gerät ausgeführt wird. Weitere Informationen zu Azure IoT Edge-Aufgaben für CD finden Sie unter [Konfigurieren von Continuous Deployment](how-to-ci-cd.md#configure-continuous-deployment).

    ![Continuous Deployment-Tasks anzeigen](media/how-to-devops-project/dev-release.png)

13. Wählen Sie auf der rechten Seite **Releases anzeigen** aus. In dieser Ansicht wird der Verlauf von Releases angezeigt.

14. Wählen Sie den Namen eines Releases, um weitere Informationen dazu anzuzeigen.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können die erstellte Azure App Service-Instanz und zugehörige Ressourcen löschen, wenn Sie sie nicht mehr benötigen. Verwenden Sie dazu die Funktion **Löschen** auf dem DevOps Projects-Dashboard.

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen über die Tasks für Azure IoT Edge auf Azure DevOps finden Sie unter [Continuous Integration und Continuous Deployment für Azure IoT Edge](how-to-ci-cd.md).
* Grundlegende Informationen zur IoT Edge-Bereitstellung finden Sie unter [Grundlegendes zu IoT Edge-Bereitstellungen für einzelne Geräte oder bedarfsabhängig](module-deployment-monitoring.md).
* Führen Sie die Schritte zum Erstellen, Aktualisieren oder Löschen einer Bereitstellung in [Bereitstellen und Überwachen von IoT Edge-Modulen im großen Maßstab](how-to-deploy-monitor.md) aus.
