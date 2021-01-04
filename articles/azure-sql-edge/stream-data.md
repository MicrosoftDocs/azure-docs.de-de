---
title: Datenstreaming in Azure SQL Edge
description: Erfahren Sie mehr zum Datenstreaming in Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: f63ab040e750c0c642c9656a5482529b926e9295
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392111"
---
# <a name="data-streaming-in-azure-sql-edge"></a>Datenstreaming in Azure SQL Edge

Azure SQL Edge stellt eine native Implementierung von Datenstreamingfunktionen bereit, die als T-SQL-Streaming bezeichnet wird. Es ermöglicht Datenstreaming, Analysen und Ereignisverarbeitung in Echtzeit, um große Mengen schneller Streamingdaten aus mehreren Quellen gleichzeitig zu analysieren und zu verarbeiten. T-SQL-Streaming basiert auf der gleichen Hochleistungsstreaming-Engine wie [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) in Microsoft Azure. Das Feature unterstützt eine ähnliche Reihe von Funktionen, die von Azure Stream Analytics in der Edge-Umgebung geboten werden.

Wie Azure Stream Analytics erkennt T-SQL-Streaming Muster und Beziehungen in Informationen, die aus einer Reihe von IoT-Eingabequellen wie z. B. Geräten, Sensoren und Anwendungen extrahiert werden. Sie können mithilfe dieser Muster Aktionen auslösen und Workflows einleiten. Sie können z. B. Warnungen erstellen, Informationen in eine Berichts- oder Visualisierungslösung übertragen oder die Daten zur späteren Verwendung speichern. 

T-SQL-Streaming eignet sich für folgende Aufgaben:

* Analysieren von Telemetriedatenströmen von IoT-Geräten in Echtzeit
* Analysieren von Daten in Echtzeit, die von autonomen und fahrerlosen Fahrzeugen generiert werden
* Remoteüberwachung und Predictive Maintenance hochwertiger Industrie- oder Produktionsanlagen
* Anomalie- und Mustererkennung von IoT-Sensormessungen in der Landwirtschaft oder einem Energiebetrieb

## <a name="how-does-t-sql-streaming-work"></a>Wie funktioniert T-SQL-Streaming?

T-SQL-Streaming funktioniert genauso wie [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md#how-does-stream-analytics-work). Beispielsweise wird das Konzept von *Streamingaufträgen* für die Verarbeitung von Datenstreaming in Echtzeit befolgt. 

Ein Stream Analytics-Auftrag besteht aus den folgenden Elementen:

- **Streameingabe** : Definiert die Verbindungen mit einer Datenquelle, aus der der Datenstrom gelesen werden soll. Azure SQL Edge unterstützt zurzeit die folgenden Streameingabetypen:
    - Edge Hub
    - Kafka (Unterstützung für Kafka-Eingaben ist zurzeit nur für Intel-/AMD64-Versionen von Azure SQL Edge verfügbar.)

- **Streamausgabe** : Definiert die Verbindungen mit einer Datenquelle, in die der Datenstrom gelesen werden soll. Azure SQL Edge unterstützt zurzeit die folgenden Streamausgabetypen:
    - Edge Hub
    - SQL (Die SQL-Ausgabe kann eine lokale Datenbank in der Azure SQL Edge-Instanz, eine SQL Server-Remoteinstanz oder eine Azure SQL-Datenbank-Instanz sein.) 

- **Streamabfrage** : Definiert die Transformation, Aggregationen, Filter, Sortierung und Joins, die auf den Eingabestream angewendet werden sollen, bevor er in die Streamausgabe geschrieben wird. Die Streamabfrage basiert auf der von Azure Stream Analytics verwendeten Abfragesprache. Weitere Informationen finden Sie unter [Stream Analytics-Abfragesprache](/stream-analytics-query/stream-analytics-query-language-reference).

> [!IMPORTANT]
> T-SQL-Streaming unterstützt im Gegensatz zu Stream Analytics derzeit keine [Verweisdaten für Suchvorgänge](../stream-analytics/stream-analytics-use-reference-data.md) oder [benutzerdefinierten Funktionen (User Defined Functions, UDFs) und benutzerdefinierten Aggregate (User-Defined Aggregates, UDA) in Streamaufträgen](../stream-analytics/streaming-technologies.md#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c).

> [!NOTE]
> T-SQL-Streaming unterstützt nur eine Teilmenge der von Stream Analytics unterstützten Sprachoberfläche. Weitere Informationen finden Sie unter [Stream Analytics-Abfragesprache](/stream-analytics-query/stream-analytics-query-language-reference).

## <a name="limitations-and-restrictions"></a>Einschränkungen

Für T-SQL-Streaming gelten die folgenden Einschränkungen. 

- Nur ein Streamingauftrag kann jeweils gleichzeitig aktiv sein. Aufträge, die bereits laufen, müssen beendet werden, bevor ein anderer Auftrag gestartet werden kann.
- Jede Ausführung eines Streamingauftrags ist ein Singlethreadvorgang. Wenn der Streamingauftrag mehrere Abfragen enthält, wird jede Abfrage in serieller Reihenfolge ausgewertet.
- Wenn Sie einen Streamingauftrag in Azure SQL Edge beendet haben, kann eine kurze Verzögerung auftreten, bevor der nächste Streamingauftrag gestartet werden kann. Diese Verzögerung wird eingefügt, weil der zugrunde liegende Streamingprozess als Reaktion auf die Anforderung zum Beenden des Auftrags beendet und dann als Reaktion auf die Anforderung zum Starten des Auftrags neu gestartet werden muss. 
- T-SQL-Streaming unterstützt bis zu 32 Partitionen für einen Kafka-Datenstrom. Versuche, eine größere Anzahl von Partitionen zu konfigurieren, führen zu einem Fehler. 

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines Stream Analytics-Auftrags in Azure SQL Edge](create-stream-analytics-job.md)
- [Anzeigen von Metadaten für Streamingaufträge in Azure SQL Edge](streaming-catalog-views.md)
- [Erstellen eines externen Streams](create-external-stream-transact-sql.md)