---
title: Machine Learning in Azure Synapse Analytics
description: Eine Übersicht über Machine Learning-Funktionen in Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 20f095a058434c36373c30ed2a633db12643814d
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448280"
---
# <a name="machine-learning-capabilities-in-azure-synapse-analytics"></a>Machine Learning-Funktionen in Azure Synapse Analytics

Azure Synapse Analytics bietet verschiedene Machine Learning-Funktionen. Dieser Artikel bietet einen Überblick darüber, wie Sie Machine Learning im Kontext von Azure Synapse anwenden können.

In dieser Übersicht werden die verschiedenen Machine Learning-bezogenen Funktionen in Synapse aus Sicht eines Data Science-Prozesses behandelt.

Möglicherweise sind Sie damit vertraut, wie ein typischer Data Science-Prozess aussieht. Es handelt sich dabei um einen bekannten Prozess, den die meisten Machine Learning-Projekte einhalten.

Generell umfasst der Prozess die folgenden Schritte:
* (Geschäftliche Aspekte)
* Datenerfassung und -auswertung
* Modellierung
* Modellimplementierung und -bewertung

In diesem Artikel werden die Machine Learning-Funktionen von Azure Synapse in verschiedenen Analyse-Engines aus Sicht eines Data Science-Prozesses behandelt. Für jeden Schritt im Data Science Prozess werden die Azure Synapse-Funktionen, die hilfreich sein können, zusammengefasst.

## <a name="azure-synapse-machine-learning-capabilities"></a>Machine Learning-Funktionen von Azure Synapse

### <a name="data-acquisition-and-understanding"></a>Datenerfassung und -auswertung

Die meisten Machine Learning-Projekte umfassen etablierte Schritte, und einer dieser Schritte besteht darin, auf die Daten zuzugreifen und diese zu verstehen.

#### <a name="data-source-and-pipelines"></a>Datenquelle und -pipelines

Dank [Azure Data Factory](/azure/data-factory/introduction), einem nativ integrierten Teil von Azure Synapse, steht ein leistungsfähiger Satz von Tools für Datenerfassungs- und Datenorchestrierungspipelines zur Verfügung. Auf diese Weise können Sie problemlos Datenpipelines erstellen, um auf die Daten zuzugreifen und sie in ein Format zu transformieren, das von Machine Learning verwendet werden kann. [Weitere Informationen zu Datenpipelines](/azure/data-factory/concepts-pipelines-activities?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) in Synapse. 

#### <a name="data-preparation-and-explorationvisualization"></a>Aufbereitung, Durchsuchen und Visualisierung von Daten

Ein wichtiger Teil des Machine Learning-Prozesses ist es, die Daten mittels Durchsuchen und Visualisierungen zu verstehen.

Abhängig davon, wo die Daten gespeichert sind, bietet Synapse eine Reihe verschiedener Tools zum Durchsuchen und Vorbereiten der Daten für die Analyse und für Machine Learning. Eine der schnellsten Möglichkeiten, um mit dem Durchsuchen von Daten zu beginnen, ist die Verwendung von Apache Spark oder serverlosen SQL-Pools direkt über Daten im Data Lake.

* [Apache Spark für Azure Synapse](../spark/apache-spark-overview.md) bietet Funktionen zum Transformieren, Vorbereiten und Untersuchen Ihrer Daten im großen Stil. Diese Spark-Pools bieten Tools wie PySpark/Python, Scala und .NET für die Datenverarbeitung im großen Stil. Mithilfe leistungsfähiger Visualisierungsbibliotheken lässt sich die Datenuntersuchungserfahrung verbessern, um die Daten besser verstehen zu können. [Weitere Informationen über die Vorgehensweise beim Durchsuchen und Visualisieren von Daten in Synapse mithilfe von Spark](../get-started-analyze-spark.md).

* [Serverlose SQL-Pools](../sql/on-demand-workspace-overview.md) bieten eine Möglichkeit, Daten mithilfe von TSQL direkt über den Data Lake zu durchsuchen. Serverlose SQL-Pools bieten auch einige integrierte Visualisierungen in Synapse Studio. [Weitere Informationen zur Vorgehensweise beim Durchsuchen von Daten mit serverlosen SQL-Pools](../get-started-analyze-sql-on-demand.md)

### <a name="modeling"></a>Modellierung

