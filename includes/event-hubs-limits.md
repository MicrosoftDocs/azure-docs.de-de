---
title: include file
description: include file
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 02/01/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 45cc5120072f2a8c7742f79cfb79161042345505
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99427274"
---
In der folgenden Tabelle finden Sie die Kontingente und Grenzwerte, die für [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) gelten. Informationen zu den Preisen von Event Hubs finden Sie unter [Event Hubs – Preise](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="common-limits-for-all-tiers"></a>Allgemeine Grenzwerte für alle Ebenen
Die folgenden Grenzwerte sind gängig für alle Ebenen: 

| Begrenzung |  Notizen | Wert |
| --- |  --- | --- |
| Anzahl von Event Hubs-Namespaces pro Abonnement |- |100 |
| Anzahl von Event Hubs pro Namespace | Nachfolgende Anforderungen für die Erstellung eines neuen Event Hub werden zurückgewiesen. |10 |
| Anzahl von Partitionen pro Event Hub |- |32 |
| Größe eines Event Hub-Namens |- | 256 Zeichen |
| Größe eines Consumergruppennamens |- | 256 Zeichen |
| Anzahl nicht epochenbezogener Empfänger pro Consumergruppe |- |5 |
| Anzahl von Autorisierungsregeln pro Namespace | Nachfolgende Anforderungen zur Erstellung von Autorisierungsregeln werden abgelehnt.|12 |
| Die Anzahl der Aufrufe der GetRuntimeInformation-Methode |  - | 50 pro Sekunde | 
| Anzahl von virtuellen Netzwerken (VNETs) | - | 128 | 
| Anzahl von IP-Konfigurationsregeln | - | 128 | 


### <a name="basic-vs-standard-tiers"></a>Basic- und Standard-Tarif
Die folgende Tabelle enthält die Grenzwerte, die sich für die Ebenen „Basic“ und „Standard“ unter Umständen unterscheiden: 

| Begrenzung | Notizen | Basic | Standard |
|---|---|--|---|
| Maximale Größe des Event Hubs-Ereignisses| &nbsp; | 256 KB | 1 MB |
| Anzahl von Consumergruppen pro Event Hub | &nbsp; |1 |20 |
| Anzahl von AMQP-Verbindungen pro Namespace | Nachfolgende Anforderungen für zusätzliche Verbindungen werden abgelehnt, und der aufrufende Code empfängt eine Ausnahme. |100 |5\.000|
| Maximale Aufbewahrungsdauer von Ereignisdaten | &nbsp; |1 Tag |1–7 Tage |
| Maximale Durchsatzeinheiten |Bei einer Überschreitung dieses Grenzwerts werden Ihre Daten gedrosselt, und es wird die [Ausnahme „Server ausgelastet“](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) ausgelöst. Um eine höhere Anzahl von Durchsatzeinheiten für den Tarif „Standard“ anzufordern, erstellen Sie eine [Supportanfrage](../articles/azure-portal/supportability/how-to-create-azure-support-request.md). [Zusätzliche Durchsatzeinheiten](../articles/event-hubs/event-hubs-auto-inflate.md) sind für einen festgelegten Kaufpreis in 20er-Blöcken verfügbar. |20 | 20 | 

### <a name="dedicated-tier-vs-standard-tier"></a>Dedicated- und Standard-Tarif
Das Event Hubs Dedicated-Angebot wird zu einem festen Monatspreis mit einem Minimum von vier Stunden Nutzung in Rechnung gestellt. Der Dedicated-Tarif umfasst die Funktionen des Standard-Tarifs, jedoch mit Kapazitäten und Limits auf Unternehmensniveau für Kunden mit anspruchsvollen Workloads. 

Informationen zum Erstellen eines dedizierten Event Hubs-Clusters mithilfe des Azure-Portals finden Sie in [diesem Dokument](../articles/event-hubs/event-hubs-dedicated-cluster-create-portal.md).

| Funktion | Standard | Dediziert |
| --- |:---|:---|
| Bandbreite | 20 TUs (bis zu 40 TUs) | 20 CUs |
| Namespaces |  1 | 50 pro CU |
| Event Hubs |  10 pro Namespace | 1000 pro Namespace |
| Eingangsereignisse | Bezahlung pro Million Ereignisse | Enthalten |
| Nachrichtengröße | 1 Million Bytes | 1 Million Bytes |
| Partitionen | 32 pro Event Hub | 1\.024 pro Event Hub<br/>2000 pro CU |
| Verbrauchergruppen | 20 pro Event Hub | Kein Limit pro CU, 1000 pro Event Hub |
| Brokerverbindungen | 1\.000 enthalten, max. 5.000 | 100.000 enthalten, gleichzeitig Max. |
| Nachrichtenaufbewahrung | 7 Tage, 84 GB enthalten pro TU | 90 Tage, 10 TB enthalten pro CU |
| Erfassung | Bezahlung pro Stunde | Enthalten |


### <a name="schema-registry-limitations"></a>Einschränkungen der Schemaregistrierung

#### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>Grenzwerte, die für die Ebenen Standard und Dedicated identisch sind 
| Funktion | Begrenzung | 
|---|---|
| Maximale Länge eines Schemagruppennamens | 50 |  
| Maximale Länge eines Schemanamens | 100 |    
| Größe in Bytes pro Schema | 1 MB |   
| Anzahl von Eigenschaften pro Schemagruppe | 1024 |
| Größe in Bytes pro Gruppeneigenschaftenschlüssel | 256 | 
| Größe in Bytes pro Gruppeneigenschaftswert | 1024 | 


#### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>Grenzwerte, die sich für die Ebenen Standard und Dedicated unterscheiden 

| Begrenzung | Standard | Dediziert | 
|---|---|--|
| Größe der Schemaregistrierung (Namespace) in Megabytes | 25 |  1024 |
| Anzahl von Schemagruppen in einer Schemaregistrierung oder einem Namespace | 1 (ohne Standardgruppe) | 1000 |
| Anzahl von Schemaversionen in allen Schemagruppen | 25 | 10000 |