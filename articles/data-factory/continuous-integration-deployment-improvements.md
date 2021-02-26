---
title: Automatisiertes Veröffentlichen für Continuous Integration und Delivery
description: Erfahren Sie, wie Sie automatisch für Continuous Integration und Delivery veröffentlichen.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: b5becd8ddaf74ab2acd059054a095ce9d21c178f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366839"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Automatisiertes Veröffentlichen für Continuous Integration und Delivery

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Übersicht

Bei Continuous Integration werden sämtliche Änderungen an Ihrer Codebasis automatisch und so früh wie möglich getestet. Der Continuous Delivery-Prozess folgt auf das Testen während des Continuous Integration-Prozesses. Änderungen werden dabei in ein Staging- oder Produktionssystem gepusht.

In Azure Data Factory bedeuten Continuous Integration und Continuous Delivery (CI/CD), dass Data Factory-Pipelines von einer Umgebung (Entwicklung, Test, Produktion) in eine andere verschoben werden. Azure Data Factory nutzt [Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/overview.md) zum Speichern der Konfiguration ihrer verschiedenen ADF-Entitäten (Pipelines, Datasets, Datenflüsse usw.). Es gibt zwei empfohlene Methoden zum Höherstufen einer Data Factory in eine andere Umgebung:

- Automatisierte Bereitstellung über Integration von Data Factory in [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines)
- Manuelles Hochladen einer Resource Manager-Vorlage über Integration der Data Factory-Benutzeroberfläche in Azure Resource Manager.

Weitere Informationen finden Sie unter [Continuous Integration und Continuous Delivery in Azure Data Factory](continuous-integration-deployment.md).

In diesem Artikel liegt der Fokus auf den Verbesserungen bei Continuous Deployment und auf dem Feature für die automatisierte Veröffentlichung für CI/CD.

## <a name="continuous-deployment-improvements"></a>Verbesserungen bei Continuous Deployment

Im Feature „Automatisierte Veröffentlichung“ sind die Funktionen *Alle überprüfen* und *Azure Resource Manager-Vorlage (ARM) exportieren* von der ADF-Benutzeroberfläche zusammengefasst. Die Logik wird über das öffentlich verfügbare npm-Paket [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) bereitgestellt. So können Sie diese Aktionen programmgesteuert initiieren und müssen nicht zur ADF-Benutzeroberfläche wechseln und auf Schaltflächen klicken. So werden Ihre CI/CD-Pipelines zu echten Continuous Integration-Prozessen.

### <a name="current-cicd-flow"></a>Aktueller CI/CD-Flow

1. Alle Benutzer nehmen Änderungen in ihren privaten Branches vor.
2. Das Pushen zum Masterbranch ist nicht erlaubt. Die Benutzer müssen einen PR an den Master erstellen, um Änderungen vorzunehmen.
3. Benutzer müssen die Benutzeroberfläche von Azure Data Factory laden und auf „Veröffentlichen“ klicken, um Änderungen an Data Factory bereitzustellen und die ARM-Vorlagen im Branch für die Veröffentlichung zu generieren.
4. Die DevOps-Releasepipeline ist so konfiguriert, dass sie jedes Mal, wenn eine neue Änderung in den Veröffentlichungsbranch gepusht wird, ein neues Release erstellt und die ARM-Vorlage bereitstellt.

