---
title: 'Übersicht: Azure Time Series Insights (Vorschauversion) | Microsoft-Dokumentation'
description: Übersicht über Azure Time Series Insights Preview.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: anshan
ms.workload: big-data
ms.topic: overview
ms.date: 04/26/2019
ms.custom: seodec18
ms.openlocfilehash: 6bcf0155be87a5b36c6b850fa889e23a340ac7ec
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237511"
---
# <a name="azure-time-series-insights-preview"></a>Azure Time Series Insights (Vorschauversion)

Azure Time Series Insights (Vorschauversion) ist ein End-to-End-PaaS-Angebot (Platform-as-a-Service). Es wird verwendet, um stark kontextualisierte, zeitreihenoptimierte IoT-Daten zu sammeln, zu verarbeiten, zu speichern, zu analysieren und abzufragen. Time Series Insights ist ideal für Ad-hoc-Datenuntersuchungen und Betriebsanalysen geeignet. Bei Time Series Insights handelt es sich um ein speziell erweiterbares und angepasstes Dienstangebot, das für die weit reichenden Anforderungen von IoT-Bereitstellungen in der Industrie konzipiert ist.

> [!TIP]
> Zu allgemein verfügbaren Funktionen lesen Sie die [Übersicht zu Azure Time Series Insights (allgemein verfügbar)](time-series-insights-overview.md).

## <a name="video"></a>Video

### <a name="learn-more-about-azure-time-series-insights-preview-br"></a>Weitere Informationen zur Vorschauversion von Azure Time Series Insights. </br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="define-iot-data"></a>Definieren von IoT-Daten

IoT-Daten sind alle Industriedaten, die in ressourcenintensiven Organisationen vorhanden sind. IoT-Daten sind häufig in hohem Maße unstrukturiert, da sie von Ressourcen gesendet werden, die Messungen aufzeichnen, die mit relativ vielen Störungen verbunden sind. Zu diesen Messungen zählen Temperatur, Bewegung und Luftfeuchtigkeit. Diese Datenströme weisen häufig erhebliche Lücken, beschädigte Signale und fehlerhafte Messwerte auf. Daten aus diesen Datenströmen müssen bereinigt werden, bevor eine Analyse durchgeführt werden kann.

IoT-Daten sind häufig nur im Kontext zusätzlicher Daten sinnvoll, die von Erstanbieterquellen wie z.B. CRM oder ERP stammen. Auch aus Drittanbieter-Datenquellen wie z.B. Wetter oder Standort kommen Eingaben.

Das Ergebnis ist, dass nur ein Teil dieser Daten für betriebliche oder geschäftliche Zwecke genutzt wird. Daten dieser Art liefern einheitliche, umfassende, aktuelle und korrekte Informationen für die Berichterstellung und Analysen in Unternehmen. Erfasste IoT-Daten in verwertbare Erkenntnisse umzuwandeln, setzt voraus:

* Datenverarbeitung zum Bereinigen, Filtern, Interpolieren, Transformieren und Aufbereiten von Daten für die Analyse.
* Eine Struktur, damit eine Navigation durch die Daten möglich ist und diese verstanden werden können, d.h. für die Normalisierung und Kontextualisierung der Daten.
* Kostengünstige Speicherung zur langen bzw. endlosen Aufbewahrung von verarbeiteten – oder abgeleiteten – Daten und Rohdaten mehrerer Jahrzehnte.

Ein typischer IoT-Datenfluss ist in der folgenden Abbildung dargestellt.

  ![IoT-Datenfluss][1]

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights für industrielle IoT-Daten

Die derzeitige IoT-Landschaft ist vielfältig. Zu den Kunden zählen Fertigung, Automobilindustrie, Energiewirtschaft, Versorgungswirtschaft, Smart Buildings und Consulting. Zu den Szenarien zählt das Ad-hoc-Durchsuchen von Daten, wobei die Form der Daten unbekannt ist. Außerdem zählt zu den Szenarien die Betriebsanalyse schematisierter oder explizit modellierter Daten, um die Betriebseffizienz zu steigern. Diese Szenarien sind normalerweise parallel vorhanden und unterstützen unterschiedliche Anwendungsfälle. Plattformfunktionen, die Schlüssel zum Erfolg industrieller IoT-Unternehmen und der digitalen Revolution sind, enthalten:

- Mehrschichtigen Speicher, sowohl Warm Storage als auch Cold Storage.
- Die Fähigkeit zum Speichern der Zeitreihendaten aus mehreren Jahrzehnten.
- Die Fähigkeit, Abfragen explizit für ressourcenbasierte betriebsbezogene Intelligence zu modellieren und optimieren.

Time Series Insights ist ein umfassendes End-to-End-PaaS-Angebot, das die Untersuchung von IoT-Daten und die Gewinnung von betriebsbezogenen Erkenntnissen ermöglicht. Mit Time Series Insights wird ein vollständig verwalteter Clouddienst zum Analysieren von IoT-Zeitreihendaten bereitgestellt.

Sie können Rohdaten in einem schemafreien In-Memory-Speicher speichern. Dann können Sie interaktive Ad-hoc-Abfragen über eine Engine und API für verteilte Abfragen ausführen. Machen Sie die Erfahrung, Milliarden von Ereignissen in Sekunden zu visualisieren. Erfahren Sie mehr über die [Funktionen für die Untersuchung von Daten](./time-series-insights-overview.md).

Time Series Insights bietet Ihnen auch Funktionen zur Gewinnung von betriebsbezogenen Erkenntnissen, die sich derzeit in der Vorschauphase befinden. Zusammen mit den Funktionen für die interaktive Datenuntersuchung und betriebsbezogene Intelligence-Daten können Sie Time Series Insights zum Ableiten eines höheren Nutzens aus Daten verwenden, die über IoT-Ressourcen erfasst wurden. Die Vorschau unterstützt:

