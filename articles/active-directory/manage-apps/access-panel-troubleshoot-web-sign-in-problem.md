---
title: Problem beim Anmelden bei der Website des Zugriffsbereichs | Microsoft-Dokumentation
description: Leitfaden zur Behandlung von Problemen, die bei der Anmeldung beim Zugriffsbereich auftreten können
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.reviwer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7c6a9c3f26c8939176197a2ecf2fcd6026e9928
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65784305"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Problem beim Anmelden bei der Website des Zugriffsbereichs

Das Zugriffspanel ist ein webbasiertes Portal, in dem Benutzer, die in Azure Active Directory (Azure AD) über ein Geschäfts-, Schul- oder Unikonto verfügen, cloudbasierte Anwendungen anzeigen und starten können, für die der Azure AD-Administrator ihnen Zugriff gewährt hat. Ein Benutzer, der über Azure AD-Editionen verfügt, kann über den Zugriffsbereich auch die Funktionen für die Self-Service-basierte Gruppen- und App-Verwaltung nutzen. Der Zugriffsbereich ist unabhängig vom Azure-Portal und setzt nicht voraus, dass Benutzer über ein Azure-Abonnement verfügen.

Benutzer können sich beim Zugriffsbereich anmelden, wenn sie über ein Geschäfts-, Schul- oder Unikonto in Azure AD verfügen.

-   Benutzer können direkt von Azure AD authentifiziert sein.

-   Benutzer können durch Verwendung der Active Directory-Verbunddienste (AD FS) authentifiziert sein.

-   Benutzer können durch Windows Server Active Directory authentifiziert sein.

Wenn ein Benutzer über ein Abonnement für Azure oder Office 365 verfügt und das Azure-Portal oder eine Office 365-Anwendung verwendet hat, kann dieser Benutzer den Zugriffsbereich nutzen, ohne sich erneut anmelden zu müssen. Nicht authentifizierte Benutzer werden aufgefordert, sich mit dem Benutzernamen und Kennwort für ihr Konto in Azure AD anzumelden. Wenn die Organisation einen Verbund konfiguriert hat, reicht die Eingabe des Benutzernamens aus.

## <a name="general-issues-to-check-first"></a>Allgemeine Probleme, die zuerst überprüft werden sollten 

-   Vergewissern Sie sich, dass sich der Benutzer bei der **richtigen URL** anmeldet: <https://myapps.microsoft.com>.

-   Stellen Sie sicher, dass die URL den **vertrauenswürdigen Sites** im Browser des Benutzers hinzugefügt wurde.

-   Stellen Sie sicher, dass das Konto des Benutzers für Anmeldungen **aktiviert** ist.

-   Stellen Sie sicher, dass das Konto des Benutzers **nicht gesperrt ist**.

-   Stellen Sie sicher, dass **Kennwort des Benutzers nicht abgelaufen ist oder vergessen wurde**.

-   Stellen Sie sicher, dass Benutzerzugriff nicht durch **Multi-Factor Authentication** blockiert wird.

-   Stellen Sie sicher, dass der Benutzerzugriff nicht durch eine **Richtlinie für bedingten Zugriff** oder eine **Identity Protection**-Richtlinie blockiert wird.

-   Stellen Sie sicher, dass die **Kontaktinformationen für die Authentifizierung** des Benutzers aktuell sind, sodass Multi-Factor Authentication-Richtlinien oder Richtlinien für den bedingten Zugriff erzwungen werden können.

-   Stellen Sie sicher, dass der Benutzer die Cookies im Browser gelöscht hat, bevor er versucht, sich erneut anzumelden.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Erfüllen der Browseranforderungen für den Zugriffsbereich

Der Zugriffsbereich erfordert einen Browser, der JavaScript unterstützt und in dem CSS aktiviert ist. Für die Verwendung des einmaligen Anmeldens per Kennwort im Anwendungsbereich muss die Erweiterung für den Zugriffsbereich im Browser des Benutzers installiert sein. Diese Erweiterung wird automatisch heruntergeladen, wenn ein Benutzer eine Anwendung auswählt, die für kennwortbasiertes SSO konfiguriert ist.

Für kennwortbasiertes SSO eignen sich folgende Browser des Endbenutzers:

-   Internet Explorer 8, 9, 10, 11 – unter Windows 7 oder höher

-   Microsoft Edge in Windows 10 Anniversary Edition oder höher 

-   Chrome – unter Windows 7 oder höher und MacOS x oder höher

-   Firefox 26.0 oder höher – unter Windows XP SP2 oder höher und Mac OS X 10.6 oder höher


