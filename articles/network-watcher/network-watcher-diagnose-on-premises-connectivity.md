---
title: Diagnostizieren der lokalen Konnektivität über VPN Gateway
titleSuffix: Azure Network Watcher
description: Dieser Artikel beschreibt die Durchführung einer Diagnose der lokalen Konnektivität über VPN-Gateways mithilfe der Azure Network Watcher-Ressource zur Problembehandlung.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 9014f24918013872ce102d094f62fd5703594ddc
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961854"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnostizieren der lokalen Konnektivität über VPN-Gateways

Azure VPN Gateway ermöglicht Ihnen die Erstellung einer Hybridlösung für Ihre Anforderungen an eine sichere Verbindung zwischen dem lokalen Netzwerk und Ihrem virtuellen Azure-Netzwerk. Da Ihre Anforderungen einzigartig sind, gilt dies auch für die Wahl des lokalen VPN-Geräts. Azure unterstützt derzeit [verschiedene VPN-Geräte](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable), die ständig in Zusammenarbeit mit den Geräteherstellern getestet werden. Überprüfen Sie die gerätespezifischen Konfigurationseinstellungen vor dem Konfigurieren Ihres lokalen VPN-Geräts. Auf ähnliche Weise wird Azure VPN Gateway mit einem Satz von [unterstützten IPsec-Parametern](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) konfiguriert, die für das Herstellen von Verbindungen verwendet werden. Zurzeit besteht keine Möglichkeit, eine bestimmte Kombination von IPsec-Parametern in Azure VPN Gateway anzugeben oder auszuwählen. Für eine erfolgreiche Verbindung zwischen einem Standort und Azure müssen die lokalen VPN-Geräteeinstellungen mit den von Azure VPN Gateway vorgeschriebenen IPsec-Parametern übereinstimmen. Sind die Einstellungen falsch, kann keine Verbindung hergestellt werden. Bisher war auch die Problembehandlung schwierig, und es dauerte in der Regel mehrere Stunden, bis das Problem identifiziert und behoben war.

Durch das Azure Network Watcher-Feature zur Problembehandlung können Sie Probleme mit dem Gateway und den Verbindungen innerhalb von Minuten diagnostizieren und verfügen dann über genügend Informationen, um eine fundierte Entscheidung zum Beheben des Problems treffen zu können.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Szenario

Sie möchten eine Site-to-Site-Verbindung zwischen Azure und dem lokalen Netzwerk konfigurieren und dazu FortiGate als lokales VPN-Gateway verwenden. Für dieses Szenario benötigen Sie folgendes Setup:

