---
title: Übersicht über dedizierte Event Hubs – Azure Event Hubs | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Übersicht über dedizierte Azure Event Hubs, die Bereitstellungen von Event Hubs mit einzelnen Mandanten bieten.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 52e092e6e48f004656860cb5d078e780039584ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730240"
---
# <a name="overview-of-event-hubs-dedicated"></a>Übersicht über Event Hubs Dedicated

*Event Hubs-Cluster* bieten Bereitstellungen mit einem einzelnen Mandanten für Kunden mit äußerst anspruchsvollen Streaminganforderungen. Dieses Einzelmandantangebot verfügt über ein garantiertes 99,99 %-SLA und ist nur in unserem Dedicated-Tarif verfügbar. Ein Event Hubs-Cluster kann Millionen eingehender Ereignisse pro Sekunde mit garantierter Kapazität und Latenz im Sekundenbruchteilbereich erfassen. Namespaces und Event Hubs, die innerhalb des dedizierten Clusters erstellt werden, bieten alle Funktionen des Standardangebots und mehr, aber ohne jegliche Einschränkung bei eingehenden Daten. Enthalten ist ferner die beliebte [Event Hubs Capture](event-hubs-capture-overview.md)-Funktion ohne zusätzliche Kosten, die es Ihnen ermöglicht, Datenströme zu Batches zusammenzufassen und in Azure Storage oder Azure Data Lake zu protokollieren. 

Cluster werden in Form von **Kapazitätseinheiten (Capacity Units, CUs)** bereitgestellt und abgerechnet, einem vorab zugewiesenen Maß an CPU- und Arbeitsspeicherressourcen. Sie können für jeden Cluster 1, 2, 4, 8, 12, 16 oder 20 CUs erwerben. Wie viele eingehende Daten Sie pro CU erfassen und streamen können, hängt von einer Vielzahl verschiedener Faktoren ab, wie z. B. der Anzahl von Producern und Consumern, Form der Nutzlast, Ausgangsrate (Weitere Informationen finden Sie weiter unten in den Benchmarkergebnissen). 

> [!NOTE]
> Alle Event Hubs-Cluster sind standardmäßig Kafka-aktiviert und unterstützen Kafka-Endpunkte, die von Ihren vorhandenen Kafka-basierten Anwendungen verwendet werden können. Wenn Kafka auf Ihrem Cluster aktiviert ist, hat dies keine Auswirkungen auf Ihre nicht Kafka-basierten Anwendungsfälle. Es gibt weder eine Option noch die Notwendigkeit, Kafka in einem Cluster zu deaktivieren.

## <a name="why-dedicated"></a>Warum dediziert?

Dedizierte Event Hubs bieten drei bestechende Vorteile für Kunden, die Kapazitäten auf Unternehmensniveau benötigen:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Ein Einzelmandant garantiert Kapazität für bessere Leistung

Ein dedizierter Cluster garantiert Kapazität in vollem Umfang und kann im Rahmen von bis zu Gigabytes eingehender Streamingdaten mit vollständig dauerhaftem Speicher und Wartezeiten von unter einer Sekunde erfassen, um jegliche Spitze im Datenverkehr zu verarbeiten. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Inklusiver und exklusiver Zugriff auf Funktionen 
Das dedizierte Angebot umfasst Funktionen wie Capture ohne zusätzliche Kosten sowie exklusiven Zugriff auf bevorstehende Funktionen wie BYOK (Bring Your Own Key). Der Dienst verwaltet auch den Lastenausgleich, BS-Updates, Sicherheitspatches und Partitionierung für den Kunden, damit Sie weniger Zeit auf die Wartung der Infrastruktur verwenden müssen und mehr Zeit zum Erstellen von clientseitigen Features haben.  

#### <a name="cost-savings"></a>Kostenersparnis
Bei hohen Eingangsvolumen (> 100 TUs) kostet ein Cluster bedeutend weniger pro Stunde als der Kauf einer vergleichbaren Menge von Durchsatzeinheiten (TUs) im Standard-Tarifangebot.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Kontingente und Limits von Event Hubs Dedicated

Das Event Hubs Dedicated-Angebot wird zu einem festen Monatspreis mit einem Minimum von vier Stunden Nutzung in Rechnung gestellt. Der Dedicated-Tarif umfasst die Funktionen des Standard-Tarifs, jedoch mit Kapazitäten und Limits auf Unternehmensniveau für Kunden mit anspruchsvollen Workloads. 

