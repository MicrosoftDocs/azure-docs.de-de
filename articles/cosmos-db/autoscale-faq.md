---
title: Häufig gestellte Fragen zu dem im Autoskalierungsmodus bereitgestellten Durchsatz von Azure Cosmos DB
description: Häufig gestellte Fragen zum im Autoskalierungsmodus bereitgestellten Durchsatz für Azure Cosmos DB-Datenbanken und -Container
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/10/2020
ms.openlocfilehash: 58e7d54750da86b8a700a4f2195bc4cfa012ae4b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092686"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Häufig gestellte Fragen zum im Autoskalierungsmodus bereitgestellten Durchsatz von Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Bei per Autoskalierung bereitgestelltem Durchsatz verwaltet und skaliert Azure Cosmos DB die RU/s Ihrer Datenbank oder Ihres Containers automatisch basierend auf der Nutzung. Dieser Artikel bietet Antworten auf häufig gestellte Fragen zur Autoskalierung.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="what-is-the-difference-between-autopilot-and-autoscale-in-azure-cosmos-db"></a>Worin besteht der Unterschied zwischen „Autopilot“ und „Autoskalierung“ in Azure Cosmos DB?
„Autoskalierung“ oder „Autoskalierung des bereitgestellten Durchsatzes“ ist der neue Name für das Feature, das zuvor als „Autopilot“ bezeichnet wurde. Im aktuellen Release der Autoskalierung wurden neue Features hinzugefügt, einschließlich der Möglichkeit, benutzerdefinierte maximale RU/s festzulegen und programmgesteuert zu arbeiten. 

### <a name="what-happens-to-databases-or-containers-created-in-the-previous-autopilot-tier-model"></a>Was geschieht mit Datenbanken oder Containern, die im vorherigen Autopilot-Ebenenmodell erstellt wurden?
Ressourcen, die mit dem früheren Ebenenmodell erstellt wurden, werden automatisch mit dem neuen benutzerdefinierten Höchstwert für RU/s der Autoskalierung unterstützt. Die Obergrenze der Ebene wird der neue Höchstwert für RU/s, sodass derselbe Skalierungsbereich gilt. 

Wenn Sie z. B. zuvor die Ebene ausgewählt haben, bei der zwischen 400 und 4.000 RU/s skaliert wurde, wird für die Datenbank oder den Container nun eine Skalierung zwischen 400 und 4.000 RU/s (Höchstwert) angezeigt. Sie können davon ausgehend den Höchstwert von RU/s in einen benutzerdefinierten Wert ändern, der für Ihre Workload geeignet ist. 

### <a name="how-quickly-will-autoscale-scale-up-based-on-spikes-in-traffic"></a>Wie schnell kann im Autoskalierungsmodus basierend auf Spitzenwerten im Datenverkehr hochskaliert werden?
Bei der Autoskalierung skaliert das System den Durchsatz (RU/s) `T` innerhalb des Bereichs `0.1 * Tmax` und `Tmax` basierend auf dem eingehenden Datenverkehr. Da die Skalierung automatisch und sofort erfolgt, können Sie zu jedem beliebigen Zeitpunkt ohne Verzögerung Durchsatz bis zum bereitgestellten Wert `Tmax` verbrauchen. 

