---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5ef67580928a45609f50d3fe798eb9d054265c0a
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376127"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="what-should-i-do-if-im-getting-a-certificate-mismatch-when-connecting-using-certificate-authentication"></a>Wie muss ich vorgehen, wenn beim Herstellen einer Verbindung über die Zertifikatauthentifizierung ein Zertifikatkonflikt gemeldet wird?

Deaktivieren Sie **Identität des Servers durch Validierung des Zertifikat überprüfen** oder **fügen Sie den Server-FQDN zusammen mit dem Zertifikat hinzu** , wenn Sie ein Profil manuell erstellen. Sie können dazu **rasphone** an einer Eingabeaufforderung ausführen und das Profil in der Dropdown-Liste auswählen.

Es wird im Allgemeinen nicht empfohlen, die Validierung der Serveridentität zu umgehen. Bei der Azure-Zertifikatauthentifizierung wird jedoch für die Servervalidierung im VPN-Tunnelingprotokoll (IKEv2/SSTP) als auch im EAP-Protokoll das gleiche Zertifikat verwendet. Da das Serverzertifikat und der FQDN bereits vom VPN-Tunnelingprotokoll validiert wurden, ist es redundant, das gleiche in EAP erneut zu validieren.

![point-to-site auth](./media/vpn-gateway-faq-p2s-all-include/servercert.png "Serverzertifikat")

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>Kann ich meine eigene interne PKI-Stammzertifizierungsstelle verwenden, um Zertifikate für Punkt-zu-Standort-Verbindungen zu generieren?

Ja. Bisher konnten nur selbstsignierte Stammzertifikate verwendet werden. Sie können weiterhin 20 Stammzertifikate hochladen.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>Kann ich Zertifikate aus Azure Key Vault verwenden?

Nein.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Mit welchen Tools kann ich Zertifikate erstellen?

Sie können Ihre Unternehmens-PKI-Lösung (interne PKI), Azure PowerShell, MakeCert und OpenSSL verwenden.

### <a name="are-there-instructions-for-certificate-settings-and-parameters"></a><a name="certsettings"></a>Gibt es Anleitungen für Zertifikateinstellungen und -parameter?

* **Interne PKI/Unternehmens-PKI-Lösung:** Sehen Sie sich die Schritte zum [Generieren von Zertifikaten](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert) an.

* **Azure PowerShell:** Entsprechende Schritte finden Sie im Artikel zu [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md).

* **MakeCert:** Die erforderlichen Schritte finden Sie im Artikel zu [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md).

* **OpenSSL:** 

    * Stellen Sie beim Exportieren von Zertifikaten sicher, dass das Stammzertifikat in Base64 konvertiert wird.

    * Clientzertifikat:

      * Geben Sie beim Erstellen des privaten Schlüssels als Länge 4096 an.
      * Geben Sie beim Erstellen des Zertifikats für den *-extensions* -Parameter *usr_cert* an.