## <a name="problems-with-the-users-account"></a>Probleme mit dem Konto des Benutzers

Der Zugriff auf den Zugriffsbereich kann aufgrund eines Problems mit dem Benutzerkonto blockiert sein. Nachfolgend finden Sie einige Möglichkeiten zum Lösen von Problemen mit Benutzerkonten und Kontoeinstellungen:

-   [Überprüfen, ob ein Benutzerkonto in Azure Active Directory vorhanden ist](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Überprüfen des Status eines Benutzerkontos](#check-a-users-account-status)

-   [Zurücksetzen des Kennworts für einen Benutzer](#reset-a-users-password)

-   [Aktivieren der Self-Service-Kennwortzurücksetzung](#enable-self-service-password-reset)

-   [Überprüfen des Multi-Factor Authentication-Status eines Benutzers](#check-a-users-multi-factor-authentication-status)

-   [Überprüfen der Kontaktinformationen für die Authentifizierung eines Benutzers](#check-a-users-authentication-contact-info)

-   [Überprüfen der Gruppenmitgliedschaften eines Benutzers](#check-a-users-group-memberships)

-   [Überprüfen der zugewiesenen Lizenzen eines Benutzers](#check-a-users-assigned-licenses)

-   [Zuweisen einer Lizenz zu einem Benutzer](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Überprüfen, ob ein Benutzerkonto in Azure Active Directory vorhanden ist

Um zu überprüfen, ob das Konto eines Benutzers vorhanden ist, führen Sie die folgenden Schritte aus:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5.  Klicken Sie auf **Alle Benutzer**.

6.  **Suchen** Sie nach dem gewünschten Benutzer, und **klicken Sie auf die Zeile**, um ihn auszuwählen.

7.  Überprüfen Sie die Eigenschaften des Benutzerobjekts, um sicherzustellen, dass diese ordnungsgemäß festgelegt sind und keine Daten fehlen.

### <a name="check-a-users-account-status"></a>Überprüfen des Status eines Benutzerkontos

Um den Status eines Benutzerkontos zu überprüfen, führen Sie die folgenden Schritte aus:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5.  Klicken Sie auf **Alle Benutzer**.

6.  **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **klicken Sie auf die Zeile**, um ihn auszuwählen.

7.  Klicken Sie auf **Profil**.

8.  Stellen Sie unter **Einstellungen** sicher, dass die Option **Anmeldung blockieren** auf **Nein** festgelegt ist.

### <a name="reset-a-users-password"></a>Zurücksetzen des Kennworts für einen Benutzer

Um das Kennwort eines Benutzers zurückzusetzen, führen Sie die folgenden Schritte aus:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5.  Klicken Sie auf **Alle Benutzer**.

6.  **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **klicken Sie auf die Zeile**, um ihn auszuwählen.

7.  Klicken Sie oben im Benutzerbereich auf die Schaltfläche **Kennwort zurücksetzen**.

8.  Klicken Sie im jetzt angezeigten Bereich **Kennwort zurücksetzen** auf die Schaltfläche **Kennwort zurücksetzen**.

9.  Kopieren Sie das **temporäre Kennwort**, oder **geben Sie ein neues Kennwort ein**.

10. Teilen Sie dem Benutzer das neue Kennwort mit, da dieser das Kennwort bei der nächsten Anmeldung bei Azure Active Directory ändern muss.

### <a name="enable-self-service-password-reset"></a>Aktivieren der Self-Service-Kennwortzurücksetzung

Um die Self-Service-Kennwortzurücksetzung zu aktivieren, führen Sie die folgenden Bereitstellungsschritte aus:

-   [Aktivieren von Benutzern für das Zurücksetzen ihrer Azure Active Directory-Kennwörter](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Aktivieren von Benutzern für das Zurücksetzen oder Ändern ihrer lokalen Active Directory-Kennwörter](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>Überprüfen des Multi-Factor Authentication-Status eines Benutzers

Um den Multi-Factor Authentication-Status eines Benutzers zu überprüfen, führen Sie die folgenden Schritte aus:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5. Klicken Sie auf **Alle Benutzer**.

6. Klicken Sie oben im Bereich auf die Schaltfläche **Multi-Factor Authentication**.

7. Wenn das **Multi-Factor Authentication-Verwaltungsportal** geöffnet ist, stellen Sie sicher, dass Sie sich auf der Registerkarte **Benutzer** befinden.

8. Suchen Sie den Benutzer, indem Sie die Liste der Benutzer durchsuchen, filtern oder sortieren.

9. Wählen Sie den Benutzer aus der Benutzerliste aus, und wählen Sie die gewünschte Option für die Multi-Factor Authentication: **Aktivieren**, **Deaktivieren** oder **Erzwingen**.

   >[!NOTE]
   >Wenn ein Benutzer sich im Status **Erzwungen** befindet, können Sie den Status vorübergehend auf **Deaktiviert** festlegen, damit der Benutzer sich wieder bei seinem Konto anmelden kann. Sobald der Benutzer wieder bei seinem Konto angemeldet ist, können Sie den Status in **Aktiviert** ändern, damit der Benutzer bei der nächsten Anmeldung seine Kontaktinformationen erneut registrieren muss. Alternativ können Sie Schritte unter [Überprüfen der Kontaktinformationen für die Authentifizierung eines Benutzers](#check-a-users-authentication-contact-info) ausführen, um diese Daten für den Benutzer zu überprüfen oder festzulegen.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>Überprüfen der Kontaktinformationen für die Authentifizierung eines Benutzers

Um die Kontaktinformationen zur Authentifizierung des Benutzers zu überprüfen, die für Multi-Factor Authentication, bedingten Zugriff, Identity Protection und Kennwortzurücksetzung verwendet werden, führen Sie die folgenden Schritte aus:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5.  Klicken Sie auf **Alle Benutzer**.

6.  **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **klicken Sie auf die Zeile**, um ihn auszuwählen.

7.  Klicken Sie auf **Profil**.

8.  Scrollen Sie nach unten zu **Kontaktinformationen für Authentifizierung**.

9.  **Überprüfen** Sie die für den Benutzer registrierten Daten, und aktualisieren Sie sie bei Bedarf.

### <a name="check-a-users-group-memberships"></a>Überprüfen der Gruppenmitgliedschaften eines Benutzers

Um die Gruppenmitgliedschaften eines Benutzers zu überprüfen, führen Sie die folgenden Schritte aus:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5.  Klicken Sie auf **Alle Benutzer**.

6.  **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **klicken Sie auf die Zeile**, um ihn auszuwählen.

7.  Klicken Sie auf **Gruppen**, um zu ermitteln, in welchen Gruppen der Benutzer Mitglied ist.

### <a name="check-a-users-assigned-licenses"></a>Überprüfen der zugewiesenen Lizenzen eines Benutzers

Um die einem Benutzer zugewiesenen Lizenzen zu überprüfen, führen Sie die folgenden Schritte aus:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5.  Klicken Sie auf **Alle Benutzer**.

6.  **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **klicken Sie auf die Zeile**, um ihn auszuwählen.

7.  Klicken Sie auf **Lizenzen**, um anzuzeigen, welche Lizenzen dem Benutzer derzeit zugewiesen sind.

### <a name="assign-a-user-a-license"></a>Zuweisen einer Lizenz zu einem Benutzer 

Um einem Benutzer eine Lizenz zuzuweisen, führen Sie die folgenden Schritte aus:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4.  Klicken Sie im Navigationsmenü auf **Benutzer und Gruppen**.

5.  Klicken Sie auf **Alle Benutzer**.

6.  **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **klicken Sie auf die Zeile**, um ihn auszuwählen.

7.  Klicken Sie auf **Lizenzen**, um anzuzeigen, welche Lizenzen dem Benutzer derzeit zugewiesen sind.

8.  Klicken Sie auf die Schaltfläche **Zuweisen**.

9.  Wählen Sie in der Liste der verfügbaren Produkte **ein oder mehrere Produkte** aus.

10. **Optional:** Klicken Sie auf die Option für **Zuweisungsoptionen**, um Produkte differenziert zuzuweisen. Klicken Sie auf **OK**, wenn Sie fertig sind.

11. Klicken Sie auf die Schaltfläche **Zuweisen**, um dem Benutzer diese Lizenzen zuzuweisen.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Wenn das Problem mit diesen Problembehandlungsschritten nicht behoben wird

Öffnen Sie ein Supportticket mit den folgenden Informationen, sofern verfügbar:

-   Fehlerkorrelations-ID

-   UPN (E-Mail-Adresse des Benutzers)

-   Mandanten-ID

-   Browsertyp

-   Zeitzone und Uhrzeit/Zeitraum des Auftretens des Fehlers

-   Fiddler-Ablaufverfolgungen

## <a name="next-steps"></a>Nächste Schritte
[Bereitstellen von einmaligem Anmelden bei Ihren Apps mit dem Anwendungsproxy](application-proxy-configure-single-sign-on-with-kcd.md)
