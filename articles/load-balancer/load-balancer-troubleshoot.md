---
title: Beheben von Problemen mit Azure Load Balancer
description: Hier erfahren Sie, wie Sie bekannte Probleme mit Azure Load Balancer beheben.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: a1a8df6d503ec5f5bf9c1e739e5ecf6486a85776
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697419"
---
# <a name="troubleshoot-azure-load-balancer"></a>Beheben von Problemen mit Azure Load Balancer

Diese Seite enthält Informationen zur Problembehandlung für häufige Fragen zu Azure Load Balancer vom Typ „Basic“ und „Standard“. Weitere Informationen zum Load Balancer vom Typ „Standard“ finden Sie unter [Übersicht: Azure Load Balancer Standard (Preview)](load-balancer-standard-diagnostics.md).

Wenn die Load Balancer-Konnektivität nicht verfügbar ist, liegen meist die folgenden Symptome vor: 

- VMs hinter dem Load Balancer antworten nicht auf Integritätstests 
- VMs hinter dem Load Balancer antworten nicht auf Datenverkehr am konfigurierten Port

Wenn die externen Clients für die Back-End-VMs den Load Balancer durchlaufen, wird die IP-Adresse der Clients für die Kommunikation verwendet. Stellen Sie sicher, dass die IP-Adressen der Clients der NSG-Zulassungsliste hinzugefügt werden. 

## <a name="symptom-no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>Symptom: Keine ausgehende Verbindung von internen Load Balancern Standard (ILB)

**Überprüfung und Lösung**

Standard-ILBs sind **standardmäßig sicher**. Basic-ILBs erlaubten eine Verbindung mit dem Internet über eine *ausgeblendete* öffentliche IP-Adresse. Dies wird für Produktionsworkloads nicht empfohlen, weil die IP-Adresse weder statisch noch über Netzwerksicherheitsgruppen gesperrt ist, die Sie besitzen. Wenn Sie vor Kurzem von einem Basic-ILB zu einem Standard-ILB gewechselt haben, sollten Sie eine öffentliche IP-Adresse explizit über die [Nur ausgehend](egress-only.md)-Konfiguration erstellen, die die IP-Adresse über Netzwerksicherheitsgruppen sperrt. Sie können in Ihrem Subnetz auch eine [NAT Gateway](../virtual-network/nat-overview.md)-Instanz verwenden.

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Symptom: VMs hinter dem Load Balancer antworten nicht auf Integritätstests
Damit Back-End-Server Teil einer Load Balancer-Gruppe sein können, müssen sie die Überprüfung bestehen. Weitere Informationen zu Integritätstests finden Sie unter [Grundlegendes zu Load Balancer-Tests](load-balancer-custom-probe-overview.md). 

Die VMs des Load Balancer-Back-End-Pools antworten möglicherweise aus einem der folgenden Gründe nicht auf die Tests: 
- Die VM des Load Balancer-Back-End-Pools ist fehlerhaft 
- Die VM des Load Balancer-Back-End-Pools lauscht nicht am Testport 
- Die Firewall oder eine Netzwerksicherheitsgruppe blockiert den Port auf den VMs des Load Balancer-Back-End-Pools 
- Andere Fehlkonfigurationen im Load Balancer

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Ursache 1: Die VM des Load Balancer-Back-End-Pools ist fehlerhaft 

**Überprüfung und Lösung**

Um dieses Problem zu beheben, melden Sie sich an den beteiligten VMs an, und überprüfen Sie, ob der VM-Status fehlerfrei lautet und ob sie auf **PsPing** oder **TCPing** von einer anderen VM im Pool antworten können. Wenn die VM fehlerhaft ist oder nicht auf den Test antworten kann, müssen Sie das Problem beheben und die VM wieder in einen fehlerfreien Status versetzen, bevor sie am Lastenausgleich teilnehmen kann.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Ursache 2: Die VM des Load Balancer-Back-End-Pools lauscht nicht am Testport
Wenn die VM fehlerfrei ist, aber nicht auf den Test antwortet, könnte eine mögliche Ursache sein, dass der Testport auf der entsprechenden VM nicht geöffnet ist oder dass die VM nicht an diesem Port lauscht.

**Überprüfung und Lösung**

