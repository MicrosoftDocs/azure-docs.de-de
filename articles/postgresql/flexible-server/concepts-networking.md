---
title: Übersicht über Netzwerkkonzepte – Azure Database for PostgreSQL – Flexible Server
description: In diesem Artikel erfahren Sie mehr über die Konnektivitäts- und Netzwerkoptionen in der Bereitstellungsoption „Flexible Server“ für Azure Database for PostgreSQL.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: cf7c5fa8563544add55a7fad5075848eb5116fe1
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96901931"
---
# <a name="networking-overview---azure-database-for-postgresql---flexible-server"></a>Übersicht über Netzwerkkonzepte – Azure Database for PostgreSQL – Flexible Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – Flexible Server befindet sich in der Vorschau.

In diesem Artikel werden die Konnektivitäts- und Netzwerkkonzepte für Azure Database for PostgreSQL – Flexible Server beschrieben. 

## <a name="choosing-a-networking-option"></a>Auswählen einer Netzwerkoption
Für Ihre Bereitstellung von Azure Database for PostgreSQL – Flexible Server haben Sie zwei Netzwerkoptionen: den **privaten Zugriff** (VNET-Integration) und den **öffentlichen Zugriff (zugelassene IP-Adressen)** . Bei der Servererstellung müssen Sie eine dieser Optionen auswählen. 

> [!NOTE]
> Nach der Servererstellung kann diese Netzwerkoption nicht mehr geändert werden. 

* **Privater Zugriff (VNET-Integration):** Sie können Ihren flexiblen Server in Ihrer [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)-Instanz bereitstellen. Virtuelle Azure-Netzwerke ermöglichen eine private und sichere Netzwerkkommunikation. Ressourcen in einem virtuellen Netzwerk können über private IP-Adressen kommunizieren.

   Die VNET-Integration bietet sich an, wenn Sie die folgenden Funktionen benötigen:
   * Verbinden von Azure-Ressourcen im gleichen virtuellen Netzwerk mit Ihrem flexiblen Server über private IP-Adressen
   * Herstellen einer Verbindung von Azure-externen Ressourcen mit Ihrem flexiblen Server über ein VPN oder eine ExpressRoute-Verbindung
   * Der flexible Server hat keinen öffentlichen Endpunkt.

* **Öffentlicher Zugriff (zugelassene IP-Adressen):** Auf Ihren flexiblen Server wird über einen öffentlichen Endpunkt zugegriffen. Der öffentliche Endpunkt ist eine öffentlich auflösbare DNS-Adresse. Der Ausdruck „zugelassene IP-Adressen“ bezieht sich auf einen Bereich von IP-Adressen, denen Sie die Berechtigung erteilen, auf den Server zuzugreifen. Diese Berechtigungen heißen **Firewallregeln**. 

   Sie sollten sich für einen öffentlichen Zugriff entscheiden, wenn Sie die folgenden Funktionen benötigen:
   * Herstellen einer Verbindung von Azure-Ressourcen aus, die keine virtuellen Netzwerke unterstützen
   * Herstellen einer Verbindung von Azure-externen Ressourcen aus, die weder ein VPN noch ExpressRoute unterstützen 
   * Der flexible Server hat keinen öffentlichen Endpunkt.

Die folgenden Eigenschaften gelten unabhängig davon, ob Sie den privaten oder den öffentlichen Zugriff wählen:
* Verbindungen von zugelassenen IP-Adressen aus müssen sich beim PostgreSQL-Server mit gültigen Anmeldeinformationen authentifizieren.
* Für den Netzwerkdatenverkehr ist die [Verbindungsverschlüsselung](#tls-and-ssl) verfügbar.
* Der Server verfügt über einen vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN). Für die Hostnamenseigenschaft in Verbindungszeichenfolgen wird empfohlen, den FQDN anstelle einer IP-Adresse zu verwenden.
* Über beide Optionen wird der Zugriff auf Serverebene, nicht auf Datenbank- oder auf Tabellenebene gesteuert. Der Zugriff auf Datenbanken, Tabellen und andere Objekte wird mithilfe der Rolleneigenschaften in PostgreSQL gesteuert.


## <a name="private-access-vnet-integration"></a>Privater Zugriff (VNET-Integration)
Der private Zugriff mit der Integration über ein virtuelles Netzwerk (VNET) gewährleistet eine private und sichere Kommunikation für Ihre PostgreSQL Flexible Server-Instanz.

### <a name="virtual-network-concepts"></a>Virtuelle Netzwerke: Konzepte
Nachstehend werden einige Konzepte erläutert, die Sie kennen sollten, wenn Sie virtuelle Netzwerke mit PostgreSQL Flexible Server-Instanzen verwenden.

