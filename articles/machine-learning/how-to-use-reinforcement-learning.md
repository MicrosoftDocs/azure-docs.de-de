---
title: Trainieren und Bereitstellen eines Modells für das Lernen durch Bestärkung (Vorschau).
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie das Lernen durch Bestärkung (Reinforcement Learning, RL) von Azure Machine Learning nutzen, um einen RL-Agent so zu trainieren, dass er Pong spielen kann.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: peterlu
author: peterclu
ms.date: 05/05/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: a7fdb370847e72657829d53df019203b0a5b211b
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302569"
---
# <a name="reinforcement-learning-preview-with-azure-machine-learning"></a>Lernen durch Bestärkung (Vorschau) mit Azure Machine Learning



> [!NOTE]
> Das Lernen durch Bestärkung von Azure Machine Learning ist derzeit eine Previewfunktion. Momentan werden nur Ray- und RLlib-Frameworks unterstützt.

In diesem Artikel erfahren Sie, wie Sie einen RL-Agent (Reinforcement Learning, Lernen durch Bestärkung) trainieren, damit er das Videospiel Pong spielen kann. Sie verwenden dabei die Open-Source-Python-Bibliothek [Ray RLlib](https://ray.readthedocs.io/en/master/rllib.html) mit Azure Machine Learning, um komplexe verteilte RL-Aufträge zu verwalten.

In diesem Artikel lernen Sie Folgendes:
> [!div class="checklist"]
> * Einrichten eines Experiments
> * Definieren von Haupt- und Workerknoten
> * Erstellen eines RL-Estimators
> * Übermitteln eines Experiments zum Starten eines Testlaufs
> * Anzeigen der Ergebnisse

Dieser Artikel basiert auf dem [RLlib Pong-Beispiel](https://aka.ms/azureml-rl-pong), das im Azure Machine Learning-Notebook-[GitHub-Repository](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md) zu finden ist.

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie diesen Code in einer der folgenden Umgebungen aus. Wir empfehlen Ihnen, die Azure Machine Learning-Computeinstanz zu testen, die einen schnellen Start ermöglicht. Die Beispielnotebooks für Lernen durch Bestärkung sind für schnelles Klonen und Ausführen auf einer Azure Machine Learning-Computeinstanz verfügbar.

 - Azure Machine Learning-Computeinstanz

     - Erfahren Sie, wie Sie Beispielnotebooks klonen können: [Tutorial: Einrichten der Umgebung und des Arbeitsbereichs](tutorial-1st-experiment-sdk-setup.md).
         - Klonen Sie anstelle von **tutorials** den Ordner **how-to-use-azureml**.
     - Führen Sie das Setup-Notebook für virtuelle Netzwerke unter `/how-to-use-azureml/reinforcement-learning/setup/devenv_setup.ipynb` aus, um die Netzwerkports zu öffnen, die für das verteilte Lernen durch Bestärkung verwendet werden.
     - Führen Sie das Beispielnotebook `/how-to-use-azureml/reinforcement-learning/atari-on-distributed-compute/pong_rllib.ipynb` aus.
 
 - Ihr eigener Jupyter Notebook-Server

    - Installieren Sie das [Azure Machine Learning SDK.](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)
    - Installieren Sie das [Azure Machine Learning RL SDK](/python/api/azureml-contrib-reinforcementlearning/?preserve-view=true&view=azure-ml-py): `pip install --upgrade azureml-contrib-reinforcementlearning`.
    - Erstellen Sie eine [Konfigurationsdatei für den Arbeitsbereich](how-to-configure-environment.md#workspace).
    - Führen Sie das virtuelle Netzwerk aus, um die Netzwerkports zu öffnen, die für das verteilte vertiefende Lernen verwendet werden.


## <a name="how-to-train-a-pong-playing-agent"></a>Trainieren eines Agents, der Pong spielt

Das Lernen durch Bestärkung ist eine Methode des maschinellen Lernens, die durch das Ausführen von Tätigkeiten lernt. Während andere Techniken für maschinelles Lernen durch passives Übernehmen von Eingabedaten und Finden von darin enthaltenen Mustern lernen, verwendet RL **Trainings-Agents**, um aktiv Entscheidungen zu treffen und aus ihren Ergebnissen zu lernen.

Ihre Trainings-Agents lernen in einer **simulierten Umgebung**, Pong zu spielen. Trainings-Agents treffen mit jedem Frame des Spiels eine Entscheidung, um das Paddle nach oben oder unten zu verschieben oder die Position beizubehalten. Sie treffen anhand des Zustands des Spiels (ein RGB-Bild des Bildschirms) eine Entscheidung.

RL setzt **Belohnungen** ein, um dem Agent mitzuteilen, ob seine Entscheidungen erfolgreich sind. In dieser Umgebung erhält der Agent eine positive Belohnung, wenn er einen Punkt erhält, und eine negative Belohnung, wenn ein Punkt gegen ihn erzielt wird. Über viele Iterationen hinweg lernt der Trainings-Agent, eine Aktion ausgehend vom aktuellen Zustand auszuführen, die die Summe der erwarteten zukünftigen Belohnungen optimiert.

Für eine solche Optimierung in RL wird in der Regel ein Modell eines **Deep Neural Network** (DNN) verwendet. Zu Beginn ist die Leistung des Agents eher schlecht, aber mit jedem Spiel werden zusätzliche Beispiele generiert, die das Modell weiter optimieren.

Das Training ist beendet, wenn der Agent im Durchschnitt 18 Belohnungen pro Trainingsepoche erhält. Das heißt, dass der Agent seinen Gegner in Spielen mit bis zu 21 Punkten um durchschnittlich mindestens 18 Punkte geschlagen hat.

Die Iteration durch Simulation und erneutes Trainieren eines DNN ist rechenintensiv und erfordert große Datenmengen. Eine Möglichkeit zum Verbessern der Leistung von RL-Aufträgen besteht in der **Parallelisierung**, sodass mehrere Trainings-Agents gleichzeitig agieren und lernen können. Das Verwalten einer verteilten RL-Umgebung kann jedoch eine komplexe Aufgabe sein.

Azure Machine Learning stellt das Framework bereit, um das komplexe Hochskalieren Ihrer RL-Workloads zu handhaben.

## <a name="set-up-the-environment"></a>Einrichten der Umgebung

Richten Sie die lokale RL-Umgebung ein, indem Sie die erforderlichen Python-Pakete laden, Ihren Arbeitsbereich initialisieren, ein Experiment erstellen und ein konfiguriertes virtuelles Netzwerk angeben.

### <a name="import-libraries"></a>Importieren von Bibliotheken

Importieren Sie die erforderlichen Python-Pakete, um den Rest dieses Beispiels auszuführen.

```python
# Azure ML Core imports
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
from azureml.core.runconfig import EnvironmentDefinition
from azureml.widgets import RunDetails
from azureml.tensorboard import Tensorboard

# Azure ML Reinforcement Learning imports
from azureml.contrib.train.rl import ReinforcementLearningEstimator, Ray
from azureml.contrib.train.rl import WorkerConfiguration
```

### <a name="initialize-a-workspace"></a>Initialisieren eines Arbeitsbereichs

Der [Azure Machine Learning-Arbeitsbereich](concept-workspace.md) ist die Ressource der obersten Ebene für Azure Machine Learning. Er stellt den zentralen Ort für die Arbeit mit allen erstellten Artefakten dar.

Initialisieren Sie ein Arbeitsbereichsobjekt aus der Datei `config.json`, die im [Abschnitt „Voraussetzungen“](#prerequisites) erstellt wurde. Wenn Sie diesen Code in einer Azure Machine Learning-Computeinstanz ausführen, wurde die Konfigurationsdatei bereits für Sie erstellt.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-reinforcement-learning-experiment"></a>Erstellen eines Experiments für Lernen durch Bestärkung

Erstellen Sie ein [Experiment](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py), um die Ausführung des Lernens durch Bestärkung nachzuverfolgen. In Azure Machine Learning handelt es sich bei Experimenten um logische Sammlungen verwandter Testversionen zum Organisieren von Ausführungsprotokollen, dem Verlauf, der Ausgabe usw.

```python
experiment_name='rllib-pong-multi-node'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="specify-a-virtual-network"></a>Festlegen eines virtuellen Netzwerks

Für RL-Aufträge, die mehrere Computeziele verwenden, müssen Sie ein virtuelles Netzwerk mit geöffneten Ports festlegen, mithilfe derer Worker- und Hauptknoten miteinander kommunizieren können. Das virtuelle Netzwerk kann sich in einer beliebigen Ressourcengruppe befinden. Es sollte sich jedoch in derselben Region wie Ihr Arbeitsbereich befinden. Weitere Informationen zum Einrichten des virtuellen Netzwerks finden Sie im Notebook zum Einrichten eines Arbeitsbereichs, das Sie im Abschnitt „Voraussetzungen“ finden. Hier geben Sie den Namen des virtuellen Netzwerks in der Ressourcengruppe an.

```python
vnet = 'your_vnet'
```

## <a name="define-head-and-worker-compute-targets"></a>Definieren von Haupt- und Workercomputezielen

In diesem Beispiel werden separate Computeziele für die Ray-Haupt- und Workerknoten verwendet. Mit diesen Einstellungen können Sie Ihre Computeressourcen abhängig von der erwarteten Workload zentral hoch- oder herunterskalieren. Legen Sie die Anzahl der Knoten und die Größe der einzelnen Knoten basierend auf den Anforderungen Ihres Experiments fest.

### <a name="head-computing-target"></a>Hauptcomputingziele

In diesem Beispiel wird ein mit GPU ausgestatteter Hauptcluster verwendet, um die Deep Learning-Leistung zu optimieren. Der Hauptknoten trainiert das neuronale Netz, das der Agent verwendet, um Entscheidungen zu treffen. Der Hauptknoten sammelt auch Datenpunkte von den Workerknoten, um das neuronale Netz weiter zu trainieren.

Der Hauptcompute verwendet einen einzelnen virtuellen [`STANDARD_NC6`-Computer](../virtual-machines/nc-series.md) (VM). Er verfügt über sechs virtuelle CPUs, was bedeutet, dass die Arbeit über sechs Arbeits-CPUs verteilt werden kann.


```python
from azureml.core.compute import AmlCompute, ComputeTarget

# choose a name for the Ray head cluster
head_compute_name = 'head-gpu'
head_compute_min_nodes = 0
head_compute_max_nodes = 2

# This example uses GPU VM. For using CPU VM, set SKU to STANDARD_D2_V2
head_vm_size = 'STANDARD_NC6'

if head_compute_name in ws.compute_targets:
    head_compute_target = ws.compute_targets[head_compute_name]
    if head_compute_target and type(head_compute_target) is AmlCompute:
        print(f'found head compute target. just use it {head_compute_name}')
else:
    print('creating a new head compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = head_vm_size,
                                                                min_nodes = head_compute_min_nodes, 
                                                                max_nodes = head_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    head_compute_target = ComputeTarget.create(ws, head_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    head_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(head_compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="worker-computing-cluster"></a>Workercomputingcluster

In diesem Beispiel werden vier [`STANDARD_D2_V2`-VMs](../virtual-machines/nc-series.md) für das Workercomputeziel verwendet. Jeder Workerknoten hat zwei verfügbare CPUs für insgesamt acht verfügbare CPUs, um die Arbeit zu parallelisieren.

GPUs sind für die Workerknoten nicht erforderlich, da sie kein Deep Learning ausführen. Die Worker führen die Spielsimulationen aus und sammeln Daten.

```python
# choose a name for your Ray worker cluster
worker_compute_name = 'worker-cpu'
worker_compute_min_nodes = 0 
worker_compute_max_nodes = 4

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
worker_vm_size = 'STANDARD_D2_V2'

# Create the compute target if it hasn't been created already
if worker_compute_name in ws.compute_targets:
    worker_compute_target = ws.compute_targets[worker_compute_name]
    if worker_compute_target and type(worker_compute_target) is AmlCompute:
        print(f'found worker compute target. just use it {worker_compute_name}')
else:
    print('creating a new worker compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = worker_vm_size,
                                                                min_nodes = worker_compute_min_nodes, 
                                                                max_nodes = worker_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    worker_compute_target = ComputeTarget.create(ws, worker_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    worker_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(worker_compute_target.get_status().serialize())
```

## <a name="create-a-reinforcement-learning-estimator"></a>Erstellen eines Estimators für Lernen durch Bestärkung

In diesem Abschnitt erfahren Sie, wie Sie mit dem [ReinforcementLearningEstimator](/python/api/azureml-contrib-reinforcementlearning/azureml.contrib.train.rl.reinforcementlearningestimator?preserve-view=true&view=azure-ml-py) einen Trainingsauftrag an Azure Machine Learning übermitteln.

Azure Machine Learning verwendet Schätzklassen, um Informationen zur Ausführungskonfiguration zu kapseln. So können Sie auf einfache Weise festlegen, wie eine Skriptausführung konfiguriert werden soll. 

### <a name="define-a-worker-configuration"></a>Definieren einer Workerkonfiguration

Das WorkerConfiguration-Objekt teilt Azure Machine Learning mit, wie der Workercluster, der das Eingabeskript ausführt, initialisiert werden soll.

```python
# Pip packages we will use for both head and worker
pip_packages=["ray[rllib]==0.8.3"] # Latest version of Ray has fixes for isses related to object transfers

# Specify the Ray worker configuration
worker_conf = WorkerConfiguration(
    
    # Azure ML compute cluster to run Ray workers
    compute_target=worker_compute_target, 
    
    # Number of worker nodes
    node_count=4,
    
    # GPU
    use_gpu=False, 
    
    # PIP packages to use
    pip_packages=pip_packages
)
```

### <a name="define-script-parameters"></a>Definieren von Skriptparametern

Das Einstiegsskript `pong_rllib.py` akzeptiert eine Liste von Parametern, die definieren, wie der Trainingsauftrag ausgeführt wird. Wenn diese Parameter über den Estimator als Kapselungsebene übergeben werden, ist es einfach, Skriptparameter zu ändern und Konfigurationen unabhängig voneinander auszuführen.

Wenn Sie den richtigen Wert für `num_workers` festlegen, nutzen Sie die Parallelisierung optimal. Legen Sie die Anzahl der Worker auf denselben Wert wie die Anzahl der verfügbaren CPUs fest. In diesem Beispiel können Sie dies wie folgt berechnen:

Der Hauptknoten ist ein [Standard_NC6](../virtual-machines/nc-series.md) mit sechs vCPUs. Der Workercluster besteht aus vier [Standard_D2_V2-VMs](../cloud-services/cloud-services-sizes-specs.md#dv2-series) mit jeweils zwei CPUs für insgesamt acht CPUs. Allerdings müssen Sie eine CPU von der Workeranzahl subtrahieren, da eine für die Hauptknotenrolle reserviert werden muss. 6 CPUs + 8 CPUs - 1 Haupt-CPU = 13 gleichzeitige Worker. Azure Machine Learning verwendet Haupt- und Workercluster, um Computeressourcen zu unterscheiden. Allerdings unterscheidet Ray nicht zwischen Haupt und Worker, und alle CPUs sind verfügbare CPUs für die Workerthreadausführung.


```python
training_algorithm = "IMPALA"
rl_environment = "PongNoFrameskip-v4"

# Training script parameters
script_params = {
    
    # Training algorithm, IMPALA in this case
    "--run": training_algorithm,
    
    # Environment, Pong in this case
    "--env": rl_environment,
    
    # Add additional single quotes at the both ends of string values as we have spaces in the 
    # string parameters, outermost quotes are not passed to scripts as they are not actually part of string
    # Number of GPUs
    # Number of ray workers
    "--config": '\'{"num_gpus": 1, "num_workers": 13}\'',
    
    # Target episode reward mean to stop the training
    # Total training time in seconds
    "--stop": '\'{"episode_reward_mean": 18, "time_total_s": 3600}\'',
}
```

### <a name="define-the-reinforcement-learning-estimator"></a>Definieren des Estimators für Lernen durch Bestärkung

Verwenden Sie die Parameterliste und das Workerkonfigurationsobjekt, um den Estimator zu erstellen.

```python
# RL estimator
rl_estimator = ReinforcementLearningEstimator(
    
    # Location of source files
    source_directory='files',
    
    # Python script file
    entry_script="pong_rllib.py",
    
    # Parameters to pass to the script file
    # Defined above.
    script_params=script_params,
    
    # The Azure ML compute target set up for Ray head nodes
    compute_target=head_compute_target,
    
    # Pip packages
    pip_packages=pip_packages,
    
    # GPU usage
    use_gpu=True,
    
    # RL framework. Currently must be Ray.
    rl_framework=Ray(),
    
    # Ray worker configuration defined above.
    worker_configuration=worker_conf,
    
    # How long to wait for whole cluster to start
    cluster_coordination_timeout_seconds=3600,
    
    # Maximum time for the whole Ray job to run
    # This will cut off the run after an hour
    max_run_duration_seconds=3600,
    
    # Allow the docker container Ray runs in to make full use
    # of the shared memory available from the host OS.
    shm_size=24*1024*1024*1024
)
```

### <a name="entry-script"></a>Eingabeskript

Das [Einstiegsskript](https://aka.ms/azure-rl-pong-script) `pong_rllib.py`trainiert ein neuronales Netz mithilfe der [OpenAI Gym-Umgebung](https://github.com/openai/gym/) `PongNoFrameSkip-v4`. OpenAI Gyms sind standardisierte Schnittstellen, mit denen Algorithmen für Lernen durch Bestärkung für klassische Atari-Spiele getestet werden können.

In diesem Beispiel wird ein Trainingsalgorithmus verwendet, der als [IMPALA](https://arxiv.org/abs/1802.01561) (Importance Weighted Actor-Learner Architecture, nach Bedeutung gewichtete Akteur-Lerner-Architektur) bezeichnet wird. IMPALA parallelisiert jeden einzelnen Lernakteur so, dass er über viele Computeknoten hinweg skaliert wird, ohne die Geschwindigkeit oder Stabilität zu beeinträchtigen.

[Ray Tune](https://ray.readthedocs.io/en/latest/tune.html) orchestriert die IMPALA-Workeraufgaben.

```python
import ray
import ray.tune as tune
from ray.rllib import train

import os
import sys

from azureml.core import Run
from utils import callbacks

DEFAULT_RAY_ADDRESS = 'localhost:6379'

if __name__ == "__main__":

    # Parse arguments
    train_parser = train.create_parser()

    args = train_parser.parse_args()
    print("Algorithm config:", args.config)

    if args.ray_address is None:
        args.ray_address = DEFAULT_RAY_ADDRESS

    ray.init(address=args.ray_address)

    tune.run(run_or_experiment=args.run,
             config={
                 "env": args.env,
                 "num_gpus": args.config["num_gpus"],
                 "num_workers": args.config["num_workers"],
                 "callbacks": {"on_train_result": callbacks.on_train_result},
                 "sample_batch_size": 50,
                 "train_batch_size": 1000,
                 "num_sgd_iter": 2,
                 "num_data_loader_buffers": 2,
                 "model": {
                    "dim": 42
                 },
             },
             stop=args.stop,
             local_dir='./logs')
```

### <a name="logging-callback-function"></a>Protokollieren der Rückruffunktion


Das Einstiegsskript verwendet eine Hilfsfunktion, um eine [benutzerdefinierte RLlib-Rückruffunktion](https://docs.ray.io/en/latest/rllib-training.html#callbacks-and-custom-metrics) zu definieren, die Metriken in Ihrem Azure Machine Learning-Arbeitsbereich protokolliert. Erfahren Sie im Abschnitt [Überwachen und Anzeigen von Ergebnissen](#monitor-and-view-results) wie Sie diese Metriken anzeigen können.

```python
'''RLlib callbacks module:
    Common callback methods to be passed to RLlib trainer.
'''
from azureml.core import Run

def on_train_result(info):
    '''Callback on train result to record metrics returned by trainer.
    '''
    run = Run.get_context()
    run.log(
        name='episode_reward_mean',
        value=info["result"]["episode_reward_mean"])
    run.log(
        name='episodes_total',
        value=info["result"]["episodes_total"])
```

## <a name="submit-a-run"></a>Initiieren einer Ausführung

Eine [Ausführung](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) verarbeitet den Ausführungsverlauf laufender oder abgeschlossener Aufträge. 

```python
run = exp.submit(config=rl_estimator)
```
> [!NOTE]
> Die Ausführung kann 30 bis 45 Minuten in Anspruch nehmen.

## <a name="monitor-and-view-results"></a>Überwachen und Anzeigen von Ergebnissen

Verwenden Sie das Azure Machine Learning Jupyter-Widget, um den Status Ihrer Ausführungen in Echtzeit anzuzeigen. In diesem Beispiel zeigt das Widget zwei untergeordnete Ausführungen an: eine für Haupt- und eine für Workerausführungen. 

```python
from azureml.widgets import RunDetails

RunDetails(run).show()
run.wait_for_completion()
```

1. Warten Sie, bis das Widget geladen ist.
1. Wählen Sie in der Liste der Ausführungen die Hauptausführung aus.

Wählen Sie **Click here to see the run in Azure Machine Learning studio** (Klicken Sie hier, um die Ausführung in Azure Machine Learning Studio anzuzeigen), um im Studio weitere Ausführungsinformationen anzuzeigen. Sie können auf diese Informationen zugreifen, während die Ausführung ausgeführt wird oder nachdem sie abgeschlossen wurde.

![Liniendiagramm, das Ausführungsdetails des Widgets anzeigt](./media/how-to-use-reinforcement-learning/pong-run-details-widget.png)

Der Plot **episode_reward_mean** zeigt die durchschnittliche Anzahl der Punkte an, die pro Trainingsepoche erreicht wurden. Sie sehen, dass die Leistung des Trainings-Agents zu Beginn schlecht war und er die Spiele verloren hat, ohne auch nur einen Punkt zu bekommen (reward_mean: -21). Innerhalb von 100 Iterationen hat der Trainings-Agent gelernt, den Computergegner um einen durchschnittlichen Wert von 18 Punkten zu schlagen.

Wenn Sie Protokolle der untergeordneten Ausführung durchsuchen, sehen Sie die Auswertungsergebnisse, die in der Datei driver_log.txt erfasst wurden. Möglicherweise müssen Sie einige Minuten warten, bis diese Metriken auf der Ausführungsseite verfügbar werden.

In kurzer Zeit haben Sie gelernt, mehrere Computeressourcen zu konfigurieren, um einen Agent für Lernen durch Bestärkung so zu trainieren, dass er sehr gut Pong spielt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie einen Agent für Lernen durch Bestärkung mithilfe eines IMPALA-Agents trainieren. Weitere Beispiele finden Sie im [GitHub-Repository für Lernen durch Bestärkung von Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md).