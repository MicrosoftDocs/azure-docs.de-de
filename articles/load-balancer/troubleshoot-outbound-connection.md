---
title: Problembehandlung bei ausgehenden Verbindungen in Azure Load Balancer
description: Hier finden Sie Lösungen für häufige Probleme mit der ausgehenden Konnektivität über Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 05/7/2020
ms.author: errobin
ms.openlocfilehash: 516576f4e005cc9fe2303945ecb1a13489908a5d
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696352"
---
# <a name="troubleshooting-outbound-connections-failures"></a><a name="obconnecttsg"></a> Problembehandlung für Fehler bei ausgehenden Verbindungen

Dieser Artikel bietet Lösungen für häufige Probleme, die bei ausgehenden Verbindungen von Azure Load Balancer auftreten können. Die meisten Probleme mit der ausgehenden Konnektivität, die bei Kunden auftreten, sind auf die SNAT-Portauslastung und Verbindungstimeouts zurückzuführen, die zu verworfenen Paketen führen. Dieser Artikel enthält Schritte zur Behebung dieser Probleme.

## <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a> Verwalten der SNAT-Portauslastung (PAT)
Wie unter [Eigenständiger virtueller Computer ohne öffentliche IP-Adresse](load-balancer-outbound-connections.md) und [Virtueller Computer mit Lastenausgleich ohne öffentliche IP-Adresse](load-balancer-outbound-connections.md) beschrieben wird, sind für [PAT](load-balancer-outbound-connections.md) verwendete [kurzlebige Ports](load-balancer-outbound-connections.md) eine begrenzte Ressource. Sie können Ihre Nutzung von kurzlebigen Ports überwachen und mit Ihrer aktuellen Zuordnung vergleichen, um das Risiko zu ermitteln oder die SNAT-Auslastung anhand [dieses Leitfadens](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) zu bestätigen.

