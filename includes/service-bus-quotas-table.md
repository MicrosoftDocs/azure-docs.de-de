---
title: include file
description: include file
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 07/15/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 764d75872eb1bad0647235cd70f9984ebd7789bd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026914"
---
In der folgenden Tabelle sind die Kontingentinformationen für das Azure Service Bus Messaging aufgeführt. Informationen zu Preisen und anderen Kontingenten für Service Bus finden Sie unter [Service Bus – Preise](https://azure.microsoft.com/pricing/details/service-bus/).

| Namen des Kontingents | `Scope` | Notizen | Wert |
| --- | --- | --- | --- |
| Maximale Anzahl von Basic- oder Standard-Namespaces pro Azure-Abonnement |Namespace |Weitere Anforderungen für zusätzliche Basic- oder Standard-Namespaces werden vom Azure-Portal abgelehnt. |100|
| Maximale Anzahl von Premium-Namespaces pro Azure-Abonnement |Namespace |Weitere Anforderungen für zusätzliche Premium-Namespaces werden vom Portal abgelehnt. |100 |
| Größe von Warteschlangen oder Themen |Entität |Bei der Erstellung der Warteschlange oder des Themas definiert. <br/><br/> Nachfolgende eingehende Nachrichten werden abgelehnt, und vom aufrufenden Code wird eine Ausnahme empfangen. |1, 2, 3, 4 oder 5 GB.<br /><br />In der Premium-SKU und in der Standard-SKU mit aktivierter [Partitionierung](../articles/service-bus-messaging/service-bus-partitioning.md) beträgt die maximale Größe von Warteschlangen bzw. Themen 80 GB. |
| Maximale Anzahl gleichzeitiger Verbindungen in einem Namespace |Namespace |Nachfolgende Anforderungen für zusätzliche Verbindungen werden abgelehnt, und der aufrufende Code empfängt eine Ausnahme. REST-Vorgänge werden nicht zu den gleichzeitigen TCP-Verbindungen hinzugezählt. |.NET-Messaging: 1.000.<br /><br />AMQP: 5.000 |
| Anzahl der gleichzeitigen Empfangsanforderungen für eine Warteschlangen-, Themen- oder Abonnemententität |Entität |Nachfolgende Empfangsanforderungen werden abgelehnt, und vom aufrufenden Code wird eine Ausnahme empfangen. Dieses Kontingent gilt für alle gleichzeitigen Empfangsvorgänge über alle Abonnements eines Themas hinweg. |5\.000 |
| Anzahl von Themen oder Warteschlangen pro Namespace |Namespace |Nachfolgende Anforderungen zum Erstellen eines neuen Themas bzw. einer neuen Warteschlange im Namespace werden abgelehnt. Als Ergebnis wird bei der Konfiguration über das [Azure-Portal][Azure portal] eine Fehlermeldung generiert. Bei einem Aufruf über die Verwaltungs-API wird vom aufrufenden Code eine Ausnahme empfangen. |10.000 für den Basic- oder Standard-Tarif. Die Gesamtzahl von Themen und Warteschlangen in einem Namespace muss kleiner oder gleich 10.000 sein. <br/><br/>Im Premium-Tarif ist ein Wert von 1.000 pro Messagingeinheit (Messaging Unit, MU) zulässig. Der maximale Wert beträgt 4.000. |
| Anzahl von [partitionierten Themen oder Warteschlangen](../articles/service-bus-messaging/service-bus-partitioning.md) pro Namespace |Namespace |Nachfolgende Anforderungen zum Erstellen eines neuen partitionierten Themas bzw. einer neuen Warteschlange im Namespace werden abgelehnt. Als Ergebnis wird bei der Konfiguration über das [Azure-Portal][Azure portal] eine Fehlermeldung generiert. Bei einem Aufruf über die Verwaltungs-API wird vom aufrufenden Code die Ausnahme **QuotaExceededException** empfangen. |Basic- und Standard-Tarif: 100.<br/><br/>Partitionierte Entitäten werden im [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md)-Tarif nicht unterstützt.<br/><br />Jede partitionierte Warteschlange bzw. jedes partitionierte Thema wird in das zulässige Kontingent von 1.000 Entitäten pro Namespace eingerechnet. |
| Maximale Größe eines Messagingentitätspfads: Warteschlange oder Thema |Entität |- |260 Zeichen |
| Maximale Größe eines Messagingentitätsnamens: Namespace, Abonnement oder Abonnementregel |Entität |- |50 Zeichen |
| Maximale Größe der [message ID](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Entität |- | 128 |
| Maximale Größe der [Sitzungs-ID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) einer Nachricht | Entität |- | 128 |
| Nachrichtengröße für eine Warteschlangen-, Themen- oder Abonnemententität |Entität |Eingehende Nachrichten, die diese Kontingente überschreiten, werden abgelehnt, und vom aufrufenden Code wird eine Ausnahme empfangen. |Maximale Nachrichtengröße: 256 KB für [Standard-Tarif](../articles/service-bus-messaging/service-bus-premium-messaging.md), 1 MB für [Premium-Tarif](../articles/service-bus-messaging/service-bus-premium-messaging.md) <br /><br />Aufgrund des Mehraufwands für das System ist dieser Grenzwert kleiner als diese Werte.<br /><br />Maximale Headergröße: 64 KB<br /><br />Maximale Anzahl der Headereigenschaften im Eigenschaftenbehälter: **byte/int.MaxValue**<br /><br />Maximale Größe der Eigenschaft im Eigenschaftenbehälter: Keine ausdrückliche Beschränkung. Beschränkung nach maximaler Headergröße. |
| Nachrichteneigenschaftsgröße für eine Warteschlangen-, Themen- oder Abonnemententität |Entität | Die Ausnahme `SerializationException` wird generiert. |Die maximale Nachrichteneigenschaftsgröße für jede Eigenschaft beträgt 32.000. Die kumulative Größe aller Eigenschaften darf 64.000 nicht überschreiten. Dieser Grenzwert gilt für den gesamten Header des [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)-Elements, das sowohl über Benutzereigenschaften als auch über Systemeigenschaften verfügt (z. B. [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label) und [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid)). |
| Anzahl von Abonnements pro Thema |Entität |Nachfolgende Anforderungen zum Erstellen weiterer Abonnements für das Thema werden abgelehnt. Als Ergebnis wird bei der Konfiguration über das Portal eine Fehlermeldung angezeigt. Bei einem Aufruf über die Verwaltungs-API wird vom aufrufenden Code eine Ausnahme empfangen. |2\.000 pro Thema für die Ebenen „Standard“ und „Premium“ |
| Anzahl von SQL-Filtern pro Thema |Entität |Nachfolgende Anforderungen für die Erstellung weiterer Filter für das Thema werden abgelehnt, und der aufrufende Code empfängt eine Ausnahme. |2\.000 |
| Anzahl von Korrelationsfiltern pro Thema |Entität |Nachfolgende Anforderungen für die Erstellung weiterer Filter für das Thema werden abgelehnt, und der aufrufende Code empfängt eine Ausnahme. |100.000 |
| Größe der SQL-Filter oder -Aktionen |Namespace |Nachfolgende Anforderungen für die Erstellung weiterer Filter werden abgelehnt, und der aufrufende Code empfängt eine Ausnahme. |Maximale Länge der Filterbedingungszeichenfolge: 1.024 (1 K)<br /><br />Maximale Länge der Regelaktionszeichenfolge: 1.024 (1 K)<br /><br />Maximale Anzahl von Ausdrücken pro Regelaktion: 32. |
| Anzahl von [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)-Regeln pro Namespace, Warteschlange oder Thema |Entität, Namespace |Nachfolgende Anforderungen für die Erstellung weiterer Regeln werden abgelehnt, und der aufrufende Code empfängt eine Ausnahme. |Maximale Anzahl von Regeln pro Entitätstyp: 12. <br /><br /> Regeln, die für einen Service Bus-Namespace konfiguriert werden, gelten für alle Typen: Warteschlangen, Themen. |
| Anzahl der Nachrichten pro Transaktion | Transaktion | Zusätzliche eingehende Nachrichten werden abgelehnt, und eine Ausnahme („Mehr als 100 Nachrichten dürfen nicht in einer einzelnen Transaktion gesendet werden“) wird durch den aufrufenden Code empfangen. | 100 <br /><br /> Für **Send()** - und **SendAsync()** -Vorgänge. |
| Anzahl von Regeln für virtuelle Netzwerke und IP-Filter | Namespace | &nbsp; | 128 | 

[Azure portal]: https://portal.azure.com