---
title: 'Azure-VPN Gateway: Informationen zu VPN-Geräten für Verbindungen'
description: In diesem Artikel werden VPN-Geräte und IPsec-Parameter für standortübergreifende S2S-VPN Gateway-Verbindungen beschrieben. Er enthält Links zu Konfigurationsanleitungen und -beispielen.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 12/02/2020
ms.author: yushwang
ms.openlocfilehash: ae498b39a421db19f0d4e0a8daca58730321b58c
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546810"
---
# <a name="about-vpn-devices-and-ipsecike-parameters-for-site-to-site-vpn-gateway-connections"></a>Informationen zu VPN-Geräten und IPsec-/IKE-Parametern für VPN-Gatewayverbindungen zwischen Standorten.

Sie benötigen ein VPN-Gerät, um eine standortübergreifende S2S-VPN-Verbindung (Site-to-Site) per VPN Gateway zu konfigurieren. Standort-zu-Standort-Verbindungen können zum Erstellen einer Hybridlösung verwendet werden. Sie können sie auch zum Herstellen von sicheren Verbindungen zwischen Ihren lokalen Netzwerken und den virtuellen Netzwerken verwenden. Dieser Artikel enthält eine Liste mit überprüften VPN-Geräten und eine Liste mit IPsec-/IKE-Parametern für VPN-Gateways.

