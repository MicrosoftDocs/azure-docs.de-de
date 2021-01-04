---
title: include file
description: include file
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/23/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0335481566ae3f28ac0f1e6bddce7050a65e7dc2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516992"
---
### <a name="what-can-i-achieve-with-a-cluster"></a>Was kann ich mit einem Cluster erreichen?

Welche Datenmengen Sie für einen Event Hubs-Cluster erfassen und streamen können, hängt von verschiedenen Faktoren ab, z.B. von den Producern, den Consumern, der Rate für die Erfassung und Verarbeitung und vielem mehr. 

In der folgenden Tabelle sind die Ergebnisse aufgeführt, die bei unseren Vergleichstests erreicht wurden:

| Form der Nutzlast | Empfänger | Eingangsbandbreite| Eingangsnachrichten | Ausgangsbandbreite | Ausgangsnachrichten | TUs gesamt | TUs pro CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Batches von 100 x 1 KB | 2 | 400 MB/s | 400T Nachrichten/Sek. | 800 MB/s | 800T Nachrichten/Sek. | 400 TUs | 100 TUs | 
| Batches von 10 x 10 KB | 2 | 666 MB/s | 66,6T Nachrichten/Sek. | 1,33 GB/s | 133T Nachrichten/Sek. | 666 TUs | 166 TUs |
| Batches von 6 x 32 KB | 1 | 1,05 GB/s | 34T Nachrichten/Sek. | 1,05 GB/s | 34T Nachrichten/Sek. | 1\.000 TUs | 250 TUs |

Bei den Tests wurden folgende Kriterien verwendet:

- Es wurde ein Event Hubs-Cluster des Dedicated-Tarif mit vier Kapazitätseinheiten (Capacity Units, CUs) verwendet. 
- Der für die Erfassung verwendete Event Hub umfasste 200 Partitionen. 
- Die erfassten und von allen Partitionen eingehenden Daten wurden von zwei Empfängeranwendungen empfangen.

### <a name="can-i-scale-updown-my-cluster"></a>Kann ich meinen Cluster zentral hoch-/herunterskalieren?

Nach ihrer Erstellung werden bei Clustern mindestens vier Stunden Nutzung in Rechnung gestellt. In der Vorschauversion der Self-Service-Umgebung können Sie eine [Supportanfrage](https://ms.portal.azure.com/#create/Microsoft.Support) an das Event Hubs-Team übermitteln unter **Technisch** > **Kontingent** > **Anforderung zum Hoch- oder Herunterskalieren eines dedizierten Clusters** , um Ihren Cluster hoch- oder herunterzuskalieren. Es kann bis zu 7 Tage dauern, bis die Anfrage zum zentralen Herunterskalieren abgeschlossen wird. 

### <a name="how-does-geo-dr-work-with-my-cluster"></a>Wie funktioniert die Geo-Notfallwiederherstellung bei meinem Cluster?

Sie können einen Namespace unter einem Cluster des Dedicated-Tarifs geografisch mit einem anderen Namespace unter einem Cluster des Dedicated-Tarifs koppeln. Wir empfehlen nicht, einen Namespace im Tarif „Dediziert“ mit einem Namespace in unserem Standardtarif zu koppeln, da das Durchsatzlimit inkompatibel ist und zu Fehlern führt. 

### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Kann ich meine Standard-Namespaces migrieren, damit sie zu einem Cluster des Dedicated-Tarifs gehören?
Zurzeit unterstützen wird keinen automatisierten Migrationsprozess für die Migration Ihrer Event Hubs-Daten von einem Standard-Namespace zu einem im Tarif „Dediziert“. 
