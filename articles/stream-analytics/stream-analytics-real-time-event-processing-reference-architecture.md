---
title: Echtzeitereignisverarbeitung mithilfe der Azure Stream Analytics-Ereignisverarbeitung
description: Dieser Artikel beschreibt die Referenzarchitektur für die Echtzeitereignisverarbeitung und -analyse mithilfe von Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: 7f9748a4e4f1c86362781aa80d8958237c97106a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60816965"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Referenzarchitektur: Echtzeitereignisverarbeitung mit Microsoft Azure Stream Analytics
Die Referenzarchitektur für die Echtzeitereignisverarbeitung mit Azure Stream Analytics soll als allgemeiner Plan dienen, mit dem eine Plattform-as-a-Service (PaaS) für die Datenstromverarbeitung in Echtzeit auf der Basis von Microsoft Azure bereitgestellt werden kann.

## <a name="summary"></a>Zusammenfassung
In der Vergangenheit stützten sich Analyselösungen auf Funktionen, wie z. B. ETL (Extract, Transform, Load) und Datawarehousing, wobei die Daten vor der Analyse gespeichert werden. Veränderte Anforderungen, darunter immer schneller eintreffende Daten, bringen dieses vorhandene Modell an seine Grenzen. Die Möglichkeit, Daten in sich bewegenden Streams vor der Speicherung zu analysieren, ist eine Lösung, und obwohl diese Fähigkeit nicht neu ist, wird der Ansatz in den verschiedenen Zweigen der Branche selten verwendet. 

Microsoft Azure stellt einen umfassenden Katalog von Analysetechnologien bereit, die ein ganzes Spektrum an verschiedenen Lösungsszenarien und Anforderungen unterstützen können. Angesichts der vielfältigen Angebote kann die Auswahl der für eine End-to-End-Lösung bereitzustellenden Azure-Dienste eine Herausforderung darstellen. In diesem Artikel werden die Funktionen und die Interaktion zwischen verschiedenen Azure-Diensten beschrieben, die eine Ereignis-Streaming-Lösung unterstützen. Darüber hinaus werden einige Szenarien erläutert, in denen Kunden von diesem Ansatz profitieren können.

## <a name="contents"></a>Inhalt:
* Kurzfassung
* Einführung in Echtzeitanalysen
* Nutzen von Echtzeitdaten in Azure
* Allgemeine Szenarien für Echtzeitanalysen
* Architektur und Komponenten
  * Datenquellen
  * Datenintegrationsebene
  * Echtzeitanalysenebene
  * Datenspeicherebene
  * Präsentations-/Nutzungsebene
* Zusammenfassung

**Autor**: Charles Feddersen, Lösungsarchitekt, Data Insights Center of Excellence, Microsoft Corporation

**Veröffentlicht**: Januar 2015

**Version**: 1.0

**Download**: [Real-Time Event Processing with Microsoft Azure Stream Analytics](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf) (Echtzeitereignisverarbeitung mit Microsoft Azure Stream Analytics)

## <a name="get-help"></a>Hier erhalten Sie Hilfe
Um weitere Hilfe zu erhalten, nutzen Sie das [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nächste Schritte
* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

