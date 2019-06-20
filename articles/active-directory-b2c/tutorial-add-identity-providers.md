---
title: 'Tutorial: Hinzufügen von Identitätsanbietern zu Ihren Anwendungen – Azure Active Directory B2C | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Ihren Anwendungen in Azure Active Directory B2C mithilfe des Azure-Portals Identitätsanbieter hinzufügen.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 76e12dc6bf9bcb50dc58e7730f3a08dd6a9d4440
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66512193"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Tutorial: Hinzufügen von Identitätsanbietern zu Ihren Anwendungen in Azure Active Directory B2C

Sie können Benutzern in Ihren Anwendungen die Anmeldung mit verschiedenen Identitätsanbietern ermöglichen. Ein *Identitätsanbieter* erstellt und verwaltet die Identitätsinformationen und stellt gleichzeitig Authentifizierungsdienste für Anwendungen bereit. Sie können die von Azure Active Directory (Azure AD) B2C unterstützten Identitätsanbieter über das Azure-Portal Ihren [Benutzerflows](active-directory-b2c-reference-policies.md) hinzufügen.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Erstellen der Identitätsanbieteranwendungen
> * Hinzufügen der Identitätsanbieter zu Ihrem Mandanten
> * Hinzufügen der Identitätsanbieter zu Ihrem Benutzerflow

In der Regel verwenden Sie nur einen Identitätsanbieter in Ihren Anwendungen, Sie haben aber die Möglichkeit, weitere hinzuzufügen. In diesem Tutorial erfahren Sie, wie Sie Ihrer Anwendung Azure AD und Facebook als Identitätsanbieter hinzufügen. Das Hinzufügen dieser beiden Identitätsanbieter zu Ihrer Anwendung ist optional. Sie können auch andere Identitätsanbieter wie z.B. [Amazon](active-directory-b2c-setup-amzn-app.md), [GitHub](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md), [LinkedIn](active-directory-b2c-setup-li-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md) oder [Twitter](active-directory-b2c-setup-twitter-app.md) hinzufügen. 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

[Erstellen Sie einen Benutzerflow](tutorial-create-user-flows.md), damit sich Benutzer bei Ihrer Anwendung registrieren und anmelden können. 

## <a name="create-applications"></a>Erstellen von Anwendungen

Identitätsanbieteranwendungen stellen die ID und den Schlüssel für die Kommunikation mit Ihrem Azure AD B2C-Mandanten bereit. In diesem Abschnitt des Tutorials erstellen Sie eine Azure AD-Anwendung und eine Facebook-Anwendung, von denen Sie IDs und Schlüssel abrufen, um Ihrem Mandanten die Identitätsanbieter hinzuzufügen. Wenn Sie nur einen der Identitätsanbieter hinzufügen, müssen Sie nur die Anwendung für diesen Anbieter erstellen.

### <a name="create-an-azure-active-directory-application"></a>Erstellen einer Azure Active Directory-Anwendung

