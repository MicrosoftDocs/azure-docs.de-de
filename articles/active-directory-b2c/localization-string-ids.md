---
title: Lokalisierungszeichenfolgen-IDs – Azure Active Directory B2C | Microsoft-Dokumentation
description: Geben Sie die IDs für eine Inhaltsdefinition mit der ID „api.signuporsignin“ in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C an.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4fa0f4d87567bd77faacfd2373dba0d2b8996446
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94873368"
---
# <a name="localization-string-ids"></a>Lokalisierungszeichenfolgen-IDs

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Mithilfe des **Localization**-Elements können Sie mehrere Gebietsschemas oder Sprachen in der Richtlinie für die User Journeys unterstützen. Dieser Artikel enthält die Liste mit Lokalisierungs-IDs, die Sie in Ihrer Richtlinie verwenden können. Informationen zur Benutzeroberflächenlokalisierung finden Sie unter [Lokalisierung](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Elemente auf der Anmelde- oder Registrierungsseite

Die folgenden IDs werden für eine Inhaltsdefinition mit der ID `api.signuporsignin` und einem [selbstbestätigten technischen Profil](self-asserted-technical-profile.md) verwendet.

| id | Standardwert | Seitenlayoutversion |
| -- | ------------- | ------ |
| **forgotpassword_link** | Forgot your password? (Kennwort vergessen?) | `All` |
| **createaccount_intro** | Sie haben kein Konto? | `All` |
| **button_signin** | Anmelden | `All` |
| **social_intro** | Melden Sie sich mit Ihrem Konto für ein soziales Netzwerk an. | `All` |
| **remember_me** |Angemeldet bleiben. | `All` |
| **unknown_error** | Wir können Sie nicht anmelden. Versuchen Sie es später noch mal. | `All` |
| **divider_title** | oder | `All` |
| **local_intro_email** | Melden Sie sich mit Ihrem vorhandenen Konto an. | `< 2.0.0` |
| **logonIdentifier_email** | E-Mail-Adresse | `< 2.0.0` |
| **requiredField_email** | Geben Sie Ihre E-Mail-Adresse ein. | `< 2.0.0` |
| **invalid_email** | Geben Sie eine gültige E-Mail-Adresse ein. | `< 2.0.0` |
| **email_pattern** | ^[a-zA-Z0-9.!#$%&''\*+/=?^\_\`{\|}~-]+@[a-zA-Z0-9-]+(?:\\.[a-zA-Z0-9-]+)\*$ | `< 2.0.0` |
| **local_intro_username** | Melden Sie sich mit Ihrem Benutzernamen an. | `< 2.0.0` |
| **logonIdentifier_username** | Username | `< 2.0.0` |
| **requiredField_username** | Geben Sie Ihren Benutzernamen ein. | `< 2.0.0` |
| **password** | Kennwort | `< 2.0.0` |
| **requiredField_password** | Geben Sie Ihr Kennwort ein. | `< 2.0.0` |
| **createaccount_link** | Jetzt anmelden | `< 2.0.0` |
| **cancel_message** | Der Benutzer hat sein Kennwort vergessen. | `< 2.0.0` |
| **invalid_password** | Das eingegebene Kennwort entspricht nicht dem erwarteten Format. | `< 2.0.0` |
| **createaccount_one_link** | Jetzt anmelden | `>= 2.0.0` |
| **createaccount_two_links** | Registrieren mit {0} oder {1} | `>= 2.0.0` |
| **createaccount_three_links** | Registrieren mit {0}, {1} oder {2} | `>= 2.0.0` |
| **local_intro_generic** | Anmelden mit Ihrer {0} | `>= 2.1.0` |
| **requiredField_generic** | Geben Sie Ihre {0} ein. | `>= 2.1.0` |
| **invalid_generic** | Geben Sie einen gültigen Wert für {0} ein. | `>= 2.1.1` |
| **heading** | Anmelden | `>= 2.1.1` |


> [!NOTE]
> * Platzhalter wie {0} werden automatisch mit dem `DisplayName`-Wert von `ClaimType` aufgefüllt. 
> * Weitere Informationen zum Lokalisieren von `ClaimType` finden Sie unter [Beispiel für Registrierung oder Anmeldung](#signupsigninexample).

Die folgenden Beispiele zeigen die Verwendung einiger Benutzeroberflächenelemente auf der Anmelde- oder Registrierungsseite:

:::image type="content" source="./media/localization-string-ids/localization-susi-2.png" alt-text="Screenshot, der Benutzeroberflächenelemente der Registrierungs- oder Anmeldeseite anzeigt.":::

### <a name="sign-up-or-sign-in-identity-providers"></a>Identitätsanbieter für Registrierung und Anmeldung

Die ID der Identitätsanbieter wird im **ClaimsExchange**-Element der User Journey konfiguriert. Um den Titel des Identitätsanbieters zu lokalisieren, wird **ElementType** auf `ClaimsProvider` festgelegt, während für **StringId** die ID von `ClaimsExchange` festgelegt wird.

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Im folgenden Beispiel wird der Facebook-Identitätsanbieter in Arabisch lokalisiert:

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

### <a name="sign-up-or-sign-in-error-messages"></a>Fehlermeldungen beim Registrieren oder Anmelden

| id | Standardwert |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | Ihr Kennwort ist falsch. |
| **UserMessageIfPasswordExpired**| Ihre Kennwort ist abgelaufen.|
| **UserMessageIfClaimsPrincipalDoesNotExist** | Wir können Ihr Konto nicht finden. |
| **UserMessageIfOldPasswordUsed** | Sie verwenden offenbar ein altes Kennwort. |
| **DefaultMessage** | Ungültiger Benutzername oder ungültiges Kennwort. |
| **UserMessageIfUserAccountDisabled** | Ihr Konto wurde gesperrt. Wenden Sie sich an einen Supportmitarbeiter, um die Sperre aufheben zu lassen, und wiederholen Sie den Vorgang. |
| **UserMessageIfUserAccountLocked** | Ihr Konto wurde vorübergehend gesperrt, um eine unbefugte Nutzung zu verhindern. Versuchen Sie es später noch einmal. |
| **AADRequestsThrottled** | Derzeit sind zu viele Anforderungen vorhanden. Warten Sie einige Zeit, und versuchen Sie es erneut. |

<a name="signupsigninexample"></a>
### <a name="sign-up-or-sign-in-example"></a>Beispiel für Registrierung oder Anmeldung

```xml
<LocalizedResources Id="api.signuporsignin.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="heading">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="social_intro">Sign in with your social account</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_intro_generic">Sign in with your {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_password">Please enter your password</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_generic">Please enter your {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_generic">Please enter a valid {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_one_link">Sign up now</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_two_links">Sign up with {0} or {1}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_three_links">Sign up with {0}, {1}, or {2}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">Forgot your password?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_signin">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="divider_title">OR</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="unknown_error">We are having trouble signing you in. Please try again later.</LocalizedString>
    <!-- Uncomment the remember_me only if the keep me signed in is activated. 
    <LocalizedString ElementType="UxElement" StringId="remember_me">Keep me signed in</LocalizedString> -->
    <LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfPasswordExpired">Your password has expired.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">Looks like you used an old password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">Invalid username or password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Benutzeroberflächenelemente für Anmeldeseiten und Seiten zur Identitätsüberprüfung

Im Folgenden finden Sie die IDs für eine Inhaltsdefinition mit der ID `api.localaccountsignup` oder einer Inhaltsdefinition, die mit `api.selfasserted` beginnt, z. B. `api.selfasserted.profileupdate` und `api.localaccountpasswordreset`, und einem [selbstbestätigten technischen Profil](self-asserted-technical-profile.md).

| id | Standardwert |
| -- | ------------- |
| **ver_sent** | Prüfcode wurde gesendet an: |
| **ver_but_default** | Standard |
| **cancel_message** | Der Benutzer hat die Eingabe von Informationen zur Identitätsüberprüfung abgebrochen. |
| **preloader_alt** | Bitte warten |
| **ver_but_send** | Überprüfungscode senden |
| **alert_yes** | Ja |
| **error_fieldIncorrect** | Mindestens ein Feld wurde falsch ausgefüllt. Überprüfen Sie Ihre Eingaben, und versuchen Sie es erneut. |
| **year** | Jahr |
| **verifying_blurb** | Warten Sie, während wir Ihre Informationen verarbeiten. |
| **button_cancel** | Abbrechen |
| **ver_fail_no_retry** | Es wurden zu viele ungültige Versuche durchgeführt. Versuchen Sie es später noch mal. |
| **month** | Month (Monat) |
| **ver_success_msg** | Die E-Mail-Adresse wurde verifiziert. Sie können den Vorgang jetzt fortsetzen. |
| **months** | Januar, Februar, März, April, Mai, Juni, Juli, August, September, Oktober, November, Dezember |
| **ver_fail_server** | Beim Überprüfen Ihrer E-Mail-Adresse sind Probleme aufgetreten. Geben Sie eine gültige E-Mail-Adresse ein, und versuchen Sie es erneut. |
| **error_requiredFieldMissing** | Ein erforderliches Feld fehlt. Füllen Sie alle erforderlichen Felder aus, und versuchen Sie es erneut. |
| **initial_intro** | Geben Sie die folgenden Details an. |
| **ver_but_resend** | Neuen Code senden |
| **button_continue** | Erstellen |
| **error_passwordEntryMismatch** | Die Kennwörter stimmen nicht überein. Geben Sie in beiden Feldern das gleiche Kennwort ein, und versuchen Sie es erneut. |
| **ver_incorrect_format** | Falsches Format. |
| **ver_but_edit** | E-Mail-Adresse ändern |
| **ver_but_verify** | Code überprüfen |
| **alert_no** | Nein |
| **ver_info_msg** | Der Prüfcode wurde an Ihr Postfach gesendet. Kopieren Sie den Code in das nachstehende Eingabefeld. |
| **day** | Day (Tag) |
| **ver_fail_throttled** | Es wurden zu viele Anforderungen zur Überprüfung dieser E-Mail-Adresse gesendet. Warten Sie einige Minuten, und versuchen Sie es erneut. |
| **helplink_text** | Was ist das? |
| **ver_fail_retry** | Dieser Code ist falsch. Wiederholen Sie den Vorgang. |
| **alert_title** | Eingabe Ihrer Details abbrechen |
| **required_field** | Diese Informationen sind erforderlich. |
| **alert_message** | Möchten Sie die Eingabe der Detailinformationen abbrechen? |
| **ver_intro_msg** | Es ist eine Überprüfung erforderlich. Klicken Sie auf die Schaltfläche „Senden“. |
| **ver_input** | Prüfcode |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>Fehlermeldungen für Anmeldeseiten und Seiten zur Identitätsüberprüfung

| id | Standardwert |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Es ist bereits ein Benutzer mit der angegebenen ID vorhanden. Wählen Sie eine andere ID. |
| **UserMessageIfClaimNotVerified** | Anspruch nicht überprüft: {0} |
| **UserMessageIfIncorrectPattern** | Ungültiges Muster für {0}. |
| **UserMessageIfMissingRequiredElement** | Erforderliches Element fehlt: {0} |
| **UserMessageIfValidationError** | Fehler bei der Überprüfung durch {0}. |
| **UserMessageIfInvalidInput** | {0} weist eine ungültige Eingabe auf. |
| **ServiceThrottled** | Derzeit sind zu viele Anforderungen vorhanden. Warten Sie einige Zeit, und versuchen Sie es erneut. |

Die folgenden Beispiele zeigen die Verwendung einiger Benutzeroberflächenelemente auf der Registrierungsseite:

![Registrierungsseite mit bezeichneten Namen der Benutzeroberflächenelemente](./media/localization-string-ids/localization-sign-up.png)

Die folgenden Beispiele zeigen die Verwendung einiger Benutzeroberflächenelemente auf der Registrierungsseite, nachdem der Benutzer auf die Schaltfläche „Überprüfungscode senden“ geklickt hat:

![UX-Elemente zur E-Mail-Überprüfung auf der Registrierungsseite](./media/localization-string-ids/localization-email-verification.png)

## <a name="sign-up-and-self-asserted-pages-example"></a>Beispiel für Registrierungsseiten und Seiten zur Identitätsüberprüfung

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Email address that can be used to contact you.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">Enter new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">Confirm New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">Confirm new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">Display Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">Your display name.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">Surname</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">Your surname (also known as family name or last name).</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">Given Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">Your given name (also known as first name).</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">A required field is missing. Please fill out all required fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="helplink_text">What is this?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please provide the following details.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="preloader_alt">Please wait</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="required_field">This information is required.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_edit">Change e-mail</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_resend">Send new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_send">Send verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_verify">Verify code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">That code is incorrect. Please try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_server">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_info_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_input">Verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_success_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">Claim not verified: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">A user with the specified ID already exists. Please choose a different one.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">Incorrect pattern for: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">{0} has invalid input.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">Missing required element: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">Error in validation by: {0}</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="phone-factor-authentication-page-user-interface-elements"></a>Benutzeroberflächenelemente auf der Seite für die Phone Factor Authentication

Im Folgenden finden Sie die IDs für eine Inhaltsdefinition mit der ID `api.phonefactor` und einem [technischen PhoneFactor-Profil](phone-factor-technical-profile.md).

| id | Standardwert |
| -- | ------------- |
| **button_verify** | Rückruf |
| **country_code_label** | Landesvorwahl |
| **cancel_message** | Der Benutzer hat die mehrstufige Authentifizierung abgebrochen. |
| **text_button_send_second_code** | Neuen Code senden |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Bei uns ist die folgende Nummer für Sie hinterlegt. Wir können Ihnen einen Code per SMS senden oder Sie anrufen, um Sie zu authentifizieren. |
| **intro_mixed_p** | Bei uns sind die folgenden Nummern für Sie hinterlegt. Wählen Sie eine Nummer aus, an die wir per Telefon oder SMS einen Code senden können, um Sie zu authentifizieren. |
| **button_verify_code** | Code überprüfen |
| **requiredField_code** | Geben Sie den Prüfcode ein, den Sie erhalten haben. |
| **invalid_code** | Geben Sie den 6-stelligen Code ein, den Sie erhalten haben. |
| **button_cancel** | Abbrechen |
| **local_number_input_placeholder_text** | Telefonnummer |
| **button_retry** | Wiederholen |
| **alternative_text** | Mein Telefon ist nicht verfügbar. |
| **intro_phone_p** | Bei uns sind die folgenden Nummern für Sie hinterlegt. Wählen Sie eine Nummer aus, die wir anrufen können, um Sie zu authentifizieren. |
| **intro_phone** | Bei uns ist die folgende Nummer für Sie hinterlegt. Wir rufen Sie an, um Sie zu authentifizieren. |
| **enter_code_text_intro** | Geben Sie unten Ihren Prüfcode ein, oder  |
| **intro_entry_phone** | Geben Sie unten eine Nummer ein, die wir anrufen können, um Sie zu authentifizieren. |
| **intro_entry_sms** | Geben Sie unten eine Nummer ein, an die wir per SMS einen Code senden können, um Sie zu authentifizieren. |
| **button_send_code** | Code senden |
| **invalid_number** | Geben Sie eine gültige Telefonnummer ein. |
| **intro_sms** | Bei uns ist die folgende Nummer für Sie hinterlegt. Wir senden einen Code per SMS, um Sie zu authentifizieren. |
| **intro_entry_mixed** | Geben Sie unten eine Nummer ein, damit wir Ihnen einen Code per SMS senden oder Sie anrufen können, um Sie zu authentifizieren. |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |Bei uns sind die folgenden Nummern für Sie hinterlegt. Wählen Sie eine Nummer aus, an die wir per SMS einen Code senden können, um Sie zu authentifizieren. |
| **requiredField_countryCode** | Wählen Sie Ihre Landeskennzahl aus. |
| **requiredField_number** | Geben Sie Ihre Telefonnummer ein. |
| **country_code_input_placeholder_text** |Land oder Region |
| **number_label** | Rufnummer |
| **error_tryagain** | Die angegebene Telefonnummer ist besetzt oder nicht erreichbar. Überprüfen Sie die Nummer, und versuchen Sie es erneut. |
| **error_incorrect_code** | Der eingegebene Prüfcode stimmt nicht mit unseren Daten überein. Versuchen Sie es erneut, oder fordern Sie einen neuen Code an. |
| **countryList** | Informationen finden Sie in der [Länderliste](#phone-factor-authentication-page-example). |
| **error_448** | Die angegebene Telefonnummer ist nicht erreichbar. |
| **error_449** | Der Benutzer hat die zulässige Anzahl von Wiederholungsversuchen überschritten. |
| **verification_code_input_placeholder_text** | Prüfcode |

Die folgenden Beispiele zeigen die Verwendung einiger Benutzeroberflächenelemente auf der MFA-Registrierungsseite:

![UX-Elemente für PhoneFactor-Authentifizierungsregistrierung](./media/localization-string-ids/localization-mfa1.png)

Die folgenden Beispiele zeigen die Verwendung einiger Benutzeroberflächenelemente auf der MFA-Überprüfungsseite:

![UX-Elemente für PhoneFactor-Authentifizierungsvalidierung](./media/localization-string-ids/localization-mfa2.png)

## <a name="phone-factor-authentication-page-example"></a>Beispiel für eine Seite zur PhoneFactor-Authentifizierung

```xml
<LocalizedResources Id="api.phonefactor.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="button_verify">Call Me</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_label">Country Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="cancel_message">The user has canceled multi-factor authentication</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="text_button_send_second_code">Send a new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="code_pattern">\d{6}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed">We have the following number on record for you. We can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed_p">We have the following numbers on record for you. Choose a number that we can phone or send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_code">Please enter the verification code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_code">Please enter the 6-digit code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_number_input_placeholder_text">Phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_retry">Retry</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="alternative_text">I don't have my phone</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone_p">We have the following numbers on record for you. Choose a number that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone">We have the following number on record for you. We will phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="enter_code_text_intro">Enter your verification code below, or</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_phone">Enter a number below that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_sms">Enter a number below that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_number">Please enter a valid phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms">We have the following number on record for you. We will send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_mixed">Enter a number below that we can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_pattern">^\+(?:[0-9][\x20-]?){6,14}[0-9]$</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms_p">We have the following numbers on record for you. Choose a number that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_countryCode">Please select your country code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_number">Please enter your phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_input_placeholder_text">Country or region</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_label">Phone Number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_tryagain">The phone number you provided is busy or unavailable. Please check the number and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_incorrect_code">The verification code you have entered does not match our records. Please try again, or request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="countryList">{"DEFAULT":"Country/Region","AF":"Afghanistan","AX":"Åland Islands","AL":"Albania","DZ":"Algeria","AS":"American Samoa","AD":"Andorra","AO":"Angola","AI":"Anguilla","AQ":"Antarctica","AG":"Antigua and Barbuda","AR":"Argentina","AM":"Armenia","AW":"Aruba","AU":"Australia","AT":"Austria","AZ":"Azerbaijan","BS":"Bahamas","BH":"Bahrain","BD":"Bangladesh","BB":"Barbados","BY":"Belarus","BE":"Belgium","BZ":"Belize","BJ":"Benin","BM":"Bermuda","BT":"Bhutan","BO":"Bolivia","BQ":"Bonaire","BA":"Bosnia and Herzegovina","BW":"Botswana","BV":"Bouvet Island","BR":"Brazil","IO":"British Indian Ocean Territory","VG":"British Virgin Islands","BN":"Brunei","BG":"Bulgaria","BF":"Burkina Faso","BI":"Burundi","CV":"Cabo Verde","KH":"Cambodia","CM":"Cameroon","CA":"Canada","KY":"Cayman Islands","CF":"Central African Republic","TD":"Chad","CL":"Chile","CN":"China","CX":"Christmas Island","CC":"Cocos (Keeling) Islands","CO":"Colombia","KM":"Comoros","CG":"Congo","CD":"Congo (DRC)","CK":"Cook Islands","CR":"Costa Rica","CI":"Côte d'Ivoire","HR":"Croatia","CU":"Cuba","CW":"Curaçao","CY":"Cyprus","CZ":"Czech Republic","DK":"Denmark","DJ":"Djibouti","DM":"Dominica","DO":"Dominican Republic","EC":"Ecuador","EG":"Egypt","SV":"El Salvador","GQ":"Equatorial Guinea","ER":"Eritrea","EE":"Estonia","ET":"Ethiopia","FK":"Falkland Islands","FO":"Faroe Islands","FJ":"Fiji","FI":"Finland","FR":"France","GF":"French Guiana","PF":"French Polynesia","TF":"French Southern Territories","GA":"Gabon","GM":"Gambia","GE":"Georgia","DE":"Germany","GH":"Ghana","GI":"Gibraltar","GR":"Greece","GL":"Greenland","GD":"Grenada","GP":"Guadeloupe","GU":"Guam","GT":"Guatemala","GG":"Guernsey","GN":"Guinea","GW":"Guinea-Bissau","GY":"Guyana","HT":"Haiti","HM":"Heard Island and McDonald Islands","HN":"Honduras","HK":"Hong Kong SAR","HU":"Hungary","IS":"Iceland","IN":"India","ID":"Indonesia","IR":"Iran","IQ":"Iraq","IE":"Ireland","IM":"Isle of Man","IL":"Israel","IT":"Italy","JM":"Jamaica","JP":"Japan","JE":"Jersey","JO":"Jordan","KZ":"Kazakhstan","KE":"Kenya","KI":"Kiribati","KR":"Korea","KW":"Kuwait","KG":"Kyrgyzstan","LA":"Laos","LV":"Latvia","LB":"Lebanon","LS":"Lesotho","LR":"Liberia","LY":"Libya","LI":"Liechtenstein","LT":"Lithuania","LU":"Luxembourg","MO":"Macao SAR","MK":"North Macedonia","MG":"Madagascar","MW":"Malawi","MY":"Malaysia","MV":"Maldives","ML":"Mali","MT":"Malta","MH":"Marshall Islands","MQ":"Martinique","MR":"Mauritania","MU":"Mauritius","YT":"Mayotte","MX":"Mexico","FM":"Micronesia","MD":"Moldova","MC":"Monaco","MN":"Mongolia","ME":"Montenegro","MS":"Montserrat","MA":"Morocco","MZ":"Mozambique","MM":"Myanmar","NA":"Namibia","NR":"Nauru","NP":"Nepal","NL":"Netherlands","NC":"New Caledonia","NZ":"New Zealand","NI":"Nicaragua","NE":"Niger","NG":"Nigeria","NU":"Niue","NF":"Norfolk Island","KP":"North Korea","MP":"Northern Mariana Islands","NO":"Norway","OM":"Oman","PK":"Pakistan","PW":"Palau","PS":"Palestinian Authority","PA":"Panama","PG":"Papua New Guinea","PY":"Paraguay","PE":"Peru","PH":"Philippines","PN":"Pitcairn Islands","PL":"Poland","PT":"Portugal","PR":"Puerto Rico","QA":"Qatar","RE":"Réunion","RO":"Romania","RU":"Russia","RW":"Rwanda","BL":"Saint Barthélemy","KN":"Saint Kitts and Nevis","LC":"Saint Lucia","MF":"Saint Martin","PM":"Saint Pierre and Miquelon","VC":"Saint Vincent and the Grenadines","WS":"Samoa","SM":"San Marino","ST":"São Tomé and Príncipe","SA":"Saudi Arabia","SN":"Senegal","RS":"Serbia","SC":"Seychelles","SL":"Sierra Leone","SG":"Singapore","SX":"Sint Maarten","SK":"Slovakia","SI":"Slovenia","SB":"Solomon Islands","SO":"Somalia","ZA":"South Africa","GS":"South Georgia and South Sandwich Islands","SS":"South Sudan","ES":"Spain","LK":"Sri Lanka","SH":"St Helena, Ascension, Tristan da Cunha","SD":"Sudan","SR":"Suriname","SJ":"Svalbard","SZ":"Swaziland","SE":"Sweden","CH":"Switzerland","SY":"Syria","TW":"Taiwan","TJ":"Tajikistan","TZ":"Tanzania","TH":"Thailand","TL":"Timor-Leste","TG":"Togo","TK":"Tokelau","TO":"Tonga","TT":"Trinidad and Tobago","TN":"Tunisia","TR":"Turkey","TM":"Turkmenistan","TC":"Turks and Caicos Islands","TV":"Tuvalu","UM":"U.S. Outlying Islands","VI":"U.S. Virgin Islands","UG":"Uganda","UA":"Ukraine","AE":"United Arab Emirates","GB":"United Kingdom","US":"United States","UY":"Uruguay","UZ":"Uzbekistan","VU":"Vanuatu","VA":"Vatican City","VE":"Venezuela","VN":"Vietnam","WF":"Wallis and Futuna","YE":"Yemen","ZM":"Zambia","ZW":"Zimbabwe"}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_448">The phone number you provided is unreachable.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_449">User has exceeded the number of retry attempts.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_code_input_placeholder_text">Verification code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="strongAuthenticationPhoneNumber" StringId="DisplayName">Phone Number</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>

```

## <a name="verification-display-control-user-interface-elements"></a>Anzeigesteuerelemente zur Überprüfung von Benutzeroberflächenelementen

Im Folgenden finden Sie die IDs für ein [Anzeigesteuerelement zur Überprüfung](display-control-verification.md) mit der [Seitenlayoutversion 2.1.0 oder höher](page-layout.md).

| id | Standardwert |
| -- | ------------- |
|intro_msg| Es ist eine Überprüfung erforderlich. Klicken Sie auf die Schaltfläche „Senden“.|
|success_send_code_msg | Der Prüfcode wurde an Ihr Postfach gesendet. Kopieren Sie den Code in das nachstehende Eingabefeld.|
|failure_send_code_msg | Beim Überprüfen Ihrer E-Mail-Adresse sind Probleme aufgetreten. Geben Sie eine gültige E-Mail-Adresse ein, und versuchen Sie es erneut.|
|success_verify_code_msg | Die E-Mail-Adresse wurde verifiziert. Sie können den Vorgang jetzt fortsetzen.|
|failure_verify_code_msg | Beim Überprüfen Ihrer E-Mail-Adresse sind Probleme aufgetreten. Wiederholen Sie den Vorgang.|
|but_send_code | Überprüfungscode senden|
|but_verify_code | Code überprüfen|
|but_send_new_code | Neuen Code senden|
|but_change_claims | E-Mail-Adresse ändern|

### <a name="verification-display-control-example"></a>Beispiel für ein Anzeigesteuerelement zur Überprüfung

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
   <!-- Display control UI elements-->
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_send_code_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_send_code_msg">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_verify_code_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_verify_code_msg">We are having trouble verifying your email address. Please try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_new_code">Send new code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_change_claims">Change e-mail</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="verification-display-control-user-interface-elements-deprecated"></a>Anzeigesteuerelemente zur Überprüfung von Benutzeroberflächenelementen (veraltet)

Im Folgenden finden Sie die IDs für ein [Anzeigesteuerelement zur Überprüfung](display-control-verification.md) mit der [Seitenlayoutversion 2.0.0 oder höher](page-layout.md).

| id | Standardwert |
| -- | ------------- |
|verification_control_but_change_claims |Change |
|verification_control_fail_send_code |Der Code konnte nicht gesendet werden, versuchen Sie es später erneut. |
|verification_control_fail_verify_code |Der Code konnte nicht überprüft werden, versuchen Sie es später erneut. |
|verification_control_but_send_code |Code senden |
|verification_control_but_send_new_code |Neuen Code senden |
|verification_control_but_verify_code |Code überprüfen |
|verification_control_code_sent| Prüfcode wurde gesendet. Kopieren Sie den Code in das nachstehende Eingabefeld. |

### <a name="verification-display-control-example-deprecated"></a>Beispiel für ein Anzeigesteuerelement zur Überprüfung (veraltet)

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_change_claims">Change</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_send_code">Failed to send the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_verify_code">Failed to verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_new_code">Send New Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_code_sent">Verification code has been sent. Please copy it to the input box below.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="restful-service-error-messages"></a>Fehlermeldungen des RESTful-Diensts

Im Folgenden finden Sie die IDs für Fehlermeldungen für das [technische Profil zum RESTful-Dienst](restful-technical-profile.md):

| id | Standardwert |
| -- | ------------- |
|DefaultUserMessageIfRequestFailed | Fehler beim Herstellen einer Verbindung mit dem Endpunkt des RESTful-Diensts. URL des RESTful-Diensts: {0} |
|UserMessageIfCircuitOpen | {0} URL des RESTful-Diensts: {1} |
|UserMessageIfDnsResolutionFailed | Der Hostname des Endpunkts des RESTful-Diensts konnte nicht aufgelöst werden. URL des RESTful-Diensts: {0} |
|UserMessageIfRequestTimeout | Fehler beim Herstellen einer Verbindung mit dem Endpunkt des RESTful-Diensts innerhalb des Zeitlimits von {0} Sekunden. URL des RESTful-Diensts: {1} |


### <a name="restful-service-example"></a>Beispiel für RESTful-Dienst

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultUserMessageIfRequestFailed">Failed to establish connection to restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCircuitOpen">Unable to connect to the restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfDnsResolutionFailed">Failed to resolve the hostname of the restful service endpoint.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfRequestTimeout">Failed to establish connection to restful service end point within timeout limit.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-mfa-error-messages"></a>Azure AD MFA-Fehlermeldungen

Im Folgenden finden Sie die IDs für Fehlermeldungen für ein [technisches Profil mit Azure AD MFA](multi-factor-auth-technical-profile.md):

| id | Standardwert |
| -- | ------------- |
|UserMessageIfCouldntSendSms | SMS kann nicht an das Telefon gesendet werden, versuchen Sie es mit einer anderen Telefonnummer. |
|UserMessageIfInvalidFormat | Das Format Ihrer Telefonnummer ist ungültig. Korrigieren Sie sie, und versuchen Sie es erneut.|
|UserMessageIfMaxAllowedCodeRetryReached | Falscher Code wurde zu oft eingegeben, versuchen Sie es später erneut.|
|UserMessageIfServerError | MFA-Dienst kann nicht verwendet werden, versuchen Sie es später erneut.|
|UserMessageIfThrottled | Ihre Anforderung wurde gedrosselt, versuchen Sie es später erneut.|
|UserMessageIfWrongCodeEntered|Falscher Code wurde eingegeben, versuchen Sie es erneut.|

### <a name="azure-ad-mfa-example"></a>Beispiel für Azure AD MFA

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCouldntSendSms">Cannot Send SMS to the phone, please try another phone number.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidFormat">Your phone number is not in a valid format, please correct it and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxAllowedCodeRetryReached">Wrong code entered too many times, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfServerError">Cannot use MFA service, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">Your request has been throttled, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfWrongCodeEntered">Wrong code entered, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-sspr"></a>Azure AD-Self-Service-Kennwortzurücksetzung

Im Folgenden finden Sie die IDs für Fehlermeldungen für ein [technisches Profil zu Azure AD-SSPR](aad-sspr-technical-profile.md):

| id | Standardwert |
| -- | ------------- |
|UserMessageIfChallengeExpired | Der Code ist abgelaufen.|
|UserMessageIfInternalError | Interner Fehler beim E-Mail-Dienst. Versuchen Sie es später noch einmal.|
|UserMessageIfThrottled | Sie haben zu viele Anforderungen gesendet. Versuchen Sie es später noch einmal.|
|UserMessageIfVerificationFailedNoRetry | Sie haben die maximale Anzahl von Überprüfungsversuchen überschritten.|
|UserMessageIfVerificationFailedRetryAllowed | Fehler bei der Überprüfung. Versuchen Sie es noch einmal.|


### <a name="azure-ad-sspr-example"></a>Beispiel für Azure AD SSPR

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInternalError">We are having trouble verifying your email address. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfChallengeExpired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedNoRetry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="one-time-password-error-messages"></a>Fehlermeldungen für Einmalkennwort

Im Folgenden finden Sie die IDs für Fehlermeldungen für ein [technisches Profil mit Einmalkennwort](one-time-password-technical-profile.md).

| id | Standardwert |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |Die für das Einmalkennwort bereitgestellte Überprüfung hat die maximale Anzahl von Versuchen überschritten |
|UserMessageIfSessionDoesNotExist |Überprüfungssitzung für Einmalkennwort ist abgelaufen |
|UserMessageIfSessionConflict |Konflikt bei der Überprüfungssitzung für Einmalkennwort |
|UserMessageIfInvalidCode |Das Einmalkennwort für die Überprüfung ist falsch |
|UserMessageIfVerificationFailedRetryAllowed |Dieser Code ist falsch. Wiederholen Sie den Vorgang. | 

### <a name="one-time-password-example"></a>Beispiel für ein Einmalkennwort

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="claims-transformations-error-messages"></a>Fehlermeldungen zu Transformationen von Ansprüchen

Im Folgenden finden Sie die IDs für Fehlermeldungen zu Transformationen von Ansprüchen:

| id | Transformation von Ansprüchen | Standardwert |
| -- | ------------- |------------- |
|UserMessageIfClaimsTransformationBooleanValueIsNotEqual |[AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) | Fehler bei booleschem Anspruchswertvergleich für Anspruchstyp „inputClaim“.| 
|DateTimeGreaterThan |[AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan) | Fehler bei Anspruchswertvergleich: Der angegebene linke Operand ist größer als der rechte Operand.|
|UserMessageIfClaimsTransformationStringsAreNotEqual |[AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal) | Fehler bei Anspruchswertvergleich mithilfe von StringComparison „OrdinalIgnoreCase“.|

### <a name="claims-transformations-example"></a>Beispiel für Transformationen von Ansprüchen

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Your email address hasn't been verified.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DateTimeGreaterThan">Expiration date must be greater that the current date.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationStringsAreNotEqual">The email entry fields do not match. Please enter the same email address in both fields and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="next-steps"></a>Nächste Schritte

Die folgenden Artikel enthalten Lokalisierungsbeispiele:

- [Sprachanpassung mit benutzerdefinierter Richtlinie in Azure Active Directory B2C](custom-policy-localization.md)
- [Sprachanpassung mit Benutzerflows in Azure Active Directory B2C](user-flow-language-customization.md)
