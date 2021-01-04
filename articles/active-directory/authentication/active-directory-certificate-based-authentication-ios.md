---
title: Zertifikatbasierte Authentifizierung unter iOS – Azure Active Directory
description: In diesem Thema lernen Sie unterstützte Szenarien sowie die Voraussetzungen für die Konfiguration der zertifikatbasierten Authentifizierung für Azure Active Directory in Lösungen mit iOS-Geräten kennen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5f9b96fe9ee0781803bbbd86316e8783b60a6f1
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861322"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Zertifikatbasierte Authentifizierung mit Azure Active Directory unter iOS

Zur Steigerung der Sicherheit können iOS-Geräte für die Authentifizierung bei Azure Active Directory (Azure AD) die zertifikatbasierte Authentifizierung verwenden, indem auf den Geräten beim Herstellen einer Verbindung mit folgenden Anwendungen oder Diensten ein Clientzertifikat verwendet wird:

* Mobile Office-Anwendungen wie Microsoft Outlook und Microsoft Word
* Exchange ActiveSync-Clients (EAS)

Bei Verwendung von Zertifikaten ist es bei bestimmten E-Mail- und Microsoft Office-Anwendungen auf Ihrem mobilen Gerät nicht mehr erforderlich, einen Benutzernamen und ein Kennwort einzugeben.

In diesem Artikel werden die Anforderungen und die unterstützten Szenarien für die Konfiguration der zertifikatbasierten Authentifizierung auf einem iOS-Gerät erläutert. Die zertifikatbasierte Authentifizierung für iOS steht in den öffentlichen Azure-Clouds, der Microsoft Government-Cloud, der Microsoft Cloud Deutschland und Microsoft Azure China 21ViaNet zur Verfügung.

## <a name="microsoft-mobile-applications-support"></a>Unterstützung mobiler Microsoft-Anwendungen

| Apps | Support |
| --- | --- |
| Azure Information Protection-App |![Häkchen, das die Unterstützung für diese Anwendung angibt][1] |
| Intune-Unternehmensportal |![Häkchen, das die Unterstützung für diese Anwendung angibt][1] |
| Microsoft Teams |![Häkchen, das die Unterstützung für diese Anwendung angibt][1] |
| Office (Mobil) |![Häkchen, das die Unterstützung für diese Anwendung angibt][1] |
| OneNote |![Häkchen, das die Unterstützung für diese Anwendung angibt][1] |
| OneDrive |![Häkchen, das die Unterstützung für diese Anwendung angibt][1] |
| Outlook |![Häkchen, das die Unterstützung für diese Anwendung angibt][1] |
| Power BI |![Häkchen, das die Unterstützung für diese Anwendung angibt][1] |
| Skype for Business |![Häkchen, das die Unterstützung für diese Anwendung angibt][1] |
| Word/Excel/PowerPoint |![Häkchen, das die Unterstützung für diese Anwendung angibt][1] |
| Yammer |![Häkchen, das die Unterstützung für diese Anwendung angibt][1] |

## <a name="requirements"></a>Requirements (Anforderungen)

Beachten Sie bei der Verwendung der zertifikatbasierten Authentifizierung in iOS die folgenden Voraussetzungen und Überlegungen:

* Die Betriebssystemversion des Geräts muss mindestens iOS 9 sein.
* Für Office-Anwendungen unter iOS ist Microsoft Authenticator erforderlich.
* Es muss eine Identitätseinstellung in der macOS-Keychain erstellt werden, die die Authentifizierungs-URL des AD FS-Servers enthält. Weitere Informationen finden Sie unter [Erstellen einer Identitätseinstellung in der App „Schlüsselbundverwaltung“ auf dem Mac](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac).

Die folgenden Anforderungen und Überlegungen gelten für Active Directory-Verbunddienste (AD FS):

* Der AD FS-Server muss für die Zertifikatauthentifizierung aktiviert sein und die Verbundauthentifizierung verwenden.
* Das Zertifikat muss die erweiterte Schlüsselverwendung (Enhanced Key Usage, EKU) verwenden und den Benutzerprinzipalnamen (UPN) des Benutzers im *alternativen Antragstellernamen (NT-Prinzipalname)* enthalten.

## <a name="configure-adfs"></a>Konfigurieren von ADFS

Damit Azure AD ein Clientzertifikat widerrufen kann, muss das AD FS-Token die folgenden Ansprüche enthalten. Wenn diese Ansprüche im AD FS-Token (oder in einem anderen SAML-Token) enthalten sind, fügt Azure AD die Ansprüche dem Aktualisierungstoken hinzu. Wenn das Aktualisierungstoken überprüft werden muss, werden diese Informationen zum Überprüfen der Sperrung verwendet:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`: Fügen Sie die Seriennummer Ihres Clientzertifikats hinzu.
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`: Fügen Sie die Zeichenfolge für den Aussteller des Clientzertifikats hinzu.

Als bewährte Methode sollten Sie auch die AD FS-Fehlerseiten Ihrer Organisation mit folgenden Informationen aktualisieren:

* Voraussetzungen für die Installation von Microsoft Authenticator unter iOS
* Anleitungen zum Abrufen eines Benutzerzertifikats

Weitere Informationen finden Sie unter [Anpassen der AD FS-Anmeldeseite](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11)).

## <a name="use-modern-authentication-with-office-apps"></a>Verwenden der modernen Authentifizierung mit Office-Apps

Einige Office-Apps mit aktivierter moderner Authentifizierung senden `prompt=login` in der Anforderung an Azure AD. Azure AD übersetzt `prompt=login` in der Anforderung standardmäßig in `wauth=usernamepassworduri` (fordert AD FS zum Durchführen der U/P-Authentifizierung auf) und `wfresh=0` (fordert AD FS auf, den SSO-Status zu ignorieren und eine erneute Authentifizierung durchzuführen). Wenn Sie die zertifikatbasierte Authentifizierung für diese Apps aktivieren möchten, ändern Sie das Azure AD-Standardverhalten.

Legen Sie zum Anpassen des Standardverhaltens in den Einstellungen der Verbunddomäne *PromptLoginBehavior* auf *Disabled* fest. Für diese Aufgabe können Sie das Cmdlet [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings) verwenden, wie im folgenden Beispiel gezeigt:

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Unterstützung von Exchange ActiveSync-Clients

Unter iOS 9 oder höher wird der native iOS-E-Mail-Client unterstützt. Wenden Sie sich bei allen anderen Exchange ActiveSync-Anwendungen an den Anwendungsentwickler, um zu erfragen, ob dieses Feature unterstützt wird.

## <a name="next-steps"></a>Nächste Schritte

Anweisungen zum Konfigurieren der zertifikatbasierten Authentifizierung in Ihrer Umgebung finden Sie unter [Erste Schritte mit der zertifikatbasierten Authentifizierung](active-directory-certificate-based-authentication-get-started.md).

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
