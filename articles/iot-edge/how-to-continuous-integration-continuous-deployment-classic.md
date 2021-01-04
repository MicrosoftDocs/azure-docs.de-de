---
title: Continuous Integration und Continuous Deployment für Azure IoT Edge-Geräte (klassischer Editor) – Azure IoT Edge
description: Einrichten von Continuous Integration und Continuous Deployment mithilfe des klassischen Editors – Azure IoT Edge mit Azure DevOps, Azure Pipelines
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b9b842b94d66cf91ad836b8ae61df1b3d3f34293
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435942"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices-classic-editor"></a>Continuous Integration und Continuous Deployment für Azure IoT Edge-Geräte (klassischer Editor)

Sie können DevOps mit den integrierten Azure IoT Edge-Aufgaben in Azure Pipelines ganz einfach in Ihren Azure IoT Edge-Anwendungen übernehmen. In diesem Artikel wird gezeigt, wie Sie die Features für Continuous Integration und Continuous Deployment von Azure Pipelines zum schnellen und effizienten Erstellen, Testen und Bereitstellen von Anwendungen in Azure IoT Edge mithilfe des klassischen Editors verwenden können. Alternativ können Sie [YAML verwenden](how-to-continuous-integration-continuous-deployment.md).

![Diagramm – CI- und CD-Branches für Entwicklung und Produktion](./media/how-to-continuous-integration-continuous-deployment-classic/model.png)

In diesem Artikel erfahren Sie, wie Sie mithilfe der integrierten [Azure IoT Edge-Aufgaben](/azure/devops/pipelines/tasks/build/azure-iot-edge) für Azure Pipelines Build- und Releasepipelines für Ihre IoT Edge-Projektmappe erstellen können. Jede Azure IoT Edge-Aufgabe, die Ihrer Pipeline hinzugefügt wird, implementiert eine der folgenden vier Aktionen:

 | Aktion | BESCHREIBUNG |
 | --- | --- |
 | Erstellen von Modulimages | Erstellt aus Ihrem IoT Edge-Projektmappencode die Containerimages.|
 | Pushen von Modulimages | Pusht Modulimages in die angegebene Containerregistrierung. |
 | Generieren des Bereitstellungsmanifests | Generiert aus einer „deployment.template.json“-Datei und den zugehörigen Variablen die finale Manifestdatei für eine IoT Edge-Bereitstellung. |
 | Bereitstellen auf IoT Edge-Geräten | Erstellt IoT Edge-Bereitstellungen auf einem oder mehreren IoT Edge-Geräten. |

Sofern nicht anders angegeben, untersuchen die Prozeduren in diesem Artikel nicht die gesamte Funktionalität, die über Aufgabenparameter zur Verfügung steht. Weitere Informationen finden Sie unter

