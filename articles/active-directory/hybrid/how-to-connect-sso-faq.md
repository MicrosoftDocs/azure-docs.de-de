---
title: 'Azure AD Connect: Nahtloses einmaliges Anmelden – häufig gestellte Fragen | Microsoft-Dokumentation'
description: Antworten auf häufig gestellte Fragen zum nahtlosen einmaligen Anmelden mit Azure Active Directory.
services: active-directory
keywords: Was ist Azure AD Connect, Active Directory installieren, erforderliche Komponenten für Azure AD, SSO, Single Sign-On, einmaliges Anmelden
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 709fb3be37850be37d6378652921ce26f4ff15fe
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60242227"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Nahtloses einmaliges Anmelden mit Azure Active Directory: Häufig gestellte Fragen

In diesem Artikel werden häufig gestellte Fragen zum nahtlosen einmaligen Anmelden (Seamless Single Sign-On, Seamless SSO) mit Azure Active Directory behandelt. Schauen Sie öfter vorbei, da wir regelmäßig neue Fragen hinzufügen.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>Welche Anmeldemethoden verwendet die nahtlose SSO?

Die nahtlose SSO kann mit den Anmeldemethoden [Kennworthashsynchronisierung](how-to-connect-password-hash-synchronization.md) oder [Passthrough-Authentifizierung](how-to-connect-pta.md) kombiniert werden. Dieses Feature kann jedoch nicht mit Active Directory-Verbunddiensten (AD FS) verwendet werden.

## <a name="is-seamless-sso-a-free-feature"></a>Ist die nahtlose SSO eine kostenlose Funktion?

