---
title: Azure VMware Solution by CloudSimple – Erweitern eines lokalen Layer-2-Netzwerks in die private Cloud
description: Erfahren Sie, wie Sie ein Layer-2-VPN zwischen NSX-T in einer privaten CloudSimple-Cloud und einem lokalen, eigenständigen NSX Edge-Client einrichten.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f524bf6af66d44bc13b7c0957de7977968cbef28
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427250"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>Migrieren von Workloads unter Verwendung von Layer-2-Stretchingnetzwerken

In dieser Anleitung erfahren Sie, wie Sie mit dem Layer-2-VPN (L2VPN) ein Layer-2-Netzwerk von Ihrer lokalen Umgebung auf Ihre private CloudSimple-Cloud ausdehnen. Diese Lösung ermöglicht die Migration von Workloads, die in Ihrer lokalen VMware-Umgebung ausgeführt werden, zur privaten Cloud in Azure. Dabei kann derselbe Subnetzadressraum verwendet werden, ohne dass den Workloads neue IP-Adressen zugewiesen werden müssen.

Ein auf L2VPN basierendes Erweitern von Layer-2-Netzwerken kann mit und ohne NSX-basierte Netzwerke in Ihrer lokalen VMware-Umgebung verwendet werden. Wenn Sie lokal nicht über NSX-basierte Netzwerke für Workloads verfügen, können Sie ein eigenständiges NSX Edge-Dienstgateway verwenden.

> [!NOTE]
> Diese Anleitung behandelt ein Szenario, in dem die lokalen Rechenzentren und die in der privaten Cloud über ein Site-to-Site-VPN verbunden sind.

## <a name="deployment-scenario"></a>Bereitstellungsszenario

Um das lokale Netzwerk mithilfe von L2VPN zu erweitern, müssen Sie einen L2VPN-Server (NSX-T-Zielrouter Ebene 0) und einen L2VPN-Client (eigenständiger Client am Ausgangspunkt) konfigurieren.  

In diesem Bereitstellungsszenario ist Ihre private Cloud mit Ihrer lokalen Umgebung über einen Site-to-Site-VPN-Tunnel verbunden, der die Kommunikation der lokalen Verwaltung und der vMotion-Subnetze mit der Verwaltung der privaten Cloud und den vMotion-Subnetzen ermöglicht. Diese Anordnung ist für Cross vCenter vMotion (xVC-vMotion) erforderlich. Ein NSX-T-Router der Ebene 0 wird als L2VPN-Server in der privaten Cloud bereitgestellt.

NSX Edge wird eigenständig in Ihrer lokalen Umgebung als L2VPN-Client bereitgestellt und anschließend mit dem L2VPN-Server gekoppelt. An jedem Ende wird ein GRE-Tunnelendpunkt erstellt und so konfiguriert, dass das lokale Layer-2-Netzwerk auf Ihre private Cloud ausgeweitet wird. Die folgende Abbildung veranschaulicht diese Konfiguration.

![Bereitstellungsszenario](media/l2vpn-deployment-scenario.png)

Weitere Informationen zur Migration mit L2-VPN finden Sie in der VMware-Dokumentation unter [Virtual Private Networks](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) (Virtuelle private Netzwerke).

## <a name="prerequisites-for-deploying-the-solution"></a>Voraussetzungen für die Bereitstellung der Lösung

Vergewissern Sie sich, dass Folgendes gewährleistet ist, bevor Sie die Lösung bereitstellen und konfigurieren:

