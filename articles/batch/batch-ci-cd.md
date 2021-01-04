---
title: Verwenden von Azure Pipelines zum Erstellen und Bereitstellen von HPC-Lösungen
description: Erfahren Sie, wie Sie eine Build-/Releaseepipeline für eine in Azure Batch ausgeführte HPC-Anwendung bereitstellen.
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: how-to
ms.openlocfilehash: e87be0db65cf12a265566e0c05815722ce3cc609
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578874"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Verwenden von Azure Pipelines zum Erstellen und Bereitstellen von HPC-Lösungen

Azure DevOps Services bietet eine Reihe von Tools, die von Entwicklungsteams beim Erstellen einer benutzerdefinierten Anwendung verwendet werden. Tools von Azure DevOps dienen zum Übersetzen in das automatische Erstellen und Testen von Hochleistungscomputelösungen. In diesem Artikel wird die Einrichtung einer Continuous Integration (CI) und Continuous Deployment (CD) mit Azure Pipelines für eine unter Azure Batch bereitgestellte Hochleistungscomputelösung veranschaulicht.

Azure Pipelines bietet eine Reihe von modernen CI-/CD-Prozessen für das Erstellen, Bereitstellen, Testen und Überwachen von Software. Diese Prozesse beschleunigen die Auslieferung Ihrer Software, sodass Sie sich auf den Code konzentrieren können, anstatt sich um die Infrastruktur und sonstige Abläufe kümmern zu müssen.

## <a name="create-an-azure-pipeline"></a>Erstellen einer Azure Pipeline

In diesem Beispiel erstellen wir eine Build- und Releaseepipeline, um eine Azure Batch-Infrastruktur bereitzustellen und ein Anwendungspaket herauszugeben. Vorausgesetzt, dass der Code lokal entwickelt wird, ist dies der Ablauf der allgemeinen Bereitstellung:

![Diagramm des Bereitstellungsablaufs in unserer Pipeline](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Einrichten

Um den Schritten in diesem Artikel folgen zu können, benötigen Sie eine Azure DevOps-Organisation und ein Teamprojekt.

* [Schnellstart: Erstellen einer Organisation](/azure/devops/organizations/accounts/create-organization)
* [Erstellen eines Projekts in Azure DevOps](/azure/devops/organizations/projects/create-project)

### <a name="source-control-for-your-environment"></a>Quellcodeverwaltung für Ihre Umgebung

Mit der Quellcodeverwaltung können Teams Änderungen an der Codebasis nachverfolgen und frühere Versionen des Codes überprüfen.

Die Quellcodeverwaltung hält typischerweise mit dem Softwarecode Schritt. Wie sieht die zugrunde liegende Infrastruktur aus? Damit kommen wir zu Infrastruktur als Code, wo wir Azure Resource Manager-Vorlagen oder andere Open-Source-Alternativen verwenden, um deklarativ unsere zugrunde liegende Infrastruktur zu definieren.

Dieses Beispiel basiert stark auf einer Reihe von Resource Manager-Vorlagen (JSON-Dokumente) und vorhandenen Binärdateien. Sie können diese Beispiele in das Repository kopieren und an Azure DevOps pushen.

Die in diesem Beispiel verwendete Codebasisstruktur ähnelt dem Folgenden:

* Ein **arm-templates**-Ordner mit einer Reihe von Azure Resource Manager-Vorlagen. Die Vorlagen werden in diesem Artikel erläutert.
* Ein **client-application**-Ordner, der eine Kopie des Beispiels [Azure Batch .NET File Processing with ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) (Azure Batch .NET-Dateiverarbeitung mit ffmpeg) ist. Dies ist für diesen Artikel nicht erforderlich.
* Ein **hpc-application**-Ordner, der die Windows-64-Bit-Version von [ffmpeg 4.3.1](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08) ist.
* Ein **pipelines**-Ordner. Dieser enthält eine YAML-Datei mit dem Konzept unseres Buildprozesses. Dies wird in diesem Artikel erläutert.

In diesem Abschnitt wird davon ausgegangen, dass Sie mit der Versionskontrolle und dem Entwerfen von Resource Manager-Vorlagen vertraut sind. Wenn Sie mit diesen Konzepten nicht vertraut sind, finden Sie auf den folgenden Seiten weitere Informationen.

* [Was ist Quellcodeverwaltung?](/azure/devops/user-guide/source-control)
* [Grundlegendes zur Struktur und Syntax von Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Azure-Ressourcen-Manager-Vorlagen

In diesem Beispiel nutzen wir mehrere Resource Manager-Vorlagen, um unsere Lösung bereitstellen. Zu diesem Zweck verwenden wir eine Reihe von Funktionsvorlagen (ähnlich wie Einheiten oder Module), um einen bestimmten Teil der Funktionalität zu implementieren. Wir verwenden auch eine End-to-End-Lösungsvorlage, die diese zugrunde liegenden Funktionen miteinander verbindet. Dieser Ansatz bietet einige Vorteile:

* Die zugrunde liegenden Funktionsvorlagen können einzeln getest werden.
* Die zugrunde liegenden Funktionsvorlagen können innerhalb einer Organisation als Standard definiert und in mehreren Lösungen wiederverwendet werden.

In diesem Beispiel wird eine End-to-End-Lösungsvorlage (deployment.json) verwendet, die drei Vorlagen bereitstellt. Die zugrunde liegenden Vorlagen sind Funktionsvorlagen für die Bereitstellung eines bestimmten Aspekts der Lösung.

![Beispiel für die verknüpfte Vorlagenstruktur mit Verwendung von Azure Resource Manager-Vorlagen](media/batch-ci-cd/ARMTemplateHierarchy.png)

Die erste Vorlage, die wir betrachten, ist für ein Azure Storage-Konto bestimmt. Unsere Lösung erfordert ein Speicherkonto, um die Anwendung in unserem Batch-Konto bereitzustellen. Das Referenzhandbuch für [Microsoft.Storage-Ressourcentypen](/azure/templates/microsoft.storage/allversions) sollte beim Erstellen der Resource Manager-Vorlagen für Storage-Konten berücksichtigt werden.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

Als Nächstes werden wir die Vorlage des Azure Batch-Kontos betrachten. Das Azure Batch-Konto fungiert als Plattform zum Pools (Gruppen von Computern) übergreifenden Ausführen zahlreicher Anwendungen. Das Referenzhandbuch für [Microsoft.Batch-Ressourcentypen](/azure/templates/microsoft.batch/allversions) sollte beim Erstellen der Resource Manager-Vorlagen für Batch-Konten berücksichtigt werden.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

Die nächste Vorlage zeigt ein Beispiel für das Erstellen eines Azure Batch-Pools (die Back-End-Computer zum Verarbeiten unserer Anwendungen). Das Referenzhandbuch für [Microsoft.Batch-Ressourcentypen](/azure/templates/microsoft.batch/allversions) sollte beim Erstellen der Resource Manager-Vorlagen für Batch-Kontenpools berücksichtigt werden.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

Schließlich haben wir eine Vorlage, die ähnlich wie ein Orchestrator fungiert. Diese Vorlage dient zum Bereitstellen von Funktionsvorlagen.

Sie können auch in einem separaten Artikel mehr über das [Erstellen verknüpfter Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/deployment-tutorial-linked-template.md) erfahren.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

#### <a name="the-hpc-solution"></a>Die HPC-Lösung

Infrastruktur und Software können als Code definiert und im selben Repository verbunden werden.

Für diese Lösung wird ffmpeg als Anwendungspaket verwendet. Das ffmpeg-Paket kann [hier](https://www.videohelp.com/software?d=ffmpeg-3.3.4-win64-static.zip) heruntergeladen werden.

![Git-Repository-Beispielstruktur](media/batch-ci-cd/git-repository.jpg)

Dieses Repository hat vier Hauptabschnitte:

* Der **arm-templates**-Ordner, in dem unsere Infrastruktur als Code gespeichert wird.
* Der **hpc-application**-Ordner mit den Binärdateien für ffmpeg.
* Der **pipelines**-Ordner, der die Definition für unsere Buildpipeline enthält.
* **Optional**: Der **client-application**-Ordner zum Speichern von Code für die .NET Anwendung. Wir verwenden dies nicht im Beispiel, aber in Ihrem eigenen Projekt können Sie die HPC-Batch-Anwendung über eine Clientanwendung ausführen.

> [!NOTE]
> Dies ist nur ein Beispiel einer Struktur einer Codebasis. Dieser Ansatz soll veranschaulichen, dass Anwendungs-, Infrastruktur- und Pipelinecode im selben Repository gespeichert werden.

Nun ist der Quellcode eingerichtet und wir können mit dem ersten Build beginnen.

## <a name="continuous-integration"></a>Continuous Integration

Mit [Azure Pipelines](/azure/devops/pipelines/get-started/) innerhalb von Azure DevOps Services können Sie eine Erstellungs-, Test- und Bereitstellungspipeline für Ihre Anwendungen implementieren.

In dieser Phase der Pipeline werden Tests in der Regel zum Überprüfen von Code und Erstellen der entsprechenden Teile der Software ausgeführt. Anzahl und Typen der Tests sowie weitere Aufgaben, die Sie ausführen, hängen von Ihrer übergeordneten Build- und Releasestrategie ab.

## <a name="preparing-the-hpc-application"></a>Vorbereiten der HPC-Anwendung

In diesem Beispiel konzentrieren wir uns auf den **hpc-application**-Ordner. Der **hpc-application**-Ordner ist die ffmpeg-Software, die vom Azure Batch-Konto aus ausgeführt wird.

1. Navigieren Sie zum Abschnitt „Builds“ von Azure Pipelines in Ihrer Azure DevOps-Organisation. Erstellen Sie eine **Neue Pipeline**.

    ![Erstellen einer neuen Buildpipeline](media/batch-ci-cd/new-build-pipeline.jpg)

1. Eine Buildpipeline kann auf zwei Arten erstellt werden:

    a. [Verwenden des Visual Designer](/azure/devops/pipelines/get-started-designer). Klicken Sie zu dessen Verwendung auf der Seite **Neue Pipeline** auf „Verwenden Sie den visuellen Designer“.

    b. [Verwenden des YAML-Builds](/azure/devops/pipelines/get-started-yaml). Sie können eine neue YAML-Pipeline erstellen, indem Sie auf der Seite „Neue Pipeline“ auf die Option für Azure Repos oder GitHub klicken. Alternativ können Sie das folgende Beispiel in Ihrer Quellcodeverwaltung speichern und auf eine vorhandene YAML-Datei verweisen, indem Sie auf Visual Designer klicken und dann die YAML-Vorlage verwenden.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. Sobald der Build wie erforderlich konfiguriert ist, wählen Sie **Speichern und in Warteschlange einreihen** aus. Wenn Sie (im **Trigger**-Abschnitt) fortlaufende Integration aktiviert haben, wird der Build automatisch ausgelöst, wenn gemäß der im Build festgelegten Bedingungen ein neuer Commit an das Repository vorgenommen wird.

    ![Beispiel für eine vorhandene Buildpipeline](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Um Liveupdates über den Fortschritt des Builds in Azure DevOps anzuzeigen, navigieren Sie zum **Build**-Abschnitt von Azure Pipelines. Wählen Sie den entsprechenden Build aus der Builddefinition aus.

    ![Anzeigen von Liveausgaben von Ihrem Build](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Wenn Sie eine Clientanwendung zum Ausführen der HPC-Batchanwendung verwenden, müssen Sie eine separate Builddefinition für diese Anwendung erstellen. Sie finden eine Reihe von Gewusst-wie-Anleitungen in der Dokumentation zu [Azure Pipelines](/azure/devops/pipelines/get-started/index).

## <a name="continuous-deployment"></a>Kontinuierliche Bereitstellung

Azure Pipelines hat auch stets Ihre Anwendung und die zugrunde liegende Infrastruktur bereitgestellt. Die Komponente [Releasepipelines](/azure/devops/pipelines/release) ermöglicht die kontinuierliche Bereitstellung und automatisiert Ihren Releaseprozess.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Bereitstellen der Anwendung und der zugrunde liegenden Infrastruktur

Das Bereitstellen der Infrastruktur ist mit einer Reihe von Schritten verbunden. Da wir [verknüpfte Vorlagen](../azure-resource-manager/templates/linked-templates.md) verwendet haben, muss der Zugriff auf diese Vorlagen von einem öffentlichen Endpunkt (HTTP oder HTTPS) möglich sein. Dies kann ein Repository auf GitHub, ein Azure Blob Storage-Konto oder ein anderer Speicherort sein. Die hochgeladenen Vorlagenartefakte können sicher bleiben, weil sie in einem privaten Modus gehalten werden können, der Zugriff auf sie aber mithilfe einer Form des Shared Access Signature-Tokens (SAS) erfolgt. Das folgende Beispiel veranschaulicht die Bereitstellung einer Infrastruktur mit Vorlagen aus einem Azure Storage-Blob.

1. Erstellen Sie eine **Neue Releasedefinition**, und wählen Sie eine leere Definition aus. Wir müssen dann die neu erstellte Umgebung unserer Pipeline gemäß umbenennen.

    ![Anfängliche Releasepipeline](media/batch-ci-cd/Release-0.jpg)

1. Erstellen Sie eine Abhängigkeit von der Buildpipeline, um die Ausgabe für die HPC-Anwendung zu erhalten.

    > [!NOTE]
    > Beachten Sie auch hier den **Quellalias**, da dieser benötigt wird, wenn Aufgaben innerhalb der Releasedefinition erstellt werden.

    ![Erstellen eines Artefaktlinks zum HPCApplicationPackage in der entsprechenden Buildpipeline](media/batch-ci-cd/Release-1.jpg)

1. Erstellen Sie einen Link zu einem anderen Artefakt, dieses Mal einem Azure-Repository. Dies ist erforderlich, um auf die in Ihrem Repository gespeicherten Resource Manager-Vorlagen zuzugreifen. Da Resource Manager-Vorlagen nicht kompiliert werden müssen, müssen Sie sie nicht über eine Buildpipeline pushen.

    > [!NOTE]
    > Beachten Sie auch hier den **Quellalias**, da dieser benötigt wird, wenn Aufgaben innerhalb der Releasedefinition erstellt werden.

    ![Erstellen eines Artefaktlinks zu den Azure Repos](media/batch-ci-cd/Release-2.jpg)

1. Navigieren Sie zum Abschnitt **Variablen**. Sie sollten eine Reihe von Variablen in Ihrer Pipeline erstellen, damit Sie nicht die gleichen Informationen in mehrere Aufgaben eingeben müssen. Im Folgenden werden die in diesem Beispiel verwendeten Variablen und ihre Auswirkungen auf die Bereitstellung aufgeführt.

    * **applicationStorageAccountName**: Name des Speicherkontos zum Speichern von Binärdateien für die HPC-Anwendung
    * **batchAccountApplicationName**: Name der Anwendung im Azure Batch-Konto
    * **batchAccountName**: Name des Azure Batch-Kontos
    * **batchAccountPoolName**: Der Name des Pools von virtuellen Computern, die die Verarbeitung ausführen
    * **batchApplicationId**: Eindeutige ID für die Azure Batch-Anwendung
    * **batchApplicationVersion**: Semantische Version Ihrer Batchanwendung (d.h. ffmpeg-Binärdateien)
    * **location**: Speicherort für die bereitzustellenden Azure-Ressourcen
    * **resourceGroupName**: Name der zu erstellenden Ressourcengruppe, und wo Ihre Ressourcen bereitgestellt werden
    * **storageAccountName**: Name des Speicherkontos zum Speichern der verknüpften Resource Manager-Vorlagen

    ![Beispiel für Variablen, die für das Azure Pipelines-Release festgelegt werden](media/batch-ci-cd/Release-4.jpg)

1. Navigieren Sie zu den Aufgaben für die Entwicklungsumgebung. In der unten stehenden Momentaufnahme werden sechs Aufgaben angezeigt. Diese Aufgaben bewirken Folgendes: Herunterladen der gezippten ffmpeg-Dateien, Bereitstellen eines Speicherkontos zum Hosten der geschachtelten Resource Manager-Vorlagen, Kopieren dieser Resource Manager-Vorlagen auf das Speicherkonto, Bereitstellen des Batch-Kontos und der erforderlichen Abhängigkeiten, Erstellen einer Anwendung im Azure Batch-Konto und Hochladen des Anwendungspakets auf das Azure Batch-Konto.

    ![Beispiel für die Aufgaben, die zum Herausgeben der HPC-Anwendung für Azure Batch verwendet werden](media/batch-ci-cd/Release-3.jpg)

1. Fügen Sie die Aufgabe **Pipelineartefakt herunterladen (Vorschau)** hinzu, und legen Sie die folgenden Eigenschaften fest:
    * **Anzeigename**: Herunterladen von ApplicationPackage auf den Agent
    * **Der Name des herunterzuladenden Artefakts:** hpc-application
    * **Pfad, der als Downloadziel verwendet werden soll**: $(System.DefaultWorkingDirectory)

1. Erstellen Sie ein Speicherkonto zum Speichern Ihrer Artefakte. Ein vorhandenes Speicherkonto aus der Lösung könnte verwendet werden, aber für das eigenständige Beispiel und zur Isolation des Inhalts erstellen wir ein dediziertes Speicherkonto für unsere Artefakte (speziell die Resource Manager-Vorlagen).

    Fügen Sie die Aufgabe **Bereitstellung einer Azure-Ressourcengruppe** hinzu, und legen Sie die folgenden Eigenschaften fest:
    * **Anzeigename**: Bereitstellen des Speicherkontos für Resource Manager-Vorlagen
    * **Azure-Abonnement:** Auswählen des entsprechenden Azure-Abonnements
    * **Aktion:** Erstellen oder aktualisieren Sie eine Ressourcengruppe.
    * **Ressourcengruppe**: $(resourceGroupName)
    * **Speicherort**: $(location)
    * **Vorlage**: $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/storageAccount.json
    * **Vorlagenparameter überschreiben**: -accountName $(storageAccountName)

1. Laden Sie die Artefakte aus der Quellcodeverwaltung in das Speicherkonto hoch. Dies kann durch eine Azure Pipelines-Aufgabe durchgeführt werden. Im Rahmen dieser Aufgabe können die URL des Speicherkontocontainers und das SAS-Token in eine Variable in Azure Pipelines ausgegeben werden. Dies bedeutet, dass sie während dieser gesamten Agent-Phase wiederverwendet werden können.

    Fügen Sie die **Azure-Dateikopiervorgang**-Aufgabe hinzu, und legen Sie die folgenden Eigenschaften fest:
    * **Quelle:** $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/
    * **Azure-Verbindungstyp**: Azure Resource Manager
    * **Azure-Abonnement:** Auswählen des entsprechenden Azure-Abonnements
    * **Zieltyp**: Azure Blob
    * **RM-Speicherkonto**: $(storageAccountName)
    * **Containername**: templates
    * **Speichercontainer-URI**: templateContainerUri
    * **SAS-Token des Speichercontainers**: templateContainerSasToken

1. Stellen Sie die Orchestratorvorlage bereit. Rufen Sie die früher verwendete Orchestratorvorlage wieder auf – Ihnen wird auffallen, dass sie zusätzlich zum SAS-Token Parameter für die Speicherkontocontainer-URL enthält. Sie sollten beachten, dass die in der Resource Manager-Vorlage erforderlichen Variablen entweder im Variablenabschnitt der Releasedefinition gespeichert werden oder von einer anderen Azure Pipelines-Aufgabe (z.B. als Teil der Azure-Blobkopiervorgang-Aufgabe) festgelegt wurden.

    Fügen Sie die Aufgabe **Bereitstellung einer Azure-Ressourcengruppe** hinzu, und legen Sie die folgenden Eigenschaften fest:
    * **Anzeigename**: Bereitstellen von Azure Batch
    * **Azure-Abonnement:** Auswählen des entsprechenden Azure-Abonnements
    * **Aktion:** Erstellen oder aktualisieren Sie eine Ressourcengruppe.
    * **Ressourcengruppe**: $(resourceGroupName)
    * **Speicherort**: $(location)
    * **Vorlage**: $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/deployment.json
    * **Vorlagenparameter überschreiben**: ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Eine gängige Praxis ist die Verwendung von Azure Key Vault-Aufgaben. Wenn für den Dienstprinzipal (Verbindung mit Ihrem Azure-Abonnement) entsprechende Zugriffsrichtlinien festgelegt sind, kann er Geheimnisse aus einer Azure Key Vault-Instanz herunterladen und als Variablen in Ihrer Pipeline verwendet werden. Der Name des Geheimnisses wird mit den zugeordneten Wert festgelegt. Beispielsweise könnte auf ein Geheimnis sshPassword in der Releasedefinition mit „$(sshPassword)“ verwiesen werden.

1. Die nächsten Schritten rufen die Azure-Befehlszeilenschnittstelle auf. Mit dem ersten wird eine Anwendung in Azure Batch erstellt. und zugehörige Pakete hochgeladen.

    Fügen Sie die **Azure CLI**-Aufgabe hinzu, und legen Sie die folgenden Eigenschaften fest:
    * **Anzeigename**: Erstellen einer Anwendung im Azure Batch-Konto
    * **Azure-Abonnement:** Auswählen des entsprechenden Azure-Abonnements
    * **Skriptspeicherort**: Inlineskript
    * **Inlineskript**: ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. Im zweiten Schritt werden zugehörige Pakete in die Anwendung hochgeladen. In unserem Fall die ffmpeg-Dateien.

    Fügen Sie die **Azure CLI**-Aufgabe hinzu, und legen Sie die folgenden Eigenschaften fest:
    * **Anzeigename**: Hochladen des Pakets in das Azure Batch-Konto
    * **Azure-Abonnement:** Auswählen des entsprechenden Azure-Abonnements
    * **Skriptspeicherort**: Inlineskript
    * **Inlineskript**: ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Die Versionsnummer des Anwendungspakets wird auf eine Variable festgelegt. Dies ist hilfreich, wenn das Überschreiben früherer Versionen des Pakets bei Ihnen funktioniert, und wenn Sie die Versionsnummer des an Azure Batch gepushten Pakets manuell steuern möchten.

1. Erstellen Sie ein neues Release durch Auswahl von **Release > Neues Release erstellen**. Wählen Sie nach dem Auslösenden den Link zu Ihrem neuen Release aus, um den Status anzuzeigen.

1. Sie können die Liveausgabe vom Agent aus anzeigen, indem Sie die **Protokolle**-Schaltfläche unterhalb Ihrer Umgebung auswählen.

    ![Anzeigen des Status Ihres Release](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Testen der Umgebung

Nachdem die Umgebung eingerichtet ist, vergewissern Sie sich, dass die folgenden Tests erfolgreich abgeschlossen werden können.

Stellen Sie mithilfe der Azure CLI über eine PowerShell-Eingabeaufforderung eine Verbindung mit dem neuen Azure Batch-Konto her.

* Melden Sie sich mit `az login` bei Ihrem Azure-Konto an, und führen Sie die Anweisungen aus, um sich zu authentifizieren.
* Authentifizieren Sie jetzt das Batch-Konto: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Auflisten der verfügbaren Anwendungen

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Überprüfen der Gültigkeit des Pools

```azurecli
az batch pool list
```

Beachten Sie den Wert von `currentDedicatedNodes` aus der Ausgabe dieses Befehls. Dieser Wert wird im nächsten Test angepasst.

#### <a name="resize-the-pool"></a>Ändern der Größe des Pools

Ändern Sie die Größe des Pools, sodass Serverknoten für den Auftrag und das Testen der Aufgabe verfügbar sind, und überprüfen Sie mit dem Befehl zur Poolauflistung den aktuellen Status, bis die Größenänderung abgeschlossen ist und Knoten verfügbar sind.

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Nächste Schritte

Zusätzlich zu diesem Artikel wird in zwei weiteren Tutorials ffmpeg mit .NET und Python verwendet. In diesen Tutorials finden Sie weitere Informationen zur Interaktion mit einem Batch-Konto über eine einfache Anwendung.

* [Ausführen einer parallelen Workload mit Azure Batch über die Python-API](tutorial-parallel-python.md)
* [Ausführen einer parallelen Workload mit Azure Batch über die .NET-API](tutorial-parallel-dotnet.md)
