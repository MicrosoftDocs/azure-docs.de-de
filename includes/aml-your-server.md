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
ms.openlocfilehash: 8d21e41ad487ad17598f2320fab5eebae02309e8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66123247"
---
1. Verwenden Sie die Anweisungen zum [Erstellen eines Azure Machine Learning Service-Arbeitsbereichs](../articles/machine-learning/service/setup-create-workspace.md#portal) für Folgendes:
    * Erstellen einer Miniconda-Umgebung
    * Installieren des Azure Machine Learning SDK für Python
    * Erstellen eines Arbeitsbereichs
    * Schreiben einer Konfigurationsdatei für den Arbeitsbereich (**aml_config/config.json**)
    
1. Klonen Sie das [GitHub-Repository](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Fügen Sie mit einer der folgenden Methoden eine Konfigurationsdatei für den Arbeitsbereich hinzu:
    * Kopieren Sie die Datei **aml_config/config.json**, die Sie in Schritt 1 erstellt haben, in das geklonte Verzeichnis.

    * Wählen Sie im [Azure-Portal](https://ms.portal.azure.com) im Abschnitt **Übersicht** Ihres Arbeitsbereichs **„config.json“ herunterladen** aus. 

    ![Herunterladen von „config.json“](./media/aml-dsvm-server/download-config.png)

    * Erstellen Sie einen neuen Arbeitsbereich, indem Sie den in [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) enthaltenen Code verwenden.

1. Starten Sie den Notebook-Server aus Ihrem geklonten Verzeichnis.
    
    ```shell
    jupyter notebook
    ```