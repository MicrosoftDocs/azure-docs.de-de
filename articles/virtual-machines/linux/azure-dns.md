---
title: DNS-Namensauflösungsoptionen für virtuelle Linux-Computer in Azure
description: Namensauflösungsszenarien für virtuelle Linux-Computer in Azure IaaS, einschließlich bereitgestellter DNS-Dienste, hybridem externen DNS und Bring Your Own DNS-Server (Verwenden eines eigenen DNS-Servers).
services: virtual-machines
documentationcenter: na
author: RicksterCDN
manager: jeconnoc
editor: tysonn
ms.assetid: 787a1e04-cebf-4122-a1b4-1fcf0a2bbf5f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: rclaus
ms.openlocfilehash: ae8315b2a484cddc500b5c2dd02a019cb4f46d8e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127088"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>DNS-Namensauflösungsoptionen für virtuelle Linux-Computer in Azure
Azure stellt die DNS-Namensauflösung standardmäßig für alle in einem einzelnen virtuellen Netzwerk enthaltenen virtuellen Computer bereit. Sie können Ihre eigene Lösung für die DNS-Namensauflösung implementieren, indem Sie auf Ihren in Azure gehosteten virtuellen Computern Ihre eigenen DNS-Dienste konfigurieren. Die folgenden Szenarien sollten Ihnen dabei helfen, situationsabhängig die jeweils am besten geeignete Lösung zu wählen.

