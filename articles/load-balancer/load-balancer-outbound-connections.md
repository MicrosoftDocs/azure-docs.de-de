---
title: Ausgehende Verbindungen in Azure
titlesuffix: Azure Load Balancer
description: In diesem Artikel wird erläutert, wie Azure virtuellen Computern die Kommunikation mit öffentlichen Internetdiensten ermöglicht.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: kumud
ms.openlocfilehash: f9742d14fc14230f2424d005aa6aa8b1db3cece4
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967741"
---
# <a name="outbound-connections-in-azure"></a>Ausgehende Verbindungen in Azure

In Azure wird die Konnektivität in ausgehender Richtung für Benutzerbereitstellungen mit mehreren unterschiedlichen Mechanismen erzielt. In diesem Artikel wird beschrieben, welche Szenarien es gibt, wann sie zutreffen, wie sie funktionieren und wie sie verwaltet werden.

>[!NOTE] 
>Dieser Artikel gilt nur für Ressourcen-Manager-Bereitstellungen. Lesen Sie für alle klassischen Bereitstellungsszenarien in Azure den Artikel [Ausgehende Verbindungen (klassisch)](load-balancer-outbound-connections-classic.md).

Eine Bereitstellung in Azure kann mit Endpunkten außerhalb von Azure im öffentlichen IP-Adressraum kommunizieren. Wenn eine Instanz einen ausgehenden Datenfluss zu einem Ziel im öffentlichen IP-Adressraum initiiert, ordnet Azure die private IP-Adresse dynamisch einer öffentlichen IP-Adresse zu. Nachdem diese Zuordnung erstellt wurde, kann der Antwortdatenverkehr für diesen ursprünglich ausgehenden Datenfluss auch die private IP-Adresse erreichen, von welcher der Datenfluss stammt.

