---
title: Konfigurieren der Verwaltung von Authentifizierungssitzungen – Azure Active Directory
description: Anpassen der Konfiguration von Azure AD-Authentifizierungssitzungen einschließlich der Häufigkeit von Benutzeranmeldungen und Persistenz von Browsersitzungen.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/23/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6deb0c005b5ed7daf5b30bea8a65ee70d8b460c5
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837463"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Konfigurieren der Verwaltung von Authentifizierungssitzungen mit bedingtem Zugriff

In komplexen Bereitstellungen müssen Organisationen Authentifizierungssitzungen manchmal einschränken. Einige mögliche Szenarien:

* Ressourcenzugriff von einem nicht verwalteten oder freigegebenen Gerät
* Zugriff auf vertrauliche Informationen von einem externen Netzwerk aus
* Intensive Systembenutzer
* Unternehmenskritische Geschäftsanwendungen

Mit Steuerungen für den bedingten Zugriff können Sie Richtlinien erstellen, die auf spezifische Anwendungsfälle in Ihrer Organisation ausgerichtet sind, ohne alle Benutzer zu beeinflussen.

Bevor wir auf die Details zur Konfiguration der Richtlinie eingehen, betrachten wir die Standardkonfiguration.

## <a name="user-sign-in-frequency"></a>Anmeldehäufigkeit von Benutzern

Die Anmeldehäufigkeit bezeichnet den Zeitraum, bevor ein Benutzer beim Zugriff auf eine Ressource aufgefordert wird, sich erneut anzumelden.

Die Standardkonfiguration von Azure Active Directory (Azure AD) sieht für die Anmeldehäufigkeit von Benutzern ein rollierendes Zeitfenster von 90 Tagen vor. Benutzer häufig zur Eingabe von Anmeldeinformationen aufzufordern, kann einerseits sinnvoll sein, andererseits aber auch das Gegenteil bewirken: Benutzer, die es gewohnt sind, ihre Anmeldeinformationen ohne Überlegung einzugeben, können diese auch versehentlich bei einer schädlichen Anmeldeaufforderung eingeben.

Es klingt vielleicht beunruhigend, keine erneute Anmeldung von einem Benutzer zu fordern, tatsächlich wird die Sitzung bei einer Verletzung der IT-Richtlinien jedoch gesperrt. Einige Beispiele beinhalten u. a. eine Kennwortänderung, ein nicht kompatibles Gerät oder eine Kontodeaktivierung. Sie können [Benutzersitzungen auch explizit mit PowerShell sperren](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0&preserve-view=true). Die Standardkonfiguration von Azure AD basiert auf dem Grundprinzip „Benutzer nicht zur Angabe ihrer Anmeldeinformationen auffordern, solange der Sicherheitsstatus ihrer Sitzung unverändert ist“.

Die Einstellung für die Anmeldehäufigkeit funktioniert bei Apps mit standardkonformer Implementierung des OAUTH2- oder OIDC-Protokolls. Die meisten nativen Microsoft-Apps für Windows, Mac und mobile Umgebungen, einschließlich der folgenden Webanwendungen, sind mit der Einstellung kompatibel.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- Microsoft 365-Verwaltungsportal
- Exchange Online
- SharePoint und OneDrive
- Teams-Webclient
- Dynamics CRM Online
- Azure-Portal

Die Einstellung für die Anmeldehäufigkeit funktioniert auch mit SAML-Anwendungen, solange diese nicht die eigenen Cookies verwerfen und in regelmäßigen Abständen an Azure AD zur Authentifizierung umgeleitet werden.

### <a name="user-sign-in-frequency-and-multi-factor-authentication"></a>Anmeldehäufigkeit und mehrstufige Authentifizierung

Die Anmeldehäufigkeit wurde bisher nur für die einstufige Authentifizierung auf Geräten angewendet, die in Azure AD oder Azure AD Hybrid eingebunden und bei Azure AD registriert waren. Für unsere Kunden gab es keine einfache Möglichkeit, die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) auf diesen Geräten zu erzwingen. Aufgrund des Kundenfeedbacks wird die Anmeldehäufigkeit jetzt auch auf die mehrstufige Authentifizierung angewendet.

