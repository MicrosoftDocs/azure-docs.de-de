---
title: Verwenden von Softwareumgebungen
titleSuffix: Azure Machine Learning
description: Erstellen und Verwalten von Umgebungen für Modelltraining und -bereitstellung. Verwalten von Python-Paketen und anderen Einstellungen für die Umgebung.
services: machine-learning
author: saachigopal
ms.author: sagopal
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 07/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 4388c1a43cf958133356ed9d1ac80aec3ba0ce16
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100093640"
---
# <a name="create--use-software-environments-in-azure-machine-learning"></a>Erstellen und Verwenden von Softwareumgebungen in Azure Machine Learning


Erfahren Sie in diesem Artikel, wie Azure Machine Learning-[Umgebungen](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) erstellt und verwaltet werden. Verwenden Sie die Umgebungen, um die Softwareabhängigkeiten Ihrer Projekte in ihrem zeitlichen Ablauf nachzuverfolgen und zu reproduzieren.

Die Verwaltung von Softwareabhängigkeiten ist eine gängige Aufgabe für Entwickler. Sinnvollerweise möchten Sie sicherstellen, dass Builds ohne extensive manuelle Softwarekonfiguration reproduzierbar sind. Die Azure Machine Learning-Klasse `Environment` ist für lokale Entwicklungslösungen wie pip und Conda sowie über Docker-Funktionen für die verteilte Cloudentwicklung bestimmt.

Die Beispiele in diesem Artikel veranschaulichen Folgendes:

* Erstellen einer Umgebung und Angeben von Paketabhängigkeiten
* Abrufen und Aktualisieren von Umgebungen
* Verwenden einer Umgebung für Training
* Verwenden einer Umgebung für die Webdienstbereitstellung

Eine allgemeine Übersicht zur Funktionsweise von Umgebungen in Azure Machine Learning finden Sie unter [Was sind ML-Umgebungen?](concept-environments.md). Weitere Informationen zum Konfigurieren von Entwicklungsumgebungen finden Sie [hier](how-to-configure-environment.md).

## <a name="prerequisites"></a>Voraussetzungen

