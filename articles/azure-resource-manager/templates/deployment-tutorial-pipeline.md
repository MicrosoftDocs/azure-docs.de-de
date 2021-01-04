---
title: Continuous Integration mit Azure Pipelines
description: Hier finden Sie Informationen zum kontinuierlichen Erstellen, Testen und Bereitstellen von Azure Resource Manager-Vorlagen (ARM-Vorlagen).
ms.date: 08/24/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: d7688a4e4838cb591bcd3ac0045a5ed22180c063
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906351"
---
# <a name="tutorial-continuous-integration-of-arm-templates-with-azure-pipelines"></a>Tutorial: Continuous Integration von ARM-Vorlagen mit Azure Pipelines

Im [vorherigen Tutorial](./deployment-tutorial-linked-template.md) haben Sie eine verknüpfte Vorlage bereitgestellt.  In diesem Tutorial finden Sie Informationen zum kontinuierlichen Erstellen und Bereitstellen von Azure Resource Manager-Vorlagenprojekten (ARM-Vorlagen) mit Azure Pipelines.

Azure DevOps stellt Entwicklerdienste bereit, die Teams bei der Arbeitsplanung, bei der gemeinsamen Codeentwicklung sowie bei der Erstellung und Bereitstellung von Anwendungen unterstützen. Mit Azure DevOps Services können Entwickler in der Cloud arbeiten. Azure DevOps bietet integrierte Features, auf die Sie über Ihren Webbrowser oder über Ihren IDE-Client zugreifen können. Eines dieser Features ist Azure Pipelines. Azure Pipelines ist ein umfassender Dienst für Continuous Integration (CI) und Continuous Delivery (CD). Er kann mit Ihrem bevorzugten Git-Anbieter sowie für Bereitstellungen in den meisten gängigen Clouddiensten verwendet werden. Anschließend können Sie das Erstellen, Testen und Bereitstellen Ihres Codes für Microsoft Azure, Google Cloud Platform oder Amazon Web Services automatisieren.

> [!NOTE]
> Überlegen Sie sich einen Projektnamen. Ersetzen Sie in diesem Tutorial jedes Vorkommen von **AzureRmPipeline** durch Ihren Projektnamen.
> Dieser Projektname wird verwendet, um Ressourcennamen zu generieren.  Bei einer der Ressourcen handelt es sich um ein Speicherkonto. Speicherkontonamen müssen zwischen 3 und 24 Zeichen lang sein und dürfen nur Zahlen und Kleinbuchstaben enthalten. Der Name muss eindeutig sein. In der Vorlage ist der Name des Speicherkontos der Projektname mit „store“ am Ende, und der Projektname muss zwischen 3 und 11 Zeichen haben. Daher muss der Projektname die Anforderungen des Speicherkontonamens erfüllen und weniger als 11 Zeichen enthalten.

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * ein GitHub-Repository erstellen
> * Erstellen eines Azure DevOps-Projekts
> * Erstellen einer Azure-Pipeline
> * Überprüfen der Pipelinebereitstellung
> * Aktualisieren der Vorlage und erneutes Bereitstellen
> * Bereinigen von Ressourcen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:

