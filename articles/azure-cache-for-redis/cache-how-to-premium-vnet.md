---
title: Konfigurieren eines virtuellen Netzwerks – Azure Cache for Redis vom Typ „Premium“
description: Erfahren Sie, wie Sie die Unterstützung für virtuelle Netzwerke für Azure Cache for Redis-Instanzen im Premium-Tarif erstellen und verwalten.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: 5fd82105c94bb9be2d07c8843834465821acd8bc
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95803770"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>Konfigurieren der Unterstützung virtueller Netzwerke für Azure Cache for Redis vom Typ „Premium“
Für Azure Cache for Redis stehen verschiedene Cacheangebote bereit, die Flexibilität bei der Auswahl von Cachegröße und -features bieten. Dazu zählen auch Features des Premium-Tarifs wie die Unterstützung für Clustering, Persistenz und virtuelle Netzwerke. Ein VNet ist ein privates Netzwerk in der Cloud. Wenn eine Azure Cache for Redis-Instanz mit einem VNET konfiguriert wird, ist dieses nicht öffentlich adressierbar, und auf das VNET kann nur über virtuelle Computer und Anwendungen innerhalb des VNET zugegriffen werden. In diesem Artikel erfahren Sie, wie Sie die Unterstützung eines virtuellen Netzwerks für eine Azure Cache for Redis-Instanz vom Typ „Premium“ konfigurieren.

> [!NOTE]
> Azure Cache for Redis unterstützt sowohl klassische als auch Ressourcen-Manager-VNETs.
> 
> 

