---
title: Referenz zu den Einstellungen für den bedingten Azure Active Directory-Zugriff | Microsoft-Dokumentation
description: Verschaffen Sie sich einen Überblick über die unterstützten Einstellungen in einer Richtlinie für den bedingten Azure Active Directory-Zugriff.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/22/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: fae09b5aeed05dfb09f2b998de805ef0607e7f39
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65823550"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Referenz zu den Einstellungen für den bedingten Azure Active Directory-Zugriff

Sie können den [bedingten Zugriff von Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md) verwenden, um zu steuern, wie autorisierte Benutzer auf Ihre Ressourcen zugreifen können.

Dieser Artikel enthält unterstützende Informationen zu den folgenden Konfigurationsoptionen in einer Richtlinie für den bedingten Zugriff:

- Zuweisungen von Cloudanwendungen
- Geräteplattformbedingung
- Bedingung für Clientanwendungen
- Genehmigte Clientanwendungsanforderung

Falls Sie andere Informationen benötigen, hinterlassen Sie einen Kommentar am Ende dieses Artikels.

## <a name="cloud-apps-assignments"></a>Zuweisungen von Cloud-Apps

Mit Richtlinien für bedingten Zugriff steuern Sie, wie Ihre Benutzer auf [Cloud-Apps](conditions.md#cloud-apps-and-actions) zugreifen. Wenn Sie eine Richtlinie für bedingten Zugriff konfigurieren, müssen Sie mindestens eine Cloud-App auswählen. 

![Auswählen der Cloud-Apps für Ihre Richtlinie](./media/technical-reference/09.png)

### <a name="microsoft-cloud-applications"></a>Microsoft Cloud-Anwendungen

Sie können folgenden Cloud-Apps von Microsoft eine Richtlinie für bedingten Zugriff zuweisen:

- Azure Analysis Services
- Azure DevOps
- Azure SQL-Datenbank und Data Warehouse – [Weitere Informationen](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- Microsoft Azure Information Protection – [Weitere Informationen](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Microsoft Azure-Verwaltung – [Weitere Informationen](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- Microsoft Azure-Abonnementverwaltung
- Microsoft Cloud App Security
- Portal für die Zugriffssteuerung auf Microsoft Commerce-Tools
- Authentifizierungsdienst für Microsoft Commerce-Tools
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- Microsoft Intune-Registrierung
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerApps
- Microsoft-Suche in Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office Sway
- Outlook-Gruppen
- Project Online
- Skype for Business Online
- Virtuelles privates Netzwerk (VPN):
- Visual Studio App Center
- Windows Defender ATP

### <a name="other-applications"></a>Andere Anwendungen

Außer den Cloud-Apps von Microsoft können Sie den folgenden Typen von Cloud-Apps eine Richtlinie für bedingten Zugriff zuweisen:

- Anwendungen mit Azure AD-Verbindung
- Vorab integrierte verbundene Software-as-a-Service-Anwendung (SaaS)
- Anwendungen, die einmaliges Anmelden mit Kennwort (SSO) verwenden
- Branchenanwendungen
- Anwendungen, die den Azure AD-Anwendungsproxy verwenden

## <a name="device-platform-condition"></a>Geräteplattformbedingung

In einer Richtlinie für bedingten Zugriff können Sie die Geräteplattformbedingung konfigurieren, um die Richtlinie an das Betriebssystem auf einem Client zu binden. Für den bedingten Azure AD-Zugriff werden folgende Geräteplattformen unterstützt:

- Android
- iOS
- Windows Phone
- Windows
- macOS

![Binden der Zugriffsrichtlinie an das Clientbetriebssystem](./media/technical-reference/41.png)

## <a name="client-apps-condition"></a>Client-Apps-Bedingung

In der Richtlinie für bedingten Zugriff können Sie die [Client-Apps](conditions.md#client-apps)-Bedingung konfigurieren, um die Richtlinie mit der Client-App zu verknüpfen, die einen Zugriffsversuch initiiert hat. Legen Sie die Client-Apps-Bedingung fest, um den Zugriff zu gewähren oder zu verweigern, wenn mit den folgenden Arten von Client-Apps ein Zugriffsversuch unternommen wird:

- "Browser"
- Mobile Apps und Desktop-Apps

![Steuern des Zugriffs für Client-Apps](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>Unterstützte Browser

In der Richtlinie für bedingten Zugriff können Sie als Client-App **Browser** auswählen.

![Steuern des Zugriffs für unterstützte Browser](./media/technical-reference/05.png)

Diese Einstellung funktioniert mit allen Browsern. Die folgenden Betriebssysteme und Browser werden jedoch unterstützt, um eine Geräterichtlinie, z. B. eine konforme Geräteanforderung, zu erfüllen:

| Betriebssystem                     | Browser                                      |
| :--                    | :--                                           |
| Windows 10             | Internet Explorer, Microsoft Edge, Chrome     |
| Windows 8/8.1        | Internet Explorer, Chrome                     |
| Windows 7              | Internet Explorer, Chrome                     |
| iOS                    | Safari, Microsoft Edge, Intune Managed Browser |
| Android                | Chrome, Microsoft Edge, Intune Managed Browser |
| Windows Phone          | Internet Explorer, Microsoft Edge             |
| Windows Server 2016    | Internet Explorer, Microsoft Edge             |
| Windows Server 2016    | Chrome                                        |
| Windows Server 2012 R2 | Internet Explorer, Chrome                     |
| Windows Server 2008 R2 | Internet Explorer, Chrome                     |
| macOS                  | Chrome, Safari                                |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Warum wird im Browser eine Aufforderung zur Clientzertifikatauswahl angezeigt?

Unter Windows 7, iOS, Android und macOS identifiziert Azure AD das Gerät anhand eines Clientzertifikats, das beim Registrieren des Geräts bei Azure AD bereitgestellt wird.  Wenn sich ein Benutzer zum ersten Mal über den Browser anmeldet, wird er zum Auswählen des Zertifikats aufgefordert. Der Benutzer muss dieses Zertifikat vor dem Verwenden des Browsers auswählen.

#### <a name="chrome-support"></a>Chrome-Unterstützung

Installieren Sie [diese Erweiterung](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji), damit Chrome unter **Windows 10 Creators Update (Version 1703**) oder höher unterstützt wird.

Um diese Erweiterung für Chrome-Browser automatisch bereitzustellen, erstellen Sie den folgenden Registrierungsschlüssel:

|    |    |
| --- | --- |
| `Path` | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| NAME | 1 |
| Type | REG_SZ (Zeichenfolge) |
| Daten | ppnbnpeolgkicgegkbkbjmhlideopiji; https://clients2.google.com/service/update2/crx |

Erstellen Sie den folgenden Registrierungsschlüssel, damit Chrome unter **Windows 8.1 und 7** unterstützt wird:

|    |    |
| --- | --- |
| `Path` | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| NAME | 1 |
| Type | REG_SZ (Zeichenfolge) |
| Daten | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

Diese Browser unterstützen die Geräteauthentifizierung, sodass das Gerät identifiziert und anhand einer Richtlinie überprüft werden kann. Bei der Geräteüberprüfung tritt ein Fehler auf, wenn der Browser im privaten Modus ausgeführt wird.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Unterstützte mobile Anwendungen und Desktopclients

In der Richtlinie für bedingten Zugriff können Sie als Client-App **Mobile Apps und Desktopclients** auswählen.

![Steuern des Zugriffs für unterstützte mobile Apps oder Desktopclients](./media/technical-reference/06.png)

Diese Einstellung hat Auswirkungen auf Zugriffsversuche von den folgenden mobilen Apps und Desktopclients:

| Client-Apps | Zieldienst | Plattform |
| --- | --- | --- |
| Dynamics CRM-App | Dynamics CRM | Windows 10, Windows 8.1, iOS und Android |
| E-Mail-/Kalender-/Kontakte-App, Outlook 2016, Outlook 2013 (mit moderner Authentifizierung)| Microsoft Office 365 Exchange Online | Windows 10 |
| MFA- und Standort-Richtlinien für Apps Gerätebasierte Richtlinien werden nicht unterstützt.| Alle Meine Apps-App-Dienste| Android und iOS |
| Microsoft Teams-Dienste: steuert alle Dienste, die Microsoft-Teams unterstützen, und alle dazugehörigen Client-Apps – Windows Desktop, iOS, Android, WP und Webclient | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android und macOS |
| Office 2016-Apps, Office 2013 (mit moderner Authentifizierung), OneDrive-Synchronisierungsclient (siehe [Hinweise](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Office 2016-Apps, universelle Office-Apps, Office 2013 (mit moderner Authentifizierung), OneDrive-Synchronisierungsclient (siehe [Hinweise](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), zukünftige Unterstützung für Office-Gruppen und SharePoint-Apps ist geplant. | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (nur Word, Excel, PowerPoint und OneNote). Unterstützung von OneDrive for Business für die Zukunft geplant| Office 365 SharePoint Online| macOS|
| Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Office Mobile-Apps | Office 365 SharePoint Online | Android, iOS |
| Office Yammer-App | Office 365 Yammer | Windows 10, iOS und Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (Office für macOS) | Microsoft Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (mit moderner Authentifizierung), Skype for Business (mit moderner Authentifizierung) | Microsoft Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Outlook Mobile-App | Microsoft Office 365 Exchange Online | Android, iOS |
| Power BI-App | Power BI-Dienst | Windows 10, Windows 8.1, Windows 7, Android und iOS |
| Skype for Business | Microsoft Office 365 Exchange Online| Android, iOS |
| Visual Studio Team Services-App | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS, Android |

## <a name="support-for-legacy-authentication"></a>Unterstützung von Legacyauthentifizierung

Wenn Sie **Andere Clients** auswählen, können Sie eine Bedingung für Apps mit Standardauthentifizierung über E-Mail-Protokolle wie IMAP, MAPI, POP oder SMTP sowie für ältere Office-Apps angeben, die keine moderne Authentifizierung verwenden.  

![Andere Clients](./media/technical-reference/11.png)

Weitere Informationen finden Sie unter [Client-Apps](conditions.md#client-apps).

## <a name="approved-client-app-requirement"></a>Genehmigte Client-App als Voraussetzung

Sie können in der Richtlinie für bedingten Zugriff vorschreiben, dass ein Zugriffsversuch für die ausgewählten Cloud-Apps über eine genehmigte Client-App erfolgen muss. 

![Steuern des Zugriffs für genehmigte Client-Apps](./media/technical-reference/21.png)

Diese Einstellung gilt für die folgenden Client-Apps:

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype for Business
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer

**Anmerkungen**

- Die genehmigten Client-Apps unterstützen das Intune-Feature für die mobile Anwendungsverwaltung.
- Anforderung **Genehmigte Client-App erforderlich**:
   - Unterstützt als [Geräteplattformbedingung](#device-platform-condition) nur iOS und Android.

## <a name="app-protection-policy-requirement"></a>App-Schutzrichtlinie als Voraussetzung 

In Ihrer Richtlinie für bedingten Zugriff können Sie verlangen, dass eine App-Schutzrichtlinie für die Client-App vorhanden sein muss, bevor der Zugriff auf die ausgewählten Cloud-Apps verfügbar ist. 

![Steuern des Zugriffs mit einer App-Schutzrichtlinie](./media/technical-reference/22.png)

Diese Einstellung gilt für die folgenden Client-Apps:

- Microsoft OneDrive
- Microsoft Outlook

**Anmerkungen**

- Apps für die App-Schutzrichtlinie unterstützen die mobile Anwendungsverwaltung mit Intune mit Richtlinienschutz.
- Die Voraussetzung **App-Schutzrichtlinie erforderlich**:
    - Unterstützt als [Geräteplattformbedingung](#device-platform-condition) nur iOS und Android.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über den bedingten Zugriff finden Sie unter [Bedingter Zugriff in Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
- Wenn Sie bereit sind, Richtlinien für den bedingten Zugriff für Ihre Umgebung zu konfigurieren, helfen Ihnen die [empfohlenen Methoden für den bedingten Zugriff in Azure Active Directory](best-practices.md) weiter.

<!--Image references-->
[1]: ./media/technical-reference/01.png
