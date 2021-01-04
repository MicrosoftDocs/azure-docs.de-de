---
title: Bedingungen in Richtlinien für bedingten Zugriff – Azure Active Directory
description: Was sind Bedingungen in einer Richtlinie für bedingten Zugriff in Azure AD?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96d1eaff4d1b93ad3bb489f177020c351fe4d13d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95904034"
---
# <a name="conditional-access-conditions"></a>Bedingter Zugriff: Bedingungen

Ein Administrator kann in einer Richtlinie für bedingten Zugriff Signale von Bedingungen wie Risiko, Geräteplattform oder Standort verwenden, um die Richtlinienentscheidungen zu verbessern. 

[ ![Definieren einer Richtlinie für bedingten Zugriff und Angeben von Bedingungen](./media/concept-conditional-access-conditions/conditional-access-conditions.png)](./media/concept-conditional-access-conditions/conditional-access-conditions.png#lightbox)

Es können mehrere Bedingungen kombiniert werden, um differenzierte und spezifische Richtlinien für bedingten Zugriff zu erstellen.

Beispielsweise kann ein Administrator beim Zugriff auf eine sensible Anwendung neben anderen Kontrollmechanismen wie mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) auch die Anmelderisikoinformationen von Identity Protection und den Standort in die Entscheidung für den Zugriff einbeziehen.

## <a name="sign-in-risk"></a>Anmelderisiko

