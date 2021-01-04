---
title: Verwenden privater Endpunkte für Azure App Configuration
description: Schützen Ihres App Configuration-Speichers mithilfe privater Endpunkte
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: 6cadadfb3623d05dd3ae3851acd5eaca13860023
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929842"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Verwenden privater Endpunkte für Azure App Configuration

Sie können [private Endpunkte](../private-link/private-endpoint-overview.md) für Azure App Configuration verwenden, um Clients in einem virtuellen Netzwerk (VNET) den sicheren Zugriff auf Daten über [Private Link](../private-link/private-link-overview.md) zu ermöglichen. Der private Endpunkt verwendet eine IP-Adresse aus dem VNET-Adressraum für Ihren App Configuration-Speicher. Der Netzwerkdatenverkehr zwischen den Clients im VNET und dem App Configuration-Speicher wird über das VNET über eine privaten Verbindung im Microsoft-Backbone-Netzwerk geleitet, sodass keine Offenlegung im öffentlichen Internet erfolgt.

Die Verwendung privater Endpunkte für Ihren App Configuration-Speicher ermöglicht Ihnen Folgendes:
- Sichern Sie die Konfigurationsdetails Ihrer Anwendung, indem Sie die Firewall so konfigurieren, dass alle Verbindungen mit App Configuration am öffentlichen Endpunkt blockiert werden.
- Erhöhen Sie die Sicherheit für das virtuelle Netzwerk (VNET), um sicherzustellen, dass Daten nicht aus dem VNET entfernt werden.
- Stellen Sie sichere Verbindungen mit dem App Configuration-Speicher aus lokalen Netzwerken her, die sich mit dem VNET über [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) oder [ExpressRoutes](../expressroute/expressroute-locations.md) mit privatem Peering verbinden.

## <a name="conceptual-overview"></a>Konzeptionelle Übersicht

Ein privater Endpunkt ist eine spezielle Netzwerkschnittstelle für einen Azure-Dienst in Ihrem [virtuellen Netzwerk](../virtual-network/virtual-networks-overview.md) (VNET). Wenn Sie einen privaten Endpunkt für Ihren App Config-Speicher erstellen, wird eine sichere Verbindung zwischen Clients in Ihrem VNET und Ihrem Konfigurationsspeicher bereitgestellt. Dem privaten Endpunkt wird eine IP-Adresse aus dem IP-Adressbereich Ihres VNET zugewiesen. Für die Verbindung zwischen dem privaten Endpunkt und dem Konfigurationsspeicher wird eine sichere private Verbindung verwendet.

Anwendungen im VNET können über den privaten Endpunkt eine Verbindung mit dem Konfigurationsspeicher herstellen, und zwar **mit denselben Verbindungszeichenfolgen und Autorisierungsmechanismen, die auch sonst verwendet würden**. Private Endpunkte können mit allen vom App Configuration-Speicher unterstützten Protokollen verwendet werden.

App Configuration unterstützt keine Dienstendpunkte. Private Endpunkte können in Subnetzen erstellt werden, die [Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) verwenden. Clients in einem Subnetz können über einen privaten Endpunkt eine sichere Verbindung mit einem App Configuration-Speicher herstellen, während Dienstendpunkte für den Zugriff auf andere verwendet werden.  

Wenn Sie einen privaten Endpunkt für einen Dienst in Ihrem VNET erstellen, wird an den Dienstkontobesitzer eine Einwilligungsanforderung zur Genehmigung gesendet. Wenn der Benutzer, der die Erstellung des privaten Endpunkts anfordert, auch ein Besitzer des Kontos ist, wird diese Einwilligungsanforderung automatisch genehmigt.

