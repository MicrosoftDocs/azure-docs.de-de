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
ms.topic: how-to
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f757d8f59c06d573d71099941530dfc28174ac42
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420483"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Nahtloses einmaliges Anmelden mit Azure Active Directory: Häufig gestellte Fragen

In diesem Artikel werden häufig gestellte Fragen zum nahtlosen einmaligen Anmelden (Seamless Single Sign-On, Seamless SSO) mit Azure Active Directory behandelt. Schauen Sie öfter vorbei, da wir regelmäßig neue Fragen hinzufügen.

**F: Welche Anmeldemethoden werden von Seamless SSO unterstützt?**

Die nahtlose SSO kann mit den Anmeldemethoden [Kennworthashsynchronisierung](how-to-connect-password-hash-synchronization.md) oder [Passthrough-Authentifizierung](how-to-connect-pta.md) kombiniert werden. Dieses Feature kann jedoch nicht mit Active Directory-Verbunddiensten (AD FS) verwendet werden.

**F: Ist Seamless SSO eine kostenlose Funktion?**

Die nahtlose SSO ist eine kostenlose Funktion, sodass Sie für deren Verwendung keine kostenpflichtigen Versionen von Azure AD benötigen.

**F: Ist Seamless SSO in der Cloud [Microsoft Azure Deutschland](https://www.microsoft.de/cloud-deutschland) und der Cloud [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) verfügbar?**

Nahtloses SSO ist für die Azure Government-Cloud verfügbar. Weitere Informationen finden Sie unter [Überlegungen zur Hybrididentität für Azure Government](./reference-connect-government-cloud.md).

**F: Welche Anwendungen nutzen die `domain_hint`- oder `login_hint`-Parameterfunktion von Seamless SSO?**

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

**F: Unterstützt Seamless SSO `Alternate ID` als Benutzername anstelle von `userPrincipalName`?**

Ja. Die nahtlose SSO unterstützt `Alternate ID` als Benutzername, wenn dies in Azure AD Connect wie [hier](how-to-connect-install-custom.md) beschrieben entsprechend konfiguriert ist. Nicht alle Microsoft 365-Anwendungen unterstützen `Alternate ID`. Angaben zur Supporterklärung finden Sie in der Dokumentation der jeweiligen Anwendung.

**F: Was ist der Unterschied zwischen dem von [Azure AD Join](../devices/overview.md) bereitgestellten einmaligen Anmelden und Seamless SSO?**

[Azure AD Join](../devices/overview.md) stellt SSO für Benutzer bereit, wenn deren Geräte bei Azure AD registriert sind. Diese Geräte müssen nicht unbedingt in eine Domäne eingebunden sein. SSO wird über *primäre Aktualisierungstoken* oder *PRTs* (Primary Refresh Token) bereitgestellt und nicht per Kerberos. Die Benutzeroberfläche ist für Windows 10-Geräte optimiert. Das einmalige Anmelden erfolgt im Microsoft Edge-Browser automatisch. Es kann mithilfe einer Browsererweiterung auch in Chrome genutzt werden.

Sie können sowohl Azure AD Join als auch das nahtlose einmalige Anmelden für Ihren Mandanten verwenden. Diese beiden Features ergänzen einander. Wenn beide Features aktiviert sind, hat das einmalige Anmelden aus Azure AD Join eine höhere Priorität als das nahtlose einmalige Anmelden.

**F: Ich möchte Geräte ohne Windows 10 mit Azure AD registrieren, ohne AD FS zu verwenden. Kann ich stattdessen Seamless SSO verwenden?**

Ja. Für dieses Szenario benötigen Sie Version 2.1 oder höher des [Clients für die Arbeitsplatzeinbindung](https://www.microsoft.com/download/details.aspx?id=53554).

**F: Wie kann ich ein Rollover des Kerberos-Entschlüsselungsschlüssels des `AZUREADSSO`-Computerkontos durchführen?**

Es ist wichtig, häufig ein Rollover des Kerberos-Entschlüsselungsschlüssels des `AZUREADSSO`-Computerkontos durchzuführen, das Azure AD repräsentiert und in Ihrer lokalen AD-Gesamtstruktur erstellt wurde.

>[!IMPORTANT]
>Es wird dringend empfohlen, das Rollover des Kerberos-Entschlüsselungsschlüssels mindestens alle 30 Tage durchzuführen.

Führen Sie diese Schritte auf dem lokalen Server durch, auf dem Azure AD Connect ausgeführt wird:

   **Schritt 1. Rufen Sie die Liste der AD-Gesamtstrukturen ab, für die das nahtlose einmalige Anmelden aktiviert wurde.**

   1. Laden Sie zuerst [Azure AD PowerShell](/powershell/azure/active-directory/overview) herunter, und installieren Sie die Software.
   2. Navigieren Sie zum Ordner `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importieren Sie das PowerShell-Modul „Nahtlose SSO“ mit folgendem Befehl: `Import-Module .\AzureADSSO.psd1`.
   4. Führen Sie PowerShell als Administrator aus. Rufen Sie in PowerShell `New-AzureADSSOAuthenticationContext` auf. Mit diesem Befehl sollte ein Popupfenster geöffnet werden, in dem Sie die Anmeldeinformationen des globalen Administrators Ihres Mandanten eingeben können.
   5. Rufen Sie `Get-AzureADSSOStatus | ConvertFrom-Json` auf. Dadurch erhalten Sie die Liste der AD-Gesamtstrukturen (siehe die Liste „Domänen“), in denen diese Funktion aktiviert ist.

   **Schritt 2. Aktualisieren Sie den Kerberos-Entschlüsselungsschlüssel in jeder AD-Gesamtstruktur, in der er eingerichtet wurde.**

   1. Rufen Sie `$creds = Get-Credential` auf. Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen des Domänenadministrators für die vorgesehene AD-Gesamtstruktur ein.

   > [!NOTE]
   >Der Benutzername der Anmeldeinformationen des Domänenadministrators muss im Namensformat für das SAM-Konto („contoso\johndoe“ oder „contoso.com\johndoe“) eingegeben werden. Wir verwenden den Domänenteil des Benutzernamens, um den Domänencontroller des Domänenadministrators mithilfe von DNS zu suchen.

   >[!NOTE]
   >Das verwendete Domänenadministratorkonto darf kein Mitglied der Gruppe „Geschützte Benutzer“ sein. Andernfalls schlägt der Vorgang fehl.

   2. Rufen Sie `Update-AzureADSSOForest -OnPremCredentials $creds` auf. Dieser Befehl aktualisiert den Kerberos-Entschlüsselungsschlüssel für das `AZUREADSSO`-Computerkonto in dieser bestimmten AD-Gesamtstruktur und in Azure AD.
   
   >[!NOTE]
   >Wenn Sie kein Domänenadministrator sind und der Domänenadministrator Ihnen Berechtigungen zugewiesen hat, sollten Sie `Update-AzureADSSOForest -OnPremCredentials $creds -PreserveCustomPermissionsOnDesktopSsoAccount` aufrufen.
   
   3. Wiederholen Sie die oben stehenden Schritte für jede AD-Gesamtstruktur, für die Sie das Feature eingerichtet haben.
   
  >[!NOTE]
   >Wenn Sie eine andere Gesamtstruktur als die von Azure AD Connect aktualisieren, stellen Sie sicher, dass Konnektivität zum globalen Katalogserver (TCP 3268 und TCP 3269) gegeben ist.

   >[!IMPORTANT]
   >Stellen Sie sicher, dass Sie den `Update-AzureADSSOForest`-Befehl _nur_ einmal ausführen. Andernfalls funktioniert das Feature erst wieder, wenn die Kerberos-Tickets Ihrer Benutzer ablaufen und von Ihrem lokalen Active Directory neu ausgestellt werden.

**F: Wie kann ich Seamless SSO deaktivieren?**

   **Schritt 1. Deaktivieren Sie das Feature für Ihren Mandanten.**

   **Option A: Deaktivieren mit Azure AD Connect**
    
   1. Führen Sie Azure AD Connect aus, wählen Sie **Benutzeranmeldung ändern** aus, und klicken Sie auf **Weiter**.
   2. Deaktivieren Sie dann die Option **Einmaliges Anmelden aktivieren**. Setzen Sie den Assistenten fort.

   Nach Abschluss des Assistenten ist die nahtlose SSO für Ihren Mandanten deaktiviert. Allerdings wird auf dem Bildschirm die folgende Meldung angezeigt:

   „Das einmalige Anmelden (SSO) ist jetzt deaktiviert, aber zur Bereinigung müssen manuell zusätzliche Schritte ausgeführt werden. Weitere Informationen“

   Um den Bereinigungsprozess abzuschließen, führen Sie die Schritte 2 und 3 auf dem lokalen Server durch, auf dem Azure AD Connect ausgeführt wird.

   **Option B: Deaktivieren mit PowerShell**

   Führen Sie die folgenden Schritte auf dem lokalen Server durch, auf dem Azure AD Connect ausgeführt wird:

   1. Laden Sie zuerst [Azure AD PowerShell](/powershell/azure/active-directory/overview) herunter, und installieren Sie die Software.
   2. Navigieren Sie zum Ordner `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importieren Sie das PowerShell-Modul „Nahtlose SSO“ mit folgendem Befehl: `Import-Module .\AzureADSSO.psd1`.
   4. Führen Sie PowerShell als Administrator aus. Rufen Sie in PowerShell `New-AzureADSSOAuthenticationContext` auf. Mit diesem Befehl sollte ein Popupfenster geöffnet werden, in dem Sie die Anmeldeinformationen des globalen Administrators Ihres Mandanten eingeben können.
   5. Rufen Sie `Enable-AzureADSSO -Enable $false` auf.
   
   Hier wird nahtloses einmaliges Anmelden deaktiviert, die Domänen behalten jedoch ihre Konfiguration, falls Sie das nahtlose einmalige Anmelden später wieder aktivieren möchten. Wenn Sie die Domänen vollständig aus der Konfiguration für nahtloses einmaliges Anmelden entfernen möchten, rufen Sie das folgende Cmdlet auf, nachdem Sie Schritt 5 oben ausgeführt haben: `Disable-AzureADSSOForest -DomainFqdn <fqdn>`.

   >[!IMPORTANT]
   >Das Deaktivieren des nahtlosen SSO mithilfe von PowerShell ändert nicht den Status in Azure AD Connect. Das nahtlose einmalige Anmelden wird auf der Seite **Benutzeranmeldung ändern** als aktiviert angezeigt.

   **Schritt 2. Rufen Sie die Liste der AD-Gesamtstrukturen ab, für die das nahtlose einmalige Anmelden aktiviert wurde.**

   Führen Sie die Aufgaben 1 bis 4 unten aus, wenn Sie das nahtlose einmalige Anmelden mit Azure AD Connect deaktiviert haben. Wenn Sie das nahtlose einmalige Anmelden stattdessen mithilfe von PowerShell deaktiviert haben, fahren Sie direkt mit Aufgabe 5 fort.

   1. Laden Sie zuerst [Azure AD PowerShell](/powershell/azure/active-directory/overview) herunter, und installieren Sie die Software.
   2. Navigieren Sie zum Ordner `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importieren Sie das PowerShell-Modul „Nahtlose SSO“ mit folgendem Befehl: `Import-Module .\AzureADSSO.psd1`.
   4. Führen Sie PowerShell als Administrator aus. Rufen Sie in PowerShell `New-AzureADSSOAuthenticationContext` auf. Mit diesem Befehl sollte ein Popupfenster geöffnet werden, in dem Sie die Anmeldeinformationen des globalen Administrators Ihres Mandanten eingeben können.
   5. Rufen Sie `Get-AzureADSSOStatus | ConvertFrom-Json` auf. Dadurch erhalten Sie die Liste der AD-Gesamtstrukturen (siehe die Liste „Domänen“), in denen diese Funktion aktiviert ist.

   **Schritt 3. Löschen Sie das Computerkonto `AZUREADSSO` manuell aus jeder in der Liste enthaltenen AD-Gesamtstruktur.**

## <a name="next-steps"></a>Nächste Schritte

- [**Schnellstart**](how-to-connect-sso-quick-start.md): Einrichten und Ausführen des nahtlosen einmaligen Anmeldens von Azure AD
- [**Technische Einzelheiten**](how-to-connect-sso-how-it-works.md) – Funktionsweise dieses Features verstehen
- [**Problembehandlung**](tshoot-connect-sso.md) – Beheben von häufig auftretenden Problemen mit diesem Feature
- [**UserVoice:**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) Verfassen neuer Feature-Anforderungen
