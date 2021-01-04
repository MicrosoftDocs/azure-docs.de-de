---
title: Beheben von Anmeldeproblemen bei einer Anwendung aus „Meine Apps“ in Azure AD
description: Beheben von Anmeldeproblemen bei einer Anwendung aus „Meine Apps“ in Azure AD
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperfq2
ms.openlocfilehash: 8cbc683f06b809ec4d9c63a61d73a0c731a92cd7
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651617"
---
# <a name="troubleshoot-problems-signing-in-to-an-application-from-azure-ad-my-apps"></a>Beheben von Anmeldeproblemen bei einer Anwendung aus „Meine Apps“ in Azure AD

„Meine Apps“ ist ein webbasiertes Portal, über das Benutzer mit einem Geschäfts-, Schul- oder Unikonto in Azure Active Directory (Azure AD) cloudbasierte Anwendungen anzeigen und starten können, für die der Azure AD-Administrator ihnen Zugriff gewährt hat. Der Zugriff auf „Meine Apps“ erfolgt mithilfe eines Webbrowsers unter [https://myapps.microsoft.com](https://myapps.microsoft.com).

Weitere Informationen dazu, wie Sie Azure AD als Identitätsanbieter für eine App verwenden können, finden Sie unter [Was ist die Anwendungsverwaltung in Azure AD?](what-is-application-management.md). Wenn Sie schnell beginnen möchten, sehen Sie sich die [Schnellstartserie zur Anwendungsverwaltung](view-applications-portal.md) an.

Diese Anwendungen werden im Namen des Benutzers im Azure AD-Portal konfiguriert. Die Anwendung muss ordnungsgemäß konfiguriert und dem Benutzer oder einer Gruppe, deren Mitglied er ist, zugewiesen sein, damit sie in „Meine Apps“ angezeigt wird. 

Es gibt drei Kategorien von Apps, die für einen Benutzer angezeigt werden können:
-   Microsoft 365-Anwendungen
-   Anwendungen von Microsoft und Drittanbietern, für die verbundbasiertes SSO konfiguriert ist
-   Anwendungen mit kennwortbasiertem SSO
-   Anwendungen mit vorhandenen SSO-Lösungen

Hier sind einige Aspekte zur Überprüfung, ob eine App angezeigt wird oder nicht.
- Stellen Sie sicher, dass die App Azure AD hinzugefügt und der Benutzer zugewiesen wird. Weitere Informationen finden Sie in der [Schnellstartserie zur Anwendungsverwaltung](add-application-portal.md).
- Wenn eine App kürzlich hinzugefügt wurde, muss sich der Benutzer abmelden und erneut anmelden. 
- Wenn die App eine Lizenz (z. B. Office) erfordert, stellen Sie sicher, dass dem Benutzer die entsprechende Lizenz zugewiesen wird.
- Die Zeit, die für Lizenzierungsänderungen benötigt wird, kann je nach Größe und Komplexität der Gruppe variieren.

## <a name="general-issues-to-check-first"></a>Allgemeine Probleme, die zuerst überprüft werden sollten

-   Stellen Sie sicher, dass der Webbrowser die Anforderungen erfüllt, und lesen Sie dazu [Meine Apps: Unterstützte Browser](../user-help/my-apps-portal-end-user-access.md).
-   Stellen Sie sicher, dass die URL der Anwendung den **vertrauenswürdigen Sites** im Browser des Benutzers hinzugefügt wurde.
-   Stellen Sie sicher, dass die Anwendung ordnungsgemäß **konfiguriert** ist.
-   Stellen Sie sicher, dass das Konto des Benutzers für Anmeldungen **aktiviert** ist.
-   Stellen Sie sicher, dass das Konto des Benutzers **nicht gesperrt ist**.
-   Stellen Sie sicher, dass **Kennwort des Benutzers nicht abgelaufen ist oder vergessen wurde**.
-   Stellen Sie sicher, dass Benutzerzugriff nicht durch **Multi-Factor Authentication** blockiert wird.
-   Stellen Sie sicher, dass der Benutzerzugriff nicht durch eine **Richtlinie für bedingten Zugriff** oder eine **Identity Protection**-Richtlinie blockiert wird.
-   Stellen Sie sicher, dass die **Kontaktinformationen für die Authentifizierung** des Benutzers aktuell sind, sodass Multi-Factor Authentication-Richtlinien oder Richtlinien für bedingten Zugriff erzwungen werden können.
-   Stellen Sie sicher, dass der Benutzer die Cookies im Browser gelöscht hat, bevor er versucht, sich erneut anzumelden.

## <a name="problems-with-the-users-account"></a>Probleme mit dem Konto des Benutzers
Der Zugriff auf „Meine Apps“ kann aufgrund eines Problems mit dem Benutzerkonto blockiert sein. Nachfolgend finden Sie einige Möglichkeiten zum Lösen von Problemen mit Benutzerkonten und Kontoeinstellungen:
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
4.  Wählen Sie im Navigationsmenü **Benutzer und Gruppen** aus.
5.  Wählen Sie **Alle Benutzer**.
6.  **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **wählen Sie die entsprechende Zeile aus**.
7.  Überprüfen Sie die Eigenschaften des Benutzerobjekts, um sicherzustellen, dass diese ordnungsgemäß festgelegt sind und keine Daten fehlen.

### <a name="check-a-users-account-status"></a>Überprüfen des Status eines Benutzerkontos
Um den Status eines Benutzerkontos zu überprüfen, führen Sie die folgenden Schritte aus:
1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.
2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.
3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.
4.  Wählen Sie im Navigationsmenü **Benutzer und Gruppen** aus.
5.  Wählen Sie **Alle Benutzer**.
6.  **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **wählen Sie die entsprechende Zeile aus**.
7.  Wählen Sie **Profil** aus.
8.  Stellen Sie unter **Einstellungen** sicher, dass die Option **Anmeldung blockieren** auf **Nein** festgelegt ist.

### <a name="reset-a-users-password"></a>Zurücksetzen des Kennworts für einen Benutzer
Um das Kennwort eines Benutzers zurückzusetzen, führen Sie die folgenden Schritte aus:
1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.
2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.
3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.
4.  Wählen Sie im Navigationsmenü **Benutzer und Gruppen** aus.
5.  Wählen Sie **Alle Benutzer**.
6.  **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **wählen Sie die entsprechende Zeile aus**.
7.  Wählen Sie oben im Benutzerbereich die Schaltfläche **Kennwort zurücksetzen** aus.
8.  Wählen Sie im jetzt angezeigten Bereich **Kennwort zurücksetzen** die Schaltfläche **Kennwort zurücksetzen** aus.
9.  Kopieren Sie das **temporäre Kennwort**, oder **geben Sie ein neues Kennwort ein**.
10. Teilen Sie dem Benutzer das neue Kennwort mit, da dieser das Kennwort bei der nächsten Anmeldung bei Azure Active Directory ändern muss.

### <a name="enable-self-service-password-reset"></a>Aktivieren der Self-Service-Kennwortzurücksetzung
Um die Self-Service-Kennwortzurücksetzung zu aktivieren, führen Sie die folgenden Bereitstellungsschritte aus:
-   [Aktivieren von Benutzern für das Zurücksetzen ihrer Azure Active Directory-Kennwörter](../authentication/tutorial-enable-sspr.md)
-   [Aktivieren von Benutzern für das Zurücksetzen oder Ändern ihrer lokalen Active Directory-Kennwörter](../authentication/tutorial-enable-sspr.md)

### <a name="check-a-users-multi-factor-authentication-status"></a>Überprüfen des Multi-Factor Authentication-Status eines Benutzers
Um den Multi-Factor Authentication-Status eines Benutzers zu überprüfen, führen Sie die folgenden Schritte aus:
1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.
2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.
3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.
4. Wählen Sie im Navigationsmenü **Benutzer und Gruppen** aus.
5. Wählen Sie **Alle Benutzer**.
6. Wählen Sie oben im Bereich die Schaltfläche **Multi-Factor Authentication** aus.
7. Wenn das **Multi-Factor Authentication-Verwaltungsportal** geöffnet ist, stellen Sie sicher, dass Sie sich auf der Registerkarte **Benutzer** befinden.
8. Suchen Sie den Benutzer, indem Sie die Liste der Benutzer durchsuchen, filtern oder sortieren.
9. Wählen Sie den Benutzer aus der Benutzerliste aus, und wählen Sie die gewünschte Option für die Multi-Factor Authentication: **Aktivieren**, **Deaktivieren** oder **Erzwingen**.
   >[!NOTE]
   >Wenn ein Benutzer sich im Status **Erzwungen** befindet, können Sie den Status vorübergehend auf **Deaktiviert** festlegen, damit der Benutzer sich wieder bei seinem Konto anmelden kann. Sobald der Benutzer wieder bei seinem Konto angemeldet ist, können Sie den Status in **Aktiviert** ändern, damit der Benutzer bei der nächsten Anmeldung seine Kontaktinformationen erneut registrieren muss. Alternativ können Sie Schritte unter [Überprüfen der Kontaktinformationen für die Authentifizierung eines Benutzers](#check-a-users-authentication-contact-info) ausführen, um diese Daten für den Benutzer zu überprüfen oder festzulegen.

### <a name="check-a-users-authentication-contact-info"></a>Überprüfen der Kontaktinformationen für die Authentifizierung eines Benutzers
Um die Kontaktinformationen zur Authentifizierung des Benutzers zu überprüfen, die für Multi-Factor Authentication, bedingten Zugriff, Identity Protection und Kennwortzurücksetzung verwendet werden, führen Sie die folgenden Schritte aus:
1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.
2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.
3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.
4.  Wählen Sie im Navigationsmenü **Benutzer und Gruppen** aus.
5.  Wählen Sie **Alle Benutzer**.
6.  **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **wählen Sie die entsprechende Zeile aus**.
7.  Wählen Sie **Profil** aus.
8.  Scrollen Sie nach unten zu **Kontaktinformationen für Authentifizierung**.
9.  **Überprüfen** Sie die für den Benutzer registrierten Daten, und aktualisieren Sie sie bei Bedarf.

### <a name="check-a-users-group-memberships"></a>Überprüfen der Gruppenmitgliedschaften eines Benutzers
Um die Gruppenmitgliedschaften eines Benutzers zu überprüfen, führen Sie die folgenden Schritte aus:
1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.
2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.
3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.
4.  Wählen Sie im Navigationsmenü **Benutzer und Gruppen** aus.
5.  Wählen Sie **Alle Benutzer**.
6.  **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **wählen Sie die entsprechende Zeile aus**.
7.  Wählen Sie **Gruppen** aus, um zu ermitteln, in welchen Gruppen der Benutzer Mitglied ist.

### <a name="check-a-users-assigned-licenses"></a>Überprüfen der zugewiesenen Lizenzen eines Benutzers
Um die einem Benutzer zugewiesenen Lizenzen zu überprüfen, führen Sie die folgenden Schritte aus:
1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.
2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.
3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.
4.  Wählen Sie im Navigationsmenü **Benutzer und Gruppen** aus.
5.  Wählen Sie **Alle Benutzer**.
6.  **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **wählen Sie die entsprechende Zeile aus**.
7.  Wählen Sie **Lizenzen** aus, um anzuzeigen, welche Lizenzen dem Benutzer zurzeit zugewiesen sind.

### <a name="assign-a-user-a-license"></a>Zuweisen einer Lizenz zu einem Benutzer 
Um einem Benutzer eine Lizenz zuzuweisen, führen Sie die folgenden Schritte aus:
1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.
2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.
3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.
4.  Wählen Sie im Navigationsmenü **Benutzer und Gruppen** aus.
5.  Wählen Sie **Alle Benutzer**.
6.  **Suchen** Sie nach dem Benutzer, an dem Sie interessiert sind, und **wählen Sie die entsprechende Zeile aus**.
7.  Wählen Sie **Lizenzen** aus, um anzuzeigen, welche Lizenzen dem Benutzer zurzeit zugewiesen sind.
8.  Wählen Sie die Schaltfläche **Zuweisen** aus.
9.  Wählen Sie in der Liste der verfügbaren Produkte **ein oder mehrere Produkte** aus.
10. **Optional:** Wählen Sie das Element **Zuweisungsoptionen** aus, um Produkte differenziert zuzuweisen. Klicken Sie auf **OK**.
11. Wählen Sie die Schaltfläche **Zuweisen** aus, um dem Benutzer diese Lizenzen zuzuweisen.

## <a name="troubleshooting-deep-links"></a>Problembehandlung bei Deeplinks
Deeplinks oder Benutzerzugriffs-URLs sind Links, mit deren Hilfe Ihre Benutzer direkt über die URL-Leiste ihrer Browser auf ihre Kennwort-SSO-Anwendungen zugreifen können. Durch Navigieren zu diesem Link werden Benutzer bei der Anwendung automatisch angemeldet, ohne zuerst zu „Meine Apps“ wechseln zu müssen. Dieser Link ist derselbe Link, mit dem Benutzer über den Microsoft 365-Anwendungsstarter auf diese Anwendungen zugreifen.

### <a name="checking-the-deep-link"></a>Überprüfen des Deeplinks

Wenn Sie überprüfen möchten, ob Sie über den richtigen Deeplink verfügen, führen Sie die folgenden Schritte aus:
1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.
2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.
3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.
4. Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.
5. Wählen Sie **Alle Anwendungen**, um eine Liste mit Ihren Anwendungen anzuzeigen.
   * Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.
6. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.
7. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.
8. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.
9. Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.
10. Wählen Sie **Alle Anwendungen**, um eine Liste mit Ihren Anwendungen anzuzeigen.
    * Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.
11. Wählen Sie die Anwendung aus, bei der Sie den Deeplink überprüfen möchten.
12. Suchen Sie die Bezeichnung **URL für den Benutzerzugriff**. Ihr Deeplink sollte mit dieser URL übereinstimmen.

## <a name="contact-support"></a>Support kontaktieren
Öffnen Sie ein Supportticket mit den folgenden Informationen, sofern verfügbar:
-   Fehlerkorrelations-ID
-   UPN (E-Mail-Adresse des Benutzers)
-   Mandanten-ID
-   Browsertyp
-   Zeitzone und Uhrzeit/Zeitraum des Auftretens des Fehlers
-   Fiddler-Ablaufverfolgungen

## <a name="next-steps"></a>Nächste Schritte
- [Schnellstartserie zur Anwendungsverwaltung](view-applications-portal.md)