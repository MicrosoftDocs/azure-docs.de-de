---
title: Erstellen von integrierten Lösungen
description: Lösungstools und Partner für die Integration in einen dedizierten SQL-Pool (früher SQL DW) in Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2afc274bf7c040eca6a83abbab24c41767f16482
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453660"
---
# <a name="integrate-other-services-with-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Integrieren Sie andere Dienste in einen dedizierten SQL-Pool (früher SQL DW) in Azure Synapse Analytics.

Der dedizierte SQL-Pool (früher SQL DW) in Azure Synapse Analytics ermöglicht Benutzern die Integration in viele andere Dienste in Azure. Ein dedizierter SQL-Pool kann mehrere zusätzliche Dienste nutzen, beispielsweise:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Weitere Informationen über Integrationsdienste in Azure finden Sie im Artikel zu [Integrationspartnern](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI

Mit der Power BI-Integration können Sie die Rechenleistung eines Data Warehouse mit der dynamischen Berichterstellung und Visualisierung von Power BI kombinieren. Die Power BI-Integration umfasst derzeit:

* **Direct Connect**: Eine erweiterte Verbindung mit logischem Pushdown für ein Data Warehouse, das mit einem dedizierten SQL-Pool (früher SQL DW) bereitgestellt wurde. Pushdown ermöglicht schnellere Analysen im größeren Maßstab.
* **In Power BI öffnen**: Mit der Schaltfläche „In Power BI öffnen“ werden zur vereinfachten Verbindungsherstellung Instanzinformationen an Power BI übergeben.

Weitere Informationen finden Sie unter [Integration in Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) oder in der [Power BI-Dokumentation](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory bietet Benutzern eine verwaltete Plattform zur Erstellung komplexer Pipelines zum Extrahieren und Laden. Die Integration in einen dedizierten SQL-Pool (früher SQL DW) in Azure Data Factory umfasst Folgendes:

* **Gespeicherte Prozeduren**: Die Orchestrierung der Ausführung von gespeicherten Prozeduren.
* **Kopieren**: Mithilfe von Azure Data Factory (ADF) können Sie Daten in den dedizierten SQL-Pool (früher SQL DW) verschieben. Dieser Vorgang kann im Hintergrund PolyBase oder den standardmäßigen Mechanismus von Azure Data Factory zum Verschieben von Daten verwenden.

Weitere Informationen finden Sie unter [Visualisieren von Daten mit Power BI](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning ist ein vollständig verwalteter Analytics-Dienst, mit dem Sie komplexe Modelle erstellen können, die einen großen Satz von Vorhersagetools nutzen. Der dedizierte SQL-Pool (früher SQL DW) wird sowohl als Quelle als auch als Ziel für diese Modelle unterstützt, und er bietet die folgenden Funktionen:

* **Daten lesen**: Bedarfsabhängige Laufwerkmodelle im großen Stil mit T-SQL für einen dedizierten SQL-Pool (früher SQL DW).
* **Daten schreiben**: Committen von Änderungen von einem beliebigen Modell zurück an den dedizierten SQL-Pool (früher SQL DW).

Weitere Informationen finden Sie unter [Analysieren von Daten mit Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

Azure Stream Analytics ist eine komplexe, vollständig verwaltete Infrastruktur für die Verarbeitung und Nutzung von Azure Event Hub generierten Ereignisdaten.  Die Integration in einen dedizierten SQL-Pool (früher SQL DW) ermöglicht das Streamen von Daten für eine effiziente Verarbeitung und die Speicherung zusammen mit relationalen Daten, sodass eine tiefergehende, erweiterte Analyse durchgeführt werden kann.  

* **Auftragsausgabe**: Direktes Senden der Ausgabe von Stream Analytics-Aufträgen an einen dedizierten SQL-Pool (früher SQL DW).

Weitere Informationen finden Sie unter [Verwenden von Azure Stream Analytics mit SQL Data Warehouse](sql-data-warehouse-integrate-azure-stream-analytics.md).
