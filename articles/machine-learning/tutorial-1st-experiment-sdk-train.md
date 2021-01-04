---
title: 'Tutorial: Trainieren Ihres ersten Machine Learning-Modells – Python'
titleSuffix: Azure Machine Learning
description: In Teil 3 der Einstiegsreihe zu Azure Machine Learning wird veranschaulicht, wie ein Machine Learning-Modell trainiert wird.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: a2f76df05878151405d56b1bff5e588f426d9d92
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093552"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-3-of-4"></a>Tutorial: Trainieren Ihres ersten Machine Learning-Modells (Teil 3 von 4)

In diesem Tutorial wird gezeigt, wie Sie ein Machine Learning-Modell in Azure Machine Learning trainieren.

Dieses Tutorial ist *Teil 3 einer vierteiligen Tutorialreihe*, in der Sie die Grundlagen von Azure Machine Learning kennenlernen und auftragsbasierte Machine Learning-Aufgaben in Azure durchführen. Dieses Tutorial basiert auf den Schritten aus [Teil 1: Einrichten](tutorial-1st-experiment-sdk-setup-local.md) und [Teil 2: Ausführen von „Hello World!“](tutorial-1st-experiment-hello-world.md) der Reihe.

In diesem Tutorial führen Sie den nächsten Schritt aus, indem Sie ein Skript senden, das ein Machine Learning-Modell trainiert. Dieses Beispiel hilft Ihnen zu verstehen, wie Azure Machine Learning ein konsistentes Verhalten zwischen lokalem Debuggen und Remoteausführungen erleichtert.

In diesem Tutorial führen Sie Folgendes durch:

> [!div class="checklist"]
> * Erstellen Sie ein Trainingsskript.
> * Definieren einer Azure Machine Learning-Umgebung mit Conda
> * Erstellen eines Steuerungsskripts
> * Kennenlernen der Azure Machine Learning-Klassen (`Environment`, `Run`, `Metrics`)
> * Senden und Ausführen des Trainingsskripts
> * Anzeigen Ihrer Codeausgabe in der Cloud
> * Protokollieren von Metriken in Azure Machine Learning
> * Anzeigen der Metriken in der Cloud

## <a name="prerequisites"></a>Voraussetzungen

* Durcharbeitung von [Teil 2](tutorial-1st-experiment-hello-world.md) der Reihe.
* Kenntnisse der Python-Sprache und der Machine Learning-Workflows auf Einstiegsniveau.
* Lokale Entwicklungsumgebung, z. B. Visual Studio Code, Jupyter oder PyCharm.
* Python (Version 3.5 bis 3.7).

## <a name="create-training-scripts"></a>Erstellen der Trainingsskripts

Zuerst definieren Sie die Architektur des neuronalen Netzwerks in einer `model.py`-Datei. Der gesamte Trainingscode (einschließlich `model.py`) wird im Unterverzeichnis `src` gespeichert.

Der folgende Code stammt aus [diesem Einführungsbeispiel](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) von PyTorch. Beachten Sie, dass die Azure Machine Learning-Konzepte für beliebigen Machine Learning-Code gelten und nicht nur für PyTorch.

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/model.py":::

Als Nächstes definieren Sie das Trainingsskript. Mit diesem Skript wird das CIFAR10-Dataset mithilfe der PyTorch-`torchvision.dataset`-APIs heruntergeladen, das in `model.py` definierte Netzwerk eingerichtet und zwei Epochen lang das Training mit Standard-SGD und Kreuzentropieverlust durchgeführt.

Erstellen Sie ein `train.py`-Skript im Unterverzeichnis `src`:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/train.py":::

Sie verfügen jetzt über die folgende Verzeichnisstruktur:

```txt
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
```

