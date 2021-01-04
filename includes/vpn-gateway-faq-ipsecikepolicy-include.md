---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/05/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6684c4269f14b91ded651dadff3f0a2e0878a4f2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025619"
---
### <a name="is-custom-ipsecike-policy-supported-on-all-azure-vpn-gateway-skus"></a>Wird die benutzerdefinierte IPsec-/IKE-Richtlinie von allen Azure VPN Gateway-SKUs unterstützt?
Eine benutzerdefinierte IPsec-/IKE-Richtlinie wird auf allen Azure-SKUs außer der SKU „Basic“ unterstützt.

### <a name="how-many-policies-can-i-specify-on-a-connection"></a>Wie viele Richtlinien kann ich für eine Verbindung angeben?
Pro Verbindung kann jeweils nur ***eine** _ Richtlinienkombination angegeben werden.

### <a name="can-i-specify-a-partial-policy-on-a-connection-for-example-only-ike-algorithms-but-not-ipsec"></a>Kann ich eine partielle Richtlinie für eine Verbindung angeben? (Beispielsweise nur IKE-Algorithmen, aber kein IPsec.)
Nein. Sie müssen alle Algorithmen und Parameter für IKE (Hauptmodus) und IPsec (Schnellmodus) angeben. Partielle Richtlinien sind nicht zulässig.

### <a name="what-are-the-algorithms-and-key-strengths-supported-in-the-custom-policy"></a>Welche Algorithmen und Schlüsselstärken werden in der benutzerdefinierten Richtlinie unterstützt?
Die folgende Tabelle gibt Aufschluss über die unterstützten Kryptografiealgorithmen und Schlüsselstärken, die von den Kunden konfiguriert werden können. Für jedes Feld muss eine Option ausgewählt werden.

| _ *IPsec/IKEv2**  | **Optionen**                                                                   |
| ---              | ---                                                                           |
| IKEv2-Verschlüsselung | AES256, AES192, AES128, DES3, DES                                             |
| IKEv2-Integrität  | SHA384, SHA256, SHA1, MD5                                                     |
| DH-Gruppe         | DHGroup24, ECP384, ECP256, DHGroup14 (DHGroup2048), DHGroup2, DHGroup1, keine  |
| IPsec-Verschlüsselung | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, keine      |
| IPsec-Integrität  | GCMAES256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                            |
| PFS-Gruppe        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, keine                              |
| QM-SA-Gültigkeitsdauer   | Sekunden (ganze Zahl; **min. 300**/Standard: 27.000 Sekunden)<br>KB (ganze Zahl; **min. 1024**/Standard: 102.400.000 KB)           |
| Datenverkehrsselektor | UsePolicyBasedTrafficSelectors ($True/$False; Standardwert: $False)                 |
|                  |                                                                               |

