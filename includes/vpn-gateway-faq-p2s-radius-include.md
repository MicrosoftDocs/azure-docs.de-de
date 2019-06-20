---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 857d29f407c9939143fbb8263be40dadb040efdc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177916"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="is-radius-authentication-supported-on-all-azure-vpn-gateway-skus"></a>Wird RADIUS-Authentifizierung von allen Azure-VPN-Gateway-SKUs unterstützt?

RADIUS-Authentifizierung wird für die SKUs „VpnGw1“, „VpnGw2“ und „VpnGw3“ unterstützt. Bei Verwendung älterer SKUs wird die RADIUS-Authentifizierung für die Standard- und die High-Performance-SKU unterstützt. In der Basic-Gateway-SKU wird sie nicht unterstützt. 
 
### <a name="is-radius-authentication-supported-for-the-classic-deployment-model"></a>Wird die RADIUS-Authentifizierung für das klassische Bereitstellungsmodell unterstützt?
 
Nein. Die RADIUS-Authentifizierung wird für das klassische Bereitstellungsmodell nicht unterstützt.
 
### <a name="are-3rd-party-radius-servers-supported"></a>Werden RADIUS-Server von Drittanbietern unterstützt?

Ja, RADIUS-Server von Drittanbietern werden unterstützt.
 
### <a name="what-are-the-connectivity-requirements-to-ensure-that-the-azure-gateway-is-able-to-reach-an-on-premises-radius-server"></a>Mit welchen Konnektivitätsanforderungen wird sichergestellt, dass das Azure-Gateway einen lokalen RADIUS-Server erreicht?

Eine VPN-S2S-Verbindung mit dem lokalen Standort ist erforderlich. (Dabei müssen die korrekten Routen konfiguriert sein.)  
 
### <a name="can-traffic-to-an-on-premises-radius-server-from-the-azure-vpn-gateway-be-routed-over-an-expressroute-connection"></a>Kann Datenverkehr für einen lokalen RADIUS-Server (vom Azure-VPN-Gateway) über eine ExpressRoute-Verbindung weitergeleitet werden?

Nein. Er kann nur über eine S2S-Verbindung weitergeleitet werden.
 
### <a name="is-there-a-change-in-the-number-of-sstp-connections-supported-with-radius-authentication-what-is-the-maximum-number-of-sstp-and-ikev2-connections-supported"></a>Hat sich die Anzahl von SSTP-Verbindungen geändert, die mit RADIUS-Authentifizierung unterstützt werden? Wie viele SSTP- und IKEv2-Verbindungen werden maximal unterstützt?

Die maximale Anzahl von SSTP-Verbindungen, die für ein Gateway mit RADIUS-Authentifizierung unterstützt werden, hat sich nicht geändert. Es werden nach wie vor maximal 128 SSTP-Verbindungen unterstützt – abhängig von der Gateway-SKU für IKEv2. Weitere Informationen zur Anzahl von unterstützten Verbindungen finden Sie unter [Gateway-SKUs](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).
 
### <a name="what-is-the-difference-between-doing-certificate-authentication-using-a-radius-server-vs-using-azure-native-certificate-authentication-by-uploading-a-trustedcertificate-to-azure"></a>Was ist der Unterschied zwischen der Zertifikatauthentifizierung mithilfe eines RADIUS-Servers und der systemeigenen Azure-Zertifikatauthentifizierung durch Hochladen eines vertrauenswürdigen Zertifikats in Azure?

Bei der RADIUS-Zertifikatauthentifizierung wird die Authentifizierungsanforderung an einen RADIUS-Server weitergeleitet, der die tatsächliche Zertifikatüberprüfung durchführt. Diese Option ist nützlich für die Integration in eine bereits vorhandene Zertifikatauthentifizierungsinfrastruktur über RADIUS.
  
Bei Verwendung von Azure für die Zertifikatauthentifizierung führt das Azure-VPN-Gateway die Überprüfung des Zertifikats durch. Sie müssen den öffentlichen Schlüssel des Zertifikats in das Gateway hochladen. Sie können auch eine Liste gesperrter Zertifikate angeben, für die die Verbindung nicht zugelassen werden soll.

### <a name="does-radius-authentication-work-with-both-ikev2-and-sstp-vpn"></a>Funktioniert die RADIUS-Authentifizierung mit IKEv2 und SSTP VPN?

Ja, die RADIUS-Authentifizierung funktioniert mit IKEv2 und SSTP VPN? 
