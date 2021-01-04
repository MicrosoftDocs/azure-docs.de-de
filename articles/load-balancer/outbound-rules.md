---
title: Ausgangsregeln – Azure Load Balancer
description: In diesem Artikel wird erläutert, wie Sie Ausgangsregeln konfigurieren, um den ausgehenden Internetdatenverkehr mit Azure Load Balancer zu steuern.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 6b73eb51831238f23400ef60d0a6162bca38ea85
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033152"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Ausgangsregeln – Azure Load Balancer

Mit Ausgangsregeln können Sie die SNAT (Quell-Netzwerkadressenübersetzung) für eine öffentliche Load Balancer Standard-Instanz explizit definieren. In dieser Konfiguration können Sie die öffentliche IP-Adresse Ihrer Load Balancers-Instanz verwenden, um ausgehende Internetkonnektivität für Ihre Back-End-Instanzen bereitzustellen.

Diese Konfiguration ermöglicht Folgendes:

* IP-Maskierung
* Vereinfachen ihrer Zulassungslisten.
* Verringern der Anzahl öffentlicher IP-Ressourcen für die Bereitstellung.

Mit Ausgangsregeln besitzen Sie vollständige deklarative Kontrolle über ausgehende Internetkonnektivität. Ausgangsregeln ermöglichen Ihnen das Skalieren und Optimieren dieser Fähigkeit gemäß Ihren speziellen Anforderungen. 

Ausgangsregeln werden nur befolgt, wenn die Back-End-VM nicht über eine öffentliche IP-Adresse (ILPIP) auf Instanzebene verfügt.

