---
title: Azure-Dateisynchronisierung – Überlegungen zum Netzwerkbetrieb | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie das Netzwerk so konfigurieren, dass die Azure-Dateisynchronisierung zum lokalen Zwischenspeichern von Dateien verwendet wird.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 76d97e3312c1df51193d8a881f3ee07fcd155d75
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629358"
---
# <a name="azure-file-sync-networking-considerations"></a>Azure-Dateisynchronisierung – Überlegungen zum Netzwerkbetrieb
Sie können auf zwei Arten eine Verbindung mit einer Azure-Dateifreigabe herstellen:

- Sie greifen über die Protokolle SMB oder FileREST direkt auf die Freigabe zu. Dieses Zugriffsmuster kommt primär dann zum Einsatz, wenn so wenige lokale Server wie möglich einbezogen werden sollen.
- Sie erstellen mithilfe der Azure-Dateisynchronisierung einen Cache der Azure-Dateifreigabe auf einem lokalen Server (oder in einer Azure-VM) und greifen auf dem lokalen Server über ein Protokoll Ihrer Wahl (SMB, NFS, FTPS usw.) auf die gewünschten Daten der Dateifreigabe zu. Dieses Zugriffsmuster ist praktisch, weil es die Vorteile einer lokalen Leistung mit serverlos anfügbaren Diensten im Cloudmaßstab – z. B. Azure Backup – kombiniert.

In diesem Artikel wird beschrieben, wie Sie das Netzwerk konfigurieren, wenn Ihr Anwendungsfall eine Nutzung der Azure-Dateisynchronisierung zum lokalen Zwischenspeichern von Dateien statt des direkten Einbindens der Azure-Dateifreigabe über SMB erfordert. Weitere Informationen zu Überlegungen zum Netzwerkbetrieb für eine Azure Files-Bereitstellung finden Sie unter [Azure Files – Überlegungen zum Netzwerkbetrieb](storage-files-networking-overview.md).

Die Netzwerkkonfiguration für die Azure-Dateisynchronisierung umfasst zwei verschiedene Azure-Objekte: einen Speichersynchronisierungsdienst und ein Azure-Speicherkonto. Ein Speicherkonto ist ein Verwaltungskonstrukt, das einen gemeinsam genutzten Pool mit Speicherplatz darstellt, in dem Sie mehrere Dateifreigaben sowie weitere Speicherressourcen wie Blobcontainer oder Warteschlangen bereitstellen können. Ein Speichersynchronisierungsdienst ist ein Verwaltungskonstrukt für registrierte Server. Hierbei handelt es sich um Windows-Dateiserver mit einer etablierten Vertrauensstellung mit der Azure-Dateisynchronisierung und mit Synchronisierungsgruppen, mit denen die Topologie der Synchronisierungsbeziehung definiert wird. 

## <a name="connecting-windows-file-server-to-azure-with-azure-file-sync"></a>Herstellen einer Verbindung zwischen Windows-Dateiserver und Azure mit Azure-Dateisynchronisierung 
Zum Einrichten und Verwenden von Azure Files und Azure-Dateisynchronisierung mit einem lokalen Windows-Dateiserver sind außer einer grundlegenden Internetverbindung keine speziellen Netzwerkfunktionen für Azure erforderlich. Zum Bereitstellen der Azure-Dateisynchronisierung installieren Sie den Azure-Dateisynchronisierung-Agent auf dem Windows-Dateiserver, den Sie mit Azure synchronisieren möchten. Der Azure-Dateisynchronisierung-Agent erreicht die Synchronisierung mit einer Azure-Dateifreigabe über zwei Kanäle:

- Das FileREST-Protokoll, ein HTTPS-basiertes Protokoll für den Zugriff auf Ihre Azure-Dateifreigabe. Da das FileREST-Protokoll den HTTPS-Standard für die Datenübertragung verwendet, muss ausgehend nur auf den Port 443 zugegriffen werden. Die Azure-Dateisynchronisierung verwendet nicht das SMB-Protokoll, um Daten von Ihren lokalen Windows-Servern auf Ihre Azure-Dateifreigabe zu übertragen.
- Das Protokoll der Azure-Dateisynchronisierung, ein HTTPS-basiertes Protokoll zum Austauschen von Synchronisierungskenntnissen, d. h. der Versionsinformationen über die Dateien und Ordner in Ihrer Umgebung, zwischen Endpunkten in Ihrer Umgebung. Dieses Protokoll wird auch zum Austauschen von Metadaten wie Zeitstempel und Zugriffssteuerungslisten (Access Control Lists, ACLs) über die Dateien und Ordner in Ihrer Umgebung verwendet. 

