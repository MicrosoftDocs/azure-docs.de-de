---
title: Übersicht über Hochverfügbarkeitsports in Azure
titlesuffix: Azure Load Balancer
description: Erfahren Sie mehr über Hochverfügbarkeitsports, die auf einem internen Load Balancer einen Lastenausgleich vornehmen.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2018
ms.author: kumud
ms.openlocfilehash: 328471292ea6cbe07e96cc18af7f9c524407de3d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60930289"
---
# <a name="high-availability-ports-overview"></a>Übersicht über Hochverfügbarkeitsports

Azure Standard Load Balancer unterstützt Sie beim gleichzeitigen Lastenausgleich von TCP- und UDP-Datenflüssen an allen Ports, wenn Sie einen internen Load Balancer verwenden. 

Eine Lastenausgleichsregel für Hochverfügbarkeitsports ist eine Variante einer Lastenausgleichsregel, die für eine interne Load Balancer Standard-Instanz konfiguriert wurde. Sie können die Verwendung eines Load Balancers vereinfachen, indem Sie eine einzelne Lastenausgleichsregel für den Lastenausgleich aller TCP- und UDP-Datenflüsse bereitstellen, die an allen Ports eines internen Standard Load Balancers eingehen. Die Entscheidung über den Lastenausgleich erfolgt pro Datenfluss. Diese Aktion basiert auf der folgenden Verbindung, die sich aus einem fünfteiligen Tupel zusammensetzt: Quell-IP-Adresse, Quellport, Ziel-IP-Adresse, Zielport und Protokoll.

Die Lastenausgleichsregeln für Hochverfügbarkeitsports unterstützen Sie bei wichtigen Szenarien, z. B. Hochverfügbarkeit und Skalierung für virtuelle Netzwerkgeräte in virtuellen Netzwerken. Das Feature kann auch hilfreich sein, wenn für eine große Anzahl von Ports ein Lastenausgleich vorgenommen werden muss. 

Die Lastenausgleichsregeln für Hochverfügbarkeitsports sind konfiguriert, wenn Sie für die Front-End- und Back-End-Ports **0** und für das Protokoll **Alle** festlegen. Die interne Load Balancer-Ressource nimmt dann für alle TCP- und UDP-Datenflüsse, unabhängig von der Portnummer, einen Lastenausgleich vor.

## <a name="why-use-ha-ports"></a>Gründe für die Verwendung von Hochverfügbarkeitsports

### <a name="nva"></a>Virtuelle Netzwerkgeräte

Sie können virtuelle Netzwerkgeräte (Network Virtual Appliances, NVAs) verwenden, um Ihre Azure-Workload vor verschiedenen Typen von Sicherheitsrisiken zu schützen. Wenn Sie virtuelle Netzwerkgeräte in diesen Szenarien verwenden, müssen sie zuverlässig, hochverfügbar und bedarfsgerecht horizontal hochskalierbar sein.

Sie können diese Ziele erreichen, indem Sie einfach NVA-Instanzen zum Back-End-Pool Ihres internen Load Balancers hinzufügen und eine Lastenausgleichsregel für Hochverfügbarkeitsports konfigurieren.

Für NVA-Hochverfügbarkeitsszenarien bieten Hochverfügbarkeitsports die folgenden Vorteile:
- Bieten schnelles Failover auf fehlerfreie Instanzen mit Integritätstests pro Instanz
- Gewährleisten höhere Leistung mit horizontaler Skalierung auf *n* aktive Instanzen
- Bieten Szenarien mit *n* aktiven sowie aktiven und passiven Instanzen
- Machen komplexe Lösungen wie Apache ZooKeeper-Knoten für die Überwachung von Geräten überflüssig

Das folgende Diagramm zeigt eine Nabe-zu-Speiche-Bereitstellung eines virtuellen Netzwerks. Die Speichen erzwingen das Tunneling für ihren Datenverkehr zum virtuellen Nabennetzwerk und über das virtuelle Netzwerkgerät, bevor der vertrauenswürdige Bereich verlassen wird. Die virtuellen Netzwerkgeräte befinden sich hinter einem internen Standard Load Balancer mit einer Konfiguration für die Hochverfügbarkeitsports. Der gesamte Datenverkehr kann entsprechend verarbeitet und weitergeleitet werden. Wenn die Konfiguration dem folgenden Diagramm entspricht, bietet eine Lastenausgleichsregel für Hochverfügbarkeitsports Flowsymmetrie für den ein- und ausgehenden Datenverkehr.