* **Virtuelles Netzwerk:** Ein virtuelles Azure-Netzwerk (Virtual Network, VNET) enthält einen privaten IP-Adressraum, der für Ihre Verwendung konfiguriert ist. Weitere Informationen zu virtuellen Azure-Netzwerken finden Sie unter [Überblick über Azure Virtual Network](../../virtual-network/virtual-networks-overview.md).

    Ihr virtuelles Netzwerk muss in derselben Azure-Region wie Ihr flexibler Server bereitgestellt werden.


* **Delegiertes Subnetz:** Ein virtuelles Netzwerk enthält Subnetze. Subnetze bieten Ihnen die Möglichkeit, Ihr virtuelles Netzwerk in kleinere Adressräume einzuteilen. Azure-Ressourcen werden in bestimmten Subnetzen innerhalb eines virtuellen Netzwerks bereitgestellt. 

   Ihre PostgreSQL Flexible Server-Instanz muss sich in einem Subnetz befinden, das eigens für PostgreSQL Flexible Server-Instanzen eingeteilt (**delegiert**) wurde. Diese Delegierung bedeutet, dass dieses Subnetz nur von Azure Database for PostgreSQL Flexible Server-Instanzen genutzt werden kann. Im delegierten Subnetz können sich keine anderen Azure-Ressourcentypen befinden. Sie können ein Subnetz delegieren, indem Sie „Microsoft.DBforPostgreSQL/flexibleServers“ als Delegierungseigenschaft festlegen.

* **Netzwerksicherheitsgruppen (NSG):** Mit Sicherheitsregeln in Netzwerksicherheitsgruppen können Sie den Typ des ein- und ausgehenden Netzwerkdatenverkehrs von Subnetzen virtueller Netzwerke und Netzwerkschnittstellen filtern. Weitere Informationen finden Sie unter [Netzwerksicherheitsgruppen](../../virtual-network/network-security-groups-overview.md).


### <a name="unsupported-virtual-network-scenarios"></a>Nicht unterstützte virtuelle Netzwerkszenarios
* Öffentlicher Endpunkt (oder öffentliche IP-Adresse oder DNS): Ein flexibler Server, der in einem virtuellen Netzwerk bereitgestellt wird, kann keinen öffentlichen Endpunkt haben.
* Nachdem der flexible Server in einem virtuellen Netzwerk und Subnetz bereitgestellt wurde, können Sie ihn in kein anderes virtuelles Netzwerk oder Subnetz verschieben. Auch das virtuelle Netzwerk kann in keine andere Ressourcengruppe oder kein anderes Abonnement verschoben werden.
* Die Subnetzgröße (Adressräume) kann nicht erhöht werden, sobald Ressourcen im Subnetz vorhanden sind.
* VNET-Peering wird nicht unterstützt.

Unter den folgenden Links erfahren Sie, wie Sie einen flexiblen Server mit privatem Zugriff (VNET-Integration) im [Azure-Portal](how-to-manage-virtual-network-portal.md) oder über die [Azure CLI](how-to-manage-virtual-network-cli.md) erstellen.

