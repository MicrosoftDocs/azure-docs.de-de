---
title: 'Einrichten von Windows Virtual Desktop zur Verwendung eines Kerberos-Schlüsselverteilungscenter-Proxys: Azure'
description: Es wird beschrieben, wie Sie einen Windows Virtual Desktop-Hostpool für die Verwendung eines Kerberos-Schlüsselverteilungscenter-Proxys einrichten.
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 102ddc1c8937c66a92416ddb6d5f2d25f2a3c349
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219654"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Konfigurieren eines Kerberos-Schlüsselverteilungscenter-Proxys (Vorschau)

> [!IMPORTANT]
> Dieses Feature ist zurzeit als öffentliche Preview verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Artikel wird veranschaulicht, wie Sie einen Kerberos-Schlüsselverteilungscenter-Proxy (Key Distribution Center, KDC) (Vorschau) für Ihren Hostpool konfigurieren. Mit diesem Proxy können Organisationen außerhalb ihrer Unternehmensgrenzen die Authentifizierung für Kerberos durchführen. Beispielsweise können Sie den KDC-Proxy nutzen, um die Smartcard-Authentifizierung für externe Clients zu aktivieren.

## <a name="how-to-configure-the-kdc-proxy"></a>Konfigurieren des KDC-Proxys

Konfigurieren Sie den KDC-Proxy wie folgt:

1. Melden Sie sich beim Azure-Portal als Administrator an.

2. Navigieren Sie zur Seite „Windows Virtual Desktop“.

3. Wählen Sie den Hostpool, für den Sie den KDC-Proxy aktivieren möchten, und dann die Option **RDP-Eigenschaften** aus.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Seite im Azure-Portal, auf der ein Benutzer die Option „Hostpools“, den Namen des Beispielhostpools und dann „RDP-Eigenschaften“ auswählt](media/rdp-properties.png)

4. Wählen Sie die Registerkarte **Erweitert** aus, und geben Sie dann einen Wert im folgenden Format ohne Leerzeichen ein:

    > kdcproxyname:s:\<fqdn\>

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Ausgewählte Registerkarte „Erweitert“ mit eingegebenem Wert (Schritt 4)](media/advanced-tab-selected.png)

5. Klicken Sie auf **Speichern**.

6. Der ausgewählte Hostpool sollte nun damit beginnen, RDP-Verbindungsdateien mit dem von Ihnen ausgefüllten Feld „kdcproxyname“ auszugeben.

## <a name="next-steps"></a>Nächste Schritte

Die Remotedesktopgateway-Rolle in Remotedesktopdienste enthält einen KDC-Proxydienst. Informationen zur Einrichtung als Ziel für Windows Virtual Desktop finden Sie unter [Bereitstellen der Remotedesktopgateway-Rolle in Windows Virtual Desktop](rd-gateway-role.md).
