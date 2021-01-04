---
title: Anwendungsfälle für Gen2 – Azure Time Series Insights Gen2 | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Anwendungsfälle für Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: b8f13a20232fab61dc082c1b12b7ddaa11807554
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016189"
---
# <a name="azure-time-series-insights-gen2-use-cases"></a>Anwendungsfälle für Azure Time Series Insights Gen2

In diesem Artikel werden einige häufige Anwendungsfälle für die Vorschauversion von Azure Time Series Insights Gen2 zusammengefasst. Die Empfehlungen in diesem Artikel dienen als Ausgangspunkt für die Entwicklung Ihrer Anwendungen und Lösungen mit Azure Time Series Insights Gen2.

Im Einzelnen werden in diesem Artikel die folgenden Fragen beantwortet:

* Was sind häufige Anwendungsfälle für Azure Time Series Insights Gen2?
* Welche Vorteile bietet die Verwendung von Azure Time Series Insights Gen2 für das [Durchsuchen von Daten und die Erkennung visueller Anomalien](#data-exploration-and-visual-anomaly-detection)?
* Welche Vorteile bietet die Verwendung von Azure Time Series Insights Gen2 für die [Betriebsanalyse und Prozesseffizienz](#operational-analysis-and-driving-process-efficiency)?
* Welche Vorteile bietet die Verwendung von Azure Time Series Insights Gen2 für die [erweiterte Analyse](#advanced-analytics)?

In den folgenden Abschnitten können Sie sich eine Übersicht über diese Nutzungsszenarien verschaffen.

## <a name="introduction"></a>Einführung

Azure Time Series Insights Gen2 ist ein End-to-End-PaaS-Angebot (Platform-as-a-Service). Es wird verwendet, um stark kontextualisierte, zeitreihenoptimierte IoT-Daten zu sammeln, zu verarbeiten, zu speichern, zu analysieren und abzufragen. Es ist ideal für Ad-hoc-Datenuntersuchungen und Betriebsanalysen geeignet. Bei Azure Time Series Insights Gen2 handelt es sich um ein speziell erweiterbares, angepasstes Dienstangebot, das für die weit reichenden Anforderungen von IoT-Bereitstellungen in der Industrie konzipiert ist.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Datenuntersuchung und visuelle Anomalieerkennung

Durchsuchen und analysiere Sie Milliarden von Ereignissen in Echtzeit, um Anomalien und versteckte Trends in Ihren Daten zu erkennen. Azure Time Series Insights Gen2 bietet Leistung in nahezu Echtzeit für Ihre IoT- und DevOps-Analyseworkloads.

[![Daten-Explorer](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

Die meisten Kunden sind sich einig, dass die minimale Zeit, die benötigt wird, um Einblicke zu gewinnen, eines der herausragenden Merkmale von Azure Time Series Insights Gen2 ist:

* Azure Time Series Insights Gen2 erfordert keine Vorbereitung der Daten.
* Es arbeitet so schnell, dass es Sie binnen weniger Minuten mit unzähligen Ereignissen in Ihren Azure IoT Hub- oder Azure Event Hubs-Instanzen verbindet.
* Sobald eine Verbindung hergestellt ist, können Sie Milliarden von Ereignissen visualisieren und analysieren, um Anomalien und versteckte Trends in Ihren Daten zu erkennen.

Azure Time Series Insights Gen2 ist intuitiv und einfach zu verwenden. Sie können mit Ihren Daten interagieren, ohne auch nur eine Zeile Code schreiben zu müssen. Es gibt auch keine neue Sprache, die Sie erlernen müssen, obwohl Azure Time Series Insights Gen2 eine detaillierte textbasierte Abfragesprache für fortgeschrittene Benutzer bietet, die mit SQL vertraut sind. Darüber hinaus bietet es Novizen die Möglichkeit der Untersuchung per Auswählen und Klicken.

Kunden können von der hohen Geschwindigkeit der Diagnose ressourcenbezogener Probleme profitieren. Sie können DevOps-Analysen ausführen, um die Grundursache eines Fehlers in einer IoT-Lösung ausfindig zu machen. Außerdem können sie Bereiche identifizieren, die zur weiteren Untersuchung im Rahmen ihrer Data Science-Initiativen gekennzeichnet werden.

Es gibt drei primäre Methoden zur Interaktion mit in Azure Time Series Insights Gen2 gespeicherten Daten:

* Die erste und einfachste Möglichkeit für den Einstieg ist der Azure Time Series Insights Gen2-Explorer. Sie können ihn verwenden, um alle Ihrer IoT-Daten an einem Ort schnell zu visualisieren. Er bietet Tools wie das Wärmebild, das Ihnen beim Erkennen von Anomalien in Ihren Daten hilft. Ferner bietet er eine perspektivische Ansicht. Mit dieser können Sie bis zu vier Ansichten aus einer oder mehreren Azure Time Series Insights Gen2-Umgebungen in einem einzelnen Dashboard vergleichen. Das Dashboard bietet Ihnen eine Ansicht Ihrer Zeitreihendaten über alle Orte hinweg. Weitere Informationen zum [Azure Time Series Insights Gen2-Explorer](./concepts-ux-panels.md). Lesen Sie zur Planung Ihrer Umgebung [Azure Time Series Insights Gen2-Planung](./how-to-plan-your-environment.md).

* Die zweite Methode für den Einstieg besteht in der Verwendung des JavaScript SDK, um leistungsstarke Diagramme und Grafiken schnell in Ihre Webanwendung einzubetten. Mit nur wenigen Codezeilen können Sie leistungsstarke Abfragen schreiben. Verwenden Sie diese zum Auffüllen von Liniendiagrammen, Kreisdiagrammen, Balkendiagrammen, Wärmebildern, Datenrastern und mehr. Alle diese Elemente sind bei Verwendung des SDK vorgefertigt und sofort einsatzbereit vorhanden. Das SDK abstrahiert außerdem Azure Time Series Insights Gen2-Abfrage-APIs. Sie können sie zum Erstellen von SQL-ähnlichen Prädikaten verwenden, um die Daten abzufragen, die Sie in einem Dashboard anzeigen möchten. Für Hybridpräsentationsebenen-Lösungen bietet Azure Time Series Insights Gen2 parametrisierte URLs. Diese bieten nahtlose Verbindungspunkte mit dem Azure Time Series Insights Gen2-Explorer für tief gehende Einblicke in Ihre Daten.

  * Lesen Sie die Dokumentationen zur [JS-Clientbibliothek](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) und zum [Beispielclient](https://github.com/Microsoft/tsiclient), um mehr über das JavaScript-SDK zu erfahren.

  * Weitere Informationen zum Freigeben von URLs und zur neuen Benutzeroberfläche finden Sie unter [Visualisieren von Daten im Azure Time Series Insights Gen2-Explorer](./concepts-ux-panels.md).

* Die dritte Methode für den Einstieg besteht in der Verwendung leistungsfähiger APIs zum Abfragen von in Azure Time Series Insights Gen2 gespeicherten Daten. Azure Time Series Insights Gen2 verfügt über temporale Operatoren wie `from`, `to`, `first` und `last`. Es verfügt über Aggregationen und Transformationen wie `average`, `sum`, `min`, `max`, `time-weighted average`, `time-weighted sum` usw. Außerdem gestattet es Filtern, arithmetische und boolesche Operatoren, Skalarwertfunktion usw. Alle diese Operatoren ermöglichen Downstreamanwendungen das schnelle Auffinden interessanter Trends und Muster in Ihren Daten. Verwenden Sie sie zum Auffüllen selbst entwickelter Visualisierungen für die Erkennung von Anomalien.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Betriebsanalyse und Optimieren der Prozesseffizienz

Verwenden Sie Azure Time Series Insights Gen2, um die Integrität, Verwendung und Leistung von Geräten im Verhältnis zu überwachen sowie um die Betriebseffizienz zu messen. Mit Azure Time Series Insights Gen2 können Sie verschiedene und unvorhersehbare IoT-Workloads verwalten, ohne die Leistung bei eingehendem Datenverkehr oder Abfragen zu beeinträchtigen.

[![Screenshot zu IoT-Geräten/-Anwendungsdaten, Streamverarbeitung, betrieblicher Effizienz, Analysen/Erkenntnissen und erweiterten Analysefunktionen in Azure Time Series Insights Gen2](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

Das Streamen und die fortlaufende Verarbeitung von Daten, die aus operativen Prozessen stammen, können jedes Unternehmen erfolgreich transformieren, wenn sie mit der richtigen Technologie oder Lösung kombiniert werden. Häufig sind diese Lösungen eine Kombination aus mehreren Systemen. Sie ermöglichen die Untersuchung und Analyse von Daten, die sich ständig ändern, insbesondere im IoT-Bereich, und ein gemeinsames Muster aufweisen.

Diese Muster beginnen häufig mit IoT-fähigen Plattformen, die Milliarden von Ereignissen von Geräten und Sensoren empfangen, die sich an verschiedenen Standorten befinden. Diese Systeme verarbeiten und analysieren Datenströme, um in Echtzeit Erkenntnisse und Aktionen aus diesen Daten abzuleiten. Daten werden normalerweise in warmem und kaltem Speicher archiviert, um Batchanalysen nahezu in Echtzeit zu ermöglichen.

Gesammelte Daten durchlaufen eine Reihe von Verarbeitungsstufen, um sie zu bereinigen und in Kontext zu setzen für Downstreamabfrage- und Analyseszenarien. Azure bietet umfassende Dienste, die auf IoT-Szenarien wie Objektwartung und -fertigung angewendet werden können. Zu diesen Diensten gehören Azure Time Series Insights Gen2, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning und Power BI.

Eine Lösungsarchitektur lässt sich auf folgende Weise aufbauen:

* Erfassen von Daten über IoT Hub oder Event Hubs für klassenbeste Sicherheit, Durchsatz und Latenz.
* Durchführen der Datenverarbeitung und -berechnungen. Durchleiten der erfassten Daten durch Dienste wie Stream Analytics, Logic Apps und Azure Functions. Der Dienst, den Sie verwenden, hängt von den spezifischen Datenverarbeitungsanforderungen ab.
* Aus den Verarbeitungspipelines berechnete Signale werden zur Speicherung und Analyse per Push an Azure Time Series Insights Gen2 gesendet.

Azure Time Series Insights Gen2 bietet Datenuntersuchung nahezu in Echtzeit sowie objektbasierte Erkenntnisse aus historischen Daten. Je nach Ihren geschäftlichen Anforderungen können MapReduce- und Hive-Aufträge mit in Azure Time Series Insights Gen2 gespeicherten Daten ausgeführt werden, indem Azure Time Series Insights Gen2 mit Azure HDInsight verbunden wird. In Azure Time Series Insights Gen2 gespeicherte Daten sind für Power BI und andere Kundenanwendungen über die Abfrage-APIs der öffentlichen Oberfläche von Azure Time Series Insights Gen2 verfügbar. Diese Daten können für tief gehende Geschäfts- und Operational Intelligence-Szenarios verwendet werden.

## <a name="advanced-analytics"></a>Erweiterte Analyse

Integration mit erweiterten Analysediensten wie Machine Learning und Azure Databricks. Azure Time Series Insights Gen2 erfasst Rohdaten von Millionen von Geräten. Es fügt Kontextdaten hinzu, die nahtlos von einer Suite von Azure-Analysediensten verwendet werden können.

[![Analysen](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

Erweiterte Analysen und Machine Learning nutzen und verarbeiten große Mengen von Daten. Diese Daten werden verwendet, um datengesteuerte Entscheidungen zu treffen und Vorhersageanalysen durchzuführen. Bei IoT-Anwendungsfällen lernen erweiterte Analysealgorithmen aus den von Millionen von Geräten gesammelten Daten. Diese Geräte übertragen mehrmals pro Sekunde Daten. Die von IoT-Geräten erfassten Daten sind roh. Ihnen fehlen Kontextinformationen wie der Ort des Geräts und die Einheit der Sensormessung. Hieraus folgt, dass sich Rohdaten schwer direkt für erweiterte Analysen verwenden lassen.

Azure Time Series Insights Gen2 schließt die Lücke zwischen IoT-Daten und erweiterten Analysen auf zwei einfache und kostengünstige Weisen:

* Erstens erfasst Azure Time Series Insights Gen2 Telemetrierohdaten von Millionen von Geräten mithilfe von IoT Hub. Es reichert die Daten mit Kontextinformationen an und transformiert die Daten in ein Parquet-Format. Dieses Format lässt sich einfach mit anderen erweiterten Analysediensten wie Machine Learning, Azure Databricks und Drittanbieteranwendungen integrieren.

    Azure Time Series Insights Gen2 kann als Quelle der Wahrheit für alle Daten in der gesamten Organisation dienen. Er erstellt ein zentrales Repository für die Nutzung von Downstreamanalyse-Workloads. Da es sich bei Azure Time Series Insights Gen2 um einen nahezu in Echtzeit arbeitenden Speicherdienst handelt, können erweiterte Analysemodelle kontinuierlich aus den eingehenden IoT-Telemetriedaten lernen. Deshalb können die Modelle exaktere Vorhersagen treffen.

* Zweitens kann die Ausgabe von Machine Learning- und Vorhersagemodellen an Azure Time Series Insights Gen2 übergeben werden, um deren Ergebnisse zu visualisieren und zu speichern. Dieses Verfahren hilft Organisationen dabei, ihre Modelle zu optimieren. Azure Time Series Insights Gen2 vereinfacht die Visualisierung von Telemetrie-Streamingdaten in derselben Ebene wie die Ausgaben der trainierten Modelle. Auf diese Weise hilft es Data Science-Teams dabei, Anomalien und Muster zu erkennen.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum [Azure Time Series Insights Gen2-Explorer](./concepts-ux-panels.md).
* Lesen Sie zur Planung Ihrer Umgebung [Azure Time Series Insights Gen2-Planung](./how-to-plan-your-environment.md).
* Lesen Sie die Dokumentation zum [Beispielclient](https://github.com/Microsoft/tsiclient).