---
title: Konfigurieren von Azure Cache for Redis
description: Grundlagen der Redis-Standardkonfiguration für Azure Cache for Redis und Informationen zur Konfiguration Ihrer Azure Cache for Redis-Instanzen
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: f0d0742994b14f692c2aea9130edc73d779cff52
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92544765"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Konfigurieren von Azure Cache for Redis
In diesem Thema sind die für Ihre Azure Cache for Redis-Instanzen verfügbaren Konfigurationen beschrieben. Darüber hinaus wird in diesem Thema die standardmäßige Redis-Serverkonfiguration für Azure Cache for Redis-Instanzen behandelt.

> [!NOTE]
> Weitere Informationen zur Konfiguration und Verwendung von Premium-Cache-Features finden Sie unter [Konfigurieren von Persistenz](cache-how-to-premium-persistence.md), [Konfigurieren von Clustern](cache-how-to-premium-clustering.md) und [Konfigurieren der Virtual Network-Unterstützung](cache-how-to-premium-vnet.md).
>
>

## <a name="configure-azure-cache-for-redis-settings"></a>Konfigurieren von Azure Cache for Redis-Einstellungen
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Azure Cache for Redis-Einstellungen werden auf dem Blatt **Azure Cache for Redis** über das **Ressourcenmenü** angezeigt und konfiguriert.

![Azure Cache for Redis-Einstellungen](./media/cache-configure/redis-cache-settings.png)

Sie können die folgenden Einstellungen über das **Ressourcenmenü** anzeigen und konfigurieren.