Wenn Sie wissen, dass Sie viele ausgehende TCP- oder UDP-Verbindungen zu derselben IP-Zieladresse und demselben Port initiieren und Fehler bei ausgehenden Verbindungen feststellen oder vom Support darauf hingewiesen werden, dass Sie zu viele SNAT-Ports (vorab zugeordnete [kurzlebige Ports](load-balancer-outbound-connections.md#preallocatedports), die für [PAT](load-balancer-outbound-connections.md) verwendet werden) in Anspruch nehmen, stehen Ihnen mehrere Lösungsmöglichkeiten zur Verfügung. Überprüfen Sie diese Optionen, und entscheiden Sie, welche für Ihr Szenario verfügbar und am besten geeignet sind. Möglicherweise kann die ein oder andere die Verwaltung dieses Szenarios erleichtern.

Wenn Sie Probleme haben, das Verhalten der ausgehenden Verbindungen zu verstehen, können Sie die IP-Stapelstatistiken (netstat) verwenden. Es kann aber auch hilfreich sein, das Verbindungsverhalten mithilfe von Paketerfassungen zu beobachten. Sie können diese Paketerfassungen im Gastbetriebssystem Ihrer Instanz durchführen oder [Network Watcher für die Paketerfassung](../network-watcher/network-watcher-packet-capture-manage-portal.md) verwenden. 

## <a name="manually-allocate-snat-ports-to-maximize-snat-ports-per-vm"></a><a name ="manualsnat"></a>Manuelles Zuordnen von SNAT-Ports zum Maximieren der SNAT-Ports pro VM
Gemäß der Definition in [vorab zugeordneten Ports](load-balancer-outbound-connections.md#preallocatedports) ordnet der Lastenausgleich Ports basierend auf der Anzahl der VMs im Back-End automatisch zu. Dieser Prozess wird standardmäßig konservativ durchgeführt, um die Skalierbarkeit zu gewährleisten. Wenn Sie die maximale Anzahl der VMs kennen, die im Back-End enthalten sein werden, können Sie SNAT-Ports in den einzelnen Ausgangsregeln manuell zuordnen. Wenn Sie beispielsweise wissen, dass Sie maximal 10 VMs verwenden, können Sie, anstelle des Standards von 1.024 Ports, pro VM 6.400 SNAT-Ports zuordnen. 

## <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>Ändern der Anwendung für die Wiederverwendung von Verbindungen 
Sie können den Bedarf an kurzlebigen Ports, die für SNAT verwendet werden, durch Wiederverwenden von Verbindungen in der Anwendung reduzieren. Die Wiederverwendung von Verbindungen trifft insbesondere für Protokolle wie HTTP/1.1 zu, das standardmäßig Verbindungen wiederverwendet. Andere Protokolle, die HTTP als Transportprotokoll verwenden (z. B. REST) können davon wiederum profitieren. 

Die Wiederverwendung ist immer besser als einzelne, unteilbare TCP-Verbindungen für jede Anforderung. Die Wiederverwendung führt zu leistungsfähigeren, sehr effizienten TCP-Transaktionen.

## <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>Ändern der Anwendung für die Verwendung von Verbindungspooling
Sie können in Ihrer Anwendung ein Verbindungspoolingschema verwenden, bei dem Anforderungen intern auf einen festen Satz von Verbindungen verteilt werden (wobei die Verbindungen möglichst wiederverwendet werden). Bei diesem Schema wird die Anzahl der verwendeten kurzlebigen Ports eingeschränkt und eine besser vorhersagbare Umgebung erstellt. Dies kann auch den Durchsatz von Anforderungen erhöhen, da mehrere gleichzeitige Vorgänge zugelassen werden, wenn eine einzelne Verbindung durch die Antwort eines Vorgangs blockiert wird.  

Verbindungspooling ist möglicherweise innerhalb des Frameworks, das Sie zum Entwickeln Ihrer Anwendung oder der Konfigurationseinstellungen für Ihre Anwendung verwenden, bereits vorhanden. Sie können das Verbindungspooling mit der Wiederverwendung von Verbindungen kombinieren. Mehrere Anforderungen nutzen dann eine feste vorhersagbare Anzahl von Ports für die gleiche IP-Zieladresse und den gleichen Port. Bei den Anforderungen profitieren Sie außerdem von der effizienten Verwendung von TCP-Transaktionen, was zu niedrigeren Latenzzeiten und geringerer Ressourcenauslastung führt. Auch bei den UDP-Transaktionen können Sie profitieren, da Sie durch das Verwalten der Anzahl von UDP-Datenflüssen wiederum Zustände mit Überlastung vermeiden und die SNAT-Portauslastung verwalten können.

## <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>Ändern der Anwendung für die Verwendung weniger aggressiver Wiederholungslogik
Wenn die für [PAT](load-balancer-outbound-connections.md) verwendeten [vorab zugeordneten kurzlebigen Ports](load-balancer-outbound-connections.md#preallocatedports) erschöpft sind oder Anwendungsfehler auftreten, führen aggressive oder Brute-Force-Wiederholungsversuche ohne Verfalls- und Backofflogik zu Erschöpfung oder gar anhaltender Erschöpfung. Sie können den Bedarf an kurzlebigen Ports durch Verwendung einer weniger aggressiven Wiederholungslogik reduzieren. 

Kurzlebige Ports haben ein Leerlauftimeout von vier Minuten (nicht veränderbar). Wenn die Wiederholungen zu aggressiv sind, besteht keine Möglichkeit, dass sich die Überlastung von alleine erholt. Daher ist die Überlegung, wie und wie oft Ihre Anwendung Transaktionen wiederholen soll, ein wichtiger Aspekt des Entwurfs.

## <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Zuweisen einer öffentlichen IP-Adresse zu jedem virtuellen Computer
Durch das Zuweisen einer öffentlichen IP-Adresse ändert sich das Szenario zu einem Szenario mit einem [virtuellen Computer mit öffentlicher IP-Adresse](load-balancer-outbound-connections.md). Alle kurzlebigen Ports für die öffentliche IP-Adresse, die für jeden virtuellen Computer verwendet werden, sind für den virtuellen Computer verfügbar. (Das steht im Gegensatz zu Szenarien, bei denen kurzlebige Ports für eine öffentliche IP-Adresse von allen virtuellen Computern, die dem entsprechenden Back-End-Pool zugeordnet sind, gemeinsam genutzt werden.) Es müssen auch Nachteile berücksichtigt werden, wie beispielsweise zusätzliche Kosten für öffentliche IP-Adressen und mögliche Auswirkungen des Filterns einer großen Anzahl einzelner IP-Adressen.

>[!NOTE] 
>Diese Option ist für Webworkerrollen nicht verfügbar.

## <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Verwenden mehrerer Front-Ends
Bei Verwendung des öffentlichen Standard Load Balancers vergeben Sie [mehrere Front-End-IP-Adressen für ausgehende Verbindungen](load-balancer-outbound-connections.md) und [multiplizieren die Anzahl der verfügbaren SNAT-Ports](load-balancer-outbound-connections.md#preallocatedports).  Erstellen Sie eine Front-End-IP-Konfiguration, eine Regel und einen Back-End-Pool, um die Programmierung von SNAT auf die öffentliche IP-Adresse des Front-Ends auszulösen.  Die Regel muss nicht funktionieren, und ein Integritätstest muss nicht erfolgreich sein.  Wenn Sie auch für eingehende (und nicht nur für ausgehende) Datenflüsse mehrere Front-Ends verwenden, sollten Sie benutzerdefinierte Integritätstests verwenden, um die Zuverlässigkeit sicherzustellen.

>[!NOTE]
>In den meisten Fällen ist die Überlastung der SNAT-Ports ein Zeichen für ein schlechtes Design.  Stellen Sie sicher, dass die Gründe für die Portüberlastung kennen, bevor Sie weitere Front-Ends verwenden, um SNAT-Ports hinzuzufügen.  Möglicherweise verdecken Sie ein Problem, das später zu Fehlern führen kann.

## <a name="scale-out"></a><a name="scaleout"></a>Aufskalieren
[Vorab zugeordnete Ports](load-balancer-outbound-connections.md#preallocatedports) werden anhand der Größe des Back-End-Pools zugewiesen und in Ebenen gruppiert, um Unterbrechungen zu minimieren, wenn einige der Ports neu zugewiesen werden müssen, um die nächstgrößere Back-End-Pool-Größenebene abzudecken.  Sie können möglicherweise die SNAT-Portnutzung für ein bestimmtes Front-End erhöhen, indem Sie Ihren Back-End-Pool entsprechend der maximalen Größe einer bestimmten Ebene skalieren.  Beachten Sie, dass die standardmäßige Portzuordnung erforderlich ist, damit die Anwendung effizient aufskaliert werden kann, ohne eine SNAT-Auslastung zu riskieren.

Beispielsweise sind für zwei virtuelle Computer im Back-End-Pool 1.024 SNAT Ports pro IP-Konfiguration verfügbar, sodass insgesamt 2.048 SNAT-Ports für die Bereitstellung zulässig sind.  Soll die Bereitstellung auf 50 virtuelle Computer vergrößert werden, können, obwohl die Anzahl der vorab zugeordneten Ports pro virtuellem Computer konstant bleibt, insgesamt 51.200 (50 x 1024) SNAT-Ports durch die Bereitstellung genutzt werden.  Wenn Sie Ihre Bereitstellung aufskalieren möchten, überprüfen Sie die Anzahl von [vorab zugeordneten Ports](load-balancer-outbound-connections.md#preallocatedports) pro Ebene, um sicherzustellen, dass Sie die Aufskalierung entsprechend dem Maximum der jeweiligen Ebene festlegen.  Hätten Sie sich im vorherigen Beispiel zu einer Aufskalierung auf 51 statt 50 Instanzen entschieden, würden Sie zur nächsten Ebene gelangen und somit weniger SNAT-Ports pro VM sowie weniger SNAT-Ports insgesamt erhalten.

Wenn Sie auf die nächstgrößere Back-End-Poolgröße aufskalieren, erfolgt möglicherweise für einige Ihrer ausgehenden Verbindungen ein Timeout, wenn zugeordnete Ports neu zugeordnet werden müssen.  Wenn Sie nur einige Ihrer SNAT-Ports verwenden, hat das Hochskalieren über die nächstgrößere Back-End-Poolgröße hinaus keine Auswirkungen.  Die Hälfte der vorhandenen Ports wird bei jedem Wechsel zur nächsten Back-End-Poolebene neu zugeordnet.  Soll dies nicht stattfinden, müssen Sie Ihre Bereitstellung an die Ebenengröße anpassen.  Oder Sie stellen sicher, dass Ihre Anwendung geeignet erkennen und wiederholen kann.  TCP-Keepalives können bei der Erkennung unterstützen, wenn SNAT-Ports nicht mehr funktionieren, weil sie neu zugeordnet werden.

## <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>Verwenden von Keepalives zum Zurücksetzen des Leerlauftimeouts für ausgehende Verbindungen
Ausgehende Verbindungen haben einen 4-Minuten-Leerlauftimeout. Dieses Zeitlimit kann über [Ausgangsregeln](outbound-rules.md) angepasst werden. Sie können Keepalives auf der Transportschicht (z. B. TCP-Keepalives) oder der Anwendungsschicht verwenden, um einen im Leerlauf befindlichen Datenfluss zu aktualisieren und den Leerlauftimeout bei Bedarf zurückzusetzen.  

Wenn TCP-Keepalives verwendet werden, genügt es, sie auf einer Seite der Verbindung zu aktivieren. Beispielsweise genügt es, sie nur auf der Serverseite zu aktivieren, um den Leerlauftimer des Datenflusses zurückzusetzen, und es ist nicht erforderlich, TCP-Keepalives auf beiden Seiten auszulösen.  Ähnliche Konzepte gibt es für die Anwendungsschicht, einschließlich Client/Server-Konfigurationen für Datenbanken.  Überprüfen Sie auf der Serverseite, welche Optionen es für anwendungsspezifische Keepalives gibt.

## <a name="next-steps"></a>Nächste Schritte
Wir möchten die Benutzerfreundlichkeit für unsere Kunden immer weiter verbessern. Wenn Probleme mit der ausgehenden Konnektivität auftreten, die in diesem Artikel nicht aufgeführt oder gelöst werden, senden Sie uns Ihr Feedback über GitHub unten auf dieser Seite. Wir werden so schnell wie möglich auf Ihr Feedback reagieren.