* **Ein GitHub-Konto** zum Erstellen eines Repositorys für Ihre Vorlagen. Falls Sie keines haben, können Sie [kostenlos eines erstellen](https://github.com). Weitere Informationen zur Verwendung von GitHub-Repositorys finden Sie unter [Build GitHub repositories](/azure/devops/pipelines/repos/github) (Erstellen von GitHub-Repositorys).
* **Git installieren**. In diesem Tutorial wird *Git Bash* oder *Git Shell* verwendet. Eine entsprechende Anleitung finden Sie unter [Install Git]( https://www.atlassian.com/git/tutorials/install-git) (Installieren von Git).
* **Eine Azure DevOps-Organisation.** Sollten Sie über keine Organisation verfügen, können Sie kostenlos eine Organisation erstellen. Weitere Informationen finden Sie unter [Quickstart: Create an organization or project collection](/azure/devops/organizations/accounts/create-organization?view=azure-devops) (Schnellstart: Erstellen einer Organisation oder einer Projektsammlung).
* (Optional) **Visual Studio Code mit der Erweiterung „Azure Resource Manager-Tools“** . Weitere Informationen finden Sie unter [Schnellstart: Erstellen von ARM-Vorlagen mit Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="prepare-a-github-repository"></a>ein GitHub-Repository erstellen

GitHub wird zum Speichern Ihres Projektquellcodes (einschließlich Resource Manager-Vorlagen) verwendet. Informationen zu weiteren unterstützten Repositorys finden Sie unter [Supported repository types](/azure/devops/pipelines/repos/?view=azure-devops) (Unterstützte Repositorytypen).

### <a name="create-a-github-repository"></a>Erstellen eines GitHub-Repositorys

Sollten Sie kein GitHub-Konto besitzen, sehen Sie sich die [Voraussetzungen](#prerequisites) an.

1. Melden Sie sich bei [GitHub](https://github.com) an.
1. Wählen Sie rechts oben Ihr Kontobild und anschließend **Your Repositorys** (Ihre Repositorys) aus.

    ![Azure Resource Manager/Azure DevOps/Azure Pipelines: Erstellen eines GitHub-Repositorys](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-github-repository.png)

1. Wählen Sie die grüne Schaltfläche **New** (Neu) aus.
1. Geben Sie unter **Repository Name** (Repositoryname) einen Repositorynamen ein.  Beispiel: **AzureRmPipeline-repo**. Denken Sie daran, alle Vorkommen von **AzureRmPipeline** durch Ihren Projektnamen zu ersetzen. In diesem Tutorial können Sie entweder **Public** (Öffentlich) oder **Private** (Privat) auswählen. Wählen Sie anschließend **Create Repository** (Repository erstellen) aus.
1. Notieren Sie sich die URL. Die Repository-URL hat das folgende Format: **`https://github.com/[YourAccountName]/[YourRepositoryName]`** .

Dieses Repository wird als *Remoterepository* bezeichnet. Jeder Entwickler des gleichen Projekts kann sein eigenes *lokales Repository* klonen und die Änderungen mit dem Remoterepository zusammenführen.

### <a name="clone-the-remote-repository"></a>Klonen des Remoterepositorys

1. Öffnen Sie Git Shell oder Git Bash.  Siehe [Voraussetzungen](#prerequisites).
1. Vergewissern Sie sich, dass Sie sich im Ordner **GitHub** befinden.
1. Führen Sie den folgenden Befehl aus:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateWebApp
    cd CreateWebApp
    pwd
    ```

    Ersetzen Sie **[YourAccountName]** durch den Namen Ihres GitHub-Kontos und **[YourGitHubRepositoryName]** durch den Namen des Repositorys, das Sie im vorherigen Schritt erstellt haben.

Der Ordner **CreateWebApp** ist der Speicherort für die Vorlage. Der Befehl **pwd** zeigt den Ordnerpfad an. Dieser Pfad wird im nächsten Schritt verwendet, um die Vorlage zu speichern.

### <a name="download-a-quickstart-template"></a>Herunterladen einer Schnellstartvorlage

Anstatt die Vorlagen zu erstellen, können Sie sie herunterladen und im Ordner **CreateWebApp** speichern.

* Die Hauptvorlage: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
* Die verknüpfte Vorlage: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Sowohl der Ordnername als auch der Dateiname werden genau so in der Pipeline verwendet.  Wenn Sie diese Namen ändern, müssen auch die in der Pipeline verwendeten Namen aktualisiert werden.

### <a name="push-the-template-to-the-remote-repository"></a>Pushen der Vorlage in das Remoterepository

Die Datei „azuredeploy.json“ wurde dem lokalen Repository hinzugefügt. Als Nächstes muss die Vorlage in das Remoterepository hochgeladen werden.

1. Öffnen Sie *Git Shell* oder *Git Bash*, sofern es nicht bereits geöffnet ist.
1. Wechseln Sie zum Ordner „CreateWebApp“ in Ihrem lokalen Repository.
1. Vergewissern Sie sich, dass der Ordner die Datei **azuredeploy.json** enthält.
1. Führen Sie den folgenden Befehl aus:

    ```bash
    git add .
    git commit -m "Add web app templates."
    git push origin master
    ```

    Daraufhin wird unter Umständen eine LF-Warnung angezeigt. Diese Warnung kann ignoriert werden. **master** ist der Masterbranch.  Üblicherweise wird für jedes Update ein eigener Branch erstellt. Zur Vereinfachung des Tutorials verwenden wir den Masterbranch direkt.
1. Navigieren Sie in einem Browser zu Ihrem GitHub-Repository.  Die URL lautet **`https://github.com/[YourAccountName]/[YourGitHubRepository]`** . Der Ordner **CreateWebApp** und die drei Dateien im Ordner werden angezeigt.
1. Wählen Sie **linkedStorageAccount.json** aus, um die Vorlage zu öffnen.
1. Wählen Sie die Schaltfläche **raw** (unformatiert) aus. Die URL wird mit **raw.githubusercontent.com** gestartet.
1. Kopieren Sie die URL.  Sie müssen diesen Wert angeben, wenn Sie die Pipeline später in diesem Tutorial konfigurieren.

Sie haben nun ein GitHub-Repository erstellt und die Vorlagen in das Repository hochgeladen.

## <a name="create-a-devops-project"></a>Erstellen eines DevOps-Projekts

Für den nächsten Schritt wird eine DevOps-Organisation benötigt.  Falls Sie keine Organisation haben, sehen Sie sich die [Voraussetzungen](#prerequisites) an.

1. Melden Sie sich bei [Azure DevOps](https://dev.azure.com) an.
1. Wählen Sie auf der linken Seite eine DevOps-Organisation aus.

    ![Azure Resource Manager/Azure DevOps/Azure Pipelines: Erstellen eines Azure DevOps-Projekts](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Wählen Sie **Neues Projekt** aus. Sollten Sie über keine Projekte verfügen, wird automatisch die Projekterstellungsseite geöffnet.
1. Geben Sie die folgenden Werte ein:

    * **Projektname**: Geben Sie einen Projektnamen ein. Sie können den Projektnamen verwenden, den Sie ganz am Anfang des Tutorials überlegt haben.
    * **Versionskontrolle**: Wählen Sie **Git** aus. Sollte **Versionskontrolle** nicht angezeigt werden, wählen Sie **Erweitert** aus, um den Bereich zu erweitern.

    Übernehmen Sie bei den anderen Eigenschaften die Standardwerte.
1. Klicken Sie auf **Erstellen**.

Erstellen Sie eine Dienstverbindung für die Projektbereitstellung in Azure.

1. Wählen Sie im unteren Bereich des linken Menüs die Option **Projekteinstellungen** aus.
1. Wählen Sie unter **Pipelines** die Option **Dienstverbindungen** aus.
1. Wählen Sie **Neue Dienstverbindung**, **Azure Resource Manager** und anschließend **Weiter** aus.
1. Wählen Sie **Dienstprinzipal** und dann **Weiter** aus.
1. Geben Sie die folgenden Werte ein:

    * **Bereichsebene**: Wählen Sie **Abonnement** aus.
    * **Abonnement**: Wählen Sie Ihr Abonnement aus.
    * **Ressourcengruppe**: Lassen Sie dieses Feld leer.
    * **Verbindungsname**: Geben Sie einen Verbindungsnamen ein. Beispiel: **AzureRmPipeline-conn**. Notieren Sie sich diesen Namen. Er wird bei der Pipelineerstellung benötigt.
    * **Gewähren Sie allen Pipelines die Zugriffsberechtigung**. (aktiviert)
1. Wählen Sie **Speichern** aus.

## <a name="create-a-pipeline"></a>Erstellen einer Pipeline

In den vorangegangenen Abschnitten wurden die folgenden Aufgaben ausgeführt.  Sollten Sie diese Abschnitte übersprungen haben, weil Sie bereits mit GitHub und DevOps vertraut sind, müssen Sie diese Aufgaben nachholen, bevor Sie fortfahren.

* Erstellen Sie ein GitHub-Repository, und speichern Sie die Vorlage im Ordner **CreateWebApp** des Repositorys.
* Erstellen eines DevOps-Projekts und einer Azure Resource Manager-Dienstverbindung

So erstellen Sie eine Pipeline mit einem Vorlagenbereitstellungsschritt:

1. Wählen Sie im linken Menü die Option **Pipelines** aus.
1. Wählen Sie **Neue Pipeline** aus.
1. Wählen Sie auf der Registerkarte **Verbinden** die Option **GitHub** aus. Geben Sie Ihre GitHub-Anmeldeinformationen ein, wenn Sie dazu aufgefordert werden, und folgen Sie dann den Anweisungen. Wählen Sie im folgenden Bildschirm die Option **Only select repositories** (Nur ausgewählte Repositorys) aus, und vergewissern Sie sich, dass Ihr Repository in der Liste enthalten ist, bevor Sie **Approve & Install** (Genehmigen und installieren) auswählen.

    ![Azure Resource Manager/Azure DevOps/Azure Pipelines: „Only select repositories“ (Nur ausgewählte Repositorys)](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Wählen Sie auf der Registerkarte **Auswählen** Ihr Repository aus.  Der Standardname lautet **[Name Ihres Kontos]/[Name Ihres GitHub-Repositorys]** .
1. Wählen Sie auf der Registerkarte **Konfigurieren** die Option **Starterpipeline** aus. Daraufhin wird die Pipelinedatei **azure-pipelines.yml** mit zwei Skriptschritten angezeigt.
1. Löschen Sie die beiden Skriptschritte aus der YML-Datei.
1. Bewegen Sie den Cursor in die Zeile nach **Schritte:** .
1. Wählen Sie auf der rechten Seite des Bildschirms **Assistent anzeigen** aus, um den Bereich **Aufgaben** zu öffnen.
1. Wählen Sie **ARM-Vorlagenbereitstellung** aus.
1. Geben Sie die folgenden Werte ein:

    * **deploymentScope**: Wählen Sie **Ressourcengruppe** aus. Weitere Informationen zum Umfang finden Sie unter [Bereitstellungsumfang](deploy-rest.md#deployment-scope).
    * **Azure Resource Manager-Verbindung**: Wählen Sie den Namen der Dienstverbindung aus, den Sie zuvor erstellt haben.
    * **Abonnement**:  Geben Sie die Zielabonnement-ID an.
    * **Aktion:** Durch Auswählen der Aktion **Ressourcengruppe erstellen oder aktualisieren** werden zwei Aktionen ausgeführt: 1. Sie erstellt eine Ressourcengruppe, falls ein neuer Ressourcengruppenname angegeben wurde. 2. Sie stellt die angegebene Vorlage bereit.
    * **Ressourcengruppe**: Geben Sie einen neuen Namen für die Ressourcengruppe ein. Beispiel: **AzureRmPipeline-rg**.
    * **Standort**: Wählen Sie einen Ort für die Ressourcengruppe (beispielsweise **USA, Mitte**).
    * **Speicherort der Vorlage**: Wählen Sie **Verknüpftes Artefakt** aus. Dies bedeutet, dass die Aufgabe direkt im verbundenen Repository nach der Vorlagendatei sucht.
    * **Vorlage**: Geben Sie **CreateWebApp/azuredeploy.json** ein. Wenn Sie den Ordnernamen und den Dateinamen geändert haben, müssen Sie diesen Wert ändern.
    * **Vorlagenparameter**: Lassen Sie dieses Feld leer. Sie geben die Parameterwerte in **Vorlagenparameter überschreiben** an.
    * **Vorlagenparameter überschreiben**: Geben Sie **-projectName [Projektnamen eingeben] -linkedTemplateUri [URL der verknüpften Vorlage eingeben]** ein. Ersetzen Sie den Projektnamen und die URL der verknüpften Vorlage. Die URL der verknüpften Vorlage ist das, was Sie am Ende von [Erstellen eines GitHub-Repositorys](#create-a-github-repository) aufgeschrieben haben. Sie beginnt mit **https://raw.githubusercontent.com** .
    * **Bereitstellungsmodus**: Wählen Sie **Inkrementell** aus.
    * **Bereitstellungsname**: Geben Sie **DeployPipelineTemplate** ein. Wählen Sie **Erweitert** aus, damit **Bereitstellungsname** angezeigt wird.

    ![Screenshot: Seite „ARM-Vorlagenbereitstellung“ mit den eingegebenen erforderlichen Werten](./media/deployment-tutorial-pipeline/resource-manager-template-pipeline-configure.png)

1. Wählen Sie **Hinzufügen**.

    Weitere Informationen zu dieser Aufgabe finden Sie unter [Aufgabe „Bereitstellung einer Azure-Ressourcengruppe“](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)und [Aufgabe „Azure Resource Manager-Vorlagenbereitstellung“](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md).

    Die YML-Datei sollte in etwa wie folgt aussehen:

    ![Screenshot: Seite „Überprüfen“ mit der neuen Pipeline mit dem Titel „Pipeline-YAML überprüfen“](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-yml.png)

1. Klicken Sie auf **Speichern und ausführen**.
1. Wählen Sie im Bereich **Speichern und ausführen** erneut die Option **Speichern und ausführen** aus. Eine Kopie der YAML-Datei wird im verbundenen Repository gespeichert. Sie können die YAML-Datei anzeigen, indem Sie zu Ihrem Repository navigieren.
1. Vergewissern Sie sich, dass die Pipeline erfolgreich ausgeführt wird.

    ![Azure Resource Manager/Azure DevOps/Azure Pipelines: YAML](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Überprüfen der Bereitstellung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Öffnen Sie die Ressourcengruppe. Der Name entspricht der Angabe aus der YAML-Pipelinedatei.  Es sollte ein einzelnes Speicherkonto erstellt werden.  Der Name des Speicherkontos beginnt mit **store**.
1. Wählen Sie den Namen des Speicherkontos aus, um es zu öffnen.
1. Wählen Sie **Eigenschaften** aus. Beachten Sie, dass unter **Replikation** die Option **Lokal redundanter Speicher (LRS)** ausgewählt ist.

## <a name="update-and-redeploy"></a>Aktualisieren und erneutes Bereitstellen

Wenn Sie die Vorlage aktualisieren und die Änderungen in das Remoterepository pushen, werden die Ressourcen (in diesem Fall: das Speicherkonto) automatisch durch die Pipeline aktualisiert.

1. Öffnen Sie **linkedStorageAccount.json** in Ihrem lokalen Repository in Visual Studio Code oder einem beliebigen Text-Editor.
1. Aktualisieren Sie den Standardwert (**defaultValue**) von **storageAccountType** auf **Standard_GRS**. Der folgende Screenshot zeigt dies:

    ![Azure Resource Manager/Azure DevOps/Azure Pipelines: YAML-Aktualisierung](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-update-yml.png)

1. Speichern Sie die Änderungen.
1. Führen Sie in Git Bash/Shell die folgenden Befehle aus, um die Änderungen in das Remoterepository zu pushen:

    ```bash
    git pull origin master
    git add .
    git commit -m "Update the storage account type."
    git push origin master
    ```

    Der erste Befehl (pull) synchronisiert das lokale Repository mit dem Remoterepository. Die YAML-Pipelinedatei wurde nur dem Remoterepository hinzugefügt. Wenn Sie den Pull-Befehl ausführen, wird eine Kopie der YAML-Datei in den lokalen Branch heruntergeladen.

    Mit dem vierten Befehl (push) wird die überarbeitete Datei „linkedStorageAccount.json“ in das Remoterepository hochgeladen. Nachdem der Masterbranch des Remoterepositorys aktualisiert wurde, wird die Pipeline erneut ausgelöst.

Sehen Sie sich zur Überprüfung der Änderungen die Replikationseigenschaft des Speicherkontos an.  Eine entsprechende Anleitung finden Sie unter [Überprüfen der Bereitstellung](#verify-the-deployment).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Azure-Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen.

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppe** aus.
2. Geben Sie den Namen der Ressourcengruppe in das Feld **Nach Name filtern** ein.
3. Klicken Sie auf den Namen der Ressourcengruppe.
4. Wählen Sie **Ressourcengruppe löschen** aus dem Menü ganz oben aus.

Falls gewünscht, können Sie auch das GitHub-Repository und das Azure DevOps-Projekt löschen.

## <a name="next-steps"></a>Nächste Schritte

Herzlichen Glückwunsch, Sie haben dieses Tutorial zur Bereitstellung von Resource Manager-Vorlagen abgeschlossen. Informieren Sie uns im Abschnitt „Feedback“, wenn Sie Kommentare oder Vorschläge haben. Vielen Dank!
Sie sind bereit, sich mit fortgeschritteneren Konzepten für Vorlagen zu befassen. Das nächste Tutorial geht detaillierter auf die Verwendung der Referenzdokumentation von Vorlagen zum Definieren von bereitzustellenden Ressourcen ein.

> [!div class="nextstepaction"]
> [Nutzen der Vorlagenreferenz](./template-tutorial-use-template-reference.md)
