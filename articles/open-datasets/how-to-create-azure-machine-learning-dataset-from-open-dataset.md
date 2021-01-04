---
title: Erstellen von Datasets mit Azure Open Datasets
titleSuffix: Azure Open Datasets
description: Hier erfahren Sie, wie Sie ein Azure Machine Learning-Dataset über Azure Open Datasets erstellen.
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: 6b9357c0fcf414c2575ca6966e8e5a3716015058
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654914"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Erstellen von Azure Machine Learning-Datasets über Azure Open Datasets

In diesem Artikel erfahren Sie, wie Sie mithilfe von [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md)-Datasets und [Azure Open Datasets](./index.yml) kuratierte Anreicherungsdaten in Ihre lokalen remote ausgeführten Machine Learning-Experimente einbringen. 

Durch Erstellen eines [Azure Machine Learning-Datasets](../machine-learning/how-to-create-register-datasets.md) erstellen Sie einen Verweis auf den Speicherort der Datenquelle sowie eine Kopie der zugehörigen Metadaten. Da Datasets nur langsam ausgewertet werden und die Daten am vorhandenen Speicherort verbleiben, profitieren Sie von folgenden Vorteilen:
* Es entstehen keine zusätzlichen Speicherkosten.
* Sie laufen nicht Gefahr, unabsichtlich Ihre ursprünglichen Datenquellen zu ändern. 
* Der ML-Workflow wird verbessert und beschleunigt.

