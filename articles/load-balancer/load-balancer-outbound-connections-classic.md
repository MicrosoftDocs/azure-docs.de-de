---
title: Ausgehende Verbindungen in Azure (klassisch)
titlesuffix: Azure Load Balancer
description: In diesem Artikel wird erläutert, wie Azure Clouddiensten die Kommunikation mit öffentlichen Internetdiensten ermöglicht.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2018
ms.author: kumud
ms.openlocfilehash: 3267d79387586f5ca8475d7ac0ed0f86d3f64f0d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60595040"
---
# <a name="outbound-connections-classic"></a>Ausgehende Verbindungen (klassisch)

In Azure wird die Konnektivität in ausgehender Richtung für Benutzerbereitstellungen mit mehreren unterschiedlichen Mechanismen erzielt. In diesem Artikel wird beschrieben, welche Szenarien es gibt, wann sie zutreffen, wie sie funktionieren und wie sie verwaltet werden.

>[!NOTE]
>Dieser Artikel gilt nur für klassische Bereitstellungen.  Lesen Sie für alle Resource Manager-Bereitstellungsszenarien in Azure den Artikel [Ausgehende Verbindungen in Azure](load-balancer-outbound-connections.md).

Eine Bereitstellung in Azure kann mit Endpunkten außerhalb von Azure im öffentlichen IP-Adressraum kommunizieren. Wenn eine Instanz einen ausgehenden Datenfluss zu einem Ziel im öffentlichen IP-Adressraum initiiert, ordnet Azure die private IP-Adresse dynamisch einer öffentlichen IP-Adresse zu. Nachdem diese Zuordnung erstellt wurde, kann der Antwortdatenverkehr für diesen ursprünglich ausgehenden Datenfluss auch die private IP-Adresse erreichen, von welcher der Datenfluss stammt.

