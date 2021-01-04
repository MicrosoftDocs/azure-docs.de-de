---
title: CI/CD mit Azure Pipelines und Vorlagen
description: Beschreibt die Konfiguration von Continuous Integration in Azure Pipelines mithilfe von Azure Resource Manager-Vorlagen. Es wird gezeigt, wie Sie ein PowerShell-Skript verwenden oder Dateien an einen Stagingspeicherort kopieren und von dort aus bereitstellen.
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: 86ad2839375b73bf9595cf3369960e614ec03e67
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93233813"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>Integrieren von ARM-Vorlagen in Azure Pipelines

Sie können Azure Resource Manager-Vorlagen (ARM-Vorlagen) in Azure Pipelines integrieren für Continuous Integration und Continuous Deployment (CI/CD). Das Tutorial [Continuous Integration von ARM-Vorlagen in Azure Pipelines](deployment-tutorial-pipeline.md) zeigt, wie Sie die [ARM-Vorlagenbereitstellungsaufgabe](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md) verwenden, um eine Vorlage aus Ihrem GitHub-Repository bereitzustellen. Diese Vorgehensweise funktioniert, wenn Sie eine Vorlage direkt aus einem Repository bereitstellen möchten.

In diesem Artikel lernen Sie zwei Methoden kennen, um Vorlagen mit Azure Pipelines bereitzustellen. In diesem Artikel werden die folgenden Aufgaben erläutert:

* **Hinzufügen einer Ausgabe, die ein Azure PowerShell-Skript ausführt**. Diese Option bietet einen konsistenten Entwicklungslebenszyklus, da Sie dasselbe Skript verwenden können, das Sie zur Ausführung lokaler Tests verwendet haben. Ihr Skript stellt die Vorlage bereit, kann aber auch andere Vorgänge ausführen, z. B. das Abrufen von Werten, die als Parameter verwendet werden sollen.

   Visual Studio stellt das [Azure Resource Group-Projekt](create-visual-studio-deployment-project.md) bereit, das ein PowerShell-Skript enthält. Das Skript stellt Artefakte aus Ihrem Projekt einem Speicherkonto zur Verfügung, auf das der Resource Manager zugreifen kann. Artefakte sind Elemente in Ihrem Projekt, wie z.B. verknüpfte Vorlagen, Skripte und Binärdateien von Anwendungen. Wenn Sie das Skript aus dem Projekt weiterhin verwenden möchten, verwenden Sie die in diesem Artikel gezeigte PowerShell-Skriptaufgabe.

* **Hinzufügen von Aufgaben zum Kopieren und Bereitstellen von Tasks**. Diese Option bietet eine praktische Alternative zum Projektskript. Sie konfigurieren zwei Aufgaben in der Pipeline. Eine Aufgabe stellt die Artefakte an einem zugänglichen Speicherort zur Verfügung. Die andere Aufgabe stellt die Vorlage von diesem Ort aus bereit.

## <a name="prepare-your-project"></a>Vorbereiten Ihres Projekts

In diesem Artikel wird vorausgesetzt, dass Ihre ARM-Vorlage und Ihre Azure DevOps-Organisation für die Erstellung der Pipeline bereit sind. Die folgenden Schritte zeigen, wie Sie sicherstellen können, dass Sie bereit sind:

* Sie haben eine Azure DevOps-Organisation. Sollten Sie über keine Organisation verfügen, [können Sie kostenlos eine erstellen](/azure/devops/pipelines/get-started/pipelines-sign-up). Wenn Ihr Team bereits über eine Azure DevOps-Organisation verfügt, stellen Sie sicher, dass Sie Administrator des Azure DevOps-Projekts sind, das Sie verwenden möchten.