| Feature | Standard | Dediziert |
| --- |:---:|:---:|
| Bandbreite | 20 TUs (bis zu 40 TUs) | 20 CUs |
| Namespaces |  1 | 50 pro CU |
| Event Hubs |  10 pro Namespace | 1000 pro Namespace |
| Eingangsereignisse | Bezahlung pro Million Ereignisse | Enthalten |
| Nachrichtengröße | 1 Million Bytes | 1 Million Bytes |
| Partitionen | 40 pro Namespace | 2000 pro CU |
| Verbrauchergruppen | 20 pro Event Hub | Kein Limit pro CU, 1000 pro Event Hub |
| Brokerverbindungen | 1\.000 enthalten, max. 5.000 | 100\.000 enthalten, gleichzeitig Max. |
| Nachrichtenaufbewahrung | 7 Tage, 84 GB enthalten pro TU | 90 Tage, 10 TB enthalten pro CU |
| Erfassen | Bezahlung pro Stunde | Enthalten |

## <a name="how-to-onboard"></a>Onboardingmethoden

Die Self-Service-Erfahrung zum [Erstellen eines Event Hubs-Clusters](event-hubs-dedicated-cluster-create-portal.md) über das [Azure-Portal](https://aka.ms/eventhubsclusterquickstart) befindet sich jetzt in der Vorschauphase. Wenden Sie Fragen haben oder Hilfe beim Onboarding von Event Hubs Dedicated benötigen, wenden Sie sich bitte an das [Event Hubs-Team](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Häufig gestellte Fragen

#### <a name="what-can-i-achieve-with-a-cluster"></a>Was kann ich mit einem Cluster erreichen?

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

#### <a name="can-i-scale-updown-my-cluster"></a>Kann ich meinen Cluster zentral hoch-/herunterskalieren?

Nach ihrer Erstellung werden bei Clustern mindestens vier Stunden Nutzung in Rechnung gestellt. In der Vorschauversion der Self-Service-Erfahrung können Sie eine [Supportanfrage](https://ms.portal.azure.com/#create/Microsoft.Support) an das Event Hubs-Team übermitteln unter *Technical (Technisch) > Quota (Kontingent) > Request to Scale Up or Scale Down Dedicated Cluster (Anforderung zum zentralen Hoch- oder Herunterskalieren eines Dedicated-Clusters)* , um Ihren Cluster zentral hoch- oder herunterzuskalieren. Es kann bis zu 7 Tage dauern, bis die Anfrage zum zentralen Herunterskalieren abgeschlossen wird. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Wie wird die Geo-Notfallwiederherstellung mit meinem Cluster funktionieren?

Sie können einen Namespace unter einem Cluster des Dedicated-Tarifs geografisch mit einem anderen Namespace unter einem Cluster des Dedicated-Tarifs koppeln. Wir empfehlen nicht, einen Namespace eines Dedicated-Tarifs mit einem Namespace in unserem Standardangebot zu koppeln, da das Durchsatzlimit inkompatibel ist und somit zu Fehlern führen wird. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Kann ich meine Standard-Namespaces migrieren, damit sie zu einem Cluster des Dedicated-Tarifs gehören?
Zurzeit unterstützen wird keinen automatisierten Migrationsprozess für die Migration Ihrer Event Hubs-Daten von einem Standard-Namespace zu einem im Dedicated-Tarif. 

## <a name="next-steps"></a>Nächste Schritte

Wenden Sie sich an Ihren Microsoft-Vertriebsmitarbeiter oder den Microsoft-Support, um weitere Details zu Dedicated Event Hubs zu erhalten. Sie können auch einen Cluster erstellen oder weitere Informationen zu Event Hubs-Tarifen erhalten, indem Sie die folgenden Links besuchen:

- [Erstellen eines Event Hubs-Clusters über das Azure-Portal](https://aka.ms/eventhubsclusterquickstart) 
- [Event Hubs Preise](https://azure.microsoft.com/pricing/details/event-hubs/) Wenden Sie sich an Ihren Microsoft-Vertriebsmitarbeiter oder den Microsoft-Support, um weitere Details zur Dedicated Event Hubs-Kapazität zu erhalten.
- Unter [Event Hubs – häufig gestellte Fragen](event-hubs-faq.md) finden Sie Preisinformationen und Antworten auf einige häufig gestellte Fragen zu Event Hubs.
