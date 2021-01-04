---
title: Optimierung der TCP/IP-Leistung für Azure-VMs | Microsoft-Dokumentation
description: Lernen Sie verschiedene allgemeine Techniken zur TCP/IP-Leistungsoptimierung und ihre Beziehung zu Azure-VMs kennen.
services: virtual-network
documentationcenter: na
author: rimayber
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: rimayber
ms.reviewer: dgoddard, stegag, steveesp, minale, btalb, prachank
ms.openlocfilehash: 67b635f09cb9407279e89b5f7b8526dab3c08946
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017609"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>Optimierung der TCP/IP-Leistung für Azure-VMs

In diesem Artikel werden gängige Techniken zur TCP/IP-Leistungsoptimierung sowie einige Punkte beschrieben, die zu beachten sind, wenn Sie diese Techniken für virtuelle Computer verwenden, die unter Azure ausgeführt werden. Der Artikel enthält eine grundlegende Übersicht über die Techniken, und es wird erläutert, wie diese optimiert werden können.

## <a name="common-tcpip-tuning-techniques"></a>Allgemeine Techniken zur TCP/IP-Leistungsoptimierung

### <a name="mtu-fragmentation-and-large-send-offload"></a>Maximale Übertragungseinheit (MTU), Fragmentierung und Large Send Offload

#### <a name="mtu"></a>MTU

Die maximale Übertragungseinheit (MTU) ist der größte in Bytes angegebene Frame (Paket), der über eine Netzwerkschnittstelle gesendet werden kann. Die MTU ist eine konfigurierbare Einstellung. Die Standard-MTU, die auf Azure-VMs verwendet wird, und die Standardeinstellung auf den meisten Netzwerkgeräten weltweit, beträgt 1.500 Bytes.

#### <a name="fragmentation"></a>Fragmentierung

Fragmentierung tritt auf, wenn ein Paket gesendet wird, dessen Größe die MTU einer Netzwerkschnittstelle überschreitet. Der TCP/IP-Stapel zerlegt das Paket in kleinere Teile (Fragmente), die der MTU der Schnittstelle entsprechen. Die Fragmentierung erfolgt auf der IP-Ebene und ist unabhängig vom zugrunde liegenden Protokoll (z.B. TCP). Wenn ein 2.000-Byte-Paket über eine Netzwerkschnittstelle mit einer MTU von 1.500 gesendet wird, wird das Paket in ein 1.500-Byte-Paket und ein 500-Byte-Paket zerlegt.

Netzwerkgeräte im Pfad zwischen Quelle und Ziel können Pakete, die die MTU überschreiten, entweder verwerfen oder in kleinere Teile zerlegen.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>Das „Don‘t Fragment“-Bit in einem IP-Paket

Das „Don‘t Fragment“-Bit (DF-Bit) ist ein Flag im IP-Protokollheader. Das DF-Bit kennzeichnet, dass Netzwerkgeräte auf dem Weg zwischen dem Sender und dem Empfänger das Paket nicht fragmentieren dürfen. Dieses Bit kann aus vielen Gründen festgelegt sein. (Ein Beispiel finden Sie in diesem Artikel im Abschnitt „Path MTU Discovery“.) Wenn ein Netzwerkgerät ein Paket mit gesetztem „Don‘t Fragment“-Bit empfängt und dieses Paket die MTU der Geräteschnittstelle überschreitet, besteht das Standardverhalten des Geräts darin, das Paket zu verwerfen. Das Gerät sendet eine „ICMP Fragmentation Needed“-Nachricht an die ursprüngliche Quelle des Pakets.

#### <a name="performance-implications-of-fragmentation"></a>Leistungsauswirkungen der Fragmentierung

Eine Fragmentierung kann sich negativ auf die Leistung auswirken. Einer der Hauptgründe für die Auswirkungen auf die Leistung sind die Auswirkungen, die die Fragmentierung und Neuzusammensetzung von Paketen auf CPU und Arbeitsspeicher hat. Wenn ein Netzwerkgerät ein Paket fragmentieren muss, muss es CPU-Speicherressourcen zuweisen, um eine Fragmentierung durchzuführen.

Das Gleiche passiert, wenn das Paket wieder zusammengesetzt wird. Das Netzwerkgerät muss alle Fragmente speichern, bis sie empfangen sind. Erst dann kann es sie wieder zum ursprünglichen Paket zusammensetzen. Dieser Prozess der Fragmentierung und Neuzusammensetzung kann auch zu Wartezeit führen.

Die andere mögliche negative Auswirkung der Fragmentierung auf die Leistung ist, dass fragmentierte Pakete möglicherweise nicht in der richtigen Reihenfolge ankommen. Wenn Pakete in falscher Reihenfolge empfangen werden, kann es passieren, dass einige Arten von Netzwerkgeräten sie verwerfen. In diesem Fall muss das gesamte Paket erneut gesendet werden.

Fragmente werden üblicherweise von Sicherheitsvorrichtungen wie Netzwerkfirewalls verworfen oder dann verworfen, wenn die Empfangspuffer eines Netzwerkgeräts voll sind. Wenn die Empfangspuffer eines Netzwerkgeräte erschöpft sind, versucht eine Netzwerkgerät, ein fragmentiertes Paket wieder zusammenzusetzen, verfügt aber nicht über die Ressourcen, um das Paket zu speichern und wieder aufzunehmen.

Fragmentierung kann als negativer Vorgang angesehen werden, aber Unterstützung für Fragmentierung ist notwendig, um verschiedene Netzwerke über das Internet zu verbinden.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Vorteile und Konsequenzen einer Änderung der MTU