* Sie haben eine [Dienstverbindung](/azure/devops/pipelines/library/connect-to-azure) zu Ihrem Azure-Abonnement konfiguriert. Die Aufgaben in der Pipeline werden unter der Identität des Dienstprinzipals ausgeführt. Die Schritte zum Erstellen der Verbindung finden Sie unter [Erstellen eines DevOps-Projekts](deployment-tutorial-pipeline.md#create-a-devops-project).

* Sie haben eine [ARM-Vorlage](quickstart-create-templates-use-visual-studio-code.md), die die Infrastruktur für Ihr Projekt definiert.

## <a name="create-pipeline"></a>Erstellen der Pipeline

1. Wenn Sie noch keine Pipeline hinzugefügt haben, müssen Sie eine neue Pipeline erstellen. Wählen Sie aus Ihrer Azure DevOps-Organisation **Pipelines** und **Neue Pipeline** aus.

   ![Hinzufügen einer neuen Pipeline](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Geben Sie an, wo Ihr Code gespeichert ist. Die folgende Abbildung zeigt die Auswahl von **Azure Repos-Git**.

   ![Auswählen der Codequelle](./media/add-template-to-azure-pipelines/select-source.png)

1. Wählen Sie aus dieser Quelle das Repository aus, das den Code für Ihr Projekt enthält.

   ![Repository auswählen](./media/add-template-to-azure-pipelines/select-repo.png)

1. Wählen Sie den zu erstellenden Pipelinetyp aus. Sie können **Starterpipeline** auswählen.

   ![Pipeline auswählen](./media/add-template-to-azure-pipelines/select-pipeline.png)

Sie können jetzt entweder die Azure PowerShell-Aufgabe oder die Aufgaben zum Kopieren und Bereitstellen der Datei hinzufügen.

## <a name="azure-powershell-task"></a>Azure PowerShell-Aufgabe

In diesem Abschnitt wird gezeigt, wie Sie die Continuous Deployment mithilfe einer einzelnen Aufgabe konfigurieren, die das PowerShell-Skript in Ihrem Projekt ausführt. Wenn Sie ein PowerShell-Skript benötigen, das eine Vorlage bereitstellt, finden Sie weitere Informationen unter [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) oder [Deploy-AzureResourceGroup.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzureResourceGroup.ps1).

Die folgende YAML-Datei erstellt eine [Azure PowerShell-Aufgabe](/azure/devops/pipelines/tasks/deploy/azure-powershell):

```yml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: AzurePowerShell@5
  inputs:
    azureSubscription: 'script-connection'
    ScriptType: 'FilePath'
    ScriptPath: './Deploy-AzTemplate.ps1'
    ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
    azurePowerShellVersion: 'LatestVersion'
```

Wenn Sie die Aufgabe auf `AzurePowerShell@5` festlegen, verwendet die Pipeline das [Az module](/powershell/azure/new-azureps-module-az). Wenn Sie das AzureRM-Modul in Ihrem Skript verwenden, legen Sie die Aufgabe auf `AzurePowerShell@3` fest.

```yaml
steps:
- task: AzurePowerShell@3
```

Geben Sie für `azureSubscription` den Namen der von Ihnen erstellten Dienstverbindung an.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Geben Sie für `scriptPath` den relativen Pfad von der Pipelinedatei zu Ihrem Skript an. Den Pfade finden Sie in Ihrem Repository.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Geben Sie in `ScriptArguments` alle für Ihr Skript erforderlichen Parameter an. Das folgende Beispiel zeigt einige Parameter für ein Skript, aber Sie müssen die Parameter für Ihr Skript anpassen.

```yaml
ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
```

Bei der Auswahl von **Speichern** wird die Buildpipeline automatisch ausgeführt. Gehen Sie zurück zur Zusammenfassung Ihrer Buildpipeline und schauen Sie sich den Status an.

![Anzeigen der Ergebnisse](./media/add-template-to-azure-pipelines/view-results.png)

Sie können die aktuell laufende Pipeline auswählen, um Details zu den Aufgaben anzuzeigen. Nach Abschluss werden die Ergebnisse für jeden Schritt angezeigt.

## <a name="copy-and-deploy-tasks"></a>Aufgaben zum Kopieren und Bereitstellen

In diesem Abschnitt wird gezeigt, wie Sie Continuous Deployment mithilfe von zwei Aufgaben konfigurieren. Die erste Aufgabe stellt die Artefakte in einem Speicherkonto bereit, und die zweite Aufgabe stellt die Vorlage bereit.

Um Dateien in ein Speicherkonto zu kopieren, muss dem Dienstprinzipal für die Dienstverbindung die Rolle „Mitwirkender von Speicherblobdaten“ oder „Besitzer von Speicherblobdaten“ zugewiesen werden. Weitere Informationen finden Sie unter [Erste Schritte mit AzCopy](../../storage/common/storage-use-azcopy-v10.md).

Die folgende YAML zeigt die [Aufgabe für den Azure-Dateikopiervorgang](/azure/devops/pipelines/tasks/deploy/azure-file-copy).

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
```

Diese Aufgabe umfasst mehrere Teile, die Sie für Ihre Umgebung überarbeiten müssen. `SourcePath` gibt den Speicherort der Artefakte relativ zur Pipelinedatei an.

```yaml
SourcePath: '<path-to-artifacts>'
```

Geben Sie für `azureSubscription` den Namen der von Ihnen erstellten Dienstverbindung an.

```yaml
azureSubscription: '<your-connection-name>'
```

Geben Sie für Speicher- und Containername die Namen des Speicherkonto und des Containers an, den Sie für die Speicherung der Artefakte verwenden möchten. Das Speicherkonto muss bereits vorhanden sein.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

Nachdem Sie die Aufgabe „Datei kopieren“ erstellt haben, sind Sie bereit, um die Aufgabe hinzuzufügen, um die bereitgestellte Vorlage bereitzustellen.

Der folgende YAML-Code zeigt die [Aufgabe für die Azure Resource Manager-Vorlagenbereitstellung](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md):

```yaml
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

Diese Aufgabe umfasst mehrere Teile, die näher betrachtet werden sollten.

- `deploymentScope`: Wählen Sie aus den folgenden Optionen den Umfang der Bereitstellung aus: `Management Group`, `Subscription` und `Resource Group`. Weitere Informationen zum Umfang finden Sie unter [Bereitstellungsumfang](deploy-rest.md#deployment-scope).

- `azureResourceManagerConnection`: Geben Sie den Namen der von Ihnen erstellten Dienstverbindung an.

- `subscriptionId`: Geben Sie die Zielabonnement-ID an. Diese Eigenschaft gilt nur für den Bereitstellungsumfang „Ressourcengruppe“ und „Abonnement“.

- `resourceGroupName` und `location`: Geben Sie den Namen und den Standort der Ressourcengruppe an, in der die Bereitstellung erfolgen soll. Diese Aufgabe erstellt die Ressourcengruppe, wenn diese noch nicht vorhanden ist.

   ```yml
   resourceGroupName: '<resource-group-name>'
   location: '<location>'
   ```

- `csmFileLink`: Geben Sie den Link für die bereitgestellte Vorlage an. Verwenden Sie beim Festlegen des Werts Variablen, die von der Aufgabe „Datei kopieren“ zurückgegeben werden. Im folgenden Beispiel wird eine Verknüpfung mit der Vorlage namens „mainTemplate.json“ hergestellt. Der Ordner mit dem Namen **templates** (Vorlagen) ist enthalten, da dies der Ort ist, an den die Dateikopieraufgabe die Datei kopiert hat. Geben Sie in Ihrer Pipeline den Pfad zu Ihrer Vorlage und deren Namen an.

   ```yml
   csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
   ```

Ihre Pipeline sieht wie folgt aus:

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

Bei der Auswahl von **Speichern** wird die Buildpipeline automatisch ausgeführt. Gehen Sie zurück zur Zusammenfassung Ihrer Buildpipeline und schauen Sie sich den Status an.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von ARM-Vorlagen mit GitHub Actions finden Sie unter [Bereitstellen von Azure Resource Manager-Vorlagen mithilfe von GitHub Actions](deploy-github-actions.md).
