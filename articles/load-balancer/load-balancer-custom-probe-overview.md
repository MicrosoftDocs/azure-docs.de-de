---
title: Integritätstests zum Skalieren und Bereitstellen von Hochverfügbarkeit für Ihren Dienst
titleSuffix: Azure Load Balancer
description: In diesem Artikel erfahren Sie, wie Sie Integritätstests verwenden, um Instanzen hinter einem Azure Load Balancer zu überwachen.
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: a008d7b26738b9552a7a43ab026391bd9afe0aa8
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780942"
---
# <a name="load-balancer-health-probes"></a>Lastenausgleichs-Integritätstests

Wenn Sie Lastausgleichsregeln mit Azure Load Balancer verwenden, müssen Sie einen Integritätstest angeben, damit Load Balancer den Back-End-Endpunktstatus erkennen kann.  Die Konfiguration des Integritätstests und die Testantworten bestimmen, welche Back-End-Poolinstanzen neue Flows empfangen. Anhand von Integritätstests können Sie einen Fehler in einer Anwendung für einen Back-End-Endpunkt erkennen. Sie können auch eine benutzerdefinierte Antwort auf einen Integritätstest generieren und den Integritätstest zur Flusssteuerung verwenden, um die Last oder geplante Downtimes zu verwalten. Wenn ein einem Integritätstest ein Fehler auftritt, beendet der Load Balancer das Senden neuer Flows an die entsprechende fehlerhafte Instanz. Die ausgehende Konnektivität ist nicht betroffen, nur eingehende Konnektivität ist beeinträchtigt.

Integritätstests unterstützen mehrere Protokolle. Die Verfügbarkeit eines bestimmten Integritätstestprotokolls hängt von der Load Balancer-SKU ab.  Darüber hinaus variiert das Verhalten des Diensts je nach Load Balancer-SKU. Die Tabelle zeigt dies:

| | Standard-SKU | Basic-SKU |
| --- | --- | --- |
| **[Testtypen](#types)** | TCP, HTTP, HTTPS | TCP, HTTP |
| **[Verhalten bei Tests mit Fehlern](#probedown)** | Alle Tests mit Fehlern, alle TCP-Flows werden fortgesetzt. | Alle Tests mit Fehlern, alle TCP-Flows laufen ab. | 


>[!IMPORTANT]
>Lesen Sie dieses Dokument in seiner Gesamtheit, einschließlich der wichtigen [Hinweise zum Entwurf](#design) unten, um einen zuverlässigen Dienst zu erstellen.

>[!IMPORTANT]
>Load Balancer-Integritätstests stammen von der IP-Adresse 168.63.129.16 und dürfen nicht blockiert werden, damit Ihre Instanz beim Test als online markiert wird.  Einzelheiten finden Sie unter [Quell-IP-Adresse von Tests](#probesource).

>[!IMPORTANT]
>Unabhängig vom konfigurierten Timeout-Schwellenwert wird bei HTTP(S)-Integritätstests von Load Balancer automatisch eine Instanz getestet, wenn der Server einen anderen Statuscode als „HTTP 200 OK“ zurückgibt oder wenn die Verbindung durch ein TCP-Reset getrennt wird.

## <a name="probe-configuration"></a><a name="probes"></a>Testkonfiguration

Die Integritätstestkonfiguration umfasst folgende Elemente:

- Dauer des Intervalls zwischen einzelnen Tests
- Anzahl der Testantworten, die beobachtet werden müssen, bevor der Test in einen anderen Zustand übergeht.
- Protokoll des Tests
- Port des Tests
- HTTP-Pfad, der für HTTP GET bei Verwendung von HTTP(S)-Tests verwendet werden soll.

>[!NOTE]
>Wenn Sie Azure PowerShell, die Azure CLI, Vorlagen oder eine API verwenden, ist eine Testdefinition nicht obligatorisch, und ihr Vorhandensein wird nicht überprüft. Validierungstests werden nur bei Verwendung des Azure-Portals durchgeführt.

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>Verstehen des Anwendungssignals, Erkennen des Signals und Reaktion der Plattform

Anzahl der Testantworten, die auf beides angewendet wird

- Anzahl der erfolgreichen Tests, bei der eine Instanz als online gekennzeichnet werden kann und
- Anzahl der Tests mit einem Timeout, bei der eine Instanz als offline gekennzeichnet wird.

Die angegebenen Werte für das Timeout und das Intervall bestimmen, ob eine Instanz als online oder offline markiert wird.  Die Dauer des Intervalls multipliziert mit der Anzahl der Testantworten bestimmt die Zeitspann, in der die Testantworten erkannt werden müssen.  Und der Dienst reagiert, nachdem die erforderlichen Tests erreicht wurden.

Wir können das Verhalten anhand eines Beispiels weiter veranschaulichen. Wenn Sie die Anzahl der Testantworten auf 2 und das Intervall auf 5 Sekunden festgelegt haben, bedeutet dies, dass 2 Testfehler durch Timeouts innerhalb eines Intervalls von 10 Sekunden beobachtet werden müssen.  Da der Zeitpunkt, zu dem ein Test gesendet wird, nicht synchronisiert ist, wenn Ihre Anwendung den Zustand ggf. ändert, können wir die Zeit bis zur Erkennung an zwei Szenarien festmachen:

1. Wenn Ihre Anwendung kurz vor dem Eintreffen des ersten Tests mit der Generierung einer Testantwort aufgrund eines Timeouts beginnt, dauert die Erkennung dieser Ereignisse 10 Sekunden (Intervalle von 2 × 5 Sekunden) plus die Dauer der Anwendung, die beginnt, ein Timeout zu signalisieren, wenn der erste Test eintrifft.  Sie können davon ausgehen, dass diese Erkennung etwas länger als 10 Sekunden dauert.
2. Wenn Ihre Anwendung unmittelbar nach dem Eintreffen des ersten Tests mit der Generierung einer Testfehlerantwort aufgrund eines Timeouts beginnt, startet die Erkennung dieser Ereignisse erst mit dem Eintreffen des nächsten Tests (mit Timeout) plus weiteren 10 Sekunden (Intervalle von 2 × 5 Sekunden).  Sie können davon ausgehen, dass diese Erkennung fast 15 Sekunden in Anspruch nimmt.

In diesem Beispiel benötigt die Plattform nach der Erkennung eine kurze Zeit, um auf diese Änderung zu reagieren.  Dies bedeutet eine Abhängigkeit von mehreren Faktoren: 

1. Wann die Anwendung mit der Zustandsänderung beginnt und
2. wann diese Änderung erkannt wird und die erforderlichen Kriterien erfüllt sind (Anzahl der im angegebenen Intervall gesendeten Tests) und
3. wann die Erkennung über die Plattform kommuniziert wurde. 

Sie können davon ausgehen, dass die Reaktion auf eine Testantwort aufgrund eines Timeouts zwischen einem Minimum von etwas mehr als 10 Sekunden und einem Maximum von etwas mehr als 15 Sekunden dauert, um auf eine Änderung des Signals von der Anwendung zu reagieren.  Dieses Beispiel dient der Veranschaulichung des Geschehens, jedoch ist es nicht möglich, eine genaue Dauer über die in diesem Beispiel dargestellte grobe Orientierung hinaus vorherzusagen.

>[!NOTE]
>Der Integritätstest testet alle ausgeführten Instanzen im Back-End-Pool. Wenn eine Instanz beendet wird, wird sie erst nach ihrem erneuten Start wieder getestet.

## <a name="probe-types"></a><a name="types"></a>Testtypen

Das vom Integritätstest verwendete Protokoll kann für Folgendes konfiguriert werden:

- [TCP-Listener](#tcpprobe)
- [HTTP-Endpunkte](#httpprobe)
- [HTTPS-Endpunkte](#httpsprobe)

Die verfügbaren Protokolle sind von der verwendeten Load Balancer-SKU abhängig:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| **Standard-SKU** |    &#9989; |   &#9989; |   &#9989; |
| **Basic-SKU** |   &#9989; |   &#9989; | &#10060; |

### <a name="tcp-probe"></a><a name="tcpprobe"></a> TCP-Test

TCP-Tests leiten eine Verbindung über einen offenen Drei-Wege-TCP-Handshake mit dem definierten Port ein.  TCP-Tests beenden eine Verbindung mit einem Vier-Wege-TCP-Schließen-Handshake.

Das Mindestintervall für Tests beträgt 5 Sekunden, und die Mindestanzahl von fehlerhaften Antworten ist 2.  Die gesamte Dauer aller Intervalle darf 120 Sekunden nicht überschreiten.

TCP-Tests führen in folgenden Fällen zu Fehlern:
* Der TCP-Listener für die Instanz reagiert innerhalb des Zeitlimits gar nicht.  Wann der Test als nicht ausgeführt markiert wird, hängt von der konfigurierten Anzahl unbeantworteter Testanforderungen aufgrund eines Timeouts vor dem Markieren des Tests als nicht ausgeführt ab.
* Der Test empfängt ein TCP-Reset von der Instanz.

Im Folgenden wird veranschaulicht, wie Sie diese Art von Testkonfiguration in einer Resource Manager-Vorlage ausdrücken können:

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

### <a name="http--https-probe"></a><a name="httpprobe"></a> <a name="httpsprobe"></a> HTTP-/HTTPS-Test

>[!NOTE]
>Der HTTPS-Test ist nur für [Load Balancer Standard](./load-balancer-overview.md) verfügbar.

HTTP- und HTTPS-Tests basieren auf dem TCP-Test und geben eine HTTP GET-Anforderung mit dem angegebenen Pfad aus. Diese beiden Tests unterstützen für HTTP GET relative Pfade. HTTPS-Tests sind mit HTTP-Tests identisch, weisen jedoch zusätzlich einen Transport Layer Security-Wrapper (TLS, früher als SSL bezeichnet) auf. Der Integritätstest kennzeichnet die Instanz als online, wenn diese innerhalb des Zeitlimits mit dem HTTP-Statuscode 200 antwortet.  Bei diesem Integritätstest wird standardmäßig versucht, den konfigurierten Integritätstestport alle 15 Sekunden zu prüfen. Das minimale Testintervall beträgt 5 Sekunden. Die gesamte Dauer aller Intervalle darf 120 Sekunden nicht überschreiten.

HTTP/HTTPS-Tests eignen sich auch zum Implementieren Ihrer eigenen Logik, um Instanzen aus der Lastenausgleichsrotation zu entfernen, wenn der Testport auch der Listener für den Dienst selbst ist. Sie können z.B. eine Instanz entfernen, wenn sie über 90 % CPU beansprucht und einen anderen HTTP-Status als 200 zurückgibt. 

> [!NOTE] 
> Der HTTPS-Test erfordert die Verwendung von Zertifikaten mit einem minimalen Signaturhash von SHA256 in der gesamten Kette.

Wenn Sie Cloud Services verwenden und über Webrollen verfügen, die „w3wp.exe“ verwenden, erreichen Sie auch eine automatische Überwachung Ihrer Website. Fehler in Ihrem Websitecode geben einen anderen Status als 200 an den Lastenausgleichstest zurück.

HTTP-/HTTPS-Tests führen in folgenden Fällen zu Fehlern:
* Der Testendpunkt gibt einen anderen HTTP-Antwortcode als 200 zurück (z.B. 403, 404 oder 500). Dadurch wird der Integritätstest sofort als nicht ausgeführt markiert. 
* Der HTTP-Testendpunkt reagiert während des kleineren Zeitraums des Testintervalls und eines Zeitlimits von 30 Sekunden gar nicht. Möglicherweise bleiben mehrere Testanforderungen unbeantwortet, bevor der Test als nicht ausgeführt markiert wird und die Summe aller Timeoutintervalle erreicht wurde.
* Der Testendpunkt schließt die Verbindung über ein TCP-Reset.

Im Folgenden wird veranschaulicht, wie Sie diese Art von Testkonfiguration in einer Resource Manager-Vorlage ausdrücken können:

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

### <a name="guest-agent-probe-classic-only"></a><a name="guestagent"></a>Gast-Agent-Test (nur klassisch)

Clouddienstrollen (Workerrollen und Webrollen) verwenden standardmäßig einen Gast-Agent für die Testüberwachung.  Ein Gast-Agent-Test ist eine „Notfallkonfiguration“.  Verwenden Sie einen Integritätstest immer explizit mit einem TCP- oder HTTP-Test. Ein Gast-Agent-Test ist bei den meisten Anwendungsszenarien nicht so effektiv wie explizit definierte Tests.

Ein Gast-Agent-Test ist eine Überprüfung des Gast-Agents auf dem virtuellen Computer. Er lauscht dann und antwortet nur mit einer HTTP-OK-200-Antwort, wenn sich die Instanz im Zustand „Bereit“ befindet. (Andere Zustände sind „Beschäftigt“, „Wird wiederverwendet“ oder „Wird beendet“.)

Weitere Informationen finden Sie unter [Konfigurieren der Dienstdefinitionsdatei (CSDEF) für Integritätstests](/previous-versions/azure/reference/ee758710(v=azure.100)) oder [Erste Schritte durch Erstellen eines öffentlichen Lastenausgleichs für Clouddienste](/previous-versions/azure/load-balancer/load-balancer-get-started-internet-classic-cloud#check-load-balancer-health-status-for-cloud-services).

Wenn der Gast-Agent nicht mit dem HTTP-OK-Code 200 antwortet, kennzeichnet der Lastenausgleich die Instanz als nicht reagierend. Er sendet dann keine Flows mehr an diese Instanz. Das Lastenausgleichsmodul überprüft die Instanz weiterhin. 

Wenn der Gast-Agent mit dem HTTP-Code 200 antwortet, sendet das Lastenausgleichsmodul wieder Flows an diese Instanz.

Wenn Sie eine Webrolle verwenden, wird der Websitecode in der Regel in „w3wp.exe“ ausgeführt. Dieses Programm wird nicht von der Azure-Fabric oder vom Gast-Agent überwacht. Fehler in „w3wp.exe“ (z. B. HTTP 500-Antworten) werden dem Gast-Agent nicht gemeldet. Folglich nimmt der Lastenausgleich diese Instanz nicht aus der Rotation.

<a name="health"></a>
## <a name="probe-up-behavior"></a><a name="probehealth"></a>Verhalten bei erfolgreichen Tests

TCP-, HTTP- und HTTPS-Integritätstests werden in den folgenden Fällen als fehlerfrei eingestuft und markieren den Back-End-Endpunkt als fehlerfrei:

* Der Integritätstest ist nach dem Starten der VM erfolgreich.
* Die angegebene Anzahl von Tests, die erforderlich ist, um den Back-End-Endpunkt als fehlerfrei zu markieren, wurde erreicht.

Jeder Back-End-Endpunkt, der einen fehlerfreien Zustand erreicht hat, ist berechtigt, neue Streams zu empfangen.  

> [!NOTE]
> Wenn das Ergebnis des Integritätstests schwankt, wartet der Lastenausgleich länger, bevor der Back-End-Endpunkt erneut in den fehlerfreien Zustand versetzt wird. Diese zusätzliche Wartezeit schützt den Benutzer und die Infrastruktur und ist eine bewusste Richtlinie.

## <a name="probe-down-behavior"></a><a name="probedown"></a>Verhalten bei Tests mit Fehlern

### <a name="tcp-connections"></a>TCP-Verbindungen

Neue TCP-Verbindungen mit dem verbleibenden fehlerfreien Back-End-Endpunkt werden erfolgreich hergestellt.

Wenn beim Integritätstest eines Back-End-Endpunkts ein Fehler auftritt, bleiben die für diesen Back-End-Endpunkt eingerichteten TCP-Verbindungen bestehen.

Wenn bei allen Überprüfungen für sämtliche Instanzen in einem Back-End-Pool ein Fehler auftritt, werden keine neue Flows an den Back-End-Pool gesendet. Load Balancer Standard erlaubt aber die Fortführung der eingerichteten TCP-Flows.  Load Balancer Basic beendet alle vorhandenen TCP-Flows an den Back-End-Pool.
 
Load Balancer ist ein Pass-Through-Dienst (TCP-Verbindungen werden nicht beendet), und der Flow erfolgt immer zwischen dem Client und dem Gastbetriebssystem sowie der Anwendung der VM. Wenn bei einem Pool alle Tests zu Fehlern führen, reagiert das Front-End nicht auf Versuche zum Öffnen von TCP-Verbindungen (SYN), da kein fehlerfreier Back-End-Endpunkt zum Empfangen des Flows vorhanden ist, und es sendet eine SYN-ACK-Antwort.

### <a name="udp-datagrams"></a>UDP-Datagramme

UDP-Datagramme werden an fehlerfreie Back-End-Endpunkte übermittelt.

Das UDP ist verbindungslos und es werden kein Flusszustände für das UDP nachverfolgt. Wenn beim Integritätstest eines beliebigen Back-End-Endpunkts ein Fehler auftritt, werden vorhandene UDP-Flows an eine andere fehlerfreie Instanz im Back-End-Pool umgeleitet.

Wenn bei allen Tests für einen Back-End-Pool Fehler auftreten, werden alle vorhandenen UDP-Flows für Load Balancer Basic und Standard beendet.

<a name="source"></a>
## <a name="probe-source-ip-address"></a><a name="probesource"></a>Quell-IP-Adresse von Tests

Load Balancer verwendet einen verteilten Dienst für die Stichprobenentnahme für sein internes Integritätsmodell. Der Testdienst befindet sich auf jedem Host mit VMs und kann bedarfsgesteuert programmiert werden, um Integritätstests gemäß der Kundenkonfiguration zu generieren. Der Datenverkehr im Rahmen von Integritätstests findet direkt zwischen dem Testdienst, der den Integritätstest generiert, und der Kunden-VM statt. Bei allen Lastenausgleichs-Integritätstests lautet die Quell-IP-Adresse 168.63.129.16.  Sie können einen IP-Adressraum in einem VNET verwenden, bei dem es sich nicht um einen RFC1918-Adressraum handelt.  Die Verwendung einer global reservierten IP-Adresse im Besitz von Microsoft reduziert die Wahrscheinlichkeit eines IP-Adressenkonflikts mit dem IP-Adressraum, den Sie innerhalb des VNET verwenden.  Diese IP-Adresse ist in allen Regionen identisch und ändert sich nicht. Sie stellt kein Sicherheitsrisiko dar, da nur die interne Azure-Plattformkomponente ein Paket von dieser IP-Adresse senden kann. 

Das Diensttag „AzureLoadBalancer“ identifiziert diese IP-Quelladresse in Ihren [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md) und lässt Datenverkehr von Integritätstests standardmäßig zu.

Zusätzlich zu Load Balancer-Integritätstests [wird diese IP-Adresse für die folgenden Vorgänge verwendet](../virtual-network/what-is-ip-address-168-63-129-16.md):

- Ermöglichen der Kommunikation zwischen VM-Agent und Plattform, um zu signalisieren, dass sich der VM-Agent im Zustand „Bereit“ befindet.
- Ermöglichen der Kommunikation mit dem virtuellen DNS-Server, um für die Kunden eine gefilterte Namensauflösung bereitzustellen, die keine benutzerdefinierten DNS-Server definieren.  Diese Filterung stellt sicher, dass Kunden nur die Hostnamen ihrer Bereitstellung auflösen können.
- Ermöglichen des Abrufs einer dynamischen IP-Adresse vom DHCP-Dienst in Azure durch die VM.

## <a name="design-guidance"></a><a name="design"></a> Leitfaden für den Entwurf

Integritätstests werden verwendet, um Ihren Dienst resilient zu machen und die Skalierung des Diensts zu ermöglichen. Eine fehlerhafte Konfiguration oder ein schlechtes Entwurfsmuster können sich auf die Verfügbarkeit und Skalierbarkeit des Diensts auswirken. Lesen Sie dieses gesamte Dokument, und überlegen Sie, wie sich die Markierung der Testantwort als offline oder online auf Ihr Szenario und die Verfügbarkeit Ihres Anwendungsszenarios auswirkt.

Beim Entwerfen des Integritätsmodells für Ihre Anwendung sollten Sie einen Port eines Back-End-Endpunkts testen, der die Integrität dieser Instanz __und__ des von Ihnen bereitgestellten Anwendungsdiensts widerspiegelt.  Der Anwendungsport und der Testport müssen nicht identisch sein.  In einigen Szenarien kann es wünschenswert sein, dass der Testport und der Port, an dem Ihre Anwendung den Dienst bereitstellt, nicht identisch sind.  

Manchmal kann es nützlich sein, wenn Ihre Anwendung nicht nur eine Integritätstestantwort zur Erkennung der Anwendungsintegrität generiert, sondern Load Balancer auch direkt signalisiert, ob Ihre Instanz neue Flows empfangen soll.  Sie können die Testantwort bearbeiten, um es Ihrer Anwendung zu ermöglichen, einen Rückstau zu erzeugen und die Übermittlung neuer Flows an eine Instanz zu drosseln, indem der Integritätstest als fehlerhaft markiert wird, oder die Wartung der Anwendung vorzubereiten und den Ausgleich für Ihr Szenario zu initiieren.  Bei Verwendung von Load Balancer Standard können TCP-Flows bei einem Signal vom Typ [Test mit Fehlern](#probedown) immer fortgesetzt werden, bis das Leerlauftimeout erreicht oder die Verbindung geschlossen wird. 

Für den UDP-Lastenausgleich sollten Sie ein benutzerdefiniertes Integritätstestsignal vom Back-End-Endpunkt generieren und einen TCP-, HTTP- oder HTTPS-Integritätstest mit dem entsprechenden Listener als Ziel verwenden, um die Integrität der UDP-Anwendung widerzuspiegeln.

Bei Verwendung von [Lastenausgleichsregeln für Hochverfügbarkeitsports](load-balancer-ha-ports-overview.md) mit [Load Balancer Standard](./load-balancer-overview.md) wird für alle Ports ein Lastenausgleich ausgeführt, sodass eine Antwort vom Integritätstest den Status der gesamten Instanz widerspiegeln muss.

Verwenden Sie für einen Integritätstest der Instanz, die den Integritätstest empfängt, weder eine Übersetzung noch einen Proxy für eine andere Instanz in Ihrem VNET. Diese Konfiguration kann zu kaskadierenden Fehlern in Ihrem Szenario führen.  Stellen Sie sich folgendes Szenario vor: Eine Reihe von Appliances von einem Drittanbieter wird im Back-End-Pool einer Load Balancer-Ressource bereitgestellt, um Skalierung und Redundanz für die Appliance zu bieten. Der Integritätstest ist zum Testen eines Ports konfiguriert, für den die Drittanbieterappliance einen Proxy oder eine Übersetzung für andere VMs hinter der Appliance verwendet.  Wenn Sie den gleichen Port testen, den Sie verwenden, um Anforderungen zu übersetzen oder über einen Proxy an die anderen VMs hinter der Appliance zu senden, wird die Appliance selbst durch jede Testantwort von einer einzelnen VM hinter der Appliance als inaktiv markiert. Diese Konfiguration kann aufgrund eines einzelnen Back-End-Endpunkts hinter der Appliance zu einem kaskadierenden Fehler des gesamten Anwendungsszenarios führen.  Der Trigger kann ein zeitweiliger Testfehler sein, durch den Load Balancer das ursprüngliche Ziel (die Applianceinstanz) als offline markiert, wodurch wiederum das gesamte Anwendungsszenario deaktiviert werden kann. Testen Sie stattdessen die Integrität der Anwendung selbst. Die Auswahl des Tests zum Bestimmen des Integritätssignals ist ein wichtiger Aspekt für Szenarien mit virtuellen Netzwerkgeräten (Network Virtual Appliance, NVA). Fragen Sie den Anwendungshersteller, welches Integritätssignal für solche Szenarien geeignet ist.

Wenn Sie die [IP-Quelladresse](#probesource) des Tests in Ihren Firewallrichtlinien nicht zulassen, tritt ein Fehler beim Integritätstest auf, da Ihre Instanz nicht erreicht werden kann.  Daraufhin kennzeichnet das Lastenausgleichsmodul Ihre Instanz als offline, da beim Integritätstest ein Fehler aufgetreten ist.  Diese Fehlkonfiguration kann dazu führen, dass Ihr Szenario mit Lastenausgleich fehlschlägt.

Damit der Load Balancer-Integritätstest Ihre Instanz als online markieren kann, **müssen** Sie diese IP-Adresse in allen Azure-[Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md) und lokalen Firewallrichtlinien zulassen.  Standardmäßig enthält jede Netzwerksicherheitsgruppe das [Diensttag](../virtual-network/network-security-groups-overview.md#service-tags) „AzureLoadBalancer“, um Datenverkehr im Rahmen von Integritätstests zulassen.

Wenn Sie einen Integritätstestfehler überprüfen oder eine einzelne Instanz abgrenzen möchten, können Sie eine [Netzwerksicherheitsgruppe](../virtual-network/network-security-groups-overview.md) verwenden, um den Integritätstest explizit zu blockieren (Zielport oder [IP-Quelladresse](#probesource)), und den Fehler für einen Test simulieren.

Konfigurieren Sie Ihr VNET nicht mit dem für Microsoft reservierten IP-Adressbereich mit 168.63.129.16.  Solche Konfigurationen verursachen ein Konflikt mit der IP-Adresse des Integritätstests und können dazu führen, dass Ihr Szenario fehlschlägt.

Wenn Sie über mehrere Schnittstellen auf dem virtuellen Computer verfügen, müssen Sie sicherstellen, dass auf den Test über die Schnittstelle geantwortet wird, über die er empfangen wurde.  Möglicherweise müssen Sie diese Adresse auf der VM oder für jede Schnittstelle einzeln mittels Übersetzung der Quellnetzwerkadresse übersetzen.

Aktivieren Sie [TCP-Zeitstempel](https://tools.ietf.org/html/rfc1323) nicht.  Das Aktivieren von TCP-Zeitstempeln kann zu einem Fehler bei Integritätstests führen, weil TCP-Pakete vom TCP-Stapel des Gastbetriebssystems der VM gelöscht werden und Load Balancer den entsprechenden Endpunkt dadurch als offline markiert.  Standardmäßig werden TCP-Zeitstempel bei VM-Images mit verstärkter Sicherheit routinemäßig aktiviert. Sie müssen deaktiviert werden.

## <a name="monitoring"></a>Überwachung

Öffentliche und interne [Load Balancer Standard](./load-balancer-overview.md) stellen pro Endpunkt und Back-End-Endpunkt den Integritätsteststatus als mehrdimensionale Metriken über Azure Monitor bereit. Diese Metriken können von anderen Azure-Diensten oder Anwendungen von Partnern genutzt werden. 

Der öffentliche Load Balancer Basic stellt den Integritätsteststatus zusammengefasst pro Back-End-Pool über Azure Monitor-Protokolle bereit.  Azure Monitor-Protokolle steht für interne Load Balancer Basic-Instanzen nicht zur Verfügung.  Mit [Azure Monitor-Protokolle](load-balancer-monitor-log.md) können Sie den Testintegritätsstatus und die Testanzahl für den öffentlichen Lastenausgleich überprüfen. Die Protokollierung kann mit Power BI oder Azure Operational Insights verwendet werden, um Statistiken zum Integritätsstatus des Lastenausgleichs bereitzustellen.

## <a name="limitations"></a>Einschränkungen

- HTTPS-Tests bieten keine Unterstützung für die gegenseitige Authentifizierung mit einem Clientzertifikat.
- Gehen Sie davon aus, dass bei Integritätstests ein Fehler auftritt, wenn TCP-Zeitstempel aktiviert sind.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum [Load Balancer Standard](./load-balancer-overview.md).
- [Schnellstart: Erstellen eines öffentlichen Lastenausgleichs im Ressourcen-Manager mit PowerShell](quickstart-load-balancer-standard-public-powershell.md)
- [REST-API für Integritätstests](/rest/api/load-balancer/loadbalancerprobes/)
- Anfordern neuer Integritätstestfunktionen mit [Uservoice von Load Balancer](https://aka.ms/lbuservoice)
