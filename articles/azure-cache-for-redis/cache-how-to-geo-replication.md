---
title: Vorgehensweise zum Einrichten der Georeplikation für Azure Cache for Redis | Microsoft-Dokumentation
description: Erfahren Sie, wie Ihre Azure Cache for Redis-Instanzen über geografische Regionen hinweg repliziert werden.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: 33d5ec89ef7563df16e0fe9b447eca88b1dba7fe
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92536877"
---
# <a name="how-to-set-up-geo-replication-for-azure-cache-for-redis"></a>Vorgehensweise zum Einrichten der Georeplikation für Azure Cache for Redis

Georeplikation bietet einen Mechanismus zum Verknüpfen von zwei Azure Cache for Redis-Instanzen im Premium-Tarif. Ein Cache wird als primärer verknüpfter Cache und der andere als sekundärer verknüpfter Cache ausgewählt. Der sekundäre verknüpfte Cache ist schreibgeschützt. Und die in den primären Cache geschriebenen Daten werden im sekundären verknüpften Cache repliziert. Die Datenübertragung zwischen primärer und sekundärer Cache-Instanz wird durch TLS geschützt. Mithilfe der Georeplikation kann ein Cache eingerichtet werden, der sich über zwei Azure-Regionen erstreckt. Dieser Artikel enthält eine Anleitung zum Konfigurieren der Georeplikation für Ihre Azure Cache for Redis-Instanzen im Premium-Tarif.

> [!NOTE]
> Die Georeplikation ist als Lösung für die Notfallwiederherstellung konzipiert. Standardmäßig wird bei Schreib- und Lesevorgängen Ihrer Anwendung die primäre Region verwendet. Die Anwendung kann optional so konfiguriert werden, dass sie aus der sekundären Region liest. Die Georeplikation bietet kein automatisches Failover. Der Grund sind Bedenken hinsichtlich zusätzlicher Netzwerkwartezeit zwischen Regionen, wenn der Rest Ihrer Anwendung in der primären Region verbleibt. Zur Verwaltung und Initiierung des Failovers muss die Verknüpfung mit dem sekundären Cache aufgehoben werden. Dadurch wird er zur neuen primären Instanz hochgestuft.

## <a name="geo-replication-prerequisites"></a>Voraussetzungen für die Georeplikation

Um die Georeplikation zwischen zwei Caches zu konfigurieren, müssen die folgenden Voraussetzungen erfüllt sein:

