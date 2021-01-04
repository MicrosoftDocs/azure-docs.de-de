---
title: Telefonregistrierung und -anmeldung mit benutzerdefinierten Richtlinien
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie mit benutzerdefinierten Richtlinien in Azure Active Directory B2C Einmalkennwörter (One-Time Passwords, OTP) in Textnachrichten an die Telefone Ihrer Anwendungsbenutzer senden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9d4fa80f669493e4dc47a9ad0f9bfe9390d4ab24
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953575"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c"></a>Einrichten von Telefonregistrierung und -anmeldung mit benutzerdefinierten Richtlinien in Azure AD B2C

Die Telefonregistrierung und -anmeldung in Azure Active Directory B2C (Azure AD B2C) ermöglicht es Ihren Benutzern, sich mit einem Einmalkennwort (One-Time Password, OTP) bei Ihren Anwendungen zu registrieren und anzumelden, das in einer Textnachricht an ihr Telefon gesendet wird. Mithilfe von Einmalkennwörtern können Sie das Risiko minimieren, dass Ihre Benutzer ihre Kennwörter vergessen oder versehentlich offenlegen.

Befolgen Sie die Schritte in diesem Artikel, um Ihren Kunden mithilfe benutzerdefinierter Richtlinien zu ermöglichen, sich mit einem an ihr Telefon gesendeten Einmalkennwort bei Ihren Anwendungen zu registrieren und anzumelden.

## <a name="pricing"></a>Preise

