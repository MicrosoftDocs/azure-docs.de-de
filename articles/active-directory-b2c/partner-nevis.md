---
title: Tutorial zum Konfigurieren von Azure Active Directory B2C mit Nevis
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie die Azure AD B2C-Authentifizierung mit Nevis für die kennwortlose Authentifizierung integrieren.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/23/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 470dc146d1658b97a707be8cb7ad868d943a8e60
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96170894"
---
# <a name="tutorial-to-configure-nevis-with-azure-active-directory-b2c-for-passwordless-authentication"></a>Tutorial zum Konfigurieren von Nevis mit Azure Active Directory B2C für die kennwortlose Authentifizierung

In diesem Beispieltutorial erfahren Sie, wie Sie Azure AD B2C mit [Nevis](https://www.nevis.net/solution/authentication-cloud) erweitern, um die kennwortlose Authentifizierung zu aktivieren. Nevis bietet mit der Nevis Access-App eine vollständig markentreue Endbenutzerumgebung an, die auf Mobilgeräte abzielt. So wird eine starke Kundenauthentifizierung bereitgestellt, die den Transaktionsanforderungen der Zahlungsdiensterichtlinie 2 (Payment Services Directive 2, PSD2) entspricht.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Nevis-[Testkonto](https://www.nevis-security.com/aadb2c/)

- Ein Azure AD-Abonnement Falls Sie noch kein Konto haben, können Sie eine [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.

- Einen [Azure AD B2C-Mandanten](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), der mit Ihrem Azure-Abonnement verknüpft ist

- Eine für die Verwendung von [benutzerdefinierten Richtlinien](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started) konfigurierte Azure AD B2C-Umgebung, wenn Sie Nevis in Ihren Registrierungsrichtlinienflow integrieren möchten.

## <a name="scenario-description"></a>Beschreibung des Szenarios

Fügen Sie in diesem Szenario Ihrer Back-End-Anwendung eine Zugriffs-App mit vollständigem Branding für die kennwortlose Authentifizierung hinzu. Die Lösung besteht aus den folgenden Komponenten:

- Ein Azure AD B2C-Mandant mit einer kombinierten Richtlinie für Anmeldung und Registrierung für Ihr Back-End
- Eine Nevis-Instanz mit REST-API zur Erweiterung von Azure AD B2C
- Ihre eigene Zugriffs-App mit Branding

Diagramm der Implementierung

![Allgemeiner Ablauf bei der Anmeldung per Kennwort mit Azure AD B2C und Nevis](./media/partner-nevis/nevis-architecture-diagram.png)

|Schritt | BESCHREIBUNG |
|:-----| :-----------|
| 1. | Ein Benutzer versucht, sich über eine Azure AD B2C-Richtlinie für die Anmeldung und Registrierung bei einer Anwendung anzumelden oder zu registrieren.
| 2. | Während der Registrierung wird die Nevis Access-App mithilfe eines QR-Codes auf dem Benutzergerät registriert. Ein privater Schlüssel wird auf dem Benutzergerät generiert und zum Signieren der Benutzeranforderungen verwendet.
| 3. |  Azure AD B2C verwendet ein technisches RESTful-Profil, um die Anmeldung mit der Nevis-Lösung zu starten.
| 4. | Die Anmeldeanforderung wird als Pushbenachrichtigung, als QR-Code oder als Deep-Link an die Zugriffs-App gesendet.
| 5. | Der Benutzer genehmigt den Anmeldeversuch mit seinen Biometriedaten. Anschließend wird eine Nachricht an Nevis zurückgegeben, damit die Anmeldung mit dem gespeicherten öffentlichen Schlüssel überprüft wird.
| 6. | Azure AD B2C sendet eine letzte Anforderung an Nevis, um zu bestätigen, dass die Anmeldung erfolgreich abgeschlossen wurde.
| 7. |Basierend auf der Erfolgs- bzw. Fehlermeldung von Azure AD B2C wird dem Benutzer der Zugriff auf die Anwendung gewährt oder verweigert.

## <a name="integrate-your-azure-ad-b2c-tenant"></a>Integrieren Ihres Azure AD B2C-Mandanten

### <a name="onboard-to-nevis"></a>Integrieren mit Nevis 

[Registrieren Sie sich für ein Nevis-Konto.](https://www.nevis-security.com/aadb2c/)
Sie erhalten zwei E-Mails:

1. Eine Benachrichtigung vom Verwaltungskonto

2. Eine Einladung für die mobile App

### <a name="add-your-azure-ad-b2c-tenant-to-your-nevis-account"></a>Hinzufügen Ihres Azure AD B2C-Mandanten zu Ihrem Nevis-Konto

1. Kopieren Sie den Verwaltungsschlüssel aus der E-Mail von Nevis für das Testkonto in die Zwischenablage.

2. Öffnen Sie die URL https://console.nevis.cloud/ in einem Browser.

3. Melden Sie sich mit Ihrem Schlüssel bei der Verwaltungskonsole an.

4. Wählen Sie **Add Instance** (Instanz hinzufügen) aus.

5. Wählen Sie die neu erstellte Instanz aus, um sie zu öffnen.

6. Wählen Sie auf der seitlichen Navigationsleiste **Custom Integrations** (Benutzerdefinierte Integrationen) aus.

7. Wählen Sie **Add custom integration** (Benutzerdefinierte Integration hinzufügen) aus.

8. Geben Sie als Namen für die Integration Ihren Azure AD B2C-Mandantennamen ein.

9. Geben Sie als URL/Domäne `https://yourtenant.onmicrosoft.com` ein.

10. Wählen Sie **Weiter** aus.

>[!NOTE]
>Sie benötigen das Nevis-Zugriffstoken später.

11. Wählen Sie **Fertig** aus.

### <a name="install-the-nevis-access-app-on-your-phone"></a>Installieren der Nevis Access-App auf Ihrem Smartphone

1. Öffnen Sie in der E-Mail von Nevis mit der Einladung für die mobile App die Einladung **Test Flight app** (Test-App).

2. Installieren Sie die App.

3. Befolgen Sie die Anweisungen zum Installieren der Nevis Access-App.

### <a name="integrate-azure-ad-b2c-with-nevis"></a>Integrieren von Azure AD B2C mit Nevis

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/).

2. Wechseln Sie zu Ihrem Azure AD B2C-Mandanten. Achten Sie darauf, den richtigen Mandanten auszuwählen, da sich der Azure AD B2C-Mandant in der Regel in einem separaten Mandanten befindet.

3. Wählen Sie im Menü **Identity Experience Framework (IEF)** aus.

4. Auswählen von **Richtlinienschlüssel**

5. Wählen Sie **Hinzufügen** aus, und erstellen Sie einen neuen Schlüssel mit den folgenden Einstellungen:

      a. Wählen Sie unter „Optionen“ den Eintrag **Manuell** aus.

      b. Legen Sie den Namen auf **AuthCloudAccessToken** fest.

      c. Fügen Sie das zuvor gespeicherte **Nevis-Zugriffstoken** in das Feld „Geheimnis“ ein.

      d. Wählen Sie für „Schlüsselverwendung“ die Option **Verschlüsselung** aus.

      e. Klicken Sie auf **Erstellen**.

### <a name="configure-and-upload-the-nevishtml-to-azure-blob-storage"></a>Konfigurieren und Hochladen der Datei „nevis.html“ in Azure Blob Storage

1. Wechseln Sie in Ihrer Identitätsumgebung (IDE) zum Ordner [**Richtlinien**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy).

2. Öffnen Sie die Datei [**nevis.html**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/nevis.html).

3. Ersetzen Sie **authentication_cloud_url** durch die URL Ihrer Nevis-Administratorkonsole: `https://<instance_id>.mauth.nevis.cloud`.

4. **Speichern** Sie die Änderungen an der Datei.

5. Befolgen Sie die [Anweisungen](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#2-create-an-azure-blob-storage-account), und laden Sie die Datei **nevis.html** in Ihre Azure Blob Storage-Instanz hoch.

6. Befolgen Sie die [Anweisungen](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#3-configure-cors), und aktivieren Sie CORS (Cross-Origin Resource Sharing) für diese Datei.

7. Wenn der Upload vollständig und CORS aktiviert ist, wählen Sie die Datei **nevis.html** in der Liste aus.

8. Wählen Sie auf der Registerkarte **Übersicht** neben der **URL** das Symbol **Link kopieren** aus.

9. Öffnen Sie den Link auf einer neuen Browserregisterkarte, um sicherzustellen, dass ein graues Feld angezeigt wird.

>[!NOTE]
>Sie benötigen den Bloblink zu einem späteren Zeitpunkt.

### <a name="customize-your-trustframeworkbasexml"></a>Anpassen Ihrer Datei „TrustFrameworkBase.xml“

1. Navigieren Sie in Ihrer IDE zum Ordner [**Richtlinien**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy).

2. Öffnen Sie die Datei [**TrustFrameworkBase.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkBase.xml).

3. Ersetzen Sie **yourtenant** durch den Kontonamen Ihres Azure-Mandanten unter **TenantId**.

4. Ersetzen Sie **yourtenant** durch den Kontonamen Ihres Azure-Mandanten unter **PublicPolicyURI**.

5. Ersetzen Sie alle Vorkommen von **authentication_cloud_url** durch die URL Ihrer Nevis-Administratorkonsole.

6. **Speichern** Sie die Änderungen an der Datei.

### <a name="customize-your-trustframeworkextensionsxml"></a>Anpassen der Datei „TrustFrameworkExtensions.xml“

1. Navigieren Sie in Ihrer IDE zum Ordner [**Richtlinien**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy).

2. Öffnen Sie die Datei [**TrustFrameworkExtensions.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkExtensions.xml).

3. Ersetzen Sie **yourtenant** durch den Kontonamen Ihres Azure-Mandanten unter **TenantId**.

4. Ersetzen Sie **yourtenant** durch den Kontonamen Ihres Azure-Mandanten unter **PublicPolicyURI**.

5. Ersetzen Sie unter **BasePolicy** in **TenantId** ebenfalls _yourtenant_ durch den Kontonamen Ihres Azure-Mandanten.

6. Ersetzen Sie unter **BuildingBlocks** den **LoadUri** durch die Bloblink-URL Ihrer Datei _nevis.html_ in Ihrem Blob Storage-Konto.

7. **Speichern** Sie die Datei.

### <a name="customize-your-signuporsigninxml"></a>Anpassen der Datei „SignUpOrSignin.xml“

1. Navigieren Sie in Ihrer IDE zum Ordner [**Richtlinien**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy).

2. Öffnen Sie die Datei [**SignUpOrSignin.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/SignUpOrSignin.xml).

3. Ersetzen Sie **yourtenant** durch den Kontonamen Ihres Azure-Mandanten unter **TenantId**.

4. Ersetzen Sie **yourtenant** durch den Kontonamen Ihres Azure-Mandanten unter **PublicPolicyUri**.

5. Ersetzen Sie unter **BasePolicy** in **TenantId** ebenfalls **yourtenant** durch den Kontonamen Ihres Azure-Mandanten.

6. **Speichern** Sie die Datei.

### <a name="upload-your-custom-policies-to-azure-ad-b2c"></a>Hochladen Ihrer benutzerdefinierten Richtlinien nach Azure AD B2C

1. Öffnen Sie die Startseite Ihres [Azure AD B2C-Mandanten](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview).

2. Wählen Sie **Framework für die Identitätsfunktion** aus.

3. Wählen Sie **Benutzerdefinierte Richtlinie hochladen** aus.

4. Wählen Sie die Datei **TrustFrameworkBase.xml** aus, die Sie geändert haben.

5. Aktivieren Sie das Kontrollkästchen **Overwrite the custom policy if it already exists** (Benutzerdefinierte Richtlinie überschreiben, sofern vorhanden).
6. Wählen Sie die Option **Hochladen**.

7. Wiederholen Sie die Schritte 5 und 6 für **TrustFrameworkExtensions.xml**.

8. Wiederholen Sie die Schritte 5 und 6 für **SignUpOrSignin.xml**.

## <a name="test-the-user-flow"></a>Testen des Benutzerflows

### <a name="test-account-creation-and-nevis-access-app-setup"></a>Testen der Kontoerstellung und der Einrichtung der Nevis Access-App

1. Öffnen Sie die Startseite Ihres [Azure AD B2C-Mandanten](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview).

2. Wählen Sie **Framework für die Identitätsfunktion** aus.

3. Scrollen Sie nach unten zu den benutzerdefinierten Richtlinien, und wählen Sie **B2C_1A_signup_signin** aus.

4. Wählen Sie **Jetzt ausführen** aus.

5. Wählen Sie im Popupfenster **Jetzt registrieren** aus.

6. Fügen Sie Ihre E-Mail-Adresse hinzu.

7. Wählen Sie **Überprüfungscode senden** aus.

8. Kopieren Sie den Überprüfungscode aus der E-Mail, und fügen Sie ihn ein.

9. Wählen Sie **Überprüfen** aus.

10. Füllen Sie das Formular mit Ihrem neuen Kennwort und dem Anzeigenamen aus.

11. Klicken Sie auf **Erstellen**.

12. Sie werden zur Seite für das Scannen des QR-Codes weitergeleitet.

13. Öffnen Sie auf Ihrem Smartphone die **Nevis Access-App**.

14. Wählen Sie **Face ID** (Gesichtserkennung) aus.

15. Wenn auf dem Bildschirm **Authenticator registration was successful** (Authentifikatorregistrierung erfolgreich) angezeigt wird, wählen Sie **Continue** (Weiter) aus.

16. Authentifizieren Sie sich auf Ihrem Smartphone erneut mit Ihrem Gesicht.

17. Sie werden zur Landing Page [jwt.ms](https://jwt.ms) weitergeleitet, auf der Details zu Ihrem decodierten Token angezeigt werden.

### <a name="test-the-pure-passwordless-sign-in"></a>Testen der kennwortlosen Anmeldung

1. Wählen Sie unter **Identity Experience Framework** den Eintrag **B2C_1A_signup_signin** aus.

2. Wählen Sie **Jetzt ausführen** aus.

3. Wählen Sie im Popupfenster **Kennwortlose Authentifizierung** aus.

4. Geben Sie Ihre E-Mail-Adresse ein.

5. Wählen Sie **Weiter**.

6. Wählen Sie auf Ihrem Smartphone in den Benachrichtigungen die **Benachrichtigung der Nevis Access-App** aus.

7. Authentifizieren Sie sich mit Ihrem Gesicht.

8. Sie werden zur Landing Page [jwt.ms](https://jwt.ms) weitergeleitet, auf der Ihre Token angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Richtlinien in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
