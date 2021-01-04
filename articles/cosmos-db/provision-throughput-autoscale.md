---
title: Erstellen von Azure Cosmos-Containern und -Datenbanken im Autoskalierungsmodus
description: Erfahren Sie mehr über die Vorteile, die Anwendungsfälle und die Bereitstellung von Azure Cosmos-Datenbanken und -Containern im Autoskalierungsmodus.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2020
ms.custom: seo-nov-2020
ms.openlocfilehash: aaedca5acf7861db05f0ec724e05449316379829
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337541"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-throughput"></a>Erstellen von Azure Cosmos-Containern und -Datenbanken mit automatisch skaliertem Durchsatz
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Mit Azure Cosmos DB können Sie Standarddurchsatz (manuell) oder per Autoskalierung bereitgestellten Durchsatz für Ihre Datenbanken und Container konfigurieren. Bei per Autoskalierung bereitgestelltem Durchsatz ermöglicht Azure Cosmos DB das **Skalieren des Durchsatzes (RU/s) Ihrer Datenbank oder Ihres Containers automatisch und sofort**. Der Durchsatz wird basierend auf der Nutzung skaliert, ohne dass dadurch Verfügbarkeit, Latenz, Durchsatz oder Leistung der Workload beeinträchtigt werden.

Per Autoskalierung bereitgestellter Durchsatz eignet sich gut für unternehmenskritische Workloads, die über variable oder unvorhersehbare Datenverkehrsmuster verfügen und SLAs für hohe Leistung und Skalierung erfordern. In diesem Artikel werden die Vorteile des per Autoskalierung bereitgestellten Durchsatzes und Anwendungsfälle beschrieben.

## <a name="benefits-of-autoscale"></a>Vorteile der automatischen Skalierung

Azure Cosmos-Datenbanken und -Container, die mit per Autoskalierung bereitgestelltem Durchsatz konfiguriert sind, bieten die folgenden Vorteile:

* **Einfach:** Durch Autoskalierung ist die komplexe Verwaltung des Durchsatzes mit benutzerdefinierter Skripterstellung oder manueller Skalierung nicht mehr erforderlich. 

* **Skalierbar:** Mit Datenbanken und Containern wird der bereitgestellte Durchsatz nach Bedarf automatisch skaliert. Dabei werden weder Clientverbindungen oder Anwendungen unterbrochen noch SLAs für Azure Cosmos DB beeinträchtigt.

* **Kosteneffizient:** Mit Autoskalierung können Sie den Durchsatz und die Kosten optimieren, indem Sie den Durchsatz situationsabhängig herunterskalieren. Sie zahlen nur für die Ressourcen, die von Ihren Workloads pro Stunde benötigt werden. Wenn Sie für alle Stunden in einem Monat eine maximale Anzahl von RUs/Sek. (Tmax) für die Autoskalierung festlegen und die gesamte Menge von Tmax für maximal 66 % (oder weniger) der Stunden nutzen, können Sie mit der Autoskalierung sparen. Weitere Informationen finden Sie im Artikel [Auswählen zwischen standardmäßig (manuell) bereitgestelltem und automatisch skaliertem Durchsatz](how-to-choose-offer.md).

* **Hochverfügbarkeit:** Datenbanken und Container mit Autoskalierung verwenden dasselbe global verteilte, fehlertolerante, hochverfügbare Azure Cosmos DB-Back-End, um die Dauerhaftigkeit und ständige Hochverfügbarkeit von Daten sicherzustellen.

## <a name="use-cases-of-autoscale"></a>Anwendungsfälle der Autoskalierung

Anwendungsfälle des automatisch skalierten Durchsatzes:

* **Variable oder nicht vorhersehbare Workloads:** Wenn Ihre Workloads variable oder unvorhersehbare Spitzenlasten aufweisen, unterstützt die Autoskalierung das automatische zentrale Hoch- und Herunterskalieren basierend auf der Nutzung. Beispiele hierfür sind Einzelhandelswebsites mit unterschiedlichen, saisonabhängigen Datenverkehrsmustern, IoT-Arbeitsauslastungen mit Spitzen zu verschiedenen Tageszeiten, Branchenanwendungen, die einige Male im Monat oder Jahr Auslastungsspitzen aufweisen, usw. Mit Autoskalierung ist es nicht mehr erforderliche, manuelle Vorkehrungen für die Spitzen- oder Durchschnittskapazität zu treffen. 