Für Kunden mit Zugriff auf [Identity Protection](../identity-protection/overview-identity-protection.md) kann das Anmelderisiko im Rahmen einer Richtlinie für bedingten Zugriff ausgewertet werden. Ein Anmelderisiko ist die Möglichkeit, dass eine bestimmte Authentifizierungsanforderung vom Identitätsbesitzer nicht autorisiert wurde. Weitere Informationen zu Anmelderisiken finden Sie in den Artikeln [Was bedeutet Risiko?](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) und [Anleitung: Konfigurieren und Aktivieren von Risikorichtlinien](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="user-risk"></a>Benutzerrisiko 

Für Kunden mit Zugriff auf [Identity Protection](../identity-protection/overview-identity-protection.md) kann das Benutzerrisiko im Rahmen einer Richtlinie für bedingten Zugriff ausgewertet werden. Ein Benutzerrisiko stellt die Wahrscheinlichkeit dar, dass eine bestimmte Identität oder ein bestimmtes Konto kompromittiert wird. Weitere Informationen zum Benutzerrisiko finden Sie in den Artikeln [Was bedeutet Risiko?](../identity-protection/concept-identity-protection-risks.md#user-risk) und [Anleitung: Konfigurieren und Aktivieren von Risikorichtlinien](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="device-platforms"></a>Geräteplattformen

Die Geräteplattform ist durch das Betriebssystem gekennzeichnet, das auf dem Gerät ausgeführt wird. Azure AD identifiziert die Plattform mithilfe der vom Gerät bereitgestellten Informationen, wie z. B. Benutzer-Agent-Zeichenfolgen. Da Benutzer-Agent-Zeichenfolgen geändert werden können, werden diese Informationen nicht überprüft. Die Geräteplattform sollte zusammen mit Microsoft Intune-Richtlinien zur Gerätekonformität oder als Teil einer Blockierungsanweisung verwendet werden. Standardmäßig werden Richtlinien auf alle Geräteplattformen angewendet.

Für den bedingten Azure AD-Zugriff werden folgende Geräteplattformen unterstützt:

- Android
- iOS
- Windows Phone
- Windows
- macOS

Wenn Sie die ältere Authentifizierung mit der Bedingung **„Andere Clients“** blockieren, können Sie auch die Geräteplattform als Bedingung festlegen.

> [!IMPORTANT]
> Microsoft empfiehlt, dass Sie eine Richtlinie für bedingten Zugriff für nicht unterstützte Geräteplattformen haben. Wenn Sie beispielsweise den Zugriff auf Ihre Unternehmensressourcen aus Linux oder anderen nicht unterstützten Clients heraus blockieren möchten, sollten Sie eine Richtlinie mit einer Bedingung für Geräteplattformen konfigurieren, die ein beliebiges Gerät einschließt, unterstützte Geräteplattformen ausschließt und das Gewährungssteuerelement zum Blockieren des Zugriffs festlegt.

## <a name="locations"></a>Standorte

Wenn der Standort als Bedingung konfiguriert wird, können Organisationen auswählen, ob Standorte ein- oder ausgeschlossen werden sollen. Diese benannten Standorte können die öffentlichen IPv4-Netzwerkinformationen, das Land oder die Region oder sogar unbekannte Bereiche umfassen, die nicht bestimmten Ländern oder Regionen zugeordnet sind. Nur IP-Bereiche können als vertrauenswürdiger Standort gekennzeichnet werden.

Wenn Sie **alle Standorte** einschließen, umfasst diese Option alle IP-Adressen im Internet und nicht nur konfigurierte benannte Standorte. Bei der Auswahl von **Alle Standorte** können Administratoren auswählen, ob **alle vertrauenswürdigen** oder **ausgewählte Standorte** ausgeschlossen werden sollen.

Beispielsweise können einige Organisationen entscheiden, dass keine mehrstufige Authentifizierung erforderlich ist, wenn ihre Benutzer mit dem Netzwerk an einem vertrauenswürdigen Standort wie dem physischen Hauptsitz verbunden sind. Administratoren können eine Richtlinie erstellen, die einen beliebigen Standort einschließt, aber die ausgewählten Standorte für ihre Netzwerke am Hauptsitz ausschließt.

Im Artikel [Was sind Standortbedingungen beim bedingten Zugriff in Azure Active Directory?](location-condition.md) finden Sie weitere Informationen zu Standorten.

## <a name="client-apps"></a>Client-Apps

Alle neu erstellten Richtlinien für bedingten Zugriff gelten standardmäßig für alle Client-App-Typen, auch wenn die Client-Apps-Bedingung nicht konfiguriert ist. 

> [!NOTE]
> Das Verhalten der Client-Apps-Bedingung wurde im August 2020 aktualisiert. Vorhandene Richtlinien für bedingten Zugriff bleiben unverändert erhalten. Wenn Sie jedoch auf eine vorhandene Richtlinie klicken, sehen Sie, dass die Umschaltfläche „Konfigurieren“ nicht vorhanden ist und die Client-Apps, für die die Richtlinie gilt, ausgewählt sind.

> [!IMPORTANT]
> Bei Anmeldungen von Clients mit Legacyauthentifizierung wird die mehrstufige Authentifizierung (MFA) nicht unterstützt, und es werden keine Gerätestatusinformationen an Azure AD übergeben. Die Anmeldungen werden daher durch den bedingten Zugriff mit seinen Gewährungssteuerelementen (z. B. MFA oder kompatible Geräte erforderlich ) blockiert. Wenn Sie Konten haben, für die die Legacyauthentifizierung verwendet werden muss, müssen Sie diese Konten entweder aus der Richtlinie ausschließen oder die Richtlinie so konfigurieren, dass sie nur für moderne Authentifizierungsclients gilt.

Wenn die Umschaltfläche **Konfigurieren** auf **Ja** festgelegt ist, gilt sie für markierte Elemente. Wenn sie auf **Nein** eingestellt ist, gilt sie für alle Client-Apps, einschließlich Clients mit moderner und Legacyauthentifizierung. Diese Umschaltfläche ist in Richtlinien, die vor August 2020 erstellt wurden, nicht enthalten.

- Clients mit moderner Authentifizierung
   - Browser
      - Hierzu gehören webbasierte Anwendungen, die Protokolle wie SAML, WS-Verbund, OpenID Connect oder Dienste verwenden, die als vertraulicher OAuth-Client registriert sind.
   - Mobile Apps und Desktop-Apps
      -  Diese Option umfasst Anwendungen wie Office-Desktop- und Telefonanwendungen.
- Clients mit Legacyauthentifizierung
   - Exchange ActiveSync-Clients
      - Umfasst die gesamte Verwendung des Exchange ActiveSync (EAS)-Protokolls.
      - Wenn die Verwendung von Exchange ActiveSync durch eine Richtlinie blockiert wird, erhält der betroffene Benutzer eine einzige Quarantäne-E-Mail. Diese E-Mail enthält Informationen zum Grund für die Blockierung und gegebenenfalls Korrekturanweisungen.
      - Administratoren können die Richtlinie über den bedingten Zugriff der Microsoft Graph-API nur auf unterstützte Plattformen (z. B. iOS, Android und Windows) anwenden.
   - Andere Clients
      - Diese Option umfasst Clients, die Standard-/Legacyauthentifizierungsprotokolle verwenden, die keine moderne Authentifizierung unterstützen.
         - Authentifiziertes SMTP: Wird von POP- und IMAP-Clients zum Senden von E-Mail-Nachrichten verwendet.
         - AutoErmittlung: wird von Outlook und EAS-Clients verwendet, um Postfächer in Exchange Online zu suchen und diese zu verbinden
         - Exchange Online PowerShell: wird zum Herstellen einer Verbindung mit Exchange Online über Remote-PowerShell verwendet Wenn Sie die Standardauthentifizierung für Exchange Online PowerShell blockieren, müssen Sie das Exchange Online PowerShell-Modul verwenden, um eine Verbindung herzustellen. Anweisungen finden Sie unter [Herstellen einer Verbindung mit Exchange Online PowerShell mithilfe der mehrstufigen Authentifizierung](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
         - Exchange Web Services (EWS): eine Programmierschnittstelle, die von Outlook, Outlook für Mac und Drittanbieter-Apps verwendet wird
         - IMAP4: wird von IMAP-E-Mail-Clients verwendet
         - MAPI über HTTP (MAPI/HTTP): wird von Outlook 2010 und höher verwendet
         - Offlineadressbuch (OAB): eine Kopie der Adressenlistensammlungen, die von Outlook heruntergeladen und verwendet werden
         - Outlook Anywhere (RPC über HTTP): wird von Outlook 2016 und früher verwendet
         - Outlook-Dienst: wird von der Mail- und Kalender-App für Windows 10 verwendet
         - POP3: wird von POP-E-Mail-Clients verwendet
         - Berichtswebdienste: Werden zum Abrufen von Berichtsdaten in Exchange Online verwendet.

Diese Bedingungen werden häufig verwendet, wenn ein verwaltetes Gerät erforderlich ist, Legacyauthentifizierung blockiert wird und Webanwendungen blockiert werden, aber mobile oder Desktop-Apps zulässig sind.

### <a name="supported-browsers"></a>Unterstützte Browser

Diese Einstellung funktioniert mit allen Browsern. Die folgenden Betriebssysteme und Browser werden jedoch unterstützt, um eine Geräterichtlinie, z. B. eine konforme Geräteanforderung, zu erfüllen:

| OS | Browser |
| :-- | :-- |
| Windows 10 | Microsoft Edge, Internet Explorer, Chrome |
| Windows 8/8.1 | Internet Explorer, Chrome |
| Windows 7 | Internet Explorer, Chrome |
| iOS | Microsoft Edge, Intune Managed Browser, Safari |
| Android | Microsoft Edge, Intune Managed Browser, Chrome |
| Windows Phone | Microsoft Edge, Internet Explorer |
| Windows Server 2019 | Microsoft Edge, Internet Explorer, Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome, Safari |

> [!NOTE]
> Bei Microsoft Edge 85+ muss der Benutzer beim Browser angemeldet sein, um die Geräteidentität ordnungsgemäß zu übergeben. Andernfalls ist das Verhalten vergleichbar mit Chrome ohne Kontoerweiterung. Diese Anmeldung erfolgt in einem Azure AD Hybrid Join-Szenario möglicherweise nicht automatisch. 

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Warum wird im Browser eine Aufforderung zur Clientzertifikatauswahl angezeigt?

Unter Windows 7, iOS, Android und macOS identifiziert Azure AD das Gerät anhand eines Clientzertifikats, das beim Registrieren des Geräts bei Azure AD bereitgestellt wird.  Wenn sich ein Benutzer zum ersten Mal über den Browser anmeldet, wird er zum Auswählen des Zertifikats aufgefordert. Der Benutzer muss dieses Zertifikat vor dem Verwenden des Browsers auswählen.

#### <a name="chrome-support"></a>Chrome-Unterstützung

Installieren Sie die [Erweiterung für Windows 10-Konten](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji), damit Chrome ab **Windows 10 Creators Update (Version 1703)** unterstützt wird. Diese Erweiterung ist erforderlich, wenn eine Richtlinie für bedingten Zugriff gerätespezifische Details erfordert.

Um diese Erweiterung für Chrome-Browser automatisch bereitzustellen, erstellen Sie den folgenden Registrierungsschlüssel:

- Path HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist
- Name 1
- Type REG_SZ (Zeichenfolge)
- Data ppnbnpeolgkicgegkbkbjmhlideopiji;https\://clients2.google.com/service/update2/crx

Erstellen Sie den folgenden Registrierungsschlüssel, damit Chrome unter **Windows 8.1 und 7** unterstützt wird:

- Path HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls
- Name 1
- Type REG_SZ (Zeichenfolge)
- Data {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}}

Diese Browser unterstützen die Geräteauthentifizierung, sodass das Gerät identifiziert und anhand einer Richtlinie überprüft werden kann. Bei der Geräteüberprüfung tritt ein Fehler auf, wenn der Browser im privaten Modus ausgeführt wird.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Unterstützte mobile Anwendungen und Desktopclients

Organisationen können **Mobile Apps und Desktopclients** als Client-App auswählen.

Diese Einstellung hat Auswirkungen auf Zugriffsversuche von den folgenden mobilen Apps und Desktopclients:

| Client-Apps | Zieldienst | Plattform |
| --- | --- | --- |
| Dynamics CRM-App | Dynamics CRM | Windows 10, Windows 8.1, iOS und Android |
| E-Mail-/Kalender-/Kontakte-App, Outlook 2016, Outlook 2013 (mit moderner Authentifizierung)| Exchange Online | Windows 10 |
| MFA- und Standort-Richtlinien für Apps Gerätebasierte Richtlinien werden nicht unterstützt.| Alle Meine Apps-App-Dienste | Android und iOS |
| Microsoft Teams-Dienste: steuert alle Dienste, die Microsoft-Teams unterstützen, und alle dazugehörigen Client-Apps – Windows Desktop, iOS, Android, WP und Webclient | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android und macOS |
| Office 2016-Apps, Office 2013 (mit moderner Authentifizierung), [OneDrive-Synchronisierungsclient](/onedrive/enable-conditional-access) | SharePoint | Windows 8.1, Windows 7 |
| Office 2016-Apps, universelle Office-Apps, Office 2013 (mit moderner Authentifizierung), [OneDrive-Synchronisierungsclient](/onedrive/enable-conditional-access) | SharePoint Online | Windows 10 |
| Office 2016 (nur Word, Excel, PowerPoint und OneNote). | SharePoint | macOS |
| Office 2019| SharePoint | Windows 10, macOS |
| Office Mobile-Apps | SharePoint | Android, iOS |
| Office Yammer-App | Yammer | Windows 10, iOS und Android |
| Outlook 2019 | SharePoint | Windows 10, macOS |
| Outlook 2016 (Office für macOS) | Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (mit moderner Authentifizierung), Skype for Business (mit moderner Authentifizierung) | Exchange Online | Windows 8.1, Windows 7 |
| Outlook Mobile-App | Exchange Online | Android, iOS |
| Power BI-App | Power BI-Dienst | Windows 10, Windows 8.1, Windows 7, Android und iOS |
| Skype for Business | Exchange Online| Android, iOS |
| Visual Studio Team Services-App | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS, Android |

### <a name="exchange-activesync-clients"></a>Exchange ActiveSync-Clients

- Organisationen können Exchange ActiveSync-Clients nur auswählen, wenn sie Benutzern oder Gruppen Richtlinien zuweisen. Bei der Auswahl von **Alle Benutzer**, **Alle Gast- und externen Benutzer** oder **Verzeichnisrollen** werden alle Benutzer blockiert.
- Wenn Sie eine Richtlinie erstellen, die Exchange ActiveSync-Clients zugewiesen ist, sollte **Exchange Online** der Richtlinie als einzige Cloudanwendung zugewiesen sein. 
- Organisationen können den Umfang dieser Richtlinie auf bestimmte Plattformen beschränken, indem sie die Bedingung **Geräteplattformen** verwenden.

Wenn die der Richtlinie zugewiesene Zugriffssteuerung die Option **Genehmigte Client-App erforderlich** verwendet, wird der Benutzer angewiesen, den Outlook Mobile-Client zu installieren und zu verwenden. Wenn die **mehrstufige Authentifizierung** erforderlich ist, werden betroffene Benutzer blockiert, weil die Standardauthentifizierung keine mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) unterstützt.

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Blockieren der Legacyauthentifizierung mit bedingtem Zugriff](block-legacy-authentication.md)
- [Vorschreiben der Verwendung von genehmigten Client-Apps für den Zugriff auf Cloud-Apps mithilfe des bedingten Zugriffs](app-based-conditional-access.md)

