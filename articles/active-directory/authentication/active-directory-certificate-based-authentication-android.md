---
title: 'Azure Active Directory: Zertifikatbasierte Authentifizierung für Android'
description: In diesem Thema lernen Sie unterstützte Szenarien sowie die Voraussetzungen für die Konfiguration der zertifikatbasierten Authentifizierung in Lösungen mit Android-Geräten kennen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1001d5524fe99783cda4d5b77bdaceacc6791848
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861372"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Zertifikatbasierte Authentifizierung mit Azure Active Directory unter Android

Android-Geräte können für die Authentifizierung bei Azure Active Directory die zertifikatbasierte Authentifizierung verwenden, indem auf den Geräten beim Herstellen einer Verbindung mit folgenden Anwendungen oder Clients ein Clientzertifikat verwendet wird:

* Mobile Office-Anwendungen wie Microsoft Outlook und Microsoft Word
* Exchange ActiveSync-Clients (EAS)

Wenn Sie dieses Feature konfigurieren, ist es bei bestimmten E-Mail- und Microsoft Office-Anwendungen auf Ihrem mobilen Gerät nicht länger erforderlich, einen Benutzernamen und ein Kennwort einzugeben.

Dieses Thema behandelt die Voraussetzungen und die unterstützten Szenarien für die Konfiguration der zertifikatbasierten Authentifizierung auf Android-Geräten für Mandantenbenutzer mit Office 365-Plänen für Enterprise, Business, Education, US Government, China und Deutschland.

Dieses Feature ist als Vorversion in Office 365 US Government Defense- und Federal-Plänen verfügbar.

## <a name="microsoft-mobile-applications-support"></a>Unterstützung mobiler Microsoft-Anwendungen

| Apps | Support |
| --- | --- |
| Azure Information Protection-App |![Häkchen, das die Unterstützung für diese Anwendung angibt][1] |
| Intune-Unternehmensportal |![Häkchen, das die Unterstützung für diese Anwendung angibt][1] |
| Microsoft Teams |![Häkchen, das die Unterstützung für diese Anwendung angibt][1] |
| OneNote |![Häkchen, das die Unterstützung für diese Anwendung angibt][1] |
| OneDrive |![Häkchen, das die Unterstützung für diese Anwendung angibt][1] |
| Outlook |![Häkchen, das die Unterstützung für diese Anwendung angibt][1] |
| Power BI |![Häkchen, das die Unterstützung für diese Anwendung angibt][1] |
| Skype for Business |![Häkchen, das die Unterstützung für diese Anwendung angibt][1] |
| Word/Excel/PowerPoint |![Häkchen, das die Unterstützung für diese Anwendung angibt][1] |
| Yammer |![Häkchen, das die Unterstützung für diese Anwendung angibt][1] |

### <a name="implementation-requirements"></a>Anforderungen an die Implementierung

Die Betriebssystemversion des Geräts muss Android 5.0 (Lollipop) oder eine höhere Version sein.

Ein Verbundserver muss konfiguriert werden.

Damit Azure Active Directory ein Clientzertifikat sperren kann, muss das AD FS-Token die folgenden Ansprüche enthalten:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (Die Seriennummer des Clientzertifikats)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (Die Zeichenfolge für den Aussteller des Clientzertifikats)

Wenn diese Ansprüche im AD FS-Token (oder in einem anderen SAML-Token) enthalten sind, fügt Azure Active Directory die Ansprüche dem Aktualisierungstoken hinzu. Wenn das Aktualisierungstoken überprüft werden muss, werden diese Informationen zum Überprüfen der Sperrung verwendet.

Als bewährte Methode sollten Sie die AD FS-Fehlerseiten Ihrer Organisation mit folgenden Informationen aktualisieren:

* Voraussetzungen für die Installation von Microsoft Authenticator unter Android
* Anleitungen zum Abrufen eines Benutzerzertifikats

Weitere Informationen finden Sie unter [Anpassen der AD FS-Anmeldeseiten](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11)).

Einige Office-Apps (mit aktivierter moderner Authentifizierung) senden *prompt=login* in der Anforderung an Azure AD. Azure AD übersetzt *prompt=login* in der Anforderung an AD FS standardmäßig in *wauth=usernamepassworduri* (fordert ADFS zum Durchführen der U/P-Authentifizierung auf) und *wfresh=0* (fordert ADFS auf, den SSO-Status zu ignorieren und eine erneute Authentifizierung durchzuführen). Wenn Sie eine zertifikatbasierte Authentifizierung für diese Apps aktivieren möchten, müssen Sie das Azure AD-Standardverhalten ändern. Legen Sie dazu einfach '*PromptLoginBehavior*' in den Einstellungen der Verbunddomäne auf '*Deaktiviert*' fest.
Für diese Aufgabe können Sie das Cmdlet [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings) verwenden:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Unterstützung von Exchange ActiveSync-Clients

Bestimmte Exchange ActiveSync-Anwendungen auf Android 5.0 (Lollipop) oder einer höheren Version werden unterstützt. Wenden Sie sich an Ihren Anwendungsentwickler, um herauszufinden, ob Ihre E-Mail-Anwendung dieses Feature unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie die zertifikatbasierte Authentifizierung in Ihrer Umgebung konfigurieren möchten, finden Sie unter [Erste Schritte mit der zertifikatbasierten Authentifizierung unter Android](active-directory-certificate-based-authentication-get-started.md) entsprechende Anweisungen.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
