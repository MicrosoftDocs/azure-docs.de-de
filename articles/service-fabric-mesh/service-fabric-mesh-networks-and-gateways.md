---
title: Einführung in Azure Service Fabric-Netzwerke
description: Erfahren Sie mehr zu Netzwerken, Gateways und intelligentem Datenverkehrsrouting in Service Fabric Mesh.
author: georgewallace
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: ef8b40be366afe24b960f7419d84cc2a9976b592
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625700"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Einführung in Netzwerke mit Service Fabric Mesh-Anwendungen

> [!IMPORTANT]
> Die Vorschauversion von Azure Service Fabric Mesh wurde eingestellt. Neue Bereitstellungen werden nicht mehr über die Service Fabric Mesh-API gestattet. Unterstützung für vorhandene Bereitstellungen wird bis zum 28. April 2021 fortgesetzt.
> 
> Einzelheiten finden Sie unter [Einstellung der Vorschauversion von Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

In diesem Artikel erfahren Sie mehr zu den verschiedenen Typen von Lastenausgleich, wie Gateways das Netzwerk mit Ihren Anwendungen mit anderen Netzwerken verbinden und wie Datenverkehr zwischen den Diensten in Ihren Anwendungen weitergeleitet wird.

## <a name="layer-4-vs-layer-7-load-balancers"></a>Lastenausgleich in Schicht 4 im Vergleich zu Schicht 7
Der Lastenausgleich für Netzwerke kann in unterschiedlichen Schichten des [OSI-Modells](https://en.wikipedia.org/wiki/OSI_model) erfolgen. Dies sind häufig Schicht 4 (L4) und Schicht 7 (L7).  In der Regel gibt es zwei Arten von Lastenausgleichsmodulen:

- Ein L4-Lastenausgleichsmodul wird in der Netzwerktransportschicht ausgeführt, die sich mit der Übermittlung von Paketen befasst, unabhängig von deren Inhalt. Nur Paketheader werden vom Lastenausgleichsmodul inspiziert. Daher sind die Ausgleichskriterien auf IP-Adressen und Ports beschränkt. Ein Beispiel: Ein Client stellt eine TCP-Verbindung zum Lastenausgleichsmodul her. Das Lastenausgleichsmodul beendet die Verbindung (als direkte Reaktion auf das SYN-Paket), wählt ein Back-End aus, und stellt eine neue TCP-Verbindung zum Back-End her (sendet ein neues SYN-Paket). Ein L4-Lastenausgleich erfolgt typischerweise nur in der Schicht der L4-TCP/UDP-Verbindung oder -Sitzung. So leitet das Lastenausgleichsmodul die Bytes um und stellt sicher, dass Bytes aus der gleichen Sitzung auch am gleichen Back-End ankommen. Die Anwendungsdetails der Bytes, die beim L4-Lastenausgleich weitergeleitet werden, spielen keine Rolle. Die Bytes können jedes beliebige Anwendungsprotokoll sein.

- Ein L7-Lastenausgleich erfolgt in der Anwendungsschicht, die sich mit dem Inhalt jedes Pakets befasst. Protokolle wie HTTP, HTTPS oder WebSockets werden gelesen und die Paketinhalte entsprechend geprüft. Dies gibt dem Lastenausgleichsmodul die Möglichkeit, ein erweitertes Routing durchzuführen. Ein Beispiel: Ein Client stellt eine einzige HTTP/2-TCP-Verbindung zum Lastenausgleichsmodul her. Das Lastenausgleichsmodul stellt dann zwei Back-End-Verbindungen her. Wenn der Client zwei HTTP/2-Streams an das Lastenausgleichsmodul sendet, wird der erste Stream an das erste Back-End und der zweite Stream an das zweite Back-End gesendet. So werden selbst bei Multiplexingclients die sehr unterschiedlichen Anforderungslasten effizient über die Back-Ends ausgeglichen. 

## <a name="networks-and-gateways"></a>Netzwerke und Gateways
Im [Service Fabric-Ressourcenmodell](service-fabric-mesh-service-fabric-resources.md) ist eine Netzwerkressource eine separat bereitstellbare Ressource. Es besteht keine Abhängigkeit zwischen ihr und anderen Anwendungen oder Dienstressourcen. Sie wird verwendet, um ein Netzwerk mit Internetzugang für Ihre Anwendungen zu erstellen. Mehrere Dienste aus verschiedenen Anwendungen können Teil desselben Netzwerks sein. Dieses private Netzwerk wird von Service Fabric erstellt und verwaltet und ist kein virtuelles Azure-Netzwerk (VNET). Anwendungen können dynamisch hinzugefügt und aus der Netzwerkressource entfernt werden, um die VNET-Konnektivität zu aktivieren bzw. zu deaktivieren. 

Zum Überbrücken zweier Netzwerke wird ein Gateway verwendet. Die Gatewayressource stellt einen [Envoy-Proxy](https://www.envoyproxy.io/) bereit, der L4-Routing für alle Protokolle und L7-Routing für erweitertes HTTP(S)-Anwendungsrouting bietet. Das Gateway leitet den Datenverkehr von einem externen Netzwerk in Ihr Netzwerk weiter und ermittelt, zu welchem Dienst der Datenverkehr weitergeleitet werden soll.  Das externe Netzwerk kann ein offenes Netzwerk (im Grunde genommen das öffentliche Internet) oder ein virtuelles Azure-Netzwerk sein, sodass Sie sich mit Ihren anderen Azure-Anwendungen und -Ressourcen verbinden können. 

![Netzwerk und Gateway][Image1]

Beim Erstellen der Netzwerkressource mit `ingressConfig` wird dieser eine öffentliche IP-Adresse zugewiesen. Die öffentliche IP-Adresse ist dann an die Lebensdauer der Netzwerkressource gebunden.

Wenn eine Mesh-Anwendung erstellt wird, sollte sie auf eine vorhandene Netzwerkressource verweisen. Neue öffentliche Ports können hinzugefügt oder vorhandene Ports aus der Eingangskonfiguration entfernt werden. Eine Netzwerkressource kann nicht gelöscht werden, wenn eine Anwendungsressource darauf verweist. Wenn die Anwendung gelöscht wird, wird auch die Netzwerkressource entfernt.

## <a name="next-steps"></a>Nächste Schritte 
In der Übersicht erfahren Sie mehr über Service Fabric Mesh:
- [Übersicht über Service Fabric Mesh](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png