---
title: Deep Learning- und KI-Frameworks – Azure | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Deep Learning-Frameworks und -Tools, die auf der Data Science Virtual Machine unterstützt werden.
keywords: Data Science-Tools, virtuelle Computer für Data Science, Tools für Data Science, Linux Data Science
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 59f88d54d3542738f1a500c8c476995eb1535ecf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130275"
---
# <a name="deep-learning-and-ai-frameworks"></a>Deep Learning und KI-Frameworks
Die [Data Science-VM](https://aka.ms/dsvm) (DSVM) und die [Deep Learning-VM](https://aka.ms/dsvm/deeplearning) unterstützen eine Reihe von Deep Learning-Frameworks zur Unterstützung bei der Erstellung von KI-Anwendungen mit Predictive Analytics und kognitiven Fähigkeiten wie Bild- und Sprachverständnis.

Hier finden Sie Details zu allen auf der DSVM verfügbaren Deep Learning-Frameworks.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

|    |           |
| ------------- | ------------- |
| Was ist das?   | Deep Learning-Framework      |
| Unterstützte DSVM-Editionen      | Windows, Linux     |
| Konfiguration/Installation auf der DSVM  | Das Microsoft Cognitive Toolkit (CNTK) wird in Python 3.5 unter [Linux und Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) und Python 3.6 unter [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) installiert.   |
| Links zu Beispielen      | Jupyter-Beispielnotebooks sind enthalten.     |
| Verwandte Tools auf der DSVM      | Keras      |
| Verwendung/Ausführung    | * An einem Terminal: Aktivieren Sie die richtige Umgebung, und führen Sie dann Python aus. <br/> * In Jupyter: Stellen Sie eine Verbindung mit [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) oder [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) her, dann öffnen Sie das CNTK-Verzeichnis für Beispiele. |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| Was ist das?   | Deep Learning-Framework      |
| Unterstützte DSVM-Editionen      | Windows, Linux     |
| Konfiguration/Installation auf der DSVM  | TensorFlow wird in Python 3.5 unter [Linux und Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) und Python 3.6 unter [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) installiert.  |
| Links zu Beispielen      | Jupyter-Beispielnotebooks sind enthalten.     |
| Verwandte Tools auf der DSVM      | Keras      |
| Verwendung/Ausführung    | * An einem Terminal: Aktivieren Sie die richtige Umgebung, und führen Sie dann Python aus. <br/> * In Jupyter: Stellen Sie eine Verbindung mit [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) oder [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) her, dann öffnen Sie das TensorFlow-Verzeichnis für Beispiele.  |

## <a name="horovod"></a>Horovod

|    |           |
| ------------- | ------------- |
| Was ist das?   | Verteiltes Deep Learning-Framework für TensorFlow      |
| Unterstützte DSVM-Editionen      | Ubuntu     |
| Konfiguration/Installation auf der DSVM  | Horovod ist in Python 3.5 unter [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition) installiert.  |
| Links zu Beispielen      | [https://github.com/uber/horovod/tree/master/examples](https://github.com/uber/horovod/tree/master/examples)     |
| Verwandte Tools auf der DSVM      | TensorFlow      |
| Verwendung/Ausführung    | An einem Terminal: Aktivieren Sie die richtige Umgebung, und führen Sie dann Python aus. |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| Was ist das?   | Allgemeine Deep Learning-API      |
| Unterstützte DSVM-Editionen      | Windows, Linux     |
| Konfiguration/Installation auf der DSVM  | TensorFlow wird in Python 3.5 unter [Linux und Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) und Python 3.6 unter [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) installiert. |
| Links zu Beispielen      | https://github.com/fchollet/keras/tree/master/examples      |
| Verwandte Tools auf der DSVM      | Microsoft Cognitive Toolkit, TensorFlow, Theano      |
| Verwendung/Ausführung    | * An einem Terminal: Aktivieren Sie die richtige Umgebung, und führen Sie dann Python aus. <br/> * In Jupyter: Laden Sie die Beispiele aus dem GitHub-Speicherort herunter, stellen Sie eine Verbindung mit [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) oder [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) her, und öffnen Sie das Beispielverzeichnis. |

## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| Was ist das?   | Deep Learning-Framework      |
| Unterstützte DSVM-Editionen      | Ubuntu     |
| Konfiguration/Installation auf der DSVM  | Caffe ist unter `/opt/caffe` installiert.    |
| Umstellung auf Python 2.7 | Führen Sie `source activate root` aus. |
| Links zu Beispielen      | Beispiele finden Sie unter `/opt/caffe/examples`.      |
| Verwandte Tools auf der DSVM      | Caffe2      |

### <a name="how-to-use--run-it"></a>Verwendung/Ausführung

Melden Sie sich mithilfe von X2Go bei Ihrem virtuellen Computer an. Starten Sie anschließend ein neues Terminal, und geben Sie Folgendes ein:

```
cd /opt/caffe/examples
source activate root
jupyter notebook
```

Daraufhin öffnet sich ein neues Browserfenster mit Beispielnotebooks.

Binärdateien werden in /opt/caffe/build/install/bin installiert.

Für die installierte Version von Caffe ist Python 2.7 erforderlich. Sie kann nicht mit der standardmäßig aktivierten Version 3.5 von Python verwendet werden. Führen Sie `source activate root` aus, um die Anaconda-Umgebung zu wechseln.

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| Was ist das?   | Deep Learning-Framework      |
| Unterstützte DSVM-Editionen      | Ubuntu     |
| Konfiguration/Installation auf der DSVM  | Caffe2 wird in der [Conda-Umgebung von Python 2.7 (root)](dsvm-languages.md#python-linux-and-windows-server-2012-edition) installiert. Die Quelle befindet sich in `/opt/caffe2`. |
| Links zu Beispielen      | In JupyterHub sind Beispielnotebooks enthalten. |
| Verwandte Tools auf der DSVM      | Caffe      |
| Verwendung/Ausführung    | * An einem Terminal: Aktivieren Sie die [Python-Umgebung (root)](dsvm-languages.md#python-linux-and-windows-server-2012-edition), starten Sie Python, und importieren Sie dann Caffe2. <br/> * In JupyterHub: [Stellen Sie eine Verbindung mit JupyterHub her](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), und navigieren Sie dann zum Caffe2-Verzeichnis. Dort finden Sie Beispielnotebooks. Für einige Notebooks muss im Python-Code der Caffe2-Stamm festgelegt werden. Geben Sie „/opt/caffe2“ ein. |
| Hinweise zum Build | Caffe2 basiert auf dem Quelltext von Linux und enthält CUDA, cuDNN und Intel MKL. Das aktuelle Commit ist 0d9c0d48c6f20143d6404b99cc568efd29d5a4be, welcher aufgrund seiner Stabilität auf allen getesteten GPUs und Beispielen ausgewählt wurde. |

## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| Was ist das?   | Deep Learning-Framework      |
| Unterstützte DSVM-Editionen      | Windows, Linux     |
| Konfiguration/Installation auf der DSVM  | Chainer wird in [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) installiert. ChainerRL und ChainerCV sind ebenfalls installiert.   |
| Links zu Beispielen      | In JupyterHub sind Beispielnotebooks enthalten. |
| Verwandte Tools auf der DSVM      | Caffe      |
| Verwendung/Ausführung  | * An einem Terminal: Aktivieren Sie die [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition)-Umgebung, starten Sie _Python_, und importieren Sie dann Chainer. <br/> * In JupyterHub: [Stellen Sie eine Verbindung mit JupyterHub her](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), und navigieren Sie dann zum Chainer-Verzeichnis. Dort finden Sie Beispielnotebooks.


## <a name="deep-water"></a>Deep Water

|    |           |
| ------------- | ------------- |
| Was ist das?   | Deep Learning-Framework für H2O      |
| Unterstützte DSVM-Editionen      | Ubuntu     |
| Konfiguration/Installation auf der DSVM  | Deep Water wird in [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) installiert und ist auch in `/dsvm/tools/deep_water` verfügbar.   |
| Links zu Beispielen      | In JupyterHub sind Beispielnotebooks enthalten.      |
| Verwandte Tools auf der DSVM      | H2O, Sparkling Water      |

### <a name="how-to-use--run-it"></a>Verwendung/Ausführung

Deep Water erfordert CUDA 8 mit cuDNN 5.1. Dies befindet sich standardmäßig nicht unter dem Bibliothekspfad, da andere Deep Learning-Frameworks CUDA 9 und cuDNN 7 verwenden. So verwenden Sie CUDA 8 und CuDNN 5.1 für Deep Water

```
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}
export CUDA_ROOT=/usr/local/cuda-8.0
```

So verwenden Sie Deep Water
* An einem Terminal: Aktivieren Sie die [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition)-Umgebung, und führen Sie dann _Python_ aus. <br/>
* In JupyterHub: [Stellen Sie eine Verbindung mit JupyterHub her](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), und navigieren Sie dann zum deep_water-Verzeichnis. Dort finden Sie Beispielnotebooks.

## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| Was ist das?   | Deep Learning-Framework      |
| Unterstützte DSVM-Editionen      | Windows, Linux     |
| Konfiguration/Installation auf der DSVM  | MXNet ist unter `C:\dsvm\tools\mxnet` (Windows) bzw. unter `/dsvm/tools/mxnet` (Linux) installiert. Python-Bindungen werden in Python 3.5 unter [Linux und Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) und Python 3.6 unter [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) installiert. R-Bindungen werden ebenfalls unter Ubuntu installiert.   |
| Links zu Beispielen      | Jupyter-Beispielnotebooks sind enthalten.    |
| Verwandte Tools auf der DSVM      | Keras      |
| Verwendung/Ausführung    | * An einem Terminal: Aktivieren Sie die richtige Umgebung, und führen Sie dann Python aus. <br/> * In Jupyter: Stellen Sie eine Verbindung mit [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) oder [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) her, dann öffnen Sie das MXNet-Verzeichnis für Beispiele.  |
 | Hinweise zum Build | MXNet basiert auf dem Quelltext von Linux. Dieser Build enthält CUDA, cuDNN, NCCL und MKL. |

## <a name="nvidia-digits"></a>NVIDIA DIGITS

|    |           |
| ------------- | ------------- |
| Was ist das?   | Deep Learning-System von NVIDIA zum schnellen Trainieren von Deep Learning-Modellen      |
| Unterstützte DSVM-Editionen      | Ubuntu     |
| Konfiguration/Installation auf der DSVM  | DIGITS ist unter `/dsvm/tools/DIGITS` installiert und als Dienst mit der Bezeichnung _digits_ verfügbar.   |

### <a name="how-to-use--run-it"></a>Verwendung/Ausführung

Melden Sie sich mithilfe von X2Go bei dem virtuellen Computer an. Starten Sie den Dienst über ein Terminal:

    sudo systemctl start digits

Das Starten des Diensts dauert ca. eine Minute. Starten Sie einen Webbrowser, und navigieren Sie zu `http://localhost:5000`. Beachten Sie, dass DIGITS keine sichere Anmeldung bereitstellt und daher nicht außerhalb der VM verfügbar gemacht werden sollte.



## <a name="nvidia-smi"></a>nvidia-smi

|    |           |
| ------------- | ------------- |
| Was ist das?   | NVIDIA-Tool zum Abfragen der GPU-Aktivität      |
| Unterstützte DSVM-Editionen      | Windows, Linux     |
| Konfiguration/Installation auf der DSVM  | _nvidia-smi_ steht unter dem Systempfad zur Verfügung.   |
| Verwendung/Ausführung | Starten Sie eine Eingabeaufforderung (Windows) oder ein Terminal (Linux), und führen Sie _nvidia-smi_ aus.



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| Was ist das?   | Deep Learning-Framework      |
| Unterstützte DSVM-Editionen      | Ubuntu     |
| Konfiguration/Installation auf der DSVM  | Theano ist in der Python 2.7-Umgebung (_root_) sowie in der Python 3.5-Umgebung (_py35_) installiert.   |
| Verwandte Tools auf der DSVM      | Keras      |
| Verwendung/Ausführung    | * An einem Terminal: Aktivieren Sie die gewünschte Python-Version („root“ oder „py35“), führen Sie Python aus, und importieren Sie anschließend Theano. <br/> * Wählen Sie in Jupyter den Python 2.7- oder den Python 3.5-Kernel aus, und importieren Sie anschließend Theano.  <br/>Zur Umgehung eines aktuellen MKL-Fehlers müssen Sie zunächst die MKL-Threadingebene festlegen:<br/><br/>_export MKL_THREADING_LAYER=GNU_|



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| Was ist das?   | Deep Learning-Framework      |
| Unterstützte DSVM-Editionen      | Ubuntu     |
| Konfiguration/Installation auf der DSVM  | Torch ist unter `/dsvm/tools/torch` installiert. PyTorch ist in der Python 2.7-Umgebung (_root_) sowie in der Python 3.5-Umgebung (_py35_) installiert.   |
| Links zu Beispielen      | Torch-Beispiele finden Sie unter `/dsvm/samples/torch`. PyTorch-Beispiele finden Sie unter `/dsvm/samples/pytorch`.      |


## <a name="pytorch"></a>PyTorch

|    |           |
| ------------- | ------------- |
| Was ist das?   | Deep Learning-Framework      |
| Unterstützte DSVM-Editionen      | Linux     |
| Konfiguration/Installation auf der DSVM  | PyTorch wird in [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) installiert.  |
| Links zu Beispielen      | Jupyter-Beispielnotebooks sind im Lieferumfang enthalten. Zudem finden Sie Beispiele unter /dsvm/samples/pytorch.      |
| Verwandte Tools auf der DSVM      | Torch      |
| Verwendung/Ausführung |* An einem Terminal: Aktivieren Sie die richtige Umgebung, und führen Sie dann Python aus. <br/> * In Jupyter: Stellen Sie eine Verbindung mit [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) her, dann öffnen Sie das PyTorch-Verzeichnis für Beispiele.  |

## <a name="mxnet-model-server"></a>MXNet Model Server

|    |           |
| ------------- | ------------- |
| Was ist das?   | Ein Server zum Erstellen von HTTP-Endpunkten für MXNet- und ONNX.Modelle      |
| Unterstützte DSVM-Editionen      | Linux     |
| Konfiguration/Installation auf der DSVM  | _mxnet-model-server_ ist am Terminal verfügbar.   |
| Links zu Beispielen      | Suchen Sie auf der [MXNet Model Server-Seite](https://github.com/awslabs/mxnet-model-server) nach aktuellen Beispielen.    |
| Verwandte Tools auf der DSVM      | MXNet      |

## <a name="tensorflow-serving"></a>TensorFlow Serving

|    |           |
| ------------- | ------------- |
| Was ist das?   | Ein Server zum Ausführen von Rückschlüssen für ein TensorFlow-Modell      |
| Unterstützte DSVM-Editionen      | Linux     |
| Konfiguration/Installation auf der DSVM  | _tensorflow_model_server_ ist am Terminal verfügbar.   |
| Links zu Beispielen      | Beispiele sind [online](https://www.tensorflow.org/serving/) verfügbar.      |
| Verwandte Tools auf der DSVM      | TensorFlow      |

## <a name="tensorrt"></a>TensorRT

|    |           |
| ------------- | ------------- |
| Was ist das?   | Ein Deep Learning-Rückschlussserver von NVIDIA. |
| Unterstützte DSVM-Editionen      | Ubuntu     |
| Konfiguration/Installation auf der DSVM  | TensorRT wird als _apt_-Paket installiert.   |
| Links zu Beispielen      | Beispiele sind [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples) verfügbar.      |
| Verwandte Tools auf der DSVM      | TensorFlow Serving, MXNet Model Server  |



