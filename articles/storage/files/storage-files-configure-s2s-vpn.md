---
title: Konfigurieren eines S2S-VPN (Site-to-Site) zur Verwendung mit Azure Files | Microsoft-Dokumentation
description: So konfigurieren Sie ein S2S-VPN (Site-to-Site) zur Verwendung mit Azure Files.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0fa3fb8040fd79d68f9260ab520d3b6823ab363d
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629290"
---
# <a name="configure-a-site-to-site-vpn-for-use-with-azure-files"></a>Konfigurieren eines Site-to-Site-VPN zur Verwendung mit Azure Files
Sie können ein S2S-VPN (Site-to-Site) verwenden, um Ihre Azure-Dateifreigaben aus Ihrem lokalen Netzwerk über SMB einzubinden, ohne Port 445 zu öffnen. Sie können ein Site-to-Site-VPN mithilfe des [Azure VPN Gateways](../../vpn-gateway/vpn-gateway-about-vpngateways.md) einrichten, einer Azure-Ressource, die VPN-Dienste anbietet, und die in einer Ressourcengruppe zusammen mit Speicherkonten oder anderen Azure-Ressourcen bereitgestellt wird.

![Ein Topologiediagramm, das die Topologie eines Azure VPN Gateways veranschaulicht, das eine Azure-Dateifreigabe über ein S2S-VPN mit einem lokalen Standort verbindet](media/storage-files-configure-s2s-vpn/s2s-topology.png)

Es wird dringend empfohlen, vor der Lektüre des vorliegenden Artikels den Artikel [Azure Files – Überlegungen zum Netzwerkbetrieb](storage-files-networking-overview.md) zu lesen, der eine umfassende Erläuterung der für Azure Files verfügbaren Netzwerkoptionen enthält.

