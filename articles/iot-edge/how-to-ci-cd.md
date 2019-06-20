---
title: Continuous Integration und Continuous Deployment – Azure IoT Edge | Microsoft-Dokumentation
description: Einrichten von Continuous Integration und Continuous Deployment – Azure IoT Edge mit Azure DevOp, Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f449449c542ce6ac04daa58ff37a3577f0d75aee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61221849"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Continuous Integration und Continuous Deployment für Azure IoT Edge

Sie können DevOps mit den integrierten Azure IoT Edge-Aufgaben in Azure Pipelines ganz einfach in Ihren Azure IoT Edge-Anwendungen übernehmen. In diesem Artikel wird gezeigt, wie Sie die Features für Continuous Integration und Continuous Deployment von Azure Pipelines zum schnellen und effizienten Erstellen, Testen und Bereitstellen von Anwendungen in Azure IoT Edge verwenden können. 

In diesem Artikel erfahren Sie, wie Sie die integrierten Azure IoT Edge-Aufgaben für Azure-Pipelines verwenden, um zwei Pipelines für Ihre IoT Edge-Lösung zu erstellen. Die erste erstellt die Lösung mithilfe Ihres Codes, wobei Ihre Modulimages per Push an Ihre Containerregistrierung übertragen werden und ein Bereitstellungsmanifest erstellt wird. Die zweite stellt Ihre Module auf gewünschten IoT Edge-Geräten bereit.  

![Diagramm – CI- und CD-Branches für Entwicklung und Produktion](./media/how-to-ci-cd/cd.png)


## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Repos-Repository. Wenn Sie keines besitzen, können Sie [ein neues Git-Repository in Ihrem Projekt erstellen](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* Eine per Push an Ihr Repository committete IoT Edge-Lösung. Wenn Sie eine neue Beispiellösung für das Testen in diesem Artikel erstellen möchten, führen Sie die Schritte in [Verwenden von Visual Studio Code zum Entwickeln und Debuggen von Modulen für Azure IoT Edge](how-to-vs-code-develop-module.md) oder [Verwenden von Visual Studio 2017 zum Entwickeln und Debuggen von C#-Modulen für Azure IoT Edge (Vorschauversion)](how-to-visual-studio-develop-csharp-module.md) aus.
   * In diesem Artikel benötigen Sie lediglich den von den IoT Edge-Vorlagen in Visual Studio Code oder Visual Studio erstellten Projektmappenordner. Sie müssen diesen Code nicht erstellen, pushen, bereitstellen oder debuggen, bevor Sie fortfahren. Sie müssen diese Prozesse in Azure Pipelines einrichten. 
   * Wenn Sie eine neue Projektmappe erstellen, klonen Sie zunächst das lokale Repository. Dann können Sie die Projektmappe wahlweise direkt im Repositoryordner erstellen. Sie können die neuen Dateien von dort problemlos committen und pushen. 
* Eine Containerregistrierung, in die Sie Modulimages per Push übertragen können. Sie können [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) oder eine Drittanbieterregistrierung verwenden. 
* Eine aktive [IoT Hub-Instanz](../iot-hub/iot-hub-create-through-portal.md), die mindestens über IoT Edge-Geräte zum Testen der separaten Test- und Produktionsbereitstellungsstufen verfügt. Sie können die Schnellstartartikel zum Erstellen eines IoT Edge-Geräts auf [Linux](quickstart-linux.md) oder [Windows](quickstart.md) befolgen.


Weitere Informationen zur Verwendung von Azure Repos finden Sie unter [Freigeben von Code mit Visual Studio und Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts).

## <a name="configure-continuous-integration"></a>Konfigurieren von Continuous Integration
In diesem Abschnitt erstellen Sie eine neue Buildpipeline. Konfigurieren Sie die Pipeline so, dass sie automatisch ausgeführt wird, wenn Sie Änderungen an der IoT Edge-Beispielprojektmappe einchecken und Buildprotokolle veröffentlichen.

