---
title: Übersicht über Azure Synapse Analytics-Notebooks
description: Dieser Artikel bietet eine Übersicht über die Funktionen, die von Azure Synapse Analytics-Notebooks zur Verfügung gestellt werden.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: bad71cfeb8f15f463bb9659be704374325d1f50a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461992"
---
# <a name="azure-synapse-analytics-notebooks"></a>Azure Synapse Analytics-Notebooks

Ein Synapse Studio-Notebook ist eine Weboberfläche zum Erstellen von Dateien, die Livecode, Visualisierungen und beschreibenden Text enthalten. Notebooks sind ein guter Ausgangspunkt, um Ideen zu überprüfen und schnelle Experimente zu verwenden, um Erkenntnisse aus Ihren Daten zu gewinnen. 

Mit einem Azure Synapse Studio-Notebook können Sie:

* Ohne Einrichtungsaufwand sofort loslegen.
* Daten mit integrierten Sicherheitsfeatures auf Unternehmensniveau schützen.
* Daten in Rohformaten (CSV, TXT, JSON usw.), verarbeiteten Dateiformaten (Parquet, Delta Lake, ORC usw.) und tabellarischen SQL-Datendateien gegen Spark und SQL analysieren.
* Produktiv sein mit erweiterten Funktionen zur Dokumenterstellung und integrierter Datenvisualisierung.

Dieser Abschnitt enthält Artikel zum Kombinieren von Sprachen, Erstellen von Datenvisualisierungen, Angeben von Parametern für Notebooks, Erstellen von Pipelines und vieles mehr. Er enthält auch Referenzen und Tutorials für den Einstieg in die Notebookentwicklung.

## <a name="create-manage-and-use-notebooks"></a>Erstellen, Verwalten und Verwenden von Notebooks
Sie können Notebooks über die Synapse Studio-Benutzeroberfläche verwalten. 

Weitere Informationen zum Erstellen und Verwalten von Notebooks finden Sie in den folgenden Artikeln:
  - Verwalten von Notebooks
    - [Erstellen von Notebooks](./spark/../apache-spark-development-using-notebooks.md#create-a-notebook)
    - [Entwickeln von Notebooks](./spark/../apache-spark-development-using-notebooks.md#develop-notebooks)
    - [Einfügen von Daten in ein Notebook](./spark/../apache-spark-development-using-notebooks.md#bring-data-to-a-notebook)
    - [Verwenden mehrerer Sprachen mithilfe von Magic-Befehlen und temporären Tabellen](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)
    - [Verwenden von Zellen-Magic-Befehlen](./spark/../apache-spark-development-using-notebooks.md#magic-commands)
  - Entwicklung
    - [Konfigurieren von Spark-Sitzungseinstellungen](./spark/../apache-spark-development-using-notebooks.md#spark-session-config)
    - [Verwenden von Microsoft Spark-Hilfsprogrammen](./spark/../microsoft-spark-utilities.md)
    - [Visualisieren von Daten mithilfe von Notebooks und Bibliotheken](./spark/../apache-spark-data-visualization.md)
    - [Integrieren eines Notebooks in Pipelines](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)


## <a name="next-steps"></a>Nächste Schritte
Notebooks werden auch häufig für Datenvorbereitung, Datenvisualisierung, Machine Learning und andere Big Data-Szenarien verwendet. Weitere Informationen dazu, wie Sie Notebooks für Ihre Datenanalyse- und Big Data-Szenarien einsetzen können, finden Sie in den folgenden Tutorials:
  - [Erstellen eines Notebooks](./spark/../../quickstart-apache-spark-notebook.md)
  - [Erstellen von Visualisierungen mit Synapse Studio-Notebooks](./spark/../apache-spark-data-visualization-tutorial.md)
  - [Erstellen von Machine Learning-Modellen mit Apache Spark MLlib](./spark/../apache-spark-machine-learning-mllib-notebook.md)
  - [Erstellen von Machine Learning-Modellen mit Azure AutoML](./spark/../apache-spark-azure-machine-learning-tutorial.md)