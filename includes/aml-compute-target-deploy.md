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
ms.date: 11/02/2020
ms.openlocfilehash: 1f7abcdd1439fe5e6eeb2f718862f4875c61230c
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509254"
---
Das Computeziel, das Sie zum Hosten Ihres Modells verwenden, wirkt sich auf die Kosten und Verfügbarkeit des bereitgestellten Endpunkts aus. Verwenden Sie die folgende Tabelle, um ein geeignetes Computeziel auszuwählen:

| Computeziel | Syntaxelemente | GPU-Unterstützung | FPGA-Unterstützung | BESCHREIBUNG |
| ----- | ----- | ----- | ----- | ----- |
| [Lokaler&nbsp;&nbsp;Webservice](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Testen/Debuggen | &nbsp; | &nbsp; | Für eingeschränkte Tests und Problembehandlung verwenden. Die Hardwarebeschleunigung hängt von der Verwendung von Bibliotheken im lokalen System ab.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Echtzeitrückschluss |  [Ja](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (Webdienstbereitstellung) | [Ja](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Für hochgradig skalierbare Produktionsbereitstellungen verwenden. Bietet schnelle Antwortzeiten und die automatische Skalierung von bereitgestellten Diensten. Die automatische Skalierung von Clustern wird vom Azure Machine Learning SDK nicht unterstützt. Die Knoten in Ihrem AKS-Cluster können Sie über die entsprechende Benutzeroberfläche im Azure-Portal ändern. <br/><br/> Wird im Designer unterstützt. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Testen oder Entwicklung | &nbsp;  | &nbsp; | Für CPU-lastige Workloads im kleinen Maßstab verwenden, die weniger als 48 GB Arbeitsspeicher erfordern. <br/><br/> Wird im Designer unterstützt. |
| [Azure Machine Learning-Computecluster](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) | Batchrückschluss&nbsp; | [Ja](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) (Machine Learning-Pipeline) | &nbsp;  | Ausführen von Batchbewertungen auf serverlosen Computezielen. Unterstützt virtuelle Computer mit normaler und niedriger Priorität. |

> [!NOTE]
> Zwar unterstützen Computeziele wie „Lokal“, „Azure Machine Learning Compute“ und „Azure Machine Learning-Computecluster“ die GPU für Training und Experimente, jedoch wird die Verwendung von GPUs für Rückschlüsse nur _bei Bereitstellung als Webdienst_ in AKS unterstützt.
>
> Das Verwenden einer GPU für Rückschlüsse _bei der Bewertung mit einer Machine Learning-Pipeline_ wird nur in Azure Machine Learning Compute unterstützt.

> [!NOTE]
> * Containerinstanzen eignen sich nur für kleine Modelle mit einer Größe von unter 1 GB.
> * Verwenden Sie AKS-Cluster mit einem Knoten für Dev/Test von größeren Modellen.