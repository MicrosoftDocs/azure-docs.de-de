---
title: Sicherstellen von Geschäftskontinuität und Notfallwiederherstellung mit Azure-Regionspaaren
description: Sicherstellen von Anwendungsresilienz mit Azure-Regionspaaren
author: martinekuan
manager: martinekuan
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: martinek
ms.custom: references_regions
ms.openlocfilehash: 3310d4a7d86db9dee7d5f71fc9410545817886f3
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511228"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Business Continuity & Disaster Recovery (BCDR): Azure-Regionspaare

## <a name="what-are-paired-regions"></a>Was sind Regionspaare?

Eine Azure-Region besteht aus einer Reihe von Rechenzentren, die innerhalb eines durch Latenz definierten Umkreises bereitgestellt werden und über ein dediziertes Netzwerk mit geringer Latenz verbunden sind.  Dadurch wird sichergestellt, dass Azure-Dienste innerhalb einer Azure-Region bestmögliche Leistung und Sicherheit bieten.  

Ein Azure-Gebiet definiert einen Bereich der Erde, der mindestens eine Azure-Region enthält. Geografische Regionen definieren einen diskreten Markt, der in der Regel mindestens zwei Regionen enthält, die Datenresidenz- und Konformitätsgrenzen beibehalten.  Weitere Informationen zur globalen Infrastruktur von Azure finden Sie [hier](https://azure.microsoft.com/global-infrastructure/regions/).

Ein Regionspaar besteht aus zwei Regionen innerhalb desselben geografischen Gebiets. In Azure werden Plattformupdates (geplante Wartung) für Regionspaare serialisiert, um sicherzustellen, dass immer nur jeweils eine Region pro Paar aktualisiert wird. Bei einem Ausfall, der sich auf mehrere Regionen auswirkt, wird mindestens eine Region in jedem Paar für die Wiederherstellung priorisiert.

![Azure-Gebiet](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Einige Azure-Dienste nutzen die Regionspaare weiter, um Geschäftskontinuität zu gewährleisten und vor Datenverlusten zu schützen.  Azure bietet mehrere [Speicherlösungen](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region), die die Vorteile von Regionspaaren nutzen, um Datenverfügbarkeit zu gewährleisten. Beispielsweise repliziert [georedundanter Azure-Speicher](./storage/common/storage-redundancy.md#geo-redundant-storage) (GRS) Daten automatisch in eine sekundäre Region und stellt so sicher, dass die Daten auch dann dauerhaft sind, wenn die primäre Region nicht wiederherstellbar ist. 

Beachten Sie, dass nicht alle Azure-Dienste automatisch Daten replizieren und auch nicht alle Azure-Dienste automatisch ein Fallback aus einer ausgefallenen Region zu ihrem Regionspaar durchführen.  In solchen Fällen müssen Wiederherstellung und Replikation vom Kunden konfiguriert werden.

## <a name="can-i-select-my-regional-pairs"></a>Kann ich meine Regionspaare auswählen?

Nein. Einige Azure-Dienste stützen sich auf Regionspaare, z. B. der [redundante Speicher](./storage/common/storage-redundancy.md) von Azure. Diese Dienste ermöglichen Ihnen nicht, neue Regionspaare zu erstellen.  Da Azure die geplante Priorisierung bei der Wartung und Wiederherstellung für Regionspaare steuert, können Sie auch nicht Ihre eigenen Regionspaare definieren, um die Vorteile dieser Dienste zu nutzen. Sie können jedoch eine eigene Lösung für die Notfallwiederherstellung erstellen, indem Sie Dienste in einer beliebigen Anzahl von Regionen erstellen und Azure-Dienste nutzen, um Paare zu bilden. 

Beispielsweise können Sie Azure-Dienste wie [AzCopy](./storage/common/storage-use-azcopy-v10.md) verwenden, um Datensicherungen in einem Speicherkonto in einer anderen Region zu planen.  Mit [Azure DNS und Azure Traffic Manager](./networking/disaster-recovery-dns-traffic-manager.md) können Kunden eine robuste Architektur für ihre Anwendungen entwerfen, die den Ausfall der primären Region übersteht.

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>Bin ich auf die Verwendung von Diensten innerhalb meiner Regionspaare beschränkt?

Nein. Während sich ein bestimmter Azure-Dienst auf ein Regionspaar stützen kann, können Sie Ihre anderen Dienste in jeder Region hosten, die Ihre Geschäftsanforderungen erfüllt.  Eine Azure GRS-Speicherlösung kann Daten in der Region „Kanada, Mitte“ mit einem Peer in der Region „Kanada, Osten“ koppeln, während Computeressourcen in der Region „USA, Osten“ verwendet werden.  

## <a name="must-i-use-azure-regional-pairs"></a>Muss ich Azure-Regionspaare verwenden?

Nein. Kunden können Azure-Dienste nutzen, um einen robusten Dienst zu entwerfen, ohne Azure-Regionspaare zu verwenden.  Wir empfehlen jedoch das Konfigurieren von Business Continuity Disaster Recovery (BCDR) zwischen Regionspaaren, um von [Isolierung](./security/fundamentals/isolation-choices.md) zu profitieren und die und [Verfügbarkeit](./availability-zones/az-overview.md) zu verbessern. Bei Anwendungen, die mehrere aktive Regionen unterstützen, empfehlen wir nach Möglichkeit die Verwendung beider Regionen in einem Regionspaar. Dadurch wird eine optimale Verfügbarkeit für Anwendungen sowie eine minimierte Wiederherstellungszeit in einem Notfall sichergestellt. Entwerfen Sie Ihre Anwendung nach Möglichkeit für [maximale Resilienz](/azure/architecture/framework/resiliency/overview) und einfache [Wiederherstellung im Notfall](/azure/architecture/framework/resiliency/backup-and-recovery).

## <a name="azure-regional-pairs"></a>Azure-Regionspaare

| Gebiet | Regionspaar A | Regionspaar B  |
|:--- |:--- |:--- |
| Asien-Pazifik |Asien, Osten (Hongkong) | Asien, Südosten (Singapur) |
| Australien |Australien (Osten) |Australien, Südosten |
| Australien |Australien, Mitte |Australien, Mitte 2 |
| Brasilien |Brasilien Süd |USA Süd Mitte |
| Canada |Kanada, Mitte |Kanada, Osten |
| China |China, Norden |China, Osten|
| China |China, Norden 2 |China, Osten 2|
| Europa |Europa, Norden (Irland) |Europa, Westen (Niederlande) |
| Frankreich |Frankreich, Mitte|Frankreich, Süden|
| Deutschland |Deutschland, Mitte |Deutschland, Nordosten |
| Indien |Indien, Mitte |Indien (Süden) |
| Indien |Indien, Westen |Indien (Süden) |
| Japan |Japan, Osten |Japan, Westen |
| Korea |Korea, Mitte |Korea, Süden |
| Nordamerika |East US |USA (Westen) |
| Nordamerika |USA (Ost) 2 |USA (Mitte) |
| Nordamerika |USA Nord Mitte |USA Süd Mitte |
| Nordamerika |USA, Westen 2 |USA, Westen-Mitte |
| Norwegen | Norwegen, Osten | Norwegen, Westen |
| Südafrika | Südafrika, Norden |Südafrika, Westen |
| Schweiz | Schweiz, Norden |Schweiz, Westen |
| UK |UK, Westen |UK, Süden |
| Vereinigte Arabische Emirate | Vereinigte Arabische Emirate, Norden | VAE, Mitte
| US-Verteidigungsministerium |US DoD, Osten |US DoD, Mitte |
| US Government |US Gov Arizona |US Gov Texas |
| US Government |US Gov Iowa |US Government, Virginia |
| US Government |US Government, Virginia |US Gov Texas |

> [!Important]
> - „Indien, Westen“ wird nur in einer Richtung zugeordnet. Die sekundäre Region von Indien (Westen) ist Indien (Süden), aber die sekundäre Region für Indien (Süden) ist Indien (Mitte).
> - „Brasilien, Süden“ ist einzigartig, da diese Region ein Paar mit einer Region außerhalb ihrer Geografie bildet. Die sekundäre Region von „Brasilien, Süden“ ist „USA, Süden-Mitte“. Die sekundäre Region von „USA, Süden-Mitte“ ist nicht „Brasilien, Süden“.


## <a name="an-example-of-paired-regions"></a>Beispiel für ein Regionspaar
Die Abbildung unten zeigt eine hypothetische Anwendung, die das Regionspaar für Notfallwiederherstellung verwendet. Die grünen Zahlen markieren die regionsübergreifenden Aktivitäten von drei Azure-Diensten (Azure Compute, Storage und Database) und ihre Konfiguration für regionsübergreifende Replikation. Die eindeutigen Vorteile einer Bereitstellung in Regionspaaren werden von den orangefarbenen Zahlen hervorgehoben.

![Übersicht über die Vorteile von Regionspaaren](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Abbildung 2 – Hypothetisches Azure-Regionspaar

## <a name="cross-region-activities"></a>Regionsübergreifende Aktivitäten
Wie in Abbildung 2 dargestellt.

1. **Azure Compute (IaaS)** : Sie müssen im Voraus zusätzliche Computeressourcen bereitstellen, um sicherzustellen, dass Ressourcen während eines Notfalls in einer anderen Region zur Verfügung stehen. Weitere Informationen finden Sie unter [Technischer Leitfaden zur Resilienz in Azure](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md). 

2. **Azure Storage**: Wenn Sie verwaltete Datenträger verwenden, machen Sie sich mit [regionsübergreifenden Sicherungen](/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) mit Azure Backup und der [Replikation von VMs](./site-recovery/azure-to-azure-tutorial-enable-replication.md) aus einer Region in eine andere mit Azure Site Recovery vertraut. Wenn Sie Speicherkonten verwenden, wird georedundanter Speicher (GRS) beim Erstellen eines Azure Storage-Kontos standardmäßig konfiguriert. Mithilfe von GRS werden Ihre Daten dreimal in der primären Region und dreimal im Regionspaar automatisch repliziert. Weitere Informationen finden Sie unter [Redundanzoptionen für Azure Storage](storage/common/storage-redundancy.md).

3. **Azure SQL Datenbank**: Mit der Georeplikation von Azure SQL-Datenbank können Sie die asynchrone Replikation von Transaktionen in jede Region der Welt konfigurieren. Es wird jedoch empfohlen, diese Ressourcen für die meisten Notfallwiederherstellungsszenarien in einem Regionspaar bereitzustellen. Weitere Informationen finden Sie unter [Georeplikation in Azure SQL-Datenbank](./azure-sql/database/auto-failover-group-overview.md).

4. **Azure Resource Manager**: Resource Manager bietet grundsätzlich regionsübergreifende logische Isolierung von Komponenten. Dies bedeutet, dass sich logische Fehler in einer Region weniger wahrscheinlich auf eine andere auswirken.

## <a name="benefits-of-paired-regions"></a>Vorteile eines Regionspaars

5. **Physische Isolierung**: Zwischen Azure-Rechenzentren in einem Regionspaar sollte nach Möglichkeit eine Entfernung von mindestens 480 km bestehen, was allerdings nicht in allen Gebieten zweckmäßig oder möglich ist. Durch die Trennung physischer Datencenter wird die Wahrscheinlichkeit einer gleichzeitigen Beeinträchtigung beider Regionen durch Naturkatastrophen, politische Unruhen, Stromausfälle oder physische Netzwerkausfälle verringert. Die Isolierung unterliegt den Einschränkungen des jeweiligen Gebiets (Größe, Verfügbarkeit der Energieversorgungs-/Netzwerkinfrastruktur, Vorschriften usw.).  

6. **Von der Plattform bereitgestellte Replikation**: Einige Dienste (beispielsweise georedundanter Speicher) bieten automatische Replikation im Regionspaar.

7. **Reihenfolge der Regionswiederherstellung**: Im Falle eines umfassenden Ausfalls besitzt bei jedem Paar die Wiederherstellung einer der Regionen Priorität. Für Anwendungen, die in Regionspaaren bereitgestellt sind, wird die priorisierte Wiederherstellung einer der Regionen garantiert. Wenn eine Anwendung in Regionen bereitgestellt ist, die kein Paar bilden, kann sich die Wiederherstellung verzögern. Im schlimmsten Fall werden die gewählten Regionen ggf. zuletzt wiederhergestellt.

8. **Sequenzielle Updates**: Geplante Azure-Systemupdates werden zur Minimierung von Ausfallzeit, der Auswirkungen von Fehlern und logischen Ausfällen im seltenen Fall eines fehlerhaften Updates sequenziell (nicht gleichzeitig) ausgeführt.

9. **Speicherort von Daten**: Eine Region befindet sich innerhalb des gleichen Gebiets wie ihr Paar (mit Ausnahme von „Brasilien, Süden“), um steuerliche und rechtliche Anforderungen an den Speicherort von Daten zu erfüllen.