* Die lokale vSphere-Version ist 6.7U1+ oder 6.5P03+.
* Die lokale vSphere-Lizenz befindet sich auf Enterprise Plus-Ebene (für den verteilten vSphere-Switch).
* Identifizieren Sie das Layer-2-Netzwerk der Workload, das auf Ihre private Cloud ausgedehnt werden soll.
* Identifizieren Sie ein Layer-2-Netzwerk in Ihrer lokalen Umgebung für die Bereitstellung Ihrer L2VPN-Clientappliance.
* [Es wurde bereits eine private Cloud erstellt.](create-private-cloud.md)
* Die Version der eigenständigen NSX-T Edge-Appliance ist mit der NSX-T Manager-Version (NSX-T 2.3.0) kompatibel, die in Ihrer privaten Cloudumgebung verwendet wird.
* Es wurde eine Trunkportgruppe im lokalen vCenter mit aktivierten gefälschten Übertragungen erstellt.
* Es wurde eine öffentliche IP-Adresse für die Verwendung als Uplink-IP-Adresse des eigenständigen NSX-T-Clients reserviert, und für die Übersetzung zwischen den beiden Adressen wurde 1:1-NAT festgelegt.
* Die DNS-Weiterleitung ist auf den lokalen DNS-Servern für die Domäne az.cloudsimple.io mit einem Verweis auf die DNS-Server der privaten Cloud festgelegt.
* Die RTT-Latenz beträgt höchstens 150 ms. Dies ist erforderlich, damit vMotion an den beiden Standorten funktioniert.

## <a name="limitations-and-considerations"></a>Einschränkungen und Aspekte

In der folgenden Tabelle sind die unterstützten vSphere-Versionen und Netzwerkadaptertypen aufgeführt.  

