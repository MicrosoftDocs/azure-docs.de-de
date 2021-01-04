---
title: Übersicht über dedizierte Event Hubs – Azure Event Hubs | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Übersicht über dedizierte Azure Event Hubs, die Bereitstellungen von Event Hubs mit einzelnen Mandanten bieten.
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 1a15206fc35f0d536c7105aa73dfdcfc9967124d
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358863"
---
# <a name="overview-of-event-hubs-dedicated"></a>Übersicht über Event Hubs Dedicated

*Event Hubs-Cluster* bieten Bereitstellungen mit einem einzelnen Mandanten für Kunden mit äußerst anspruchsvollen Streaminganforderungen. Dieses Einzelmandantangebot verfügt über ein garantiertes 99,99 %-SLA und ist nur in unserem Dedicated-Tarif verfügbar. Ein Event Hubs-Cluster kann Millionen Ereignisse pro Sekunde mit garantierter Kapazität und Latenz im Sekundenbruchteilbereich erfassen. Namespaces und Event Hubs, die innerhalb des dedizierten Clusters erstellt werden, bieten alle Funktionen des Standardangebots und mehr, aber ohne jegliche Einschränkung bei eingehenden Daten. Er umfasst auch das verbreitete Feature [Event Hubs Capture](event-hubs-capture-overview.md) ohne zusätzliche Kosten. Mit diesem Feature können Sie Datenströme automatisch in Batches zusammenfassen und in Azure Storage oder Azure Data Lake protokollieren. 

Cluster werden in Form von **Kapazitätseinheiten (Capacity Units, CUs)** bereitgestellt und abgerechnet, einem vorab zugewiesenen Maß an CPU- und Arbeitsspeicherressourcen. Sie können für jeden Cluster 1, 2, 4, 8, 12, 16 oder 20 CUs erwerben. Wie viel Sie pro CU erfassen und streamen können, hängt von einer Vielzahl von Faktoren ab, z. B. den folgenden: 

- Anzahl von Producern und Consumern
- Form der Nutzlast
- Ausgangsrate

> [!NOTE]
> Alle Event Hubs-Cluster sind standardmäßig Kafka-aktiviert und unterstützen Kafka-Endpunkte, die von Ihren vorhandenen Kafka-basierten Anwendungen verwendet werden können. Wenn Kafka auf Ihrem Cluster aktiviert ist, hat dies keine Auswirkungen auf Ihre nicht Kafka-basierten Anwendungsfälle. Es gibt weder eine Option noch die Notwendigkeit, Kafka in einem Cluster zu deaktivieren.

## <a name="why-dedicated"></a>Warum dediziert?

Dedizierte Event Hubs bieten drei bestechende Vorteile für Kunden, die Kapazitäten auf Unternehmensniveau benötigen:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Ein Einzelmandant garantiert Kapazität für bessere Leistung

Ein dedizierter Cluster garantiert die Kapazität in vollem Umfang. Er kann mehrere Gigabyte an eingehenden Streamingdaten mit vollständig dauerhaftem Speicher und Latenzen von unter einer Sekunde verarbeiten, sodass Bursts beim Datenverkehr kein Problem mehr darstellen. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Inklusiver und exklusiver Zugriff auf Funktionen 
Das Dedicated-Angebot umfasst Funktionen wie Capture ohne zusätzliche Kosten sowie exklusiven Zugriff auf neue Features wie BYOK (Bring Your Own Key). Der Dienst verwaltet auch den Lastenausgleich, Betriebssystemupdates, Sicherheitspatches und die Partitionierung. Daher müssen Sie weniger Zeit für die Infrastrukturwartung aufwenden und haben mehr Zeit für das Entwickeln von clientseitigen Features.  

#### <a name="cost-savings"></a>Kostenersparnis
Bei hohen Eingangsvolumen (> 100 Durchsatzeinheiten) kostet ein Cluster bedeutend weniger pro Stunde als der Kauf einer vergleichbaren Menge an Durchsatzeinheiten im Standard-Angebot.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Event Hubs Dedicated: Kontingente und Grenzwerte

Das Event Hubs Dedicated-Angebot wird zu einem festen Monatspreis mit einem Minimum von vier Stunden Nutzung in Rechnung gestellt. Der Dedicated-Tarif umfasst alle Features des Standard-Tarifs, jedoch mit Kapazitäten und Grenzwerten auf Unternehmensniveau für Kunden mit anspruchsvollen Workloads. 

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

Weitere Informationen zu Kontingenten und Grenzwerten finden Sie unter [Kontingente und Grenzwerte in Event Hubs](event-hubs-quotas.md).

## <a name="how-to-onboard"></a>Onboardingmethoden

Die Self-Service-Funktion zum [Erstellen eines Event Hubs-Clusters](event-hubs-dedicated-cluster-create-portal.md) über das [Azure-Portal](https://aka.ms/eventhubsclusterquickstart) befindet sich jetzt in der Vorschauphase. Wenn Sie Fragen haben oder Hilfe beim Onboarding von Event Hubs Dedicated benötigen, wenden Sie sich an das [Event Hubs-Team](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Häufig gestellte Fragen

[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]

## <a name="next-steps"></a>Nächste Schritte

Wenden Sie sich an Ihren Microsoft-Vertriebsmitarbeiter oder den Microsoft-Support, um weitere Details zu Dedicated Event Hubs zu erhalten. Sie können auch einen Cluster erstellen oder weitere Informationen zu Event Hubs-Tarifen erhalten, indem Sie die folgenden Links besuchen:

- [Erstellen eines Event Hubs-Clusters über das Azure-Portal](https://aka.ms/eventhubsclusterquickstart) 
- [Event Hubs Preise](https://azure.microsoft.com/pricing/details/event-hubs/) Wenden Sie sich an Ihren Microsoft-Vertriebsmitarbeiter oder den Microsoft-Support, um weitere Details zur Dedicated Event Hubs-Kapazität zu erhalten.
- Unter [Event Hubs – häufig gestellte Fragen](event-hubs-faq.md) finden Sie Preisinformationen und Antworten auf einige häufig gestellte Fragen zu Event Hubs.
