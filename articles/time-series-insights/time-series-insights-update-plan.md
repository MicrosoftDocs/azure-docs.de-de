---
title: Planen Ihrer Azure Time Series Insights Preview-Umgebung | Microsoft-Dokumentation
description: Planen Sie Ihre Azure Time Series Insights Preview-Umgebung.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 435e5f2163270672ac5f1f5695ca2fe9be22ee6b
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388729"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planen Ihrer Azure Time Series Insights Preview-Umgebung

Dieser Artikel beschreibt bewährte Methoden zum Planen und schnellen Starten mit Azure Time Series Insights Preview.

> [!NOTE]
> Bewährte Methoden zum Planen einer Time Series Insights-Instanz für allgemeine Verfügbarkeit finden Sie unter [Planen Ihrer Azure Time Series Insights-GA-Umgebung](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Bewährte Methoden für Planung und Vorbereitung

Kenntnis und Verständnis der folgenden Aspekte erleichtern die ersten Schritte mit Time Series Insights:

* Was Sie erhalten, wenn Sie [eine Azure Time Series Insights Preview-Umgebung bereitstellen](#the-preview-environment).
* Wie die [Time Series-IDs und Zeitstempeleigenschaften lauten](#configure-time-series-ids-and-timestamp-properties).
* Was das neue [Time Series-Modell ist](#understand-the-time-series-model) und wie Sie ein eigenes erstellen.
* Wie Sie [Ereignisse effizient an JSON senden](#shape-your-events).
* [Business Disaster Recovery-Optionen](#business-disaster-recovery) mit Time Series Insights.

Für Azure Time Series Insights wird ein Geschäftsmodell mit nutzungsbasierte Bezahlung angewendet. Weitere Informationen zu Gebühren und Kapazitäten finden Sie unter [Time Series Insights – Preise](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>Die Preview-Umgebung

Wenn Sie eine Time Series Insights Preview-Umgebung bereitstellen, erstellen Sie zwei Azure-Ressourcen:

* Eine Azure Time Series Insights Preview-Umgebung
* Ein Azure-Speicherkonto vom Typ „Allgemein v1“

Um zu beginnen, benötigen Sie drei zusätzliche Elemente:

* Ein [Zeitreihenmodell](./time-series-insights-update-tsm.md)
* Eine [mit Time Series Insights verbundene Ereignisquelle](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [In die Ereignisquelle fließende Ereignisse](./time-series-insights-send-events.md), die sowohl dem Modell zugeordnet als auch im gültigen JSON-Format sind

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Konfigurieren von Time Series-IDs und Zeitstempeleigenschaften

Um eine neue Time Series Insights-Umgebung zu erstellen, wählen Sie eine Time Series-ID aus. Diese Vorgehensweise fungiert als logische Partition für Ihre Daten. Wie bereits erwähnt, stellen Sie sicher, dass Sie Ihre Time Series-ID bereit haben.

> [!IMPORTANT]
> Time Series-IDs sind *unveränderlich* und *können später nicht mehr geändert werden*. Überprüfen Sie jede vor der endgültigen Auswahl und der ersten Verwendung.

Sie können bis zu drei Schlüssel auswählen, um Ihre Ressourcen eindeutig zu unterscheiden. Weitere Informationen finden Sie unter [Bewährte Methoden für die Auswahl einer Time Series-ID](./time-series-insights-update-how-to-id.md) und [Speicherung und Dateneingang](./time-series-insights-update-storage-ingress.md).

Die Timestamp-Eigenschaft ist ebenfalls wichtig. Sie können diese Eigenschaft festlegen, wenn Sie Ereignisquellen hinzufügen. Jede Ereignisquelle besitzt eine optionale Timestamp-Eigenschaft, die zum Nachverfolgen von Ereignisquellen im Laufe der Zeit verwendet wird. Zeitstempelwerte beachten die Groß- und Kleinschreibung und müssen gemäß der individuellen Spezifikation jeder Ereignisquelle formatiert sein.

> [!TIP]
> Überprüfen Sie die Formatierungs- und Analyseanforderungen für Ihre Ereignisquellen.

Wenn der Zeitstempel eines Ereignisses leer bleibt, wird der Zeitpunkt der Einreihung des Ereignisses in die Warteschlange einer Ereignisquelle verwendet. Wenn Sie historische Daten oder Ereignisse im Batch senden, ist es hilfreicher, die Timestamp-Eigenschaft anzupassen, statt den Zeitpunkt der Einreihung des Ereignisses in die Warteschlange zu verwenden. Weitere Informationen finden Sie unter der Vorgehensweise zum [Hinzufügen von Ereignisquellen in Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Grundlagen des Zeitreihenmodells

Sie können jetzt das Zeitreihenmodell Ihrer Time Series Insights-Umgebung konfigurieren. Das neue Modell erleichtert das Auffinden und Analysieren von IoT-Daten. Es ermöglicht die Zusammenstellung, Wartung und Anreicherung von Zeitreihendaten und hilft bei der Vorbereitung für Consumer bereiter Datasets. Das Modell verwendet Time Series-IDs, die einer Instanz zugeordnet werden, die die eindeutige Ressource mit Variablen, bekannt als Typen, und Hierarchien verknüpft. Erfahren Sie mehr über das neue [Zeitreihenmodell](./time-series-insights-update-tsm.md).

Das Modell ist dynamisch, damit es jederzeit erstellt werden kann. Für den schnellen Einstieg erstellen und laden Sie es hoch, bevor Sie Daten per Push in Time Series Insights übertragen. Informationen zum Erstellen Ihres Modells finden Sie unter [Verwenden des Zeitreihenmodells](./time-series-insights-update-how-to-tsm.md).

Bei vielen Kunden wird das Zeitreihenmodell einem vorhandenen Ressourcenmodell oder einem bereits eingerichteten ERP-System zugeordnet. Wenn Sie nicht über ein vorhandenes Modell verfügen, ist wird eine vordefinierte Erfahrung [bereitgestellt](https://github.com/Microsoft/tsiclient), um schnell den Betrieb aufnehmen zu können. Um einen Eindruck zu erhalten, wie Ihnen ein Modell helfen könnte, sehen Sie sich die [Beispieldemoumgebung](https://insights.timeseries.azure.com/preview/demo) an.

## <a name="shape-your-events"></a>Gestalten Ihrer Ereignisse

Sie können die Art überprüfen, in der Sie Ereignisse an Time Series Insights senden. Idealerweise werden Ihrer Ereignisse gut und effizient denormalisiert.

Eine gute Faustregel ist:

* Speichern Sie Metadaten in Ihrem Zeitreihenmodell.
* Time Series-Modus, Instanzfelder und Ereignisse enthalten nur erforderliche Informationen, z.B. eine Time Series-ID oder einen Zeitstempel.

Weitere Informationen finden Sie unter [Gestalten von Ereignissen](./time-series-insights-send-events.md#json).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Speicherung und Dateneingang](./time-series-insights-update-storage-ingress.md) finden Sie in der Time Series Insights-Vorschauversion.
- Weitere Informationen zu [Datenmodellierung](./time-series-insights-update-tsm.md) finden Sie in der Time Series Insights-Vorschauversion.