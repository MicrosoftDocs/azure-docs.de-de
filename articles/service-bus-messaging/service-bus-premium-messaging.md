---
title: Übersicht über Azure Service Bus Premium- und Standard-Tarife für Messaging | Microsoft-Dokumentation
description: Service Bus Premium- und Standard-Tarif für Messaging
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: aschhab
ms.openlocfilehash: 600577ebf05a8bc89dbec35d3b3ee5162aa246e1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64872724"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Service Bus Premium- und Standard-Preisstufe für Messaging

Service Bus-Messaging, das Entitäten wie Warteschlangen und Themen umfasst, kombiniert Messagingfunktionen für Unternehmen mit umfangreicher Veröffentlichen/Abonnieren-Semantik auf Cloudebene. Service Bus-Messaging wird als Kommunikationsbackbone für zahlreiche komplexe Cloudlösungen verwendet.

Der *Premium*-Tarif von Service Bus-Messaging ist für allgemeine Kundenanfragen zur Skalierung, Leistung und Verfügbarkeit für unternehmenswichtige Anwendungen vorgesehen. Für Produktionsszenarien wird der Premium-Tarif empfohlen. Obwohl die Funktionssätze fast identisch sind, sind diese zwei Stufen von Service Bus-Messaging für verschiedene Anwendungsfälle vorgesehen.

In der folgenden Tabelle sind einige allgemeine Unterschiede hervorgehoben:

| Premium | Standard |
| --- | --- |
| Hoher Durchsatz |Variabler Durchsatz |
| Vorhersagbare Leistung |Variable Latenzzeit |
| Feste Preise |Variable Preisgestaltung (nutzungsbasierte Bezahlung) |
| Möglichkeit zur Herauf- und Herunterskalierung der Workload |– |
| Nachrichtengröße bis 1 MB |Nachrichtengröße bis 256 KB |

**Service Bus Premium-Messaging** bietet Ressourcenisolierung auf CPU- und Arbeitsspeicherebene, sodass die Workloads der einzelnen Kunden isoliert ausgeführt werden. Dieser Ressourcencontainer wird als *Messaging-Einheit* bezeichnet. Jedem Premium-Namespace wird mindestens eine Messaging-Einheit zugeordnet. Sie können 1, 2, 4 oder 8 Messagingeinheiten für jeden Service Bus Premium-Namespace erwerben. Eine einzelne Workload oder Entität kann mehrere Messagingeinheiten umfassen, und die Anzahl der Einheiten kann beliebig geändert werden. Das Ergebnis ist eine vorhersehbare und wiederholbare Leistung Ihrer Service Bus-basierten Lösung.

Diese Leistung ist nicht nur besser vorhersehbar und verfügbar, sondern auch schneller. Service Bus Premium-Messaging basiert auf der in [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) eingeführten Speicher-Engine. Mit Premium-Messaging wird bei Spitzenleistung eine viel höhere Geschwindigkeit als beim Standard-Tarif erzielt.

## <a name="premium-messaging-technical-differences"></a>Premium-Messaging – technische Unterschiede

In den folgenden Abschnitten werden einige Unterschiede zwischen der Premium- und der Standard-Messagingstufe erläutert.

### <a name="partitioned-queues-and-topics"></a>Partitionierte Warteschlangen und Themen

Partitionierte Warteschlangen und Themen werden bei Premium-Messaging nicht unterstützt. Weitere Informationen zur Partitionierung finden Sie unter [Partitionierte Warteschlangen und Themen](service-bus-partitioning.md).

### <a name="express-entities"></a>Expressentitäten

Da Premium-Messaging in einer vollständig isolierten Laufzeitumgebung ausgeführt wird, werden Expressentitäten in Premium-Namespaces nicht unterstützt. Weitere Informationen zur Expressfunktion finden Sie in der [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress)-Eigenschaft.

Wenn Sie unter Standard-Messaging über Code verfügen und diesen auf den Premium-Tarif portieren möchten, stellen Sie sicher, dass die Eigenschaft [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) auf **false** (Standardwert) gesetzt ist.

## <a name="premium-messaging-resource-usage"></a>Premium-Messaging-Ressourcennutzung
Grundsätzlich kann jeder Vorgang für eine Entität CPU- und Arbeitsspeichernutzung verursachen. Nachstehend sind einige dieser Vorgänge aufgeführt: 

- Verwaltungsvorgänge, z. B. Erstell-, Abruf-, Aktualisier- und Löschvorgänge, für Warteschlangen, Themen und Abonnements
- Laufzeitvorgänge (Nachrichten senden und empfangen)
- Überwachen von Vorgängen und Warnungen

Die zusätzliche CPU- und Speichernutzung wird jedoch nicht zusätzlich berechnet. Im Premium-Messaging-Tarif gibt es einen einzigen Preis für die Nachrichteneinheit.

Die CPU- und die Arbeitsspeichernutzung werden aus folgenden Gründen nachverfolgt und für Sie angezeigt: 

- Bereitstellen von transparenter Einsicht in interne Systemabläufe
- Verstehen der Kapazität der erworbenen Ressourcen
- Kapazitätsplanung, damit Sie über zentrales Hoch-/Herunterskalieren entscheiden können

## <a name="get-started-with-premium-messaging"></a>Erste Schritte mit Premium-Messaging

Die ersten Schritte mit Premium-Messaging sind einfach, und der Prozess ähnelt der Vorgehensweise für Standard-Messaging. Beginnen Sie durch [Erstellung eines Namespace](service-bus-create-namespace-portal.md) im [Azure-Portal](https://portal.azure.com). Stellen Sie sicher, dass Sie unter **Tarif** die Option **Premium** wählen. Klicken Sie auf **Alle Preisinformationen anzeigen**, um weitere Informationen zu jedem Tarif anzuzeigen.

![create-premium-namespace][create-premium-namespace]

Sie können auch [Premium-Namespaces mit Azure Resource Manager-Vorlagen erstellen](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Service Bus-Messaging finden Sie unter folgenden Links:

* [Introducing Azure Service Bus Premium Messaging (Blogbeitrag)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/) (Einführung in Azure Service Bus Premium-Messaging)
* [Introducing Azure Service Bus Premium Messaging (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging) (Einführung in Azure Service Bus Premium-Messaging)
* [Service Bus Messaging: Flexible Datenübermittlung in der Cloud](service-bus-messaging-overview.md)
* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