Die nahtlose SSO ist eine kostenlose Funktion, sodass Sie für deren Verwendung keine kostenpflichtigen Versionen von Azure AD benötigen.

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpswwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Ist das nahtlose einmalige Anmelden in der Cloud [Microsoft Azure Deutschland](https://www.microsoft.de/cloud-deutschland) und der Cloud [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) verfügbar?

Nein. Das nahtlose einmalige Anmelden ist nur in der weltweiten Instanz von Azure AD verfügbar.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>Welche Anwendungen nutzen die `domain_hint`- oder `login_hint`-Parameterfunktion der nahtlosen SSO?

Nachfolgend finden Sie eine nicht erschöpfende Liste von Anwendungen, die diese Parameter an Azure AD senden und Benutzern so eine unbeaufsichtigte Anmeldung über das nahtlose einmalige Anmelden ermöglichen können (d.h. Ihre Benutzer müssen ihre Benutzernamen oder Kennwörter nicht eingeben):

| Anwendungsname | Zu verwendende Anwendungs-URL |
| -- | -- |
| Zugriffsbereich | https:\//myapps.microsoft.com/contoso.com |
| Outlook im Web | https:\//outlook.office365.com/contoso.com |
| Office 365-Portale | https:\//portal.office.com?domain_hint=contoso.com, https:\//www.office.com?domain_hint=contoso.com |

Darüber hinaus erhalten Benutzer eine automatische Anmeldeerfahrung, wenn eine Anwendung Anmeldeanforderungen an die als Mandanten eingerichteten Endpunkte von Azure AD sendet - also https:\//login.microsoftonline.com/contoso.com/<..> oder https:\//login.microsoftonline.com/<tenant_ID>/<..> - anstelle des gemeinsamen Endpunkts von Azure AD - also https:\//login.microsoftonline.com/common/<...>. Nachfolgend finden Sie eine nicht erschöpfende Liste von Anwendungen, die diese Arten von Anmeldeanforderungen vornehmen.

| Anwendungsname | Zu verwendende Anwendungs-URL |
| -- | -- |
| SharePoint Online | https:\//contoso.sharepoint.com |
| Azure-Portal | https:\//portal.azure.com/contoso.com |

Ersetzen Sie „contoso.com“ in den Tabellen oben durch Ihren Domänennamen, um die richtigen Anwendungs-URLs für Ihren Mandanten zu erhalten.

Wenn unsere automatische Anmeldung für andere Anwendungen verwenden möchten, teilen Sie uns dies im Feedbackabschnitt mit.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Unterstützt die nahtlose SSO `Alternate ID` als Benutzername anstelle von `userPrincipalName`?

Ja. Die nahtlose SSO unterstützt `Alternate ID` als Benutzername, wenn dies in Azure AD Connect wie [hier](how-to-connect-install-custom.md) beschrieben entsprechend konfiguriert ist. Nicht alle Office 365-Anwendungen unterstützen `Alternate ID`. Angaben zur Supporterklärung finden Sie in der Dokumentation der jeweiligen Anwendung.

## <a name="what-is-the-difference-between-the-single-sign-on-experience-provided-by-azure-ad-joinactive-directory-azureadjoin-overviewmd-and-seamless-sso"></a>Was ist der Unterschied zwischen dem einmaligen Anmelden durch [Azure AD Join](../active-directory-azureadjoin-overview.md) und dem nahtlosen einmaligen Anmelden?

[Azure AD Join](../active-directory-azureadjoin-overview.md) stellt SSO für Benutzer bereit, wenn deren Geräte bei Azure AD registriert sind. Diese Geräte müssen nicht unbedingt in eine Domäne eingebunden sein. SSO wird über *primäre Aktualisierungstoken* oder *PRTs* (Primary Refresh Token) bereitgestellt und nicht per Kerberos. Die Benutzeroberfläche ist für Windows 10-Geräte optimiert. Das einmalige Anmelden erfolgt im Microsoft Edge-Browser automatisch. Es kann mithilfe einer Browsererweiterung auch in Chrome genutzt werden.

Sie können sowohl Azure AD Join als auch das nahtlose einmalige Anmelden für Ihren Mandanten verwenden. Diese beiden Features ergänzen einander. Wenn beide Features aktiviert sind, hat das einmalige Anmelden aus Azure AD Join eine höhere Priorität als das nahtlose einmalige Anmelden.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Ich möchte Geräte ohne Windows 10 mit Azure AD registrieren, ohne AD FS zu verwenden. Kann ich stattdessen die nahtlose SSO verwenden?

Ja. Für dieses Szenario benötigen Sie Version 2.1 oder höher des [Clients für die Arbeitsplatzeinbindung](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>Wie kann ich ein Rollover des Kerberos-Entschlüsselungsschlüssels des `AZUREADSSOACC`-Computerkontos durchführen?

Es ist wichtig, häufig ein Rollover des Kerberos-Entschlüsselungsschlüssels des `AZUREADSSOACC`-Computerkontos durchzuführen, das Azure AD repräsentiert und in Ihrer lokalen AD-Gesamtstruktur erstellt wurde.

>[!IMPORTANT]
>Es wird dringend empfohlen, das Rollover des Kerberos-Entschlüsselungsschlüssels mindestens alle 30 Tage durchzuführen.

Führen Sie diese Schritte auf dem lokalen Server durch, auf dem Azure AD Connect ausgeführt wird:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Schritt 1: Rufen Sie die Liste der AD-Gesamtstrukturen ab, für die das nahtlose einmalige Anmelden aktiviert wurde.

1. Laden Sie zuerst [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview) herunter, und installieren Sie die Software.
2. Navigieren Sie zum Ordner `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importieren Sie das PowerShell-Modul „Nahtlose SSO“ mit folgendem Befehl: `Import-Module .\AzureADSSO.psd1`.
4. Führen Sie PowerShell als Administrator aus. Rufen Sie in PowerShell `New-AzureADSSOAuthenticationContext` auf. Mit diesem Befehl sollte ein Popupfenster geöffnet werden, in dem Sie die Anmeldeinformationen des globalen Administrators Ihres Mandanten eingeben können.
5. Rufen Sie `Get-AzureADSSOStatus | ConvertFrom-Json` auf. Dadurch erhalten Sie die Liste der AD-Gesamtstrukturen (siehe die Liste „Domänen“), in denen diese Funktion aktiviert ist.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>Schritt 2: Aktualisieren Sie den Kerberos-Entschlüsselungsschlüssel in jeder AD-Gesamtstruktur, in der er eingerichtet ist.

1. Rufen Sie `$creds = Get-Credential` auf. Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen des Domänenadministrators für die vorgesehene AD-Gesamtstruktur ein.

   > [!NOTE]
   > Um die gewünschte AD-Gesamtstruktur zu finden, verwenden wir den Domänenadministrator-Benutzernamen, der im Format der Benutzerprinzipalnamen (User Principal Names, UPN) (johndoe@contoso.com) oder qualifizierten SAM-Kontodomänennamen (contoso\johndoe oder contoso.com\johndoe) bereitgestellt wird. Wenn Sie qualifizierte SAM-Kontodomänennamen verwenden, verwenden wir den Domänenteil des Benutzernamens, um [den Domänencontroller des Domänenadministrators mithilfe des DNS zu suchen](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Wenn Sie stattdessen den UPN verwenden, [übersetzen wir ihn in einen qualifizierten SAM-Kontodomänennamen ](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa), bevor der entsprechende Domänencontroller gesucht wird.

2. Rufen Sie `Update-AzureADSSOForest -OnPremCredentials $creds` auf. Dieser Befehl aktualisiert den Kerberos-Entschlüsselungsschlüssel für das `AZUREADSSOACC`-Computerkonto in dieser bestimmten AD-Gesamtstruktur und in Azure AD.
3. Wiederholen Sie die oben stehenden Schritte für jede AD-Gesamtstruktur, für die Sie das Feature eingerichtet haben.

>[!IMPORTANT]
>Stellen Sie sicher, dass Sie den `Update-AzureADSSOForest`-Befehl _nur_ einmal ausführen. Andernfalls funktioniert das Feature erst wieder, wenn die Kerberos-Tickets Ihrer Benutzer ablaufen und von Ihrem lokalen Active Directory neu ausgestellt werden.

## <a name="how-can-i-disable-seamless-sso"></a>Wie kann ich die nahtlose SSO deaktivieren?

### <a name="step-1-disable-the-feature-on-your-tenant"></a>Schritt 1: Deaktivieren Sie das Feature für Ihren Mandanten.

#### <a name="option-a-disable-using-azure-ad-connect"></a>Option A: Deaktivieren mit Azure AD Connect

1. Führen Sie Azure AD Connect aus, wählen Sie **Benutzeranmeldung ändern** aus, und klicken Sie auf **Weiter**.
2. Deaktivieren Sie dann die Option **Einmaliges Anmelden aktivieren**. Setzen Sie den Assistenten fort.

Nach Abschluss des Assistenten ist die nahtlose SSO für Ihren Mandanten deaktiviert. Allerdings wird auf dem Bildschirm die folgende Meldung angezeigt:

„Das einmalige Anmelden (SSO) ist jetzt deaktiviert, aber zur Bereinigung müssen manuell zusätzliche Schritte ausgeführt werden. Weitere Informationen“

Um den Bereinigungsprozess abzuschließen, führen Sie die Schritte 2 und 3 auf dem lokalen Server durch, auf dem Azure AD Connect ausgeführt wird.

#### <a name="option-b-disable-using-powershell"></a>Option B: Deaktivieren mit PowerShell

Führen Sie die folgenden Schritte auf dem lokalen Server durch, auf dem Azure AD Connect ausgeführt wird:

1. Laden Sie zuerst [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview) herunter, und installieren Sie die Software.
2. Navigieren Sie zum Ordner `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importieren Sie das PowerShell-Modul „Nahtlose SSO“ mit folgendem Befehl: `Import-Module .\AzureADSSO.psd1`.
4. Führen Sie PowerShell als Administrator aus. Rufen Sie in PowerShell `New-AzureADSSOAuthenticationContext` auf. Mit diesem Befehl sollte ein Popupfenster geöffnet werden, in dem Sie die Anmeldeinformationen des globalen Administrators Ihres Mandanten eingeben können.
5. Rufen Sie `Enable-AzureADSSO -Enable $false` auf.

>[!IMPORTANT]
>Das Deaktivieren des nahtlosen SSO mithilfe von PowerShell ändert nicht den Status in Azure AD Connect. Das nahtlose einmalige Anmelden wird auf der Seite **Benutzeranmeldung ändern** als aktiviert angezeigt.

### <a name="step-2-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Schritt 2: Rufen Sie die Liste der AD-Gesamtstrukturen ab, für die das nahtlose einmalige Anmelden aktiviert wurde.

Führen Sie die Aufgaben 1 bis 4 unten aus, wenn Sie das nahtlose einmalige Anmelden mit Azure AD Connect deaktiviert haben. Wenn Sie das nahtlose einmalige Anmelden stattdessen mithilfe von PowerShell deaktiviert haben, fahren Sie direkt mit Aufgabe 5 fort.

1. Laden Sie zuerst [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview) herunter, und installieren Sie die Software.
2. Navigieren Sie zum Ordner `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importieren Sie das PowerShell-Modul „Nahtlose SSO“ mit folgendem Befehl: `Import-Module .\AzureADSSO.psd1`.
4. Führen Sie PowerShell als Administrator aus. Rufen Sie in PowerShell `New-AzureADSSOAuthenticationContext` auf. Mit diesem Befehl sollte ein Popupfenster geöffnet werden, in dem Sie die Anmeldeinformationen des globalen Administrators Ihres Mandanten eingeben können.
5. Rufen Sie `Get-AzureADSSOStatus | ConvertFrom-Json` auf. Dadurch erhalten Sie die Liste der AD-Gesamtstrukturen (siehe die Liste „Domänen“), in denen diese Funktion aktiviert ist.

### <a name="step-3-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>Schritt 3: Löschen Sie das Computerkonto `AZUREADSSOACCT` manuell aus jeder AD-Gesamtstruktur, die Sie in der Liste finden.

## <a name="next-steps"></a>Nächste Schritte

- [**Schnellstart**](how-to-connect-sso-quick-start.md): Einrichten und Ausführen der nahtlosen SSO mit Azure AD
- [**Technische Einzelheiten**](how-to-connect-sso-how-it-works.md) – Funktionsweise dieses Features verstehen
- [**Problembehandlung**](tshoot-connect-sso.md) – Beheben von häufig auftretenden Problemen mit diesem Feature
- [**UserVoice:** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) Verfassen neuer Feature-Anforderungen