> [!IMPORTANT]
> 1. DHGroup2048 und PFS2048 sind identisch mit der Diffie-Hellman-Gruppe **14** in IKE- und IPsec-PFS. Die vollständigen Zuordnungen finden Sie unter [Welche Diffie-Hellman-Gruppen werden unterstützt?](#DH).
> 2. Bei GCMAES-Algorithmen müssen Sie für IPsec-Verschlüsselung und Integrität den gleichen GCMAES-Algorithmus und die gleiche Schlüssellänge angeben.
> 3. Die SA-Gültigkeitsdauer von IKEv2 (Hauptmodus) ist für die Azure-VPN-Gateways auf 28.800 Sekunden festgelegt.
> 4. QM-SA-Gültigkeitsdauern sind optionale Parameter. Ohne Angabe werden die folgenden Standardwerte verwendet: 27.000 Sekunden (7,5 Std.) und 102.400.000 KB (102 GB).
> 5. „UsePolicyBasedTrafficSelector“ ist ein Optionsparameter für die Verbindung. Informationen zu „UsePolicyBasedTrafficSelectors“ finden Sie unter der nächsten häufig gestellten Frage.

### <a name="does-everything-need-to-match-between-the-azure-vpn-gateway-policy-and-my-on-premises-vpn-device-configurations"></a>Müssen alle Angaben zwischen der Azure-VPN-Gatewayrichtlinie und meinen lokalen VPN-Gerätekonfigurationen übereinstimmen?
Ihre lokale VPN-Gerätekonfiguration muss folgenden Algorithmus- und Parameterangaben für die Azure-IPsec-/IKE-Richtlinie entsprechen oder selbige enthalten:

* IKE-Verschlüsselungsalgorithmus
* IKE-Integritätsalgorithmus
* DH-Gruppe
* IPsec-Verschlüsselungsalgorithmus
* IPsec-Integritätsalgorithmus
* PFS-Gruppe
* Datenverkehrsselektor (*)

Bei der SA-Gültigkeitsdauer handelt es sich lediglich um eine lokale Angabe. Diese muss nicht übereinstimmen.

Wenn Sie **UsePolicyBasedTrafficSelectors** aktivieren, müssen für Ihr VPN-Gerät die entsprechenden Datenverkehrsselektoren mit allen Präfixkombinationen zwischen Ihrem lokalen Netzwerk (lokalen Netzwerkgateway) und den Präfixen des virtuellen Azure-Netzwerks definiert sein (anstelle von Any-to-Any). Wenn die Präfixe Ihres lokalen Netzwerks also beispielsweise 10.1.0.0/16 und 10.2.0.0/16 lauten und für Ihr virtuelles Netzwerk die Präfixe 192.168.0.0/16 und 172.16.0.0/16 verwendet werden, müssen folgende Datenverkehrsselektoren angegeben werden:
* 10.1.0.0/16 <====> 192.168.0.0/16
* 10.1.0.0/16 <====> 172.16.0.0/16
* 10.2.0.0/16 <====> 192.168.0.0/16
* 10.2.0.0/16 <====> 172.16.0.0/16

Weitere Informationen finden Sie unter [Herstellen einer Verbindung zwischen Azure-VPN-Gateways und mehreren lokalen richtlinienbasierten VPN-Geräten mit PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).

### <a name="which-diffie-hellman-groups-are-supported"></a><a name ="DH"></a>Welche Diffie-Hellman-Gruppen werden unterstützt?
Die folgende Tabelle enthält die unterstützten Diffie-Hellman-Gruppen für IKE (DHGroup) und IPsec (PFSGroup):

| **Diffie-Hellman-Gruppe**  | **DHGroup**              | **PFSGroup** | **Schlüssellänge** |
| ---                       | ---                      | ---          | ---            |
| 1                         | DHGroup1                 | PFS1         | 768-Bit-MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024-Bit-MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048-Bit-MODP  |
| 19                        | ECP256                   | ECP256       | 256-Bit-ECP    |
| 20                        | ECP384                   | ECP384       | 384-Bit-ECP    |
| 24                        | DHGroup24                | PFS24        | 2048-Bit-MODP  |
|                           |                          |              |                |

Weitere Informationen finden Sie unter [RFC3526](https://tools.ietf.org/html/rfc3526) und [RFC5114](https://tools.ietf.org/html/rfc5114).

### <a name="does-the-custom-policy-replace-the-default-ipsecike-policy-sets-for-azure-vpn-gateways"></a>Ersetzt die benutzerdefinierte Richtlinie die IPsec-/IKE-Standardrichtliniensätze für Azure-VPN-Gateways?
Ja. Wenn eine benutzerdefinierte Richtlinie für eine Verbindung angegeben wird, verwendet das Azure-VPN-Gateway nur diese Richtlinie für die Verbindung (sowohl als IKE-Initiator als auch als IKE-Antwortender).

### <a name="if-i-remove-a-custom-ipsecike-policy-does-the-connection-become-unprotected"></a>Ist die Verbindung nach dem Entfernen einer benutzerdefinierten IPsec-/IKE-Richtlinie ungeschützt?
Nein. Die Verbindung ist weiterhin durch IPsec/IKE geschützt. Wenn Sie die benutzerdefinierte Richtlinie für eine Verbindung entfernen, verwendet das Azure-VPN-Gateway wieder die [Standardliste mit IPsec-/IKE-Vorschlägen](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) und startet erneut den IKE-Handshake mit Ihrem lokalen VPN-Gerät.

### <a name="would-adding-or-updating-an-ipsecike-policy-disrupt-my-vpn-connection"></a>Führt das Hinzufügen oder Aktualisieren einer IPsec-/IKE-Richtlinie zu einer Unterbrechung meiner VPN-Verbindung?
Ja. Ein solcher Vorgang kann dazu führen, dass die Verbindung kurzzeitig (für wenige Sekunden) unterbrochen wird, da das Azure-VPN-Gateway die vorhandene Verbindung trennt und den IKE-Handshake neu startet, um den IPsec-Tunnel mit den neuen kryptografischen Algorithmen und Parametern einzurichten. Stellen Sie sicher, dass Ihr lokales VPN-Gerät mit den entsprechenden Algorithmen und Schlüssellängen konfiguriert ist, damit die Unterbrechung möglichst kurz ausfällt.

### <a name="can-i-use-different-policies-on-different-connections"></a>Kann ich unterschiedliche Richtlinien für unterschiedliche Verbindungen verwenden?
Ja. Benutzerdefinierte Richtlinien sind verbindungsspezifisch. Sie können verschiedene IPsec-/IKE-Richtlinien für verschiedene Verbindungen erstellen und anwenden. Außerdem können Sie benutzerdefinierte Richtlinien auf eine Teilmenge der Verbindungen anwenden. Für die restlichen Verbindungen werden die IPsec-/IKE-Standardrichtliniensätze von Azure verwendet.

### <a name="can-i-use-the-custom-policy-on-vnet-to-vnet-connection-as-well"></a>Kann ich die benutzerdefinierte Richtlinie auch für VNET-zu-VNET-Verbindungen verwenden?
Ja. Sie können benutzerdefinierte Richtlinien sowohl für standortübergreifende IPsec-Verbindungen als auch für VNET-zu-VNET-Verbindungen verwenden.

### <a name="do-i-need-to-specify-the-same-policy-on-both-vnet-to-vnet-connection-resources"></a>Muss ich die gleiche Richtlinie für beide VNET-zu-VNET-Verbindungsressourcen angeben?
Ja. Ein VNET-zu-VNET-Tunnel besteht aus zwei Verbindungsressourcen in Azure (je eine pro Richtung). Damit die VNET-zu-VNET-Verbindung hergestellt werden kann, müssen beide Verbindungsressourcen über die gleiche Richtlinie verfügen.

### <a name="what-is-the-default-dpd-timeout-value-can-i-specify-a-different-dpd-timeout"></a>Wie lautet der Standardwert für das DPD-Timeout? Kann ich ein anderes DPD-Timeout angeben?
Das DPD-Timeout ist standardmäßig auf 45 Sekunden festgelegt. Sie können für jede IPsec- oder VNet-to-VNet-Verbindung einen anderen DPD-Timeoutwert zwischen neun und 3.600 Sekunden angeben.

### <a name="does-custom-ipsecike-policy-work-on-expressroute-connection"></a>Können benutzerdefinierte IPsec-/IKE-Richtlinien für ExpressRoute-Verbindungen verwendet werden?
Nein. IPsec-/IKE-Richtlinien können nur für S2S-VPN- und VNET-zu-VNET-Verbindungen über Azure-VPN-Gateways verwendet werden.

### <a name="how-do-i-create-connections-with-ikev1-or-ikev2-protocol-type"></a>Wie kann ich Verbindungen mit dem IKEv1- oder IKEv2-Protokolltyp erstellen?
IKEv1-Verbindungen können für alle SKUs vom Typ „RouteBased VPN“, mit Ausnahme der SKU „Basic“, „Standard“ und anderen [Legacy-SKUs](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#gwsku) erstellt werden. Sie können beim Erstellen von Verbindungen IKEv1 oder IKEv2 als Verbindungsprotokolltyp angeben. Wenn Sie keinen Verbindungsprotokolltyp angeben, wird IKEv2 als Standardoption verwendet, sofern dies zutreffend ist. Weitere Informationen finden Sie in der Dokumentation zum [PowerShell-Cmdlet](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection). Informationen zu den SKU-Typen und zur IKEv1/IKEv2-Unterstützung finden Sie unter [Herstellen einer Verbindung zwischen Azure-VPN-Gateways und mehreren lokalen richtlinienbasierten VPN-Geräten mit PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).

### <a name="is-transit-between-between-ikev1-and-ikev2-connections-allowed"></a>Ist die Übertragung zwischen IKEv1- und IKEv2-Verbindungen zulässig?
Ja. Die Übertragung zwischen IKEv1- und IKEv2-Verbindungen wird unterstützt.

### <a name="can-i-have-ikev1-site-to-site-connections-on-basic-skus-of-routebased-vpn-type"></a>Kann ich IKEv1-Site-to-Site-Verbindungen für Basic-SKUs mit dem Typ „RouteBased VPN“ verwenden?
Nein. Die Basic-SKU unterstützt dies nicht.

### <a name="can-i-change-the-connection-protocol-type-after-the-connection-is-created-ikev1-to-ikev2-and-vice-versa"></a>Kann ich den Verbindungsprotokolltyp ändern, nachdem die Verbindung erstellt wurde (IKEv1 in IKEv2 und umgekehrt)?
Nein. Nachdem die Verbindung erstellt wurde, können die IKEv1/IKEv2-Protokolle nicht mehr geändert werden. Sie müssen einen Löschvorgang durchführen und eine neue Verbindung mit dem gewünschten Protokolltyp erstellen.

### <a name="where-can-i-find-more-configuration-information-for-ipsec"></a>Wo finde ich weitere Informationen zur Konfiguration von IPsec?
Lesen Sie [Konfigurieren der IPsec/IKE-Richtlinie für S2S- oder VNet-zu-VNet-Verbindungen](../articles/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell.md)