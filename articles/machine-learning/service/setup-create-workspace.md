---
title: Erstellen eines Arbeitsbereichs
titleSuffix: Azure Machine Learning service
description: Verwenden Sie das Azure-Portal, das SDK, eine Vorlage oder die CLI, um Ihren Azure Machine Learning Service-Arbeitsbereich zu erstellen. Dieser Arbeitsbereich ist ein zentraler Ort für die Arbeit mit allen Artefakten, die Sie bei der Verwendung von Azure Machine Learning Service erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: 36f3d421ee0b41a0ff71b549a4d4b5646188c3fa
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417357"
---
# <a name="create-an-azure-machine-learning-service-workspace"></a>Erstellen eines Azure Machine Learning Service-Arbeitsbereichs

Sie benötigen einen [**Azure Machine Learning Service-Arbeitsbereich**](concept-workspace.md), um Azure Machine Learning Service verwenden zu können.  Dieser Arbeitsbereich ist die Ressource auf oberster Ebene des Diensts und bietet einen zentralen Ort für die Arbeit mit allen erstellten Artefakten. 

In diesem Artikel erfahren Sie, wie Sie einen Arbeitsbereich mit einer der folgenden Methoden erstellen können: 
* über die Benutzeroberfläche des [Azure-Portals](#portal)
* über das [Azure Machine Learning SDK für Python](#sdk)
* über eine Azure Resource Manager-Vorlage
* über die [Azure Machine Learning-CLI](#cli)

Der Arbeitsbereich, den Sie mithilfe der Schritte in diesem Artikel befolgen, kann als Ausgangspunkt für andere Tutorials und exemplarische Vorgehensweisen verwendet werden.

Wenn Sie ein Skript zum Einrichten von automatisiertem, maschinellem Lernen in einer lokalen Python-Umgebung verwenden möchten, finden Sie Anweisungen dazu im [GitHub-Repository Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning).  

Wenn Sie einen Arbeitsbereich erstellen, werden folgende Azure-Ressourcen automatisch hinzugefügt, sofern sie regional verfügbar sind:
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): Um Kosten zu minimieren, wird ACR **verzögert geladen**, bis Bereitstellungsimages erstellt sind.
- [Azure Storage (in englischer Sprache)](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

>[!Note]
>Genau wie bei anderen Azure-Diensten gelten auch für Machine Learning bestimmte Grenzwerte und Kontingente. [Erfahren Sie mehr zu Kontingenten und ihrer Erhöhung.](how-to-manage-quotas.md)


## <a name="prerequisites"></a>Voraussetzungen
Zum Erstellen eines Arbeitsbereichs benötigen Sie ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie noch heute die [kostenlose oder kostenpflichtige Version von Azure Machine Learning Service](https://aka.ms/AMLFree) aus.

## <a name="portal"></a> Azure-Portal

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Sie können Ihren Arbeitsbereich unabhängig von der zur Erstellung verwendeten Methode im [Azure-Portal](https://portal.azure.com/) anzeigen:  Weitere Informationen finden Sie unter [Erstellen eines Arbeitsbereichs](how-to-manage-workspace.md#view).

## <a name="sdk"></a> Python SDK

Wenn Sie Ihren Arbeitsbereich mithilfe des Python SDK erstellen, müssen Sie das SDK zunächst installieren.

> [!IMPORTANT]
> Überspringen Sie die Installation des SDK, wenn Sie Azure Data Science Virtual Machine oder Azure Databricks verwenden.
> * Auf nach dem 27. September 2018 erstellten Azure Data Science Virtual Machines-Computern ist das Python SDK bereits vorinstalliert. Überspringen Sie die Installation, und beginnen Sie mit [Create a workspace with the SDK (Erstellen eines Arbeitsbereichs mit dem SDK)](#sdk-create).
> * Führen Sie in der Azure Databricks-Umgebung stattdessen die [Schritte für die Databricks-Installation](how-to-configure-environment.md#azure-databricks) aus.

>[!NOTE]
> Befolgen Sie diese Anweisungen, um das SDK auf Ihrem lokalen Computer zu installieren und zu verwenden. Richten Sie eine Remotedesktop- oder X-Terminalsitzung ein, um Jupyter auf einem virtuellen Remotecomputer zu verwenden.

Es empfiehlt sich, vor der Installation des SDK eine isolierte Python-Umgebung zu erstellen. In diesem Artikel wird zwar [Miniconda](https://docs.conda.io/en/latest/miniconda.html) verwendet, aber Sie können auch eine vollständige [Anaconda](https://www.anaconda.com/)-Installation oder [Python virtualenv](https://virtualenv.pypa.io/en/stable/) nutzen.

Mit den Schritten in diesem Artikel werden sämtliche Pakete installiert, die Sie zum Ausführen der Schnellstart- und Tutorial-Notebooks benötigen.  Für andere Beispiel-Notebooks müssen ggf. zusätzliche Komponenten installiert werden.  Weitere Informationen zu diesen Komponenten finden Sie unter [Install the Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/install) (Installieren des Azure Machine Learning SDK für Python).

### <a name="install-miniconda"></a>Installieren von Miniconda

[Führen Sie den Download für Miniconda durch, und installieren Sie die Anwendung](https://docs.conda.io/en/latest/miniconda.html). Installieren Sie mindestens die Python-Version 3.7. Verwenden Sie nicht die Python-Version 2.x.  

### <a name="create-an-isolated-python-environment"></a>Erstellen einer isolierten Python-Umgebung

1. Öffnen Sie eine Anaconda-Eingabeaufforderung, erstellen Sie eine neue Conda-Umgebung namens *myenv*, und installieren Sie Python 3.6.5. Das Azure Machine Learning SDK kann mit Python 3.5.2 oder höheren Versionen verwendet werden, die Komponenten für automatisiertes maschinelles Lernen sind unter Python 3.7 jedoch nicht voll funktionsfähig.  Es dauert einige Minuten, bis die Komponenten und Pakete heruntergeladen wurden und die Umgebung erstellt wurde. 

    ```shell
    conda create -n myenv python=3.6.5
    ```

1. Aktivieren Sie die Umgebung.

    ```shell
    conda activate myenv
    ```

1. Aktivieren Sie umgebungsspezifische ipython-Kernel:

    ```shell
    conda install notebook ipykernel
    ```

    Erstellen Sie dann den Kernel:

    ```shell
    ipython kernel install --user
    ```

### <a name="install-the-sdk"></a>Installieren des SDKs

1. Installieren Sie in der aktivierten Conda-Umgebung die Kernkomponenten des Machine Learning SDK mit Jupyter Notebook-Funktionen. Der Installationsvorgang dauert abhängig von der Konfiguration Ihres Computers einige Minuten.

    ```shell
    pip install --upgrade azureml-sdk[notebooks]
    ```

1. Damit Sie diese Umgebung für die Azure Machine Learning-Tutorials verwenden können, installieren Sie die folgenden Pakete:

    ```shell
    conda install -y cython matplotlib pandas
    ```

1. Damit Sie diese Umgebung für die Azure Machine Learning-Tutorials verwenden können, installieren Sie die Komponenten für automatisiertes maschinelles Lernen:

    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

> [!IMPORTANT]
> Bei einigen Befehlszeilentools müssen Sie möglicherweise Anführungszeichen hinzufügen, wie im Folgenden gezeigt:
> *  'azureml-sdk[notebooks]'
> * 'azureml-sdk[automl]'
>

### <a name='sdk-create'></a> Erstellen eines Arbeitsbereichs mit dem SDK

Erstellen Sie mit dem Python SDK Ihren Arbeitsbereich in einer Jupyter Notebook-Instanz.

1. Erstellen Sie das Verzeichnis, das Sie für den Schnellstart und die Tutorials verwenden möchten, und wechseln Sie mit dem cd-Befehl in dieses Verzeichnis.

1. Geben Sie den folgenden Befehl ein, um Jupyter Notebook zu starten:

    ```shell
    jupyter notebook
    ```

1. Erstellen Sie im Browserfenster ein neues Notebook unter Verwendung des Standardkernels `Python 3`. 

1. Geben Sie zum Anzeigen der SDK-Version den folgenden Python-Code in eine Zelle des Notebooks ein, und führen Sie ihn dann aus:

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. Suchen Sie in der [Liste mit den Abonnements im Azure-Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nach einem Wert für den Parameter `<azure-subscription-id>`. Verwenden Sie ein beliebiges Abonnement, für das Sie über die Rolle „Besitzer“ oder „Mitwirkender“ verfügen. Weitere Informationen zu Rollen finden Sie im Artikel [Verwalten des Zugriffs auf einen Azure Machine Learning-Arbeitsbereich](how-to-assign-roles.md).

   ```python
   from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
                         subscription_id='<azure-subscription-id>', 
                         resource_group='myresourcegroup',
                         create_resource_group=True,
                         location='eastus2' 
                        )
   ```

   Wenn Sie den Code ausführen, werden Sie möglicherweise aufgefordert, sich bei Ihrem Azure-Konto anzumelden. Nachdem Sie sich angemeldet haben, wird das Authentifizierungstoken lokal zwischengespeichert.

1. Geben Sie den folgenden Code ein, um Details des Arbeitsbereichs wie etwa den zugeordneten Speicher, die Containerregistrierung und den Schlüsseltresor anzuzeigen:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


### <a name="write-a-configuration-file"></a>Schreiben einer Konfigurationsdatei

Speichern Sie die Details Ihres Arbeitsbereichs in einer Konfigurationsdatei im aktuellen Verzeichnis. Der Name dieser Datei lautet *.azureml/config.json*.  

Diese Konfigurationsdatei für den Arbeitsbereich erleichtert das spätere Laden desselben Arbeitsbereichs. Sie können ihn mit anderen Notebooks und Skripts im gleichen Verzeichnis oder in einem Unterverzeichnis laden, indem Sie den Code `ws=Workspace.from_config()` ausführen. 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

Mit dem API-Aufruf `write_config()` wird die Konfigurationsdatei im aktuellen Verzeichnis erstellt. Die Datei *.azureml/config.json* enthält Folgendes:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

> [!TIP]
> Wenn Sie Ihren Arbeitsbereich in Python-Skripts oder Jupyter-Notebooks verwenden möchten, die sich in anderen Verzeichnissen befinden, kopieren Sie diese Datei in das entsprechende Verzeichnis. Die Datei kann sich im selben Verzeichnis, in einem Unterverzeichnis namens *.azureml* oder in einem übergeordneten Verzeichnis befinden.

## <a name="resource-manager-template"></a>Resource Manager-Vorlage

Weitere Informationen zum Erstellen eines Arbeitsbereichs mithilfe einer Vorlage finden Sie unter [Erstellen eines Azure Machine Learning Service-Arbeitsbereichs mithilfe einer Vorlage](how-to-create-workspace-template.md).

<a name="cli"></a>
## <a name="command-line-interface"></a>Befehlszeilenschnittstelle

Weitere Informationen zum Erstellen eines Arbeitsbereichs mit der CLI finden Sie unter [Verwenden der CLI-Erweiterung für Azure Machine Learning Service](reference-azure-machine-learning-cli.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

* Sie können Ihren Arbeitsbereich unabhängig von der zur Erstellung verwendeten Methode im [Azure-Portal](https://portal.azure.com/) anzeigen:  Weitere Informationen finden Sie unter [Erstellen eines Arbeitsbereichs](how-to-manage-workspace.md#view).

* Testen Sie Ihren Arbeitsbereich an diesen Schnellstarts und Tutorials.

    * Schnellstart: [Schnellstart: Verwenden eines cloudbasierten Notebook-Servers für die ersten Schritte mit Azure Machine Learning](quickstart-run-cloud-notebook.md)
    * Schnellstart: [Schnellstart: Verwenden Ihres eigenen Notebook-Servers für die ersten Schritte mit Azure Machine Learning](quickstart-run-local-notebook.md)
    * Zweiteiliges Tutorial: [Trainieren](tutorial-train-models-with-aml.md) und [Bereitstellen](tutorial-deploy-models-with-aml.md) eines Bildklassifizierungsmodells
    * Zweiteiliges Tutorial: [Vorbereiten von Daten](tutorial-data-prep.md) und [Verwenden von automatisiertem Machine Learning](tutorial-auto-train-models.md) zur Erstellung eines Regressionsmodells

* Lesen sie mehr über die [Konfiguration einer Entwicklungsumgebung](how-to-configure-environment.md).

* Lesen Sie mehr über das [Azure Machine Learning SDK für Python](https://aka.ms/aml-sdk).
