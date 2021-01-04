---
title: include file
description: include file
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9caf63fc90be7bae0461ddc24c94594a32199765
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812703"
---
#### <a name="microsoft-windows"></a>Microsoft Windows

##### <a name="openvpn"></a>OpenVPN

1. Laden Sie den OpenVPN-Client von der offiziellen Website herunter, und installieren Sie ihn.
1. Laden Sie das VPN-Profil für das Gateway herunter. Dies kann auf der Registerkarte „Benutzer-VPN-Konfigurationen“ im Azure-Portal oder über „New-AzureRmVpnClientConfiguration“ in PowerShell erfolgen.
1. Entzippen Sie das Profil. Öffnen Sie dann im Editor im Ordner „OpenVPN“ die Konfigurationsdatei „vpnconfig.ovpn“.
1. Füllen Sie den Abschnitt „P2S client certificate“ mit dem öffentlichen Schlüssel für das P2S-Clientzertifikat in Base64 aus. In einem Zertifikat im PEM-Format können Sie die CER-Datei öffnen und den Base64-Schlüssel zwischen den Zertifikatskopfzeilen herauskopieren. Die Schritte sind unter [Generieren und Exportieren von Zertifikaten für VPN-Verbindungen von Virtual WAN-Benutzern](../articles/virtual-wan/certificates-point-to-site.md) beschrieben.
1. Füllen Sie den Abschnitt „Private key“ mit dem privaten Schlüssel für das P2S-Clientzertifikat in Base64 aus. Weitere Informationen finden Sie unter [Extrahieren des privaten Schlüssels](../articles/virtual-wan/howto-openvpn-clients.md#windows).
1. Ändern Sie keine anderen Felder. Verwenden Sie die ausgefüllte Konfiguration in der Clienteingabe, um eine Verbindung mit dem VPN herzustellen.
1. Kopieren Sie die Datei „vpnconfig.ovpn“ in den Ordner „C:\Programme\OpenVPN\config“.
1. Klicken Sie mit rechten Maustaste in der Taskleiste auf das Symbol „OpenVPN“, und wählen Sie **Verbinden** aus.

##### <a name="ikev2"></a>IKEv2

1. Wählen Sie die VPN-Clientkonfigurationsdateien, die der Architektur des Windows-Computers entsprechen. Wählen Sie für eine 64-Bit-Prozessorarchitektur das Installer-Paket „VpnClientSetupAmd64“ aus. Wählen Sie für eine 32-Bit-Prozessorarchitektur das Installer-Paket „VpnClientSetupX86“ aus.
1. Doppelklicken Sie auf das Paket, um es zu installieren. Sollte eine SmartScreen-Popupmeldung angezeigt werden, wählen Sie **Weitere Informationen** und anschließend **Trotzdem ausführen** aus.
1. Navigieren Sie auf dem Clientcomputer zu **Netzwerkeinstellungen**, und wählen Sie **VPN** aus. Die VPN-Verbindung zeigt den Namen des virtuellen Netzwerks an, mit dem eine Verbindung hergestellt wird.
1. Bevor Sie versuchen, eine Verbindung herzustellen, überprüfen Sie, ob Sie auf dem Clientcomputer ein Clientzertifikat installiert haben. Ein Clientzertifikat ist für die Authentifizierung erforderlich, wenn Sie den Typ „native Azure-Zertifikatauthentifizierung“ verwenden. Weitere Informationen zum Generieren von Zertifikaten finden Sie unter [Generieren von Zertifikaten](../articles/virtual-wan/certificates-point-to-site.md). Weitere Informationen zum Installieren eines Clientzertifikats finden Sie unter [Installieren eines Clientzertifikats](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md).
