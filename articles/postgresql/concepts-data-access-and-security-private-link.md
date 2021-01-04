---
title: Private Link – Azure Database for PostgreSQL – Einzelserver
description: Hier erfahren Sie, wie Private Link für Azure Database for PostgreSQL-Einzelserver funktioniert.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: a864ce42888aace385cf60a4122f204c8f76831d
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240425"
---
# <a name="private-link-for-azure-database-for-postgresql-single-server"></a>Private Link für Azure Database for PostgreSQL-Einzelserver

Private Link ermöglicht Ihnen das Erstellen privater Endpunkte für Azure Database for PostgreSQL-Einzelserver und integriert so Azure-Dienste in Ihr privates virtuelles Netzwerk (VNET). Der private Endpunkt macht eine private IP-Adresse verfügbar, die Sie verwenden können, um mit Ihrem Datenbankserver wie mit jeder anderen Ressource im VNET eine Verbindung herzustellen.

Eine Liste der PaaS-Dienste, die die Private Link-Funktion unterstützen, finden Sie in der [Dokumentation](../private-link/index.yml) zu Private Link. Ein privater Endpunkt ist eine private IP-Adresse in einem bestimmten [VNET](../virtual-network/virtual-networks-overview.md) und Subnetz.

> [!NOTE]
> Das Feature „Private Link“ ist nur für Azure Database for PostgreSQL-Server in den Tarifen „Universell“ oder „Arbeitsspeicheroptimiert“ verfügbar. Stellen Sie sicher, dass für den Datenbankserver einer dieser Tarife festgelegt wird.

## <a name="data-exfiltration-prevention"></a>Verhinderung der Datenexfiltration

Datenexfiltration in einem Azure Database for PostgreSQL-Einzelserver bedeutet, dass ein autorisierter Benutzer (etwa ein Datenbankadministrator) Daten aus einem System extrahieren und an einen anderen Ort oder in ein anderes System außerhalb der Organisation verschieben kann. So kann ein Benutzer beispielsweise Daten in ein Speicherkonto eines Dritten verschieben.

Stellen Sie sich ein Szenario vor, in dem ein Benutzer PGAdmin auf einem virtuellen Azure-Computer (Virtual Machine, VM) ausführt, der eine Verbindung mit einem in der Region „USA, Westen“ bereitgestellten Azure Database for PostgreSQL-Einzelserver herstellt. Das folgende Beispiel zeigt, wie Sie den Zugriff mit öffentlichen Endpunkten in Azure Database for PostgreSQL-Einzelservern unter Verwendung von Netzwerkzugriffssteuerungen einschränken.

* Deaktivieren Sie den gesamten Datenverkehr, der von Azure-Diensten über den öffentlichen Endpunkt an Azure Database for PostgreSQL-Einzelserver gerichtet wird, indem Sie die Option *Allow Azure Services* (Azure-Dienste zulassen) auf „AUS“ festlegen. Stellen Sie entweder mithilfe von [Firewallregeln](./concepts-firewall-rules.md) oder mithilfe von [VNET-Dienstendpunkten](./concepts-data-access-and-security-vnet.md) sicher, dass keine IP-Adressen oder IP-Adressbereiche auf den Server zugreifen können.

* Lassen Sie nur Datenverkehr an Azure Database for PostgreSQL-Einzelserver mit der privaten IP-Adresse des virtuellen Computers zu. Weitere Informationen finden Sie in den Artikeln [Verwenden von VNET-Dienstendpunkten und -Regeln für Datenbankserver](concepts-data-access-and-security-vnet.md) und [IP-Firewallregeln für Azure SQL-Datenbank und Azure SQL Data Warehouse](howto-manage-vnet-using-portal.md).

* Schränken Sie auf dem virtuellen Azure-Computer den Bereich der ausgehenden Verbindung mithilfe von Netzwerksicherheitsgruppen (NSGs) und Diensttags ein:

    * Geben Sie eine NSG-Regel an, um Datenverkehr für *Service Tag = SQL.WestUS* zuzulassen. Dadurch ist nur eine Verbindung mit Azure Database for PostgreSQL-Einzelservern in der Region „USA, Westen“ möglich.
    * Geben Sie eine NSG-Regel (mit einer höheren Priorität) an, um Datenverkehr für *Service Tag = SQL* zu verweigern. Dadurch werden Verbindungen mit der PostgreSQL-Datenbank in allen Regionen verweigert.</br></br>

Am Ende dieser Einrichtung kann der virtuelle Azure-Computer nur eine Verbindung mit Azure Database for PostgreSQL-Einzelservern in der Region „USA, Westen“ herstellen. Die Konnektivität ist jedoch nicht auf einen einzelnen Azure Database for PostgreSQL-Einzelserver beschränkt. Der virtuelle Computer kann weiterhin eine Verbindung mit beliebigen Azure Database for PostgreSQL-Einzelservern in der Region „USA, Westen“ herstellen – also auch mit den Datenbanken, die nicht Teil des Abonnements sind. Wir haben den Bereich der Datenexfiltration im obigen Szenario auf eine bestimmte Region reduziert, sie aber nicht vollständig unterbunden.</br>