<a node="diagram"></a>
![Diagramm eines virtuellen Nabe-zu-Speiche-Netzwerks mit virtuellen Netzwerkgeräten, die im Hochverfügbarkeitsmodus bereitgestellt wurden](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Bei Verwendung von virtuellen Netzwerkgeräten überprüfen Sie bei den jeweiligen Anbietern, wie Hochverfügbarkeitsports optimal genutzt werden, und informieren Sie sich, welche Szenarien unterstützt werden.

### <a name="load-balancing-large-numbers-of-ports"></a>Lastenausgleich für eine große Anzahl von Ports

Sie können Hochverfügbarkeitsports auch für Anwendungen verwenden, in denen ein Lastenausgleich für eine große Anzahl von Ports erforderlich ist. Sie können diese Szenarien vereinfachen, indem Sie einen internen [Standard Load Balancer](load-balancer-standard-overview.md) mit Hochverfügbarkeitsports verwenden. Eine einzelne Lastenausgleichsregel ersetzt mehrere einzelne Lastenausgleichsregeln, die jeweils für einen Port verwendet wurden.

## <a name="region-availability"></a>Regionale Verfügbarkeit

Das Feature für Hochverfügbarkeitsports ist in allen Azure-Regionen weltweit verfügbar.

## <a name="supported-configurations"></a>Unterstützte Konfigurationen

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Eine einzige Non-Floating-IP-Konfiguration (kein Direct Server Return) für Hochverfügbarkeitsports auf einem internen Standard Load Balancer

Dies ist eine Basiskonfiguration für Hochverfügbarkeitsports. Mithilfe der folgenden Schritte können Sie eine Lastenausgleichsregel für Hochverfügbarkeitsports für eine einzelne Front-End-IP-Adresse konfigurieren:
1. Aktivieren Sie bei der Konfiguration von Standard Load Balancer das Kontrollkästchen **HA-Ports** in der Load Balancer-Regelkonfiguration.
2. Wählen Sie für **Floating IP** die Option **Deaktiviert** aus.

Diese Konfiguration erlaubt keine andere Regelkonfiguration für den Lastenausgleich auf der aktuellen Load Balancer-Ressource. Sie ermöglicht außerdem keine andere interne Load Balancer-Ressourcenkonfiguration für die angegebene Gruppe von Back-End-Instanzen.

Sie können jedoch zusätzlich zu dieser Regel für Hochverfügbarkeitsports einen öffentlichen Standard Load Balancer für die Back-End-Instanzen konfigurieren.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Eine einzige Floating-IP-Konfiguration (Direct Server Return) für Hochverfügbarkeitsports auf einem internen Standard Load Balancer

Sie können Load Balancer auch so konfigurieren, dass eine Lastenausgleichsregel mit **HA-Port** mit einem einzigen Front-End verwendet wird, indem Sie **Floating IP** auf **Aktiviert** setzen. 

Mithilfe dieser Konfiguration ist es Ihnen möglich, weitere Lastenausgleichsregeln für Floating IPs und/oder einen öffentlichen Load Balancer hinzuzufügen. Es ist jedoch nicht möglich, zusätzlich zu dieser Konfiguration eine Lastenausgleichskonfiguration für Hochverfügbarkeitsports mit Non-Floating IPs zu verwenden.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Mehrere Konfigurationen für Hochverfügbarkeitsports auf einem internen Standard Load Balancer

Wenn Ihr Szenario erfordert, dass Sie mehrere Front-Ends mit Hochverfügbarkeitsports für den gleichen Back-End-Pool konfigurieren, können Sie folgendermaßen vorgehen: 
- Konfigurieren Sie mehrere private Front-End-IP-Adressen für eine einzige interne Standard Load Balancer-Ressource.
- Konfigurieren Sie mehrere Lastenausgleichsregeln, wobei für jede Regel eine einzige eindeutige Front-End-IP-Adresse ausgewählt ist.
- Wählen Sie die Option **HA-Ports**, und setzen Sie dann für alle Lastenausgleichsregeln die Option **Floating IP** auf **Aktiviert**.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Ein interner Load Balancer mit Hochverfügbarkeitsports und ein öffentlicher Load Balancer auf derselben Back-End-Instanz

Sie können *eine* öffentliche Standard Load Balancer-Ressource für die Back-End-Ressourcen zusammen mit einem einzigen internen Standard Load Balancer mit Hochverfügbarkeitsports konfigurieren.

>[!NOTE]
>Diese Funktion ist derzeit über die Azure Resource Manager-Vorlagen verfügbar, jedoch nicht über das Azure-Portal.

## <a name="limitations"></a>Einschränkungen

- Die Konfiguration von Hochverfügbarkeitsports ist nur für interne Load Balancer verfügbar. Für öffentliche Load Balancer steht sie nicht zur Verfügung.

- Die Kombination einer Lastenausgleichsregel mit und einer ohne Hochverfügbarkeitsports wird nicht unterstützt.

- Das Feature für Hochverfügbarkeitsports ist für IPv6 nicht verfügbar.

- Flowsymmetrie (in erster Linie für Szenarien mit virtuellen Netzwerkgeräten) wird über die Back-End-Instanz und ein einzelnes NIC (und eine einzelne IP-Konfiguration) nur dann unterstützt, wenn der Einsatz wie im obigen Diagramm erfolgt und Lastenausgleichsregeln für Hochverfügbarkeitsports verwendet werden. In anderen Szenarien wird sie nicht unetrstützt. Dies bedeutet, dass zwei oder mehr Load Balancer-Ressourcen mit ihren jeweiligen Regeln unabhängige Entscheidungen treffen und nie miteinander koordiniert werden. Sehen Sie sich die Beschreibung und das Diagramm unter [Virtuelle Netzwerkgeräte](#nva) an. Wenn Sie mehrere NICs verwenden oder das virtuelle Netzwerkgerät in Sandwichmanier zwischen einem öffentlichen und internen Load Balancer einbetten, ist keine Flowsymmetrie verfügbar.  Sie können das Problem möglicherweise umgehen, indem Sie per Adressübersetzung den eingehenden Datenfluss an die IP-Adresse des Geräts leiten, um den Eingang von Antworten auf demselben virtuellen Netzwerkgerät zu ermöglichen.  Es wird allerdings dringend empfohlen, ein einzelnes NIC und die im obigen Diagramm gezeigte Referenzarchitektur zu verwenden.


## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Hochverfügbarkeitsports für einen internen Standard Load Balancer](load-balancer-configure-ha-ports.md)
- [Übersicht über Standard Load Balancer](load-balancer-standard-overview.md)
