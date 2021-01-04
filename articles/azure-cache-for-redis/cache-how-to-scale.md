---
title: Skalieren von Azure Cache for Redis
description: Erfahren Sie, wie Sie Ihre Azure Cache for Redis-Instanzen über das Azure-Portal sowie mithilfe von Tools wie Azure PowerShell und der Azure-Befehlszeilenschnittstelle skalieren.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 04/11/2017
ms.openlocfilehash: 332233873bfbcb2ae77f5a70b4aaa5a6102cecec
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537846"
---
# <a name="how-to-scale-azure-cache-for-redis"></a>Skalieren von Azure Cache for Redis
Für Azure Cache for Redis stehen verschiedene Cacheangebote bereit, die Flexibilität bei der Auswahl von Cachegröße und -funktionen bieten. Nach dem Erstellen des Caches können Sie Größe und Tarif des Caches skalieren, wenn sich die Anforderungen Ihrer Anwendung ändern. In diesem Artikel wird erläutert, wie Sie Ihren Cache im Azure-Portal mithilfe von Tools wie Azure PowerShell und der Azure-Befehlszeilenschnittstelle skalieren.

## <a name="when-to-scale"></a>Zeitpunkt für die Skalierung
Sie können mithilfe der [Überwachungsfunktionen](cache-how-to-monitor.md) von Azure Cache for Redis die Integration und Leistung des Caches überwachen und ermitteln, ob der Cache skaliert werden muss. 

Überwachen Sie die folgenden Metriken überwachen, um herauszufinden, ob eine Skalierung notwendig ist.

* Redis-Serverlast
* Speicherauslastung
* Netzwerkbandbreite
* CPU-Auslastung