Dienstkontobesitzer können Einwilligungsanforderung und private Endpunkte über die Registerkarte `Private Endpoints` im Konfigurationsspeicher im [Azure-Portal](https://portal.azure.com) verwalten.

### <a name="private-endpoints-for-app-configuration"></a>Private Endpunkte für App Configuration 

Beim Erstellen eines privaten Endpunkts müssen Sie den App Configuration-Speicher angeben, mit dem er verbunden ist. Wenn Sie über mehrere App Configuration-Instanzen innerhalb eines Kontos verfügen, benötigen Sie einen separaten privaten Endpunkt für jeden Speicher.

### <a name="connecting-to-private-endpoints"></a>Herstellen einer Verbindung mit privaten Endpunkten

Azure basiert auf der DNS-Auflösung zum Weiterleiten von Verbindungen vom VNET zum Konfigurationsspeicher über einen privaten Link. Sie können Verbindungszeichenfolgen im Azure-Portal schnell finden, indem Sie Ihren App Configuration-Speicher und anschließend **Einstellungen** > **Zugriffsschlüssel** auswählen.  

> [!IMPORTANT]
> Verwenden Sie die Verbindungszeichenfolge, die Sie für einen öffentlichen Endpunkt verwenden würden, um eine Verbindung mit Ihrem App Configuration-Speicher über private Endpunkte herzustellen. Stellen Sie die Verbindung mit dem Speicher nicht mithilfe der `privatelink`-URL der Unterdomäne her.

## <a name="dns-changes-for-private-endpoints"></a>DNS-Änderungen für private Endpunkte

Wenn Sie einen privaten Endpunkt erstellen, wird der DNS-CNAME-Ressourceneintrag für den Konfigurationsspeicher auf einen Alias in einer Unterdomäne mit dem Präfix `privatelink` aktualisiert. Azure erstellt außerdem eine [private DNS-Zone](../dns/private-dns-overview.md), die der Unterdomäne `privatelink` entspricht, mit den DNS-A-Ressourceneinträgen für die privaten Endpunkte.

Wenn Sie die Endpunkt-URL innerhalb des virtuellen Netzwerks auflösen, das den privaten Endpunkt hostet, wird sie in den privaten Endpunkt des Speichers aufgelöst. Wenn Sie sie außerhalb des virtuellen Netzwerks auflösen, wird die Endpunkt-URL in den öffentlichen Endpunkt aufgelöst. Wenn Sie einen privaten Endpunkt erstellen, wird der öffentliche Endpunkt deaktiviert.

Wenn Sie einen benutzerdefinierten DNS-Server in Ihrem Netzwerk verwenden, müssen Clients in der Lage sein, den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) für den Dienstendpunkt in die IP-Adresse des privaten Endpunkts aufzulösen. Konfigurieren Sie den DNS-Server so, dass die Unterdomäne der privaten Verbindung an die private DNS-Zone für das VNET delegiert wird, oder konfigurieren Sie die A-Einträge für `AppConfigInstanceA.privatelink.azconfig.io` mit der IP-Adresse des privaten Endpunkts.

> [!TIP]
> Wenn Sie einen benutzerdefinierten oder lokalen DNS-Server verwenden, müssen Sie den DNS-Server so konfigurieren, dass der Speichername in der Unterdomäne `privatelink` in die IP-Adresse des privaten Endpunkts aufgelöst wird. Hierzu können Sie die Unterdomäne `privatelink` an die private DNS-Zone des VNET delegieren oder die DNS-Zone auf dem DNS-Server konfigurieren und die DNS-A-Einträge hinzufügen.

## <a name="pricing"></a>Preise

Zum Aktivieren privater Endpunkte ist ein App Configuration-Speicher des [Standard-Tarifs](https://azure.microsoft.com/pricing/details/app-configuration/) erforderlich.  Informationen zu Preisen für Private Link finden Sie unter [Azure Private Link – Preise](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Erstellen eines privaten Endpunkts für Ihren App Configuration-Speicher finden Sie in den folgenden Artikeln:

- [Erstellen eines privaten Endpunkts über das Private Link Center im Azure-Portal](../private-link/create-private-endpoint-portal.md)
- [Erstellen eines privaten Endpunkts mit Azure CLI](../private-link/create-private-endpoint-cli.md)
- [Erstellen eines privaten Endpunkts mit Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

Erfahren Sie, wie Sie Ihren DNS-Server mit privaten Endpunkten konfigurieren:

- [Namensauflösung für Ressourcen in virtuellen Azure-Netzwerken](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [DNS-Konfiguration für private Endpunkte](../private-link/private-endpoint-overview.md#dns-configuration)