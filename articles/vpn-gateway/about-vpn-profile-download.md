---
title: 'Azure-VPN Gateway: Informationen zu P2S VPN-Clientprofilen'
description: In diesem Artikel finden Sie die Informationen, die Sie für ein VPN-Clientprofil benötigen.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: 9bb66363d525648df08f32451842402ad1d0d93b
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979068"
---
# <a name="working-with-p2s-vpn-client-profile-files"></a>Arbeiten mit P2S-VPN-Clientprofildateien

Die Profildateien enthalten Informationen, die zum Konfigurieren einer VPN-Verbindung erforderlich sind. Dieser Artikel hilft Ihnen beim Abrufen und Verstehen der Informationen, die für ein VPN-Clientprofil erforderlich sind.

## <a name="generate-and-download-profile"></a>Profil generieren und herunterladen

Sie können Clientkonfigurationsdateien mithilfe von PowerShell oder mithilfe des Azure-Portals erstellen. Mit beiden Methoden wird die gleiche ZIP-Datei zurückgegeben.

### <a name="portal"></a>Portal

1. Navigieren Sie im Azure-Portal zum Gateway für das virtuelle Netzwerk, mit dem Sie eine Verbindung herstellen möchten.
1. Wählen Sie auf der Seite mit dem Gateway für virtuelle Netzwerke die Option **Point-to-Site-Konfiguration** aus.
1. Wählen Sie oben auf der Seite „Point-to-Site-Konfiguration“ die Option **VPN-Client herunterladen** aus. Es dauert einige Minuten, bis das Clientkonfigurationspaket generiert wird.
1. In Ihrem Browser wird ein Hinweis angezeigt, dass eine Clientkonfigurationsdatei im ZIP-Format verfügbar ist. Sie hat den gleichen Namen wie das Gateway. Entzippen Sie die Datei, um die Ordner anzuzeigen.

### <a name="powershell"></a>PowerShell

Zum Generieren mithilfe von PowerShell können Sie das folgende Beispiel verwenden:

1. Beim Generieren von VPN-Clientkonfigurationsdateien ist für „-AuthenticationMethod“ der Wert „EapTls“ angegeben. Erstellen Sie die VPN-Clientkonfigurationsdateien mit dem folgenden Befehl:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. Kopieren Sie die URL in Ihren Browser, um die ZIP-Datei herunterzuladen. Entzippen Sie dann die Datei, um die Ordner anzuzeigen.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* Der Ordner **OpenVPN** enthält das Profil *ovpn*, das geändert werden muss, um den Schlüssel und das Zertifikat einzuschließen. Weitere Informationen finden Sie unter [Konfigurieren von OpenVPN-Clients für Azure VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows). Wenn die Azure AD-Authentifizierung auf dem VPN-Gateway ausgewählt ist, ist dieser Ordner in der ZIP-Datei nicht vorhanden. Navigieren Sie stattdessen zum Ordner „AzureVPN“, und suchen Sie nach der Datei „azurevpnconfig.xml“.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Point-to-Site-VPN-Verbindungen finden Sie unter [Informationen zu Point-to-Site-VPN](point-to-site-about.md).
