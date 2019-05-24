---
title: Erstellen von integrierten Lösungen mit SQL Data Warehouse | Microsoft Docs
description: 'Tools und Partner mit Lösungen, die in SQL Data Warehouse integriert werden können. '
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 43a714ae175e0d60f20b5e7ad79e1fa90125b0f8
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873338"
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>Integrieren anderer Dienste in SQL Data Warehouse
Zusätzlich zur Kernfunktionalität ermöglicht SQL Data Warehouse Benutzern die Integration in viele andere Dienste in Azure. Zu diesen Diensten gehören u.a.:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

SQL Data Warehouse lässt sich weiterhin in weitere Dienste in Azure sowie in weitere [Integrationspartner](sql-data-warehouse-partner-data-integration.md) integrieren.

## <a name="power-bi"></a>Power BI
Mit der Power BI-Integration können Sie die Rechenleistung von SQL Data Warehouse mit der dynamischen Berichterstellung und Visualisierung der Power BI kombinieren. Die Power BI-Integration umfasst derzeit:

* **Direct Connect**: Eine erweiterte Verbindung mit logischem Pushdown für SQL Data Warehouse. Pushdown ermöglicht schnellere Analysen im größeren Maßstab.
* **In Power BI öffnen**: Mit der Schaltfläche „In Power BI öffnen“ werden zur vereinfachten Verbindungsherstellung Instanzinformationen an Power BI übergeben.

Weitere Informationen finden Sie unter [Integration in Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) oder in der [Power BI-Dokumentation](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory bietet Benutzern eine verwaltete Plattform zur Erstellung komplexer Pipelines zum Extrahieren und Laden. Die SQL Data Warehouse-Integration in Azure Data Factory umfasst Folgendes:

* **Gespeicherte Prozeduren**: Orchestrieren Sie die Ausführung von gespeicherten Prozeduren in SQL Data Warehouse.
* **Kopieren**: Verwenden Sie Azure Data Factory, um Daten nach SQL Data Warehouse zu verschieben. Dieser Vorgang kann im Hintergrund PolyBase oder den standardmäßigen Mechanismus von Azure Data Factory zum Verschieben von Daten verwenden. 

Weitere Informationen finden Sie unter [Visualisieren von Daten mit Power BI](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning ist ein vollständig verwalteter Analytics-Dienst, mit dem Sie komplexe Modelle erstellen können, die einen großen Satz von Vorhersagetools nutzen. SQL Data Warehouse wird sowohl als Quelle als auch als Ziel für diese Modelle mit den folgenden Funktionen unterstützt:

* **Daten lesen**: Skalierte Laufwerkmodelle mit T-SQL gegen SQL Data Warehouse.
* **Daten schreiben**: Zurückgeben von Änderungen von einem beliebigen Modell an SQL Data Warehouse.

Weitere Informationen finden Sie unter [Analysieren von Daten mit Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics ist eine komplexe, vollständig verwaltete Infrastruktur für die Verarbeitung und Nutzung von Azure Event Hub generierten Ereignisdaten.  Die Integration mit SQL Data Warehouse ermöglicht das effiziente Streamen von Daten und die Speicherung neben relationalen Daten, sodass eine tiefergehende, erweiterte Analyse durchgeführt werden kann.  

* **Auftragsausgabe**: Direktes Senden der Ausgabe von Stream Analytics-Aufträgen an SQL Data Warehouse.

Weitere Informationen finden Sie unter [Verwenden von Azure Stream Analytics mit SQL Data Warehouse](sql-data-warehouse-integrate-azure-stream-analytics.md).


