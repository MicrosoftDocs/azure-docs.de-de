---
title: Continuous Integration und Continuous Deployment für Azure IoT Edge-Geräte – Azure IoT Edge
description: Einrichten von Continuous Integration und Continuous Deployment mithilfe von YAML– Azure IoT Edge mit Azure DevOps, Azure Pipelines
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 444ab8ccfe5a8441a4fd7d280e33d8e929d9387d
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435888"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices"></a>Continuous Integration und Continuous Deployment für Azure IoT Edge-Geräte

Sie können DevOps mit den integrierten Azure IoT Edge-Aufgaben in Azure Pipelines ganz einfach in Ihren Azure IoT Edge-Anwendungen übernehmen. In diesem Artikel wird gezeigt, wie Sie die Features für Continuous Integration und Continuous Deployment von Azure Pipelines zum schnellen und effizienten Erstellen, Testen und Bereitstellen von Anwendungen in Azure IoT Edge mithilfe von YAML verwenden können. Alternativ können Sie [den klassischen Editor verwenden](how-to-continuous-integration-continuous-deployment-classic.md).

![Diagramm – CI- und CD-Branches für Entwicklung und Produktion](./media/how-to-continuous-integration-continuous-deployment/model.png)

In diesem Artikel erfahren Sie, wie Sie mithilfe der integrierten [Azure IoT Edge-Aufgaben](/azure/devops/pipelines/tasks/build/azure-iot-edge) für Azure Pipelines Build- und Releasepipelines für Ihre IoT Edge-Projektmappe erstellen können. Jede Azure IoT Edge-Aufgabe, die Ihrer Pipeline hinzugefügt wird, implementiert eine der folgenden vier Aktionen:

 | Aktion | BESCHREIBUNG |
 | --- | --- |
 | Erstellen von Modulimages | Erstellt aus Ihrem IoT Edge-Projektmappencode die Containerimages.|
 | Pushen von Modulimages | Pusht Modulimages in die angegebene Containerregistrierung. |
 | Generieren des Bereitstellungsmanifests | Generiert aus einer „deployment.template.json“-Datei und den zugehörigen Variablen die finale Manifestdatei für eine IoT Edge-Bereitstellung. |
 | Bereitstellen auf IoT Edge-Geräten | Erstellt IoT Edge-Bereitstellungen auf einem oder mehreren IoT Edge-Geräten. |

Sofern nicht anders angegeben, untersuchen die Prozeduren in diesem Artikel nicht die gesamte Funktionalität, die über Aufgabenparameter zur Verfügung steht. Weitere Informationen finden Sie unter

