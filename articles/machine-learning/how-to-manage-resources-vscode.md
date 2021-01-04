---
title: Erstellen und Verwalten von Ressourcen mit der VS Code-Erweiterung (Vorschau)
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Azure Machine Learning-Ressourcen mithilfe der Azure Machine Learning Visual Studio Code-Erweiterung erstellen und verwalten.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 11/16/2020
ms.openlocfilehash: f8eb18b190b72381f1a93575eb39b3d19d8d431b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010672"
---
# <a name="manage-azure-machine-learning-resources-with-the-vs-code-extension-preview"></a>Verwalten von Azure Machine Learning-Ressourcen mit der VS Code-Erweiterung (Vorschau)

Hier erfahren Sie, wie Sie Azure Machine Learning-Ressourcen mit der VS Code-Erweiterung verwalten.

![Azure Machine Learning-VS Code-Erweiterung](media/how-to-manage-resources-vscode/azure-machine-learning-vscode-extension.png)

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement. Wenn Sie keins besitzen, können Sie sich für die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) registrieren.
- Visual Studio Code. Sollte diese Komponente noch nicht vorhanden sein, [installieren Sie sie](https://code.visualstudio.com/docs/setup/setup-overview).
- VS Code-Azure Machine Learning-Erweiterung. Befolgen Sie die [Anleitung zur Installation der Azure Machine Learning-Erweiterung für VS Code](tutorial-setup-vscode-extension.md#install-the-extension), um die Erweiterung zu installieren.

Bei allen folgenden Prozessen wird davon ausgegangen, dass Sie die Azure Machine Learning-Ansicht in Visual Studio Code verwenden. Wählen Sie zum Starten der Erweiterung das Symbol **Azure** in der VS Code-Aktivitätsleiste aus.

## <a name="workspaces"></a>Arbeitsbereiche

Weitere Informationen finden Sie im [Artikel zu Arbeitsbereichen](concept-workspace.md).

### <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs

1. Klicken Sie in der Azure Machine Learning-Ansicht mit der rechten Maustaste auf Ihren Abonnementknoten, und wählen Sie **Create workspace** (Arbeitsbereich erstellen) aus.
1. An der Eingabeaufforderung:
    1. Geben Sie einen Namen für den Arbeitsbereich ein.
    1. Wählen Sie Ihr Azure-Abonnement aus.
    1. Wählen Sie eine Ressourcengruppe aus, in der der Arbeitsbereich bereitgestellt werden soll, oder erstellen Sie sie.
    1. Wählen Sie den Speicherort aus, in dem der Arbeitsbereich bereitgestellt werden soll.

Alternative Verfahren zum Erstellen eines Arbeitsbereichs:

- Öffnen Sie die Befehlspalette **Ansicht > Befehlspalette**, und geben Sie in die Texteingabeaufforderung **Azure ML: Arbeitsbereich erstellen** ein.
- Klicken Sie oben in der Azure Machine Learning-Ansicht auf das Symbol `+`.
- Erstellen Sie einen neuen Arbeitsbereich, wenn Sie während der Bereitstellung anderer Ressourcen aufgefordert werden, einen Arbeitsbereich auszuwählen.

### <a name="remove-a-workspace"></a>Entfernen eines Arbeitsbereichs

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Klicken Sie mit der rechten Maustaste auf den Arbeitsbereich, den Sie entfernen möchten.
1. Wählen Sie aus, was entfernt werden soll:
    - *Only the workspace* (Nur Arbeitsbereich): Mit dieser Option wird **nur** die Azure-Ressource für den Arbeitsbereich gelöscht. Die Ressourcengruppe, Speicherkonten und alle anderen Ressourcen, mit denen der Arbeitsbereich verknüpft ist, befinden sich immer noch in Azure.
    - *With associated resources* (Mit zugeordneten Ressourcen): Mit dieser Option werden der Arbeitsbereich **und** alle zugehörigen Ressourcen gelöscht.

## <a name="datastores"></a>Datenspeicher

Die VS Code-Erweiterung unterstützt derzeit Datenspeicher der folgenden Typen:

- Azure-Dateifreigabe
- Azure Blob Storage

Wenn Sie einen Arbeitsbereich erstellen, wird ein Datenspeicher für jeden dieser Typen erstellt.

Weitere Informationen finden Sie im [Artikel zu Datenspeichern](concept-data.md#datastores).

### <a name="create-a-datastore"></a>Erstellen eines Datenspeichers

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Arbeitsbereichsknoten, unter dem Sie den Datenspeicher erstellen möchten.
1. Klicken Sie mit der rechten Maustaste auf den Knoten **Datastores** (Datenspeicher), und wählen Sie **Register datastore** (Datenspeicher registrieren) aus.
1. An der Eingabeaufforderung:
    1. Geben Sie einen Namen für den Datenspeicher an.
    1. Wählen Sie den Datenspeichertyp aus.
    1. Wählen Sie die Speicherressource aus. Sie können entweder eine Speicherressource auswählen, die dem Arbeitsbereich zugeordnet ist, oder eine beliebige gültige Speicherressource in ihren Azure-Abonnements auswählen.
    1. Wählen Sie den Container aus, in dem sich Ihre Daten in der zuvor ausgewählten Speicherressource befinden.
1. In VS Code wird eine Konfigurationsdatei angezeigt. Wenn Sie mit Ihrer Konfigurationsdatei zufrieden sind, wählen Sie **Save and continue** (Speichern und fortfahren) aus, oder öffnen Sie die VS Code-Befehlspalette (**View > Command Palette** (Ansicht > Befehlspalette)), und geben Sie **Azure ML: Save and Continue** (Speichern und fortfahren) ein.

### <a name="manage-a-datastore"></a>Verwalten eines Datenspeichers

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Arbeitsbereichsknoten.
1. Erweitern Sie den Knoten **Datastores** (Datenspeicher) in Ihrem Arbeitsbereich.
1. Wählen Sie den Datenspeicher aus, für den Sie folgende Aktionen ausführen möchten:
    - *Set as default* (Als Standard festlegen). Wenn Sie Experimente ausführen, wird dieser Datenspeicher verwendet.
    - *Inspect read-only settings* (Einstellungen für Lesezugriff überprüfen).
    - *Modify* (Ändern). Hiermit können Sie den Authentifizierungstyp und die Anmeldeinformationen ändern. Zu den unterstützten Authentifizierungstypen zählen Kontoschlüssel und SAS-Token.

## <a name="datasets"></a>Datasets

Die Erweiterung unterstützt derzeit die folgenden Datasettypen:

- *Tabellarisch:* Ermöglicht das Materialisieren von Daten in einen DataFrame (Pandas oder PySpark).
- *Datei:* Eine Datei oder Sammlung von Dateien. Hiermit können Sie Dateien herunterladen oder in Ihrer Computeressource einbinden.

Weitere Informationen finden Sie im [Artikel zu Datasets](concept-data.md#datasets).

### <a name="create-dataset"></a>Erstellen von Datasets

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Arbeitsbereichsknoten, unter dem Sie den Datenspeicher erstellen möchten.
1. Klicken Sie mit der rechten Maustaste auf den Knoten **Datasets**, und wählen Sie **Create dataset** (Dataset erstellen) aus.
1. An der Eingabeaufforderung:
    1. Wählen Sie den Datasettyp aus.
    1. Hiermit wird definiert, ob sich die Daten auf Ihrem PC oder im Web befinden.
    1. Geben Sie den Speicherort Ihrer Daten an. Dabei kann es sich entweder um eine einzelne Datei oder um ein Verzeichnis handeln, die bzw. das Ihre Datendateien enthält.
    1. Wählen Sie den Datenspeicher aus, in den Ihre Daten hochgeladen werden sollen.
    1. Geben Sie ein Präfix an, anhand dessen Sie das Dataset im Datenspeicher identifizieren können.

### <a name="version-datasets"></a>Versionsverwaltung von Datasets

Wenn Sie Machine Learning-Modelle entwickeln und sich die Daten dabei ändern, empfiehlt es sich, die verschiedenen Versionen des Datasets zu verwalten. So führen Sie dies in der VS Code-Erweiterung aus:

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Arbeitsbereichsknoten.
1. Erweitern Sie den Knoten **Datasets**.
1. Klicken Sie mit der rechten Maustaste auf das Dataset, für das Sie die Versionsverwaltung einrichten möchten, und wählen Sie **Create New Version** (Neue Version erstellen) aus.
1. An der Eingabeaufforderung:
    1. Wählen Sie den Datasettyp aus.
    1. Definieren Sie, ob sich die Daten auf Ihrem PC oder im Web befinden.
    1. Geben Sie den Speicherort Ihrer Daten an. Dabei kann es sich entweder um eine einzelne Datei oder um ein Verzeichnis handeln, die bzw. das Ihre Datendateien enthält.
    1. Wählen Sie den Datenspeicher aus, in den Ihre Daten hochgeladen werden sollen.
    1. Geben Sie ein Präfix an, anhand dessen Sie das Dataset im Datenspeicher identifizieren können.

### <a name="view-dataset-properties"></a>Anzeigen von Dataseteigenschaften

Mit dieser Option können Sie Metadaten anzeigen, die einem bestimmten Dataset zugeordnet sind. So führen Sie dies in der VS Code-Erweiterung aus:

1. Erweitern Sie den Arbeitsbereichsknoten.
1. Erweitern Sie den Knoten **Datasets**.
1. Klicken Sie mit der rechten Maustaste auf das Dataset, das Sie überprüfen möchten, und wählen Sie **View Dataset Properties** (Eigenschaften des Datasets anzeigen) aus. Hiermit wird eine Konfigurationsdatei mit den Eigenschaften der neuesten Datasetversion angezeigt.

> [!NOTE]
> Wenn Sie über mehrere Versionen Ihres Datasets verfügen, können Sie auswählen, dass nur die Dataseteigenschaften einer bestimmten Version angezeigt werden. Erweitern Sie dazu den Datasetknoten, und führen Sie die in diesem Abschnitt beschriebenen Schritte für die gewünschte Version aus.

### <a name="unregister-datasets"></a>Aufheben der Registrierung von Datasets

Wenn Sie ein Dataset und alle Versionen entfernen möchten, heben Sie die Registrierung auf. So führen Sie dies in der VS Code-Erweiterung aus:

1. Erweitern Sie den Arbeitsbereichsknoten.
1. Erweitern Sie den Knoten **Datasets**.
1. Klicken Sie mit der rechten Maustaste auf das Dataset, dessen Registrierung Sie aufheben möchten, und wählen Sie **Unregister dataset** (Registrierung des Datasets aufheben) aus.

## <a name="environments"></a>Umgebungen

Weitere Informationen finden Sie im [Artikel zu Umgebungen](concept-environments.md).

### <a name="create-environment"></a>Erstellen der Umgebung

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Arbeitsbereichsknoten, unter dem Sie den Datenspeicher erstellen möchten.
1. Klicken Sie mit der rechten Maustaste auf den Knoten **Environments** (Umgebungen), und wählen Sie **Create Environment** (Umgebung erstellen) aus.
1. An der Eingabeaufforderung:
    1. Geben Sie einen Namen für die Umgebung an.
    1. Definieren Sie die Umgebungskonfiguration:
        - *Zusammengestellte Umgebungen*: Dies sind vorkonfigurierte Umgebungen in Azure Machine Learning. Sie können die Umgebung weiter anpassen, indem Sie die `dependencies`-Eigenschaft in der JSON-Datei ändern. [Weitere Informationen zu zusammengestellten Umgebungen](resource-curated-environments.md).
        - *Conda-Abhängigkeitsdatei*: In Anaconda-Umgebungen kann die Datei, die die Umgebungsdefinition enthält, bereitgestellt werden.
        - *Pip-Anforderungsdatei*: In Pip-Umgebungen kann die Datei, die die Umgebungsdefinition enthält, bereitgestellt werden.
        - *Vorhandene Conda-Umgebung*: Hierbei werden die Conda-Umgebungen auf dem lokalen PC gesucht, und es wird versucht, eine Umgebung aus der ausgewählten Umgebung zu erstellen.
        - *Benutzerdefiniert*: Definieren Sie eigene Kanäle und Abhängigkeiten.
    1. Die Konfigurationsdatei wird im Editor geöffnet. Wenn Sie mit Ihrer Konfiguration zufrieden sind, wählen Sie **Save and continue** (Speichern und fortfahren) aus, oder öffnen Sie die VS Code-Befehlspalette (**View > Command Palette** (Ansicht > Befehlspalette)), und geben Sie **Azure ML: Save and Continue** (Speichern und fortfahren) ein.

### <a name="view-environment-configurations"></a>Anzeigen von Umgebungskonfigurationen

So zeigen Sie die Abhängigkeiten und Konfigurationen für eine bestimmte Umgebung in der Erweiterung an:

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Arbeitsbereichsknoten.
1. Erweitern Sie den Knoten **Environments** (Umgebungen).
1. Klicken Sie mit der rechten Maustaste auf die Umgebung, die Sie anzeigen möchten, und wählen Sie **View Environment** (Umgebung anzeigen) aus.

### <a name="edit-environment-configurations"></a>Bearbeiten von Umgebungskonfigurationen

So bearbeiten Sie die Abhängigkeiten und Konfigurationen für eine bestimmte Umgebung in der Erweiterung:

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Knoten **Environments** (Umgebungen) in Ihrem Arbeitsbereich.
1. Klicken Sie mit der rechten Maustaste auf die Umgebung, die Sie anzeigen möchten, und wählen Sie **Edit environment** (Umgebung bearbeiten) aus.
1. Wenn Sie die Änderungen vorgenommen haben und mit Ihrer Konfiguration zufrieden sind, wählen Sie **Save and continue** (Speichern und fortfahren) aus, oder öffnen Sie die VS Code-Befehlspalette (**View > Command Palette** (Ansicht > Befehlspalette)), und geben Sie **Azure ML: Save and Continue** (Speichern und fortfahren) ein.

## <a name="experiments"></a>Experimente

Weitere Informationen finden Sie im [Artikel zu Experimenten](concept-azure-machine-learning-architecture.md#experiments).

### <a name="create-experiment"></a>Erstellen eines Experiments

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Arbeitsbereichsknoten.
1. Klicken Sie im Arbeitsbereich mit der rechten Maustaste auf den Knoten **Experiments** (Experimente), und wählen Sie **Create experiment** (Experiment erstellen) aus.
1. Geben Sie an der Eingabeaufforderung einen Namen für das Experiment ein.

### <a name="run-experiment"></a>Ausführen des Experiments

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Knoten **Experiments** (Experimente) in Ihrem Arbeitsbereich.
1. Klicken Sie mit der rechten Maustaste auf das Experiment, das Sie ausführen möchten.
1. Wählen Sie in der Aktivitätsleiste das Symbol **Run Experiment** (Experiment ausführen) aus.
1. Wählen Sie aus, ob Sie Ihr Experiment lokal oder remote ausführen möchten. Weitere Informationen zum lokalen Ausführen und Debuggen von Experimenten finden Sie im [Leitfaden zum Debuggen](how-to-debug-visual-studio-code.md).
1. Wählen Sie Ihr Abonnement aus.
1. Wählen Sie den Azure ML-Arbeitsbereich aus, unter dem das Experiment ausgeführt werden soll.
1. Wählen Sie das Experiment aus.
1. Wählen Sie die Computeressource zum Ausführen des Experiments aus, oder erstellen Sie sie.
1. Wählen Sie eine Laufzeitkonfiguration für Ihr Experiment aus, oder erstellen Sie sie.

Alternativ können Sie die Schaltfläche **Run Experiment** (Experiment ausführen) oben in der Erweiterung auswählen und die Ausführung des Experiments an der Eingabeaufforderung konfigurieren.

### <a name="view-experiment"></a>Anzeigen des Experiments

So zeigen Sie Ihr Experiment in Azure Machine Learning Studio an:

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Knoten **Experiments** (Experimente) in Ihrem Arbeitsbereich.
1. Klicken Sie mit der rechten Maustaste auf das Experiment, das Sie anzeigen möchten, und wählen Sie **View Experiment** (Experiment anzeigen) aus. 
1. Es wird die Aufforderung angezeigt, die Experiment-URL in Azure Machine Learning Studio zu öffnen. Wählen Sie **Open**(Öffnen).

### <a name="track-run-progress"></a>Nachverfolgen des Ausführungsfortschritts

Wenn Sie das Experiment ausführen, möchten Sie möglicherweise den Fortschritt sehen. So können Sie den Fortschritt einer Ausführung in Azure Machine Learning Studio aus der Erweiterung nachverfolgen:

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Knoten **Experiments** (Experimente) in Ihrem Arbeitsbereich.
1. Erweitern Sie den Experimentknoten, für den Sie den Fortschritt nachverfolgen möchten.
1. Klicken Sie mit der rechten Maustaste die Ausführung, und wählen Sie **View Run in Azure portal** (Ausführung im Azure-Portal anzeigen) aus.
1. Es wird die Aufforderung angezeigt, die Ausführungs-URL in Azure Machine Learning Studio zu öffnen. Wählen Sie **Open**(Öffnen).

### <a name="download-run-logs--outputs"></a>Herunterladen von Ausführungsprotokollen und Ausgaben

Wenn eine Ausführung angeschlossen wurde, können Sie die Protokolle und Assets herunterladen, z. B. das Modell, das als Teil einer Experimentausführung generiert wurde.

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Knoten **Experiments** (Experimente) in Ihrem Arbeitsbereich.
1. Erweitern Sie den Experimentknoten, für den Sie den Fortschritt nachverfolgen möchten.
1. Klicken Sie mit der rechten Maustaste auf die Ausführung:
    - Wählen Sie **Download outputs** (Ausgaben herunterladen) aus, um die Ausgaben herunterzuladen.
    - Klicken Sie zum Herunterladen der Protokolle auf **Download logs** (Protokolle herunterladen).

### <a name="view-run-metadata"></a>Anzeigen von Ausführungsmetadaten

In der Erweiterung können Sie die Metadaten überprüfen, z. B. die für die Ausführung verwendete Laufzeitkonfiguration sowie Informationen zur Ausführung.

## <a name="compute-instances"></a>Compute-Instanzen

Weitere Informationen finden Sie im Artikel über [Compute-Instanzen](concept-compute-instance.md).

### <a name="create-compute-instance"></a>Erstellen einer Compute-Instanz

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Arbeitsbereichsknoten, unter dem Sie die Compute-Instanz erstellen möchten.
1. Klicken Sie mit der rechten Maustaste auf den Knoten **Compute instances** (Compute-Instanzen), und klicken Sie dann auf **Create compute instance** (Compute-Instanz erstellen).
1. An der Eingabeaufforderung:
    1. Geben Sie einen Namen für Ihre Compute-Instanz an.
    1. Wählen Sie in der Liste eine VM-Größe aus.
    1. Geben Sie an, ob Sie SSH-Zugriff aktivieren möchten.
        1. Wenn Sie SSH-Zugriff aktivieren, müssen Sie auch den öffentlichen SSH-Schlüssel angeben oder die Datei, die den Schlüssel enthält. Weitere Informationen finden Sie in der [Anleitung zum Erstellen und Verwenden von SSH-Schlüsseln in Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

### <a name="stop-or-restart-compute-instance"></a>Anhalten oder Neustart einer Compute-Instanz

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Knoten **Compute instance** (Compute-Instanz) in Ihrem Arbeitsbereich.
1. Klicken Sie mit der rechten Maustaste auf die Compute-Instanz, die Sie anhalten oder neu starten möchten, und klicken Sie auf **Stop Compute instance** (Compute-Instanz anhalten) bzw. **Restart compute instance** (Compute-Instanz neu starten).

### <a name="view-compute-instance-configuration"></a>Anzeigen der Konfiguration für eine Compute-Instanz

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Knoten **Compute instance** (Compute-Instanz) in Ihrem Arbeitsbereich.
1. Klicken Sie mit der rechten Maustaste auf die Compute-Instanz, die Sie untersuchen möchten, und klicken Sie dann auf **View Compute instance Properties** (Compute-Instanzeigenschaften anzeigen).

### <a name="delete-compute-instance"></a>Löschen einer Compute-Instanz

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Knoten **Compute instance** (Compute-Instanz) in Ihrem Arbeitsbereich.
1. Klicken Sie mit der rechten Maustaste auf die Compute-Instanz, die Sie löschen möchten, und klicken Sie dann auf **Delete compute instance** (Compute-Instanz löschen).

## <a name="compute-clusters"></a>Computecluster

Diese Erweiterung unterstützt die folgenden Computetypen:

- Azure Machine Learning-Computecluster
- Azure Kubernetes Service

Weitere Informationen finden Sie im [Artikel zu Computezielen](concept-compute-target.md#train).

### <a name="create-compute"></a>Erstellen von Computeressourcen

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Arbeitsbereichsknoten, unter dem Sie den Computecluster erstellen möchten.
1. Klicken Sie mit der rechten Maustaste auf den Knoten **Computecluster**, und wählen Sie **Create Compute** (Computeressource erstellen) aus.
1. An der Eingabeaufforderung:
    1. Wählen Sie einen Computetyp aus.
    1. Wählen Sie eine VM-Größe aus. [Weitere Informationen zu VM-Größen](../virtual-machines/sizes.md).
    1. Geben Sie einen Computnamen an.

### <a name="view-compute-configuration"></a>Anzeigen der Computekonfiguration

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Knoten **Compute clusters** (Computecluster) in Ihrem Arbeitsbereich.
1. Klicken Sie mit der rechten Maustaste auf die Computeressource, die Sie anzeigen möchten, und wählen Sie **View Compute Properties** (Computeeigenschaften anzeigen) aus.

### <a name="edit-compute-scale-settings"></a>Bearbeiten von Compute-Skalierungseinstellungen

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Knoten **Compute clusters** (Computecluster) in Ihrem Arbeitsbereich.
1. Klicken Sie mit der rechten Maustaste auf die Regel, die Sie bearbeiten möchten, und wählen Sie **Edit Compute** (Computeressource bearbeiten) aus.
1. Die Compute-Konfigurationsdatei wird im Editor geöffnet. Wenn Sie mit Ihrer Konfiguration zufrieden sind, wählen Sie **Save and continue** (Speichern und fortfahren) aus, oder öffnen Sie die VS Code-Befehlspalette (**View > Command Palette** (Ansicht > Befehlspalette)), und geben Sie **Azure ML: Save and Continue** (Speichern und fortfahren) ein.

### <a name="delete-compute"></a>Löschen einer Computeressource

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Knoten **Compute clusters** (Computecluster) in Ihrem Arbeitsbereich.
1. Klicken Sie mit der rechten Maustaste auf die Computeressource, die Sie löschen möchten, und wählen Sie **Delete Compute** (Computeressource löschen) aus.

### <a name="create-run-configuration"></a>Erstellen einer Laufzeitkonfiguration

So erstellen Sie eine Laufzeitkonfiguration in der Erweiterung:

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Knoten **Compute clusters** (Computecluster) in Ihrem Arbeitsbereich.
1. Klicken Sie mit der rechten Maustaste auf das Computeziel, unter dem Sie die Laufzeitkonfiguration erstellen möchten, und wählen Sie **Create Run Configuration** (Laufzeitkonfiguration erstellen) aus.
1. An der Eingabeaufforderung:
    1. Geben Sie einen Namen für das Computeziel an.
    1. Wählen Sie eine Umgebung aus, oder erstellen Sie sie.
    1. Geben Sie den Namen des Skripts ein, das Sie ausführen möchten, oder drücken Sie die Eingabetaste, um das Skript auf dem lokalen Computer zu suchen.
    1. Optional: Wählen Sie aus, ob Sie einen Datenverweis für die Trainingsausführung erstellen möchten. Dabei werden Sie aufgefordert, ein Dataset in der Laufzeitkonfiguration zu definieren.
        1. Wählen Sie eines der registrierten Datasets aus, um eine Verknüpfung mit der Laufzeitkonfiguration zu erstellen. Eine Konfigurationsdatei für das Dataset wird im Editor geöffnet. Wenn Sie mit Ihrer Konfiguration zufrieden sind, wählen Sie **Save and continue** (Speichern und fortfahren) aus, oder öffnen Sie die VS Code-Befehlspalette (**View > Command Palette** (Ansicht > Befehlspalette)), und geben Sie **Azure ML: Save and Continue** (Speichern und fortfahren) ein.
    1. Wenn Sie mit Ihrer Konfiguration zufrieden sind, wählen Sie **Save and continue** (Speichern und fortfahren) aus, oder öffnen Sie die VS Code-Befehlspalette (**View > Command Palette** (Ansicht > Befehlspalette)), und geben Sie **Azure ML: Save and Continue** (Speichern und fortfahren) ein.

### <a name="edit-run-configuration"></a>Bearbeiten der Laufzeitkonfiguration

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Computeclusterknoten im Knoten **Compute clusters** (Computecluster) Ihres Arbeitsbereichs.
1. Klicken Sie mit der rechten Maustaste auf die Laufzeitkonfiguration, die Sie bearbeiten möchten, und wählen Sie **Edit Run Configuration** (Laufzeitkonfiguration bearbeiten) aus.
1. Die Konfigurationsdatei für die Laufzeitkonfiguration wird im Editor geöffnet. Wenn Sie mit Ihrer Konfiguration zufrieden sind, wählen Sie **Save and continue** (Speichern und fortfahren) aus, oder öffnen Sie die VS Code-Befehlspalette (**View > Command Palette** (Ansicht > Befehlspalette)), und geben Sie **Azure ML: Save and Continue** (Speichern und fortfahren) ein.

### <a name="delete-run-configuration"></a>Löschen der Laufzeitkonfiguration

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Arbeitsbereichsknoten.
1. Erweitern Sie den gewünschten Computeclusterknoten im Knoten **Compute cluster** (Computecluster).
1. Klicken Sie mit der rechten Maustaste auf die gewünschte Laufzeitkonfiguration, und wählen Sie **Delete Run Configuration** (Laufzeitkonfiguration löschen) aus.

## <a name="models"></a>Modelle

Weitere Informationen finden Sie im [Artikel zu Modellen](concept-azure-machine-learning-architecture.md#models).

### <a name="register-model"></a>Registrieren des Modells

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Arbeitsbereichsknoten.
1. Klicken Sie mit der rechten Maustaste auf den Knoten **Models** (Modelle), und wählen Sie **Register Model** (Modell registrieren) aus.
1. An der Eingabeaufforderung:
    1. Geben Sie einen Namen für das Modell ein.
    1. Wählen Sie aus, ob das Modell eine Datei oder ein Ordner ist.
    1. Suchen Sie das Modell auf dem lokalen PC.
    1. Die Konfigurationsdatei für das Modell wird im Editor geöffnet. Wenn Sie mit Ihrer Konfiguration zufrieden sind, wählen Sie **Save and continue** (Speichern und fortfahren) aus, oder öffnen Sie die VS Code-Befehlspalette (**View > Command Palette** (Ansicht > Befehlspalette)), und geben Sie **Azure ML: Save and Continue** (Speichern und fortfahren) ein.

### <a name="view-model-properties"></a>Anzeigen von Modelleigenschaften

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Knoten **Models** (Modelle) in Ihrem Arbeitsbereich.
1. Klicken Sie mit der rechten Maustaste auf das Modell, dessen Eigenschaften Sie anzeigen möchten, und wählen Sie **View Model Properties** (Modelleigenschaften anzeigen) aus. Im Editor wird eine Datei mit den Modelleigenschaften geöffnet.

### <a name="download-model"></a>Herunterladen des Modells

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Knoten **Models** (Modelle) in Ihrem Arbeitsbereich.
1. Klicken Sie mit der rechten Maustaste auf das Modell, das Sie herunterladen möchten, und wählen Sie **Download Model File** (Modelldatei herunterladen) aus.

### <a name="delete-a-model"></a>Löschen eines Modells

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Knoten **Models** (Modelle) in Ihrem Arbeitsbereich.
1. Klicken Sie mit der rechten Maustaste auf das zu löschende Modell und anschließend auf **Remove Model** (Modell entfernen).

## <a name="endpoints"></a>Endpunkte

Die VS Code-Erweiterung unterstützt die folgenden Bereitstellungsziele:

- Azure Container Instances
- Azure Kubernetes Service

Weitere Informationen finden Sie unter [Webdienst-Endpunkt](concept-azure-machine-learning-architecture.md#web-service-endpoint).

### <a name="create-deployments"></a>Erstellen von Bereitstellungen

> [!NOTE]
> Die Erstellung von Bereitstellungen funktioniert zurzeit nur mit Conda-Umgebungen.

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Arbeitsbereichsknoten.
1. Klicken Sie mit der rechten Maustaste auf den Knoten **Endpoints** (Endpunkte), und wählen Sie **Deploy Service** (Dienst bereitstellen) aus.
1. An der Eingabeaufforderung:
    1. Wählen Sie aus, ob Sie ein bereits registriertes Modell oder eine lokale Modelldatei verwenden möchten.
    1. Auswählen des Modells
    1. Wählen Sie das Bereitstellungsziel aus, in dem Sie das Modell bereitstellen möchten.
    1. Geben Sie einen Namen für das Modell ein.
    1. Stellen Sie das Skript bereit, das beim Bewerten des Modells ausgeführt werden soll.
    1. Stellen Sie eine Conda-Abhängigkeitsdatei bereit.
    1. Im Editor wird die Konfigurationsdatei für die Bereitstellung angezeigt. Wenn Sie mit Ihrer Konfiguration zufrieden sind, wählen Sie **Save and continue** (Speichern und fortfahren) aus, oder öffnen Sie die VS Code-Befehlspalette (**View > Command Palette** (Ansicht > Befehlspalette)), und geben Sie **Azure ML: Save and Continue** (Speichern und fortfahren) ein.

> [!NOTE]
> Alternativ dazu können Sie im Knoten *Models* (Modelle) mit der rechten Maustaste auf ein registriertes Modell klicken und **Deploy Service From Registered Model** (Dienst aus registriertem Modell bereitstellen) auswählen.

### <a name="delete-deployments"></a>Löschen von Bereitstellungen

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Knoten **Endpoints** (Endpunkte) in Ihrem Arbeitsbereich.
1. Klicken Sie mit der rechten Maustaste auf die Bereitstellung, die Sie entfernen möchten, und wählen Sie **Remove Service** (Dienst entfernen) aus.
1. Sie werden aufgefordert, das Löschen des Diensts zu bestätigen. Klicken Sie auf **OK**.

### <a name="manage-deployments"></a>Verwalten von Bereitstellungen

Zusätzlich zum Erstellen und Löschen von Bereitstellungen können Sie auch die Einstellungen für eine Bereitstellung anzeigen.

1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält.
1. Erweitern Sie den Knoten **Endpoints** (Endpunkte) in Ihrem Arbeitsbereich.
1. Klicken Sie mit der rechten Maustaste auf die zu verwaltende Bereitstellung:
    - Wählen Sie zum Bearbeiten der Einstellungen **Edit service** (Dienst bearbeiten) aus.
        - Im Editor wird die Konfigurationsdatei für die Bereitstellung angezeigt. Wenn Sie mit Ihrer Konfiguration zufrieden sind, wählen Sie **Save and continue** (Speichern und fortfahren) aus, oder öffnen Sie die VS Code-Befehlspalette (**View > Command Palette** (Ansicht > Befehlspalette)), und geben Sie **Azure ML: Save and Continue** (Speichern und fortfahren) ein.
    - Wählen Sie zum Anzeigen der Konfigurationseinstellungen für die Bereitstellung **View service properties** (Diensteigenschaften anzeigen) aus.

## <a name="next-steps"></a>Nächste Schritte

[Trainieren eines Bildklassifizierungsmodells](tutorial-train-deploy-image-classification-model-vscode.md) mit der VS Code-Erweiterung.