1. Melden Sie sich an der Back-End-VM an. 
2. Öffnen Sie eine Eingabeaufforderung, und führen Sie den folgenden Befehl aus, um zu überprüfen, ob eine Anwendung am Testport lauscht:   
            netstat -an
3. Wenn der Portstatus nicht als **EMPFANGSBEREIT** aufgeführt ist, konfigurieren Sie den richtigen Port. 
4. Wählen Sie alternativ einen anderen Port aus, der als **EMPFANGSBEREIT** aufgeführt wird, und aktualisieren Sie die Load Balancer-Konfiguration entsprechend.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Ursache 3: Die Firewall oder eine Netzwerksicherheitsgruppe blockiert den Port auf den VMs des Load Balancer-Back-End-Pools  
Wenn die Firewall auf der VM den Testport blockiert oder eine oder mehrere Netzwerksicherheitsgruppen, die für das Subnetz oder die VM konfiguriert sind, nicht zulassen, dass der Test den Port erreicht, kann die VM nicht auf den Integritätstest antworten.          

**Überprüfung und Lösung**

* Wenn die Firewall aktiviert ist, überprüfen Sie, ob sie so konfiguriert ist, dass der Testport zulässig ist. Wenn dies nicht der Fall ist, konfigurieren Sie die Firewall zum Zulassen von Datenverkehr am Testport, und führen Sie den Test erneut aus. 
* Überprüfen Sie anhand der Liste der Netzwerksicherheitsgruppen, ob beim eingehenden oder ausgehenden Datenverkehr am Testport Störungen auftreten. 
* Überprüfen Sie auch, ob eine Regel **Alle verweigern** für Netzwerksicherheitsgruppen der NIC der VM oder des Subnetzes eine höhere Priorität als die Standardregel aufweist, die Load Balancer-Tests und -Datenverkehr zulässt (Netzwerksicherheitsgruppen müssen die Load Balancer-IP-Adresse 168.63.129.16 zulassen). 
* Wenn eine dieser Regeln den Testdatenverkehr blockiert, entfernen und konfigurieren Sie die Regeln, um den Testdatenverkehr zuzulassen.  
* Testen Sie, ob die VM jetzt auf Integritätstests antwortet. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Ursache 4: Andere Fehlkonfigurationen im Load Balancer
Wenn alle vorhergehenden Ursachen anscheinend ordnungsgemäß überprüft und aufgelöst wurden und die Back-End-VM noch immer nicht auf Integritätstests antwortet, dann testen Sie die Konnektivität manuell, und erfassen Sie einige Ablaufverfolgungen, um die Konnektivität zu analysieren.

**Überprüfung und Lösung**

* Verwenden Sie **Psping** von einer der anderen VMs im VNET, um die Testportantwort zu testen (Beispiel: .\psping.exe -t 10.0.0.4:3389), und zeichnen Sie die Ergebnisse auf. 
* Verwenden Sie **TCPing** von einer der anderen VMs im VNET, um die Testportantwort zu testen (Beispiel: .\tcping.exe 10.0.0.4 3389), und zeichnen Sie die Ergebnisse auf. 
* Wenn in diesen Pingtests keine Antwort empfangen wird, gehen Sie wie folgt vor:
    - Führen Sie eine gleichzeitige Netsh-Ablaufverfolgung für die Ziel-Back-End-Pool-VM und eine andere Test-VM im selben VNET aus. Führen Sie jetzt für einige Zeit einen PsPing-Test aus, erfassen Sie einige Netzwerk-Ablaufverfolgungen, und beenden Sie den Test. 
    - Analysieren Sie die erfassten Netzwerkdaten, und stellen Sie fest, ob es eingehende und ausgehende Pakete im Zusammenhang mit der Pingabfrage gibt. 
        - Wenn keine eingehenden Pakete auf der Back-End-Pool-VM festgestellt werden, blockiert möglicherweise die Fehlkonfiguration von Netzwerksicherheitsgruppen oder UDR den Datenverkehr. 
        - Wenn keine ausgehenden Pakete auf der Back-End-Pool-VM festgestellt werden, muss die VM auf andere Probleme (z. B. das Blockieren des Testports durch eine Anwendung) überprüft werden. 
    - Überprüfen Sie, ob die Testpakete zwangsweise an ein anderes Ziel gesendet werden (möglicherweise über UDR-Einstellungen), bevor sie den Load Balancer erreichen. Dies kann verursachen, dass der Datenverkehr die Back-End-VM nicht erreicht. 
