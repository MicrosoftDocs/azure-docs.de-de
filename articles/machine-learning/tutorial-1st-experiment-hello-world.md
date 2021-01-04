---
title: 'Tutorial: Ausführen von „Hello World!“ Python-Skript'
titleSuffix: Azure Machine Learning
description: Teil 2 der Azure Machine Learning-Einstiegsreihe zeigt, wie Sie ein triviales Python-Skript „Hello World!“ in die Cloud übermitteln.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 971bac8a0b0951d4e07e139aea6c465a9159b8db
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96570959"
---
# <a name="tutorial-run-a-hello-world-python-script-part-2-of-4"></a>Tutorial: Ausführen eines Python-Skripts „Hello World!“ (Teil 2 von 4)

In diesem Tutorial erfahren Sie, wie das Azure Machine Learning SDK für Python verwendet wird, um ein Python-Skript „Hello World“ zu übermitteln und auszuführen.

Dieses Tutorial ist *Teil 2 einer vierteiligen Tutorialreihe*, in der Sie die Grundlagen von Azure Machine Learning kennenlernen und auftragsbasierte Machine Learning-Aufgaben in Azure ausführen. Dieses Tutorial basiert auf den Schritten aus [Teil 1: Einrichten Ihres lokalen Computers für Azure Machine Learning](tutorial-1st-experiment-sdk-setup-local.md).

In diesem Tutorial wird Folgendes vermittelt:

> [!div class="checklist"]
> * Lokales Ausführen eines Python-Skripts „Hello World!“.
> * Erstellen eines Python-Steuerungsskripts zum Übermitteln von „Hello World!“ an Azure Machine Learning.
> * Verstehen der Azure Machine Learning Konzepte im Steuerungsskript.
> * Senden und Ausführen des Skripts „Hello World!“.
> * Anzeigen Ihrer Codeausgabe in der Cloud

## <a name="prerequisites"></a>Voraussetzungen

- Abschluss von [Teil 1](tutorial-1st-experiment-sdk-setup-local.md), wenn Sie noch nicht über einen Azure Machine Learning-Arbeitsbereich verfügen.
- Kenntnisse der Python-Sprache und der Machine Learning-Workflows auf Einstiegsniveau.
- Lokale Entwicklungsumgebung, z. B. Visual Studio Code, Jupyter oder PyCharm.
- Python (Version 3.5 bis 3.7).

## <a name="create-and-run-a-python-script-locally"></a>Lokales Erstellen und Ausführen eines Python-Skripts

Erstellen Sie ein neues Unterverzeichnis mit dem Namen `src` unter dem Verzeichnis `tutorial` zur Speicherung von Code, den Sie auf einem Azure Machine Learning-Computecluster ausführen möchten. Erstellen Sie im Unterverzeichnis `src` das Python-Skript `hello.py`:

```python
# src/hello.py
print("Hello world!")
```

Ihre Projektverzeichnisstruktur sieht nun wie folgt aus:

```Bash
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
└──01-create-workspace.py
└──02-create-compute.py
```


### <a name="test-your-script-locally"></a><a name="test"></a>Lokales Testen des Skripts

Sie können Ihren Code lokal ausführen, indem Sie Ihre bevorzugte IDE oder ein Terminal verwenden. Das lokale Ausführen von Code hat den Vorteil, dass Sie Code interaktiv debuggen.

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