* **Neue Anwendungen:** Wenn Sie eine neue Anwendung entwickeln und sich nicht sicher sind, welchen Durchsatz (RU/s) Sie benötigen, erleichtert Ihnen die Autoskalierung die ersten Schritte. Sie können mit einem Autoskalierungseinstiegspunkt von 400–4.000 RU/s beginnen, die Nutzung überwachen und im Laufe der Zeit den richtigen Durchsatz bestimmen.

* **Selten genutzte Anwendungen:** Wenn Sie über eine Anwendung verfügen, die nur mehrmals pro Tag, Woche oder Monat einige Stunden lang verwendet wird – z. B. eine Anwendungswebsite/allgemeine Website/Blogwebsite –, passt die Autoskalierung die Kapazität an, um die Spitzenauslastung zu bewältigen, und verringert die Kapazität, wenn die Auslastung gesunken ist. 

* **Entwicklungs- und Testworkloads:** Wenn Sie oder Ihr Team Azure Cosmos-Datenbanken und -Container während der Arbeitszeit verwenden, diese aber nicht in der Nacht oder an Wochenenden benötigen, können Sie durch die Autoskalierung Kosten sparen, indem Sie auf ein Minimum zentral herunterskalieren, wenn keine Nutzung erfolgt. 

* **Geplante Produktionsworkloads/-abfragen:** Wenn eine Reihe geplanter Anforderungen, Vorgänge oder Abfragen in Leerlaufzeiten ausgeführt werden soll, können Sie dies problemlos mit Autoskalierung durchführen. Wenn Sie die Arbeitsauslastung ausführen müssen, wird der Durchsatz automatisch auf die erforderliche Kapazität skaliert und anschließend herunterskaliert. 

Das Entwickeln einer benutzerdefinierten Lösung für diese Probleme erfordert nicht nur sehr viel Zeit, sondern macht auch die Konfiguration oder den Code Ihrer Anwendung kompliziert. Mit der Autoskalierung werden die oben beschriebenen Szenarien vorkonfiguriert bereitgestellt, und die Notwendigkeit einer benutzerdefinierten oder manuellen Skalierung der Kapazität entfällt. 

## <a name="how-autoscale-provisioned-throughput-works"></a>Funktionsweise von per Autoskalierung bereitgestelltem Durchsatz

Beim Konfigurieren von Containern und Datenbanken mit Autoskalierung müssen Sie den maximalen Durchsatz `Tmax` angeben. Azure Cosmos DB skaliert den Durchsatz `T`, sodass `0.1*Tmax <= T <= Tmax`. Wenn Sie z. B. den maximalen Durchsatz auf 20.000 RU/s festlegen, wird der Durchsatz auf einen Wert zwischen 2.000 und 20.000 RU/s skaliert. Da die Skalierung automatisch und sofort erfolgt, kann der Durchsatz zu jedem beliebigen Zeitpunkt und ohne Verzögerung bis zum festgelegten Wert `Tmax` betragen. 

Jede Stunde wird Ihnen der höchste Durchsatz `T` in Rechnung gestellt, auf den das System innerhalb dieser Stunde skaliert wurde.

Der Einstiegspunkt für den maximalen Durchsatz `Tmax` bei Autoskalierung beträgt 4.000 RU/s, sodass eine Skalierung zwischen 400 und 4.000 RU/s erfolgt. Sie können `Tmax` in Schritten von 1.000 RU/s festlegen und den Wert jederzeit ändern.  

## <a name="enable-autoscale-on-existing-resources"></a>Aktivieren der Autoskalierung für vorhandene Ressourcen

Verwenden Sie das [Azure-Portal](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container), um Autoskalierung für vorhandene Datenbanken oder Container zu aktivieren. Sie können jederzeit zwischen per Autoskalierung bereitgestelltem Durchsatz und Standarddurchsatz (manuell) wechseln. Weitere Informationen finden Sie in dieser [Dokumentation](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work). Derzeit kann für alle APIs nur das Azure-Portal verwendet werden, um die Autoskalierung für bereits vorhandene Ressourcen zu aktivieren.

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a> Durchsatz- und Speicherlimits für die Autoskalierung

