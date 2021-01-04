---
title: Überprüfen des VPN-Durchsatzes zu einer Microsoft Azure Virtual Network-Instanz
description: Dieser Artikel unterstützt Sie bei der Überprüfung des Netzwerkdurchsatzes von lokalen Ressourcen zu einem virtuellen Azure-Computer.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
manager: dcscontentpm
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: 2d5b51e8cfbfcb5f771e9da524231f8ddfc40a9e
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660932"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Überprüfen des VPN-Durchsatzes zu einem virtuellen Netzwerk

Mit einer VPN-Gateway-Verbindung sorgen Sie für sichere, standortübergreifende Konnektivität in Ihrem virtuellen Netzwerk innerhalb der Azure-Infrastruktur und Ihrer lokalen IT-Infrastruktur.

In diesem Artikel wird die Überprüfung des Netzwerkdurchsatzes von lokalen Ressourcen zu einem virtuellen Azure-Computer (VM) beschrieben.

> [!NOTE]
> Dieser Artikel ist dazu gedacht, bei der Diagnose und beim Beheben von häufigen Problemen zu helfen. Wenn Sie das Problem nicht mithilfe der folgenden Informationen beheben können, [wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="overview"></a>Übersicht

Die VPN-Gateway-Verbindung umfasst die folgenden Komponenten:

* Lokales VPN-Gerät (eine Liste [überprüfter VPN-Geräte](vpn-gateway-about-vpn-devices.md#devicetable) anzeigen)
* Öffentliches Internet
* Azure VPN Gateway
* Azure VM

Das folgende Diagramm zeigt die logische Verbindung von einem lokalen Netzwerk mit einem Azure Virtual Network über VPN.

![Logische Konnektivität vom Kundennetzwerk zum MSFT-Netzwerk über VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Berechnen des maximal erwarteten Eingangs/Ausgangs

1. Bestimmen Sie die Grundvoraussetzungen für den Durchsatz Ihrer Anwendung.
1. Bestimmen Sie die Durchsatzlimits für Ihr Azure VPN Gateway. Wenn Sie Hilfe benötigen, lesen Sie den Abschnitt „Gateway-SKUs“ von [Informationen zu VPN Gateway](vpn-gateway-about-vpngateways.md#gwsku).
1. Bestimmen Sie die [Durchsatzanleitung des virtuellen Azure-Computers](../virtual-machines/sizes.md) für die Größe Ihres virtuellen Computers.
1. Bestimmen Sie die Bandbreite Ihres Internetdienstanbieters.
1. Berechnen Sie Ihren erwarteten Durchsatz, indem Sie die geringste Bandbreite entweder der VM, des VPN-Gateways oder des ISP verwenden. Diese wird in Megabit pro Sekunde (/) geteilt durch acht (8) gemessen.

Wenn der berechnete Durchsatz nicht die Grundvoraussetzungen für den Durchsatz Ihrer Anwendung erfüllt, müssen Sie die Bandbreite der Ressource erhöhen, die Sie als Engpass identifiziert haben. Informationen zum Ändern der Größe für ein Azure VPN Gateway finden Sie unter [Ändern einer Gateway-SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Informationen zum Ändern der Größe eines virtuellen Computers finden Sie unter [Ändern der Größe eines virtuellen Computers](../virtual-machines/windows/resize-vm.md). Wenn Sie nicht die erwartete Internetbandbreite erhalten, sollten Sie sich auch an Ihren Internetdienstanbieter wenden.

> [!NOTE]
> Der Durchsatz des VPN-Gateways ist eine Summe aller Site-to-Site-\VNET-to-VNET- oder Point-to-Site-Verbindungen.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Überprüfen des Netzwerkdurchsatzes mithilfe von Leistungstools

Diese Überprüfung sollte nicht während der Spitzenlastzeiten ausgeführt werden, da die Durchsatzausnutzung des VPN-Tunnels während der Tests keine präzisen Ergebnisse liefert.

Für diesen Test verwenden wir das Tool „iPerf“, das sowohl unter Windows als auch unter Linux funktioniert und Client- und Servermodi bietet. Bei virtuellen Windows-Computern ist es auf 3 GBit/s begrenzt.

Dieses Tool führt keine Lese-/Schreibvorgänge auf den Datenträger aus. Es erzeugt ausschließlich selbstgenerierten TCP-Datenverkehr von einem Ende zum anderen. Es hat Statistiken auf Basis von Versuchen generiert, die die verfügbare Bandbreite zwischen Client- und Server-Knoten messen. Beim Testen zwischen zwei Knoten dient ein Knoten als Server und der andere Knoten als Client. Nachdem dieser Test abgeschlossen ist, wird empfohlen, dass Sie die Rollen der Knoten tauschen, um den Durchsatz sowohl beim Upload als auch beim Download auf beiden Knoten zu testen.

### <a name="download-iperf"></a>Herunterladen von iPerf

Laden Sie [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip) herunter. Ausführliche Informationen finden Sie in der [iPerf-Dokumentation](https://iperf.fr/iperf-doc.php).

 > [!NOTE]
 > Die in diesem Artikel beschriebenen Drittanbieterprodukte werden von Unternehmen hergestellt, die von Microsoft unabhängig sind. Microsoft übernimmt keine Garantie, weder ausdrücklich noch konkludent, für die Leistungsfähigkeit oder Zuverlässigkeit dieser Produkte.

### <a name="run-iperf-iperf3exe"></a>Ausführen von iPerf (iperf3.exe)

1. Aktivieren Sie eine NSG/ACL-Regel, die den Datenverkehr gestattet (zum öffentlichen Testen der IP-Adresse auf dem virtuellen Azure-Computer).

1. Aktivieren Sie auf beiden Knoten eine Firewallausnahme für Port 5001.

   **Windows:** Führen Sie den folgenden Befehl als Administrator aus:

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   Führen Sie den folgenden Befehl aus, um die Regel nach Abschluss der Tests zu entfernen:

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **Azure Linux:** Azure Linux-Images besitzen tolerante Firewalls. Wenn eine Anwendung einen Port überwacht, wird der Datenverkehr durchgelassen. Für benutzerdefinierte Images, die gesichert sind, müssen die Ports möglicherweise explizit geöffnet werden. Allgemeine Firewalls der Linux-Betriebssystemebene enthalten `iptables`, `ufw` oder `firewalld`.

1. Wechseln Sie auf dem Serverknoten in das Verzeichnis, in dem „iperf3.exe“ extrahiert wird. Führen Sie dann iPerf im Modus „Server“ aus, und legen Sie die Überwachung von Port 5001 mit den folgenden Befehlen fest:

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > Port 5001 ist anpassbar, um bestimmte Firewalleinschränkungen in Ihrer Umgebung zu berücksichtigen.

1. Wechseln Sie auf dem Clientknoten in das Verzeichnis, in dem das Tool „iPerf“ exportiert wurde, und führen Sie dann den folgenden Befehl aus:

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   Der Client leitet 30 Sekunden Datenverkehr auf Port 5001 zum Server weiter. Das Flag „-P“ gibt an, dass 32 gleichzeitige Verbindungen mit dem Serverknoten verwendet werden.

   Der folgende Bildschirm zeigt die Ausgabe dieses Beispiels:

   ![Output](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. (OPTIONAL) Führen Sie diesen Befehl aus, um die Testergebnisse beizubehalten:

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. Führen Sie nach Abschluss der vorherigen Schritte dieselben Schritte mit den vertauschten Rollen aus, damit der Serverknoten jetzt zum Clientknoten wird und umgekehrt.

> [!Note]
> Iperf ist nicht das einzige Tool. [NTTTCP ist eine alternative Lösung für Tests](../virtual-network/virtual-network-bandwidth-testing.md).

## <a name="test-vms-running-windows"></a>Testen von virtuellen Computern unter Windows

### <a name="load-latteexe-onto-the-vms"></a>Laden von „Latte.exe“ auf die virtuellen Computer

Laden Sie die neueste Version von [Latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b) herunter.

Erwägen Sie, „Latte.exe“ in einem separaten Ordner abzulegen, z. B. `c:\tools`.

### <a name="allow-latteexe-through-the-windows-firewall"></a>Zulassen von „Latte.exe“ durch die Windows-Firewall

Erstellen Sie auf dem Empfänger eine „Zulassen“-Regel für die Windows-Firewall, damit der „Latte.exe“-Datenstrom empfangen werden kann. Am einfachsten ist es, das gesamte „Latte.exe“-Programm nach Name anstatt nach bestimmten eingehenden TCP-Ports zuzulassen.

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>Zulassen von „Latte.exe“ durch die Windows-Firewall

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

Angenommen, Sie haben „latte.exe“ in den Ordner „C:\tools“ kopiert, dann wäre dies der Befehl:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Durchführen von Latenztests

Starten Sie „latte.exe“ auf dem EMPFÄNGER (Ausführen über CMD, nicht über PowerShell):

`latte -a <Receiver IP address>:<port> -i <iterations>`

Etwa 65.000 Iterationen sollten repräsentative Ergebnisse liefern.

Jede verfügbare Portnummer ist in Ordnung.

Wenn der virtuelle Computer die IP-Adresse 10.0.0.4 aufweist, würde es folgendermaßen aussehen:

`latte -c -a 10.0.0.4:5005 -i 65100`

Starten Sie „latte.exe“ auf dem SENDER (Ausführen über CMD, nicht über PowerShell):

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

Der resultierende Befehl ist derselbe wie beim Empfänger, abgesehen davon, dass er den Zusatz „-c“ enthält, um anzuzeigen, dass es sich um den „Client“ oder Sender handelt.

`latte -c -a 10.0.0.4:5005 -i 65100`

Warten Sie auf die Ergebnisse. Je nachdem, wie weit die VMs voneinander entfernt sind, kann es einige Minuten dauern, bis der Vorgang abgeschlossen ist. Erwägen Sie, mit weniger Iterationen zu beginnen, um den Erfolg zu testen, bevor Sie längere Tests durchführen.

## <a name="test-vms-running-linux"></a>Testen von virtuellen Computern unter Linux

Verwenden Sie [SockPerf](https://github.com/mellanox/sockperf), um virtuelle Computer zu testen.

### <a name="install-sockperf-on-the-vms"></a>Installieren von SockPerf auf den virtuellen Computern

Führen Sie diese Befehle zum Vorbereiten von SockPerf auf Ihren virtuellen Computern (jeweils SENDER und EMPFÄNGER) auf den Linux-VMs aus:

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS/RHEL: Installieren von Git und anderen nützlichen Tools

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu: Installieren von Git und anderen nützlichen Tools

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash: alle

Von der bash-Befehlszeile (sofern Git installiert ist)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

„make“ ist langsamer und kann mehrere Minuten dauern.

`make`

„make install“ ist schnell.

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>Ausführen von SockPerf auf den virtuellen Computern

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>Beispielsbefehle nach der Installation. Server/Empfänger: IP-Adresse des Servers wird als „10.0.0.4“ angenommen

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>Client: IP-Adresse des Servers wird als „10.0.0.4“ angenommen

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> Stellen Sie sicher, dass es während des Durchsatztests zwischen VM und Gateway keine Zwischenhops (z. B. virtuelle Geräte) gibt.
> Wenn die Ergebnisse der obigen iPERF/NTTTCP-Tests (in Bezug auf den Gesamtdurchsatz) nicht zufriedenstellend sind, lesen Sie bitte den folgenden Artikel, um die Schlüsselfaktoren hinter den möglichen Ursachen des Problems zu verstehen: https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

Insbesondere die Analyse von Paketerfassungsverfolgungen (Wireshark/Netzwerkmonitor), die während dieser Tests parallel vom Client und Server gesammelt werden, wird bei der Beurteilung schlechter Leistung helfen. Diese Ablaufverfolgungen können Paketverlust, hohe Latenzzeiten, MTU-Größe, Fragmentierung, TCP 0 Window, Fragmente in falscher Reihenfolge usw. beinhalten.

## <a name="address-slow-file-copy-issues"></a>Behandeln von Problemen durch langsames Kopieren von Dateien

Auch wenn der Gesamtdurchsatz, der in den vorherigen Schritten (iPERF/NTTTCP/usw.) ermittelt wurde, gut war, kann es bei der Verwendung des Windows Explorers oder beim Drag & Drop durch eine RDP-Sitzung zu einer langsamen Dateibewältigung kommen. Dieses Problem ergibt sich normalerweise aufgrund eines oder der beiden folgenden Faktoren:

* Anwendungen zum Kopieren von Dateien, z. B. Windows-Explorer und RDP, verwenden beim Kopieren von Dateien nicht mehrere Threads. Verwenden Sie eine Anwendung mit mehreren Threads zum Kopieren von Dateien, z. B. [Richcopy](/previous-versions/technet-magazine/dd547088(v=msdn.10)), damit Dateien mithilfe von 16 oder 32 Threads kopiert werden. Um die Threadanzahl für das Kopieren von Dateien in Richcopy zu ändern, klicken Sie auf **Action** > **Copy options** > **File copy**.

   ![Probleme durch langsames Kopieren von Dateien](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > Nicht alle Anwendungen funktionieren gleich, und nicht alle Anwendungen/Prozesse nutzen alle Threads. Wenn Sie den Test ausführen, können Sie sehen, dass einige Threads leer sind und keine genauen Durchsatzergebnisse liefern.
   > Um die Leistung Ihrer Anwendungsdateiübertragung zu überprüfen, verwenden Sie Multithread, indem Sie die Anzahl der Threads nacheinander erhöhen oder verringern, um den optimalen Durchsatz der Anwendung oder der Dateiübertragung zu ermitteln.

* Unzureichende Lese-/Schreibgeschwindigkeiten des Datenträgers des virtuellen Computers. Weitere Informationen finden Sie unter [Problembehandlung für Azure Storage](/previous-versions/azure/storage/common/storage-e2e-troubleshooting).

## <a name="on-premises-device-external-facing-interface"></a>Nach außen gerichtete Schnittstelle für lokale Geräte

Es wurden die Subnetze der lokalen Bereiche erwähnt, die Azure über VPN am lokalen Netzwerkgateway erreichen soll. Definieren Sie gleichzeitig den VNET-Adressraum in Azure für das lokale Gerät.

* **Routenbasiertes Gateway**: Die Richtlinie bzw. der Datenverkehrselektor für routenbasierte VPNs werden im Any-to-Any-Format (bzw. als Platzhalter) konfiguriert.

* **Richtlinienbasiertes Gateway**: Bei richtlinienbasierten VPNs werden Pakete verschlüsselt und durch IPsec-Tunnel geleitet. Grundlage hierfür sind Kombinationen aus Adresspräfixen zwischen Ihrem lokalen Netzwerk und dem Azure-VNet. Die Richtlinie (auch Datenverkehrsselektor genannt) wird in der Regel als Zugriffsliste in der VPN-Konfiguration definiert.

* **UsePolicyBasedTrafficSelector**-Verbindungen: Wenn „UsePolicyBasedTrafficSelectors“ für eine Verbindung auf „$True“ festgelegt wird, wird das Azure-VPN-Gateway zum Herstellen einer Verbindung mit einer richtlinienbasierten VPN-Firewall an einem lokalen Standort konfiguriert. Wenn Sie „PolicyBasedTrafficSelectors“ aktivieren, müssen für Ihr VPN-Gerät die entsprechenden Datenverkehrsselektoren mit allen Präfixkombinationen zwischen Ihrem lokalen Netzwerk (lokalen Netzwerkgateway) und den Präfixen des virtuellen Azure-Netzwerks definiert sein (anstelle von Any-to-Any).

Eine ungeeignete Konfiguration kann zu häufigen Verbindungsabbrüchen innerhalb des Tunnels, Paketausfällen, schlechtem Durchsatz und Latenzzeiten führen.

## <a name="check-latency"></a>Überprüfen der Latenzzeit

Sie können die Latenzzeit mithilfe der folgenden Tools überprüfen:

* WinMTR
* TCPTraceroute
* `ping` und `psping` (Diese Tools können eine gute Einschätzung der RTT liefern, aber sie können nicht in allen Fällen verwendet werden.)

![Latenzzeit überprüfen](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

Wenn Sie eine hohe Latenzspitzenbildung bei einem der Hops feststellen, bevor der Netzwerkbackbone von Microsoft erreicht wird, sollten Sie eventuell weitere Untersuchungen bei Ihrem Internetanbieter durchführen.

Wenn innerhalb von „msn.net“ ein großer, ungewöhnlicher Latenzanstieg von Hops erkannt wird, wenden Sie sich bitte an den MS-Support für weitere Untersuchungen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen oder Hilfe finden Sie über den folgenden Link:

* [Microsoft-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)