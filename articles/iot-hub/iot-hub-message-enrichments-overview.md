---
title: Übersicht über Azure IoT Hub-Nachrichtenanreicherungen
description: Übersicht über Nachrichtenanreicherungen bei Azure IoT Hub-Nachrichten
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 13e35ab93fc37541548785c6355489eaf3a3efc2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66754553"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages-preview"></a>Nachrichtenanreicherungen bei Gerät-zu-Cloud-IoT Hub-Nachrichten (Vorschauversion)

Bei *Nachrichtenanreicherungen* handelt es sich um die Fähigkeit des IoT-Hubs, Nachrichten mit zusätzlichen Informationen zu *stempeln*, bevor sie an den angegebenen Endpunkt gesendet werden. Ein Grund für die Verwendung von Nachrichtenanreicherungen: Auf diese Weise lassen sich Daten einbeziehen, die zur Vereinfachung der Downstreamverarbeitung verwendet werden können. So kann beispielsweise durch die Anreicherung von Gerätetelemetrienachrichten mit einem Gerätezwillingstag die Last bei Kunden reduziert werden, um Gerätezwillings-API-Aufrufe für diese Informationen durchzuführen.

![Fluss von Nachrichtenanreicherungen](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Eine Nachrichtenanreicherung besteht aus drei Hauptelementen:

* Name oder Schlüssel der Anreicherung

* Einem Wert

* Einem oder mehreren [Endpunkten](iot-hub-devguide-endpoints.md), bei dem/denen die Anreicherung angewendet werden soll.

Der Schlüssel kann eine beliebige Zeichenfolge sein.

Der Wert kann eines der folgenden Beispiele sein:

* Eine statische Zeichenfolge. Dynamische Werte wie Bedingungen, Logik, Vorgänge und Funktionen sind nicht zulässig. Wenn Sie beispielsweise eine SaaS-Anwendung entwickeln, die von mehreren Kunden verwendet wird, können Sie jedem Kunden einen Bezeichner zuweisen und diesen Bezeichner in der Anwendung zur Verfügung stellen. Bei Ausführung der Anwendung stempelt IoT Hub die Gerätetelemetrienachrichten mit dem Bezeichner des Kunden und ermöglicht es so, dass die Nachrichten bei jedem Kunden anders verarbeitet werden.

* Informationen aus dem Gerätezwilling, z.B. dessen Pfad. Beispiele hierfür wären *$twin.tags.field* und *$twin.tags.latitude*.

* Der Name des IoT-Hubs, der die Nachricht sendet. Dieser Wert ist *$iothubname*.

## <a name="applying-enrichments"></a>Anwenden von Anreicherungen

Die Nachrichten können aus einer beliebigen Datenquelle stammen, die vom [IoT Hub-Nachrichtenrouting](iot-hub-devguide-messages-d2c.md) unterstützt wird, darunter die folgenden Beispiele:

* Gerätetelemetrie, z.B. Temperatur oder Druck
* Benachrichtigungen zu Änderungen bei Gerätezwillingen – Änderungen am Gerätezwilling
* Ereignisse im Gerätelebenszyklus, z.B. wenn das Gerät erstellt oder gelöscht wird

Sie können Anreicherungen zu Nachrichten, die an den integrierten Endpunkt eines IoT-Hubs gesendet werden, oder zu Nachrichten hinzufügen, die an benutzerdefinierte Endpunkte wie Azure Blob Storage, eine Service Bus-Warteschlange oder ein Service Bus-Thema weitergeleitet werden.

Sie können auch Anreicherungen zu Nachrichten hinzufügen, die bei Event Grid veröffentlicht werden, indem Sie den Endpunkt als Event Grid auswählen. Weitere Informationen finden Sie unter [Iot Hub und Event Grid](iot-hub-event-grid.md).

Anreicherungen werden pro Endpunkt angewendet. Wenn Sie fünf Anreicherungen angeben, die für einen bestimmten Endpunkt gestempelt werden sollen, werden alle an diesen Endpunkt gesendeten Nachrichten mit denselben fünf Anreicherungen gestempelt.

Wenn Sie erfahren möchten, wie Nachrichtenanreicherungen ausprobiert werden können, lesen Sie das [Tutorial zu Nachrichtenanreicherungen](tutorial-message-enrichments.md).

## <a name="limitations"></a>Einschränkungen

* Sie können bis zu 10 Anreicherungen pro IoT-Hub für diese Hubs im Standard- oder Basic-Tarif hinzufügen. Bei IoT-Hubs im Free-Tarif können Sie bis zu 2 Anreicherungen hinzufügen.

* Wenn Sie eine Anreicherung mit einem Wert anwenden, der auf ein Tag oder eine Eigenschaft im Gerätezwilling festgelegt wurde, wird der Wert in einigen Fällen als Zeichenfolgenwert gestempelt. Wenn beispielsweise ein Anreicherungswert auf „$twin.tags.field“ festgelegt wurde, werden die Nachrichten mit der Zeichenfolge „$twin.tags.field“ und nicht mit dem Wert dieses Feldes aus dem Zwilling gestempelt. Dies geschieht in den folgenden Fällen:

   * Ihr IoT-Hub ist im Basic-Tarif. IoT-Hubs im Basic-Tarif unterstützen keine Gerätezwillinge.

   * Ihr IoT-Hub ist im Standard-Tarif, doch das Gerät, das die Nachricht sendet, hat keinen Gerätezwilling.

   * Ihr IoT-Hub ist im Standard-Tarif, doch der für den Wert der Anreicherung verwendete Pfad für den Gerätezwilling ist nicht vorhanden. Wenn beispielsweise der Anreicherungswert auf „$twin.tags.location“ festgelegt wurde und es für den Gerätezwilling unter „tags“ keine„location“-Eigenschaft gibt, wird die Nachricht mit der Zeichenfolge „$twin.tags.location“ gestempelt. 

* Bei Updates an einem Gerätezwilling kann es bis zu fünf Minuten dauern, bis sie im entsprechenden Anreicherungswert angezeigt werden.

* Die Gesamtgröße der Nachricht, einschließlich der Anreicherungen, darf 256 KB nicht überschreiten. Wenn eine Nachricht größer als 256 KB ist, wird sie vom IoT-Hub gelöscht. Sie können Fehler mithilfe von [IoT Hub-Metriken](iot-hub-metrics.md) identifizieren und debuggen, wenn Nachrichten gelöscht werden. So können Sie beispielsweise „d2c.telemetry.egress.invalid“ überwachen.

## <a name="pricing"></a>Preise

Nachrichtenanreicherungen stehen ohne zusätzliche Gebühren zur Verfügung. Derzeit fallen für Sie Kosten an, wenn Sie eine Nachricht an einen IoT-Hub senden. Diese Nachricht wird Ihnen nur einmal in Rechnung gestellt – sogar dann, wenn sie an mehrere Endpunkte gesendet wird.

## <a name="availability"></a>Verfügbarkeit

Diese Funktion steht in der Vorschauversion zur Verfügung und zwar in allen Regionen mit Ausnahme von „USA, Osten“, „USA, Westen“, „Europa, Westen“, [Azure Government](/azure/azure-government/documentation-government-welcome), [Azure China 21Vianet](/azure/china) und [Azure Deutschland](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Weiterleiten von Nachrichten an einen IoT-Hub finden Sie in diesen Artikeln:

* [Verwenden des IoT Hub-Nachrichtenroutings zum Senden von Gerät-zu-Cloud-Nachrichten an verschiedene Endpunkte](iot-hub-devguide-messages-d2c.md)

* [Tutorial: IoT Hub-Routing](tutorial-routing.md)