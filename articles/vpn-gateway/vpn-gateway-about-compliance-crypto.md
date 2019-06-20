---
title: Kryptografische Anforderungen und Azure-VPN-Gateways | Microsoft-Dokumentation
description: In diesem Artikel werden kryptografische Anforderungen und Azure-VPN-Gateways behandelt.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2017
ms.author: yushwang
ms.openlocfilehash: 060e647badcc3bad7b44d7cef3530c36b8ecdf57
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60648676"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Kryptografische Anforderungen und Azure-VPN-Gateways

In diesem Artikel wird erläutert, wie Sie Azure-VPN-Gateways so konfigurieren können, dass Ihre kryptografischen Anforderungen für standortübergreifende S2S-VPN-Tunnel und VNET-zu-VNET-Verbindungen in Azure erfüllt werden. 

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>IPsec- und IKE-Richtlinienparameter für Azure-VPN-Gateways
Der IPsec- und IKE-Protokollstandard unterstützt ein breites Spektrum von Kryptografiealgorithmen in verschiedenen Kombinationen. Wenn Kunden keine bestimmte Kombination aus Kryptografiealgorithmen und Parametern anfordern, verwenden Azure-VPN-Gateways einen Satz von Standardvorschlägen. Die Standardrichtliniensätze wurden so gewählt, dass maximale Interoperabilität mit einer Vielzahl von VPN-Geräten von Drittanbietern in Standardkonfigurationen gewährleistet ist. Somit können die Richtlinien und zahlreichen Vorschläge nicht alle möglichen Kombinationen aus verfügbaren Kryptografiealgorithmen und Schlüsselstärken abdecken.

Der Standardrichtliniensatz für Azure-VPN-Gateways ist im Dokument [Informationen zu VPN-Geräten und IPsec-/IKE-Parametern für VPN-Gatewayverbindungen zwischen Standorten](vpn-gateway-about-vpn-devices.md) aufgeführt.

## <a name="cryptographic-requirements"></a>Kryptografische Anforderungen
Für Kommunikationsverbindungen, die spezifische Kryptografiealgorithmen oder Parameter erfordern, was normalerweise auf Konformitäts- oder Sicherheitsanforderungen zurückzuführen ist, können Kunden ihre Azure-VPN-Gateways jetzt so konfigurieren, dass anstelle der Azure-Standardrichtliniensätze eine benutzerdefinierte IPsec/IKE-Richtlinie mit bestimmten Kryptografiealgorithmen und Schlüsselstärken verwendet wird.

Beispielsweise nutzen die IKEv2-Hauptmodusrichtlinien für Azure-VPN-Gateways nur Diffie-Hellman-Gruppe 2 (1024 Bit), während Kunden möglicherweise stärkere Gruppen zur Verwendung in IKE angeben möchten, z.B. Gruppe 14 (2048 Bit), Gruppe 24 (2048-Bit-MODP-Gruppe) oder ECP (Gruppe für elliptische Kurve) 256 oder 384 Bit (Gruppe 19 bzw. Gruppe 20). Ähnliche Anforderungen gelten auch für IPsec-Schnellmodusrichtlinien.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Benutzerdefinierte IPsec/IKE-Richtlinie mit Azure-VPN-Gateways
Azure-VPN-Gateways unterstützen jetzt eine benutzerdefinierte IPsec/IKE-Richtlinie pro Verbindung. Für eine Standort-zu-Standort- oder eine VNET-zu-VNET-Verbindung können Sie eine bestimmte Kombination aus Kryptografiealgorithmen für IPsec und IKE mit der gewünschten Schlüsselstärke auswählen, wie im folgenden Beispiel gezeigt:

![IPsec/IKE-Richtlinie](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Sie können eine IPsec/IKE-Richtlinie erstellen und auf eine neue oder vorhandene Verbindung anwenden. 

### <a name="workflow"></a>Workflow

1. Erstellen Sie die virtuellen Netzwerke, VPN-Gateways oder lokalen Netzwerkgateways für Ihre Netzwerktopologie entsprechend den Beschreibungen in anderen Anleitungen.
2. Erstellen Sie eine IPsec/IKE-Richtlinie.
3. Sie können die Richtlinie anwenden, wenn Sie eine S2S- oder VNET-zu-VNET-Verbindung erstellen.
4. Wenn die Verbindung bereits erstellt wurde, können Sie die Richtlinie für eine vorhandene Verbindung anwenden oder aktualisieren.


## <a name="ipsecike-policy-faq"></a>Häufig gestellte Fragen zur IPsec/IKE-Richtlinie

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]


## <a name="next-steps"></a>Nächste Schritte
Ausführliche Anweisungen zum Konfigurieren einer benutzerdefinierten IPsec/IKE-Richtlinie für eine Verbindung finden Sie unter [Konfigurieren einer IPsec/IKE-Richtlinie](vpn-gateway-ipsecikepolicy-rm-powershell.md).

Weitere Informationen zur Option UsePolicyBasedTrafficSelectors finden Sie unter [Verbinden mehrerer richtlinienbasierter VPN-Geräte](vpn-gateway-connect-multiple-policybased-rm-ps.md).