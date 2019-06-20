---
title: Azure Data Explorer-Datenvisualisierung
description: Erfahren Sie mehr über die verschiedenen Möglichkeiten, mit denen Sie Ihre Azure Data Explorer-Daten visualisieren können
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 85c37b6d626fc9942f5df956e738431d2727d282
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482052"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Datenvisualisierung mit Azure Data Explorer 

Azure Data Explorer ist ein schneller und hochgradig skalierbarer Dienst für die Untersuchung von Protokoll- und Telemetriedaten, die zum Erstellen von komplexen Analyselösungen für große Datenmengen verwendet werden. Azure Data Explorer wird in verschiedene Visualisierungstools integriert, damit Sie Ihre Daten visualisieren und die Ergebnisse unternehmensweit freigeben können. Diese Daten können in umfassende Einblicke hinsichtlich der Auswirkungen auf Ihr Unternehmen transformiert werden.

Datenvisualisierung und Berichterstellung ist ein wichtiger Schritt im Datenanalyseprozess. Azure Data Explorer unterstützt viele BI-Dienste, damit Sie denjenigen nutzen können, der für Ihr Szenario und Budget am besten geeignet ist.

* Azure Data Explorer-Visualisierungen: Mithilfe der Abfragesprache Kusto bietet [`render operator`](/azure/kusto/query/renderoperator) verschiedene Visualisierungstypen zum Darstellen von Abfrageergebnissen. Abfragevisualisierungen sind hilfreich bei Anomalieerkennung und -vorhersage, maschinellem Lernen und vielem mehr.

* [Power BI](https://powerbi.microsoft.com): Azure Data Explorer ermöglicht es, eine Verbindung mit Power BI mithilfe verschiedener Methoden herzustellen: 

  * [Integrierter nativer Power BI-Connector](/azure/data-explorer/power-bi-connector)

  * [Abfragenimport aus Azure Data Explorer in Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL-Abfrage](/azure/data-explorer/power-bi-sql-query).

* [Microsoft Excel](https://products.office.com/excel): Azure Data Explorer ermöglicht es, eine Verbindung mit Excel über den integrierten nativen Excel-Connector herzustellen oder eine Abfrage aus Azure Data Explorer in Excel zu importieren.

* [Grafana](https://grafana.com): Grafana stellt ein Plug-In für Azure Data Explorer zur Verfügung, mit dem Sie Daten aus Azure Data Explorer visualisieren können. Sie [richten Azure Data Explorer als Datenquelle für Grafana ein und visualisieren dann die Daten](/azure/data-explorer/grafana).

* [Sisense](https://www.sisense.com): Azure Data Explorer ermöglicht es, eine Verbindung mit Sisense über den JDBC-Connector herzustellen. Sie [richten Azure Data Explorer als Datenquelle für Sisense ein und visualisieren dann die Daten](/azure/data-explorer/sisense).

* [Tableau](https://www.tableau.com): Azure Data Explorer ermöglicht es, eine Verbindung mit Tableau über den [ODBC-Connector herzustellen und die Daten in Tableau zu visualisieren](/azure/data-explorer/connect-odbc).

* [Qlik](https://www.qlik.com): Azure Data Explorer ermöglicht es, eine Verbindung mit Qlik über den [ODBC-Connector](/azure/data-explorer/connect-odbc) herzustellen.