Einmalkennwörter werden mithilfe von SMS-Textnachrichten an Ihre Benutzer gesendet. Es fallen möglicherweise für jede gesendete Nachricht Gebühren an. Preisinformationen finden Sie im Abschnitt **Separate Gebühren** unter [Azure Active Directory B2C – Preise](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="user-experience-for-phone-sign-up-and-sign-in"></a>Benutzeroberfläche und Vorgang für die Telefonregistrierung und -anmeldung

Über die Telefonregistrierung und -anmeldung kann sich der Benutzer mit einer Telefonnummer als primärem Bezeichner für die App registrieren. Im Folgenden wird die Vorgehensweise bei der Registrierung und Anmeldung für Endbenutzer beschrieben.

> [!NOTE]
> Wir empfehlen Ihnen dringend, bei der Anmeldung und Registrierung Zustimmungsinformationen ähnlich wie im Beispieltext unten anzugeben. Dieser Beispieltext dient nur zu Informationszwecken. Lesen Sie das Short Code Monitoring Handbook auf der [CTIA-Website](https://www.ctia.org/programs), und wenden Sie sich an Ihre eigenen Rechts- oder Complianceexperten, um eine Anleitung für die Formulierung Ihres endgültigen Texts und für die Featurekonfiguration zur Erfüllung Ihrer eigenen Complianceanforderungen zu erhalten:
>
> *Durch die Angabe Ihrer Telefonnummer erklären Sie sich damit einverstanden, eine Einmalkennung per SMS zu erhalten, mit der Sie sich bei *&lt;Name Ihrer Anwendung einfügen&gt;* anmelden können. Möglicherweise gelten Standardnachrichten- und -datentarife.*
>
> *&lt;Einfügen: Link zu Ihrer Datenschutzerklärung&gt;*<br/>*&lt;Einfügen: Link zu Ihren Vertragsbedingungen&gt;*

Wenn Sie Ihre eigenen Zustimmungsinformationen hinzufügen möchten, passen Sie das folgende Beispiel an. Fügen Sie den entsprechenden Inhalt in die „LocalizedResources“-Elemente für die Inhaltsdefinition (ContentDefinition) ein, die von der selbstbestätigten Seite mit dem Anzeigesteuerelement verwendet wird (Datei „Phone-Email-Base.xml“ im Starter Pack für die Telefonregistrierung und -anmeldung):

```xml
<LocalizedResources Id="phoneSignUp.en">        
    <LocalizedStrings>
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_msg_intro">By providing your phone number, you consent to receiving a one-time passcode sent by text message to help you sign into {insert your application name}. Standard messsage and data rates may apply.</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_text">Privacy Statement</LocalizedString>                
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_url">{insert your privacy statement URL}</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_text">Terms and Conditions</LocalizedString>             
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_url">{insert your terms and conditions URL}</LocalizedString>          
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please verify your country code and phone number</LocalizedString>        
    </LocalizedStrings>      
</LocalizedResources>
   ```

### <a name="phone-sign-up-experience"></a>Oberfläche für die Telefonregistrierung

Wenn der Benutzer noch nicht über ein Konto für Ihre Anwendung verfügt, kann er durch Auswählen des Links **Jetzt registrieren** ein Konto erstellen. Daraufhin wird eine Registrierungsseite angezeigt. Auf dieser Seite muss der Benutzer **Land/Region** auswählen, seine Telefonnummer eingeben und **Code senden** auswählen.

![Der Benutzer startet die Telefonregistrierung.](media/phone-authentication/phone-signup-start.png)

Ein Einmalprüfcode wird an die Telefonnummer des Benutzers gesendet. Der Benutzer gibt auf der Registrierungsseite den **Prüfcode** ein und wählt dann **Code überprüfen** aus. (Wenn der Benutzer den Code nicht abrufen konnte, kann er die Option **Neuen Code senden** auswählen.)

![Überprüfung des Codes während der Telefonregistrierung durch den Benutzer](media/phone-authentication/phone-signup-verify-code.png)

 Der Benutzer gibt auf der Registrierungsseite alle weiteren erforderlichen Informationen ein, z. B. den **Anzeigenamen**, den **Vornamen** und den **Nachnamen** (das Land/die Region und die Telefonnummer sind bereits eingefügt). Wenn der Benutzer eine andere Telefonnummer verwenden möchte, kann er **Nummer ändern** auswählen, um die Registrierung neu zu starten. Wenn der Benutzer fertig ist, wählt er **Weiter** aus.

![Der Benutzer gibt zusätzliche Informationen an.](media/phone-authentication/phone-signup-additional-info.png)

Als Nächstes wird der Benutzer aufgefordert, eine E-Mail-Adresse für die Wiederherstellung anzugeben. Der Benutzer gibt seine E-Mail-Adresse ein und wählt dann **Prüfcode senden** ein. An den E-Mail-Posteingang des Benutzers wird ein Code gesendet, den der Benutzer abrufen und in das Feld **Prüfcode** eingeben kann. Anschließend wählt der Benutzer die Option **Code überprüfen** aus. 

Nachdem der Code überprüft wurde, wählt der Benutzer die Option **Erstellen** aus, um sein Konto zu erstellen. Oder wenn der Benutzer eine andere E-Mail-Adresse verwenden möchte, kann er **E-Mail-Adresse ändern** auswählen.

![Der Benutzer erstellt das Konto.](media/phone-authentication/email-verification.png)

### <a name="phone-sign-in-experience"></a>Oberfläche für die Telefonanmeldung

Wenn der Benutzer über ein bestehendes Konto mit der Telefonnummer als Bezeichner verfügt, gibt der Benutzer seine Telefonnummer ein und wählt **Weiter** aus. Der Benutzer bestätigt das Land/die Region und die Telefonnummer, indem er **Weiter** auswählt. Daraufhin wird ein Einmalprüfcode an die Telefonnummer des Benutzers gesendet. Der Benutzer gibt den Prüfcode ein und wählt **Weiter** aus, um sich anzumelden.

![Benutzeroberfläche für die Telefonanmeldung](media/phone-authentication/phone-signin-screens.png)

## <a name="deleting-a-user-account"></a>Löschen eines Benutzerkontos

In bestimmten Fällen müssen Sie möglicherweise einen Benutzer und die zugehörigen Daten aus Ihrem Azure AD B2C-Verzeichnis löschen. Ausführliche Informationen zum Löschen eines Benutzerkontos im Azure-Portal finden Sie [hier](/microsoft-365/compliance/gdpr-dsr-azure#step-5-delete). 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]



## <a name="prerequisites"></a>Voraussetzungen

Vor dem Einrichten von OTP benötigen Sie die folgenden Ressourcen.

* [Azure AD B2C-Mandant](tutorial-create-tenant.md)
* In Ihrem Mandanten [registrierte Webanwendung](tutorial-register-applications.md)
* [Benutzerdefinierte Richtlinien](custom-policy-get-started.md), die in Ihren Mandanten hochgeladen wurden

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Abrufen des Starter Packs für die Registrierung und Anmeldung

Aktualisieren Sie zunächst die benutzerdefinierten Richtliniendateien für die Telefonregistrierung und -anmeldung, damit diese mit Ihrem Azure AD B2C-Mandanten funktionieren.

In den folgenden Schritten wird davon ausgegangen, dass Sie die [Voraussetzungen](#prerequisites) abgeschlossen und das Repository für das [benutzerdefinierte Richtlinien-Starter Pack][starter-pack] auf Ihren lokalen Computer geklont haben.

1. Suchen Sie die [benutzerdefinierten Richtliniendateien für die Telefonregistrierung und -anmeldung][starter-pack-phone] in Ihrem lokalen Klon des Starter Pack-Repositorys, oder laden Sie sie direkt herunter. Die XML-Richtliniendateien befinden sich im folgenden Verzeichnis:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. Ersetzen Sie in jeder Datei die Zeichenfolge `yourtenant` durch den Namen Ihres Azure AD B2C-Mandanten. Wenn der Name des B2C-Mandanten z.B. *contosob2c* lautet, werden alle Instanzen von `yourtenant.onmicrosoft.com` zu `contosob2c.onmicrosoft.com`.

1. Führen Sie die Schritte im Abschnitt [Hinzufügen von Anwendungs-IDs zur benutzerdefinierten Richtlinie](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) von [Erste Schritte mit benutzerdefinierten Richtlinien in Azure Active Directory B2C](custom-policy-get-started.md) aus. Aktualisieren Sie in diesem Fall `/phone-number-passwordless/`**`Phone_Email_Base.xml`** mit den **Anwendungs-IDs (Client-IDs)** der beiden Anwendungen, die Sie beim Abschließen der Voraussetzungen registriert haben: *IdentityExperienceFramework* und *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>Hochladen der Richtliniendateien

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem Azure AD B2C-Mandanten.
1. Wählen Sie unter **Richtlinien** die Option **Identity Experience Framework** aus.
1. Wählen Sie **Benutzerdefinierte Richtlinie hochladen** aus.
1. Laden Sie die Richtliniendateien in der folgenden Reihenfolge hoch:
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone.xml*
    1. *SignUpOrSignInWithPhoneOrEmail.xml*
    1. *ProfileEditPhoneOnly.xml*
    1. *ProfileEditPhoneEmail.xml*
    1. *ChangePhoneNumber.xml*
    1. *PasswordResetEmail.xml*

Beim Hochladen der einzelnen Dateien fügt Azure das Präfix `B2C_1A_` hinzu.

## <a name="test-the-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Wählen Sie unter **Benutzerdefinierte Richtlinien** die Richtlinie **B2C_1A_SignUpOrSignInWithPhone** aus.
1. Wählen Sie unter **Anwendung auswählen** die Anwendung *webapp1* aus, die Sie beim Abschließen der Voraussetzungen registriert haben.
1. Wählen Sie für **Antwort-URL** die Option `https://jwt.ms` aus.
1. Wählen Sie **Jetzt ausführen** aus, und registrieren Sie sich mit einer E-Mail-Adresse oder Telefonnummer.
1. Wählen Sie **Jetzt ausführen** erneut aus, und melden Sie sich zur Bestätigung der richtigen Konfiguration mit demselben Konto an.

## <a name="get-user-account-by-phone-number"></a>Abrufen von Benutzerkonten anhand der Telefonnummer

Ein Benutzer, der sich mit einer Telefonnummer anmeldet, aber keine E-Mail-Adresse für die Wiederherstellung bereitstellt, wird in Ihrem Azure AD B2C-Verzeichnis mit seiner Telefonnummer als Anmeldename erfasst. Wenn der Benutzer seine Telefonnummer später ändern möchte, muss das Helpdesk- oder Supportteam zunächst sein Konto ermitteln und dann seine Telefonnummer aktualisieren.

Sie können einen Benutzer anhand der Telefonnummer (Anmeldename) mithilfe von [Microsoft Graph](manage-user-accounts-graph-api.md) ermitteln:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Beispiel:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>Nächste Schritte

Sie finden das Starter Pack für die benutzerdefinierte Richtlinie für Telefonregistrierung und -anmeldung (und andere Starter Packs) auf GitHub: [Azure-Samples/active-directory-b2c-custom-policy-starterpack/scenarios/phone-number-passwordless][starter-pack-phone]. Die Richtliniendateien des Starter Packs verwenden technische Multi-Factor Authentication-Profile und Telefonnummern-Anspruchstransformationen:
* [Definieren eines technischen Azure AD Multi-Factor Authentication-Profils](multi-factor-auth-technical-profile.md)
* [Definieren von Telefonnummern-Anspruchstransformationen](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless