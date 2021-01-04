---
title: Continuous Deployment mit Azure DevOps (Vorschau)
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Sie eine Continuous Deployment für Ihre Anwendungen für benutzerdefinierte Befehle einrichten können. Sie erstellen die Skripts zur Unterstützung der Continuous Deployment-Workflows.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 6dda50a443babca88a0a650fde60df0744d0a1d2
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021829"
---
# <a name="continuous-deployment-with-azure-devops"></a>Continuous Deployment mit Azure DevOps

In diesem Artikel erfahren Sie, wie Sie eine Continuous Deployment für Ihre Anwendungen für benutzerdefinierte Befehle einrichten können. Die Skripts zur Unterstützung des CI/CD-Workflows werden Ihnen zur Verfügung gestellt.

## <a name="prerequisite"></a>Voraussetzung
> [!div class = "checklist"]
> * Eine Anwendung für benutzerdefinierte Befehle für die Entwicklung (DEV)
> * Eine Anwendung für benutzerdefinierte Befehle für die Produktion (PROD)
> * Registrieren Sie sich für [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops).

## <a name="exportimportpublish"></a>Exportieren/Importieren/Veröffentlichen

Die Skripts werden unter [Sprachassistent für Cognitive Services – benutzerdefinierte Befehle](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands) gehostet. Klonen Sie die Skripts im bash-Verzeichnis in Ihr Repository. Achten Sie darauf, dass Sie denselben Pfad beibehalten.

### <a name="set-up-a-pipeline"></a>Einrichten einer Pipeline 