> [!div class="nextstepaction"]
> [Ich habe die Trainingsskripts erstellt.](?success=create-scripts#environment) [Es ist ein Problem aufgetreten.](https://www.research.net/r/7CTJQQN?issue=create-scripts)

## <a name="create-a-python-environment"></a><a name="environment"></a> Erstellen einer Python-Umgebung

Zu Demonstrationszwecken verwenden wir eine Conda-Umgebung. (Die Schritte für eine virtuelle PIP-Umgebung sind fast identisch.)

Erstellen Sie im verborgenen Verzeichnis `.azureml` eine Datei mit dem Namen `pytorch-env.yml`.

:::code language="yml" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/environments/pytorch-env.yml":::

Diese Umgebung verfügt über alle Abhängigkeiten, die für das Modell und das Trainingsskript erforderlich sind. Beachten Sie, dass keine Abhängigkeit vom Azure Machine Learning SDK für Python vorhanden ist.

> [!div class="nextstepaction"]
> [Ich habe die Umgebungsdatei erstellt.](?success=create-env-file#test-local) [Es ist ein Problem aufgetreten.](https://www.research.net/r/7CTJQQN?issue=create-env-file)

## <a name="test-locally"></a><a name="test-local"></a> Lokales Testen

Verwenden Sie den folgenden Code, um zu testen, ob Ihr Skript lokal in dieser Umgebung ausgeführt wird:

```bash
conda env create -f .azureml/pytorch-env.yml    # create conda environment
conda activate pytorch-env                      # activate conda environment
python src/train.py                             # train model
```

Nachdem Sie dieses Skript ausgeführt haben, werden die Daten in ein Verzeichnis mit dem Namen `tutorial/data` heruntergeladen.

> [!div class="nextstepaction"]
> [Ich habe die Umgebungsdatei erstellt.](?success=test-local#create-local) [Es ist ein Problem aufgetreten.](https://www.research.net/r/7CTJQQN?issue=test-local)

## <a name="create-the-control-script"></a><a name="create-local"></a> Erstellen des Steuerungsskripts

Der Unterschied zwischen dem folgenden Steuerungsskript und dem Skript, das Sie zum Übermitteln von „Hello World!“ verwendet haben, besteht darin, dass Sie einige zusätzliche Zeilen zum Festlegen der Umgebung hinzufügen.

Erstellen Sie eine neue Python-Datei im Verzeichnis `tutorial` mit dem Namen `04-run-pytorch.py`:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/04-run-pytorch.py":::

### <a name="understand-the-code-changes"></a>Erläuterung der Codeänderungen

:::row:::
   :::column span="":::
      `env = ...`
   :::column-end:::
   :::column span="2":::
      Bei Azure Machine Learning wird das Konzept einer [Umgebung](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) verwendet, um eine reproduzierbare versionierte Python-Umgebung zum Ausführen von Experimenten darzustellen. Eine Umgebung kann ganz einfach aus einer lokalen Conda- oder PIP-Umgebung erstellt werden.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      Fügt die Umgebung der [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py)-Klasse hinzu.
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Ich habe das Steuerungsskript erstellt.](?success=control-script#submit) [Es ist ein Problem aufgetreten.](https://www.research.net/r/7CTJQQN?issue=control-script)


## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit"></a> Senden der Ausführung an Azure Machine Learning

Falls Sie die lokale Umgebung gewechselt haben, sollten Sie zurück zu einer Umgebung navigieren, in der das Azure Machine Learning SDK für Python installiert ist.

Führen Sie dann Folgendes aus:

```bash
python 04-run-pytorch.py
```

>[!NOTE] 
> Wenn Sie dieses Skript zum ersten Mal ausführen, erstellt Azure Machine Learning ein neues Docker-Image aus Ihrer PyTorch-Umgebung. Die gesamte Ausführung kann fünf bis zehn Minuten dauern. 
>
> Die Docker-Buildprotokolle werden in Azure Machine Learning Studio angezeigt. Folgen Sie dem Link zu Studio, und wählen Sie die Registerkarte **Ausgaben und Protokolle** und dann `20_image_build_log.txt` aus.
>
> Dieses Image wird auch in zukünftigen Ausführungen verwendet, um die Dauer deutlich zu verkürzen.

Nachdem das Image erstellt wurde, wählen Sie `70_driver_log.txt` aus, um die Ausgabe des Trainingsskripts anzuzeigen.

```txt
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
...
Files already downloaded and verified
epoch=1, batch= 2000: loss 2.19
epoch=1, batch= 4000: loss 1.82
epoch=1, batch= 6000: loss 1.66
epoch=1, batch= 8000: loss 1.58
epoch=1, batch=10000: loss 1.52
epoch=1, batch=12000: loss 1.47
epoch=2, batch= 2000: loss 1.39
epoch=2, batch= 4000: loss 1.38
epoch=2, batch= 6000: loss 1.37
epoch=2, batch= 8000: loss 1.33
epoch=2, batch=10000: loss 1.31
epoch=2, batch=12000: loss 1.27
Finished Training
```

> [!WARNING]
> Wenn der Fehler `Your total snapshot size exceeds the limit` angezeigt wird, befindet sich das Verzeichnis `data` unter dem Wert `source_directory`, der in `ScriptRunConfig` verwendet wird.
>
> Verschieben Sie `data` aus `src` heraus.

Umgebungen können mit `env.register(ws)` für einen Arbeitsbereich registriert werden. Anschließend können sie problemlos freigegeben, wiederverwendet und versioniert werden. Umgebungen erleichtern das Reproduzieren vorheriger Ergebnisse und die Zusammenarbeit im Team.

Azure Machine Learning bietet auch eine Sammlung zusammengestellter Umgebungen. Solche Umgebungen sind für gängige Machine Learning-Szenarien verfügbar und werden durch zwischengespeicherte Docker-Images unterstützt. Zwischengespeicherte Docker-Images beschleunigen die erste Remoteausführung.

Kurz gesagt: Mit registrierten Umgebungen können Sie Zeit sparen. Weitere Informationen finden Sie unter [Verwenden von Umgebungen](./how-to-use-environments.md).

> [!div class="nextstepaction"]
> [Ich habe die Ausführung übermittelt.](?success=test-w-environment#log) [Es ist ein Problem aufgetreten.](https://www.research.net/r/7CTJQQN?issue=test-w-environment)

## <a name="log-training-metrics"></a><a name="log"></a> Protokollieren von Trainingsmetriken

Nachdem Sie nun über ein Modelltraining in Azure Machine Learning verfügen, beginnen Sie mit dem Nachverfolgen einiger Leistungsmetriken.

Das aktuelle Trainingsskript gibt Metriken auf dem Terminal aus. Azure Machine Learning bietet einen Mechanismus zum Protokollieren von Metriken mit weiteren Funktionen. Durch Hinzufügen von wenigen Codezeilen können Sie Metriken in Studio visualisieren und Metriken aus mehreren Ausführungen vergleichen.

### <a name="modify-trainpy-to-include-logging"></a>Ändern Sie `train.py` so, dass die Protokollierung eingeschlossen wird.

Ändern Sie Ihr Skript `train.py` so, dass es zwei weitere Codezeilen enthält:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-train-with-logging/train.py":::


#### <a name="understand-the-additional-two-lines-of-code"></a>Erläuterungen zu den beiden zusätzlichen Codezeilen

In `train.py` greifen Sie _innerhalb_ des Trainingsskripts selbst mit der Methode `Run.get_context()` auf das Run-Objekt zu und verwenden es zum Protokollieren der Metriken:

```python
# in train.py
run = Run.get_context()

...

run.log('loss', loss)
```

Vorteile der Metriken in Azure Machine Learning:

- Metriken sind nach Experiment und Ausführung strukturiert und können daher leicht nachverfolgt und verglichen werden.
- Sie verfügen über eine Benutzeroberfläche zur Visualisierung der Trainingsleistung in Studio.
- Sie sind skalierbar konzipiert, sodass Sie auch bei Hunderten von Experimenten von diesen Vorteilen profitieren.

> [!div class="nextstepaction"]
> [Ich habe „train.py“ geändert. ](?success=modify-train#log) [Es ist ein Problem aufgetreten.](https://www.research.net/r/7CTJQQN?issue=modify-train)

### <a name="update-the-conda-environment-file"></a>Aktualisieren der Conda-Umgebungsdatei

Das `train.py`-Skript weist eine neue Abhängigkeit von `azureml.core` auf. Aktualisieren Sie `pytorch-env.yml`, sodass diese Änderung widergespiegelt wird:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/configuration/pytorch-aml-env.yml":::

> [!div class="nextstepaction"]
> [Ich habe die Umgebungsdatei aktualisiert.](?success=update-environment#submit-again) [Es ist ein Problem aufgetreten.](https://www.research.net/r/7CTJQQN?issue=update-environment)

### <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-again"></a> Senden der Ausführung an Azure Machine Learning
Übermitteln Sie dieses Skript noch einmal:

```bash
python 04-run-pytorch.py
```

Bei diesem Zugriff auf Studio öffnen Sie die Registerkarte **Metriken**, auf der nun Liveupdates zum Modelltrainingsverlust angezeigt werden.

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="Diagramm: Trainingsverlust auf der Registerkarte „Metriken“":::

> [!div class="nextstepaction"]
> [Ich habe die Ausführung erneut übermittelt.](?success=resubmit-with-logging#next-steps) [Es ist ein Problem aufgetreten.](https://www.research.net/r/7CTJQQN?issue=resubmit-with-logging)

## <a name="next-steps"></a>Nächste Schritte

In dieser Sitzung haben Sie ein Upgrade von einem einfachen „Hello World!“-Skript zu einem realistischeren Trainingsskript durchgeführt, das eine bestimmte Python-Umgebung für die Ausführung benötigt. Sie haben gesehen, wie Sie mit Azure Machine Learning-Umgebungen eine lokale Conda-Umgebung in die Cloud verlagern. Schließlich haben Sie erfahren, wie Sie mit einigen wenigen Codezeilen Metriken für Azure Machine Learning protokollieren können.

Es gibt weitere Möglichkeiten, Azure Machine Learning-Umgebungen zu erstellen, z. B. [mit der PIP-Datei „requirements.txt“](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#from-pip-requirements-name--file-path-) oder [aus einer vorhandenen lokalen Conda-Umgebung](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-).

In der nächsten Sitzung sehen Sie, wie Sie mit Daten in Azure Machine Learning arbeiten, indem Sie das CIFAR10-Dataset in Azure hochladen.

> [!div class="nextstepaction"]
> [Tutorial: Verwenden eigener Daten](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> Wenn Sie die Tutorialreihe hier beenden und nicht mit dem nächsten Schritt fortfahren möchten, sollten Sie nicht vergessen, [Ihre Ressourcen zu bereinigen](tutorial-1st-experiment-bring-data.md#clean-up-resources).