* Ändern Sie den Testtyp (z.B. von HTTP in TCP), und konfigurieren Sie den entsprechenden Port in Netzwerksicherheitsgruppen-ACLs und der Firewall, um zu prüfen, ob das Problem in der Konfiguration der Testantwort liegt. Weitere Informationen zur Konfiguration von Integritätstests finden Sie unter [Endpoint Load Balancing health probe configuration](/archive/blogs/mast/endpoint-load-balancing-heath-probe-configuration-details) (Konfiguration von Integritätstests für den Endpunktlastenausgleich).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Symptom: VMs hinter dem Load Balancer antworten nicht auf Datenverkehr am konfigurierten Datenport

Wenn eine Back-End-Pool-VM als fehlerfrei aufgeführt ist und auf die Integritättests antwortet, jedoch noch immer nicht am Lastenausgleich teilnimmt oder nicht auf den Datenverkehr antwortet, kann einer der folgenden Gründe vorliegen: 
* Die VM des Load Balancer-Back-End-Pools lauscht nicht am Datenport 
* Eine Netzwerksicherheitsgruppe blockiert den Port auf der VM des Load Balancer-Back-End-Pools  
* Zugreifen auf den Load Balancer über die gleiche VM und NIC 
* Zugreifen auf das Internet Load Balancer-Front-End über die beteiligte VM des Load Balancer-Back-End-Pools 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Ursache 1: Die VM des Load Balancer-Back-End-Pools lauscht nicht am Datenport 
Wenn eine VM nicht auf den Datenverkehr antwortet, kann dies daran liegen, dass der Zielport auf der beteiligten VM nicht geöffnet ist oder dass die VM nicht an diesem Port lauscht. 

**Überprüfung und Lösung**

1. Melden Sie sich an der Back-End-VM an. 
2. Öffnen Sie eine Eingabeaufforderung, und führen Sie den folgenden Befehl aus, um zu überprüfen, ob eine Anwendung am Datenport lauscht:  
            netstat -an 
3. Wenn der Port nicht mit dem Status „EMPFANGSBEREIT“ aufgeführt ist, konfigurieren Sie den richtigen Listenerport. 
4. Wenn der Port als „Empfangsbereit“ markiert ist, überprüfen Sie die Zielanwendung an diesem Port auf mögliche Probleme.

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Ursache 2: Eine Netzwerksicherheitsgruppe blockiert den Port auf der VM des Load Balancer-Back-End-Pools  

Wenn eine oder mehrere im Subnetz oder auf der VM konfigurierte Netzwerksicherheitsgruppen die Quell-IP oder den Port blockieren, kann die VM nicht antworten.

Beim öffentlichen Load Balancer wird für die Kommunikation zwischen den Clients und den Back-End-VMs des Load Balancers die IP-Adresse der Internetclients verwendet. Stellen Sie sicher, dass die IP-Adressen der Clients in der Netzwerksicherheitsgruppe der Back-End-VMs zulässig sind.

1. Listen Sie die Netzwerksicherheitsgruppen auf, die auf der Back-End-VM konfiguriert sind. Weitere Informationen finden Sie unter [Verwalten von Netzwerksicherheitsgruppen](../virtual-network/manage-network-security-group.md).
1. Überprüfen Sie Folgendes anhand der Liste von Netzwerksicherheitsgruppen:
    - Beim eingehenden oder ausgehenden Datenverkehr am Datenport tritt eine Störung auf. 
    - Eine Regel **Alle verweigern** für Netzwerksicherheitsgruppen der NIC der VM oder des Subnetzes weist eine höhere Priorität als die Standardregel auf, die Load Balancer-Tests und -Datenverkehr zulässt (Netzwerksicherheitsgruppen müssen die Load Balancer-IP-Adresse 168.63.129.16 (Testport) zulassen).
1. Wenn eine der Regeln den Datenverkehr blockiert, entfernen und konfigurieren Sie diese Regeln, um den Datenverkehr zuzulassen.  
1. Testen Sie, ob die VM jetzt auf Integritätstests antwortet.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Ursache 3: Der Zugriff auf den Load Balancer erfolgt über die gleiche VM und Netzwerkschnittstelle 

