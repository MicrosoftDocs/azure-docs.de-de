---
title: SNAT für ausgehende Verbindungen
description: Beschreibt, wie Azure Load Balancer zum Ausführen von SNAT für ausgehende Internetverbindungen verwendet wird.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: d30998e0d99ba7b1eeb55bdc47cd5cc54690156f
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032914"
---
# <a name="using-snat-for-outbound-connections"></a>Verwenden von SNAT für ausgehende Verbindungen

Mithilfe der Front-End-IP-Adressen einer öffentlichen Azure Load Balancer-Instanz können ausgehende Verbindungen mit dem Internet für Back-End-Instanzen bereitgestellt werden. Diese Konfiguration verwendet **SNAT (Source Network Address Translation, Quell-Netzwerkadressenübersetzung)** . Die IP-Adresse des Back-Ends wird von SNAT in die öffentliche IP-Adresse Ihrer Load Balancer-Instanz umgeschrieben. 

SNAT ermöglicht **IP-Maskierung** der Back-End-Instanz. Diese Maskierung verhindert, dass externe Quellen eine direkte Adresse für die Back-End-Instanzen abrufen können. Wenn Sie eine IP-Adresse zwischen Back-End-Instanzen freigeben, werden die Kosten für statische öffentliche IPs reduziert, und es werden Szenarien wie das Vereinfachen von IP-Zulassungslisten mit Datenverkehr von bekannten öffentlichen IP-Adressen unterstützt. 

>[!Note]
> Für Anwendungen, bei denen eine große Anzahl ausgehender Verbindungen erforderlich ist, oder Unternehmenskunden, die einen einzelnen Satz von IP-Adressen in einem bestimmten virtuellen Netzwerk benötigen, wird [Virtual Network NAT](../virtual-network/nat-overview.md) empfohlen. Die dynamische Zuordnung ermöglicht eine einfache Konfiguration und die effizienteste Verwendung der SNAT-Ports von jeder IP-Adresse. Außerdem können alle Ressourcen im virtuellen Netzwerk eine Gruppe von IP-Adressen gemeinsam nutzen, ohne dass ein Load Balancer freigegeben werden muss.

>[!Important]
> Auch ohne Konfiguration einer ausgehenden SNAT sind Azure Storage-Konten innerhalb derselben Region weiterhin zugänglich, und Back-End-Ressourcen erhalten weiterhin Zugriff auf Microsoft-Dienste wie Windows Update.

>[!NOTE] 
>Dieser Artikel gilt nur für Azure Resource Manager-Bereitstellungen. Lesen Sie für alle klassischen Bereitstellungsszenarien in Azure den Artikel [Ausgehende Verbindungen (klassisch)](/previous-versions/azure/load-balancer/load-balancer-outbound-connections-classic).

## <a name="sharing-frontend-ip-address-across-backend-resources"></a><a name ="snat"></a> Gemeinsame Nutzung der Front-End-IP-Adresse für Back-End-Ressourcen

Wenn die Back-End-Ressourcen eines Load Balancers keine öffentlichen IP-Adressen auf Instanzebene (ILPIP) aufweisen, richten sie die ausgehende Konnektivität über die Front-End-IP-Adresse des öffentlichen Load Balancers ein. Ports werden verwendet, um eindeutige Bezeichner zu generieren, mit denen unterschiedliche Datenflüsse verwaltet werden. Das Internet verwendet ein 5-Tupel, um diesen Unterschied zu gewährleisten.

Das 5-Tupel besteht aus den folgenden Elementen:

* Ziel-IP
* Zielport
* Quell-IP
* Quellport und Protokoll, um diesen Unterschied zu gewährleisten.

Wenn ein Port für eingehende Verbindungen verwendet wird, verfügt er über einen **Listener** für eingehende Verbindungsanforderungen an diesem Port und kann nicht für ausgehende Verbindungen verwendet werden. Um eine ausgehende Verbindung herzustellen, muss ein **kurzlebiger Port** verwendet werden, um dem Ziel einen Port bereitzustellen, über den ein eindeutiger Datenverkehrsfluss kommuniziert und verwaltet werden kann. Wenn diese kurzlebigen Ports zum Ausführen von SNAT verwendet werden, bezeichnet man sie als **SNAT-Ports**. 

Per Definition umfasst jede IP-Adresse 65.535 Ports. Jeder Port kann entweder für eingehende oder ausgehende Verbindungen für TCP (Transmission Control Protocol) und UDP (User Datagram Protocol) verwendet werden. Wenn einem Load Balancer eine öffentliche IP-Adresse als Front-End-IP-Adresse hinzugefügt wird, werden in Azure 64.000 Ports zur Verwendung als SNAT-Ports freigegeben. 