Wenn Sie feststellen, dass der Cache die Anforderungen der Anwendung nicht mehr erfüllt, können Sie in einen höheren oder niedrigeren Tarif wechseln, der zu Ihrer Anwendung passt. Weitere Informationen dazu, wie Sie ermitteln, welcher Cachetarif geeignet ist, finden Sie unter [Auswählen der richtigen Ebene](cache-overview.md#choosing-the-right-tier).

## <a name="scale-a-cache"></a>Skalieren eines Caches
Zum Skalieren Ihres Caches [navigieren Sie zum Cache](cache-configure.md#configure-azure-cache-for-redis-settings) im [Azure-Portal](https://portal.azure.com) und klicken im Menü **Ressource** auf **Skalieren** .

![Skalieren](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Wählen Sie auf dem Blatt **Tarif auswählen** den gewünschten Tarif aus, und klicken Sie auf **Auswählen** .

![Tarif][redis-cache-pricing-tier-blade]


Sie können mit den folgenden Einschränkungen zu einem anderen Tarif wechseln:

* Sie können keine Skalierung von einem höheren Tarif auf einen niedrigeren Tarif vornehmen.
  * Eine Skalierung von einem **Premium** -Cache auf einen niedrigeren **Standard** - oder **Basic** -Cache ist nicht möglich.
  * Ein **Standard** -Cache kann nicht auf einen niedrigeren **Basic** -Cache skaliert werden.
* Ein **Basic** -Cache kann auf einen **Standard** -Cache skaliert werden, die Größe kann jedoch nicht gleichzeitig geändert werden. Wenn Sie eine andere Größe benötigen, können Sie anschließend einen Skalierungsvorgang auf die gewünschte Größe durchführen.
* Ein **Basic** -Cache kann nicht direkt auf einen **Premium** -Cache skaliert werden. Skalieren Sie zunächst in einem ersten Skalierungsvorgang von **Basic** auf **Standard** und dann in einem nachfolgenden Skalierungsvorgang von **Standard** auf **Premium** .
* Von einer größeren Größe kann nicht auf **C0 (250 MB)** herunterskaliert werden.
 
Während der Cache in den neuen Tarif skaliert wird, wird auf dem Blatt **Azure Cache for Redis** der Status **Wird skaliert** angezeigt.

![Skalierung][redis-cache-scaling]

Wenn die Skalierung abgeschlossen ist, ändert sich der Status von **Wird skaliert** zu **Wird ausgeführt** .

## <a name="how-to-automate-a-scaling-operation"></a>Automatisieren eines Skalierungsvorgangs
Sie können Ihre Cache-Instanzen nicht nur über das Azure-Portal skalieren, sondern auch mithilfe der PowerShell-Cmdlets, über die Azure-Befehlszeilenschnittstelle (Azure-CLI) und mithilfe der Microsoft Azure-Verwaltungsbibliotheken (MAML). 

* [Skalieren mithilfe von PowerShell](#scale-using-powershell)
* [Skalieren über die Azure-Befehlszeilenschnittstelle](#scale-using-azure-cli)
* [Skalieren mithilfe der Microsoft Azure-Verwaltungsbibliotheken](#scale-using-maml)

### <a name="scale-using-powershell"></a>Skalieren mithilfe von PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Sie können die Azure Cache for Redis-Instanzen mithilfe von PowerShell skalieren. Dazu verwenden Sie das Cmdlet [Set-AzRedisCache](/powershell/module/az.rediscache/set-azrediscache) und ändern die Eigenschaften `Size`, `Sku` oder `ShardCount`. Das folgende Beispiel veranschaulicht, wie ein Cache mit Namen `myCache` zu einem 2,5-GB-Cache skaliert wird. 

```powershell
   Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

Weitere Informationen zum Skalieren mithilfe von PowerShell finden Sie unter [Skalieren eines Azure Cache for Redis mit PowerShell](cache-how-to-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Skalieren über die Azure-Befehlszeilenschnittstelle
Wenn Sie die Azure Cache for Redis-Instanzen über die Azure-Befehlszeilenschnittstelle (CLI) skalieren möchten, rufen Sie den Befehl `azure rediscache set` auf und übergeben die gewünschten Konfigurationsänderungen. Abhängig vom gewünschten Skalierungsvorgang zählen dazu die neue Größe, die SKU oder die Clustergröße.

Weitere Informationen zum Skalieren über die Azure-Befehlszeilenschnittstelle finden Sie unter [Ändern der Einstellungen eines vorhandenen Azure Cache for Redis](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Skalieren mithilfe der Microsoft Azure-Verwaltungsbibliotheken
Wenn Sie die Azure Cache for Redis-Instanzen mithilfe der [Microsoft Azure-Verwaltungsbibliotheken](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/) (MAML, Microsoft Azure Management Libraries) skalieren möchten, rufen Sie die Methode `IRedisOperations.CreateOrUpdate` auf und übergeben die neue Größe für `RedisProperties.SKU.Capacity`.

```csharp
    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }
```

Weitere Informationen finden Sie im Beispiel [Verwalten von Azure Cache for Redis mithilfe von Microsoft Azure-Verwaltungsbibliotheken](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML).

## <a name="scaling-faq"></a>Häufig gestellte Fragen zur Skalierung
Die folgende Liste enthält Antworten auf häufig gestellte Fragen zur Skalierung von Azure Cache for Redis-Instanzen.

* [Kann ich eine Skalierung auf einen Premium-Cache, aus diesem oder innerhalb von diesem vornehmen?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Muss ich nach dem Skalieren den Namen oder die Zugriffsschlüssel für den Cache ändern?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Wie funktioniert die Skalierung?](#how-does-scaling-work)
* [Gehen während der Skalierung Daten aus dem Cache verloren?](#will-i-lose-data-from-my-cache-during-scaling)
* [Hat die Skalierung Auswirkungen auf meine benutzerdefinierte Einstellung für Datenbanken?](#is-my-custom-databases-setting-affected-during-scaling)
* [Ist der Cache während der Skalierung verfügbar?](#will-my-cache-be-available-during-scaling)
* Warum kann bei konfigurierter geografischer Replikation meinen Cache nicht skalieren oder die Shards in einem Cluster nicht ändern?
* [Nicht unterstützte Vorgänge](#operations-that-are-not-supported)
* [Wie lange dauert die Skalierung?](#how-long-does-scaling-take)
* [Woher weiß ich, dass die Skalierung abgeschlossen ist?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Kann ich eine Skalierung auf einen Premium-Cache, aus diesem oder innerhalb von diesem vornehmen?
* Eine Skalierung von einem **Premium** -Cache auf die niedrigeren Tarife **Basic** oder **Standard** ist nicht möglich.
* Eine Skalierung von einem bestimmten **Premium** -Cachetarif zu einem anderen ist jedoch möglich.
* Ein **Basic** -Cache kann nicht direkt auf einen **Premium** -Cache skaliert werden. Skalieren Sie zunächst in einem ersten Skalierungsvorgang von **Basic** auf **Standard** und dann in einem nachfolgenden Skalierungsvorgang von **Standard** auf **Premium** .
* Wenn Sie beim Erstellen des **Premium** -Caches die Clusterunterstützung aktiviert haben, können Sie die [Clustergröße ändern](cache-how-to-premium-clustering.md#cluster-size). Wenn der Cache ohne aktiviertes Clustering erstellt wurde, können Sie das Clustering zu einem späteren Zeitpunkt konfigurieren.
  
  Weitere Informationen finden Sie unter [Konfigurieren von Clustern für Azure Cache for Redis vom Typ „Premium“](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Muss ich nach dem Skalieren den Namen oder die Zugriffsschlüssel für den Cache ändern?
Nein, Cachename und -schlüssel bleiben während eines Skalierungsvorgangs unverändert.

### <a name="how-does-scaling-work"></a>Wie funktioniert die Skalierung?
* Wenn ein **Basic** -Cache auf eine andere Größe skaliert wird, wird er heruntergefahren, und es wird ein neuer Cache mit der neuen Größe bereitgestellt. Während dieser Zeit ist der Cache nicht verfügbar, und alle Daten im Cache gehen verloren.
* Wenn ein **Basic** -Cache auf einen **Standard** -Cache skaliert wird, wird ein Replikatcache bereitgestellt, und die Daten werden aus dem primären Cache in den Replikatcache kopiert. Der Cache bleibt während des Skalierungsvorgangs verfügbar.
* Wenn ein **Standard** -Cache auf eine andere Größe oder auf einen **Premium** -Cache skaliert wird, wird eines der Replikate heruntergefahren und mit der neuen Größe erneut bereitgestellt, und die Daten werden übertragen. Anschließend führt das andere Replikat ein Failover durch, bevor es erneut bereitgestellt wird. Dieser Vorgang ähnelt dem, der beim Ausfall eines Cacheknotens durchgeführt wird.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Gehen während der Skalierung Daten aus dem Cache verloren?
* Wenn ein **Basic** -Cache auf eine neue Größe skaliert wird, gehen alle Daten verloren, und der Cache ist während des Skalierungsvorgangs nicht verfügbar.
* Wenn ein **Basic** -Cache auf einen **Standard** -Cache skaliert wird, werden die Daten im Cache in der Regel beibehalten.
* Wenn ein **Standard** -Cache auf eine größere Größe oder auf einen höheren Tarif bzw. ein **Premium** -Cache auf eine größere Größe skaliert wird, bleiben normalerweise alle Daten erhalten. Wenn Sie einen **Standard** -Cache oder einen **Premium** -Cache auf eine kleinere Größe herunterskalieren, können Daten verloren gehen, je nachdem, ob der neue Cache groß genug für alle bisher enthaltenen Daten ist. Wenn Daten beim Herunterskalieren verloren gehen, werden die Schlüssel mithilfe der Entfernungsrichtlinie [allkeys-lru](https://redis.io/topics/lru-cache) entfernt. 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Hat die Skalierung Auswirkungen auf meine benutzerdefinierte Einstellung für Datenbanken?
Wenn Sie bei der Cacheerstellung einen benutzerdefinierten Wert für die Einstellung `databases` konfiguriert haben, gelten bei einigen Tarifen andere [Grenzwerte für Datenbanken](cache-configure.md#databases). Hier sind einige Aspekte aufgeführt, die beim Skalieren in diesem Szenario wichtig sind:

* Wenn Sie auf einen Tarif mit einem niedrigeren `databases` -Grenzwert skalieren als beim aktuellen Tarif:
  * Wenn Sie die Standardanzahl für `databases` verwenden (sie beträgt bei allen Tarifen 16), gehen keine Daten verloren.
  * Wenn Sie eine benutzerdefinierte Anzahl für `databases` verwenden, die innerhalb der Grenzwerte für den Tarif liegt, auf den Sie skalieren, wird diese `databases`-Einstellung beibehalten, und es gehen keine Daten verloren.
  * Wenn Sie eine benutzerdefinierte Anzahl für `databases` verwenden, die die Grenzwerte des neuen Tarifs überschreitet, wird die `databases`-Einstellung auf die Grenzwerte des neuen Tarifs herabgesetzt, und alle Daten in den entfernten Datenbanken gehen verloren.
* Wenn Sie auf einen Tarif mit dem gleichen oder einem höheren `databases`-Grenzwert skalieren wie beim aktuellen Tarif, wird Ihre `databases`-Einstellung beibehalten, und es gehen keine Daten verloren.

Für Caches der Tarife „Standard“ und „Premium“ gilt zwar eine SLA (Service Level Agreement, Vereinbarung zum Servicelevel) von 99,9 % für die Verfügbarkeit, aber keine SLA zum Datenverlust.

### <a name="will-my-cache-be-available-during-scaling"></a>Ist der Cache während der Skalierung verfügbar?
* **Standard** - und **Premium** -Caches bleiben während des Skalierungsvorgangs verfügbar. Es können jedoch Verbindungsunterbrechungen während der Skalierung von Standard- und Premium-Caches und auch während der Skalierung von Basic- auf Standard-Caches auftreten. Diese Verbindungsunterbrechungen sollten voraussichtlich kurz sein, und die Redis-Clients sollten in der Lage sein, ihre Verbindung sofort erneut herzustellen.
* **Basic** -Caches sind während der Skalierung auf eine andere Größe offline. Basic-Caches bleiben bei einer Skalierung von **Basic** auf **Standard** verfügbar, es können aber kurze Verbindungsunterbrechungen auftreten. Wenn Verbindungsunterbrechungen auftreten, sollten die Redis-Clients in der Lage sein, ihre Verbindung sofort erneut herzustellen.


### <a name="scaling-limitations-with-geo-replication"></a>Skalierungseinschränkungen bei Verwendung der Georeplikation

Wenn Sie zwischen zwei Caches eine Verknüpfung für die Georeplikation hinzugefügt haben, können Sie keine Skalierungsvorgänge mehr initiieren oder die Anzahl von Shards in einem Cluster ändern. Wenn Sie diese Befehle verwenden möchten, müssen Sie zuerst die Verknüpfung für den Cache aufheben. Weitere Informationen finden Sie unter [Vorgehensweise zum Konfigurieren der Georeplikation für Azure Redis Cache](cache-how-to-geo-replication.md).


### <a name="operations-that-are-not-supported"></a>Nicht unterstützte Vorgänge
* Sie können keine Skalierung von einem höheren Tarif auf einen niedrigeren Tarif vornehmen.
  * Eine Skalierung von einem **Premium** -Cache auf einen niedrigeren **Standard** - oder **Basic** -Cache ist nicht möglich.
  * Ein **Standard** -Cache kann nicht auf einen niedrigeren **Basic** -Cache skaliert werden.
* Ein **Basic** -Cache kann auf einen **Standard** -Cache skaliert werden, die Größe kann jedoch nicht gleichzeitig geändert werden. Wenn Sie eine andere Größe benötigen, können Sie anschließend einen Skalierungsvorgang auf die gewünschte Größe durchführen.
* Ein **Basic** -Cache kann nicht direkt auf einen **Premium** -Cache skaliert werden. Skalieren Sie zunächst in einem ersten Skalierungsvorgang von **Basic** auf **Standard** und dann in einem nachfolgenden Skalierungsvorgang von **Standard** auf **Premium** .
* Von einer größeren Größe kann nicht auf **C0 (250 MB)** herunterskaliert werden.

Wenn bei einem Skalierungsvorgang ein Fehler auftritt, versucht der Dienst, den Vorgang rückgängig zu machen, und der Cache wird auf die ursprüngliche Größe zurückgesetzt.


### <a name="how-long-does-scaling-take"></a>Wie lange dauert die Skalierung?
Die Skalierungszeit hängt von der Datenmenge im Cache ab, wobei größere Datenmengen länger für den Abschluss benötigen. Die Skalierung dauert ca. 20 Minuten. Bei gruppierten Caches dauert die Skalierung ungefähr 20 Minuten pro Shard.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Woher weiß ich, dass die Skalierung abgeschlossen ist?
Im Azure-Portal können Sie den Fortschritt der Skalierung anzeigen. Wenn die Skalierung abgeschlossen ist, ändert sich der Status des Caches zu **Wird ausgeführt** .

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png