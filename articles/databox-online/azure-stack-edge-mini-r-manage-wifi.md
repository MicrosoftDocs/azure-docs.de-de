---
title: WLAN-Verwaltung für Azure Stack Edge Mini R
description: Hier wird beschrieben, wie Sie WLAN für Azure Stack Edge über das Azure-Portal verwalten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/28/2020
ms.author: alkohli
ms.openlocfilehash: f00be43d023d912d4b0b6e825dfe9d3e0ca2d250
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465744"
---
# <a name="use-the-local-web-ui-to-manage-wireless-connectivity-on-your-azure-stack-edge-mini-r"></a>Verwenden der lokalen Webbenutzeroberfläche, um die drahtlose Konnektivität für Azure Stack Edge Mini R zu verwalten

In diesem Artikel wird beschrieben, wie Sie Drahtlosnetzwerk-Konnektivität für Ihr Gerät vom Typ „ Azure Stack Edge Mini R“ verwalten. Sie können die lokale Webbenutzeroberfläche auf Ihrem Gerät vom Typ „Azure Stack Edge Mini R“ verwenden, um WLAN-Profile hinzuzufügen, zu verbinden und zu löschen.

## <a name="about-wi-fi"></a>Informationen über WLAN

Geräte vom Typ „Azure Stack Edge Mini R“ können auf zwei Arten betrieben werden: angeschlossen an das Netzwerk oder über ein Drahtlosnetzwerk. Das Gerät verfügt über einen WLAN-Port, der aktiviert werden muss, damit das Gerät eine Verbindung mit einem Drahtlosnetzwerk herstellen kann. 

Ihr Gerät verfügt über fünf Ports: PORT 1 bis PORT 4 plus einem fünften WLAN-Port. Das Diagramm zeigt die Rückseite eines Mini-R-Geräts, das mit einem Drahtlosnetzwerk verbunden ist.

![Verkabelung für WLAN](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)


## <a name="add-connect-to-wi-fi-profile"></a>Hinzufügen und Verbinden von Geräten mit einem WLAN-Profil