Mit Private Link können Sie nun Netzwerkzugriffssteuerungen wie NSGs einrichten, um den Zugriff auf den privaten Endpunkt einzuschränken. Einzelne Azure-PaaS-Ressourcen werden dann bestimmten privaten Endpunkten zugeordnet. Ein böswilliger Insider kann somit nur auf die zugeordnete PaaS-Ressource (also etwa auf einen Azure Database for PostgreSQL-Einzelserver) zugreifen, nicht aber auf andere Ressourcen.

## <a name="on-premises-connectivity-over-private-peering"></a>Lokale Konnektivität über privates Peering

Wenn Sie von lokalen Computern aus eine Verbindung mit dem öffentlichen Endpunkt herstellen, muss Ihre IP-Adresse mithilfe einer Firewallregel auf Serverebene der IP-basierten Firewall hinzugefügt werden. Dieses Modell eignet sich zwar gut, um den Zugriff auf einzelne Computer für Entwicklungs- oder Testworkloads zuzulassen, in einer Produktionsumgebung gestaltet sich die Verwaltung jedoch schwierig.

Mit Private Link können Sie standortübergreifenden Zugriff auf den privaten Endpunkt mittels [ExpressRoute](https://azure.microsoft.com/services/expressroute/), privatem Peering oder [VPN-Tunneln](../vpn-gateway/index.yml) ermöglichen. Anschließend können Sie den gesamten Zugriff über einen öffentlichen Endpunkt deaktivieren, ohne die IP-basierte Firewall zu verwenden.

> [!NOTE]
> In einigen Fällen befinden sich Azure Database for PostgreSQL und das VNET-Subnetz in unterschiedlichen Abonnements. In diesen Fällen müssen Sie folgende Konfigurationen sicherstellen:
> - Stellen Sie sicher, dass für beide Abonnements der Ressourcenanbieter **Microsoft.DBforPostgreSQL** registriert ist. Weitere Informationen finden Sie unter [Azure-Ressourcenanbieter und -typen][resource-manager-portal].

## <a name="configure-private-link-for-azure-database-for-postgresql-single-server"></a>Konfigurieren von Private Link für Azure Database for PostgreSQL-Einzelserver

### <a name="creation-process"></a>Erstellungsprozess

Zum Aktivieren von Private Link sind private Endpunkte erforderlich. Hierfür können Sie die folgenden Schrittanleitungen verwenden.

* [Azure portal](./howto-configure-privatelink-portal.md)
* [BEFEHLSZEILENSCHNITTSTELLE (CLI)](./howto-configure-privatelink-cli.md)

### <a name="approval-process"></a>Genehmigungsprozess
Nachdem der Netzwerkadministrator den privaten Endpunkt (PE) erstellt hat, kann der PostgreSQL-Administrator die private Endpunktverbindung (Private Endpoint Connection, PEC) mit Azure Database for PostgreSQL verwalten. Diese Aufteilung von Aufgaben zwischen dem Netzwerkadministrator und Datenbankadministrator ist für die Verwaltung der Konnektivität von Azure Database for PostgreSQL hilfreich. 

* Navigieren Sie im Azure-Portal zur Azure Database for PostgreSQL-Serverressource. 
    * Auswählen der privaten Endpunktverbindung im linken Bereich
    * Liste mit allen privaten Endpunktverbindungen (Private Endpoint Connections, PECs)
    * Der entsprechende erstellte private Endpunkt (PE)

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-portal.png" alt-text="Auswählen des privaten Endpunkts im Portal":::

* Wählen Sie eine einzelne PEC aus der Liste aus.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link.png" alt-text="Auswählen des privaten Endpunkts mit ausstehender Genehmigung":::

* Der PostgreSQL-Serveradministrator kann eine PEC genehmigen oder ablehnen und optional eine kurze Textantwort hinzufügen.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-message.png" alt-text="Auswählen der Nachricht für den privaten Endpunkt":::

* Nach der Genehmigung oder Ablehnung wird der entsprechende Zustand zusammen mit dem Antworttext in der Liste angezeigt.

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png" alt-text="Auswählen des Endstatus des privaten Endpunkts":::

## <a name="use-cases-of-private-link-for-azure-database-for-postgresql"></a>Anwendungsfälle von Private Link für Azure Database for PostgreSQL

Clients können eine Verbindung mit dem privaten Endpunkt über das gleiche VNET, über das mittels Peering verbundene VNET in der gleichen Region oder regionsübergreifend über eine VNET-zu-VNET-Verbindung herstellen. Darüber hinaus können Clients von der lokalen Umgebung aus eine Verbindung über ExpressRoute, privates Peering oder VPN-Tunneling herstellen. Die gängigen Anwendungsfälle sind im folgenden Diagramm vereinfacht dargestellt:

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-overview.png" alt-text="Übersicht zum Auswählen des privaten Endpunkts":::

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Herstellen einer Verbindung über einen virtuellen Azure-Computer in einem virtuellen Netzwerk (VNET) mit Peering
Konfigurieren Sie das [VNET-Peering](../virtual-network/tutorial-connect-virtual-networks-powershell.md), um über einen virtuellen Azure-Computer in einem VNET mit Peering eine Verbindung mit dem Azure Database for PostgreSQL-Einzelserver herzustellen.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Herstellen einer Verbindung über einen virtuellen Azure-Computer in einer VNET-zu-VNET-Umgebung
Konfigurieren Sie eine [VNET-zu-VNET-VPN-Gatewayverbindung](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), um über einen virtuellen Azure-Computer in einer anderen Region oder in einem anderen Abonnement eine Verbindung mit einem Azure Database for PostgreSQL-Einzelserver herzustellen.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Herstellen einer VPN-Verbindung in einer lokalen Umgebung
Verwenden oder implementieren Sie eine der folgenden Optionen, um in einer lokalen Umgebung eine Verbindung mit dem Azure Database for PostgreSQL-Einzelserver herzustellen:

* [Point-to-Site-Verbindung](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Site-to-Site-VPN-Verbindung](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [ExpressRoute-Verbindung](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

## <a name="private-link-combined-with-firewall-rules"></a>Private Link in Kombination mit Firewallregeln

Folgende Fälle und Ergebnisse sind bei Verwendung von Private Link in Verbindung mit Firewallregeln möglich:

* Wenn Sie keine Firewallregeln konfigurieren, ist standardmäßig kein Datenverkehrszugriff auf den Azure Database for PostgreSQL-Einzelserver möglich.

* Wenn Sie öffentlichen Datenverkehr oder einen Dienstendpunkt konfigurieren und private Endpunkte erstellen, werden verschiedene Arten von eingehendem Datenverkehr durch den entsprechenden Typ der Firewallregel autorisiert.

* Wenn Sie keinen öffentlichen Datenverkehr oder Dienstendpunkt konfigurieren und private Endpunkte erstellen, kann auf den Azure Database for PostgreSQL-Einzelserver nur über die privaten Endpunkte zugegriffen werden. Wenn Sie keinen öffentlichen Datenverkehr oder Dienstendpunkt konfigurieren, ist nach dem Ablehnen oder Löschen aller genehmigten privaten Endpunkte kein Datenverkehrszugriff auf den Azure Database for PostgreSQL-Einzelserver möglich.

## <a name="deny-public-access-for-azure-database-for-postgresql-single-server"></a>Verweigern des öffentlichen Zugriffs auf Azure Database for PostgreSQL-Einzelserver

Wenn Sie sich für den Zugriff auf Ihren Azure Database for PostgreSQL-Einzelserver nur auf private Endpunkte verlassen möchten, können Sie das Festlegen aller öffentlichen Endpunkte ( [Firewallregeln](concepts-firewall-rules.md) und [VNET-Dienstendpunkte](concepts-data-access-and-security-vnet.md)) deaktivieren, indem Sie die Konfiguration **Zugriff auf öffentliches Netzwerk verweigern** auf dem Datenbankserver festlegen. 

Wenn diese Einstellung auf *JA* festgelegt ist, sind nur Verbindungen über private Endpunkte mit Ihrer Azure Database for PostgreSQL-Instanz zulässig. Wenn diese Einstellung auf *NEIN* festgelegt ist, können Clients basierend auf Ihrer Firewall- oder VNET-Dienstendpunkt-Einstellung eine Verbindung mit Ihrer Azure Database for PostgreSQL-Instanz herstellen. Sobald der Wert des privaten Netzwerkzugriffs festgelegt ist, können Kunden außerdem weder Firewall- oder VNET-Dienstendpunkt-Regeln hinzufügen noch vorhandene aktualisieren.

> [!Note]
> Dieses Feature steht in allen Azure-Regionen zur Verfügung, in denen Azure Database for PostgreSQL-Einzelserver die Tarife „Universell“ und „Arbeitsspeicheroptimiert“ unterstützen.
>
> Diese Einstellung hat keinerlei Auswirkung auf die SSL- und TLS-Konfigurationen für Ihren Azure Database for PostgreSQL-Einzelserver.

Informationen zum Festlegen von **Zugriff auf öffentliches Netzwerk verweigern** für Ihren Azure Database for PostgreSQL-Einzelserver im Azure-Portal finden Sie unter [Konfigurieren von „Zugriff auf öffentliches Netzwerk verweigern“](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Sicherheitsfeatures für Azure Database for PostgreSQL-Einzelserver finden Sie in den folgenden Artikeln:

* Informationen zum Konfigurieren einer Firewall für Azure Database for PostgreSQL-Einzelserver finden Sie unter [Firewallregeln in Azure Database for PostgreSQL – Einzelserver](./concepts-firewall-rules.md).

* Informationen zum Konfigurieren eines VNET-Dienstendpunkts für Ihren Azure Database for PostgreSQL-Einzelserver finden Sie unter [Verwenden von VNET-Dienstendpunkten und -Regeln für Azure Database for PostgreSQL – Einzelserver](./concepts-data-access-and-security-vnet.md).

* Eine Übersicht über die Konnektivität für Azure Database for PostgreSQL-Einzelserver finden Sie unter [Verbindungsarchitektur in Azure Database for PostgreSQL](./concepts-connectivity-architecture.md).

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md