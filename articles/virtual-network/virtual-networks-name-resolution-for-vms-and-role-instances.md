---
title: Namensauflösung für Ressourcen in virtuellen Azure-Netzwerken
titlesuffix: Azure Virtual Network
description: Szenarien für die Namensauflösung für Azure IaaS, Hybridlösungen, zwischen verschiedenen Clouddiensten, Active Directory und Verwenden eines eigenen DNS-Servers
services: virtual-network
documentationcenter: na
author: rohinkoul
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/2/2020
ms.author: rohink
ms.custom: fasttrack-edit
ms.openlocfilehash: 340ca07ba605359f71c1dbf23ca38abd75d84416
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937048"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Namensauflösung für Ressourcen in virtuellen Azure-Netzwerken

Abhängig davon, wie Sie Azure zum Hosten von IaaS, PaaS und Hybridlösungen verwenden, müssen Sie für die virtuellen Computer und andere Ressourcen, die in einem virtuellen Netzwerk bereitgestellt werden, die Kommunikation untereinander zulassen. Sie können diese Kommunikation zwar über IP-Adressen aktivieren, doch ist es deutlich einfacher, Namen zu verwenden, die leicht zu merken sind und sich nicht verändern. 

Wenn in einem virtuellen Netzwerk bereitgestellte Ressourcen Domänennamen in interne IP-Adressen auflösen müssen, können sie eine dieser drei Methoden verwenden:

