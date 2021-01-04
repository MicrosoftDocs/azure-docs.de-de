---
title: Planen Ihrer Gen2-Umgebung – Azure Time Series Insights | Microsoft-Dokumentation
description: Bewährte Methoden für die Konfiguration, Verwaltung, Planung und Bereitstellung Ihrer Azure Time Series Insights Gen2-Umgebung.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: c8d96d7b5952c020493af278ee1ea8ad5ff46716
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016784"
---
# <a name="plan-your-azure-time-series-insights-gen2-environment"></a>Planen Ihrer Azure Time Series Insights Gen2-Umgebung

Dieser Artikel beschreibt bewährte Methoden zum Planen und schnellen Starten mit Azure Time Series Insights Gen2.

## <a name="best-practices-for-planning-and-preparation"></a>Bewährte Methoden für Planung und Vorbereitung

Bewährte Methoden zur Planung und Vorbereitung Ihrer Umgebung werden in den folgenden Artikeln ausführlicher beschrieben:

* Was Sie erhalten, wenn Sie [eine Azure Time Series Insights Gen2-Umgebung bereitstellen](#the-gen2-environment).
* Wie die [Time Series-IDs und Zeitstempeleigenschaften lauten](#configure-time-series-ids-and-timestamp-properties).
* Was das neue [Time Series-Modell ist](#understand-the-time-series-model) und wie Sie ein eigenes erstellen.
* Wie Sie [Ereignisse effizient an JSON senden](#shape-your-events).
* [Notfallwiederherstellung für Unternehmen](#business-disaster-recovery)

Für Azure Time Series Insights wird ein Geschäftsmodell mit nutzungsbasierte Bezahlung angewendet. Weitere Informationen zu Gebühren und Kapazitäten finden Sie auf der Seite [Azure Time Series Insights – Preise](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-gen2-environment"></a>Die Gen2-Umgebung

Beim Bereitstellen einer Azure Time Series Insights Gen2-Umgebung erstellen Sie zwei Azure-Ressourcen:

* Eine Azure Time Series Insights Gen2-Umgebung
* Ein Azure-Speicherkonto

Im Rahmen des Bereitstellungsprozesses geben Sie an, ob Sie einen Warm Storage aktivieren möchten. Der Warm Storage bietet Ihnen ein mehrstufiges Abfrageerlebnis. Wenn diese Option aktiviert ist, müssen Sie einen Aufbewahrungszeitraum zwischen 7 und 30 Tagen angeben. Abfragen, die innerhalb des Warm Storage-Aufbewahrungszeitraums ausgeführt werden, bieten in der Regel schnellere Antwortzeiten. Wenn sich eine Abfrage über den Warm Storage-Aufbewahrungszeitraum erstreckt, wird sie aus dem Cold Storage bearbeitet.

Abfragen zum Warm Storage sind kostenlos, während Abfragen zum Cold Storage Kosten verursachen. Es ist wichtig, Ihre Abfragemuster zu verstehen und Ihre Warm Storage-Konfiguration entsprechend zu planen. Es wird empfohlen, dass sich die interaktiven Analysen der aktuellsten Daten in Ihrem Warm Storage und die Musteranalysen sowie langfristigen Trends im Cold Storage befinden.

> [!NOTE]
> Weitere Informationen zum Abfragen Ihrer warmen Daten finden Sie in der [API-Referenz](/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters).

Um zu beginnen, benötigen Sie drei zusätzliche Elemente:

* Ein [Zeitreihenmodell](./concepts-model-overview.md)
* Eine [mit Time Series Insights verbundene Ereignisquelle](./concepts-streaming-ingestion-event-sources.md)
* [In die Ereignisquelle fließende Ereignisse](./time-series-insights-send-events.md), die sowohl dem Modell zugeordnet als auch im gültigen JSON-Format sind

## <a name="review-azure-time-series-insights-gen2-limits"></a>Überprüfen der Grenzwerte für Azure Time Series Insights Gen2

[!INCLUDE [Review Azure Time Series Insights Gen2 limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Konfigurieren von Time Series-IDs und Zeitstempeleigenschaften

Um eine neue Azure Time Series Insights-Umgebung zu erstellen, wählen Sie eine Zeitreihen-ID aus. Diese Vorgehensweise fungiert als logische Partition für Ihre Daten. Wie bereits erwähnt, stellen Sie sicher, dass Sie Ihre Time Series-ID bereit haben.

> [!IMPORTANT]
> Zeitreihen-IDs können *später nicht mehr geändert werden*. Überprüfen Sie jede vor der endgültigen Auswahl und der ersten Verwendung.

Sie können bis zu drei Schlüssel auswählen, um Ihre Ressourcen eindeutig zu unterscheiden. Weitere Informationen finden Sie unter [Bewährte Methoden für die Auswahl einer Time Series-ID](./how-to-select-tsid.md) und [Datenerfassungsregeln](concepts-json-flattening-escaping-rules.md).

Die **Timestamp**-Eigenschaft ist ebenfalls wichtig. Sie können diese Eigenschaft festlegen, wenn Sie Ereignisquellen hinzufügen. Jede Ereignisquelle besitzt eine optionale Timestamp-Eigenschaft, die zum Nachverfolgen von Ereignisquellen im Laufe der Zeit verwendet wird. Zeitstempelwerte beachten die Groß- und Kleinschreibung und müssen gemäß der individuellen Spezifikation jeder Ereignisquelle formatiert sein.

Wenn dieser Wert leer gelassen wird, wird als Ereigniszeitstempel der Zeitpunkt verwendet, zu dem das Ereignis in die IoT Hub- oder Event Hub-Warteschlange eingereiht wurde. Im Allgemeinen sollten sich Benutzer dafür entscheiden, die Zeitstempeleigenschaft anzupassen und den Zeitpunkt zu verwenden, zu dem der Sensor oder das Tag den Lesevorgang generiert hat, anstatt den Zeitpunkt der Einreihung in die Hubwarteschlange zu verwenden. Weitere Informationen zu Zeitzonenoffsets finden Sie unter [Zeitstempel der Ereignisquelle](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp).

## <a name="understand-the-time-series-model"></a>Grundlagen des Zeitreihenmodells

Sie können jetzt das Zeitreihenmodell Ihrer Azure Time Series Insights-Umgebung konfigurieren. Das neue Modell erleichtert das Auffinden und Analysieren von IoT-Daten. Es ermöglicht die Zusammenstellung, Wartung und Anreicherung von Zeitreihendaten und hilft bei der Vorbereitung für Consumer bereiter Datasets. Das Modell verwendet Time Series-IDs, die einer Instanz zugeordnet werden, die die eindeutige Ressource mit Variablen, bekannt als Typen, und Hierarchien verknüpft. Weitere Informationen finden Sie unter [Zeitreihenmodell in Azure Time Series Insights Gen2](./concepts-model-overview.md).

Das Modell ist dynamisch, damit es jederzeit erstellt werden kann. Für den schnellen Einstieg erstellen und laden Sie es hoch, bevor Sie Daten per Push in Azure Time Series Insights übertragen. Informationen zum Erstellen Ihres Modells finden Sie unter [Datenmodellierung in Azure Time Series Insights Preview](./concepts-model-overview.md).

Bei vielen Kunden wird das Zeitreihenmodell einem vorhandenen Ressourcenmodell oder einem bereits eingerichteten ERP-System zugeordnet. Wenn Sie nicht über ein vorhandenes Modell verfügen, ist wird eine vordefinierte Erfahrung [bereitgestellt](https://github.com/Microsoft/tsiclient), um schnell den Betrieb aufnehmen zu können. Um einen Eindruck zu erhalten, wie Ihnen ein Modell helfen könnte, sehen Sie sich die [Beispieldemoumgebung](https://insights.timeseries.azure.com/preview/demo) an.

## <a name="shape-your-events"></a>Gestalten Ihrer Ereignisse

Sie können die Art überprüfen, in der Sie Ereignisse an Azure Time Series Insights senden. Idealerweise werden Ihrer Ereignisse gut und effizient denormalisiert.

Eine gute Faustregel ist:

* Speichern Sie Metadaten in Ihrem Zeitreihenmodell.
* Stellen Sie sicher, dass Time Series-Modus, Instanzfelder und Ereignisse nur erforderliche Informationen enthalten, z. B. eine Time Series-ID oder eine Timestamp-Eigenschaft.

Lesen Sie die [JSON-Vereinfachungs- und -Escaperegeln](./concepts-json-flattening-escaping-rules.md), um zu verstehen, wie Ereignisse vereinfacht und gespeichert werden.

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Nächste Schritte

* Planen Sie mithilfe des Artikels [Einführung in Azure Advisor](../advisor/advisor-overview.md) Ihre Konfigurationseinstellungen für die Geschäftswiederherstellung.
* Planen Sie mithilfe des Artikels [Einführung in Azure Advisor](../advisor/advisor-overview.md) Ihre Konfigurationseinstellungen für die Geschäftswiederherstellung.
* Informieren Sie sich ausführlicher über die [Datenerfassung](./concepts-ingestion-overview.md) in Azure Time Series Insights Gen2.
* Lesen Sie den Artikel zur [Datenspeicherung](./concepts-storage.md) in Azure Time Series Insights Gen2.
* Erfahren Sie mehr über die [Datenmodellierung](./concepts-model-overview.md) in Azure Time Series Insights Gen2.