| vSphere-Version | vSwitch-Quelltyp | Treiber der virtuellen NIC | vSwitch-Zieltyp | Unterstützt? |
------------ | ------------- | ------------ | ------------- | ------------- 
| All | DVS | All | DVS | Ja |
| vSphere 6.7UI oder höher, 6.5P03 oder höher | DVS | VMXNET3 | N-VDS | Ja |
| vSphere 6.7UI oder höher, 6.5P03 oder höher | DVS | E1000 | N-VDS | [Nicht von VMware unterstützt](https://kb.vmware.com/s/article/56991) |
| vSphere 6.7UI oder 6.5P03, NSX-V oder Versionen unter NSX-T2.2, 6.5P03 oder höher | All | All | N-VDS | [Nicht von VMware unterstützt](https://kb.vmware.com/s/article/56991) |

Ab Release VMware NSX-T 2.3:

* Der logische Switch aufseiten der privaten Cloud, die über L2VPN auf einen lokalen Standort ausgedehnt wird, kann nicht gleichzeitig weitergeleitet werden. Der erweiterte logische Switch kann nicht mit einem logischen Router verbunden werden.
* L2VPN und routenbasierte IPsec-VPNs können nur mithilfe von API-Aufrufen konfiguriert werden.

Weitere Informationen finden Sie in der VMware-Dokumentation unter [Virtual Private Networks](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) (Virtuelle private Netzwerke).

### <a name="sample-l2-vpn-deployment-addressing"></a>Adressierung der L2VPN-Beispielbereitstellung

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>Lokales Netzwerk, in dem der eigenständige ESG (L2VPN-Client) bereitgestellt wird

| **Element** | **Wert** |
|------------|-----------------|
| Netzwerkname | MGMT_NET_VLAN469 |
| VLAN | 469 |
| CIDR| 10.250.0.0/24 |
| IP-Adresse für eigenständige Edge-Appliance | 10.250.0.111 |
| NAT-IP-Adresse für eigenständige Edge-Appliance | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>Zu erweiterndes lokales Netzwerk

| **Element** | **Wert** |
|------------|-----------------|
| VLAN | 472 |
| CIDR| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>IP-Schema der privaten Cloud für NSX-T-Router der Ebene 0 (L2VPN-Dienst)

| **Element** | **Wert** |
|------------|-----------------|
| Loopbackschnittstelle | 192.168.254.254/32 |
| Tunnelschnittstelle | 5.5.5.1/29 |
| Logischer Switch (erweitert) | Stretch_LS |
| Loopbackschnittstelle (NAT-IP-Adresse) | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>Privates Cloudnetzwerk, das dem erweiterten Netzwerk zugeordnet werden soll

| **Element** | **Wert** |
|------------|-----------------|
| VLAN | 712 |
| CIDR| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>Abrufen der ID des logischen Routers für L2VPN

Die folgenden Schritte zeigen, wie Sie die ID des logischen Routers der logischen Routerinstanz für die Notfallwiederherstellung der Ebene 0 der IPsec- und L2VPN-Dienste abrufen. Die ID des logischen Routers wird später bei der Implementierung des L2VPN benötigt.

1. Melden Sie sich beim NSX-T-Manager `https://*nsx-t-manager-ip-address*` an, und wählen Sie **Networking** > **Routers** > **Provider-LR** > **Overview** (Netzwerk > Router > Anbieter-LR > Übersicht) aus. Wählen Sie für **High Availability Mode** (Hochverfügbarkeitsmodus) die Option **Active-Standby** (Aktiver Standbymodus) aus. Mit dieser Aktion wird ein Popupfenster geöffnet, in dem die Edge-VM angezeigt wird, auf der der Router der Ebene 0 derzeit aktiv ist.

    ![Auswählen von „active-standby“](media/l2vpn-fetch01.png)

2. Wählen Sie **Fabric** > **Nodes** > **Edges** (Fabric > Knoten > Edges) aus. Notieren Sie sich die Verwaltungs-IP-Adresse der aktiven Edge-VM (Edge VM1), die im vorherigen Schritt ermittelt wurde.

    ![Notieren der Verwaltungs-IP-Adresse](media/l2vpn-fetch02.png)

3. Öffnen Sie eine SSH-Sitzung mit der Verwaltungs-IP-Adresse der Edge-VM. Führen Sie den Befehl ```get logical-router``` mit dem Benutzernamen **admin** und dem Kennwort **CloudSimple 123!** aus.

    ![Screenshot: Offene SSH-Sitzung](media/l2vpn-fetch03.png)

4. Wenn der Eintrag „DR-Provider-LR“ nicht angezeigt wird, führen Sie die folgenden Schritte aus.

5. Erstellen Sie zwei logische Switches mit Überlagerung. Ein logischer Switch wird auf den lokalen Standort erweitert, an dem sich die migrierten Workloads befinden. Ein anderer logischer Switch ist ein Platzhalterswitch. Anweisungen hierzu finden Sie in der VMware-Dokumentation unter [Create a Logical Switch](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html) (Erstellen eines logischen Switches).

    ![Erstellen des logischen Switches](media/l2vpn-fetch04.png)

6. Fügen Sie den Platzhalterswitch mit einer verlinkten lokalen IP-Adresse oder einem nicht überlappenden Subnetz aus der lokalen Umgebung oder der privaten Cloud an den Router der Ebene 1 an. Weitere Informationen finden Sie in der VMware-Dokumentation unter [Add a Downlink Port on a Tier-1 Logical Router](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html) (Hinzufügen eines Downlinkports auf einem logischen Router der Ebene 1).

    ![Anfügen des Platzhalterswitches](media/l2vpn-fetch05.png)

7. Führen Sie den Befehl `get logical-router` erneut in der SSH-Sitzung auf der Edge-VM aus. Die UUID des logischen Routers „DR-Provider-LR“ wird angezeigt. Notieren Sie sich die UUID. Diese wird für das Konfigurieren des L2VPN benötigt.

    ![Screenshot: UUID für den logischen Router](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>Abrufen der ID des logischen Switches für L2VPN

1. Melden Sie sich beim NSX-T-Manager an (`https://nsx-t-manager-ip-address`).
2. Wählen Sie **Networking** > **Switching** > **Switches** >  **<\Logical switch\>**  > **Overview** (Netzwerk > Switching > Switches > <\Logischer Switch > Übersicht) aus.
3. Notieren Sie sich die UUID des logischen Switches für die Erweiterung. Diese wird für das Konfigurieren des L2VPN benötigt.

    ![Abrufen der Ausgabe des logischen Routers](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>Routing- und Sicherheitsaspekte für L2VPN

Um ein auf IPsec-Routen basierendes VPN zwischen dem NSX-T-Router der Ebene 0 und dem eigenständigen NSX Edge-Client einzurichten, muss die Loopbackschnittstelle des NSX-T-Routers der Ebene 0 in der Lage sein, lokal über UDP 500/4500 mit der öffentlichen IP-Adresse des eigenständigen NSX-Clients zu kommunizieren.

### <a name="allow-udp-5004500-for-ipsec"></a>Zulassen von UDP 500/4500 für IPsec

1. [Erstellen Sie eine öffentliche IP-Adresse](public-ips.md) für die NSX-T-Loopbackschnittstelle der Ebene 0 im CloudSimple-Portal.

2. [Erstellen Sie eine Firewalltabelle](firewall.md) mit zustandsbehafteten Regeln, die eingehenden UDP 500/4500-Datenverkehr zulassen, und fügen Sie die Firewalltabelle dem NSX-T-Subnetz „HostTransport“ hinzu.

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>Veröffentlichen der IP-Adresse der Loopbackschnittstelle für das zugrunde liegende Netzwerk

1. Erstellen Sie eine Nullroute für das Netzwerk der Loopbackschnittstelle. Melden Sie sich beim NSX-T-Manager an, und wählen Sie **Networking** > **Routing** > **Routers** > **Provider-LR** > **Routing** > **Static Routes** (Netzwerk > Routing > Router > Anbieter-LR > Routing > Übersicht) aus. Klicken Sie auf **Hinzufügen**. Geben Sie unter **Network** (Netzwerk) die IP-Adresse der Loopbackschnittstelle ein. Klicken Sie für **Next Hops** (Nächste Hops) auf **Add** (Hinzufügen), geben Sie für den nächsten Hop „Null“ an, und behalten Sie den Standardwert „1“ für die Administratorentfernung bei.

    ![Hinzufügen von statischem Routing](media/l2vpn-routing-security01.png)

2. Erstellen Sie eine IP-Präfixliste. Melden Sie sich beim NSX-T-Manager an, und wählen Sie **Networking** > **Routing** > **Routers** > **Provider-LR** > **Routing** > **IP Prefix Lists** (Netzwerk > Routing > Router > Anbieter-LR > Routing > IP-Präfixliste) aus. Klicken Sie auf **Hinzufügen**. Geben Sie einen Namen zum Identifizieren der Liste ein. Klicken Sie für **Prefixes** (Präfixe) zweimal auf **Add** (Hinzufügen). Geben Sie in der ersten Zeile „0.0.0.0/0“ für **Network** (Netzwerk) und „Deny“ für **Action** (Aktion) ein. Wählen Sie in der zweiten Zeile **Any** (Jedes) für **Network** (Netzwerk) und **Permit** (Zulassen) als **Action** (Aktion) aus.
3. Fügen Sie die IP-Präfixliste an beide BGP-Nachbarn (TOR) an. Das Anfügen der IP-Präfixliste an den BGP-Nachbar verhindert, dass die Standardroute in BGP für TOR-Switches offengelegt wird. Jede andere Route, die die Nullroute enthält, veröffentlicht jedoch die IP-Adresse der Loopbackschnittstelle für die TOR-Switches.

    ![Erstellen der IP-Präfixliste](media/l2vpn-routing-security02.png)

4. Melden Sie sich beim NSX-T-Manager an, und wählen Sie **Networking** > **Routing** > **Routers** > **Provider-LR** > **Routing** > **BGP** > **Neighbors** (Netzwerk > Routing > Router > Anbieter-LR > Routing > BGP > Nachbarn) aus. Wählen Sie den ersten Nachbarn aus. Klicken Sie auf **Edit** > **Address Families** (Bearbeiten > Adressenfamilien). Bearbeiten Sie für die IPv4-Familie die Spalte **Out Filter** (Ausgangsfilter), und wählen Sie die IP-Präfixliste aus, die Sie erstellt haben. Klicken Sie auf **Speichern**. Wiederholen Sie diesen Schritt für den zweiten Nachbarn.

    ![Anfügen der IP-Präfixliste 1](media/l2vpn-routing-security03.png) ![Anfügen der IP-Präfixliste 2](media/l2vpn-routing-security04.png)

5. Verteilen Sie die statische Nullroute in BGP neu. Um die Route der Loopbackschnittstelle für das zugrunde liegende Netzwerk zu veröffentlichen, müssen Sie die statische Nullroute in BGP neu verteilen. Melden Sie sich beim NSX-T-Manager an, und wählen Sie **Networking** > **Routing** > **Routers** > **Provider-LR** > **Routing** > **Route Redistribution** > **Neighbors** (Netzwerk > Routing > Router > Anbieter-LR > Routing > Routenneuverteilung > Nachbarn) aus. Wählen Sie **Provider-LR-Route_Redistribution** aus, und klicken Sie auf **Edit** (Bearbeiten). Aktivieren Sie das Kontrollkästchen **Static** (Statisch), und klicken Sie auf **Save** (Speichern).

    ![Neuverteilen der statischen Nullroute in BGP](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>Konfigurieren eines routenbasierten VPN auf dem NSX-T-Router der Ebene 0

Verwenden Sie die folgende Vorlage, um alle Details zum Konfigurieren eines routenbasierten VPN auf dem NSX-T-Router der Ebene 0 auszufüllen. Die UUIDs in den einzelnen POST-Aufruf sind bei nachfolgenden POST-Aufrufen erforderlich. Die IP-Adressen für die Loopback-und Tunnelschnittstellen für L2VPN müssen eindeutig sein und dürfen sich nicht mit den lokalen Netzwerken oder denen der privaten Cloud überlappen.

Die IP-Adressen, die für die Loopback-und Tunnelschnittstellen für L2VPN ausgewählt wurden, müssen eindeutig sein und dürfen sich nicht mit den lokalen Netzwerken oder denen der privaten Cloud überlappen. Das Netzwerk der Loopbackschnittstelle muss immer /32 sein.

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section "Steps to fetch Logical-Router ID needed for L2VPN"
Logical-switch ID(Stretch) : UUID of Stretch Logical Switch obtained earlier
IPSec Service ID :
IKE profile ID :
DPD profile ID :
Tunnel Profile ID :
Local-endpoint ID :
Peer end-point ID :
IPSec VPN session ID (route-based) :
L2VPN service ID :
L2VPN session ID :
Logical-Port ID :
Peer Code :
```

Ersetzen Sie für alle folgenden API-Aufrufe die IP-Adresse durch die IP-Adresse Ihres NSX-T-Managers. Sie können alle diese API-Aufrufe über den POSTMAN-Client oder mithilfe von `curl`-Befehlen ausführen.

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>Aktivieren des IPsec-VPN-Diensts auf dem logischen Router

```
POST   https://192.168.110.201/api/v1/vpn/ipsec/services/
{
"resource_type": "IPSecVPNService",
"description": "Manage VPN service",
"display_name": "IPSec VPN service",
"logical_router_id": "Logical-router ID",
"ike_log_level": "INFO",
"enabled": true
}
```

### <a name="create-profiles-ike"></a>Erstellen der Profile: IKE

```
POST https://192.168.110.201/api/v1/vpn/ipsec/ike-profiles
 
{
"resource_type": "IPSecVPNIKEProfile",
"description": "IKEProfile for siteA",
"display_name": "IKEProfile siteA",
"encryption_algorithms": ["AES_128"],
"ike_version": "IKE_V2",
"digest_algorithms": ["SHA2_256"],
"sa_life_time":21600,
"dh_groups": ["GROUP14"]
}
```

### <a name="create-profiles-dpd"></a>Erstellen der Profile: DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>Erstellen der Profile: Tunnel

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/tunnel-profiles
 
{
"resource_type": "IPSecVPNTunnelProfile",
"display_name": "nsx-default-tunnel-profile",
"enable_perfect_forward_secrecy": true,
"encryption_algorithms": ["AES_GCM_128"],
"digest_algorithms": [],
"sa_life_time":3600,
"dh_groups": ["GROUP14"],
"encapsulation_mode": "TUNNEL_MODE",
"transform_protocol": "ESP",
"df_policy": "COPY"
}
```

### <a name="create-a-local-endpoint"></a>Erstellen eines lokalen Endpunkts

``` 
POST https://192.168.110.201/api/v1/vpn/ipsec/local-endpoints
 
{
"resource_type": "IPSecVPNLocalEndpoint",
"description": "Local endpoint",
"display_name": "Local endpoint",
"local_id": "<Public IP of Loopback interface>",
"ipsec_vpn_service_id": {
"target_id": "IPSec VPN service ID"},
"local_address": "<IP of Loopback interface>",
"trust_ca_ids": [],
"trust_crl_ids": []
}
```

### <a name="create-a-peer-endpoint"></a>Erstellen eines Peerendpunkts

```
POST https://192.168.110.201/api/v1/vpn/ipsec/peer-endpoints

{
"resource_type": "IPSecVPNPeerEndpoint",
"description": "Peer endpoint for site B",
"display_name": "Peer endpoint for site B",
"connection_initiation_mode": "INITIATOR",
"authentication_mode": "PSK",
"ipsec_tunnel_profile_id": "IPSec Tunnel profile ID",
"dpd_profile_id": "DPD profile ID",
"psk":"nsx",
"ike_profile_id": "IKE profile ID",
"peer_address": "<Public IP of Standalone client",
"peer_id": "<Public IP of Standalone client>"
}
```

### <a name="create-a-route-based-vpn-session"></a>Erstellen einer routenbasierten VPN-Sitzung

```
POST :  https://192.168.110.201/api/v1/vpn/ipsec/sessions
 
{
"resource_type": "RouteBasedIPSecVPNSession",
"peer_endpoint_id": "Peer Endpoint ID",
"ipsec_vpn_service_id": "IPSec VPN service ID",
"local_endpoint_id": "Local Endpoint ID",
"enabled": true,
"tunnel_ports": [
{
"ip_subnets": [
{
"ip_addresses": [
 "5.5.5.1"
],
"prefix_length": 24
}
  ]
}
]
}
```

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>Konfigurieren von L2VPN auf dem NSX-T-Router der Ebene 0

Geben Sie die folgenden Informationen nach jedem POST-Aufruf ein. Die IDs sind in nachfolgenden POST-Aufrufen erforderlich.

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>Erstellen des L2VPN-Diensts

Die Ausgabe des folgenden GET-Befehls ist leer, da die Konfiguration noch nicht abgeschlossen wurde.

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

Für den folgenden POST-Befehl ist die ID des logischen Routers die UUID des logischen Routers der Ebene 0 für die Notfallwiederherstellung, die zuvor abgerufen wurde.

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>Erstellen der L2VPN-Sitzung

Für den folgenden POST-Befehl ist die ID des L2VPN-Diensts die ID, die Sie soeben abgerufen haben, und die ID der IPsec-VPN-Sitzung ist die ID, die Sie im vorherigen Abschnitt abgerufen haben.

```    
POST: https://192.168.110.201/api/v1/vpn/l2vpn/sessions

{
"l2vpn_service_id" : "L2VPN service ID",
"transport_tunnels" : [
    {
    "target_id" : "IPSec VPN session ID"
    }]
}
```

Diese Aufrufe erstellen einen GRE-Tunnelendpunkt. Führen Sie den folgenden Befehl aus, um den Status zu überprüfen.

```
edge-2> get tunnel-port
Tunnel      : 44648dae-8566-5bc9-a065-b1c4e5c3e03f
IFUID       : 328
LOCAL       : 169.254.64.1
REMOTE      : 169.254.64.2
ENCAP       : GRE

Tunnel      : cf950ca1-5cf8-5438-9b1a-d2c8c8e7229b
IFUID       : 318
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.152
ENCAP       : GENEVE

Tunnel      : 63639321-87c5-529e-8a61-92c1939799b2
IFUID       : 304
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.156
ENCAP       : GENEVE
```

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>Erstellen des logischen Ports mit der angegebenen Tunnel-ID

```
    POST https://192.168.110.201/api/v1/logical-ports/

{
"resource_type": "LogicalPort",
"display_name": "Extend logicalSwitch, port for service",
"logical_switch_id": "Logical switch ID",
"admin_state" : "UP",
"attachment": {
"attachment_type":"L2VPN_SESSION",
"id":"L2VPN session ID",
"context" : {
"resource_type" : "L2VpnAttachmentContext",
    "tunnel_id" : 10
}
    }
        }
```

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>Abrufen des Peercodes für L2VPN auf NSX-T-Seite

Rufen Sie den Peercode des NSX-T-Endpunkts ab. Der Peercode wird für das Konfigurieren des Remoteendpunkts benötigt. Die <Sitzungs-ID> für das L2VPN kann im vorherigen Abschnitt abgerufen werden. Weitere Informationen finden Sie im [Handbuch zur NSX-T 2.3-API](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html).

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>Bereitstellen des eigenständigen NSX-T-Clients (lokal)

Stellen Sie vor der Bereitstellung sicher, dass Ihre lokalen Firewallregeln eingehenden und ausgehenden UDP 500/4500-Datenverkehr an der öffentlichen CloudSimple-IP-Adresse zulassen, die zuvor für die Loopbackschnittstelle des NSX-T-Routers der Ebene 0 reserviert wurde. 

1. [Laden Sie die OVF-Dateien des eigenständigen NSX Edge-Clients herunter](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230), und extrahieren Sie diese aus dem heruntergeladenen Paket in einen Ordner.

    ![Herunterladen des eigenständigen NSX Edge-Clients](media/l2vpn-deploy-client01.png)

2. Wechseln Sie zum Ordner mit allen extrahierten Dateien. Wählen Sie alle VMDK-Dateien aus („NSX-l2t-client-large.mf“ und „NSX-l2t-client-large.ovf“ für große Appliances bzw. „NSX-l2t-client-Xlarge.mf“ und „NSX-l2t-client-Xlarge.ovf“ für sehr große Appliances). Klicken Sie auf **Weiter**.

    ![Vorlage auswählen](media/l2vpn-deploy-client02.png) ![Screenshot: Ausgewählte VMDKS-Dateien](media/l2vpn-deploy-client03.png)

3. Geben Sie einen Namen für den eigenständigen NSX-T-Client ein, und klicken Sie auf **Next** (Weiter).

    ![Eingeben des Vorlagennamens](media/l2vpn-deploy-client04.png)

4. Klicken Sie bei Bedarf auf **Next** (Weiter), um zu den Datenspeichereinstellungen zu gelangen. Wählen Sie den entsprechenden Datenspeicher für den eigenständigen NSX-T-Client aus, und klicken Sie auf **Next** (Weiter).

    ![Auswählen des Datenspeichers](media/l2vpn-deploy-client06.png)

5. Wählen Sie die passenden Portgruppen für Trunk- (Trunk PG), öffentliche (Uplink PG) und Hochverfügbarkeitsschnittstelle (Uplink PG) für den eigenständigen NSX-T-Client aus. Klicken Sie auf **Weiter**.

    ![Auswählen von Portgruppen](media/l2vpn-deploy-client07.png)

6. Geben Sie die folgenden Details auf dem Bildschirm **Customize template** (Vorlage anpassen) ein, und klicken Sie auf **Next** (Weiter):

    Erweitern von L2T:

    * **Peer Address** (Peeradresse): Geben Sie die IP-Adresse ein, die im Azure-CloudSimple-Portal für die Loopbackschnittstelle von NSX-T Ebene 0 reserviert wurde.
    * **Peer Code** (Peercode): Fügen Sie den Peercode aus dem letzten Schritt der L2VPN-Serverbereitstellung ein.
    * **Sub Interfaces VLAN (Tunnel ID)** (Subschnittstellen-VLAN (Tunnel-ID)): Geben Sie die ID des zu erweiternden VLAN ein. Geben Sie in Klammern () die Tunnel-ID ein, die zuvor konfiguriert wurde.

    Erweitern der Uplinkschnittstelle:

    * **DNS IP Address** (DNS-IP-Adresse): Geben Sie die lokale DNS-IP-Adresse ein.
    * **Default Gateway** (Standardgateway):  Geben Sie das Standardgateway des VLAN ein, das als Standardgateway für diesen Client fungieren soll.
    * **IP Address** (IP-Adresse): Geben Sie die Uplink-IP-Adresse des eigenständigen Clients ein.
    * **Prefix Length** (Präfixlänge): Geben Sie die Präfixlänge für das Uplink-VLAN/-Subnetz ein.
    * **CLI admin/enable/root User Password** (CLI-Kennwort für admin/enable/root): Legen Sie das Kennwort für die Konten „admin“, „enable“ und „root“ fest.

      ![Anpassen der Vorlage](media/l2vpn-deploy-client08.png)
      ![Anpassen der Vorlage, Fortsetzung](media/l2vpn-deploy-client09.png)

7. Überprüfen Sie die Einstellungen, und klicken Sie auf **Fertig stellen**.

    ![Abschließen der Konfiguration](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>Konfigurieren eines lokalen Senkenports

Wenn an einem der VPN-Standorte NSX nicht bereitgestellt wird, können Sie ein L2VPN konfigurieren, indem Sie an diesem Standort einen eigenständigen NSX Edge bereitstellen. Ein eigenständiger NSX Edge wird mithilfe einer OVF-Datei auf einem Host bereitgestellt, der nicht von NSX verwaltet wird. Dadurch wird eine NSX Edge Services Gateway-Appliance bereitgestellt, die als L2VPN-Client fungiert.

Wenn eine Trunk-vNIC für den eigenständigen Edge mit einem verteilten vSphere-Switch verbunden wird, ist für die L2VPN-Funktion entweder der Modus „Promisk“ oder ein Senkenport erforderlich. Die Verwendung des promisken Modus kann doppelte Pings und doppelte Antworten verursachen. Verwenden Sie aus diesem Grund den Modus „Senkenport“ in der L2VPN-Konfiguration des eigenständigen NSX Edge. Weitere Informationen finden Sie in der VMware-Dokumentation unter [Configure a sink port](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html) (Konfigurieren eines Senkenports).

## <a name="ipsec-vpn-and-l2vpn-verification"></a>Überprüfung von IPsec-VPN und L2VPN

Verwenden Sie die folgenden Befehle, um IPsec- und L2VPN-Sitzungen auf dem eigenständigen NSX-T Edge zu überprüfen.

```
nsx-l2t-edge> show service ipsec
-----------------------------------------------------------------------
vShield Edge IPSec Service Status:
IPSec Server is running.
AESNI is enabled.
Total Sites: 1, 1 UP, 0 Down
Total Tunnels: 1, 1 UP, 0 Down
----------------------------------
Site:  10.250.0.111_0.0.0.0/0-104.40.21.81_0.0.0.0/0
Channel: PeerIp: 104.40.21.81    LocalIP: 10.250.0.111  Version: IKEv2  Status: UP
Tunnel: PeerSubnet: 0.0.0.0/0    LocalSubnet: 0.0.0.0/0   Status: UP
----------------------------------
```

```
nsx-l2t-edge> show service l2vpn
L2 VPN is running
----------------------------------------
L2 VPN type: Client/Spoke

SITENAME                       IPSECSTATUS          VTI                  GRE
1ecb00fb-a538-4740-b788-c9049e8cb6c6 UP                   vti-100              l2t-1
```

Verwenden Sie die folgenden Befehle, um IPsec- und L2VPN-Sitzungen auf dem NSX-T-Router der Ebene 0 zu überprüfen.

```
edge-2> get ipsecvpn session
Total Number of Sessions: 1

IKE Session ID : 3
UUID           : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Type           : Route

Local IP       : 192.168.254.254      Peer IP        : 192.227.85.167
Local ID       : 104.40.21.81         Peer ID        : 192.227.85.167
Session Status : Up

Policy Rules
    VTI UUID       : 4bf96e3b-e50b-49cc-a16e-43a6390e3d53
    ToRule ID      : 560874406            FromRule ID    : 2708358054
    Local Subnet   : 0.0.0.0/0            Peer Subnet    : 0.0.0.0/0
    Tunnel Status  : Up
```

```
edge-2> get l2vpn session
Session       : f7147137-5dd0-47fe-9e53-fdc2b687b160
Tunnel        : b026095b-98c8-5932-96ff-dda922ffe316
IPSEC Session : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Status        : UP
```

Verwenden Sie die folgenden Befehle, um den Senkenport auf dem ESXi-Host zu überprüfen, auf dem sich die VM des eigenständigen NSX-T-Clients in der lokalen Umgebung befindet.

```
 [root@esxi02:~] esxcfg-vswitch -l |grep NSX
  53                  1           NSXT-client-large.eth2
  225                1           NSXT-client-large.eth1
  52                  1           NSXT-client-large.eth0
```

```
[root@esxi02:~] net-dvs -l | grep "port\ [0-9]\|SINK\|com.vmware.common.alias"
                com.vmware.common.alias = csmlab2DS ,   propType = CONFIG
        port 24:
        port 25:
        port 26:
        port 27:
        port 13:
        port 19:
        port 169:
        port 54:
        port 110:
        port 6:
        port 107:
        port 4:
        port 199:
        port 168:
        port 201:
        port 0:
        port 49:
        port 53:
        port 225:
                com.vmware.etherswitch.port.extraEthFRP =   SINK
        port 52:
```
