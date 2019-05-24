---
title: Was ist Azure Load Balancer Standard?
titlesuffix: Azure Load Balancer
description: 'Übersicht: Features von Azure Standard Load Balancer'
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/28/2019
ms.author: kumud
ms.openlocfilehash: 266630cb7c9601af69073a6c9beb7d7ada9b8034
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957471"
---
# <a name="azure-standard-load-balancer-overview"></a>Übersicht: Azure Load Balancer Standard

Mit Azure Load Balancer können Sie Ihre Anwendungen skalieren und Ihre Dienste mit Hochverfügbarkeit bereitstellen. Load Balancer kann sowohl für Szenarien mit eingehenden als auch ausgehenden Verbindungen verwendet werden, bietet niedrige Latenzen und einen hohen Durchsatz und unterstützt Millionen Datenflüsse für alle TCP- und UDP-Anwendungen. 

In diesem Artikel geht es hauptsächlich um Standard Load Balancer.  Eine allgemeinere Übersicht über Azure Load Balancer finden Sie unter [Übersicht über Azure Load Balancer](load-balancer-overview.md).

## <a name="what-is-standard-load-balancer"></a>Was ist Standard Load Balancer?

Standard Load Balancer ist ein neues Load Balancer-Produkt für alle TCP- und UDP-Anwendungen und hat im Vergleich zu Basic Load Balancer einen erweiterten und detaillierteren Featuresatz.  Zwar gibt es viele Ähnlichkeiten, es ist aber unverzichtbar, dass Sie sich mit den Unterschieden vertraut machen, die in diesem Artikel skizziert sind.

Sie können Standard Load Balancer als öffentlichen oder internen Load Balancer verwenden. Ein virtueller Computer kann mit einer öffentlichen und einer internen Load Balancer-Ressource verbunden werden.

Die Funktionen einer Load Balancer-Ressource werden immer als Front-End-, Regel-, Integritätstest- und Back-End-Pool-Definition ausgedrückt.  Eine Ressource kann mehrere Regeln enthalten. Sie können virtuelle Computer im Back-End-Pool platzieren, indem Sie den Back-End-Pool aus der NIC-Ressource des virtuellen Computers angeben.  Bei Verwendung einer VM-Skalierungsgruppe wird dieser Parameter über das Netzwerkprofil übergeben und erweitert.

Ein Hauptaspekt ist der Bereich des virtuellen Netzwerks für die Ressource.  Während ein Basic Load Balancer im Bereich einer Verfügbarkeitsgruppe vorhanden ist, ist ein Standard Load Balancer vollständig in den Bereich eines virtuellen Netzwerks integriert, und es gelten alle Konzepte für ein virtuelles Netzwerk.

Load Balancer-Ressourcen sind Objekte, in denen Sie formulieren können, wie Azure seine mehrinstanzenfähige Infrastruktur programmieren sollte, um das Szenario zu erzielen, das Sie erstellen möchten.  Es gibt keine direkte Beziehung zwischen Load Balancer-Ressourcen und tatsächlicher Infrastruktur. Durch Erstellen eines Load Balancers wird keine Instanz erstellt, Kapazität ist immer verfügbar, und es sind weder Start- noch Skalierungsverzögerungen zu berücksichtigen. 