### <a name="other-clients"></a>Andere Clients

Wenn Sie **Andere Clients** auswählen, können Sie eine Bedingung für Apps mit Standardauthentifizierung über E-Mail-Protokolle wie IMAP, MAPI, POP oder SMTP sowie für ältere Office-Apps angeben, die keine moderne Authentifizierung verwenden.

## <a name="device-state-preview"></a>Gerätezustand (Vorschau)

Mit der Bedingung „Gerätezustand“ können in Hybrid-Azure AD eingebundene Geräte und/oder Geräte ausgeschlossen werden, die mit einer Microsoft Intune-Compliancerichtlinie aus den Richtlinien für bedingten Zugriff einer Organisation als konform markiert sind.

Beispiel: *Alle Benutzer*, die auf die Cloud-App für die *Microsoft Azure-Verwaltung* zugreifen, einschließlich **Alle Gerätezustände**, ausgenommen **Gerät in Hybrid-Azure AD eingebunden** und **Gerät als konform markiert** und für *Zugriffssteuerungen* die Option **Blockieren**. 
   - In diesem Beispiel würde eine Richtlinie erstellt, die nur den Zugriff auf die Microsoft Azure-Verwaltung von Geräten zulässt, die in Hybrid-Azure AD eingebunden oder als konform markiert sind.

## <a name="next-steps"></a>Nächste Schritte

- [Bedingter Zugriff: Gewähren](concept-conditional-access-grant.md)

- [Allgemeine Richtlinien für bedingten Zugriff](concept-conditional-access-policy-common.md)