## <a name="why-vnet"></a>Gründe für ein VNet
Die Bereitstellung über [Azure Virtual Network (VNET)](https://azure.microsoft.com/services/virtual-network/) bietet ein höheres Maß an Sicherheit und Isolation sowohl für Ihre Azure Cache for Redis-Instanz als auch für Ihre Subnetze. Sie profitieren außerdem von Richtlinien für die Zugriffssteuerung sowie von weiteren Features zur Begrenzung des Zugriffs.

## <a name="virtual-network-support"></a>Unterstützung für virtuelle Netzwerke
Die Unterstützung für ein virtuelles Netzwerk (VNET) wird während der Erstellung des Caches auf dem Blatt **New Azure Cache for Redis** (Neue Azure Cache for Redis-Instanz) konfiguriert. 

1. Melden Sie sich zum Erstellen eines Premium-Caches beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie **Ressource erstellen** aus. Hinweis: Sie können Caches nicht nur über das Azure-Portal, sondern auch mithilfe von Resource Manager-Vorlagen, PowerShell oder der Azure CLI erstellen. Weitere Informationen zum Erstellen einer Azure Cache for Redis-Instanz finden Sie unter [Erstellen eines Caches](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Ressource erstellen.":::
   
2. Wählen Sie auf der Seite **Neu** die Option **Datenbanken** und dann **Azure Cache for Redis** aus.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Auswählen von „Azure Cache for Redis“.":::

3. Konfigurieren Sie auf der Seite **Neuer Redis Cache** die Einstellungen für den neuen Premium-Cache.
   
   | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-Name** | Geben Sie einen global eindeutigen Namen ein. | Der Cachename muss zwischen 1 und 63 Zeichen lang sein und darf nur Zahlen, Buchstaben und Bindestriche enthalten. Der Name muss mit einer Zahl oder einem Buchstaben beginnen und enden und darf keine aufeinanderfolgenden Bindestriche enthalten. Der *Hostname* Ihrer Cache-Instanz lautet *\<DNS name>.redis.cache.windows.net*. | 
   | **Abonnement** | Öffnen Sie die Dropdownliste, und wählen Sie Ihr Abonnement aus. | Das Abonnement, unter dem diese neue Azure Cache for Redis-Instanz erstellt wird. | 
   | **Ressourcengruppe** | Öffnen Sie die Dropdownliste, und wählen Sie eine Ressourcengruppe aus. Alternativ dazu wählen Sie **Neu erstellen** aus und geben einen Namen für eine neue Ressourcengruppe ein. | Der Name der Ressourcengruppe, in der Ihr Cache und weitere Ressourcen erstellt werden. Wenn Sie alle Ihre App-Ressourcen in einer Ressourcengruppe zusammenfassen, können Sie sie einfacher gemeinsam verwalten oder löschen. | 
   | **Location** | Öffnen Sie die Dropdownliste, und wählen Sie einen Standort aus. | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in der Nähe anderer Dienste aus, die Ihren Cache verwenden. |
   | **Cachetyp** | Öffnen Sie die Dropdownliste, und wählen Sie einen Premium-Cache aus, um Premium-Features zu konfigurieren. Weitere Informationen finden Sie unter [Azure Cache for Redis – Preise](https://azure.microsoft.com/pricing/details/cache/). |  Der Tarif bestimmt Größe, Leistung und verfügbare Features für den Cache. Weitere Informationen finden Sie unter [What is Azure Cache for Redis](cache-overview.md) (Was ist Azure Cache for Redis?). |

4. Wählen Sie die Registerkarte **Netzwerk** aus, oder klicken Sie unten auf der Seite auf die Schaltfläche **Netzwerk**.

5. Wählen Sie auf der Registerkarte **Netzwerk** die Option **Virtuelle Netzwerke** als Konnektivitätsmethode aus. Um ein neues virtuelles Netzwerk verwenden zu können, müssen Sie es zuerst erstellen. Befolgen Sie dazu die Anleitungen unter [Erstellen eines virtuellen Netzwerks über das Azure-Portal](../virtual-network/manage-virtual-network.md#create-a-virtual-network) und [Erstellen eines virtuellen Netzwerks (klassisch) über das Azure-Portal](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal). Kehren Sie anschließend zum Blatt **Neue Azure Cache for Redis-Instanz** zurück, um Ihrem Premium-Cache zu erstellen und zu konfigurieren.

   > [!IMPORTANT]
   > Beim Bereitstellen von Azure Cache for Redis in einem Ressourcen-Manager-VNET muss sich der Cache in einem dedizierten Subnetz befinden, das keine anderen Ressourcen außer Azure Cache for Redis-Instanzen enthält. Wird versucht, eine Azure Cache for Redis-Instanz in einem virtuellen Ressourcen-Manager-VNET in einem Subnetz bereitzustellen, das andere Ressourcen enthält, tritt bei der Bereitstellung ein Fehler auf.
   > 
   > 

   | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Virtual Network** | Öffnen Sie die Dropdownliste, und wählen Sie Ihr virtuelles Netzwerk aus. | Wählen Sie ein virtuelles Netzwerk aus, das sich im selben Abonnement und am selben Standort befindet wie der Cache. | 
   | **Subnetz** | Öffnen Sie die Dropdownliste, und wählen Sie Ihr Subnetz aus. | Der Adressbereich des Subnetzes in CIDR-Schreibweise (z. B. 192.168.1.0/24). Er muss innerhalb des Adressraums des virtuellen Netzwerks liegen. | 
   | **Statische IP-Adresse** | (Optional) Geben Sie eine statische IP-Adresse ein. | Wenn Sie keine statische IP-Adresse angeben, wird automatisch eine IP-Adresse ausgewählt. | 

   > [!IMPORTANT]
   > Einige IP-Adressen innerhalb jedes Subnetzes sind in Azure reserviert und können deshalb nicht genutzt werden. Die erste und letzte IP-Adresse der Subnetze sind aus Gründen der Protokollkonformität reserviert. Darüber hinaus sind drei weitere für Azure-Dienste verwendete IP-Adressen reserviert. Weitere Informationen finden Sie unter [Unterliegen die in den Subnetzen verwendeten IP-Adressen bestimmten Beschränkungen?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
   > 
   > Zusätzlich zu den IP-Adressen, die von der Azure-VNet-Infrastruktur verwendet werden, nutzt jede Redis-Instanz im Subnetz zwei IP-Adressen pro Shard und eine zusätzliche IP-Adresse für den Lastenausgleich. Ein nicht gruppierter Cache hat definitionsgemäß einen Shard.
   > 
   > 

6. Wählen Sie unten auf der Seite die Registerkarte **Weiter: Erweitert** aus, oder klicken Sie unten auf der Seite auf die Schaltfläche **Weiter: Erweitert**.

7. Konfigurieren Sie auf der Registerkarte **Erweitert** für eine Premium-Cache-Instanz die Einstellungen für einen Nicht-TLS-Port sowie für Clustering und Datenpersistenz. 

8. Wählen Sie die Registerkarte **Weiter: Tags** aus, oder klicken Sie unten auf der Seite auf die Schaltfläche **Weiter: Tags** (Weiter: Tags) aus.

9. Geben Sie optional auf der Registerkarte **Tags** den Namen und den Wert ein, wenn Sie die Ressource kategorisieren möchten. 

10. Klicken Sie auf **Überprüfen + erstellen**. Sie werden zur Registerkarte „Überprüfen und erstellen“ weitergeleitet, auf der Azure Ihre Konfiguration überprüft.

11. Wenn die grüne Meldung „Validierung erfolgreich“ angezeigt wird, wählen Sie **Erstellen** aus.

Es dauert eine Weile, bis der Cache erstellt wird. Sie können den Fortschritt auf der Seite **Übersicht** von Azure Cache for Redis überwachen. Wenn **Wird ausgeführt** als **Status** angezeigt wird, ist der Cache einsatzbereit. Nachdem der Cache erstellt wurde, können Sie die Konfiguration für das virtuelle Netzwerk anzeigen, indem Sie im **Ressourcenmenü** auf **Virtuelles Netzwerk** klicken.

![Virtuelles Netzwerk][redis-cache-vnet-info]

Um bei Verwenden eines VNET eine Verbindung mit Ihrer Azure Cache for Redis-Instanz herzustellen, geben Sie in der Verbindungszeichenfolge den Hostnamen Ihres Caches an (siehe das folgende Beispiel):

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

## <a name="azure-cache-for-redis-vnet-faq"></a>Häufig gestellte Fragen zu VNETs und Azure Cache for Redis
Die folgende Liste enthält Antworten auf häufig gestellte Fragen zur Skalierung von Azure Cache for Redis-Instanzen.

* Welche Probleme treten häufig bei einer fehlerhaften Konfiguration von Azure Cache for Redis und VNETs auf?
* [Wie kann ich sicherstellen, dass mein Cache in einem VNET funktioniert?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* Warum erhalte ich beim Versuch, eine Verbindung mit Azure Cache for Redis in einem VNET herzustellen, die Fehlermeldung, dass das Remotezertifikat ungültig ist?
* [Kann ich VNets mit einem Standard-Cache oder Basic-Cache verwenden?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Warum misslingt das Erstellen einer Azure Cache for Redis-Instanz in einigen Subnetzen, aber in anderen nicht?
* [Welche Anforderungen gelten für den Subnetzadressraum?](#what-are-the-subnet-address-space-requirements)
* [Funktionieren alle Cachefeatures beim Hosten eines Cache in einem VNET?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Welche Probleme treten häufig bei einer fehlerhaften Konfiguration von Azure Cache for Redis und VNETs auf?
Beim Hosten von Azure Cache for Redis in einem VNET werden die in den folgenden Tabellen angegebenen Ports verwendet. 

>[!IMPORTANT]
>Wenn diese Ports gesperrt sind, funktioniert der Cache möglicherweise nicht ordnungsgemäß. Eine bestehende Sperre für mindestens einen dieser Ports ist die häufigste Fehlkonfiguration, die beim Verwenden von Azure Cache for Redis in einem VNET auftritt.
> 
> 

- [Anforderungen für ausgehende Ports](#outbound-port-requirements)
- [Anforderungen für eingehende Ports](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Anforderungen für ausgehende Ports

Es liegen Anforderungen für neun ausgehende Ports vor. Ausgehende Anforderungen in diesen Bereichen sind entweder ausgehend zu anderen Diensten, die erforderlich sind, damit der Cache funktioniert, oder intern an das Redis-Subnetz für die Kommunikation zwischen Knoten. Bei der Georeplikation sind zusätzliche Anforderungen an ausgehenden Datenverkehr für die Kommunikation zwischen Subnetzen des primären Caches und des Replikatcaches vorhanden.

| Port(s) | Direction | Transportprotokoll | Zweck | Lokale IP | Remote-IP |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Ausgehend |TCP |Redis-Abhängigkeiten von Azure Storage/PKI (Internet) | (Redis-Subnetz) |* <sup>4</sup> |
| 443 | Ausgehend | TCP | Redis-Abhängigkeit von Azure Key Vault und Azure Monitor | (Redis-Subnetz) | AzureKeyVault, AzureMonitor <sup>1</sup> |
| 53 |Ausgehend |TCP/UDP |Redis-Abhängigkeiten von DNS (Internet/VNet) | (Redis-Subnetz) | 168.63.129.16 und 169.254.169.254 <sup>2</sup> und jeder benutzerdefinierte DNS-Server für das Subnetz <sup>3</sup> |
| 8443 |Ausgehend |TCP |Interne Kommunikation für Redis | (Redis-Subnetz) | (Redis-Subnetz) |
| 10221-10231 |Ausgehend |TCP |Interne Kommunikation für Redis | (Redis-Subnetz) | (Redis-Subnetz) |
| 20226 |Ausgehend |TCP |Interne Kommunikation für Redis | (Redis-Subnetz) |(Redis-Subnetz) |
| 13000-13999 |Ausgehend |TCP |Interne Kommunikation für Redis | (Redis-Subnetz) |(Redis-Subnetz) |
| 15000-15999 |Ausgehend |TCP |Interne Kommunikation für Redis und Georeplikation | (Redis-Subnetz) |(Redis-Subnetz) (Georeplikat pro Subnetz) |
| 6379-6380 |Ausgehend |TCP |Interne Kommunikation für Redis | (Redis-Subnetz) |(Redis-Subnetz) |

<sup>1</sup> Sie können die Diensttags „AzureKeyVault“ und „AzureMonitor“ mit Resource Manager-Netzwerksicherheitsgruppen verwenden.

<sup>2</sup> Diese IP-Adressen im Besitz von Microsoft werden für die Host-VM verwendet, die Azure DNS bereitstellt.

<sup>3</sup> Nicht erforderlich für Subnetze ohne benutzerdefinierten DNS-Server oder neuere Redis Cache-Instanzen, die benutzerdefiniertes DNS ignorieren

<sup>4</sup> Weitere Informationen finden Sie unter [Zusätzliche VNET-Netzwerkverbindungsanforderungen](#additional-vnet-network-connectivity-requirements).

#### <a name="geo-replication-peer-port-requirements"></a>Anforderungen an Peer-Ports für die Georeplikation

Wenn Sie Georeplikation zwischen Caches in virtuellen Azure-Netzwerken verwenden, beachten Sie, dass die empfohlene Konfiguration darin besteht, die Blockierung der Ports 15000–15999 für das gesamte Subnetz in eingehenden UND ausgehenden Richtungen für beide Caches zu aufzuheben, sodass alle Replikatkomponenten im Subnetz direkt miteinander kommunizieren können, auch im Falle eines zukünftigen geografischen Failovers.

#### <a name="inbound-port-requirements"></a>Anforderungen für eingehende Ports

Es liegen Anforderungen für acht eingehende Portbereiche vor. Eingehende Anforderungen in diesen Bereichen gehen entweder von anderen im gleichen VNET gehosteten Diensten ein oder erfolgen über die interne Kommunikation im Redis-Subnetz.

| Port(s) | Direction | Transportprotokoll | Zweck | Lokale IP | Remote-IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Eingehend |TCP |Clientkommunikation mit Redis, Azure-Lastenausgleich | (Redis-Subnetz) | (Redis-Subnetz), (Clientsubnetz), AzureLoadBalancer <sup>1</sup> |
| 8443 |Eingehend |TCP |Interne Kommunikation für Redis | (Redis-Subnetz) |(Redis-Subnetz) |
| 8500 |Eingehend |TCP/UDP |Azure-Lastenausgleich | (Redis-Subnetz) | AzureLoadBalancer |
| 10221-10231 |Eingehend |TCP |Clientkommunikation mit Redis-Clustern, interne Kommunikation für Redis | (Redis-Subnetz) |(Redis-Subnetz), AzureLoadBalancer, (Clientsubnetz) |
| 13000-13999 |Eingehend |TCP |Clientkommunikation mit Redis-Clustern, Azure-Lastenausgleich | (Redis-Subnetz) | (Redis-Subnetz), (Clientsubnetz), AzureLoadBalancer |
| 15000-15999 |Eingehend |TCP |Clientkommunikation mit Redis-Clustern, Azure-Lastenausgleich und Georeplikation | (Redis-Subnetz) | (Redis-Subnetz), (Clientsubnetz), AzureLoadBalancer, (Peersubnetz des Georeplikats) |
| 16001 |Eingehend |TCP/UDP |Azure-Lastenausgleich | (Redis-Subnetz) | AzureLoadBalancer |
| 20226 |Eingehend |TCP |Interne Kommunikation für Redis | (Redis-Subnetz) |(Redis-Subnetz) |

<sup>1</sup> Sie können für die Erstellung der NSG-Regeln das Diensttag „AzureLoadBalancer“ (bei Resource Manager- oder „AZURE_LOADBALANCER“ bei klassischen Bereitstellungen) verwenden.

#### <a name="additional-vnet-network-connectivity-requirements"></a>Zusätzliche VNET-Netzwerkverbindungsanforderungen

Es gibt Netzwerkverbindungsanforderungen für Azure Cache for Redis, die ursprünglich nicht von einem virtuellen Netzwerk erfüllt werden konnten. Azure Cache for Redis erfordert bei Verwendung in einem virtuellen Netzwerk, dass alle folgenden Voraussetzungen erfüllt sind.

* Ausgehende Netzwerkverbindungen mit Azure-Speicherendpunkten in der ganzen Welt. Dies umfasst sowohl Endpunkte, die sich in derselben Region wie die Azure Cache for Redis-Instanz befinden, als auch Speicherendpunkte in **anderen** Azure-Regionen. Azure Storage-Endpunkte werden unter den folgenden DNS-Domänen aufgelöst: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* und *file.core.windows.net*. 
* Ausgehende Netzwerkkonnektivität zu *ocsp.digicert.com*, *crl4.digicert.com*, *ocsp.msocsp.com*, *mscrl.microsoft.com*, *crl3.digicert.com*, *cacerts.digicert.com*, *oneocsp.microsoft.com* und *crl.microsoft.com*. Diese Verbindungen sind zur Unterstützung von TLS-/SSL-Funktionen erforderlich.
* Die DNS-Konfiguration für das virtuelle Netzwerk muss alle der zuvor genannten Endpunkte und Domänen auflösen können. Diese DNS-Anforderungen können erfüllt werden, indem Sie sicherstellen, dass eine gültige DNS-Infrastruktur für das virtuelle Netzwerk konfiguriert und beibehalten wird.
* Ausgehende Netzwerkkonnektivität zu den folgenden Azure Monitor-Endpunkten, die zu den folgenden DNS-Domänen auflösen: *shoebox2-black.shoebox2.metrics.nsatc.net*, *north-prod2.prod2.metrics.nsatc.net*, *azglobal-black.azglobal.metrics.nsatc.net*, *shoebox2-red.shoebox2.metrics.nsatc.net*, *east-prod2.prod2.metrics.nsatc.net*, *azglobal-red.azglobal.metrics.nsatc.net*.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Wie kann ich sicherstellen, dass mein Cache in einem VNET funktioniert?

>[!IMPORTANT]
>Wenn eine Verbindung mit einer in einem VNET gehosteten Azure Cache for Redis-Instanz hergestellt wird, müssen sich Ihre Cacheclients im selben VNET oder in einem VNET mit aktiviertem VNET-Peering innerhalb derselben Azure-Region befinden. Globales VNET-Peering wird zurzeit nicht unterstützt. Dies schließt alle Testanwendungen oder Diagnosepingtools ein. Unabhängig davon, wo die Clientanwendung gehostet wird, müssen Netzwerksicherheitsgruppen oder andere Netzwerkschichten so konfiguriert sein, dass der Netzwerkdatenverkehr des Clients die Redis-Instanz erreichen kann.
>
>

Nachdem die Portanforderungen wie im vorherigen Abschnitt beschrieben konfiguriert wurden, können Sie anhand der folgenden Schritte sicherstellen, dass Ihr Cache funktioniert.

- [Starten Sie alle Cacheknoten neu](cache-administration.md#reboot). Wenn keine der erforderlichen Cacheabhängigkeiten erreicht werden kann (wie unter [Anforderungen für eingehende Ports](cache-how-to-premium-vnet.md#inbound-port-requirements) und [Anforderungen für ausgehende Ports](cache-how-to-premium-vnet.md#outbound-port-requirements) dokumentiert), kann der Cache nicht neu gestartet werden.
- Nachdem die Cacheknoten neu gestartet wurden (wie durch den Cachestatus im Azure-Portal gemeldet wird), können Sie folgende Tests durchführen:
  - Pingen Sie mit [Tcping](https://www.elifulkerson.com/projects/tcping.php) den Cacheendpunkt (über Port 6380) von einem Computer, der sich im selben VNET wie der Cache befindet. Beispiel:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Wenn das Tool `tcping` meldet, dass der Port geöffnet ist, ist der Cache für die Verbindung über Clients im VNET verfügbar.

  - Eine weitere Möglichkeit zum Testen besteht darin, einen Testcacheclient (eventuell eine einfache Konsolenanwendung mit StackExchange.Redis) zu erstellen, der eine Verbindung mit dem Cache herstellt und einige Elemente aus dem Cache hinzufügt und abruft. Installieren Sie die Beispielclientanwendung auf einer VM, die sich im selben VNET wie der Cache befindet, und führen Sie sie aus, um die Konnektivität mit dem Cache zu überprüfen.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Warum erhalte ich beim Versuch, eine Verbindung mit Azure Cache for Redis in einem VNET herzustellen, die Fehlermeldung, dass das Remotezertifikat ungültig ist?

Beim Versuch, eine Verbindung mit Azure Cache for Redis in einem VNET herzustellen, wird ein Zertifikatvalidierungsfehler wie dieser angezeigt:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Die Ursache könnte sein, dass Sie sich über die IP-Adresse mit dem Host verbinden. Wir empfehlen die Verwendung des Hostnamens. Verwenden Sie also Folgendes:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Vermeiden Sie die Verwendung einer IP-Adresse, die der folgenden Verbindungszeichenfolge ähnelt:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Wenn Sie den DNS-Namen nicht auflösen können, enthalten einige Clientbibliotheken Konfigurationsoptionen wie `sslHost`, die vom StackExchange.Redis-Client bereitgestellt werden. Dadurch können Sie den für die Zertifikatsvalidierung verwendeten Hostnamen überschreiben. Beispiel:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Kann ich VNets mit einem Standard-Cache oder Basic-Cache verwenden?
VNets können nur mit Premium-Caches verwendet werden.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Warum misslingt das Erstellen einer Azure Cache for Redis-Instanz in einigen Subnetzen, aber in anderen nicht?
Wenn Sie eine Azure Cache for Redis-Instanz in einem VNET bereitstellen, muss sich der Cache in einem dedizierten Subnetz befinden, das keine anderen Ressourcentypen enthält. Wird versucht, eine Azure Cache for Redis-Instanz im Subnetz eines Resource Manager-VNET bereitzustellen, das andere Ressourcen enthält (z. B. Anwendungsgateways, ausgehende NAT usw.), tritt bei der Bereitstellung in der Regel ein Fehler auf. Bevor Sie eine neue Azure Cache for Redis-Instanz erstellen können, müssen Sie die vorhandenen anderen Ressourcentypen löschen.

Außerdem müssen Sie über genügend IP-Adressen im Subnetz verfügen.

### <a name="what-are-the-subnet-address-space-requirements"></a>Welche Anforderungen gelten für den Subnetzadressraum?
Einige IP-Adressen innerhalb jedes Subnetzes sind in Azure reserviert und können deshalb nicht genutzt werden. Die erste und letzte IP-Adresse der Subnetze sind aus Gründen der Protokollkonformität reserviert. Darüber hinaus sind drei weitere für Azure-Dienste verwendete IP-Adressen reserviert. Weitere Informationen finden Sie unter [Unterliegen die in den Subnetzen verwendeten IP-Adressen bestimmten Beschränkungen?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Zusätzlich zu den IP-Adressen, die von der Azure-VNET-Infrastruktur verwendet werden, verwendet jede Redis-Instanz im Subnetz zwei IP-Adressen pro Shard (plus zusätzliche IP-Adressen für ggf. vorhandene weitere Replikate) und eine zusätzliche IP-Adresse für den Lastenausgleich. Ein nicht gruppierter Cache hat definitionsgemäß einen Shard.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Funktionieren alle Cachefeatures beim Hosten eines Cache in einem VNET?
Wenn der Cache zu einem virtuellen Netzwerk gehört, haben nur Clients in diesem virtuellen Netzwerk Zugriff auf den Cache. Daher stehen zurzeit in diesem Fall folgende Cache-Verwaltungsfunktionen nicht zur Verfügung.

* Redis-Konsole: Da die Redis-Konsole in Ihrem lokalen Browser ausgeführt wird, der sich in der Regel auf einem Entwicklercomputer befindet, der nicht mit dem VNET verbunden ist, kann keine Verbindung mit dem Cache hergestellt werden.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>Verwenden von ExpressRoute mit Azure Cache for Redis

Kunden können eine [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) -Verbindung mit ihrer virtuellen Netzwerkinfrastruktur verbinden und so ihr lokales Netzwerks auf Azure ausdehnen. 

Standardmäßig führt eine neu erstellte ExpressRoute-Leitung in einem VNET keine erzwungenes Tunneling durch (Ankündigung einer Standardroute 0.0.0.0/0). Infolgedessen wird die ausgehende Internetkonnektivität direkt über das VNET zugelassen, und Clientanwendungen können eine Verbindung mit anderen Azure-Endpunkten, einschließlich Azure Cache for Redis, herstellen.

Eine gängige Kundenkonfiguration sieht hingegen die Verwendung eines erzwungenen Tunnelings (Ankündigung einer Standardroute) vor, das ausgehenden Internetdatenverkehr stattdessen zwingt, die lokale Infrastruktur zu durchlaufen. Dieser Datenverkehrsfluss unterbricht die Verbindung mit Azure Cache for Redis, wenn der ausgehende Datenverkehr anschließend lokal so blockiert wird, dass die Azure Cache for Redis-Instanz nicht mit ihren Abhängigkeiten kommunizieren kann.

Die Lösung besteht darin, mindestens eine benutzerdefinierte Route (User-Defined Routes, UDRs) in dem Subnetz zu definieren, in dem sich die Azure Cache for Redis-Instanz befindet. Eine benutzerdefinierte Route definiert subnetzspezifische Routen, die anstelle der Standardroute berücksichtigt werden.

Nach Möglichkeit sollte die folgende Konfiguration verwendet werden:

* Die ExpressRoute-Konfiguration kündigt 0.0.0.0/0 an und tunnelt standardmäßig allen ausgehenden Datenverkehr zwangsweise lokal.
* Die für das Subnetz mit der Azure Cache for Redis-Instanz geltende benutzerdefinierte Route definiert 0.0.0.0/0 mit einer funktionierenden Route für TCP/IP-Datenverkehr zum öffentlichen Internet, beispielsweise durch Festlegen des nächsten Hoptyps auf „Internet“.

Gemeinsam führen diese Schritte dazu, dass die benutzerdefinierte Route auf Subnetzebene Vorrang vor erzwungenen ExpressRoute-Tunneln hat. Dadurch wird ausgehender Internetzugriff aus der Azure Cache for Redis-Instanz sichergestellt.

Das Herstellen einer Verbindung mit einer Azure Cache for Redis-Instanz aus einer lokalen Anwendung über ExpressRoute ist aus Leistungsgründen kein typisches Nutzungsszenario (die beste Leistung erzielen Azure Cache for Redis-Clients, wenn sie sich in derselben Region wie die Azure Cache for Redis-Instanz befinden).

>[!IMPORTANT] 
>Die in einer UDR definierten Routen **müssen** ausreichend spezifisch sein, damit sie Vorrang vor allen von der ExpressRoute-Konfiguration angekündigten Routen erhalten. Im folgenden Beispiel wird der allgemeine Adressbereich 0.0.0.0/0 verwendet, der durch Routenankündigungen mit spezifischeren Adressbereichen versehentlich überschrieben werden kann.

>[!WARNING]  
>Azure Cache for Redis wird nicht mit ExpressRoute-Konfigurationen unterstützt, die **fälschlicherweise Routen „über Kreuz“ vom öffentlichen Peeringpfad zum privaten Peeringpfad ankündigen**. ExpressRoute-Konfigurationen, für die öffentliches Peering konfiguriert ist, erhalten Routenankündigungen von Microsoft für zahlreiche Microsoft Azure-IP-Adressbereiche. Werden diese Adressbereiche fälschlicherweise „über Kreuz“ im privaten Peeringpfad angekündigt, führt dies dazu, dass alle ausgehenden Netzwerkpakete aus dem Subnetz der Azure Cache for Redis-Instanz fälschlicherweise erzwungenermaßen zur lokalen Netzwerkinfrastruktur eines Kunden getunnelt werden. Dieser Netzwerkdatenfluss unterbricht Azure Cache for Redis. Die Lösung für dieses Problem besteht darin, „Über-Kreuz-Ankündigungen“ von Routen vom öffentlichen Peeringpfad zum privaten Peeringpfad zu verhindern.


Hintergrundinformationen zu benutzerdefinierten Routen finden Sie in dieser [Übersicht](../virtual-network/virtual-networks-udr-overview.md).

Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – technische Übersicht](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über Azure Cache for Redis-Features.

* [Premium-Dienstebenen für Azure Cache for Redis](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