>[!NOTE]
>In diesem Artikel verwenden Sie den visuellen Designer von Azure DevOps. Bevor Sie die Schritte in diesem Abschnitt ausführen, deaktivieren Sie die Previewfunktion für die neue Oberfläche für das Erstellen der YAML-Pipeline. 
>1. Wählen Sie in Azure DevOps Ihr Profilsymbol und dann **Vorschaufeatures** aus.
>2. Deaktivieren Sie dann **Neue Benutzeroberfläche zum Erstellen einer YAML-Pipeline**. 
>
>Weitere Informationen finden Sie unter [Erstellen einer Buildpipeline](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav#create-a-build-pipeline).

1. Melden Sie sich bei Ihrer Azure DevOps-Organisation (**https:\//dev.azure.com/{Ihre Organisation}/** ) an, und öffnen Sie das Projekt, das Ihr IoT Edge-Projektmappenrepository enthält.

   Für diesen Artikel haben wir ein Repository namens **IoTEdgeRepo** erstellt. Dieses Repository enthält **IoTEdgeSolution**, was den Code für ein Modul namens **filtermodule** enthält. 

   ![Öffnen Ihres DevOps-Projekts](./media/how-to-ci-cd/init-project.png)

2. Navigieren Sie in Ihrem Projekt zu Azure Pipelines. Öffnen Sie die Registerkarte **Builds**, und wählen Sie **Neue Pipeline** aus. Wenn Sie bereits über Buildpipelines verfügen, wählen Sie die Schaltfläche **Neu** aus. Wählen Sie dann **Neue Buildpipeline** aus.

    ![Erstellen einer neuen Buildpipeline](./media/how-to-ci-cd/add-new-build.png)

3. Folgen Sie den Anweisungen zum Erstellen Ihrer Pipeline. 

   1. Geben Sie die Quelleninformationen für die neue Buildpipeline ein. Wählen Sie **Azure Repos Git** als Quelle und anschließend Projekt, Repository und Branch aus, wo sich der IoT Edge-Projektmappencode befindet. Wählen Sie anschließend **Weiter** aus. 

      ![Auswählen Ihrer Pipelinequelle](./media/how-to-ci-cd/pipeline-source.png)

   2. Wählen Sie **Leerer Auftrag** anstelle einer Vorlage aus. 

      ![Starten mit einem leeren Prozess](./media/how-to-ci-cd/start-with-empty.png)

4. Sobald die Pipeline erstellt wurde, gelangen Sie zum Pipeline-Editor. Wählen Sie in der Pipelinebeschreibung basierend auf Ihrer Zielplattform den richtigen Agent-Pool aus: 
    
   * Wenn Sie Ihre Module auf der Plattform amd64 für Linux-Container erstellen möchten, wählen Sie **Hosted Ubuntu 1604** aus.

   * Wenn Sie Ihre Module auf der Plattform amd64 für Windows 1809-Container erstellen möchten, müssen Sie [einen selbstgehosteten Agent unter Windows einrichten](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Wenn Sie Ihre Module auf der Plattform arm32v7 für Linux-Container erstellen möchten, müssen Sie [einen selbstgehosteten Agent unter Linux einrichten](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
     ![Konfigurieren des Build-Agent-Pools](./media/how-to-ci-cd/configure-env.png)

5. Die Pipeline wird mit einem Auftrag mit dem Namen **Agent job 1** vorkonfiguriert. Wählen Sie das Pluszeichen ( **+** ) aus, um dem Auftrag drei Aufgaben hinzuzufügen: Zweimal **Azure IoT Edge** und einnal **Buildartefakte veröffentlichen**. (Zeigen Sie auf den Namen der einzelnen Aufgaben, um die Schaltfläche **Hinzufügen** anzuzeigen.)

   ![Hinzufügen der Azure IoT Edge-Aufgabe](./media/how-to-ci-cd/add-iot-edge-task.png)

   Wenn alle drei Aufgaben hinzugefügt werden, sieht Ihr Agent-Auftrag wie im folgenden Beispiel aus:
    
   ![Drei Aufgaben in der Buildpipeline](./media/how-to-ci-cd/add-tasks.png)

6. Wählen Sie die erste zu bearbeitende **Azure IoT Edge**-Aufgabe aus. Diese Aufgabe erstellt alle Module in der Projektmappe mit der Zielplattform, die Sie angeben, und generiert außerdem die **deployment.json**-Datei, die Ihren IoT Edge-Geräten mitteilt, wie Sie die Bereitstellung konfigurieren.

   * **Anzeigename**: Übernehmen Sie den Standard **Azure IoT Edge – Modulimages erstellen**.
   * **Aktion:** Übernehmen Sie den Standard **Modulimages erstellen**. 
   * **.template.json-Datei**: Wählen Sie die Auslassungspunkte ( **...** ) aus, und navigieren Sie zu der **deployment.template.json**-Datei in dem Repository, das die IoT Edge-Projektmappe enthält. 
   * **Standardplattform**: Wählen Sie die entsprechende Plattform für die Module auf dem gewünschten IoT Edge-Gerät basierend aus. 
   * **Ausgabevariablen**: Die Ausgabevariablen enthalten einen Verweisnamen, mit dem Sie den Dateipfad konfigurieren können, in dem die deployment.json-Datei generiert wird. Wählen Sie einen einprägsamen Verweisnamen wie **edge**. 

7. Wählen Sie die zweite **Azure IoT Edge**-Aufgabe aus, um sie zu bearbeiten. Diese Aufgabe überträgt alle Modulimages in die von Ihnen ausgewählte Containerregistrierung. Sie fügt auch Anmeldeinformationen für Ihre Containerregistrierung der Datei **deployment.JSON** hinzu, sodass Ihr IoT Edge-Gerät auf die Modulimages zugreifen kann. 

   * **Anzeigename**: Der Anzeigename wird automatisch aktualisiert, wenn das Aktionsfeld geändert wird. 
   * **Aktion:** Verwenden Sie die Dropdownliste, um **Modulimages pushen** auszuwählen. 
   * **Containerregistrierungstyp**: Wählen Sie den Typ der Containerregistrierung aus, mit dem Sie Ihre Modulimages speichern. Je nach ausgewähltem Registrierungstyp ändert sich die Form. Wenn Sie **Azure Container Registry** auswählen, verwenden Sie die Dropdownlisten, um das Azure-Abonnement und den Namen Ihrer Containerregistrierung auszuwählen. Wenn Sie **Generische Containerregistrierung** auswählen, wählen Sie **Neu** zum Erstellen einer Registrierungsdienstverbindung aus. 
   * **.template.json-Datei**: Wählen Sie die Auslassungspunkte ( **...** ) aus, und navigieren Sie zu der **deployment.template.json**-Datei in dem Repository, das die IoT Edge-Projektmappe enthält. 
   * **Standardplattform**: Wählen Sie die gleiche Plattform aus, auf der sich auch Ihre Modulimages befinden.

   Wenn Sie die Modulimages in mehreren Containerregistrierungen hosten können, müssen Sie diese Aufgabe duplizieren. Wählen Sie eine andere Containerregistrierung aus, und verwenden Sie **Bypass module(s)** (Modul(e) umgehen) in den erweiterten Einstellungen, um die Images auszulassen, die nicht für diese spezifische Registrierung bestimmt sind.

8. Wählen Sie die Aufgabe **Buildartefakte veröffentlichen**  aus, um sie zu bearbeiten. Geben Sie den Dateipfad zu der Bereitstellungsdatei an, die von der Buildaufgabe generiert wird. Legen Sie den Wert für **Pfad für Veröffentlichung** entsprechend der Ausgabevariablen fest, die Sie in der Modulerstellungsaufgabe festlegen. Beispiel: `$(edge.DEPLOYMENT_FILE_PATH)`. Behalten Sie bei den anderen Optionen die Standardwerte bei. 

9. Wählen Sie die Registerkarte **Trigger** aus, und aktivieren Sie **Continuous Integration aktivieren**. Stellen Sie sicher, dass der Branch mit Ihrem Code enthalten ist.

    ![Aktivieren des Continuous Integration-Triggers](./media/how-to-ci-cd/configure-trigger.png)

10. Speichern Sie die neue Buildpipeline mit der Schaltfläche **Speichern**.

Diese Pipeline ist jetzt für die automatische Ausführung konfiguriert, wenn Sie neuen Code in Ihr Repository pushen. Die letzte Aufgabe, Veröffentlichen der Pipelineartefakte, löst eine Releasepipeline aus. Fortfahren Sie mit dem nächsten Abschnitt fort, um die Releasepipeline zu erstellen. 

## <a name="configure-continuous-deployment"></a>Konfigurieren von Continuous Deployment
In diesem Abschnitt erstellen Sie eine Releasepipeline, die so konfiguriert ist, dass sie beim Ablegen von Artefakten durch Ihre Buildpipeline automatisch ausgeführt wird und Bereitstellungsprotokolle in Azure Pipelines anzeigt.

In diesem Abschnitt erstellen Sie zwei verschiedene Stufen, eine für Testbereitstellungen und eine für Produktionsbereitstellungen. 

### <a name="create-test-stage"></a>Erstellen der Teststufe

Erstellen Sie eine neue Pipeline, und konfigurieren Sie ihre erste Stufe für Qualitätssicherungsbereitstellungen (Quality Assurance, QA). 

1. Wählen Sie auf der Registerkarte **Releases** die Option **+ Neue Pipeline** aus. Wenn Sie bereits über Releasepipelines verfügen, wählen Sie die Schaltfläche **+ Neu** und dann **+ Neue Releasepipeline** aus.  

    ![Hinzufügen einer Releasepipeline](./media/how-to-ci-cd/add-release-pipeline.png)

2. Wenn Sie aufgefordert werden, eine Vorlage auszuwählen, wählen Sie für den Einstieg **Leerer Auftrag** aus.

    ![Starten mit einem leeren Auftrag](./media/how-to-ci-cd/start-with-empty-job.png)

3. Ihre neue Releasepipeline wird mit einer Stufe namens **Stage 1** initialisiert. Benennen Sie „Stage 1“ in **QA** um, und behandeln Sie sie als Testumgebung. Pipelines für kontinuierliche Bereitstellung verfügen in der Regel über mehrere Stufen. Sie können basierend auf Ihrer DevOps-Praxis weitere erstellen. Schließen Sie das Stufendetailsfenster nach der Umbenennung. 

    ![Erstellen der Testumgebungsphase](./media/how-to-ci-cd/QA-env.png)

4. Verknüpfen Sie das Release mit den Buildartefakten, die von der Buildpipeline veröffentlicht werden. Klicken Sie im Artefaktbereich auf **Hinzufügen**.

   ![Hinzufügen von Artefakten](./media/how-to-ci-cd/add-artifacts.png)  
    
5. Wählen Sie auf der Seite **Artefakt hinzufügen** den Quellentyp **Build** aus. Wählen Sie dann das Projekt und die erstellte Buildpipeline aus. Klicken Sie anschließend auf **Hinzufügen**.

   ![Hinzufügen eines Buildartefakts](./media/how-to-ci-cd/add-an-artifact.png)

6. Öffnen Sie die Artefakttrigger, und wählen Sie die Umschaltfläche aus, um die Continuous Deployment-Trigger zu aktivieren. Nun wird immer dann ein neues Release erstellt, wenn ein neuer Build verfügbar ist.

   ![Konfigurieren des Continuous Deployment-Triggers](./media/how-to-ci-cd/add-a-trigger.png)

7. Die **QA**-Stufe ist mit einem Auftrag und null (0) Aufgaben vorkonfiguriert. Wählen Sie im Pipelinemenü **Aufgaben** und dann die **QA**-Stufe aus.  Wählen Sie die Anzahl von Aufträgen und Aufgaben aus, um die Aufgaben in dieser Stufe zu konfigurieren.

    ![Konfigurieren der QA-Aufgaben](./media/how-to-ci-cd/view-stage-tasks.png)

8. In der QA-Stufe sollte der Standardwert **Agent-Auftrag** angezeigt werden. Sie können Details zum Agent-Auftrag konfigurieren, aber bei Bereitstellungsaufgaben ist die Plattform nicht von Bedeutung, sodass Sie im **Agent-Pool** eine der Optionen **Hosted VS2017** oder **Hosted Ubuntu 1604** (oder einen beliebigen anderen, von Ihnen selbst verwalteten Agent) verwenden können. 

9. Wählen Sie das Pluszeichen ( **+** ) aus, um eine Aufgabe hinzuzufügen. Suchen Sie nach **Azure IoT Edge**, und fügen Sie es hinzu. 

    ![Hinzufügen von Aufgaben für QA](./media/how-to-ci-cd/add-task-qa.png)

10. Wählen Sie die neue Azure IoT Edge-Aufgabe aus, und konfigurieren Sie sie mit den folgenden Werten:

    * **Anzeigename**: Der Anzeigename wird automatisch aktualisiert, wenn das Aktionsfeld geändert wird. 
    * **Aktion:** Verwenden Sie die Dropdownliste, um **Für IoT Edge-Gerät bereitstellen** auszuwählen. Ändern des Werts für die Aktion aktualisiert auch den Anzeigenamen der Aufgabe, mit dem Übereinstimmung herrschen muss.
    * **Azure-Abonnement**: Wählen Sie das Abonnement aus, das Ihre IoT Hub-Instanz enthält.
    * **IoT Hub-Name**: Wählen Sie Ihren IoT Hub aus. 
    * **Einzelnes/mehrere Geräte auswählen**: Wählen Sie aus, ob die Bereitstellung durch die Releasepipeline auf einem oder mehreren Geräten ausgeführt werden soll. 
      * Geben Sie bei Bereitstellung auf einem einzelnen Gerät die **IoT Edge-Geräte-ID** ein. 
      * Geben Sie bei einer Bereitstellung auf mehreren Geräten die **Zielbedingung** des Geräts an. Die Zielbedingung ist ein Filter, der mit einem Satz von Edge-Geräten in IoT Hub übereinstimmen muss. Wenn Sie Gerätetags als Bedingung verwenden möchten, müssen Sie die Tags der entsprechenden Geräte mit einem Iot Hub-Gerätezwilling aktualisieren. Aktualisieren Sie die **IoT Edge-Bereitstellungs-ID** und **IoT Edge-Bereitstellungspriorität** in den erweiterten Einstellungen. Weitere Informationen zum Erstellen einer Bereitstellung für mehrere Geräte finden Sie unter [Grundlegendes zu automatischen IoT Edge-Bereitstellungen für einzelne Geräte oder nach Bedarf](module-deployment-monitoring.md).

11. Wählen Sie **Speichern** zum Speichern der Änderungen auf der neuen Releasepipeline aus. Kehren Sie durch Auswahl von **Pipeline** im Menü zur Pipelineansicht zurück. 

### <a name="create-production-stage"></a>Erstellen einer Produktionsstufe

Erstellen Sie eine zweite Stufe in Ihrer Releasepipeline für die Produktionsbereitstellung. 

1. Schaffen Sie eine zweite Stufe für die Produktion durch Klonen der QA-Stufe. Bewegen Sie den Cursor über die QA-Stufe, und wählen Sie dann die Schaltfläche „Klonen“ aus. 

    ![Klonen der Phase](./media/how-to-ci-cd/clone-stage.png)

2. Wählen Sie die neue Stufe namens **QA-Kopie** aus, um ihre Eigenschaften zu öffnen. Ändern Sie den Namen der Stufe für die Produktion in **PROD**. Schließen Sie das Stufeneigenschaftenfenster. 

3. Wählen Sie zum Öffnen der PROD-Stufenaufgaben die Option **Aufgaben** im Pipelinemenü aus, und wählen Sie dann die Stufe **PROD** aus. 

4. Wählen Sie die Azure IoT Edge-Aufgabe aus, um sie für Ihre Produktionsumgebung zu konfigurieren. Die Bereitstellungseinstellungen entsprechen wahrscheinlich denen für QA und PROD, außer dass Sie in der Produktion ein anderes Gerät oder eine andere Gruppe von Geräten verwenden möchten. Aktualisieren Sie das Geräte-ID-Feld bzw. die Zielbedingungs- und Bereitstellungs-ID-Felder für die Geräte in der Produktion. 

5. Speichern Sie mit der Schaltfläche **Speichern**. Wählen Sie dann **Pipeline** aus, um zur Pipeline zurückzukehren.
    
6. Diese Releasepipeline ist gegenwärtig derart konfiguriert, dass der Buildartefakt jedes Mal, wenn ein neuer Build abgeschlossen ist, die **QA**- und dann die **PROD**-Stufe auslöst. In der Regel möchten Sie jedoch wohl einige Testfälle auf den QA-Geräten integrieren und die Bereitstellung für die Produktion manuell genehmigen. Erstellen Sie in den folgenden Schritten eine Zustimmungsbedingung für die PROD-Stufe:

    1. Öffnen Sie den Einstellungsbereich **Bedingungen vor der Bereitstellung**.

        ![Öffnen von „Bedingungen vor der Bereitstellung“](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Schalten Sie **Bedingungen vor der Bereitstellung** in **Aktiviert** um. Fügen Sie einen oder mehrere Benutzer oder Gruppen im Feld **Genehmigende Personen** hinzu, und passen Sie andere gewünschte Genehmigungsrichtlinien an. Um Ihre Änderungen zu speichern, schließen Sie den Bereich „Bedingungen vor der Bereitstellung“.
    
       ![Festlegen von Bedingungen](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


7. Speichern Sie Ihre Releasepipeline mit der Schaltfläche **Speichern**. 

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Überprüfen von CI/CD in IoT Edge mit den Build- und Releasepipelines

Um einen Buildauftrag auszulösen, können Sie einen Commit in das Quellcoderepository pushen oder manuell auslösen. In diesem Abschnitt lösen Sie manuell die CI/CD-Pipeline aus, um die Funktionsweise zu testen. Anschließend überprüfen Sie, ob die Bereitstellung erfolgreich ausgeführt wird.

1. Navigieren Sie zu der Buildpipeline, die Sie am Anfang dieses Artikels erstellt haben. 

2. Sie können einen Buildauftrag in Ihrer Buildpipeline auslösen, indem Sie die Schaltfläche **Warteschlange** auswählen, wie im folgenden Screenshot dargestellt.

    ![Manueller Trigger](./media/how-to-ci-cd/manual-trigger.png)

3. Wählen Sie den Buildauftrag aus, um seinen Fortschritt zu überwachen. Wenn die Buildpipeline erfolgreich abgeschlossen ist, wird eine Freigabe zur **QA**-Stufe ausgelöst. 

    ![Buildprotokolle](./media/how-to-ci-cd/build-logs.png)

4. Die erfolgreiche Bereitstellung zur **QA**-Stufe sollte eine Benachrichtigung an die genehmigende Person auslösen. Stellen Sie sicher, dass die Module erfolgreich auf dem Gerät oder den Geräten bereitgestellt werden, die Sie der QA-Stufe zugewiesen haben. Navigieren Sie dann zur Releasepipeline, und geben Sie die Genehmigung für die Freigabe in der PROD-Stufe, indem Sie die **PROD**-Schaltfläche und dann **Genehmigen** auswählen. 

    ![Ausstehende Genehmigung](./media/how-to-ci-cd/pending-approval.png)

5. Nachdem die genehmigende Person diese Änderung genehmigt, kann sie in **PROD** bereitgestellt werden.

## <a name="next-steps"></a>Nächste Schritte

* Grundlegende Informationen zur IoT Edge-Bereitstellung finden Sie unter [Grundlegendes zu IoT Edge-Bereitstellungen für einzelne Geräte oder bedarfsabhängig](module-deployment-monitoring.md).
* Führen Sie die Schritte zum Erstellen, Aktualisieren oder Löschen einer Bereitstellung in [Bereitstellen und Überwachen von IoT Edge-Modulen im großen Maßstab](how-to-deploy-monitor.md) aus.