* [Von Azure bereitgestellte Namensauflösung](#name-resolution-that-azure-provides)
* [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server)

Welche Art der Namensauflösung Sie verwenden, hängt davon ab, wie die virtuellen Computer und Rolleninstanzen miteinander kommunizieren sollen.

In der folgenden Tabelle sind die Szenarien und entsprechenden Lösungen für die Namensauflösung aufgeführt:

| **Szenario** | **Lösung** | **Suffix** |
| --- | --- | --- |
| Namensauflösung zwischen Rolleninstanzen oder virtuellen Computern im gleichen virtuellen Netzwerk |Von Azure bereitgestellte Namensauflösung |Hostname oder vollqualifizierter Domänenname (FQDN) |
| Namensauflösung zwischen Rolleninstanzen oder virtuellen Computern in unterschiedlichen virtuellen Netzwerken |Vom Kunden verwaltete DNS-Server, die Abfragen zwischen virtuellen Netzwerken zur Auflösung durch Azure weiterleiten (DNS-Proxy). Siehe [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server). |Nur FQDN |
| Auflösung lokaler Computer- und Dienstnamen von Rolleninstanzen oder virtuellen Computern in Azure |Vom Kunden verwaltete DNS-Server (z.B. lokale Domänencontroller, lokale schreibgeschützte Domänencontroller oder ein sekundärer DNS-Server, der mithilfe von Zonenübertragungen synchronisiert wird). Siehe [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server). |Nur FQDN |
| Auflösung von Azure-Hostnamen von lokalen Computern |Weiterleiten von Abfragen an einen vom Kunden verwalteten DNS-Proxyserver im zugehörigen virtuellen Netzwerk. Der Proxyserver leitet Abfragen zur Auflösung an Azure weiter. Siehe [Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server). |Nur FQDN |
| Reverse-DNS für interne IPs |[Namensauflösung mithilfe eines eigenen DNS-Servers](#name-resolution-using-your-own-dns-server) |– |

## <a name="name-resolution-that-azure-provides"></a>Von Azure bereitgestellte Namensauflösung
Zusammen mit der Auflösung der öffentlichen DNS-Namen bietet Azure die Auflösung interner Namen für virtuelle Computer und Rolleninstanzen, die sich innerhalb des gleichen virtuellen Netzwerks befinden. In virtuellen Netzwerken, die auf Azure Resource Manager basieren, ist das DNS-Suffix im gesamten virtuellen Netzwerk gleich. Der FQDN ist nicht erforderlich. DNS-Namen können Netzwerkkarten sowie virtuellen Computern zugewiesen werden. Obwohl für die von Azure bereitgestellte Namensauflösung keine Konfiguration erforderlich ist, ist sie nicht für alle Bereitstellungsszenarien die beste Lösung (siehe die vorherige Tabelle).

### <a name="features-and-considerations"></a>Features und Überlegungen
**Funktionen:**

* Zur Verwendung der von Azure bereitgestellten Namensauflösung ist keine Konfiguration erforderlich.
* Der von Azure bereitgestellte Dienst zur Namensauflösung ist hoch verfügbar. Sie müssen Cluster Ihrer eigenen DNS-Server weder erstellen noch verwalten.
* Der von Azure bereitgestellte Dienst zur Namensauflösung kann in Verbindung mit Ihren eigenen DNS-Servern zum Auflösen von lokalen und Azure-Hostnamen verwendet werden.
* Die Namensauflösung wird zwischen virtuellen Computern in virtuellen Netzwerken bereitgestellt, ohne dass der FQDN erforderlich ist.
* Sie können Hostnamen verwenden, die Ihre Bereitstellungen am besten beschreiben, und müssen nicht mit automatisch generierten Namen arbeiten.

**Überlegungen:**

* Das von Azure erstellte DNS-Suffix kann nicht geändert werden.
* Sie können keine eigenen Einträge manuell registrieren.
* WINS und NetBIOS werden nicht unterstützt.
* Hostnamen müssen DNS-kompatibel sein.
    Für die Namen dürfen nur die Zeichen 0–9, a–z und „-“ verwendet werden, zudem dürfen sie nicht mit „-“ beginnen. Siehe RFC 3696 Abschnitt 2.
* Der DNS-Abfragedatenverkehr wird für den jeweiligen virtuellen Computer gedrosselt. Die Drosselung sollte auf die meisten Anwendungen keine Auswirkungen haben.  Wenn eine Drosselung der Anforderungen festgestellt wird, stellen Sie sicher, dass clientseitiges Zwischenspeichern aktiviert ist.  Weitere Informationen finden Sie unter [Optimale Nutzung der von Azure bereitgestellten Namensauflösung](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Optimale Nutzung der von Azure bereitgestellten Namensauflösung
**Clientseitiges Zwischenspeichern:**

Einige DNS-Abfragen werden nicht über das Netzwerk gesendet. Clientseitiges Zwischenspeichern kann die Latenz verringern und die Flexibilität bei Netzwerkinkonsistenzen verbessern, indem sich wiederholende DNS-Abfragen aus einem lokalen Cache aufgelöst werden. DNS-Einträge enthalten ein Time-To-Live (TTL), damit der Cache den Datensatz so lange wie möglich speichern kann, ohne die Aktualität der Datensätze zu beeinträchtigen. Daher ist das clientseitige Zwischenspeichern in den meisten Situationen geeignet.

Einige Linux-Distributionen bieten standardmäßig kein Zwischenspeichern. Es wird empfohlen, jedem virtuellen Linux-Computer einen Cache hinzuzufügen, nachdem Sie sich vergewissert haben, dass noch kein lokaler Cache vorhanden ist.

Verschiedene DNS-Cachingpakete, z.B. dnsmasq, stehen zur Verfügung. Es folgen die Schritte zur Installation von dnsmasq auf den gängigsten Distributionen:

**Ubuntu (verwendet resolvconf)**
  * Installieren Sie das dnsmasq-Paket („sudo apt-get install dnsmasq“).

**SUSE (verwendet netconf)** :
1. Installieren Sie das dnsmasq-Paket („sudo zypper install dnsmasq“).
2. Aktivieren Sie den dnsmasq-Dienst („systemctl enable dnsmasq.service“).
3. Starten Sie den dnsmasq-Dienst („systemctl start dnsmasq.service“).
4. Bearbeiten Sie „/etc/sysconfig/network/config“, und ändern Sie NETCONFIG_DNS_FORWARDER="" in „dnsmasq“.
5. Aktualisieren Sie resolv.conf („netconfig update“), um den Cache als lokale DNS-Auflösung festzulegen.

**CentOS von Rogue Wave Software (früher OpenLogic; verwendet NetworkManager)**
1. Installieren Sie das dnsmasq-Paket („sudo yum install dnsmasq“).
2. Aktivieren Sie den dnsmasq-Dienst („systemctl enable dnsmasq.service“).
3. Starten Sie den dnsmasq-Dienst („systemctl start dnsmasq.service“).
4. Fügen Sie „prepend domain-name-servers 127.0.0.1;“ zu „/etc/dhclient-eth0.conf“ hinzu.
5. Starten Sie den Netzwerkdienst neu („service network restart“), um den Cache als lokale DNS-Auflösung festzulegen.

> [!NOTE]
> : Das dnsmasq-Paket ist nur einer der vielen DNS-Caches, die für Linux verfügbar sind. Bevor Sie es nutzen, überprüfen Sie die Eignung für Ihre Anforderungen und außerdem, ob kein anderer Cache installiert ist.
>
>

**Clientseitige Wiederholungsversuche**

DNS ist in erster Linie ein UDP-Protokoll. Da das UDP-Protokoll keine Nachrichtenübermittlung garantiert, wird die Wiederholungslogik vom DNS-Protokoll selbst verarbeitet. Jeder DNS-Client (Betriebssystem) kann je nach Bevorzugung des Erstellers eine unterschiedliche Wiederholungslogik aufweisen:

* Windows-Betriebssysteme starten nach einer Sekunde einen Wiederholungsversuch und dann erneut nach weiteren zwei, vier und weiteren vier Sekunden.
* Das standardmäßige Linux-Setup führt nach fünf Sekunden einen Wiederholungsversuch aus.  Sie sollten dies so ändern, dass fünfmal im Abstand von einer Sekunde ein Wiederholungsversuch gestartet wird.  

Wechseln Sie zum Überprüfen der aktuellen Einstellungen auf einem virtuellen Linux-Computer „cat /etc/resolv.conf“ ein, und sehen Sie sich die Zeile „options“ an, z.B.:

    options timeout:1 attempts:5

Die Datei „resolv.conf“ wird automatisch generiert und darf nicht bearbeitet werden. Die entsprechenden Schritte zum Hinzufügen der Zeile „options“ variieren je nach Distribution:

**Ubuntu** (verwendet resolvconf)
1. Fügen Sie in „/etc/resolveconf/resolv.conf.d/head“ die Zeile „options“ hinzu.
2. Führen Sie „resolvconf -u“ zum Aktualisieren aus.

**SUSE** (verwendet netconf)
1. Fügen Sie „timeout:1 attempts:5“ dem Parameter NETCONFIG_DNS_RESOLVER_OPTIONS="" in „/etc/sysconfig/network/config“ hinzu.
2. Führen Sie „netconfig update“ zum Aktualisieren aus.

**CentOS von Rogue Wave Software (früher OpenLogic)** (verwendet NetworkManager)
1. Fügen Sie „RES_OPTIONS="timeout:1 attempts:5"“ zu „/etc/sysconfig/network“ hinzu.
2. Führen Sie „service network restart“ zum Aktualisieren aus.

## <a name="name-resolution-using-your-own-dns-server"></a>Namensauflösung mithilfe eines eigenen DNS-Servers
Ihre Namensauflösungsanforderungen gehen möglicherweise über die von Azure bereitgestellten Features hinaus. Dies ist beispielsweise der Fall, wenn Sie eine DNS-Auflösung zwischen virtuellen Netzwerken benötigen. Für dieses Szenario können Sie Ihre eigenen DNS-Server verwenden.  

DNS-Server innerhalb eines virtuellen Netzwerks können DNS-Abfragen an die rekursiven Resolver von Azure weiterleiten, um Hostnamen im gleichen virtuellen Netzwerk aufzulösen. Beispielsweise kann ein in Azure ausgeführter DNS-Server auf DNS-Abfragen für die eigenen DNS-Zonendateien antworten und alle anderen Abfragen an Azure weiterleiten. Aufgrund dieser Funktionalität sind sowohl Ihre Einträge in Ihren Zonendateien als auch die von Azure bereitgestellten Hostnamen (über die Weiterleitung) für die virtuellen Computer sichtbar. Der Zugriff auf die rekursiven Resolver von Azure wird über die virtuelle IP 168.63.129.16 bereitgestellt.

Durch die DNS-Weiterleitung wird außerdem eine DNS-Auflösung zwischen virtuellen Netzwerken ermöglicht, und die lokalen Computer können die von Azure bereitgestellten Hostnamen auflösen. Um einen Hostnamen eines virtuellen Computers aufzulösen, muss sich die DNS-Server-VM im selben virtuellen Netzwerk befinden und zur Weiterleitung von Abfragen für Hostnamen an Azure konfiguriert sein. Da jedes virtuelle Netzwerk ein eigenes DNS-Suffix verwendet, können Sie mithilfe von Regeln für die bedingte Weiterleitung DNS-Abfragen zur Auflösung an das richtige virtuelle Netzwerk senden. In der folgenden Abbildung sind zwei virtuelle Netzwerke und ein lokales Netzwerk dargestellt, in dem eine DNS-Auflösung zwischen virtuellen Netzwerken mithilfe dieser Methode durchgeführt wird.

![DNS-Auflösung zwischen virtuellen Netzwerken](./media/azure-dns/inter-vnet-dns.png)

Bei Verwendung der von Azure bereitgestellten Namensauflösung wird jedem virtuellen Computer über DHCP das interne DNS-Suffix bereitgestellt. Wenn Sie eine eigene Lösung für die Namensauflösung verwenden, wird dieses Suffix nicht für die virtuellen Computer bereitgestellt, weil es Konflikte mit anderen DNS-Architekturen verursacht. Um per FQDN auf Computer zu verweisen oder das Suffix auf Ihren virtuellen Computern zu konfigurieren, können Sie das Suffix mithilfe von PowerShell oder der API ermitteln:

* Für von Azure Resource Manager verwaltete virtuelle Netzwerke ist das Suffix über die Ressource [Netzwerkkarte](https://msdn.microsoft.com/library/azure/mt163668.aspx) verfügbar. Zudem können Sie den Befehl `azure network public-ip show <resource group> <pip name>` ausführen, um die Details der öffentlichen IP-Adresse, einschließlich des FQDN der Netzwerkkarte, anzuzeigen.

Wenn eine Abfrageweiterleitung an Azure nicht Ihren Anforderungen entspricht, müssen Sie eine eigene DNS-Lösung bereitstellen.  Die DNS-Lösung muss Folgendes leisten:

* Bereitstellung einer geeigneten Hostnamensauflösung, z.B. über [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). Bei Verwendung von DDNS müssen Sie möglicherweise die DNS-Eintragsbereinigung deaktivieren. Die DHCP-Leases von Azure sind sehr lange gültig, und die DNS-Einträge werden durch eine Bereinigung möglicherweise zu früh entfernt.
* Bereitstellung einer geeigneten rekursiven Lösung, um eine Auflösung externer Domänennamen zu ermöglichen.
* Möglichkeit zum Zugriff (TCP und UDP auf Port 53) von den Clients, die Dienste beziehen, und für den Internetzugriff.
* Schutz vor einem Zugriff aus dem Internet, um mögliche Bedrohungen durch externe Agents zu minimieren.

> [!NOTE]
> Für eine optimale Leistung bei Verwendung von virtuellen Azure-Computern als DNS-Server sollte IPv6 deaktiviert und eine [öffentliche IP auf Instanzebene](../../virtual-network/virtual-networks-instance-level-public-ip.md) jedem virtuellen DNS-Servercomputer zugewiesen werden.  
>
>