> [!NOTE]
> Wenn Sie den benutzerdefinierten DNS-Server verwenden, müssen Sie eine DNS-Weiterleitung verwenden, um den FQDN von Azure Database for MySQL: Flexible Server aufzulösen. Weitere Informationen finden Sie unter [Namensauflösung mithilfe eines eigenen DNS-Servers](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="public-access-allowed-ip-addresses"></a>Öffentlicher Zugriff (zugelassene IP-Adressen)
Die öffentliche Zugriffsmethode weist u. a. folgende Eigenschaften auf:
* Nur die IP-Adressen, die Sie zulassen, dürfen auf Ihre PostgreSQL Flexible Server-Instanz zugreifen. Standardmäßig sind keine IP-Adressen zugelassen. Sie können IP-Adressen während der Servererstellung oder später hinzufügen.
* Ihr PostgreSQL-Server verfügt über einen öffentlich auflösbaren DNS-Namen.
* Ihr flexibler Server befindet sich nicht in einem Ihrer virtuellen Azure-Netzwerke.
* Der Netzwerkdatenverkehr zu und von Ihrem Server erfolgt nicht über ein privates Netzwerk. Der Datenverkehr verwendet die allgemeinen Internetpfade.

### <a name="firewall-rules"></a>Firewallregeln
Eine Berechtigung, die einer IP-Adresse erteilt wird, wird als Firewallregel bezeichnet. Wenn ein Verbindungsversuch von einer IP-Adresse stammt, die Sie nicht zugelassen haben, wird dem Ursprungsclient ein Fehler zurückgegeben.

Unter den folgenden Links erfahren Sie, wie Sie einen flexiblen Server mit öffentlichem Zugriff (zugelassene IP-Adressen) im [Azure-Portal](how-to-manage-firewall-portal.md) oder über die [Azure CLI](how-to-manage-firewall-cli.md) erstellen.


### <a name="allowing-all-azure-ip-addresses"></a>Zulassen aller Azure-IP-Adressen
Wenn eine feste IP-Adresse für ausgehenden Datenverkehr für Ihren Azure-Dienst nicht verfügbar ist, können Sie die Aktivierung von Verbindungen von allen IP-Adressen im Azure-Rechenzentrum in Erwägung ziehen.

> [!IMPORTANT]
> Die Option **Allow public access from Azure services and resources within Azure** (Öffentlichen Zugriff von Azure-Diensten und -Ressourcen aus gestatten) konfiguriert die Firewall so, dass alle von Azure ausgehenden Verbindungen zugelassen werden, einschließlich der Verbindungen aus Abonnements anderer Kunden. Wenn Sie diese Option auswählen, stellen Sie sicher, dass die Anmelde- und die Benutzerberechtigungen den Zugriff nur auf autorisierte Benutzer beschränken.

### <a name="troubleshooting-public-access-issues"></a>Behandeln von Problemen mit dem öffentlichen Zugriff
Wenn der Zugriff auf den Microsoft Azure-Datenbank für PostgreSQL-Serverdienst nicht das erwartete Verhalten aufweist, sind folgende Punkte zu beachten:

* **Änderungen an der Zulassungsliste sind noch nicht wirksam**: Änderungen der Firewallkonfiguration für den Azure Database for PostgreSQL-Server werden möglicherweise erst nach fünf Minuten wirksam.

* **Fehler bei der Authentifizierung:** Wenn ein Benutzer nicht über die Berechtigungen für den Azure Database for PostgreSQL-Server verfügt oder das verwendete Kennwort falsch ist, wird die Verbindung mit dem Azure Database for PostgreSQL-Server verweigert. Durch das Erstellen einer Firewalleinstellung wird Clients lediglich die Möglichkeit gegeben, einen Verbindungsversuch zum Server zu unternehmen. Jeder Client muss weiterhin die erforderlichen Sicherheitsanmeldeinformationen bereitstellen.

* **Dynamische Client-IP-Adresse:** Wenn Sie über eine Internetverbindung mit dynamischer IP-Adressierung verfügen und Probleme beim Passieren der Firewall auftreten, können Sie eine der folgenden Lösungen ausprobieren:

   * Fragen Sie Ihren Internetdienstanbieter (ISP) nach dem IP-Adressbereich, der den Clientcomputern zugewiesen ist, mit denen auf den Azure-Datenbank für PostgreSQL-Server zugegriffen wird, und fügen Sie dann den IP-Adressbereich als Firewallregel hinzu.
   * Verwenden Sie stattdessen die statische IP-Adressierung für die Clientcomputer, und fügen Sie dann die statische IP-Adresse als Firewallregel hinzu.

* **Die Firewallregel ist für das IPv6-Format nicht verfügbar**: Firewallregeln müssen das IPv4-Format aufweisen. Wenn Sie Firewallregeln im IPv6-Format angeben, wird ein Validierungsfehler angezeigt.

## <a name="hostname"></a>Hostname
Unabhängig von Ihrer gewählten Netzwerkoption empfiehlt es sich, beim Herstellen einer Verbindung mit Ihrem flexiblen Server immer einen FQDN als Hostnamen zu verwenden. Es ist nicht gewährleistet, dass die IP-Adresse des Servers statisch bleibt. Mithilfe des FQDN können Sie verhindern, dass Änderungen an der Verbindungszeichenfolge vorgenommen werden. 

Beispiel
* Empfohlen: `hostname = servername.postgres.database.azure.com`
* Vermeiden Sie nach Möglichkeit die Verwendung von `hostname = 10.0.0.4` (eine private Adresse) oder `hostname = 40.2.45.67` (eine öffentliche Adresse).


## <a name="tls-and-ssl"></a>TLS und SSL
Azure Database for PostgreSQL – Flexible Server unterstützt das Verbinden Ihrer Clientanwendungen mit dem PostgreSQL-Dienst mithilfe der Transport Layer Security (TLS). Die TLS ist ein Standardprotokoll der Branche, das verschlüsselte Netzwerkverbindungen zwischen dem Datenbankserver und Clientanwendungen gewährleistet. Bei der TLS handelt es sich um eine aktualisierte Version von Secure Sockets Layer (SSL).

Azure Database for PostgreSQL – Flexible Server unterstützt nur verschlüsselte Verbindungen mithilfe der Transport Layer Security. Alle eingehenden Verbindungen mit TLS 1.0 und TLS 1.1 werden verweigert. 

## <a name="next-steps"></a>Nächste Schritte
* Unter den folgenden Links erfahren Sie, wie Sie einen flexiblen Server mit **privatem Zugriff (VNET-Integration)** im [Azure-Portal](how-to-manage-virtual-network-portal.md) oder über die [Azure CLI](how-to-manage-virtual-network-cli.md) erstellen.
* Unter den folgenden Links erfahren Sie, wie Sie einen flexiblen Server mit **öffentlichem Zugriff (zugelassene IP-Adressen)** im [Azure-Portal](how-to-manage-firewall-portal.md) oder über die [Azure CLI](how-to-manage-firewall-cli.md) erstellen.
