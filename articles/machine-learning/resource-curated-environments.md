---
title: Zusammengestellte Umgebungen
titleSuffix: Azure Machine Learning
description: Erfahren Sie mehr über die für Azure Machine Learning zusammengestellten Umgebungen, eine Reihe von vorkonfigurierten Umgebungen, die dabei helfen, die Vorbereitungszeiten für Experimente und Bereitstellung zu verkürzen.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.date: 11/16/2020
ms.openlocfilehash: 0e6817e03c5e5363ad925367b0632e26fc2da4a2
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701397"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning – zusammengestellte Umgebungen

In diesem Artikel werden die zusammengestellten Umgebungen in Azure Machine Learning sowie die darin vorinstallierten Pakete und Kanäle aufgelistet. Zusammengestellte Umgebungen werden von Azure Machine Learning bereitgestellt und sind standardmäßig in Ihrem Arbeitsbereich verfügbar. Sie werden durch zwischengespeicherte Docker-Images gesichert, wodurch die Kosten für die Vorbereitung der Ausführung reduziert und kürzere Bereitstellungszeiten ermöglicht werden. Verwenden Sie diese Umgebungen, um schnell mit verschiedenen Machine Learning-Frameworks zu beginnen.

> [!NOTE]
> Diese Liste wurde im September 2020 aktualisiert. Verwenden Sie das Python SDK, um die aktuelle Liste zu erhalten. Weitere Informationen finden Sie im [Artikel zu Umgebungen](./how-to-use-environments.md#use-a-curated-environment).

## <a name="azureml-automl"></a>AzureML-AutoML

**Paketkanäle:**

* anaconda
* conda-forge
* pytorch

**Conda-Pakete:**

* Python
* numpy
* scikit-learn
* Pandas
* py-xgboost
* fbprophet
* Feiertage
* setuptools-git
* psutil

**PIP-Pakete:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-defaults
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inference-schema
* py-cpuinfo

## <a name="azureml-automl-dnn"></a>AzureML-AutoML-DNN

**Paketkanäle:**

* anaconda
* conda-forge
* pytorch

**Conda-Pakete:**

* Python
* numpy
* scikit-learn
* Pandas
* py-xgboost
* fbprophet
* Feiertage
* setuptools-git
* pytorch
* cudatoolkit
* psutil

**PIP-Pakete:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-defaults
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inference-schema
* pytorch-transformers
* spacy
* en_core_web_sm
* py-cpuinfo

## <a name="azureml-automl-dnn-gpu"></a>AzureML-AutoML-DNN-GPU

**Paketkanäle:**

* anaconda
* conda-forge
* pytorch

**Conda-Pakete:**

* Python
* numpy
* scikit-learn
* Pandas
* fbprophet
* Feiertage
* setuptools-git
* pytorch
* cudatoolkit
* psutil

**PIP-Pakete:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-defaults
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inference-schema
* horovod
* pytorch-transformers
* spacy
* en_core_web_sm
* py-cpuinfo

## <a name="azureml-automl-dnn-vision-gpu"></a>AzureML-AutoML-DNN-Vision-GPU

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-dataset-runtime
* azureml-contrib-dataset
* azureml-telemetry
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-defaults
* azureml-interpret
* azureml-explain-model
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-contrib-automl-dnn-vision

## <a name="azureml-automl-gpu"></a>AzureML-AutoML-GPU

**Paketkanäle:**

* anaconda
* conda-forge
* pytorch

**Conda-Pakete:**

* Python
* numpy
* scikit-learn
* Pandas
* fbprophet
* Feiertage
* setuptools-git
* psutil

**PIP-Pakete:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-defaults
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inference-schema
* py-cpuinfo

## <a name="azureml-chainer-510-cpu"></a>AzureML-Chainer-5.1.0-CPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* chainer
* mpi4py

## <a name="azureml-chainer-510-gpu"></a>AzureML-Chainer-5.1.0-GPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* chainer
* cupy-cuda90
* mpi4py

## <a name="azureml-dask-cpu"></a>AzureML-Dask-CPU

**Paketkanäle:**

* conda-forge
* pytorch
* Standardwerte

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* adlfs
* azureml-core
* azureml-dataset-runtime
* dask[complete]
* dask-ml[complete]
* distributed
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* Notebook
* pyarrow

## <a name="azureml-dask-gpu"></a>AzureML-Dask-GPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python
* matplotlib

**PIP-Pakete:**

* azureml-defaults
* adlfs
* azureml-core
* dask[complete]
* dask-ml[complete]
* distributed
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* Notebook
* pyarrow

## <a name="azureml-hyperdrive-forecastdnn"></a>AzureML-Hyperdrive-ForecastDNN

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-defaults
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-contrib-automl-dnn-forecasting

## <a name="azureml-minimal"></a>AzureML-Minimal

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults

## <a name="azureml-pyspark-mmlspark-015"></a>AzureML-PySpark-MmlSpark-0.15

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core

## <a name="azureml-pytorch-10-cpu"></a>AzureML-PyTorch-1.0-CPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod

## <a name="azureml-pytorch-10-gpu"></a>AzureML-PyTorch-1.0-GPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod

## <a name="azureml-pytorch-11-cpu"></a>AzureML-PyTorch-1.1-CPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-11-gpu"></a>AzureML-PyTorch-1.1-GPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-12-cpu"></a>AzureML-PyTorch-1.2-CPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-12-gpu"></a>AzureML-PyTorch-1.2-GPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-13-cpu"></a>AzureML-PyTorch-1.3-CPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-13-gpu"></a>AzureML-PyTorch-1.3-GPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-14-cpu"></a>AzureML-PyTorch-1.4-CPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-14-gpu"></a>AzureML-PyTorch-1.4-GPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-15-cpu"></a>AzureML-PyTorch-1.5-CPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-15-gpu"></a>AzureML-PyTorch-1.5-GPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-16-cpu"></a>AzureML-PyTorch-1.6-CPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-pytorch-16-gpu"></a>AzureML-PyTorch-1.6-GPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* tensorboard
* future

## <a name="azureml-scikit-learn-0203"></a>AzureML-Scikit-learn-0.20.3

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* scikit-learn
* scipy
* joblib

## <a name="azureml-tensorflow-110-cpu"></a>AzureML-TensorFlow-1.10-CPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-110-gpu"></a>AzureML-TensorFlow-1.10-GPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-112-cpu"></a>AzureML-TensorFlow-1.12-CPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-112-gpu"></a>AzureML-TensorFlow-1.12-GPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-113-cpu"></a>AzureML-TensorFlow-1.13-CPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-113-gpu"></a>AzureML-TensorFlow-1.13-GPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-20-cpu"></a>AzureML-TensorFlow-2.0-CPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-20-gpu"></a>AzureML-TensorFlow-2.0-GPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-21-cpu"></a>AzureML-TensorFlow-2.1-CPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-21-gpu"></a>AzureML-TensorFlow-2.1-GPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-22-cpu"></a>AzureML-TensorFlow-2.2-CPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-22-gpu"></a>AzureML-TensorFlow-2.2-GPU

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tutorial"></a>AzureML-Tutorial

**Paketkanäle:**

* anaconda
* conda-forge

**Conda-Pakete:**

* Python
* pandas
* numpy
* tqdm
* scikit-learn
* matplotlib

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* azureml-widgets
* azureml-pipeline-core
* azureml-pipeline-steps
* azureml-opendatasets
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-train
* azureml-sdk
* azureml-interpret
* azureml-tensorboard
* azureml-mlflow
* mlflow
* sklearn-pandas

## <a name="azureml-vowpalwabbit-880"></a>AzureML-VowpalWabbit-8.8.0

**Paketkanäle:**

* conda-forge

**Conda-Pakete:**

* Python

**PIP-Pakete:**

* azureml-core
* azureml-defaults
* azureml-dataset-runtime[fuse,pandas]
