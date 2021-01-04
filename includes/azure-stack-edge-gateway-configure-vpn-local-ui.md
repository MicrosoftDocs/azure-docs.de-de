---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: fa65a7354112a2b220686372459b348d45832dd9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465463"
---
Gehen Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts wie folgt vor. Dieser Schritt dauert einschließlich des Uploads der VPN-Konfigurationsdatei (oder der Diensttagdatei) etwa 15 Minuten. 

1. Wechseln Sie zu **Konfiguration > VPN**. Wählen Sie **Konfigurieren** aus.

    ![Konfigurieren der lokalen Benutzeroberfläche 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-1.png)

2. Gehen Sie auf dem Blatt **VPN konfigurieren** wie folgt vor:

    - Aktivieren Sie **VPN-Einstellungen**.
    - Geben Sie das **gemeinsame VPN-Geheimnis** an. Dies ist der gemeinsam verwendete Schlüssel, den Sie beim Erstellen der Azure-VPN-Verbindungsressource angegeben haben.
    - Geben Sie die **IP-Adresse des VPN-Gateways** an. Dies ist die IP-Adresse des Gateways des lokalen Azure-Netzwerks.
    - Wählen Sie für **PFS-Gruppe** die Option **Keine** aus. 
    - Wählen Sie für **DH-Gruppe** die Option **Gruppe2** aus.
    - Wählen Sie für **IPSec-Integritätsmethode** die Option **SHA256** aus.
    - Wählen Sie für **IPSec-Verschlüsselungsverfahren-Transformationskonstanten** die Option **GCMAES256** aus.
    - Wählen Sie für **IPSec-Authentifizierungs-Transformationskonstanten** die Option **GCMAES256** aus.
    - Wählen Sie für **IKE-Verschlüsselungsmethode** die Option **AES256** aus.
    - Wählen Sie **Übernehmen**.

        ![Konfigurieren der lokalen Benutzeroberfläche 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-2.png)

    Weitere Informationen zu den unterstützten Kryptografiealgorithmen finden Sie unter [Häufig gestellte Fragen zur IPsec/IKE-Richtlinie](../articles/vpn-gateway/vpn-gateway-about-compliance-crypto.md#ipsecike-policy-faq). 

3. Wählen Sie zum Hochladen der VPN-Routenkonfigurationsdatei **Hochladen** aus. 

    ![Konfigurieren der lokalen Benutzeroberfläche 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-3.png)

    - Navigieren Sie zu der Diensttags-*JSON*-Datei, die Sie im vorherigen Schritt auf Ihr lokales System heruntergeladen haben.
    - Wählen Sie die Region, die dem Gerät, dem virtuellen Netzwerk und den Gateways zugeordnet ist, als Azure-Region aus.
    - Wählen Sie **Übernehmen**.

        ![Konfigurieren der lokalen Benutzeroberfläche 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-4.png)
    
    Der Upload dauert auf dem Gerät ungefähr 7-8 Minuten.

4. Zum Hinzufügen clientspezifischer Routen konfigurieren Sie IP-Adressbereiche, auf die nur über das VPN zugegriffen werden soll. 

    - Wählen Sie unter **IP-Adressbereiche, auf die nur über VPN zugegriffen werden soll** die Option **Konfigurieren** aus.
    - Geben Sie einen gültigen IPv4-Bereich an, und wählen Sie **Hinzufügen** aus. Wiederholen Sie die Schritte zum Hinzufügen weiterer Bereiche.
    - Wählen Sie **Übernehmen**.

        ![Konfigurieren der lokalen Benutzeroberfläche 5](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-5.png)

