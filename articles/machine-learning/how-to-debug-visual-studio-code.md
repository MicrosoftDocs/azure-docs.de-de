---
title: Interaktives Debuggen mit Visual Studio Code
titleSuffix: Azure Machine Learning
description: Interaktives Debuggen von Azure Machine Learning-Code, -Pipelines und -Bereitstellungen mit Visual Studio Code
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 09/30/2020
ms.openlocfilehash: 12163419ad779acfa116f1dee66284623e2d45fb
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616109"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>Interaktives Debuggen mit Visual Studio Code



In diesem Artikel wird beschrieben, wie Sie Azure Machine Learning-Experimente, -Pipelines und -Bereitstellungen mit Visual Studio Code (VS Code) und [debugpy](https://github.com/microsoft/debugpy/) interaktiv debuggen.

## <a name="run-and-debug-experiments-locally"></a>Lokales Ausführen und Debuggen von Experimenten

Verwenden Sie die Azure Machine Learning-Erweiterung zum Überprüfen, Ausführen und Debuggen Ihrer Machine Learning-Experimente, bevor Sie sie an die Cloud senden.

### <a name="prerequisites"></a>Voraussetzungen

* Azure Machine Learning-Erweiterung für VS Code (Vorschau). Weitere Informationen finden Sie unter [Einrichten der Azure Machine Learning-Erweiterung für VS Code](tutorial-setup-vscode-extension.md).
* [Docker](https://www.docker.com/get-started)
  * Docker Desktop für Mac und Windows
  * Docker-Engine für Linux.
* [Python 3](https://www.python.org/downloads/)

> [!NOTE]
> Stellen Sie unter Windows sicher, dass Sie [in Docker konfigurieren, dass Linux-Container verwendet werden](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

> [!TIP]
> Für Windows wird die Verwendung von [Docker mit dem Windows-Subsystem für Linux (WSL) 2](/windows/wsl/tutorials/wsl-containers#install-docker-desktop) dringend empfohlen, auch wenn dies nicht erforderlich ist.

> [!IMPORTANT]
> Bevor Sie Ihr Experiment lokal ausführen, stellen Sie sicher, dass Docker ausgeführt wird.

### <a name="debug-experiment-locally"></a>Lokales Debuggen eines Experiments

1. Öffnen Sie in VS Code die Azure Machine Learning-Erweiterung.
1. Erweitern Sie den Abonnementknoten, der den Arbeitsbereich enthält. Wenn Sie noch keines haben, können Sie [mit der Erweiterung einen Azure Machine Learning-Arbeitsbereich erstellen](how-to-manage-resources-vscode.md#create-a-workspace).
1. Erweitern Sie den Arbeitsbereichsknoten.
1. Klicken Sie mit der rechten Maustaste auf den Knoten **Experiments** (Experimente), und wählen Sie **Create experiment** (Experiment erstellen) aus. Geben Sie in der Eingabeaufforderung einen Namen für das Experiment ein.
1. Erweitern Sie den Knoten **Experiments** (Experimente), und klicken Sie mit der rechten Maustaste auf das Experiment, das Sie ausführen möchten. Klicken Sie anschließend auf **Run Experiment** (Experiment ausführen).
1. Wählen Sie in der Liste der Optionen zum Ausführen des Experiments **Locally** (Lokal) aus.
1. **Nur bei erstmaliger Verwendung unter Windows:** Wenn Sie aufgefordert werden, die Dateifreigabe zuzulassen, wählen Sie **Yes** (Ja) aus. Wenn Sie die Dateifreigabe aktivieren, kann Docker das Verzeichnis mit dem Skript in den Container einbinden. Außerdem kann Docker so die Protokolle und Ausgaben Ihrer Ausführung in einem temporären Verzeichnis auf Ihrem System speichern.
1. Wählen Sie **Yes** aus, um das Experiment zu Debuggen. Klicken Sie andernfalls auf **Nein**. Wenn Sie „No“ auswählen, wird das Experiment lokal ohne Anfügen an den Debugger ausgeführt.
1. Klicken Sie auf **Create new Run Configuration** (Neue Laufzeitkonfiguration erstellen), um Ihre Laufzeitkonfiguration zu erstellen. Die Laufzeitkonfiguration definiert das Skript, das Sie ausführen möchten, sowie Abhängigkeiten und verwendete Datasets. Wenn Sie bereits über eine Laufzeitkonfiguration verfügen, wählen Sie diese aus der Dropdownliste aus.
    1. Wählen Sie Ihre Umgebung aus. Sie können eine beliebige [von Azure Machine Learning vorgeschlagene Umgebung](resource-curated-environments.md) wählen oder eine eigene erstellen.
    1. Geben Sie den Namen des Skripts an, das Sie ausführen möchten. Der Pfad ist relativ zum Verzeichnis, das in VS Code geöffnet ist.
    1. Wählen Sie aus, ob Sie ein Azure Machine Learning-Dataset verwenden möchten. Mithilfe der Erweiterung können Sie [Azure Machine Learning-Datasets](how-to-manage-resources-vscode.md#create-dataset) erstellen.
    1. Debugpy ist erforderlich, um den Debugger an den Container anzufügen, der Ihr Experiment ausführt. Klicken Sie auf **Add debugpy** (Debugpy hinzufügen), um debugpy als Abhängigkeit hinzuzufügen. Klicken Sie andernfalls auf **Skip** (Überspringen). Wenn Sie debugpy nicht als Abhängigkeit hinzufügen, wird das Experiment ohne Anfügen an den Debugger ausgeführt.
    1. Eine Konfigurationsdatei mit den Einstellungen für die Laufzeitkonfiguration wird im Editor geöffnet. Wenn Sie mit den Einstellungen zufrieden sind, klicken Sie auf **Submit experiment** (Experiment übermitteln). Öffnen Sie alternativ die Befehlspalette (**View > Command Palette**; Ansicht > Befehlspalette) in der Menüleiste, und geben Sie den Befehl `Azure ML: Submit experiment` in das Textfeld ein.
1. Nachdem das Experiment übermittelt wurde, wird ein Docker-Image erstellt, das Ihr Skript und die in der Laufzeitkonfiguration angegebenen Konfigurationen enthält.

    Wenn der Buildprozess für das Docker-Image beginnt, wird der Inhalt der `60_control_log.txt`-Datei an die Ausgabekonsole in VS Code gestreamt.

    > [!NOTE]
    > Wenn das Docker-Image zum ersten Mal erstellt wird, kann das mehrere Minuten in Anspruch nehmen.

1. Nachdem das Image erstellt wurde, wird eine Eingabeaufforderung angezeigt, um den Debugger zu starten. Legen Sie die Breakpoints in Ihrem Skript fest, und klicken Sie auf **Start debugger** (Debugger starten), wenn Sie mit dem Debuggen beginnen möchten. Dadurch wird der VS Code-Debugger an den Container angefügt, der das Experiment ausgeführt hat. Alternativ können Sie in der Azure Machine Learning-Erweiterung mit dem Mauszeiger auf den Knoten für die aktuelle Ausführung zeigen und auf das Wiedergabesymbol klicken, um den Debugger zu starten.

    > [!IMPORTANT]
    > Für ein einzelnes Experiment können nicht mehrere Debugsitzungen gleichzeitig vorhanden sein. Sie können jedoch zwei oder mehr Experimente mithilfe mehrerer VS Code-Instanzen debuggen.

Jetzt sollten Sie in der Lage sein, Ihren Code mit VS Code schrittweise zu durchlaufen und zu debuggen.

Wenn Sie an einem beliebigen Punkt die Ausführung abbrechen möchten, klicken Sie mit der rechten Maustaste auf den Ausführungsknoten, und klicken Sie auf **Cancel run** (Ausführung abbrechen).

Ähnlich wie bei Remoteexperimentausführungen können Sie den Ausführungsknoten erweitern, um die Protokolle und Ausgaben anzuzeigen.

> [!TIP]
> Docker-Images, die dieselben Abhängigkeiten verwenden, die in Ihrer Umgebung definiert sind, werden zwischen den Ausführungen wiederverwendet. Wenn Sie ein Experiment jedoch mit einer neuen oder einer anderen Umgebung ausführen, wird ein neues Image erstellt. Da diese Images in Ihrem lokalen Speicher gespeichert werden, wird empfohlen, alte oder nicht verwendete Docker-Images zu löschen. Verwenden Sie zum Entfernen von Images aus Ihrem System die [Docker-CLI](https://docs.docker.com/engine/reference/commandline/rmi/) oder die [Docker-Erweiterung für VS Code](https://code.visualstudio.com/docs/containers/overview).

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Debuggen und Problembehandlung für Machine Learning-Pipelines

In einigen Fällen muss der in Ihrer ML-Pipeline verwendete Python-Code ggf. interaktiv debugged werden. Mit VS Code und debugpy können Sie Elemente an den Code anfügen, während er in der Trainingsumgebung ausgeführt wird.

### <a name="prerequisites"></a>Voraussetzungen

* __Azure Machine Learning-Arbeitsbereich__, die für die Verwendung eines __virtuellen Azure-Netzwerks__ konfiguriert ist.
* __Azure Machine Learning-Pipeline__, deren Schritte Python-Skripts enthalten (Beispiel: PythonScriptStep).
* Azure Machine Learning Compute-Cluster, der sich __im virtuellen Netzwerk__ befindet und __von der Pipeline zum Trainieren verwendet wird__.
* __Entwicklungsumgebung__, die sich __im virtuellen Netzwerk__ befindet. Mögliche Entwicklungsumgebung wären etwa:

  * Ein virtueller Azure-Computer im virtuellen Netzwerk
  * Eine Compute-Instanz einer Notebook-VM im virtuellen Netzwerk
  * Ein Clientcomputer, der über eine private Netzwerkverbindung mit dem virtuellen Netzwerk verfügt, entweder per VPN oder ExpressRoute.

Weitere Informationen zur Verwendung eines virtuellen Azure-Netzwerks mit Azure Machine Learning finden Sie unter [Übersicht zu Isolation und Datenschutz bei virtuellen Netzwerken](how-to-network-security-overview.md).

> [!TIP]
> Sie können zwar auch Azure Machine Learning-Ressourcen nutzen, die sich nicht hinter einem virtuellen Netzwerk befinden, aber wir empfehlen Ihnen die Verwendung eines virtuellen Netzwerks.

### <a name="how-it-works"></a>Funktionsweise

Von Ihren ML-Pipelineschritten werden Python-Skripts ausgeführt. Diese Skripts werden geändert, um folgende Aktionen auszuführen:

1. Protokollieren der IP-Adresse des Hosts, auf dem sie ausgeführt werden. Die IP-Adresse wird verwendet, um den Debugger mit dem Skript zu verbinden.

2. Starten Sie die debugpy-Debugkomponente, und warten Sie, bis von einem Debugger eine Verbindung hergestellt wird.

3. Von Ihrer Entwicklungsumgebung aus überwachen Sie die Protokolle, die durch den Trainingsprozess erstellt wurden, um die IP-Adresse zu ermitteln, unter der das Skript ausgeführt wird.

4. Sie geben die IP-Adresse, mit der der Debugger verbunden werden soll, an VS Code weiter (unter Verwendung einer Datei vom Typ `launch.json`).

5. Sie fügen den Debugger an, und durchlaufen interaktiv die Schritte des Skripts.

### <a name="configure-python-scripts"></a>Konfigurieren von Python-Skripts

Ändern Sie die von Schritten in Ihrer ML-Pipeline verwendeten Python-Skripts wie folgt, um das Debuggen zu ermöglichen:

1. Fügen Sie die folgenden import-Anweisungen hinzu:

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. Fügen Sie die folgenden Argumente hinzu. Diese Argumente ermöglichen die bedarfsgerechte Aktivierung des Debuggers sowie die Festlegung des Timeouts für das Anfügen des Debuggers:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                        default=300,
                        help=f'Defines how much time the AML compute target '
                        f'will await a connection from a debugger client (VSCODE).')
    parser.add_argument('--remote_debug_client_ip', type=str,
                        help=f'Defines IP Address of VS Code client')
    parser.add_argument('--remote_debug_port', type=int,
                        default=5678,
                        help=f'Defines Port of VS Code client')
    ```

1. Fügen Sie die folgenden Anweisungen hinzu. Diese Anweisungen dienen zum Laden des aktuellen Ausführungskontexts, um die IP-Adresse des Knotens protokollieren zu können, auf dem der Code ausgeführt wird:

    ```python
    global run
    run = Run.get_context()
    ```

1. Fügen Sie eine `if`-Anweisung hinzu, die debugpy startet und auf das Anfügen eines Debuggers wartet. Wenn innerhalb des Timeouts kein Debugger angefügt wird, wird das Skript normal fortgesetzt. Stellen Sie sicher, dass Sie die Werte `HOST` und `PORT` in der Funktion `listen` durch Ihre eigenen Werte ersetzen.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        try:
            ip = args.remote_debug_client_ip
        except:
            print("Need to supply IP address for VS Code client")
        print(f'ip_address: {ip}')
        debugpy.listen(address=(ip, args.remote_debug_port))
        # Wait for the timeout for debugger to attach
        debugpy.wait_for_client()
        print(f'Debugger attached = {debugpy.is_client_connected()}')
    ```

Das folgende Python-Beispiel zeigt eine einfache `train.py`-Datei, die das Debuggen ermöglicht:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import debugpy
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
parser.add_argument('--remote_debug_client_ip', type=str,
                    help=f'Defines IP Address of VS Code client')
parser.add_argument('--remote_debug_port', type=int,
                    default=5678,
                    help=f'Defines Port of VS Code client')

# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    # ip = socket.gethostbyname(socket.gethostname())
    try:
        ip = args.remote_debug_client_ip
    except:
        print("Need to supply IP address for VS Code client")
    print(f'ip_address: {ip}')
    debugpy.listen(address=(ip, args.remote_debug_port))
    # Wait for the timeout for debugger to attach
    debugpy.wait_for_client()
    print(f'Debugger attached = {debugpy.is_client_connected()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>Konfigurieren der ML-Pipeline

Erstellen Sie eine Umgebung, und legen Sie `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']` fest, um die zum Starten von debugpy erforderlichen Python-Pakete bereitzustellen und den Ausführungskontext abzurufen. Legen Sie die SDK-Version auf die von Ihnen verwendete Version fest. Im folgenden Codeausschnitt wird die Vorgehensweise zum Erstellen einer Umgebung veranschaulicht:

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>'])
```

Im Abschnitt [Konfigurieren von Python-Skripts](#configure-python-scripts) wurden den von Ihren ML-Pipelineschritten verwendeten Skripts neue Argumente hinzugefügt. Der folgende Codeausschnitt zeigt, wie diese Argumente verwendet werden, um das Debuggen für die Komponente zu aktivieren und ein Timeout festzulegen. Außerdem wird gezeigt, wie Sie die zuvor erstellte Umgebung verwenden, indem Sie `runconfig=run_config` festlegen:

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300,'--remote_debug_client_ip','<VS-CODE-CLIENT-IP>','--remote_debug_port',5678],
                         compute_target=aml_compute,
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

Wenn die Pipeline ausgeführt wird, wird durch jeden Schritt eine untergeordnete Ausführung erstellt. Ist das Debuggen aktiviert, protokolliert das geänderte Skript Informationen wie die folgenden in der Datei `70_driver_log.txt` für die untergeordnete Ausführung:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Speichern Sie den Wert von `ip_address`. Sie werden im nächsten Abschnitt verwendet.

> [!TIP]
> Die IP-Adresse finden Sie auch in den Ausführungsprotokollen für die untergeordnete Ausführung dieses Pipelineschritts. Weitere Informationen zum Anzeigen dieser Informationen finden Sie unter [Überwachen von Azure ML-Experimentausführungen und -metriken](how-to-track-experiments.md).

### <a name="configure-development-environment"></a>Konfigurieren der Entwicklungsumgebung

1. Verwenden Sie den folgenden Befehl, um debugpy in Ihrer VS Code-Entwicklungsumgebung zu installieren:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Weitere Informationen zur Verwendung von debugpy mit VS Code finden Sie unter [Remotedebuggen](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Erstellen Sie eine neue Debugkonfiguration, um VS Code für die Kommunikation mit der Azure Machine Learning Compute-Instanz zu konfigurieren, von der der Debugger ausgeführt wird:

    1. Wählen Sie aus VS Code das Menü __Debuggen__ aus, und wählen Sie dann __Konfigurationen öffnen__ aus. Eine Datei namens __launch.json__ wird geöffnet.

    1. Suchen Sie in der Datei __launch.json__ nach der Zeile, die `"configurations": [` enthält, und fügen Sie danach den folgenden Text ein. Ändern Sie den Eintrag `"host": "<IP-ADDRESS>"` in die IP-Adresse, die in Ihren Protokollen aus dem vorherigen Abschnitt zurückgegeben wurde. Ändern Sie den Eintrag `"localRoot": "${workspaceFolder}/code/step"` in ein lokales Verzeichnis, das eine Kopie des zu debuggenden Skripts enthält:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "<IP-ADDRESS>",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Wenn es bereits andere Einträge im Konfigurationsabschnitt gibt, fügen Sie ein Komma (,) nach dem von Ihnen eingegebenen Code ein.

        > [!TIP]
        > Vor allem für Pipelines besteht die bewährte Methode darin, die Ressourcen für Skripts in separaten Verzeichnissen aufzubewahren, damit Code jeweils nur für die einzelnen Schritte relevant ist. In diesem Beispiel verweist der Beispielwert `localRoot` auf `/code/step1`.
        >
        > Wenn Sie mehrere Skripts in unterschiedlichen Verzeichnissen debuggen, muss für jedes Skript ein separater Konfigurationsabschnitt erstellt werden.

    1. Speichern Sie die Datei __launch.json__.

### <a name="connect-the-debugger"></a>Herstellen einer Verbindung für den Debugger

1. Öffnen Sie in VS Code eine lokale Kopie des Skripts.
2. Legen Sie Breakpoints an den Stellen fest, an denen das Skript nach dem Anfügen angehalten werden soll.
3. Drücken Sie F5, oder wählen Sie __Debuggen__ aus, während der untergeordnete Prozess das Skript ausführt und das Timeout für die Debugverbindung (`Timeout for debug connection`) in den Protokollen angezeigt wird. Wählen Sie die Konfiguration __Azure Machine Learning Compute: remote debug__ aus, wenn Sie dazu aufgefordert werden. Sie können auch das Debugsymbol auf der Seitenleiste, den Eintrag __Azure Machine Learning: remote debug__ aus dem Dropdownmenü „Debuggen“ und anschließend den grünen Pfeil auswählen, um den Debugger anzufügen.

    An diesem Punkt stellt VS Code eine Verbindung mit debugpy auf dem Computeknoten her und hält den Vorgang an dem Breakpoint an, den Sie zuvor festgelegt haben. Sie können den Code jetzt während der Ausführung schrittweise durchlaufen, Variablen anzeigen usw.

    > [!NOTE]
    > Wenn das Protokoll einen Eintrag mit der Angabe `Debugger attached = False` enthält, ist das Timeout abgelaufen, und das Skript wurde ohne den Debugger fortgesetzt. Übermitteln Sie die Pipeline erneut, und verbinden Sie den Debugger nach der Nachricht `Timeout for debug connection`, aber noch vor Ablauf des Timeouts.

## <a name="debug-and-troubleshoot-deployments"></a>Debuggen und Durchführen der Problembehandlung für Bereitstellungen

In einigen Fällen müssen Sie den in der Modellbereitstellung enthaltenen Python-Code ggf. interaktiv debuggen. Dies ist beispielsweise der Fall, wenn das Einstiegsskript fehlschlägt und der Grund nicht durch zusätzliche Protokollierung ermittelt werden kann. Mit VS Code und debugpy können Sie Elemente an den Code anfügen, der im Docker-Container ausgeführt wird.

> [!IMPORTANT]
> Diese Methode des Debuggens funktioniert nicht, wenn `Model.deploy()` und `LocalWebservice.deploy_configuration` verwendet werden, um ein Modell lokal bereitzustellen. Stattdessen müssen Sie ein Image mithilfe der [Model.package()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truepackage-workspace--models--inference-config-none--generate-dockerfile-false-)-Methode erstellen.

Bereitstellungen lokaler Webdienste erfordern eine funktionierende Installation von Docker auf Ihrem lokalen System. Weitere Informationen zum Verwenden von Docker finden Sie in der [Docker-Dokumentation](https://docs.docker.com/). Beachten Sie bei Verwendung von Compute-Instanzen, dass Docker bereits installiert ist.

### <a name="configure-development-environment"></a>Konfigurieren der Entwicklungsumgebung

1. Verwenden Sie den folgenden Befehl, um debugpy in Ihrer lokalen VS Code-Entwicklungsumgebung zu installieren:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Weitere Informationen zur Verwendung von debugpy mit VS Code finden Sie unter [Remotedebuggen](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Um VS Code für die Kommunikation mit dem Docker-Image zu konfigurieren, erstellen Sie eine neue Debugkonfiguration:

    1. Wählen Sie aus VS Code das Menü __Debuggen__ aus, und wählen Sie dann __Konfigurationen öffnen__ aus. Eine Datei namens __launch.json__ wird geöffnet.

    1. Suchen Sie in der Datei __launch.json__ nach der Zeile, die `"configurations": [` enthält, und fügen Sie hinter ihr den folgenden Text ein:

        ```json
        {
            "name": "Azure Machine Learning Deployment: Docker Debug",
            "type": "python",
            "request": "attach",
            "connect": {
                "port": 5678,
                "host": "0.0.0.0",
            },
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Wenn es bereits andere Einträge im Konfigurationsabschnitt gibt, fügen Sie ein Komma (,) nach dem von Ihnen eingegebenen Code ein.

        Dieser Abschnitt stellt die Verbindung mit dem Docker-Container über Port 5678 her.

    1. Speichern Sie die Datei __launch.json__.

### <a name="create-an-image-that-includes-debugpy"></a>Erstellen eines Images, das debugpy enthält

1. Ändern Sie die Conda-Umgebung für die Bereitstellung so, dass sie debugpy enthält. Das folgende Beispiel veranschaulicht, wie PTVSD mithilfe des `pip_packages`-Parameters hinzugefügt wird:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.83', 'debugpy'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Um debugpy zu starten und beim Start des Diensts auf eine Verbindung zu warten, fügen Sie Folgendes am Anfang Ihrer Datei `score.py` hinzu:

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. Erstellen Sie ein Image auf der Grundlage der Umgebungsdefinition, und pullen Sie das Image in die lokale Registrierung. 

    > [!NOTE]
    > Dieses Beispiel geht davon aus, dass `ws` auf Ihren Azure Machine Learning-Arbeitsbereich zeigt und `model` das Modell ist, das bereitgestellt wird. Die Datei `myenv.yml` enthält die Conda-Abhängigkeiten, die in Schritt 1 erstellt wurden.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    Nachdem das Image erstellt und heruntergeladen wurde, wird der Imagepfad (einschließlich Repository, Name und Tag, das in diesem Fall auch der Digest ist) in einer Meldung wie der folgenden angezeigt:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Um die Arbeit mit dem Image zu erleichtern, verwenden Sie den folgenden Befehl, um ein Tag hinzuzufügen. Ersetzen Sie `myimagepath` durch den Speicherortwert aus dem vorherigen Schritt.

    ```bash
    docker tag myimagepath debug:1
    ```

    Für die restlichen Schritte können Sie sich auf das lokale Image als `debug:1` beziehen, anstatt den vollständigen Wert des Bildpfads zu verwenden.

### <a name="debug-the-service"></a>Debuggen des Diensts

> [!TIP]
> Wenn Sie in der Datei `score.py` ein Timeout für die debugpy-Verbindung festlegen, müssen Sie VS Code mit der Debugsitzung verbinden, bevor das Timeout abläuft. Starten Sie VS Code, öffnen Sie die lokale Kopie von `score.py`, legen Sie einen Breakpoint fest, und bereiten Sie alles vor, bevor Sie die Schritte in diesem Abschnitt ausführen.
>
> Weitere Informationen zum Debuggen und Festlegen von Breakpoints finden Sie unter [Debuggen](https://code.visualstudio.com/Docs/editor/debugging).

1. Um einen Docker-Container mithilfe des Image zu starten, verwenden Sie den folgenden Befehl:

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_path_to_score.py>:/var/azureml-apps/score.py debug:1 /bin/bash
    ```

    Hiermit wird Ihre Datei `score.py` lokal an die Datei im Container angefügt. Aus diesem Grund werden alle im Editor vorgenommenen Änderungen automatisch im Container widergespiegelt.

1. Führen Sie im Container den folgenden Befehl in der Shell aus.

    ```bash
    runsvdir /var/runit
    ```

1. Um VS Code an debugpy im Container anzufügen, öffnen Sie VS Code und verwenden die Taste F5 oder wählen die Option __Debuggen__ aus. Wählen Sie bei entsprechender Aufforderung die Konfiguration __Azure Machine Learning-Bereitstellung: Docker debuggen__ aus. Sie können auch das Debugsymbol in der Seitenleiste und den Eintrag __Azure Machine Learning-Bereitstellung: Docker debuggen__ im Debugdropdownmenü auswählen und dann den grünen Pfeil zum Anfügen des Debuggers verwenden.

    ![Das Symbol „Debuggen“, die Schaltfläche „Debuggen starten“ und die Konfigurationsauswahl](./media/how-to-troubleshoot-deployment/start-debugging.png)

An diesem Punkt stellt VS Code eine Verbindung mit debugpy im Docker-Container her und hält an dem von Ihnen zuvor festgelegten Breakpoint an. Sie können den Code jetzt während der Ausführung schrittweise durchlaufen, Variablen anzeigen usw.

Weitere Informationen zum Verwenden von VS Code zum Debuggen von Python finden Sie unter [Debuggen von Python-Code](https://code.visualstudio.com/docs/python/debugging).

### <a name="stop-the-container"></a>Beenden des Containers

Zum Beenden des Containers verwenden Sie den folgenden Befehl:

```bash
docker stop debug
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die VS Code-Remoteerweiterung eingerichtet haben, können Sie eine Compute-Instanz als Remotecompute-Instanz über VS Code verwenden, um den Code interaktiv zu debuggen. 

[Tutorial: Trainieren Ihres ersten ML-Modells](tutorial-1st-experiment-sdk-train.md) zeigt, wie eine Compute-Instanz mit einem integrierten Notebook verwendet wird.
