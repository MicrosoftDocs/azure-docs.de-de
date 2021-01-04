---
title: Beheben von Problemen bei der Ressourcenintegrität sowie bei Problemen mit der Front-End- und Back-End-Verfügbarkeit bei Azure Load Balancer
description: Verwenden Sie die verfügbaren Metriken für die Diagnose Ihrer beeinträchtigten oder nicht verfügbaren Azure Load Balancer Standard-Instanzen.
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2020
ms.author: errobin
ms.openlocfilehash: dcfce06bb158888b56483a73ededd354c229a99b
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696318"
---
# <a name="troubleshoot-resource-health-frontend-and-backend-availability-issues"></a>Behandeln von Problemen im Zusammenhang mit der Ressourcenintegrität sowie mit der Front-End- und Back-End-Verfügbarkeit 

In diesem Artikel werden die Schritte zum Untersuchen von Problemen beschrieben, die sich auf die Verfügbarkeit der Front-End-IP-Adresse und Back-End-Ressourcen Ihrer Load Balancer-Instanz auswirken. 

## <a name="about-the-metrics-well-use"></a>Informationen zu den verwendeten Metriken
Für die Schritte dieses Artikels werden die beiden Metriken *Datenpfadverfügbarkeit* und *Integritätsteststatus* verwendet. Um die richtigen Rückschlüsse zu ziehen, sollten Sie mit diesen beiden Metriken vertraut sein. 

## <a name="data-path-availability"></a>Datenpfadverfügbarkeit
Die Metrik „Datenpfadverfügbarkeit“ wird von einem TCP-Ping alle 25 Sekunden an allen Front-End-Ports generiert, für die Lastenausgleichregeln und NAT-Regeln für eingehenden Datenverkehr konfiguriert sind. Dieser TCP-Ping wird dann an alle fehlerfreien (als verfügbar getesteten) Back-End-Instanzen weitergeleitet. Wenn der Dienst eine Antwort auf den Ping empfängt, wird der Vorgang als erfolgreich eingestuft, und für die Summe der Metrik erfolgt eine einmalige Iteration. Bei einem Fehler bleibt die Metrik unverändert. Die Anzahl dieser Metrik ist 1/100 der Gesamtzahl an TCP-Pings pro Testzeitraum. Folglich soll der Durchschnitt betrachtet werden. Dabei wird der Durchschnitt der Summe/Anzahl für den Zeitraum angezeigt. Bei Betrachtung der für den Durchschnitt aggregierten Metrik „Datenpfadverfügbarkeit“ erhalten wir also eine prozentuale Erfolgsrate für TCP-Pings für Ihre/n Front-End-IP-Adresse/Port für jede Ihrer Lastenausgleichsregeln und NAT-Regeln für eingehenden Datenverkehr.

## <a name="health-probe-status"></a>Integritätsteststatus
Die Metrik „Integritätsteststatus“ wird von einem Ping des Protokolls generiert, das im Integritätstest definiert ist. Dieser Ping wird an jede Instanz im Back-End-Pool sowie an dem im Integritätstest definierten Port gesendet. Bei HTTP- und HTTPS-Tests ist für ein erfolgreiches Ping eine HTTP 200 OK-Antwort erforderlich, bei TCP-Tests wird jede Antwort als erfolgreicher Vorgang eingestuft. Anhand der aufeinanderfolgenden erfolgreichen Vorgänge oder Fehler der einzelnen Tests wird bestimmt, ob eine Back-End-Instanz fehlerfrei ist und Datenverkehr für die Lastenausgleichsregeln empfängt, denen der Back-End-Pool zugewiesen ist. Vergleichbar mit der Datenpfadverfügbarkeit wird die Durchschnittsaggregation verwendet, die Auskunft über den prozentualen Anteil an erfolgreichen Pings während des Testintervalls gibt. Dieser Wert des Integritätsteststatus zeigt die Back-End-Integrität isoliert von Ihrem Lastenausgleichsmodul an, indem Ihre Back-End-Instanzen getestet werden, ohne Datenverkehr durch das Front-End zu senden.

>[!IMPORTANT]
>Das Zeitfenster für die Ermittlung des Integritätsteststatus beträgt eine Minute. Dadurch kann es bei einem ansonsten konstanten Wert zu geringfügigen Schwankungen kommen. Wenn z. B. zwei Back-End-Instanzen vorhanden sind (eine als verfügbar und eine als nicht verfügbar getestete), erfasst der Integritätstestdienst möglicherweise 7 Proben für den fehlerfreie Instanz und 6 für die fehlerhafte Instanz. Dadurch wird ein zuvor konstanter Wert von 50 für ein einminütiges Intervall als 46,15 angezeigt. 