1. Wechseln Sie zu **Azure DevOps – Pipelines**, und klicken Sie auf „Neue Pipeline“.
1. Wählen Sie im Abschnitt **Verbinden** den Speicherort Ihres Repositorys aus, an dem sich diese Skripts befinden.
1. Wählen Sie im Abschnitt **Auswählen** Ihr Repository aus.
1. Wählen Sie im Abschnitt **Konfigurieren** die Option „Starterpipeline“ aus.
1. Als nächstes erhalten Sie einen Editor mit einer YAML-Datei. Ersetzen Sie den Abschnitt „Schritte“ durch dieses Skript.

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Export source app'
      inputs:
        targetType: filePath
        filePath: ./bash/export.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(SourceAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Import to target app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish target app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```
    
1. Beachten Sie, dass diese Skripts davon ausgehen, dass Sie die Region `westus2` verwenden. Falls dies nicht der Fall ist, aktualisieren Sie die Argumente der Aufgaben entsprechend.

    > [!div class="mx-imgBorder"]
    > ![Screenshot, der den Regionswert in den Argumenten hervorhebt](media/custom-commands/cicd-new-pipeline-yaml.png)

1. Öffnen Sie in der Schaltfläche „Speichern und ausführen“ die Dropdownliste, und klicken Sie auf „Speichern“.

### <a name="hook-up-the-pipeline-with-your-application"></a>Verbinden der Pipeline mit Ihrer Anwendung

1. Navigieren Sie zur Hauptseite der Pipeline.
1. Wählen Sie in der Dropdownliste oben rechts die Option **Pipeline bearbeiten** aus. Damit gelangen Sie zu einem YAML-Editor. 
1. Wählen Sie in der rechten oberen Ecke neben der Schaltfläche „Ausführen“ die Option **Variablen** aus. Klicken Sie auf **Neue Variable**.
1. Fügen Sie folgende Variablen hinzu:
    
    | Variable | BESCHREIBUNG |
    | ------- | --------------- | ----------- |
    | SourceAppId | ID der DEV-Anwendung |
    | TargetAppId | ID der PROD-Anwendung |
    | SubscriptionKey | Für beide Anwendungen verwendeter Abonnementschlüssel |
    | Kultur | Kultur der Anwendungen (d. h. de-de) |

    > [!div class="mx-imgBorder"]
    > ![Nutzlast zum Senden von Aktivitäten](media/custom-commands/cicd-edit-pipeline-variables.png)

1. Klicken Sie auf „Ausführen“ und dann in den aktiven „Auftrag“. 

    Es sollte eine Liste der ausgeführten Aufgaben angezeigt werden, die Folgendes enthält: „Quell-App exportieren“, „In Ziel-App importieren“ und „Ziel-App trainieren und veröffentlichen“.

## <a name="deploy-from-source-code"></a>Bereitstellen aus dem Quellcode

Für den Fall, dass Sie die Definition Ihrer Anwendung in einem Repository aufbewahren möchten, stellen wir die Skripts für Bereitstellungen aus dem Quellcode zur Verfügung. Da sich die Skripts in Bash befinden, müssen Sie, wenn Sie Windows verwenden, das [Linux-Subsystem](/windows/wsl/install-win10) installieren.

Die Skripts werden unter [Sprachassistent für Cognitive Services – benutzerdefinierte Befehle](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands) gehostet. Klonen Sie die Skripts im bash-Verzeichnis in Ihr Repository. Achten Sie darauf, dass Sie denselben Pfad beibehalten.

### <a name="prepare-your-repository"></a>Vorbereiten Ihres Repositorys

1. Erstellen Sie ein Verzeichnis für Ihre Anwendung, in unserem Beispiel erstellen Sie ein Verzeichnis namens „Apps“.
1. Aktualisieren Sie die Argumente des Bash-Skripts unten, und führen Sie es aus. Es importiert das Dialogmodell Ihrer Anwendung in die Datei „myapp.json“.
    ```BASH
    bash/export.sh -r <region> -s <subscriptionkey> -c en-us -a <appid> -f apps/myapp.json
    ```
    | Argumente | BESCHREIBUNG |
    | ------- | --------------- | ----------- |
    | region | Region der Anwendung, d. h. „westus2“. |
    | subscriptionkey | Abonnementschlüssel Ihrer Speech-Ressource. |
    | appid | Die zu exportierende ID der Anwendung für benutzerdefinierte Befehle. |

1. Pushen Sie diese Änderungen in Ihr Repository.

### <a name="set-up-a-pipeline"></a>Einrichten einer Pipeline 

1. Wechseln Sie zu **Azure DevOps – Pipelines**, und klicken Sie auf „Neue Pipeline“.
1. Wählen Sie im Abschnitt **Verbinden** den Speicherort Ihres Repositorys aus, an dem sich diese Skripts befinden.
1. Wählen Sie im Abschnitt **Auswählen** Ihr Repository aus.
1. Wählen Sie im Abschnitt **Konfigurieren** die Option „Starterpipeline“ aus.
1. Als nächstes erhalten Sie einen Editor mit einer YAML-Datei. Ersetzen Sie den Abschnitt „Schritte“ durch dieses Skript.

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Import app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ../apps/myapp.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```

    > [!NOTE]
    > Diese Skripts gehen davon aus, dass Sie die Region „westus2“ verwenden. Falls dies nicht der Fall ist, aktualisieren Sie die Argumente der Aufgaben entsprechend.

1. Öffnen Sie in der Schaltfläche „Speichern und ausführen“ die Dropdownliste, und klicken Sie auf „Speichern“.

### <a name="hook-up-the-pipeline-with-your-target-applications"></a>Verbinden der Pipeline mit Ihren Zielanwendungen

1. Navigieren Sie zur Hauptseite der Pipeline.
1. Wählen Sie in der Dropdownliste oben rechts die Option **Pipeline bearbeiten** aus. Damit gelangen Sie zu einem YAML-Editor. 
1. Wählen Sie in der rechten oberen Ecke neben der Schaltfläche „Ausführen“ die Option **Variablen** aus. Klicken Sie auf **Neue Variable**.
1. Fügen Sie folgende Variablen hinzu:

    | Variable | BESCHREIBUNG |
    | ------- | --------------- | ----------- |
    | TargetAppId | ID der PROD-Anwendung |
    | SubscriptionKey | Für beide Anwendungen verwendeter Abonnementschlüssel |
    | Kultur | Kultur der Anwendungen (d. h. de-de) |

1. Klicken Sie auf „Ausführen“ und dann in den aktiven „Auftrag“.
    Es sollte eine Liste der ausgeführten Aufgaben angezeigt werden, die Folgendes enthält: „App importieren“ und „App trainieren und veröffentlichen“

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen finden Sie in den Beispielen auf GitHub](https://aka.ms/speech/cc-samples)