Wenn die auf der Back-End-VM eines Load Balancers gehostete Anwendung versucht, auf eine andere Anwendung zuzugreifen, die auf der gleichen Back-End-VM gehostet wird und die gleiche Netzwerkschnittstelle verwendet, ist dies ein nicht unterstütztes Szenario, das fehl schlägt. 

**Lösung:** Sie können dieses Problem mit einer der folgenden Methoden beheben:
* Konfigurieren Sie separate Back-End-Pool-VMs für jede Anwendung. 
* Konfigurieren Sie die Anwendung in VMs mit zwei NICs, damit jede Anwendung eine eigene Netzwerkschnittstelle und IP-Adresse verwendet. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Ursache 4: Der Zugriff auf das Front-End des internen Load Balancers erfolgt über die beteiligte VM des Load Balancer-Back-End-Pools

Wenn ein interner Load Balancer innerhalb eines VNet konfiguriert wird und eine der teilnehmenden Back-End-VMs versucht, auf das Front-End des internen Load Balancers zuzugreifen, treten möglicherweise Fehler auf, wenn der Flow der Ausgangs-VM zugeordnet wird. Dieses Szenario wird nicht unterstützt.

**Auflösung:** Es gibt mehrere Möglichkeiten, die Blockierung für dieses Szenarios aufzuheben, einschließlich der Verwendung eines Proxys. Evaluieren Sie Application Gateway oder andere Proxys von Drittanbietern (z.B. nginx oder haproxy). Weitere Informationen zu Application Gateway finden Sie unter [Übersicht über Application Gateway](../application-gateway/overview.md).

**Details:** Interne Lastenausgleichsmodule führen für ausgehende Verbindungen keine Übersetzung für das Front-End eines internen Lastenausgleichsmoduls durch, da sich beide im privaten IP-Adressraum befinden. Über öffentliche Load Balancer werden [ausgehende Verbindungen](load-balancer-outbound-connections.md) von privaten IP-Adressen im virtuellen Netzwerk mit öffentlichen IP-Adressen bereitgestellt. Bei internen Load Balancern wird mit diesem Ansatz die potenzielle SNAT-Portüberlastung in einem eindeutigen internen IP-Adressraum vermieden, für den die Übersetzung nicht erforderlich ist.

Hierbei ergibt sich die folgende Nebenwirkung: Wenn ein von einem virtuellen Computer ausgehender Flow in den Back-End-Pool versucht, einen Flow zum Front-End der internen Load Balancer-Instanz im Pool auszuführen _und_ sich selbst zugewiesen ist, stimmen die beiden Verzweigungen des Flows nicht überein. Aufgrund dieser fehlenden Übereinstimmung tritt für den Flow ein Fehler auf. Der Flow ist erfolgreich, wenn er nicht dem virtuellen Computer im Back-End-Pool zugeordnet ist, die den Flow zum Front-End erstellt hat.

Wenn der Flow sich selbst zugeordnet ist, geht der ausgehende Flow zum Front-End scheinbar vom virtuellen Computer und der zugehörige eingehende Flow scheinbar vom virtuellen Computer an sich selbst aus. Aus Sicht des Gastbetriebssystems stimmen die eingehenden und ausgehenden Bestandteile desselben Flows innerhalb des virtuellen Computers nicht überein. Der TCP-Stack erkennt nicht, dass es sich bei diesen Hälften um Teile desselben Flows handelt. Die Quelle und das Ziel stimmen nicht überein. Wenn der Flow keinem anderen virtuellen Computer im Back-End-Pool zugeordnet ist, stimmen die Teile des Flows überein, und der virtuelle Computer kann auf den Flow antworten.

Das Symptom für dieses Szenario sind zeitweilige Verbindungstimeouts, die auftreten, wenn der Flow zu demselben Back-End zurückkehrt, vom dem er ursprünglich stammt. Zu den häufig genutzten Problemumgehungen gehören das Einfügen einer Proxyebene hinter dem internen Load Balancer und das Verwenden von DSR-Stilregeln (Direct Server Return). Weitere Informationen finden Sie unter [Mehrere Front-Ends für Azure Load Balancer](load-balancer-multivip-overview.md).

