---
title: Datenerfassung und Automatisierung
titleSuffix: Azure Machine Learning
description: Erfahren Sie mehr über die Vor- und Nachteile der Optionen zur Datenerfassung für das Training Ihrer Machine Learning-Modelle.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: 60cf97c4cb650120a4b6e2989b93d96ea120d040
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360121"
---
# <a name="data-ingestion-options-for-azure-machine-learning-workflows"></a>Optionen für die Datenerfassung für Azure Machine Learning-Workflows

In diesem Artikel lernen Sie die Vor- und Nachteile von Optionen zur Datenerfassung kennen, die mit Azure Machine Learning verfügbar sind. 

Folgende Optionen stehen zur Auswahl:
+ [Azure Data Factory](#azure-data-factory)-Pipelines, die speziell zum Extrahieren, Laden und Transformieren von Daten erstellt wurden

+ [Azure Machine Learning Python SDK](#azure-machine-learning-python-sdk), das eine benutzerdefinierte Codelösung für Datenerfassungsaufgaben bietet.

+ Eine Kombination beider Optionen

Die Datenerfassung ist der Prozess, bei dem unstrukturierte Daten aus einer oder mehreren Quellen extrahiert und dann für das Training von Machine Learning-Modellen vorbereitet werden. Sie ist auch zeitintensiv, insbesondere wenn sie manuell durchgeführt wird und Sie über große Datenmengen aus mehreren Quellen verfügen. Die Automatisierung dieses Aufwands setzt Ressourcen frei und stellt sicher, dass Ihre Modelle die aktuellsten und geeignetsten Daten verwenden.

## <a name="azure-data-factory"></a>Azure Data Factory

[Azure Data Factory](../data-factory/introduction.md) bietet native Unterstützung für die Überwachung von Datenquellen und für Trigger für Datenerfassungspipelines.  

In der folgenden Tabelle sind die Vor- und Nachteile der Verwendung von Azure Data Factory für Ihre Datenerfassungsworkflows zusammengefasst.

|Vorteile|Nachteile
---|---
Speziell zum Extrahieren, Laden und Transformieren von Daten konzipiert.|Bietet derzeit eine begrenzte Anzahl von Pipelineaufgaben für Azure Data Factory. 
Gestattet Ihnen die Erstellung datengesteuerter Workflows zur Orchestrierung von Datenverschiebungen und Transformationen in großem Stil.|Erstellung und Wartung sind teuer. Weitere Informationen finden Sie auf der [Preisgestaltungsseite](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) von Azure Data Factory.
Integriert mit verschiedenen Azure-Tools wie [Azure Databricks](../data-factory/transform-data-using-databricks-notebook.md) und [Azure Functions](../data-factory/control-flow-azure-function-activity.md) | Führt keine nativen Skripts aus, sondern vertraut stattdessen auf ein separates Compute für Skriptausführungen 
Unterstützt nativ die von der Datenquelle ausgelöste Datenerfassung| 
Die Prozesse für die Datenvorbereitung und das Modelltraining sind voneinander getrennt.|
Eingebettete Funktionen für die Datenherkunft für Azure Data Factory-Dataflows|
Bietet eine [Benutzeroberfläche](../data-factory/quickstart-create-data-factory-portal.md) für geringe Codeerfahrungen für Ansätze ohne Skripterstellung |

Diese Schritte und das folgende Diagramm veranschaulichen den Workflow von Azure Data Factory bei der Datenerfassung.

1. Pullen der Daten aus den Quellen
1. Transformieren und Speichern der Daten in einen Ausgabeblobcontainer, der als Datenspeicher für Azure Machine Learning dient
1. Wenn vorbereitete Daten gespeichert sind, ruft die Azure Data Factory-Pipeline eine Machine Learning-Pipeline auf, die die vorbereiteten Daten für das Modelltraining erhält


    ![ADF-Datenerfassung](media/concept-data-ingestion/data-ingest-option-one.svg)
    
Erfahren Sie, wie Sie eine Datenerfassungspipeline für Machine Learning mit [Azure Data Factory](how-to-data-ingest-adf.md) erstellen.

## <a name="azure-machine-learning-python-sdk"></a>Python-SDK für Azure Machine Learning 

Mit dem [Python SDK](/python/api/overview/azure/ml) können Sie Datenerfassungsaufgaben in eine [Azure Machine Learning-Pipeline](how-to-create-your-first-pipeline.md) integrieren.

In der folgenden Tabelle sind die Vor- und Nachteile der Verwendung des SDK und eines ML-Pipelineschritts für Aufgaben der Datenerfassung zusammengefasst.

Vorteile| Nachteile
---|---
Konfigurieren eigener Python-Skripts | Unterstützt das Auslösen von Datenquellenänderungen nicht nativ Erfordert Implementierungen von Logic App oder Azure Functions
Datenvorbereitung als Teil jeder Modelltrainingsausführung|Erfordert Entwicklungsfähigkeiten zum Erstellen eines Skripts für die Datenerfassung
Unterstützt Datenvorbereitungsskripts für verschiedene Computeziele, einschließlich [Azure Machine Learning-Compute](concept-compute-target.md#azure-machine-learning-compute-managed) |Bietet keine Benutzeroberfläche für die Erstellung des Erfassungsmechanismus

In der folgenden Abbildung besteht die Azure Machine Learning-Pipeline aus zwei Schritten: Datenerfassung und Modelltraining. Der Schritt der Datenerfassung umfasst Aufgaben, die mithilfe von Python-Bibliotheken und dem Python-SDK durchgeführt werden können, z. B. das Extrahieren von Daten aus lokalen/webseitigen Quellen und Datentransformationen wie die Imputation fehlender Werte. Der Trainingsschritt verwendet dann die vorbereiteten Daten als Eingabe für Ihr Trainingsskript, um Ihr Machine Learning-Modell zu trainieren. 

![Azure-Pipeline und SDK-Datenerfassung](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie diese Anleitungen:
* [Erstellen einer Datenerfassungspipeline mit Azure Data Factory](how-to-data-ingest-adf.md)

* [Automatisieren und Verwalten von Datenerfassungspipelines mit Azure Pipelines](how-to-cicd-data-ingestion.md).