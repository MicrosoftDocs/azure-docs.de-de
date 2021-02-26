---
title: Erstellen und Untersuchen von Datasets mit Bezeichnungen
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Datasetbezeichnungen aus Ihren Azure Machine Learning-Bezeichnungsprojekten exportieren und für Aufgaben für maschinelles Lernen verwenden können.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, data4ml
ms.date: 05/14/2020
ms.openlocfilehash: a4f15a1a0911e5a33da8b5f9445709cb42e7e10e
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981509"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Erstellen und Untersuchen eines Azure Machine Learning-Datasets mit Bezeichnungen

In diesem Artikel erfahren Sie, wie Sie die Datenbezeichnungen aus einem Azure Machine Learning-Datenbezeichnungsprojekt exportieren und in gängige Formate laden können, z. B. einen Pandas-Datenrahmen zum Untersuchen von Daten oder einen Torchvision-Dataset zur Bildtransformation. 

## <a name="what-are-datasets-with-labels"></a>Was sind Datasets mit Bezeichnungen? 

Azure Machine Learning-Datasets mit Bezeichnungen sind ein spezieller Typ von Dataset. Datasets dieses Typs werden nur als Ausgabe von Azure Machine Learning-Datenbezeichnungsprojekten erstellt. Erstellen Sie ein Datenbezeichnungsprojekt mit [den folgenden Schritten](how-to-create-labeling-projects.md). Machine Learning unterstützt Datenbezeichnungsprojekte für die Bildklassifizierung – mit mehreren Bezeichnungen oder mehreren Klassen – und die Objektidentifikation mit Begrenzungsrahmen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://aka.ms/AMLFree) erstellen, bevor Sie beginnen.
* Das [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py), oder greifen Sie auf [Azure Machine Learning-Studio](https://ml.azure.com/) zu.
    * Installieren des Pakets [azure-contrib-dataset](/python/api/azureml-contrib-dataset/?preserve-view=true&view=azure-ml-py)
* Ein Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).
* Zugriff auf ein Azure Machine Learning-Datenbezeichnungsprojekt. Wenn Sie nicht über ein Bezeichnungsprojekt verfügen, erstellen Sie eines mit [den folgenden Schritten](how-to-create-labeling-projects.md).

## <a name="export-data-labels"></a>Exportieren von Datenbezeichnungen 

Wenn Sie ein Datenbezeichnungsprojekt abschließen, können Sie die Bezeichnungsdaten aus einem Bezeichnungsprojekt exportieren. Auf diese Weise können Sie sowohl den Bezug zu den Daten als auch deren Bezeichnungen erfassen und sie im [COCO-Format](http://cocodataset.org/#format-data) oder als Azure Machine Learning-Dataset exportieren. Verwenden Sie die Schaltfläche **Exportieren** auf der Seite **Projektdetails** Ihres Beschriftungsprojekts.

### <a name="coco"></a>COCO 

 Die COCO-Datei wird im Standardblobspeicher des Azure Machine Learning-Arbeitsbereichs in einem Ordner unter *export/coco* erstellt. 
 
>[!NOTE]
>In Objekterkennungsprojekten werden die exportierten Werte „bbox: [x,y,Breite,Höhe]“ in der COCO-Datei normalisiert. Sie werden auf 1 skaliert. Beispiel: Ein Begrenzungsrahmen an der Position (10, 10) mit einer Breite von 30 Pixeln und einer Höhe von 60 Pixeln wird in einem Bild mit den Maßen 640 × 480 Pixel wie folgt bezeichnet: (0,015625, 0,02083, 0,046875, 0,125). Da die Koordinaten normalisiert werden, wird bei allen Bildern für „Breite“ und „Höhe“ der Wert „0,0“ angezeigt. Die tatsächliche Breite und Höhe können Sie mithilfe einer Python-Bibliothek wie OpenCV oder Pillow(PIL) abrufen.

### <a name="azure-machine-learning-dataset"></a>Azure Machine Learning-Dataset

Sie können im Abschnitt **Datasets** von Ihrem Azure Machine Learning-Studio auf das exportierte Azure Machine Learning-Dataset zugreifen. Die Seite mit **Datasetdetails** bietet auch Beispielcode für den Zugriff auf Ihre Beschriftungen aus Python.

![Exportiertes Dataset](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Untersuchen bezeichneter Datasets

Laden Sie Ihre bezeichneten Datasets in einen Pandas-Datenrahmen oder in ein Torchvision-Dataset, um beliebte Open-Source-Bibliotheken für die Untersuchung von Daten zu nutzen, ebenso wie die von PyTorch bereitgestellten Bibliotheken für die Bildtransformation und das Training.

### <a name="pandas-dataframe"></a>Pandas-Datenrahmen

Mit der Methode [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueto-pandas-dataframe-on-error--null---out-of-range-datetime--null--) der Klasse `azureml-contrib-dataset` können Sie bezeichnete Datasets in einen Pandas-Datenrahmen laden. Installieren Sie die Klasse mit dem folgenden Shellbefehl: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>Der Namespace „azureml.contrib“ ändert sich häufig, während wir an der Verbesserung des Diensts arbeiten. Daher sollte alles in diesem Namespace als Vorschau und als von Microsoft nicht vollständig unterstützt betrachtet werden.

Azure Machine Learning bietet die folgenden Optionen zur Handhabung von Dateien für Dateidatenströme bei der Konvertierung in einen Pandas-Datenrahmen.
* Herunterladen: Laden Sie Ihre Datendateien in einen lokalen Pfad herunter.
* Einbinden: Binden Sie Ihre Datendateien an einem Bereitstellungspunkt ein. Das Einbinden funktioniert nur für Linux-basiertes Computing, einschließlich Azure Machine Learning-Notebook (virtueller Computer) und Azure Machine Learning Compute.

Im folgenden Code ist das `animal_labels`-Dataset die Ausgabe eines zuvor im Arbeitsbereich gespeicherten Bezeichnungsprojekts.

```Python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Torchvision-Datasets

Sie können bezeichnete Datasets mit der Methode [to_torchvision()](/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueto-torchvision--) auch von der Klasse `azureml-contrib-dataset` in ein Torchvision-Dataset laden. Um diese Methode zu verwenden, muss [PyTorch](https://pytorch.org/) installiert sein. 

Im folgenden Code ist das `animal_labels`-Dataset die Ausgabe eines zuvor im Arbeitsbereich gespeicherten Bezeichnungsprojekts.

```python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

from torchvision.transforms import functional as F

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>Nächste Schritte

* Ein vollständiges Trainingsbeispiel finden Sie im [Notebook für Datasets mit Bezeichnungen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb).
