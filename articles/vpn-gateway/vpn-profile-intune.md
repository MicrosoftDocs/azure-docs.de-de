---
title: Erstellen eines Intune-Profils für Azure-VPN-Clients
titleSuffix: Azure VPN Gateway
description: Erfahren Sie, wie Sie ein benutzerdefiniertes Intune-Profil erstellen, um es für Azure-VPN-Clientprofile bereitzustellen.
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/15/2020
ms.author: cherylmc
ms.openlocfilehash: 63b1d7f95711017feba52cad97f05128d83734da
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655169"
---
# <a name="create-an-intune-profile-to-deploy-vpn-client-profiles"></a>Erstellen eines Intune-Profils zum Bereitstellen für VPN-Clientprofile

Sie können Profile für Azure-VPN-Clients (Windows 10) mit Microsoft Intune bereitstellen. Dieser Artikel unterstützt Sie beim Erstellen eines Intune-Profils mit benutzerdefinierten Einstellungen.

> [!NOTE]
> Diese Methode eignet sich nur für Bereitstellungsprofile, die Azure Active Directory oder ein gängiges Zertifikat für die Clientauthentifizierung verwenden. Wenn eindeutige Clientzertifikate genutzt werden, muss jeder Benutzer das korrekte Zertifikat manuell im Azure VPN Client auswählen.
>

## <a name="prerequisites"></a>Voraussetzungen

* Geräte sind bereits mit Intune MDM registriert.
* Der Azure-VPN-Client für Windows 10 wurde bereits auf dem Clientcomputer bereitgestellt.
* Nur Windows-Versionen ab 19H2 werden unterstützt.

## <a name="modify-xml"></a><a name="xml"></a>Ändern des XML-Codes

In den folgenden Schritten verwenden wir eine XML-Beispieldatei für ein benutzerdefiniertes OMA-URI-Profil für Intune mit den folgenden Einstellungen:

* Automat. Verbindung EIN
* Erkennung vertrauenswürdiger Netzwerke aktiviert.

Weitere unterstützte Optionen finden Sie im Artikel [VPNv2-Konfigurationsdienstanbieter](/windows/client-management/mdm/vpnv2-csp).

1. Laden Sie das VPN-Profil aus dem Azure-Portal herunter, und extrahieren Sie die Datei *azurevpnconfig.xml* aus dem Paket.
1. Kopieren Sie den folgenden Text, und fügen Sie ihn im Text-Editor in eine neue Datei ein.

   ```xml-interactive
    <VPNProfile>
      <!--<EdpModeId>corp.contoso.com</EdpModeId>-->
      <RememberCredentials>true</RememberCredentials>
      <AlwaysOn>true</AlwaysOn>
      <TrustedNetworkDetection>contoso.com,test.corp.contoso.com</TrustedNetworkDetection>
      <DeviceTunnel>false</DeviceTunnel>
      <RegisterDNS>false</RegisterDNS>
      <PluginProfile>
        <ServerUrlList>azuregateway-7cee0077-d553-4323-87df-069c331f58cb-053dd0f6af02.vpn.azure.com</ServerUrlList> 
        <CustomConfiguration>

        </CustomConfiguration>
        <PluginPackageFamilyName>Microsoft.AzureVpn_8wekyb3d8bbwe</PluginPackageFamilyName>
      </PluginProfile>
    </VPNProfile>
   ```
1. Ändern Sie den Eintrag zwischen ```<ServerUrlList>``` und ```</ServerUrlList>``` mit dem Eintrag aus dem heruntergeladenen Profil (azurevpnconfig.xml). Ändern Sie den FQDN „TrustedNetworkDetection“, sodass er Ihrer Umgebung entspricht.
1. Öffnen Sie das heruntergeladene Azure-Profil (azurevpnconfig.xml), und kopieren Sie den gesamten Inhalt in die Zwischenablage, indem Sie den Text markieren und STRG+C drücken. 
1. Fügen Sie den kopierten Text aus dem vorherigen Schritt in die Datei ein, die Sie in Schritt 2 zwischen den ```<CustomConfiguration>  </CustomConfiguration>```-Tags erstellt haben. Speichern Sie die Datei mit der Erweiterung XML.
1. Notieren Sie sich den Wert in den ```<name>  </name>```-Tags. Dies ist der Name des Profils. Sie benötigen diesen Namen, wenn Sie das Profil in Intune erstellen. Schließen Sie die Datei, und merken Sie sich ihren Speicherort.

## <a name="create-intune-profile"></a>Erstellen eines Intune-Profils

In diesem Abschnitt erstellen Sie ein Microsoft Intune-Profil mit benutzerdefinierten Einstellungen.

1. Melden Sie sich bei Intune an, und navigieren Sie zu **Geräte-> Konfigurationsprofile**. Wählen Sie **+ Profil erstellen** aus.

   :::image type="content" source="./media/create-profile-intune/configuration-profile.png" alt-text="Konfigurationsprofile":::
1. Wählen Sie unter **Plattform** die Option **Windows 10 und höher** aus. Wählen Sie für **Profil** die Option **Benutzerdefiniert** aus. Wählen Sie anschließend **Erstellen**.
1. Geben Sie dem Profil einen Namen und eine Beschreibung, und wählen Sie dann **Weiter** aus.
1. Wählen Sie auf der Registerkarte **Konfigurationseinstellungen** die Option **Hinzufügen** aus.

    * **Name:** Geben Sie einen Namen für die Konfiguration ein.
    * **Beschreibung:** Optionale Beschreibung.
    * **OMA-URI:** ```./User/Vendor/MSFT/VPNv2/<name of your connection>/ProfileXML``` (diese Information finden Sie in der Datei „azurevpnconfig.xml“ im <name></name>-Tag).
    * **Datentyp:** Zeichenfolge (XML-Datei).

   Wählen Sie das Ordnersymbol aus und die Datei, die Sie in Schritt 6 in den [XML](#xml)-Schritten gespeichert haben. Wählen Sie **Hinzufügen**.

   :::image type="content" source="./media/create-profile-intune/configuration-settings.png" alt-text="Konfigurationseinstellungen" lightbox="./media/create-profile-intune/configuration-settings.png":::
1. Wählen Sie **Weiter** aus.
1. Wählen Sie unter **Zuweisungen** die Gruppe aus, zu der Sie die Konfiguration pushen möchten. Klicken Sie anschließend auf **Weiter**.
1. Anwendbarkeitsregeln sind optional. Definieren Sie bei Bedarf beliebige Regeln, und wählen Sie dann **Weiter** aus.
1. Wählen Sie auf der Seite **Überprüfen + erstellen** die Option **Erstellen** aus.

    :::image type="content" source="./media/create-profile-intune/create-profile.png" alt-text="Profil erstellen":::
1. Ihr benutzerdefiniertes Profil wird nun erstellt. Die Microsoft Intune-Schritte zum Bereitstellen dieses Profils finden Sie unter [Zuweisen von Benutzer- und Geräteprofilen in Microsoft Intune](/mem/intune/configuration/device-profile-assign).
 
## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Point-to-Site-VPN-Verbindungen finden Sie unter [Informationen zu Point-to-Site-VPN](point-to-site-about.md).