Da Azure Files direkten SMB-Protokollzugriff auf Azure-Dateifreigaben bietet, fragen Kunden sich häufig, ob sie spezielle Netzwerkfunktionen zum Einbinden der Azure-Dateifreigaben mit SMB konfigurieren müssen, damit der Azure-Dateisynchronisierung-Agent darauf zugreifen kann. Dies ist nicht nur überflüssig, sondern Administratorszenarios ausgenommen wird auch davon abgeraten, da direkt an der Azure-Dateifreigabe vorgenommene Änderungen nicht schnell erkannt werden können (je nach Größe und Anzahl der Elemente in der Azure-Dateifreigabe werden die Änderungen möglicherweise erst nach mehr als 24 Stunden erkannt). Wenn Sie die Azure-Dateifreigabe direkt verwenden, also nicht mit der Azure-Dateisynchronisierung lokal zwischenspeichern möchten, finden Sie weitere Informationen zu den Netzwerküberlegungen für den direkten Zugriff unter [Azure Files – Überlegungen zum Netzwerkbetrieb](storage-files-networking-overview.md).

Obwohl die Azure-Dateisynchronisierung keine besondere Netzwerkkonfiguration erfordert, möchten einige Kunden möglicherweise erweiterte Netzwerkeinstellungen konfigurieren, um die folgenden Szenarien zu ermöglichen:

- Interoperabilität mit der Proxyserverkonfiguration Ihrer Organisation.
- Öffnen Sie die lokale Firewall Ihrer Organisation für die Dienste Azure Files und Azure-Dateisynchronisierung.
- Tunneln Sie Azure Files und Azure-Dateisynchronisierung über ExpressRoute oder eine VPN-Verbindung.

### <a name="configuring-proxy-servers"></a>Konfigurieren von Proxyservern
Viele Organisationen verwenden einen Proxyserver als Vermittler zwischen Ressourcen innerhalb Ihres lokalen Netzwerks und Ressourcen außerhalb Ihres Netzwerks, z. B. in Azure. Proxyserver sind für viele Anwendungen wie Netzwerkisolation und Sicherheit sowie Überwachung und Protokollierung nützlich. Die Azure-Dateisynchronisierung kann vollständig mit einem Proxyserver zusammenarbeiten. Sie müssen jedoch die Proxyendpunkt-Einstellungen für Ihre Umgebung mit der Azure-Dateisynchronisierung manuell konfigurieren. Dies muss über PowerShell mithilfe der Server-Cmdlets der Azure-Dateisynchronisierung erfolgen. 

Weitere Informationen zum Konfigurieren der Azure-Dateisynchronisierung mit einem Proxyserver finden Sie unter [Proxy- und Firewalleinstellungen der Azure-Dateisynchronisierung](storage-sync-files-firewall-and-proxy.md).

### <a name="configuring-firewalls-and-service-tags"></a>Konfigurieren von Firewalls und Diensttags
Sie können Ihre Dateiserver aus Sicherheitsgründen von den meisten Internetstandorten isolieren. Um die Azure-Dateisynchronisierung in Ihrer Umgebung verwenden zu können, müssen Sie die Firewall so anpassen, dass sie für ausgewählte Azure-Dienste geöffnet ist. Dazu rufen Sie die IP-Adressbereiche für die benötigten Dienste über einen Mechanismus namens [Diensttags](../../virtual-network/service-tags-overview.md) ab.

Die Azure-Dateisynchronisierung benötigt die IP-Adressbereiche für die folgenden Dienste, wie durch die Diensttags angegeben:

