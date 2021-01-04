---
title: Behandeln von Problemen mit dem Azure IoT-Nachrichtenrouting
description: Hier erfahren Sie, wie Sie Probleme im Zusammenhang mit dem Azure IoT-Nachrichtenrouting behandeln.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: asrastog
ms.openlocfilehash: 29127a9dff42c0f733e3721d1ea5fea7350e774e
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547355"
---
# <a name="troubleshooting-message-routing"></a>Behandeln von Problemen mit dem Nachrichtenrouting

Dieser Artikel enthält Informationen zur Überwachung und Behandlung von Problemen im Zusammenhang mit dem [Nachrichtenrouting](iot-hub-devguide-messages-d2c.md) von IoT Hub sowie entsprechende Lösungen.

## <a name="monitoring-message-routing"></a>Überwachen des Nachrichtenroutings

Es empfiehlt sich, [IoT Hub-Metriken im Zusammenhang mit Nachrichtenrouting und -endpunkten](monitor-iot-hub-reference.md#routing-metrics) zu überwachen, um eine Übersicht über die gesendeten Nachrichten zu erhalten. Sie können auch eine Diagnoseeinstellung erstellen, um Vorgänge für [**Routen** in IoT-Hub-Ressourcenprotokollen](monitor-iot-hub-reference.md#routes) zur benutzerdefinierten Verarbeitung an Azure Monitor-Protokolle, Event Hub-Instanzen oder Azure Storage zu senden. Weitere Informationen zur Verwendung von Metriken, Ressourcenprotokollen und Diagnoseeinstellungen finden Sie unter [Überwachen von IoT Hub](monitor-iot-hub.md). Ein Tutorial finden Sie unter [Einrichten und Verwenden von Metriken und Ressourcenprotokollen mit einem IoT-Hub](tutorial-use-metrics-and-diags.md).

Darüber hinaus empfiehlt es sich, die [Fallbackroute](iot-hub-devguide-messages-d2c.md#fallback-route) zu aktivieren, wenn Sie Nachrichten aufbewahren möchten, die nicht der Abfrage für eine der Routen entsprechen. Diese können im [integrierten Endpunkt](iot-hub-devguide-messages-read-builtin.md) für die konfigurierte Aufbewahrungsdauer (in Tagen) aufbewahrt werden.

## <a name="top-issues"></a>Häufigste Probleme

Im Anschluss finden Sie die Probleme, die im Zusammenhang mit dem Nachrichtenrouting am häufigsten auftreten. Klicken Sie auf ein Problem, um ausführliche Schritte anzuzeigen und mit der Problembehandlung zu beginnen.

* [Nachrichten von meinen Geräten werden nicht erwartungsgemäß weitergeleitet.](#messages-from-my-devices-are-not-being-routed-as-expected)
* [Ich erhalte plötzlich keine Nachrichten mehr am integrierten Event Hubs-Endpunkt.](#i-suddenly-stopped-getting-messages-at-the-built-in-endpoint)

### <a name="messages-from-my-devices-are-not-being-routed-as-expected"></a>Nachrichten von meinen Geräten werden nicht erwartungsgemäß weitergeleitet.

Analysieren Sie Folgendes:

#### <a name="the-routing-metrics-for-this-endpoint"></a>Die Routingmetriken für diesen Endpunkt

Alle routingbezogenen [IoT Hub-Metriken](monitor-iot-hub-reference.md#routing-metrics) beginnen mit *Routing*. Sie können Informationen aus mehreren Metriken kombinieren, um die Grundursache eines Problems zu ermitteln. Verwenden Sie beispielsweise die Metrik **Routing Delivery Attempts** (Routing: Zustellversuche), um zu ermitteln, wie viele Nachrichten an einen Endpunkt übermittelt oder verworfen wurden, wenn sie keiner Abfrage für eine der Routen entsprachen und die Fallbackroute deaktiviert war. Überprüfen Sie anhand der Metrik **Routing Latency** (Routingwartezeit), ob die Wartezeit für die Nachrichtenübermittlung konstant ist oder zunimmt. Eine zunehmende Wartezeit kann auf ein Problem mit einem bestimmten Endpunkt hindeuten, und es empfiehlt sich, die [Integrität des Endpunkts](#the-health-of-the-endpoint) zu überprüfen. Diese Routingmetriken verfügen auch über [Dimensionen](monitor-iot-hub-reference.md#metric-dimensions), die Details zur Metrik liefern – etwa den Endpunkttyp, den Namen eines bestimmten Endpunkts und einen Grund dafür, warum die Nachricht nicht übermittelt wurde.

#### <a name="the-resource-logs-for-any-operational-issues"></a>Die Ressourcenprotokolle für Betriebsprobleme

Sehen Sie sich die [**Ressourcenprotokolle** für Routen](monitor-iot-hub-reference.md#routes) an, um weitere Informationen zu [Routing- und Endpunktvorgängen](#operation-names) zu erhalten oder Fehler und relevante [Fehlercodes](#common-error-codes) zu ermitteln, um das Problem besser zu verstehen. Der Vorgangsname **RouteEvaluationError** im Protokoll bedeutet beispielsweise, dass die Route aufgrund eines Problems mit dem Nachrichtenformat nicht ausgewertet werden konnte. Nutzen Sie bei der Problembehandlung die für den jeweiligen [Vorgangsnamen](#operation-names) angegebenen Tipps. Wenn ein Ereignis als Fehler protokolliert wird, enthält das Protokoll auch weitere Informationen dazu, warum die Auswertung nicht erfolgreich war. Wenn der Vorgangsname also beispielsweise **EndpointUnhealthy** lautet, wird durch den [Fehlercode](#common-error-codes) 403004 angegeben, dass für den Endpunkt nicht mehr genügend Speicherplatz zur Verfügung stand.

#### <a name="the-health-of-the-endpoint"></a>Die Integrität des Endpunkts

Verwenden Sie die REST-API [Get Endpoint Health](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) (Endpunktintegrität abrufen), um den [Integritätsstatus](iot-hub-devguide-endpoints.md#custom-endpoints) der Endpunkte abzurufen. Die API *Get Endpoint Health* (Endpunktintegrität abrufen) liefert auch Informationen zum Zeitpunkt der letzten erfolgreichen Übermittlung einer Nachricht an den Endpunkt, zum [letzten bekannten Fehler](#last-known-errors-for-iot-hub-routing-endpoints), zum Zeitpunkt des letzten bekannten Fehlers und zum letzten Übermittlungsversuch für den Endpunkt. Verwenden Sie die mögliche Abhilfe, die für den spezifischen [letzten bekannten Fehler](#last-known-errors-for-iot-hub-routing-endpoints) angegeben ist.

### <a name="i-suddenly-stopped-getting-messages-at-the-built-in-endpoint"></a>Ich erhalte plötzlich keine Nachrichten mehr am integrierten Endpunkt.

Analysieren Sie Folgendes:

#### <a name="was-a-new-route-created"></a>Wurde eine neue Route erstellt?

Sobald eine Route erstellt wird, werden keine Daten mehr an den integrierten Endpunkt gesendet, es sei denn, eine Route zu diesem Endpunkt wird erstellt. Konfigurieren Sie eine Route zum Endpunkt *events* , um sicherzustellen, dass Nachrichten weiterhin an den integrierten Endpunkt weitergeleitet werden, wenn eine neue Route hinzugefügt wird. 

#### <a name="was-the-fallback-route-disabled"></a>Wurde die Fallbackroute deaktiviert?

Durch die Fallbackroute werden alle Nachrichten, die die Abfragebedingungen für die vorhandenen Routen nicht erfüllen, an den [integrierten Endpunkt](iot-hub-devguide-messages-read-builtin.md) (messages/events) gesendet, der mit [Event Hubs](../event-hubs/index.yml) kompatibel ist. Wenn das Nachrichtenrouting aktiviert ist, können Sie die Funktion der Fallbackroute verwenden. Wenn keine Routen zum integrierten Endpunkt vorhanden sind und eine Fallbackroute aktiviert ist, werden nur Nachrichten an den integrierten Endpunkt gesendet, die keinen Abfragebedingungen in Routen entsprechen. Wenn alle vorhandenen Routen gelöscht wurden, muss eine Fallbackroute aktiviert werden, um alle Daten im integrierten Endpunkt zu empfangen.

Sie können die Fallbackroute im Azure-Portal auf dem Blatt „Nachrichtenrouting“ aktivieren und deaktivieren. Sie können auch Azure Resource Manager verwenden, um [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) für die Nutzung eines benutzerdefinierten Endpunkts für die Fallbackroute festzulegen.

## <a name="last-known-errors-for-iot-hub-routing-endpoints"></a>Letzte bekannte Fehler für IoT Hub-Routingendpunkte

<a id="last-known-errors"></a>
[!INCLUDE [iot-hub-include-last-known-errors](../../includes/iot-hub-include-last-known-errors.md)]

## <a name="routes-resource-logs"></a>Ressourcenprotokolle zu Routen

Im Anschluss finden Sie die Vorgangsnamen und Fehlercodes, die in den [Ressourcenprotokolle zu Routen](monitor-iot-hub-reference.md#routes) protokolliert werden.

<a id="diagnostics-operation-names"></a>
### <a name="operation-names"></a>Vorgangsnamen

[!INCLUDE [iot-hub-diagnostics-operation-names](../../includes/iot-hub-diagnostics-operation-names.md)]

<a id="diagnostics-error-codes"></a>
### <a name="common-error-codes"></a>Häufige Fehlermeldungen

[!INCLUDE [iot-hub-diagnostics-error-codes](../../includes/iot-hub-diagnostics-error-codes.md)]

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) mit Azure-Experten in Verbindung setzen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie **Support erhalten** aus.