* Das [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (mindestens 1.13.0)
* Ein [Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md).

## <a name="create-an-environment"></a>Erstellen einer Umgebung

In den folgenden Abschnitten werden die verschiedenen Möglichkeiten erläutert, wie Sie eine Umgebung für Ihre Experimente erstellen können.

### <a name="instantiate-an-environment-object"></a>Instanziieren eines Umgebungsobjekts

Zum manuellen Erstellen einer Umgebung importieren Sie die `Environment`-Klasse aus dem SDK. Verwenden Sie anschließend den folgenden Code, um ein Umgebungsobjekt zu instanziieren.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-a-curated-environment"></a>Verwenden einer zusammengestellten Umgebung

Zusammengestellte Umgebungen enthalten Sammlungen mit Python-Paketen und sind standardmäßig in Ihrem Arbeitsbereich verfügbar. Diese Umgebungen werden durch zwischengespeicherte Docker-Images unterstützt, wodurch die Kosten für die Vorbereitung der Ausführung reduziert werden. Sie können für den Einstieg eine dieser beliebten zusammengestellten Umgebungen auswählen: 

* Die _AzureML-Minimal_-Umgebung enthält einen minimalen Satz von Paketen, um die Nachverfolgung von Durchläufen und das Hochladen von Ressourcen zu ermöglichen. Sie können sie als Ausgangspunkt für Ihre eigene Umgebung verwenden.

* Die _AzureML-Tutorial_-Umgebung enthält allgemeine Data Science-Pakete. Zu diesen Paketen gehören Scikit-Learn, Pandas, Matplotlib und eine größere Menge an azureml-sdk-Paketen.

Eine Liste der zusammengestellten Umgebungen finden Sie im [Artikel zu zusammengestellten Umgebungen](resource-curated-environments.md).

Verwenden Sie die `Environment.get`-Methode, um eine der zusammengestellten Umgebungen auszuwählen:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Sie können die zusammengestellten Umgebungen und deren Pakete mit folgendem Code auflisten:

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Beginnen Sie den Namen Ihrer eigenen Umgebung nicht mit dem Präfix _AzureML_. Dieses Präfix ist für zusammengestellte Umgebungen reserviert.

### <a name="use-conda-dependencies-or-pip-requirements-files"></a>Verwenden von Conda-Abhängigkeiten oder pip-Anforderungsdateien

Sie können eine Umgebung aus einer Conda-Spezifikations- oder einer pip-Anforderungsdatei erstellen. Verwenden Sie die [`from_conda_specification()`](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#&preserve-view=truefrom-conda-specification-name--file-path-)-Methode oder die [`from_pip_requirements()`](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#&preserve-view=truefrom-pip-requirements-name--file-path-)-Methode. Schließen Sie den Namen Ihrer Umgebung und den Dateipfad der gewünschten Datei in das Methodenargument ein. 

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv",
                                          file_path = "path-to-pip-requirements-file")                                          
```

### <a name="enable-docker"></a>Aktivieren von Docker

Wenn Sie Docker aktivieren, erstellt Azure Machine Learning ein Docker-Image und eine Python-Umgebung innerhalb dieses Containers gemäß Ihren Spezifikationen. Die Docker-Images werden zwischengespeichert und wiederverwendet: Die erste Ausführung in einer neuen Umgebung dauert in der Regel länger, da das Image erstellt wird.

Die [`DockerSection`](/python/api/azureml-core/azureml.core.environment.dockersection?preserve-view=true&view=azure-ml-py) der Azure Machine Learning-Klasse `Environment` ermöglicht es Ihnen, das Gastbetriebssystem, in dem Ihr Trainingslauf ausgeführt wird, im Detail anzupassen und zu steuern. Mithilfe der Variable `arguments` können zusätzliche Argumente angegeben werden, die an den Docker-Ausführungsbefehl übergeben werden sollen.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Standardmäßig wird das neu erstellte Docker-Image in der Containerregistrierung angezeigt, die dem Arbeitsbereich zugeordnet ist.  Der Repositoryname hat das Format *azureml/azureml_\<uuid\>* . Der *UUID*-Anteil (universally unique identifier, global eindeutiger Bezeichner) des Namens entspricht einem Hash, der aus der Umgebungskonfiguration errechnet wird. Diese Entsprechung ermöglicht es dem Dienst, zu ermitteln, ob bereits ein Image der betreffenden Umgebung zur Wiederverwendung vorhanden ist.

#### <a name="use-a-prebuilt-docker-image"></a>Verwenden eines vorgefertigten Docker-Images

Standardmäßig verwendet der Dienst automatisch eines der Ubuntu Linux-basierten [Basisimages](https://github.com/Azure/AzureML-Containers), insbesondere das von `azureml.core.environment.DEFAULT_CPU_IMAGE` definierte Image. Anschließend werden alle angegebenen Python-Pakete installiert, die von der bereitgestellten Azure ML-Umgebung definiert sind. Weitere Azure ML-CPU- und -GPU-Basisimages sind im [Containerrepository](https://github.com/Azure/AzureML-Containers) verfügbar. Es ist auch möglich, ein [benutzerdefiniertes Docker-Basisimage](./how-to-deploy-custom-docker-image.md#create-a-custom-base-image) zu verwenden.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

>[!IMPORTANT]
> Azure Machine Learning unterstützt nur Docker-Images, die die folgende Software enthalten:
> * Ubuntu 16.04 oder höher.
> * Conda 4.5.# oder höher.
> * Python 3.5 oder höher.

#### <a name="use-your-own-dockerfile"></a>Verwenden Ihres eigenen Dockerfiles 

Sie können auch eine benutzerdefinierte Dockerfile angeben. Am einfachsten ist es, von einem der Azure Machine Learning-Basisimages mit dem Docker-Befehl ```FROM``` zu beginnen und dann Ihre eigenen benutzerdefinierten Schritte hinzuzufügen. Verwenden Sie diesen Ansatz, wenn Sie Nicht-Python-Pakete als Abhängigkeiten installieren müssen. Denken Sie daran, das Basisimage auf „Kein“ festzulegen.

Beachten Sie, dass Python eine implizite Abhängigkeit in Azure Machine Learning ist, sodass Python auf einer benutzerdefinierten Dockerfile-Datei installiert werden muss.

```python
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = "./Dockerfile"
```

Bei Verwenden benutzerdefinierter Docker-Images empfiehlt es sich, Paketversionen anzuheften, um Reproduzierbarkeit besser zu gewährleisten.

#### <a name="specify-your-own-python-interpreter"></a>Angeben eines eigenen Python-Interpreters

In einigen Situationen kann Ihr benutzerdefiniertes Basisimage bereits eine Python-Umgebung mit Paketen enthalten, die Sie verwenden möchten.

Legen Sie den Parameter `Environment.python.user_managed_dependencies = True` fest, um Ihre eigenen installierten Pakete zu verwenden und Conda zu deaktivieren. Stellen Sie sicher, dass das Basisimage einen Python-Interpreter enthält und über die Pakete verfügt, die Ihr Trainingsskript benötigt.

Für die Ausführung in einer grundlegenden Miniconda-Umgebung, in der das NumPy-Paket installiert ist, geben Sie z. B. zunächst eine Dockerfile mit einem Schritt zur Installation des Pakets an. Legen Sie dann die vom Benutzer verwalteten Abhängigkeiten auf `True` fest. 

Sie können auch einen Pfad zu einem bestimmten Python-Interpreter innerhalb des Images angeben, indem Sie die Variable `Environment.python.interpreter_path` festlegen.

```python
dockerfile = """
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN conda install numpy
"""

myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
myenv.python.user_managed_dependencies=True
myenv.python.interpreter_path = "/opt/miniconda/bin/python"
```

> [!WARNING]
> Wenn Sie einige Python-Abhängigkeiten in Ihrem Docker-Image installieren und vergessen, `user_managed_dependencies=True` festzulegen, werden diese Pakete in der Ausführungsumgebung nicht vorhanden sein, was zu Laufzeitfehlern führt. Standardmäßig erstellt Azure ML eine Conda-Umgebung mit den von Ihnen angegebenen Abhängigkeiten und führt die Ausführung in dieser Umgebung aus, anstatt die Python-Bibliotheken zu verwenden, die Sie auf dem Basisimage installiert haben.

#### <a name="retrieve-image-details"></a>Abrufen von Imagedetails

Für eine registrierte Umgebung können Sie die Imagedetails mithilfe des folgenden Codes abrufen, wobei `details` eine Instanz von [DockerImageDetails](/python/api/azureml-core/azureml.core.environment.dockerimagedetails?preserve-view=true&view=azure-ml-py) (AzureML Python SDK >= 1.11) ist und alle Informationen über das Umgebungsimage bereitstellt, z. B. die Dockerfile-Datei, die Registrierung und den Imagenamen.

```python
details = environment.get_image_details(workspace=ws)
```

Verwenden Sie den folgenden Code, um die Imagedetails aus einer Umgebung abzurufen, die automatisch aus einer Ausführung gespeichert wurde:

```python
details = run.get_environment().get_image_details(workspace=ws)
```

### <a name="use-existing-environments"></a>Verwenden vorhandener Umgebungen

Wenn Sie über eine vorhandene Conda-Umgebung auf Ihrem lokalen Computer verfügen, können Sie den Dienst verwenden, um ein Umgebungsobjekt zu erstellen. Mit dieser Strategie können Sie Ihre lokale interaktive Umgebung bei Remoteausführungen wiederverwenden.

Der folgende Code erstellt ein Umgebungsobjekt aus der vorhandenen Conda-Umgebung `mycondaenv`. Dabei wird die [`from_existing_conda_environment()`](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#&preserve-view=truefrom-existing-conda-environment-name--conda-environment-name-)-Methode verwendet.

``` python
myenv = Environment.from_existing_conda_environment(name="myenv",
                                                    conda_environment_name="mycondaenv")
```

Eine Umgebungsdefinition kann mit der [`save_to_directory()`](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#&preserve-view=truesave-to-directory-path--overwrite-false-)-Methode in einem einfach bearbeitbaren Format in einem Verzeichnis gespeichert werden. Nach der Änderung kann durch Laden von Dateien aus dem Verzeichnis eine neue Umgebung instanziiert werden.

```python
myenv = Environment.save_to_directory(path="path-to-destination-directory", overwrite=False)
# modify the environment definition
newenv = Environment.load_from_directory(path="path-to-source-directory")
```

### <a name="implicitly-use-the-default-environment"></a>Implizites Verwenden der Standardumgebung

Wenn Sie in Ihrer Skriptausführungskonfiguration keine Umgebung angeben, bevor Sie die Ausführung übermitteln, wird eine Standardumgebung für Sie erstellt.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
src = ScriptRunConfig(source_directory=".", script="example.py", compute_target="local")

# Submit the run
run = myexp.submit(config=src)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

## <a name="add-packages-to-an-environment"></a>Hinzufügen von Paketen zu einer Umgebung

Sie fügen einer Umgebung Pakete mithilfe von Conda-, pip- oder Private Wheels-Dateien hinzu. Geben Sie jede Paketabhängigkeit mithilfe der [`CondaDependency`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?preserve-view=true&view=azure-ml-py)-Klasse an. Fügen Sie es der `PythonSection` der Umgebung hinzu.

### <a name="conda-and-pip-packages"></a>Conda- und pip-Pakete

Wenn ein Paket in einem Conda-Paketrepository verfügbar ist, empfehlen wir, die Conda-Installation anstelle der pip-Installation zu verwenden. Conda-Pakete verfügen in der Regel über vorgefertigte Binärdateien, die die Installation zuverlässiger machen.

Das folgende Beispiel zeigt eine Hinzufügung zur Umgebung `myenv`. Sie fügt Version 1.17.0 von `numpy` hinzu. Außerdem wird das `pillow`-Paket hinzugefügt. Im Beispiel wird die [`add_conda_package()`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?preserve-view=true&view=azure-ml-py#&preserve-view=trueadd-conda-package-conda-package-)-Methode bzw. die [`add_pip_package()`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?preserve-view=true&view=azure-ml-py#&preserve-view=trueadd-pip-package-pip-package-)-Methode verwendet.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs numpy version 1.17.0 conda package
conda_dep.add_conda_package("numpy==1.17.0")

# Installs pillow package
conda_dep.add_pip_package("pillow")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

Sie können auch Umgebungsvariablen zu Ihrer Umgebung hinzufügen. Diese werden dann über „os.environ.get“ in Ihrem Trainingsskript verfügbar.

```python
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

>[!IMPORTANT]
> Wenn Sie dieselbe Umgebungsdefinition für eine weitere Ausführung verwenden, verwendet der Azure Machine Learning Service das zwischengespeicherte Image aus Ihrer Umgebung wieder. Wenn Sie eine Umgebung mit losgelöster Paketabhängigkeit erstellen, z. B. ```numpy```, wird diese Umgebung weiterhin die Paketversion verwenden, _die zum Zeitpunkt der Erstellung der Umgebung installiert war_. Außerdem wird jede zukünftige Umgebung mit passender Definition weiterhin die alte Version verwenden. Weitere Informationen finden Sie unter [Erstellen, Zwischenspeichern und Wiederverwenden von Umgebungen](./concept-environments.md#environment-building-caching-and-reuse).

### <a name="private-python-packages"></a>Private Python-Pakete

Informationen dazu, wie Sie Python-Pakete privat und sicher verwenden, ohne sie im öffentlichen Internet verfügbar zu machen, finden Sie im Artikel [Verwenden von privaten Python-Paketen](how-to-use-private-python-packages.md).

## <a name="manage-environments"></a>Verwalten von Umgebungen

Verwalten Sie Umgebungen, damit Sie sie über Computeziele hinweg und mit anderen Benutzern des Arbeitsbereichs zusammen aktualisieren, nachverfolgen und wiederverwenden können.

### <a name="register-environments"></a>Registrieren von Umgebungen

Die Umgebung wird automatisch bei Ihrem Arbeitsbereich registriert, wenn Sie einen Lauf übermitteln oder einen Webdienst bereitstellen. Sie können die Umgebung auch manuell mithilfe der [`register()`](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-workspace-)-Methode registrieren. Durch diesen Vorgang wird die Umgebung zu einer Entität, die in der Cloud nachverfolgt und versionsverwaltet wird. Die Entität kann von Arbeitsbereichsbenutzern gemeinsam genutzt werden.

Mit folgendem Code wird die `myenv`-Umgebung beim `ws`-Arbeitsbereich registriert.

```python
myenv.register(workspace=ws)
```

Wenn Sie die Umgebung zum ersten Mal in Training oder Bereitstellung verwenden, wird sie beim Arbeitsbereich registriert. Anschließend wird sie auf dem Computeziel erstellt und bereitgestellt. Der Dienst speichert die Umgebungen zwischen. Die Wiederverwendung einer zwischengespeicherten Umgebung braucht viel weniger Zeit als die Verwendung eines neuen oder aktualisierten Diensts.

### <a name="get-existing-environments"></a>Abrufen vorhandener Umgebungen

Die `Environment`-Klasse bietet Methoden, mit denen Sie vorhandene Umgebungen in Ihrem Arbeitsbereich abrufen können. Sie können Umgebungen anhand des Namens, als Liste oder nach einem bestimmten Trainingslauf abrufen. Diese Informationen sind für Problembehandlung, Überwachung und Reproduzierbarkeit nützlich.

#### <a name="view-a-list-of-environments"></a>Anzeigen einer Liste der Umgebungen

Zeigen Sie die Umgebungen in Ihrem Arbeitsbereich mithilfe der [`Environment.list(workspace="workspace_name")`](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truelist-workspace-)-Klasse an. Wählen Sie dann eine wiederzuverwendende Umgebung aus.

#### <a name="get-an-environment-by-name"></a>Abrufen einer Umgebung anhand des Namens

Sie können auch eine bestimmte Umgebung mittels Name und Version abrufen. Der folgende Code verwendet die [`get()`](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-workspace--name--version-none-)-Methode, um Version `1` der `myenv`-Umgebung im Arbeitsbereich `ws` abzurufen.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Trainieren einer laufspezifischen Umgebung

Um nach dem Abschluss des Trainings die Umgebung abzurufen, die für einen bestimmten Lauf verwendet wurde, verwenden Sie die [`get_environment()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-environment--)-Methode der `Run`-Klasse.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Aktualisieren einer vorhandenen Umgebung

Angenommen, Sie ändern eine vorhandene Umgebung, beispielsweise durch Hinzufügen eines Python-Pakets. Die Kompilierung nimmt einige Zeit in Anspruch, da eine neue Version der Umgebung erstellt wird, wenn Sie eine Ausführung übermitteln, ein Modell bereitstellen oder die Umgebung manuell registrieren. Mithilfe der Versionsverwaltung können Sie die Änderungen an der Umgebung im zeitlichen Verlauf anzeigen. 

Um die Version eines Python-Pakets einer vorhandenen Umgebung zu aktualisieren, geben Sie die Versionsnummer für dieses Paket an. Wenn Sie nicht die genaue Versionsnummer angeben, führt Azure Machine Learning eine Wiederverwendung der vorhandenen Umgebung mit ihren ursprünglichen Paketversionen aus.

### <a name="debug-the-image-build"></a>Debuggen der Imageerstellung

Das folgende Beispiel verwendet die [`build()`](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truebuild-workspace--image-build-compute-none-)-Methode, um eine Umgebung manuell als Docker-Image zu erstellen. Die Ausgabeprotokolle der Imageerstellung werden mithilfe von [`wait_for_completion()`](/python/api/azureml-core/azureml.core.image%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truewait-for-creation-show-output-false-) überwacht. Das erstellte Image wird dann in der Azure Container Registry-Instanz des Arbeitsbereichs angezeigt. Diese Informationen sind beim Debuggen nützlich.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

Es ist hilfreich, Images mithilfe der [`build_local()`](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#&preserve-view=truebuild-local-workspace--platform-none----kwargs-)-Methode zuerst lokal zu kompilieren. Legen Sie zum Erstellen eines Docker-Images den optionalen Parameter `useDocker=True` fest. Um das sich ergebende Image in die Containerregistrierung des AzureML-Arbeitsbereichs zu pushen, legen Sie `pushImageToWorkspaceAcr=True` fest.

```python
build = env.build_local(workspace=ws, useDocker=True, pushImageToWorkspaceAcr=True)
```

> [!WARNING]
>  Das Ändern der Reihenfolge von Abhängigkeiten oder Kanälen in einer Umgebung führt zu einer neuen Umgebung und erfordert einen neuen Imagebuild. Außerdem werden beim Aufrufen der `build()`-Methode für ein vorhandenes Image die Abhängigkeiten aktualisiert, wenn neue Versionen vorhanden sind. 

## <a name="use-environments-for-training"></a>Verwenden von Umgebungen für das Training

Zum Übermitteln eines Trainingslaufs müssen Sie Ihre Umgebung, das [Computeziel](concept-compute-target.md) und das Python-Trainingsskript zu einer Ausführungskonfiguration kombinieren. Diese Konfiguration ist ein Wrapper-Objekt, das zum Übermitteln von Läufen verwendet wird.

Wenn Sie einen Trainingslauf übermitteln, kann das Erstellen einer neuen Umgebung einige Minuten dauern. Die Dauer hängt von der Größe der erforderlichen Abhängigkeiten ab. Die Umgebungen werden vom Dienst zwischengespeichert. Sofern die Umgebungsdefinition unverändert bleibt, wird die gesamte Setupzeit nur einmal in Anspruch genommen.

Das folgende Beispiel einer lokalen Skriptausführung zeigt, wo Sie [`ScriptRunConfig`](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?preserve-view=true&view=azure-ml-py) als Wrapper-Objekt verwenden würden.

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Configure the ScriptRunConfig and specify the environment
src = ScriptRunConfig(source_directory=".", script="train.py", target="local", environment=myenv)

# Submit run 
run = exp.submit(src)
```

> [!NOTE]
> Um den Ausführungsverlauf oder Momentaufnahmen der Ausführung zu deaktivieren, verwenden Sie die Einstellung unter `src.run_config.history`.

Wenn Sie in Ihrer Ausführungskonfiguration keine Umgebung angeben, erstellt der Dienst eine Standardumgebung für Sie, wenn Sie Ihren Lauf übermitteln.

## <a name="use-environments-for-web-service-deployment"></a>Verwenden von Umgebungen für die Webdienstbereitstellung

Sie können Umgebungen verwenden, wenn Sie Ihr Modell als Webdienst bereitstellen. Diese Funktion ermöglicht einen reproduzierbaren, verbundenen Workflow. In diesem Workflow können Sie das Modell trainieren, testen und bereitstellen, indem Sie die gleichen Bibliotheken sowohl für das Trainingscompute als auch für das Rückschlusscompute verwenden.


Beim Definieren einer eigenen Umgebung für die Webdienstbereitstellung müssen Sie `azureml-defaults` mit der Version >= 1.0.45 als pip-Abhängigkeit auflisten. Dieses Paket enthält die erforderlichen Funktionen zum Hosten des Modells als Webdienst.

Zum Bereitstellen eines Webdiensts kombinieren Sie die Umgebung, das Rückschlusscompute, das Bewertungsskript und das registrierte Modell in Ihrem Bereitstellungsobjekt [`deploy()`](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-). Weitere Informationen finden Sie unter [Wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).

Nehmen wir in diesem Beispiel an, dass Sie einen Trainingslauf abgeschlossen haben. Nun möchten Sie dieses Modell in Azure Container Instances bereitstellen. Beim Erstellen des Webdiensts werden die Modell- und Bewertungsdateien in das Image eingebunden, und der Azure Machine Learning-Rückschlussstapel wird dem Image hinzugefügt.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference, & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="notebooks"></a>Notebooks

Dieser [Artikel](./how-to-access-terminal.md#add-new-kernels) enthält Informationen darüber, wie Sie eine Conda-Umgebung als Kernel in einem Notebook installieren können.

[Bereitstellen eines Modells mithilfe eines benutzerdefinierten Docker-Basisimages](how-to-deploy-custom-docker-image.md) veranschaulicht, wie ein Modell mit einem benutzerdefinierten Docker-Basisimage bereitgestellt wird.

Dieses [Beispielnotebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) zeigt, wie ein Spark-Modell als Webdienst bereitgestellt werden kann.

## <a name="create-and-manage-environments-with-the-cli"></a>Erstellen und Verwalten von Umgebungen mit der Befehlszeilenschnittstelle

Die [Azure Machine Learning-CLI](reference-azure-machine-learning-cli.md) spiegelt den größten Teil der Funktionalität des Python SDK. Sie können sie zum Erstellen und Verwalten von Umgebungen verwenden. Die in diesem Abschnitt behandelten Befehle veranschaulichen die grundlegende Funktionalität.

Das folgende Befehlsgerüst stellt die Dateien für eine standardmäßige Umgebungsdefinition im angegebenen Verzeichnis dar. Diese Dateien sind JSON-Dateien. Sie funktionieren wie die entsprechende Klasse im SDK. Sie können die Dateien verwenden, um neue Umgebungen zu erstellen, die benutzerdefinierte Einstellungen aufweisen. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Führen Sie den folgenden Befehl aus, um eine Umgebung aus einem bestimmten Verzeichnis zu registrieren.

```azurecli-interactive
az ml environment register -d myenvdir
```

Führen Sie den folgenden Befehl aus, um alle registrierten Umgebungen aufzulisten.

```azurecli-interactive
az ml environment list
```

Laden Sie mit dem folgenden Befehl eine registrierte Umgebung herunter.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Verwenden eines verwalteten Computeziels zum Trainieren eines Modells finden Sie unter [Tutorial: Trainieren eines Modells](tutorial-train-models-with-aml.md).
* Erfahren Sie nach dem Erstellen eines trainierten Modells, [wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).
* Sehen Sie sich die [SDK-Referenz der `Environment`-Klasse](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py) an.
