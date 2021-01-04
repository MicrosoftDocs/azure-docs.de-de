---
title: 'Häufig gestellte Fragen: Azure Load Balancer'
description: Antworten auf häufig gestellte Fragen zu Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 2e559d574413b8eb0be2303798e0b16bfffad2cb
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695400"
---
# <a name="load-balancer-frequently-asked-questions"></a>Häufig gestellte Fragen zu Load Balancer

## <a name="what-types-of-load-balancer-exist"></a>Welche Typen von Load Balancer sind vorhanden?
Interne Lastenausgleichsmodule, die Datenverkehr in einem VNET ausgleichen, und externe Lastenausgleichsmodule, die Datenverkehr zu und von einem mit dem Internet verbundenen Endpunkt ausgleichen. Weitere Informationen finden Sie unter [Load Balancer-Typen](components.md#frontend-ip-configurations). 

Für beide Typen bietet Azure eine Basic-SKU und eine Standard-SKU mit unterschiedlichen Möglichkeiten für Funktion, Leistung, Sicherheit und Integritätsnachverfolgung. Diese Unterschiede werden im Artikel zum [SKU-Vergleich](skus.md) erläutert.

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Wie kann ich ein Upgrade von Load Balancer Basic-auf Load Balancer Standard durchführen?
Ein automatisiertes Skript und Anleitungen zum Upgrade einer Load Balancer-SKU finden Sie im Artikel [Upgrade von Basic auf Standard](upgrade-basic-standard.md).

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Was sind die verschiedenen Lastenausgleichsoptionen in Azure?
Informationen zu den verfügbaren Lastenausgleichsdiensten und den jeweils empfohlenen Verwendungsmöglichkeiten finden Sie im [Technologieleitfaden zum Lastenausgleich](/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Wo finde ich Load Balancer ARM-Vorlagen?
Weitere Informationen zu ARM-Vorlagen für allgemeine Bereitstellungen finden Sie in der [Liste der Azure Load Balancer-Schnellstartvorlagen](/azure/templates/microsoft.network/loadbalancers#quickstart-templates).

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>Wie unterscheiden sich NAT-Regeln für eingehenden Datenverkehr von Lastenausgleichsregeln?
NAT-Regeln werden verwendet, um eine Back-End-Ressource anzugeben, an die Datenverkehr weitergeleitet werden soll. Beispielsweise das Konfigurieren eines bestimmten Load Balancer-Ports zum Senden von RDP-Datenverkehr an eine bestimmte VM. Lastenausgleichsregeln werden verwendet, um einen Pool mit Back-End-Ressourcen anzugeben, an die Datenverkehr weitergeleitet werden soll, damit die Last instanzenübergreifend verteilt wird. Beispielsweise kann eine Lastenausgleichsregel TCP-Pakete an Port 80 des Load Balancers über einen Pool von Webservern weiterleiten.

## <a name="what-is-ip-1686312916"></a>Was ist IP 168.63.129.16?
Die virtuelle IP-Adresse für den Host, der als Load Balancer für die Azure-Infrastruktur gekennzeichnet ist und als Ausgangspunkt für die Integritätstests von Azure dient. Beim Konfigurieren von Back-End-Instanzen müssen diese Datenverkehr von dieser IP-Adresse zulassen, um erfolgreich auf Integritätstests reagieren zu können. Diese Regel interagiert nicht mit dem Zugriff auf das Load Balancer-Front-End. Sie können diese Regel außer Kraft setzen, wenn Sie den Azure Load Balancer nicht verwenden. Weitere Informationen zu Diensttags finden Sie [hier](../virtual-network/service-tags-overview.md#available-service-tags).

## <a name="can-i-use-global-vnet-peering-with-basic-load-balancer"></a>Kann ich das globale VNet-Peering mit Load Balancer Basic verwenden?
Nein. Load Balancer Basic unterstützt kein globales VNet-Peering. Sie können stattdessen Load Balancer Standard verwenden. Informationen zu einem nahtlosen Upgrade finden Sie im Artikel [Aktualisieren von „Basic“ auf „Standard“](upgrade-basic-standard.md).

## <a name="how-can-i-discover-the-public-ip-that-an-azure-vm-uses"></a>Wie kann ich die von einer Azure-VM verwendete öffentliche IP-Adresse ermitteln?

Es gibt viele Möglichkeiten, die öffentliche IP-Quelladresse einer ausgehenden Verbindung zu bestimmen. OpenDNS bietet einen Dienst, der die öffentliche IP-Adresse Ihrer VM anzeigen kann.
Mit dem Befehl „nslookup“ können Sie eine DNS-Abfrage für den Namen „myip.opendns.com“ an den OpenDNS-Resolver senden. Der Dienst gibt die IP-Quelladresse zurück, die zum Senden der Abfrage verwendet wurde. Wenn Sie die folgende Abfrage auf Ihrem virtuellen Computer ausführen, wird die öffentliche IP-Adresse zurückgegeben, die für diesen virtuellen Computer verwendet wird:

 ```nslookup myip.opendns.com resolver1.opendns.com```

## <a name="how-do-connections-to-azure-storage-in-the-same-region-work"></a>Wie funktionieren Verbindungen mit Azure Storage in derselben Region?
Eine ausgehende Konnektivität mithilfe der oben genannten Szenarios ist nicht erforderlich, um eine Verbindung mit Azure Storage in derselben Region wie die VM herzustellen. Alternativ können Sie NSGs (Netzwerksicherheitsgruppen) verwenden, wenn Sie dies nicht möchten. Für Verbindungen mit Azure Storage in anderen Regionen ist die ausgehende Konnektivität erforderlich. Beachten Sie, dass es sich bei der Quell-IP-Adresse in den Azure Storage-Diagnoseprotokollen um eine interne Anbieteradresse handelt und nicht um die öffentliche IP-Adresse Ihrer VM, wenn Sie eine Verbindung mit Azure Storage über eine VM in derselben Region herstellen. Wenn Sie den Zugriff auf Ihr Azure Storage-Konto für VMs in mindestens einem Subnetz des virtuellen Netzwerks innerhalb derselben Region einschränken möchten, sollten Sie [VNET-Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) anstelle Ihrer öffentlichen IP-Adresse verwenden, wenn Sie die Firewall für Ihr Speicherkonto konfigurieren. Sobald die Dienstendpunkte konfiguriert wurden, wird Ihre private VNET-IP-Adresse in Ihren Azure Storage-Diagnoseprotokollen anstelle der internen Anbieteradresse angezeigt.

## <a name="what-are-best-practises-with-respect-to-outbound-connectivity"></a>Welche Best Practices gelten in Bezug auf ausgehende Verbindungen?
Load Balancer Standard und Standard Public IP führen Fähigkeiten und andere Verhaltensweisen für ausgehende Verbindungen ein. Sie sind nicht identisch mit Basic-SKUs. Wenn Sie bei der Arbeit mit Standard-SKUs ausgehende Verbindungen wünschen, müssen Sie diese explizit entweder mit Standard Public IP-Adressen oder Standard Public Load Balancer definieren. Dies umfasst die Erstellung von ausgehenden Verbindungen bei der Verwendung eines internen Load Balancer Standard. Es wird empfohlen, dass Sie für einen Standard Public Load Balancer immer Ausgangsregeln verwenden. Das bedeutet, dass Sie bei Verwendung eines internen Load Balancer Standard Schritte unternehmen müssen, um die ausgehenden Verbindungen für die VMs im Back-End-Pool herzustellen, wenn ausgehende Verbindungen gewünscht sind. Im Kontext der ausgehenden Verbindungen verhalten sich eine einzelne eigenständige VM, alle VMs in einer Verfügbarkeitsgruppe sowie alle Instanzen in einem VMSS wie eine Gruppe. Das bedeutet, wenn eine einzelne VM in einer Verfügbarkeitsgruppe einer Standard-SKU zugeordnet ist, verhalten sich jetzt alle VM-Instanzen in dieser Verfügbarkeitsgruppe nach denselben Regeln wie bei der Zuordnung zu einer Standard-SKU, auch wenn eine einzelne Instanz nicht direkt mit ihr verbunden ist. Dieses Verhalten wird auch bei einem eigenständigen virtuellen Computer mit mehreren Netzwerkschnittstellenkarten beobachtet, die an einen Lastenausgleich angeschlossen sind. Wenn eine eigenständige NIC hinzugefügt wird, zeigt sie dasselbe Verhalten. Lesen Sie dieses Dokument sorgfältig und vollständig, um die allgemeinen Konzepte zu verstehen, überprüfen Sie [Load Balancer Standard](./load-balancer-overview.md) auf Unterschiede zwischen den SKUs, und überprüfen Sie die [Ausgangsregeln](load-balancer-outbound-connections.md#outboundrules).
Die Verwendung von Ausgangsregeln ermöglicht Ihnen eine differenzierte Steuerung aller Aspekte ausgehender Verbindungen.
 
## <a name="next-steps"></a>Nächste Schritte
Wenn Ihre Frage oben nicht aufgeführt wird, senden Sie uns Feedback zu dieser Seite mit Ihrer Frage. Dadurch wird ein Issue auf GitHub für das Produktteam erstellt, um sicherzustellen, dass alle wichtigen Fragen unserer Kunden beantwortet werden.