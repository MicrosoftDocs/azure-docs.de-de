---
title: Ausgabe von Service Bus-Warteschlangen von Azure Stream Analytics
description: In diesem Artikel werden Service Bus-Warteschlangen als Ausgabe für Azure Stream Analytics beschrieben.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 3527bdbbf1ba337d24dc390f97ed090c8dfbe039
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348388"
---
# <a name="service-bus-queues-output-from-azure-stream-analytics"></a>Ausgabe von Service Bus-Warteschlangen von Azure Stream Analytics

[Service Bus-Warteschlangen](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) bieten eine FIFO-Nachrichtenzustellung (First In, First Out) an einen Consumer oder an mehrere konkurrierende Consumer. In der Regel werden Nachrichten von den Empfängern in der zeitlichen Reihenfolge empfangen und verarbeitet, in der sie zur Warteschlange hinzugefügt wurden. Jede Nachricht wird von nur einem Nachrichtenconsumer empfangen und verarbeitet.

Auf [Kompatibilitätsebene 1.2](stream-analytics-compatibility-level.md) verwendet Azure Stream Analytics das Nachrichtenprotokoll [Advanced Message Queueing Protocol (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md), um in Service Bus-Warteschlangen und -Themen zu schreiben. AMQP gibt Ihnen die Möglichkeit, plattformübergreifende Hybridanwendungen mit einem offenen Standard zu erstellen.

## <a name="output-configuration"></a>Ausgabekonfiguration

Die folgende Tabelle enthält die Eigenschaftennamen und die entsprechenden Beschreibungen zum Erstellen einer Warteschlangenausgabe.

| Eigenschaftenname | BESCHREIBUNG |
| --- | --- |
| Ausgabealias |Ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diese Service Bus-Warteschlange weiterzuleiten. |
| Service Bus-Namespace |Ein Container für einen Satz von Nachrichtenentitäten. |
| Warteschlangenname |Der Name der Service Bus-Warteschlange. |
| Name der Warteschlangenrichtlinie |Beim Erstellen einer Warteschlange können Sie auf der Registerkarte **Konfigurieren** der Warteschlange entsprechende Richtlinien für den gemeinsamen Zugriff erstellen. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. |
| Schlüssel der Warteschlangenrichtlinie |Der Schlüssel für den gemeinsamen Zugriff, der für die Authentifizierung des Zugriffs auf den Service Bus-Namespace verwendet wird. |
| Ereignisserialisierungsformat |Das Serialisierungsformat für Ausgabedaten. Es werden JSON, CSV und Avro unterstützt. |
| Codieren |Bei CSV und JSON ist UTF-8 gegenwärtig das einzige unterstützte Codierungsformat. |
| Trennzeichen |Gilt nur für die CSV-Serialisierung. Stream Analytics unterstützt eine Reihe von üblichen Trennzeichen zum Serialisieren der Daten im CSV-Format. Unterstützte Werte sind Komma, Semikolon, Leerzeichen, Tabstopp und senkrechter Strich. |
| Format |Gilt nur für den JSON-Typ. **Separate Zeile** gibt an, dass die Ausgabe so formatiert wird, dass jedes JSON-Objekt in einer neuen Zeile enthalten ist. Wenn Sie **Separate Zeile** auswählen, wird der JSON-Code objektweise gelesen. Der gesamte Inhalt an sich wäre kein gültiger JSON-Code. **Array** gibt an, dass die Ausgabe als Array aus JSON-Objekten formatiert wird. |
| Eigenschaftenspalten | Optional. Durch Komma getrennte Spalten, die anstelle der Nutzlast als Benutzereigenschaften der ausgehenden Nachricht angefügt werden müssen. Weitere Informationen zu diesem Feature finden Sie im Abschnitt [Benutzerdefinierte Metadateneigenschaften für die Ausgabe](#custom-metadata-properties-for-output). |
| Systemeigenschaftsspalten | Optional. Schlüssel-Wert-Paare von Systemeigenschaften und entsprechenden Spaltennamen, die an die ausgehende Nachricht anstatt an die Nutzlast angefügt werden müssen.  |

Die Anzahl der Partitionen [basiert auf der Service Bus-SKU und -Größe](../service-bus-messaging/service-bus-partitioning.md). Ein Partitionsschlüssel gibt einen eindeutigen ganzzahligen Wert für jede Partition an.

## <a name="partitioning"></a>Partitionierung

Die Partitionierung wird automatisch ausgewählt. Die Anzahl der Partitionen basiert auf der [Service Bus-SKU und -Größe](../service-bus-messaging/service-bus-partitioning.md). Der Partitionsschlüssel gibt einen eindeutigen ganzzahligen Wert für jede Partition an. Die Anzahl der Ausgabeschreiber entspricht der Anzahl von Partitionen in der Ausgabewarteschlange.

## <a name="output-batch-size"></a>Ausgabebatchgröße

Die maximale Nachrichtengröße beträgt 256 KB pro Nachricht im Standard-Tarif und 1 MB im Premium-Tarif. Weitere Informationen finden Sie unter [Service Bus-Grenzwerte](../service-bus-messaging/service-bus-quotas.md). Verwenden Sie zur Optimierung ein einzelnes Ereignis pro Nachricht.

## <a name="custom-metadata-properties-for-output"></a>Benutzerdefinierte Metadateneigenschaften für die Ausgabe

Sie können Abfragespalten als Benutzereigenschaften an Ihre ausgehenden Nachrichten anfügen. Diese Spalten gelangen nicht in die Nutzlast. Die Eigenschaften liegen in Form eines Wörterbuchs für die Ausgabemeldung vor. *Schlüssel* ist der Spaltenname und der *Wert* ist der Spaltenwert im Eigenschaftenwörterbuch. Alle Stream Analytics-Datentypen werden mit Ausnahme von „Datensatz“ und „Array“ unterstützt.

Im folgenden Beispiel werden die Felder `DeviceId` und `DeviceStatus` zu den Metadaten hinzugefügt.

1. Verwenden Sie die folgende Abfrage:

   ```sql
   select *, DeviceId, DeviceStatus from iotHubInput
   ```

1. Konfigurieren Sie `DeviceId,DeviceStatus` als Eigenschaftenspalten in der Ausgabe.

   :::image type="content" source="media/service-bus-queues-output/property-columns.png" alt-text="Eigenschaftenspalten":::

Die folgende Abbildung zeigt die erwarteten Eigenschaften der Ausgabemeldung, die in EventHub mit dem [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) überprüft wurden.

:::image type="content" source="media/service-bus-queues-output/custom-properties.png" alt-text="Benutzerdefinierte Ereigniseigenschaften":::

## <a name="system-properties"></a>Systemeigenschaften

Sie können Abfragespalten als [Systemeigenschaften](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet&preserve-view=true#properties) an Ihre ausgehenden Service Bus-Warteschlangen- oder Themenmeldungen anfügen.

Diese Spalten werden nicht in die Nutzlast eingefügt. Stattdessen wird die entsprechende BrokeredMessage-[Systemeigenschaft](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet&preserve-view=true#properties) mit den Werten der Abfragespalte aufgefüllt.
Diese Systemeigenschaften werden unterstützt: `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`.

Zeichenfolgenwerte dieser Spalten werden als entsprechender Systemeigenschaftswert-Typ analysiert, und alle Analysefehler werden als Datenfehler behandelt.
Dieses Feld wird im JSON-Objektformat bereitgestellt. Details zu diesem Format:

* Umgeben von geschweiften Klammern {}.
* Geschrieben in Schlüssel/Wert-Paaren.
* Schlüssel und Werte müssen Zeichenfolgen sein.
* Der Schlüssel ist der Name der Systemeigenschaft, und der Wert ist der Name der Abfragespalte.
* Schlüssel und Werte werden hierbei durch einen Doppelpunkt voneinander getrennt.
* Die einzelnen Schlüssel/Wert-Paare sind durch ein Komma voneinander getrennt.

So wird diese Eigenschaft verwendet:

* Abfrage: `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Systemeigenschaftsspalten: `{ "MessageId": "column1", "PartitionKey": "column2"}`

Dadurch wird die `MessageId` für Service Bus-Warteschlangenmeldungen mit den Werten von `column1` und PartitionKey mit `column2`-Werten festgelegt.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe des Azure-Portals](stream-analytics-quick-create-portal.md)
* [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags mit der Azure CLI](quick-create-azure-cli.md)
* [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags mithilfe einer ARM-Vorlage](quick-create-azure-resource-manager.md)
* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mit Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags mithilfe von Visual Studio](stream-analytics-quick-create-vs.md)
* [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags in Visual Studio Code](quick-create-visual-studio-code.md)