- Für beide Caches gilt der [Premium-Tarif](cache-overview.md#service-tiers).
- Beide Caches müssen sich in demselben Azure-Abonnement befinden.
- Der sekundäre verknüpfte Cache hat entweder die gleiche Größe oder ist größer als der primäre verknüpfte Cache.
- Beide Caches wurden erstellt und werden ausgeführt.

Einige Funktionen werden für die Georeplikation nicht unterstützt:

- Für die Georeplikation wird Persistenz nicht unterstützt.
- Das Clustering wird unterstützt, wenn für beide Caches das Clustering aktiviert ist und jeweils die gleiche Anzahl von Shards vorhanden ist.
- Caches in demselben VNET werden unterstützt.
- Caches in unterschiedlichen VNETs werden mit Einschränkungen unterstützt. Weitere Informationen finden Sie unter [Kann ich die Georeplikation bei meinen Caches in einem VNET verwenden?](#can-i-use-geo-replication-with-my-caches-in-a-vnet).

Nach der Konfiguration der Georeplikation gelten folgende Einschränkungen für Ihr verknüpftes Cachepaar:

- Der sekundäre verknüpfte Cache ist schreibgeschützt: Sie können nur Daten lesen, jedoch keine Daten darin schreiben. 
- Alle Daten, die vor dem Hinzufügen der Verknüpfung im sekundären verknüpften Cache enthalten waren, werden entfernt. Wenn die Georeplikation aber später entfernt wird, verbleiben die replizierten Daten im sekundären verknüpften Cache.
- Sie können keinen dieser Caches [skalieren](cache-how-to-scale.md), während die Caches verknüpft sind.
- Sie können die [Anzahl von Shards nicht ändern](cache-how-to-premium-clustering.md), wenn für den Cache das Clustering aktiviert ist.
- Für keinen der Caches kann Persistenz aktiviert werden.
- Der [Export](cache-how-to-import-export-data.md#export) aus den Caches ist möglich.
- Es ist nicht möglich, einen [Import](cache-how-to-import-export-data.md#import) in den sekundären verknüpften Cache durchzuführen.
- Sie können die verknüpften Caches oder die Ressourcengruppe, in denen diese enthalten sind, erst löschen, nachdem Sie die Verknüpfung der Caches aufgehoben haben. Weitere Informationen finden Sie unter [Warum ist bei dem Versuch, meinen verknüpften Cache zu löschen, ein Fehler beim Vorgang aufgetreten?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Wenn sich die Caches in unterschiedlichen Regionen befinden, fallen für die Daten, die zwischen Regionen verschoben werden, Kosten für ausgehenden Netzwerkdatenverkehr an. Weitere Informationen finden Sie unter [Wie viel kostet die Replikation meiner Daten über verschiedene Azure-Regionen hinweg?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Zwischen dem primären und sekundären verknüpften Cache wird kein automatisches Failover ausgeführt. Weitere Informationen und Informationen zum Ausführen eines Failovers für eine Clientanwendung finden Sie unter [Wie funktioniert ein Failover zum sekundären verknüpften Cache?](#how-does-failing-over-to-the-secondary-linked-cache-work).

## <a name="add-a-geo-replication-link"></a>Hinzufügen einer Verknüpfung für die Georeplikation

1. Klicken Sie zum Verknüpfen von zwei Caches für die Georeplikation zuerst im Menü „Ressource“ des Caches, der als primärer verknüpfter Cache verwendet werden soll, auf **Georeplikation** . Klicken Sie anschließend auf dem Blatt **Georeplikation** auf **Link für Cachereplikation hinzufügen** .

    ![Hinzufügen einer Verknüpfung](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Klicken Sie in der Liste **Kompatible Caches** auf den Namen des gewünschten sekundären Caches. Wenn der sekundäre Cache nicht in der Liste angezeigt wird, sollten Sie sicherstellen, dass die [Voraussetzungen für die Georeplikation](#geo-replication-prerequisites) für den sekundären Cache erfüllt sind. Klicken Sie zum Filtern der Caches nach Region auf der Karte auf die Region, um nur Caches in der Liste **Kompatible Caches** anzuzeigen.

    ![Für die Georeplikation kompatible Caches](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Sie können mithilfe des Kontextmenüs auch den Verknüpfungsvorgang starten oder Details zum sekundären Cache anzeigen.

    ![Kontextmenü für die Georeplikation](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Klicken Sie auf **Verknüpfen** , um zwei Caches zu verknüpfen und den Replikationsvorgang zu starten.

    ![Verknüpfen von Caches](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Auf dem Blatt **Georeplikation** können Sie den Status des Replikationsvorgangs sehen.

    ![Verknüpfungsstatus](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Auf dem Blatt **Übersicht** können Sie auch den Verknüpfungsstatus für den primären und sekundären Cache anzeigen.

    ![Screenshot: Anzeigen des Verknüpfungsstatus für den primären Cache und sekundäre Caches.](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Nach Abschluss des Replikationsvorgangs wechselt der **Verknüpfungsstatus** zu **Erfolgreich** .

    ![Cachestatus](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Der primäre verknüpfte Cache bleibt während des Verknüpfungsvorgangs für die Nutzung verfügbar. Der sekundäre verknüpfte Cache ist erst verfügbar, nachdem der Verknüpfungsvorgang abgeschlossen wurde.

## <a name="remove-a-geo-replication-link"></a>Entfernen einer Verknüpfung für die Georeplikation

1. Um die Verknüpfung zwischen zwei Caches zu entfernen und die Georeplikation zu beenden, klicken Sie auf dem Blatt **Georeplikation** auf **Verknüpfung von Caches aufheben** .
    
    ![Aufheben der Verknüpfung von Caches](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Wenn der Vorgang zur Aufhebung der Verknüpfung abgeschlossen ist, ist der sekundäre Cache für Lese- und Schreibvorgänge verfügbar.

>[!NOTE]
>Wenn die Verknüpfung für die Georeplikation entfernt wird, bleiben die replizierten Daten aus dem primären verknüpften Cache im sekundären Cache.
>
>

## <a name="geo-replication-faq"></a>FAQs zur Georeplikation

- [Kann ich die Georeplikation bei einem Cache mit Standard- oder Basic-Tarif verwenden?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Ist mein Cache während des Vorgangs zur Verknüpfung oder Aufhebung der Verknüpfung verfügbar?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Kann ich mehr als zwei Caches miteinander verknüpfen?](#can-i-link-more-than-two-caches-together)
- [Kann ich zwei Caches aus verschiedenen Azure-Abonnements verknüpfen?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Kann ich zwei Caches von unterschiedlicher Größe verknüpfen?](#can-i-link-two-caches-with-different-sizes)
- [Kann ich die Georeplikation mit aktiviertem Clustering verwenden?](#can-i-use-geo-replication-with-clustering-enabled)
- [Kann ich die Georeplikation bei meinen Caches in einem VNET verwenden?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Was ist der Replikationszeitplan für die Redis-Georeplikation?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Wie lange dauert die Georeplikation?](#how-long-does-geo-replication-replication-take)
- [Ist der Wiederherstellungspunkt für die Replikation garantiert?](#is-the-replication-recovery-point-guaranteed)
- [Kann ich die Georeplikation mit PowerShell oder der Azure CLI verwalten?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Wie viel kostet die Replikation meiner Daten über verschiedene Azure-Regionen hinweg?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Warum ist bei dem Versuch, meinen verknüpften Cache zu löschen, ein Fehler beim Vorgang aufgetreten?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Welche Region sollte ich für meinen sekundären verknüpften Cache verwenden?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Wie funktioniert ein Failover zum sekundären verknüpften Cache?](#how-does-failing-over-to-the-secondary-linked-cache-work)
- [Kann ich die Firewall mit Georeplikation konfigurieren?](#can-i-configure-a-firewall-with-geo-replication)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Kann ich die Georeplikation bei einem Cache mit Standard- oder Basic-Tarif verwenden?

Nein, die Georeplikation ist nur für Caches im Premium-Tarif verfügbar.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Ist mein Cache während des Vorgangs zur Verknüpfung oder Aufhebung der Verknüpfung verfügbar?

- Beim Verknüpfen bleibt der primäre verknüpfte Cache verfügbar, während der Vorgang abgeschlossen wird.
- Beim Verknüpfen ist der sekundäre verknüpfte Cache erst verfügbar, nachdem der Verknüpfungsvorgang abgeschlossen wurde.
- Wenn die Verknüpfung aufgehoben wird, bleiben beide Caches während des Vorgangs verfügbar.

### <a name="can-i-link-more-than-two-caches-together"></a>Kann ich mehr als zwei Caches miteinander verknüpfen?

Nein. Sie können nur zwei Caches miteinander verknüpfen.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Kann ich zwei Caches aus verschiedenen Azure-Abonnements verknüpfen?

Nein, beide Caches müssen sich im selben Azure-Abonnement befinden.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Kann ich zwei Caches von unterschiedlicher Größe verknüpfen?

Ja, solange der sekundäre verknüpfte Cache größer ist als der primäre verknüpfte Cache.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Kann ich die Georeplikation mit aktiviertem Clustering verwenden?

Ja, solange beide Caches über dieselbe Anzahl von Shards verfügen.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Kann ich die Georeplikation bei meinen Caches in einem VNET verwenden?

Ja. Die Georeplikation von Caches in VNETs wird mit Einschränkungen unterstützt:

- Es wird Unterstützung für die Georeplikation zwischen Caches im selben VNET geboten.
- Die Georeplikation zwischen Caches in unterschiedlichen VNETs wird ebenfalls unterstützt.
  - Wenn sich die VNETs in derselben Region befinden, können Sie sie per [VNET-Peering](../virtual-network/virtual-network-peering-overview.md) oder per [VPN Gateway-VNET-zu-VNET-Verbindung](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) verbinden.
  - Wenn sich die VNETs in verschiedenen Regionen befinden, wird Georeplikation über VNET-Peering unterstützt, aber eine Client-VM in VNET 1 (Region 1) kann aufgrund einer Einschränkung mit internen Lastausgleichsmodulenvom Typ „Basic“ nicht über ihren DNS-Namen auf den Cache in VNET 2 (Region 2) zugreifen. Weitere Informationen zu Einschränkungen beim VNET-Peering finden Sie im Artikel zu den [Anforderungen und Einschränkungen beim VNET-Peering](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Die empfohlene Lösung ist die Verwendung einer VPN Gateway-VNET-zu-VNET-Verbindung.
  
Mit [dieser Azure-Vorlage](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/) können Sie schnell zwei georeplizierte Caches in einem VNET bereitstellen, indem Sie eine VPN Gateway-VNET-zu-VNET-Verbindung verwenden.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Was ist der Replikationszeitplan für die Redis-Georeplikation?

Die Replikation wird kontinuierlich und asynchron und nicht nach einem bestimmten Zeitplan durchgeführt. Alle Schreibvorgänge für die primäre Datenbank werden sofort und asynchron in der sekundären Datenbank repliziert.

### <a name="how-long-does-geo-replication-replication-take"></a>Wie lange dauert die Georeplikation?

Die Replikation ist ein inkrementeller, asynchroner und kontinuierlicher Vorgang, und die in Anspruch genommene Zeit unterscheidet sich nicht wesentlich von der Latenz zwischen Regionen. Unter bestimmten Umständen muss für den sekundären Cache eine vollständige Synchronisierung der Daten aus der primären Datenbank erfolgen. Die Replikationszeit hängt in diesem Fall von mehreren Faktoren ab, z. B. der Last im primären Cache, der verfügbaren Netzwerkbandbreite, der Latenz zwischen den Regionen usw. Wir haben ermittelt, dass die Replikationsdauer für ein Georeplikationspaar mit 53 GB zwischen 5 und 10 Minuten betragen kann.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Ist der Wiederherstellungspunkt für die Replikation garantiert?

Für Caches in einem georeplizierten Modus ist die Persistenz deaktiviert. Wenn die Verknüpfung eines Georeplikationspaars aufgehoben wird, z. bei einem vom Kunden initiierten Failover, werden die synchronisierten Daten des sekundären verknüpften Caches bis zu diesem Zeitpunkt beibehalten. In diesen Situationen wird kein Wiederherstellungspunkt garantiert.

Zum Erhalten eines Wiederherstellungspunkts können Sie einen [Export](cache-how-to-import-export-data.md#export) aus einem der beiden Caches durchführen. Später können Sie dann einen [Import](cache-how-to-import-export-data.md#import) in den primären verknüpften Cache durchführen.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Kann ich die Georeplikation mit PowerShell oder der Azure CLI verwalten?

Ja. Die Georeplikation kann über das Azure-Portal, PowerShell oder die Azure CLI verwaltet werden. Weitere Informationen finden Sie in den [PowerShell-Dokumenten](/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) bzw. [Azure CLI-Dokumenten](/cli/azure/redis/server-link?view=azure-cli-latest).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Wie viel kostet die Replikation meiner Daten über verschiedene Azure-Regionen hinweg?

Beim Einsatz der Georeplikation werden Daten aus dem primären verknüpften Cache in den sekundären verknüpften Cache repliziert. Es fallen keine Gebühren für die Datenübertragung an, wenn sich die beiden verknüpften Caches in derselben Region befinden. Falls sich die beiden verknüpften Caches in unterschiedlichen Regionen befinden, entsprechen die Gebühren für die Datenübertragung den Kosten für ausgehenden Netzwerkdatenverkehr in beiden Regionen. Weitere Informationen finden Sie unter [Bandbreite – Preisdetails](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Warum ist bei dem Versuch, meinen verknüpften Cache zu löschen, ein Fehler beim Vorgang aufgetreten?

Georeplizierte Caches und die zugehörigen Ressourcengruppen können nicht gelöscht werden, während sie verknüpft sind, sondern erst nach dem Entfernen der Verknüpfung für die Georeplikation. Wenn Sie versuchen, die Ressourcengruppe zu löschen, die einen oder beide verknüpfte Caches enthält, werden die anderen Ressourcen in der Ressourcengruppe gelöscht, die Ressourcengruppe bleibt jedoch im Status `deleting` und alle verknüpften Caches in der Ressourcengruppe im Status `running`. Um die Ressourcengruppe und die darin enthaltenen verknüpften Caches vollständig zu löschen, müssen Sie die Verknüpfung des Caches aufheben. Dies ist unter [Entfernen einer Verknüpfung für die Georeplikation](#remove-a-geo-replication-link) beschrieben.

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Welche Region sollte ich für meinen sekundären verknüpften Cache verwenden?

Grundsätzlich sollte sich Ihr Cache in derselben Azure-Region wie die Anwendung befinden, die darauf zugreift. Für Anwendungen mit separater primärer Region und Fallbackregion wird empfohlen, dass sich Ihre primären und sekundären Caches in denselben Regionen befinden. Weitere Informationen zu gekoppelten Regionen finden Sie unter [Best Practices – gekoppelte Azure-Regionen](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Wie funktioniert ein Failover zum sekundären verknüpften Cache?

Das automatische Failover über Azure-Regionen hinweg wird für georeplizierte Caches nicht unterstützt. In einem Szenario mit Notfallwiederherstellung sollten Kunden in Ihrer Sicherungsregion den gesamten Anwendungsstapel auf koordinierte Weise aufrufen. Wenn einzelnen Anwendungskomponenten die Entscheidung, wann auf die Sicherungen umgeschaltet wird, selbst überlassen wird, kann sich dies negativ auf die Leistung auswirken. Einer der wesentlichen Vorteile bei Redis besteht darin, dass es sich um einen Speicher mit sehr geringen Wartezeiten handelt. Wenn sich die Hauptanwendung des Kunden in einer anderen Region als der zugehörige Cache befindet, wirkt sich die zusätzliche Roundtripzeit wahrnehmbar auf die Leistung aus. Aus diesem Grund werden automatische Failover vermieden, da es zu vorübergehenden Problemen mit der Verfügbarkeit kommen kann.

Heben Sie zuerst die Verknüpfung der Caches auf, um ein vom Kunden initiiertes Failover zu starten. Ändern Sie anschließend Ihren Redis-Client, um den Verbindungsendpunkt des (zuvor verknüpften) sekundären Caches zu verwenden. Wenn die Verknüpfung beider Caches aufgehoben wurde, wird der sekundäre Cache wieder zu einem Cache mit regulärem Lese-/Schreibzugriff und akzeptiert direkte Anforderungen von Redis-Clients.

### <a name="can-i-configure-a-firewall-with-geo-replication"></a>Kann ich eine Firewall mit Georeplikation konfigurieren?

Ja, Sie können eine [Firewall](./cache-configure.md#firewall) mit Georeplikation konfigurieren. Damit Georeplikation mit einer Firewall funktioniert, müssen Sie sicherstellen, dass die IP-Adresse des sekundären Caches den Firewallregeln des primären Caches hinzugefügt wird.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Azure Cache for Redis-Features.

* [Azure Cache for Redis-Dienstebenen](cache-overview.md#service-tiers)