---
title: Load Balancer mit TCP-Rücksetzung bei Leerlauf
titlesuffix: Azure Load Balancer
description: Load Balancer mit bidirektionalen TCP-RST-Paketen bei Leerlauftimeout
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2019
ms.author: kumud
ms.openlocfilehash: 4a09492fcb8a7985fa27b6daae89aa5dec0fa6e0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65413854"
---
# <a name="load-balancer-with-tcp-reset-on-idle-public-preview"></a>Load Balancer mit TCP-Rücksetzung bei Leerlauf (öffentliche Vorschau)

Sie können [Load Balancer Standard ](load-balancer-standard-overview.md) verwenden, um ein besser vorhersagbares Anwendungsverhalten für Ihre Szenarien zu erzielen, indem Sie für eine bestimmte Regel die TCP-Rücksetzung bei Leerlauf aktivieren. Das Standardverhalten von Load Balancer besteht darin, Flows ohne Rückmeldung zu verwerfen, wenn das Leerlauftimeout eines Flows erreicht ist.  Das Aktivieren dieser Funktion bewirkt, dass Load Balancer bidirektionale TCP-Rücksetzungen (TCP-RST-Paket) bei einem Leerlauftimeout sendet.  Dadurch werden Ihre Anwendungsendpunkte darüber informiert, dass bei der Verbindung ein Timeout aufgetreten ist und sie nicht mehr verwendet werden kann.  Die Endpunkte können dann bei Bedarf sofort eine neue Verbindung herstellen.

![Load Balancer mit TCP-Rücksetzung](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)

>[!NOTE] 
>Die Funktion von Load Balancer mit TCP-Rücksetzung bei Leerlauftimeout ist zurzeit als Public Preview verfügbar. Diese Vorschau wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Ergänzende Nutzungsbedingungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
Sie ändern dieses Standardverhalten und ermöglichen das Senden von TCP-Rücksetzungen bei Leerlauftimeout in NAT-Eingangsregeln, Lastenausgleichsregeln und [Ausgangsregeln](https://aka.ms/lboutboundrules).  Wenn dies durch eine Regel aktiviert ist, sendet Load Balancer zum Zeitpunkt des Leerlauftimeouts für alle passenden Flows bidirektionale TCP-Rücksetzungen (TCP-RST-Pakete) sowohl an den Client- als auch an den Serverendpunkt.

Endpunkte, die TCP-RST-Pakete empfangen, schließen den entsprechenden Socket sofort. Damit werden Endpunkte sofort darüber benachrichtigt, dass die Verbindung getrennt wurde und jegliche weitere Kommunikation über die gleiche TCP-Verbindung zu einem Fehler führt.  Anwendungen können Verbindungen bereinigen, wenn der Socket geschlossen wird, und Verbindungen bei Bedarf erneut herstellen, ohne auf das Timeout der TCP-Verbindung zu warten.

In vielen Szenarien müssen damit weniger TCP-Keepalives (bzw. Keepalives auf der Anwendungsschicht) gesendet werden, um das Leerlauftimeout eines Flows zu aktualisieren. 

Wenn die Leerlaufzeiträume die in der Konfiguration erlaubten Werte überschreiten oder Ihre Anwendung mit aktivierten TCP-Rücksetzungen ein unerwünschtes Verhalten zeigt, müssen Sie diese TCP-Keepalives (bzw. Keepalives auf der Anwendungsschicht) möglicherweise weiterhin verwenden, um die Verfügbarkeit der TCP-Verbindungen zu überwachen.  Keepalives (insbesondere Keepalives auf der Anwendungsschicht) sind zudem weiterhin nützlich, wenn eine Verbindung an irgendeiner Stelle im Pfad über einen Proxy geleitet wird.  

Überprüfen Sie das gesamte Szenario sorgfältig, um zu entscheiden, ob Sie – ggf. bei angepassten Leerlauftimeouts – von TCP-Rücksetzungen profitieren und ob zusätzliche Schritte erforderlich sind, um das gewünschte Anwendungsverhalten sicherzustellen.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>Aktivieren der TCP-Rücksetzung bei Leerlauftimeout

Mithilfe der API-Version 2018-07-01 können Sie das Senden von bidirektionalen TCP-Rücksetzungen bei Leerlauftimeout für die einzelnen Regeln aktivieren:

```json
      "loadBalancingRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "inboundNatRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "outboundRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

## <a name="regions"></a> Regionale Verfügbarkeit

In allen Regionen verfügbar.

## <a name="limitations"></a>Einschränkungen

- Sie können TCP-Rücksetzungen im Portal weder konfigurieren noch ansehen.  Verwenden Sie stattdessen Vorlagen, die REST-API, die Azure CLI 2.0 oder PowerShell.
- TCP RST sendete nur während der TCP-Verbindung im Status ESTABLISHED.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Load Balancer Standard](load-balancer-standard-overview.md).
- Erfahren Sie mehr über [Ausgangsregeln](load-balancer-outbound-rules-overview.md).