Azure verwendet für diese Aufgabe das Verfahren „Übersetzung der Quellnetzwerkadresse“ (Source Network Address Translation, SNAT). Wenn mehrere private IP-Adressen durch eine einzelne öffentliche IP-Adresse maskiert sind, wird in Azure die [Portadressenübersetzung](#pat) (Port Address Translation, PAT) genutzt, um private IP-Adressen zu maskieren. Kurzlebige Ports werden für PAT verwendet und sind je nach Poolgröße [vorab zugeordnet](#preallocatedports).

Es gibt mehrere [Szenarien für die ausgehende Richtung](#scenarios). Diese Szenarien können nach Bedarf kombiniert werden. Prüfen Sie sie sorgfältig, um die Funktionen, Einschränkungen und Muster sowie die damit verbundenen Auswirkungen auf Ihr Bereitstellungsmodell und Anwendungsszenario zu verstehen. Sehen Sie sich die Anleitung zur [Verwaltung dieser Szenarien](#snatexhaust) an.

## <a name="scenarios"></a>Übersicht über das Szenario

Azure bietet drei verschiedene Methoden, um ausgehende Konnektivität für klassische Bereitstellungen zu ermöglichen.  Es stehen nicht für alle klassischen Bereitstellungen alle drei Szenarien zur Verfügung:

| Szenario | Methode | IP-Protokolle | BESCHREIBUNG | Webworkerrolle | IaaS | 
| --- | --- | --- | --- | --- | --- |
| [1. VM mit einer öffentlichen IP-Adresse auf Instanzebene](#ilpip) | SNAT, keine Portmaskierung | TCP, UDP, ICMP, ESP | Azure verwendet die öffentliche IP-Adresse, die dem virtuellen Computer zugewiesen ist. Für die Instanz sind alle kurzlebigen Ports verfügbar. | Nein | Ja |
| [2. Öffentlicher Endpunkt mit Lastenausgleich](#publiclbendpoint) | SNAT mit Portmaskierung (PAT) für den öffentlichen Endpunkt | TCP, UDP | Azure gibt die öffentliche IP-Adresse eines öffentlichen Endpunkts für mehrere private Endpunkte frei. Azure verwendet die kurzlebigen Ports des öffentlichen Endpunkts für die PAT. | Ja | Ja |
| [3. Eigenständige VM](#defaultsnat) | SNAT mit Portmaskierung (PAT) | TCP, UDP | Azure weist für die SNAT automatisch eine öffentliche IP-Adresse zu, gibt diese öffentliche IP-Adresse für die gesamte Bereitstellung frei und verwendet die kurzlebigen Ports dieser öffentlichen Endpunkt-IP-Adresse für die PAT. Dieses Szenario ist ein Fallbackszenario für die vorherigen Szenarien. Es ist nicht zu empfehlen, wenn Sie Sichtbarkeit und Kontrolle benötigen. | Ja | Ja |

Dies ist eine Teilmenge der Funktion für ausgehende Verbindungen, die für Resource Manager-Bereitstellungen in Azure verfügbar ist.  

Verschiedene Bereitstellungen im klassischen Portal weisen unterschiedliche Funktionen auf:

| Klassische Bereitstellung | Verfügbare Funktionen | 
| --- | --- |
| Virtual Machine | Szenario [1](#ilpip), [2](#publiclbendpoint) oder [3](#defaultsnat) |
| Webworkerrolle | Nur Szenario [2](#publiclbendpoint), [3](#defaultsnat) | 

Für [Lösungsstrategien](#snatexhaust) gelten die gleichen Unterschiede.

Der Algorithmus zum Reservieren von kurzlebigen Ports für die PAT bei klassischen Bereitstellungen entspricht dem Algorithmus für Azure Resource Manager-Ressourcenbereitstellungen.

### <a name="ilpip"></a>Szenario 1: VM mit einer öffentlichen IP-Adresse auf Instanzebene

In diesem Szenario ist der VM eine öffentliche IP-Adresse auf Instanzebene (Instance Level Public IP, ILPIP) zugewiesen. Bei ausgehenden Verbindungen spielt es keine Rolle, ob die VM einen Endpunkt mit Lastenausgleich aufweist. Dieses Szenario hat Vorrang vor den anderen Szenarien. Wenn eine öffentliche IP-Adresse auf Instanzebene (ILPIP) verwendet wird, nutzt die VM die ILPIP für alle ausgehenden Datenflüsse.  

Eine öffentliche IP-Adresse, die einem virtuellen Computer zugewiesen ist, ist eine 1:1-Beziehung (keine 1:n-Beziehung) und wird als zustandslose 1:1-NAT implementiert.  Es wird keine Portmaskierung (PAT) verwendet, und für den virtuellen Computer sind alle kurzlebigen Ports verfügbar.

Wenn Ihre Anwendung viele ausgehende Datenflüsse initiiert und es zu einer Überlastung der SNAT-Ports kommt, sollten Sie das [Zuweisen einer öffentlichen IP-Adresse auf Instanzebene erwägen, um SNAT-Engpässe zu entschärfen](#assignilpip). Lesen Sie den Abschnitt [Verwalten der SNAT-Auslastung](#snatexhaust) ganz durch.

### <a name="publiclbendpoint"></a>Szenario 2: Öffentlicher Endpunkt mit Lastenausgleich

In diesem Szenario ist die VM oder Webworkerrolle über den Endpunkt mit Lastenausgleich einer öffentlichen IP-Adresse zugeordnet. Der VM ist keine öffentliche IP-Adresse zugewiesen. 

Wenn die dem Lastenausgleich unterliegende VM einen ausgehenden Datenfluss erstellt, übersetzt Azure die private IP-Quelladresse des ausgehenden Datenflusses in die öffentliche IP-Adresse des öffentlichen Endpunkts mit Lastenausgleich. Azure verwendet SNAT, um diese Funktion durchzuführen. Azure verwendet auch [PAT](#pat), um mehrere private IP-Adressen durch eine öffentliche IP-Adresse zu maskieren. 

Mit kurzlebigen Ports der öffentlichen Front-End-IP-Adresse des Lastenausgleich werden die einzelnen Datenflüsse unterschieden, die von dem virtuellen Computer stammen. Beim Erstellen ausgehender Datenflüsse werden für SNAT [vorab zugewiesene kurzlebige Ports](#preallocatedports) verwendet. In diesem Zusammenhang werden die kurzlebigen für SNAT verwendeten Ports als SNAT-Ports bezeichnet.

SNAT-Ports werden vorab zugeordnet, wie im Abschnitt [Grundlagen von SNAT und PAT](#snat) beschrieben. Es handelt sich um eine begrenzte Ressource, die überlastet werden kann. Es ist wichtig zu verstehen, wie sie [genutzt](#pat) wird. Um zu verstehen, wie Sie diese Nutzung beim Entwurf berücksichtigen und je nach Bedarf Abhilfemaßnahmen schaffen können, lesen Sie den Abschnitt [Verwalten der SNAT-Auslastung](#snatexhaust).

Wenn [mehrere Endpunkte mit öffentlichem Lastenausgleich](load-balancer-multivip.md) vorhanden sind, sind diese öffentlichen IP-Adressen Kandidaten für ausgehende Datenflüsse, und eine davon wird zufällig ausgewählt.  

### <a name="defaultsnat"></a>Szenario 3: Keine zugeordnete öffentliche IP-Adresse

In diesem Szenario ist die VM oder Webworkerrolle nicht Teil eines öffentlichen Endpunkts mit Lastenausgleich.  VMs sind keine ILPIP-Adressen zugewiesen. Wenn der virtuelle Computer einen ausgehenden Datenfluss einleitet, übersetzt Azure die private IP-Quelladresse für den ausgehenden Datenfluss in eine öffentliche IP-Quelladresse. Die für diesen ausgehenden Datenfluss verwendete öffentliche IP-Adresse ist nicht konfigurierbar und wird nicht auf die Ressourcengrenze des Abonnements für öffentliche IP-Adressen angerechnet.  Azure weist diese Adresse automatisch zu.

Azure verwendet SNAT mit Portmaskierung ([PAT](#pat)) für diese Aufgabe. Dieses Szenario ähnelt Szenario 2. Der Unterschied besteht darin, dass keine Steuerung der IP-Adresse verwendet wird. Dies ist ein Fallbackszenario für den Fall, dass Szenario 1 und Szenario 2 nicht vorhanden sind. Dieses Szenario wird nicht empfohlen, wenn eine Kontrolle über die ausgehende Adresse gewünscht ist. Wenn ausgehende Verbindungen ein wichtiger Teil Ihrer Anwendung sind, sollten Sie ein anderes Szenario wählen.

SNAT-Ports werden vorab zugeordnet, wie im Abschnitt [Grundlagen von SNAT und PAT](#snat) beschrieben.  Die Anzahl der VMs oder Webworkerrollen, die gemeinsam die öffentliche IP-Adresse verwenden, bestimmt die Anzahl der reservierten kurzlebigen Ports.   Es ist wichtig zu verstehen, wie sie [genutzt](#pat) wird. Um zu verstehen, wie Sie diese Nutzung beim Entwurf berücksichtigen und je nach Bedarf Abhilfemaßnahmen schaffen können, lesen Sie den Abschnitt [Verwalten der SNAT-Auslastung](#snatexhaust).

## <a name="snat"></a>Grundlagen von SNAT und PAT

### <a name="pat"></a>Portmaskierung mit SNAT (PAT)

Wenn eine Bereitstellung eine ausgehende Verbindung herstellt, wird jede Quelle für ausgehende Verbindungen umgeschrieben. Die Quelle wird von dem privaten IP-Adressraum in die der Bereitstellung zugeordneten öffentliche IP-Adresse umgeschrieben (auf Grundlage der oben beschriebenen Szenarios). Im öffentlichen IP-Adressraum müssen die fünf Tupel des Datenflusses (IP-Quelladresse, Quellport, IP-Transportprotokoll, IP-Zieladresse, Zielport) eindeutig sein.  

Hierfür werden kurzlebige Ports (SNAT-Ports) verwendet, nachdem die private IP-Quelladresse umgeschrieben wurde, da mehrere Datenflüsse von einer einzelnen öffentlichen IP-Adresse stammen. 

Pro Datenfluss zu einer einzelnen IP-Zieladresse, einem Port und einem Protokoll wird ein SNAT-Port genutzt. Bei mehreren Datenflüssen zur gleichen IP-Zieladresse bzw. zum Port und Protokoll belegt jeder Datenfluss einen einzelnen SNAT-Port. Hierdurch wird sichergestellt, dass die Datenflüsse eindeutig sind, wenn sie von der gleichen öffentlichen IP-Adresse stammen und die gleiche IP-Zieladresse, den gleichen Port und das gleiche Protokoll aufweisen. 

Mehrere Datenflüsse mit jeweils anderen Angaben für IP-Zieladresse, Port und Protokoll verwenden gemeinsam einen einzelnen SNAT-Port. Die IP-Adresse, der Port und das Protokoll für das Ziel machen den Datenfluss eindeutig, ohne dass zusätzliche Quellports verwendet werden müssen, um Datenflüsse im öffentlichen IP-Adressraum zu unterscheiden.

Sobald die SNAT-Portressourcen erschöpft sind, sind ausgehende Datenflüsse erst wieder möglich, wenn SNAT-Ports von vorhandenen Datenflüssen freigegeben werden. Der Load Balancer gibt die SNAT-Ports wieder frei, wenn der Datenflussvorgang abgeschlossen ist. Es wird ein [Leerlauftimeout von 4 Minuten](#idletimeout) verwendet, um SNAT-Ports für im Leerlauf befindliche Datenflüsse wieder freizugeben.

Im Abschnitt [Verwalten von SNAT](#snatexhaust) werden Muster für das Entschärfen von Bedingungen beschrieben, die häufig zu einer Überlastung von SNAT-Ports führen.

### <a name="preallocatedports"></a>Vorabzuordnung von kurzlebigen Ports für die Portmaskierung per SNAT (PAT)

In Azure wird ein Algorithmus verwendet, um basierend auf der Größe des Back-End-Pools bei der Portmaskierung per SNAT ([PAT](#pat)) die Anzahl von verfügbaren vorab zugeordneten SNAT-Ports zu ermitteln. SNAT-Ports sind kurzlebige Ports, die für eine bestimmte öffentliche IP-Quelladresse verfügbar sind.

Azure reserviert bei der Bereitstellung einer Instanz SNAT-Ports abhängig davon, wie viele VM- oder Webworkerrollen-Instanzen eine bestimmte öffentliche IP-Adresse gemeinsam nutzen.  Bei Erstellung von ausgehenden Datenflüssen werden diese Ports von [PAT](#pat) dynamisch genutzt (bis zum vorab festgelegten Grenzwert) und wieder freigegeben, wenn der Datenfluss geschlossen wird oder ein Leerlauftimeout eintritt.

In der folgenden Tabelle sind die SNAT-Port-Vorabzuordnungen für die Ebenen der Back-End-Poolgrößen angegeben:

| Instanzen | Reservierte SNAT-Ports pro Instanz |
| --- | --- |
| 1-50 | 1024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |

Denken Sie daran, dass die Anzahl der verfügbaren SNAT-Ports nicht direkt in die Anzahl der Datenflüsse zu übersetzen ist. Ein einzelner SNAT-Port kann für mehrere eindeutige Ziele wiederverwendet werden. Ports werden nur genutzt, wenn Datenflüsse eindeutig gemacht werden müssen. Eine Anleitung für den Entwurf und Lösungsmöglichkeiten finden Sie in den Abschnitten, in den die [Verwaltung dieser begrenzten Ressource](#snatexhaust) und [PAT](#pat) beschrieben sind.

Von einer Größenänderung Ihrer Bereitstellung können einige der eingerichteten Datenflüsse betroffen sein. Wenn die Größe des Back-End-Pools zunimmt und der Übergang auf die nächste Ebene erfolgt, wird die Hälfte der vorab zugeordneten SNAT-Ports während des Übergangs zur nächstgrößeren Back-End-Poolebene freigegeben. Bei Datenflüssen, die einem wieder freigegebenen SNAT-Port zugeordnet sind, tritt ein Timeout auf, und sie müssen neu eingerichtet werden. Versuche zum Einrichten eines neuen Datenflusses sind sofort erfolgreich, solange vorab zugeordnete Ports verfügbar sind.

Wenn sich die Größe der Bereitstellung reduziert und der Übergang auf eine niedrigere Ebene erfolgt, nimmt die Anzahl der verfügbaren SNAT-Ports zu. In diesem Fall sind vorhandene zugeordnete SNAT-Ports und die entsprechenden Datenflüsse nicht betroffen.

Wenn ein Clouddienst erneut bereitgestellt oder geändert wird, kann die Infrastruktur möglicherweise vorübergehend eine Back-End-Poolgröße melden, die dem doppelten der realen Größe entspricht, und Azure wird wiederum weniger SNAT-Ports pro Instanz als erwartet reservieren.  Dies kann vorübergehend die Wahrscheinlichkeit der SNAT-Portüberlastung erhöhen. Schließlich wird die Poolgröße zum realen Wert übergehen und Azure die reservierten SNAT-Ports automatisch gemäß der obigen Tabelle auf die erwartete Anzahl erhöhen.  Dieses Verhalten ist beabsichtigt und nicht konfigurierbar.

SNAT-Portzuordnungen gelten speziell für das jeweilige IP-Transportprotokoll (TCP und UDP werden separat verwaltet) und werden unter den folgenden Bedingungen freigegeben:

### <a name="tcp-snat-port-release"></a>TCP-SNAT-Portfreigabe

- Wenn Server und Client FIN/ACK senden, wird der SNAT-Port nach 240 Sekunden freigegeben.
- Tritt ein RST auf, wird der SNAT-Port nach 15 Sekunden freigegeben.
- Leerlauftimeout wurde erreicht.

### <a name="udp-snat-port-release"></a>UDP-SNAT-Portfreigabe

- Leerlauftimeout wurde erreicht.

## <a name="problemsolving"></a> Problembehebung 

Dieser Abschnitt soll dazu beitragen, die SNAT-Überlastung und andere Szenarien, die bei ausgehenden Verbindungen in Azure auftreten können, zu verringern.

### <a name="snatexhaust"></a> Verwalten der SNAT-Portauslastung (PAT)
Die für die [PAT](#pat) verwendeten [kurzlebigen Ports](#preallocatedports) stellen eine erschöpfbare Ressource dar, wie unter [Keine zugeordnete öffentliche IP-Adresse](#defaultsnat) und [Öffentlicher Endpunkt mit Lastenausgleich](#publiclbendpoint) beschrieben wird.

Wenn Sie wissen, dass Sie viele ausgehende TCP- oder UDP-Verbindungen zu derselben IP-Zieladresse und demselben Port initiieren und Fehler bei ausgehenden Verbindungen feststellen oder vom Support darauf hingewiesen werden, dass Sie zu viele SNAT-Ports (vorab zugeordnete [kurzlebige Ports](#preallocatedports), die für [PAT](#pat) verwendet werden) in Anspruch nehmen, stehen Ihnen mehrere Lösungsmöglichkeiten zur Verfügung. Überprüfen Sie diese Optionen, und entscheiden Sie, welche für Ihr Szenario verfügbar und am besten geeignet sind. Möglicherweise kann die ein oder andere die Verwaltung dieses Szenarios erleichtern.

Wenn Sie Probleme haben, das Verhalten der ausgehenden Verbindungen zu verstehen, können Sie die IP-Stapelstatistiken (netstat) verwenden. Es kann aber auch hilfreich sein, das Verbindungsverhalten mithilfe von Paketerfassungen zu beobachten.

#### <a name="connectionreuse"></a>Ändern der Anwendung für die Wiederverwendung von Verbindungen 
Sie können den Bedarf an kurzlebigen Ports, die für SNAT verwendet werden, durch Wiederverwenden von Verbindungen in der Anwendung reduzieren. Dies gilt insbesondere für Protokolle wie HTTP/1.1, das standardmäßig Verbindungen wiederverwendet. Andere Protokolle, die HTTP als Transportprotokoll verwenden (z. B. REST) können davon wiederum profitieren. 

Die Wiederverwendung ist immer besser als einzelne, unteilbare TCP-Verbindungen für jede Anforderung. Die Wiederverwendung führt zu leistungsfähigeren, sehr effizienten TCP-Transaktionen.

#### <a name="connection pooling"></a>Ändern der Anwendung für die Verwendung von Verbindungspooling
Sie können in Ihrer Anwendung ein Verbindungspoolingschema verwenden, bei dem Anforderungen intern auf einen festen Satz von Verbindungen verteilt werden (wobei die Verbindungen möglichst wiederverwendet werden). Bei diesem Schema wird die Anzahl der verwendeten kurzlebigen Ports eingeschränkt und eine besser vorhersagbare Umgebung erstellt. Dies kann auch den Durchsatz von Anforderungen erhöhen, da mehrere gleichzeitige Vorgänge zugelassen werden, wenn eine einzelne Verbindung durch die Antwort eines Vorgangs blockiert wird.  

Verbindungspooling ist möglicherweise innerhalb des Frameworks, das Sie zum Entwickeln Ihrer Anwendung oder der Konfigurationseinstellungen für Ihre Anwendung verwenden, bereits vorhanden. Sie können das Verbindungspooling mit der Wiederverwendung von Verbindungen kombinieren. Mehrere Anforderungen nutzen dann eine feste vorhersagbare Anzahl von Ports für die gleiche IP-Zieladresse und den gleichen Port. Bei den Anforderungen profitieren Sie außerdem von der effizienten Verwendung von TCP-Transaktionen, was zu niedrigeren Latenzzeiten und geringerer Ressourcenauslastung führt. Auch bei den UDP-Transaktionen können Sie profitieren, da Sie durch das Verwalten der Anzahl von UDP-Datenflüssen wiederum Zustände mit Überlastung vermeiden und die SNAT-Portauslastung verwalten können.

#### <a name="retry logic"></a>Ändern der Anwendung für die Verwendung weniger aggressiver Wiederholungslogik
Wenn die für [PAT](#pat) verwendeten [vorab zugeordneten kurzlebigen Ports](#preallocatedports) erschöpft sind oder Anwendungsfehler auftreten, führen aggressive oder Brute-Force-Wiederholungsversuche ohne Verfalls- und Backofflogik zu Erschöpfung oder gar anhaltender Erschöpfung. Sie können den Bedarf an kurzlebigen Ports durch Verwendung einer weniger aggressiven Wiederholungslogik reduzieren. 

Kurzlebige Ports haben ein Leerlauftimeout von vier Minuten (nicht veränderbar). Wenn die Wiederholungen zu aggressiv sind, besteht keine Möglichkeit, dass sich die Überlastung von alleine erholt. Daher ist die Überlegung, wie und wie oft Ihre Anwendung Transaktionen wiederholen soll, ein wichtiger Aspekt des Entwurfs.

#### <a name="assignilpip"></a>Zuweisen einer öffentlichen IP-Adresse auf Instanzebene für jeden virtuellen Computer
Durch das Zuweisen einer öffentlichen IP-Adresse auf Instanzebene erhalten Sie das Szenario [Virtueller Computer mit öffentlicher IP-Adresse auf Instanzebene](#ilpip). Alle kurzlebigen Ports für die öffentliche IP-Adresse, die für jeden virtuellen Computer verwendet werden, sind für den virtuellen Computer verfügbar. (Das steht im Gegensatz zu Szenarien, bei denen kurzlebige Ports für eine öffentliche IP-Adresse von allen VMs, die dem entsprechenden Bereitstellung zugeordnet sind, gemeinsam genutzt werden.) Es müssen auch Nachteile berücksichtigt werden – etwa mögliche Auswirkungen des Whitelistings einer großen Anzahl von einzelnen IP-Adressen.

>[!NOTE] 
>Diese Option ist für Webworkerrollen nicht verfügbar.

### <a name="idletimeout"></a>Verwenden von Keepalives zum Zurücksetzen des Leerlauftimeouts für ausgehende Verbindungen

Ausgehende Verbindungen haben einen 4-Minuten-Leerlauftimeout. Dieses Timeout ist nicht anpassbar. Sie können jedoch Keepalives auf der Transportschicht (z. B. TCP-Keepalives) oder der Anwendungsschicht verwenden, um einen im Leerlauf befindlichen Datenfluss zu aktualisieren und den Leerlauftimeout bei Bedarf zurückzusetzen.  Wenden Sie sich an den Hersteller von Softwarepaketen, um in Erfahrung zu bringen, ob dieses Feature unterstützt oder wie es aktiviert wird.  In der Regel muss nur eine Partei Keep-alives generieren, um das Leerlauftimeout zurückzusetzen. 

## <a name="discoveroutbound"></a>Ermitteln der von einem virtuellen Computer verwendeten öffentlichen IP-Adresse
Es gibt viele Möglichkeiten, die öffentliche IP-Quelladresse einer ausgehenden Verbindung zu bestimmen. OpenDNS bietet einen Dienst, der die öffentliche IP-Adresse Ihrer VM anzeigen kann. 

Mit dem Befehl „nslookup“ können Sie eine DNS-Abfrage für den Namen „myip.opendns.com“ an den OpenDNS-Resolver senden. Der Dienst gibt die IP-Quelladresse zurück, die zum Senden der Abfrage verwendet wurde. Wenn Sie die folgende Abfrage auf Ihrem virtuellen Computer ausführen, wird die öffentliche IP-Adresse zurückgegeben, die für diesen virtuellen Computer verwendet wird:

    nslookup myip.opendns.com resolver1.opendns.com


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über in Resource Manager-Bereitstellungen verwendeten [Load Balancer](load-balancer-overview.md).
- Informieren Sie sich ausführlicher über verfügbare Szenarien mit [ausgehender Verbindung](load-balancer-outbound-connections.md) in Resource Manager-Bereitstellungen.
