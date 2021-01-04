---
title: Sicherer Datenzugriff in der Cloud
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie mit Azure Machine Learning-Datenspeichern und -Datasets eine sichere Verbindung mit Ihrem Datenspeicher in Azure herstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 08/31/2020
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: 6d8c04e48a3d0009a152830a4ee332cd706c4b2c
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360172"
---
# <a name="secure-data-access-in-azure-machine-learning"></a>Sicherer Datenzugriff in Azure Machine Learning

Azure Machine Learning macht es einfach, eine Verbindung mit Ihren Daten in der Cloud herzustellen.  Es wird eine Abstraktionsschicht über dem zugrunde liegenden Speicherdienst bereitgestellt, sodass Sie sicher auf Ihre Daten zugreifen und diese bearbeiten können, ohne für Ihren Speichertyp spezifischen Code schreiben zu müssen. Azure Machine Learning bietet auch folgende Datenfunktionen:

*    Interoperabilität mit Pandas und Spark DataFrames
*    Versionsverwaltung und Nachverfolgung der Datenherkunft
*    Datenbeschriftung 
*    Überwachung von Datenabweichungen
    
## <a name="data-workflow"></a>Datenworkflow

Wenn Sie bereit sind, die Daten in ihrer cloudbasierten Speicherlösung zu verwenden, wird der folgende Datenübermittlungsworkflow empfohlen. Dieser Workflow setzt voraus, dass Sie über ein [Azure-Speicherkonto](../storage/common/storage-account-create.md?tabs=azure-portal) und Daten in einem cloudbasierten Speicherdienst in Azure verfügen. 