### <a name="how-do-i-determine-what-rus-the-system-is-currently-scaled-to"></a>Wie finde ich heraus, auf welche RU/s das System zurzeit skaliert ist?
Verwenden Sie [Azure Monitor-Metriken](how-to-choose-offer.md#measure-and-monitor-your-usage), um die von der Autoskalierung bereitgestellten maximalen RU/s und den aktuellen Durchsatz (RU/s), auf den das System skaliert ist, zu überwachen. 

### <a name="what-is-the-pricing-for-autoscale"></a>Wie sieht das Preismodell für den Autoskalierungsmodus aus?
Jede Stunde wird Ihnen der höchste Durchsatz `T` in Rechnung gestellt, auf den das System innerhalb dieser Stunde skaliert wurde. Wenn Ihre Ressource während der Stunde keine Anforderungen empfangen hat oder nicht über `0.1 * Tmax` hinaus skaliert wurde, wird Ihnen der Mindestwert `0.1 * Tmax` in Rechnung gestellt. Ausführliche Informationen dazu finden Sie auf der Seite „Azure Cosmos DB – [Preise](https://azure.microsoft.com/pricing/details/cosmos-db/)“. 

### <a name="how-does-autoscale-show-up-on-my-bill"></a>Wie wird die Autoskalierung auf meiner Rechnung angezeigt?
Bei Konten mit einer Schreibregion beträgt die Autoskalierungsrate pro 100 RU/s das 1,5-Fache der Rate des standardmäßig (manuell) bereitgestellten Durchsatzes. Auf Ihrer Rechnung wird die Verbrauchseinheit des vorhandenen standardmäßig bereitgestellten Durchsatzes angezeigt. Die Menge dieser Verbrauchseinheit wird mit 1,5 multipliziert. Wenn z. B. der höchste Wert für RU/s, auf die das System innerhalb einer Stunde skaliert wurde, 6.000 RU/s betrug, werden Ihnen für diese Stunde 60 × 1,5 = 90 Einheiten dieser Verbrauchseinheit in Rechnung gestellt.

Bei Konten mit mehreren Schreibregionen ist die Autoskalierungsrate pro 100 RU/s mit der Rate des standardmäßig (manuell) bereitgestellten Durchsatzes für mehrere Schreibregionen identisch. Auf Ihrer Rechnung wird die bestehende Verbrauchseinheit für mehrere Schreibregionen angezeigt. Da die Tarife identisch sind, wird bei Verwendung der Autoskalierung dieselbe Menge wie beim Standarddurchsatz angegeben.

### <a name="does-autoscale-work-with-reserved-capacity"></a>Funktioniert die Autoskalierung mit reservierter Kapazität?
Ja. Wenn Sie reservierte Kapazität für Konten mit mehreren Schreibregionen erwerben, wird der Reservierungsrabatt für Ressourcen mit Autoskalierung in einem Verhältnis von 1,5 × der [Rate für die jeweilige Region](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md#reservation-discount-per-region) auf die Verbrauchseinheit angewandt. 

Die reservierte Kapazität für mehrere Schreibregionen funktioniert für Durchsatz mit Autoskalierung und den standardmäßig (manuell) bereitgestellten Durchsatz gleich. Weitere Informationen finden Sie unter [Reservierte Azure Cosmos DB-Kapazität](cosmos-db-reserved-capacity.md).

### <a name="does-autoscale-work-with-free-tier"></a>Funktioniert die Autoskalierung auch im Free-Tarif?
Ja. Im Free-Tarif können Sie den Durchsatz mit Autoskalierung für einen Container verwenden. Unterstützung für Datenbanken mit gemeinsam genutztem Durchsatz mit Autoskalierung und einem benutzerdefinierten Höchstwert an RU/s ist noch nicht verfügbar. Weitere Informationen finden Sie unter [Abrechnung im Free-Tarif mit Autoskalierung](understand-your-bill.md#billing-examples-with-free-tier-accounts).

### <a name="is-autoscale-supported-for-all-apis"></a>Wird die Autoskalierung für alle APIs unterstützt?
Ja, die Autoskalierung wird für alle APIs unterstützt: Core (SQL), Gremlin, Table, Cassandra und API für MongoDB.

### <a name="is-autoscale-supported-for-multi-region-write-accounts"></a>Wird die Autoskalierung für Konten mit mehreren Schreibregionen unterstützt?
Ja. Die maximalen RU/s sind in jeder Region verfügbar, die dem Azure Cosmos DB-Konto hinzugefügt wird. 

### <a name="how-do-i-enable-autoscale-on-new-databases-or-containers"></a>Wie aktiviere ich die Autoskalierung für neue Datenbanken oder Container?
Weitere Informationen finden Sie im Artikel zum [Aktivieren der Autoskalierung](how-to-provision-autoscale-throughput.md).

### <a name="can-i-enable-autoscale-on-an-existing-database-or-a-container"></a>Kann ich die Autoskalierung für vorhandene Datenbanken oder Container aktivieren?
Ja. Sie können bei Bedarf auch zwischen Autoskalierung und standardmäßig (manuell) bereitgestelltem Durchsatz wechseln. Derzeit können Sie diese Vorgänge für alle APIs nur im [Azure-Portal](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container) durchführen.

### <a name="how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work"></a>Wie funktioniert die Migration zwischen Autoskalierung und standardmäßig (manuell) bereitgestelltem Durchsatz?
Konzeptionell ist das Ändern des Durchsatztyps ein zweistufiger Prozess. Zuerst senden Sie eine Anforderung zum Ändern der Durchsatzeinstellungen, um entweder die Autoskalierung oder den manuell bereitgestellten Durchsatz zu verwenden. In beiden Fällen wird vom System automatisch ein anfänglicher Wert für die RU/s basierend auf den aktuellen Durchsatzeinstellungen und dem aktuellen Speicher festgelegt. Während dieses Schritts wird kein vom Benutzer bereitgestellter Wert für die RU/s akzeptiert. Nachdem das Update dann fertiggestellt wurde, können Sie [die RU/s ändern](#can-i-change-the-max-rus-on-the-database-or-container) und an Ihre Workload anpassen. 

**Migration vom standardmäßig (manuell) bereitgestellten Durchsatz zur Autoskalierung**

Verwenden Sie für einen Container die folgende Formel, um den anfänglichen Höchstwert an RU/s für die Autoskalierung zu schätzen: ``MAX(4000, current manual provisioned RU/s, maximum RU/s ever provisioned / 10, storage in GB * 100)``, gerundet auf die nächsten 1.000 RU/s. Der tatsächliche anfängliche Höchstwert an RU/s für die Autoskalierung kann je nach Kontokonfiguration variieren.

Beispiel 1: Angenommen, Sie verfügen über einen Container mit einem manuell bereitgestellten Durchsatz von 10.000 RU/s und 25 GB Speicherplatz. Wenn Sie die Autoskalierung aktivieren, gilt zunächst folgender Höchstwert für die RU/s: 10.000 RU/s, die zwischen 1.000 und 10.000 RU/s skaliert werden. 

Beispiel 2: Angenommen, Sie verfügen über einen Container mit einem manuell bereitgestellten Durchsatz von 50.000 RU/s und 2.500 GB Speicherplatz. Wenn Sie die Autoskalierung aktivieren, gilt zunächst folgender Höchstwert für die RU/s: 250.000 RU/s, die zwischen 25.000 und 250.000 RU/s skaliert werden. 

**Migration von Durchsatz mit Autoskalierung zu standardmäßig (manuell) bereitgestelltem Durchsatz**

Der anfänglich manuell bereitgestellte Durchsatz entspricht dem aktuellen Höchstwert für die RU/s bei der Autoskalierung. 

Beispiel: Angenommen, Sie verfügen über eine Datenbank oder einen Container mit Autoskalierung und einem Höchstwert von 20.000 RU/s (Skalierung zwischen 2.000 und 20.000 RU/s). Wenn Sie auf manuell bereitgestellten Durchsatz umstellen, beträgt der anfängliche Durchsatz 20.000 RU/s. 

### <a name="is-there-azure-cli-or-powershell-support-to-manage-databases-or-containers-with-autoscale"></a>Werden für die Verwaltung von Datenbanken oder Containern mit Autoskalierung die Azure-Befehlszeilenschnittstelle oder PowerShell unterstützt?
Derzeit können Sie Ressourcen mit Autoskalierung nur über das Azure-Portal, das .NET SDK v3 und das Java SDK v4 sowie mit Azure Resource Manager erstellen und verwalten. Unterstützung für die Azure-Befehlszeilenschnittstelle, PowerShell und andere SDKs ist noch nicht verfügbar.

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>Wird die Autoskalierung für Datenbanken mit gemeinsam genutztem Durchsatz unterstützt?
Ja, die Autoskalierung wird für Datenbanken mit gemeinsam genutztem Durchsatz unterstützt. Wählen Sie zum Aktivieren dieses Features bei der Erstellung der Datenbank die Autoskalierung und die Option **Durchsatz bereitstellen** aus. 

### <a name="what-is-the-number-of-allowed-containers-per-shared-throughput-database-when-autoscale-is-enabled"></a>Wie viele Container sind pro Datenbank mit gemeinsam genutztem Durchsatz zulässig, wenn die Autoskalierung aktiviert ist?
Azure Cosmos DB erzwingt in einer Datenbank mit gemeinsam genutztem Durchsatz einen Höchstwert von 25 Container. Dieser Wert gilt für Datenbanken mit Autoskalierung und standardmäßig (manuell) bereitgestelltem Durchsatz. 

### <a name="what-is-the-impact-of-autoscale-on-database-consistency-level"></a>Welche Auswirkungen hat die Autoskalierung auf der Datenbankkonsistenzebene?
Es gibt keine Auswirkungen der Autoskalierung auf der Datenbankkonsistenzebene.
Weitere Informationen zu verfügbaren Konsistenzebenen finden Sie im Artikel [Konsistenzebenen](consistency-levels.md).

### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Welches Speicherlimit gilt für die einzelnen „Max. RU/s“-Optionen?  
Das Speicherlimit in GB für die einzelnen maximalen RU/s lautet: Max. RU/s der Datenbank oder des Containers / 100. Wenn z. B. der Wert für maximale RU/s 20.000 RU/s beträgt, kann die Ressource 200 GB Speicherplatz unterstützen. Informationen zu den verfügbaren maximalen RU/s und Speicheroptionen finden Sie im Artikel [Beschränkungen für den Autoskalierungsmodus](provision-throughput-autoscale.md#autoscale-limits). 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Was passiert, wenn ich das mit meinem maximalen Durchsatz verbundene Speicherlimit überschreite?
Wenn das mit dem maximalen Durchsatz der Datenbank oder des Containers verbundene Speicherlimit überschritten wird, erhöht Azure Cosmos DB automatisch den maximalen Durchsatz auf den Höchstwert an RU/s, den dieses Speicherniveau unterstützen kann.

Wenn Sie z. B. mit dem Höchstwert von 50.000 RU/s beginnen (Skalierungen zwischen 5.000 und 50.000 RU/s), können Sie bis zu 500 GB an Daten speichern. Wenn Sie 500 GB überschreiten (z. B. 600 GB), gilt als neuer Höchstwert 60.000 RU/s (Skalierungen zwischen 6.000 und 60.000 RU/s).

### <a name="can-i-change-the-max-rus-on-the-database-or-container"></a>Kann ich die maximale Anzahl von RU/s für die Datenbank oder den Container ändern? 
Ja. Informationen zum Ändern des Höchstwerts für die RU/s finden Sie in [diesem Artikel](how-to-provision-autoscale-throughput.md). Wenn Sie die maximale Anzahl von RU/s ändern, kann es je nach angefordertem Wert zu einem asynchronen Vorgang kommen, dessen Abschluss einige Zeit in Anspruch nehmen kann (je nach den ausgewählten RU/s bis zu 4–6 Stunden).

#### <a name="increasing-the-max-rus"></a>Erhöhen der maximalen Anzahl von RU/s
Wenn Sie eine Anforderung senden, um die maximale Anzahl von RU/s (`Tmax`) zu erhöhen, stellt der Dienst mehr Ressourcen zur Verfügung, um die höhere maximale Anzahl von RU/s zu unterstützen. Während dieser Umstellung werden Ihre vorhandene Workloads und Vorgänge nicht beeinträchtigt. Das System skaliert Ihre Datenbank oder Container weiterhin zwischen den vorherigen Werten `0.1*Tmax` und `Tmax`, bis der neue Skalierungsbereich zwischen `0.1*Tmax_new` und `Tmax_new` bereit ist.

#### <a name="lowering-the-max-rus"></a>Verringern der maximalen Anzahl von RU/s
Wenn Sie die maximale Anzahl von RU/s senken, ist der mögliche Mindestwert `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)`, gerundet auf die nächsten 1.000 RU/s. 

Beispiel 1: Angenommen, Sie verfügen über einen Container mit Autoskalierung und einem Höchstwert von 20.000 RU/s (Skalierung zwischen 2.000 und 20.000 RU/s) sowie 50 GB Speicher. Der Mindestwert, auf den Sie die RU/s festlegen können, ist: MAX(4.000, 20.000 / 10, **50 × 100** ) = 5.000 RU/s (Skalierung zwischen 500 und 5.000 RU/s).

Beispiel 2: Angenommen, Sie verfügen über einen Container mit Autoskalierung und einem Höchstwert von 100.000 RU/s und 100 GB Speicher. Nun legen Sie den Höchstwert auf 150.000 RU/s fest (Skalierung zwischen 15.000 und 150.000 RU/s). Der niedrigste Wert, auf den Sie die maximalen RU/s festlegen können, lautet: MAX(4000, **150.000 / 10** , 100 × 100) = 15.000 RU/s (Skalierung zwischen 1.500 und 15.000 RU/s). 

Wenn Sie bei einer Datenbank mit gemeinsam genutztem Durchsatz die maximale Anzahl von RU/s senken, ist der mögliche Mindestwert `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))`, gerundet auf die nächsten 1.000 RU/s.  

Die obigen Formeln und Beispiele beziehen sich auf den kleinsten möglichen Wert für die Autoskalierung für die RU/s, den Sie festlegen können. Dieser unterscheidet sich vom Bereich `0.1 * Tmax` bis `Tmax`, in dem das System automatisch skaliert. Unabhängig davon, wie der Höchstwert für die RU/s lautet, skaliert das System immer zwischen `0.1 * Tmax` und `Tmax`. 

### <a name="how-does-ttl-work-with-autoscale"></a>Wie funktioniert TTL mit der Autoskalierung?
Bei der Autoskalierung haben TTL-Vorgänge keine Auswirkung auf die Skalierung von RU/s. Alle aufgrund der Gültigkeitsdauer (TTL) verbrauchten RUs werden nicht zusammen mit den in Rechnung gestellten RU/s des Containers mit Autoskalierung abgerechnet. 

Nehmen Sie beispielsweise an, Sie verfügen über einen Container mit Autoskalierung mit 400 bis 4.000 RU/s:
- Stunde 1: T=0: Der Container wird nicht verwendet (keine TTL- oder Workloadanforderungen). Die abrechnungsfähigen RU/s betragen 400 RU/s.
- Stunde 1: T=1: Gültigkeitsdauer (TTL) ist aktiviert.
- Stunde 1: T=2: Der Container beginnt, Anforderungen zu erhalten, die 1.000 RU in 1 Sekunde verbrauchen. Außerdem fallen 200 RUs in die TTL. Die abrechnungsfähigen RU/s betragen trotzdem 1.000 RU/s. Unabhängig davon, wann die TTLs gelten, hat dies keinen Einfluss auf die Logik der Autoskalierung.

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Welche Zuordnung besteht zwischen den maximalen RU/s und den physischen Partitionen?
Wenn Sie zum ersten Mal die maximale Anzahl der RU/s auswählen, stellt Azure Cosmos DB Folgendes bereit: Max RU/s / 10.000 RU/s = Anzahl der physischen Partitionen. Jede [physische Partition](partitioning-overview.md#physical-partitions) kann bis zu 10.000 RU/s und 50 GB Speicherplatz unterstützen. Wenn die Speichergröße zunimmt, teilt Azure Cosmos DB die Partitionen automatisch auf, um weitere physische Partitionen zur Bewältigung des Speicherzuwachses hinzuzufügen, oder erhöht den Höchstwert für die RU/s, wenn der Speicher [die zugeordneten Grenzwerte](#what-is-the-storage-limit-associated-with-each-max-rus-option) überschreitet. 

Die maximalen RU/s der Datenbank oder des Containers werden gleichmäßig auf alle physischen Partitionen aufgeteilt. Daher gilt für den Gesamtdurchsatz, auf den jede einzelne physische Partition skaliert werden kann, Folgendes: Max. RU/s der Datenbank oder des Containers / Anzahl der physischen Partitionen. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Was passiert, wenn eingehende Anforderungen die maximalen RU/s der Datenbank oder des Containers überschreiten?
Wenn die insgesamt verbrauchten RU/s die maximalen RU/s der Datenbank oder des Containers überschreiten, werden Anforderungen, die die maximalen RU/s überschreiten, gedrosselt und der Statuscode 429 zurückgegeben. Anforderungen, die zu einer über 100%igen normalisierten Auslastung führen, werden ebenfalls gedrosselt. Die normalisierte Auslastung ist definiert als die maximale Auslastung der RU/s über alle physischen Partitionen. Nehmen Sie z. B. an, Ihr maximaler Durchsatz beträgt 20.000 RU/s und Sie haben zwei physische Partitionen, P_1 und P_2, die jeweils auf 10.000 RU/s skaliert werden können. In einer bestimmten Sekunde, wenn P_1 6000 RUs und P_2 8000 RUs verwendet hat, ist die normalisierte Auslastung MAX(6000 RU / 10.000 RU, 8000 RU / 10.000 RU) = 0,8.

> [!NOTE]
> Die SDKs des Azure Cosmos DB-Clients und die Tools für den Datenimport (Azure Data Factory, BulkExecutor-Bibliothek) führen beim Code 429 automatisch einen erneuten Versuch durch, sodass gelegentliche 429er Codes akzeptabel sind. Eine anhaltend hohe Anzahl von 429 Codes kann darauf hindeuten, dass Sie die maximalen RU/s erhöhen oder Ihre Partitionierungsstrategie für eine [Hot-Partition](#autoscale-rate-limiting) überprüfen müssen.

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a> Ist es weiterhin möglich, dass der Code 429 (Drosselung/Ratenbegrenzung) angezeigt wird, wenn der Autoskalierungsmodus aktiviert ist? 
Ja. Der Code 429 kann in zwei Szenarien angezeigt werden. Erstens, wenn die insgesamt verbrauchten RU/s die maximalen RU/s der Datenbank oder des Containers überschreiten, wird der Dienst die Anforderungen entsprechend drosseln. 

Zweitens, wenn es eine Hot-Partition gibt, d. h. einen logischen Partitionsschlüsselwert, der im Vergleich zu anderen Partitionsschlüsselwerten eine unverhältnismäßig höhere Anzahl von Anforderungen aufweist, ist es möglich, dass die zugrunde liegende physische Partition ihr RU/s-Budget überschreitet. Um Hot-Partitionen zu vermeiden, ist es am besten, [einen geeigneten Partitionsschlüssel](partitioning-overview.md#choose-partitionkey) auszuwählen, der zu einer gleichmäßigen Verteilung von Speicher und Durchsatz führt. 

Wenn Sie z. B. die Option für maximalen Durchsatz von 20.000 RU/s auswählen und über 200 GB Speicherplatz mit vier physischen Partitionen verfügen, kann jede physische Partition automatisch auf bis zu 5000 RU/s hochskaliert werden. Wenn sich auf einem bestimmten logischen Partitionsschlüssel eine Hot-Partition befand, wurde der Code 429 angezeigt, wenn die zugrunde liegende physische Partition, in der sie sich befindet, 5000 RU/s überschreitet, d. h. eine normalisierte Auslastung von 100 %.


## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie die [Autoskalierung für eine Azure Cosmos DB-Datenbank oder einen -Container aktivieren](how-to-provision-autoscale-throughput.md) können.
* Erfahren Sie mehr über die [Vorteile des bereitgestellten Durchsatzes mit Autoskalierung](provision-throughput-autoscale.md#benefits-of-autoscale).
* Erfahren Sie mehr über [logische und physische Partitionen](partitioning-overview.md).
                        
