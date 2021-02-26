---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/08/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f3eb2d9469ab3a3d2c1d09e4adc3ee2cb1f86e6e
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979067"
---
## <a name="extract-the-zip-file"></a>Extrahieren der ZIP-Datei

Extrahieren Sie die ZIP-Datei. Die Datei enthält die folgenden Ordner:

* AzureVPN
* Allgemein
* OpenVPN (wenn Sie OpenVPN mit Einstellungen für ein **Azure-Zertifikat** oder eine **RADIUS-Authentifizierung** auf dem Gateway aktiviert haben). Wählen Sie den zu Ihrer Konfiguration gehörenden Artikel aus, um einen Mandanten zu erstellen.

  * [VPN Gateway: Erstellen eines Mandanten](../articles/vpn-gateway/openvpn-azure-ad-tenant.md).
  * [Virtual WAN: Erstellen eines Mandanten](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="retrieve-information"></a>Abrufen von Informationen

Navigieren Sie im Ordner **AzureVPN** zur Datei **_azurevpnconfig.xml_**, und öffnen Sie sie in Editor. Notieren Sie sich den Text zwischen den folgenden Tags.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Profildetails

Wenn Sie eine Verbindung hinzufügen, verwenden Sie die im vorherigen Schritt auf der Seite mit den Profildetails gesammelten Informationen. Die Felder entsprechen den folgenden Informationen:

* **Audience:** Die Empfängerressource, für die das Token vorgesehen ist.
* **Issuer:** Hiermit wird der Azure AD-Mandant angegeben und der Sicherheitstokendienst (STS) identifiziert, der das Token ausgegeben hat.
* **Tenant:** Ein unveränderlicher, eindeutiger Bezeichner des Verzeichnismandanten, der das Token ausgestellt hat.
* **FQDN:** Der vollqualifizierte Domänenname (FQDN) in Azure VPN Gateway.
* **ServerSecret:** Der vorinstallierte Schlüssel des VPN-Gateways.

## <a name="folder-contents"></a>Ordnerinhalt

* Der Ordner **generic** enthält das öffentliche Serverzertifikat und die Datei „VpnSettings.xml“. Die Datei „VpnSettings.xml“ enthält Informationen, die zum Konfigurieren eines generischen Clients benötigt werden.

* Die heruntergeladene ZIP-Datei kann auch noch die Ordner **WindowsAmd64** und **WindowsX86** enthalten. Diese Ordner enthalten das Installationsprogramm für SSTP und IKEv2 für Windows-Clients. Sie benötigen Administratorrechte auf dem Client, um diese zu installieren.