In Azure Synapse kann das Training von Machine Learning-Modellen in den Apache Spark-Pools mithilfe von Tools wie PySpark/Python, Scala oder .NET erfolgen.

#### <a name="train-models-on-spark-pools-with-mllib"></a>Trainieren von Modellen in Spark-Pools mit MLlib

Machine Learning-Modelle können mithilfe verschiedener Algorithmen und Bibliotheken trainiert werden. [Spark MLlib](http://spark.apache.org/docs/latest/ml-guide.html) bietet skalierbare Machine Learning-Algorithmen, mit denen sich die meisten klassischen Machine Learning-Probleme lösen lassen. [In diesem Tutorial](../spark/apache-spark-machine-learning-mllib-notebook.md) wird erläutert, wie ein Modell mithilfe von MLlib in Synapse trainiert wird.

Neben MLlib können auch beliebte Bibliotheken wie [Scikit Learn](https://scikit-learn.org/stable/) zum Entwickeln von Modellen verwendet werden. Ausführliche Informationen zum Installieren von Bibliotheken in Synapse Spark-Pools finden Sie unter [Verwalten von Bibliotheken für Apache Spark in Azure Synapse Analytics](../spark/apache-spark-azure-portal-add-libraries.md).

#### <a name="train-models-with-azure-machine-learning-automl"></a>Trainieren von Modellen mit Azure Machine Learning AutoML

Eine andere Möglichkeit zum Trainieren von Machine Learning-Modellen, die nicht viel Erfahrung mit Machine Learning voraussetzt, ist die Verwendung von AutoML. [AutoML](/azure/machine-learning/concept-automated-ml) ist eine Funktion, die automatisch eine Gruppe von Machine Learning-Modellen trainiert und es dem Benutzer ermöglicht, auf Grundlage bestimmter Metriken das beste Modell auszuwählen. Dank einer nahtlosen Integration in Azure Machine Learning von Azure Synapse Notebooks können Benutzer AutoML problemlos in Synapse mit Passthrough-Authentifizierung von Azure Active Directory nutzen.  Dies bedeutet, dass Sie nur auf Ihren Azure Machine Learning-Arbeitsbereich verweisen und keine Anmeldeinformationen eingeben müssen. Im Folgenden finden Sie ein [AutoML-Tutorial](../spark/apache-spark-azure-machine-learning-tutorial.md), das beschreibt, wie Modelle mithilfe von Azure Machine Learning AutoML in Synapse Spark-Pools trainiert werden.

### <a name="model-deployment-and-scoring"></a>Modellimplementierung und -bewertung

Modelle, die entweder in Azure Synapse oder außerhalb von Azure Synapse trainiert wurden, können problemlos für die Batchbewertung verwendet werden. Derzeit gibt es in Synapse zwei Möglichkeiten, wie Sie die Batchbewertung ausführen können.

* Sie können die [TSQL-Funktion PREDICT](../sql-data-warehouse/sql-data-warehouse-predict.md) in Synapse SQL-Pools verwenden, um Ihre Vorhersagen direkt an dem Ort auszuführen, wo sich Ihre Daten befinden. Diese leistungsstarke und skalierbare Funktion ermöglicht es Ihnen, Ihre Daten anzureichern, ohne Daten aus Ihrem Data Warehouse verschieben zu müssen. Eine neue [angeleitete Machine Learning-Modellerfahrung in Synapse Studio](https://aka.ms/synapse-ml-ui) wurde eingeführt, in der Sie ein ONNX-Modell aus der Azure Machine Learning-Modellregistrierung in Synapse SQL-Pools für die Batchbewertung mittels PREDICT bereitstellen können.

* Eine weitere Option für die Batchbewertung von Machine Learning-Modellen in Azure Synapse besteht darin, die Apache Spark-Pools für Azure Synapse zu verwenden. Abhängig von den Bibliotheken, die zum Trainieren der Modelle verwendet werden, können Sie Ihre Batchbewertung mithilfe einer Codeerfahrung ausführen.

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit Azure Synapse Analytics](../get-started.md)
* [Erstellen eines Arbeitsbereichs](../get-started-create-workspace.md)
* [Schnellstart: Erstellen eines neuen verknüpften Azure Machine Learning-Diensts in Synapse](quickstart-integrate-azure-machine-learning.md)
* [Tutorial: Assistent für die Bewertung von Machine Learning-Modellen: Dedizierter SQL-Pool](tutorial-sql-pool-model-scoring-wizard.md)