Generell lässt sich feststellen, dass Sie durch Vergrößern der MTU ein effizienteres Netzwerk aufbauen können. Jedes Paket, das übertragen wird, hat Headerinformationen, die dem ursprünglichen Paket hinzugefügt werden. Wenn Fragmentierung weitere Pakete erstellt, gibt es zusätzlichen Headeraufwand, und dieser bewirkt eine Verringerung der Netzwerkeffizienz.

Hier sehen Sie ein Beispiel. Die Ethernet-Headergröße beträgt 14 Bytes plus einer 4-Byte-Blockprüfzeichenfolge (Frame Check Sequence), um die Konsistenz eines Pakets sicherzustellen. Wird ein 2.000-Byte-Paket gesendet, werden 18 Bytes an Ethernet-Aufwand im Netzwerk hinzugefügt. Wird das Paket in ein 1.500-Byte-Paket und ein 500-Byte-Paket fragmentiert, hat jedes Paket einen Ethernet-Header von 18 Bytes, zusammen also 36 Bytes.

Eine Vergrößerung der MTU (Maximum Transmission Unit, maximale Übertragungseinheit) führt aber nicht notwendigerweise zu einem effizienteren Netzwerk. Wenn eine Anwendung nur 500-Byte-Pakete sendet, gibt es den gleichen Headeraufwand, unabhängig davon, ob die MTU gleich 1.500 Bytes oder 9.000 Bytes ist. Das Netzwerk wird nur effizienter, wenn in ihm größere Paketgrößen verwendet werden, auf die sich die MTU auswirkt.

#### <a name="azure-and-vm-mtu"></a>Azure und VM-MTU

Die Standard-MTU für Azure-VMs ist 1.500 Bytes. Der Azure Virtual Network-Stapel versucht, ein Paket auf 1.400 Bytes zu fragmentieren.

Beachten Sie, dass der Virtual Network-Stapel nicht von sich aus ineffizient ist, weil er Pakete bei 1.400 Bytes fragmentiert, obwohl virtuelle Computer eine MTU von 1.500 haben. Ein großer Prozentsatz der Netzwerkpakete ist wesentlich kleiner ist als 1.400 oder 1.500 Bytes.

#### <a name="azure-and-fragmentation"></a>Azure und Fragmentierung

Der Virtual Network-Stapel ist so konfiguriert, dass er „Fragmente in falscher Reihenfolge“, also fragmentierte Pakete, die nicht in ihrer ursprünglichen fragmentierten Reihenfolge ankommen, verwirft. Diese Pakete werden hauptsächlich aufgrund einer im November 2018 veröffentlichten Sicherheitslücke namens FragmentSmack verworfen.

FragmentSmack ist ein Fehler in der Art und Weise, wie der Linux-Kernel die Neuzusammensetzung von fragmentierten IPv4- und IPv6-Paketen verarbeitet. Ein externer Angreifer könnte diesen Fehler nutzen, um teure Vorgänge zur Neuzusammenstellung von Fragmenten auszulösen, was zu einer erhöhten CPU-Last und einem Denial of Service auf dem Zielsystem führen kann.

#### <a name="tune-the-mtu"></a>Optimieren der MTU

Sie können eine Azure-VM-MTU konfigurieren, wie Sie dies in allen anderen Betriebssystemen können. Aber Sie sollten die weiter oben beschriebene Fragmentierung, die in Azure auftritt, beachten, wenn Sie eine MTU konfigurieren.

Es wird Kunden nicht empfohlen, die VM-MTUs zu erhöhen. Ziel dieser Diskussion ist es, zu erläutern, wie MTU in Azure implementiert ist und wie Fragmentierung in Azure ausgeführt wird.

> [!IMPORTANT]
>Ein Vergrößern der MTU hat keine Leistungssteigerung gezeigt und könnte sich negativ auf die Anwendungsleistung auswirken.
>
>

#### <a name="large-send-offload"></a>Large Send Offload