Sie können eine interne Load Balancer-Instanz mit dem Proxy eines Drittanbieters kombinieren oder die interne [Application Gateway](../application-gateway/overview.md)-Instanz für Proxyszenarien mit HTTP/HTTPS verwenden. Es ist zwar möglich, zum Beheben dieses Problems einen öffentlichen Load Balancer zu nutzen, aber das sich ergebende Szenario ist anfällig für [SNAT-Auslastung](load-balancer-outbound-connections.md). Nutzen Sie diesen zweiten Ansatz nur, wenn eine sorgfältige Verwaltung durchgeführt werden kann.

## <a name="symptom-cannot-change-backend-port-for-existing-lb-rule-of-a-load-balancer-which-has-vm-scale-set-deployed-in-the-backend-pool"></a>Symptom: Der Back-End-Port für die vorhandene LB-Regel eines Lastenausgleichs, der eine VM-Skalierungsgruppe im Back-End-Pool bereitgestellt hat, kann nicht geändert werden. 
### <a name="cause--the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-vm-scale-set"></a>Ursache: Der Back-End-Port kann nicht für eine Lastenausgleichsregel geändert werden, die von einem Integritätstest für den Lastenausgleich verwendet wird, auf den von der VM-Skalierungsgruppe verwiesen wird.
**Lösung** Um den Port zu ändern, können Sie den Integritätstest entfernen, indem Sie die VM-Skalierungsgruppe aktualisieren, den Port aktualisieren und dann den Integritätstest erneut konfigurieren.

## <a name="symptom-small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>Symptom: Geringer Datenverkehr wird noch über den Lastenausgleich geleitet, nachdem VMs aus dem Back-End-Pool des Lastenausgleichs entfernt wurden. 
### <a name="cause--vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>Ursache: Aus dem Back-End-Pool entfernte virtuelle Computer sollten keinen Datenverkehr mehr empfangen. Die geringe Menge an Netzwerkdatenverkehr könnte im Zusammenhang mit Speicher, DNS und andere Funktionen in Azure stehen. 
Um dies zu überprüfen, können Sie eine Netzwerküberwachung durchführen. Die für Ihre Blob-Speicherkonten verwendeten vollqualifizierten Namen (FQDN) werden in den Eigenschaften des jeweiligen Speicherkontos aufgeführt.  Auf einem virtuellen Computer innerhalb Ihres Azure-Abonnements können Sie einen nslookup-Vorgang durchführen, um die Azure IP zu ermitteln, die diesem Speicherkonto zugewiesen ist.

## <a name="additional-network-captures"></a>Zusätzliche Netzwerkerfassungen
Wenn Sie eine Supportanfrage öffnen möchten, erfassen Sie die folgenden Informationen, um eine schnellere Lösung zu ermöglichen. Wählen Sie eine einzelne Back-End-VM für die Durchführung der folgenden Tests aus:
- Verwenden Sie „Psping“ von einer der Back-End-VMs im VNET, um die Testportantwort zu testen (Beispiel: psping 10.0.0.4:3389), und zeichnen Sie die Ergebnisse auf. 
- Wenn bei diesen Pingtests keine Antwort empfangen wird, führen Sie beim Ausführen von „PsPing“ eine gleichzeitige Netsh-Ablaufverfolgung auf der Back-End-VM und der VNET-Test-VM aus, und beenden Sie dann die Netsh-Ablaufverfolgung. 
 
## <a name="symptom-load-balancer-in-failed-state"></a>Symptom: Load Balancer in fehlerhaftem Zustand 

**Lösung**

- Nachdem Sie die Ressource identifiziert haben, die sich in einem fehlerhaften Zustand befindet, wechseln Sie zum [Azure-Ressourcen-Explorer](https://resources.azure.com/), und identifizieren Sie die Ressource in diesem Zustand. 
- Aktualisieren Sie die Umschaltfläche oben rechts auf „Lesen/Schreiben“.
- Klicken Sie für die Ressource im fehlerhaften Zustand auf „Bearbeiten“.
- Klicken Sie auf „PUT“, gefolgt von „GET“, um sicherzustellen, dass der Bereitstellungszustand auf „Erfolgreich“ aktualisiert wurde.
- Sie können dann mit anderen Aktionen fortfahren, weil die Ressource nicht mehr fehlerhaft ist.


## <a name="next-steps"></a>Nächste Schritte

Sollte sich das Problem mit den oben genannten Schritten nicht beheben lassen, erstellen Sie ein [Supportticket](https://azure.microsoft.com/support/options/).