* [Aufgabenversion](/azure/devops/pipelines/process/tasks?tabs=yaml#task-versions)
* **Erweitert** – Falls zutreffend, geben Sie Module an, die nicht erstellt werden sollen.
* [Steuerungsoptionen](/azure/devops/pipelines/process/tasks?tabs=yaml#task-control-options)
* [Umgebungsvariablen](/azure/devops/pipelines/process/variables?tabs=yaml#environment-variables)
* [Ausgabevariablen](/azure/devops/pipelines/process/variables?tabs=yaml#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Repos-Repository. Wenn Sie keines besitzen, können Sie [ein neues Git-Repository in Ihrem Projekt erstellen](/azure/devops/repos/git/create-new-repo). Für diesen Artikel haben wir ein Repository namens **IoTEdgeRepo** erstellt.
* Eine per Push an Ihr Repository committete IoT Edge-Lösung. Wenn Sie eine neue Beispiellösung für das Testen in diesem Artikel erstellen möchten, führen Sie die Schritte in [Verwenden von Visual Studio Code zum Entwickeln und Debuggen von Modulen für Azure IoT Edge](how-to-vs-code-develop-module.md) oder [Verwenden von Visual Studio 2017 zum Entwickeln und Debuggen von C#-Modulen für Azure IoT Edge (Vorschauversion)](./how-to-visual-studio-develop-module.md) aus. Für diesen Artikel haben wir in unserem Repository die Projektmappe **IoTEdgeSolution** erstellt, die den Code für das Modul **filtermodule** enthält.

   In diesem Artikel benötigen Sie lediglich den von den IoT Edge-Vorlagen in Visual Studio Code oder Visual Studio erstellten Projektmappenordner. Sie müssen diesen Code nicht erstellen, pushen, bereitstellen oder debuggen, bevor Sie fortfahren. Sie werden diese Prozesse in Azure Pipelines einrichten.

   Wenn Sie eine neue Projektmappe erstellen, klonen Sie zunächst das lokale Repository. Dann können Sie die Projektmappe wahlweise direkt im Repositoryordner erstellen. Sie können die neuen Dateien von dort problemlos committen und pushen.

* Eine Containerregistrierung, in die Sie Modulimages per Push übertragen können. Sie können [Azure Container Registry](../container-registry/index.yml) oder eine Drittanbieterregistrierung verwenden.
* Eine aktive Azure [IoT Hub-Instanz](../iot-hub/iot-hub-create-through-portal.md) mit mindestens zwei IoT Edge-Geräten zum Testen der separaten Test- und Produktionsbereitstellungsstufen. Sie können die Schnellstartartikel zum Erstellen eines IoT Edge-Geräts auf [Linux](quickstart-linux.md) oder [Windows](quickstart.md) befolgen.

Weitere Informationen zur Verwendung von Azure Repos finden Sie unter [Freigeben von Code mit Visual Studio und Azure Repos](/azure/devops/repos/git/share-your-code-in-git-vs).

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Erstellen einer Buildpipeline für Continuous Integration

In diesem Abschnitt erstellen Sie eine neue Buildpipeline. Sie konfigurieren die Pipeline so, dass sie automatisch ausgeführt wird, wenn Sie Änderungen an der IoT Edge-Beispielprojektmappe einchecken und Buildprotokolle veröffentlichen.

1. Melden Sie sich bei Ihrer Azure DevOps-Organisation an (`https://dev.azure.com/{your organization}`), und öffnen Sie das Projekt, das Ihr IoT Edge-Projektmappenrepository enthält.

   ![Öffnen Ihres DevOps-Projekts](./media/how-to-continuous-integration-continuous-deployment/initial-project.png)

2. Wählen Sie im Menü im linken Bereich Ihres Projekts **Pipelines** aus. Wählen Sie in der Mitte der Seite **Pipeline erstellen** aus. Wenn Sie aber bereits Buildpipelines haben, wählen Sie oben rechts die Schaltfläche **Neue Pipeline** aus.

    ![Erstellen einer neuen Buildpipeline über die Schaltfläche „Neue Pipeline“](./media/how-to-continuous-integration-continuous-deployment/add-new-pipeline.png)

3. Wählen Sie auf der Seite **Wo befindet sich Ihr Code?** die Option **Azure Repos Git`YAML`** aus. Wenn Sie zum Erstellen der Buildpipelines für Ihr Projekt den klassischen Editor verwenden möchten, finden Sie Informationen dazu im [classic editor guide](how-to-continuous-integration-continuous-deployment-classic.md) (Leitfaden zum klassischen Editor).

4. Wählen Sie das Repository aus, für das Sie eine Pipeline erstellen.

    ![Das Repository für Ihre Buildpipeline auswählen](./media/how-to-continuous-integration-continuous-deployment/select-repository.png)

5. Wählen Sie auf der Seite **Pipeline konfigurieren** die Option **Starterpipeline** aus. Wenn Sie bereits eine YAML-Datei für Azure Pipelines haben, die Sie zum Erstellen dieser Pipeline verwenden möchten, können Sie **Vorhandene Azure Pipelines-YAML-Datei** auswählen sowie den Branch und Pfad im Repository für die Datei angeben.

    ![Zum Beginnen Ihrer Buildpipeline „Starterpipeline“ oder „Vorhandene Azure Pipelines-YAML-Datei“ auswählen](./media/how-to-continuous-integration-continuous-deployment/configure-pipeline.png)

6. Auf der Seite **Pipeline-YAML überprüfen** können Sie auf den Standardnamen `azure-pipelines.yml` klicken, um die Konfigurationsdatei Ihrer Pipeline umzubenennen.

   Wählen Sie **Assistent anzeigen** aus, um die Palette **Aufgaben** zu öffnen.

    ![„Assistent anzeigen“ zum Öffnen der Palette „Aufgaben“ auswählen](./media/how-to-continuous-integration-continuous-deployment/show-assistant.png)

7. Wenn Sie eine Aufgabe hinzufügen möchten, setzen Sie den Cursor an das Ende der YAML oder an jede beliebige Stelle, an der die Anweisungen für Ihre Aufgabe hinzugefügt werden sollen. Suchen Sie nach der Aufgabe **Azure IoT Edge**, und wählen Sie sie aus. Füllen Sie die Parameter der Aufgabe folgendermaßen aus. Klicken Sie anschließend auf **Hinzufügen**.

   | Parameter | BESCHREIBUNG |
   | --- | --- |
   | Aktion | Auswählen von **Modulimages erstellen**. |
   | „.template.json“-Datei | Geben Sie den Pfad zur Datei **deployment.template.json** in dem Repository an, das Ihre IoT Edge-Projektmappe enthält. |
   | Standardplattform | Wählen Sie das entsprechende Betriebssystem für Ihre Module, basierend auf Ihrem IoT Edge-Zielgerät, aus. |

    ![Über die Palette „Aufgaben“ Ihrer Pipeline Aufgaben hinzufügen](./media/how-to-continuous-integration-continuous-deployment/add-build-task.png)

   >[!TIP]
   > Nachdem jede Aufgabe hinzugefügt wurde, werden die hinzugefügten Zeilen vom Editor automatisch hervorgehoben. Wenn Sie ein versehentliches Überschreiben verhindern möchten, deaktivieren Sie die Zeilen, und geben Sie einen neuen Bereich für Ihre nächste Aufgabe an, bevor Sie weitere Aufgaben hinzufügen.

8. Wiederholen Sie diesen Vorgang zum Hinzufügen von drei weiteren Aufgaben mit den folgenden Parametern:

   * Aufgabe: **Azure IoT Edge**

       | Parameter | BESCHREIBUNG |
       | --- | --- |
       | Aktion | Wählen Sie **Modulimages pushen** aus. |
       | Containerregistrierungstyp | Verwenden Sie den Standardtyp: **Azure Container Registry**: |
       | Azure-Abonnement | Wählen Sie Ihr Abonnement aus. |
       | Azure Container Registry | Wählen Sie die Registrierung aus, die Sie für die Pipeline verwenden möchten. |
       | „.template.json“-Datei | Geben Sie den Pfad zur Datei **deployment.template.json** in dem Repository an, das Ihre IoT Edge-Projektmappe enthält. |
       | Standardplattform | Wählen Sie das entsprechende Betriebssystem für Ihre Module, basierend auf Ihrem IoT Edge-Zielgerät, aus. |

   * Aufgabe: **Kopieren von Dateien**

       | Parameter | BESCHREIBUNG |
       | --- | --- |
       | Quellordner | Der Quellordner, aus dem kopiert werden soll. „Empty“ ist der Stamm des Repositorys. Verwenden Sie Variablen, wenn keine Dateien im Repository sind. Beispiel: `$(agent.builddirectory)`.
       | Inhalte | Fügen Sie zwei Zeilen hinzu: `deployment.template.json` und `**/module.json`. |
       | Zielordner | Geben Sie die Variable `$(Build.ArtifactStagingDirectory)` an. Eine Beschreibung finden Sie unter [Buildvariablen](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables). |

   * Aufgabe: **Veröffentlichen von Buildartefakten**

       | Parameter | BESCHREIBUNG |
       | --- | --- |
       | Pfad für Veröffentlichung | Geben Sie die Variable `$(Build.ArtifactStagingDirectory)` an. Eine Beschreibung finden Sie unter [Buildvariablen](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables). |
       | Artefaktname | Geben Sie den Standardnamen an: `drop`. |
       | Veröffentlichungsort für Artefakte | Verwenden Sie den Standardort: `Azure Pipelines`. |

9. Wählen Sie oben rechts in der Dropdownliste **Speichern und ausführen** die Option **Speichern** aus.

10. Der Trigger für Continuous Integration ist bei Ihrer YAML-Pipeline standardmäßig aktiviert. Wenn Sie diese Einstellungen bearbeiten möchten, wählen Sie Ihre Pipeline aus, und klicken Sie oben rechts auf **Bearbeiten**. Wählen Sie neben der Schaltfläche **Ausführen** oben rechts **Weitere Aktionen** aus, und wechseln Sie zu **Trigger**. Unter dem Namen ihrer Pipeline wird **Continuous Integration** als aktiviert angezeigt. Wenn Sie die Details zum Trigger anzeigen möchten, aktivieren Sie das Kontrollkästchen **YAML-Continuous Integration-Trigger von hier aus überschreiben**.

    ![Informationen zum Überprüfen der Triggereinstellungen für Ihre Pipeline finden Sie unter „Weitere Aktionen“ in „Trigger“.](./media/how-to-continuous-integration-continuous-deployment/check-trigger-settings.png)

Fortfahren Sie mit dem nächsten Abschnitt fort, um die Releasepipeline zu erstellen.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Nächste Schritte

* Beispiel für Best Practices für IoT Edge DevOps in [Azure DevOps Starter für IoT Edge](how-to-devops-starter.md)
* Grundlegende Informationen zur IoT Edge-Bereitstellung finden Sie unter [Grundlegendes zu IoT Edge-Bereitstellungen für einzelne Geräte oder bedarfsabhängig](module-deployment-monitoring.md).
* Führen Sie die Schritte zum Erstellen, Aktualisieren oder Löschen einer Bereitstellung in [Bereitstellen und Überwachen von IoT Edge-Modulen im großen Maßstab](how-to-deploy-at-scale.md) aus.