Für einen beliebigen Wert `Tmax` kann die Datenbank oder der Container insgesamt `0.01 * Tmax GB` speichern. Nachdem diese Menge an Speicher erreicht wurde, wird der maximale Durchsatz automatisch basierend auf dem neuen Speicherwert erhöht, ohne dass sich dies auf Ihre Anwendung auswirkt. 

Wenn Sie z. B. mit dem Höchstwert von 50.000 RU/s beginnen (Skalierungen zwischen 5.000 und 50.000 RU/s), können Sie bis zu 500 GB an Daten speichern. Wenn Sie 500 GB überschreiten (z. B. 600 GB), gilt als neuer Höchstwert 60.000 RU/s (Skalierungen zwischen 6.000 und 60.000 RU/s).

Wenn Sie den Durchsatz auf Datenbankebene mit Autoskalierung verwenden, gilt für die ersten 25 Container ein maximaler automatisch skalierter Durchsatz von 4.000 RU/s (Skalierungen zwischen 400 und 4.000 RU/s), solange Sie nicht mehr als 40 GB Speicher benötigen. Weitere Informationen finden Sie in dieser [Dokumentation](autoscale-faq.md#can-i-change-the-max-rus-on-the-database-or-container).

## <a name="comparison--containers-configured-with-manual-vs-autoscale-throughput"></a>Vergleich – Container mit manuell skaliertem und automatisch skaliertem Durchsatz
Weitere Informationen finden Sie in diesem [Dokument](how-to-choose-offer.md), in dem erläutert wird, wie Sie zwischen Standarddurchsatz (manuell) und Durchsatz mit Autoskalierung wählen.  

|| Container mit Standarddurchsatz (manuell)  | Container mit automatisch skaliertem Durchsatz |
|---------|---------|---------|
| **Bereitgestellter Durchsatz (RU/s)** | Manuell bereitgestellt. | Automatische und sofortige Skalierung basierend auf den Workload-Verwendungsmustern. |
| **Ratenbegrenzung von Anforderungen/Vorgängen (429)**  | Kann passieren, wenn die Nutzung die bereitgestellte Kapazität überschreitet. | Dies geschieht nicht, wenn Sie Durchsatz innerhalb des von Ihnen festgelegten Durchsatzbereichs mit Autoskalierung nutzen.    |
| **Kapazitätsplanung** |  Sie müssen eine Kapazitätsplanung durchführen und den genauen benötigten Durchsatz bereitstellen. |    Das System übernimmt automatisch die Kapazitätsplanung und -verwaltung. |
| **Preise** | Sie zahlen für den manuell bereitgestellten Durchsatz pro Stunde nach dem [Tarif für Standard-RU/s (manuell) auf Stundenbasis](https://azure.microsoft.com/pricing/details/cosmos-db/). | Sie zahlen pro Stunde für den höchsten Durchsatz, auf den das System innerhalb der Stunde zentral hochskaliert wurde. <br/><br/> Bei Konten mit einer Schreibregion zahlen Sie für den genutzten Durchsatz auf Stundenbasis nach dem [Tarif für Autoskalierungs-RUs/s pro Stunde](https://azure.microsoft.com/pricing/details/cosmos-db/). <br/><br/>Bei Konten mit mehreren Schreibregionen fallen keine zusätzlichen Kosten für die Autoskalierung an. Sie zahlen für den genutzten Durchsatz auf Stundenbasis nach demselben [Tarif für Schreib-RU/s für mehrere Regionen pro Stunde](https://azure.microsoft.com/pricing/details/cosmos-db/). |
| **Für folgende Workloadtypen am besten geeignet** |  Vorhersehbare und stabile Workloads|   Nicht vorhersehbare und variable Workloads  |

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr in den [häufig gestellten Fragen zur Autoskalierung](autoscale-faq.md).
* Erfahren Sie, wie Sie sich [zwischen manuellem Durchsatz und automatisch skaliertem Durchsatz entscheiden](how-to-choose-offer.md).
* Erfahren Sie mehr über das [Bereitstellen von automatisch skaliertem Durchsatz für eine Datenbank oder einen Container in Azure Cosmos DB](how-to-provision-autoscale-throughput.md).
* Erfahren Sie mehr über die [Partitionierung](partitioning-overview.md) in Azure Cosmos DB.