* [Übersicht](#overview)
* [Aktivitätsprotokoll](#activity-log)
* [Zugriffssteuerung (IAM)](#access-control-iam)
* [Tags](#tags)
* [Diagnose und Problembehandlung](#diagnose-and-solve-problems)
* [Einstellungen](#settings)
    * [Zugriffsschlüssel](#access-keys)
    * [Erweiterte Einstellungen](#advanced-settings)
    * [Azure Cache for Redis-Ratgeber](#azure-cache-for-redis-advisor)
    * [Skalieren](#scale)
    * [Clustergröße](#cluster-size)
    * [Datenpersistenz](#redis-data-persistence)
    * [Planen von Updates](#schedule-updates)
    * [Georeplikation](#geo-replication)
    * [Virtual Network](#virtual-network)
    * [Firewall](#firewall)
    * [Eigenschaften](#properties)
    * [Locks](#locks)
    * [Automatisierungsskript](#automation-script)
* Verwaltung
    * [Importieren von Daten](#importexport)
    * [Daten exportieren](#importexport)
    * [Neustart](#reboot)
* [Überwachung](#monitoring)
    * [Redis-Metriken](#redis-metrics)
    * [Warnregeln](#alert-rules)
    * [Diagnose](#diagnostics)
* Einstellungen für Support und Problembehandlung
    * [Ressourcenintegrität](#resource-health)
    * [Neue Supportanfrage](#new-support-request)


## <a name="overview"></a>Übersicht

**Übersicht** enthält grundlegende Informationen zu Ihrem Cache, z.B. Name, Ports, Tarif und ausgewählte Cachemetriken.

### <a name="activity-log"></a>Aktivitätsprotokoll

Klicken Sie auf **Aktivitätsprotokoll** , um in Ihrem Cache ausgeführte Aktionen anzuzeigen. Sie können auch filtern, um diese Ansicht zum Einbeziehen anderer Ressourcen zu erweitern. Weitere Informationen zur Arbeit mit Überwachungsprotokollen finden Sie unter [Überwachen von Vorgängen mit Resource Manager](../azure-resource-manager/management/view-activity-logs.md). Weitere Informationen zum Überwachen von Azure Cache for Redis-Ereignissen finden Sie unter [Vorgänge und Warnungen](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Zugriffssteuerung (IAM)

Der Abschnitt **Zugriffssteuerung (IAM)** bietet Unterstützung für die rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC) über das Azure-Portal. Mit dieser Konfiguration können Organisationen ihre Zugriffsverwaltungsanforderungen einfach und präzise erfüllen. Weitere Informationen finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>`Tags`

Im Abschnitt **Tags** können Sie Ihre Ressourcen organisieren. Weitere Informationen finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md).


### <a name="diagnose-and-solve-problems"></a>Diagnose und Problembehandlung

Klicken Sie auf **Diagnose und Problembehandlung** , um gängige Probleme und Strategien zu deren Behebung anzuzeigen.



## <a name="settings"></a>Einstellungen
Der Abschnitt **Einstellungen** ermöglicht den Zugriff auf die folgenden Cacheeinstellungen und deren Konfiguration.

* [Zugriffsschlüssel](#access-keys)
* [Erweiterte Einstellungen](#advanced-settings)
* [Azure Cache for Redis-Ratgeber](#azure-cache-for-redis-advisor)
* [Skalieren](#scale)
* [Clustergröße](#cluster-size)
* [Datenpersistenz](#redis-data-persistence)
* [Planen von Updates](#schedule-updates)
* [Georeplikation](#geo-replication)
* [Virtual Network](#virtual-network)
* [Firewall](#firewall)
* [Eigenschaften](#properties)
* [Locks](#locks)
* [Automatisierungsskript](#automation-script)



### <a name="access-keys"></a>Zugriffsschlüssel
Klicken Sie auf **Zugriffsschlüssel** , um die Zugriffsschlüssel für Ihren Cache anzuzeigen oder neu zu generieren. Diese Schlüssel werden von den Clients verwendet, die eine Verbindung mit dem Cache herstellen.

![Azure Cache for Redis: Zugriffsschlüssel](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Erweiterte Einstellungen
Die folgenden Einstellungen werden auf dem Blatt **Erweiterte Einstellungen** konfiguriert.

* [Zugriffsports](#access-ports)
* [Arbeitsspeicherrichtlinien](#memory-policies)
* [Keyspacebenachrichtigungen (Erweiterte Einstellungen)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Zugriffsports
TLS-/SSL-fremder Zugriff ist für neue Caches standardmäßig deaktiviert. Klicken Sie zum Aktivieren des TLS-fremden Ports auf dem Blatt **Erweiterte Einstellungen** für **Nur Zugriff über SSL zulassen** auf **Nein** und anschließend auf **Speichern**.

> [!NOTE]
> Für den TLS-Zugriff auf Azure Cache for Redis werden derzeit TLS 1.0, 1.1 und 1.2 unterstützt. Die Versionen 1.0 und 1.1 werden jedoch bald eingestellt.  Weitere Informationen finden Sie auf unserer Seite [Entfernen von TLS 1.0 und 1.1](cache-remove-tls-10-11.md).

![Azure Cache for Redis: Zugriffsports](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Arbeitsspeicherrichtlinien
Mit den Einstellungen **Maxmemory policy** , **maxmemory-reserved** und **maxfragmentationmemory-reserved** auf dem Blatt **Erweiterte Einstellungen** konfigurieren Sie die Arbeitsspeicherrichtlinien für den Cache.

![Azure Cache for Redis: Maxmemory-Richtlinie](./media/cache-configure/redis-cache-maxmemory-policy.png)

Mit **Maxmemory policy** wird die Entfernungsrichtlinie für den Cache konfiguriert und die Auswahl zwischen den folgenden Entfernungsrichtlinien ermöglicht:

* `volatile-lru`:Dies ist die Standardrichtlinie für die Entfernung.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Weitere Informationen zu `maxmemory`-Richtlinien finden Sie unter [Eviction policies](https://redis.io/topics/lru-cache#eviction-policies) (Entfernungsrichtlinien).

Mit der Einstellung **maxmemory-reserved** wird die Arbeitsspeichermenge in MB prot Instanz in einem Cluster konfiguriert, die für andere Prozesse als Cacheprozesse reserviert ist, z. B. die Replikation während eines Failovers. Mit dem Festlegen dieses Werts können Sie dafür sorgen, dass Sie bei wechselnden Auslastungen eine konsistentere Redis-Servererfahrung erzielen. Für Workloads, die mit hohem Schreibaufwand verbunden sind, sollte ein höherer Wert festgelegt werden. Wenn Arbeitsspeicher für Vorgänge dieser Art reserviert ist, ist er nicht für die Speicherung zwischengespeicherter Daten verfügbar.

Mit der Einstellung **maxfragmentationmemory-reserved** wird der Arbeitsspeicher in MB pro Instanz in einem Cluster konfiguriert, der für die Speicherfragmentierung reserviert ist. Durch Festlegen dieses Werts können Sie eine konsistentere Redis-Servererfahrung erzielen, wenn der Cache voll bzw. beinahe voll und das Fragmentierungsverhältnis hoch ist. Wenn Arbeitsspeicher für Vorgänge dieser Art reserviert ist, ist er nicht für die Speicherung zwischengespeicherter Daten verfügbar.

Bei der Auswahl eines neuen Speicherreservierungswerts ( **maxmemory-reserved** oder **maxfragmentationmemory-reserved** ) sollte berücksichtigt werden, wie sich diese Änderung auf einen Cache auswirkt, der bereits mit einer großen Datenmenge ausgeführt wird. Beispiel: Wenn Ihr Cache über eine Kapazität von 53 GB verfügt und 49 GB an Daten enthält, ändern Sie den Reservierungswert in 8 GB, um den maximal verfügbaren Arbeitsspeicher für das System auf 45 GB zu verringern. Wenn entweder der aktuelle Wert für `used_memory` oder der Wert für `used_memory_rss` höher als der neue Grenzwert von 45 GB ist, muss das System Daten entfernen, bis sowohl `used_memory` als auch `used_memory_rss` unter 45 GB liegen. Durch die Entfernung können sich Serverauslastung und Arbeitsspeicherfragmentierung erhöhen. Weitere Informationen zu Cachemetriken, etwa `used_memory` und `used_memory_rss`, finden Sie unter [Verfügbare Metriken und Berichtsintervalle](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> Die Einstellungen **maxmemory-reserved** und **maxfragmentationmemory-reserved** sind nur für Standard- und Premium-Caches verfügbar.
>
>

#### <a name="keyspace-notifications-advanced-settings"></a>Keyspacebenachrichtigungen (Erweiterte Einstellungen)
Redis-Keyspacebenachrichtigungen werden auf dem Blatt **Erweiterte Einstellungen** konfiguriert. Mit Keyspacebenachrichtigungen können Clients Benachrichtigungen empfangen, wenn bestimmte Ereignisse eintreten.

![Azure Cache for Redis: Erweiterte Einstellungen](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Keyspacebenachrichtigungen und die Einstellung **notify-keyspace-events** sind nur für Caches vom Typ "Standard" und "Premium" verfügbar.
>
>

Weitere Informationen finden Sie unter [Redis-Keyspacebenachrichtigungen](https://redis.io/topics/notifications). Beispielcode finden Sie in der Datei [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) im [Hello world-Beispiel](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Azure Cache for Redis-Ratgeber
Das Blatt **Azure Cache for Redis-Ratgeber** zeigt Empfehlungen für Ihren Cache an. Während des normalen Betriebs werden keine Empfehlungen angezeigt.

![Screenshot: Anzeige der Empfehlungen.](./media/cache-configure/redis-cache-no-recommendations.png)

Wenn es während des Betriebs Ihres Caches zu Zuständen kommt wie hohe Speicherauslastung, Netzwerkbandbreite oder Serverauslastung, wird auf dem Blatt **Azure Cache for Redis** eine Warnung angezeigt.

![Screenshot: Anzeige der Warnungen im Abschnitt „Azure Cache for Redis“.](./media/cache-configure/redis-cache-recommendations-alert.png)

Weitere Informationen finden Sie auf dem Blatt **Empfehlungen** .

![Empfehlungen](./media/cache-configure/redis-cache-recommendations.png)

Sie können diese Metriken auf dem Blatt [Azure Cache for Redis](cache-how-to-monitor.md#monitoring-charts) in den Abschnitten [Überwachungsdiagramme](cache-how-to-monitor.md#usage-charts) und **Nutzungsdiagramme** überwachen.

Jeder Tarif hat verschiedene Limits für Clientverbindungen, Speicher und Bandbreite. Wenn Ihr Cache über einen längeren Zeitraum maximale Kapazität für diese Metriken erreicht, wird eine Empfehlung erstellt. Weitere Informationen zu den Metriken und Grenzwerten, die vom Tool **Empfehlungen** überprüft werden, finden Sie in der folgenden Tabelle:

| Azure Cache for Redis-Metrik | Weitere Informationen |
| --- | --- |
| Netzwerkbandbreiten-Nutzung |[Cacheleistung – verfügbare Bandbreite](cache-planning-faq.md#azure-cache-for-redis-performance) |
| Verbundene Clients |[Standardmäßige Redis-Serverkonfiguration – maxclients](#maxclients) |
| Serverauslastung |[Nutzungsdiagramme – Arbeitsauslastung des Redis-Servers](cache-how-to-monitor.md#usage-charts) |
| Speicherauslastung |[Cacheleistung – Größe](cache-planning-faq.md#azure-cache-for-redis-performance) |

Klicken Sie zum Upgraden Ihres Caches auf **Jetzt aktualisieren** , um den [Tarif](#scale) zu ändern und Ihren Cache zu skalieren. Weitere Informationen zur Tarifauswahl finden Sie unter [Auswählen der richtigen Ebene](cache-overview.md#choosing-the-right-tier).


### <a name="scale"></a>Skalieren
Klicken Sie auf **Staffelung** , um den Tarif für Ihren Cache anzuzeigen oder zu ändern. Weitere Informationen zur Skalierung finden Sie unter [Skalieren von Azure Cache for Redis](cache-how-to-scale.md).

![Azure Cache for Redis: Tarifpreise](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Redis-Clustergröße
Klicken Sie zum Ändern der Clustergröße für einen aktiven Premium-Cache mit aktiviertem Clustering auf **Clustergröße**.

![Clustergröße](./media/cache-configure/redis-cache-redis-cluster-size.png)

Um die Clustergröße zu ändern, verwenden Sie den Schieberegler, oder geben Sie im Textfeld **Shardanzahl** eine Zahl zwischen 1 und 10 ein, und klicken Sie zum Speichern auf **OK**.

> [!IMPORTANT]
> Redis-Clustering ist nur für Premium-Caches verfügbar. Weitere Informationen finden Sie unter [Konfigurieren von Clustern für Azure Cache for Redis vom Typ „Premium“](cache-how-to-premium-clustering.md).
>
>


### <a name="redis-data-persistence"></a>Redis-Datenpersistenz
Klicken Sie auf **Datenpersistenz** , um die Datenpersistenz für Ihren Premium-Cache zu aktivieren, zu deaktivieren oder zu konfigurieren. Azure Cache for Redis bietet Redis-Persistenz entweder per RDB-Persistenz oder per AOF-Persistenz.

Weitere Informationen finden Sie unter [Konfigurieren von Persistenz für Azure Cache for Redis vom Typ „Premium“](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> Redis-Datenpersistenz ist nur für Premium-Caches verfügbar.
>
>

### <a name="schedule-updates"></a>Planen von Updates
Auf dem Blatt **Updates planen** können Sie ein Wartungsfenster für Redis-Serverupdates für Ihren Cache bestimmen.

> [!IMPORTANT]
> Das Wartungsfenster bezieht sich nur auf Redis-Serverupdates und nicht auf Azure-Updates oder Updates des Betriebssystems der virtuellen Computer, die den Cache hosten.
>
>

![Planen von Updates](./media/cache-configure/redis-schedule-updates.png)

Aktivieren Sie zum Angeben eines Wartungsfensters die Kontrollkästchen der gewünschten Tage, geben Sie jeweils die Startzeit des Wartungsfensters an, und klicken Sie auf **OK**. Das Wartungsfenster wird als UTC angegeben.

> [!IMPORTANT]
> Die Funktion **Zeitplanaktualisierungen** ist nur für Caches im Premium-Tarif verfügbar. Weitere Informationen und Anweisungen finden Sie unter [Azure Cache for Redis-Verwaltung – Planen von Updates](cache-administration.md#schedule-updates).
>
>

### <a name="geo-replication"></a>Georeplikation

Das Blatt **Georeplikation** bietet einen Mechanismus zum Verknüpfen von zwei Azure Cache for Redis-Instanzen im Premium-Tarif. Ein Cache wird als primärer verknüpfter Cache festgelegt und der andere als sekundärer verknüpfter Cache. Der sekundäre verknüpfte Cache ist schreibgeschützt. Und die in den primären Cache geschriebenen Daten werden im sekundären verknüpften Cache repliziert. Über diese Funktion kann ein Cache über verschiedene Azure-Regionen hinweg repliziert werden.

> [!IMPORTANT]
> Die **Georeplikation** ist nur für Caches im Premium-Tarif verfügbar. Weitere Informationen und Anweisungen finden Sie unter [Konfigurieren der Georeplikation für Azure Cache for Redis](cache-how-to-geo-replication.md).
>
>

### <a name="virtual-network"></a>Virtual Network
Im Abschnitt **Virtuelles Netzwerk** können Sie die Einstellungen des virtuellen Netzwerks für Ihren Cache konfigurieren. Weitere Informationen zum Erstellen eines Premium-Caches mit VNET-Unterstützung sowie zum Aktualisieren der Einstellungen finden Sie unter [Konfigurieren der Unterstützung virtueller Netzwerke für Azure Cache for Redis vom Typ „Premium“](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> Einstellungen des virtuellen Netzwerks stehen nur für Premium-Caches zur Verfügung, die während der Erstellung des Caches mit VNET-Unterstützung konfiguriert wurden.
>
>

### <a name="firewall"></a>Firewall

Die Konfiguration von Firewall-Regeln ist für alle Azure Cache for Redis-Tarife verfügbar.

Klicken Sie auf **Firewall** , um Firewallregeln für den Cache anzuzeigen und zu konfigurieren.

![Firewall](./media/cache-configure/redis-firewall-rules.png)

Sie können Firewallregeln mit einem Start- und End-IP-Adressbereich angeben. Wenn Firewallregeln konfiguriert werden, können nur Clientverbindungen aus dem angegebenen IP-Adressbereich eine Verbindung mit dem Cache herstellen. Wenn eine Firewallregel gespeichert wird, gibt es eine kurze Verzögerung, bevor die Regel in Kraft tritt. Diese Verzögerung beträgt in der Regel weniger als eine Minute.

> [!IMPORTANT]
> Verbindungen von Azure Cache for Redis-Überwachungssystemen werden immer zugelassen, auch wenn Firewallregeln konfiguriert sind.
>
>

### <a name="properties"></a>Eigenschaften
Klicken Sie auf **Eigenschaften** , um Informationen zu Ihrem Cache anzuzeigen, z.B. den Endpunkt und die Ports des Caches.

![Azure Cache for Redis-Eigenschaften](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Locks
Im Abschnitt **Sperren** können Sie ein Abonnement, eine Ressourcengruppe oder eine Ressource sperren, um zu verhindern, dass andere Benutzer in Ihrer Organisation versehentlich wichtige Ressourcen löschen oder ändern. Weitere Informationen finden Sie unter [Sperren von Ressourcen mit dem Azure-Ressourcen-Manager](../azure-resource-manager/management/lock-resources.md).

### <a name="automation-script"></a>Automatisierungsskript

Klicken Sie auf **Automatisierungsskript** , um für künftige Bereitstellungen eine Vorlage Ihrer bereitgestellten Ressourcen zu erstellen und zu exportieren. Weitere Informationen zum Arbeiten mit Vorlagen finden Sie unter [Bereitstellen von Ressourcen mit einer Azure Resource Manager-Vorlage](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="administration-settings"></a>Verwaltungseinstellungen
Mit den Einstellungen im Abschnitt **Verwaltung** können Sie folgende Verwaltungsaufgaben für Ihren Cache durchführen.

![Verwaltung](./media/cache-configure/redis-cache-administration.png)

* [Importieren von Daten](#importexport)
* [Daten exportieren](#importexport)
* [Neustart](#reboot)


### <a name="importexport"></a>Importieren/Exportieren
Import/Export ist ein Vorgang der Azure Cache for Redis-Datenverwaltung, bei dem Sie Daten in den Cache importieren oder aus dem Cache exportieren können. Hierzu importieren bzw. exportieren Sie eine Momentaufnahme der Azure Cache for Redis-Datenbank (RDB) aus einem Premium-Cache in ein Seitenblob in einem Azure Storage-Konto. So können Sie zwischen verschiedenen Azure Cache for Redis-Instanzen migrieren oder den Cache vor der Nutzung mit Daten auffüllen.

Die Importfunktion kann verwendet werden, um Redis-kompatible RDB-Dateien von beliebigen Redis-Servern zu importieren, die in einer Cloud oder Umgebung ausgeführt werden, z.B. Redis unter Linux oder Windows oder bei einem Cloudanbieter wie Amazon Web Services und anderen. Das Importieren von Daten ist eine einfache Möglichkeit zum Erstellen eines Cache mit vorab aufgefüllten Daten. Während des Importvorgangs lädt Azure Cache for Redis die RDB-Dateien aus Azure Storage in den Arbeitsspeicher und fügt die Schlüssel anschließend in den Cache ein.

Mit der Exportfunktion können Sie die in Azure Cache for Redis gespeicherten Daten als Redis-kompatible RDB-Dateien exportieren. Sie können diese Funktion nutzen, um Daten von einer Azure Cache for Redis-Instanz zur anderen oder auf einen anderen Redis-Server zu verschieben. Während des Exportvorgangs wird auf dem virtuellen Computer, der die Azure Cache for Redis-Serverinstanz hostet, eine temporäre Datei erstellt, die dann in das angegebene Speicherkonto hochgeladen wird. Nachdem der Exportvorgang mit dem Status „Erfolg“ oder „Fehler“ abgeschlossen wurde, wird die temporäre Datei gelöscht.

> [!IMPORTANT]
> Import/Export ist nur für Caches im Premium-Tarif verfügbar. Weitere Informationen und Anweisungen finden Sie unter [Importieren und Exportieren von Daten in Azure Cache for Redis](cache-how-to-import-export-data.md).
>
>

### <a name="reboot"></a>Reboot
Auf dem Blatt **Neustart** können Sie die Knoten Ihres Caches neu starten. Mit dieser Neustartfunktion können Sie Ihre Anwendung bei einem Ausfall eines Cacheknotens auf Resilienz testen.

![Reboot](./media/cache-configure/redis-cache-reboot.png)

Wenn Sie über einen Premium-Cache mit aktiviertem Clustering verfügen, können Sie die Shards des Caches auswählen, die neu gestartet werden sollen.

![Screenshot: Auswählen der Shards des Caches, die neu gestartet werden sollen.](./media/cache-configure/redis-cache-reboot-cluster.png)

Zum Neustarten eines oder mehrerer Knoten Ihres Caches wählen Sie die gewünschten Knoten aus und klicken auf **Neustart**. Wenn Sie über einen Premium-Cache mit aktiviertem Clustering verfügen, wählen Sie die Shards aus, die neu gestartet werden sollen, und klicken Sie dann auf **Neustart**. Nach einigen Minuten werden die ausgewählten Knoten neu gestartet, die paar Minuten später wieder online sind.

> [!IMPORTANT]
> Ein Neustart ist nun für alle Tarife verfügbar. Weitere Informationen und Anweisungen finden Sie unter [Azure Cache for Redis-Verwaltung – Neustart](cache-administration.md#reboot).
>
>


## <a name="monitoring"></a>Überwachung

Im Abschnitt **Überwachung** können Sie die Diagnose und Überwachung für Ihren Azure Cache for Redis konfigurieren.
Weitere Informationen zur Azure Cache for Redis-Diagnose und -Überwachung finden Sie unter [Überwachen von Azure Cache for Redis](cache-how-to-monitor.md).

![Diagnose](./media/cache-configure/redis-cache-diagnostics.png)

* [Redis-Metriken](#redis-metrics)
* [Warnregeln](#alert-rules)
* [Diagnose](#diagnostics)

### <a name="redis-metrics"></a>Redis-Metriken
Klicken Sie auf **Redis Metriken** , um für Ihren Cache [Metriken anzuzeigen](cache-how-to-monitor.md#view-cache-metrics).

### <a name="alert-rules"></a>Warnregeln

Klicken Sie auf **Warnregeln** , um Warnungen basierend auf Azure Cache for Redis-Metriken zu konfigurieren. Weitere Informationen finden Sie unter [Warnungen](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnose

Cachemetriken werden in Azure Monitor standardmäßig [30 Tage lang gespeichert](../azure-monitor/platform/data-platform-metrics.md) und anschließend gelöscht. Um Cachemetriken länger als 30 Tage beizubehalten, klicken Sie zum **Konfigurieren des Speicherkontos** für Cachediagnosedaten auf [Diagnose](cache-how-to-monitor.md#export-cache-metrics).

>[!NOTE]
>Neben dem Archivieren der Cachemetriken im Speicher können Sie sie auch [an einen Event Hub streamen oder an Azure Monitor-Protokolle senden](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).
>
>

## <a name="support--troubleshooting-settings"></a>Einstellungen für Support und Problembehandlung
Die Einstellungen im Abschnitt **Support und Problembehandlung** bieten Optionen zur Behandlung von Problemen mit dem Cache.

![Support und Problembehandlung](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Ressourcenintegrität](#resource-health)
* [Neue Supportanfrage](#new-support-request)

### <a name="resource-health"></a>Ressourcenintegrität
**Ressourcenintegrität** dienen zum Überwachen Ihrer Ressource und informieren Sie darüber, ob sie wie erwartet ausgeführt wird. Weitere Informationen zum Azure Resource Health-Dienst finden Sie in der [Übersicht über Azure Resource Health](../service-health/resource-health-overview.md).

> [!NOTE]
> Die Ressourcenintegrität kann derzeit keine Informationen zur Integrität der in einem virtuellen Netzwerk gehosteten Azure Cache for Redis-Instanzen liefern. Weitere Informationen finden Sie unter [Funktionieren alle Cachefeatures beim Hosten eines Cache in einem VNET?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
>
>

### <a name="new-support-request"></a>Neue Supportanfrage
Klicken Sie auf **Neue Supportanfrage** , um eine Supportanfrage für Ihren Cache zu erstellen.





## <a name="default-redis-server-configuration"></a>Standardmäßige Redis-Serverkonfiguration
Neue Azure Cache for Redis-Instanzen werden mit den folgenden standardmäßigen Redis-Konfigurationswerten konfiguriert:

> [!NOTE]
> Die Einstellungen in diesem Abschnitt können mit der `StackExchange.Redis.IServer.ConfigSet`-Methode nicht geändert werden. Wenn diese Methode mit einem der Befehle in diesem Abschnitt aufgerufen wird, wird eine Ausnahme ausgelöst, die in etwa wie folgt lautet:  
>
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>
> Alle Werte, die konfigurierbar sind, z.B. **maxmemory-policy** , können über das Azure-Portal oder Befehlszeilenverwaltungstools wie die Azure-Befehlszeilenschnittstelle oder PowerShell konfiguriert werden.
>
>

| Einstellung | Standardwert | BESCHREIBUNG |
| --- | --- | --- |
| `databases` |16 |Die Standardanzahl von Datenbanken ist 16, Sie können aber basierend auf dem Tarif eine andere Anzahl konfigurieren.<sup>1</sup> Die Standarddatenbank ist „DB 0“. Sie können mithilfe von `connection.GetDatabase(dbid)` pro Verbindung eine andere Datenbank auswählen. Hierbei steht `dbid` für eine Zahl zwischen `0` und `databases - 1`. |
| `maxclients` |Tarifabhängig<sup>2</sup> |Dieser Wert ist die maximale Anzahl von verbundenen Clients, die gleichzeitig zulässig sind. Sobald der Grenzwert erreicht ist, schließt Redis alle neuen Verbindungen und gibt den Fehler „max number of clients reached“ (Maximale Anzahl von Clients erreicht) zurück. |
| `maxmemory-policy` |`volatile-lru` |Mit der Einstellung „maxmemory-policy“ wird festgelegt, was in Redis entfernt werden soll, wenn der Wert für `maxmemory` (Größe des Caches, die Sie beim Erstellen des Caches ausgewählt haben) erreicht ist. Bei Azure Cache for Redis lautet die Standardeinstellung `volatile-lru`. Hierbei werden die Schlüssel anhand eines Ablaufverfahrens mit LRU-Algorithmus entfernt. Diese Einstellung kann im Azure-Portal konfiguriert werden. Weitere Informationen finden Sie unter [Arbeitsspeicherrichtlinien](#memory-policies). |
| `maxmemory-samples` |3 |Zur Einsparung von Arbeitsspeicher sind LRU- und minimale TTL-Algorithmen keine präzisen Algorithmen, sondern angenäherte Algorithmen. Standardmäßig werden von Redis drei Schlüssel geprüft, und es wird der Schlüssel ausgewählt, der vor längerer Zeit verwendet wurde. |
| `lua-time-limit` |5\.000 |Maximale Ausführungszeit eines Lua-Skripts in Millisekunden. Wenn die maximale Ausführungszeit erreicht wird, protokolliert Redis, dass ein Skript nach der maximal zulässigen Ausführungszeit weiterhin ausgeführt wird. Es wird dann damit begonnen, auf Abfragen mit einem Fehler zu antworten. |
| `lua-event-limit` |500 |Maximale Größe der Skriptereigniswarteschlange. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |Die Clientausgabepuffer-Grenzwerte können verwendet werden, um die Verbindungstrennung für Clients zu erzwingen, die aus einem bestimmten Grund Daten nicht schnell genug vom Server lesen. Weitere Informationen finden Sie unter [https://redis.io/topics/clients](https://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup>Der Grenzwert für `databases` ist für jeden Azure Cache for Redis-Tarif unterschiedlich und kann bei der Erstellung des Caches festgelegt werden. Wenn bei der Cacheerstellung keine Einstellung für `databases` angegeben wird, wird der Standardwert (16) verwendet.

* Caches vom Typ „Basic“ und „Standard“
  * C0 (250 MB) – bis zu 16 Datenbanken
  * C1 (1 GB) – bis zu 16 Datenbanken
  * C2 (2,5 GB) – bis zu 16 Datenbanken
  * C3 (6 GB) – bis zu 16 Datenbanken
  * C4 (13 GB) – bis zu 32 Datenbanken
  * C5 (26 GB) – bis zu 48 Datenbanken
  * C6 (53 GB) – bis zu 64 Datenbanken
* Premium-Caches
  * P1 (6 bis 60 GB) – bis zu 16 Datenbanken
  * P2 (13 bis 130 GB) – bis zu 32 Datenbanken
  * P3 (26 bis 260 GB) – bis zu 48 Datenbanken
  * P4 (53 bis 530 GB) – bis zu 64 Datenbanken
  * Alle Premium-Caches mit aktiviertem Redis-Cluster: Ein Redis-Cluster unterstützt nur die Nutzung der Datenbank 0. Deshalb ist der `databases`-Grenzwert für Premium-Caches mit aktiviertem Redis-Cluster effektiv 1, und der Befehl [Auswählen](https://redis.io/commands/select) ist nicht zulässig. Weitere Informationen finden Sie unter [Muss ich Änderungen an meiner Clientanwendung vornehmen, um Clustering verwenden zu können?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Weitere Informationen zu Datenbanken finden Sie unter [Was sind Redis-Datenbanken?](cache-development-faq.md#what-are-redis-databases)

> [!NOTE]
> Mit den Einstellungen `databases` kann nur bei der Erstellung des Caches konfiguriert und nur mit PowerShell, der Befehlszeilenschnittstelle oder anderen Verwaltungsclients verwendet werden. Ein Beispiel für das Konfigurieren von `databases` bei der Cacheerstellung mithilfe von PowerShell finden Sie unter [New-AzRedisCache](cache-how-to-manage-redis-cache-powershell.md#databases).
>
>

<a name="maxclients"></a>
<sup>2</sup>`maxclients` ist für jeden Azure Cache for Redis-Tarif unterschiedlich.

* Caches vom Typ „Basic“ und „Standard“
  * Cache C0 (250 MB) – bis zu 256 Verbindungen
  * Cache C1 (1 GB) – bis zu 1.000 Verbindungen
  * Cache C2 (2,5 GB) – bis zu 2.000 Verbindungen
  * Cache C3 (6 GB) – bis zu 5.000 Verbindungen
  * Cache C4 (13 GB) – bis zu 10.000 Verbindungen
  * Cache C5 (26 GB) – bis zu 15.000 Verbindungen
  * Cache C6 (53 GB) – bis zu 20.000 Verbindungen
* Premium-Caches
  * P1 (6 GB - 60 GB) – bis zu 7.500 Verbindungen
  * P2 (13 GB - 130 GB) – bis zu 15.000 Verbindungen
  * P3 (26 GB - 260 GB) – bis zu 30.000 Verbindungen
  * P4 (53 GB - 530 GB) – bis zu 40.000 Verbindungen

> [!NOTE]
> Obgleich jede Cachegröße eine *bestimmte* Anzahl von Verbindungen zulässt, fällt für jede Verbindung ein Mehraufwand an. Ein Beispiel für einen solchen Aufwand ist die CPU- und Arbeitsspeicherauslastung aufgrund der TLS-/SSL-Verschlüsselung. Das maximale Verbindungslimit für eine angegebene Cachegröße geht von einem geringfügig ausgelasteten Cache aus. Wenn die Last des Verbindungsaufwands *plus* die Last von Clientvorgängen die Systemkapazität überschreiten, können im Cache Kapazitätsprobleme entstehen, auch wenn Sie das Verbindungslimit für die aktuelle Cachegröße nicht überschritten haben.
>
>



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Redis-Befehle, die in Azure Cache for Redis nicht unterstützt werden
> [!IMPORTANT]
> Da die Konfiguration und Verwaltung von Azure Cache for Redis-Instanzen von Microsoft verwaltet wird, sind die folgenden Befehle deaktiviert. Wenn Sie versuchen, sie aufzurufen, erhalten Sie etwa folgende Fehlermeldung: `"(error) ERR unknown command"`.
>
> * BGREWRITEAOF
> * BGSAVE
> * CONFIG
> * DEBUG
> * MIGRATE
> * SAVE
> * SHUTDOWN
> * SLAVEOF
> * CLUSTER: Clusterschreibbefehle sind deaktiviert, aber schreibgeschützte Clusterbefehle sind zulässig.
>
>

Weitere Informationen zu Redis-Befehlen finden Sie unter [https://redis.io/commands](https://redis.io/commands).

## <a name="redis-console"></a>Redis-Konsole
Über die **Redis-Konsole** , die im Azure-Portal für alle Cachetarife zur Verfügung steht, können Sie Befehle sicher auf Ihre Azure Cache for Redis-Instanzen anwenden.

> [!IMPORTANT]
> - Die Redis-Konsole kann nicht mit [VNET](cache-how-to-premium-vnet.md) verwendet werden. Wenn der Cache zu einem virtuellen Netzwerk gehört, haben nur Clients in diesem virtuellen Netzwerk Zugriff auf den Cache. Da die Redis-Konsole in Ihrem lokalen Browser ausgeführt wird, der sich außerhalb des VNET befindet, kann sie keine Verbindung mit Ihrem Cache herstellen.
> - Nicht alle Redis-Befehle werden in Azure Cache for Redis unterstützt. Eine Liste der Redis-Befehle, die für Azure Cache for Redis deaktiviert sind, finden Sie im vorherigen Abschnitt [Redis-Befehle, die in Azure Cache for Redis nicht unterstützt werden](#redis-commands-not-supported-in-azure-cache-for-redis). Weitere Informationen zu Redis-Befehlen finden Sie unter [https://redis.io/commands](https://redis.io/commands).
>
>

Um auf die Redis-Konsole zuzugreifen, klicken Sie auf dem Blatt **Azure Cache for Redis** auf **Konsole**.

![Screenshot mit hervorgehobener Schaltfläche „Konsole“.](./media/cache-configure/redis-console-menu.png)

Zum Anwenden von Befehlen auf Ihre Cache-Instanz geben Sie den gewünschten Befehl in der Konsole ein.

![Screenshot: Redis-Konsole mit Eingabebefehl und Ergebnissen.](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Verwenden der Redis-Konsole mit einem gruppierten Premium-Cache

Bei Verwendung der Redis-Konsole mit einem gruppierten Premium-Cache können Sie Befehle auf einen einzelnen Shard des Caches anwenden. Um einen Befehl auf einen bestimmten Shard anzuwenden, müssen Sie zunächst eine Verbindung mit dem gewünschten Shard herstellen, indem Sie in der Shard-Auswahl auf ihn klicken.

![Redis-Konsole](./media/cache-configure/redis-console-premium-cluster.png)

Wenn Sie auf einen Schlüssel zugreifen, der in einem anderen als dem verbundenen Shard gespeichert ist, wird eine Fehlermeldung ähnlich der folgenden angezeigt:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

In diesem Beispiel ist Shard 1 der ausgewählte Shard, `myKey` ist jedoch im Shard 0 gespeichert, wie im Teil `(shard 0)` der Fehlermeldung angegeben. In diesem Beispiel müssen Sie für den Zugriff auf `myKey` Shard 0 über die Shard-Auswahl auswählen und können dann den gewünschten Befehl anwenden.


## <a name="move-your-cache-to-a-new-subscription"></a>Verschieben Ihres Caches in ein neues Abonnement
Sie können Ihren Cache in ein neues Abonnement verschieben, indem Sie auf **Verschieben** klicken.

![Verschieben von Azure Cache for Redis](./media/cache-configure/redis-cache-move.png)

Informationen zum Verschieben von Ressourcen zwischen Ressourcengruppen und zwischen Abonnements finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Verwenden von Redis-Befehlen finden Sie unter [Wie führe ich Redis-Befehle aus?](cache-development-faq.md#how-can-i-run-redis-commands)