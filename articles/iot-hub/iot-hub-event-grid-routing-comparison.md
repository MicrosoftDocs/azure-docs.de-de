---
title: Vergleich von Event Grid und Weiterleitung für IoT Hub | Microsoft-Dokumentation
description: IoT Hub bietet einen eigenen Nachrichtenweiterleitungsdienst, ist aber auch für die Ereignisveröffentlichung mit Event Grid integriert. Hier werden die beiden Features verglichen.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.custom: fasttrack-edit
ms.openlocfilehash: 3f0853261e770b2cba9a243ae66b0b0d766fcd92
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024686"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Vergleichen von Nachrichtenweiterleitung und Event Grid für IoT Hub

Azure IoT Hub stellt die Möglichkeit bereit, Daten von Ihren verbundenen Geräten zu streamen und diese Daten in Ihre Geschäftsanwendungen zu integrieren. IoT Hub bietet zwei Methoden zum Integrieren von IoT-Ereignissen in andere Azure-Dienste oder Geschäftsanwendungen. Dieser Artikel beschreibt die beiden Features, die diese Funktion bereitstellen, damit Sie entscheiden können, welche Option für Ihr Szenario am besten geeignet ist.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[IoT Hub-Nachrichtenweiterleitung:](iot-hub-devguide-messages-d2c.md)** Dieses IoT Hub-Feature ermöglicht es Benutzern, Gerät-zu-Cloud-Nachrichten an Dienstendpunkte wie Azure Storage-Container, Event Hubs, Service Bus-Warteschlangen oder Service Bus-Themen weiterzuleiten. Die Weiterleitung verfügt auch über eine Abfragefunktion zum Filtern der Daten, bevor diese an die Endpunkte weitergeleitet werden. Zusätzlich zu Gerätetelemetriedaten können Sie auch [Ereignisse ohne Telemetriedaten](iot-hub-devguide-messages-d2c.md#non-telemetry-events) senden, die zum Auslösen von Aktionen verwendet werden können. 

**IoT Hub-Integration mit Event Grid:** Azure Event Grid ist ein vollständig verwalteter Ereignisroutingdienst, der ein Veröffentlichen-Abonnieren-Modell verwendet. IoT Hub und Event Grid [integrieren gemeinsam IoT Hub-Ereignisse in Azure- und Nicht-Azure-Dienste](iot-hub-event-grid.md) praktisch in Echtzeit. IoT Hub veröffentlicht sowohl [Geräteereignisse](iot-hub-event-grid.md#event-types) als auch Telemetrieereignisse.

## <a name="differences"></a>Unterschiede

Sowohl bei der Nachrichtenweiterleitung als auch bei Even Grid ist eine Warnungskonfiguration möglich, es gibt jedoch einige wichtige Unterschiede zwischen den beiden Methoden. Details finden Sie in der folgenden Tabelle:

| Funktion | IoT Hub-Nachrichtenweiterleitung | IoT Hub-Integration mit Event Grid |
| ------- | --------------- | ---------- |
| **Gerätenachrichten und -ereignisse** | Ja, die Nachrichtenweiterleitung kann verwendet werden für Telemetriedaten sowie zum Melden von Änderungen am Gerätezwilling, Ereignissen im Gerätelebenszyklus (z. B. wenn Geräte erstellt, gelöscht, mit IoT Hub verbunden oder von IoT Hub getrennt werden) und Änderungsereignissen beim digitalen Zwilling. | Ja, Event Grid kann für Telemetriedaten und Ereignisse im Gerätelebenszyklus verwendet werden. Event Grid kann aber nicht für Änderungsereignisse bei Gerätezwillingen und Änderungsereignisse bei digitalen Zwillingen verwendet werden. |
| **Sortieren** | Ja, die Reihenfolge von Ereignissen wird beibehalten.  | Nein, die Reihenfolge von Ereignissen ist nicht garantiert. | 
| **Filterung** | Umfassendes Filtern von Nachrichtenanwendungseigenschaften, Nachrichtensystemeigenschaften, Nachrichtentext, Gerätezwillingstags und Gerätezwillingseigenschaften. Filterung wird auf Änderungsereignisse beim digitalen Zwilling nicht angewendet. Beispiele finden Sie im Artikel zur [Abfragesyntax für die Nachrichtenweiterleitung](iot-hub-devguide-routing-query-syntax.md). | Filtern basierend auf Ereignistyp, Thementyp und Attributen in den einzelnen Ereignissen. Beispiele finden Sie unter [Grundlegendes zur Ereignisfilterung für Event Grid-Abonnements](../event-grid/event-filtering.md). Beim Abonnieren von Telemetrieereignissen können Sie zusätzliche Filter auf die Daten anwenden, um vor der Veröffentlichung in Event Grid nach Nachrichteneigenschaften, Nachrichtentext und Gerätezwillingen in Ihrem IoT-Hub zu filtern. Informationen hierzu finden Sie unter [Filtern von Ereignissen](../iot-hub/iot-hub-event-grid.md#filter-events). |
| **Endpunkte** | <ul><li>Event Hubs</li> <li>Azure Blob Storage</li> <li>Service Bus-Warteschlange</li> <li>Service Bus-Themen</li></ul><br>Kostenpflichtige IoT Hub-SKUs (S1, S2 und S3) sind auf 10 benutzerdefinierte Endpunkte beschränkt. Pro IoT Hub können 100 Routen erstellt werden. | <ul><li>Azure-Funktionen</li> <li>Azure-Automatisierung</li> <li>Event Hubs</li> <li>Logic Apps</li> <li>Speicherblob</li> <li>Benutzerdefinierte Themen</li> <li>Queue Storage</li> <li>Power Automate</li> <li>Drittanbieterdienste über Webhooks</li></ul><br>Pro IoT-Hub werden 500 Endpunkte unterstützt. Die aktuelle Liste der Endpunkte finden Sie unter [Event Grid-Ereignishandler](../event-grid/overview.md#event-handlers). |
| **Kosten** | Für die Nachrichtenweiterleitung fallen keine separaten Gebühren an. Nur der Eingang von Telemetriedaten in IoT Hub wird in Rechnung gestellt. Wenn beispielsweise eine Nachricht an drei verschiedene Endpunkte weitergeleitet wird, wird Ihnen nur eine Nachricht berechnet. | Es fallen keine Gebühren von IoT Hub an. Event Grid bietet die ersten 100.000 Vorgänge pro Monat kostenlos, danach 0,60 US-Dollar pro Million Vorgänge. |

## <a name="similarities"></a>Ähnlichkeiten

IoT Hub-Nachrichtenweiterleitung und Event Grid weisen auch Ähnlichkeiten auf, von denen einige in der folgenden Tabelle beschrieben werden:

| Komponente | IoT Hub-Nachrichtenweiterleitung | IoT Hub-Integration mit Event Grid |
| ------- | --------------- | ---------- |
| **Maximale Nachrichtengröße** | 256 KB, Gerät-zu-Cloud | 256 KB, Gerät-zu-Cloud |
| **Zuverlässigkeit** | Hoch: Jede Nachricht wird für jede Route mindestens einmal an den Endpunkt übermittelt. Alle Nachrichten, die nicht innerhalb einer Stunde übermittelt werden, laufen ab. | Hoch: Jede Nachricht wird für jedes Abonnement mindestens einmal an den Webhook übermittelt. Alle Ereignisse, die nicht innerhalb von 24 Stunden übermittelt werden, laufen ab. | 
| **Skalierbarkeit** | Hoch: für die Unterstützung von Millionen gleichzeitig verbundener Geräte, die Milliarden Nachrichten senden, optimiert. | Hoch: kann 10.000.000 Ereignisse pro Sekunde pro Region weiterleiten. |
| **Latenz** | Niedrig: nahezu in Echtzeit. | Niedrig: nahezu in Echtzeit. |
| **Senden an mehrere Endpunkte** | Ja, Senden einer einzelnen Nachricht an mehrere Endpunkte. | Ja, Senden einer einzelnen Nachricht an mehrere Endpunkte.  
| **Security** | IoT Hub ermöglicht gerätespezifische Identitäten und widerrufbare Zugriffssteuerung. Weitere Informationen finden Sie unter [IoT Hub-Zugriffssteuerung](iot-hub-devguide-security.md). | Event Grid bietet eine Überprüfung an drei Punkten: Ereignisabonnements, Ereignisveröffentlichung und Webhook-Ereignisübermittlung. Weitere Informationen finden Sie unter [Event Grid – Sicherheit und Authentifizierung](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Auswahl

Die IoT Hub-Nachrichtenweiterleitung und die IoT Hub-Integration mit Event Grid erzielen über unterschiedliche Aktionen ähnliche Ergebnisse. Beide übernehmen Informationen aus Ihrer IoT Hub-Lösung und geben sie weiter, damit andere Dienste reagieren können. Wie entscheiden Sie also, welche Methode Sie verwenden? Erwägen Sie die folgenden Fragen, die Sie bei der Entscheidungsfindung unterstützen: 

* **Welche Art von Daten senden Sie an die Endpunkte?**

   Verwenden Sie die IoT Hub-Nachrichtenweiterleitung, wenn Sie Telemetriedaten an andere Dienste senden müssen. Die Nachrichtenweiterleitung ermöglicht auch das Abfragen von Nachrichtenanwendungs- und Systemeigenschaften, des Nachrichtentexts sowie von Gerätezwillingstags und -eigenschaften.

   Die IoT Hub-Integration mit Event Grid arbeitet mit Ereignissen, die im IoT Hub-Dienst auftreten. Zu diesen IoT Hub-Ereignissen gehören Telemetriedaten und Daten zu erstellten, gelöschten, verbundenen und getrennten Geräten. Beim Abonnieren von Telemetrieereignissen können Sie zusätzliche Filter auf die Daten anwenden, um vor der Veröffentlichung in Event Grid nach Nachrichteneigenschaften, Nachrichtentext und Gerätezwillingen in Ihrem IoT-Hub zu filtern. Informationen hierzu finden Sie unter [Filtern von Ereignissen](../iot-hub/iot-hub-event-grid.md#filter-events).

* **Welche Endpunkte müssen diese Informationen erhalten?**

   Die IoT Hub-Nachrichtenweiterleitung unterstützt eine eingeschränkte Anzahl eindeutiger Endpunkte und Endpunkttypen. Sie können jedoch Connectors erstellen, um die Daten und Ereignisse an zusätzliche Endpunkte umzuleiten. Eine vollständige Liste der unterstützten Endpunkte finden Sie in der Tabelle im vorherigen Abschnitt. 

   Die IoT Hub-Integration in Event Grid unterstützt 500 Endpunkte pro IoT-Hub und eine größere Vielzahl von Endpunkttypen. Sie ist nativ in Azure Functions-, Logic Apps-, Storage- und Service Bus-Warteschlangen integriert und funktioniert auch mit Webhooks, um das Senden von Daten auf Dienste außerhalb des Ökosystems von Azure-Diensten sowie auf Geschäftsanwendungen von Drittanbietern zu erweitern.

* **Ist es wichtig, ob Ihre Daten in der richtigen Reihenfolge empfangen werden?**

   Die IoT Hub-Nachrichtenweiterleitung behält die Reihenfolge, in der Nachrichten gesendet werden, bei, sodass sie bei der Ankunft unverändert bleibt.

   Event Grid garantiert nicht, dass Endpunkte die Ereignisse in der gleichen Reihenfolge empfangen, in der sie aufgetreten sind. In Fällen, in denen die absolute Reihenfolge der Nachrichten von Bedeutung ist und/oder ein Kunde einen vertrauenswürdigen eindeutigen Bezeichner für Nachrichten benötigt, empfehlen wir die Nachrichtenweiterleitung. 

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die [IoT Hub-Nachrichtenweiterleitung](iot-hub-devguide-messages-d2c.md) und [IoT Hub-Endpunkte](iot-hub-devguide-endpoints.md).
* Informieren Sie sich ausführlicher über [Azure Event Grid](../event-grid/overview.md).
* Im Tutorial [Verarbeiten von IoT Hub-D2C-Nachrichten mit Routen](../iot-hub/tutorial-routing.md) erfahren Sie, wie Sie Nachrichtenrouten erstellen.
* Probieren Sie die Event Grid-Integration aus, indem Sie [E-Mail-Benachrichtigungen zu Azure IoT Hub-Ereignissen mit Logic Apps senden](../event-grid/publish-iot-hub-events-to-logic-apps.md).