Large Send Offload (LSO) kann die Netzwerkleistung verbessern, indem die Segmentierung der Pakete dem Ethernet-Adapter überlassen wird. Wenn LSO aktiviert ist, erstellt der TCP/IP-Stapel ein großes TCP-Paket und sendet dieses zur Segmentierung an den Ethernet-Adapter, bevor er das Paket weiterleitet. Der Vorteil von LSO ist, dass die CPU Pakete nicht in Größen segmentieren muss, die der MTU entsprechen, und diese Verarbeitung an die Ethernet-Schnittstelle auslagern kann, wo sie in Hardware ausgeführt wird. Weitere Informationen zu den Vorteilen von LSO finden Sie unter [Unterstützung von Large Send Offload](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Wenn LSO aktiviert ist, können Azure-Kunden, wenn sie Paketerfassungen vornehmen, große Framegrößen sehen. Diese großen Framegrößen könnten einige Kunden zu der Annahme veranlassen, dass Fragmentierung auftritt oder eine große MTU verwendet wird, obwohl dies nicht der Fall ist. Mit LSO kann der Ethernet-Adapter dem TCP/IP-Stapel eine größere maximale Segmentgröße (Maximum Segment Size, MSS) anbieten, um ein größeres TCP-Paket zu erstellen. Dieser gesamte nicht segmentierte Frame wird dann an den Ethernetadapter weitergeleitet und wäre in einer Paketerfassung auf der VM sichtbar. Das Paket wird jedoch durch den Ethernet-Adapter gemäß seiner MTU in viele kleinere Frames zerlegt.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>TCP-MSS-Fensterskalierung und PMTUD

#### <a name="tcp-maximum-segment-size"></a>Maximale TCP-Segmentgröße

Die maximale TCP-Segmentgröße (Maximum Segment Size, MSS) ist eine Einstellung, die die Größe von TCP-Segmenten beschränkt, wodurch Fragmentierung der TCP-Pakete vermieden wird. In Betriebssystemen wird MSS üblicherweise mit der folgenden Formel festgelegt:

`MSS = MTU - (IP header size + TCP header size)`

Der IP-Header und der TCP-Header haben jeweils 20 Bytes, zusammen also 40 Bytes. Eine Schnittstelle mit einer MTU von 1.500 hat also eine MSS von 1.460. Aber die MSS kann konfiguriert werden.

Diese Einstellung wird im TCP-Drei-Wege-Handschlag vereinbart, wenn eine TCP-Sitzung zwischen einer Quelle und einem Ziel eingerichtet wird. Beide Seiten senden einen MSS-Wert, und der niedrigere der beiden wird für die TCP-Verbindung verwendet.

Bedenken Sie, dass die MTUs der Quelle und des Ziels nicht die einzigen Faktoren sind, die den MSS-Wert bestimmen. Zwischengeschaltete Netzwerkgeräte, etwa VPN-Gateways, so auch Azure VPN Gateway, können die MTU unabhängig von Quelle und Ziel anpassen, um eine optimale Netzwerkleistung zu gewährleisten.

#### <a name="path-mtu-discovery"></a>Path MTU Discovery

MSS wird ausgehandelt, aber diese kennzeichnet möglicherweise nicht die tatsächliche MSS, die verwendet werden kann. Dies liegt daran, dass andere Netzwerkgeräte auf dem Weg zwischen der Quelle und dem Ziel möglicherweise einen niedrigeren MTU-Wert haben als die Quelle und das Ziel. In diesem Fall verwirft das Gerät, dessen MTU kleiner ist als das Paket, das Paket. Das Gerät sendet eine ICMP Fragmentation Needed-Nachricht (Typ 3, Code 4) zurück, die die MTU des Geräts enthält. Diese ICMP-Nachricht ermöglicht es dem Quellhost, seine Path-MTU entsprechend zu reduzieren. Der Prozess wird als „Path MTU Discovery“ (PMTUD) bezeichnet.

Der PMTUD-Prozess ist ineffizient und wirkt sich auf die Leistung des Netzwerks aus. Wenn Pakete gesendet werden, die die MTU des Netzwerkpfads überschreiten, müssen diese Pakete mit einer niedrigeren MSS erneut übertragen werden. Wenn der Sender die ICMP Fragmentation Needed-Nachricht nicht erhält, möglicherweise wegen einer Netzwerkfirewall im Pfad (allgemein als *PMTUD Blackhole* bezeichnet), dann weiß der Sender nicht, dass er die MSS senken muss, und er wird das Paket kontinuierlich erneut senden. Daher ist es nicht zu empfehlen, die Azure-VM-MTU zu erhöhen.

#### <a name="vpn-and-mtu"></a>VPN und MTU

Wenn Sie virtuelle Computer verwenden, die Kapselung vornehmen (z. B. IPsec-VPNs), gibt es einige zusätzlichen Aspekte hinsichtlich Paketgröße und MTU. VPNs fügen weitere Header zu Paketen hinzu, wodurch die Paketgröße erhöht und eine kleinere MSS erforderlich wird.

Für Azure lautet die Empfehlung, TCP-MSS-Clamping auf 1.350 Bytes und die MTU der Tunnelschnittstelle auf 1.400 festzulegen. Weitere Informationen finden Sie auf der Seite [Informationen zu VPN-Geräten und IPsec-/IKE-Parametern für VPN-Gatewayverbindungen zwischen Standorten](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Latenz, Paketumlaufzeit und TCP-Fensterskalierung

#### <a name="latency-and-round-trip-time"></a>Latenz und Paketumlaufzeit

Die Netzwerklatenz wird durch die Lichtgeschwindigkeit über ein Glasfasernetz bestimmt. Der Netzwerkdurchsatz von TCP wird außerdem stark durch die Paketumlaufzeit (Round Trip Time, RTT) zwischen zwei Netzwerkgeräten bestimmt.

| Route | Distance | Unidirektionale Zeit | RTT |
| ----- | -------- | ------------ | --- |
|New York nach San Francisco|4\.148 km|21 ms|42 ms|
|New York nach London|5\.585 km|28 ms|56 ms|
|New York nach Sydney|15.993 km|80 ms|160 ms|

Diese Tabelle zeigt die Luftlinie zwischen zwei Orten. In Netzwerken ist der Abstand in der Regel länger als die Luftlinie. Dies ist eine einfache Formel zur Berechnung der minimalen RTT, die von der Lichtgeschwindigkeit bestimmt wird:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

Sie können 200 für die Ausbreitungsgeschwindigkeit (speed of propagation) verwenden. Dies ist die Entfernung in Kilometern, die Licht in 1 Millisekunde zurücklegt.

Nehmen Sie die Strecke New York nach San Francisco als Beispiel. Die Luftlinie beträgt 4.148 km. Nach Einsetzen dieses Werts in die Gleichung, ergibt sich Folgendes:

`Minimum RTT = 2 * (4,148 / 200)`

Das Ergebnis der Formel hat die Einheit Millisekunden.

Wenn Sie die beste Netzwerkleistung erreichen möchten, besteht die logische Option darin, Ziele auszuwählen, zu denen die kürzesten Abstände vorliegen. Sie sollten auch Ihr virtuelles Netzwerk so gestalten, dass der Pfad des Datenverkehrs optimiert und die Wartezeit (Latenz) verringert ist. Weitere Informationen finden Sie im Abschnitt „Überlegungen zum Netzwerkentwurf“ in diesem Artikel.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Auswirkungen von Latenz und Paketumlaufzeit auf TCP

Die Paketumlaufzeit hat direkte Auswirkung auf den maximalen TCP-Durchsatz. Im TCP-Protokoll ist *Window Size* (Fenstergröße) ist die maximale Datenmenge, die über eine TCP-Verbindung gesendet werden kann, bevor der Sender eine Bestätigung vom Empfänger erhalten muss. Wenn die TCP MSS auf 1.460 und die TCP Window Size auf 65.535 festgelegt ist, kann der Sender 45 Pakete senden, bevor er eine Bestätigung vom Empfänger empfangen muss. Erhält der Sender keine Bestätigung, übermittelt er die Daten erneut. Die zugehörige Formel sieht so aus:

`TCP window size / TCP MSS = packets sent`

In diesem Beispiel wird 65.535/1.460 auf 45 aufgerundet.

Dieser „Warten auf Bestätigung“-Zustand als Mechanismus zur Gewährleistung einer zuverlässigen Datenübermittlung ist die Ursache, dass sich die RTT auf den TCP-Durchsatz auswirkt. Je länger der Sender auf eine Bestätigung wartet, desto länger muss er auch warten, bevor er weitere Daten senden kann.

Die Formel zur Berechnung des maximalen Durchsatzes einer einzelnen TCP-Verbindung sieht wie folgt aus:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

In dieser Tabelle ist der maximale Durchsatz einer einzelnen TCP-Verbindung in Megabytes pro Sekunde (MB/s) aufgeführt. (Zur besseren Lesbarkeit wird MB als Maßeinheit verwendet.)

| TCP Window Size (Bytes) | RTT-Latenz (ms) | Maximaler Durchsatz in MB/s | Maximaler Durchsatz in MBit/s |
| ----------------------- | ---------------- | ---------------------------------- | --------------------------------- |
|65.535|1|65,54|524,29|
|65.535|30|2,18|17,48|
|65.535|60|1,09|8,74|
|65.535|90|0,73|5,83|
|65.535|120|0,55|4.37|

Wenn Paket verloren gehen, verringert sich der maximale Durchsatz einer TCP-Verbindung, weil der Sender Daten, die er bereits gesendet hat, erneut sendet.

#### <a name="tcp-window-scaling"></a>TCP Window Scaling

TCP Window Scaling (Fensterskalierung) ist eine Technik, bei der die TCP-Fenstergröße dynamisch erhöht wird, um ein Senden von mehr Daten zu ermöglichen, bevor eine Bestätigung erforderlich ist. Im vorherigen Beispiel wurden 45 Pakete gesendet, bevor eine Bestätigung erforderlich war. Wenn Sie die Anzahl der Pakete erhöhen, die gesendet werden können, bevor eine Bestätigung erforderlich ist, verringern Sie die Häufigkeit, mit der der Sender auf eine Bestätigung wartet. Dadurch erhöht sich der maximale TCP-Durchsatz.

In der folgenden Tabelle sind diese Beziehungen veranschaulicht:

| TCP Window Size (Bytes) | RTT-Latenz (ms) | Maximaler Durchsatz in MB/s | Maximaler Durchsatz in MBit/s |
| ----------------------- | ---------------- | ---------------------------------- | --------------------------------- |
|65.535|30|2,18|17,48|
|131.070|30|4.37|34,95|
|262.140|30|8,74|69,91|
|524.280|30|17,48|139,81|

Der TCP-Headerwert für die TCP Window Size (Fenstergröße) ist jedoch nur 2 Bytes lang, was bedeutet, dass der Maximalwert für ein Empfangsfenster 65.535 beträgt. Um die maximale Fenstergröße zu erhöhen, wurde ein TCP Window Scale-Faktor (Fensterskalierungsfaktor) eingeführt.

Der Skalierungsfaktor ist ebenfalls eine Einstellung, die in einem Betriebssystem konfiguriert werden kann. Die Formel zur Berechnung der TCP-Fenstergröße durch Verwenden von Skalierungsfaktoren lautet wie folgt:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Die Berechnung für einen Skalierungsfaktor von 3 und eine Fenstergröße von 65.535 sieht wie folgt aus:

`65,535 \* (2^3) = 262,140 bytes`

Ein Skalierungsfaktor von 14 ergibt eine TCP-Fenstergröße von 14 (der maximal zulässige Offset). Die TCP-Fenstergröße beträgt dann 1.073.725.440 Bytes (8,5 Gigabit).

#### <a name="support-for-tcp-window-scaling"></a>Unterstützung für TCP-Fensterskalierung

In Windows können unterschiedliche Skalierungsfaktoren für unterschiedliche Verbindungstypen festgelegt werden. (Die Klassen von Verbindungen umfassen Rechenzentrum, Internet usw.) Sie verwenden den PowerShell-Befehl `Get-NetTCPConnection`, um den Verbindungstyp der Fensterskalierung anzuzeigen:

```powershell
Get-NetTCPConnection
```

Sie können den PowerShell-Befehl `Get-NetTCPSetting` verwenden, um die Werte von jeder Klasse anzuzeigen:

```powershell
Get-NetTCPSetting
```

Mit dem PowerShell-Befehl `Set-NetTCPSetting` können Sie in Windows die anfängliche TCP-Fenstergröße und den anfänglichen TCP-Skalierungsfaktor festlegen. Weitere Informationen hierzu finden Sie unter [Set-NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps).

```powershell
Set-NetTCPSetting
```

Dies sind die wirksamen TCP-Einstellungen für `AutoTuningLevel`:

| AutoTuningLevel | Skalierungsfaktor | Skalierungsmultiplikator | Formel zum<br/>Berechnen der maximalen Fenstergröße |
| --------------- | -------------- | ------------------ | -------------------------------------------- |
|Disabled|Keine|Keine|Fenstergröße|
|Eingeschränkt|4|2^4|Fenstergröße * (2^4)|
|Stark eingeschränkt|2|2^2|Fenstergröße * (2^2)|
|Normal|8|2^8|Fenstergröße * (2^8)|
|Experimentell|14|2^14|Fenstergröße * (2^14)|

Diese Einstellungen wirken sich am wahrscheinlichsten auf die TCP-Leistung aus, es gibt aber viele andere Faktoren im Internet, die außerhalb der Kontrolle von Azure liegen und sich ebenfalls auf die TCP-Leistung auswirken können.

#### <a name="increase-mtu-size"></a>Erhöhen der MTU-Größe

Weil eine größere MTU eine größere MSS bedeutet, fragen Sie sich möglicherweise, ob eine Erhöhung der MTU zu einer besseren TCP-Leistung führt. Wahrscheinlich nicht. Es gibt Vor- und Nachteile bezüglich der Paketgröße die über den reinen TCP-Datenverkehr hinaus. Wie bereits erläutert, sind TCP-Fenstergröße, Paketverlust und RTT die wichtigsten Faktoren, die die TCP-Durchsatzleistung beeinflussen.

> [!IMPORTANT]
> Microsoft empfiehlt nicht, dass Azure-Kunden den MTU-Standardwert für virtuelle Computer ändern.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Beschleunigter Netzwerkbetrieb und empfangsseitige Skalierung

#### <a name="accelerated-networking"></a>Beschleunigte Netzwerke

Netzwerkfunktionen eines virtuellen Computers waren in der Vergangenheit sowohl auf einer Gast-VM als auch auf einem Hypervisor/Host CPU-intensiv. Jedes Paket, das durch den Host übertragen wird, wird von der Host-CPU in Software verarbeitet, einschließlich der gesamten Kapselung und Entkapselung des virtuellen Netzwerks. Somit gilt, je mehr Datenverkehr durch den Host geleitet wird, desto höher ist die CPU-Last. Und wenn die Host-CPU damit beschäftigt ist, andere Vorgänge auszuführen, wirkt sich dies auch auf den Netzwerkdurchsatz und die Latenz aus. Azure löst dieses Problem mit beschleunigtem Netzwerkbetrieb.

Der beschleunigte Netzwerkbetrieb bietet eine durchgängig extrem niedrige Netzwerklatenz über die firmeneigene programmierbare Hardware von Azure und Technologien wie SR-IOV. Der beschleunigte Netzwerkbetrieb verschiebt einen Großteil des softwaredefinierten Azure-Netzwerkstapels von den CPUs in die FPGA-basierten SmartNICs. Diese Änderung ermöglicht es Endbenutzeranwendungen, Computezyklen freizugeben, wodurch der virtuelle Computer entlastet wird, sodass Jitter und Inkonsistenz in der Latenz verringert werden. Mit anderen Worten, die Leistung kann deterministischer sein.

Der beschleunigte Netzwerkbetrieb verbessert die Leistung, indem er es der Gast-VM ermöglicht, den Host zu umgehen und einen Datenpfad direkt mit dem SmartNIC eines Hosts einzurichten. Dies sind einige der Vorteile des beschleunigten Netzwerkbetriebs:

- **Niedrigere Latenz/mehr Pakete pro Sekunde (pps)** : Durch das Entfernen des virtuellen Switch aus dem Datenpfad wird die Zeit gespart, die Pakete auf dem Host auf die Verarbeitung von Richtlinien warten müssen, und wird die Anzahl von Paketen erhöht, die im virtuellen Computer (VM) verarbeitet werden können.

- **Reduzierte Jitter**: Die Verarbeitung im virtuellen Switch hängt vom Umfang der anzuwendenden Richtlinien und von der Workload der CPU ab, die die Verarbeitung durchführt. Das Auslagern der Richtlinienerzwingung auf die Hardware entfernt diesen Umweg, indem die Pakete direkt an den virtuellen Computer gesendet werden. Dadurch entfallen die Host-zu-VM-Kommunikation und alle Softwareinterrupts und Kontextwechsel.

- **Verringerte CPU-Auslastung**: Das Umgehen des virtuellen Switch auf dem Host führt zu weniger CPU-Auslastung für die Verarbeitung des Netzwerkdatenverkehrs.

Um beschleunigten Netzwerkbetrieb verwenden zu können, müssen Sie diesen explizit auf jedem entsprechenden virtuellen Computer aktivieren. Anweisungen hierzu finden Sie unter [Erstellen eines virtuellen Linux-Computers mit beschleunigtem Netzwerkbetrieb](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).

#### <a name="receive-side-scaling"></a>Empfangsseitige Skalierung

Die empfangsseitige Skalierung ist eine Netzwerktreibertechnologie, die den Empfang von Netzwerkverkehr effizienter verteilt, indem sie die Empfangsverarbeitung auf mehrere CPUs in einem Multiprozessorsystem verteilt. Einfach ausgedrückt ermöglicht empfangsseitige Skalierung es einem System, mehr empfangenen Datenverkehr zu verarbeiten, da es alle verfügbaren CPUs anstelle von nur einer verwendet. Eine technischere Beschreibung der empfangsseitigen Skalierung finden Sie unter [Introduction to Receive Side Scaling](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling) (Einführung in die empfangsseitige Skalierung).

Um die beste Leistung zu erzielen, wenn der beschleunigte Netzwerkbetrieb auf einem virtuellen Computer aktiviert ist, müssen Sie die empfangsseitige Skalierung aktivieren. Die empfangsseitige Skalierung kann auch zu Vorteilen auf virtuellen Computern führen, auf denen der beschleunigte Netzwerkbetrieb nicht verwendet wird. Eine Übersicht darüber, wie Sie feststellen können, ob die empfangsseitige Skalierung aktiviert ist, und wie diese aktiviert wird, finden Sie unter [Optimieren des Netzwerkdurchsatzes für virtuelle Azure-Computer](https://aka.ms/FastVM).

### <a name="tcp-time_wait-and-time_wait-assassination"></a>TCP TIME_WAIT und TIME_WAIT Assassination

TCP TIME_WAIT ist eine weitere gängige Einstellung, die die Netzwerk- und Anwendungsleistung beeinflusst. Auf ausgelasteten VMs, die viele Sockets öffnen und schließen, entweder als Clients oder Server (Quell-IP:Quellport + Ziel-IP:Zielport), kann ein bestimmter Socket während des normalen Betriebs von TCP für lange Zeit in einem TIME_WAIT-Zustand verharren. Der TIME_WAIT-Zustand soll es ermöglichen, dass jegliche zusätzliche Daten an einen Socket gesendet werden können, bevor er geschlossen wird. Daher verhindern TCP/IP-Stapel grundsätzlich die Wiederverwendung eines Sockets, indem sie das TCP-SYN-Paket des Clients automatisch verwerfen.

Die Zeitspanne, die ein Socket in TIME_WAIT verbleibt, ist konfigurierbar. Sie kann von 30 Sekunden bis zu 240 Sekunden lang sein. Sockets sind eine endliche Ressource, und die Anzahl der Sockets, die zu einem bestimmten Zeitpunkt verwendet werden können, ist konfigurierbar. (Die Anzahl der verfügbaren Sockets liegt in der Regel bei etwa 30.000.) Wenn die verfügbaren Sockets vergeben sind oder wenn Clients und Server ungleiche TIME_WAIT-Einstellungen haben und eine VM versucht, einen Socket im TIME_WAIT-Zustand wiederzuverwenden, schlagen neue Verbindungen fehl, da TCP-SYN-Pakete automatisch verworfen werden.

Der Wert für den Portbereich für ausgehende Sockets kann normalerweise im TCP/IP-Stapel eines Betriebssystems konfiguriert werden. Dasselbe gilt auch für TCP TIME_WAIT-Einstellungen und Socketwiederverwendung. Ein Ändern dieser Werte kann die Skalierbarkeit verbessern. Aber je nach Situation können diese Änderungen Interoperabilitätsprobleme verursachen. Sie sollten vorsichtig sein, wenn Sie diese Werte ändern.

Sie können TIME_WAIT Assassination verwenden, um diese Skalierungsbeschränkung zu umgehen. TIME_WAIT Assassination ermöglicht die Wiederverwendung eines Sockets in bestimmten Situationen, etwa wenn die Sequenznummer im IP-Paket der neuen Verbindung die Sequenznummer des letzten Pakets der vorherigen Verbindung überschreitet. In diesem Fall ermöglicht das Betriebssystem das Herstellen der neuen Verbindung (es akzeptiert das neue SYN/ACK), und es erzwingt das Schließen der vorherigen Verbindung, die sich in einem TIME_WAIT-Zustand befunden hat. Diese Funktionalität wird auf Windows-VMs in Azure unterstützt. Um mehr über die Unterstützung in anderen VMs zu erfahren, wenden Sie sich an den Betriebssystemanbieter.

Informationen über das Konfigurieren von TCP TIME_WAIT-Einstellungen und des Quellportbereichs finden Sie unter [Einstellungen, die zur Verbesserung der Netzwerkleistung geändert werden können](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Faktoren eines virtuellen Netzwerks, die sich auf die Leistung auswirken können

### <a name="vm-maximum-outbound-throughput"></a>Maximaler ausgehender VM-Durchsatz

Azure stellt eine Vielzahl von VM-Größen und -Typen bereit, jeweils mit einer unterschiedlichen Kombination von Leistungsmerkmalen. Eines dieser Merkmale ist der Netzwerkdurchsatz (oder die Bandbreite), der in Megabits pro Sekunde (MBit/s) gemessen wird. Da virtuelle Computer auf gemeinsam genutzter Hardware gehostet werden, muss die Netzwerkkapazität zwischen den virtuellen Computern, die dieselbe Hardware verwenden, gerecht aufgeteilt werden. Größeren virtuellen Computern wird mehr Bandbreite als kleineren virtuellen Computern zugeordnet.

Die Netzwerkbandbreite, die dem jeweiligen virtuellen Computer zugeordnet ist, wird anhand des ausgehenden Datenverkehrs vom virtuellen Computer gemessen. Der gesamte Netzwerkdatenverkehr, der vom virtuellen Computer ausgeht, wird unabhängig vom Ziel auf den zugewiesenen Grenzwert angerechnet. Wenn der Grenzwert eines virtuellen Computers beispielsweise bei 1.000 MBit/s liegt, gilt dieser Grenzwert unabhängig davon, ob der ausgehende Datenverkehr für einen anderen virtuellen Computer im selben virtuellen Netzwerk ist oder außerhalb von Azure vorgesehen ist.

Der eingehende Datenverkehr wird nicht gemessen oder direkt beschränkt. Es gibt jedoch weitere Faktoren, z. B. CPU- und Speicherbegrenzungen, die sich darauf auswirken können, wie ein virtueller Computer eingehende Daten verarbeitet.

Der beschleunigte Netzwerkbetrieb ist dazu ausgelegt, die Netzwerkleistung, einschließlich Latenz, Durchsatz und CPU-Auslastung, zu verbessern. Durch den beschleunigten Netzwerkbetrieb kann der Durchsatz eines virtuellen Computers erhöht werden, dies kann jedoch nur bis zu der Bandbreite erfolgen, die dem virtuellen Computer zugewiesen ist.

Jedem virtuellen Azure-Computer ist mindestens eine Netzwerkschnittstelle zugeordnet. Es können aber auch mehrere zugeordnet sein. Die einem virtuellen Computer zugeordnete Bandbreite ist die Summe des gesamten ausgehenden Datenverkehrs über alle Netzwerkschnittstellen, die dem Computer zugeordnet sind. Mit anderen Worten, die Bandbreite wird pro virtuellem Computer zugeordnet, unabhängig davon, wie viele Netzwerkschnittstellen dem Computer zugeordnet sind.

Der erwartete ausgehende Durchsatz und die Anzahl der Netzwerkschnittstellen, die von der jeweiligen Größe eines virtuellen Computers unterstützt werden, sind unter [Größen für virtuelle Windows-Computer in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) ausführlich beschrieben. Um den maximalen Durchsatz zu sehen, wählen Sie einen Typ aus, etwa **Allgemeiner Zweck**, und suchen Sie dann auf der angezeigten Seite nach dem Abschnitt über die Größenserie (z. B. „Dv2-Serie“). Für jede Serie gibt es eine Tabelle, in der in der letzten Spalte, die mit „Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBit/s)“ überschrieben ist, Netzwerkspezifikationen enthalten sind.

Die Durchsatzbegrenzung gilt für den virtuellen Computer. Die folgenden Faktoren wirken sich nicht auf den Durchsatz aus:

- **Anzahl der Netzwerkschnittstellen**: Der Bandbreitengrenzwert bezieht sich auf die Summe des gesamten ausgehenden Datenverkehrs vom virtuellen Computer.

- **Beschleunigter Netzwerkbetrieb**: Obwohl dieses Feature beim Erreichen des veröffentlichten Grenzwerts hilfreich sein kann, nimmt es keine Änderung am Grenzwert vor.

- **Datenverkehrsziel**: Alle Ziele werden auf den Grenzwert für ausgehenden Datenverkehr angerechnet.

- **Protokoll:** Der gesamte ausgehende Datenverkehr über alle Protokolle wird auf den Grenzwert angerechnet.

Weitere Informationen finden Sie unter [Netzwerkdurchsatz virtueller Computer](https://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Überlegungen zur Internetleistung

Wie in diesem Artikel erläutert, können Faktoren im Internet und außerhalb der Kontrolle von Azure die Netzwerkleistung beeinträchtigen. Einige dieser Faktoren sind:

- **Latenz**: Die Paketumlaufzeit zwischen zwei Zielen kann durch Probleme in Zwischennetzwerken, durch Datenverkehr, der nicht den „kürzesten“ Weg nimmt, und durch suboptimale Peering-Pfade beeinflusst werden.

- **Paketverlust**: Paketverluste können durch Netzwerküberlastung, Probleme mit dem physischen Pfad und leistungsschwache Netzwerkgeräte verursacht werden.

- **MTU-Größe/Fragmentierung**: Fragmentierung entlang des Pfads kann zu Verzögerungen bei der Datenankunft oder zu Paketen führen, die in der falschen Reihenfolge eintreffen, was die Übermittlung von Paketen beeinträchtigen kann.

Traceroute ist ein gutes Tool, um die Leistungsmerkmale eines Netzwerks (etwa Paketverlust und Latenz) entlang jedes Netzwerkpfads zwischen einem Quell- und einem Zielgerät zu messen.

### <a name="network-design-considerations"></a>Überlegungen zum Netzwerkentwurf

Über die weiter oben in diesem Artikel beschriebenen Aspekte hinaus kann sich die Topologie eines virtuellen Netzwerks auf die Leistung des Netzwerks auswirken. Zum Beispiel führt ein Hub-and-Spoke-Entwurf, der den Datenverkehr global zu einem einzelnen virtuellen Hub zurückleitet, zu einer Netzwerklatenz, die sich auf die Gesamtleistung des Netzwerks auswirkt.

Die Anzahl der Netzwerkgeräte, die der Netzwerkdatenverkehr durchläuft, kann sich ebenfalls auf die Gesamtlatenz auswirken. Wenn beispielsweise bei einem Hub-and-Spoke-Entwurf der Datenverkehr ein virtuelles Spoke-Netzwerkgerät und ein virtuelles Hub-Gerät durchläuft, bevor er ins Internet gelangt, kann das virtuelle Netzwerkgerät Latenz verursachen.

### <a name="azure-regions-virtual-networks-and-latency"></a>Azure-Regionen, virtuelle Netzwerke und Latenz

Azure-Regionen bestehen aus mehreren Rechenzentren (Datencenter), die sich in einem allgemeinen geografischen Gebiet befinden. Diese Datencenter sind physisch möglicherweise nicht nebeneinander. In einigen Fällen sind sie mehr als 10 Kilometer voneinander entfernt. Ein virtuelles Netzwerk ist eine Überlagerung auf dem physischen Netzwerk eines Azure-Datencenters. Ein virtuelles Netzwerk erfordert keine spezielle Netzwerktopologie im Datencenter.

Zum Beispiel können sich zwei virtuelle Computer, die zum selben virtuellen Netzwerk und Subnetz gehören, in verschiedenen Racks, Reihen oder sogar Datencentern befinden. Zwischen ihnen können meter- oder kilometerlange Glasfaserkabel liegen. Diese Abweichung kann zu variablen Latenzen (einige Millisekunden Differenz) zwischen verschiedenen virtuellen Computern führen.

Die geografische Platzierung von virtuellen Computern und die mögliche resultierende Latenz zwischen zwei virtuellen Computern können durch die Konfiguration von Verfügbarkeitsgruppen und Verfügbarkeitszonen beeinflusst werden. Aber der Abstand zwischen den Datencentern in einer Region ist regionsspezifisch und hauptsächlich der Datencentertopologie in der Region geschuldet.

### <a name="source-nat-port-exhaustion"></a>Quell-NAT-Portauslastung

Eine Bereitstellung in Azure kann mit Endpunkten außerhalb von Azure im öffentlichen Internet und/oder im öffentlichen IP-Adressraum kommunizieren. Wenn eine Instanz eine ausgehende Verbindung initiiert, ordnet Azure die private IP-Adresse dynamisch einer öffentlichen IP-Adresse zu. Nachdem Azure diese Zuordnung erstellt hat, kann der Antwortdatenverkehr für den ausgehenden ursprünglichen Datenfluss auch die private IP-Adresse erreichen, von der der Datenfluss stammte.

Für jede ausgehende Verbindung muss der Azure Load Balancer diese Zuordnung für einige Zeit aufrechterhalten. Aufgrund der Mehrmandantenfähigkeit von Azure kann die Aufrechterhaltung dieser Zuordnung für jeden ausgehenden Datenverkehr für jede VM ressourcenintensiv sein. Daher gibt es Einschränkungen, die entsprechend der Konfiguration des virtuellen Azure-Netzwerks festgelegt werden. Oder, um dies genauer zu formulieren, eine Azure-VM kann jeweils nur eine bestimmte Anzahl von ausgehenden Verbindungen herstellen. Wenn diese Einschränkungen erreicht sind, können für die VM keine weiteren ausgehenden Verbindungen hergestellt werden.

Dieses Verhalten ist aber konfigurierbar. Weitere Informationen zu SNAT und SNAT-Portauslastung finden Sie in [diesem Artikel](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Messen der Netzwerkleistung in Azure

Eine Reihe der Leistungshöchstwerte in diesem Artikel beziehen sich auf die Netzwerklatenz/Paketumlaufzeit (Round Trip Time, RTT) zwischen zwei VMs. Dieser Abschnitt enthält einige Vorschläge, wie Latenz/RTT und wie die TCP-Leistung und die VM-Netzwerkleistung getestet werden können. Sie können die früher diskutierten TCP/IP- und Netzwerkwerte optimieren und hinsichtlich der Leistung testen, indem Sie die in diesem Abschnitt beschriebenen Techniken verwenden. Sie können Werte für Latenz, MTU, MSS und Fenstergröße (Window Size) in die weiter oben aufgeführten Berechnungen eingeben und die theoretischen Höchstwerte mit den tatsächlichen Werte vergleichen, die Sie während des Testens beobachten.

### <a name="measure-round-trip-time-and-packet-loss"></a>Messen von Paketumlaufzeit (RTT) und Paketverlust

Die TCP-Leistung hängt stark von RTT und Paketverlust ab. Das in Windows und Linux verfügbaren PING-Hilfsprogramm bietet die einfachste Möglichkeit, RTT und Paketverlust zu messen. Die Ausgabe von PING enthält die minimale/maximale/durchschnittliche Latenz zwischen einer Quelle und einem Ziel. Darüber hinaus enthält sie auch den Paketverlust. PING verwendet standardmäßig das ICMP-Protokoll. Sie können PsPing verwenden, um TCP RTT zu testen. Weitere Informationen hierzu finden Sie unter [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Messen des tatsächlichen Durchsatzes für eine TCP-Verbindung

NTttcp ist ein Tool zum Testen der TCP-Leistung einer Linux- oder Windows-VM. Sie können verschiedene TCP-Einstellungen ändern und dann die Vorteile testen, indem Sie NTttcp verwenden. Weitere Informationen finden Sie in den folgenden Ressourcen:

- [Testen der Bandbreite/des Durchsatzes (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [NTttcp-Hilfsprogramm](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Messen der tatsächlichen Bandbreite eines virtuellen Computers

Sie können die Leistung von verschiedenen VM-Typen, von beschleunigtem Netzwerkbetrieb usw. testen, indem Sie ein Tool namens iPerf verwenden. iPerf ist ebenfalls unter Linux und Windows verfügbar. iPerf kann mit TCP oder UDP den Netzwerkgesamtdurchsatz testen. iPerf TCP-Durchsatztests werden von den in diesem Artikel beschriebenen Faktoren beeinflusst (etwa Latenz und RTT). Daher kann UDP zu besseren Ergebnissen führen, wenn Sie nur den maximalen Durchsatz testen möchten.

Weitere Informationen und Beispiele finden Sie in diesen Artikeln:

- [Beheben von Problemen bei der Expressroute-Netzwerkleistung](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Überprüfen des VPN-Durchsatzes zu einem virtuellen Netzwerk](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Erkennen von ineffizientem TCP-Verhalten

In Paketerfassungen sehen Azure-Kunden möglicherweise TCP-Pakete mit TCP-Flags (SACK, DUP ACK, RETRANSMIT und FAST RETRANSMIT), die auf Probleme mit der Netzwerkleistung hinweisen können. Diese Pakete weisen insbesondere auf Netzwerkineffizienzen hin, die aus Paketverlust resultieren. Paketverlust muss jedoch nicht notwendigerweise durch Azure Leistungsprobleme verursacht sein. Leistungsprobleme können auch das Ergebnis von Anwendungsproblemen, von Betriebssystemproblemen oder von anderen Problemen sein, die nicht direkt mit der Azure-Plattform zusammenhängen.

Beachten Sie auch, dass einige Neuübertragungen und doppelte ACKs in einem Netzwerk normal sind. TCP-Protokolle wurden entwickelt, um zuverlässig zu sein. Das Vorhandensein dieser TCP-Pakete in einer Paketerfassung deutet nicht unbedingt auf ein systemisches Netzwerkproblem hin, es sei denn, ihre Anzahl ist sehr hoch.

Trotzdem sind diese Pakettypen weiterhin Hinweise darauf, dass der TCP-Durchsatz nicht seine maximale Leistung erreicht. Die Gründe dafür sind in anderen Abschnitten dieses Artikels erläutert.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie sich die TCP/IP-Leistung für virtuelle Azure-Computer optimieren lässt, möchten Sie wahrscheinlich mehr über andere Aspekte des [Planens von virtuellen Netzwerken](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) oder [über Verbinden und Konfigurieren von virtuellen Netzwerken](https://docs.microsoft.com/azure/virtual-network/) erfahren.