![Load Balancer-Ausgangsregeln](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Mit Ausgangsregeln können Sie das Verhalten von ausgehenden **SNAT**-Verbindungen explizit definieren.

Mit Ausgangsregeln können Sie steuern:

* **Welchen IP-Adressen von VMs in welche öffentlichen IP-Adressen übersetzt werden.**
     * Zwei Regeln,durch die Back-End-Pool A die IP-Adressen A und B verwendet und Back-End-Pool B die IP-Adressen C und D.
* **Die Art der Zuordnung ausgehender SNAT-Ports.**
     * Back-End-Pool B ist der einzige Pool, der ausgehende Verbindungen ermöglicht, alle SNAT-Ports werden Back-End-Pool B und kein Port Back-End-Pool A zugewiesen.
* **Welche Protokolle zum Übersetzen ausgehenden Datenverkehrs verwendet werden.**
     * Back-End-Pool B benötigt UDP-Ports für ausgehenden Datenverkehr. Back-End-Pool A benötigt TCP. Weisen Sie TCP-Ports A und UDP-Ports B zu.
* **Wie lange das Leerlauftimeout für ausgehende Verbindungen dauert (4 bis 120 Minuten).**
     * Wenn zeitintensive Verbindungen mit Keepalives vorhanden sind, reservieren Sie für zeitintensive Verbindungen bis zu 120 Minuten lang Ports mit Leerlauf. Gehen Sie davon aus, dass veraltete Verbindungen eingestellt werden, und geben Sie Ports innerhalb von 4 Minuten für neue Verbindungen frei. 
* **Ob eine TCP-Zurücksetzung bei Leerlauftimeout gesendet wird.**
     * Wenn bei Verbindungen im Leerlauf ein Timeout auftritt, senden wir TCP RST an den Client und Server, damit diese wissen, dass der Datenfluss eingestellt wird?

## <a name="outbound-rule-definition"></a>Definition der Ausgangsregel

Ausgangsregeln folgen der gleichen vertrauten Syntax wie Lastenausgleichsregeln und NAT-Eingangsregeln: **Front-End** + **Parameter** + **Back-End-Pool**. 

Eine Ausgangsregel konfiguriert die NAT für ausgehenden Datenverkehr für _alle VMs, die vom Back-End-Pool_ für die _Front-End_-Übersetzung identifiziert wurden.  

Die _Parameter_ ermöglichen eine zusätzliche differenzierte Steuerung des NAT-Algorithmus für ausgehenden Datenverkehr.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Skalieren der NAT für ausgehenden Datenverkehr mit mehreren IP-Adressen

Jede zusätzliche IP-Adresse, die von einem Front-End bereitgestellt wird, stellt zusätzliche 64.000 kurzlebige Ports zur Verfügung, die Load Balancer als SNAT-Ports verwenden kann. 

Verwenden Sie mehrere IP-Adressen, um umfangreiche Szenarien zu planen. Verwenden Sie Ausgangsregeln, um [SNAT-Erschöpfung](troubleshoot-outbound-connection.md#snatexhaust) zu vermeiden. 

Sie können auch ein [öffentliches IP-Präfix](./load-balancer-outbound-connections.md#outboundrules) direkt mit einer Ausgangsregel verwenden. 

Ein öffentliches IP-Präfix erhöht die Skalierung Ihrer Bereitstellung. Das Präfix kann der Positivliste der Datenflüsse hinzugefügt werden, die aus ihren Azure-Ressourcen stammen. Sie können eine Front-End-IP-Konfiguration innerhalb der Load Balancer-Instanz konfigurieren, um auf das Präfix einer öffentlichen IP-Adresse zu verweisen.  

Die Load Balancer-Instanz besitzt die Kontrolle über das öffentliche IP-Präfix. Die Ausgangsregel verwendet automatisch alle öffentlichen IP-Adressen, die im Präfix für öffentliche IP-Adressen für ausgehende Verbindungen enthalten sind. 

Jede IP-Adresse innerhalb des öffentlichen IP-Präfixes bietet zusätzliche 64.000 kurzlebige Ports pro IP-Adresse, die Load Balancer als SNAT-Ports verwenden kann.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Leerlauftimeout für ausgehenden Datenfluss und TCP-Zurücksetzung

Ausgangsregeln stellen einen Konfigurationsparameter bereit, um das Leerlauftimeout für den ausgehenden Datenfluss zu steuern und ihn an die Anforderungen Ihrer Anwendung anzupassen. Das Leerlauftimeout für ausgehenden Datenverkehr tritt standardmäßig nach 4 Minuten ein. Weitere Informationen finden Sie unter [Konfigurieren von Leerlauftimeouts](load-balancer-tcp-idle-timeout.md). 

Das Standardverhalten von Load Balancer besteht darin, den Datenfluss allmählich zu entfernen, wenn das Leerlauftimeout für ausgehenden Datenverkehr erreicht wurde. Der `enableTCPReset`-Parameter ermöglicht vorhersagbares Anwendungsverhalten und Kontrolle. Der Parameter gibt vor, ob beim Timeout des ausgehenden Leerlauftimeouts bidirektionales TCP Reset (TCP RST) gesendet werden soll. 

Unter [TCP-Zurücksetzung bei Leerlauftimeout](./load-balancer-tcp-reset.md) finden Sie weitere Informationen, unter anderem zur regionalen Verfügbarkeit.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>Explizites Sichern und Steuern der ausgehenden Konnektivität

Lastenausgleichsregeln bieten automatische Programmierung der NAT für ausgehenden Datenverkehr. In einigen Szenarien müssen Sie die automatische Programmierung der NAT für ausgehenden Datenverkehr durch die Lastenausgleichsregel deaktivieren. Durch die Deaktivierung über die Regel können Sie das Verhalten steuern oder verfeinern.  

Sie haben zwei Möglichkeiten, diesen Parameter zu verwenden:

1. Verhinderung der eingehenden IP-Adresse für ausgehende SNAT-Verbindungen. Deaktivieren Sie SNAT für ausgehenden Datenverkehr in der Lastenausgleichsregel.
  
2. Passen Sie die **SNAT**-Parameter für ausgehenden Datenverkehr einer IP-Adresse an, die zugleich für eingehenden und ausgehenden Datenverkehr verwendet wird. Automatische NAT für ausgehenden Datenverkehr muss deaktiviert werden, damit eine Ausgangsregel die Kontrolle übernehmen kann. Um die SNAT-Portzuordnung einer Adresse zu ändern, die auch für den eingehenden Datenverkehr verwendet wird, muss der Parameter `disableOutboundSnat` auf TRUE festgelegt werden. 

Der Vorgang zum Konfigurieren einer Ausgangsregel schlägt fehl, wenn Sie versuchen, eine IP-Adresse neu zu definieren, die für eingehenden Datenverkehr verwendet wird.  Deaktivieren Sie zuerst die ausgehende NAT der Lastenausgleichsregel.

>[!IMPORTANT]
> Ihre VM hat keine ausgehenden Verbindungen, wenn Sie diesen Parameter auf TRUE festlegen und keine Ausgangsregel zum Definieren ausgehender Verbindungen haben.  Einige Vorgänge Ihrer VM oder Ihrer Anwendung können davon abhängen, ob ausgehende Verbindungen verfügbar sind. Stellen Sie sicher, dass Sie die Abhängigkeiten Ihres Szenarios verstehen und die Auswirkungen von Änderungen berücksichtigt haben.

Manchmal ist es nicht wünschenswert, dass einem VM einen ausgehenden Datenflusses erstellt. Möglicherweise müssen Sie auch verwalten, welche Ziele mit ausgehende Datenflüsse empfangen bzw. welche Ziele eingehende Datenflüsse beginnen. Verwenden Sie [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md) zum Verwalten der Ziele,die die VM erreicht. Verwenden Sie Netzwerksicherheitsgruppen, um zu verwalten, welche öffentlichen Ziele eingehende Datenflüsse starten.

Wenn Sie eine Netzwerksicherheitsgruppe einem virtuellen Computer mit Lastenausgleich zuordnen, müssen Sie auf die [Diensttags](../virtual-network/network-security-groups-overview.md#service-tags) und [Standardsicherheitsregeln](../virtual-network/network-security-groups-overview.md#default-security-rules) achten. 

Stellen Sie sicher, dass die VM Integritätstestanforderungen von Azure Load Balancer empfangen kann.

Wenn eine Netzwerksicherheitsgruppe Anforderungen von Integritätstests vom Standardtag AZURE_LOADBALANCER blockiert, misslingt Ihr VM-Integritätstests, weshalb die VM mit „Nicht verfügbar“ markiert wird. Der Lastenausgleich beendet das Senden neuer Datenflüsse an diese VM.

## <a name="scenarios-with-outbound-rules"></a>Szenarien mit Ausgangsregeln
        

### <a name="outbound-rules-scenarios"></a>Szenarien für Ausgangsregeln


* Konfigurieren Sie ausgehende Verbindungen für einen bestimmten Satz öffentlicher IP-Adressen oder Präfixe.
* Ändern Sie die [SNAT](load-balancer-outbound-connections.md)-Portzuordnung.
* Aktivieren nur ausgehenden Datenverkehr.
* Legen Sie die NAT nur für ausgehenden Datenverkehr für VMs fest (kein eingehender Datenverkehr).
* Legen Sie die NAT für ausgehenden Datenverkehr für die interne Load Balancer Standard-Instanz fest.
* Aktivieren Sie sowohl das TCP-Protokoll als auch das UDP-Protokoll für die NAT für ausgehenden Datenverkehr mit einer öffentlichen Load Balancer Standard-Instanz.


### <a name="scenario-1-configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>Szenario 1: Konfigurieren ausgehender Verbindungen für einen bestimmten Satz öffentlicher IP-Adressen oder Präfixe


#### <a name="details"></a>Details


Verwenden Sie dieses Szenario, um einen Satz öffentlicher IP-Adressen als Ursprung ausgehender Datenverbindungen einzurichten. Fügen Sie öffentliche IP-Adressen oder Präfixe basierend auf dem Ursprung zu Zulassungs- oder Ablehnungslisten hinzu.


Diese öffentliche IP-Adresse oder das Präfix kann dieselbe bzw. dasselbe sein, die/das von einer Lastenausgleichsregel verwendet wird. 


Gehen Sie folgendermaßen vor, um andere als die von einer Lastenausgleichsregel verwendeten öffentlichen IP-Adressen oder Präfixe zu verwenden: 


1. Erstellen Sie eine öffentliche IP-Adresse oder ein öffentliches IP-Präfix.
2. Erstellen Sie eine standardmäßige öffentliche Load Balancer Standard-Instanz. 
3. Erstellen Sie ein Front-End, und verweisen Sie dabei auf das gewünschte öffentliche Präfix oder die gewünschte öffentliche IP-Adresse. 
4. Verwenden Sie einen vorhandenen Back-End-Pool, oder erstellen Sie einen neuen Back-End-Pool, und stellen Sie die VMs in einem Back-End-Pool der öffentlichen Load Balancer-Instanz bereit.
5. Konfigurieren Sie eine Ausgangsregel in der öffentlichen Load Balancer-Instanz, um die NAT für ausgehenden Datenverkehr für diese VMs über das Front-End zu aktivieren. Es wird nicht empfohlen, eine Lastenausgleichsregel für ausgehenden Datenverkehr zu verwenden. Deaktivieren Sie die ausgehende SNAT in der Lastenausgleichsregel.


### <a name="scenario-2-modify-snatport-allocation"></a><a name="scenario2out"></a>Szenario 2: Ändern der [SNAT](load-balancer-outbound-connections.md)-Portzuordnung


#### <a name="details"></a>Details


Sie können Ausgangsregeln verwenden, um die [automatische SNAT-Portzuweisung basierend auf der Back-End-Poolgröße](load-balancer-outbound-connections.md#preallocatedports) anzupassen. 


Wenn Sie eine SNAT-Überlastung feststellen, vergrößern Sie die Anzahl von [SNAT](load-balancer-outbound-connections.md)-Ports über den Standardwert von 1.024 hinaus. 


Jede öffentliche IP-Adresse stellt bis zu 64.000 kurzlebige Ports bereit. Die Anzahl von VMs im Back-End-Pool bestimmt die Anzahl von Ports, die an jede VM verteilt werden. Eine VM im Back-End-Pool kann auf maximal 64.000 Ports zugreifen. Bei zwei VMs können maximal 32.000 [SNAT](load-balancer-outbound-connections.md)-Ports mit einer Ausgangsregel zugeordnet werden (2 × 32.000 = 64.000). 


Sie können Ausgangsregeln verwenden, um die standardmäßig zugeordneten SNAT-Ports zu optimieren. Sie können einen höheren oder einen niedrigeren Wert als den Wert der standardmäßigen [SNAT](load-balancer-outbound-connections.md)-Portzuweisung verwenden. Jede öffentliche IP-Adresse in einem Front-End einer Ausgangsregel stellt bis zu 64.000 kurzlebige Ports zur Verwendung als [SNAT](load-balancer-outbound-connections.md)-Ports bereit. 


Der Lastenausgleich weist [SNAT](load-balancer-outbound-connections.md)-Ports als Vielfache von 8 zu. Wenn Sie einen Wert angeben, der nicht durch 8 teilbar ist, wird der Konfigurationsvorgang abgelehnt. Jede Lastenausgleichsregel und jede NAT-Regel für eingehenden Datenverkehr benötigt einen Bereich von 8 Ports. Wenn eine Lastenausgleichsregel oder eine NAT-Regel für eingehenden Datenverkehr denselben Bereich von 8 Ports verwendet wie eine andere Regel, werden keine zusätzlichen Ports benötigt.


Wenn Sie versuchen, mehr [SNAT](load-balancer-outbound-connections.md)-Ports zuzuordnen, als basierend auf öffentlichen IP-Adressen vorhanden sind, wird der Konfigurationsvorgang abgelehnt. Eine Beispiel: Sie ordnen 10.000 Ports pro VM zu, und sieben VMs in einem Back-End-Pool nutzen eine einzige öffentliche IP-Adresse. In diesem Fall wird die Konfiguration abgelehnt, da 7 ×10.000 das Limit von 64.000 Ports überschreitet. In diesem Szenario müssen Sie dem Front-End der Ausgangsregel weitere öffentliche IP-Adressen hinzu. 


Indem Sie die Anzahl von Ports auf 0 festlegen, kehren Sie zur [standardmäßigen Portzuordung](load-balancer-outbound-connections.md#preallocatedports) zurück. In diesem Fall erhalten die ersten 50 VM-Instanzen 1024 Ports. Die nächsten 51-100 VM-Instanzen erhalten 512 Ports (bis zur maximalen Anzahl von Instanzen). Weitere Informationen zur standardmäßigen SNAT-Portzuweisung finden Sie in der [Zuordnungstabelle für SNAT-Ports](./load-balancer-outbound-connections.md#preallocatedports).


### <a name="scenario-3-enable-outbound-only"></a><a name="scenario3out"></a>Szenario 3: Ausschließliches Aktivieren des ausgehenden Datenverkehrs


#### <a name="details"></a>Details


Verwenden Sie eine öffentliche Load Balancer Standard-Instanz, um die NAT für ausgehenden Datenverkehr für eine Gruppe von VMs bereitzustellen. In diesem Szenario verwenden Sie eine Ausgangsregel alleine, ohne zusätzliche konfigurierte Regeln.


> [!NOTE]
> **Azure Virtual Network NAT** kann ausgehende Konnektivität für virtuelle Computer bereitstellen, ohne dass ein Lastenausgleich erforderlich ist. Weitere Informationen finden Sie unter [Was ist Azure Virtual Network NAT?](../virtual-network/nat-overview.md).

### <a name="scenario-4-outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>Szenario 4: NAT für ausgehenden Datenverkehr nur für VMs (kein eingehender Datenverkehr)


> [!NOTE]
> **Azure Virtual Network NAT** kann ausgehende Konnektivität für virtuelle Computer bereitstellen, ohne dass ein Lastenausgleich erforderlich ist. Weitere Informationen finden Sie unter [Was ist Azure Virtual Network NAT?](../virtual-network/nat-overview.md).

#### <a name="details"></a>Details


In diesem Szenario: Ausgangsregeln von Azure Load Balancer und Virtual Network NAT sind Optionen, die für ausgehenden Datenverkehr aus einem virtuellen Netzwerk verfügbar sind.


1. Erstellen Sie eine öffentliche IP-Adresse oder ein öffentliches Präfix.
2. Erstellen Sie eine öffentliche Load Balancer Standard-Instanz. 
3. Erstellen Sie ein Front-End, das der öffentlichen IP-Adresse oder dem öffentlichen Präfix für ausgehenden Datenverkehr zugeordnet ist.
4. Erstellen Sie einen Back-End-Pool für die VMs.
5. Platzieren Sie die VMs im Back-End-Pool.
6. Konfigurieren Sie eine Ausgangsregel, um die NAT für ausgehenden Datenverkehr zu aktivieren.



Verwenden Sie ein Präfix oder eine öffentliche IP-Adresse, um [SNAT](load-balancer-outbound-connections.md)-Ports zu skalieren. Fügen Sie die Quelle der ausgehenden Datenverbindungen zu einer Zulassungs-oder Ablehnungsliste hinzu.



### <a name="scenario-5-outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>Szenario 5: NAT für ausgehenden Datenverkehr für die interne Load Balancer Standard-Instanz


> [!NOTE]
> **Azure Virtual Network NAT** kann über eine interne Load Balancer Standard-Instanz ausgehende Konnektivität für virtuelle Computer bereitstellen. Weitere Informationen finden Sie unter [Was ist Azure Virtual Network NAT?](../virtual-network/nat-overview.md).

#### <a name="details"></a>Details


Ausgehende Konnektivität ist für eine interne Load Balancer Standard-Instanz erst verfügbar, wenn diese über öffentliche IP-Adressen oder Virtual Network NAT auf Instanzebene deklariert wurde oder wenn die Mitglieder des Back-End-Pools explizit mit einer Load Balancer-Konfiguration nur für ausgehenden Datenverkehr verknüpft wurden. 


Weitere Informationen finden Sie unter [Lastenausgleichskonfiguration (nur ausgehender Datenverkehr)](./egress-only.md).




### <a name="scenario-6-enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>Szenario 6: Aktivieren sowohl des TCP-Protokoll als auch des UDP-Protokolls für die NAT für ausgehenden Datenverkehr mit einer öffentlichen Load Balancer Standard-Instanz


#### <a name="details"></a>Details


Beim Verwenden einer öffentlichen Load Balancer Standard-Instanz entspricht die bereitgestellte automatische NAT für ausgehenden Datenverkehr dem Transportprotokoll der Lastenausgleichsregel. 


1. Deaktivieren Sie die [SNAT](load-balancer-outbound-connections.md) für ausgehenden Datenverkehr in der Lastenausgleichsregel. 
2. Konfigurieren Sie eine Ausgangsregel in derselben Load Balancer-Instanz.
3. Verwenden Sie erneut den Back-End-Pool, den Ihre VMs bereits verwenden. 
4. Geben Sie „Protokoll“: „Alle“ als Teil der Ausgangsregel an. 


Wenn nur NAT-Eingangsregeln verwendet werden, wird keine NAT für ausgehenden Datenverkehr bereitgestellt. 


1. Stellen Sie die VMs in einem Back-End-Pool bereit.
2. Definieren Sie mindestens eine Front-End-IP-Konfiguration mit mindestens einer öffentlichen IP-Adresse oder einem Präfix für öffentliche IP-Adressen. 
3. Konfigurieren Sie eine Ausgangsregel in derselben Load Balancer-Instanz. 
4. Geben Sie „Protokoll“: „Alle“ als Teil der Ausgangsregel an.


## <a name="limitations"></a>Einschränkungen

- Die maximale Anzahl von verwendbaren kurzlebigen Ports pro Front-End-IP-Adresse beträgt 64.000.
- Das konfigurierbare Leerlauftimeout für ausgehenden Datenverkehr beträgt ist 4 bis 120 Minuten (240 bis 7200 Sekunden).
- Load Balancer unterstützt kein ICMP für die NAT ausgehenden Datenverkehrs.
- Ausgangsregeln können nur auf die primäre IP-Konfiguration einer NIC angewandt werden.  Sie können keine Ausgangsregel für die sekundäre IP-Adresse einer VM oder virtuellen Netzwerkappliance erstellen. Es werden mehrere NICs unterstützt.
- Alle virtuellen Computer innerhalb einer **Verfügbarkeitsgruppe** müssen dem Back-End-Pool für ausgehende Verbindungen hinzugefügt werden. 
- Alle virtuellen Computer in einer **VM-Skalierungsgruppe** müssen dem Back-End-Pool für ausgehende Verbindungen hinzugefügt werden.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über [Azure Load Balancer Standard](load-balancer-overview.md)
- Weitere Informationen finden Sie unter den [häufig gestellten Fragen zu Azure Load Balancer](load-balancer-faqs.md)