---
title: Unterstützung für den Datentyp „long“ – Azure Time Series Insights Gen2 | Microsoft-Dokumentation
description: Unterstützung für den Datentyp „long“ in Azure Time Series Insights Gen2.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/07/2020
ms.custom: dpalled
ms.openlocfilehash: 0f7ac9844bfe7eac7094d3b7fdf653e07f236599
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780823"
---
# <a name="adding-support-for-long-data-type-in-azure-time-series-insights-gen2"></a>Hinzufügen von Unterstützung für den Datentyp „long“ in Azure Time Series Insights Gen2

Das Hinzufügen von Unterstützung für den Datentyp „long“ wirkt sich nur darauf aus, wie numerische Daten in Azure Time Series Insights Gen2-Umgebungen gespeichert und indiziert werden. Wenn Sie eine Gen1-Umgebung verwenden, können Sie diese Änderungen ignorieren.

Ab dem 29. oder 30. Juni 2020, je nach Ihrer Region, werden Ihre Daten als **Long** oder **Double** indiziert.  Wenn Sie Fragen zu dieser Änderung oder Bedenken haben, reichen Sie ein Supportticket über das Azure-Portal ein, und beziehen Sie sich auf diese Kommunikation.

Wenn Sie von einem der folgenden Fälle betroffen sind, nehmen Sie die empfohlenen Änderungen vor:

