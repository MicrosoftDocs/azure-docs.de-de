---
title: Anzeigen von Metriken mit Azure Monitor
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Azure Digital Twins-Metriken in Azure Monitor angezeigt werden.
author: baanders
ms.author: baanders
ms.date: 8/4/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4b72bb8bac8f9949c83d0bbc85a0995f790c437d
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347896"
---
# <a name="troubleshooting-azure-digital-twins-metrics"></a>Problembehandlung von Azure Digital Twins: Metriken

Die in diesem Artikel beschriebenen Metriken liefern Informationen zum Zustand der Azure Digital Twins-Ressourcen in Ihrem Azure-Abonnement. Azure Digital Twins-Metriken helfen Ihnen, die allgemeine Integrität des Azure Digital Twins-Diensts und der damit verbundenen Ressourcen zu bewerten. Anhand dieser benutzerorientierten Statistiken können Sie Vorgänge in Azure Digital Twins erkennen und die Ursache von Problemen analysieren, ohne sich an den Azure-Support wenden zu müssen.

Metriken sind standardmäßig aktiviert. Azure Digital Twins-Metriken können über das [Azure-Portal](https://portal.azure.com) angezeigt werden.

## <a name="how-to-view-azure-digital-twins-metrics"></a>Anzeigen von Azure Digital Twins-Metriken

1. Erstellen Sie eine Azure Digital Twins-Instanz. Anweisungen zum Einrichten einer Azure Digital Twins-Instanz finden Sie unter [*Vorgehensweise: Einrichten einer Instanz und der Authentifizierung*](how-to-set-up-instance-portal.md).

2. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach Ihrer Azure Digital Twins-Instanz (Sie können die Seite für die Instanz öffnen, indem Sie ihren Namen in die Suchleiste des Portals eingeben). 

    Wählen Sie im Menü der Instanz **Metriken** aus.
   
    :::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Screenshot der Seite „Metriken“ für Azure Digital Twins":::

    Auf dieser Seite werden die Metriken für Ihre Azure Digital Twins-Instanz angezeigt. Sie können auch benutzerdefinierte Ansichten Ihrer Metriken erstellen, indem Sie die gewünschten Metriken in der Liste auswählen.
    
3. Sie können Ihre Metrikdaten an einen Event Hubs-Endpunkt oder ein Azure Storage-Konto senden, indem Sie im Menü **Diagnoseeinstellungen** und dann **Diagnoseeinstellung hinzufügen** auswählen.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Screenshot: Seite „Diagnoseeinstellungen“und Schaltfläche zum Hinzufügen":::

    Weitere Informationen zu diesem Vorgang finden Sie unter [*Problembehandlung: Einrichten der Diagnose*](troubleshoot-diagnostics.md).

4. Sie können Warnungen für Ihre Metrikdaten einrichten, indem Sie im Menü **Warnungen** und dann **+ Neue Warnungsregel** auswählen.
    :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="Screenshot mit der Seite „Warnungen“ und der Schaltfläche zum Hinzufügen":::

    Weitere Informationen zu diesem Vorgang finden Sie unter [*Problembehandlung: Einrichten von Warnungen*](troubleshoot-alerts.md).

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Azure Digital Twins-Metriken und deren Verwendung

Azure Digital Twins bietet mehrere Metriken, um Ihnen einen Überblick über die Integrität Ihrer Instanz und der zugehörigen Ressourcen zu ermöglichen. Sie können auch Informationen aus mehreren Metriken kombinieren, um sich ein umfassenderes Bild des Zustands Ihrer Instanz zu machen. 

In den folgenden Tabellen werden die Metriken beschrieben, die von jeder Azure Digital Twins-Instanz nachverfolgt werden, und es wird erläutert, wie sich jede Metrik auf den Gesamtstatus Ihrer Instanz bezieht.

#### <a name="metrics-for-tracking-service-limits"></a>Metriken zum Überwachen von Diensteinschränkungen

Sie können diese Metriken so konfigurieren, dass überwacht wird, wann Sie sich einem [veröffentlichten Dienstlimit](reference-service-limits.md#functional-limits) für einen bestimmten Aspekt Ihrer Lösung nähern. 

Verwenden Sie das Feature [Warnungen](troubleshoot-alerts.md) in Azure Monitor, um dies einzurichten. Sie können Schwellenwerte für diese Metriken definieren, sodass Sie eine Warnung erhalten, wenn eine Metrik einen bestimmten Prozentsatz dieses veröffentlichten Limits erreicht.

| Metrik | Anzeigename der Metrik | Einheit | Aggregationstyp| BESCHREIBUNG | Dimensionen |
| --- | --- | --- | --- | --- | --- |
| TwinCount | Twin Count (Vorschau) | Anzahl | Gesamt | Hierbei handelt es sich um die Gesamtanzahl von Zwillingen in einer Azure Digital Twins-Instanz. Verwenden Sie diese Metrik, um zu bestimmen, ob Sie sich dem [Dienstlimit](reference-service-limits.md#functional-limits) für die maximal pro Instanz zulässige Anzahl an Zwillingen nähern. |  Keine |
| ModelCount | Model Count (Vorschau) | Anzahl | Gesamt | Hierbei handelt es sich um die Gesamtanzahl von Modellen in einer Azure Digital Twins-Instanz. Verwenden Sie diese Metrik, um zu bestimmen, ob Sie sich dem [Dienstlimit](reference-service-limits.md#functional-limits) für die maximal pro Instanz zulässige Anzahl an Modellen nähern. | Keine |

#### <a name="api-request-metrics"></a>API-Anforderungsmetriken

Metriken im Zusammenhang mit API-Anforderungen:

| Metrik | Anzeigename der Metrik | Einheit | Aggregationstyp| BESCHREIBUNG | Dimensionen |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | API-Anforderungen | Anzahl | Gesamt | Die Anzahl der API-Anforderungen, die für Lese-, Schreib-, Lösch- und Abfragevorgänge für Digital Twins durchgeführt wurden. |  Authentifizierung, <br>Betrieb, <br>Protokoll, <br>Statuscode, <br>Statuscodeklasse, <br>Statustext |
| ApiRequestsFailureRate | API-Anforderungsfehlerrate | Percent | Average | Der Prozentsatz der API-Anforderungen, die der Dienst für Ihre Instanz erhält und die einen internen Fehler (500) als Antwortcode für Lese-, Schreib-, Lösch- und Abfragevorgänge von Digital Twins angeben. | Authentifizierung, <br>Betrieb, <br>Protokoll, <br>Statuscode, <br>Statuscodeklasse, <br>Statustext
| ApiRequestsLatency | API-Anforderungslatenz | Millisekunden | Average | Die Antwortzeit für API-Anforderungen. Dies bezieht sich auf den Zeitraum zwischen dem Eingang der Anforderung bei Azure Digital Twins und dem Zeitpunkt, zu dem der Dienst ein Erfolgs- bzw. Fehlerergebnis für Lese-, Schreib-, Lösch- und Abfragevorgänge von Digital Twins sendet. | Authentifizierung, <br>Betrieb, <br>Protocol |

#### <a name="billing-metrics"></a>Abrechnungsmetriken

Metriken im Zusammenhang mit Abrechnung:

| Metrik | Anzeigename der Metrik | Einheit | Aggregationstyp| BESCHREIBUNG | Dimensionen |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | API-Abrechnungsvorgänge | Anzahl | Gesamt | Abrechnungsmetrik für die Anzahl aller API-Anforderungen, die für den Azure Digital Twins-Dienst durchgeführt wurden. | ID der Verbrauchseinheit |
| BillingMessagesProcessed | Verarbeitete Abrechnungsnachrichten | Anzahl | Gesamt | Abrechnungsmetrik für die Anzahl von Nachrichten, die von Azure Digital Twins Zwillingen an externe Endpunkte gesendet werden.<br><br>Nutzdaten dürfen maximal 1 KB groß sein, da sie ansonsten nicht als einzelne Nachricht für die Abrechnung betrachtet wird. Umfangreichere Nutzdaten werden als zusätzliche Nachrichten in Schritten von 1 KB gezählt. (Eine Nachricht zwischen 1 und 2 KB wird also als zwei Nachrichten gezählt, eine Nachricht zwischen 2 und 3 KB als drei Nachrichten usw.)<br>Diese Einschränkung gilt auch für Antworten: Ein Aufruf, der 1,5 KB im Antworttext zurückgibt, wird beispielsweise als zwei Vorgänge in Rechnung gestellt. | ID der Verbrauchseinheit |
| BillingQueryUnits | Abrechnungsabfrageeinheiten | Anzahl | Gesamt | Die Anzahl der Abfrageeinheiten (ein intern berechnetes Measure der Dienstressourcennutzung), die zum Ausführen von Abfragen genutzt werden. Es ist auch eine Hilfs-API zum Messen von Abfrageeinheiten verfügbar: [QueryChargeHelper-Klasse](/dotnet/api/azure.digitaltwins.core.querychargehelper?preserve-view=true&view=azure-dotnet-preview) | ID der Verbrauchseinheit |

Weitere Informationen zur Art und Weise, wie Azure Digital Twins abgerechnet wird, finden Sie unter [*Azure Digital Twins-Preise*](https://azure.microsoft.com/pricing/details/digital-twins/).

#### <a name="ingress-metrics"></a>Eingangsmetriken

Metriken im Zusammenhang mit Dateneingang:

| Metrik | Anzeigename der Metrik | Einheit | Aggregationstyp| BESCHREIBUNG | Dimensionen |
| --- | --- | --- | --- | --- | --- |
| IngressEvents | Eingangsereignisse | Anzahl | Gesamt | Die Anzahl der in Azure Digital Twins eingehenden Telemetrieereignisse. | Ergebnis |
| IngressEventsFailureRate | Eingangsereignis-Fehlerrate | Percent | Average | Der Prozentsatz der eingehenden Telemetrieereignisse, bei denen der Dienst einen internen Fehlerantwortcode (500) zurückgibt. | Ergebnis |
| IngressEventsLatency | Latenz von Eingangsereignissen | Millisekunden | Average | Die Zeit zwischen dem Eintreffen eines Ereignisses und dem Zeitpunkt, zu dem es von Azure Digital Twins ausgegeben werden kann, wobei der Dienst ein Erfolgs-/Fehlerergebnis sendet. | Ergebnis |

#### <a name="routing-metrics"></a>Routingmetriken

Metriken im Zusammenhang mit Routing:

| Metrik | Anzeigename der Metrik | Einheit | Aggregationstyp| BESCHREIBUNG | Dimensionen |
| --- | --- | --- | --- | --- | --- |
| MessagesRouted | Weitergeleitete Nachrichten | Anzahl | Gesamt | Die Anzahl der Nachrichten, die an einen Azure-Endpunktdienst (z. B. Event Hub, Service Bus oder Event Grid) weitergeleitet werden. | Endpunkttyp, <br>Ergebnis |
| RoutingFailureRate | Routingfehlerrate | Percent | Average | Der Prozentsatz der Ereignisse, die zu einem Fehler führen, wenn sie von Azure Digital Twins zu einem Azure-Endpunktdienst wie Event Hub, Service Bus oder Event Grid weitergeleitet werden. | Endpunkttyp, <br>Ergebnis |
| RoutingLatency | Routinglatenz | Millisekunden | Average | Die Zeit, die zwischen der Weiterleitung eines Ereignisses von Azure Digital Twins bis zu dem Zeitpunkt verstrichen ist, an dem es an den Azure-Endpunktdienst wie Event Hub, Service Bus oder Event Grid gesendet wird. | Endpunkttyp, <br>Ergebnis |

## <a name="dimensions"></a>Dimensionen

Mit Dimensionen lassen sich genauere Details zu den Metriken ermitteln. Einige Routingmetriken bieten Informationen zu den einzelnen Endpunkten. In der folgenden Tabelle sind die möglichen Werte für diese Dimensionen aufgeführt.

| Dimension | Werte |
| --- | --- |
| Authentifizierung | OAuth |
| Vorgang (für API-Anforderungen) | Microsoft.DigitalTwins/digitaltwins/delete, <br>Microsoft.DigitalTwins/digitaltwins/write, <br>Microsoft.DigitalTwins/digitaltwins/read, <br>Microsoft.DigitalTwins/eventroutes/read, <br>Microsoft.DigitalTwins/eventroutes/write, <br>Microsoft.DigitalTwins/eventroutes/delete, <br>Microsoft.DigitalTwins/models/read, <br>Microsoft.DigitalTwins/models/write, <br>Microsoft.DigitalTwins/models/delete, <br>Microsoft.DigitalTwins/query/action |
| Endpunkttyp | Event Grid, <br>Event Hub, <br>Service Bus |
| Protocol | HTTPS |
| Ergebnis | Erfolg, <br>Fehler |
| Statuscode | 200, 404, 500 usw. |
| Statuscodeklasse | 2xx, 4xx, 5xx usw. |
| Statustext | Interner Serverfehler, Nicht gefunden, usw. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Verwalten von aufgezeichneten Metriken für Azure Digital Twins finden Sie unter [*Problembehandlung: Einrichten der Diagnose*](troubleshoot-diagnostics.md).