> [!div class="nextstepaction"]
> [Ich habe das Skript lokal ausgeführt.](?success=run-local#control-script) [Es ist ein Problem aufgetreten.](https://www.research.net/r/7C2NTH7?issue=run-local)

## <a name="create-a-control-script"></a><a name="control-script"></a> Erstellen eines Steuerungsskripts

Mithilfe eines *Steuerungsskripts* können Sie ihr `hello.py`-Skript in der Cloud ausführen. Sie verwenden das Steuerungsskript, um zu steuern, wie und wo Ihr Machine Learning-Code ausgeführt wird.  

Erstellen Sie in Ihrem Tutorialverzeichnis eine neue Python-Datei mit dem Namen `03-run-hello.py`, kopieren Sie den folgenden Code, und fügen Sie ihn in diese Datei ein:

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```



### <a name="understand-the-code"></a>Grundlegendes zum Code

Eine kurze Beschreibung zur Funktionsweise des Steuerungsskripts:

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      [Workspace](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) stellt eine Verbindung mit Ihrem Azure Machine Learning-Arbeitsbereich her, so dass Sie mit Ihren Azure Machine Learning-Ressourcen kommunizieren können.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [Experiment](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py) bietet eine einfache Möglichkeit, mehrere Ausführungen unter einem einzelnen Namen zu ordnen. Später können Sie sehen, wie Experimente den Vergleich von Metriken zwischen Dutzenden von Ausführungen einfach machen.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) umschließt Ihren `hello.py`-Code und übergibt ihn an Ihren Arbeitsbereich. Wie der Name schon nahelegt, können Sie diese Klasse verwenden, um die _Ausführung_ Ihres _Skripts_ in Azure Machine Learning zu _konfigurieren_. Außerdem wird angegeben, auf welchem Computeziel das Skript ausgeführt wird. In diesem Code ist das Ziel der Computecluster, den Sie im [Setup-Tutorial](tutorial-1st-experiment-sdk-setup-local.md) erstellt haben.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       Übermittelt Ihr Skript. Diese Übermittlung wird als [Ausführung](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) bezeichnet. Eine Ausführung (Run) verkapselt eine einzelne Ausführung (Execution) Ihres Codes. Verwenden Sie eine Ausführung, um den Status Ihres Skripts zu überwachen, die Ausgabe zu erfassen, die Ergebnisse zu analysieren, die Metriken zu visualisieren und mehr.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        Das `run`-Objekt stellt ein Handle für die Ausführung Ihres Codes bereit. Überwachen Sie deren Status in Azure Machine Learning Studio mit der URL, die vom Python-Skript ausgegeben wird.  
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Ich habe das Steuerungsskript erstellt.](?success=create-control-script#submit) [Es ist ein Problem aufgetreten.](https://www.research.net/r/7C2NTH7?issue=create-control-script)

## <a name="submit-and-run-your-code-in-the-cloud"></a><a name="submit"></a> Übermitteln und Ausführen Ihres Codes in der Cloud

Führen Sie Ihr Steuerungsskript aus, das seinerseits `hello.py` auf dem Computecluster ausführt, den Sie im [Setup-Tutorial](tutorial-1st-experiment-sdk-setup-local.md) erstellt haben.

Die erste Ausführung dauert fünf bis zehn Minuten. Dies hat folgende Ursachen:

* Ein Docker-Image wird in der Cloud erstellt.
* Die Größe des Computeclusters wird von 0 in 1 Knoten geändert.
* Das Docker-Image wird in den Computecluster heruntergeladen. 

Nachfolgende Ausführungen sind wesentlich schneller (etwa 15 Sekunden), da das Docker-Image im Computecluster zwischengespeichert wird. Sie können dies testen, indem Sie den folgenden Code nach Abschluss der ersten Ausführung erneut übermitteln.

```bash
python 03-run-hello.py
```

> [!TIP]
> Wenn Sie beim Ausführen dieses Codes den Fehler erhalten, dass Sie keinen Zugriff auf das Abonnement haben, finden Sie Informationen zu Authentifizierungsoptionen unter [Herstellen einer Verbindung mit einem Arbeitsbereich](how-to-manage-workspace.md?tab=python#connect-multi-tenant).

> [!div class="nextstepaction"]
> [Ich habe Code in der Cloud übermittelt.](?success=submit-to-cloud#monitor) [Es ist ein Problem aufgetreten.](https://www.research.net/r/7C2NTH7?issue=submit-to-cloud)

## <a name="monitor-your-code-in-the-cloud-by-using-the-studio"></a><a name="monitor"></a>Überwachen Ihres Codes in der Cloud mithilfe von Studio

Die Ausgabe enthält einen Link zu Studio, der etwa wie folgt aussieht: `https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>`.

Folgen Sie dem Link, und navigieren Sie zur Registerkarte **Ausgaben und Protokolle**. Dort sehen Sie eine Datei `70_driver_log.txt`, die wie folgt aussieht:

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

In Zeile 8 sehen Sie die Ausgabe „Hallo Welt!“ .

Die Datei `70_driver_log.txt` enthält die Standardausgabe einer Ausführung. Diese Datei kann nützlich sein, wenn Sie Remoteausführungen in der Cloud debuggen.

> [!div class="nextstepaction"]
> [Ich habe das Protokoll in Studio angezeigt.](?success=monitor-in-studio#next-steps) [Es ist ein Problem aufgetreten.](https://www.research.net/r/7C2NTH7?issue=monitor-in-studio)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein einfaches Skript „Hello World!“ in Azure ausgeführt. Sie haben erfahren, wie Sie die Verbindung mit Ihrem Azure Machine Learning-Arbeitsbereich herstellen, ein Experiment erstellen und Ihren `hello.py`-Code in die Cloud übermitteln.

Im nächsten Tutorial bauen Sie auf dem Gelernten auf, indem Sie etwas Interessanteres als `print("Hello world!")`ausführen.

> [!div class="nextstepaction"]
> [Tutorial 1: Trainieren eines Bildklassifizierungsmodells mit Azure Machine Learning](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> Wenn Sie die Tutorialreihe hier beenden und nicht mit dem nächsten Schritt fortfahren möchten, vergessen Sie nicht, [Ihre Ressourcen zu bereinigen](tutorial-1st-experiment-bring-data.md#clean-up-resources).