Um die Anmeldung für Benutzer von Azure AD zu aktivieren, müssen Sie eine Anwendung im Azure AD-Mandanten registrieren. Der Azure AD-Mandant ist nicht mit Ihrem Azure AD B2C-Mandanten identisch.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das Verzeichnis auswählen, das Ihren Azure AD-Mandanten enthält.
3. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **App-Registrierungen**, und wählen Sie dann diese Option aus.
4. Wählen Sie **Registrierung einer neuen Anwendung** aus.
5. Geben Sie einen Namen für Ihre Anwendung ein. Beispiel: `Azure AD B2C App`.
6. Wählen Sie als **Anwendungstyp** die Option `Web app / API` aus.
7. Geben Sie als **Anmelde-URL** die folgende URL in Kleinbuchstaben ein, und ersetzen Sie dabei `your-B2C-tenant-name` durch den Namen Ihres Azure AD B2C-Mandanten.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```
    
    Beispiel: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
    
    Alle URLs sollten jetzt [b2clogin.com](b2clogin.md) verwenden.

8. Klicken Sie auf **Create**. Kopieren Sie die **Anwendungs-ID** zur späteren Verwendung.
9. Wählen Sie die Anwendung und dann **Einstellungen** aus.
10. Wählen Sie **Schlüssel** aus, geben Sie die Schlüsselbeschreibung ein, wählen Sie eine Dauer aus, und klicken Sie dann auf **Speichern**. Kopieren Sie den Wert des Schlüssels, da Sie ihn später in diesem Tutorial verwenden.

### <a name="create-a-facebook-application"></a>Erstellen einer Facebook-Anwendung

Um ein Facebook-Konto als Identitätsanbieter in Azure AD B2C verwenden zu können, müssen Sie eine Anwendung bei Facebook erstellen. Wenn Sie noch über kein Facebook-Konto verfügen, können Sie unter [https://www.facebook.com/](https://www.facebook.com/) eines erstellen.

1. Melden Sie sich auf der [Facebook-Entwickler-Website](https://developers.facebook.com/) mit den Anmeldeinformationen für Ihr Facebook-Konto an.
2. Wenn Sie dies noch nicht getan haben, müssen Sie sich als Facebook-Entwickler registrieren. Wählen Sie für die Registrierung rechts oben auf der Seite die Option **Register** (Registrieren) aus, akzeptieren die Facebook-Richtlinien und führen die Registrierungsschritte aus.
3. Wählen Sie **My Apps** (Meine Apps) aus, und klicken Sie dann auf **Add a New App** (Neue App hinzufügen). 
4. Geben Sie unter **Display Name** einen Anzeigenamen und unter **Contact Email** (Kontakt-E-Mail) eine gültige E-Mail-Adresse ein.
5. Klicken Sie auf **Create App ID** (App-ID erstellen). Sie müssen eventuell die Richtlinien für die Facebook-Plattform akzeptieren und eine Online-Sicherheitsüberprüfung durchführen.
6. Wählen Sie **Settings** > **Basic** (Einstellungen > Allgemeines) aus.
7. Wählen Sie eine **Kategorie**, z.B. `Business and Pages`. Dieser Wert ist für Facebook erforderlich, wird aber nicht für Azure AD B2C verwendet.
8. Wählen Sie unten auf der Seite die Option **Add Platform** (Plattform hinzufügen) und dann **Website** aus.
9. Geben Sie für **Website-URL** `https://your-tenant-name.b2clogin.com/` ein, und ersetzen Sie `your-tenant-name` durch Ihren Mandantennnamen. Geben Sie unter **Privacy Policy URL** (Datenrichtlinien-URL) eine URL ein, z.B. `http://www.contoso.com`. Die Richtlinien-URL ist eine von Ihnen verwaltete Seite, die Datenschutzinformationen zu Ihrer Anwendung enthält.
10. Klicken Sie auf **Save changes** (Änderungen speichern).
11. Kopieren Sie im oberen Bereich der Seite den Wert von **App ID** (App-ID). 
12. Klicken Sie auf **Show**, und kopieren Sie den Wert unter **App Secret**. Sie benötigen beide Angaben, um Facebook als Identitätsanbieter in Ihrem Mandanten zu konfigurieren. **App Secret** handelt es sich um eine wichtige Sicherheitsinformation für die Anmeldung.
13. Wählen Sie **Products** (Produkte) und dann **Set up** (Einrichten) unter **Facebook Login** (Facebook-Anmeldung) aus.
14. Wählen Sie unter **Facebook Login** (Facebook-Anmeldung) die Option **Settings** (Einstellungen) aus.
15. Geben Sie `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` unter **Valid OAuth redirect URIs** (Gültige OAuth-Umleitungs-URIs) ein. Ersetzen Sie `your-tenant-name` durch den Namen Ihres Mandanten. Klicken Sie unten auf der Seite auf **Save Changes**.
16. Um Ihre Facebook-Anwendung in Azure AD B2C verfügbar zu machen, klicken Sie rechts oben auf der Seite auf den **Statusselektor**, und schalten Sie ihn auf **Ein** um. Klicken Sie auf **Confirm** (Bestätigen). An diesem Punkt sollte sich der Status von **Entwicklung** in **Live** ändern.

## <a name="add-the-identity-providers"></a>Hinzufügen der Identitätsanbieter

Nach der Erstellung der Anwendung für den betreffenden Identitätsanbieter fügen Sie den Identitätsanbieter Ihrem Mandanten hinzu.

