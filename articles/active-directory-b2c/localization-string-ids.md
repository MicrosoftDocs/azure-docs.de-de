---
title: Lokalisierungszeichenfolgen-IDs – Azure Active Directory B2C | Microsoft-Dokumentation
description: Geben Sie die IDs für eine Inhaltsdefinition mit der ID „api.signuporsignin“ in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C an.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 41a72013f1538b0a857c76bc949a7109e1cd54b4
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510924"
---
# <a name="localization-string-ids"></a>Lokalisierungszeichenfolgen-IDs

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Mithilfe des **Localization**-Elements können Sie mehrere Gebietsschemas oder Sprachen in der Richtlinie für die User Journeys unterstützen. Dieser Artikel enthält die Liste mit Lokalisierungs-IDs, die Sie in Ihrer Richtlinie verwenden können. Informationen zur Benutzeroberflächenlokalisierung finden Sie unter [Lokalisierung](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Elemente auf der Anmelde- oder Registrierungsseite

Die folgenden IDs werden für eine Inhaltsdefinition mit der ID `api.signuporsignin` verwendet.

| ID | Standardwert |
| -- | ------------- |
| **local_intro_email** | Melden Sie sich mit Ihrem vorhandenen Konto an. |
| **logonIdentifier_email** | E-Mail-Adresse |
| **requiredField_email** | Geben Sie Ihre E-Mail-Adresse ein. |
| **invalid_email** | Geben Sie eine gültige E-Mail-Adresse ein. |
| **email_pattern** | ^[a-zA-Z0-9.!#$%&’' *+/=?^_\`{\|}~-]+@[a-zA-Z0-9-]+(?:\\.[a-zA-Z0-9-]+)* $ |
| **local_intro_username** | Melden Sie sich mit Ihrem Benutzernamen an. |
| **logonIdentifier_username** | Username |
| **requiredField_username** | Geben Sie Ihren Benutzernamen ein. |
| **password** | Kennwort |
| **requiredField_password** | Geben Sie Ihr Kennwort ein. |
| **invalid_password** | Das eingegebene Kennwort entspricht nicht dem erwarteten Format. |
| **forgotpassword_link** | Forgot your password? (Kennwort vergessen?) |
| **createaccount_intro** | Sie haben kein Konto? |
| **createaccount_link** | Jetzt anmelden |
| **divider_title** | OR |
| **cancel_message** | Der Benutzer hat sein Kennwort vergessen. |
| **button_signin** | Anmelden |
| **social_intro** | Melden Sie sich mit Ihrem Konto für ein soziales Netzwerk an. |
  **remember_me** |Angemeldet bleiben|
| **unknown_error** | Wir können Sie nicht anmelden. Versuchen Sie es später noch mal. |

Die folgenden Beispiele zeigen die Verwendung einiger Benutzeroberflächenelemente auf der Anmelde- oder Registrierungsseite:

![UX-Elemente auf der Anmelde- oder Registrierungsseite](./media/localization-string-ids/localization-susi.png)

Die ID der Identitätsanbieter wird im **ClaimsExchange**-Element der User Journey konfiguriert. Um den Titel des Identitätsanbieters zu lokalisieren, wird **ElementType** auf `ClaimsProvider` festgelegt, während für **StringId** die ID von `ClaimsExchange` festgelegt wird.

```XML
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

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

## <a name="sign-up-or-sign-in-error-messages"></a>Fehlermeldungen beim Registrieren oder Anmelden

| ID | Standardwert |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | Ihr Kennwort ist falsch. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | Wir können Ihr Konto nicht finden. |
| **UserMessageIfOldPasswordUsed** | Sie verwenden offenbar ein altes Kennwort. |  
| **DefaultMessage** | Ungültiger Benutzername oder ungültiges Kennwort. |  
| **UserMessageIfUserAccountDisabled** | Ihr Konto wurde gesperrt. Wenden Sie sich an einen Supportmitarbeiter, um die Sperre aufheben zu lassen, und wiederholen Sie den Vorgang. |  
| **UserMessageIfUserAccountLocked** | Ihr Konto wurde vorübergehend gesperrt, um eine unbefugte Nutzung zu verhindern. Versuchen Sie es später erneut. |  
| **AADRequestsThrottled** | Derzeit sind zu viele Anforderungen vorhanden. Warten Sie einige Zeit, und versuchen Sie es erneut. |  

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Benutzeroberflächenelemente für Anmeldeseiten und Seiten zur Identitätsüberprüfung

Folgende IDs dienen der Inhaltsdefinition mit der ID `api.localaccountsignup` oder einer Inhaltsdefinition, die mit `api.selfasserted` beginnt, wie etwa `api.selfasserted.profileupdate` und `api.localaccountpasswordreset`.

| ID | Standardwert |
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

## <a name="sign-up-and-self-asserted-pages-error-messages"></a>Fehlermeldungen für Anmeldeseiten und Seiten zur Identitätsüberprüfung

| ID | Standardwert |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Es ist bereits ein Benutzer mit der angegebenen ID vorhanden. Wählen Sie eine andere ID. |
| **UserMessageIfClaimNotVerified** | Anspruch nicht überprüft: {0} |
| **UserMessageIfIncorrectPattern** | Ungültiges Muster für {0}. |
| **UserMessageIfMissingRequiredElement** | Erforderliches Element fehlt: {0} |
| **UserMessageIfValidationError** | Fehler bei der Überprüfung durch {0}. |
| **UserMessageIfInvalidInput** | {0} weist eine ungültige Eingabe auf. |
| **ServiceThrottled** | Derzeit sind zu viele Anforderungen vorhanden. Warten Sie einige Zeit, und versuchen Sie es erneut. |

Die folgenden Beispiele zeigen die Verwendung einiger Benutzeroberflächenelemente auf der Registrierungsseite:

![UX-Elemente auf der Registrierungsseite](./media/localization-string-ids/localization-sign-up.png)

Die folgenden Beispiele zeigen die Verwendung einiger Benutzeroberflächenelemente auf der Registrierungsseite, nachdem der Benutzer auf die Schaltfläche „Überprüfungscode senden“ geklickt hat:

![UX-Elemente zur E-Mail-Überprüfung auf der Registrierungsseite](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Benutzeroberflächenelemente auf der Seite für die Phone Factor Authentication

Die folgenden IDs werden für eine Inhaltsdefinition mit der ID `api.phonefactor` verwendet. 

| ID | Standardwert |
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
| **countryList** | {\"STANDARD\":\"Land/Region\",\"AF\":\"Afghanistan\",\"AX\":\"Ålandinseln\",\"AL\":\"Albanien\",\"DZ\":\"Algerien\",\"AS\":\"Amerikanisch-Samoa\",\"AD\":\"Andorra\",\"AO\":\"Angola\",\"AI\":\"Anguilla\",\"AQ\":\"Antarktis\",\"AG\":\"Antigua und Barbuda\",\"AR\":\"Argentinien\",\"AM\":\"Armenien\",\"AW\":\"Aruba\",\"AU\":\"Australien\",\"AT\":\"Österreich\",\"AZ\":\"Aserbaidschan\",\"BS\":\"Bahamas\",\"BH\":\"Bahrain\",\"BD\":\"Bangladesh\",\"BB\":\"Barbados\",\"BY\":\"Belarus\",\"BE\":\"Belgien\",\"BZ\":\"Belize\",\"BJ\":\"Benin\",\"BM\":\"Bermuda\",\"BT\":\"Bhutan\",\"BO\":\"Bolivien\",\"BQ\":\"Bonaire\",\"BA\":\"Bosnien-Herzegowina\",\"BW\":\"Botswana\",\"BV\":\"Bouvetinsel\",\"BR\":\"Brasilien\",\"IO\":\"Britisches Territorium im Indischen Ozean\",\"VG\":\"Britische Jungferninseln\",\"BN\":\"Brunei\",\"BG\":\"Bulgarien\",\"BF\":\"Burkina Faso\",\"BI\":\"Burundi\",\"CV\":\"Cabo Verde\",\"KH\":\"Kambodscha\",\"CM\":\"Kamerun\",\"CA\":\"Kanada\",\"KY\":\"Kaimaninseln\",\"CF\":\"Zentralafrikanische Republik\",\"TD\":\"Tschad\",\"CL\":\"Chile\",\"CN\":\"China\",\"CX\":\"Weihnachtsinsel\",\"CC\":\"Kokosinseln\",\"CO\":\"Kolumbien\",\"KM\":\"Komoren\",\"CG\":\"Kongo\",\"CD\":\"Demokratische Republik Kongo\",\"CK\":\"Cookinseln\",\"CR\":\"Costa Rica\",\"CI\":\"Côte d’Ivoire\",\"HR\":\"Kroatien\",\"CU\":\"Kuba\",\"CW\":\"Curaçao\",\"CY\":\"Zypern\",\"CZ\":\"Tschechische Republik\",\"DK\":\"Dänemark\",\"DJ\":\"Dschibuti\",\"DM\":\"Dominica\",\"DO\":\"Dominikanische Republik\",\"EC\":\"Ecuador\",\"EG\":\"Ägypten\",\"SV\":\"El Salvador\",\"GQ\":\"Äquatorialguinea\",\"ER\":\"Eritrea\",\"EE\":\"Estland\",\"ET\":\"Äthiopien\",\"FK\":\"Falklandinseln\",\"FO\":\"Faröer\",\"FJ\":\"Fidschi\",\"FI\":\"Finnland\",\"FR\":\"Frankreich\",\"GF\":\"Französisch-Guyana\",\"PF\":\"Französisch-Polynesien\",\"TF\":\"Französische Süd- und Antarktisgebiete\",\"GA\":\"Gabun\",\"GM\":\"Gambia\",\"GE\":\"Georgien\",\"DE\":\"Deutschland\",\"GH\":\"Ghana\",\"GI\":\"Gibraltar\",\"GR\":\"Griechenland\",\"GL\":\"Grönland\",\"GD\":\"Grenada\",\"GP\":\"Guadeloupe\",\"GU\":\"Guam\",\"GT\":\"Guatemala\",\"GG\":\"Guernsey\",\"GN\":\"Guinea\",\"GW\":\"Guinea-Bissau\",\"GY\":\"Guyana\",\"HT\":\"Haiti\",\"HM\":\"Heard und McDonaldinseln\",\"HN\":\"Honduras\",\"HK\":\"Hong Kong SAR\",\"HU\":\"Ungarn\",\"IS\":\"Island\",\"IN\":\"Indien\",\"ID\":\"Indonesien\",\"IR\":\"Iran\",\"IQ\":\"Irak\",\"IE\":\"Irland\",\"IM\":\"Insel Man\",\"IL\":\"Israel\",\"IT\":\"Italien\",\"JM\":\"Jamaika\",\"JP\":\"Japan\",\"JE\":\"Jersey\",\"JO\":\"Jordanien\",\"KZ\":\"Kasachstan\",\"KE\":\"Kenia\",\"KI\":\"Kiribati\",\"KR\":\"Korea\",\"KW\":\"Kuwait\",\"KG\":\"Kirgisistan\",\"LA\":\"Laos\",\"LV\":\"Lettland\",\"LB\":\"Libanon\",\"LS\":\"Lesotho\",\"LR\":\"Liberia\",\"LY\":\"Libyen\",\"LI\":\"Liechtenstein\",\"LT\":\"Litauen\",\"LU\":\"Luxemburg\",\"MO\":\"Macau (SAR)\",\"MK\":\"Nordmazedonien\",\"MG\":\"Madagaskar\",\"MW\":\"Malawi\",\"MY\":\"Malaysia\",\"MV\":\"Malediven\",\"ML\":\"Mali\",\"MT\":\"Malta\",\"MH\":\"Marshallinseln\",\"MQ\":\"Martinique\",\"MR\":\"Mauretanien\",\"MU\":\"Mauritius\",\"YT\":\"Mayotte\",\"MX\":\"Mexiko\",\"FM\":\"Mikronesien\",\"MD\":\"Republik Moldau\",\"MC\":\"Monaco\",\"MN\":\"Mongolei\",\"ME\":\"Montenegro\",\"MS\":\"Montserrat\",\"MA\":\"Marokko\",\"MZ\":\"Mosambik\",\"MM\":\"Myanmar\",\"NA\":\"Namibia\",\"NR\":\"Nauru\",\"NP\":\"Nepal\",\"NL\":\"Niederlande\",\"NC\":\"Neukaledonien\",\"NZ\":\"Neuseeland\",\"NI\":\"Nicaragua\",\"NE\":\"Niger\",\"NG\":\"Nigeria\",\"NU\":\"Niue\",\"NF\":\"Norfolkinsel\",\"KP\":\"Nordkorea\",\"MP\":\"Nördliche Marianen\",\"NO\":\"Norwegen\",\"OM\":\"Oman\",\"PK\":\"Pakistan\",\"PW\":\"Palau\",\"PS\":\"Palästinensische Behörde\",\"PA\":\"Panama\",\"PG\":\"Papua-Neuguinea\",\"PY\":\"Paraguay\",\"PE\":\"Peru\",\"PH\":\"Philippinen\",\"PN\":\"Pitcairninseln\",\"PL\":\"Polen\",\"PT\":\"Portugal\",\"PR\":\"Puerto Rico\",\"QA\":\"Katar\",\"RE\":\"Réunion\",\"RO\":\"Rumänien\",\"RU\":\"Russland\",\"RW\":\"Ruanda\",\"BL\":\"St. Barthélemy\",\"KN\":\"St. Kitts und Nevis\",\"LC\":\"St. Lucia\",\"MF\":\"St. Martin\",\"PM\":\"St. Pierre und Miquelon\",\"VC\":\"St. Vincent und die Grenadinen\",\"WS\":\"Samoa\",\"SM\":\"San Marino\",\"ST\":\"São Tomé und Príncipe\",\"SA\":\"Saudi-Arabien\",\"SN\":\"Senegal\",\"RS\":\"Serbien\",\"SC\":\"Seychellen\",\"SL\":\"Sierra Leone\",\"SG\":\"Singapur\",\"SX\":\"St. Martin\",\"SK\":\"Slowakei\",\"SI\":\"Slowenien\",\"SB\":\"Salomonen\",\"SO\":\"Somalia\",\"ZA\":\"Südafrika\",\"GS\":\"Südgeorgien und die Südlichen Sandwichinseln\",\"SS\":\"Südsudan\",\"ES\":\"Spanien\",\"LK\":\"Sri Lanka\",\"SH\":\"St. Helena, Ascension, Tristan da Cunha\",\"SD\":\"Sudan\",\"SR\":\"Surinam\",\"SJ\":\"Spitzbergen\",\"SZ\":\"Swasiland\",\"SE\":\"Schweden\",\"CH\":\"Schweiz\",\"SY\":\"Syrien\",\"TW\":\"Taiwan\",\"TJ\":\"Tadschikistan\",\"TZ\":\"Tansania\",\"TH\":\"Thailand\",\"TL\":\"Timor-Leste\",\"TG\":\"Togo\",\"TK\":\"Tokelau\",\"TO\":\"Tonga\",\"TT\":\"Trinidad und Tobago\",\"TN\":\"Tunesien\",\"TR\":\"Türkei\",\"TM\":\"Turkmenistan\",\"TC\":\"Turks- und Caicosinseln\",\"TV\":\"Tuvalu\",\"UM\":\"Kleinere  Amerikanische Überseeinseln\",\"VI\":\"Amerikanische  Jungferninseln\",\"UG\":\"Uganda\",\"UA\":\"Ukraine\",\"AE\":\"Vereinigte Arabische Emirate\",\"GB\":\"Großbritannien\",\"US\":\"Vereinigte Staaten\",\"UY\":\"Uruguay\",\"UZ\":\"Usbekistan\",\"VU\":\"Vanuatu\",\"VA\":\"Vatikanstadt\",\"VE\":\"Venezuela\",\"VN\":\"Vietnam\",\"WF\":\"Wallis und Futuna\",\"YE\":\"Jemen\",\"ZM\":\"Sambia\",\"ZW\":\"Simbabwe\"} |
| **error_448** | Die angegebene Telefonnummer ist nicht erreichbar. |
| **error_449** | Der Benutzer hat die zulässige Anzahl von Wiederholungsversuchen überschritten. |
| **verification_code_input_placeholder_text** | Prüfcode |

Die folgenden Beispiele zeigen die Verwendung einiger Benutzeroberflächenelemente auf der MFA-Registrierungsseite:

![UX-Elemente zur E-Mail-Überprüfung auf der Registrierungsseite](./media/localization-string-ids/localization-mfa1.png)

Die folgenden Beispiele zeigen die Verwendung einiger Benutzeroberflächenelemente auf der MFA-Überprüfungsseite:

![UX-Elemente zur E-Mail-Überprüfung auf der Registrierungsseite](./media/localization-string-ids/localization-mfa2.png)







