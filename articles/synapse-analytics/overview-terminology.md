---
title: 'Terminologie: Azure Synapse Analytics'
description: Azure Synapse Analytics-Referenzhandbuch für Benutzer
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/18/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 7f0ca83c194aae6f4a8d04d70c2d4f3746ad2ca5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446721"
---
# <a name="azure-synapse-analytics-terminology"></a>Terminologie für Azure Synapse Analytics

Dieses Dokument enthält Informationen zu den grundlegenden Konzepten von Azure Synapse Analytics.

## <a name="basics"></a>Grundlagen

Ein **Synapse-Arbeitsbereich** bietet einen sicherungsfähigen abgegrenzten Bereich für die Zusammenarbeit an cloudbasierten Unternehmensanalysen in Azure. Ein Arbeitsbereich wird in einer bestimmten Region bereitgestellt und verfügt über ein zugeordnetes ADLS Gen2-Konto und Dateisystem (zum Speichern temporärer Daten). Ein Arbeitsbereich befindet sich unter einer Ressourcengruppe.

Ein Arbeitsbereich ermöglicht die Ausführung von Analysen mit SQL und Apache Spark. Für SQL- und Spark-Analysen verfügbare Ressourcen werden in SQL- und Spark-**Pools** organisiert. 

## <a name="linked-services"></a>Verknüpfte Dienste

Ein Arbeitsbereich kann eine beliebige Anzahl von **verknüpften Diensten** enthalten. Dabei handelt es sich im Wesentlichen um Verbindungszeichenfolgen, mit denen die Verbindungsinformationen definiert werden, die erforderlich sind, damit vom Arbeitsbereich eine Verbindung mit externen Ressourcen hergestellt werden kann.

## <a name="synapse-sql"></a>Synapse-SQL

**Synapse SQL** ermöglicht T-SQL-basierte Analysen in Synapse-Arbeitsbereichen. Synapse SQL hat zwei Verbrauchsmodelle: dediziert und serverlos.  Verwenden Sie für das dedizierte Modell **dedizierte SQL-Pools**. Ein Arbeitsbereich kann eine beliebige Anzahl dieser Pools enthalten. Verwenden Sie zur Nutzung des serverlosen Modells **serverlose SQL-Pools**. Jeder Arbeitsbereich verfügt über einen dieser Pools.

In Synapse Studio können Sie SQL-Pools verwenden, indem Sie **SQL-Skripts** erstellen und ausführen.

## <a name="apache-spark-for-synapse"></a>Apache Spark für Synapse

Erstellen und verwenden Sie zur Nutzung von Spark-Analysen **serverlose Apache Spark-Pools** in Ihrem Synapse-Arbeitsbereich. Wenn Sie mit der Verwendung eines Spark-Pools beginnen, wird vom Arbeitsbereich eine **Spark-Sitzung** für die Behandlung der mit dieser Sitzung verknüpften Ressourcen erstellt. 

Spark kann in Synapse auf zwei Arten verwendet werden:
* Von **Spark Notebooks** werden Scala, PySpark, C# und SparkSQL für Aufgaben im Zusammenhang mit Data Science und Datentechnik verwendet.
* Von **Spark-Auftragsdefinitionen** zur Ausführung von Spark-Batchaufträgen werden JAR-Dateien verwendet.

## <a name="pipelines"></a>Pipelines

Pipelines werden von Azure Synapse für die Datenintegration verwendet und ermöglichen es Ihnen, Daten zwischen Diensten zu verschieben und Aktivitäten zu orchestrieren.

* Bei **Pipelines** handelt es sich um logische Gruppierungen von Aktivitäten, die gemeinsam eine Aufgabe ausführen.
* **Aktivitäten** dienen zum Definieren von Aktionen innerhalb einer Pipeline, die für Daten ausgeführt werden sollen (beispielsweise Kopieren von Daten oder Ausführen einer Notebook-Instanz oder eines SQL-Skripts).
* Bei **Datenflüssen** handelt es sich um eine bestimmte Art von Aktivität für codefreie Datentransformationen mit Synapse Spark im Hintergrund.
* **Trigger:** Dient zum Ausführen einer Pipeline. Sie kann manuell oder automatisch ausgeführt werden (zeitplanbasiert, rollierendes Fenster oder ereignisbasiert).
* **Integrationsdataset:** Benannte Ansicht von Daten, die einfach auf die Daten verweist, die in einer Aktivität als Ein- und Ausgabe verwendet werden sollen. Gehört zu einem verknüpften Dienst.

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit Azure Synapse Analytics](get-started.md)
* [Erstellen eines Arbeitsbereichs](quickstart-create-workspace.md)
* [Verwenden eines serverlosen SQL-Pools](quickstart-sql-on-demand.md)

