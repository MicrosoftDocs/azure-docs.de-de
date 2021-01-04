---
title: Einführung in Azure Stream Analytics
description: Hier erfahren Sie mehr über Azure Stream Analytics, einen verwalteten Dienst, der Ihnen bei der Analyse der Streamingdaten aus dem Internet der Dinge (IoT) in Echtzeit hilft.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc, contperf-fy21q2
ms.date: 11/12/2020
ms.openlocfilehash: 1871fdda7fe37e938f52c68e97bd9f40dbd9791d
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029276"
---
# <a name="welcome-to-azure-stream-analytics"></a>Willkommen bei Azure Stream Analytics

Azure Stream Analytics ist eine Engine für Analysen in Echtzeit und die Verarbeitung komplexer Ereignisse, die entwickelt wurde, um große Mengen schneller Streamingdaten aus mehreren Quellen gleichzeitig zu analysieren und zu verarbeiten. In Informationen aus einer Reihe von Eingabequellen wie Geräten, Sensoren, Clickstreams, Feeds sozialer Medien und Anwendungen können Muster und Beziehungen identifiziert werden. Diese Muster können verwendet werden, Aktionen und Workflows wie das Erstellen von Warnungen, das Senden von Informationen per Feed an ein Berichterstellungstool oder das Speichern transformierter Informationen zur späteren Verwendung auszulösen. Außerdem ist Stream Analytics in der Azure IoT Edge-Laufzeit verfügbar, wo es die Verarbeitung von Daten auf IoT-Geräten ermöglicht.

Die folgenden Szenarien sind Beispiele für Situationen, in denen Azure Stream Analytics verwenden werden kann:

* Analysieren von Telemetriedatenströmen von IoT-Geräten in Echtzeit
* Analyse von Webprotokollen/Clickstreams
* Geoanalysen für das Flottenmanagement und fahrerlose Fahrzeuge
* Remoteüberwachung und Predictive Maintenance für Objekte mit hohem Wert
* Echtzeitanalysen von POS-Daten (Point of Sale) für die Bestandskontrolle und Anomalieerkennung

Sie können Azure Stream Analytics mit einem kostenlosen Azure-Abonnement ausprobieren.