* [Aufgabenversion](/azure/devops/pipelines/process/tasks?tabs=classic#task-versions)
* **Erweitert** – Falls zutreffend, geben Sie Module an, die nicht erstellt werden sollen.
* [Steuerungsoptionen](/azure/devops/pipelines/process/tasks?tabs=classic#task-control-options)
* [Umgebungsvariablen](/azure/devops/pipelines/process/variables?tabs=classic#environment-variables)
* [Ausgabevariablen](/azure/devops/pipelines/process/variables?tabs=classic#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Repos-Repository. Wenn Sie keines besitzen, können Sie [ein neues Git-Repository in Ihrem Projekt erstellen](/azure/devops/repos/git/create-new-repo). Für diesen Artikel haben wir ein Repository namens **IoTEdgeRepo** erstellt.
* Eine per Push an Ihr Repository committete IoT Edge-Lösung. Wenn Sie eine neue Beispiellösung für das Testen in diesem Artikel erstellen möchten, führen Sie die Schritte in [Verwenden von Visual Studio Code zum Entwickeln und Debuggen von Modulen für Azure IoT Edge](how-to-vs-code-develop-module.md) oder [Verwenden von Visual Studio 2017 zum Entwickeln und Debuggen von C#-Modulen für Azure IoT Edge (Vorschauversion)](./how-to-visual-studio-develop-module.md) aus. Für diesen Artikel haben wir in unserem Repository die Projektmappe **IoTEdgeSolution** erstellt, die den Code für das Modul **filtermodule** enthält.

   In diesem Artikel benötigen Sie lediglich den von den IoT Edge-Vorlagen in Visual Studio Code oder Visual Studio erstellten Projektmappenordner. Sie müssen diesen Code nicht erstellen, pushen, bereitstellen oder debuggen, bevor Sie fortfahren. Sie werden diese Prozesse in Azure Pipelines einrichten.

   Wenn Sie eine neue Projektmappe erstellen, klonen Sie zunächst das lokale Repository. Dann können Sie die Projektmappe wahlweise direkt im Repositoryordner erstellen. Sie können die neuen Dateien von dort problemlos committen und pushen.

* Eine Containerregistrierung, in die Sie Modulimages per Push übertragen können. Sie können [Azure Container Registry](../container-registry/index.yml) oder eine Drittanbieterregistrierung verwenden.
* Eine aktive Azure [IoT Hub-Instanz](../iot-hub/iot-hub-create-through-portal.md) mit mindestens zwei IoT Edge-Geräten zum Testen der separaten Test- und Produktionsbereitstellungsstufen. Sie können die Schnellstartartikel zum Erstellen eines IoT Edge-Geräts auf [Linux](quickstart-linux.md) oder [Windows](quickstart.md) befolgen.

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Erstellen einer Buildpipeline für Continuous Integration

In diesem Abschnitt erstellen Sie eine neue Buildpipeline. Sie konfigurieren die Pipeline so, dass sie automatisch ausgeführt wird, wenn Sie Änderungen an der IoT Edge-Beispielprojektmappe einchecken und Buildprotokolle veröffentlichen.

1. Melden Sie sich bei Ihrer Azure DevOps-Organisation an (`https://dev.azure.com/{your organization}`), und öffnen Sie das Projekt, das Ihr IoT Edge-Projektmappenrepository enthält.

    ![Öffnen Ihres DevOps-Projekts](./media/how-to-continuous-integration-continuous-deployment-classic/initial-project.png)

2. Wählen Sie im Menü im linken Bereich Ihres Projekts **Pipelines** aus. Wählen Sie in der Mitte der Seite **Pipeline erstellen** aus. Wenn Sie aber bereits Buildpipelines haben, wählen Sie oben rechts die Schaltfläche **Neue Pipeline** aus.

    ![Erstellen einer neuen Buildpipeline](./media/how-to-continuous-integration-continuous-deployment-classic/add-new-pipeline.png)

3. Wählen Sie unten auf der Seite **Wo befindet sich Ihr Code?** die Option **Klassischen Editor verwenden** aus. Wenn Sie YAML zum Erstellen der Buildpipelines für Ihr Projekt verwenden möchten, lesen Sie den [Leitfaden zu YAML](how-to-continuous-integration-continuous-deployment.md).

    ![„Klassischen Editor verwenden“ auswählen](./media/how-to-continuous-integration-continuous-deployment-classic/create-without-yaml.png)

4. Folgen Sie den Anweisungen zum Erstellen Ihrer Pipeline.

   1. Geben Sie die Quelleninformationen für die neue Buildpipeline ein. Wählen Sie **Azure Repos Git** als Quelle und anschließend Projekt, Repository und Branch aus, wo sich der IoT Edge-Projektmappencode befindet. Wählen Sie anschließend **Weiter** aus.

      ![Auswählen Ihrer Pipelinequelle](./media/how-to-continuous-integration-continuous-deployment-classic/pipeline-source.png)

   2. Wählen Sie **Leerer Auftrag** anstelle einer Vorlage aus.

      ![Starten mit einem leeren Auftrag für Ihre Buildpipeline](./media/how-to-continuous-integration-continuous-deployment-classic/start-with-empty-build-job.png)

5. Sobald die Pipeline erstellt wurde, gelangen Sie zum Pipeline-Editor. Hier können Sie den Namen, den Agentpool und die Agentspezifikation für die Pipeline ändern.

   Sie können einen von Microsoft gehosteten Pool oder einen von Ihnen verwalteten selbstgehosteten Pool auswählen.

   Wählen Sie in der Pipelinebeschreibung, basierend auf Ihrer Zielplattform, die richtige Agentspezifikation aus:

   * Wenn Sie Ihre Module auf der Plattform amd64 für Linux-Container erstellen möchten, wählen Sie **ubuntu-16.04** aus.

   * Wenn Sie Ihre Module auf der Plattform amd64 für Windows 1809-Container erstellen möchten, müssen Sie [einen selbstgehosteten Agent unter Windows einrichten](/azure/devops/pipelines/agents/v2-windows).

   * Wenn Sie Ihre Module auf der Plattform arm32v7 oder arm64 für Linux-Container erstellen möchten, müssen Sie [einen selbstgehosteten Agent unter Linux einrichten](https://devblogs.microsoft.com/iotdev/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent).

    ![Konfigurieren der Build-Agent-Spezifikation](./media/how-to-continuous-integration-continuous-deployment-classic/configure-env.png)

6. Die Pipeline wird mit einem Auftrag mit dem Namen **Agent job 1** vorkonfiguriert. Wählen Sie das Pluszeichen ( **+** ) aus, um dem Auftrag vier Aufgaben hinzuzufügen: Zweimal **Azure IoT Edge**, einmal **Dateien kopieren** und einmal **Buildartefakte veröffentlichen**. Suchen Sie nach jeder Aufgabe, und zeigen Sie auf deren Namen, um die Schaltfläche **Hinzufügen** anzuzeigen.

   ![Hinzufügen der Azure IoT Edge-Aufgabe](./media/how-to-continuous-integration-continuous-deployment-classic/add-iot-edge-task.png)

   Wenn alle vier Aufgaben hinzugefügt werden, sieht Ihr Agent-Auftrag wie im folgenden Beispiel aus:

   ![Vier Aufgaben in der Buildpipeline](./media/how-to-continuous-integration-continuous-deployment-classic/add-tasks.png)

7. Wählen Sie die erste zu bearbeitende **Azure IoT Edge**-Aufgabe aus. Diese Aufgabe erstellt alle Module in der Projektmappe mit der angegebenen Zielplattform. Bearbeiten Sie die Aufgabe mit den folgenden Werten:

    | Parameter | BESCHREIBUNG |
    | --- | --- |
    | Anzeigename | Der Anzeigename wird bei einer Änderung des Felds „Aktion“ automatisch aktualisiert. |
    | Aktion | Auswählen von **Modulimages erstellen**. |
    | „template.json“-Datei | Wählen Sie die Auslassungspunkte (**...**) aus, und navigieren Sie zu der **deployment.template.json**-Datei in dem Repository, das die IoT Edge-Projektmappe enthält. |
    | Standardplattform | Wählen Sie das entsprechende Betriebssystem für Ihre Module, basierend auf Ihrem IoT Edge-Zielgerät, aus. |
    | Ausgabevariablen | Geben Sie den Verweisnamen an, der dem Dateipfad zugeordnet werden soll, in dem Ihre Datei „deployment.json“ generiert wird, z. B. **edge**. |

   Diese Konfigurationen verwenden das Imagerepository und -tag, die in der `module.json`-Datei definiert sind, um das Modulimage zu benennen und zu kennzeichnen. Das **Erstellen von Modulimages** hilft auch dabei, die Variablen durch die exakten Werte zu ersetzen, die Sie in der Datei `module.json` definiert haben. In Visual Studio oder Visual Studio Code geben Sie den tatsächlichen Wert in einer `.env`-Datei an. In Azure Pipelines legen Sie den Wert auf der Registerkarte **Pipelinevariablen** fest. Wählen Sie im Menü des Pipeline-Editors die Registerkarte **Variablen** aus, und konfigurieren Sie den Namen und den Wert folgendermaßen:

    * **ACR_ADDRESS**: Der Wert **Anmeldeserver** für Ihre Azure Container Registry. Den Anmeldeserver können Sie im Azure-Portal auf der Übersichtsseite Ihrer Containerregistrierung ermitteln.

    Wenn in Ihrem Projekt noch weitere Variablen vorhanden sind, können Sie die Namen und Werte auf diese Registerkarte angeben. Das **Erstellen von Modulimages** erkennt nur Variablen, die im `${VARIABLE}`-Format vorliegen. Stellen Sie sicher, dass Sie dieses Format in ihren `**/module.json`-Dateien verwenden.

8. Wählen Sie die zweite **Azure IoT Edge**-Aufgabe aus, um sie zu bearbeiten. Diese Aufgabe überträgt alle Modulimages in die von Ihnen ausgewählte Containerregistrierung.

    | Parameter | BESCHREIBUNG |
    | --- | --- |
    | Anzeigename | Der Anzeigename wird bei einer Änderung des Felds „Aktion“ automatisch aktualisiert. |
    | Aktion | Wählen Sie **Modulimages pushen** aus. |
    | Containerregistrierungstyp | Verwenden Sie den Standardtyp: `Azure Container Registry`. |
    | Azure-Abonnement | Wählen Sie Ihr Abonnement aus. |
    | Azure Container Registry | Wählen Sie den Typ der Containerregistrierung aus, mit dem Sie Ihre Modulimages speichern. Je nach ausgewähltem Registrierungstyp ändert sich die Form. Wenn Sie **Azure Container Registry** auswählen, verwenden Sie die Dropdownlisten, um das Azure-Abonnement und den Namen Ihrer Containerregistrierung auszuwählen. Wenn Sie **Generische Containerregistrierung** auswählen, wählen Sie **Neu** zum Erstellen einer Registrierungsdienstverbindung aus. |
    | „template.json“-Datei | Wählen Sie die Auslassungspunkte (**...**) aus, und navigieren Sie zu der **deployment.template.json**-Datei in dem Repository, das die IoT Edge-Projektmappe enthält. |
    | Standardplattform | Wählen Sie das entsprechende Betriebssystem für Ihre Module, basierend auf Ihrem IoT Edge-Zielgerät, aus. |
    | Hinzufügen von Registrierungsanmeldeinformationen zum Bereitstellungsmanifest | Geben Sie „true“ an, um dem Bereitstellungsmanifest die Registrierungsanmeldeinformationen zum Pushen von Docker-Images hinzuzufügen. |

   Wenn Sie Ihre Modulimages in mehreren Containerregistrierungen hosten können, müssen Sie diese Aufgabe duplizieren. Wählen Sie eine andere Containerregistrierung aus, und verwenden Sie **Module für Umgehung** in den Einstellungen **Erweitert**, um die für diese spezifische Registrierung nicht bestimmten Module zu umgehen.

9. Wählen Sie die Aufgabe **Dateien kopieren** aus, um sie zu bearbeiten. Verwenden Sie diese Aufgabe, um Dateien in das Artefaktstagingverzeichnis zu kopieren.

    | Parameter | BESCHREIBUNG |
    | --- | --- |
    | Anzeigename | Den Standardnamen verwenden oder anpassen |
    | Quellordner | Der Ordner mit den Dateien, die kopiert werden sollen. |
    | Inhalte | Fügen Sie zwei Zeilen hinzu: `deployment.template.json` und `**/module.json`. Diese beiden Dateien dienen als Eingaben zum Generieren des IoT Edge-Bereitstellungsmanifests. |
    | Zielordner | Geben Sie die Variable `$(Build.ArtifactStagingDirectory)` an. Eine Beschreibung finden Sie unter [Buildvariablen](/azure/devops/pipelines/build/variables#build-variables). |

10. Wählen Sie die Aufgabe **Buildartefakte veröffentlichen** aus, um sie zu bearbeiten. Geben Sie in der Aufgabe einen Pfad für das Artefaktstagingverzeichnis an, sodass dieser für die Releasepipeline veröffentlicht werden kann.

    | Parameter | BESCHREIBUNG |
    | --- | --- |
    | Anzeigename | Verwenden Sie den Standardnamen, oder passen Sie ihn an. |
    | Pfad für Veröffentlichung | Geben Sie die Variable `$(Build.ArtifactStagingDirectory)` an. Weitere Informationen finden Sie unter [Buildvariablen](/azure/devops/pipelines/build/variables#build-variables). |
    | Artefaktname | Verwenden Sie den Standardnamen: **drop** |
    | Veröffentlichungsort für Artefakte | Verwenden Sie den Standardspeicherort: **Azure Pipelines** |

11. Wählen Sie die Registerkarte **Trigger** aus, und aktivieren Sie **Continuous Integration aktivieren**. Stellen Sie sicher, dass der Branch mit Ihrem Code enthalten ist.

    ![Aktivieren des Continuous Integration-Triggers](./media/how-to-continuous-integration-continuous-deployment-classic/configure-trigger.png)

12. Wählen Sie in der Dropdownliste **Speichern und in Warteschlange einreihen** die Option **Speichern** aus.

Diese Pipeline ist jetzt für die automatische Ausführung konfiguriert, wenn Sie neuen Code in Ihr Repository pushen. Die letzte Aufgabe, Veröffentlichen der Pipelineartefakte, löst eine Releasepipeline aus. Fortfahren Sie mit dem nächsten Abschnitt fort, um die Releasepipeline zu erstellen.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

>[!NOTE]
>Wenn Sie in ihrer Pipeline **mehrstufige Bereitstellungen** verwenden möchten, ist dies leider nicht möglich. Mehrstufige Bereitstellungen werden in Azure IoT Edge-Aufgaben in Azure DevOps nämlich noch nicht unterstützt.
>
>Sie können jedoch mithilfe einer [Azure CLI-Aufgabe in Azure DevOps](/azure/devops/pipelines/tasks/deploy/azure-cli) Ihre Bereitstellung als eine mehrstufige Bereitstellung erstellen. Für den Wert **Inlineskript** können Sie den [Befehl „az iot edge deployment create“](/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment) verwenden:
>
>   ```azurecli-interactive
>   az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>   ```

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Nächste Schritte

* Beispiel für Best Practices für IoT Edge DevOps in [Azure DevOps Starter für IoT Edge](how-to-devops-starter.md)
* Grundlegende Informationen zur IoT Edge-Bereitstellung finden Sie unter [Grundlegendes zu IoT Edge-Bereitstellungen für einzelne Geräte oder bedarfsabhängig](module-deployment-monitoring.md).
* Führen Sie die Schritte zum Erstellen, Aktualisieren oder Löschen einer Bereitstellung in [Bereitstellen und Überwachen von IoT Edge-Modulen im großen Maßstab](how-to-deploy-at-scale.md) aus.