## <a name="diagnose-degraded-and-unavailable-load-balancers"></a>Diagnose bei beeinträchtigten und nicht verfügbaren Lastenausgleichsmodulen
Wie im [Artikel zur Ressourcenintegrität](load-balancer-standard-diagnostics.md#resource-health-status) beschrieben, weist ein beeinträchtigtes Lastenausgleichsmodul eine Datenpfadverfügbarkeit von 25 % bis 90 % auf. Ein nicht verfügbares Lastenausgleichsmodul weist eine Datenpfadverfügbarkeit von weniger als 25 % innerhalb eines zweiminütigen Zeitraums auf. Anhand dieser Schritte können auch Fehler untersucht werden, die in Ihren konfigurierten Warnungen zum Integritätsteststatus oder zur Datenpfadverfügbarkeit angezeigt werden. Wir betrachten ein Beispiel, in dem beim Überprüfen der Ressourcenintegrität ermittelt wurde, dass das Lastenausgleichsmodul nicht verfügbar ist und eine Datenpfadverfügbarkeit von 0 % aufweist – der Dienst ist also nicht verfügbar.

Zunächst rufen wir die Detailansicht der Metriken auf dem Blatt mit den Erkenntnissen für unser Lastenausgleichsmodul auf. Diese Ansicht kann über das Ressourcenblatt des Lastenausgleichsmoduls oder über den Link in der Nachricht zur Ressourcenintegrität geöffnet werden.  Anschließend navigieren wir zur Registerkarte mit der Front-End- und Back-End-Verfügbarkeit. Dort überprüfen wir ein dreißigminütiges Zeitfenster innerhalb des Zeitraums, in dem der beeinträchtigte oder nicht verfügbare Zustand eingetreten ist. Wenn die Datenpfadverfügbarkeit bei 0 % lag, wissen wir, dass aufgrund eines Problems Datenverkehr für alle unsere Lastenausgleichsregeln und NAT-Regeln für eingehenden Datenverkehr verhindert wird. Außerdem lässt sich die Dauer dieser Beeinträchtigung erkennen. 

Als Nächstes müssen wir anhand der Integritätsteststatus-Metrik ermitteln, ob der Datenpfad nicht verfügbar ist, weil keine fehlerfreien Back-End-Instanzen für den Datenverkehr verfügbar sind. Wenn mindestens eine fehlerfreie Back-End-Instanz für alle unsere Lastenausgleichsregeln und Regeln für eingehenden Datenverkehr vorhanden ist, wissen wir, dass die Nichtverfügbarkeit der Datenpfade nicht auf unsere Konfiguration zurückzuführen ist. Dieses Szenario deutet auf ein Problem mit der Azure-Plattform hin. Diese Art von Problem tritt selten auf, und unser Team wird in einer automatisierten Warnung umgehend informiert, um sämtliche Plattformprobleme schnellstmöglich zu behandeln.

## <a name="diagnose-health-probe-failures"></a>Diagnose bei Integritätstestfehlern
Angenommen, wir überprüfen den Integritätsteststatus und finden heraus, dass alle Instanzen als fehlerhaft angezeigt werden. Da kein Datenverkehr übermittelt werden kann, ist dieser Status also der Grund dafür, dass der Datenpfad nicht verfügbar ist. In diesem Fall sollten Sie mithilfe der folgenden Checkliste gängige Konfigurationsfehler ausschließen:
* Überprüfen Sie die CPU-Nutzung für Ihre Ressourcen. Anhand dieser Metrik können Sie herausfinden, ob Ihre Instanzen möglicherweise doch fehlerfrei sind
  * Überprüfen Sie diesen Wert. 
* Überprüfen Sie bei Verwendung eines HTTP- oder HTTPS-Tests, ob die Anwendung fehlerfrei ausgeführt wird und reagiert
  * Greifen Sie dazu über die private IP-Adresse oder die öffentliche IP-Adresse auf Instanzebene, die Ihrer Back-End-Instanz zugeordnet ist, direkt auf die Anwendungen zu
* Überprüfen Sie die Netzwerksicherheitsgruppen, die auf Ihre Back-End-Ressourcen angewendet werden. Stellen Sie sicher, dass keine Regeln mit höherer Priorität als AllowAzureLoadBalancerInBound konfiguriert sind, die den Integritätstest blockieren
  * Sie können dies auf dem Blatt „Netzwerk“ Ihrer Back-End-VMs oder VM-Skalierungsgruppen überprüfen
  * Wenn dieses NSG-Problem vorliegt, verschieben Sie die vorhandene Zulassungsregel, oder erstellen Sie eine neue Regel mit hoher Priorität, um AzureLoadBalancer-Datenverkehr zuzulassen
* Überprüfen Sie Ihr Betriebssystem. Stellen Sie sicher, dass Ihre VMs am Testport lauschen, und überprüfen Sie die zugehörigen Betriebssystem-Firewallregeln, um sicherzustellen, dass der Testdatenverkehr von der IP-Adresse 168.63.129.16 nicht blockiert wird
  * Zum Überprüfen der Lauschports können Sie an einer Windows-Eingabeaufforderung netstat -a oder bei einem Linux-Terminal netstat -l ausführen
* Platzieren Sie keine Firewall-NVA-VM im Back-End-Pool des Lastenausgleichsmoduls. Verwenden Sie [benutzerdefinierte Routen](../virtual-network/virtual-networks-udr-overview.md#user-defined), um Datenverkehr über die Firewall an Back-End-Instanzen zu leiten
* Stellen Sie sicher, dass das richtige Protokoll verwendet wird. Bei Verwendung von HTTP zum Testen eines Ports, der für Datenverkehr einer Nicht-HTTP-Anwendung konfiguriert ist, tritt bei dem Test ein Fehler auf

Wenn Sie diese Checkliste durchgearbeitet haben und weiterhin Integritätstestfehler auftreten, kann ein seltenes Plattformproblem vorliegen, das den Testdienst für Ihre Instanzen beeinträchtigt. In diesem Fall wird eine automatisierte Warnung an unser Team gesendet, um jegliche Plattformprobleme schnellstmöglich zu behandeln.

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zum Azure Load Balancer-Integritätstest](load-balancer-custom-probe-overview.md)
* [Weitere Informationen zu Azure Load Balancer-Metriken](load-balancer-standard-diagnostics.md)