> [!IMPORTANT]
> Bei Verbindungsproblemen zwischen Ihren lokalen VPN-Geräten und VPN-Gateways helfen Ihnen die Informationen unter [Bekannte Probleme mit der Gerätekompatibilität](#known) weiter.
>

### <a name="items-to-note-when-viewing-the-tables"></a>Was Sie beim Anzeigen der Tabellen beachten sollten:

* Die Terminologie für Azure-VPN-Gateways wurde geändert. Die Änderung betrifft nur die Namen. Dies ist nicht mit einer Änderung der Funktionalität verbunden.
  * Statisches Routing = Richtlinienbasiert (PolicyBased)
  * Dynamisches Routing = Routenbasiert (RouteBased)
* Die Spezifikationen für Hochleistungs-VPN-Gateways und routenbasierte VPN-Gateways bleiben unverändert, sofern nicht anders angegeben. Beispielsweise sind die überprüften VPN-Geräte, die mit den routenbasierten VPN-Gateways kompatibel sind, auch mit dem Hochleistungs-VPN-Gateway kompatibel.

## <a name="validated-vpn-devices-and-device-configuration-guides"></a><a name="devicetable"></a>Überprüfte VPN-Geräte und Konfigurationshandbücher für Geräte

Wir haben in Zusammenarbeit mit Geräteherstellern eine Reihe von VPN-Standardgeräten getestet. Alle Geräte der in der folgenden Liste aufgeführten Gerätefamilien sollten mit VPN-Gateways kompatibel sein. Informationen zur Verwendung des VPN-Typs (richtlinienbasiert oder routenbasiert) für die VPN-Gatewaylösung, die Sie konfigurieren möchten, finden Sie unter [Informationen zu VPN Gateway-Einstellungen](vpn-gateway-about-vpn-gateway-settings.md#vpntype).

Hilfreiche Informationen zur Konfiguration des VPN-Geräts finden Sie unter den Links für die entsprechende Gerätefamilie. Die Links zu den Konfigurationsanleitungen werden nach bestem Wissen bereitgestellt. Support für VPN-Geräte erhalten Sie vom jeweiligen Gerätehersteller.

|**Hersteller**          |**Gerätefamilie**     |**Betriebssystemversion (Min.)** |**PolicyBased-Konfiguration – Anleitung** |**RouteBased-Konfiguration – Anleitung** |
| ---                | ---                  | ---                   | ---            | ---           |
| A10 Networks, Inc. |Thunder CFW           |ACOS 4.1.1             |Nicht kompatibel  |[Konfigurationshandbuch](https://www.a10networks.com/wp-content/uploads/A10-DG-16161-EN.pdf)|
| Allied Telesis     |VPN-Router der AR-Serie |AR-Serie 5.4.7+               | [Konfigurationshandbuch](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router) |[Konfigurationshandbuch](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router)|
| Arista | CloudEOS-Router | vEOS 4.24.0FX | (nicht getestet) | [Konfigurationshandbuch](https://www.arista.com/en/cg-veos-router/veos-router-cloudeos-ipsec-connectivity-to-azure-virtual-network-gateway) |
| Barracuda Networks, Inc. |Barracuda CloudGen Firewall |PolicyBased: 5.4.3<br>RouteBased: 6.2.0 |[Konfigurationshandbuch](https://campus.barracuda.com/product/cloudgenfirewall/doc/79462887/how-to-configure-an-ikev1-ipsec-site-to-site-vpn-to-the-static-microsoft-azure-vpn-gateway/) |[Konfigurationshandbuch](https://campus.barracuda.com/product/cloudgenfirewall/doc/79462889/how-to-configure-bgp-over-ikev2-ipsec-site-to-site-vpn-to-an-azure-vpn-gateway/) |
| Check Point |Sicherheitsgateway |R80.10 |[Konfigurationshandbuch](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Konfigurationshandbuch](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco              |ASA       |8.3<br>8.4+ (IKEv2* ) |Unterstützt |[Konfigurationshandbuch * ](https://www.cisco.com/c/en/us/support/docs/security/adaptive-security-appliance-asa-software/214109-configure-asa-ipsec-vti-connection-to-az.html) |
| Cisco |ASR |PolicyBased: IOS 15.1<br>RouteBased: IOS 15.2 |Unterstützt |Unterstützt |
| Cisco | CSR | RouteBased: IOS-XE 16.10 | (nicht getestet) | [Konfigurationsskript](vpn-gateway-download-vpndevicescript.md) |
| Cisco |ISR |PolicyBased: IOS 15.0<br>RouteBased*: IOS 15.1 |Unterstützt |Unterstützt |
| Cisco |Meraki (MX) | MX v15.12 |Nicht kompatibel | [Konfigurationshandbuch](https://documentation.meraki.com/MX/Site-to-site_VPN/Configuring_Site_to_Site_VPN_tunnels_to_Azure_VPN_Gateway) |
| Cisco | vEdge (Viptela-Betriebssystem) | 18.4.0 (Aktiv/Passiv-Modus)<br><br>19.2 (Aktiv/Aktiv-Modus) | Nicht kompatibel |  [Manuelle Konfiguration (aktiv/passiv)](https://community.cisco.com/t5/networking-documents/how-to-configure-ipsec-vpn-connection-between-cisco-vedge-and/ta-p/3841454)<br><br>[Cloudeinstiegskonfiguration (aktiv/aktiv)](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/Network-Optimization-and-High-Availability/Network-Optimization-High-Availability-book/b_Network-Optimization-and-HA_chapter_00.html) |
| Citrix |NetScaler MPX, SDX, VPX |ab 10.1 |[Konfigurationshandbuch](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |Nicht kompatibel |
| F5 |BIG-IP-Serie |12.0 |[Konfigurationshandbuch](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Konfigurationshandbuch](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.6 | (nicht getestet) |[Konfigurationshandbuch](https://docs.fortinet.com/document/fortigate/5.6.0/cookbook/255100/ipsec-vpn-to-azure) |
| Hillstone Networks | Next-Gen Firewalls (NGFW) | 5.5R7  | (nicht getestet) | [Konfigurationshandbuch](https://www.hillstonenet.com/wp-content/uploads/How-to-setup-Site-to-Site-VPN-between-Microsoft-Azure-and-an-on-premise-Hillstone-Networks-Security-Gateway.pdf) |
| Internet Initiative Japan (IIJ) |SEIL-Serie |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Konfigurationshandbuch](https://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |Nicht kompatibel |
| Juniper |SRX |PolicyBased: JunOS 10.2<br>Routebased: JunOS 11.4 |Unterstützt |[Konfigurationsskript](vpn-gateway-download-vpndevicescript.md) |
| Juniper |J-Serie |PolicyBased: JunOS 10.4r9<br>RouteBased: JunOS 11.4 |Unterstützt |[Konfigurationsskript](vpn-gateway-download-vpndevicescript.md) |
| Juniper |ISG |ScreenOS 6.3 |Unterstützt |[Konfigurationsskript](vpn-gateway-download-vpndevicescript.md) |
| Juniper |SSG |ScreenOS 6.2 |Unterstützt |[Konfigurationsskript](vpn-gateway-download-vpndevicescript.md) |
| Juniper |MX |JunOS 12.x|Unterstützt |[Konfigurationsskript](vpn-gateway-download-vpndevicescript.md) |
| Microsoft |Routing- und RAS-Dienst |Windows Server 2012 |Nicht kompatibel |Unterstützt |
| Open Systems AG |Mission Control Security Gateway |– |[Konfigurationshandbuch](https://open-systems.com/wp-content/uploads/2019/12/OpenSystems-AzureVPNSetup-Installation-Guide.pdf) |Nicht kompatibel |
| Palo Alto Networks |Alle Geräte mit PAN-OS 5.0 |PAN-OS<br>PolicyBased: 6.1.5 oder höher<br>RouteBased: 7.1.4 |Unterstützt |[Konfigurationshandbuch](https://knowledgebase.paloaltonetworks.com/KCSArticleDetail?id=kA10g000000Cm6WCAS) |
| Sentrium (Entwickler) | VyOS | VyOS 1.2.2 | (nicht getestet) | [Konfigurationshandbuch](https://vyos.readthedocs.io/en/latest/appendix/examples/azure-vpn-bgp.html)|
| ShareTech | UTM der nächsten Generation (NU-Serie) | 9.0.1.3 | Nicht kompatibel | [Konfigurationshandbuch](http://www.sharetech.com.tw/images/file/Solution/NU_UTM/S2S_VPN_with_Azure_Route_Based_en.pdf) |
| SonicWall |TZ-Serie, NSA-Serie<br>SuperMassive-Serie<br>E-Class-NSA-Serie |SonicOS 5.8.x<br>SonicOS 5.9.x<br>SonicOS 6.x |Nicht kompatibel |[Konfigurationshandbuch](https://www.sonicwall.com/support/knowledge-base/170505320011694) |
| Sophos | XG-Firewall der nächsten Generation | XG v17 | (nicht getestet) | [Konfigurationshandbuch](https://community.sophos.com/kb/127546)<br><br>[Configuration guide - Multiple SAs](https://community.sophos.com/kb/en-us/133154) (Konfigurationshandbuch: Mehrere SAs) |
| Synology | MR2200ac <br>RT2600ac <br>RT1900ac | SRM1.1.5/VpnPlusServer-1.2.0 | (nicht getestet) | [Konfigurationshandbuch](https://www.synology.com/en-global/knowledgebase/SRM/tutorial/VPN/How_to_set_up_Site_to_Site_VPN_between_Synology_Router_and_MS_Azure) |
| Ubiquiti | EdgeRouter | EdgeOS v1.10 | (nicht getestet) | [BGP über IKEv2/IPsec](https://help.ubnt.com/hc/en-us/articles/115012374708)<br><br>[VTI über IKEv2/IPsec](https://help.ubnt.com/hc/en-us/articles/115012305347) |
| Ultra | 3E-636L3 | 5.2.0.T3 Build 13  | (nicht getestet) | [Konfigurationshandbuch](https://ultra-3eti.com/wp-content/uploads/2020/07/Azure-VPN-636L3-Site-to-Site-Test-Notes.pdf) |
| WatchGuard |All |Fireware XTM<br> PolicyBased: v11.11.x<br>RouteBased: v11.12.x |[Konfigurationshandbuch](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Konfigurationshandbuch](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|
| Zyxel |ZyWALL USG-Serie<br>ZyWALL ATP-Serie<br>ZyWALL VPN-Serie | ZLD v4.32+ | (nicht getestet) | [VTI über IKEv2/IPsec](https://businessforum.zyxel.com/discussion/2648/)<br><br>[BGP über IKEv2/IPsec](https://businessforum.zyxel.com/discussion/2650/)|

> [!NOTE]
>
> ( * ) Die Cisco ASA-Version 8.4 und höhere Versionen fügen IKEv2-Unterstützung hinzu und können mithilfe der benutzerdefinierten IPsec-/IKE-Richtlinie mit der Option „UsePolicyBasedTrafficSelectors“ eine Verbindung mit Azure VPN Gateway herstellen. Informationen finden Sie in [diesem](vpn-gateway-connect-multiple-policybased-rm-ps.md) Artikel mit Anleitungen.
>
> ( ** ) Router der Serie ISR 7200 unterstützen nur richtlinienbasierte VPNs.

## <a name="download-vpn-device-configuration-scripts-from-azure"></a><a name="configscripts"></a>Herunterladen von Konfigurationsskripts für ein VPN-Gerät von Azure

Für bestimmte Geräte können direkt von Azure Konfigurationsskripts heruntergeladen werden. Weitere Informationen und Downloadanweisungen finden Sie unter [Herunterladen von VPN-Gerätekonfigurationsskripts für S2S-VPN-Verbindungen](vpn-gateway-download-vpndevicescript.md).

### <a name="devices-with-available-configuration-scripts"></a>Geräte mit verfügbaren Konfigurationsskripts

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="non-validated-vpn-devices"></a><a name="additionaldevices"></a>Nicht überprüfte VPN-Geräte

Falls Ihr Gerät nicht in der Tabelle mit den überprüften VPN-Geräten enthalten ist, kann es unter Umständen für eine Site-to-Site-Verbindung verwendet werden. Zusätzliche Unterstützung und Konfigurationsanweisungen erhalten Sie vom Gerätehersteller.

## <a name="editing-device-configuration-samples"></a><a name="editing"></a>Bearbeiten von Gerätekonfigurationsbeispielen

Nachdem Sie die bereitgestellte Konfigurationsvorlage für das VPN-Gerät heruntergeladen haben, müssen Sie einige der Werte entsprechend den Einstellungen Ihrer Umgebung ersetzen.

### <a name="to-edit-a-sample"></a>So bearbeiten Sie eine Vorlage:

1. Öffnen Sie die Vorlage im Editor.
2. Suchen und ersetzen Sie alle <*text*>-Zeichenfolgen mit den Werten, die für Ihre Umgebung gelten. Schließen Sie dabei unbedingt „<“ und „>“ mit ein. Wenn ein Name angegeben ist, sollte der ausgewählte Name eindeutig sein. Sehen Sie zuerst in der Dokumentation des Geräteherstellers nach, falls ein Befehl nicht funktioniert.

| **Vorlagentext** | **Ändern in** |
| --- | --- |
| &lt;RP_OnPremisesNetwork&gt; |Der von Ihnen gewählte Name für dieses Objekt. Beispiel: meinLokalesNetzwerk |
| &lt;RP_AzureNetwork&gt; |Der von Ihnen gewählte Name für dieses Objekt. Beispiel: meinAzureNetzwerk |
| &lt;RP_AccessList&gt; |Der von Ihnen gewählte Name für dieses Objekt. Beispiel: meineAzureZugriffsliste |
| &lt;RP_IPSecTransformSet&gt; |Der von Ihnen gewählte Name für dieses Objekt. Beispiel: meinIPSecTransformSet |
| &lt;RP_IPSecCryptoMap&gt; |Der von Ihnen gewählte Name für dieses Objekt. Beispiel: meineIPSecKryptoMap |
| &lt;SP_AzureNetworkIpRange&gt; |Geben Sie den Bereich an. Beispiel: 192.168.0.0 |
| &lt;SP_AzureNetworkSubnetMask&gt; |Geben Sie die Subnetzmaske an. Beispiel: 255.255.0.0 |
| &lt;SP_OnPremisesNetworkIpRange&gt; |Geben Sie den lokalen Bereich an. Beispiel: 10.2.1.0 |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; |Geben Sie die lokale Subnetzmaske an. Beispiel: 255.255.255.0 |
| &lt;SP_AzureGatewayIpAddress&gt; |Diese Informationen gelten nur für Ihr virtuelles Netzwerk und befinden sich im Verwaltungsportal unter **Gateway-IP-Adresse**. |
| &lt;SP_PresharedKey&gt; |Diese Informationen gelten nur für Ihr virtuelles Netzwerk und befinden sich im Verwaltungsportal unter "Schlüssel verwalten". |

## <a name="default-ipsecike-parameters"></a><a name="ipsec"></a>IPsec-/IKE-Standardparameter

Die folgenden Tabellen enthalten die Kombinationen aus Algorithmen und Parametern, die von Azure-VPN-Gateways in der Standardkonfiguration verwendet werden (**Standardrichtlinien**). Für routenbasierte VPN-Gateways, die mit dem Azure Resource Manager-Bereitstellungsmodell erstellt wurden, können Sie für jede einzelne Verbindung eine benutzerdefinierte Richtlinie angeben. Ausführliche Anweisungen finden Sie unter [Konfigurieren der IPsec/IKE-Richtlinie für S2S-VPN- oder VNet-zu-VNet-Verbindungen](vpn-gateway-ipsecikepolicy-rm-powershell.md).

Darüber hinaus müssen Sie TCP **MSS** mit **1350** verknüpfen. Wenn Ihre VPN-Geräte MSS-Clamping nicht unterstützen, können Sie stattdessen alternativ den **MTU**-Wert der Tunnelschnittstelle auf **1400** Byte festlegen.

Für die folgenden Tabellen gilt:

* SA = Sicherheitszuordnung
* IKE-Phase 1 wird auch als „Hauptmodus“ bezeichnet.
* IKE-Phase 2 wird auch als „Schnellmodus“ bezeichnet.

### <a name="ike-phase-1-main-mode-parameters"></a>Parameter der IKE-Phase 1 (Hauptmodus)

| **Eigenschaft**          |**PolicyBased**    | **RouteBased**    |
| ---                   | ---               | ---               |
| IKE-Version           |IKEv1              |IKEv1 und IKEv2    |
| Diffie-Hellman-Gruppe  |Gruppe 2 (1024 Bit) |Gruppe 2 (1024 Bit) |
| Authentifizierungsmethode |Vorab ausgetauschter Schlüssel     |Vorab ausgetauschter Schlüssel     |
| Verschlüsselung und Hashalgorithmen |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |1. AES256, SHA1<br>2. AES256, SHA256<br>3. AES128, SHA1<br>4. AES128, SHA256<br>5. 3DES, SHA1<br>6. 3DES, SHA256 |
| SA-Gültigkeitsdauer           |28.800 Sekunden     |28.800 Sekunden     |

### <a name="ike-phase-2-quick-mode-parameters"></a>Parameter der IKE-Phase 2 (Schnellmodus)

| **Eigenschaft**                  |**PolicyBased**| **RouteBased**                              |
| ---                           | ---           | ---                                         |
| IKE-Version                   |IKEv1          |IKEv1 und IKEv2                              |
| Verschlüsselung und Hashalgorithmen |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |[SA-Angebote für RouteBased QM](#RouteBasedOffers) |
| SA-Gültigkeitsdauer (Zeit)            |3\.600 Sekunden  |27.000 Sekunden                               |
| SA-Gültigkeitsdauer (Bytes)           |102.400.000 KB |102.400.000 KB                               |
| Perfect Forward Secrecy (PFS) |Nein             |[SA-Angebote für RouteBased QM](#RouteBasedOffers) |
| Dead Peer Detection (DPD)     |Nicht unterstützt  |Unterstützt                                    |


### <a name="routebased-vpn-ipsec-security-association-ike-quick-mode-sa-offers"></a><a name ="RouteBasedOffers"></a>RouteBased-VPN-Angebote der IPsec Security Association (IKE-Schnellmodus-SA)

Die folgende Tabelle enthält IPsec-SA-Angebote (IKE-Schnellmodus). Angebote werden in der Reihenfolge ihrer Priorität aufgeführt, in der das Angebot dargeboten oder akzeptiert wurde.

#### <a name="azure-gateway-as-initiator"></a>Azure-Gateway als Initiator

|-  |**Verschlüsselung**|**Authentifizierung**|**PFS-Gruppe**|
|---| ---          |---               |---          |
| 1 |GCM AES256    |GCM (AES256)      |Keine         |
| 2 |AES256        |SHA1              |Keine         |
| 3 |3DES          |SHA1              |Keine         |
| 4 |AES256        |SHA256            |Keine         |
| 5 |AES128        |SHA1              |Keine         |
| 6 |3DES          |SHA256            |Keine         |

#### <a name="azure-gateway-as-responder"></a>Azure-Gateway als Antwortender

|-  |**Verschlüsselung**|**Authentifizierung**|**PFS-Gruppe**|
|---| ---          | ---              |---          |
| 1 |GCM AES256    |GCM (AES256)      |Keine         |
| 2 |AES256        |SHA1              |Keine         |
| 3 |3DES          |SHA1              |Keine         |
| 4 |AES256        |SHA256            |Keine         |
| 5 |AES128        |SHA1              |Keine         |
| 6 |3DES          |SHA256            |Keine         |
| 7 |DES           |SHA1              |Keine         |
| 8 |AES256        |SHA1              |1            |
| 9 |AES256        |SHA1              |2            |
| 10|AES256        |SHA1              |14           |
| 11|AES128        |SHA1              |1            |
| 12|AES128        |SHA1              |2            |
| 13|AES128        |SHA1              |14           |
| 14|3DES          |SHA1              |1            |
| 15|3DES          |SHA1              |2            |
| 16|3DES          |SHA256            |2            |
| 17|AES256        |SHA256            |1            |
| 18|AES256        |SHA256            |2            |
| 19|AES256        |SHA256            |14           |
| 20|AES256        |SHA1              |24           |
| 21|AES256        |SHA256            |24           |
| 22|AES128        |SHA256            |Keine         |
| 23|AES128        |SHA256            |1            |
| 24|AES128        |SHA256            |2            |
| 25|AES128        |SHA256            |14           |
| 26|3DES          |SHA1              |14           |

* Sie können IPsec-ESP-NULL-Verschlüsselung mit routenbasierten VPN-Gateways und Hochleistungs-VPN-Gateways angeben. Verschlüsselung auf Basis von NULL bietet keinen Schutz der Daten während der Übertragung und sollte nur verwendet werden, wenn maximaler Durchsatz und minimale Latenz erforderlich sind. Clients können diese in Szenarien mit VNet-zu-VNet-Kommunikation oder bei Anwendung der Verschlüsselung an anderer Stelle in der Lösung verwenden.
* Verwenden Sie für standortübergreifende Konnektivität über das Internet die Standardeinstellungen für Azure-VPN-Gateways mit Verschlüsselung und Hashalgorithmen, die in der Tabelle oben aufgelistet werden, um die Sicherheit Ihrer kritischen Kommunikation zu gewährleisten.

## <a name="known-device-compatibility-issues"></a><a name="known"></a>Bekannte Probleme mit der Gerätekompatibilität

> [!IMPORTANT]
> Hier finden Sie bekannte Kompatibilitätsprobleme zwischen Drittanbieter-VPN-Geräten und Azure-VPN-Gateways. Das Azure-Team arbeitet zusammen mit den Anbietern aktiv an der Lösung der hier angegebenen Probleme. Nachdem die Probleme behoben wurden, wird diese Seite mit den neuesten Informationen aktualisiert. Es ist also ratsam, diese Seite regelmäßig aufzurufen.
>
>

### <a name="feb-16-2017"></a>16. Februar 2017

**Palo Alto Networks-Geräte mit älteren Versionen als 7.1.4** für routenbasiertes Azure-VPN: Führen Sie die folgenden Schritte aus, wenn Sie VPN-Geräte von Palo Alto Networks mit einer älteren PAN-OS-Version als 7.1.4 verwenden und Konnektivitätsprobleme mit routenbasierten Azure-VPN-Gateways auftreten:

1. Überprüfen Sie die Firmwareversion Ihres Palo Alto Networks-Geräts. Führen Sie ein Upgrade auf Version 7.1.4 durch, falls Ihre PAN-OS-Version älter als 7.1.4 ist.
2. Ändern Sie auf dem Palo Alto Networks-Gerät die Phase 2 SA-Lebensdauer (bzw. Quick Mode SA) in 28.800 Sekunden (8 Stunden), wenn Sie die Verbindung mit dem Azure-VPN-Gateway herstellen.
3. Erstellen Sie über das Azure-Portal eine Supportanfrage, falls weiterhin Probleme mit der Konnektivität auftreten.