Azure verwendet für diese Aufgabe das Verfahren „Übersetzung der Quellnetzwerkadresse“ (Source Network Address Translation, SNAT). Wenn mehrere private IP-Adressen durch eine einzelne öffentliche IP-Adresse maskiert sind, wird in Azure die [Portadressenübersetzung](#pat) (Port Address Translation, PAT) genutzt, um private IP-Adressen zu maskieren. Kurzlebige Ports werden für PAT verwendet und sind je nach Poolgröße [vorab zugeordnet](#preallocatedports).

Es gibt mehrere [Szenarien für die ausgehende Richtung](#scenarios). Diese Szenarien können nach Bedarf kombiniert werden. Prüfen Sie sie sorgfältig, um die Funktionen, Einschränkungen und Muster sowie die damit verbundenen Auswirkungen auf Ihr Bereitstellungsmodell und Anwendungsszenario zu verstehen. Sehen Sie sich die Anleitung zur [Verwaltung dieser Szenarien](#snatexhaust) an.

>[!IMPORTANT] 
>Load Balancer Standard und Standard Public IP führen neue Fähigkeiten und andere Verhaltensweisen für ausgehende Verbindungen ein.  Sie sind nicht identisch mit Basic-SKUs.  Wenn Sie bei der Arbeit mit Standard-SKUs ausgehende Verbindungen wünschen, müssen Sie diese explizit entweder mit Standard Public IP-Adressen oder Standard Public Load Balancer definieren.  Dies umfasst die Erstellung von ausgehenden Verbindungen bei der Verwendung eines internen Load Balancer Standard.  Es wird empfohlen, dass Sie für einen Standard Public Load Balancer immer Ausgangsregeln verwenden.  [Szenario 3](#defaultsnat) ist mit der Standard-SKU nicht verfügbar.  Das bedeutet, dass Sie bei Verwendung eines internen Load Balancer Standard Schritte unternehmen müssen, um die ausgehenden Verbindungen für die VMs im Back-End-Pool herzustellen, wenn ausgehende Verbindungen gewünscht sind.  Im Kontext der ausgehenden Verbindungen verhalten sich eine einzelne eigenständige VM, alle VMs in einer Verfügbarkeitsgruppe sowie alle Instanzen in einem VMSS als Gruppe. Das bedeutet, wenn eine einzelne VM in einer Verfügbarkeitsgruppe einer Standard-SKU zugeordnet ist, verhalten sich jetzt alle VM-Instanzen in dieser Verfügbarkeitsgruppe nach denselben Regeln wie bei der Zuordnung zu einer Standard-SKU, auch wenn eine einzelne Instanz nicht direkt mit ihr verbunden ist.  Lesen Sie dieses Dokument sorgfältig und vollständig, um die allgemeinen Konzepte zu verstehen, überprüfen Sie [Load Balancer Standard](load-balancer-standard-overview.md) auf Unterschiede zwischen den SKUs, und überprüfen Sie die [Ausgangsregeln](load-balancer-outbound-rules-overview.md).  Die Verwendung von Ausgangsregeln ermöglicht Ihnen eine differenzierte Steuerung aller Aspekte ausgehender Verbindungen.

## <a name="scenarios"></a>Übersicht über das Szenario

Azure Load Balancer und die zugehörigen Ressourcen werden bei Verwendung von [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) explizit definiert.  In Azure gibt es derzeit drei verschiedene Methoden, wie die ausgehende Konnektivität für Azure Resource Manager-Ressourcen erreicht werden kann. 

| SKUs | Szenario | Methode | IP-Protokolle | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| Standard, Basic | [1. Virtuelle Computer mit öffentlicher IP-Adresse auf Instanzebene (mit oder ohne Azure Load Balancer)](#ilpip) | SNAT, keine Portmaskierung | TCP, UDP, ICMP, ESP | In Azure wird die öffentliche IP-Adresse verwendet, die der IP-Konfiguration der NIC einer Instanz zugewiesen ist. Für die Instanz sind alle kurzlebigen Ports verfügbar. Bei der Verwendung von Load Balancer Standard sollten Sie [Ausgangsregeln](load-balancer-outbound-rules-overview.md) verwenden, um die ausgehenden Verbindungen explizit zu definieren. |
| Standard, Basic | [2. Öffentlicher Lastenausgleich, der einem virtuellen Computer zugewiesen ist (keine öffentliche IP-Adresse auf Instanzebene für die Instanz)](#lb) | SNAT mit Portmaskierung (PAT) unter Verwendung der Front-Ends des Lastenausgleichs | TCP, UDP |In Azure wird die öffentliche Front-End-IP-Adresse des öffentlichen Lastenausgleichs mit mehreren privaten IP-Adressen gemeinsam genutzt. Für PAT verwendet Azure kurzlebige Ports der Front-Ends. |
| Keine oder Basic. | [3. Eigenständiger virtueller Computer (kein Azure Load Balancer, keine öffentliche IP-Adresse auf Instanzebene)](#defaultsnat) | SNAT mit Portmaskierung (PAT) | TCP, UDP | Azure weist SNAT automatisch eine öffentliche IP-Adresse zu, nutzt diese öffentliche IP-Adresse gemeinsam mit mehreren privaten IP-Adressen der Verfügbarkeitsgruppe und verwendet kurzlebige Ports dieser öffentlichen IP-Adresse. Dieses Szenario ist ein Fallback für die vorherigen Szenarien. Es ist nicht zu empfehlen, wenn Sie Sichtbarkeit und Kontrolle benötigen. |

Wenn ein virtueller Computer nicht mit Endpunkten außerhalb von Azure im öffentlichen IP-Adressraum kommunizieren soll, können Sie nach Bedarf zum Blockieren des Zugriffs Netzwerksicherheitsgruppen verwenden. Netzwerksicherheitsgruppen werden im Abschnitt [Verhindern der ausgehenden Konnektivität](#preventoutbound) ausführlicher beschrieben. Das Entwerfen, Implementieren und Verwalten eines virtuellen Netzwerks ohne ausgehenden Zugriff und die entsprechenden Anleitungen hierzu sind nicht Gegenstand dieses Artikels.

### <a name="ilpip"></a>Szenario 1: VM mit einer öffentlichen IP-Adresse auf Instanzebene

In diesem Szenario ist der VM eine öffentliche IP-Adresse auf Instanzebene (Instance Level Public IP, ILPIP) zugewiesen. Bei ausgehenden Verbindungen spielt es keine Rolle, ob für den virtuellen Computer ein Lastenausgleich durchgeführt wird. Dieses Szenario hat Vorrang vor den anderen Szenarien. Wenn eine öffentliche IP-Adresse auf Instanzebene (ILPIP) verwendet wird, nutzt die VM die ILPIP für alle ausgehenden Datenflüsse.  

Eine öffentliche IP-Adresse, die einem virtuellen Computer zugewiesen ist, ist eine 1:1-Beziehung (keine 1:n-Beziehung) und wird als zustandslose 1:1-NAT implementiert.  Es wird keine Portmaskierung (PAT) verwendet, und für den virtuellen Computer sind alle kurzlebigen Ports verfügbar.

Wenn Ihre Anwendung viele ausgehende Datenflüsse initiiert und es zu einer Überlastung der SNAT-Ports kommt, sollten Sie das [Zuweisen einer öffentlichen IP-Adresse auf Instanzebene erwägen, um SNAT-Engpässe zu entschärfen](#assignilpip). Lesen Sie den Abschnitt [Verwalten der SNAT-Auslastung](#snatexhaust) ganz durch.

### <a name="lb"></a>Szenario 2: Virtueller Computer mit Lastenausgleich ohne öffentliche IP-Adresse auf Instanzebene

Bei diesem Szenario ist die VM Teil eines Back-End-Pools für den öffentlichen Lastenausgleich. Der VM ist keine öffentliche IP-Adresse zugewiesen. Die Lastenausgleichsressource muss mit einer Lastenausgleichsregel zum Erstellen einer Verknüpfung der öffentlichen Front-End-IP mit dem Back-End-Pool konfiguriert werden.

Wenn Sie diese Regelkonfiguration nicht abschließen, ergibt sich das Verhalten wie oben unter [Eigenständige VM ohne öffentliche IP-Adresse auf Instanzebene](#defaultsnat) beschrieben. Die Regel muss nicht über einen funktionierenden Listener im Back-End-Pool verfügen, damit der Integritätstest erfolgreich durchgeführt werden kann.

Wenn die dem Lastenausgleich unterliegende VM einen ausgehenden Datenfluss erstellt, übersetzt Azure die private IP-Quelladresse des ausgehenden Datenflusses in die öffentliche IP-Adresse des öffentlichen Lastenausgleichs-Frond-End. Azure verwendet SNAT, um diese Funktion durchzuführen. Azure verwendet auch [PAT](#pat), um mehrere private IP-Adressen durch eine öffentliche IP-Adresse zu maskieren. 

Mit kurzlebigen Ports der öffentlichen Front-End-IP-Adresse des Lastenausgleich werden die einzelnen Datenflüsse unterschieden, die von dem virtuellen Computer stammen. Beim Erstellen ausgehender Datenflüsse werden für SNAT [vorab zugewiesene kurzlebige Ports](#preallocatedports) verwendet. In diesem Zusammenhang werden die kurzlebigen für SNAT verwendeten Ports als SNAT-Ports bezeichnet.

SNAT-Ports werden vorab zugeordnet, wie im Abschnitt [Grundlagen von SNAT und PAT](#snat) beschrieben. Es handelt sich um eine begrenzte Ressource, die überlastet werden kann. Es ist wichtig zu verstehen, wie sie [genutzt](#pat) wird. Um zu verstehen, wie Sie diese Nutzung beim Entwurf berücksichtigen und je nach Bedarf Abhilfemaßnahmen schaffen können, lesen Sie den Abschnitt [Verwalten der SNAT-Auslastung](#snatexhaust).

Wenn [Load Balancer Basic mehrere öffentliche IP-Adressen zugeordnet sind](load-balancer-multivip-overview.md), sind diese öffentlichen IP-Adressen Kandidaten für ausgehende Datenflüsse, und eine davon wird nach dem Zufallsprinzip ausgewählt.  

Zum Überwachen der Integrität ausgehender Verbindungen mit Load Balancer Basic können Sie [Azure Monitor-Protokolle für Azure Load Balancer](load-balancer-monitor-log.md) und [Warnungsereignisprotokolle](load-balancer-monitor-log.md#alert-event-log) verwenden.

### <a name="defaultsnat"></a>Szenario 3: Eigenständiger virtueller Computer ohne öffentliche IP-Adresse auf Instanzebene

In diesem Szenario gehört die VM nicht zu einem öffentlichen Load Balancer-Pool (und sie ist nicht Teil eines internen Standard Load Balancer-Pools), und ihr ist keine ILPIP-Adresse zugewiesen. Wenn der virtuelle Computer einen ausgehenden Datenfluss einleitet, übersetzt Azure die private IP-Quelladresse für den ausgehenden Datenfluss in eine öffentliche IP-Quelladresse. Die für diesen ausgehenden Datenfluss verwendete öffentliche IP-Adresse ist nicht konfigurierbar und wird nicht auf die Ressourcengrenze des Abonnements für öffentliche IP-Adressen angerechnet. Diese öffentliche IP-Adresse gehört Ihnen nicht und kann nicht reserviert werden. Bei der erneuten Bereitstellung der VM, Verfügbarkeitsgruppe oder VM-Skalierungsgruppe wird diese öffentliche IP-Adresse freigegeben und eine neue öffentliche IP-Adresse angefordert. Verwenden Sie dieses Szenario nicht für die Aufnahme von IP-Adressen in die Whitelist. Verwenden Sie stattdessen eins der anderen zwei Szenarien, in denen Sie das ausgehende Szenario und die für ausgehende Verbindungen zu verwendende IP-Adresse explizit deklarieren.

>[!IMPORTANT] 
>Dieses Szenario gilt auch, wenn __nur__ ein interner Basic Load Balancer verknüpft ist. Szenario 3 ist __nicht verfügbar__, wenn ein interner Standard Load Balancer mit einer VM verknüpft ist.  Sie müssen [Szenario 1](#ilpip) oder [Szenario 2](#lb) zusätzlich zu einem internen Standard Load Balancer explizit erstellen.

Azure verwendet SNAT mit Portmaskierung ([PAT](#pat)) für diese Aufgabe. Dieses Szenario ähnelt [Szenario 2](#lb). Der Unterschied besteht darin, dass keine Steuerung der IP-Adresse verwendet wird. Dies ist ein Fallbackszenario für den Fall, dass Szenario 1 und Szenario 2 nicht vorhanden sind. Dieses Szenario wird nicht empfohlen, wenn eine Kontrolle über die ausgehende Adresse gewünscht ist. Wenn ausgehende Verbindungen ein wichtiger Teil Ihrer Anwendung sind, sollten Sie ein anderes Szenario auswählen.

SNAT-Ports werden vorab zugeordnet, wie im Abschnitt [Grundlagen von SNAT und PAT](#snat) beschrieben.  Die Anzahl der VMs, die sich eine Verfügbarkeitsgruppe teilen, bestimmt, welche Stufe der Vorabzuordnung gilt.  Eine eigenständige VM ohne Verfügbarkeitsgruppe ist effektiv ein Pool von 1 für die Ermittlung der Vorabzuordnung (1024 SNAT-Ports). SNAT Ports sind begrenzte Ressourcen, die sich erschöpfen können. Es ist wichtig zu verstehen, wie sie [genutzt](#pat) wird. Um zu verstehen, wie Sie diese Nutzung beim Entwurf berücksichtigen und je nach Bedarf Abhilfemaßnahmen schaffen können, lesen Sie den Abschnitt [Verwalten der SNAT-Auslastung](#snatexhaust).

### <a name="combinations"></a>Mehrere kombinierte Szenarien

Sie können die in den vorherigen Abschnitten beschriebenen Szenarien kombinieren, um ein bestimmtes Ergebnis zu erzielen. Wenn mehrere Szenarien vorhanden sind, gilt eine Prioritätsreihenfolge: [Szenario 1](#ilpip) hat Vorrang vor [Szenario 2](#lb) und [3](#defaultsnat). [Szenario 2](#lb) überschreibt [Szenario 3](#defaultsnat).

Ein Beispiel hierfür ist eine Azure Resource Manager-Bereitstellung, bei der die Anwendung stark von ausgehenden Verbindungen mit einer begrenzten Anzahl von Zielen abhängig ist, aber auch eingehende Datenflüsse über ein Front-End des Lastenausgleichs empfängt. In diesem Fall können Sie als Lösung die Szenarien 1 und 2 kombinieren. Informationen zu zusätzlichen Mustern finden Sie unter [Verwalten der SNAT-Auslastung](#snatexhaust).

### <a name="multife"></a>Mehrere Front-Ends für ausgehende Datenflüsse

#### <a name="standard-load-balancer"></a>Load Balancer Standard

Load Balancer Standard verwendet alle Kandidaten für ausgehende Datenflüsse, wenn gleichzeitig [mehrere (öffentliche) IP-Front-Ends](load-balancer-multivip-overview.md) vorhanden sind. Jedes Front-End multipliziert die Anzahl von verfügbaren, vorab zugeordneten SNAT-Ports, wenn eine Lastenausgleichsregel für ausgehende Verbindungen aktiviert ist.

Sie können die Verwendung einer Front-End-IP-Adresse für ausgehende Verbindungen mit einer neuen Option für Lastausgleichsregeln unterdrücken:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Standardmäßig ist die Option `disableOutboundSnat` auf _false_ festgelegt. Dies bedeutet, dass diese Regel den ausgehenden SNAT für die zugehörigen VMs im Back-End-Pool der Lastenausgleichsregel programmiert. `disableOutboundSnat` kann auf _true_ festgelegt werden, um zu verhindern, dass für den Lastenausgleich die zugehörige Front-End-IP-Adresse für ausgehende Verbindungen der VMs im Back-End-Pool dieser Lastenausgleichsregel verwendet wird.  Zudem können Sie noch immer für ausgehende Datenflüsse eine bestimmte IP-Adresse festlegen, wie unter [Mehrere kombinierte Szenarien](#combinations) beschrieben.

#### <a name="load-balancer-basic"></a>Load Balancer Basic

Load Balancer Basic wählt ein einzelnes Front-End für ausgehende Datenflüsse aus, wenn [mehrere (öffentliche) Front-End-IP-Adressen](load-balancer-multivip-overview.md) Kandidaten für ausgehende Datenflüsse sind. Diese Auswahl ist nicht konfigurierbar; Sie sollten den Auswahlalgorithmus als zufällig betrachten. Sie können für ausgehende Datenflüsse eine bestimmte IP-Adresse festlegen, wie unter [Mehrere kombinierte Szenarien](#combinations) beschrieben.

### <a name="az"></a>Verfügbarkeitszonen

Bei Verwendung von [Standard Load Balancer mit Verfügbarkeitszonen](load-balancer-standard-availability-zones.md) können zonenredundante ausgehende SNAT-Verbindungen von zonenredundanten Front-Ends bereitgestellt werden. Die SNAT-Programmierung ist nach Zonenausfällen noch immer vorhanden.  Wenn zonenbezogene Front-Ends verwendet werden, geschieht mit ausgehenden SNAT-Verbindungen dasselbe wie mit der Zone, zu der sie gehören.

## <a name="snat"></a>Grundlagen von SNAT und PAT

### <a name="pat"></a>Portmaskierung mit SNAT (PAT)

Wenn eine öffentliche Load Balancer-Ressource mit VM-Instanzen verknüpft ist, wird jede ausgehende Verbindungsquelle erneut geschrieben. Die Quelle wird aus dem privaten IP-Adressraum des virtuellen Netzwerks in die öffentliche Front-End-IP-Adresse des Lastenausgleichs umgeschrieben. Im öffentlichen IP-Adressraum müssen die fünf Tupel des Datenflusses (IP-Quelladresse, Quellport, IP-Transportprotokoll, IP-Zieladresse, Zielport) eindeutig sein.  Portmaskierung mit SNAT kann mit entweder mit dem TCP- oder UDP IP-Protokoll verwendet werden.

Hierfür werden kurzlebige Ports (SNAT-Ports) verwendet, nachdem die private IP-Quelladresse umgeschrieben wurde, da mehrere Datenflüsse von einer einzelnen öffentlichen IP-Adresse stammen. Die Portmaskierung mit SNAT-Algorithmus weist SNAT-Ports für UDP und TCP unterschiedlich zu.

#### <a name="tcp"></a>TCP-SNAT-Ports

Pro Datenfluss zu einer einzelnen IP-Zieladresse/einem einzelnen Port wird ein SNAT-Port genutzt. Bei mehreren TCP-Datenflüssen zur gleichen IP-Zieladresse bzw. zum Port und Protokoll belegt jeder TCP-Datenfluss einen einzelnen SNAT-Port. Hierdurch wird sichergestellt, dass die Datenflüsse eindeutig sind, wenn sie von der gleichen öffentlichen IP-Adresse stammen und die gleiche IP-Zieladresse, den gleichen Port und das gleiche Protokoll aufweisen. 

Mehrere Datenflüsse mit jeweils anderen Angaben für IP-Zieladresse, Port und Protokoll verwenden gemeinsam einen einzelnen SNAT-Port. Die IP-Adresse, der Port und das Protokoll für das Ziel machen den Datenfluss eindeutig, ohne dass zusätzliche Quellports verwendet werden müssen, um Datenflüsse im öffentlichen IP-Adressraum zu unterscheiden.

#### <a name="udp"></a> UDP-SNAT-Ports

UDP-SNAT-Ports werden von einem anderen Algorithmus als TCP-SNAT-Ports verwaltet.  Load Balancer verwendet für UDP einen Algorithmus, der als „portbeschränktes Cone-NAT“ bezeichnet wird.  Für jeden Datenfluss wird unabhängig von der IP-Zieladresse/dem Zielport ein SNAT-Port genutzt.

#### <a name="exhaustion"></a>Auslastung

Sobald die SNAT-Portressourcen erschöpft sind, sind ausgehende Datenflüsse erst wieder möglich, wenn SNAT-Ports von vorhandenen Datenflüssen freigegeben werden. Der Load Balancer gibt die SNAT-Ports wieder frei, wenn der Datenflussvorgang abgeschlossen ist. Es wird ein [Leerlauftimeout von 4 Minuten](#idletimeout) verwendet, um SNAT-Ports für im Leerlauf befindliche Datenflüsse wieder freizugeben.

UDP-SNAT-Ports erschöpfen aufgrund des Unterschieds im verwendeten Algorithmus in der Regel viel schneller als TCP-SNAT-Ports. Diesen Unterschied müssen Sie beim Entwerfen und Skalieren berücksichtigen.

Im Abschnitt [Verwalten von SNAT](#snatexhaust) werden Muster für das Entschärfen von Bedingungen beschrieben, die häufig zu einer Überlastung von SNAT-Ports führen.

### <a name="preallocatedports"></a>Vorabzuordnung von kurzlebigen Ports für die Portmaskierung per SNAT (PAT)

In Azure wird ein Algorithmus verwendet, um basierend auf der Größe des Back-End-Pools bei der Portmaskierung per SNAT ([PAT](#pat)) die Anzahl von verfügbaren vorab zugeordneten SNAT-Ports zu ermitteln. SNAT-Ports sind kurzlebige Ports, die für eine bestimmte öffentliche IP-Quelladresse verfügbar sind.

Dieselbe Anzahl von SNAT-Ports wird vorab für UDP bzw. TCP zugeordnet und unabhängig voneinander pro IP-Transportprotokoll genutzt.  Die Verwendung des SNAT-Ports unterscheidet sich jedoch abhängig davon, ob es sich um einen UDP- oder TCP-Datenfluss handelt.

>[!IMPORTANT]
>Standard-SKU-SNAT Programmierung erfolgt pro IP-Transportprotokoll und wird aus der Lastenausgleichsregel abgeleitet.  Ist nur eine TCP-Lastenausgleichsregel vorhanden, ist SNAT nur für TCP verfügbar. Wenn Sie nur eine TCP-Lastenausgleichsregel haben und ausgehendes SNAT für UDP benötigen, erstellen Sie eine UDP-Lastenausgleichsregel vom selben Front-End- zum selben Back-End-Pool.  Dadurch wird SNAT-Programmierung für UDP ausgelöst.  Eine Arbeitsregel oder ein Integritätstest ist nicht erforderlich.  Basic-SKU-SNAT bewirkt immer, dass SNAT für beide IP-Transportprotokolle programmiert wird, unabhängig von dem Transportprotokoll, das in der Lastenausgleichsregel angegeben ist.

In Azure werden SNAT-Ports vorab der IP-Konfiguration der NIC jeder VM zugeordnet. Wenn dem Pool eine IP-Konfiguration hinzugefügt wird, werden die SNAT-Ports für diese IP-Konfiguration basierend auf der Größe des Back-End-Pools vorab zugeordnet. Bei Erstellung von ausgehenden Datenflüssen werden diese Ports von [PAT](#pat) dynamisch genutzt (bis zum vorab festgelegten Grenzwert) und wieder freigegeben, wenn der Datenfluss geschlossen wird oder ein [Leerlauftimeout](#idletimeout) eintritt.

In der folgenden Tabelle sind die SNAT-Port-Vorabzuordnungen für die Ebenen der Back-End-Poolgrößen angegeben:

| Poolgröße (VM-Instanzen) | Vorab zugeordnete SNAT-Ports pro IP-Konfiguration|
| --- | --- |
| 1-50 | 1024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1.000 | 32 |

>[!NOTE]
> Bei Verwendung von Load Balancer Standard mit [mehreren Front-Ends](load-balancer-multivip-overview.md) multipliziert jede Front-End-IP-Adresse die Anzahl der verfügbaren SNAT-Ports in der vorherigen Tabelle. Beispielsweise verwendet ein Back-End-Pool von 50 VMs mit zwei Lastenausgleichsregeln, jede mit einer eigenen Front-End-IP-Adresse, 2048 (2 x 1024) SNAT-Ports pro IP-Konfiguration. Weitere Informationen finden Sie in den Details zu [mehreren Front-Ends](#multife).

Denken Sie daran, dass die Anzahl der verfügbaren SNAT-Ports nicht direkt in die Anzahl der Datenflüsse zu übersetzen ist. Ein einzelner SNAT-Port kann für mehrere eindeutige Ziele wiederverwendet werden. Ports werden nur genutzt, wenn Datenflüsse eindeutig gemacht werden müssen. Eine Anleitung für den Entwurf und Lösungsmöglichkeiten finden Sie in den Abschnitten, in den die [Verwaltung dieser begrenzten Ressource](#snatexhaust) und [PAT](#pat) beschrieben sind.

Von einer Größenänderung Ihres Back-End-Pools können einige der eingerichteten Datenflüsse betroffen sein. Wenn die Größe des Back-End-Pools zunimmt und der Übergang auf die nächste Ebene erfolgt, wird die Hälfte der vorab zugeordneten SNAT-Ports während des Übergangs zur nächstgrößeren Back-End-Poolebene freigegeben. Bei Datenflüssen, die einem wieder freigegebenen SNAT-Port zugeordnet sind, tritt ein Timeout auf, und sie müssen neu eingerichtet werden. Versuche zum Einrichten eines neuen Datenflusses sind sofort erfolgreich, solange vorab zugeordnete Ports verfügbar sind.

Wenn sich die Größe des Back-End-Pools reduziert und der Übergang auf eine niedrigere Ebene erfolgt, nimmt die Anzahl der verfügbaren SNAT-Ports zu. In diesem Fall sind vorhandene zugeordnete SNAT-Ports und die entsprechenden Datenflüsse nicht betroffen.

SNAT-Portzuordnungen gelten speziell für das jeweilige IP-Transportprotokoll (TCP und UDP werden separat verwaltet) und werden unter den folgenden Bedingungen freigegeben:

### <a name="tcp-snat-port-release"></a>TCP-SNAT-Portfreigabe

- Wenn entweder der Server oder der Client FINACK sendet, wird der SNAT-Port nach 240 Sekunden freigegeben.
- Tritt ein RST auf, wird der SNAT-Port nach 15 Sekunden freigegeben.
- Ist das Leerlauftimeout erreicht, wird der Port freigegeben.

### <a name="udp-snat-port-release"></a>UDP-SNAT-Portfreigabe

- Ist das Leerlauftimeout erreicht, wird der Port freigegeben.

## <a name="problemsolving"></a> Problembehebung 

Dieser Abschnitt enthält Informationen dazu, wie Sie das Problem einer SNAT-Überlastung, die bei ausgehenden Verbindungen in Azure auftreten kann, lösen können.

### <a name="snatexhaust"></a> Verwalten der SNAT-Portauslastung (PAT)
Für [PAT](#pat) verwendete [kurzlebige Ports](#preallocatedports) sind eine begrenzte Ressource, wie unter [Eigenständiger virtueller Computer ohne öffentliche IP-Adresse auf Instanzebene](#defaultsnat) und [Virtueller Computer mit Lastenausgleich ohne öffentliche IP-Adresse auf Instanzebene](#lb) beschrieben.

Wenn Sie wissen, dass Sie viele ausgehende TCP- oder UDP-Verbindungen zu derselben IP-Zieladresse und demselben Port initiieren und Fehler bei ausgehenden Verbindungen feststellen oder vom Support darauf hingewiesen werden, dass Sie zu viele SNAT-Ports (vorab zugeordnete [kurzlebige Ports](#preallocatedports), die für [PAT](#pat) verwendet werden) in Anspruch nehmen, stehen Ihnen mehrere Lösungsmöglichkeiten zur Verfügung. Überprüfen Sie diese Optionen, und entscheiden Sie, welche für Ihr Szenario verfügbar und am besten geeignet sind. Möglicherweise kann die ein oder andere die Verwaltung dieses Szenarios erleichtern.

Wenn Sie Probleme haben, das Verhalten der ausgehenden Verbindungen zu verstehen, können Sie die IP-Stapelstatistiken (netstat) verwenden. Es kann aber auch hilfreich sein, das Verbindungsverhalten mithilfe von Paketerfassungen zu beobachten. Sie können diese Paketerfassungen im Gastbetriebssystem Ihrer Instanz durchführen oder [Network Watcher für die Paketerfassung](../network-watcher/network-watcher-packet-capture-manage-portal.md) verwenden.

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
Durch das Zuweisen einer öffentlichen IP-Adresse auf Instanzebene erhalten Sie das Szenario [Virtueller Computer mit öffentlicher IP-Adresse auf Instanzebene](#ilpip). Alle kurzlebigen Ports für die öffentliche IP-Adresse, die für jeden virtuellen Computer verwendet werden, sind für den virtuellen Computer verfügbar. (Das steht im Gegensatz zu Szenarien, bei denen kurzlebige Ports für eine öffentliche IP-Adresse von allen virtuellen Computern, die dem entsprechenden Back-End-Pool zugeordnet sind, gemeinsam genutzt werden.) Es müssen auch Nachteile berücksichtigt werden, wie beispielsweise zusätzliche Kosten für öffentliche IP-Adressen und mögliche Auswirkungen des Whitelistings für eine große Anzahl einzelner IP-Adressen.

>[!NOTE] 
>Diese Option ist für Webworkerrollen nicht verfügbar.

#### <a name="multifesnat"></a>Verwenden mehrerer Front-Ends

Bei Verwendung des öffentlichen Standard Load Balancers vergeben Sie [mehrere Front-End-IP-Adressen für ausgehende Verbindungen](#multife) und [multiplizieren die Anzahl der verfügbaren SNAT-Ports](#preallocatedports).  Erstellen Sie eine Front-End-IP-Konfiguration, eine Regel und einen Back-End-Pool, um die Programmierung von SNAT auf die öffentliche IP-Adresse des Front-Ends auszulösen.  Die Regel muss nicht funktionieren, und ein Integritätstest muss nicht erfolgreich sein.  Wenn Sie auch für eingehende (und nicht nur für ausgehende) Datenflüsse mehrere Front-Ends verwenden, sollten Sie benutzerdefinierte Integritätstests verwenden, um die Zuverlässigkeit sicherzustellen.

>[!NOTE]
>In den meisten Fällen ist die Überlastung der SNAT-Ports ein Zeichen für ein schlechtes Design.  Stellen Sie sicher, dass die Gründe für die Portüberlastung kennen, bevor Sie weitere Front-Ends verwenden, um SNAT-Ports hinzuzufügen.  Möglicherweise verdecken Sie ein Problem, das später zu Fehlern führen kann.

#### <a name="scaleout"></a>Horizontales Skalieren

[Vorab zugeordnete Ports](#preallocatedports) werden anhand der Größe des Back-End-Pools zugewiesen und in Ebenen gruppiert, um Unterbrechungen zu minimieren, wenn einige der Ports neu zugewiesen werden müssen, um die nächstgrößere Back-End-Pool-Größenebene abzudecken.  Sie können möglicherweise die Intensität der SNAT-Portnutzung für ein bestimmtes Front-End erhöhen, indem Sie Ihren Back-End-Pool entsprechend der maximalen Größe einer bestimmten Ebene skalieren.  Dies erfordert, dass die Anwendung effizient horizontal skaliert wird.

Beispielsweise sind für zwei virtuelle Computer im Back-End-Pool 1.024 SNAT Ports pro IP-Konfiguration verfügbar, sodass insgesamt 2.048 SNAT-Ports für die Bereitstellung zulässig sind.  Soll die Bereitstellung auf 50 virtuelle Computer vergrößert werden, können, obwohl die Anzahl der vorab zugeordneten Ports pro virtuellem Computer konstant bleibt, insgesamt 51.200 (50 x 1024) SNAT-Ports durch die Bereitstellung genutzt werden.  Wenn Sie Ihre Bereitstellung horizontal skalieren möchten, überprüfen Sie die Anzahl von [vorab zugeordneten Ports](#preallocatedports) pro Ebene, um sicherzustellen, dass Sie die horizontale Skalierung entsprechend dem Maximum der jeweiligen Ebene festlegen.  Hätten Sie sich im vorherigen Beispiel zu einer horizontalen Skalierung auf 51 statt 50 Instanzen entschieden, würden Sie zur nächsten Ebene gelangen und somit weniger SNAT-Ports pro VM sowie weniger SNAT-Ports insgesamt erhalten.

Wenn Sie horizontal auf die nächstgrößere Back-End-Poolgröße hochskalieren, erfolgt möglicherweise für einige Ihrer ausgehenden Verbindungen ein Timeout, wenn zugeordnete Ports neu zugeordnet werden müssen.  Wenn Sie nur einige Ihrer SNAT-Ports verwenden, hat das Hochskalieren über die nächstgrößere Back-End-Poolgröße hinaus keine Auswirkungen.  Die Hälfte der vorhandenen Ports wird bei jedem Wechsel zur nächsten Back-End-Poolebene neu zugeordnet.  Soll dies nicht stattfinden, müssen Sie Ihre Bereitstellung an die Ebenengröße anpassen.  Oder Sie stellen sicher, dass Ihre Anwendung geeignet erkennen und wiederholen kann.  TCP-Keepalives können bei der Erkennung unterstützen, wenn SNAT-Ports nicht mehr funktionieren, weil sie neu zugeordnet werden.

### <a name="idletimeout"></a>Verwenden von Keepalives zum Zurücksetzen des Leerlauftimeouts für ausgehende Verbindungen

Ausgehende Verbindungen haben einen 4-Minuten-Leerlauftimeout. Dieses Timeout ist nicht anpassbar. Sie können jedoch Keepalives auf der Transportschicht (z. B. TCP-Keepalives) oder der Anwendungsschicht verwenden, um einen im Leerlauf befindlichen Datenfluss zu aktualisieren und den Leerlauftimeout bei Bedarf zurückzusetzen.  

Wenn TCP-Keepalives verwendet werden, genügt es, sie auf einer Seite der Verbindung zu aktivieren. Beispielsweise genügt es, sie nur auf der Serverseite zu aktivieren, um den Leerlauftimer des Datenflusses zurückzusetzen, und es ist nicht erforderlich, TCP-Keepalives auf beiden Seiten auszulösen.  Ähnliche Konzepte gibt es für die Anwendungsschicht, einschließlich Client/Server-Konfigurationen für Datenbanken.  Überprüfen Sie auf der Serverseite, welche Optionen es für anwendungsspezifische Keepalives gibt.

## <a name="discoveroutbound"></a>Ermitteln der von einem virtuellen Computer verwendeten öffentlichen IP-Adresse
Es gibt viele Möglichkeiten, die öffentliche IP-Quelladresse einer ausgehenden Verbindung zu bestimmen. OpenDNS bietet einen Dienst, der die öffentliche IP-Adresse Ihrer VM anzeigen kann. 

Mit dem Befehl „nslookup“ können Sie eine DNS-Abfrage für den Namen „myip.opendns.com“ an den OpenDNS-Resolver senden. Der Dienst gibt die IP-Quelladresse zurück, die zum Senden der Abfrage verwendet wurde. Wenn Sie die folgende Abfrage auf Ihrem virtuellen Computer ausführen, wird die öffentliche IP-Adresse zurückgegeben, die für diesen virtuellen Computer verwendet wird:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Verhindern der Konnektivität in ausgehender Richtung
Manchmal ist es nicht wünschenswert, einem virtuellen Computer das Erstellen eines ausgehenden Datenflusses zu erlauben. Möglicherweise müssen Sie auch verwalten, welche Ziele mit ausgehenden Datenflüssen erreicht werden bzw. welche Ziele eingehende Datenflüsse beginnen können. In diesem Fall können Sie [Netzwerksicherheitsgruppen](../virtual-network/security-overview.md) zum Verwalten der vom virtuellen Computer erreichbaren Ziele verwenden. Mit Netzwerksicherheitsgruppen können Sie auch verwalten, welches öffentliche Ziel eingehende Datenflüsse initiieren kann.

Wenn Sie eine Netzwerksicherheitsgruppe einem virtuellen Computer mit Lastenausgleich zuordnen, müssen Sie auf die [Diensttags](../virtual-network/security-overview.md#service-tags) und [Standardsicherheitsregeln](../virtual-network/security-overview.md#default-security-rules) achten. Sie müssen sicherstellen, dass die VM Anforderungen von Integritätstests von Azure Load Balancer empfangen kann. 

Wenn eine Netzwerksicherheitsgruppe Anforderungen von Integritätstests vom Standardtag AZURE_LOADBALANCER blockiert, misslingt Ihr VM-Integritätstests, weshalb die VM mit „Außer Betrieb“ markiert wird. Der Lastenausgleich beendet das Senden neuer Datenflüsse an diese VM.

## <a name="limitations"></a>Einschränkungen
- „DisableOutboundSnat“ ist bei der Konfiguration einer Lastausgleichsregel im Portal nicht als Option verfügbar.  Verwenden Sie stattdessen REST, eine Vorlage oder Clienttools.
- Auf Web-Workerrollen ohne VNet und andere Plattformdienste von Microsoft kann aufgrund eines Nebeneffekts der Funktionsweise von Diensten vor VNet und anderen Plattformdiensten nur zugegriffen werden, wenn interner Standard-Load Balancer verwendet wird. Verlassen Sie sich nicht auf diesen Nebeneffekt, da der jeweilige Dienst oder die zugrunde liegende Plattform ohne vorherige Ankündigung geändert werden kann. Sie müssen immer davon ausgehen, dass Sie ausgehende Verbindungen, falls gewünscht, explizit erstellen müssen, wenn Sie nur einen internen Load Balancer im Tarif „Standard“ verwenden. Das in diesem Artikel beschriebene Szenario 3 für [Standard-SNAT](#defaultsnat) ist nicht verfügbar.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Load Balancer Standard](load-balancer-standard-overview.md).
- Weitere Informationen zu [Ausgangsregeln](load-balancer-outbound-rules-overview.md) für Standard Public Load Balancer.
- Weitere Informationen zu [Load Balancer](load-balancer-overview.md).
- Weitere Informationen zu [Netzwerksicherheitsgruppen](../virtual-network/security-overview.md).
- Erfahren Sie mehr über die anderen zentralen [Netzwerkfunktionen](../networking/networking-overview.md) in Azure.
