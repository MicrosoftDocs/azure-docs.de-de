---
title: Übersicht über Azure Stream Analytics
description: Erfahren Sie mehr über Stream Analytics, einen verwalteten Dienst, der Ihnen bei der Analyse der Streamingdaten aus dem Internet der Dinge (IoT) in Echtzeit hilft.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 05/16/2019
ms.openlocfilehash: f607c1e79262efdbec79677fa7f16863b7a9d160
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393983"
---
# <a name="what-is-azure-stream-analytics"></a>Was ist Azure Stream Analytics?

Azure Stream Analytics ist eine Ereignisverarbeitungs-Engine, mit der große Mengen an Streamingdaten untersucht werden können. In Informationen aus einer Reihe von Eingabequellen wie Geräten, Sensoren, Websites, Feeds sozialen Medien und Anwendungen können Muster und Beziehungen identifiziert werden. Diese Muster können dann dazu verwendet werden, nachgeschaltet andere Aktionen wie das Erstellen von Warnungen, das Senden von Informationen per Feed an ein Berichterstellungstool oder das Speichern von transformierten Informationen zur späteren Verwendung auszulösen.

Die folgenden Szenarien sind Beispiele für Situationen, in denen Azure Stream Analytics verwenden werden kann:

* Sensorfusion für das Internet der Dinge (Internet of Things, IoT) und Echtzeitanalysen für Gerätetelemetrie
* Analyse von Webprotokollen/Clickstreams
* Geoanalysen für das Flottenmanagement und fahrerlose Fahrzeuge
* Remoteüberwachung und Predictive Maintenance für Objekte mit hohem Wert
* Echtzeitanalysen von POS-Daten (Point of Sale) für die Bestandskontrolle und Anomalieerkennung

## <a name="how-does-stream-analytics-work"></a>Funktionsweise von Stream Analytics

Ein Azure Stream Analytics-Auftrag besteht aus einer Eingabe, einer Transformationsabfrage und einer Ausgabe. Wenn Ereignisse aus Ihrer Software oder Ihren Geräten von Azure Event Hubs, Azure IoT Hub oder Azure Blob Storage erfasst werden, können Sie diese Dienste als Eingabequelle für den Auftrag angeben. Die Transformationsabfrage basiert auf der SQL-Abfragesprache. Mit ihr werden Streamingdaten über einen bestimmten Zeitraum einfach gefiltert, sortiert, aggregiert und eingebunden. Sie können beim Durchführen von Aggregationsvorgängen die Optionen für die Ereignissortierung und die Dauer der Zeitfenster anpassen.

Zu jedem Auftrag gehört eine Ausgabe für die transformierten Daten. Sie können die Reaktion auf die analysierten Informationen steuern. Sie haben beispielsweise folgende Möglichkeiten:

* Senden von Daten an eine überwachte Warteschlange, um nachgeschaltete Warnungen oder benutzerdefinierte Workflows auszulösen
* Senden von Daten an ein Power BI-Dashboard für die Echtzeitvisualisierung
* Speichern von Daten in anderen Azure Storage-Diensten zum Trainieren eines Machine Learning-Modells basierend auf Verlaufsdaten oder zum Durchführen von Batchanalysen.

In der folgenden Abbildung ist zu sehen, wie Daten an Stream Analytics gesendet, analysiert und für andere Aktionen wie Speicherung oder Darstellung weitergeleitet werden:

![Stream Analytics-Einführung zur Pipeline](./media/stream-analytics-introduction/stream-analytics-intro-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Wichtige Funktionen und Vorteile

Azure Stream Analytics ist benutzerfreundlich, flexibel, zuverlässig und auf jede Auftragsgröße skalierbar. Dieses Modul steht übergreifend für mehrere Azure-Regionen zur Verfügung. In der folgenden Abbildung sind die wichtigsten Funktionen von Azure Stream Analytics dargestellt:

![Wichtige Funktionen von Stream Analytics](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Einfacher Einstieg

Der Einstieg in Azure Stream Analytics ist einfach. Es sind nur wenige Klicks erforderlich, um eine Verbindung mit mehreren Quellen oder Senken herzustellen und so eine End-to-End-Pipeline zu erstellen. Stream Analytics kann Verbindungen mit [Azure Event Hubs](/azure/event-hubs/) und [Azure IoT Hub](/azure/iot-hub/) zur Datenstromerfassung und mit [Azure Blob Storage](/azure/storage/storage-introduction) zur Erfassung von Verlaufsdaten herstellen. Die Auftragseingabe kann auch statische oder langsam veränderliche Referenzdaten von Azure Blob Storage oder [SQL-Datenbank](stream-analytics-use-reference-data.md#azure-sql-database) enthalten, die Sie mit Streamingdaten verknüpfen können, um Suchvorgänge durchzuführen.

Stream Analytics kann Auftragsausgaben an viele Speichersysteme leiten, z. B. [Azure Blob Storage](/azure/storage/storage-introduction), [Azure SQL-Datenbank](/azure/sql-database/), [Azure Data Lake Storage](/azure/data-lake-store/) und [Azure Cosmos DB](/azure/cosmos-db/introduction). Sie können Batchanalysen für gespeicherte Ausgaben mit Azure HDInsight ausführen oder die Ausgabe an einen anderen Dienst senden, z. B. Event Hubs zur Verarbeitung oder [Power BI](https://docs.microsoft.com/power-bi/) für Echtzeitvisualisierungen.

Die gesamte Liste von Stream Analytics-Ausgaben finden Sie unter [Grundlegendes zu den Ausgaben von Azure Stream Analytics](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Produktivität der Programmierer

Azure Stream Analytics verwendet eine einfache SQL-basierte Abfragesprache, die mit leistungsstarken zeitlichen Einschränkungen versehen wurde, um Daten während der Übertragung zu analysieren. Zum Definieren von Auftragstransformationen verwenden Sie eine einfache, deklarative [Stream Analytics-Abfragesprache](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference), mit der Sie komplexe temporale Abfragen und Analysen mithilfe von einfachen SQL-Konstrukten erstellen können. Da die Stream Analytics-Abfragesprache mit der SQL-Sprache konsistent ist, genügt die Vertrautheit mit SQL zum Erstellen von Aufträgen. Sie können Aufträge auch mit Entwicklertools wie Azure PowerShell, [Stream Analytics Visual Studio-Tools](stream-analytics-tools-for-visual-studio-install.md), der [Visual Studio Code-Erweiterung für Stream Analytics](quick-create-vs-code.md) oder Azure Resource Manager-Vorlagen erstellen. Die Nutzung von Entwicklertools ermöglicht Ihnen die Offlineentwicklung von Transformationsabfragen und die Verwendung der [CI/CD-Pipeline](stream-analytics-tools-for-visual-studio-cicd.md) zum Übermitteln von Aufträgen an Azure.

Die Stream Analytics-Abfragesprache verfügt über viele verschiedene Funktionen zum Analysieren und Verarbeiten von Streamingdaten. Diese Abfragesprache unterstützt die einfache Datenbearbeitung, Aggregationsfunktionen und komplexe geografische Funktionen. Sie können Abfragen im Portal bearbeiten und anhand von Stichprobendaten testen, die aus einem Livestream extrahiert werden.

Sie können die Funktionen der Abfragesprache erweitern, indem Sie zusätzliche Funktionen definieren und aufrufen. Sie können Funktionsaufrufe in Azure Machine Learning Service definieren, um Azure Machine Learning-Lösungen zu nutzen, und benutzerdefinierte JavaScript- oder C#-Funktionen oder benutzerdefinierte Aggregate integrieren, um im Rahmen einer Stream Analytics-Abfrage komplexe Berechnungen durchzuführen.

## <a name="fully-managed"></a>Vollständige Verwaltung

Azure Stream Analytics ist ein vollständig verwaltetes serverloses Angebot (PaaS) in Azure. Sie müssen zum Ausführen Ihrer Aufträge keine Hardware bereitstellen und keine Cluster verwalten. Azure Stream Analytics übernimmt die gesamte Verwaltung Ihres Auftrags, indem komplexe Computecluster in der Cloud eingerichtet werden und die erforderliche Leistungsoptimierung für die Ausführung des Auftrags durchgeführt wird. Die Integration von Azure Event Hubs und Azure IoT Hub macht es möglich, dass Aufträge Millionen von Ereignissen pro Sekunde erfassen, die von verbundenen Geräten, aus Clickstreams oder aus Protokolldateien stammen. Indem Sie die Partitionierungsfunktion von Event Hubs verwenden, können Sie Berechnungen in logische Schritte partitionieren, die bei Bedarf jeweils weiter partitioniert werden können, um die Skalierbarkeit zu erhöhen.

## <a name="run-in-the-cloud-on-in-the-intelligent-edge"></a>Ausführung in der Cloud oder im Intelligent Edge-Bereich

Für umfangreiche Analysen kann Azure Stream Analytics in der Cloud ausgeführt werden, und für Analysen mit sehr niedriger Latenz ist die Ausführung auf IoT Edge möglich. Für Azure Stream Analytics wird für die Cloud und den Edge die gleiche Abfragesprache genutzt, sodass Entwickler echte Hybridarchitekturen für die Datenstromverarbeitung erstellen können.

## <a name="low-total-cost-of-ownership"></a>Niedrige Gesamtkosten

Stream Analytics ist ein kostenoptimierter Clouddienst. Es fallen keine Anfangskosten an. Sie bezahlen nur für die [verbrauchten Streamingeinheiten](stream-analytics-streaming-unit-consumption.md) und die verarbeitete Datenmenge. Es sind keine Mindestabnahme und keine Clusterbereitstellungen erforderlich, und Sie können den Auftrag basierend auf Ihren Geschäftsanforderungen hoch- oder herunterskalieren.

## <a name="mission-critical-ready"></a>Geeignet für unternehmenskritische Workloads

Azure Stream Analytics ist in mehreren Regionen auf der ganzen Welt verfügbar und dank seiner Zuverlässigkeit, Sicherheit und Compliance für die Ausführung unternehmenskritischer Workloads geeignet.

### <a name="reliability"></a>Zuverlässigkeit

Für Azure Stream Analytics wird die Exactly-Once-Ereignisverarbeitung (genau einmal) und die At-Least-Once-Zustellung (mindestens einmal) von Ereignissen garantiert, sodass Ereignisse niemals verloren gehen. Die Exactly-Once-Verarbeitung wird mit ausgewählter Ausgabe garantiert, wie unter [Event Delivery Guarantees (Azure Stream Analytics)](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) (Garantien zur Ereignisbereitstellung (Azure Stream Analytics)) beschrieben.

Azure Stream Analytics verfügt über integrierte Wiederherstellungsfunktionen für den Fall, dass für ein Ereignis ein Fehler auftritt. Stream Analytics verfügt außerdem über integrierte Prüfpunkte, um den Zustand Ihres Auftrags aufrecht zu erhalten und wiederholbare Ergebnisse zu liefern.

Als verwalteter Dienst wird für Stream Analytics eine Ereignisverarbeitung mit einer Verfügbarkeit von 99,9 Prozent (minutengenau) garantiert. Weitere Informationen finden Sie auf der Seite [SLA für Stream Analytics](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/). 

### <a name="security"></a>Sicherheit

Azure Stream Analytics verschlüsselt jegliche ein- und ausgehende Kommunikation und unterstützt TLS 1.2. Integrierte Prüfpunkte werden ebenfalls verschlüsselt. Die eingehenden Daten werden von Stream Analytics nicht gespeichert, da die gesamte Verarbeitung im Arbeitsspeicher stattfindet.

### <a name="compliance"></a>Compliance

Azure Stream Analytics erfüllt die Anforderungen mehrerer Compliancezertifizierungen, wie in der [Übersicht über die Azure-Compliance](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) beschrieben. 

## <a name="performance"></a>Leistung

Stream Analytics kann pro Sekunde Millionen von Ereignissen verarbeiten und Ergebnisse mit kurzer Wartezeit liefern. Sie können vertikal und horizontal hochskalieren, um große Echtzeitanwendungen und komplexe Anwendungen für die Ereignisverarbeitung zu nutzen. Stream Analytics unterstützt die Leistung durch eine Partitionierung, damit komplexe Abfragen parallelisiert und auf mehreren Streamingknoten ausgeführt werden können. Azure Stream Analytics basiert auf [Trill](https://github.com/Microsoft/Trill), einem Hochleistungsanalysemodul für In-Memory-Streaming, das in Zusammenarbeit mit der Forschungsabteilung von Microsoft entwickelt wurde.

## <a name="next-steps"></a>Nächste Schritte

Sie haben einen Überblick über Azure Stream Analytics erhalten. Nun können Sie tiefer einsteigen und Ihren ersten Stream Analytics-Auftrag erstellen:

* [Erstellen eines Stream Analytics-Auftrags mithilfe des Azure-Portals](stream-analytics-quick-create-portal.md)
* [Erstellen eines Stream Analytics-Auftrags mithilfe von Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Erstellen eines Stream Analytics-Auftrags mithilfe von Visual Studio](stream-analytics-quick-create-vs.md)
* [Erstellen eines Stream Analytics-Auftrags mithilfe von Visual Studio Code](quick-create-vs-code.md)
