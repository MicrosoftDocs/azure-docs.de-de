---
title: include file
description: include file
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: fa6b4d6d0db09f8c4955430d6dc227356416d915
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735995"
---
In der folgenden Tabelle sind die Kontingente und Grenzwerte aufgelistet, die für [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) gelten. Informationen zu den Preisen von Event Hubs finden Sie unter [Event Hubs – Preise](https://azure.microsoft.com/pricing/details/event-hubs/).

| Begrenzung | `Scope` | Notizen | Wert |
| --- | --- | --- | --- |
| Anzahl von Event Hubs-Namespaces pro Abonnement |Abonnement |- |100 |
| Anzahl von Event Hubs pro Namespace |Namespace |Nachfolgende Anforderungen für die Erstellung eines neuen Event Hub werden zurückgewiesen. |10 |
| Anzahl von Partitionen pro Event Hub |Entität |- |32 |
| Anzahl von Consumergruppen pro Event Hub |Entität |- |20 |
| Anzahl von AMQP-Verbindungen pro Namespace |Namespace |Nachfolgende Anforderungen für zusätzliche Verbindungen werden abgelehnt, und der aufrufende Code empfängt eine Ausnahme. |5\.000 |
| Maximale Größe des Event Hubs-Ereignisses|Entität |- |1 MB |
| Maximale Größe eines Event Hub-Namens |Entität |- |50 Zeichen |
| Anzahl nicht epochenbezogener Empfänger pro Consumergruppe |Entität |- |5 |
| Maximale Aufbewahrungsdauer von Ereignisdaten |Entität |- |1–7 Tage |
| Maximale Durchsatzeinheiten |Namespace |Bei einer Überschreitung des Grenzwerts für Durchsatzeinheiten werden Ihre Daten gedrosselt, und es wird eine [ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) ausgelöst. Um eine höhere Anzahl von Durchsatzeinheiten für den Tarif „Standard“ anzufordern, erstellen Sie eine [Supportanfrage](/azure/azure-supportability/how-to-create-azure-support-request). [Zusätzliche Durchsatzeinheiten](../articles/event-hubs/event-hubs-auto-inflate.md) sind für einen festgelegten Kaufpreis in 20er-Blöcken verfügbar. |20 |
| Anzahl von Autorisierungsregeln pro Namespace |Namespace|Nachfolgende Anforderungen zur Erstellung von Autorisierungsregeln werden abgelehnt.|12 |
| Die Anzahl der Aufrufe der GetRuntimeInformation-Methode | Entität | - | 50 pro Sekunde | 

### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs Dedicated – Kontingente und Limits
Das Event Hubs Dedicated-Angebot wird zu einem festen Monatspreis mit einem Minimum von vier Stunden Nutzung in Rechnung gestellt. Der Dedicated-Tarif umfasst die Funktionen des Standard-Tarifs, jedoch mit Kapazitäten und Limits auf Unternehmensniveau für Kunden mit anspruchsvollen Workloads. 

| Feature | Einschränkungen |
| --- | ---|
| Bandbreite |  20 CUs |
| Namespaces | 50 pro CU |
| Event Hubs |  1000 pro Namespace |
| Eingangsereignisse | Enthalten |
| Nachrichtengröße | 1 Million Bytes |
| Partitionen | 2000 pro CU |
| Verbrauchergruppen | Kein Limit pro CU, 1000 pro Event Hub |
| Brokerverbindungen | 100\.000 enthalten |
| Nachrichtenaufbewahrung | Bis zu 7 Tage (Aufbewahrungszeitraum von 90 Tagen in Kürze verfügbar), 10 TB enthalten pro CU |
| Erfassen | Enthalten |
