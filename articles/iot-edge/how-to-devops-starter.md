---
title: CI/CD-Pipeline mit Azure DevOps Starter – Azure IoT Edge | Microsoft-Dokumentation
description: Azure DevOps Starter erleichtert die ersten Schritte mit Azure. Damit können Sie eine Azure IoT Edge-App Ihrer Wahl in einigen wenigen Schritten starten.
author: kgremban
ms.author: kgremban
ms.date: 08/25/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 97dc0fe5a3720a41dd63583c222762d832d636ea
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436996"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-starter"></a>Erstellen einer CI/CD-Pipeline für IoT Edge mit Azure DevOps Starter

Konfigurieren Sie mit DevOps Projects Continuous Integration (CI) und Continuous Delivery (CD) für Ihre IoT Edge-Anwendung. DevOps Starter erleichtert die Erstkonfiguration einer Build- und Releasepipeline in Azure Pipelines.

Wenn Sie über kein aktives Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Mit DevOps Starter wird eine CI/CD-Pipeline in Azure DevOps erstellt. Sie können eine neue Azure DevOps-Organisation erstellen oder eine bestehende Organisation verwenden. Ferner werden mit DevOps Starter Azure-Ressourcen im Azure-Abonnement Ihrer Wahl erstellt.

1. Melden Sie sich beim [Microsoft Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im linken Bereich die Option **Ressource erstellen** aus, und suchen Sie nach **DevOps Starter**.  

1. Klicken Sie auf **Erstellen**.

## <a name="create-a-new-application-pipeline"></a>Erstellen einer neuen Anwendungspipeline

1. Ihre Azure IoT Edge-Module können in [C#](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) und [Java](tutorial-java-module.md) geschrieben werden. Wählen Sie Ihre bevorzugte Sprache aus, um eine neue Anwendung zu starten: **.NET**, **Node.js**, **Python**, **C** oder **Java**. Klicken Sie auf **Weiter**, um fortzufahren.

   ![Sprache auswählen, um eine neue Anwendung zu erstellen](./media/how-to-devops-starter/select-language.png)

2. Wählen Sie **Einfaches IoT** als Anwendungsframework und dann **Weiter** aus.

   ![Einfaches IoT-Framework auswählen](media/how-to-devops-starter/select-iot.png)

3. Wählen Sie **IoT Edge** als Azure-Dienst aus, der die Anwendung bereitstellt, und wählen Sie dann **Weiter** aus.

   ![Dienst „IoT Edge“ auswählen](media/how-to-devops-starter/select-iot-edge.png)

4. Sie können eine neue kostenlose Azure DevOps-Organisation erstellen oder eine bestehende Organisation auswählen.

   1. Geben Sie einen Namen für Ihr Projekt an.

   2. Wählen Sie Ihre Azure DevOps-Organisation aus. Wenn keine Organisation vorhanden ist, wählen Sie **Zusätzliche Einstellungen** aus, um eine neue zu erstellen.

   3. Wählen Sie Ihr Azure-Abonnement.

   4. Verwenden Sie den Namen der durch Ihren Projektnamen generierten IoT Hub-Instanz, oder geben Sie einen eigenen an.

   5. Akzeptieren Sie den Standardspeicherort, oder wählen Sie einen in Ihrer Nähe aus.

   6. Wählen Sie **Zusätzliche Einstellungen** zum Konfigurieren der Azure-Ressourcen aus, die DevOps Starter in Ihrem Namen erstellt.

   7. Wählen Sie **Fertig** aus, um das Erstellen Ihres Projekts abzuschließen.

   ![Projekt benennen und erstellen](media/how-to-devops-starter/create-project.png)

Nach wenigen Minuten wird das DevOps Starter-Dashboard im Azure-Portal angezeigt. Wählen Sie Ihren Projektnamen aus, um den Status anzuzeigen. Möglicherweise müssen Sie die Seite aktualisieren. Eine Beispiel-IoT Edge-Anwendung wird in einem Repository in Ihrer Azure DevOps-Organisation eingerichtet, ein Build wird ausgeführt, und Ihre Anwendung wird auf dem IoT Edge-Gerät bereitgestellt. Dieses Dashboard bietet Einblick in Ihr Coderepository, in Ihre CI/CD-Pipeline und in Ihre Anwendung in Azure.

   ![Projekt im Azure-Portal anzeigen](./media/how-to-devops-starter/portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Ausführen eines Commits für Codeänderungen und Ausführen von CI/CD

DevOps Starter hat in Azure Repos ein Git-Repository für Ihr Projekt erstellt. In diesem Abschnitt zeigen Sie das Repository an und nehmen Codeänderungen an Ihrer Anwendung vor.

1. Navigieren Sie zu dem für Ihr Projekt erstellten Repository, und wählen Sie **Repositorys** im Menü Ihres Projektdashboards aus. Über diesen Link werden eine Browserregisterkarte und das Azure DevOps-Repository für Ihr neues Projekt geöffnet.

   ![Ansicht des in Azure Repos generierten Repositorys](./media/how-to-devops-starter/view-repositories.png)

   > [!NOTE]
   > Die folgenden Schritte führen Sie durch die Verwendung des Webbrowsers zum Ändern von Code. Wenn Sie stattdessen Ihr Repository lokal klonen möchten, wählen Sie **Klonen** oben rechts im Fenster aus. Verwenden Sie die angegebene URL zum Klonen Ihres Git-Repositorys in Visual Studio Code oder Ihrem bevorzugten Entwicklungstool.

2. Das Repository enthält bereits Code für das Modul **FilterModule**, das auf der Anwendungssprache basiert, die Sie bei der Erstellung ausgewählt haben. Öffnen Sie die Datei **modules/FilterModule/module.json**.

   ![Öffnen der module.json-Datei in Azure Repos](./media/how-to-devops-starter/open-module-json.png)

3. Beachten Sie, dass diese Datei im Parameter **version**[Azure DevOps-Buildvariablen](/azure/devops/pipelines/build/variables#build-variables) verwendet. Diese Konfiguration stellt sicher, dass bei jeder Ausführung eines neuen Builds eine neue Version des Moduls erstellt wird.

## <a name="examine-the-cicd-pipeline"></a>Überprüfen der CI/CD-Pipeline

In den vorherigen Abschnitten hat Azure DevOps Starter eine vollständige CI/CD-Pipeline für Ihre IoT Edge-Anwendung automatisch konfiguriert. Untersuchen Sie nun die Pipeline, und passen Sie sie bei Bedarf an. Führen Sie die folgenden Schritte aus, um sich mit den Build- und Releasepipelines von Azure DevOps vertraut zu machen.

1. Wählen Sie zum Anzeigen der Buildpipelines in Ihrem DevOps-Projekt **Buildpipelines** im Menü Ihres Projektdashboards aus. Über diesen Link werden eine Browserregisterkarte und die Azure DevOps-Buildpipeline für Ihr neues Projekt geöffnet.

   ![Anzeigen von Buildpipelines in Azure Pipelines](./media/how-to-devops-starter/view-build-pipelines.png)

2. Öffnen Sie die automatisch generierte Buildpipeline, und wählen Sie oben rechts **Bearbeiten** aus.

    ![Bearbeiten der Buildpipeline](media/how-to-devops-starter/click-edit-button.png)

3. In dem Bereich, der geöffnet wird, können Sie die Aufgaben überprüfen, die auftreten, wenn Ihre Buildpipeline ausgeführt wird. Die Buildpipeline führt verschiedene Aufgaben durch. Beispielsweise werden Quellen aus dem Git-Repository abgerufen, IoT Edge-Modulimages erstellt und IoT Edge-Module zu einer Containerregistrierung gepusht sowie für Bereitstellungen verwendete Ausgaben veröffentlicht. Weitere Informationen zu Azure IoT Edge-Aufgaben in Azure DevOps finden Sie unter [Konfigurieren von Azure Pipelines für Continuous Integration](how-to-continuous-integration-continuous-deployment-classic.md#create-a-build-pipeline-for-continuous-integration).

4. Wählen Sie die **Pipeline**-Kopfzeile oben in der Buildpipeline aus, um die Pipelinedetails aufzurufen. Ersetzen Sie den Namen Ihrer Buildpipeline durch einen aussagekräftigeren Namen.

   ![Bearbeiten der Pipelinedetails](./media/how-to-devops-starter/edit-build-pipeline.png)

5. Wählen Sie **Speichern und in Warteschlange einreihen** und dann **Speichern** aus. Die Eingabe eines Kommentars ist optional.

6. Wählen Sie im Menü für die Buildpipeline **Trigger** aus. Mit DevOps Starter wurde automatisch ein CI-Trigger erstellt, und mit jedem für das Repository ausgeführten Commit wird ein neuer Build gestartet.  Optional können Sie Branches aus dem CI-Prozess einbeziehen oder ausschließen.

7. Wählen Sie **Aufbewahrung** aus. Folgen Sie dem Link, der Sie zu den Projekteinstellungen umleitet, in denen die Aufbewahrungsrichtlinien zu finden sind. Abhängig vom Szenario können Sie Richtlinien zum Aufbewahren oder Entfernen einer bestimmten Anzahl von Builds festlegen.

8. Wählen Sie **Verlauf** aus. Der Verlaufsbereich enthält ein Überwachungsprotokoll mit den letzten Änderungen des Builds. An der Buildpipeline vorgenommene Änderungen werden von Azure Pipelines nachverfolgt, sodass Sie verschiedene Versionen vergleichen können.

9. Wenn Sie die Buildpipeline untersucht haben, navigieren Sie zu der entsprechenden Releasepipeline. Wählen Sie **Releases** unter **Pipelines** und dann **Bearbeiten** aus, um die Pipelinedetails anzuzeigen.

    ![Releasepipeline anzeigen](media/how-to-devops-starter/release-pipeline.png)

10. Wählen Sie unter **Artefakte** die Option **Ablegen** aus. Die Quelle, die dieses Artefakt überwacht, ist die Ausgabe der Buildpipeline, die Sie in den vorherigen Schritten untersucht haben.

11. Wählen Sie neben dem Symbol **Ablegen** das Symbol **Continuous Deployment-Trigger** aus, das wie ein Blitz aussieht. Diese Releasepipeline hat den Trigger aktiviert, der jedes Mal eine Bereitstellung ausführt, wenn ein neues Buildartefakt verfügbar ist. Optional können Sie den Trigger deaktivieren, sodass Ihre Bereitstellungen manuell ausgeführt werden müssen.  

12. Wählen Sie im Menü für Ihre Releasepipeline **Aufgaben** aus, und wählen Sie dann die Stufe **dev** in der Dropdownliste aus. DevOps Projects erstellte eine Releasestufe für Sie, die eine IoT Hub-Instanz und ein IoT Edge-Gerät in dieser IoT Hub-Instanz erstellt, das Beispielmodul aus der Buildpipeline bereitstellt sowie einen virtuellen Computer, der als Ihr IoT Edge-Gerät ausgeführt wird. Weitere Informationen zu Azure IoT Edge-Aufgaben für CD finden Sie unter [Konfigurieren von Continuous Deployment](how-to-continuous-integration-continuous-deployment-classic.md#create-a-release-pipeline-for-continuous-deployment).

    ![Continuous Deployment-Tasks anzeigen](media/how-to-devops-starter/choose-release.png)

13. Wählen Sie auf der rechten Seite **Releases anzeigen** aus. In dieser Ansicht wird der Verlauf von Releases angezeigt.

14. Wählen Sie den Namen eines Releases, um weitere Informationen dazu anzuzeigen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können die erstellte Azure App Service-Instanz und zugehörige Ressourcen löschen, wenn Sie sie nicht mehr benötigen. Verwenden Sie die Funktion **Löschen** auf dem DevOps Starter-Dashboard.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen über die Tasks für Azure IoT Edge auf Azure DevOps finden Sie unter [Continuous Integration und Continuous Deployment für Azure IoT Edge](how-to-continuous-integration-continuous-deployment.md).
* Grundlegende Informationen zur IoT Edge-Bereitstellung finden Sie unter [Grundlegendes zu IoT Edge-Bereitstellungen für einzelne Geräte oder bedarfsabhängig](module-deployment-monitoring.md).
* Führen Sie die Schritte zum Erstellen, Aktualisieren oder Löschen einer Bereitstellung in [Bereitstellen und Überwachen von IoT Edge-Modulen im großen Maßstab](how-to-deploy-at-scale.md) aus.