### <a name="add-the-azure-active-directory-identity-provider"></a>Hinzufügen von Azure Active Directory als Identitätsanbieter

1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen, das Ihren Azure AD B2C-Mandanten enthält.
2. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
3. Wählen Sie **Identitätsanbieter** und dann **Hinzufügen** aus.
4. Geben Sie einen **Namen** ein. Geben Sie beispielsweise *Contoso Azure AD* ein.
5. Klicken Sie auf **Identitätsanbietertyp**, wählen Sie **OpenID Connect (Vorschau)** aus, und klicken Sie dann auf **OK**.
6. Klicken Sie auf **Diesen Identitätsanbieter einrichten**.
7. Geben Sie für **Metadaten-URL** die folgende URL ein, in der `your-AD-tenant-domain` durch den Domänennamen Ihres Azure AD-Mandanten ersetzt wird.

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Beispiel: `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

8. Geben Sie für die **Client-ID** die Anwendungs-ID und für **Clientgeheimnis** den Schlüsselwert ein, die Sie beide zuvor notiert haben.
9. Geben Sie optional einen Wert für **Domänenhinweis** ein. Beispiel: `ContosoAD`. 
10. Klicken Sie auf **OK**.
11. Wählen Sie **Ansprüche dieses Identitätsanbieters zuordnen** aus, und legen Sie die folgenden Ansprüche fest:
    
    - Geben Sie für **Benutzer-ID** `oid` ein.
    - Geben Sie für **Anzeigename** `name` ein.
    - Geben Sie für **Vorname** `given_name` ein.
    - Geben Sie für **Nachname** `family_name` ein.
    - Geben Sie für **E-Mail** `unique_name` ein.

12. Klicken Sie auf **OK** und dann auf **Erstellen**, um die Konfiguration zu speichern.

### <a name="add-the-facebook-identity-provider"></a>Hinzufügen von Facebook als Identitätsanbieter

1. Wählen Sie **Identitätsanbieter** und dann **Hinzufügen** aus.
2. Geben Sie einen **Namen** ein. Geben Sie z.B. *Facebook* ein.
3. Wählen Sie **Identitätsanbietertyp** und dann **Facebook** aus, und klicken Sie auf **OK**.
4. Wählen Sie **Diesen Identitätsanbieter einrichten** aus, und geben Sie die App-ID, die Sie zuvor notiert haben, als **Client-ID** ein. Geben Sie das App-Geheimnis, das Sie zuvor notiert haben, als **Clientgeheimnis** ein.
5. Klicken Sie auf **OK** und dann auf **Erstellen**, um die Facebook-Konfiguration zu speichern.

## <a name="update-the-user-flow"></a>Aktualisieren des Benutzerflows

In dem Tutorial, das Sie im Rahmen der Voraussetzungen abgeschlossen haben, haben Sie einen Benutzerflow mit dem Namen *B2C_1_signupsignin1* für die Registrierung und Anmeldung erstellt. In diesem Abschnitt fügen Sie die Identitätsanbieter dem Benutzerflow *B2C_1_signupsignin1* hinzu.

1. Wählen Sie **Benutzerflows (Richtlinien)** und dann den Benutzerflow *B2C_1_signupsignin1* aus.
2. Wählen Sie **Identitätsanbieter** und dann die von Ihnen hinzugefügten Identitätsanbieter **Facebook** und **Contoso Azure AD** aus.
3. Wählen Sie **Speichern** aus.

### <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Wählen Sie auf der Übersichtsseite des erstellten Benutzerflows die Option **Benutzerflow ausführen** aus.
2. Wählen Sie für **Anwendung** die Webanwendung *webapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
3. Klicken Sie auf **Benutzerflow ausführen**, und melden Sie sich mit dem zuvor hinzugefügten Identitätsanbieter an.
4. Wiederholen Sie die Schritte 1 bis 3 für andere Identitätsanbieter, die Sie hinzugefügt haben.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen der Identitätsanbieteranwendungen
> * Hinzufügen der Identitätsanbieter zu Ihrem Mandanten
> * Hinzufügen der Identitätsanbieter zu Ihrem Benutzerflow

> [!div class="nextstepaction"]
> [Anpassen der Benutzeroberfläche Ihrer Anwendungen in Azure Active Directory B2C](tutorial-customize-ui.md)