Führen Sie die folgenden Schritte auf der lokalen Benutzeroberfläche Ihres Geräts aus, um es hinzuzufügen und mit einem WLAN-Profil zu verbinden.

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts zur Seite **Erste Schritte**. Wählen Sie auf der Kachel **Netzwerk** die Option **Konfigurieren** aus.  
    
    Auf Ihrem physischen Gerät stehen fünf Netzwerkschnittstellen zur Verfügung. PORT 1 und PORT 2 sind 1-Gbit/s-Netzwerkschnittstellen. PORT 3 und PORT 4 sind 10-Gbit/s-Netzwerkschnittstellen. Der fünfte Port ist der WLAN-Port. 

    [![Seite mit den Netzwerkeinstellungen auf der lokalen Webbenutzeroberfläche (1)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)  
    
    Wählen Sie den WLAN-Port aus, und konfigurieren Sie die Porteinstellungen. 
    
    > [!IMPORTANT]
    > Es wird dringend empfohlen, eine statische IP-Adresse für den WLAN-Port zu konfigurieren.  

    ![Seite mit den Netzwerkeinstellungen auf der lokalen Webbenutzeroberfläche (2)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    Nachdem Sie die Einstellungen für den WLAN-Port angewendet haben, wird die Seite **Netzwerk** aktualisiert.

    ![Seite mit den Netzwerkeinstellungen auf der lokalen Webbenutzeroberfläche (3)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)

   
2. Wählen Sie **WLAN-Profil hinzufügen** aus, und laden Sie Ihr WLAN-Profil hoch. 

    ![Seite mit den Netzwerkeinstellungen für den WLAN-Port auf der lokalen Webbenutzeroberfläche (1)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    Ein Drahtlosnetzwerk-Profil enthält die SSID (Netzwerkname), den Kennwortschlüssel und Sicherheitsinformationen für die Verbindungsherstellung mit einem Drahtlosnetzwerk. Das WLAN-Profil für Ihre Umgebung erhalten Sie von Ihrem Netzwerkadministrator.

    ![Seite mit den Netzwerkeinstellungen für den WLAN-Port auf der lokalen Webbenutzeroberfläche (2)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    Nach dem Hinzufügen des Profils wird die Liste mit den WLAN-Profilen aktualisiert, und das neue Profil wird angezeigt. Der **Verbindungsstatus** des Profils sollte **Getrennt** lauten. 

    ![Seite mit den Netzwerkeinstellungen für den WLAN-Port auf der lokalen Webbenutzeroberfläche (3)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)
    
3. Nachdem das Drahtlosnetzwerk-Profil erfolgreich geladen wurde, können Sie eine Verbindung mit diesem Profil herstellen. Wählen Sie **Verbindung mit WLAN-Profil herstellen** aus. 

    ![Seite mit den Netzwerkeinstellungen für den WLAN-Port auf der lokalen Webbenutzeroberfläche (4)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

4. Wählen Sie das im vorherigen Schritt hinzugefügte WLAN-Profil und anschließend **Anwenden** aus. 

    ![Seite mit den Netzwerkeinstellungen für den WLAN-Port auf der lokalen Webbenutzeroberfläche (5)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    Der **Verbindungsstatus** sollte in **Verbunden** geändert werden. Die Signalstärke wird aktualisiert, um die Qualität des Signals anzugeben. 

    ![Seite mit den Netzwerkeinstellungen für den WLAN-Port auf der lokalen Webbenutzeroberfläche (6)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > Für die Übertragung großer Datenmengen empfiehlt es sich, anstelle des Drahtlosnetzwerks eine Kabelverbindung zu verwenden. 


## <a name="download-wi-fi-profile"></a>Herunterladen des WLAN-Profils

Sie können ein WLAN-Profil herunterladen, das Sie für die Drahtlosnetzwerk-Konnektivität verwenden.

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts zu **Konfiguration > Netzwerk**. 

2. Wählen Sie unter den Einstellungen für das WLAN-Profil **Profil herunterladen** aus. Dadurch sollte das WLAN-Profil heruntergeladen werden, das Sie zurzeit verwenden.


## <a name="delete-wi-fi-profile"></a>Löschen des WLAN-Profils

Sie können ein WLAN-Profil löschen, das Sie für die Drahtlosnetzwerk-Konnektivität verwenden.


1. Navigieren Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts zu **Konfiguration > Netzwerk**. 

2. Wählen Sie in den Einstellungen für das WLAN-Profil **WLAN-Profil löschen** aus.

3. Wählen Sie auf dem Blatt **WLAN-Profil löschen** das Profil aus, das Sie löschen möchten. Wählen Sie **Anwenden** aus.


## <a name="configure-cisco-wi-fi-profile"></a>Konfigurieren des WLAN-Profils für Cisco

Hier finden Sie eine Anleitung zum Verwalten und Konfigurieren eines Cisco Wireless Controllers und -Zugriffspunkts auf Ihrem Gerät. 

### <a name="dhcp-bridging-mode"></a>DHCP-Bridgingmodus

Wenn Sie einen Cisco Wireless Controller für Ihr Gerät verwenden möchten, müssen Sie den DHCP (Dynamic Host Configuration Protocol)-Bridgingmodus für den WLC (Wireless LAN Controller) aktivieren.

Weitere Informationen finden Sie unter [DHCP-Bridgingmodus](https://www.cisco.com/c/en/us/support/docs/wireless/4400-series-wireless-lan-controllers/110865-dhcp-wlc.html#anc9).

#### <a name="bridging-configuration-example"></a>Beispiel für die Bridgingkonfiguration

Um die DHCP-Bridgingfunktionalität für den Controller zu aktivieren, müssen Sie das DHCP-Proxyfeature auf dem Controller deaktivieren. So aktivieren Sie DHCP-Bridging über die Befehlszeile:

```powershell
(Cisco Controller) > config dhcp proxy disable
(Cisco Controller) > show dhcp proxy
DHCP Proxy Behaviour: disabled
```

Wenn der DHCP-Server nicht im selben L2-Netzwerk (Layer 2) wie der Client vorhanden ist, sollte die Übertragung über ein IP-Hilfsprogramm an den DHCP-Server auf dem Clientgateway weitergeleitet werden. Die Konfiguration kann z. B. so aussehen:

```powershell
Switch#conf t
Switch(config)#interface vlan <client vlan #>
Switch(config-if)#ip helper-address <dhcp server IP>
```

Beim DHCP-Bridgingfeature handelt es sich um eine globale Einstellung, die sich auf alle DHCP-Transaktionen innerhalb des Controllers auswirkt. Sie müssen Anweisungen des IP-Hilfsprogramms in der verkabelten Infrastruktur für alle erforderlichen virtuellen lokalen Netzwerke (VLANs) auf dem Controller hinzufügen.

### <a name="enable-the-passive-client-for-wlan"></a>Aktivieren des passiven Clients für WLAN

So aktivieren Sie das Feature „Passiver Client“ für WLANs (Wireless Local Area Networks) auf einem Cisco Wireless Controller:

* Für die Schnittstelle, die dem WLAN zugeordnet ist, muss VLAN-Tagging aktiviert sein.
* Für das WLAN muss Multicast-VLAN aktiviert werden.
* Die GARP-Weiterleitung muss auf dem WLC aktiviert werden.

Weitere Informationen finden Sie unter den Informationen zur [Multicastoptimierung für Multicast-VLANs](https://www.cisco.com/c/en/us/td/docs/wireless/controller/8-5/config-guide/b_cg85/wlan_interfaces.html).

### <a name="troubleshoot"></a>Problembehandlung

Wenn Sie Probleme mit der IP-Adresszuweisung bei VMs haben, die auf einem Gerät vom Typ „Azure Stack Edge Mini R“ ausgeführt werden, sollten Sie die oben genannten Konfigurationseinstellungen in Ihrer Netzwerkumgebung überprüfen.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich unter [Bereitstellen eines Geräts vom Typ „Azure Stack Edge Mini R“](azure-stack-edge-mini-r-deploy-prep.md).
