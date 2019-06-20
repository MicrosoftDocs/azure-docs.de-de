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
ms.openlocfilehash: e2e91dc91cf0fbe6827808785a4c3cc25b06542b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177911"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Kann ich für Punkt-zu-Standort-Verbindungen meine eigene interne PKI-Stammzertifizierungsstelle verwenden?

Ja. Bisher konnten nur selbstsignierte Stammzertifikate verwendet werden. Sie können weiterhin 20 Stammzertifikate hochladen.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Mit welchen Tools kann ich Zertifikate erstellen?

Sie können Ihre Unternehmens-PKI-Lösung (interne PKI), Azure PowerShell, MakeCert und OpenSSL verwenden.

### <a name="certsettings"></a>Gibt es Anleitungen für Zertifikateinstellungen und -parameter?

* **Interne PKI/Unternehmens-PKI-Lösung:** Lesen Sie die Schritte zum [Generieren von Zertifikaten](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** Entsprechende Schritte finden Sie im Artikel zu [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md).

* **MakeCert:** Die erforderlichen Schritte finden Sie im Artikel zu [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md).

* **OpenSSL:** 

    * Stellen Sie beim Exportieren von Zertifikaten sicher, dass das Stammzertifikat in Base64 konvertiert wird.

    * Clientzertifikat:

      * Geben Sie beim Erstellen des privaten Schlüssels als Länge 4096 an.
      * Geben Sie beim Erstellen des Zertifikats für den *-extensions*-Parameter *usr_cert* an.