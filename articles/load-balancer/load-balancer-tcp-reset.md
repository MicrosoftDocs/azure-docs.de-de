---
title: TCP-Zurücksetzung und Leerlauftimeout in Azure für Load Balancer
titleSuffix: Azure Load Balancer
description: In diesem Artikel erfahren Sie über Load Balancer mit bidirektionalen TCP-RST-Paketen bei Leerlauftimeout.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/07/2020
ms.author: allensu
ms.openlocfilehash: 0d02b46345af13770f77a7dac452127a665e01fd
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696743"
---
# <a name="load-balancer-tcp-reset-and-idle-timeout"></a>TCP-Zurücksetzung und Leerlauftimeout für Load Balancer

Sie können [Load Balancer Standard ](./load-balancer-overview.md) verwenden, um ein besser vorhersagbares Anwendungsverhalten für Ihre Szenarien zu erzielen, indem Sie für eine bestimmte Regel die TCP-Rücksetzung bei Leerlauf aktivieren. Das Standardverhalten von Load Balancer besteht darin, Flows ohne Rückmeldung zu verwerfen, wenn das Leerlauftimeout eines Flows erreicht ist.  Das Aktivieren dieser Funktion bewirkt, dass Load Balancer bidirektionale TCP-Rücksetzungen (TCP-RST-Paket) bei einem Leerlauftimeout sendet.  Dadurch werden Ihre Anwendungsendpunkte darüber informiert, dass bei der Verbindung ein Timeout aufgetreten ist und sie nicht mehr verwendet werden kann.  Die Endpunkte können dann bei Bedarf sofort eine neue Verbindung herstellen.

![Load Balancer mit TCP-Rücksetzung](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
## <a name="tcp-reset"></a>TCP-Zurücksetzung

Sie ändern dieses Standardverhalten und ermöglichen das Senden von TCP-Rücksetzungen bei Leerlauftimeout in NAT-Eingangsregeln, Lastenausgleichsregeln und [Ausgangsregeln](./load-balancer-outbound-connections.md#outboundrules).  Wenn dies durch eine Regel aktiviert ist, sendet Load Balancer zum Zeitpunkt des Leerlauftimeouts für alle passenden Flows bidirektionale TCP-Rücksetzungen (TCP-RST-Pakete) sowohl an den Client- als auch an den Serverendpunkt.

Endpunkte, die TCP-RST-Pakete empfangen, schließen den entsprechenden Socket sofort. Damit werden Endpunkte sofort darüber benachrichtigt, dass die Verbindung getrennt wurde und jegliche weitere Kommunikation über die gleiche TCP-Verbindung zu einem Fehler führt.  Anwendungen können Verbindungen bereinigen, wenn der Socket geschlossen wird, und Verbindungen bei Bedarf erneut herstellen, ohne auf das Timeout der TCP-Verbindung zu warten.

In vielen Szenarien müssen damit weniger TCP-Keepalives (bzw. Keepalives auf der Anwendungsschicht) gesendet werden, um das Leerlauftimeout eines Flows zu aktualisieren. 

Wenn die Leerlaufzeiträume die in der Konfiguration erlaubten Werte überschreiten oder Ihre Anwendung mit aktivierten TCP-Rücksetzungen ein unerwünschtes Verhalten zeigt, müssen Sie diese TCP-Keepalives (bzw. Keepalives auf der Anwendungsschicht) möglicherweise weiterhin verwenden, um die Verfügbarkeit der TCP-Verbindungen zu überwachen.  Keepalives (insbesondere Keepalives auf der Anwendungsschicht) sind zudem weiterhin nützlich, wenn eine Verbindung an irgendeiner Stelle im Pfad über einen Proxy geleitet wird.  

Überprüfen Sie das gesamte Szenario sorgfältig, um zu entscheiden, ob Sie – ggf. bei angepassten Leerlauftimeouts – von TCP-Rücksetzungen profitieren und ob zusätzliche Schritte erforderlich sind, um das gewünschte Anwendungsverhalten sicherzustellen.

## <a name="configurable-tcp-idle-timeout"></a>Konfigurierbares TCP-Leerlauftimeout

Azure Load Balancer weist den folgenden Leerlauftimeoutbereich auf:
-  4 Minuten bis 100 Minuten für Ausgangsregeln
-  4 Minuten bis 30 Minuten für Load Balancer-Regeln und NAT-Regeln für eingehenden Datenverkehr

Standardmäßig ist der Wert auf vier Minuten festgelegt. Wenn die Dauer einer Inaktivitätsperiode den Timeoutwert überschreitet, gibt es keine Garantie dafür, dass die TCP- oder HTTP-Sitzung zwischen dem Client und Ihrem Clouddienst aufrechterhalten wird.

Sobald die Verbindung geschlossen wird, wird in der Clientanwendung möglicherweise die folgende Fehlermeldung angezeigt: „Die zugrunde liegende Verbindung wurde geschlossen: Eine Verbindung, deren Aufrechterhaltung erwartet wurde, wurde vom Server geschlossen.“

Eine gängige Methode zur Aufrechterhaltung von Verbindungen ist TCP-Keep-Alive. Dadurch bleibt die Verbindung länger aktiv. Weitere Informationen finden Sie in [diesen .NET-Beispielen](/dotnet/api/system.net.servicepoint.settcpkeepalive). Bei aktivierter Keep-Alive-Funktion werden während inaktiver Phasen Pakete über die Verbindung gesendet. Keep-Alive-Pakete sorgen dafür, dass der Wert für das Leerlauftimeout nicht erreicht und die Verbindung über einen langen Zeitraum aufrechterhalten wird.

Die Einstellung funktioniert nur für eingehende Verbindungen. Um den Verlust der Verbindung zu vermeiden, konfigurieren Sie ein TCP-Keep-Alive-Intervall, das kürzer ist als das Leerlauftimeout, oder erhöhen Sie den Leerlauftimeout-Wert. Für diese Szenarien wurde Unterstützung für konfigurierbare Leerlauftimeouts hinzugefügt.

TCP-Keep-Alive eignet sich für Szenarien, in denen die Akkulaufzeit keine Einschränkung darstellt. Bei mobilen Anwendungen ist die Verwendung dieser Funktion hingegen nicht empfehlenswert. TCP-Keep-Alive kann bei mobilen Anwendungen zu einer schnelleren Entladung des Geräteakkus führen.


## <a name="limitations"></a>Einschränkungen

- TCP-Zurücksetzung wird nur während der TCP-Verbindung im Status ESTABLISHED gesendet.
- TCP-Zurücksetzung wird nicht für interne Load Balancer mit konfigurierten HA-Ports gesendet.
- Der TCP-Leerlauftimeout wirkt sich nicht auf die Lastenausgleichsregeln des UDP-Protokolls aus.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Load Balancer Standard](./load-balancer-overview.md).
- Erfahren Sie mehr über [Ausgangsregeln](./load-balancer-outbound-connections.md#outboundrules).
- [Konfigurieren von TCP RST bei Leerlauftimeout](load-balancer-tcp-idle-timeout.md)