> [!div class="nextstepaction"]
> [Azure Stream Analytics ausprobieren](https://azure.microsoft.com/services/stream-analytics/)

## <a name="how-does-stream-analytics-work"></a>Funktionsweise von Stream Analytics

Ein Azure Stream Analytics-Auftrag besteht aus einer Eingabe, Abfrage und Ausgabe. Stream Analytics erfasst Daten aus Azure Event Hubs (einschließlich Azure Event Hubs von Apache Kafka), Azure IoT Hub oder Azure Blob Storage. Mit der Abfrage, die auf SQL (Structured Query Language) basiert, können Streamingdaten über einen bestimmten Zeitraum einfach gefiltert, sortiert, aggregiert und verknüpft werden. Sie können diese SQL-Sprache auch mit JavaScript und benutzerdefinierten C#-Funktionen (UDFs) erweitern. Sie können die Optionen für die Ereignisreihenfolge und die Dauer der Zeitfenster bei der Durchführung von Aggregationsvorgängen mithilfe einfacher Sprachkonstrukte und/oder Konfigurationen einfach anpassen.

Zu jedem Auftrag gehören eine oder mehrere Ausgaben für die transformierten Daten. Sie können die Reaktion auf die analysierten Informationen steuern. Beispielsweise können Sie folgende Aktionen ausführen:

* Senden von Daten an Dienste wie Azure Functions, Service Bus Topics oder Queues, um Kommunikation oder benutzerdefinierte Workflows nachgelagert auszulösen.
* Senden von Daten an ein Power BI-Dashboard für die Echtzeitvisualisierung
* Speichern von Daten in anderen Azure-Speicherdiensten (z. B. Azure Data Lake, Azure Synapse Analytics usw.) zum Trainieren eines Machine Learning-Modells basierend auf Verlaufsdaten oder zum Durchführen von Batchanalysen

In der folgenden Abbildung ist zu sehen, wie Daten an Stream Analytics gesendet, analysiert und für andere Aktionen wie Speicherung oder Darstellung weitergeleitet werden:

![Stream Analytics-Einführung zur Pipeline](./media/stream-analytics-introduction/stream-analytics-e2e-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Wichtige Funktionen und Vorteile

Azure Stream Analytics ist benutzerfreundlich, flexibel, zuverlässig und auf jede Auftragsgröße skalierbar. Die Engine ist in mehreren Azure-Regionen verfügbar und kann unter IoT Edge oder Azure Stack ausgeführt werden.

## <a name="ease-of-getting-started"></a>Einfacher Einstieg

Der Einstieg in Azure Stream Analytics ist einfach. Es sind nur wenige Klicks erforderlich, um eine Verbindung mit mehreren Quellen oder Senken herzustellen und so eine End-to-End-Pipeline zu erstellen. Stream Analytics kann Verbindungen mit Azure Event Hubs und Azure IoT Hub zur Datenstromerfassung und mit Azure Blob Storage zur Erfassung von Verlaufsdaten herstellen. Die Auftragseingabe kann auch statische oder langsam veränderliche Referenzdaten von Azure Blob Storage oder SQL-Datenbank enthalten, die Sie mit Streamingdaten verknüpfen können, um Suchvorgänge durchzuführen.

Stream Analytics kann Auftragsausgaben an viele Speichersysteme leiten, z.B. Azure Blob Storage, Azure SQL-Datenbank, Azure Data Lake Storage und Azure Cosmos DB. Sie können auch Batchanalysen für Datenstromausgaben mit Azure Synapse Analytics oder HDInsight ausführen oder die Ausgabe an einen anderen Dienst senden, z. B. Event Hubs zur Verarbeitung oder Power BI für Echtzeitvisualisierungen.

Die gesamte Liste von Stream Analytics-Ausgaben finden Sie unter [Grundlegendes zu den Ausgaben von Azure Stream Analytics](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Produktivität der Programmierer

Azure Stream Analytics verwendet eine SQL-Abfragesprache, die mit leistungsstarken zeitlichen Einschränkungen versehen wurde, um Daten während der Übertragung zu analysieren. Sie können Aufträge auch mit Entwicklertools wie Azure PowerShell, der Azure CLI, Stream Analytics Visual Studio-Tools, der [Visual Studio Code-Erweiterung für Stream Analytics](quick-create-visual-studio-code.md) oder Azure Resource Manager-Vorlagen erstellen. Die Nutzung von Entwicklertools ermöglicht Ihnen die Offlineentwicklung von Transformationsabfragen und die Verwendung der CI/CD-Pipeline zum Übermitteln von Aufträgen an Azure.

Mit der Stream Analytics-Abfragesprache können Sie CEP-Vorgänge (Complex Event Processing, komplexe Ereignisverarbeitung) durchführen, da sie über viele verschiedene Funktionen zum Analysieren von Streamingdaten verfügt. Diese Abfragesprache unterstützt die einfache Datenbearbeitung, Aggregations- und Analysefunktionen, räumliche Funktionen, Musterabgleich und Anomalieerkennung. Sie können Abfragen im Portal bearbeiten oder Ihre Entwicklungstools verwenden und sie anhand von Stichprobendaten testen, die aus einem Livestream extrahiert werden.

Sie können die Funktionen der Abfragesprache erweitern, indem Sie zusätzliche Funktionen definieren und aufrufen. Sie können Funktionsaufrufe in Azure Machine Learning definieren, um Azure Machine Learning-Lösungen zu nutzen, und benutzerdefinierte JavaScript- oder C#-Funktionen oder benutzerdefinierte Aggregate integrieren, um im Rahmen einer Stream Analytics-Abfrage komplexe Berechnungen durchzuführen.

## <a name="fully-managed"></a>Vollständige Verwaltung

Azure Stream Analytics ist ein vollständig verwaltetes Angebot (PaaS) in Azure. Sie müssen keine Hardware oder Infrastruktur bereitstellen und das Betriebssystem und die Software nicht aktualisieren. Azure Stream Analytics verwaltet Ihren Auftrag vollständig, sodass Sie sich auf Ihre Geschäftslogik konzentrieren können, nicht auf die Infrastruktur.


## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Ausführung in der Cloud oder im Intelligent Edge-Bereich

Für umfangreiche Analysen kann Azure Stream Analytics in der Cloud ausgeführt werden, und für Analysen mit sehr niedriger Latenz ist die Ausführung unter IoT Edge oder Azure Stack möglich. Azure Stream Analytics verwendet in der Cloud und in Edge dieselben Tools und dieselbe Abfragesprache, sodass Entwickler echte Hybridarchitekturen für die Datenstromverarbeitung erstellen können. 

## <a name="low-total-cost-of-ownership"></a>Niedrige Gesamtkosten

Stream Analytics ist ein kostenoptimierter Clouddienst. Es fallen keine Anfangskosten an. Sie bezahlen nur für die [verbrauchten Streamingeinheiten](stream-analytics-streaming-unit-consumption.md). Es sind keine Mindestabnahme und keine Clusterbereitstellungen erforderlich, und Sie können den Auftrag basierend auf Ihren Geschäftsanforderungen hoch- oder herunterskalieren.

## <a name="mission-critical-ready"></a>Geeignet für unternehmenskritische Workloads

Azure Stream Analytics ist in mehreren Regionen auf der ganzen Welt verfügbar und dank seiner Zuverlässigkeit, Sicherheit und Compliance für die Ausführung unternehmenskritischer Workloads geeignet.

### <a name="reliability"></a>Zuverlässigkeit

Für Azure Stream Analytics wird die Exactly-Once-Ereignisverarbeitung (genau einmal) und die At-Least-Once-Zustellung (mindestens einmal) von Ereignissen garantiert, sodass Ereignisse niemals verloren gehen. Die Exactly-Once-Verarbeitung wird mit ausgewählter Ausgabe garantiert, wie in den Garantien zur Ereignisbereitstellung beschrieben.

Azure Stream Analytics verfügt über integrierte Wiederherstellungsfunktionen für den Fall, dass für ein Ereignis ein Fehler auftritt. Stream Analytics verfügt außerdem über integrierte Prüfpunkte, um den Zustand Ihres Auftrags aufrecht zu erhalten und wiederholbare Ergebnisse zu liefern.

Als verwalteter Dienst wird für Stream Analytics eine Ereignisverarbeitung mit einer Verfügbarkeit von 99,9 % (minutengenau) garantiert. 

### <a name="security"></a>Sicherheit

Azure Stream Analytics verschlüsselt jegliche ein- und ausgehende Kommunikation und unterstützt TLS 1.2. Integrierte Prüfpunkte werden ebenfalls verschlüsselt. Die eingehenden Daten werden von Stream Analytics nicht gespeichert, da die gesamte Verarbeitung im Arbeitsspeicher stattfindet. Stream Analytics unterstützt beim Ausführen eines Auftrags in einem [Stream Analytics-Cluster](./cluster-overview.md) auch virtuelle Azure-Netzwerke (VNETs).

### <a name="compliance"></a>Kompatibilität

Azure Stream Analytics erfüllt die Anforderungen mehrerer Compliancezertifizierungen, wie in der [Übersicht über die Azure-Compliance](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) beschrieben. 

## <a name="performance"></a>Leistung

Stream Analytics kann pro Sekunde Millionen von Ereignissen verarbeiten und Ergebnisse mit äußerst kurzer Wartezeit liefern. Sie können vertikal und horizontal hochskalieren, um große Echtzeitanwendungen und komplexe Anwendungen für die Ereignisverarbeitung zu nutzen. Stream Analytics unterstützt eine höhere Leistung mittels Partitionierung, damit komplexe Abfragen parallelisiert und auf mehreren Streamingknoten ausgeführt werden können. Azure Stream Analytics basiert auf [Trill](https://github.com/Microsoft/Trill), einem Hochleistungsanalysemodul für In-Memory-Streaming, das in Zusammenarbeit mit der Forschungsabteilung von Microsoft entwickelt wurde.

## <a name="next-steps"></a>Nächste Schritte

Sie haben einen Überblick über Azure Stream Analytics erhalten. Nun können Sie tiefer einsteigen und Ihren ersten Stream Analytics-Auftrag erstellen:

* [Erstellen eines Stream Analytics-Auftrags mithilfe des Azure-Portals](stream-analytics-quick-create-portal.md)
* [Erstellen eines Stream Analytics-Auftrags mithilfe von Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Erstellen eines Stream Analytics-Auftrags mithilfe von Visual Studio](stream-analytics-quick-create-vs.md)
* [Erstellen eines Stream Analytics-Auftrags mithilfe von Visual Studio Code](quick-create-visual-studio-code.md)
