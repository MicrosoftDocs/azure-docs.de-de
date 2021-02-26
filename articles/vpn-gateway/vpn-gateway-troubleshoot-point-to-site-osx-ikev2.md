---
title: 'Azure-VPN Gateway: Problembehandlung für Point-to-Site-Verbindungen: Mac OS X-Clients'
description: Erfahren Sie, wie Sie Point-to-Site-Konnektivitätsprobleme von Mac OS X unter Verwendung des nativen VPN-Clients und IKEv2 beheben.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 02/10/2021
ms.author: alzam
ms.openlocfilehash: 4c8657f8a40084a726280efe471c0791931294f7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366635"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Problembehandlung bei Point-to-Site-VPN-Verbindungen von Mac OS X-VPN-Clients

Dieser Artikel hilft Ihnen beim Beheben von Point-to-Site-Konnektivitätsproblemen von Mac OS X unter Verwendung des nativen VPN-Clients und IKEv2. Der VPN-Client in Mac für IKEv2 ist sehr einfach und bietet wenig Anpassungsspielraum. Es müssen nur vier Einstellungen überprüft werden:

* Serveradresse
* Remote-ID
* Lokale ID
* Authentifizierungseinstellungen
* Betriebssystemversion (10.11 oder höher)


## <a name="troubleshoot-certificate-based-authentication"></a><a name="VPNClient"></a> Problembehandlung bei zertifikatbasierter Authentifizierung
1. Überprüfen Sie die VPN-Clienteinstellungen. Wechseln Sie durch Drücken von BEFEHLTASTE + UMSCHALTTASTE ZU **Network Setting (Netzwerkeinstellung)** , und geben Sie dann „VPN“ ein, um die VPN-Clienteinstellungen zu überprüfen. Klicken Sie in der Liste auf den VPN-Eintrag, der untersucht werden muss.

   ![IKEv2-zertifikatbasierte Authentifizierung](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Überprüfen Sie, ob die **Server Address (Serveradresse)** der vollständige FQDN ist und die „cloudapp.net“ enthält.
3. Die **Remote ID** sollte mit der „Server Address“ (Serveradresse) (Gateway-FQDN) des Servers identisch sein.
4. Die **Local ID (lokale ID)** muss mit dem **Antragsteller** des Clientzertifikats identisch sein.
5. Klicken Sie auf **Authentication Settings (Authentifizierungseinstellungen)** um die Seite „Authentication Settings“ zu öffnen.

   ![Screenshot eines Dialogfelds „Authentifizierungseinstellungen“ mit ausgewählter Option „Zertifikat“.](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Überprüfen Sie, ob **Certificate (Zertifikat)** in der Dropdownliste ausgewählt ist.
7. Klicken Sie auf die Schaltfläche **Select (Auswählen)** , und stellen Sie sicher, dass das richtige Zertifikat ausgewählt ist. Klicken Sie zum Speichern etwaiger Änderungen auf **OK**.

## <a name="troubleshoot-username-and-password-authentication"></a><a name="ikev2"></a> Problembehandlung bei Benutzernamen- und Kennwortauthentifizierung

1. Überprüfen Sie die VPN-Clienteinstellungen. Wechseln Sie durch Drücken von BEFEHLTASTE + UMSCHALTTASTE ZU **Network Setting (Netzwerkeinstellung)** , und geben Sie dann „VPN“ ein, um die VPN-Clienteinstellungen zu überprüfen. Klicken Sie in der Liste auf den VPN-Eintrag, der untersucht werden muss.

   ![IKEv2-Benutzername/-Kennwort](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Überprüfen Sie, ob die **Server Address (Serveradresse)** der vollständige FQDN ist und die „cloudapp.net“ enthält.
3. Die **Remote ID** sollte mit der „Server Address“ (Serveradresse) (Gateway-FQDN) des Servers identisch sein.
4. Die **Local ID (lokale ID)** kann leer sein.
5. Klicken Sie auf die Schaltfläche **Authentication Setting (Authentifizierungseinstellung)** , und stellen Sie sicher, dass „Username“ (Benutzername) in der Dropdownliste ausgewählt ist.

   ![Screenshot eines Dialogfelds „Authentifizierungseinstellungen“ mit ausgewählter Option „Benutzername“.](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. Stellen Sie sicher, dass die richtigen Anmeldeinformationen eingegeben sind.

## <a name="additional-steps"></a><a name="additional"></a>Zusätzliche Schritte

Wenn Sie die vorherigen Schritte versucht haben, und alles ordnungsgemäß konfiguriert ist, laden Sie [Wireshark](https://www.wireshark.org/#download) herunter, und führen Sie eine Paketerfassung durch.

1. Filtern Sie nach *isakmp*, und achten Sie auf die **IKE_SA**-Pakete. Sie sollten die SA-Vorschlagdetails unter **Payload: Security Association** (Nutzlast: Sicherheitszuordnung) sehen. 
2. Stellen Sie sicher, dass Client und Server über einen gemeinsamen Satz verfügen.

   ![Paket](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Wenn keine Serverantwort auf die Netzwerkablaufverfolgungen erfolgt, überprüfen Sie auf der Website des Azure-Portals auf der Seite für die Azure-Gatewaykonfiguration, ob das IKEv2-Protokoll aktiviert ist.

## <a name="next-steps"></a>Nächste Schritte
Wenn Sie weitere Hilfe benötigen, wenden Sie sich an den [Microsoft-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