| Dienst | BESCHREIBUNG | Diensttag |
|---------|-------------|-------------|
| Azure-Dateisynchronisierung | Der durch das Speichersynchronisierungsdienst-Objekt dargestellte Azure-Dateisynchronisierung-Dienst ist verantwortlich für die Hauptaktivität der Synchronisierung von Daten zwischen einer Azure-Dateifreigabe und einem Windows-Dateiserver. | `StorageSyncService` |
| Azure Files | Alle über die Azure-Dateisynchronisierung synchronisierten Daten werden in der Azure-Dateifreigabe gespeichert. Dateien, die auf den Windows-Dateiservern geändert werden, werden auf Ihre Azure-Dateifreigabe repliziert, und auf dem lokalen Dateiserver gespeicherte Dateien werden bei Anforderung durch einen Benutzer nahtlos heruntergeladen. | `Storage` |
| Azure Resource Manager | Der Azure Resource Manager ist die Verwaltungsschnittstelle für Azure. Alle Verwaltungsaufrufe einschließlich Azure-Dateisynchronisierung-Serverregistrierung und laufender Synchronisierungsservertasks werden über den Azure Resource Manager durchgeführt. | `AzureResourceManager` |
| Azure Active Directory | Azure Active Directory (Azure AD) enthält die Benutzerprinzipale, die zum Autorisieren der Serverregistrierung bei einem Speichersynchronisierungsdienst erforderlich sind, und die Dienstprinzipale, die die Azure-Dateisynchronisierung für die Autorisierung zum Zugriff auf Ihre Cloudressourcen benötigt. | `AzureActiveDirectory` |

Wenn Sie die Azure-Dateisynchronisierung in Azure verwenden, auch in einer anderen Region, können Sie den Namen des Diensttags direkt in der Netzwerksicherheitsgruppe verwenden, um Datenverkehr zu diesem Dienst zuzulassen. Weitere Informationen zur Vorgehensweise finden Sie unter [Netzwerksicherheitsgruppen](../../virtual-network/network-security-groups-overview.md). 

Wenn Sie die Azure-Dateisynchronisierung lokal verwenden, können Sie die Diensttag-API verwenden, um bestimmte IP-Adressbereiche für die Zulassungsliste Ihrer Firewall abzurufen. Es gibt zwei Methoden zum Abrufen dieser Informationen:

- Die aktuelle Liste der IP-Adressbereiche für alle Azure-Dienste, die Diensttags unterstützen, wird wöchentlich im Microsoft Download Center in Form eines JSON-Dokuments veröffentlicht. Es gibt für jede Azure-Cloud ein eigenes JSON-Dokument mit den IP-Adressbereichen, die für diese Cloud relevant sind:
    - [Azure öffentlich](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure China](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Deutschland](https://www.microsoft.com/download/details.aspx?id=57064)
- Die Diensttagermittlungs-API (Vorschauversion) ermöglicht das programmgesteuerte Abrufen der aktuellen Liste von Diensttags. In der Vorschauversion gibt die Diensttagermittlungs-API eventuell weniger aktuelle Informationen zurück, als in den im Microsoft Download Center veröffentlichten JSON-Dokumenten enthalten sind. Sie können je nach Ihren Automatisierungsvorlieben auch die API-Benutzeroberfläche verwenden:
    - [REST-API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure-Befehlszeilenschnittstelle](/cli/azure/network#az-network-list-service-tags)

Weitere Informationen zur Verwendung der Diensttag-API zum Abrufen der Adressen Ihrer Dienste finden Sie unter [Zulassungsliste für Azure-Dateisynchronisierungs-IP-Adressen](storage-sync-files-firewall-and-proxy.md#allow-list-for-azure-file-sync-ip-addresses).

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Tunneln von Datenverkehr über ein virtuelles privates Netzwerk oder über ExpressRoute
Einige Organisationen setzen voraus, dass die Kommunikation mit Azure über einen Netzwerktunnel erfolgt, z. B. ein virtuelles privates Netzwerk (VPN) oder ExpressRoute, um eine zusätzliche Sicherheitsebene zu gewährleisten oder sicherzustellen, dass die Kommunikation mit Azure einer deterministischen Route folgt. 

Wenn Sie einen Netzwerktunnel zwischen Ihrem lokalen Netzwerk und Azure einrichten, entsteht eine Peeringbeziehung zwischen Ihrem lokalen Netzwerk und mindestens einem virtuellen Netzwerk in Azure. Ein [virtuelles Netzwerk](../../virtual-network/virtual-networks-overview.md) (VNET) ähnelt einem herkömmlichen Netzwerk in Ihrer lokalen Umgebung. Ähnlich wie ein Azure-Speicherkonto oder eine Azure-VM ist ein VNET eine Azure-Ressource, die in einer Ressourcengruppe bereitgestellt wird. 

Azure Files und Azure-Dateisynchronisierung unterstützen folgende Mechanismen, um Datenverkehr zwischen Ihren lokalen Servern und Azure zu tunneln:

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): Ein VPN-Gateway ist eine spezielle Art von Gateway für virtuelle Netzwerke, das verwendet wird, um verschlüsselten Datenverkehr zwischen einem virtuellen Azure-Netzwerk und einem anderen Standort (beispielsweise einer lokalen Umgebung) über das Internet zu senden. Azure VPN Gateway ist eine Azure-Ressource, die neben einem Speicherkonto oder anderen Azure-Ressourcen in einer Ressourcengruppe bereitgestellt werden kann. Da die Azure-Dateisynchronisierung mit einem lokalen Windows-Dateiserver verwendet werden soll, verwenden Sie normalerweise ein [Site-to-Site-VPN (S2S)](../../vpn-gateway/design.md#s2smulti), obwohl es technisch möglich ist, ein [Point-to-Site-VPN (P2S)](../../vpn-gateway/point-to-site-about.md) zu verwenden. 

    Site-to-Site-VPN-Verbindungen (S2S) verbinden Ihr virtuelles Azure-Netzwerk und das lokale Netzwerk Ihrer Organisation. Mit dieser Lösung können Sie einmalig eine VPN-Verbindung für einen VPN-Server oder ein im Netzwerk Ihrer Organisation gehostetes Gerät konfigurieren und müssen diese Einrichtung nicht für jedes Clientgerät wiederholen, das auf Ihre Azure-Dateifreigabe zugreifen muss. Informationen zur Vereinfachung der Bereitstellung einer S2S-VPN-Verbindung finden Sie unter [Konfigurieren eines Site-to-Site-VPN zur Verwendung mit Azure Files](storage-files-configure-s2s-vpn.md).

- [ExpressRoute](../../expressroute/expressroute-introduction.md) ermöglicht die Erstellung einer definierten Route zwischen Azure und Ihrem lokalen Netzwerk, die nicht über das Internet läuft. Da ExpressRoute einen dedizierten Pfad zwischen Ihrem lokalen Rechenzentrum und Azure bereitstellt, ist dieser Dienst sehr nützlich, wenn die Netzwerkleistung ein wichtiger Aspekt ist. ExpressRoute ist auch dann eine gute Option, wenn die Richtlinie Ihrer Organisation oder gesetzliche Vorschriften einen deterministischen Pfad zu den Ressourcen in der Cloud erfordern.

### <a name="private-endpoints"></a>Private Endpunkte
Zusätzlich zu den standardmäßigen öffentlichen Endpunkten, die Azure Files und die Dateisynchronisierung über das Speicherkonto und den Speichersynchronisierungsdienst bereitstellen, bieten Azure Files und die Dateisynchronisierung die Möglichkeit, pro Ressource über einen oder mehrere private Endpunkte zu verfügen. Wenn Sie einen privaten Endpunkt für eine Azure-Ressource erstellen, erhält sie eine private IP-Adresse aus dem Adressraum Ihres virtuellen Netzwerks. Dies ist vergleichbar mit einem lokalen Windows-Dateiserver, der eine IP-Adresse aus dem dedizierten Adressraum Ihres lokalen Netzwerks erhält. 

> [!Important]  
> Um private Endpunkte für die Speichersynchronisierungsdienst-Ressource verwenden zu können, müssen Sie mindestens Version 10.1 des Azure-Dateisynchronisierungs-Agent nutzen. Für Agent-Versionen, die älter als 10.1 sind, werden private Endpunkte unter dem Speichersynchronisierungsdienst nicht unterstützt. Alle früheren Agent-Versionen unterstützen private Endpunkte für die Speicherkontoressource.

Ein privater Endpunkt ist einem bestimmten Subnetz des virtuellen Azure-Netzwerks zugeordnet. Speicherkonten und Speichersynchronisierungsdienste können über private Endpunkte in mehreren virtuellen Netzwerken verfügen.

Die Verwendung privater Endpunkte ermöglicht Folgendes:
- Herstellen einer sicheren Verbindung mit Ihren Azure-Ressourcen aus lokalen Netzwerken über eine VPN- oder ExpressRoute-Verbindung mit privatem Peering.
- Sichern Ihrer Azure-Ressourcen durch Deaktivieren der öffentlichen Endpunkte für Azure Files und Dateisynchronisierung. Durch die Erstellung eines privaten Endpunkts werden Verbindungen mit dem öffentlichen Endpunkt nicht standardmäßig blockiert.
- Erhöhen der Sicherheit für das virtuelle Netzwerk durch die Möglichkeit zum Blockieren der Exfiltration von Daten aus dem virtuellen Netzwerk (und Peeringgrenzen)

Informationen zum Erstellen eines privaten Endpunkts finden Sie unter [Konfigurieren von Netzwerkendpunkten für die Azure-Dateisynchronisierung](storage-sync-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Private Endpunkte und DNS
Wenn Sie einen privaten Endpunkt erstellen, wird standardmäßig auch eine private DNS-Zone erstellt, die der Unterdomäne `privatelink` entspricht, oder es wird eine vorhandene private DNS-Zone entsprechend aktualisiert. Für öffentliche Cloudregionen sind diese DNS-Zonen `privatelink.file.core.windows.net` für Azure Files und `privatelink.afs.azure.net` für die Azure-Dateisynchronisierung.

> [!Note]  
> In diesem Artikel wird das Speicherkonto-DNS-Suffix für die öffentlichen Azure-Regionen (`core.windows.net`) verwendet. Dieser Kommentar gilt auch für Azure Sovereign Clouds wie etwa die Azure US Government-Cloud und die Azure China-Cloud. Verwenden Sie einfach die entsprechenden Suffixe für Ihre Umgebung. 

Wenn Sie private Endpunkte für ein Speicherkonto und einen Speichersynchronisierungsdienst erstellen, erstellen wir für diese A-Datensätze in ihren jeweiligen privaten DNS-Zonen. Außerdem aktualisieren wir den öffentlichen DNS-Eintrag so, dass die regulären voll qualifizierten Domänennamen CNAMEs für den relevanten PrivateLink-Namen sind. So können die vollqualifizierten Domänennamen auf die IP-Adress(en) des privaten Endpunkts verweisen, wenn sich der Anforderer innerhalb des virtuellen Netzwerks befindet, und auf die IP-Adress(en) des öffentlichen Endpunkts, wenn der Anforderer sich außerhalb des virtuellen Netzwerks befindet. 

Bei Azure Files verfügt jeder private Endpunkt über einen einzelnen, dem Muster `storageaccount.privatelink.file.core.windows.net` folgenden vollqualifizierten Domänennamen, der einer privaten IP-Adresse für den privaten Endpunkt zugeordnet ist. Für die Azure-Dateisynchronisierung hat jeder private Endpunkt vier vollständig qualifizierte Domänennamen für die vier verschiedenen Endpunkte, die die Azure-Dateisynchronisierung verfügbar macht: Verwaltung, Synchronisierung (primär), Synchronisierung (sekundär) und Überwachung. Die vollqualifizierten Domänennamen für diese Endpunkte folgen normalerweise dem Namen des Speichersynchronisierungsdiensts, d. h. sofern der Name keine Nicht-ASCII-Zeichen enthält. Wenn der Name Ihres Speichersynchronisierungsdiensts beispielsweise `mysyncservice` in der Region „USA, Westen 2“ ist, wären die entsprechenden Endpunkte `mysyncservicemanagement.westus2.afs.azure.net`, `mysyncservicesyncp.westus2.afs.azure.net`, `mysyncservicesyncs.westus2.afs.azure.net` und `mysyncservicemonitoring.westus2.afs.azure.net`. Jeder private Endpunkt für einen Speichersynchronisierungsdienst enthält 4 verschiedene IP-Adressen. 

Da Ihre private Azure-DNS-Zone mit dem virtuellen Netzwerk verbunden ist, das den privaten Endpunkt enthält, können Sie sich die DNS-Konfiguration ansehen, indem Sie das Cmdlet `Resolve-DnsName` über PowerShell auf einem virtuellen Azure-Computer aufrufen (oder `nslookup` unter Windows und Linux):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

In diesem Beispiel wird das Speicherkonto `storageaccount.file.core.windows.net` in die private IP-Adresse des privaten Endpunkts (`192.168.0.4`) aufgelöst.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

Wenn Sie den gleichen Befehl lokal ausführen, sehen Sie, dass der gleiche Speicherkontoname stattdessen in die öffentliche IP-Adresse des Speicherkontos aufgelöst wird. `storageaccount.file.core.windows.net` ist ein CNAME-Eintrag für `storageaccount.privatelink.file.core.windows.net`, was wiederum ein CNAME-Eintrag für den Azure-Speichercluster ist, von dem das Speicherkonto gehostet wird:

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

Dies spiegelt die Tatsache wider, dass Azure Files und Azure-Dateisynchronisierung sowohl ihre öffentlichen Endpunkte als auch einen oder mehrere private Endpunkte pro Ressource verfügbar machen können. Um sicherzustellen, dass die vollqualifizierten Domänennamen für Ihre Ressourcen in die privaten IP-Adressen der privaten Endpunkte aufgelöst werden, müssen Sie die Konfiguration auf Ihren lokalen DNS-Servern ändern. Hierzu gibt es verschiedene Möglichkeiten:

- Ändern Sie die Hostdatei auf Ihren Clients, um die vollqualifizierten Domänennamen für Ihre Speicherkonten und Speichersynchronisierungsdienste in die gewünschten privaten IP-Adressen aufzulösen. Dies wird für Produktionsumgebungen dringend empfohlen, da Sie diese Änderungen für jeden Client vornehmen müssen, der auf Ihre privaten Endpunkte zugreifen muss. Änderungen an Ihren privaten Endpunkten/Ressourcen (Löschungen, Modifizierungen usw.) werden nicht automatisch behandelt.
- Erstellen von DNS-Zonen auf Ihren lokalen Servern für `privatelink.file.core.windows.net` und `privatelink.afs.azure.net` mit A-Datensätzen für Ihre Azure-Ressourcen. Diese Lösung hat den Vorteil, dass Clients in Ihrer lokalen Umgebung Azure-Ressourcen automatisch auflösen können, ohne dass jeder Client einzeln konfiguriert werden muss. Sie ist jedoch ähnlich fehleranfällig wie das Ändern der Hostdatei, da Änderungen nicht berücksichtigt werden. Für einige Umgebungen ist diese Lösung trotz ihrer Fehleranfälligkeit die beste Wahl.
- Weiterleiten der Zonen `core.windows.net` und `afs.azure.net` von Ihren lokalen DNS-Servern an Ihre private Azure-DNS-Zone. Der private Azure-DNS-Host ist über eine spezielle IP-Adresse (`168.63.129.16`) erreichbar, auf die nur innerhalb von virtuellen Netzwerken zugegriffen werden kann, die mit der privaten Azure-DNS-Zone verknüpft sind. Um diese Einschränkung zu umgehen, können Sie zusätzliche DNS-Server in Ihrem virtuellen Netzwerk ausführen, die `core.windows.net` und `afs.azure.net` an die entsprechenden privaten Azure-DNS-Zonen weiterleiten. Zur Vereinfachung dieser Einrichtung stehen PowerShell-Cmdlets zur Verfügung, die automatisch DNS-Server in Ihrem virtuellen Azure-Netzwerk bereitstellen und wie gewünscht konfigurieren. Informationen zum Einrichten der DNS-Weiterleitung finden Sie unter [Konfigurieren von DNS mit Azure Files](storage-files-networking-dns.md).

## <a name="encryption-in-transit"></a>Verschlüsselung während der Übertragung
Verbindungen, die zwischen dem Azure-Dateisynchronisierung-Agent und Ihrer Azure-Dateifreigabe oder einem Speichersynchronisierungsdienst hergestellt werden, sind immer verschlüsselt. Obwohl Azure-Speicherkonten über eine Einstellung zum Deaktivieren der Voraussetzung der Verschlüsselung während der Übertragung für die Kommunikation mit Azure Files (und den anderen Azure-Speicherdiensten, die über das Speicherkonto verwaltet werden) verfügen, wirkt sich das Deaktivieren dieser Einstellung nicht auf die Verschlüsselung der Azure-Dateisynchronisierung bei der Kommunikation mit Azure Files aus. Standardmäßig ist in allen Azure-Speicherkonten die Verschlüsselung während der Übertragung aktiviert. 

Weitere Informationen zur Verschlüsselung während der Übertragung finden Sie unter [Vorschreiben einer sicheren Übertragung in Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Weitere Informationen
- [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](storage-sync-files-planning.md)
- [Bereitstellen der Azure-Dateisynchronisierung](storage-sync-files-deployment-guide.md)