1. Erstellen Sie einen [Azure Machine Learning-Datenspeicher](#datastores), um Verbindungsinformationen in Ihrem Azure-Speicher zu speichern.

2. Erstellen Sie in diesem Datenspeicher ein [Azure Machine Learning-Dataset](#datasets), um auf (eine) bestimmte Datei(en) im zugrunde liegenden Speicher zu verweisen. 

3. Um dieses Dataset in Ihrem Machine Learning-Experiment zu verwenden, können Sie es entweder
    1. in das Computeziel Ihres Experiments zum Modelltraining einbinden,

        **OR** 

    1. direkt in Azure Machine Learning-Lösungen, z. B. Experimentausführungen für automatisiertes maschinelles Lernen (automatisiertes ML), Machine Learning-Pipelines oder [Azure Machine Learning-Designer](concept-designer.md), verwenden.

4. Erstellen Sie [Datasetüberwachungen](#data-drift) für Ihr Modellausgabedataset, um Datendrift zu erkennen. 

5. Wenn Datendrift erkannt wird, aktualisieren Sie Ihr Eingabedataset, und trainieren Sie Ihr Modell entsprechend neu.

In der folgenden Abbildung ist dieser empfohlene Workflow dargestellt.

![Die Abbildung zeigt den Azure Storage-Dienst mit einem Flow in einen Datenspeicher, der wiederum einen Flow in ein Dataset aufweist. Das Dataset weist einen Flow in das Modelltraining auf, dieses wiederum einen Flow in einen Datendrift, der wiederum einen Flow zurück zum Dataset aufweist.](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>Datenspeicher

Azure Machine Learning-Datenspeicher speichern die Informationen zur Verbindung mit Ihrem Azure-Speicher sicher, sodass Sie sie nicht in Ihren Skripts programmieren müssen. [Registrieren und erstellen Sie einen Datenspeicher](how-to-access-data.md), um auf einfache Weise eine Verbindung mit Ihrem Speicherkonto herzustellen und auf die Daten in Ihrem zugrunde liegenden Azure-Speicherdienst zuzugreifen. 

Unterstützte cloudbasierte Speicherdienste in Azure, die als Datenspeicher registriert werden können:

+ Azure-Blobcontainer
+ Azure-Dateifreigabe
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL-Datenbank
+ Azure Database for PostgreSQL
+ Databricks-Dateisystem
+ Azure Database for MySQL

## <a name="datasets"></a>Datasets

Bei Azure Machine Learning-Datasets handelt es sich nicht um Kopien Ihrer Daten. Durch Erstellen eines Datasets erstellen Sie einen Verweis auf die Daten in ihrem Speicherdienst sowie eine Kopie der zugehörigen Metadaten. 

Da Datasets nur langsam ausgewertet werden und die Daten am vorhandenen Speicherort verbleiben, profitieren Sie von folgenden Vorteilen:

* Es entstehen keine zusätzlichen Speicherkosten.
* Sie laufen nicht Gefahr, unabsichtlich Ihre ursprünglichen Datenquellen zu ändern.
* Der ML-Workflow wird verbessert und beschleunigt.

[Erstellen Sie ein Dataset](how-to-create-register-datasets.md), um auf Daten in Ihrem Speicher zuzugreifen und die Daten in einem nutzbaren Objekt für Machine Learning-Aufgaben zusammenzustellen. Registrieren Sie das Dataset in Ihrem Arbeitsbereich, um es freizugeben und in verschiedenen Experimenten ohne Datenerfassungskomplexität wiederzuverwenden.

Datasets können aus lokalen Dateien, öffentlichen URLs, [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) oder Azure-Speicherdiensten in Datenspeichern erstellt werden. 

Die folgenden beiden Datasettypen stehen zur Verfügung: 

+ Ein [FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset?preserve-view=true&view=azure-ml-py) verweist auf eine einzelne Datei oder auf mehrere Dateien in Ihren Datenspeichern oder öffentlichen URLs. Wenn Ihre Daten bereits bereinigt und für Trainingsexperimente bereit sind, können Sie Dateien, auf die von FileDatasets verwiesen wird, auf Ihr Computeziel [herunterladen oder sie einbinden](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets).

+ Ein [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py) stellt Daten in einem tabellarischen Format dar, indem die bereitgestellte Datei oder Liste von Dateien analysiert wird. Sie können ein TabularDataset zur weiteren Verarbeitung oder zur Bereinigung in einen Pandas- oder Spark-Datenrahmen laden. Eine vollständige Liste der Datenformate, aus denen Sie TabularDatasets erstellen können, finden Sie im Artikel über die [TabularDatasetFactory-Klasse](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory).

Weitere Funktionalität für Datasets finden Sie in der folgenden Dokumentation:

+ [Versionieren und Nachverfolgen von Datasets in Experimenten](how-to-version-track-datasets.md).
+ [Überwachen Ihres Datasets](how-to-monitor-datasets.md), um die Erkennung von Datendrift zu unterstützen.    

## <a name="work-with-your-data"></a>Arbeiten mit Ihren Daten

Mit Datasets können Sie eine Reihe von Machine Learning-Aufgaben über nahtlose Integration in Azure Machine Learning-Funktionen ausführen. 

+ Erstellen eines [Datenbeschriftungsprojekts](#label)
+ Trainieren von Machine Learning-Modellen:
     + [automatisierten ML-Experimenten](how-to-use-automated-ml-for-ml-models.md)
     + dem [Designer](tutorial-designer-automobile-price-train-score.md#import-data)
     + [Notebooks](how-to-train-with-datasets.md)
     + [Azure Machine Learning-Pipelines](how-to-create-your-first-pipeline.md)
+ Zugreifen auf Datasets zur Bewertung mit [Batchrückschluss](./tutorial-pipeline-batch-scoring-classification.md) in [Machine Learning-Pipelines](how-to-create-your-first-pipeline.md).
+ Einrichten einer Datasetüberwachung zur Erkennung von [Datendrift](#drift)

<a name="label"></a>

## <a name="data-labeling"></a>Datenbeschriftung

Das Beschriften großer Datenmengen bereitet in Projekten für maschinelles Lernen (ML) häufig Kopfzerbrechen. Projekte mit einer Komponente für maschinelles Sehen – z. B. zur Bildklassifizierung oder Objekterkennung – erfordern im Allgemeinen Tausende von Bildern und entsprechende Beschriftungen.

Azure Machine Learning ist Ihr zentraler Ort zum Erstellen, Verwalten und Überwachen von Beschriftungsprojekten. Beschriftungsprojekte helfen dabei, Daten, Beschriftungen und Teammitglieder zu koordinieren, sodass Sie die Beschriftungsaufgaben effizienter verwalten können. Zu den zurzeit unterstützten Aufgaben gehören die Bildklassifizierung – mit mehreren Beschriftungen oder mehreren Klassen – und die Objektidentifikation mithilfe von Begrenzungsrahmen.

Erstellen Sie ein [Datenbeschriftungsprojekt](how-to-create-labeling-projects.md), und geben Sie ein Dataset aus, das in Machine Learning-Experimenten verwendet werden kann.

<a name="drift"></a>

## <a name="data-drift"></a>Datendrift

Im Zusammenhang mit maschinellem Lernen ist die Datenabweichung die Änderung der Modelleingabedaten, die zu einem Abfallen der Modellleistung führt. Datendrift ist einer der Hauptgründe für die Abnahme der Modellgenauigkeit im Lauf der Zeit, daher hilft das Überwachen auf Datendrift bei der Erkennung von Problemen bei der Leistung eines Modells.

Weitere Informationen dazu, wie Sie Datendrift bei neuen Daten in einem Dataset erkennen und melden können, finden Sie im Artikel [Erkennen von Datendrift (Vorschau) in Datasets](how-to-monitor-datasets.md).

## <a name="next-steps"></a>Nächste Schritte 

+ Erstellen Sie ein Dataset in Azure Machine Learning Studio oder mit dem Python SDK, indem Sie [die folgenden Schritte ausführen](how-to-create-register-datasets.md).
+ Testen Sie Beispiele zum Trainieren von Datasets mit unseren [Beispielnotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).