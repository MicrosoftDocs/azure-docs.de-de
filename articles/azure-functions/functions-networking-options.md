---
title: Netzwerkoptionen von Azure Functions
description: Enthält eine Übersicht über alle Netzwerkoptionen, die in Azure Functions verfügbar sind.
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: f13e498859986d5ee697cbd67907fd344147ed0c
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66492834"
---
# <a name="azure-functions-networking-options"></a>Netzwerkoptionen von Azure Functions

In diesem Artikel werden die Netzwerkfunktionen beschrieben, die für die Hostingoptionen von Azure Functions zur Verfügung stehen. Alle folgenden Netzwerkoptionen verfügen über die Möglichkeit zum Zugreifen auf Ressourcen ohne Verwendung von Adressen, die über das Internet geroutet werden können, oder die Einschränkung des Internetzugriffs auf eine Funktions-App. 

Die Hostingmodelle weisen verschiedene verfügbare Ebenen der Netzwerkisolation auf. Durch Auswahl der jeweils richtigen Ebene lassen sich Ihre Anforderungen an die Netzwerkisolation erfüllen.

Sie können Funktions-Apps auf verschiedene Arten hosten:

* Es gibt eine Gruppe von Tarifoptionen, die in einer mehrinstanzenfähigen Infrastruktur ausgeführt werden und über unterschiedliche Ebenen von Konnektivitäts- und Skalierungsoptionen für virtuelle Netzwerke verfügen:
    * Der [Verbrauchstarif](functions-scale.md#consumption-plan), der als Reaktion auf Last dynamisch skaliert wird und nur über Optionen für eine minimale Netzwerkisolation verfügt.
    * Der [Premium-Tarif](functions-scale.md#premium-plan-public-preview), bei dem ebenfalls dynamisch skaliert wird, der aber eine umfassendere Netzwerkisolation ermöglicht.
    * Der [Azure App Service-Plan](functions-scale.md#app-service-plan), bei dem eine feste Skalierung verwendet wird und der eine ähnliche Netzwerkisolation wie der Premium-Tarif aufweist.
* Sie können Funktionen in einer [App Service-Umgebung](../app-service/environment/intro.md) ausführen. Mit dieser Methode werden die Funktionen in Ihrem virtuellen Netzwerk bereitgestellt und eine umfassende Netzwerksteuerung und -isolation ermöglicht.

## <a name="matrix-of-networking-features"></a>Matrix der Netzwerkfunktionen

|                |[Verbrauchstarif](functions-scale.md#consumption-plan)|[Premium-Plan (Vorschauversion)](functions-scale.md#premium-plan-public-preview)|[App Service-Plan](functions-scale.md#app-service-plan)|[App Service-Umgebung](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[IP-Einschränkungen für eingehenden Datenverkehr](#inbound-ip-restrictions)|✅ Ja|✅ Ja|✅ Ja|✅ Ja|
|[IP-Einschränkungen für ausgehenden Datenverkehr](#private-site-access)|❌ Nein| ❌ Nein|❌ Nein|✅ Ja|
|[Integration in ein virtuelles Netzwerk](#virtual-network-integration)|❌ Nein|❌ Nein|✅ Ja|✅ Ja|
|[Vorschau der Integration des virtuellen Netzwerks (Azure ExpressRoute und Dienstendpunkte ausgehend)](#preview-version-of-virtual-network-integration)|❌ Nein|✅ Ja|✅ Ja|✅ Ja|
|[Hybridverbindungen](#hybrid-connections)|❌ Nein|❌ Nein|✅ Ja|✅ Ja|
|[Privater Websitezugriff](#private-site-access)|❌ Nein| ✅ Ja|✅ Ja|✅ Ja|

## <a name="inbound-ip-restrictions"></a>IP-Einschränkungen für eingehenden Datenverkehr

Mit IP-Einschränkungen können Sie eine nach Priorität sortierte Liste mit IP-Adressen definieren, über die der Zugriff auf Ihre App zugelassen bzw. abgelehnt wird. Die Liste kann IPv4- und IPv6-Adressen enthalten. Wenn mindestens ein Eintrag vorhanden ist, enthält die Liste am Ende einen impliziten Eintrag vom Typ „Alle ablehnen“. IP-Einschränkungen können für alle Hostingoptionen für Funktionen verwendet werden.

> [!NOTE]
> Um den Azure-Portal-Editor verwenden zu können, muss im Portal direkt auf Ihre ausgeführte Funktions-App zugegriffen werden können. Außerdem muss das Gerät, über das Sie auf das Portal zugreifen, in einer IP-Whitelist aufgeführt sein. Wenn die Netzwerkeinschränkungen eingerichtet sind, können Sie weiterhin auf die Funktionen der Registerkarte **Plattformfeatures** zugreifen.

Weitere Informationen finden Sie unter [Azure App Service – statische Zugriffseinschränkungen](../app-service/app-service-ip-restrictions.md).

## <a name="outbound-ip-restrictions"></a>IP-Einschränkungen für ausgehenden Datenverkehr

IP-Einschränkungen für ausgehenden Datenverkehr sind nur für Funktionen verfügbar, die in einer App Service-Umgebung bereitgestellt werden. Sie können ausgehende Einschränkungen für das virtuelle Netzwerk konfigurieren, wo Ihre App Service-Umgebung bereitgestellt wird.

## <a name="virtual-network-integration"></a>Integration in ein virtuelles Netzwerk

Durch die Integration des virtuellen Netzwerks kann die Funktions-App auf Ressourcen eines virtuellen Netzwerks zugreifen. Diese Funktion ist sowohl für den Premium-Tarif als auch für den App Service-Plan verfügbar. In einer App Service-Umgebung befindet sich Ihre App bereits in einem virtuellen Netzwerk, sodass die Integration des virtuellen Netzwerks nicht erforderlich ist, um Ressourcen im selben virtuellen Netzwerk zu erreichen.

Die Integration des virtuellen Netzwerks ermöglicht Ihrer Funktions-App den Zugriff auf Ressourcen in Ihrem virtuellen Netzwerk, gewährt aber keinen [privaten Websitezugriff](#private-site-access) auf Ihre Funktions-App aus dem virtuellen Netzwerk.

Mithilfe der Integration des virtuellen Netzwerks kann der Zugriff von Apps auf Datenbanken und Webdienste im virtuellen Netzwerk ermöglicht werden. Mit der Integration des virtuellen Netzwerks müssen Sie keinen öffentlichen Endpunkt für Anwendungen auf Ihrem virtuellen Computer verfügbar machen. Stattdessen können Sie die privaten Adressen verwenden, die nicht über das Internet geroutet werden.

Die allgemein verfügbare Version der Integration des virtuellen Netzwerks beruht auf einem VPN-Gateway zum Verbinden von Funktions-Apps mit einem virtuellen Netzwerk. Sie ist verfügbar, wenn Functions unter einem App Service-Plan gehostet wird. Informationen zum Konfigurieren der Funktion finden Sie unter [Integrieren Ihrer App in ein virtuelles Azure-Netzwerk](../app-service/web-sites-integrate-with-vnet.md).

### <a name="preview-version-of-virtual-network-integration"></a>Vorschauversion der Integration des virtuellen Netzwerks

Eine neue Version der Funktion für die Integration des virtuellen Netzwerks befindet sich in der Vorschauphase. Sie ist nicht von einer Point-to-Site-VPN-Verbindung abhängig. Sie unterstützt den Zugriff auf Ressourcen über ExpressRoute oder Dienstendpunkte. Sie ist unter dem Premium-Tarif und unter App Service-Plänen mit Skalierung auf PremiumV2 verfügbar.

Im Folgenden sind einige Merkmale dieser Version aufgeführt:

* Zur Verwendung dieser Version ist kein Gateway erforderlich.
* Sie können ohne zusätzliche Konfiguration bis auf die Integration mit dem mit ExpressRoute verbundenen virtuelle Netzwerk über ExpressRoute-Verbindungen auf Ressourcen zugreifen.
* Sie können mit Dienstendpunkten geschützte Ressourcen über die ausgeführte Functions-Instanz nutzen. Aktivieren Sie dazu Dienstendpunkte in dem für die Integration des virtuellen Netzwerks verwendeten Subnetz.
* Sie können keine Trigger für die Nutzung von mit Dienstendpunkten geschützten Ressourcen konfigurieren. 
* Die Funktions-App und das virtuelle Netzwerk müssen sich in derselben Region befinden.
* Die neue Funktion erfordert ein nicht verwendetes Subnetz in dem virtuellen Netzwerk, das Sie über Azure Resource Manager bereitgestellt haben.
* Produktionsworkloads werden nicht unterstützt, während sich die neue Funktion in der Vorschauphase befindet.
* Routingtabellen und globales Peering sind mit der Funktion noch nicht verfügbar.
* Für jede potenzielle Instanz einer Funktions-App wird eine Adresse verwendet. Verwenden Sie ein Subnetz, in dem Ihre maximale Skalierungsgröße problemlos unterstützt wird, da die Subnetzgröße nach dem Zuweisen nicht mehr geändert werden kann. Zur Unterstützung eines Premium-Tarifs, bei dem auf bis zu 80 Instanzen skaliert werden kann, empfehlen wir beispielsweise ein `/25`-Subnetz mit 126 Hostadressen.

Weitere Informationen zur Verwendung der Vorschauversion der Integration virtueller Netzwerke finden Sie unter [Integrieren einer Funktions-App in ein virtuelles Azure-Netzwerk](functions-create-vnet.md).

## <a name="hybrid-connections"></a>Hybridverbindungen

[Hybrid Connections](../service-bus-relay/relay-hybrid-connections-protocol.md) ist ein Feature von Azure Relay, das Sie zum Zugreifen auf Anwendungsressourcen in anderen Netzwerken verwenden können. Es ermöglicht den Zugriff von Ihrer App auf einen Anwendungsendpunkt. Sie können es nicht verwenden, um auf Ihre Anwendung zuzugreifen. Hybrid Connections ist für Funktionen verfügbar, die unter einem [App Service-Plan](functions-scale.md#app-service-plan) und in einer [App Service-Umgebung](../app-service/environment/intro.md) ausgeführt werden.

Bei der Verwendung in Azure Functions entspricht jede Hybridverbindung einer Kombination aus einem einzelnen TCP-Host und einem Port. Dies bedeutet, dass sich der Hybridverbindungsendpunkt in einem beliebigen Betriebssystem und einer beliebigen Anwendung befinden kann, solange der Zugriff über einen TCP-Lauschport erfolgt. Das Feature „Hybrid Connections“ verfügt nicht über Informationen zum Anwendungsprotokoll oder zum abzurufenden Inhalt und benötigt diese Informationen auch nicht. Es ermöglicht lediglich den Netzwerkzugriff.

Weitere Informationen finden Sie in der [App Service-Dokumentation zu Hybrid Connections](../app-service/app-service-hybrid-connections.md). Functions wird unter einem App Service-Tarif unterstützt.

## <a name="private-site-access"></a>Privater Websitezugriff

Privater Websitezugriff bezieht sich darauf, den Zugriff auf Ihre App nur über ein privates Netzwerk zuzulassen, z. B. über ein virtuelles Azure-Netzwerk. 
* Zugriff auf private Sites ist im Premium- und App Service-Plan verfügbar, wenn **Dienstendpunkte** konfiguriert sind. Weitere Informationen finden Sie unter [ virtueller Netzwerke](../virtual-network/virtual-network-service-endpoints-overview.md)
    * Bedenken Sie, dass Ihre Funktion auch mit Dienstendpunkten immer noch vollständigen ausgehenden Zugriff auf das Internet besitzt, selbst wenn VNET-Integration konfiguriert ist.
* Der private Websitezugriff ist nur verfügbar, wenn eine App Service-Umgebung mit einem internen Lastenausgleich (ILB) konfiguriert ist. Weitere Informationen finden Sie unter [Erstellen und Verwenden eines internen Lastenausgleichs mit einer App Service-Umgebung](../app-service/environment/create-ilb-ase.md).

Es gibt viele Möglichkeiten, um in anderen Hostingoptionen auf Ressourcen des virtuellen Netzwerks zuzugreifen. Jedoch kann nur über eine App Service-Umgebung zugelassen werden, dass Trigger für eine Funktion über ein virtuelles Netzwerk verwendet werden.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Netzwerk und zu Azure Functions: 

* [Tutorial zum Einstieg in die Integration des virtuellen Netzwerks](./functions-create-vnet.md)
* [Häufig gestellte Fragen zu Netzwerken in Functions](./functions-networking-faq.md)
* [Weitere Informationen zur Integration des virtuellen Netzwerks mit App Service und Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Weitere Informationen zu virtuellen Netzwerken in Azure](../virtual-network/virtual-networks-overview.md)
* [Aktivieren weiterer Netzwerkfunktionen und Steuerung mit App Service-Umgebungen](../app-service/environment/intro.md)
* [Verbinden mit einzelnen lokalen Ressourcen ohne Änderungen an der Firewall mithilfe von Hybrid Connections](../app-service/app-service-hybrid-connections.md)