Informationen dazu, welche Rolle Datasets im Workflow für den Datenzugriff in Azure Machine Learning spielen, finden Sie im Artikel [Datenzugriff in Azure Machine Learning](../machine-learning/concept-data.md#data-workflow).

Azure Open Datasets sind kuratierte öffentliche Datasets, mit denen Sie szenariospezifische Features hinzufügen können, um Ihre Vorhersagelösungen anzureichern und die Genauigkeit zu verbessern. Im [Open Datasets-Katalog](https://azure.microsoft.com/en-in/services/open-datasets/catalog/) finden Sie Daten der öffentlichen Domäne, mit denen Sie Ihre Machine Learning-Modelle trainieren können, z. B. zu folgenden Themen:

* [Wetter](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)
* [Bevölkerung](https://azure.microsoft.com/services/open-datasets/catalog/us-decennial-census-zip/)
* [Feiertage](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
* [Öffentliche Sicherheit](https://azure.microsoft.com/services/open-datasets/catalog/chicago-safety-data/)
* location

Open Datasets befinden sich in Microsoft Azure in der Cloud und sind sowohl im [Azure Machine Learning Python SDK](#create-datasets-with-the-sdk) als auch in [Azure Machine Learning Studio](#create-datasets-with-the-studio) enthalten.


## <a name="prerequisites"></a>Voraussetzungen

Für diesen Artikel ist Folgendes erforderlich:

* Ein Azure-Abonnement. Wenn Sie keines haben, erstellen Sie ein kostenloses Konto, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) aus.

* Ein [Azure Machine Learning-Arbeitsbereich](../machine-learning/how-to-manage-workspace.md).

* Eine [Installation des Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py), in dem das Paket `azureml-datasets` enthalten ist.

    * Erstellen Sie eine [Azure Machine Learning-Computeinstanz](../machine-learning/how-to-create-manage-compute-instance.md), bei der es sich um eine vollständig konfigurierte und verwaltete Entwicklungsumgebung handelt, die integrierte Notebooks und das bereits installierte SDK beinhaltet.

    **OR**

    * Arbeiten Sie in Ihrer eigenen Python-Umgebung, und installieren Sie das SDK selbst anhand [dieser Anweisungen](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).

> [!NOTE]
> Einige Datasetklassen sind vom Paket [azureml-dataprep](/python/api/azureml-dataprep/) abhängig, das nur mit 64-Bit Python kompatibel ist. Für Linux-Benutzer werden diese Klassen nur unter den folgenden Distributionen unterstützt:  Red Hat Enterprise Linux (7, 8), Ubuntu (14.04, 16.04, 18.04), Fedora (27, 28), Debian (8, 9) und CentOS (7).

## <a name="create-datasets-with-the-sdk"></a>Erstellen von Datasets mit dem SDK

Wenn Sie Azure Machine Learning-Datasets über Open Datasets-Klassen im Python SDK erstellen möchten, vergewissern Sie sich, dass Sie das Paket mit `pip install azureml-opendatasets` installiert haben. Jedes einzelne Dataset wird durch eine eigene Klasse im SDK repräsentiert, und bestimmte Klassen sind als [`TabularDataset` oder `FileDataset`](../machine-learning/how-to-create-register-datasets.md#dataset-types) von Azure Machine Learning oder als beides verfügbar. Eine vollständige Liste der `opendatasets`-Klassen finden Sie in der [Referenzdokumentation](/python/api/azureml-opendatasets/azureml.opendatasets?preserve-view=true&view=azure-ml-py).

Sie können bestimmte `opendatasets`-Klassen als `TabularDataset` oder als `FileDataset` abrufen, sodass Sie die Dateien direkt bearbeiten und/oder herunterladen können. Andere Klassen können ein Dataset **ausschließlich** mithilfe der Funktion `get_tabular_dataset()` oder `get_file_dataset()` aus der `Dataset`-Klasse im Python SDK abrufen.

Der folgende Code zeigt, dass die `opendatasets`-Klasse „MNIST“ ein `TabularDataset` oder ein `FileDataset` zurückgeben kann. 


```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()
```

In diesem Beispiel ist die `opendatasets`-Klasse „Diabetes“ nur als `TabularDataset` verfügbar, daher die Verwendung von `get_tabular_dataset()`.

```python

from azureml.opendatasets import Diabetes
from azureml.core import Dataset

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```
## <a name="register-datasets"></a>Registrieren von Datasets

Registrieren Sie ein Azure Machine Learning-Dataset bei Ihrem Arbeitsbereich, damit Sie es für andere Benutzer freigeben und in Experimenten in Ihrem Arbeitsbereich wiederverwenden können. Wenn Sie ein Azure Machine Learning-Dataset registrieren, das aus Open Datasets erstellt wurde, werden die Daten nicht sofort heruntergeladen. Auf die Daten wird erst später, wenn sie angefordert werden (z. B. während des Trainings), in einem zentralen Speicherort zugegriffen.

Verwenden Sie die [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-&preserve-view=true )-Methode, um Ihre Datasets bei einem Arbeitsbereich zu registrieren. 
```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-with-the-studio"></a>Erstellen von Datasets mit Studio

Sie können Azure Machine Learning-Datasets auch über [Azure Machine Learning Studio](https://ml.azure.com) aus Azure Open Datasets erstellen. Studio ist eine konsolidierte Webumgebung mit Machine Learning-Tools, über die Data Scientists mit den unterschiedlichsten Qualifikationen Data Science-Szenarien ausführen können.

> [!Note]
> Datasets, die über Azure Machine Learning Studio erstellt werden, werden automatisch beim Arbeitsbereich registriert.

1. Wählen Sie in Ihrem Arbeitsbereich unter **Assets** die Registerkarte **Datasets** aus. Wählen Sie im Dropdownmenü **Dataset erstellen** die Option **Aus Open Datasets** aus.

    ![Öffnen eines Datasets über die Benutzeroberfläche](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. Wählen Sie ein Dataset aus, indem Sie die entsprechende Kachel auswählen. (Über die Suchleiste kann gefiltert werden.) Wählen Sie **Weiter** aus.

    ![Dataset auswählen](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. Wählen Sie einen Namen, unter dem das Dataset registriert werden soll, und filtern Sie die Daten optional mithilfe der verfügbaren Filter. Filtern Sie in diesem Fall das Dataset der **gesetzlichen Feiertage** nach Zeitraum (ein Jahr) und Ländercode (nur USA). Weitere Details zu den Daten, wie z. B. Feldbeschreibungen und Datumsbereiche, finden Sie im [Azure Open Datasets-Katalog](https://azure.microsoft.com/services/open-datasets/catalog). Klicken Sie auf **Erstellen**.

    ![Dataset-Parameter festlegen und Dataset erstellen](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    Das Dataset ist nun in Ihrem Arbeitsbereich unter **Datasets** verfügbar. Sie können es auf die gleiche Weise verwenden wie andere Datasets, die Sie erstellt haben.


## <a name="access-datasets-for-your-experiments"></a>Zugreifen auf Datasets für Ihre Experimente

Verwenden Sie Ihre Datasets in Ihren Machine Learning-Experimenten zum Trainieren von ML-Modellen. [Erfahren Sie mehr über das Trainieren mit Datasets](../machine-learning/how-to-train-with-datasets.md).

## <a name="example-notebooks"></a>Beispielnotebooks

Beispiele und Demos für die Open Datasets-Funktionen finden Sie in [diesen Beispiel-Notebooks](samples.md).

## <a name="next-steps"></a>Nächste Schritte

* [Trainieren Sie Ihr erstes ML-Modell](../machine-learning/tutorial-1st-experiment-sdk-train.md).

* [Trainieren Sie mit Datasets](../machine-learning/how-to-train-with-datasets.md).

* [Erstellen Sie ein Azure Machine Learning-Dataset](../machine-learning/how-to-create-register-datasets.md).
