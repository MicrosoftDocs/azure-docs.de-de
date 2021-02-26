---
title: Verteilungsmodi von Azure Load Balancer
description: Machen Sie sich mit den verschiedenen Verteilungsmodi von Azure Load Balancer vertraut.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 02/04/2021
ms.custom: template-concept
ms.openlocfilehash: fcea2e962722773f59e73df898e0188c3f6454b6
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551100"
---
# <a name="azure-load-balancer-distribution-modes"></a>Verteilungsmodi von Azure Load Balancer

Azure Load Balancer unterstützt zwei Verteilungsmodi für das Routing von Verbindungen mit Ihrer Anwendung mit Lastenausgleich:

* Hashbasiert
* Quell-IP-Affinität

## <a name="hash-based"></a>Hashbasiert

Der Standardverteilungsmodus für Azure Load Balancer ist ein Fünf-Tupel-Hash. 

Das Tupel besteht aus den folgenden Elementen:
* **Quell-IP**
* **Quellport**
* **Ziel-IP**
* **Zielport**
* **Protokolltyp**

Der Hash wird verwendet, um Datenverkehr den verfügbaren Servern zuzuordnen. Der Algorithmus stellt Bindung nur innerhalb einer Transportsitzung bereit. Pakete in der gleichen Sitzung werden an die gleiche Instanz der Rechenzentrums-IP hinter dem Endpunkt mit Lastenausgleich weitergeleitet. Wenn der Client eine neue Sitzung über die gleiche Quell-IP startet, wird der Quellport geändert, wodurch der Datenverkehr an einen anderen Rechenzentrumendpunkt weitergeleitet wird.

![Auf 5-Tupel-Hash basierender Verteilungsmodus](./media/distribution-mode-concepts/load-balancer-distribution.png)

Der hashbasierte Modus weist einen Konfigurationstyp auf:

* **None (hash-based)** (Keine (Hash-basiert)): Gibt an, dass aufeinander folgende Anforderungen vom selben Client von jedem virtuellen Computer verarbeitet werden können.

## <a name="source-ip-affinity"></a>Quell-IP-Affinität

Dieser Verteilungsmodus ist auch als Sitzungsaffinität oder Client-IP-Affinität bekannt. Der Modus verwendet einen Zwei-Tupel-Hash (Quell-IP und Ziel-IP) oder Drei-Tupel-Hash (Quell-IP, Ziel-IP und Protokolltyp) zum Zuordnen des Datenverkehrs zu verfügbaren Servern. 

Mithilfe der Quell-IP-Affinität werden Verbindungen, die vom gleichen Clientcomputer gestartet werden, an den gleichen Rechenzentrumendpunkt geleitet.

Die folgende Abbildung veranschaulicht eine Konfiguration mit zwei Tupeln. Beachten Sie, wie der Zwei-Tupel-Hash durch den Lastenausgleich zum ersten virtuellen Computer (VM1) führt. VM1 wird dann durch VM2 und VM3 abgesichert.

![Verteilungsmodus „Zwei-Tupel-Sitzungsaffinität“](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Der Modus „Quell-IP-Affinität“ bietet zwei Konfigurationstypen:

* **Client IP (source IP affinity 2-tuple)** (Client-IP (Quell-IP-Affinität 2-tupel)): Gibt an, dass aufeinanderfolgende Anforderungen von derselben Client-IP-Adresse vom selben virtuellen Computer verarbeitet werden.
* **Client IP and protocol (source IP affinity 3-tuple)** (Client-IP und Protokoll (Quell-IP-Affinität 3-tupel)): Gibt an, dass aufeinanderfolgende Anforderungen von derselben Kombination aus Client-IP-Adresse und Protokoll vom selben virtuellen Computer verarbeitet werden.

## <a name="use-cases"></a>Anwendungsfälle

Mit dem Modus „Quell-IP-Affinität“ mit Client-IP-Adresse und Protokoll (3-Tupel) wird das Problem der Inkompatibilität zwischen Azure Load Balancer und Remotedesktopgateway (RD-Gateway) gelöst. 

Ein weiterer Anwendungsfall ist das Hochladen von Medien. Der Datenupload erfolgt über UDP, während die Steuerungsebene über TCP realisiert wird:

* Ein Client startet eine TCP-Sitzung zur öffentlichen Adresse mit Lastenausgleich und wird zu einer bestimmten DIP-Adresse geleitet. Der Kanal bleibt zur Überwachung der Verbindungsintegrität aktiv.
* Es wird eine neue UDP-Sitzung vom gleichen Clientcomputer zum gleichen öffentlichen Endpunkt mit Lastenausgleich gestartet. Die Verbindung wird zum gleichen DIP-Endpunkt wie bei der vorherigen TCP-Verbindung geleitet. Das Hochladen von Medien kann mit hohem Durchsatz erfolgen, während gleichzeitig ein Steuerkanal über TCP betrieben wird.

> [!NOTE]
> Bei einer Änderung der Lastenausgleichsgruppe durch Entfernen oder Hinzufügen eines virtuellen Computers wird die Verteilung der Clientanforderungen neu berechnet. Sie können sich nicht darauf verlassen, dass neue Verbindungen von vorhandenen Clients beim gleichen Server landen. Darüber hinaus kann die Verwendung des Quell-IP-Affinitäts-Verteilungsmodus zu einer ungleichen Verteilung des Datenverkehrs führen. Clients, die hinter Proxys ausgeführt werden, können als eine einzige Clientanwendung betrachtet werden.


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Konfigurieren des Verteilungsmodus von Azure Load Balancer finden Sie unter [Konfigurieren des Verteilungsmodus für Azure Load Balancer](load-balancer-distribution-mode.md).

