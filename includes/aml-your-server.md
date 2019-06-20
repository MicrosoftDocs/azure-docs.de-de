---
title: Includedatei
description: Includedatei
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 01/25/2019
ms.openlocfilehash: ce8b117a3cbe0e3a5c4265729ccf5c0264241013
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391784"
---
1. Verwenden Sie die Anweisungen zum [Erstellen eines Azure Machine Learning Service-Arbeitsbereichs](../articles/machine-learning/service/setup-create-workspace.md#portal) für Folgendes:
    * Erstellen einer Miniconda-Umgebung
    * Installieren des Azure Machine Learning SDK für Python
    * Erstellen eines Arbeitsbereichs
    * Schreiben einer Konfigurationsdatei für den Arbeitsbereich (**aml_config/config.json**)

1. Klonen Sie das [GitHub-Repository](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Starten Sie den Notebook-Server aus Ihrem geklonten Verzeichnis.

    ```shell
    jupyter notebook
    ```