[![Anmeldehäufigkeit und MFA](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart-small.png)](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart.png#lightbox)

### <a name="user-sign-in-frequency-and-device-identities"></a>Anmeldehäufigkeit von Benutzern und Geräteidentitäten

Wenn Sie über in Azure AD oder Azure AD Hybrid eingebundene oder bei Azure AD registrierte Geräte verfügen, erfüllt auch das Entsperren von Geräten oder interaktive Anmeldungen von Besuchern die Richtlinie für die Anmeldehäufigkeit. In den folgenden beiden Beispielen ist die Anmeldehäufigkeit von Benutzern auf 1 Stunde festgelegt:

Beispiel 1:

- Um 00:00 Uhr meldet sich ein Benutzer bei seinem in Azure AD eingebundenen Windows 10-Gerät an und beginnt mit der Bearbeitung eines in SharePoint Online gespeicherten Dokuments.
- Der Benutzer arbeitet auf seinem Gerät eine Stunde an diesem Dokument.
- Um 01:00 Uhr wird der Benutzer basierend auf der vom Administrator in der Richtlinie für bedingten Zugriff konfigurierten Anmeldehäufigkeitsanforderung zur erneuten Anmeldung aufgefordert.

Beispiel 2:

- Um 00:00 Uhr meldet sich ein Benutzer bei seinem in Azure AD eingebundenen Windows 10-Gerät an und beginnt mit der Bearbeitung eines in SharePoint Online gespeicherten Dokuments.
- Um 00:30 Uhr steht der Benutzer auf und macht eine Pause, wobei er sein Gerät sperrt.
- Um 00:45 Uhr kehrt der Benutzer aus seiner Pause zurück und entsperrt das Gerät.
- Um 01:45 Uhr wird der Benutzer basierend auf der vom Administrator in der Richtlinie für bedingten Zugriff konfigurierten Anmeldehäufigkeitsanforderung zur erneuten Anmeldung aufgefordert, weil die letzte Anmeldung um 00:45 Uhr erfolgte.

## <a name="persistence-of-browsing-sessions"></a>Persistenz von Browsersitzungen

Bei einer persistenten Browsersitzung können Benutzer angemeldet bleiben, nachdem sie ihr Browserfenster geschlossen und erneut geöffnet haben.

Durch die Azure AD-Standardeinstellung für die Persistenz von Browsersitzungen können Benutzer auf persönlichen Geräten wählen, ob die Sitzung beibehalten wird, indem nach der erfolgreichen Authentifizierung die Aufforderung „Angemeldet bleiben?“ angezeigt wird. Wenn die Browserpersistenz in AD FS mithilfe der Anleitung im Artikel [AD FS: Einstellungen für einmaliges Anmelden](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
) konfiguriert wird, wird diese Richtlinie berücksichtigt und die Azure AD-Sitzung ebenfalls beibehalten. Sie können auch konfigurieren, ob Benutzer in Ihrem Mandanten die Aufforderung „Angemeldet bleiben?“ erhalten. Dazu ändern Sie im Azure-Portal im Bereich für Unternehmensbranding die entsprechende Einstellung mithilfe der Anleitung im Artikel [Anpassen Ihrer Azure AD-Anmeldeseite](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Konfigurieren von Steuerungen für Authentifizierungssitzungen

Der bedingte Zugriff ist eine Azure AD Premium-Funktion und erfordert eine Premium-Lizenz. Weitere Informationen zum bedingten Zugriff finden Sie unter [Was ist bedingter Zugriff in Azure Active Directory?](overview.md#license-requirements)

> [!WARNING]
> Wenn Sie die derzeit in der Public Preview verfügbare Funktion für die [konfigurierbare Tokengültigkeitsdauer](../develop/active-directory-configurable-token-lifetimes.md) verwenden, sollten Sie beachten, dass die Erstellung von zwei verschiedenen Richtlinien für dieselbe Benutzer- oder App-Kombination nicht unterstützt wird: eine mit dieser Funktion und eine andere mit der konfigurierbaren Tokengültigkeitsdauer. Microsoft beabsichtigt, die Funktion für die konfigurierbare Tokengültigkeitsdauer bei den Lebensdauern von Aktualisierungs- und Sitzungstoken am 30. Januar 2021 außer Betrieb zu nehmen und durch die Funktion für die Verwaltung von Authentifizierungssitzungen mit bedingtem Zugriff zu ersetzen.  
>
> Stellen Sie vor dem Aktivieren der Anmeldehäufigkeit sicher, dass andere Einstellungen für die erneute Authentifizierung in Ihrem Mandanten deaktiviert sind. Wenn „MFA auf vertrauenswürdigen Geräten speichern“ aktiviert ist, deaktivieren Sie diese Option vor dem Verwenden der Anmeldehäufigkeit, da die Verwendung dieser beiden Einstellungen dazu führen kann, dass Benutzer unerwartet aufgefordert werden. Weitere Informationen zu Eingabeaufforderungen für die erneute Authentifizierung und zur Sitzungslebensdauer finden Sie im Artikel [Optimieren von Aufforderungen für die erneute Authentifizierung und Grundlegendes zur Sitzungslebensdauer für Azure AD Multi-Factor Authentication](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

### <a name="policy-1-sign-in-frequency-control"></a>Richtlinie 1: Steuerung der Anmeldehäufigkeit

1. Erstellen Sie eine neue Richtlinie.
1. Wählen Sie alle erforderlichen Bedingungen für die Kundenumgebung aus, einschließlich der Cloud-Apps, die als Ziel dienen.

   > [!NOTE]
   > Es wird empfohlen, für Authentifizierungsaufforderungen bei wichtigen Microsoft Office-Apps wie Exchange Online und SharePoint Online dieselbe Häufigkeit festzulegen, um eine optimale Benutzererfahrung zu gewährleisten.

1. Wechseln Sie zu **Zugriffskontrollen** > **Sitzung**, und klicken Sie auf **Anmeldehäufigkeit**.
1. Geben Sie den erforderlichen Wert in Tagen und Stunden in das erste Textfeld ein.
1. Wählen Sie einen Wert für **Stunden** oder **Tage** aus der Dropdownliste aus.
1. Speichern Sie die Richtlinie.

![Richtlinie für bedingten Zugriff, die für die Anmeldehäufigkeit konfiguriert ist](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Auf Windows-Geräten, die in Azure AD registriert sind, gilt die Anmeldung beim Gerät als Erfüllung einer Aufforderung. Beispiel: Wenn Sie die Anmeldehäufigkeit für Office-Apps auf 24 Stunden festgelegt haben, erfüllen Benutzer von Windows-Geräten, die in Azure AD registriert sind, die Richtlinie für die Anmeldehäufigkeit bereits, indem sie sich beim Gerät anmelden. Sie erhalten keine erneute Aufforderung, wenn sie Office-Apps öffnen.

### <a name="policy-2-persistent-browser-session"></a>Richtlinie 2: Persistente Browsersitzung

1. Erstellen Sie eine neue Richtlinie.
1. Wählen Sie alle erforderlichen Bedingungen aus.

   > [!NOTE]
   > Beachten Sie, dass die Auswahl von „Alle Cloud-Apps“ als Bedingung bei dieser Steuerung erforderlich ist. Die Browsersitzungspersistenz wird durch das Token der Authentifizierungssitzung gesteuert. Da alle Registerkarten in einer Browsersitzung über dasselbe Sitzungstoken verfügen, müssen sie alle denselben Persistenzzustand aufweisen.

1. Wechseln Sie zu **Zugriffskontrollen** > **Sitzung**, und klicken Sie auf **Persistente Browsersitzung**.
1. Wählen Sie einen Wert aus der Dropdownliste aus.
1. Speichern Sie die Richtlinie.

![Richtlinie für bedingten Zugriff, die für einen persistenten Browser konfiguriert ist](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Wenn Sie beide Richtlinien konfiguriert haben, wird die Einstellung „Angemeldet bleiben?“, die Sie im Azure-Portal im Bereich für Unternehmensbranding für denselben Benutzer festgelegt haben, durch die Konfiguration der persistenten Browsersitzung unter „Bedingter Azure AD-Zugriff“ außer Kraft gesetzt.

## <a name="validation"></a>Überprüfen

Verwenden Sie das Was-wäre-wenn-Tool, um eine Anmeldung des Benutzers bei der Zielanwendung und weitere Bedingungen zu simulieren, die davon abhängig sind, wie Sie die Richtlinie konfiguriert haben. Die Steuerungen für die Verwaltung von Authentifizierungssitzungen werden in den Ergebnissen des Tools angezeigt.

![Bedingter Zugriff: Ergebnisse des Was-wäre-wenn-Tools](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Richtlinienbereitstellung

Um die erwartete Funktionsweise der Richtlinie sicherzustellen, empfiehlt es sich, sie zu testen, bevor Sie sie in der Produktionsumgebung verwenden. Idealerweise sollten Sie in einem Testmandanten überprüfen, ob die neue Richtlinie wie erwartet funktioniert. Weitere Informationen finden Sie im Artikel [Planen einer Bereitstellung für bedingten Zugriff](plan-conditional-access.md).

## <a name="next-steps"></a>Nächste Schritte

* Wenn Sie Richtlinien für bedingten Zugriff für Ihre Umgebung konfigurieren möchten, lesen Sie den Artikel [Planen einer Bereitstellung für bedingten Zugriff](plan-conditional-access.md).