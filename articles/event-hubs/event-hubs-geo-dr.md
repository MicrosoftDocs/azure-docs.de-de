---
title: 'Georedundante Notfallwiederherstellung: Azure Event Hubs | Microsoft-Dokumentation'
description: Verwenden von geografischen Regionen für Failover und Notfallwiederherstellung in Azure Event Hubs
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 6dd2385a6f6e61136a1284171532aedd70a9cc96
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608349"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs: Georedundante Notfallwiederherstellung 
Wenn gesamte Azure-Regionen oder -Rechenzentren ausfallen (wenn keine [Verfügbarkeitszonen](../availability-zones/az-overview.md) verwendet werden), ist es von entscheidender Bedeutung, dass die Datenverarbeitung in einer anderen Region oder in einem anderen Rechenzentrum fortgesetzt werden kann. Daher sind die *georedundante Notfallwiederherstellung* und die *Georeplikation* wichtige Funktionen für jedes Unternehmen. Azure Event Hubs unterstützt die georedundante Notfallwiederherstellung und die Georeplikation auf Namespaceebene. 

> [!NOTE]
> Das Feature zur georedundanten Notfallwiederherstellung ist nur für die [Tarife „Standard“ und „Dediziert“](https://azure.microsoft.com/pricing/details/event-hubs/) verfügbar.  

## <a name="outages-and-disasters"></a>Ausfälle und Notfälle

Es ist wichtig, dass Sie den Unterschied zwischen „Ausfällen“ und „Notfällen“ kennen. Ein **Ausfall** ist die vorübergehende Nichtverfügbarkeit von Azure Event Hubs und kann einige Komponenten des Diensts (etwa einen Nachrichtenspeicher) oder aber das gesamte Datencenter betreffen. Nachdem das Problem behoben wurde, ist Event Hubs allerdings wieder verfügbar. In der Regel führt ein Ausfall nicht zum Verlust von Nachrichten oder anderen Daten. Ein Beispiel für einen Ausfall ist ein Stromausfall im Rechenzentrum. Bei einigen Ausfällen handelt es sich nur um kurze Verbindungsunterbrechungen aufgrund von vorübergehenden Problemen bzw. Netzwerkproblemen. 

Ein *Notfall* ist als dauerhafter oder längerer Ausfall eines Event Hubs-Clusters, einer Azure-Region oder eines Datencenters definiert. Die Region oder das Datencenter wird ggf. wieder verfügbar, kann aber auch für mehrere Stunden oder Tage ausfallen. Beispiele für Notfälle sind Feuer, Überflutung und Erdbeben. Ein Notfall, der dauerhaft wird, kann zum Verlust von Nachrichten, Ereignissen oder anderen Daten führen. In den meisten Fällen kommt es allerdings zu keinem Datenverlust, und Nachrichten können wiederhergestellt werden, sobald das Rechenzentrum gesichert ist.

Das Feature der georedundanten Notfallwiederherstellung von Azure Event Hubs ist eine Lösung zur Notfallwiederherstellung. Die Konzepte und der Workflow, die in diesem Artikel beschrieben werden, gelten für Notfallszenarien und nicht für vorübergehende Ausfälle. Eine ausführliche Erläuterung der Notfallwiederherstellung in Microsoft Azure finden Sie in [diesem Artikel](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Allgemeine Konzepte und Begriffe

Bei der Funktion zur Notfallwiederherstellung wird die Notfallwiederherstellung von Metadaten implementiert, und sie basiert auf speziellen primären und sekundären Namespaces. 

Das Feature zur georedundanten Notfallwiederherstellung ist ausschließlich für die [Tarife „Standard“ und „Dediziert“](https://azure.microsoft.com/pricing/details/event-hubs/) verfügbar. Sie müssen keine Änderungen an den Verbindungszeichenfolgen vornehmen, da die Verbindung über einen Alias hergestellt wird.

In diesem Artikel werden die folgenden Begriffe verwendet:

-  *Alias*: Der Name für die Konfiguration einer von Ihnen eingerichteten Notfallwiederherstellung. Der Alias stellt einen einzelnen, stabilen, vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) als Verbindungszeichenfolge bereit. Anwendungen verwenden diese Alias-Verbindungszeichenfolge, um eine Verbindung mit einem Namespace herzustellen. 

-  *Primärer/sekundärer Namespace*: Die Namespaces, die dem Alias entsprechen. Der primäre Namespace ist aktiv und empfängt Nachrichten. (Dies kann ein bereits vorhandener oder ein neuer Namespace sein.) Der sekundäre Namespace ist passiv und empfängt keine Nachrichten. Die Metadaten zwischen beiden Namespaces werden synchronisiert, sodass beide nahtlos und ohne Änderung des Anwendungscodes oder der Verbindungszeichenfolge Nachrichten entgegennehmen können. Um sicherzustellen, dass nur der aktive Namespace Nachrichten empfängt, müssen Sie den Alias verwenden. 

-  *Metadaten*: Entitäten (etwa Event Hubs und Consumergruppen) sowie deren Eigenschaften des Diensts, die dem Namespace zugeordnet sind. Nur Entitäten und ihre Einstellungen werden automatisch repliziert. Nachrichten und Ereignisse werden nicht repliziert. 

-  *Failover*: Der Vorgang zum Aktivieren des sekundären Namespace.

## <a name="supported-namespace-pairs"></a>Unterstützte Namespacepaare
Die folgenden Kombinationen von primären und sekundären Namespaces werden unterstützt:  

| Primärer Namespace | Sekundärer Namespace | Unterstützt | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Ja | 
| Standard | Dediziert | Ja | 
| Dediziert | Dediziert | Ja | 
| Dediziert | Standard | Nein | 

> [!NOTE]
> Namespaces, die sich im selben dedizierten Cluster befinden, können nicht paarweise gekoppelt werden. Namespaces, die sich in unterschiedlichen Clustern befinden, können paarweise gekoppelt werden. 

## <a name="setup-and-failover-flow"></a>Setup und Failoverablauf

Im folgenden Abschnitt finden Sie eine Übersicht über den Failoverprozess und erfahren, wie Sie das erste Failover einrichten. 

![1][]

### <a name="setup"></a>Einrichten

Zuerst erstellen bzw. verwenden Sie einen vorhandenen primären Namespace und einen neuen sekundären Namespace und koppeln diese anschließend. Über diese Kopplung erhalten Sie einen Alias, mit dem Sie eine Verbindung herstellen können. Da Sie einen Alias verwenden, müssen Sie keine Verbindungszeichenfolgen ändern. Nur neue Namespaces können der Failoverkopplung hinzugefügt werden. Abschließend sollten Sie einige Überwachungsfunktionen hinzufügen, um zu ermitteln, ob ein Failover erforderlich ist. In den meisten Fällen ist der Dienst Teil eines großen Ökosystems, sodass automatische Failover selten möglich sind, da Failover häufig synchron mit dem restlichen Subsystem oder der Infrastruktur durchgeführt werden müssen.

### <a name="example"></a>Beispiel

In einem Beispiel für dieses Szenario geht es um eine POS-Lösung (Point of Sale), die entweder Nachrichten oder Ereignisse ausgibt. Event Hubs übergibt diese Ereignisse an eine Lösung für die Zuordnung oder Neuformatierung, von der dann zugeordnete Daten zur weiteren Verarbeitung an ein anderes System weitergeleitet werden. An diesem Punkt werden alle diese Systeme ggf. in derselben Azure-Region gehostet. Die Entscheidung darüber, wann für welchen Teil ein Failover durchgeführt wird, richtet sich nach dem Datenfluss in Ihrer Infrastruktur. 

Sie können das Failover entweder mit Überwachungssystemen oder mit benutzerdefinierten Überwachungslösungen automatisieren. Für diese Art der Automatisierung sind aber zusätzliche Planungs- und Arbeitsschritte erforderlich, was den Rahmen dieses Artikels sprengen würde.

### <a name="failover-flow"></a>Failoverablauf

Im Zuge der Initiierung des Failovers müssen zwei Schritte ausgeführt werden:

1. Bei einem weiteren Ausfall muss ein erneutes Failover möglich sein. Richten Sie daher einen weiteren passiven Namespace ein, und aktualisieren Sie die Kopplung. 

2. Übertragen Sie Nachrichten mithilfe von Pull aus dem ehemals primären Namespace, nachdem er wieder verfügbar ist. Verwenden Sie diesen Namespace danach für das reguläre Messaging außerhalb Ihrer eingerichteten geografischen Wiederherstellung, oder löschen Sie den alten primären Namespace.

> [!NOTE]
> Es wird nur die Semantik für „Fail Forward“ unterstützt. In diesem Szenario führen Sie das Failover und anschließend die Reparatur mit einem neuen Namespace durch. Ein Failback wird nicht unterstützt, z.B. in einem SQL-Cluster. 

![2][]

## <a name="management"></a>Verwaltung

Falls Sie einen Fehler gemacht haben (z.B. eine Kopplung der falschen Regionen während des anfänglichen Setups), können Sie die Kopplung der beiden Namespaces jederzeit trennen. Wenn Sie die gekoppelten Namespaces als reguläre Namespaces verwenden möchten, löschen Sie den Alias.

## <a name="samples"></a>Beispiele

Das [Beispiel auf GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) zeigt, wie Sie ein Failover einrichten und initiieren. In diesem Beispiel werden folgende Konzepte veranschaulicht:

- Einstellungen, die in Azure Active Directory für die Verwendung von Azure Resource Manager mit Event Hubs erforderlich sind 
- Schritte, die zum Ausführen des Beispielcodes erforderlich sind 
- Senden und Empfangen aus dem aktuellen primären Namespace 

## <a name="considerations"></a>Überlegungen

Beachten Sie Folgendes:

1. Die georedundante Notfallwiederherstellung von Event Hubs repliziert entwurfsbedingt keine Daten. Daher können Sie den alten Offsetwert Ihres primären Event Hubs für den sekundären Event Hub nicht wiederverwenden. Es wird empfohlen, den Ereignisempfänger mit einer der folgenden Methoden neu zu starten:

- *EventPosition.FromStart()* : Wenn Sie alle Daten auf dem sekundären Event Hub lesen möchten.
- *EventPosition.FromEnd()* : Wenn Sie alle neuen Daten ab dem Zeitpunkt der Verbindung mit dem sekundären Event Hub lesen möchten.
- *EventPosition.FromEnqueuedTime(dateTime)* : Wenn Sie alle Daten lesen möchten, die im sekundären Event Hub ab einem bestimmten Datum und einer bestimmten Uhrzeit empfangen wurden.

2. Berücksichtigen Sie bei der Failoverplanung auch den Zeitfaktor. Falls beispielsweise länger als 15 bis 20 Minuten keine Konnektivität vorhanden ist, empfiehlt es sich unter Umständen, das Failover zu initiieren. 
 
3. Die Tatsache, dass keine Daten repliziert werden, bedeutet, dass derzeit keine aktiven Sitzungen repliziert werden. Außerdem kann es sein, dass die Duplikaterkennung und geplante Nachrichten nicht funktionieren. Neue Sitzungen, neue geplante Nachrichten und neue Duplikate funktionieren. 

4. Die Durchführung eines Failovers für eine komplexe verteilte Infrastruktur sollte mindestens einmal [durchgespielt](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) werden. 

5. Das Synchronisieren von Entitäten kann einige Zeit dauern (etwa eine Minute pro 50 bis 100 Entitäten).

## <a name="availability-zones"></a>Verfügbarkeitszonen 

Die Event Hubs-Standard-SKU unterstützt [Verfügbarkeitszonen](../availability-zones/az-overview.md), die fehlerisolierte Standorte innerhalb einer Azure-Region bieten. 

> [!NOTE]
> Die Unterstützung für Verfügbarkeitszonen für Azure Event Hubs Standard ist nur in [Azure-Regionen](../availability-zones/az-region.md) verfügbar, in denen Verfügbarkeitszonen vorhanden sind.

Sie können Verfügbarkeitszonen nur für neue Namespaces über das Azure-Portal aktivieren. In Event Hubs wird die Migration vorhandener Namespaces nicht unterstützt. Sie können die Zonenredundanz nicht deaktivieren, wenn Sie sie für Ihren Namespace aktiviert haben.

![3][]

## <a name="private-endpoints"></a>Private Endpunkte
Dieser Abschnitt enthält zusätzliche Überlegungen zur Verwendung der georedundanten Notfallwiederherstellung mit Namespaces, bei denen private Endpunkte verwendet werden. Informationen zur Verwendung privater Endpunkte mit Event Hubs im Allgemeinen finden Sie unter [Konfigurieren privater Endpunkte](private-link-service.md).

### <a name="new-pairings"></a>Neue Kopplungen
Wenn Sie versuchen, eine Kopplung zwischen einem primären Namespace mit einem privaten Endpunkt und einem sekundären Namespace ohne einen privaten Endpunkt zu erstellen, wird die Kopplung nicht durchgeführt. Die Kopplung erfolgt nur, wenn sowohl der primäre als auch der sekundäre Namespace einen privaten Endpunkt aufweist. Es wird empfohlen, für den primären und den sekundären Namespace sowie für virtuelle Netzwerke, in denen private Endpunkte erstellt werden, die gleichen Konfigurationen zu verwenden.  

> [!NOTE]
> Wenn Sie versuchen, den primären Namespace mit einem privaten Endpunkt und einen sekundären Namespace zu koppeln, prüft der Überprüfungsprozess lediglich, ob ein privater Endpunkt im sekundären Namespace vorhanden ist. Es wird nicht überprüft, ob der Endpunkt funktioniert oder nach dem Failover ausgeführt wird. Sie selbst müssen sicherstellen, dass der sekundäre Namespace mit dem privaten Endpunkt nach dem Failover erwartungsgemäß funktioniert.
>
> Um zu testen, ob die Konfiguration des privaten Endpunkts für den primären und den sekundären Namespace identisch ist, senden Sie von außerhalb des virtuellen Netzwerks eine Leseanforderung (z. B. [Get Event Hub](/rest/api/eventhub/get-event-hub)) an den sekundären Namespace, und überprüfen Sie, ob der Dienst eine Fehlermeldung ausgibt.

### <a name="existing-pairings"></a>Vorhandene Kopplungen
Wenn die Kopplung zwischen dem primären und dem sekundären Namespace bereits vorhanden ist, kann für den primären Namespace kein privater Endpunkt erstellt werden. Erstellen Sie zunächst einen privaten Endpunkt für den sekundären Namespace und dann einen privaten Endpunkt für den primären Endpunkt, um das Problem zu beheben.

> [!NOTE]
> Für den sekundären Namespace ist nur Lesezugriff möglich, Konfigurationen des privaten Endpunkts können dagegen aktualisiert werden. 

### <a name="recommended-configuration"></a>Empfohlene Konfiguration
Beim Erstellen einer Konfiguration für die Notfallwiederherstellung Ihrer Anwendung und Event Hubs-Namespaces müssen Sie private Endpunkte für primäre und sekundäre Event Hubs-Namespaces für die virtuellen Netzwerke erstellen, die primäre und sekundäre Instanzen der Anwendung enthalten. 

Sie haben beispielsweise die beiden virtuellen Netzwerke VNET-1 und VNET-2 sowie den primären Namespace EventHubs-Namespace1-Primary und den sekundären Namespace EventHubs-Namespace2-Secondary erstellt. Dann müssen Sie die folgenden Schritte ausführen: 

- Erstellen Sie für EventHubs-Namespace1-Primary zwei private Endpunkte, an denen Subnetze aus VNET-1 und VNET-2 verwendet werden.
- Erstellen Sie für EventHubs-Namespace2-Secondary zwei private Endpunkte, an denen dieselben Subnetze aus VNET-1 und VNET-2 verwendet werden. 

![Private Endpunkte und virtuelle Netzwerke](./media/event-hubs-geo-dr/private-endpoints-virtual-networks.png)

Der Vorteil dieses Ansatzes besteht darin, dass ein Failover auf Anwendungsebene unabhängig vom Event Hubs-Namespace erfolgen kann. Betrachten Sie die folgenden Szenarien: 

**Nur Anwendungsfailover:** In diesem Fall ist die Anwendung nicht in VNET-1 vorhanden, wird jedoch in VNET-2 verschoben. Da beide privaten Endpunkte sowohl in VNET-1 als auch in VNET-2 für primäre und sekundäre Namespaces konfiguriert sind, wird die Anwendung ausgeführt. 

**Nur Failover für Event Hubs-Namespaces:** Da auch in diesem Fall beide privaten Endpunkte in beiden virtuellen Netzwerken für primäre und sekundäre Namespaces konfiguriert sind, wird die Anwendung ausgeführt. 

> [!NOTE]
> Eine Anleitung zur georedundanten Notfallwiederherstellung eines virtuellen Netzwerks finden Sie unter [Virtuelles Netzwerk: Geschäftskontinuität](../virtual-network/virtual-network-disaster-recovery-guidance.md).
 
## <a name="next-steps"></a>Nächste Schritte

* Im [Beispiel auf GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) wird ein einfacher Workflow erläutert, der eine Geo-Kopplung erstellt und ein Failover für ein Notfallwiederherstellungsszenario initiiert.
* Der [REST API-Verweis](/rest/api/eventhub/) beschreibt APIs für das Konfigurieren der georedundanten Notfallwiederherstellung.

Weitere Informationen zu Event Hubs erhalten Sie unter den folgenden Links:

- Erste Schritte mit Event Hubs
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [Event Hubs – häufig gestellte Fragen](event-hubs-faq.md)
* [Beispielanwendungen mit Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
