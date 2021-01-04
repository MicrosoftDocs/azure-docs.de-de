---
title: Freigeben eines Private Link-Diensts über Virtual WAN
titleSuffix: Azure Virtual WAN
description: Schritte zum Konfigurieren von Private Link in Virtual WAN
services: virtual-wan
author: erjosito
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: jomore
ms.custom: fasttrack-new
ms.openlocfilehash: cc8e7314c941035207ecf809a9d85ef46bd58379
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913754"
---
# <a name="use-private-link-in-virtual-wan"></a>Verwenden von Private Link in Virtual WAN

Mit der Technologie [Azure Private Link](../private-link/private-link-overview.md) können Sie Platform-as-a-Service-Angebote von Azure über private IP-Adressen verbinden, indem Sie [private Endpunkte](../private-link/private-endpoint-overview.md) verfügbar machen. Mit Azure Virtual WAN können Sie einen privaten Endpunkt in einem der virtuellen Netzwerke bereitstellen, die mit einem beliebigen virtuellen Hub verbunden sind. Dadurch wird eine Verbindung mit einem beliebigen anderen virtuellen Netzwerk (oder seiner Verzweigung) hergestellt, das mit demselben Virtual WAN verbunden ist.

## <a name="before-you-begin"></a>Voraussetzungen

Bei den Schritten in diesem Artikel wird davon ausgegangen, dass Sie bereits ein virtuelles WAN mit einem oder mehreren Hubs bereitgestellt haben und mindestens zwei virtuelle Netzwerke mit Virtual WAN verbunden sind.

Führen Sie die Schritte in den folgenden Artikeln aus, um ein neues virtuelles WAN und einen neuen Hub zu erstellen:

* [Erstellen eines virtuellen WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [Erstellen eines Hubs](virtual-wan-site-to-site-portal.md#hub)
* [Verbinden eines VNET mit einem Hub](virtual-wan-site-to-site-portal.md#hub)

## <a name="create-a-private-link-endpoint"></a><a name="endpoint"></a>Erstellen eines Private Link-Endpunkts

Sie können einen Private Link-Endpunkt für viele verschiedene Dienste erstellen. In diesem Beispiel verwenden wir Azure SQL-Datenbank. Weitere Informationen zum Erstellen eines privaten Endpunkts für eine Azure SQL-Datenbank finden Sie in [Schnellstart: Erstellen eines privaten Endpunkts mit dem Azure-Portal](../private-link/create-private-endpoint-portal.md). Die folgende Abbildung zeigt die Netzwerkkonfiguration der Azure SQL-Datenbank:

:::image type="content" source="./media/howto-private-link/create-private-link.png" alt-text="Erstellen eines Private Link" lightbox="./media/howto-private-link/create-private-link.png":::

Nachdem Sie die Azure SQL-Datenbank erstellt haben, können Sie die privaten Endpunkte durchsuchen, um die IP-Adresse des privaten Endpunkts zu überprüfen:

:::image type="content" source="./media/howto-private-link/endpoints.png" alt-text="Private Endpunkte" lightbox="./media/howto-private-link/endpoints.png":::

Wenn Sie auf den privaten Endpunkt klicken, den wir erstellt haben, werden die private IP-Adresse sowie ihr vollqualifizierter Domänenname (Fully Qualified Domain Name, FQDN) angezeigt. Beachten Sie, dass der private Endpunkt über eine IP-Adresse im Bereich des VNET verfügt, in dem er bereitgestellt wurde (10.1.3.0/24):

:::image type="content" source="./media/howto-private-link/sql-endpoint.png" alt-text="SQL-Endpunkt" lightbox="./media/howto-private-link/sql-endpoint.png":::

## <a name="verify-connectivity-from-the-same-vnet"></a><a name="connectivity"></a>Überprüfen der Konnektivität aus demselben VNET

In diesem Beispiel überprüfen wir die Konnektivität mit der Azure SQL-Datenbank von einem virtuellen Ubuntu-Computer, auf dem MS SQL-Tools installiert sind. Zunächst wird überprüft, ob die DNS-Auflösung funktioniert und der vollqualifizierte Domänenname der Azure SQL-Datenbank in eine private IP-Adresse im gleichen VNET aufgelöst wird, in dem der private Endpunkt bereitgestellt wurde (10.1.3.0/24):

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

Wie Sie aus der vorherigen Ausgabe ersehen können, ist der FQDN `wantest.database.windows.net` zu `wantest.privatelink.database.windows.net` zugeordnet, und die am privaten Endpunkt erstellte private DNS-Zone wird in die private IP-Adresse `10.1.3.228` aufgelöst. Die Überprüfung der privaten DNS-Zone ergibt, dass für den privaten Endpunkt, der der privaten IP-Adresse zugeordnet ist, ein A-Eintrag vorhanden ist:

:::image type="content" source="./media/howto-private-link/dns-zone.png" alt-text="DNS-Zone" lightbox="./media/howto-private-link/dns-zone.png":::

Nachdem wir die DNS-Auflösung erfolgreich überprüft haben, können wir versuchen, eine Verbindung mit der Datenbank herzustellen:

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.3.75
```

Wie Sie sehen, verwenden wir eine spezielle SQL-Abfrage, die uns die Quell-IP-Adresse liefert, die für den SQL Server vom Client sichtbar ist. In diesem Fall wird für den Server der Client mit seiner privaten IP-Adresse (`10.1.3.75`) angezeigt. Das bedeutet, dass der Datenverkehr direkt aus dem VNET an den privaten Endpunkt geleitet wird.

Beachten Sie, dass Sie die Variablen `username` und `password` so festlegen müssen, dass sie den in der Azure SQL-Datenbank definierten Anmeldeinformationen entsprechen, damit die Beispiele in dieser Anleitung ausgeführt werden können.

## <a name="connect-from-a-different-vnet"></a><a name="vnet">Herstellen einer Verbindung von einem anderen VNET</a>

Da jetzt ein VNET in Azure Virtual WAN mit dem privaten Endpunkt verbunden ist, können alle anderen VNETs und Verzweigungen, die mit Virtual WAN verbunden sind, ebenfalls darauf zugreifen. Sie müssen die Konnektivität über eines der von Azure Virtual WAN unterstützten Modelle bereitstellen, z. B. das [Any-to-Any-Szenario](scenario-any-to-any.md) oder das [Szenario von VNET mit gemeinsamen Diensten](scenario-shared-services-vnet.md), um nur zwei Beispiele zu nennen.

Sobald die Konnektivität zwischen dem VNET oder der Verzweigung mit dem VNET besteht, in dem der private Endpunkt bereitgestellt wurde, müssen Sie die DNS-Auflösung konfigurieren:

* Wenn Sie die Verbindung mit dem privaten Endpunkt über ein VNET herstellen, können Sie dieselbe private Zone verwenden, die mit der Azure SQL-Datenbank erstellt wurde.
* Wenn die Verbindung mit dem privaten Endpunkt von einer Verzweigung erfolgt (Site-to-Site-VPN, Point-to-Site-VPN oder ExpressRoute), müssen Sie die lokale DNS-Auflösung verwenden.

In diesem Beispiel stellen wir die Verbindung aus einem anderen VNET her. Zuerst fügen wir die private DNS-Zone an das neue VNET an, damit die Workloads den vollqualifizierten Domänennamen der Azure SQL-Datenbank in die private IP-Adresse auflösen können. Dies erfolgt durch Verknüpfen der privaten DNS-Zone mit dem neuen VNET:

:::image type="content" source="./media/howto-private-link/dns-link.png" alt-text="DNS-Link" lightbox="./media/howto-private-link/dns-link.png":::

Nun sollte jeder virtuelle Computer im zugeordneten VNET den FQDN der Azure SQL-Datenbank ordnungsgemäß in die private IP-Adresse des Private Link auflösen:

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

Um zu überprüfen, ob das VNET (10.1.1.0/24) über Konnektivität mit dem ursprünglichen VNET verfügt, in dem der private Endpunkt konfiguriert wurde (10.1.3.0/24), können Sie die effektive Routingtabelle auf jedem virtuellen Computer im VNET überprüfen:

:::image type="content" source="./media/howto-private-link/effective-routes.png" alt-text="Effektive Routen" lightbox="./media/howto-private-link/effective-routes.png":::

Wie Sie sehen können, gibt es eine Route, die auf das VNET 10.1.3.0/24 verweist, das von den Gateways für virtuelle Netzwerke in Azure Virtual WAN eingefügt wurde. Jetzt können wir die Konnektivität mit der Datenbank testen:

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.1.75
```

In diesem Beispiel haben wir gesehen, wie durch das Erstellen eines privaten Endpunkts in einem der VNETs, die einem Virtual WAN zugeordnet sind, Konnektivität mit den restlichen VNETs und Verzweigungen im Virtual WAN bereitgestellt wird.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Virtual WAN finden Sie unter [Häufig gestellte Fragen](virtual-wan-faq.md).