Der Artikel beschreibt die Schritte zur Konfiguration eines Site-to-Site-VPN, um Azure-Dateifreigaben direkt lokal einzubinden. Wenn Sie den Synchronisierungsdatenverkehr für die Azure-Dateisynchronisierung über ein Site-to-Site-VPN leiten möchten, lesen Sie den Artikel [Konfigurieren der Proxy- und Firewalleinstellungen der Dateisynchronisierung](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Voraussetzungen
- Eine Azure-Dateifreigabe, die Sie lokal einbinden möchten. Azure-Dateifreigaben werden in Speicherkonten bereitgestellt. Hierbei handelt es sich um Verwaltungskonstrukte, die einen gemeinsam genutzten Speicherpool darstellen, in dem Sie mehrere Dateifreigaben sowie weitere Speicherressourcen wie Blobcontainer oder Warteschlangen bereitstellen können. Weitere Informationen zum Bereitstellen von Azure-Dateifreigaben und Speicherkonten finden Sie unter [Erstellen einer Azure-Dateifreigabe](storage-how-to-create-file-share.md).

- Ein privater Endpunkt für das Speicherkonto mit der Azure-Dateifreigabe, die Sie lokal bereitstellen möchten. Weitere Informationen zum Erstellen eines privaten Endpunkts finden Sie unter [Konfigurieren von Azure Files-Netzwerkendpunkten](storage-files-networking-endpoints.md?tabs=azure-portal). 

- Ein mit Azure VPN Gateway kompatibles Netzwerkgerät oder ein Netzwerkserver in Ihrem lokalen Rechenzentrum. Azure Files ist unabhängig vom ausgewählten lokalen Netzwerkgerät, aber Azure VPN Gateway verwaltet eine [Liste der getesteten Geräte](../../vpn-gateway/vpn-gateway-about-vpn-devices.md). Unterschiedliche Netzwerkgeräte bieten unterschiedliche Features, Leistungsmerkmale und Verwaltungsfunktionen. Dies sollten Sie bei der Auswahl eines Netzwerkgeräts berücksichtigen.

    Wenn Sie kein vorhandenes Netzwerkgerät haben, bietet Ihnen Windows Server eine integrierte Serverrolle, Routing and Remote Access (RRAS), die als lokales Netzwerkgerät verwendet werden kann. Weitere Informationen zum Konfigurieren von Routing and Remote Access in Windows Server finden Sie unter [RAS-Gateway](/windows-server/remote/remote-access/ras-gateway/ras-gateway).

## <a name="add-storage-account-to-vnet"></a>Hinzufügen eines Speicherkontos zu VNET
Navigieren Sie im Azure-Portal zum Speicherkonto mit der Azure-Dateifreigabe, die Sie lokal einbinden möchten. Wählen Sie im Inhaltsverzeichnis des Speicherkontos den Eintrag **Firewalls und virtuelle Netzwerke** aus. Wenn Sie Ihrem Speicherkonto bei der Erstellung kein virtuelles Netzwerk hinzugefügt haben, wird im angezeigten Bereich für das Optionsfeld **Zugriff erlauben von** **Alle Netzwerke** ausgewählt.

Wenn Sie Ihr Speicherkonto dem gewünschten virtuellen Netzwerk hinzufügen möchten, wählen Sie **Ausgewählte Netzwerke** aus. Klicken Sie unter der Unterüberschrift **Virtuelle Netzwerke** je nach gewünschtem Zustand entweder auf **+ Vorhandenes virtuelles Netzwerk hinzufügen** oder **+ Neues virtuelles Netzwerk hinzufügen**. Wenn Sie ein neues virtuelles Netzwerk erstellen, wird auch eine neue Azure-Ressource erstellt. Die neue oder vorhandene VNET-Ressource muss sich nicht in derselben Ressourcengruppe oder im selben Abonnement befinden wie das Speicherkonto, aber in derselben Region wie das Speicherkonto. Außerdem müssen VNET und VPN Gateway in derselben Ressourcengruppe und im selben Abonnement bereitgestellt werden. 

![Screenshot der Azure-Portal mit der Option zum Hinzufügen eines vorhandenen oder neuen virtuellen Netzwerks zum Speicherkonto](media/storage-files-configure-s2s-vpn/add-vnet-1.png)

Wenn Sie ein bestehendes virtuelles Netzwerk hinzufügen, werden Sie aufgefordert, ein oder mehrere Subnetze dieses virtuellen Netzwerks auszuwählen, zu dem das Speicherkonto hinzugefügt werden soll. Wenn Sie ein neues virtuelles Netzwerk erstellen, erstellen Sie gleichzeitig ein Subnetz. Später können Sie über die entstehende Azure-Ressource weitere Subnetze für das virtuelle Netzwerk hinzufügen.

Wenn Sie Ihrem Abonnement noch kein Speicherkonto hinzugefügt haben, muss der Endpunkt des Microsoft.Storage-Diensts dem virtuellen Netzwerk hinzugefügt werden. Dies kann einige Zeit in Anspruch nehmen. Bis dieser Vorgang abgeschlossen ist, können Sie nicht auf die Azure-Dateifreigaben innerhalb dieses Speicherkonto zugreifen, auch nicht über die VPN-Verbindung. 

## <a name="deploy-an-azure-vpn-gateway"></a>Bereitstellen einer Azure VPN Gateway-Instanz
Klicken Sie im Inhaltsverzeichnis des Azure-Portals auf **Neue Ressource erstellen**, und suchen Sie nach *Virtuelles Netzwerkgateway*. Ihr virtuelles Netzwerkgateway muss sich im selben Abonnement, derselben Azure-Region und Ressourcengruppe wie das virtuelle Netzwerkbefinden, das Sie im vorherigen Schritt bereitgestellt haben (beachten Sie, dass die Ressourcengruppe bei der Auswahl des virtuellen Netzwerks automatisch ausgewählt wird). 

Zum Bereitstellen eines Azure VPN Gateway müssen Sie die folgenden Felder ausfüllen:

- **Name**: Der Name der Azure-Ressource für das VPN Gateway. Dies kann ein beliebiger Name sein, den Sie gut für Ihre Verwaltung verwenden können.
- **Region**: Die Region, in der das VPN Gateway bereitgestellt wird.
- **Gatewaytyp**: Zum Bereitstellen eines Site-to-Site-VPN müssen Sie **VPN** auswählen.
- **VPN-Typ:** Abhängig von Ihrem VPN-Gerät können Sie entweder *Routenbasiert** oder **Richtlinienbasiert** auswählen. Routenbasierte VPNs unterstützen IKEv2, richtlinienbasierte VPNs unterstützen nur IKEv1. Weitere Informationen zu den beiden Typen von VPN-Gateways finden Sie unter [Informationen zu richtlinienbasierten und routenbasierten VPN-Gateways](../../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md#about).
- **SKU**: Die SKU steuert die Anzahl der zulässigen Site-zu-Site-Tunnel und die gewünschte Leistung des VPN. Weitere Informationen zum Auswählen der entsprechenden SKU für Ihren Anwendungsfall finden Sie in der Liste [Gateway-SKU](../../vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku). Die SKU des VPN Gateways kann bei Bedarf später geändert werden.
- **Virtuelles Netzwerk:** Das im vorherigen Schritt erstellte virtuelle Netzwerk.
- **Öffentliche IP-Adresse:** Die IP-Adresse des VPN Gateways, die für das Internet verfügbar gemacht wird. Wahrscheinlich müssen Sie eine neue IP-Adresse erstellen. Sie können ggf. aber auch eine vorhandene, nicht verwendete IP-Adresse verwenden. Wenn Sie **Neue erstellen** auswählen, wird eine neue IP-Adresse für die Azure-Ressource in der gleichen Ressourcengruppe wie das VPN Gateway erstellt, und der Name **Öffentliche IP-Adresse** ist der Name der neu erstellten IP-Adresse. Wenn Sie **Vorhandene verwenden** auswählen, müssen Sie die vorhandene, nicht verwendete IP-Adresse auswählen.
- **Aktiv/Aktiv-Modus aktivieren**: Wählen Sie **Aktiviert** nur dann aus, wenn Sie eine Aktiv/Aktiv-Gatewaykonfiguration erstellen. Andernfalls behalten Sie die Auswahl von **Deaktiviert** bei. Weitere Informationen zum Aktiv/Aktiv-Modus finden Sie unter [Standortübergreifende Verbindungen und VNET-to-VNET-Verbindungen mit hoher Verfügbarkeit](../../vpn-gateway/vpn-gateway-highlyavailable.md).
- **BGP-ASN konfigurieren:** Wählen Sie **Aktiviert** nur dann aus, wenn dies für Ihre Konfiguration ausdrücklich erforderlich ist. Weitere Informationen zu dieser Einstellung finden Sie unter [Übersicht über BGP mit Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md).

Wählen Sie **Überprüfen + Erstellen**, um das VPN Gateway zu erstellen. Die gesamte Erstellung und Bereitstellung eines VPN Gateways kann bis zu 45 Minuten dauern.

### <a name="create-a-local-network-gateway-for-your-on-premises-gateway"></a>Erstellen eines lokalen Netzwerkgateways für Ihr lokales Gateway 
Ein lokales Netzwerkgateway ist eine Azure-Ressource, die Ihr lokales Netzwerkgerät darstellt. Klicken Sie im Inhaltsverzeichnis des Azure-Portals auf **Neue Ressource erstellen**, und suchen Sie nach *Lokales Netzwerkgateway*. Das lokale Netzwerkgateway ist eine Azure-Ressource, die zusammen mit Ihrem Speicherkonto, dem virtuellen Netzwerk und dem VPN Gateway bereitgestellt wird, sich aber nicht in derselben Ressourcengruppe oder demselben Abonnement wie das Speicherkonto befinden muss. 

Zum Bereitstellen eines lokalen Netzwerkgateways müssen Sie die folgenden Felder ausfüllen:

- **Name**: Der Name der Azure-Ressource für das lokale Netzwerkgateway. Dies kann ein beliebiger Name sein, den Sie gut für Ihre Verwaltung verwenden können.
- **IP-Adresse**: Die öffentliche IP-Adresse Ihres lokalen Gateways.
- **Adressraum:** Der Adressbereich für das Netzwerk, das dieses lokale Netzwerkgateway darstellt. Sie können mehrere Adressraumbereiche hinzufügen. Achten Sie jedoch darauf, dass sich die hier angegebenen Bereiche nicht mit den Bereichen anderer Netzwerke überschneiden, mit denen Sie eine Verbindung herstellen möchten. 
- **BGP-Einstellungen konfigurieren**: Konfigurieren Sie BGP-Einstellungen nur dann, wenn diese Einstellung für Ihre Konfiguration erforderlich ist. Weitere Informationen zu dieser Einstellung finden Sie unter [Übersicht über BGP mit Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md).
- **Abonnement**: Das gewünschte Abonnement. Dies muss nicht dasselbe Abonnement sein, das für das VPN Gateway oder das Speicherkonto verwendet wird.
- **Ressourcengruppe**: Die gewünschte Ressourcengruppe. Dies muss nicht dieselbe Ressourcengruppe sein, die für das VPN Gateway oder das Speicherkonto verwendet wird.
- **Standort**: Die Azure-Region, in der das lokale Netzwerkgateway erstellt werden soll. Diese muss mit der Region übereinstimmen, die Sie für das VPN Gateway und das Speicherkonto ausgewählt haben.

Klicken Sie auf **Erstellen**, um das lokale Netzwerkgateway zu erstellen.  

## <a name="configure-on-premises-network-appliance"></a>Konfigurieren eines lokale Netzwerkgeräts
Die spezifischen Schritte zur Konfiguration Ihres lokalen Netzwerkgeräts hängen von dem von Ihrer Organisation ausgewählten Netzwerkgerät ab. Je nachdem, welches Gerät Ihre Organisation ausgewählt hat, enthält die [Liste der getesteten Geräte](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) einen Link zu den Anweisungen Ihres Geräteanbieters für die Konfiguration mit Azure VPN Gateway.

## <a name="create-the-site-to-site-connection"></a>Erstellen der Site-to-Site-Verbindung
Um die Bereitstellung eines S2S-VPN abzuschließen, müssen Sie eine Verbindung zwischen Ihrem lokalen Netzwerkgerät (dargestellt durch das lokale Netzwerkgateway) und dem VPN Gateway herstellen. Navigieren Sie dafür zum oben erstellten VPN Gateway. Wählen Sie im Inhaltsverzeichnis für das VPN Gateway die Option **Verbindungen** aus, und klicken Sie auf **Hinzufügen**. Im daraufhin angezeigten Bereich **Verbindung hinzufügen** müssen folgende Felder ausgefüllt werden:

- **Name**: Der Name der Verbindung. Ein VPN Gateway kann mehrere Verbindungen hosten. Wählen Sie daher einen für Ihre Verwaltung hilfreichen Namen, durch den diese bestimmte Verbindung von den anderen unterschieden werden kann.
- **Verbindungstyp**: Da es sich hier um eine Site-to-Site-Verbindung handelt, wählen Sie in der Dropdownliste **Site-to-Site (IPSec)** aus.
- **Gateway für virtuelle Netzwerke**: Dieses Feld wird automatisch für das VPN Gateway ausgewählt, mit dem Sie die Verbindung herstellen, und kann nicht geändert werden.
- **Lokales Netzwerkgateway**: Dies ist das lokale Netzwerkgateway, mit dem Sie Ihr VPN Gateway verbinden möchten. Im anschließend angezeigten Auswahlbereich finden Sie den Namen des lokalen Netzwerkgateways, das Sie oben erstellt haben.
- **Gemeinsam verwendeter Schlüssel (PSK)** : Eine Mischung aus Buchstaben und Zahlen zum Erstellen der Verschlüsselung für die Verbindung. Sowohl im virtuellen Netzwerk als auch im lokalen Netzwerkgateway muss eder gleiche gemeinsam verwendete Schlüssel genutzt werden. Wenn das Gatewaygerät keinen bereitstellt, können Sie einen hier generieren und für Ihr Gerät bereitstellen.

Klicken Sie auf **OK**, um die Verbindung zu erstellen. Auf der Seite **Verbindungen** können Sie sehen, ob die Verbindung erfolgreich hergestellt wurde.

## <a name="mount-azure-file-share"></a>Einbinden einer Azure-Dateifreigabe 
Im letzten Schritt der Konfiguration eines S2S-VPN müssen Sie überprüfen, ob das VPN für Azure Files funktioniert. Hierzu können Sie Ihre Azure-Dateifreigabe lokal in Ihr bevorzugtes Betriebssystem einbinden. Die Anweisungen zum Einbinden für Ihr Betriebssystem finden Sie hier:

- [Windows](storage-how-to-use-files-windows.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Linux](storage-how-to-use-files-linux.md)

## <a name="see-also"></a>Weitere Informationen
- [Azure Files – Überlegungen zum Netzwerkbetrieb](storage-files-networking-overview.md)
- [Konfigurieren eines P2S-VPN (Point-to-Site) unter Windows zur Verwendung mit Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Konfigurieren eines P2S-VPN (Point-to-Site) unter Linux zur Verwendung mit Azure Files](storage-files-configure-p2s-vpn-linux.md)