* [Private Azure DNS-Zonen](../dns/private-dns-overview.md)
* [Von Azure bereitgestellte Namensauflösung](#azure-provided-name-resolution)
* [Namensauflösung mit einem eigenen DNS-Server](#name-resolution-that-uses-your-own-dns-server) (dieser kann Abfragen an die von Azure bereitgestellten DNS-Server weiterleiten)

Welche Art der Namensauflösung Sie verwenden, hängt davon ab, wie die Ressourcen miteinander kommunizieren müssen. In der folgenden Tabelle sind die Szenarien und entsprechenden Lösungen für die Namensauflösung aufgeführt:

> [!NOTE]
> Private Azure DNS-Zonen sind die bevorzugte Lösung und bieten Ihnen Flexibilität bei der Verwaltung Ihrer DNS-Zonen und -Einträge. Weitere Informationen finden Sie unter [Verwenden von Azure DNS für private Domänen](../dns/private-dns-overview.md).

> [!NOTE]
> Wenn Sie von Azure bereitgestelltes DNS verwenden, wird Ihren virtuellen Computern das entsprechende DNS-Suffix automatisch zugewiesen. Für alle anderen Optionen müssen Sie entweder vollqualifizierte Domänennamen (Fully Qualified Domain Names, FQDN) verwenden oder Ihren virtuellen Computern das entsprechende DNS-Suffix manuell zuweisen.

| **Szenario** | **Lösung** | **DNS-Suffix** |
| --- | --- | --- |
| Namensauflösung zwischen virtuellen Computern im gleichen virtuellen Netzwerk oder Azure Cloud Services-Rolleninstanzen im gleichen Clouddienst. | [Private Azure DNS-Zonen](../dns/private-dns-overview.md) oder [von Azure bereitgestellte Namensauflösung](#azure-provided-name-resolution) |Hostname oder FQDN |
| Namensauflösung zwischen virtuellen Computern in verschiedenen virtuellen Netzwerken oder Rolleninstanzen in unterschiedlichen Clouddiensten. |[Private Azure DNS-Zonen](../dns/private-dns-overview.md) oder vom Kunden verwaltete DNS-Server, die Abfragen zwischen virtuellen Netzwerken zur Auflösung durch Azure weiterleiten (DNS-Proxy). Siehe [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-that-uses-your-own-dns-server). |Nur FQDN |
| Namensauflösung aus einem Azure App Service (Web-App, Funktion oder Bot) mithilfe von Integration virtueller Netzwerke in Rolleninstanzen oder virtuelle Computer im gleichen virtuellen Netzwerk. |Vom Kunden verwaltete DNS-Server, die Abfragen zwischen virtuellen Netzwerken zur Auflösung durch Azure weiterleiten (DNS-Proxy). Siehe [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-that-uses-your-own-dns-server). |Nur FQDN |
| Namensauflösung aus App Service-Web-Apps in virtuelle Computer im gleichen virtuellen Netzwerk. |Vom Kunden verwaltete DNS-Server, die Abfragen zwischen virtuellen Netzwerken zur Auflösung durch Azure weiterleiten (DNS-Proxy). Siehe [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-that-uses-your-own-dns-server). |Nur FQDN |
| Namensauflösung aus App Service-Web-Apps in einem virtuellen Netzwerk in virtuelle Computer in einem anderen virtuellen Netzwerk. |Vom Kunden verwaltete DNS-Server, die Abfragen zwischen virtuellen Netzwerken zur Auflösung durch Azure weiterleiten (DNS-Proxy). Siehe [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-that-uses-your-own-dns-server). |Nur FQDN |
| Auflösung lokaler Computer- und Dienstnamen von VMs oder Rolleninstanzen in Azure. |Vom Kunden verwaltete DNS-Server (z. B. lokale Domänencontroller, lokale schreibgeschützte Domänencontroller oder ein sekundärer DNS-Server, der mithilfe von Zonenübertragungen synchronisiert wird). Siehe [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-that-uses-your-own-dns-server). |Nur FQDN |
| Auflösung von Azure-Hostnamen von lokalen Computern |Weiterleiten von Abfragen an einen vom Kunden verwalteten DNS-Proxyserver im zugehörigen virtuellen Netzwerk. Der Proxyserver leitet Abfragen zur Auflösung an Azure weiter. Siehe [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-that-uses-your-own-dns-server). |Nur FQDN |
| Reverse-DNS für interne IPs |[Private Azure DNS-Zonen](../dns/private-dns-overview.md) oder [von Azure bereitgestellte Namensauflösung](#azure-provided-name-resolution) oder [Namensauflösung mit einem eigenen DNS-Server](#name-resolution-that-uses-your-own-dns-server) |Nicht verfügbar |
| Namensauflösung zwischen virtuellen Computern oder Rolleninstanzen in unterschiedlichen Clouddiensten, nicht in einem virtuellen Netzwerk |Nicht zutreffend Die Konnektivität zwischen virtuellen Computern und Rolleninstanzen in verschiedenen Clouddiensten wird außerhalb eines virtuellen Netzwerks nicht unterstützt. |Nicht verfügbar|

## <a name="azure-provided-name-resolution"></a>Von Azure bereitgestellte Namensauflösung

Die von Azure bereitgestellte Namensauflösung bietet nur grundlegende autoritative DNS-Funktionen. Bei Wahl dieser Option werden die DNS-Zonennamen und -Einträge automatisch von Azure verwaltet. Sie können weder die DNS-Zonennamen noch den Lebenszyklus von DNS-Einträgen beeinflussen. Wenn Sie eine voll ausgestattete DNS-Lösung für Ihre virtuellen Netzwerke benötigen, müssen Sie [private Azure DNS-Zonen](../dns/private-dns-overview.md) oder vom [Kunden verwaltete DNS-Server](#name-resolution-that-uses-your-own-dns-server) einsetzen.

Zusammen mit der Auflösung des öffentlichen DNS-Namens bietet Azure die Auflösung interner Namen für virtuelle Computer und Rolleninstanzen, die sich innerhalb des gleichen virtuellen Netzwerks oder Clouddiensts befinden. Virtuelle Computer und Instanzen in einem Clouddienst verwenden das gleiche DNS-Suffix gemeinsam, sodass der Hostname allein ausreichend ist. In virtuellen Netzwerken, die mit dem klassischen Bereitstellungsmodell bereitgestellt werden, weisen verschiedene Clouddienste jedoch unterschiedliche DNS-Suffixe auf. In diesem Fall benötigen Sie den FQDN zum Auflösen von Namen zwischen verschiedenen Clouddiensten. In virtuellen Netzwerken, die mit dem Azure Resource Manager-Bereitstellungsmodell bereitgestellt werden, ist das DNS-Suffix aller virtuellen Computer in einem virtuellen Netzwerk einheitlich, sodass der FQDN nicht benötigt wird. DNS-Namen können virtuellen Computern und Netzwerkschnittstellen zugewiesen werden. Obwohl für die von Azure durchgeführte Namensauflösung keine Konfiguration erforderlich ist, ist sie nicht für alle Bereitstellungsszenarien die beste Lösung (siehe die vorherige Tabelle).

> [!NOTE]
> Bei Verwendung von Clouddienstweb- und Workerrollen können Sie auf die internen IP-Adressen von Rolleninstanzen über die REST-API für die Azure-Dienstverwaltung zugreifen. Weitere Informationen finden Sie unter [Referenz zur REST-API der Dienstverwaltung](https://msdn.microsoft.com/library/azure/ee460799.aspx). Die Adresse basiert auf dem Rollennamen und der Instanznummer. 
>

### <a name="features"></a>Features

Die von Azure bereitgestellte Namensauflösung umfasst die folgenden Features:
* Einfache Bedienung. Es ist keine Konfiguration erforderlich.
* Hochverfügbarkeit. Sie müssen Cluster Ihrer eigenen DNS-Server weder erstellen noch verwalten.
* Sie können den Dienst in Verbindung mit Ihren eigenen DNS-Servern zum Auflösen von lokalen und Azure-Hostnamen verwenden.
* Sie können die Namensauflösung zwischen VMs und Rolleninstanzen im gleichen Clouddienst verwenden, ohne dass ein FQDN erforderlich ist.
* Sie können die Namensauflösung zwischen virtuellen Computern in virtuellen Netzwerken verwenden, die das Azure Resource Manager-Bereitstellungsmodell verwenden, ohne dass ein FQDN erforderlich ist. Virtuelle Netzwerke im klassischen Bereitstellungsmodell erfordern einen FQDN beim Auflösen von Namen in verschiedenen Clouddiensten. 
* Sie können Hostnamen verwenden, die Ihre Bereitstellungen am besten beschreiben, und müssen nicht mit automatisch generierten Namen arbeiten.

### <a name="considerations"></a>Überlegungen

Wenn Sie die von Azure bereitgestellte Namensauflösung verwenden, beachten Sie folgende Punkte:
* Das von Azure erstellte DNS-Suffix kann nicht geändert werden.
* DNS-Lookup ist auf ein virtuelles Netzwerk begrenzt. DNS-Namen, die für ein virtuelles Netzwerk erstellt wurden, können in anderen virtuellen Netzwerken nicht aufgelöst werden.
* Sie können keine eigenen Einträge manuell registrieren.
* WINS und NetBIOS werden nicht unterstützt. Ihre virtuellen Computer werden nicht im Windows-Explorer angezeigt.
* Hostnamen müssen DNS-kompatibel sein. Für die Namen dürfen nur die Zeichen 0 bis 9, a bis z und „-“ verwendet werden, zudem dürfen sie nicht mit „-“ beginnen oder enden.
* Der DNS-Abfragedatenverkehr wird für den jeweiligen virtuellen Computer gedrosselt. Die Drosselung sollte auf die meisten Anwendungen keine Auswirkungen haben. Wenn eine Drosselung der Anforderungen festgestellt wird, stellen Sie sicher, dass clientseitiges Zwischenspeichern aktiviert ist. Weitere Informationen finden Sie unter [DNS-Clientkonfiguration](#dns-client-configuration).
* Nur virtuelle Computer in den ersten 180 Clouddiensten werden für jedes virtuelle Netzwerk in einem klassischen Bereitstellungsmodell registriert. Diese Begrenzung gilt nicht für virtuelle Netzwerke in Azure Resource Manager.
* Die IP-Adresse für Azure DNS für DNS lautet 168.63.129.16. Dies ist eine statische IP-Adresse, die sich nicht ändert.

### <a name="reverse-dns-considerations"></a>Überlegungen zu Reverse-DNS
Reverse-DNS wird in allen auf Azure Resource Manager basierenden virtuellen Netzwerken unterstützt. Sie können Reverse-DNS-Abfragen (PTR-Abfragen) ausführen, um IP-Adressen virtueller Computer zu FQDNs virtueller Computer zuzuordnen.
* Alle PTR-Abfragen für IP-Adressen virtueller Computer geben FQDNs im Format\[VM-Name\].internal.cloudapp.net zurück.
* Forward-Lookup für FQDNs im Format \[VM-Name\].internal.cloudapp.net wird in die IP-Adresse aufgelöst, die dem virtuellen Computer zugewiesen ist.
* Wenn das virtuelle Netzwerk mit [privaten Azure DNS-Zonen](../dns/private-dns-overview.md) als virtuelles Registrierungsnetzwerk verknüpft ist, geben die Reverse-DNS-Abfragen zwei Einträge zurück. Ein Eintrag hat das Format „\[VM-Name\].[Name der privaten Zone]“, der andere das Format „\[VM-Name\].internal.cloudapp.net“.
* Reverse-DNS-Lookup ist auf ein bestimmtes virtuelles Netzwerk begrenzt, auch bei Peerings mit anderen virtuellen Netzwerken. Reverse-DNS-Abfragen (PTR-Abfragen) für IP-Adressen virtueller Computer im Peering mit virtuellen Netzwerken geben NXDOMAIN zurück.
* Wenn Sie die Reverse-DNS-Funktion in einem virtuellen Netzwerk deaktivieren möchten, können Sie dazu mit [Azure DNS Private Zones](../dns/private-dns-overview.md) eine Reverse-Lookup-Zone erstellen und diese mit Ihrem virtuellen Netzwerk verknüpfen. Wenn der IP-Adressraum des virtuellen Netzwerks z. B. 10.20.0.0/16 lautet, können Sie die leere private DNS-Zone 20.10.in-addr.arpa erstellen und diese mit dem virtuellen Netzwerk verknüpfen. Beim Verknüpfen der Zone mit dem virtuellen Netzwerk sollten Sie die automatische Registrierung für den Link deaktivieren. Diese Zone setzt die Reverse-Lookup-Standardzonen für das virtuelle Netzwerk außer Kraft, und da diese Zone leer ist, erhalten Sie für Ihre Reverse-DNS-Abfragen NXDOMAIN. Weitere Informationen zum Erstellen einer privaten DNS-Zone und Verknüpfen der Zone mit einem virtuellen Netzwerk finden Sie in unserem [Schnellstart](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal).

> [!NOTE]
> Wenn Sie möchten, dass sich Reverse-DNS-Lookup über ein virtuelles Netzwerk erstreckt, können Sie eine Reverse-Lookup-Zone (in-addr.arpa [private Azure DNS-Zonen](../dns/private-dns-overview.md)) erstellen und diese mit mehreren virtuellen Netzwerken verknüpfen. Sie müssen jedoch die Reverse-DNS-Einträge für die virtuellen Computer manuell verwalten.
>


## <a name="dns-client-configuration"></a>DNS-Clientkonfiguration

Dieser Abschnitt behandelt clientseitiges Zwischenspeichern und clientseitige Wiederholungsversuche.

### <a name="client-side-caching"></a>Clientseitiges Caching

Nicht alle DNS-Abfragen müssen über das Netzwerk gesendet werden. Clientseitiges Zwischenspeichern kann die Latenz verringern und die Flexibilität bei Netzwerkproblemen verbessern, indem sich wiederholende DNS-Abfragen aus einem lokalen Cache aufgelöst werden. DNS-Einträge enthalten einen TTL-Mechanismus (Time-To-Live), damit der Cache den Datensatz so lange wie möglich speichern kann, ohne die Aktualität der Datensätze zu beeinträchtigen. Daher ist das clientseitige Zwischenspeichern in den meisten Situationen geeignet.

Der DNS-Standardclient von Windows verfügt über einen integrierten DNS-Cache. Einige Linux-Distributionen bieten standardmäßig kein Zwischenspeichern. Wenn Sie feststellen, dass noch kein lokaler Cache vorhanden ist, fügen Sie jedem virtuellen Linux-Computer einen DNS-Cache hinzu.

Es sind mehrere unterschiedliche DNS-Cachingpakete verfügbar (z.B. dnsmasq). So installieren Sie dnsmasq für die gängigsten Distributionen:

* **Ubuntu (verwendet resolvconf)** :
  * Installieren Sie das dnsmasq-Paket mit `sudo apt-get install dnsmasq`.
* **SUSE (verwendet netconf)** :
  * Installieren Sie das dnsmasq-Paket mit `sudo zypper install dnsmasq`.
  * Aktivieren Sie den dnsmasq-Dienst mit `systemctl enable dnsmasq.service`. 
  * Starten Sie den dnsmasq-Dienst mit `systemctl start dnsmasq.service`. 
  * Bearbeiten Sie **/etc/sysconfig/network/config**, und ändern Sie *NETCONFIG_DNS_FORWARDER=""* in *dnsmasq*.
  * Aktualisieren Sie „resolv.conf“ mit `netconfig update`, um den Cache als lokalen DNS-Resolver festzulegen.
* **CentOS** (verwendet NetworkManager):
  * Installieren Sie das dnsmasq-Paket mit `sudo yum install dnsmasq`.
  * Aktivieren Sie den dnsmasq-Dienst mit `systemctl enable dnsmasq.service`.
  * Starten Sie den dnsmasq-Dienst mit `systemctl start dnsmasq.service`.
  * Fügen Sie *prepend domain-name-servers 127.0.0.1;* zu **/etc/dhclient-eth0.conf** hinzu.
  * Starten Sie den Netzwerkdienst mit `service network restart` neu, um den Cache als lokalen DNS-Resolver festzulegen.

> [!NOTE]
> Das dnsmasq-Paket ist nur einer der vielen DNS-Caches, die für Linux verfügbar sind. Bevor Sie es nutzen, überprüfen Sie dessen Eignung für Ihre besonderen Bedürfnisse und außerdem, ob kein anderer Cache installiert ist.

    
### <a name="client-side-retries"></a>Clientseitige Wiederholungsversuche

DNS ist in erster Linie ein UDP-Protokoll. Da das UDP-Protokoll keine Nachrichtenübermittlung garantiert, wird die Wiederholungslogik im DNS-Protokoll selbst behandelt. Jeder DNS-Client (Betriebssystem) kann je nach Vorliebe des Erstellers eine unterschiedliche Wiederholungslogik aufweisen:

* Windows-Betriebssysteme starten nach einer Sekunde einen Wiederholungsversuch und dann erneut nach weiteren zwei Sekunden, vier Sekunden und weiteren vier Sekunden. 
* Das standardmäßige Linux-Setup führt nach fünf Sekunden einen Wiederholungsversuch aus. Es wird empfohlen, die Wiederholungsversuchspezifikationen in fünf Wiederholungsversuche in Intervallen von einer Sekunde zu ändern.

Überprüfen Sie die aktuellen Einstellungen auf einem virtuellen Linux-Computer mit `cat /etc/resolv.conf`. Sehen Sie sich beispielsweise die Zeile *options* an:

```bash
options timeout:1 attempts:5
```

Die Datei „resolv.conf“ wird normalerweise automatisch generiert und sollte nicht bearbeitet werden. Die entsprechenden Schritte zum Hinzufügen der Zeile *options* variieren je nach Distribution:

* **Ubuntu** (verwendet resolvconf):
  1. Fügen Sie in **/etc/resolvconf/resolv.conf.d/tail** die Zeile *options* hinzu.
  2. Führen Sie `resolvconf -u` zum Aktualisieren aus.
* **SUSE** (verwendet netconf):
  1. Fügen Sie *timeout:1 attempts:5* dem Parameter **NETCONFIG_DNS_RESOLVER_OPTIONS=""** in **/etc/sysconfig/network/config** hinzu.
  2. Führen Sie `netconfig update` zum Aktualisieren aus.
* **CentOS** (verwendet NetworkManager):
  1. Fügen Sie *echo "options timeout:1 attempts:5"* zu **/etc/NetworkManager/dispatcher.d/11-dhclient** hinzu.
  2. Aktualisieren Sie mit `service network restart`.

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Namensauflösung mithilfe eines eigenen DNS-Servers.

In diesem Abschnitt werden virtuelle Computer, Rolleninstanzen sowie Web-Apps behandelt.

### <a name="vms-and-role-instances"></a>Virtuelle Computer und Rolleninstanzen

Ihre Namensauflösungsanforderungen gehen möglicherweise über die von Azure bereitgestellten Features hinaus. Beispielsweise müssen Sie für die Verwendung von Microsoft Windows Server Active Directory-Domänen vielleicht DNS-Namen zwischen virtuellen Netzwerken auflösen. Um diese Szenarien abzudecken, bietet Azure Ihnen die Möglichkeit, eigene DNS-Server zu verwenden.

DNS-Server in einem virtuellen Netzwerk können DNS-Abfragen an die rekursiven Resolver in Azure weiterleiten. Dadurch können Sie Hostnamen innerhalb dieses virtuellen Netzwerks auflösen. Beispielsweise kann ein in Azure ausgeführter Domänencontroller (DC) auf DNS-Abfragen für die eigenen Domänen antworten und alle anderen Abfragen an Azure weiterleiten. Durch das Weiterleiten von Abfragen sind sowohl Ihre lokalen Ressourcen (über den DC) als auch die von Azure bereitgestellten Hostnamen (über die Weiterleitung) für die virtuellen Computer sichtbar. Der Zugriff auf die rekursiven Resolver in Azure wird über die virtuelle IP-Adresse 168.63.129.16 bereitgestellt.

Durch die DNS-Weiterleitung wird außerdem eine DNS-Auflösung zwischen virtuellen Netzwerken ermöglicht, sodass die lokalen Computer von Azure bereitgestellte Hostnamen auflösen können. Um den Hostnamen eines virtuellen Computers aufzulösen, muss sich die DNS-Server-VM im selben virtuellen Netzwerk befinden und zur Weiterleitung von Abfragen für Hostnamen an Azure konfiguriert sein. Da jedes virtuelle Netzwerk ein eigenes DNS-Suffix verwendet, können Sie mithilfe von Regeln für die bedingte Weiterleitung DNS-Abfragen zur Auflösung an das richtige virtuelle Netzwerk senden. In der folgenden Abbildung sind zwei virtuelle Netzwerke und ein lokales Netzwerk dargestellt, in dem eine DNS-Auflösung zwischen virtuellen Netzwerken mithilfe dieser Methode ausgeführt wird. Ein Beispiel für eine DNS-Weiterleitung steht im [Azure-Katalog mit Schnellstartvorlagen](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) und auf [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder) zur Verfügung.

> [!NOTE]
> Eine Rolleninstanz kann die Namensauflösung von virtuellen Computern innerhalb des gleichen virtuellen Netzwerks ausführen. Diese erfolgt mithilfe des FQDN, der aus dem Hostnamen des virtuellen Computers und dem DNS-Suffix **internal.cloudapp.net** besteht. In diesem Fall ist die Namensauflösung jedoch nur erfolgreich, wenn für die Rolleninstanz der Namen des virtuellen Computers im [Rollenschema (CSCFG-Datei)](https://msdn.microsoft.com/library/azure/jj156212.aspx) definiert ist.
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> Rolleninstanzen, die eine Namensauflösung virtueller Computer in einem anderen virtuellen Netzwerk ausführen müssen (FQDN mit dem Suffix **internal.cloudapp.net**), müssen diese mit der in diesem Abschnitt beschriebenen Methode vornehmen (über benutzerdefinierte DNS-Server, die für eine Weiterleitung zwischen den beiden virtuellen Netzwerken sorgen).
>

![Abbildung der DNS-Auflösung zwischen virtuellen Netzwerken](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Wenn Sie die von Azure bereitgestellte Namensauflösung verwenden, stellt Azure DHCP (Dynamic Host Configuration Protocol) ein internes DNS-Suffix ( **.internal.cloudapp.net**) für jeden virtuellen Computer bereit. Dieses Suffix ermöglicht die Auflösung von Hostnamen, weil sich die Einträge für die Hostnamen in der Zone **internal.cloudapp.net** befinden. Wenn Sie eine eigene Lösung für die Namensauflösung verwenden, wird dieses Suffix nicht für die virtuellen Computer bereitgestellt, weil es Konflikte mit anderen DNS-Architekturen verursacht (z.B. in Szenarien mit Domäneneinbindung). Stattdessen stellt Azure einen nicht funktionsfähigen Platzhalter bereit (*reddog.microsoft.com*).

Bei Bedarf kann das interne DNS-Suffix mithilfe von PowerShell oder der API ermittelt werden:

* Für virtuelle Netzwerke in Azure Resource Manager-Bereitstellungsmodellen ist das Suffix über die [Netzwerkschnittstellen-REST-API](https://docs.microsoft.com/rest/api/virtualnetwork/networkinterfaces), das PowerShell-Cmdlet [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) und den Azure CLI-Befehl [az network nic show](/cli/azure/network/nic#az-network-nic-show) verfügbar.
* In klassischen Bereitstellungsmodellen steht das Suffix über einen Aufruf von [Get Deployment API](https://msdn.microsoft.com/library/azure/ee460804.aspx) oder das Cmdlet [Get-AzureVM -Debug](/powershell/module/servicemanagement/azure.service/get-azurevm) zur Verfügung.

Wenn eine Abfrageweiterleitung an Azure nicht Ihren Anforderungen entspricht, sollten Sie eine eigene DNS-Lösung bereitstellen. Die DNS-Lösung muss Folgendes leisten:

* Bereitstellung einer geeigneten Hostnamensauflösung, z.B. über [DDNS](virtual-networks-name-resolution-ddns.md). Bei Verwendung von DDNS muss möglicherweise die DNS-Eintragsbereinigung deaktiviert werden. Die Azure DHCP-Leases sind sehr lange gültig, und die DNS-Einträge werden durch eine Bereinigung möglicherweise zu früh entfernt. 
* Bereitstellung einer geeigneten rekursiven Lösung, um eine Auflösung externer Domänennamen zu ermöglichen.
* Möglichkeit zum Zugriff (TCP und UDP auf Port 53) von den Clients, die Dienste beziehen, und für den Internetzugriff.
* Schutz vor einem Zugriff aus dem Internet, um mögliche Bedrohungen durch externe Agents zu minimieren.

> [!NOTE]
> Wenn Sie virtuelle Azure-Computer als DNS-Server verwenden, sollte IPv6 für optimale Leistung deaktiviert sein.

### <a name="web-apps"></a>Web-Apps
Angenommen, Sie müssen Namensauflösung aus Ihrer Web-App, die mithilfe von App Service erstellt wurde und mit einem virtuellen Netzwerk verbunden ist, für virtuelle Computer im gleichen virtuellen Netzwerk ausführen. Zusätzlich zur Einrichtung eines benutzerdefinierten DNS-Servers mit DNS-Weiterleitung, die Abfragen an Azure weiterleitet (virtuelle IP-Adresse 168.63.129.16), führen Sie die folgenden Schritte aus:
1. Aktivieren Sie die Integration virtueller Netzwerke für Ihre Web-App (falls noch nicht geschehen), wie unter [Integrieren Ihrer App in ein virtuelles Netzwerk](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) beschrieben wird.
2. Wählen Sie im Azure-Portal für den App Service-Plan, in dem die Web-App gehostet wird, unter **Netzwerk** > **Integration des virtuellen Netzwerks** die Option **Netzwerk synchronisieren** aus.

    ![Screenshot der Namensauflösung für ein virtuelles Netzwerk](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Wenn Sie eine Namensauflösung von Ihrer mit App Service erstellten und mit einem virtuellen Netzwerk verknüpften Web-App aus in virtuelle Computer in einem anderen virtuellen Netzwerk durchführen müssen, müssen Sie in beiden virtuellen Netzwerken folgendermaßen benutzerdefinierte DNS-Server verwenden:

* Richten Sie einen DNS-Server im virtuellen Zielnetzwerk auf einem virtuellen Computer ein, der auch Abfragen an den rekursiven Resolver in Azure (virtuelle IP-Adresse 168.63.129.16) weiterleiten kann. Ein Beispiel für eine DNS-Weiterleitung steht im [Azure-Katalog mit Schnellstartvorlagen](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) und auf [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder) zur Verfügung. 
* Richten Sie eine DNS-Weiterleitung im virtuellen Quellnetzwerk auf einem virtuellen Computer ein. Konfigurieren Sie diese DNS-Weiterleitung für das Weiterleiten von Abfragen an den DNS-Server in Ihrem virtuellen Zielnetzwerk.
* Konfigurieren Sie den DNS-Quellserver in den Einstellungen für Ihr virtuelles Quellnetzwerk.
* Aktivieren Sie die Integration virtueller Netzwerke für Ihre Web-App, um eine Verknüpfung mit dem virtuellen Quellnetzwerk herzustellen. Befolgen Sie dazu die Anweisungen unter [Integrieren Ihrer App in ein virtuelles Netzwerk](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Wählen Sie im Azure-Portal für den App Service-Plan, in dem die Web-App gehostet wird, unter **Netzwerk** > **Integration des virtuellen Netzwerks** die Option **Netzwerk synchronisieren** aus.

## <a name="specify-dns-servers"></a>Angeben von DNS-Servern
Wenn Sie eigene DNS-Server verwenden, bietet Azure die Möglichkeit, für jedes virtuelle Netzwerk mehrere DNS-Server anzugeben. Sie können auch mehrere DNS-Server pro Netzwerkschnittstelle (für Azure Resource Manager) oder pro Clouddienst (für das klassische Bereitstellungsmodell) angeben. Die für einen Clouddienst oder eine Netzwerkschnittstelle angegebenen DNS-Server besitzen Vorrang vor den für das virtuelle Netzwerk angegebenen DNS-Servern.

> [!NOTE]
> Netzwerkverbindungseigenschaften wie die IP-Adressen von DNS-Servern sollten nicht direkt auf VMs bearbeitet werden. Dies liegt daran, dass sie während der Dienstreparatur gelöscht werden können, wenn der virtuelle Netzwerkadapter ersetzt wird. Dies gilt für Windows- und Linux-VMs.

Wenn Sie das Azure Resource Manager-Bereitstellungsmodell verwenden, können Sie DNS-Server für ein virtuelles Netzwerk und eine Netzwerkschnittstelle angeben. Weitere Informationen finden Sie unter [Erstellen, Ändern oder Löschen eines virtuellen Netzwerks](manage-virtual-network.md) und [Erstellen, Ändern oder Löschen von Netzwerkschnittstellen](virtual-network-network-interface.md).

> [!NOTE]
> Wenn Sie sich für den benutzerdefinierten DNS-Server für Ihr virtuelles Netzwerk entscheiden, müssen Sie mindestens eine DNS-Server-IP-Adresse angeben; andernfalls ignoriert das virtuelle Netzwerk die Konfiguration und verwendet stattdessen das von Azure bereitgestellte DNS.

Wenn Sie das klassische Bereitstellungsmodell verwenden, können Sie DNS-Server für das virtuelle Netzwerk im Portal oder in der [Netzwerkkonfigurationsdatei](https://msdn.microsoft.com/library/azure/jj157100) angeben. Für Clouddienste können Sie DNS-Server über die [Dienstkonfigurationsdatei](https://msdn.microsoft.com/library/azure/ee758710) oder mithilfe von PowerShell mit [New-AzureVM](/powershell/module/servicemanagement/azure.service/new-azurevm) angegeben.

> [!NOTE]
> Wenn Sie die DNS-Einstellungen für ein virtuelles Netzwerk oder einen virtuellen Computer ändern, das bzw. der schon bereitgestellt wurde, müssen Sie für alle betroffenen virtuellen Computer im virtuellen Netzwerk die DHCP-Leasedauer verlängern, damit die neuen DNS-Einstellungen wirksam werden. Bei virtuellen Computern, auf denen das Windows-Betriebssystem ausgeführt wird, können Sie dazu `ipconfig /renew` direkt auf dem virtuellen Computer eingeben. Die Schritte unterscheiden sich je nach Betriebssystem. Weitere Informationen finden Sie in der jeweiligen Dokumentation für Ihren Betriebssystemtyp.

## <a name="next-steps"></a>Nächste Schritte

Azure Resource Manager-Bereitstellungsmodell:

* [Erstellen, Ändern oder Löschen eines virtuellen Netzwerks](manage-virtual-network.md)
* [Erstellen, Ändern oder Löschen von Netzwerkschnittstellen](virtual-network-network-interface.md)

Klassisches Bereitstellungsmodell:

* [Azure-Dienstkonfigurationsschema (.cscfg-Datei)](https://msdn.microsoft.com/library/azure/ee758710)
* [Konfigurationsschema für virtuelle Netzwerke](https://msdn.microsoft.com/library/azure/jj157100)
* [Konfigurieren eines virtuellen Netzwerks mithilfe einer Netzwerkkonfigurationsdatei](virtual-networks-using-network-configuration-file.md)
