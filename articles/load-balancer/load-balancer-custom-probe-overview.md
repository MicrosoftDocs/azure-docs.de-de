---
title: Verwenden von Azure Load Balancer-Integritätstests zum Skalieren und Bereitstellen von Hochverfügbarkeit für Ihren Dienst
titlesuffix: Azure Load Balancer
description: Erfahren Sie, wie Sie Integritätstests verwenden, um Instanzen hinter einem Lastenausgleichsmodul zu überwachen.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: kumud
ms.openlocfilehash: e488a4a6438279270f3d86dafa16c45eda184059
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65415707"
---
# <a name="load-balancer-health-probes"></a>Lastenausgleichs-Integritätstests

Azure Load Balancer bietet Integritätstests zur Verwendung mit Lastenausgleichsregeln.  Die Integritätstestkonfiguration und Testantworten bestimmen, welche Back-End-Poolinstanzen neue Flows empfangen. Anhand von Integritätstests können Sie einen Fehler in einer Anwendung auf einer Back-End-Instanz erkennen. Sie können auch eine benutzerdefinierte Antwort auf einen Integritätstest generieren und den Integritätstest zur Flusssteuerung verwenden, um die Last oder geplante Downtimes zu verwalten. Wenn ein einem Integritätstest ein Fehler auftritt, beendet der Load Balancer das Senden neuer Flows an die entsprechende fehlerhafte Instanz.

Integritätstests unterstützen mehrere Protokolle. Die Verfügbarkeit eines bestimmten Integritätstesttyps zur Unterstützung eines bestimmten Protokolls hängt von der Load Balancer-SKU ab.  Darüber hinaus variiert das Verhalten des Diensts je nach Load Balancer-SKU.