1. Virtual Network-Gateway: VPN Gateway in Azure
1. Gateway des lokalen Netzwerks: die Darstellung des [lokalen VPN-Gateways (FortiGate)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) in der Azure-Cloud
1. Site-to-Site-Verbindung (routenbasiert): [Verbindung zwischen VPN Gateway und dem lokalen Router](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#CreateConnection)
1. [Konfigurieren von FortiGate](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Eine Schritt-für-Schritt-Anleitung zum Konfigurieren einer Standort-zu-Standort-Konfiguration finden Sie unter [Erstellen eines VNET mit einer Standort-zu-Standort-Verbindung über das Azure-Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

Ein wichtiger Konfigurationsschritt ist die Konfiguration der IPsec-Kommunikationsparameter. Eine fehlerhafte Konfiguration führt zum Verlust der Verbindung zwischen dem lokalen Netzwerk und Azure. Derzeit sind Azure VPN Gateway-Instanzen für die Unterstützung der folgenden IPsec-Parameter für Phase 1 konfiguriert. Wie Sie in der Tabelle unten sehen können, unterstützt Azure VPN Gateway die Verschlüsselungsalgorithmen AES256, AES128 und 3DES.

### <a name="ike-phase-1-setup"></a>IKE Phase 1-Einrichtung

| **Eigenschaft** | **PolicyBased** | **Routenbasiertes und Standard- oder Hochleistungs-VPN-Gateway** |
| --- | --- | --- |
| IKE-Version |IKEv1 |IKEv2 |
| Diffie-Hellman-Gruppe |Gruppe 2 (1024 Bit) |Gruppe 2 (1024 Bit) |
| Authentifizierungsmethode |Vorab ausgetauschter Schlüssel |Vorab ausgetauschter Schlüssel |
| Verschlüsselungsalgorithmen |AES256 AES128 3DES |AES256 3DES |
| Hashalgorithmus |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Phase 1 Sicherheitszuordnung (SA) Lebensdauer (Zeit) |28.800 Sekunden |28.800 Sekunden |

Als Benutzer müssen Sie FortiGate konfigurieren. Eine Beispielkonfiguration finden Sie auf [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Ohne es zu wissen, haben Sie FortiGate für die Verwendung des Hashalgorithmus SHA-512 konfiguriert. Da dieser Algorithmus für richtlinienbasierte Verbindungen nicht unterstützt wird, funktioniert Ihre VPN-Verbindung nicht.

Diese Probleme sind schwierig zu beheben, und die eigentlichen Ursachen sind häufig nicht offensichtlich. In diesem Fall können Sie ein Supportticket öffnen, um Hilfe bei der Lösung des Problems zu erhalten. Mithilfe der Problembehandlungs-API von Azure Network Watcher können Sie diese Probleme jedoch selbständig identifizieren.

## <a name="troubleshooting-using-azure-network-watcher"></a>Beheben von Problemen mithilfe von Azure Network Watcher

Um eine Diagnose für die Verbindung durchzuführen, stellen Sie eine Verbindung mit Azure PowerShell her und initiieren das Cmdlet `Start-AzNetworkWatcherResourceTroubleshooting`. Einzelheiten zur Verwendung dieses Cmdlets finden Sie unter [Beheben von Problemen bei Virtual Network-Gateways und -Verbindungen – PowerShell](network-watcher-troubleshoot-manage-powershell.md). Dies Ausführung dieses Cmdlets kann einige Minuten dauern.

Wenn die Ausführung des Cmdlets abgeschlossen wurde, können Sie zu dem im Cmdlet angegebenen Speicherort navigieren, um ausführliche Informationen zu dem Problem und den Protokollen zu erhalten. Azure Network Watcher erstellt einen ZIP-Ordner, der die folgenden Protokolldateien enthält:

![1][1]

Öffnen Sie die Datei „IKEErrors.txt“. Sie zeigt den folgenden Fehler. Dieser gibt an, dass ein Problem mit einer lokalen IKE-Einstellung vorhanden ist.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Ausführliche Informationen zu dem Fehler finden Sie in der Datei „Scrubbed-wfpdiag.txt“. In diesem Fall wird `ERROR_IPSEC_IKE_POLICY_MATCH` angezeigt – ein Fehler, aufgrund dessen die Verbindung nicht ordnungsgemäß funktioniert.

Ein anderer häufiger Konfigurationsfehler ist die Angabe falscher gemeinsam verwendeter Schlüssel. Wenn im vorherigen Beispiel unterschiedliche gemeinsam verwendete Schlüssel angegeben wurden, enthält „IKEErrors.txt“ den folgenden Fehler: `Error: Authentication failed. Check shared key`.

Das Azure Network Watcher-Feature zur Problembehandlung ermöglicht es Ihnen, Probleme mit Ihrer VPN Gateway-Instanz und der Verbindung mit einem einfachen PowerShell-Cmdlet zu diagnostizieren und zu beheben. Derzeit wird die Diagnose der folgenden Bedingungen unterstützt. Wir arbeiten aber bereits an weiteren Bedingungen.

### <a name="gateway"></a>Gateway

| Fehlertyp | `Reason` | Log|
|---|---|---|
| NoFault | Es wurde kein Fehler erkannt. |Ja|
| GatewayNotFound | Das Gateway wurde nicht gefunden oder nicht bereitgestellt. |Nein|
| PlannedMaintenance |  Die Gatewayinstanz wird zurzeit gewartet.  |Nein|
| UserDrivenUpdate | Es wird ein Benutzerupdate des Geräts durchgeführt. Dies könnte z.B. eine Größenänderung sein. | Nein |
| VipUnResponsive | Die primäre Instanz des Gateways ist nicht erreichbar. Dies tritt auf, wenn beim Integritätstest ein Fehler auftritt. | Nein |
| PlatformInActive | Es ist ein Problem mit der Plattform aufgetreten. | Nein|
| ServiceNotRunning | Der zugrunde liegende Dienst wird nicht ausgeführt. | Nein|
| NoConnectionsFoundForGateway | Es gibt keine Verbindungen auf dem Gateway. Dies ist nur eine Warnung.| Nein|
| ConnectionsNotConnected | Es wurde keine der Verbindungen hergestellt. Dies ist nur eine Warnung.| Ja|
| GatewayCPUUsageExceeded | Die aktuelle CPU-Auslastung auf dem Gateway liegt über 95 %. | Ja |

### <a name="connection"></a>Verbindung

| Fehlertyp | `Reason` | Log|
|---|---|---|
| NoFault | Es wurde kein Fehler erkannt. |Ja|
| GatewayNotFound | Das Gateway wurde nicht gefunden oder nicht bereitgestellt. |Nein|
| PlannedMaintenance | Die Gatewayinstanz wird zurzeit gewartet.  |Nein|
| UserDrivenUpdate | Es wird ein Benutzerupdate des Geräts durchgeführt. Dies könnte z.B. eine Größenänderung sein.  | Nein |
| VipUnResponsive | Die primäre Instanz des Gateways ist nicht erreichbar. Dies tritt auf, wenn beim Integritätstest ein Fehler auftritt. | Nein |
| ConnectionEntityNotFound | Die Verbindungskonfiguration fehlt. | Nein |
| ConnectionIsMarkedDisconnected | Die Verbindung ist als „getrennt“ gekennzeichnet. |Nein|
| ConnectionNotConfiguredOnGateway | Für den zugrunde liegenden Dienst wurde die Verbindung nicht konfiguriert. | Ja |
| ConnectionMarkedStandby | Der zugrunde liegende Dienst ist als im Ruhezustand gekennzeichnet.| Ja|
| Authentication | Der vorinstallierte Schlüssel stimmt nicht überein. | Ja|
| PeerReachability | Das Peergateway ist nicht erreichbar. | Ja|
| IkePolicyMismatch | Das Peergateway verfügt über IKE-Richtlinien, die von Azure nicht unterstützt werden. | Ja|
| WfpParse Error | Beim Analysieren des WFP-Protokolls ist ein Fehler aufgetreten. |Ja|

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie unter [Überwachen von VPN-Gateways mit der Problembehandlung von Azure Network Watcher](network-watcher-monitor-with-azure-automation.md), wie Sie die VPN Gateway-Konnektivität mit PowerShell und Azure Automation überprüfen.

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png