* Eine skalierbare leistungs- und kostenoptimierte Speicherung von Zeitreihendaten. Diese cloudbasierte IoT-Lösung kann aus Zeitreihendaten von Jahre in Sekunden Trends ermitteln.
* Unterstützung des Semantikmodells zur Beschreibung der Domänen- und Metadaten, die den abgeleiteten und nicht abgeleiteten Signalen von Ressourcen und Geräten zugeordnet sind.
* Eine verbesserte Benutzeroberfläche, für die ressourcenbasierte Datenerkenntnisse mit einer umfassenden Ad-hoc-Datenanalyse kombiniert werden. Diese Kombination unterstützt Business Intelligence und betriebsbezogene Intelligence.
* Integration in erweiterte Machine Learning- und Analysetools. Diese Tools umfassen Azure Databricks, Apache Spark, Azure Machine Learning, Jupyter-Notebooks und Power BI. Diese Tools helfen Ihnen, die Herausforderungen von Zeitreihendaten anzunehmen und die betriebliche Effizienz zu fördern.

Die Funktionen für betriebsbezogene Erkenntnisse und die Datenuntersuchung werden gemeinsam mit einem einfachen Preismodell mit nutzungsbasierter Bezahlung für Datenverarbeitung, -speicherung und -abfragen angeboten. Dieses Abrechnungsmodell passt sich Ihren wechselnden Geschäftsanforderungen an.

Dieses Datenflussdiagramm auf hoher Ebene zeigt die Updates an.

  ![Wichtige Funktionen][2]

Aufgrund der Einführung dieser wichtigen IoT-Funktionen für die Industrie verfügt Time Series Insights über die unten angegebenen entscheidenden Vorteile.

| | |
| ---| ---|
| Speicherung auf mehreren Ebenen für Zeitreihendaten im IoT-Maßstab | Mit einer allgemeinen Datenverarbeitungspipeline für die Erfassung von Daten können Sie Daten für interaktive Abfragen in Warm Storage speichern. Sie können auch große Mengen an Daten in „kaltem“ Speicher speichern. Profitieren Sie von der hohen Leistung ressourcenbasierter [Abfragen](./time-series-insights-update-tsq.md). |
| Zeitreihenmodell zum Kontextualisieren von Telemetrierohdaten und Gewinnen von ressourcenbasierten Erkenntnissen | Kontextualisieren Sie Telemetrierohdaten mit dem beschreibenden [Zeitreihenmodell](./time-series-insights-update-tsm.md). Gewinnen Sie umfangreiche betriebsbezogene Intelligence aus in hohem Maße leistungs- und kostenoptimierten gerätebasierten Abfragen. |
| Nahtlose und kontinuierliche Integration in andere Datenlösungen | Daten in Time Series Insights werden in Apache Parquet-Open Source-Dateien [gespeichert](./time-series-insights-update-storage-ingress.md). Diese Integration in andere Datenlösungen, ob von Erst- oder Drittanbietern, ist für End-to-End-Szenarien einfach. Diese Szenarien umfassen Business Intelligence, erweitertes Machine Learning und Predictive Analytics. |
| Datenuntersuchung nahezu in Echtzeit | Die Benutzeroberfläche des [Azure Time Series Insights Preview-Explorers](./time-series-insights-update-explorer.md) ermöglicht die Visualisierung für den gesamten Datenstreamingprozess über die Erfassungspipeline. Kurz nachdem Sie eine Verbindung mit einer Ereignisquelle hergestellt haben, können Sie Ereignisdaten anzeigen, durchsuchen und abfragen. Auf diese Weise können Sie überprüfen, ob ein Gerät Daten wie erwartet ausgibt. Sie können auch eine IoT-Ressource auf Integrität, Produktivität und allgemeine Wirksamkeit überwachen. |
| Ursachenanalyse und Anomalieerkennung | Der [Azure Time Series Insights Preview-Explorer](./time-series-insights-update-explorer.md) unterstützt sowohl Muster- als auch perspektivische Ansichten zum Durchführen und Speichern von mehrstufigen Ursachenanalysen. In Kombination mit Azure Stream Analytics können Sie Time Series Insights nutzen, um nahezu in Echtzeit Warnungen und Anomalien zu erkennen. |
| Benutzerdefinierte Anwendungen, die auf der Time Series Insights-Plattform basieren | Time Series Insights unterstützt das [JavaScript SDK](./tutorial-explore-js-client-lib.md). Das SDK bietet umfassende Steuerungsmöglichkeiten und vereinfachten Zugriff auf Abfragen. Verwenden Sie das SDK zum Erstellen benutzerdefinierter IoT-Anwendungen ergänzend zu Time Series Insights zur Anpassung an Ihre speziellen geschäftlichen Anforderungen. Darüber hinaus können Sie Time Series Insights-[Abfrage-APIs](./time-series-insights-update-tsq.md) direkt verwenden, um Daten in benutzerdefinierte IoT-Anwendungen zu befördern. |

## <a name="next-steps"></a>Nächste Schritte

Erste Schritte mit Azure Time Series Insights (Vorschauversion):

> [!div class="nextstepaction"]
> [Lesen Sie die Schnellstartanleitung](./time-series-insights-update-quickstart.md)

Hier erfahren Sie mehr über Anwendungsfälle:

> [!div class="nextstepaction"]
> [Anwendungsfälle für Azure Time Series Insights Preview](./time-series-insights-update-use-cases.md)

<!-- Images -->
[1]: media/v2-update-overview/overview_one.png
[2]: media/v2-update-overview/overview_two.png
