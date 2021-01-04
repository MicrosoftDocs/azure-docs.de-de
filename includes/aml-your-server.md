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
ms.date: 03/05/2020
ms.openlocfilehash: 6e7580abfd113a279e2223b9cc9665f6a0b86bc8
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95554793"
---
1. Installieren Sie das Azure Machine Learning SDK für Python gemäß den Anweisungen unter [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?view=azure-ml-py).

1. Erstellen Sie einen [Azure Machine Learning-Arbeitsbereich](../articles/machine-learning/how-to-manage-workspace.md).

1. Schreiben Sie eine [Konfigurationsdatei](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Klonen Sie das [GitHub-Repository](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Starten Sie den Notebook-Server aus Ihrem geklonten Verzeichnis.

    ```bash
    jupyter notebook
    ```