>[!NOTE]
> Azure stellt eine Sammlung aus vollständig verwalteten Lastenausgleichslösungen für Ihre Szenarien bereit.  Wenn Sie nach Informationen zu TLS-Terminierung („SSL-Auslagerung“) oder Verarbeitung pro HTTP/HTTPS-Anforderungsanwendungsschicht suchen, lesen Sie [Application Gateway](../application-gateway/application-gateway-introduction.md).  Wenn Sie nach Informationen zu globalem DNS-Lastenausgleich suchen, lesen Sie [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Für Ihre End-to-End-Szenarien kann es vorteilhaft sein, diese Lösungen nach Bedarf zu kombinieren.

## <a name="why-use-standard-load-balancer"></a>Gründe zum Verwenden von Standard Load Balancer

Standard Load Balancer ermöglicht Ihnen das Skalieren Ihrer Anwendungen sowie die Bereitstellung von Hochverfügbarkeit für kleine Bereitstellungen bis hin zu großen komplexen Architekturen mit mehreren Zonen.

In der folgenden Tabelle finden Sie einen Überblick über die Unterschiede zwischen Standard Load Balancer und Basic Load Balancer:

>[!NOTE]
> Für neue Entwürfe sollte Load Balancer Standard verwendet werden. 

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Lesen Sie [Dienstgrenzwerte für Load Balancer](https://aka.ms/lblimits) sowie [Preise](https://aka.ms/lbpricing) und [SLA](https://aka.ms/lbsla).


### <a name="backend"></a>Back-End-Pool

Ein Load Balancer Standard-Back-End-Pool wird um beliebige Ressourcen eines virtuellen Computers in einem virtuellen Netzwerk erweitert.  Er kann bis zu 1000 Back-End-Instanzen enthalten.  Eine Back-End-Instanz ist eine IP-Konfiguration, die eine Eigenschaft einer NIC-Ressource ist.

Der Back-End-Pool kann eigenständige virtuelle Computer, Verfügbarkeitsgruppen oder VM-Skalierungsgruppen enthalten.  Ressourcen können im Back-End-Pool auch gemischt werden. Pro Load Balancer-Ressource können Sie bis zu 150 Ressourcen im Back-End-Pool kombinieren.

Wenn Sie überlegen, wie Sie Ihren Back-End-Pool gestalten, können Sie die Anzahl der einzelnen Back-End-Pool-Ressourcen so gering wie möglich halten, um die Dauer von Verwaltungsvorgängen weiter zu optimieren.  Es gibt keinen Unterschied in der Datenebenenleistung oder -skalierung.

### <a name="probes"></a>Integritätstests
  
Load Balancer Standard fügt Unterstützung für [HTTPS-Integritätstests](load-balancer-custom-probe-overview.md#httpprobe) (HTTP-Test mit TLS-Wrapper (Transport Layer Security)) hinzu, um eine genaue Überwachung der HTTPS-Anwendungen zu ermöglichen.  

Wenn [alle Tests des gesamten Back-End-Pools zu Fehlern führen](load-balancer-custom-probe-overview.md#probedown), sorgt Load Balancer Standard darüber hinaus dafür, dass alle bestehenden TCP-Verbindungen erhalten bleiben. (Load Balancer im Tarif „Basic“ hingegen beendet alle TCP-Verbindungen für alle Instanzen.)

Ausführliche Informationen finden Sie unter [Lastenausgleichs-Integritätstests](load-balancer-custom-probe-overview.md).

### <a name="az"></a>Verfügbarkeitszonen

>[!IMPORTANT]
>Informationen zu verwandten Themen finden Sie unter [Verfügbarkeitszonen](../availability-zones/az-overview.md), einschließlich aller regionsspezifischen Informationen.

Standard Load Balancer unterstützt zusätzliche Funktionen in Regionen, in denen Verfügbarkeitszonen verfügbar sind.  Diese Funktionen ergänzen alle Funktionen, die Standard Load Balancer bereitstellt.  Verfügbarkeitszonenkonfigurationen sind für einen öffentlichen und internen Standard Load Balancer verfügbar.

Nicht zonale Front-Ends werden standardmäßig zonenredundant, wenn sie in einer Region mit Verfügbarkeitszonen bereitgestellt werden.   Ein zonenredundantes Front-End überdauert Zonenfehler und wird von dedizierter Infrastruktur in allen Zonen gleichzeitig versorgt. 

Darüber hinaus können Sie ein Front-End für eine bestimmte Zone garantieren. Ein zonales Front-End ist direkt an die jeweilige Zone gekoppelt und wird nur durch dedizierte Infrastruktur in einer einzelnen Zone versorgt.

Für den Back-End-Pool steht zonenübergreifender Lastenausgleich zur Verfügung, und jede Ressource eines virtuellen Computers in einem virtuellen Netzwerk kann Bestandteil eines Back-End-Pools sein.

Lesen Sie die [ausführliche Erläuterung der Funktionalität im Zusammenhang mit Verfügbarkeitszonen](load-balancer-standard-availability-zones.md).

### <a name="diagnostics"></a>Diagnose

Standard Load Balancer stellt mehrdimensionale Metriken über Azure Monitor bereit.  Diese Metriken können für eine bestimmte Dimension gefiltert, gruppiert und unterteilt werden.  Sie liefern aktuelle und vergangenheitsbezogene Einblicke in die Leistung und Integrität Ihres Diensts.  Resource Health (Ressourcenintegrität) wird ebenfalls unterstützt.  Es folgt eine kurze Übersicht über unterstützte Diagnosen:

| Metrik | BESCHREIBUNG |
| --- | --- |
| VIP-Verfügbarkeit | Load Balancer Standard wendet kontinuierlich den Datenpfad aus einer Region auf das Load Balancer-Front-End bis hin zum SDN-Stapel an, der Ihren virtuellen Computer unterstützt. Solange integre Instanzen verbleiben, folgt die Messung demselben Pfad wie der Datenverkehr mit Lastenausgleich Ihrer Anwendungen. Der Datenpfad, der von Ihren Kunden verwendet wird, wird ebenfalls überprüft. Die Messung ist für Ihre Anwendung nicht sichtbar und bewirkt keine Beeinträchtigung bei anderen Vorgängen.|
| DIP-Verfügbarkeit | Standard Load Balancer verwendet einen verteilten Integritätsprüfungsdienst, der die Integrität Ihres Anwendungsendpunkts gemäß Ihren Konfigurationseinstellungen überwacht. Diese Metrik stellt eine Aggregat- oder nach Endpunkt gefilterte Ansicht jedes einzelnen Instanzendpunkts im Load Balancer-Pool bereit.  Sie können sehen, wie Load Balancer die Integrität Ihrer Anwendung gemäß Ihrer Integritätsprüfungskonfiguration beurteilt.
| SYN-Pakete | Standard Load Balancer beendet keine TCP-Verbindungen und interagiert nicht mit TCP- oder UDP-Paketdatenflüssen. Datenflüsse und deren Handshakes erfolgen immer zwischen der Quelle und der VM-Instanz. Für eine bessere Problembehandlung Ihrer TCP-Protokollszenarien können Sie SYN-Paketzähler verwenden, um zu verstehen, wie viele TCP-Verbindungsversuche vorgenommen werden. Die Metrik gibt die Anzahl der TCP-SYN-Pakete an, die empfangen wurden.|
| SNAT-Verbindungen | Load Balancer Standard meldet die Anzahl von maskierten ausgehenden Datenflüssen an das Front-End mit der öffentlichen IP-Adresse. SNAT Ports stellen eine erschöpfbare Ressource dar. Diese Metrik kann einen Hinweis darauf geben, in welchem Umfang Ihre Anwendung SNAT für ausgehende Flows nutzt.  Die Zählerstände für erfolgreiche und fehlgeschlagene ausgehende SNAT-Flows werden gemeldet und können zur Problembehebung und Analyse Ihrer ausgehenden Flows verwendet werden.|
| Byteleistungsindikatoren | Load Balancer Standard meldet die pro Front-End verarbeiteten Daten.|
| Paketleistungsindikatoren | Load Balancer Standard meldet die pro Front-End verarbeiteten Pakete.|

Lesen Sie die [ausführliche Erläuterung zu Standard Load Balancer-Diagnosen](load-balancer-standard-diagnostics.md).

### <a name="haports"></a>HA-Ports

Standard Load Balancer unterstützt einen neuen Typ von Regel.  

Sie können Lastenausgleichsregeln konfigurieren, damit die Anwendung skaliert werden kann und hohe Zuverlässigkeit bietet. Wenn Sie eine Lastenausgleichsregel für Hochverfügbarkeitsports verwenden, stellt Standard Load Balancer einen Lastausgleich pro Datenfluss an jedem kurzlebigen Port der Front-End-IP-Adresse eines internen Load Balancer Standard bereit.  Das Feature ist auch für andere Szenarien hilfreich, in denen es unpraktisch oder nicht erwünscht ist, einzelne Port anzugeben.

Eine Lastenausgleichsregel für Hochverfügbarkeitsports ermöglicht es Ihnen, aktiv-passive oder aktiv-aktive n+1-Szenarien für virtuelle Netzwerkgeräte und jede Anwendung zu erstellen, die große Portbereiche für eingehende Verbindungen erfordert.  Ein Integritätstest kann verwendet werden, um zu bestimmen, welche Back-Ends neue Datenflüsse empfangen sollen.  Sie können eine Netzwerksicherheitsgruppe verwenden, um ein Portbereichsszenario zu emulieren.

>[!IMPORTANT]
> Wenn Sie beabsichtigen, ein virtuelles Netwerkgerät zu verwenden, fragen Sie Ihren Anbieter danach, ob sein Produkt mit HA-Ports getestet wurde, und befolgen Sie dessen spezielle Anweisungen für die Implementierung. 

Lesen Sie die [ausführliche Erläuterung zu HA-Ports](load-balancer-ha-ports-overview.md).

### <a name="securebydefault"></a>Standardmäßig sicher

Standard Load Balancer ist vollständig in das virtuelle Netzwerk integriert.  Das virtuelle Netzwerk ist ein privates geschlossenes Netzwerk.  Da Standard Load Balancer und öffentliche Standard-IP-Adressen so ausgelegt sind, dass sie Zugriff auf dieses virtuelle Netzwerk von außerhalb des virtuellen Netzwerks zulassen, werden diese Ressourcen nun standardmäßig geschlossen, es sei denn, Sie öffnen sie. Dies bedeutet, dass nun Netzwerksicherheitsgruppen (NSGs) verwendet werden, um zulässigen Datenverkehr explizit zu gestatten und whitelistgemäß zu verarbeiten.  Sie können Ihr gesamtes virtuelles Rechenzentrum erstellen und über NSGs entscheiden, was verfügbar sein soll und wann es verfügbar sein soll.  Wenn Sie über keine NSG für ein Subnetz oder für eine NIC Ihrer VM-Ressource verfügen, ist diese Ressource für Datenverkehr nicht erreichbar.

Weitere Informationen zu NSGs und ihrer Verwendung in Ihrem Szenario finden Sie unter [Netzwerksicherheitsgruppen](../virtual-network/security-overview.md).

### <a name="outbound"></a> Ausgehende Verbindungen

Load Balancer unterstützt Szenarien mit eingehenden und ausgehenden Verbindungen.  Load Balancer Standard unterscheidet sich hinsichtlich ausgehender Verbindungen deutlich von Load Balancer Basic.

SNAT (Source Network Address Translation) wird verwendet, um interne private IP-Adressen in Ihrem virtuellen Netzwerk auf öffentliche IP-Adressen auf Load Balancer-Front-Ends abzubilden.

Mit Standard Load Balancer wird ein neuer Algorithmus für einen [robusten sowie besser skalierbaren und vorhersagbaren SNAT-Algorithmus](load-balancer-outbound-connections.md#snat) eingeführt, und Standard Load Balancer ermöglicht neue Fähigkeiten, beseitigt Mehrdeutigkeit und erzwingt explizite Konfigurationen anstelle von Nebeneffekten. Diese Änderungen sind erforderlich, damit neue Features eingebunden werden können. 

Dies sind die wichtigsten Grundsätze, die beim Arbeiten mit Standard Load Balancer zu beachten sind:

- Der Abschluss einer Regel steuert die Load Balancer-Ressource.  Die gesamte Azure-Programmierung leitet sich aus ihrer Konfiguration ab.
- Wenn mehrere Front-Ends verfügbar sind, werden alle Front-Ends verwendet, und jedes Front-End multipliziert die Anzahl der verfügbaren SNAT-Ports.
- Sie können für ein bestimmtes Front-End festlegen, dass es nicht für ausgehende Verbindungen verwendet werden darf.
- Szenarien mit ausgehenden Verbindungen sind explizit, und ausgehende Verbindungen sind nicht vorhanden, bis sie angegeben wurden.
- Lastenausgleichsregeln folgen der SNAT-Programmierung. Lastenausgleichsregeln sind protokollspezifisch. SNAT ist protokollspezifisch, und dies sollte sich in der Konfiguration wiederfinden, statt einen Nebeneffekt zu erstellen.

#### <a name="multiple-frontends"></a>Mehrere Front-Ends
Wenn Sie weitere SNAT-Ports benötigen, weil Sie einen hohen Bedarf an ausgehenden Verbindungen erwarten oder bereits feststellen, können Sie auch einen inkrementellen SNAT Portvorrat hinzufügen, indem Sie zusätzliche Front-Ends, Regeln und Back-End-Pools für dieselben Ressourcen der virtuellen Computer konfigurieren.

#### <a name="control-which-frontend-is-used-for-outbound"></a>Steuern, welches Front-End für ausgehende Verbindungen verwendet wird
Wenn Sie ausgehende Verbindungen so einschränken möchten, dass sie nur von einer bestimmten Front-End-IP-Adresse stammen, können Sie optional ausgehende SNAT-Ports in der Regel deaktivieren, in der die Zuordnung für ausgehende Verbindungen formuliert ist.

#### <a name="control-outbound-connectivity"></a>Steuern von ausgehenden Verbindungen
Standard Load Balancer ist im Kontext des virtuellen Netzwerks vorhanden.  Ein virtuelles Netzwerk ist ein isoliertes privates Netzwerk.  Solange es keine Zuordnung zu einer öffentlichen IP-Adresse gibt, sind öffentliche Verbindungen nicht zulässig.  Sie können [VNet-Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) erreichen, da sie sich in Ihrem virtuellen Netzwerk befinden und zu diesem lokal sind.  Wenn Sie eine ausgehende Verbindung zu einem Ziel außerhalb Ihres virtuellen Netzwerks herstellen möchten, haben Sie zwei Möglichkeiten:
- Sie können der Ressource des virtuellen Computers eine öffentliche Standard-SKU-IP-Adresse als öffentliche IP-Adresse auf Instanzebene zuweisen, oder
- Sie können die Ressource des virtuellen Computers im Back-End-Pool eines öffentlichen Standard Load Balancers platzieren.

Auf beide Arten werden ausgehende Verbindungen aus dem virtuellen Netzwerk ermöglicht. 

Wenn Sie _nur_ einen internen Standard Load Balancer mit dem Back-End-Pool verknüpft haben, in dem sich die Ressource des virtuellen Computers befindet, kann Ihr virtueller Computer nur virtuelle Netzwerkressourcen und [VNet-Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) erreichen.  Sie können die im vorherigen Abschnitt beschriebenen Schritte ausführen, um ausgehende Verbindungen zu erstellen.

Ausgehende Verbindungen für eine Ressource eines virtuellen Computers, die keinen Standard-SKUs zugeordnet ist, bleiben ungeändert.

Lesen Sie die [ausführliche Erläuterung zu ausgehenden Verbindungen](load-balancer-outbound-connections.md).

### <a name="multife"></a>Mehrere Front-Ends
Load Balancer unterstützt mehrere Regeln mit mehreren Front-Ends.  Für Standard Load Balancer wird dies auf Szenarien mit ausgehenden Verbindungen erweitert.  Szenarien mit ausgehenden Verbindungen sind im Prinzip die Umkehrung einer Lastenausgleichsregel für eingehende Verbindungen.  Mit der Lastenausgleichsregel für eingehende Verbindungen wird auch eine Zuordnung für ausgehende Verbindungen erstellt. Load Balancer Standard verwendet über eine Lastenausgleichsregel alle Front-Ends, die einer virtuellen Computerressource zugeordnet sind.  Darüber hinaus gibt es einen Parameter in der Lastenausgleichsregel, mit dem Sie eine Lastenausgleichsregel bezüglich ausgehender Verbindungen unterdrücken und bestimmte Front-Ends (oder auch keins) auswählen können.

Zum Vergleich: Basic Load Balancer wählt ein einzelnes Front-End nach dem Zufallsprinzip aus, und es gibt keine Möglichkeit, zu steuern, welches ausgewählt wird.

Lesen Sie die [ausführliche Erläuterung zu ausgehenden Verbindungen](load-balancer-outbound-connections.md).

### <a name="operations"></a> Verwaltungsvorgänge

Standard Load Balancer-Ressourcen sind nur auf einer vollständig neuen Infrastrukturplattform vorhanden.  Dies ermöglicht schnellere Verwaltungsvorgänge für Standard-SKUs, und die Ausführungszeiten liegen in der Regel unter 30 Sekunden pro Standard-SKU-Ressource.  Mit der Vergrößerung von Back-End-Pools erhöht sich auch die Dauer, die für Änderungen von Back-End-Pools erforderlich ist.

Sie können viel schneller Standard Load Balancer-Ressourcen ändern und eine öffentliche Standard-IP-Adresse von einem virtuellen Computer zu einem anderen verschieben.

## <a name="migration-between-skus"></a>Migration zwischen SKUs

SKUs sind nicht änderbar. Führen Sie die Schritte in diesem Abschnitt aus, um aus einer Ressourcen-SKU in eine andere zu wechseln.

>[!IMPORTANT]
>Lesen Sie sich dieses Dokument vollständig durch, um die Unterschiede zwischen den SKUs zu verstehen, und machen Sie sich umfassend mit Ihrem Szenario vertraut.  Unter Umständen sind zur Anpassung an Ihr Szenario zusätzliche Änderungen erforderlich.

### <a name="migrate-from-basic-to-standard-sku"></a>Migrieren von der SKU „Basic“ zu „Standard“

1. Erstellen Sie eine neue Standard-Ressource (Load Balancer bzw. Public IP nach Bedarf). Erstellen Sie Ihre Regeln und Prüfdefinitionen erneut.  Falls Sie zuvor einen TCP-Test für 443/TCP verwendet haben, ändern Sie dieses Testprotokoll ggf. in einen HTTPS-Test, und fügen Sie einen Pfad hinzu.

2. Erstellen Sie eine neue Netzwerksicherheitsgruppe (NSG) für die Netzwerkschnittstelle (NIC) (bzw. aktualisieren Sie eine vorhandene NSG) oder ein Subnetz für die Whitelist mit dem vom Lastenausgleich verteilten Datenverkehr, eine Prüfung sowie beliebigen anderen Datenverkehr, den Sie zulassen möchten.

3. Entfernen Sie die Basic-SKU-Ressourcen (Load Balancer und Public IPs nach Bedarf) aus allen VM-Instanzen. Stellen Sie sicher, dass auch alle VM-Instanzen einer Verfügbarkeitsgruppe entfernt werden.

4. Fügen Sie alle VM-Instanzen an die neuen SKU-Ressourcen vom Typ „Standard“ an.

### <a name="migrate-from-standard-to-basic-sku"></a>Migrieren von der SKU „Standard“ zu „Basic“

1. Erstellen Sie eine neue Basic-Ressource (Load Balancer bzw. Public IP nach Bedarf). Erstellen Sie Ihre Regeln und Prüfdefinitionen erneut.  Ändern Sie einen HTTPS-Test in einen TCP-Test für 443/TCP. 

2. Entfernen Sie die Standard-SKU-Ressourcen (Load Balancer und Public IPs nach Bedarf) aus allen VM-Instanzen. Stellen Sie sicher, dass auch alle VM-Instanzen einer Verfügbarkeitsgruppe entfernt werden.

3. Fügen Sie alle VM-Instanzen an die neuen SKU-Ressourcen vom Typ „Basic“ an.

>[!IMPORTANT]
>
>Es bestehen Einschränkungen hinsichtlich der Nutzung der SKUs „Basic“ und „Standard“.
>
>HA-Ports und Diagnose der Standard-SKU sind nur in der SKU „Standard“ verfügbar. Sie können nicht von der SKU „Standard“ zur SKU „Basic“ migrieren und diese Funktionen beibehalten.
>
>Basic- und Standard-SKU haben eine Reihe von Unterschieden, wie sie in diesem Artikel beschrieben sind.  Sie sollten versuchen, diese zu verstehen und sich darauf vorzubereiten.
>
>Für Load Balancer- und Public IP-Ressourcen müssen übereinstimmende SKUs verwendet werden. Eine Kombination von Ressourcen vom SKU-Typ „Basic“ und „Standard“ ist nicht möglich. Sie können eigenständige virtuelle Computer, virtuelle Computer in einer Ressource einer Verfügbarkeitsgruppe oder eine Ressource einer VM-Skalierungsgruppe an beide SKUs gleichzeitig anfügen.

## <a name="region-availability"></a>Regionale Verfügbarkeit

Load Balancer Standard ist zurzeit in allen öffentlichen Cloudregionen verfügbar.

## <a name="sla"></a>SLA

Standard Load Balancer sind mit einer SLA von 99,99 % verfügbar.  Lesen Sie die [SLA für Standard Load Balancer](https://aka.ms/lbsla), wenn Sie ausführliche Informationen wünschen.

## <a name="pricing"></a>Preise

Die Verwendung von Load Balancer Standard ist kostenpflichtig.

- Anzahl konfigurierter Lastenausgleichs- und Ausgangsregeln (NAT-Regeln für eingehenden Datenverkehr werden nicht auf die Gesamtanzahl der Regeln angerechnet.)
- Menge der eingehenden und ausgehenden verarbeiteten Daten, ungeachtet der Regel. 

Informationen zu den Preisen für den Standard-Load Balancer finden Sie auf der Seite [Load Balancer – Preise](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="limitations"></a>Einschränkungen

- SKUs sind nicht änderbar. Sie können die SKU einer vorhandenen Ressource nicht ändern.
- Eine Ressource eines eigenständigen virtuellen Computers, eine Verfügbarkeitsgruppenressource oder eine Ressource einer VM-Skalierungsgruppe kann auf eine SKU verweisen, nie auf beide.
- Eine Load Balancer-Regel kann sich nicht über zwei virtuelle Netzwerke erstrecken.  Front-Ends und ihre zugehörigen Back-End-Instanzen müssen sich im gleichen virtuellen Netzwerk befinden.  
- [Vorgänge zum Verschieben von Abonnements](../azure-resource-manager/resource-group-move-resources.md) werden für Standard-SKU-LB- und -PIP-Ressourcen nicht unterstützt.
- Auf Web-Workerrollen ohne VNet und andere Plattformdienste von Microsoft kann aufgrund eines Nebeneffekts der Funktionsweise von Diensten vor VNet und anderen Plattformdiensten nur zugegriffen werden, wenn interner Standard-Load Balancer verwendet wird. Sie können sich nicht darauf verlassen, da der jeweilige Dienst oder die zugrunde liegende Plattform ohne vorherige Ankündigung geändert werden kann. Sie müssen immer davon ausgehen, dass Sie [ausgehende Verbindungen](load-balancer-outbound-connections.md), falls gewünscht, explizit erstellen müssen, wenn Sie nur den internen Standard-Load Balancer verwenden.
- Load Balancer ist ein TCP- oder UDP-Produkt für den Lastenausgleich und die Portweiterleitung für diese spezifischen IP-Protokolle.  Regeln für den Lastenausgleich und eingehende NAT-Regeln werden für TCP und UDP, aber nicht für andere IP-Protokolle wie ICMP unterstützt. Der Load Balancer beendet die Payload des UDP- oder TCP-Flows nicht, antwortet nicht auf diese und interagiert auch nicht auf andere Weise mit ihr. Es handelt sich nicht um einen Proxy. Eine erfolgreiche Validierung der Konnektivität auf ein Front-End muss über einen In-Band-Zugriff mit dem Protokoll ausgeführt werden, das in einer Regel für den Lastenausgleich oder die eingehende Netzwerkadressenübersetzung (TCP oder UDP) verwendet wird, _und_ mindestens einer Ihrer virtuellen Computer muss eine Antwort für einen Client generieren, damit eine Antwort von einem Front-End angezeigt werden kann.  Wenn Sie keine In-Band-Antwort vom Load Balancer-Front-End erhalten, deutet dies darauf hin, dass keine virtuellen Computer antworten konnten.  Die Interaktion mit einer Load Balancer-Instanz ist nicht möglich, wenn ein virtueller Computer nicht antworten kann.  Dies gilt auch für ausgehende Verbindungen, für die die [Portmaskierung mit SNAT](load-balancer-outbound-connections.md#snat) nur für TCP und UDP unterstützt wird. Andere IP-Protokolle wir ICMP schlagen fehl.  Weisen Sie eine öffentlichen IP-Adresse auf Instanzebene zu, die eingeschränkt werden soll.
- Im Gegensatz zu öffentlichen Load Balancer-Instanzen, die [ausgehende Verbindungen](load-balancer-outbound-connections.md) bereitstellen, wenn ein Übergang von privaten IP-Adressen innerhalb des virtuellen Netzwerks auf öffentliche IP-Adressen vorgenommen wird, übersetzen interne Load Balancer-Instanzen keine ursprünglichen ausgehenden Verbindungen mit dem Front-End einer internen Load Balancer-Instanz, da sich beide in einem privaten IP-Adressraum befinden.  Dadurch wird das Risiko einer SNAT-Auslastung innerhalb eines eindeutigen internen IP-Adressraums gemindert, in dem keine Übersetzung erforderlich ist.  Eine Nebenwirkung besteht darin, wenn ein von einem virtuellen Computer ausgehender Flow in den Back-End-Pool versucht, einen Flow auf das Front-End der internen Load Balancer-Instanz in den Pool auszuführen, in dem er sich befindet, _und_ sich selbst zugewiesen ist, stimmen beide Verzweigungen des Flows nicht überein. Somit schlägt der Flow fehl.  Wenn der Flow nicht dem virtuellen Computer im Back-End-Pool zugeordnet ist, die den Flow auf das Front-End erstellt hat, ist der Flow erfolgreich.   Wenn der Flow sich selbst zugeordnet ist, scheint es, als würde der ausgehende Flow vom virtuellen Computer auf das Front-End ausgehen und als würde der zugehörige eingehende Flow vom virtuellen Computer selbst ausgehen. Aus der Sicht des Gastbetriebssystems stimmen die eingehenden und ausgehenden Bestandteile desselben Flows innerhalb des virtuellen Computers nicht überein. Der TCP-Stack erkennt diese Teile desselben Flows als Teil desselben Flows an, da Quelle und Ziel nicht übereinstimmen.  Wenn der Flow keinem anderen virtuellen Computer im Back-End-Pool zugeordnet ist, stimmen die Teile des Flows überein, und der virtuelle Computer kann erfolgreich auf den Flow antworten.  In Folge dieses Szenarios entstehen zeitweilige Verbindungstimeouts. Es gibt einige häufig verwendete Workarounds, damit Sie dieses Szenario zuverlässig abschließen können. Diese Workarounds umfassen entweder das Einfügen eines Proxys eines Drittanbieters hinter dem internen Load Balancer oder die [Verwendung von DSR-Stilregeln](load-balancer-multivip-overview.md).  Sie können zwar einen öffentlichen Load Balancer zur Entschärfung verwenden, aber in dem daraus resultierenden Szenarios kann es zu einer [SNAT-Überlastung](load-balancer-outbound-connections.md#snat) kommen. Daher sollten Sie diesen in dem Zusammenhang nur mit Bedacht verwenden.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu [Standard Load Balancer und Verfügbarkeitszonen](load-balancer-standard-availability-zones.md)
- Informationen zu [Integritätstests](load-balancer-custom-probe-overview.md)
- Weitere Informationen zu [Verfügbarkeitszonen](../availability-zones/az-overview.md).
- Weitere Informationen zu [Diagnosen für Standard Load Balancer](load-balancer-standard-diagnostics.md).
- Weitere Informationen zu [unterstützten mehrdimensionalen Metriken](../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) für Diagnosen in [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).
- Informationen zur Verwendung von [Load Balancer für ausgehende Verbindungen](load-balancer-outbound-connections.md)
- Informationen zu [Ausgangsregeln](load-balancer-outbound-rules-overview.md).
- Informationen zur [TCP-Zurücksetzung bei Leerlauf](load-balancer-tcp-reset.md).
- Informationen zu [Load Balancer Standard mit Lastenausgleichsregeln für HA-Ports](load-balancer-ha-ports-overview.md)
- Informationen zur Verwendung von [Load Balancer mit mehreren Front-Ends](load-balancer-multivip-overview.md)
- Weitere Informationen zu [virtuellen Netzwerken](../virtual-network/virtual-networks-overview.md).
- Weitere Informationen zu [Netzwerksicherheitsgruppen](../virtual-network/security-overview.md).
- Informationen zu [VNET-Dienstendpunkten](../virtual-network/virtual-network-service-endpoints-overview.md)
- Erfahren Sie mehr über die anderen zentralen [Netzwerkfunktionen](../networking/networking-overview.md) in Azure.
- Weitere Informationen zu [Load Balancer](load-balancer-overview.md).
