---
title: Überprüfen des VPN-Durchsatzes zu einem Microsoft Azure Virtual Network | Microsoft-Dokumentation
description: Dieses Dokument ist dafür vorgesehen, einen Benutzer beim Überprüfen des Netzwerkdurchsatzes von seinen lokalen Ressourcen zu einem virtuellen Azure-Computer zu helfen.
services: vpn-gateway
author: cherylmc
manager: jasmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 05/29/2019
ms.author: radwiv;chadmat;genli
ms.openlocfilehash: c1117afcf6254c32ebe0a4e72ad5619606098253
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388609"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Überprüfen des VPN-Durchsatzes zu einem virtuellen Netzwerk

Mit einer VPN-Gateway-Verbindung sorgen Sie für sichere, standortübergreifende Konnektivität in Ihrem virtuellen Netzwerk innerhalb der Azure-Infrastruktur und Ihrer lokalen IT-Infrastruktur.

In diesem Artikel wird die Überprüfung des Netzwerkdurchsatzes von lokalen Ressourcen zu einem virtuellen Azure-Computer (VM) beschrieben. Darüber hinaus enthält er Informationen zur Problembehandlung. 

>[!NOTE]
>Dieser Artikel ist dazu gedacht, bei der Diagnose und beim Beheben von häufigen Problemen zu helfen. Wenn Sie das Problem nicht mithilfe der folgenden Informationen beheben können, [wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="overview"></a>Übersicht

Die VPN-Gateway-Verbindung umfasst die folgenden Komponenten:

- Lokales VPN-Gerät (Liste von [überprüften VPN-Geräten anzeigen](vpn-gateway-about-vpn-devices.md#devicetable)).
- Öffentliches Internet
- Azure VPN Gateway
- Azure-VM

Das folgende Diagramm zeigt die logische Verbindung von einem lokalen Netzwerk mit einem Azure Virtual Network über VPN.

![Logische Konnektivität vom Kundennetzwerk zum MSFT-Netzwerk über VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Berechnen des maximal erwarteten Eingangs/Ausgangs

1.  Bestimmen Sie die Grundvoraussetzungen für den Durchsatz Ihrer Anwendung.
2.  Bestimmen Sie die Durchsatzlimits für Ihr Azure VPN Gateway. Wenn Sie Hilfe benötigen, lesen Sie den Abschnitt „Gateway-SKUs“ von [Informationen zu VPN Gateway](vpn-gateway-about-vpngateways.md#gwsku).
3.  Bestimmen Sie die [Durchsatzanleitung des virtuellen Azure-Computers](../virtual-machines/virtual-machines-windows-sizes.md) für die Größe Ihres virtuellen Computers.
4.  Bestimmen Sie die Bandbreite Ihres Internetdienstanbieters.
5.  Berechnen Sie Ihren erwarteten Durchsatz – Niedrigste Bandbreite von (virtueller Computer, Gateway, Internetdienstanbieter) * 0,8.

Wenn der berechnete Durchsatz nicht die Grundvoraussetzungen für den Durchsatz Ihrer Anwendung erfüllt, müssen Sie die Bandbreite der Ressource erhöhen, die Sie als Engpass identifiziert haben. Informationen zum Ändern der Größe für ein Azure VPN Gateway finden Sie unter [Ändern einer Gateway-SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Informationen zum Ändern der Größe eines virtuellen Computers finden Sie unter [Ändern der Größe eines virtuellen Computers](../virtual-machines/virtual-machines-windows-resize-vm.md). Wenn Sie nicht die erwartete Internetbandbreite erhalten, sollten Sie sich auch an Ihren Internetdienstanbieter wenden.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Überprüfen des Netzwerkdurchsatzes mithilfe von Leistungstools

Diese Überprüfung sollte nicht während der Spitzenlastzeiten ausgeführt werden, da die Durchsatzausnutzung des VPN-Tunnels während der Tests keine präzisen Ergebnisse liefert.

Für diesen Test verwenden wir das Tool „iPerf“, das sowohl unter Windows als auch unter Linux funktioniert und Client- und Servermodi bietet. Bei virtuellen Windows-Computern ist es auf 3 GBit/s begrenzt.

Dieses Tool führt keine Lese-/Schreibvorgänge auf den Datenträger aus. Es erzeugt ausschließlich selbstgenerierten TCP-Datenverkehr von einem Ende zum anderen. Es hat Statistiken auf Basis von Versuchen generiert, die die verfügbare Bandbreite zwischen Client- und Server-Knoten messen. Beim Testen zwischen zwei Knoten dient ein Knoten als Server und der andere Knoten als Client. Nachdem dieser Test abgeschlossen ist, wird empfohlen, dass Sie die Rollen tauschen, um den Durchsatz sowohl beim Upload als auch beim Download auf beiden Knoten zu testen.

### <a name="download-iperf"></a>Herunterladen von iPerf
Laden Sie [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip) herunter. Ausführliche Informationen finden Sie in der [iPerf-Dokumentation](https://iperf.fr/iperf-doc.php).

 >[!NOTE]
 >Die in diesem Artikel beschriebenen Drittanbieterprodukte werden von Unternehmen hergestellt, die von Microsoft unabhängig sind. Microsoft übernimmt keine Garantie, weder ausdrücklich noch konkludent, für die Leistungsfähigkeit oder Zuverlässigkeit dieser Produkte.
 >
 >

### <a name="run-iperf-iperf3exe"></a>Ausführen von iPerf (iperf3.exe)
1. Aktivieren Sie eine NSG/ACL-Regel, die den Datenverkehr gestattet (zum öffentlichen Testen der IP-Adresse auf dem virtuellen Azure-Computer).

2. Aktivieren Sie auf beiden Knoten eine Firewallausnahme für Port 5001.

    **Windows:** Führen Sie den folgenden Befehl als Administrator aus:

    ```CMD
    netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
    ```

    Führen Sie den folgenden Befehl aus, um die Regel nach Abschluss der Tests zu entfernen:

    ```CMD
    netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
    ```
     
    **Azure Linux:**  Azure Linux-Images besitzen tolerante Firewalls. Wenn eine Anwendung einen Port überwacht, wird der Datenverkehr durchgelassen. Für benutzerdefinierte Images, die gesichert sind, müssen die Ports möglicherweise explizit geöffnet werden. Allgemeine Firewalls der Linux-Betriebssystemebene enthalten `iptables`, `ufw` oder `firewalld`.

3. Wechseln Sie auf dem Serverknoten in das Verzeichnis, in dem „iperf3.exe“ extrahiert wird. Führen Sie dann iPerf im Modus „Server“ aus, und legen Sie die Überwachung von Port 5001 mit den folgenden Befehlen fest:

     ```CMD
     cd c:\iperf-3.1.2-win65

     iperf3.exe -s -p 5001
     ```

4. Wechseln Sie auf dem Clientknoten in das Verzeichnis, in dem das Tool „iPerf“ exportiert wurde, und führen Sie dann den folgenden Befehl aus:

    ```CMD
    iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
    ```

    Der Client verursacht für 30 Sekunden an Port 5001 Datenverkehr zum Server. Das Flag „-P“ gibt an, dass 32 gleichzeitige Verbindungen mit dem Serverknoten verwendet werden.

    Der folgende Bildschirm zeigt die Ausgabe dieses Beispiels:

    ![Output](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

5. (OPTIONAL) Führen Sie diesen Befehl aus, um die Testergebnisse beizubehalten:

    ```CMD
    iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
    ```

6. Führen Sie nach Abschluss der vorherigen Schritte dieselben Schritte mit den vertauschten Rollen aus, damit der Serverknoten jetzt zum Client wird und umgekehrt.

## <a name="address-slow-file-copy-issues"></a>Behandeln von Problemen durch langsames Kopieren von Dateien
Bei der Verwendung von Windows-Explorer oder beim Drag & Drop in einer RDP-Sitzung kann es vorkommen, dass Dateien langsam kopiert werden. Dieses Problem ergibt sich normalerweise aufgrund eines oder der beiden folgenden Faktoren:

- Anwendungen zum Kopieren von Dateien, z. B. Windows-Explorer und RDP, verwenden beim Kopieren von Dateien nicht mehrere Threads. Verwenden Sie eine Anwendung mit mehreren Threads zum Kopieren von Dateien, z. B. [Richcopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx), damit Dateien mithilfe von 16 oder 32 Threads kopiert werden. Um die Threadanzahl für das Kopieren von Dateien in Richcopy zu ändern, klicken Sie auf **Action** > **Copy options** > **File copy**.<br><br>
![Probleme durch langsames Kopieren von Dateien](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>
- Unzureichende Lese-/Schreibgeschwindigkeiten des Datenträgers des virtuellen Computers. Weitere Informationen finden Sie unter [Problembehandlung für Azure Storage](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Nach außen gerichtete Schnittstelle für lokale Geräte
Wenn die über das Internet zugängliche IP-Adresse des lokalen VPN-Geräts in der [LAN](vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway)-Adressraumdefinition in Azure enthalten ist, kann es vorkommen, dass das VPN nicht angesprochen werden kann oder dass sporadische Unterbrechungen oder Leistungsprobleme auftreten.

## <a name="checking-latency"></a>Überprüfen der Latenz
Mithilfe von „tracert“ können Sie die Ablaufverfolgung zum Microsoft Azure-Randberät ausführen, um zu ermitteln, ob es zwischen Hops zu Verzögerungen kommt, die 100 ms überschreiten.

Führen Sie *tracert* vom lokalen Netzwerk aus für die VIP-Adresse des Azure-Gateways oder den virtuellen Computer aus. Wenn Sie sehen, dass nur * zurückgegeben wird, wissen Sie, dass Sie den Azure-Rand erreicht haben. Wenn DNS-Namen zurückgegeben werden, die „MSN“ enthalten, bedeutet dies, dass Sie den Microsoft-Backbone erreicht haben.<br><br>
![Überprüfen der Latenz](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen oder Hilfe finden Sie unter den folgenden Links:

- [Optimieren des Netzwerkdurchsatzes für virtuelle Azure-Computer](../virtual-network/virtual-network-optimize-network-bandwidth.md)
- [Microsoft-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