>[!NOTE]
> Jeder Port, der für eine Lastenausgleichsregel oder NAT-Regel für eingehenden Datenverkehr verwendet wird, nutzt von diesen 64.000 Ports einen Bereich von acht Ports, wodurch die Anzahl der für SNAT verfügbaren Ports verringert wird. Wenn eine Lastenausgleichs- oder NAT-Regel denselben Bereich von acht Ports einschließt, werden keine zusätzlichen Ports genutzt. 

Über [Ausgangsregeln](./outbound-rules.md) und Lastenausgleichsregeln können diese SNAT-Ports auf Back-End-Instanzen verteilt werden, damit sie die öffentlichen IP-Adressen des Load Balancers für ausgehende Verbindungen freigeben können.

Wenn das weiter unten beschriebene [Szenario 2](#scenario2) konfiguriert ist, führt der Host für jede Back-End-Instanz SNAT für Pakete aus, die Teil einer ausgehenden Verbindung sind. Beim Ausführen von SNAT für eine ausgehende Verbindung über eine Back-End-Instanz schreibt der Host die Quell-IP-Adresse in eine der Front-End-IP-Adressen um. Um eindeutige Datenflüsse aufrechtzuerhalten, schreibt der Host den Quellport jedes ausgehenden Pakets in einen der SNAT-Ports um, die für die Back-End-Instanz zugeordnet sind.

## <a name="outbound-connection-behavior-for-different-scenarios"></a>Verhalten der ausgehenden Verbindungen bei verschiedenen Szenarien
  * VM mit öffentlicher IP-Adresse
  * VM ohne öffentliche IP-Adresse
  * VM ohne öffentliche IP-Adresse und ohne Load Balancer Standard
        

 ### <a name="scenario-1-virtual-machine-with-public-ip"></a><a name="scenario1"></a> Szenario 1: VM mit öffentlicher IP-Adresse


 | Associations | Methode | IP-Protokolle |
 | ---------- | ------ | ------------ |
 | Öffentlicher Load Balancer oder eigenständig | [SNAT (Source Network Address Translation)](#snat) </br> Nicht verwendet. | TCP (Transmission Control Protocol) </br> UDP (User Datagram Protocol) </br> ICMP (Internet Control Message Protocol) </br> ESP (Encapsulating Security Payload) |


 #### <a name="description"></a>BESCHREIBUNG


 In Azure wird die öffentliche IP-Adresse verwendet, die der IP-Konfiguration der NIC einer Instanz für alle ausgehenden Datenflüsse zugewiesen ist. Für die Instanz sind alle kurzlebigen Ports verfügbar. Es ist unerheblich, ob der virtuelle Computer einen Lastenausgleich aufweist oder nicht. Dieses Szenario hat Vorrang vor den anderen Szenarien. 


 Eine öffentliche IP-Adresse, die einem virtuellen Computer zugewiesen ist, ist eine 1:1-Beziehung (keine 1:n-Beziehung) und wird als zustandslose 1:1-NAT implementiert.


 ### <a name="scenario-2-virtual-machine-without-public-ip-and-behind-standard-public-load-balancer"></a><a name="scenario2"></a>Szenario 2: VM ohne öffentliche IP-Adresse und hinter öffentlichem Load Balancer Standard


 | Associations | Methode | IP-Protokolle |
 | ------------ | ------ | ------------ |
 | Öffentlicher Load Balancer | Verwendung von Load Balancer-Front-End-IP-Adressen für [SNAT](#snat)| TCP </br> UDP |


 #### <a name="description"></a>BESCHREIBUNG


 Die Load Balancer-Ressource wird mit einer Ausgangsregel oder einer Lastenausgleichsregel konfiguriert, über die Standard-SNAT aktiviert wird. Diese Regel wird zum Erstellen eines Links zwischen der öffentlichen Front-End-IP-Adresse und dem Back-End-Pool verwendet. 


 Wenn Sie diese Regelkonfiguration nicht abschließen, entspricht das Verhalten dem in Szenario 3 beschriebenen. 


 Es ist keine Regel mit einem Listener erforderlich, damit der Integritätstest erfolgreich durchgeführt wird.


 Wenn eine VM einen ausgehenden Datenfluss erstellt, übersetzt Azure die Quell-IP-Adresse in die öffentliche IP-Adresse des öffentlichen Load Balancer-Front-Ends. Diese Übersetzung erfolgt über [SNAT](#snat). 


 Mit kurzlebigen Ports der öffentlichen Front-End-IP-Adresse des Load Balancers werden die einzelnen Datenflüsse unterschieden, die von der VM stammen. Beim Erstellen ausgehender Datenflüsse werden für SNAT [vorab zugewiesene kurzlebige Ports](#preallocatedports) verwendet. 


 In diesem Zusammenhang werden die kurzlebigen für SNAT verwendeten Ports als SNAT-Ports bezeichnet. Es wird dringend empfohlen, explizit eine [Ausgangsregel](./outbound-rules.md) zu konfigurieren. Bei Verwendung von Standard-SNAT über eine Lastenausgleichsregel werden SNAT-Ports vorab zugeordnet, wie in der [Tabelle zur Zuordnung von Standard-SNAT-Ports](#snatporttable) angegeben.


 ### <a name="scenario-3-virtual-machine-without-public-ip-and-behind-basic-load-balancer"></a><a name="scenario3"></a>Szenario 3: VM ohne öffentliche IP-Adresse und hinter öffentlichem Load Balancer Basic


 | Associations | Methode | IP-Protokolle |
 | ------------ | ------ | ------------ |
 |Keine </br> Load Balancer Basic | [SNAT](#snat) mit dynamischer IP-Adresse auf Instanzebene| TCP </br> UDP | 

 #### <a name="description"></a>BESCHREIBUNG


 Wenn die VM einen ausgehenden Datenfluss erstellt, übersetzt Azure die Quell-IP-Adresse in eine dynamisch zugeordnete öffentliche Quell-IP-Adresse. Diese öffentliche IP-Adresse **ist nicht konfigurierbar** und kann nicht reserviert werden. Diese Adresse wird nicht auf den Grenzwert der öffentlichen IP-Ressourcen des Abonnements angerechnet. 


 Die öffentliche IP-Adresse wird freigegeben und eine neue öffentliche IP-Adresse wird angefordert, wenn Sie Folgendes erneut bereitstellen: 


 * Virtual Machine
 * Verfügbarkeitsgruppe
 * VM-Skalierungsgruppe 


 Verwenden Sie dieses Szenario nicht, um IP-Adressen einer Positivliste hinzuzufügen. Verwenden Sie Szenario 1 oder 2, bei dem Sie das ausgehende Verhalten explizit deklarieren. [SNAT](#snat)-Ports sind vorab zugeordnet, wie in der [Tabelle zur Zuordnung von Standard-SNAT-Ports](#snatporttable) angegeben.


## <a name="exhausting-ports"></a><a name="scenarios"></a> Ausschöpfung von Ports

Für jede Verbindung mit derselben IP-Adresse und demselben Zielport wird ein SNAT-Port verwendet. Diese Verbindung verwaltet einen eindeutigen **Datenverkehrsfluss** von der Back-End-Instanz oder dem **Client** zu einem **Server**. Durch diesen Prozess erhält der Server einen eindeutigen Port, an den Datenverkehr adressiert werden soll. Ohne diesen Prozess weiß der Clientcomputer nicht, zu welchem Datenfluss ein Paket gehört.

Stellen Sie sich vor, dass mehrere Browser https://www.microsoft.com verwenden:

* IP-Zieladresse = 23.53.254.142
* Zielport = 443
* Protokoll = TCP

Ohne unterschiedliche Zielports für den Rückgabedatenverkehr (der SNAT-Port, der zum Herstellen der Verbindung verwendet wird) hat der Client keine Möglichkeit, ein Abfrageergebnis von einem anderen zu trennen.

Für ausgehende Verbindungen kann ein Burst auftreten. Einer Back-End-Instanz können nicht genügend Ports zugeordnet werden. Wenn **Wiederverwendung von Verbindungen** nicht aktiviert ist, wird das Risiko von SNAT-**Porterschöpfung** gesteigert.

Neue ausgehende Verbindungen mit einer IP-Zieladresse schlagen fehl, wenn Porterschöpfung auftritt. Verbindungen werden erfolgreich hergestellt, wenn ein Port verfügbar wird. Diese Erschöpfung tritt auf, wenn die 64.000 Ports von einer IP-Adresse dünn über viele Back-End-Instanzen verteilt sind. Anleitungen zur Entschärfung von SNAT-Porterschöpfung finden Sie im [Leitfaden zur Problembehandlung](./troubleshoot-outbound-connection.md).  

Bei TCP-Verbindungen verwendet der Load Balancer einen einzelnen SNAT-Port für jede Ziel-IP und jeden -Port. Diese Mehrfachnutzung ermöglicht mehrere Verbindungen mit derselben IP-Zieladresse und demselben SNAT-Port. Diese Mehrfachnutzung ist eingeschränkt, wenn die Verbindung nicht mit verschiedenen Zielports erfolgt.

Für UDP-Verbindungen verwendet der Load Balancer einen **porteingeschränkten Cone NAT**-Algorithmus, der einen SNAT-Port pro IP-Zieladresse für den Zielport verwendet. 

Ein Port wird für eine unbegrenzte Anzahl von Verbindungen wiederverwendet. Der Port wird nur wiederverwendet, wenn die IP-Zieladresse oder der -port unterschiedlich ist.

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> Standardportzuordnung

Jede öffentliche IP-Adresse, die als Front-End-IP-Adresse Ihres Load Balancers zugewiesen ist, erhält 64.000 SNAT-Ports für die zugehörigen Back-End-Elemente. Ports können nicht für Back-End-Poolelemente freigegeben werden. Ein Bereich von SNAT-Ports kann nur von einer einzelnen Back-End-Instanz verwendet werden, um sicherzustellen, dass Rückgabepakete ordnungsgemäß weitergeleitet werden. 

Es wird empfohlen, eine explizite Ausgangsregel zum Konfigurieren der SNAT-Portzuordnung zu verwenden. Diese Regel maximiert die Anzahl der SNAT-Ports, die jeder Back-End-Instanz für ausgehende Verbindungen zur Verfügung stehen. 

Sollten Sie automatische Zuordnung von ausgehendem SNAT durch eine Lastausgleichsregel verwenden, definiert die Zuordnungstabelle Ihre Portzuordnung.

In der folgenden <a name="snatporttable"></a>-Tabelle werden die SNAT-Port-Vorabzuordnungen für die Ebenen der Back-End-Poolgrößen angegeben:

| Poolgröße (VM-Instanzen) | Vorab zugeordnete SNAT-Ports pro IP-Konfiguration |
| --- | --- |
| 1-50 | 1\.024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1.000 | 32 | 

>[!NOTE]
> Wenn Sie über einen Back-End-Pool mit einer maximalen Größe von 10 verfügen, kann jede Instanz 64.000/10 = 6.400 Ports aufweisen, wenn Sie eine explizite Ausgangsregel definieren. Gemäß der Tabelle oben verfügt jede Instanz nur über 1.024 Ports, wenn Sie automatische Zuordnung auswählen.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Ausgangsregeln und Virtual Network NAT

Ausgangsregeln von Azure Load Balancer und Virtual Network NAT sind Optionen, die für ausgehenden Datenverkehr aus einem virtuellen Netzwerk verfügbar sind.

Weitere Informationen zu Ausgangsregeln finden Sie unter [Ausgangsregeln](outbound-rules.md).

Weitere Informationen zu Azure Virtual Network NAT finden Sie unter [Was ist Azure Virtual Network NAT?](../virtual-network/nat-overview.md)

## <a name="constraints"></a>Einschränkungen

*   Wenn sich eine Verbindung im Leerlauf befindet und keine neuen Pakete gesendet werden, werden die Ports nach 4 bis 120 Minuten freigegeben.
  * Dieser Schwellenwert kann über Ausgangsregeln konfiguriert werden.
*   Jede IP-Adresse stellt 64.000 Ports bereit, die für SNAT verwendet werden können.
*   Jeder Port kann sowohl für TCP- als auch für UDP-Verbindungen mit einer IP-Zieladresse verwendet werden.
  * Ein UDP-SNAT-Port ist unabhängig davon erforderlich, ob der Zielport eindeutig ist. Für jede UDP-Verbindung mit einer IP-Zieladresse wird ein UDP-SNAT-Port verwendet.
  * Ein TCP-SNAT-Port kann für mehrere Verbindungen mit derselben IP-Zieladresse verwendet werden, vorausgesetzt, die Zielports unterscheiden sich.
*   SNAT-Erschöpfung tritt auf, wenn einer Back-End-Instanz die angegebenen SNAT-Ports ausgehen. Ein Load Balancer kann weiterhin nicht verwendete SNAT-Ports aufweisen. Wenn die von einer Back-End-Instanz verwendeten SNAT-Ports die angegebenen SNAT-Ports überschreiten, können keine neuen ausgehenden Verbindungen hergestellt werden.

## <a name="next-steps"></a>Nächste Schritte

*   [Problembehandlung von Fehlern bei ausgehenden Verbindungen aufgrund von SNAT-Erschöpfung](./troubleshoot-outbound-connection.md)
*   [Überprüfen Sie die SNAT-Metriken](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation), und machen Sie sich mit den richtigen Verfahren zum Filtern, Aufteilen und Anzeigen dieser Metriken vertraut.
