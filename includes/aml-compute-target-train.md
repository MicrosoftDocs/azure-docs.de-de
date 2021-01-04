---
title: include file
description: include file
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 09/17/2020
ms.openlocfilehash: 87b1f4ab7b7091970d7bb76ae1e00b06549fb0b4
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96152718"
---
**Computeziele können für mehrere Trainingsaufträge wiederverwendet werden.** Beispielsweise können Sie eine Remote-VM, die Sie an Ihren Arbeitsbereich angefügt haben, für mehrere Aufträge wiederverwenden. Verwenden Sie für Machine Learning-Pipelines den entsprechenden [Pipelineschritt](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py) für jedes Computeziel.

Sie können für die meisten Aufträge eine beliebige der folgenden Ressourcen als Trainingscomputeziel verwenden. Allerdings können nicht alle Ressourcen für automatisiertes maschinelles Lernen, Machine Learning-Pipelines oder den Machine Learning-Designer verwendet werden.

|Trainings&nbsp;ziele|[Automatisiertes maschinelles Lernen](../articles/machine-learning/concept-automated-ml.md) | [Machine Learning-Pipelines](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning-Designer](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Lokaler Computer](../articles/machine-learning/how-to-attach-compute-targets.md#local)| Ja | &nbsp; | &nbsp; |
|[Azure Machine Learning Compute-Cluster](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| Ja | Ja | Ja |
|[Azure Machine Learning-Computeinstanz](../articles/machine-learning/how-to-create-manage-compute-instance.md) | Ja (über SDK)  | Ja |  |
|[Remote-VM](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | Ja  | Ja | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| Ja (nur lokaler SDK-Modus) | Ja | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | Ja | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | Ja | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | Ja | &nbsp; |
