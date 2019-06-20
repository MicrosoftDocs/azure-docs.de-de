---
title: Einführung in den Clusterressourcen-Manager von Service Fabric | Microsoft Docs
description: Eine Einführung in den Clusterressourcen-Manager von Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: cfab735b-923d-4246-a2a8-220d4f4e0c64
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: e9b1cc8b66be36a0a77118f4de672c9411433ba5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60743655"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Einführung in den Clusterressourcen-Manager von Service Fabric
Zur Verwaltung von IT-Systemen oder Onlinediensten wurden diesen bisher üblicherweise spezielle physische oder virtuelle Computer zugewiesen. Dienste wurden als Ebenen entworfen. Es gab eine Webebene und eine Daten- oder Speicherebene. Clientanwendungen verfügten über eine Messaging-Ebene, auf der Anforderungen eingingen und ausgingen, und einen speziellen Satz von Computern für die Zwischenspeicherung. Für jede Ebene oder jede Art von Workload gab es bestimmte fest zugeordnete Computer oder Computergruppen, so z.B. für die Datenbank und die Webserver. Wenn eine bestimmte Art von Workload eine Überlastung der Computer verursachte, auf denen sie ausgeführt wurde, wurden dieser Ebene weitere Computer mit derselben Konfiguration hinzugefügt. Jedoch konnten nicht alle Workloads so einfach skaliert werden – insbesondere für die Datenebene mussten Sie in der Regel Computer durch größere Computer ersetzen. Ganz einfach. Wenn ein Computer ausfiel, wurde dieser Teil der gesamten Anwendung mit niedriger Kapazität ausgeführt, bis der Computer wiederhergestellt werden konnte. Noch immer recht einfach (wenn auch nicht unbedingt spaßig).

Inzwischen hat sich jedoch die Dienst- und Softwarearchitektur geändert. Anwendungen verfügen häufiger über ein Design mit horizontaler Skalierung. Es ist inzwischen üblich, Anwendungen mit Containern oder Microservices (oder beiden) zu erstellen. Sie verfügen jetzt eventuell immer noch nur über einige Computer, jedoch führen diese nicht nur eine einzelne Instanz einer Workload aus. Möglicherweise führen sie sogar mehrere unterschiedliche Workloads gleichzeitig aus. Sie verwenden jetzt Dutzende verschiedener Arten von Diensten (von denen keiner die vollständigen Ressourcen eines Computers beansprucht) und möglicherweise Hunderte verschiedener Instanzen dieser Dienste. Für Hochverfügbarkeit weist jede benannte Instanz eine oder mehrere Instanzen oder Replikate auf. Abhängig von der Größe dieser Workloads und ihrer Auslastung verwenden Sie möglicherweise Hunderte oder Tausende von Computern. 