![Aktueller CI/CD-Flow](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Manueller Schritt

Im aktuellen CI/CD-Flow ist die Benutzeroberfläche das Bindeglied zum Erstellen der ARM-Vorlage. Daher müssen Benutzer zur ADF-Benutzeroberfläche wechseln und manuell auf „Veröffentlichen“ klicken, um das Generieren der ARM-Vorlage zu starten und sie in den Veröffentlichungsbranch zu pushen. Dies ist ein bisschen umständlich.

### <a name="the-new-cicd-flow"></a>Der neue CI/CD-Flow

1. Alle Benutzer nehmen Änderungen in ihren privaten Branches vor.
2. Das Pushen zum Masterbranch ist nicht erlaubt. Die Benutzer müssen einen PR an den Master erstellen, um Änderungen vorzunehmen.
3. **Der Buildvorgang der Azure DevOps-Pipeline wird bei jedem neuen Commit an den Masterbranch ausgeführt. Die Ressourcen werden überprüft, und es wird eine ARM-Vorlage als Artefakt generiert, wenn die Überprüfung erfolgreich ist.**
4. Die DevOps-Releasepipeline ist so konfiguriert, dass sie jedes Mal ein neues Release erstellt und die ARM-Vorlage bereitstellt, wenn ein neuer Build verfügbar ist. 

![Neuer CI/CD-Flow](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>Was hat sich geändert?

- Der neue Buildprozess nutzt eine DevOps-Buildpipeline.
- Die Buildpipeline verwendet das npm-Paket ADFUtilities, das alle Ressourcen überprüft und die ARM-Vorlagen generiert (einzelne und verknüpfte Vorlagen).
- Die Buildpipeline ist für das Überprüfen von ADF-Ressourcen und das Generieren der ARM-Vorlage zuständig, sodass die ADF-Benutzeroberfläche (Schaltfläche „Veröffentlichen“) nicht mehr benötigt wird.
- Die DevOps-Releasedefinition nutzt nun diese neue Buildpipeline anstelle des Git-Artefakts.

> [!NOTE]
> Sie können weiterhin das bestehende Verfahren (adf_publish-Branch) verwenden oder den neuen Flow nutzen. Beide Verfahren werden unterstützt. 

## <a name="package-overview"></a>Paketübersicht

Im Paket sind zurzeit zwei Befehle verfügbar:
- Exportieren einer ARM-Vorlage
- Überprüfen

### <a name="export-arm-template"></a>Exportieren einer ARM-Vorlage

Führen Sie „npm run start export <rootFolder> <factoryId> [Ausgabeordner]“ aus, um die ARM-Vorlage mit den Ressourcen eines bestimmten Ordners zu exportieren. Dieser Befehl führt vor dem Generieren der ARM-Vorlage auch eine Validierung aus. Unten ist ein Beispiel aufgeführt:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- „RootFolder“ ist ein obligatorisches Feld, das den Speicherort der Data Factory-Ressourcen darstellt.
- „FactoryId“ ist ein obligatorisches Feld, das die Data Factory-Ressourcen-ID im folgenden Format darstellt: „/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>“.
- „OutputFolder“ ist ein optionaler Parameter, der den relativen Pfad zum Speichern der generierten ARM-Vorlage angibt.
 
> [!NOTE]
> Die generierte ARM-Vorlage wird nicht in der `Live`-Version der Factory veröffentlicht. Die Bereitstellung sollte mithilfe einer CI/CD-Pipeline erfolgen. 
 

### <a name="validate"></a>Überprüfen

Führen Sie „npm run start validate <rootFolder> <factoryId>“ aus, um alle Ressourcen eines bestimmten Ordners zu überprüfen. Unten ist ein Beispiel aufgeführt:
    
```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- „RootFolder“ ist ein obligatorisches Feld, das den Speicherort der Data Factory-Ressourcen darstellt.
- „FactoryId“ ist ein obligatorisches Feld, das die Data Factory-Ressourcen-ID im folgenden Format darstellt: „/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>“.


## <a name="create-an-azure-pipeline"></a>Erstellen einer Azure-Pipeline

npm-Pakete können zwar auf verschiedene Weise genutzt werden, aber einer der Hauptvorteile ist die Nutzung über eine [Azure-Pipeline](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0). Bei jedem Merge in den Kollaborationsbranch kann eine Pipeline ausgelöst werden, die zuerst den gesamten Code überprüft und dann die ARM-Vorlage in ein [Buildartefakt](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) exportiert, das von einer Releasepipeline verwendet werden kann. **Der Unterschied zum aktuellen CI/CD-Prozess besteht darin, dass Sie in der Releasepipeline nicht auf den vorhandenen `adf_publish`-Branch, sondern auf dieses Artefakt verweisen.**

Führen Sie zum Einstieg die folgenden Schritte aus:

1.  Öffnen Sie ein Azure DevOps-Projekt, und navigieren Sie zu „Pipelines“. Wählen Sie „Neue Pipeline“ aus.

    ![Neue Pipeline](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
2.  Wählen Sie das Repository aus, in dem Sie das YAML-Skript Ihrer Pipeline speichern möchten. Es wird empfohlen, die Datei im Ordner *build* innerhalb desselben Repositorys wie Ihre ADF-Ressourcen zu speichern. Stellen Sie sicher, dass im Repository auch eine Datei **package.json** vorhanden ist, die den Paketnamen enthält (wie im folgenden Beispiel gezeigt).

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.2"
        }
    } 
    ```
    
3.  Wählen Sie *Starterpipeline* aus. Wenn Sie die YAML-Datei hochgeladen oder zusammengeführt haben (wie im Beispiel unten), können Sie auch direkt darauf verweisen und sie bearbeiten. 

    ![Starterpipeline](media/continuous-integration-deployment-improvements/starter-pipeline.png) 

    ```yaml
    # Sample YAML file to validate and export an ARM template into a Build Artifact
    # Requires a package.json file located in the target repository
    
    trigger:
    - main #collaboration branch
    
    pool:
      vmImage: 'ubuntu-latest'
    
    steps:
    
    # Installs Node and the npm packages saved in your package.json file in the build
    
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    
    - task: Npm@1
      inputs:
        command: 'install'
        verbose: true
      displayName: 'Install npm package'
    
    # Validates all of the ADF resources in the repository. You will get the same validation errors as when "Validate All" is clicked
    # Enter the appropriate subscription and name for the source factory 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder. Same as clicking "Publish" from UX
    # The ARM template generated is not published to the ‘Live’ version of the factory. Deployment should be done using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the Artifact to be used as a source for a release pipeline
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

4.  Geben Sie den YAML-Code ein. Es wird empfohlen, die YAML-Datei als Ausgangspunkt zu verwenden.
5.  Speichern Sie das Projekt, und führen Sie es aus. Wenn Sie die YAML-Datei verwenden, wird diese jedes Mal ausgelöst, wenn der Hauptbranch aktualisiert wird.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Continuous Integration und Continuous Delivery in Data Factory: 

- [Continuous Integration und Continuous Delivery in Azure Data Factory](continuous-integration-deployment.md).
