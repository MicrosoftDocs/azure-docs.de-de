---
title: 'Azure AD Connect: Überlegungen zur Hybrididentität für die Azure Government-Cloud'
description: Spezielle Überlegungen zum Bereitstellen von Azure AD Connect in der Azure Government-Cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e163ea34948906060996ee952f45ec0cdb467557
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504354"
---
# <a name="hybrid-identity-considerations-for-the-azure-government-cloud"></a>Überlegungen zur Hybrididentität für die Azure Government-Cloud

In diesem Artikel werden Überlegungen zum Integrieren einer Hybridumgebung in die Microsoft Azure Government-Cloud beschrieben. Diese Informationen dienen als Referenz für Administratoren und Architekten, die mit der Azure Government-Cloud arbeiten.

> [!NOTE]
> Um eine lokale Microsoft Active Directory-Umgebung (lokal oder in einer IaaS in derselben Cloudinstanz gehostet) mit der Azure Government-Cloud zu integrieren, müssen Sie ein Upgrade auf das neueste Release von [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) durchführen.

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Whitelist*, den Microsoft nicht länger verwendet. Sobald der Begriff aus der Software entfernt wurde, wird er auch aus diesem Artikel entfernt.

Eine vollständige Liste der Endpunkte des US-Verteidigungsministeriums finden Sie in der [Dokumentation](/office365/enterprise/office-365-u-s-government-dod-endpoints).

## <a name="azure-ad-pass-through-authentication"></a>Azure AD-Passthrough-Authentifizierung

Die folgenden Informationen beschreiben die Implementierung von Pass-Through-Authentifizierung und die Azure Government-Cloud.

### <a name="allow-access-to-urls"></a>Zulassen des Zugriffs auf URLs

Überprüfen Sie vor dem Bereitstellen des Pass-Through-Authentifizierungs-Agents, ob zwischen den Servern und Azure AD eine Firewall vorhanden ist. Wenn Ihre Firewall oder Ihr Proxy durch DNS (Domain Name System) blockierte oder sichere Programme zulässt, fügen Sie die folgenden Verbindungen hinzu.

> [!NOTE]
> Die folgende Anleitung gilt auch für die Installation des [Azure AD-Anwendungsproxyconnectors](../manage-apps/what-is-application-proxy.md) für Azure Government-Umgebungen.

|URL |Wie diese verwendet wird|
|-----|-----|
|&#42;.msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|Der Agent verwendet diese URLs für die Kommunikation mit dem Azure AD-Clouddienst. |
|`mscrl.microsoft.us:80` </br>`crl.microsoft.us:80` </br>`ocsp.msocsp.us:80` </br>`www.microsoft.us:80`| Der Agent verwendet diese URLs zum Überprüfen von Zertifikaten.|
|login.windows.us </br>secure.aadcdn.microsoftonline-p.com </br>&#42;.microsoftonline.us </br>&#42;.microsoftonline-p.us </br>&#42;.msauth.net </br>&#42;.msauthimages.net </br>&#42;.msecnd.net</br>&#42;.msftauth.net </br>&#42;.msftauthimages.net</br>&#42;.phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctldl.windowsupdate.us:80| Der Agent verwendet diese URLs während der Registrierung.

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Installieren des Agents für die Azure Government-Cloud

Führen Sie die folgenden Schritte aus, um den Agent für die Azure Government-Cloud zu installieren:

1. Navigieren Sie im Befehlszeilenterminal zu dem Ordner, der die ausführbare Datei enthält, mit der der Agent installiert wird.
1. Führen Sie die folgenden Befehle aus, die angeben, dass die Installation für Azure Government erfolgt.

   Für Pass-Through-Authentifizierung:

   ```
   AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
   ```

   Für den Anwendungsproxy:

   ```
   AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
   ```

## <a name="single-sign-on"></a>Einmaliges Anmelden

### <a name="set-up-your-azure-ad-connect-server"></a>Einrichten des Azure AD Connect-Servers

Wenn Sie Pass-Through-Authentifizierung als Anmeldemethode verwenden, ist keine zusätzliche Überprüfung der Voraussetzungen erforderlich. Wenn Ihre Anmeldemethode Kennworthashsynchronisierung ist und eine Firewall zwischen Azure AD Connect und Azure AD vorhanden ist, sollten Sie Folgendes sicherstellen:

- Sie verwenden Azure AD Connect, Version 1.1.644.0 oder höher.
- Wenn Ihre Firewall oder Ihr Proxy durch DNS blockierte oder sichere Programme zulässt, fügen Sie die Verbindungen den *.msapproxy.us-URLs über Port 443 hinzu.

  Aktivieren Sie andernfalls den Zugriff auf die IP-Adressbereiche für das Azure-Rechenzentrum, die wöchentlich aktualisiert werden. Diese Voraussetzung gilt nur, wenn Sie das Feature aktivieren. Sie ist für tatsächliche Benutzeranmeldungen nicht erforderlich.

### <a name="roll-out-seamless-single-sign-on"></a>Rollout von nahtlosem einmaligem Anmelden

Mithilfe der folgenden Anleitungen können Sie schrittweise nahtloses einmaliges Anmelden von Azure AD für Ihre Benutzer einführen. Sie fügen zuerst mithilfe der Gruppenrichtlinie in Active Directory den Intranetzoneneinstellungen aller oder ausgewählter Benutzer die Azure AD-URL `https://autologon.microsoft.us` hinzu.

Darüber hinaus müssen Sie die Intranetzonen-Gruppenrichtlinieneinstellung **Aktualisierungen der Statusleiste über Skript über Gruppenrichtlinie zulassen** aktivieren.

## <a name="browser-considerations"></a>Überlegungen zum Browser

### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (alle Plattformen)

Mozilla Firefox verwendet nicht automatisch die Kerberos-Authentifizierung. Jeder Benutzer muss den Firefox-Einstellungen manuell die Azure AD-URL mithilfe der folgenden Schritte hinzufügen:

1. Führen Sie Firefox aus, und geben Sie in die Adressleiste  **about:config**  ein. Schließen Sie alle Benachrichtigungen, die ggf. angezeigt werden.
1. Suchen Sie nach der Einstellung  **network.negotiate-auth.trusted-uris** . Diese Einstellung listet die Websites auf, denen Firefox für Kerberos-Authentifizierung vertraut.
1. Klicken Sie mit der rechten Maustaste auf den Namen der Einstellung, und wählen Sie dann  **Ändern** aus.
1. Geben Sie `https://autologon.microsoft.us` in das Feld ein.
1. Wählen Sie  **OK**  aus, und öffnen Sie den Browser dann erneut.

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge auf Chromium-Basis (alle Plattformen)

Wenn Sie die Richtlinieneinstellungen  `AuthNegotiateDelegateAllowlist`  oder `AuthServerAllowlist`  in Ihrer Umgebung außer Kraft gesetzt haben, stellen Sie sicher, dass Sie ihnen die Azure AD-URL (`https://autologon.microsoft.us`) hinzufügen.

### <a name="google-chrome-all-platforms"></a>Google Chrome (alle Plattformen)

Wenn Sie die Richtlinieneinstellungen  `AuthNegotiateDelegateWhitelist`  oder `AuthServerWhitelist`  in Ihrer Umgebung außer Kraft gesetzt haben, stellen Sie sicher, dass Sie ihnen die Azure AD-URL (`https://autologon.microsoft.us`) hinzufügen.

## <a name="next-steps"></a>Nächste Schritte

- [Passthrough-Authentifizierung](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [Einmaliges Anmelden](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)