- **Fall 1**: Sie verwenden aktuell Variablen des Zeitreihenmodells und senden in Ihren Telemetriedaten nur ganzzahlige Datentypen.
- **Fall 2**: Sie verwenden aktuell Variablen des Zeitreihenmodells und senden in Ihren Telemetriedaten sowohl ganzzahlige als auch nicht ganzzahlige Datentypen.
- **Fall 3**: Sie verwenden kategorische Variablen, um Kategorien ganzzahlige Werte zuzuordnen.
- **Fall 4**: Sie verwenden das JavaScript SDK, um eine benutzerdefinierte Front-End-Anwendung zu erstellen.
- **Fall 5**: Sie nähern sich an den Grenzwert von 1.000 für Eigenschaftsnamen im warmen Speicher an und senden sowohl ganzzahlige als auch nicht ganzzahlige Daten. Die Eigenschaftsanzahl kann im [Azure-Portal](https://portal.azure.com/) als Metrik angezeigt werden.

Wenn einer der Fälle auf Sie zutrifft, nehmen Sie Änderungen an Ihrem Modell vor. Aktualisieren Sie den Zeitreihenausdruck (TSX) in Ihrer Variablendefinition mit den empfohlenen Änderungen. Aktualisieren Sie beides:

- Azure Time Series Insights-Explorer
- Alle benutzerdefinierten Clients, die unsere APIs verwenden

Abhängig von ihrer IoT-Lösung und den Einschränkungen haben Sie möglicherweise keinen Einblick in die Daten, die an Ihre Azure Time Series Insights Gen2-Umgebung gesendet werden. Wenn Sie nicht sicher sind, ob Ihre Daten rein ganzzahlig oder aus ganzzahligen und nicht ganzzahligen zusammengesetzt sind, stehen Ihnen einige Optionen offen:

- Sie können warten, bis die Funktion freigegeben wird. Untersuchen Sie dann Ihre unformatierten Ereignisse auf der Benutzeroberfläche des Explorers, um zu verstehen, welche Eigenschaften in zwei separaten Spalten gespeichert werden.
- Sie können die empfohlenen Änderungen für alle numerischen Tags präventiv vornehmen.
- Sie könnten vorübergehend eine Teilmenge von Ereignissen an den Speicher weiterleiten, um das Schema besser verstehen und untersuchen zu können.

Zum Speichern von Ereignissen aktivieren Sie die [Ereigniserfassung](../event-hubs/event-hubs-capture-overview.md) für Azure Event Hubs, oder Sie [routen](../iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint) sie von Ihrem IoT Hub an Azure Blob Storage.

Daten können außerdem im [Event Hub-Explorer](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer) oder mithilfe des [Ereignisprozessorhosts](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md#receive-events) beobachtet werden.

Wenn Sie IoT Hub verwenden, finden Sie Informationen, wie Sie auf den integrierten Endpunkt zugreifen, unter [Lesen von D2C-Nachrichten vom integrierten Endpunkt](../iot-hub/iot-hub-devguide-messages-read-builtin.md).

> [!NOTE]
> Wenn Sie die empfohlenen Änderungen nicht vornehmen, kann es möglicherweise zu Unterbrechungen kommen. Beispielsweise geben die betroffenen Time Series Insights-Variablen, auf die über die Abfrage-APIs oder den Time Series Insights-Explorer zugegriffen wird, **Null** zurück (das heißt, es werden keine Daten im Explorer angezeigt).

## <a name="recommended-changes"></a>Empfohlene Änderungen

### <a name="case-1-using-time-series-model-variables-and-sending-only-integral-data-types-in-telemetry-data"></a>Fall 1: Verwenden von Variablen des Zeitreihenmodells und Senden von nur ganzzahlige Datentypen in Ihren Telemetriedaten

Die empfohlenen Änderungen für Fall 1 sind identisch mit denen für Fall 2. Folgen Sie den Anweisungen im Abschnitt für Fall 2.

### <a name="case-2-using-time-series-model-variables-and-sending-both-integral-and-nonintegral-types-in-telemetry-data"></a>Fall 2: Verwenden von Variablen des Zeitreihenmodells und Senden von sowohl ganzzahligen als auch nicht ganzzahligen Datentypen in den Telemetriedaten

Wenn Sie derzeit ganzzahlige Telemetriedaten senden, werden Ihre Daten in zwei Spalten aufgeteilt:

- **propertyValue_double**
- **propertyValue_long**

Die ganzzahligen Daten werden in **propertyValue_long** geschrieben. Zuvor in **propertyValue_double** erfasste (und zukünftige erfasste) numerische Daten werden nicht rüberkopiert.

Wenn Sie Daten für die Eigenschaft **propertyValue** übergreifend für diese zwei Spalten abfragen möchten, müssen Sie die Skalarfunktion **coalesce()** in Ihrem TSX verwenden. Die Funktion akzeptiert Argumente desselben Datentyps (**DataType**) und gibt den ersten Wert in der Argumentliste zurück, der nicht Null ist. Weitere Informationen finden Sie unter [Konzepte des Azure Time Series Insights Gen2-Datenzugriffs](/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions).

#### <a name="variable-definition-in-tsx---numeric"></a>Variablendefinition in TSX: numerisch

*Vorherige Variablendefinition:*

[![Screenshot des Dialogfelds „Neue Variable hinzufügen“ für die Variable „PropertyValue“, numerisch](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*Neue Variablendefinition:*

[![Screenshot des Dialogfelds „Neue Variable hinzufügen“ für die Variable „PropertyValue“ mit einem benutzerdefinierten Wert, numerisch](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

Sie können außerdem **coalesce($event.propertyValue.Double, toDouble($event.propertyValue.Long))** als benutzerdefinierten [Zeitreihenausdruck](/rest/api/time-series-insights/reference-time-series-expression-syntax) verwenden.

#### <a name="inline-variable-definition-using-tsx-query-apis---numeric"></a>Inlinevariablendefinition mithilfe von TSX-Abfrage-APIs: numerisch

*Vorherige Variablendefinition:*

```JSON
"PropertyValueVariable": {

    "kind": "numeric",

    "value": {

        "tsx": "$event.propertyValue.Double"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

*Neue Variablendefinition:*

```JSON
"PropertyValueVariable ": {

    "kind": "numeric",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

Sie können außerdem **coalesce($event.propertyValue.Double, toDouble($event.propertyValue.Long))** als benutzerdefinierten [Zeitreihenausdruck](/rest/api/time-series-insights/reference-time-series-expression-syntax) verwenden.

> [!NOTE]
> Es empfiehlt sich, diese Variablen an allen Stellen zu aktualisieren, an denen sie möglicherweise verwendet werden. Zu diesen Stellen zählen das Zeitreihenmodell, gespeicherte Abfragen und Power BI-Connectorabfragen.

### <a name="case-3-using-categorical-variables-to-map-integer-values-to-categories"></a>Fall 3: Verwenden von kategorischen Variablen, um Kategorien ganzzahlige Werte zuzuordnen

Wenn Sie zurzeit kategorische Variablen verwenden, die Kategorien ganzzahlige Werte zuordnen, nutzen Sie wahrscheinlich die **toLong**-Funktion, um Daten aus dem Typ **Double** in den Typ **Long** zu konvertieren. Wie in den Fällen 1 und 2 müssen Sie die Datentypspalten (**DataType**) **Double** und **Long** zusammenfügen.

#### <a name="variable-definition-in-time-series-explorer---categorical"></a>Variablendefinition im Time Series Explorer: kategorisch

*Vorherige Variablendefinition:*

[![Screenshot des Dialogfelds „Neue Variable hinzufügen“ für die Variable „PropertyValue“, kategorisch](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*Neue Variablendefinition:*

[![Screenshot des Dialogfelds „Neue Variable hinzufügen“ für die Variable „PropertyValue“ mit einem benutzerdefinierten Wert, kategorisch](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

Sie können außerdem **coalesce($event.propertyValue.Double, toDouble($event.propertyValue.Long))** als benutzerdefinierten [Zeitreihenausdruck](/rest/api/time-series-insights/preview#time-series-expression-and-syntax) verwenden.

Kategorische Variablen erfordern weiterhin einen ganzzahligen Werttyp. Der Datentyp (**DataType**) aller Argumente in **coalesce()** muss im benutzerdefinierten [Zeitreihenausdruck](/rest/api/time-series-insights/reference-time-series-expression-syntax) vom Typ **Long** sein.

#### <a name="inline-variable-definition-using-tsx-query-apis---categorical"></a>Inlinevariablendefinition mithilfe von TSX-Abfrage-APIs: kategorisch

*Vorherige Variablendefinition:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "tolong($event.propertyValue.Double)"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

*Neue Variablendefinition:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, tolong($event.propertyValue.Double))"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

Kategorische Variablen erfordern weiterhin einen ganzzahligen Werttyp. Der Datentyp (**DataType**) aller Argumente in **coalesce()** muss im benutzerdefinierten [Zeitreihenausdruck](/rest/api/time-series-insights/reference-time-series-expression-syntax) vom Typ **Long** sein.

> [!NOTE]
> Es empfiehlt sich, diese Variablen an allen Stellen zu aktualisieren, an denen sie möglicherweise verwendet werden. Zu diesen Stellen zählen das Zeitreihenmodell, gespeicherte Abfragen und Power BI-Connectorabfragen.

### <a name="case-4-using-the-javascript-sdk-to-build-a-custom-front-end-application"></a>Fall 4: Verwenden des JavaScript SDK zum Erstellen einer benutzerdefinierten Front-End-Anwendung

Wenn Sie von den Fällen 1–3 betroffen sind und benutzerdefinierte Anwendungen erstellen, müssen Sie Ihre Abfragen für die Verwendung der Funktion **coalesce()** aktualisieren, wie in den vorherigen Beispielen gezeigt.

### <a name="case-5-nearing-warm-store-1000-property-limit"></a>Fall 5: Annäherung an den Grenzwert von 1.000 Eigenschaften im warmen Speicher

Wenn Sie Benutzer eines warmen Speichers mit einer großen Anzahl von Eigenschaften sind und Grund zu der Annahme haben, dass diese Änderung Ihre Umgebung über den Grenzwert für warme Speicher von 1.000 Eigenschaftsnamen bringen würde, reichen Sie über das Azure-Portal ein Supportticket ein, und beziehen Sie sich auf diese Kommunikation.

## <a name="next-steps"></a>Nächste Schritte

- Anzeigen der vollständigen Liste der [unterstützten Datentypen](concepts-supported-data-types.md).