| | Standard-SKU | Basic-SKU |
| --- | --- | --- |
| [Testtypen](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Verhalten bei Tests mit Fehlern](#probedown) | Alle Tests mit Fehlern, alle TCP-Flows werden fortgesetzt. | Alle Tests mit Fehlern, alle TCP-Flows laufen ab. | 

> [!IMPORTANT]
> Load Balancer-Integritätstests stammen von der IP-Adresse 168.63.129.16 und dürfen nicht blockiert werden, damit Ihre Instanz beim Test als online markiert wird.  Einzelheiten finden Sie unter [Quell-IP-Adresse von Tests](#probesource).

## <a name="types"></a>Testtypen

Der Integritätstest kann mithilfe der folgenden drei Protokolle für Listener konfiguriert werden:

- [TCP-Listener](#tcpprobe)
- [HTTP-Endpunkte](#httpprobe)
- [HTTPS-Endpunkte](#httpsprobe)

Die verfügbaren Typen von Integritätstests sind abhängig von der ausgewählten Load Balancer-SKU unterschiedlich:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Standard-SKU |    &#9989; |   &#9989; |   &#9989; |
| Basic-SKU |   &#9989; |   &#9989; | &#10060; |

Unabhängig vom ausgewählten Typ kann mit Integritätstests ein beliebiger Port einer Back-End-Instanz (einschließlich des Ports, an dem der eigentliche Dienst bereitgestellt wird) überwacht werden.

### <a name="tcpprobe"></a> TCP-Test

TCP-Tests leiten eine Verbindung über einen offenen Drei-Wege-TCP-Handshake mit dem definierten Port ein.  TCP-Tests beenden eine Verbindung mit einem Vier-Wege-TCP-Schließen-Handshake.

Das Mindestintervall für Tests beträgt 5 Sekunden, und die Mindestanzahl von fehlerhaften Antworten ist 2.  Die gesamte Dauer aller Intervalle darf 120 Sekunden nicht überschreiten.

TCP-Tests führen in folgenden Fällen zu Fehlern:
* Der TCP-Listener für die Instanz reagiert innerhalb des Zeitlimits gar nicht.  Wann der Test als nicht ausgeführt markiert wird, hängt von der konfigurierten Anzahl unbeantworteter fehlerhafter Testanforderungen vor dem Markieren des Tests als nicht ausgeführt ab.
* Der Test empfängt ein TCP-Reset von der Instanz.

#### <a name="resource-manager-template"></a>Resource Manager-Vorlage

```json
    {
      "name": "tcp",
      "properties": {
        "protocol": "Tcp",
        "port": 1234,
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="httpprobe"></a> <a name="httpsprobe"></a> HTTP-/HTTPS-Test

> [!NOTE]
> Der HTTPS-Test ist nur für [Load Balancer Standard](load-balancer-standard-overview.md) verfügbar.

HTTP- und HTTPS-Tests basieren auf dem TCP-Test und geben eine HTTP GET-Anforderung mit dem angegebenen Pfad aus. Diese beiden Tests unterstützen für HTTP GET relative Pfade. HTTPS-Tests sind mit HTTP-Tests identisch, weisen jedoch zusätzlich einen Transport Layer Security-Wrapper (TLS, früher als SSL bezeichnet) auf. Der Integritätstest kennzeichnet die Instanz als online, wenn diese innerhalb des Zeitlimits mit dem HTTP-Statuscode 200 antwortet.  Bei diesem Integritätstest wird standardmäßig versucht, den konfigurierten Integritätstestport alle 15 Sekunden zu prüfen. Das minimale Testintervall beträgt 5 Sekunden. Die gesamte Dauer aller Intervalle darf 120 Sekunden nicht überschreiten.

HTTP-/HTTPS-Tests können auch nützlich sein, wenn Sie einen schnellen Integritätstest ausführen möchten.  Implementieren Sie eine eigene Logik, um Instanzen aus der Lastenausgleichsrotation zu entfernen, wenn der Testport auch der Listener für den Dienst selbst ist. Sie können z.B. eine Instanz entfernen, wenn sie über 90 % CPU beansprucht und einen anderen HTTP-Status als 200 zurückgibt. 

Wenn Sie Cloud Services verwenden und über Webrollen verfügen, die „w3wp.exe“ verwenden, erreichen Sie auch eine automatische Überwachung Ihrer Website. Fehler in Ihrem Websitecode geben einen anderen Status als 200 an den Lastenausgleichstest zurück.

HTTP-/HTTPS-Tests führen in folgenden Fällen zu Fehlern:
* Der Testendpunkt gibt einen anderen HTTP-Antwortcode als 200 zurück (z.B. 403, 404 oder 500). Dadurch wird der Integritätstest sofort als nicht ausgeführt markiert. 
* Der Testendpunkt reagiert während des Zeitlimits von 31 Sekunden gar nicht. Möglicherweise bleiben mehrere Testanforderungen unbeantwortet, bevor der Test als nicht ausgeführt markiert wird und die Summe aller Timeoutintervalle erreicht wurde.
* Der Testendpunkt schließt die Verbindung über ein TCP-Reset.

#### <a name="resource-manager-templates"></a>Resource Manager-Vorlagen

```json
    {
      "name": "http",
      "properties": {
        "protocol": "Http",
        "port": 80,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

```json
    {
      "name": "https",
      "properties": {
        "protocol": "Https",
        "port": 443,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="guestagent"></a>Gast-Agent-Test (nur klassisch)

Clouddienstrollen (Workerrollen und Webrollen) verwenden standardmäßig einen Gast-Agent für die Testüberwachung.  Ein Gast-Agent-Test ist eine „Notfallkonfiguration“.  Verwenden Sie einen Integritätstest immer explizit mit einem TCP- oder HTTP-Test. Ein Gast-Agent-Test ist bei den meisten Anwendungsszenarien nicht so effektiv wie explizit definierte Tests.

Ein Gast-Agent-Test ist eine Überprüfung des Gast-Agents auf dem virtuellen Computer. Er lauscht dann und antwortet nur mit einer HTTP-OK-200-Antwort, wenn sich die Instanz im Zustand „Bereit“ befindet. (Andere Zustände sind „Beschäftigt“, „Wird wiederverwendet“ oder „Wird beendet“.)

Weitere Informationen finden Sie unter [Konfigurieren der Dienstdefinitionsdatei (CSDEF) für Integritätstests](https://msdn.microsoft.com/library/azure/ee758710.aspx) oder [Erste Schritte durch Erstellen eines öffentlichen Lastenausgleichs für Clouddienste](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Wenn der Gast-Agent nicht mit dem HTTP-OK-Code 200 antwortet, kennzeichnet der Lastenausgleich die Instanz als nicht reagierend. Er sendet dann keine Flows mehr an diese Instanz. Das Lastenausgleichsmodul überprüft die Instanz weiterhin. 

Wenn der Gast-Agent mit dem HTTP-Code 200 antwortet, sendet das Lastenausgleichsmodul wieder Flows an diese Instanz.

Wenn Sie eine Webrolle verwenden, wird der Websitecode in der Regel in „w3wp.exe“ ausgeführt. Dieses Programm wird nicht von der Azure-Fabric oder vom Gast-Agent überwacht. Fehler in „w3wp.exe“ (z. B. HTTP 500-Antworten) werden dem Gast-Agent nicht gemeldet. Folglich nimmt der Lastenausgleich diese Instanz nicht aus der Rotation.

<a name="health"></a>
## <a name="probehealth"></a>Testen der Integrität

TCP-, HTTP- und HTTPS-Integritätstests werden in den folgenden Fällen als fehlerfrei eingestuft und markieren die Rolleninstanz als fehlerfrei:

* Der Integritätstest ist nach dem Starten der VM erfolgreich.
* Die angegebene Anzahl von Tests, die erforderlich ist, um die Rolleninstanz als fehlerfrei zu markieren, wurde erreicht.

> [!NOTE]
> Wenn das Ergebnis des Integritätstests schwankt, wartet der Lastenausgleich länger, bevor die Rolleninstanz wieder in den fehlerfreien Zustand versetzt wird. Diese zusätzliche Wartezeit schützt den Benutzer und die Infrastruktur und ist eine bewusste Richtlinie.

## <a name="probe-count-and-timeout"></a>Anzahl und Timeout von Tests

Das Verhalten von Tests hängt von folgenden Faktoren ab:

* Anzahl der erfolgreichen Tests, bei der eine Instanz als online gekennzeichnet werden kann
* Anzahl der fehlerhaften Tests, bei der eine Instanz als offline gekennzeichnet wird

Die angegebenen Werte für das Timeout und das Intervall bestimmen, ob eine Instanz als online oder offline markiert wird.

## <a name="probedown"></a>Verhalten bei Tests mit Fehlern

### <a name="tcp-connections"></a>TCP-Verbindungen

Neue TCP-Verbindungen mit den verbleibenden fehlerfreien Back-End-Instanzen werden erfolgreich hergestellt.

Wenn beim Integritätstest einer Back-End-Instanz ein Fehler auftritt, bleiben die für diese Back-End-Instanz eingerichteten TCP-Verbindungen bestehen.

Wenn bei allen Überprüfungen für sämtliche Instanzen in einem Back-End-Pool ein Fehler auftritt, werden keine neue Flows an den Back-End-Pool gesendet. Load Balancer Standard erlaubt aber die Fortführung der eingerichteten TCP-Flows.  Load Balancer Basic beendet alle vorhandenen TCP-Flows an den Back-End-Pool.
 
Load Balancer ist ein Pass-Through-Dienst (TCP-Verbindungen werden nicht beendet), und der Flow erfolgt immer zwischen dem Client und dem Gastbetriebssystem sowie der Anwendung der VM. Wenn bei einem Pool alle Tests zu Fehlern führen, reagiert das Front-End nicht auf Versuche zum Öffnen von TCP-Verbindungen (SYN), da keine fehlerfreie Back-End-Instanz zum Empfangen des Flows vorhanden ist, und es sendet eine SYN-ACK-Antwort.

### <a name="udp-datagrams"></a>UDP-Datagramme

UDP-Datagramme werden an fehlerfreie Back-End-Instanzen übermittelt.

Das UDP ist verbindungslos und es werden kein Flusszustände für das UDP nachverfolgt. Wenn beim Integritätstest einer beliebigen Back-End-Instanz ein Fehler auftritt, können vorhandene UDP-Flows an eine andere fehlerfreie Instanz im Back-End-Pool umgeleitet werden.

Wenn bei allen Tests für einen Back-End-Pool Fehler auftreten, werden alle vorhandenen UDP-Flows für Load Balancer Basic und Standard beendet.

<a name="source"></a>
## <a name="probesource"></a>Quell-IP-Adresse von Tests

Load Balancer verwendet einen verteilten Dienst für die Stichprobenentnahme für sein internes Integritätsmodell. Der Testdienst befindet sich auf jedem Host mit VMs und kann bedarfsgesteuert programmiert werden, um Integritätstests gemäß der Kundenkonfiguration zu generieren. Der Datenverkehr im Rahmen von Integritätstests findet direkt zwischen dem Testdienst, der den Integritätstest generiert, und der Kunden-VM statt. Bei allen Lastenausgleichs-Integritätstests lautet die Quell-IP-Adresse 168.63.129.16.  Sie können einen IP-Adressraum in einem VNET verwenden, bei dem es sich nicht um einen RFC1918-Adressraum handelt.  Die Verwendung einer global reservierten IP-Adresse im Besitz von Microsoft reduziert die Wahrscheinlichkeit eines IP-Adressenkonflikts mit dem IP-Adressraum, den Sie innerhalb des VNET verwenden.  Diese IP-Adresse ist in allen Regionen identisch und ändert sich nicht. Sie stellt kein Sicherheitsrisiko dar, da nur die interne Azure-Plattformkomponente ein Paket von dieser IP-Adresse senden kann. 

Das Diensttag „AzureLoadBalancer“ identifiziert diese IP-Quelladresse in Ihren [Netzwerksicherheitsgruppen](../virtual-network/security-overview.md) und lässt Datenverkehr von Integritätstests standardmäßig zu.

Zusätzlich zu Load Balancer-Integritätstests [wird diese IP-Adresse für die folgenden Vorgänge verwendet](../virtual-network/what-is-ip-address-168-63-129-16.md):

- Ermöglichen der Kommunikation zwischen VM-Agent und Plattform, um zu signalisieren, dass sich der VM-Agent im Zustand „Bereit“ befindet.
- Ermöglichen der Kommunikation mit dem virtuellen DNS-Server, um für die Kunden eine gefilterte Namensauflösung bereitzustellen, die keine benutzerdefinierten DNS-Server definieren.  Diese Filterung stellt sicher, dass Kunden nur die Hostnamen ihrer Bereitstellung auflösen können.
- Ermöglichen des Abrufs einer dynamischen IP-Adresse vom DHCP-Dienst in Azure durch die VM.

## <a name="design"></a> Leitfaden für den Entwurf

Integritätstests werden verwendet, um Ihren Dienst resilient zu machen und die Skalierung des Diensts zu ermöglichen. Eine fehlerhafte Konfiguration oder ein schlechtes Entwurfsmuster können sich auf die Verfügbarkeit und Skalierbarkeit des Diensts auswirken. Lesen Sie dieses gesamte Dokument, und überlegen Sie, wie sich die Markierung der Testantwort als offline oder online auf Ihr Szenario und die Verfügbarkeit Ihres Anwendungsszenarios auswirkt.

Beim Entwerfen des Integritätsmodells für Ihre Anwendung sollten Sie einen Port einer Back-End-Instanz testen, der die Integrität dieser Instanz __und__ des von Ihnen bereitgestellten Anwendungsdiensts widerspiegelt.  Der Anwendungsport und der Testport müssen nicht identisch sein.  In einigen Szenarien kann es wünschenswert sein, dass der Testport und der Port, an dem Ihre Anwendung den Dienst bereitstellt, nicht identisch sind.  

Manchmal kann es nützlich sein, wenn Ihre Anwendung nicht nur eine Integritätstestantwort zur Erkennung der Anwendungsintegrität generiert, sondern Load Balancer auch direkt signalisiert, ob Ihre Instanz neue Flows empfangen soll.  Sie können die Testantwort bearbeiten, um es Ihrer Anwendung zu ermöglichen, einen Rückstau zu erzeugen und die Übermittlung neuer Flows an eine Instanz zu drosseln, indem der Integritätstest als fehlerhaft markiert wird, oder die Wartung der Anwendung vorzubereiten und den Ausgleich für Ihr Szenario zu initiieren.  Bei Verwendung von Load Balancer Standard können TCP-Flows bei einem Signal vom Typ [Test mit Fehlern](#probedown) immer fortgesetzt werden, bis das Leerlauftimeout erreicht oder die Verbindung geschlossen wird. 

Für den UDP-Lastenausgleich sollten Sie ein benutzerdefiniertes Integritätstestsignal von der Back-End-Instanz generieren und einen TCP-, HTTP- oder HTTPS-Integritätstest mit dem entsprechenden Listener als Ziel verwenden, um die Integrität der UDP-Anwendung widerzuspiegeln.

Bei Verwendung von [Lastenausgleichsregeln für Hochverfügbarkeitsports](load-balancer-ha-ports-overview.md) mit [Load Balancer Standard](load-balancer-standard-overview.md) wird für alle Ports ein Lastenausgleich ausgeführt, sodass eine Antwort vom Integritätstest den Status der gesamten Instanz widerspiegeln muss.

Verwenden Sie für einen Integritätstest der Instanz, die den Integritätstest empfängt, weder eine Übersetzung noch einen Proxy für eine andere Instanz in Ihrem VNET. Diese Konfiguration kann zu kaskadierenden Fehlern in Ihrem Szenario führen.  Stellen Sie sich folgendes Szenario vor: Eine Reihe von Appliances von einem Drittanbieter wird im Back-End-Pool einer Load Balancer-Ressource bereitgestellt, um Skalierung und Redundanz für die Appliance zu bieten. Der Integritätstest ist zum Testen eines Ports konfiguriert, für den die Drittanbieterappliance einen Proxy oder eine Übersetzung für andere VMs hinter der Appliance verwendet.  Wenn Sie den gleichen Port testen, den Sie verwenden, um Anforderungen zu übersetzen oder über einen Proxy an die anderen VMs hinter der Appliance zu senden, wird die Appliance selbst durch jede Testantwort von einer einzelnen VM hinter der Appliance als inaktiv markiert. Diese Konfiguration kann aufgrund einer einzelnen Back-End-Instanz hinter der Appliance zu einem kaskadierenden Fehler des gesamten Anwendungsszenarios führen.  Der Trigger kann ein zeitweiliger Testfehler sein, durch den Load Balancer das ursprüngliche Ziel (die Applianceinstanz) als offline markiert, wodurch wiederum das gesamte Anwendungsszenario deaktiviert werden kann. Testen Sie stattdessen die Integrität der Anwendung selbst. Die Auswahl des Tests zum Bestimmen des Integritätssignals ist ein wichtiger Aspekt für Szenarien mit virtuellen Netzwerkgeräten (Network Virtual Appliance, NVA). Fragen Sie den Anwendungshersteller, welches Integritätssignal für solche Szenarien geeignet ist.

Wenn Sie die [IP-Quelladresse](#probesource) des Tests in Ihren Firewallrichtlinien nicht zulassen, tritt ein Fehler beim Integritätstest auf, da Ihre Instanz nicht erreicht werden kann.  Daraufhin kennzeichnet das Lastenausgleichsmodul Ihre Instanz als offline, da beim Integritätstest ein Fehler aufgetreten ist.  Diese Fehlkonfiguration kann dazu führen, dass Ihr Szenario mit Lastenausgleich fehlschlägt.

Damit der Load Balancer-Integritätstest Ihre Instanz als online markieren kann, **müssen** Sie diese IP-Adresse in allen Azure-[Netzwerksicherheitsgruppen](../virtual-network/security-overview.md) und lokalen Firewallrichtlinien zulassen.  Standardmäßig enthält jede Netzwerksicherheitsgruppe das [Diensttag](../virtual-network/security-overview.md#service-tags) „AzureLoadBalancer“, um Datenverkehr im Rahmen von Integritätstests zulassen.

Wenn Sie einen Integritätstestfehler überprüfen oder eine einzelne Instanz abgrenzen möchten, können Sie eine [Netzwerksicherheitsgruppe](../virtual-network/security-overview.md) verwenden, um den Integritätstest explizit zu blockieren (Zielport oder [IP-Quelladresse](#probesource)), und den Fehler für einen Test simulieren.

Konfigurieren Sie Ihr VNET nicht mit dem für Microsoft reservierten IP-Adressbereich mit 168.63.129.16.  Solche Konfigurationen verursachen ein Konflikt mit der IP-Adresse des Integritätstests und können dazu führen, dass Ihr Szenario fehlschlägt.

Wenn Sie über mehrere Schnittstellen auf dem virtuellen Computer verfügen, müssen Sie sicherstellen, dass auf den Test über die Schnittstelle geantwortet wird, über die er empfangen wurde.  Möglicherweise müssen Sie diese Adresse auf der VM oder für jede Schnittstelle einzeln mittels Übersetzung der Quellnetzwerkadresse übersetzen.

Aktivieren Sie [TCP-Zeitstempel](https://tools.ietf.org/html/rfc1323) nicht.  Das Aktivieren von TCP-Zeitstempeln führt zu einem Fehler bei Integritätstests, weil TCP-Pakete vom TCP-Stapel des Gastbetriebssystems der VM gelöscht werden und Load Balancer den entsprechenden Endpunkt dadurch als offline markiert.  Standardmäßig werden TCP-Zeitstempel bei VM-Images mit verstärkter Sicherheit routinemäßig aktiviert. Sie müssen deaktiviert werden.

## <a name="monitoring"></a>Überwachung

Öffentliche und interne [Load Balancer Standard](load-balancer-standard-overview.md) stellen pro Endpunkt und Back-End-Instanz den Integritätsteststatus als mehrdimensionale Metriken über Azure Monitor bereit. Diese Metriken können von anderen Azure-Diensten oder Anwendungen von Partnern genutzt werden. 

Der öffentliche Load Balancer Basic stellt den Integritätsteststatus zusammengefasst pro Back-End-Pool über Azure Monitor-Protokolle bereit.  Azure Monitor-Protokolle steht für interne Load Balancer Basic-Instanzen nicht zur Verfügung.  Mit [Azure Monitor-Protokolle](load-balancer-monitor-log.md) können Sie den Testintegritätsstatus und die Testanzahl für den öffentlichen Lastenausgleich überprüfen. Die Protokollierung kann mit Power BI oder Azure Operational Insights verwendet werden, um Statistiken zum Integritätsstatus des Lastenausgleichs bereitzustellen.

## <a name="limitations"></a>Einschränkungen

- HTTPS-Tests bieten keine Unterstützung für die gegenseitige Authentifizierung mit einem Clientzertifikat.
- Bei Integritätstests tritt ein Fehler auf, wenn TCP-Zeitstempel aktiviert sind.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum [Load Balancer Standard](load-balancer-standard-overview.md).
- [Schnellstart: Erstellen eines öffentlichen Lastenausgleichs im Ressourcen-Manager mit PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [REST-API für Integritätstests](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Anfordern neuer Integritätstestfunktionen mit [Uservoice von Load Balancer](https://aka.ms/lbuservoice)