Dann ist die Verwaltung Ihrer Umgebung nicht mehr so einfach wie das Verwalten einiger Computer, die einzelnen Typen von Workloads fest zugeordnet sind. Ihre Server sind virtuell und haben keine Namen mehr (denn Ihr Modell hat sich tatsächlich von einem [Haustierbestand in eine Rinderherde](https://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) geändert). Die Konfiguration dreht sich weniger um die Computer und mehr um die Dienste selbst. Hardware, die einer einzelnen Instanz einer Workload fest zugeordnet ist, gehört überwiegend der Vergangenheit an. Dienste sind selbst zu kleinen verteilten Systemen geworden, die mehrere kleinere Standardhardwarekomponenten überspannen.

Da es sich bei Ihrer App nicht mehr um eine Reihe auf mehrere Ebenen verteilter Monolithen handelt, müssen Sie eine weitaus größere Anzahl von Kombinationen berücksichtigen. Wer entscheidet, welche Arten und wie viele Workloads auf welcher Hardware ausgeführt werden können? Welche Workloads können problemlos auf der gleichen Hardware ausgeführt werden, bei welchen treten Konflikte auf? Wie wissen Sie, was auf einem Computer ausgeführt wurde, der gerade ausgefallen ist? Wer ist dafür verantwortlich, dass die Workload wieder den Betrieb aufnimmt? Warten Sie, bis der (virtuelle?) Computer wieder aktiv wird, oder führen Ihre Workloads automatisch ein Failover auf andere Computer durch und werden weiter ausgeführt? Ist Benutzereingriff erforderlich? Was ist mit Upgrades in dieser Umgebung?

Als Entwickler und Operatoren, die sich mit dieser Umgebung beschäftigen, werden wir bei der Verwaltung dieser Komplexität Hilfe wünschen. Eine Einstellungswelle und der Versuch, die Komplexität mit Personal in den Griff zu bekommen, ist wahrscheinlich nicht die richtige Antwort. Wie gehen wir also vor?

## <a name="introducing-orchestrators"></a>Einführung in Orchestratoren
„Orchestrator“ ist der allgemeine Begriff für eine Softwarekomponente, mit deren Hilfe Administratoren diese Arten von Umgebungen verwalten können. Orchestratoren sind die Komponenten, die Anforderungen wie „Ich möchte 5 Kopien dieses Diensts in meiner Umgebung ausführen“ annehmen. Sie versuchen, die Umgebung unter allen Umständen in den gewünschten Zustand zu versetzen.

Orchestratoren (nicht Personen) kommen zum Einsatz, wenn ein Computer ausfällt oder eine Workload aus einem unerwarteten Grund beendet wird. Die meisten Orchestratoren beschäftigen sich nicht nur mit Fehlern. Weitere Funktionen umfassen das Verwalten neuer Bereitstellungen, das Durchführen von Upgrades sowie den Umgang mit Ressourcenverbrauch und Governance. Alle Orchestratoren sind im Grunde darauf ausgelegt, einen gewünschten Konfigurationszustand in der Umgebung zu halten. Sie möchten einem Orchestrator angeben können, was Sie wünschen, und ihm die Hauptarbeit überlassen. Aurora auf Basis von Mesos, Docker Datacenter/Docker Swarm, Kubernetes und Service Fabric sind alle Beispiele für Orchestratoren. Diese Orchestrators werden aktiv weiterentwickelt, um die Anforderungen realer Workloads in Produktionsumgebungen zu erfüllen. 

## <a name="orchestration-as-a-service"></a>Orchestrierung als Dienst
Der Clusterressourcen-Manager ist die Systemkomponente, durch die Orchestrierung in Service Fabric erfolgt. Der Clusterressourcen-Manager kümmert sich um drei Aufgaben:

1. Erzwingen von Regeln
2. Optimieren der Umgebung
3. Helfen bei anderen Prozessen

### <a name="what-it-isnt"></a>Was der Clusterressourcen-Manager nicht ist
In herkömmlichen N-Schichten-Anwendungen gibt es immer einen [Load Balancer](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Hierbei handelte es sich normalerweise um einen Netzwerklastenausgleich (Network Load Balancer, NLB) oder einen Anwendungslastenausgleich (ALB), je nachdem, an welcher Stelle im Netzwerkstapel er stattfand. Einige Load Balancer sind hardwarebasiert, wie z.B. das BigIP-Angebot von F5, andere sind softwarebasiert, wie z.B. der Netzwerklastenausgleich von Microsoft. In anderen Umgebungen wird der Lastenausgleich möglicherweise von HAProxy, nginx, Istio oder Envoy ausgeführt. In diesen Architekturen besteht die Aufgabe des Lastenausgleichs darin sicherzustellen, dass zustandslose Workloads (ungefähr) die gleiche Menge an Arbeit erhalten. Für den Lastenausgleich gab es verschiedene Strategien. Einige sendeten jeden Aufruf an einen anderen Server. Andere stellten feste/unveränderliche Sitzungen bereit. Fortgeschrittenere Load Balancer verwenden Schätzungen der tatsächlichen Auslastung oder Berichte, um einen Aufruf basierend auf den erwarteten Kosten und der aktuellen Computerauslastung weiterzuleiten.

Netzwerklastenausgleichsmodule oder Nachrichtenrouter versuchten sicherzustellen, dass die Web-/Workerschicht einigermaßen ausgeglichen blieb. Strategien für den Lastenausgleich auf Datenebene waren unterschiedlich und hingen vom Datenspeichermechanismus ab. Für den Lastenausgleich auf Datenebene wurden horizontales Skalieren der Daten, Zwischenspeichern, verwaltete Sichten, gespeicherte Prozeduren und weitere speicherspezifische Mechanismen verwendet.

Während einige dieser Strategien interessant sind, ist der Clusterressourcen-Manager von Service Fabric mit einem NLB oder Cache nicht vergleichbar. Ein Netzwerk-Load Balancer führt den Lastenausgleich für Front-Ends durch Verteilen des Datenverkehrs auf die Front-Ends aus. Der Clusterressourcen-Manager von Service Fabric verfolgt eine andere Strategie. Im Grunde verschiebt Service Fabric *Dienste* dorthin, wo sie am sinnvollsten erscheinen, und erwartet, dass der Datenverkehr oder die Auslastung ihnen folgt. Beispielsweise werden Dienste auf Knoten verschoben, die zurzeit unausgelastet sind, weil die dort befindlichen Dienste nicht sehr beansprucht werden. Die Knoten werden möglicherweise deshalb nicht beansprucht, weil die dort befindlichen Dienste gelöscht oder an eine andere Stelle verschoben wurden. Als weiteres Beispiel kann der Clusterressourcen-Manager auch einen Dienst von einem Computer an eine andere Stelle verschieben. Möglicherweise soll auf dem Computer ein Upgrade stattfinden, oder der Computer ist aufgrund einer intensiven Nutzung durch die darauf ausgeführten Dienste überlastet. Oder die Ressourcenanforderungen des Dienstes haben zugenommen. Daher sind auf dem Computer nicht genügend Ressourcen vorhanden sind, um den Betrieb fortzusetzen. 

Da der Clusterressourcen-Manager für das Verschieben von Diensten zuständig ist, umfasst er einen anderen Featuresatz als ein Netzwerk-Load Balancer. Der Grund dafür ist, dass Netzwerk-Load Balancer Netzwerkdatenverkehr dorthin lenken, wo sich bereits Dienste befinden, auch wenn dieser Ort nicht perfekt zum Ausführen des Diensts geeignet ist. Der Clusterressourcen-Manager von Service Fabric verfolgt grundlegend andere Strategien, um sicherzustellen, dass die Ressourcen im Cluster effizient genutzt werden.

## <a name="next-steps"></a>Nächste Schritte
- Informationen zur Architektur und zum Informationsfluss innerhalb des Cluster Resource Managers finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-architecture.md).
- Der Clusterressourcen-Manager bietet zahlreiche Optionen zum Beschreiben des Clusters. Weitere Informationen zu Metriken finden Sie in diesem Artikel zum [Beschreiben eines Service Fabric-Clusters](service-fabric-cluster-resource-manager-cluster-description.md).
- Weitere Informationen zum Konfigurieren von Diensten finden Sie unter [Konfigurieren von Einstellungen des Clusterressourcen-Managers für Service Fabric-Dienste](service-fabric-cluster-resource-manager-configure-services.md).
- Metriken bestimmen, wie der Clusterressourcen-Manager von Service Fabric den Ressourcenverbrauch und die Kapazität im Cluster verwaltet. Weitere Informationen zu Metriken und deren Konfiguration finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-metrics.md).
- Der Clusterressourcen-Manager arbeitet mit den Verwaltungsfunktionen von Service Fabric. Weitere Informationen hierzu finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-management-integration.md)
- Informationen darüber, wie der Clusterressourcen-Manager die Auslastung im Cluster verwaltet und verteilt, finden Sie im Artikel zum [Lastenausgleich](service-fabric-cluster-resource-manager-balancing.md)
