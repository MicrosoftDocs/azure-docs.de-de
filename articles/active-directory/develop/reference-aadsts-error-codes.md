---
title: Fehlercodes für die Azure AD-Authentifizierung und -Autorisierung
description: Erfahren Sie mehr über die AADSTS-Fehlercodes, die vom Azure AD-Sicherheitstokendienst (STS) zurückgegeben werden.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: fa6fc11441811589967ddd7728501b521f9f9155
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169272"
---
# <a name="azure-ad-authentication-and-authorization-error-codes"></a>Fehlercodes für die Azure AD-Authentifizierung und -Autorisierung

Suchen Sie nach Informationen zu den AADSTS-Fehlercodes, die vom Azure Active Directory-Sicherheitstokendienst (STS) zurückgegeben werden? Lesen Sie dieses Dokument, um AADSTS-Fehlerbeschreibungen, Fehlerbehebungen und einige vorgeschlagene Problemumgehungen zu erhalten.

> [!NOTE]
> Diese Informationen sind vorläufig und können sich ändern. Haben Sie eine Frage, oder können Sie nicht finden, was Sie suchen? Erstellen Sie ein GitHub-Problem, oder lesen Sie [Support- und Hilfeoptionen für Entwickler](./developer-support-help-options.md), um mehr über andere Möglichkeiten zu erfahren, wie Sie Hilfe und Unterstützung erhalten können.
>
> Diese Dokumentation dient als Leitfaden für Entwickler und Administratoren, sollte aber niemals vom Kunden selbst verwendet werden. Fehlercodes können jederzeit geändert werden, um detailliertere Fehlermeldungen zu erhalten, die den Entwickler bei der Erstellung seiner Anwendung unterstützen sollen. Apps, die von Text- oder Fehlercodenummern abhängig sind, funktionieren im Lauf der Zeit nicht mehr.

## <a name="handling-error-codes-in-your-application"></a>Problembehandlung bei Fehlercodes in Ihrer Anwendung

In der [OAuth 2.0-Spezifikation](https://tools.ietf.org/html/rfc6749#section-5.2) finden Sie Anleitungen zum Behandeln von Fehlern während der Authentifizierung mithilfe der `error`-Angabe in der Fehlerantwort. 

Beispielfehlerantwort:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://example.contoso.com/activity.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7", 
  "error_uri":"https://login.microsoftonline.com/error?code=70011"
}
```

| Parameter         | BESCHREIBUNG    |
|-------------------|----------------|
| `error`       | Eine Fehlercodezeichenfolge, anhand der die auftretenden Fehlertypen klassifiziert werden können und die verwendet werden sollte, um auf Fehler zu reagieren. |
| `error_description` | Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können. Verwenden Sie dieses Feld niemals, um auf einen Fehler in Ihrem Code zu reagieren. |
| `error_codes` | Eine Liste der STS-spezifischen Fehlercodes, die bei der Diagnose helfen können.  |
| `timestamp`   | Der Zeitpunkt, zu dem der Fehler aufgetreten ist |
| `trace_id`    | Ein eindeutiger Bezeichner für die Anforderung, die bei der Diagnose helfen kann |
| `correlation_id` | Ein eindeutiger Bezeichner für die Anforderung, die bei der komponentenübergreifenden Diagnose helfen kann |
| `error_uri` |  Ein Link zur Fehlersuchseite mit zusätzlichen Informationen über den Fehler.  Diese Angabe ist nur für Entwickler bestimmt und darf den Benutzern nicht angezeigt werden.  Diese Angabe ist nur vorhanden, wenn das Fehlersuchsystem zusätzliche Informationen über den Fehler enthält. Es werden nicht für alle Fehler zusätzliche Informationen bereitgestellt.|

Für das Feld `error` gibt es mehrere mögliche Werte. Unter den Protokolldokumentationslinks und in den OAuth 2.0-Spezifikationen finden Sie weitere Informationen zu bestimmten Fehlern (z. B. zu `authorization_pending` im [Gerätcodeflow](v2-oauth2-device-code.md)) und dazu, wie Sie darauf reagieren können.  Im Folgenden sind einige häufige Fehlercodes aufgeführt:

| Fehlercode         | BESCHREIBUNG        | Clientaktion    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Protokollfehler, z.B. ein fehlender erforderlicher Parameter. | Korrigieren Sie die Anforderung, und senden Sie sie erneut.|
| `invalid_grant`    | Einige der Authentifizierungselemente (Authentifizierungscode, Aktualisierungstoken, Zugriffstoken, PKCE-Abfrage) waren ungültig, konnten nicht analysiert werden, fehlen oder sind anderweitig nicht verwendbar | Versuchen Sie, eine neue Anforderung an den `/authorize`-Endpunkt zu senden, um einen neuen Autorisierungscode zu erhalten.  Überprüfen und validieren Sie die Verwendung der Protokolle durch diese App. |
| `unauthorized_client` | Der authentifizierte Client ist zur Verwendung dieses Autorisierungsgewährungstyps nicht autorisiert. | Dies tritt in der Regel auf, wenn die Clientanwendung nicht in Azure AD registriert ist oder dem Azure AD-Mandanten des Benutzers nicht hinzugefügt wird. Die Anwendung kann den Benutzer zum Installieren der Anwendung und zum Hinzufügen zu Azure AD auffordern. |
| `invalid_client` | Clientauthentifizierung fehlgeschlagen.  | Die Client-Anmeldeinformationen sind nicht gültig. Um das Problem zu beheben, aktualisiert der Anwendungsadministrator die Anmeldeinformationen.   |
| `unsupported_grant_type` | Der Autorisierungsserver unterstützt den Autorisierungsgewährungstyp nicht. | Ändern Sie den Gewährungstyp in der Anforderung. Diese Art von Fehler sollte nur während der Entwicklung auftreten und bei den ersten Tests erkannt werden. |
| `invalid_resource` | Die Zielressource ist ungültig, da sie nicht vorhanden ist, Azure AD sie nicht findet oder sie nicht ordnungsgemäß konfiguriert ist. | Dies gibt an, dass die Ressource, falls vorhanden, im Mandanten nicht konfiguriert wurde. Die Anwendung kann den Benutzer zum Installieren der Anwendung und zum Hinzufügen zu Azure AD auffordern.  Während der Entwicklung deutet dies in der Regel auf einen falsch eingerichteten Testmandanten oder einen Tippfehler im Namen des angeforderten Bereichs hin. |
| `interaction_required` | Die Anforderung erfordert eine Benutzerinteraktion. Beispielsweise ist ein zusätzlicher Schritt zur Authentifizierung erforderlich. | Wiederholen Sie die Anforderung mit der gleichen Ressource (interaktiv), damit der Benutzer alle erforderlichen Aufgaben abschließen kann.  |
| `temporarily_unavailable` | Der Server ist vorübergehend überlastet und kann die Anforderung nicht verarbeiten. | Wiederholen Sie die Anforderung. Die Clientanwendung kann dem Benutzer erklären, dass ihre Antwort aufgrund einer temporären Bedingung verzögert ist. |

## <a name="lookup-current-error-code-information"></a>Nachschlagen aktueller Fehlercodeinformationen
Fehlercodes und Meldungen unterliegen Änderungen.  Die aktuellen Informationen finden Sie auf der Seite [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error). Sie enthält AADSTS-Fehlerbeschreibungen, Fehlerbehebungen und einige Vorschläge für Problemumgehungen.  

Wenn Sie beispielsweise den Fehlercode „AADSTS50058“ erhalten haben, suchen Sie unter [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) nach „50058“.  Sie können auch einen direkten Link zu einem bestimmten Fehler einrichten, indem Sie die Nummer des Fehlercodes der URL hinzufügen: [https://login.microsoftonline.com/error?code=50058](https://login.microsoftonline.com/error?code=50058).

## <a name="aadsts-error-codes"></a>AADSTS-Fehlercodes

| Fehler | BESCHREIBUNG |
|---|---|
| AADSTS16000 | SelectUserAccount: Dies ist ein Interrupt, der von Azure AD ausgelöst wird. Er führt zu einer Benutzeroberfläche, die es dem Benutzer ermöglicht, aus mehreren gültigen SSO-Sitzungen auszuwählen. Dieser Fehler tritt recht häufig auf und kann an die Anwendung zurückgegeben werden, wenn `prompt=none` angegeben wird. |
| AADSTS16001 | UserAccountSelectionInvalid: Dieser Fehler wird angezeigt, wenn der Benutzer auf eine Kachel klickt, die die Logik für die Auswahl der Sitzung abgelehnt hat. Wenn dieser Fehler ausgelöst wird, ermöglicht er dem Benutzer die Wiederherstellung, indem er aus einer aktualisierten Liste von Kacheln/Sitzungen auswählt oder ein anderes Konto auswählt. Dieser Fehler kann aufgrund eines Codedefekts oder einer Racebedingung auftreten. |
| AADSTS16002 | AppSessionSelectionInvalid: Die von der App angegebene SID-Anforderung wurde nicht erfüllt.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant: Weist darauf hin, dass der Benutzer nicht explizit zum Mandanten hinzugefügt wurde. |
| AADSTS17003 | CredentialKeyProvisioningFailed: Azure AD kann den Benutzerschlüssel nicht bereitstellen. |
| AADSTS20001 | WsFedSignInResponseError: Es besteht ein Problem mit Ihrem Verbundidentitätsanbieter. Wenden Sie sich an Ihren Identitätsanbieter, um das Problem zu beheben. |
| AADSTS20012 | WsFedMessageInvalid: Es besteht ein Problem mit Ihrem Verbundidentitätsanbieter. Wenden Sie sich an Ihren Identitätsanbieter, um das Problem zu beheben. |
| AADSTS20033 | FedMetadataInvalidTenantName: Es besteht ein Problem mit Ihrem Verbundidentitätsanbieter. Wenden Sie sich an Ihren Identitätsanbieter, um das Problem zu beheben. |
| AADSTS40008 | OAuth2IdPUnretryableServerError: Es besteht ein Problem mit Ihrem Verbundidentitätsanbieter. Wenden Sie sich an Ihren Identitätsanbieter, um das Problem zu beheben. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError: Es besteht ein Problem mit Ihrem Verbundidentitätsanbieter. Wenden Sie sich an Ihren Identitätsanbieter, um das Problem zu beheben. |
| AADSTS40010 | OAuth2IdPRetryableServerError: Es besteht ein Problem mit Ihrem Verbundidentitätsanbieter. Wenden Sie sich an Ihren Identitätsanbieter, um das Problem zu beheben. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError: Es besteht ein Problem mit Ihrem Verbundidentitätsanbieter. Wenden Sie sich an Ihren Identitätsanbieter, um das Problem zu beheben. |
| AADSTS50000 | TokenIssuanceError: Es besteht ein Problem mit dem Anmeldedienst. [Öffnen Sie ein Supportticket](../fundamentals/active-directory-troubleshooting-support-howto.md), um dieses Problem zu beheben. |
| AADSTS50001 | InvalidResource: Die Ressource ist deaktiviert oder nicht vorhanden. Überprüfen Sie den Code Ihrer App, um sicherzustellen, dass Sie die genaue Ressourcen-URL für die Ressource angegeben haben, auf die Sie zugreifen möchten.  |
| AADSTS50002 | NotAllowedTenant: Fehler bei der Anmeldung aufgrund von eingeschränktem Proxyzugriff auf dem Mandanten. Falls es sich um Ihre eigene Mandantenrichtlinie handelt, können Sie die Einschränkungseinstellungen des Mandanten ändern, um dieses Problem zu beheben. |
| AADSTS50003 | MissingSigningKey: Fehler bei der Anmeldung aufgrund eines fehlenden Signaturschlüssels oder Zertifikats. Der Grund hierfür kann sein, dass in der App kein Signaturschlüssel konfiguriert wurde. Sehen Sie sich die unter [../manage-apps/application-sign-in-problem-federated-sso-gallery.md#certificate-or-key-not-configured](../manage-apps/application-sign-in-problem-federated-sso-gallery.md#certificate-or-key-not-configured) erläuterten Lösungen an. Falls weiterhin Probleme auftreten, können Sie sich an den Besitzer oder Administrator der App wenden. |
| AADSTS50005 | DevicePolicyError: Der Benutzer hat versucht, sich über eine Plattform an einem Gerät anzumelden, die aufgrund einer Richtlinie für bedingten Zugriff zurzeit nicht unterstützt wird. |
| AADSTS50006 | InvalidSignature: Fehler bei der Signaturüberprüfung aufgrund einer ungültigen Signatur. |
| AADSTS50007 | PartnerEncryptionCertificateMissing: Das Partnerverschlüsselungszertifikat wurde für diese App nicht gefunden. [Öffnen Sie ein Supportticket](../fundamentals/active-directory-troubleshooting-support-howto.md) bei Microsoft, um dieses Problem zu beheben. |
| AADSTS50008 | InvalidSamlToken: Die SAML-Assertion fehlt oder wurde im Token falsch konfiguriert. Wenden Sie sich an Ihren Verbundanbieter. |
| AADSTS50010 | AudienceUriValidationFailed: Fehler bei der Überprüfung des Benutzergruppen-URI für die App, da keine Tokenzielgruppen konfiguriert wurden. |
| AADSTS50011 | InvalidReplyTo: Die Antwortadresse fehlt, ist falsch konfiguriert oder stimmt nicht mit den für die App konfigurierten Antwortadressen überein.  Fügen Sie zur Behebung der Azure Active Directory-Anwendung diese fehlende Antwortadresse hinzu, oder bitten Sie einen Benutzer mit den Berechtigungen zum Verwalten Ihrer Anwendung in Active Directory, dies für Sie zu tun.|
| AADSTS50012 | AuthenticationFailed: Fehler bei der Authentifizierung aus einem der folgenden Gründe:<ul><li>Der Antragstellername des signierenden Zertifikats ist nicht autorisiert.</li><li>Eine übereinstimmende Richtlinie der vertrauenswürdige Autorität wurde für den autorisierten Antragstellernamen nicht gefunden.</li><li>Die Zertifikatskette ist ungültig.</li><li>Das Signaturzertifikat ist ungültig.</li><li>Die Richtlinie ist auf dem Mandanten nicht konfiguriert.</li><li>Der Fingerabdruck des Signaturzertifikats ist nicht autorisiert.</li><li>Die Clientassertion enthält eine ungültige Signatur.</li></ul> |
| AADSTS50013 | InvalidAssertion: Die Assertion ist aus verschiedenen Gründen ungültig: Der Aussteller des Tokens stimmt nicht mit der API-Version innerhalb des gültigen Zeitraums überein, ist abgelaufen, ist falsch formatiert, Aktualisierungstoken in der Assertion ist kein primäres Aktualisierungstoken. |
| AADSTS50014 | GuestUserInPendingState: Die Einlösung des Benutzers steht aus. Das Gastkonto wurde noch nicht vollständig erstellt. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState: Für den Benutzer ist ein Nachweis der Volljährigkeit erforderlich. |
| AADSTS50017 | CertificateValidationFailed: Fehler bei der Überprüfung der Zertifizierung aus den folgenden Gründen:<ul><li>Das Ausstellerzertifikat kann in der Liste mit den vertrauenswürdigen Zertifikaten nicht gefunden werden.</li><li>Das erwartete CrlSegment-Element kann nicht gefunden werden.</li><li>Das Ausstellerzertifikat kann in der Liste mit den vertrauenswürdigen Zertifikaten nicht gefunden werden.</li><li>Delta-Zertifikatsperrlisten-Verteilungspunkt wurde ohne entsprechenden Zertifikatsperrlisten-Verteilungspunkt konfiguriert.</li><li>Aufgrund eines Timeoutfehlers können keine gültigen Zertifikatsperrlisten-Segmente abgerufen werden.</li><li>Die Zertifikatsperrliste kann nicht heruntergeladen werden.</li></ul>Wenden Sie sich an den Administrator des Mandanten. |
| AADSTS50020 | UserUnauthorized: Benutzer sind nicht autorisiert, diesen Endpunkt aufzurufen. |
| AADSTS50027 | InvalidJwtToken: Das JWT-Token ist aus den folgenden Gründen ungültig:<ul><li>Enthält keinen Nonce-Anspruch, untergeordneten Anspruch</li><li>Antragstellerbezeichner stimmt nicht überein</li><li>Doppelter Anspruch in idToken-Ansprüchen</li><li>Unerwarteter Aussteller</li><li>Unerwartete Zielgruppe</li><li>Liegt nicht innerhalb des gültigen Zeitbereichs </li><li>Tokenformat ist fehlerhaft</li><li>Fehler bei Signaturüberprüfung für das Token für die externe ID vom Aussteller.</li></ul> |
| AADSTS50029 | Ungültiger URI: Domänenname enthält ungültige Zeichen. Wenden Sie sich an den Administrator des Mandanten. |
| AADSTS50032 | WeakRsaKey: Gibt den fehlerhaften Benutzerversuch an, einen schwachen RSA-Schlüssel zu verwenden. |
| AADSTS50033 | RetryableError: Gibt einen vorübergehenden Fehler an, der nicht im Zusammenhang mit den Datenbankvorgängen steht. |
| AADSTS50034 | UserAccountNotFound: Zum Anmelden bei dieser Anwendung muss das Konto dem Verzeichnis hinzugefügt werden. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt: Der Salt-Wert, der für das Generieren eines paarweisen Bezeichners erforderlich ist, fehlt im Prinzip. Wenden Sie sich an den Administrator des Mandanten. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer: Der Antragsteller stimmt nicht mit dem Ausstelleranspruch in der Clientassertion überein. Wenden Sie sich an den Administrator des Mandanten. |
| AADSTS50049 | NoSuchInstanceForDiscovery: Unbekannte oder ungültige Instanz. |
| AADSTS50050 | MalformedDiscoveryRequest: Die Anforderung ist falsch formatiert. |
| AADSTS50053 | IdsLocked: Das Konto ist gesperrt, weil der Benutzer zu häufig versucht hat, sich mit einer falschen Benutzer-ID bzw. einem falschen Kennwort anzumelden. |
| AADSTS50055 | InvalidPasswordExpiredPassword: Das Kennwort ist abgelaufen. |
| AADSTS50056 | Ungültiges oder NULL-Kennwort: Das Kennwort ist für diesen Benutzer nicht im Speicher vorhanden. |
| AADSTS50057 | UserDisabled: Das Benutzerkonto ist deaktiviert. Das Konto wurde von einem Administrator deaktiviert. |
| AADSTS50058 | UserInformationNotProvided: Dies bedeutet, dass ein Benutzer nicht angemeldet ist. Dieser Fehler kommt häufig vor und wird erwartet, wenn ein Benutzer nicht authentifiziert wurde und sich noch nicht angemeldet hat.</br>Wenn dieser Fehler in einem SSO-Kontext gemeldet wird, in dem sich der Benutzer zuvor angemeldet hat, bedeutet dies, dass die SSO-Sitzung nicht gefunden wurde oder ungültig war.</br>Dieser Fehler kann an die Anwendung zurückgegeben werden, wenn prompt=none angegeben wird. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided: Es wurden keine Informationen zur Identifizierung des Mandanten in der Anforderung gefunden bzw. nicht über angegebene Anmeldeinformationen impliziert. Der Benutzer kann sich an den Mandantenadministrator wenden, um das Problem zu lösen. |
| AADSTS50061 | SignoutInvalidRequest: Die Anforderung zur Abmeldung ist ungültig. |
| AADSTS50064 | CredentialAuthenticationError: Fehler beim Überprüfen der Anmeldeinformationen bezüglich Benutzername und Kennwort. |
| AADSTS50068 | SignoutInitiatorNotParticipant: Fehler beim Abmelden. Die App, die den Abmeldevorgang eingeleitet hat, ist kein Teilnehmer der aktuellen Sitzung. |
| AADSTS50070 | SignoutUnknownSessionIdentifier: Fehler beim Abmelden. In der Abmeldeanforderung wurde ein Namensbezeichner angegeben, der nicht mit der/den vorhandenen Sitzung(en) übereinstimmt. |
| AADSTS50071 | SignoutMessageExpired: Die Abmeldeanforderung ist abgelaufen. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt: Der Benutzer muss sich für zweistufige Authentifizierung (interaktiv) registrieren. |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt: Starke Authentifizierung ist erforderlich, und der Benutzer hat die MFA-Überprüfung nicht bestanden. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired: Aufgrund einer Konfigurationsänderung durch den Administrator oder aufgrund einer Verschiebung an einen neuen Standort muss der Benutzer für den Zugriff auf die Ressource eine mehrstufige Authentifizierung verwenden. Wiederholen Sie den Vorgang mit einer neuen Autorisierungsanforderung für die Ressource. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired: Aufgrund einer Konfigurationsänderung durch den Administrator oder eines neuen Standorts des Benutzers muss der Benutzer mehrstufige Authentifizierung verwenden. |
| AADSTS50085 | Für Aktualisierungstoken ist eine IdP-Anmeldung per sozialem Netzwerk erforderlich. Bitten Sie Benutzer, sich per Benutzername und Kennwort erneut anzumelden. |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError: Der Dienst ist vorübergehend nicht verfügbar. Versuchen Sie es erneut. |
| AADSTS50089 | Flow-Token abgelaufen: Fehler bei der Authentifizierung. Bitten Sie den Benutzer, sich mit Benutzername und Kennwort erneut anzumelden. |
| AADSTS50097 | DeviceAuthenticationRequired: Geräteauthentifizierung ist erforderlich. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized: Die JWT-Signatur ist ungültig. |
| AADSTS50105 | EntitlementGrantsNotFound: Der angemeldete Benutzer ist keiner Rolle für die angemeldete App zugewiesen. Weisen Sie den Benutzer der App zu. Weitere Informationen finden Sie unter [../manage-apps/application-sign-in-problem-federated-sso-gallery.md#user-not-assigned-a-role](../manage-apps/application-sign-in-problem-federated-sso-gallery.md#user-not-assigned-a-role). |
| AADSTS50107 | InvalidRealmUri: Das angeforderte Verbundbereichsobjekt ist nicht vorhanden. Wenden Sie sich an den Administrator des Mandanten. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat: Problem mit JWT-Header. Wenden Sie sich an den Administrator des Mandanten. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter: Die Anspruchstransformation enthält ungültige Eingabeparameter. Wenden Sie sich an den Administrator des Mandanten, um die Richtlinie zu aktualisieren. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt: Die Anmeldung wurde aufgrund einer Kennwortzurücksetzung oder eines Kennwortregistrierungseintrags unterbrochen. |
| AADSTS50126 | InvalidUserNameOrPassword: Fehler beim Überprüfen der Anmeldeinformationen aufgrund eines ungültigen Benutzernamens oder Kennworts. |
| AADSTS50127 | BrokerAppNotInstalled: Der Benutzer muss für den Zugriff auf diesen Inhalt eine Broker-App installieren. |
| AADSTS50128 | Ungültiger Domänenname: Es wurden keine Informationen zur Identifizierung des Mandanten in der Anforderung gefunden bzw. nicht über angegebene Anmeldeinformationen impliziert. |
| AADSTS50129 | DeviceIsNotWorkplaceJoined: Um das Gerät zu registrieren, ist ein Workplace Join erforderlich. |
| AADSTS50131 | ConditionalAccessFailed: Gibt verschiedene bedingte Zugriffsfehler an, z. B. schlechten Windows-Gerätezustand, aufgrund verdächtiger Aktivitäten blockierte Anforderung, Zugriffsrichtlinie oder Sicherheitsrichtlinienentscheidungen. |
| AADSTS50132 | SsoArtifactInvalidOrExpired: Die Sitzung ist aufgrund von Kennwortablauf oder aktueller Kennwortänderung ungültig. |
| AADSTS50133 | SsoArtifactRevoked: Die Sitzung ist aufgrund von Kennwortablauf oder aktueller Kennwortänderung ungültig. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter: Falsches Rechenzentrum. Um eine Anforderung zu autorisieren, die von einer App im OAuth 2.0-Geräteflow eingeleitet wurde, muss die autorisierende Seite sich im selben Rechenzentrum befinden wie demjenigen, aus dem die ursprüngliche Anforderung stammt. |
| AADSTS50135 | PasswordChangeCompromisedPassword: Eine Kennwortänderung ist aufgrund des Kontorisikos erforderlich. |
| AADSTS50136 | RedirectMsaSessionToApp: Einzelne MSA-Sitzung erkannt. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken: Die Sitzung ist aufgrund eines fehlenden externen Aktualisierungstokens ungültig. |
| AADSTS50140 | KmsiInterrupt: Dieser Fehler ist aufgetreten, weil beim Anmelden des Benutzers der Interruptvorgang „Angemeldet bleiben“ aufgetreten ist. [Öffnen Sie ein Supportticket](../fundamentals/active-directory-troubleshooting-support-howto.md) mit der Korrelations-ID, Anforderungs-ID und dem Fehlercode, um weitere Details anzuzeigen. |
| AADSTS50143 | Sitzungskonflikt: Die Sitzung ist ungültig, weil der Benutzermandant aufgrund einer anderen Ressource nicht mit dem Domänenhinweis übereinstimmt. [Öffnen Sie ein Supportticket](../fundamentals/active-directory-troubleshooting-support-howto.md) mit der Korrelations-ID, Anforderungs-ID und dem Fehlercode, um weitere Details anzuzeigen. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword: Das Active Directory-Kennwort des Benutzers ist abgelaufen. Generieren Sie ein neues Kennwort für den Benutzer, oder lassen Sie den Benutzer das Self-Service-Tool für die Zurücksetzung verwenden, um sein Kennwort zurückzusetzen. |
| AADSTS50146 | MissingCustomSigningKey: Diese App muss mit einem anwendungsspezifischen Signaturschlüssel konfiguriert werden. Entweder ist dies nicht der Fall, oder der Schlüssel ist abgelaufen oder noch nicht gültig. |
| AADSTS50147 | MissingCodeChallenge: Die Größe des Codeabfrageparameters ist ungültig. |
| AADSTS50155 | DeviceAuthenticationFailed: Fehler bei der Geräteauthentifizierung für diesen Benutzer. |
| AADSTS50158 | ExternalSecurityChallenge: Externe Sicherheitsabfrage wurde nicht erfüllt. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration: Vom externen Anbieter gesendete Ansprüche sind nicht ausreichend, oder vom externen Anbieter angeforderter Anspruch fehlt. |
| AADSTS50166 | ExternalClaimsProviderThrottled: Fehler beim Senden der Anforderung an den Anspruchsanbieter. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired: Der Client ist in der Lage, über die Windows 10-Kontenerweiterung ein SSO-Token abzurufen, aber das Token wurde in der Anforderung nicht gefunden, oder das angegebene Token ist abgelaufen. |
| AADSTS50169 | InvalidRequestBadRealm: Der Bereich ist kein konfigurierter Bereich des aktuellen Dienstnamespace. |
| AADSTS50170 | MissingExternalClaimsProviderMapping: Die externe Steuerelementzuordnung fehlt. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers: Die externe Überprüfung wird für Passthrough-Benutzer nicht unterstützt. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers: Sitzungssteuerung wird für Passthrough-Benutzer nicht unterstützt. |
| AADSTS50180 | WindowsIntegratedAuthMissing: Integrierte Windows-Authentifizierung ist erforderlich. Aktivieren Sie den Mandanten für das nahtlose einmalige Anmelden. |
| AADSTS50187 | DeviceInformationNotProvided: Der Dienst konnte das Gerät nicht authentifizieren. |
| AADSTS50196 | LoopDetected: Eine Clientschleife wurde erkannt. Überprüfen Sie die Logik der App, um sicherzustellen, dass das Zwischenspeichern von Token implementiert ist und dass Fehlerbedingungen ordnungsgemäß behandelt werden.  Die App hat zu viele der gleichen Anforderungen in einem zu kurzen Zeitraum ausgeführt. Dies weist darauf hin, dass sie sich in einem fehlerhaften Zustand befindet oder böswillig Token anfordert. |
| AADSTS50197 | ConflictingIdentities: Der Benutzer konnte nicht gefunden werden. Versuchen Sie erneut, sich anzumelden. |
| AADSTS50199 | CmsiInterrupt: Aus Sicherheitsgründen ist für diese Anforderung eine Benutzerbestätigung erforderlich.  Da es sich hierbei um einen Fehler vom Typ „interaction_required“ handelt, sollte der Client eine interaktive Authentifizierung durchführen.  Dies liegt daran, dass eine System-WebView verwendet wurde, um ein Token für eine native Anwendung anzufordern. Der Benutzer muss aufgefordert werden, zu erfragen, ob es sich tatsächlich um die App handelt, bei der er sich anmelden wollte. Um diese Aufforderung zu vermeiden, sollte der Umleitungs-URI Teil der folgenden sicheren Liste sein: <br />http://<br />https://<br />msauth://(nur iOS)<br />msauthv2://(nur iOS)<br />chrome-extension://(nur Chrome-Desktopbrowser) |
| AADSTS51000 | RequiredFeatureNotEnabled: Das Feature ist deaktiviert. |
| AADSTS51001 | DomainHintMustbePresent: Domänenhinweis ist für lokale Sicherheits-ID oder lokalen UPN nicht vorhanden. |
| AADSTS51004 | UserAccountNotInDirectory: Benutzerkonto ist nicht im Verzeichnis vorhanden. |
| AADSTS51005 | TemporaryRedirect: Äquivalent zu HTTP-Status 307. Dies bedeutet, dass die angeforderten Informationen unter dem im Location-Header angegebenen URI vorhanden sind. Wenn Sie diesen Status erhalten, folgen Sie dem Location-Header, der der Antwort zugeordnet ist. Wenn die ursprüngliche Anforderungsmethode POST war, verwendet die umgeleitete Anforderung ebenfalls die POST-Methode. |
| AADSTS51006 | ForceReauthDueToInsufficientAuth: Integrierte Windows-Authentifizierung ist erforderlich. Der Benutzer hat sich mit einem Sitzungstoken angemeldet, in dem der Anspruch der integrierten Windows-Authentifizierung fehlt. Bitten Sie den Benutzer, sich erneut anzumelden. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn: Der Benutzer hat seine Zustimmung für den Zugriff auf LinkedIn-Ressourcen nicht gegeben. |
| AADSTS53000 | DeviceNotCompliant: Die Richtlinie für bedingten Zugriff erfordert ein konformes Gerät, und das Gerät ist nicht konform. Der Benutzer muss das Gerät mit einem genehmigten MDM-Anbieter (z.B. Intune) registrieren. |
| AADSTS53001 | DeviceNotDomainJoined: Für die Richtlinie für bedingten Zugriff ist ein in die Domäne eingebundenes Gerät erforderlich, und das Gerät ist nicht in eine Domäne eingebunden. Bitten Sie den Benutzer, ein in die Domäne eingebundenes Gerät zu nutzen. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp: Die verwendete App ist keine genehmigte App für bedingten Zugriff. Der Benutzer muss eine der Apps aus der Liste mit den genehmigten Apps nutzen, um Zugriff zu erhalten. |
| AADSTS53003 | BlockedByConditionalAccess: Zugriff wurde von Richtlinien für bedingten Zugriff blockiert. Die Zugriffsrichtlinie erlaubt nicht die Ausstellung von Token. |
| AADSTS53004 | ProofUpBlockedDueToRisk: Der Benutzer muss den Registrierungsprozess für die mehrstufige Authentifizierung durchführen, bevor er auf diesen Inhalt zugreifen kann. Benutzer sollten sich für die mehrstufige Authentifizierung registrieren. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist: Der Benutzer oder Administrator hat der Verwendung der Anwendung mit ID X nicht zugestimmt. Senden Sie eine interaktive Autorisierungsanforderung für diesen Benutzer und diese Ressource. |
| AADSTS65004 | UserDeclinedConsent: Der Benutzer hat seine Zustimmung für den Zugriff auf die App abgelehnt. Bitten Sie den Benutzer, die Anmeldung zu wiederholen und die Zustimmung für die App zu erteilen.|
| AADSTS65005 | MisconfiguredApplication: Die für die App erforderliche Liste für den Ressourcenzugriff enthält keine Apps, die von der Ressource ermittelt werden können, die Client-App hat den Zugriff auf eine Ressource angefordert, die nicht in der erforderlichen Liste für den Ressourcenzugriff angegeben ist, oder der Graph-Dienst hat „Fehlerhafte Anforderung“ oder „Ressource nicht gefunden“ zurückgegeben. Wenn die App SAML unterstützt, haben Sie die App ggf. mit dem falschen Bezeichner (Entität) konfiguriert. Probieren Sie es mit der Lösung, die unter dem folgenden Link für SAML angegeben ist: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](../manage-apps/application-sign-in-problem-federated-sso-gallery.md?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav) |
| AADSTS650052 | Die App benötigt Zugriff auf einen Dienst `(\"{name}\")`, den Ihre Organisation `\"{organization}\"` nicht abonniert oder aktiviert hat. Wenden Sie sich an Ihren IT-Administrator, damit dieser die Konfiguration Ihrer Dienstabonnements überprüft. |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant: Fehler bei der Authentifizierung. Das Aktualisierungstoken ist ungültig. Der Fehler kann aus den folgenden Gründen auftreten:<ul><li>Der Bindungsheader des Tokens ist leer.</li><li>Der Tokenbindungshash stimmt nicht überein.</li></ul> |
| AADSTS70001 | UnauthorizedClient: Die Anwendung ist deaktiviert. |
| AADSTS70002 | InvalidClient: Fehler beim Überprüfen der Anmeldeinformationen. Der angegebene Wert für „client_secret“ entspricht nicht dem erwarteten Wert für diesen Client. Korrigieren Sie „client_secret“, und versuchen Sie es noch mal. Weitere Informationen finden Sie unter [Anfordern eines Zugriffstokens mithilfe des Autorisierungscodes](v2-oauth2-auth-code-flow.md#request-an-access-token). |
| AADSTS70003 | UnsupportedGrantType: Die App hat einen nicht unterstützten Gewährungstyp zurückgegeben. |
| AADSTS70004 | InvalidRedirectUri: Die App hat einen ungültigen Umleitungs-URI zurückgegeben. Die vom Client angegebene Umleitungsadresse stimmt nicht mit den konfigurierten Adressen oder anderen Adressen in der Liste mit den OIDC-Genehmigungen überein. |
| AADSTS70005 | UnsupportedResponseType: Die App hat aus den folgenden Gründen einen nicht unterstützten Antworttyp zurückgegeben:<ul><li>Der Antworttyp „token“ ist für die App nicht aktiviert.</li><li>Für den Antworttyp „id_token“ ist der Bereich „OpenID“ erforderlich: - Enthält einen nicht unterstützten OAuth-Parameterwert im codierten wctx-Element.</li></ul> |
| AADSTS70007 | UnsupportedResponseMode: Die App hat beim Anfordern eines Tokens den nicht unterstützten Wert `response_mode` zurückgegeben.  |
| AADSTS70008 | ExpiredOrRevokedGrant: Das Aktualisierungstoken ist aufgrund von Inaktivität abgelaufen. Das Token wurde auf XXX ausgestellt und war für einen bestimmten Zeitraum inaktiv. |
| AADSTS70011 | InvalidScope: Der von der App angeforderte Bereich ist ungültig. |
| AADSTS70012 | MsaServerError: Beim Authentifizieren eines MSA-Benutzers (Consumer) ist ein Serverfehler aufgetreten. Versuchen Sie es erneut. [Öffnen Sie ein Supportticket](../fundamentals/active-directory-troubleshooting-support-howto.md), wenn der Fehler weiterhin auftritt. |
| AADSTS70016 | AuthorizationPending: OAuth 2.0-Geräteflussfehler. Die Autorisierung steht aus. Das Gerät wird den Abruf der Anforderung wiederholen. |
| AADSTS70018 | BadVerificationCode: Ungültiger Überprüfungscode, weil der Benutzer den falschen Benutzercode für den Gerätecode-Datenfluss eingegeben hat. Die Autorisierung wird nicht genehmigt. |
| AADSTS70019 | CodeExpired: Der Überprüfungscode ist abgelaufen. Bitten Sie den Benutzer, die Anmeldung erneut durchzuführen. |
| AADSTS75001 | BindingSerializationError: Während der SAML-Nachrichtenbindung ist ein Fehler aufgetreten. |
| AADSTS75003 | UnsupportedBindingError: Die Anwendung hat einen Fehler zu einer nicht unterstützten Bindung zurückgegeben (SAML-Protokollantwort kann nicht über andere Bindungen als HTTP POST gesendet werden). |
| AADSTS75005 | Saml2MessageInvalid: Azure AD unterstützt die von der App für SSO gesendete SAML-Anforderung nicht. |
| AADSTS7500514 | Es wurde kein unterstützter Typ von SAML-Antwort gefunden. Die unterstützten Antworttypen sind „Response“ (im XML-Namespace „urn:oasis:names:tc:SAML:2.0:protocol“) und „Assertion“ (im XML-Namespace „urn:oasis:names:tc:SAML:2.0:assertion“). Anwendungsfehler: Dieser Fehler wird vom Entwickler behoben.|
| AADSTS75008 | RequestDeniedError: Die Anforderung von der App wurde abgelehnt, da die SAML-Anforderung über ein unerwartetes Ziel verfügt. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims: Die Authentifizierungsmethode, mit der sich der Benutzer beim Dienst authentifiziert hat, stimmt nicht mit der angeforderten Authentifizierungsmethode überein. |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy: Die SAML2-Authentifizierungsanforderung weist eine ungültige NameIdPolicy auf. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable: Der Authentifizierungs-Agent kann keine Verbindung mit Active Directory herstellen. Stellen Sie sicher, dass die Agent-Server Mitglieder derselben AD-Gesamtstruktur wie die Benutzer sind, deren Kennwörter überprüft werden müssen, und dass sie eine Verbindung mit Active Directory herstellen können. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout: Für die Anforderung zur Kennwortüberprüfung ist ein Timeout aufgetreten. Stellen Sie sicher, dass Active Directory verfügbar ist und auf Anforderungen der Agents antwortet. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException: Unbekannter Fehler beim Verarbeiten der Antwort vom Authentifizierungs-Agent. Wiederholen Sie die Anforderung. Wenn der Fehler weiterhin auftritt, [öffnen Sie ein Supportticket](../fundamentals/active-directory-troubleshooting-support-howto.md), um weitere Details zum Fehler zu erhalten. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem: Der Authentifizierungs-Agent kann das Kennwort des Benutzers nicht überprüfen. Suchen Sie in den Agent-Protokollen nach weiteren Informationen, und überprüfen Sie, ob Active Directory erwartungsgemäß funktioniert. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException: Der Authentifizierungs-Agent kann das Kennwort nicht entschlüsseln. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours: Benutzer haben versucht, sich außerhalb der zulässigen Zeiten (in AD angegeben) anzumelden. |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew: Der Authentifizierungsversuch konnte nicht abgeschlossen werden, da zwischen dem Computer, auf dem der Authentifizierungs-Agent ausgeführt wird, und AD eine Zeitabweichung besteht. Beheben Sie die Probleme mit der Zeitsynchronisierung. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated: Fehler beim Kerberos-Authentifizierungsversuch. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported: Das Authentifizierungspaket wird nicht unterstützt. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader: Es wurde kein Autorisierungsheader gefunden. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn: Der Mandant ist nicht für nahtloses SSO aktiviert. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat: Das Kerberos-Ticket des Benutzers kann nicht überprüft werden. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid: Fehler beim nahtlosen einmaligen Anmelden, da das Kerberos-Ticket des Benutzers abgelaufen oder ungültig ist. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed: Das Benutzerobjekt konnte anhand der Informationen im Kerberos-Ticket des Benutzers nicht gefunden werden. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn: Der Benutzer, der versucht, sich an Azure AD anzumelden, unterscheidet sich von dem Benutzer, der am Gerät angemeldet ist. |
| AADSTS90002 | InvalidTenantName: Der Mandantenname wurde im Datenspeicher nicht gefunden. Stellen Sie sicher, dass Sie über die richtige Mandanten-ID verfügen. |
| AADSTS90004 | InvalidRequestFormat: Die Anforderung ist nicht ordnungsgemäß formatiert. |
| AADSTS90005 | InvalidRequestWithMultipleRequirements: Die Anforderung konnte nicht ausgeführt werden. Die Anforderung ist ungültig, weil der Bezeichner und der Anmeldehinweis nicht zusammen verwendet werden können.  |
| AADSTS90006 | ExternalServerRetryableError: Der Dienst ist vorübergehend nicht verfügbar.|
| AADSTS90007 | InvalidSessionId: Ungültige Anforderung. Die übergebene Sitzungs-ID kann nicht analysiert werden. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission: Der Benutzer oder der Administrator hat der Verwendung der Anwendung nicht zugestimmt. Die Anwendung benötigt mindestens Zugriff auf Azure AD, indem die Berechtigung zur Anmeldung und zum Lesen des Benutzerprofils erteilt wird. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier: Die Anwendung fordert ein Token für sich selbst an. Dieses Szenario wird nur unterstützt, wenn die angegebene Ressource die GUID-basierte Anwendungs-ID verwendet. |
| AADSTS90010 | NotSupported: Der Algorithmus kann nicht erstellt werden. |
| AADSTS90012 | RequestTimeout: Timeout bei der Anforderung. |
| AADSTS90013 | InvalidUserInput: Die Eingabe des Benutzers ist ungültig. |
| AADSTS90014 | MissingRequiredField: Dieser Fehlercode wird in unterschiedlichen Fällen angezeigt, wenn ein erwartetes Feld in den Anmeldeinformationen nicht vorhanden ist. |
| AADSTS90015 | QueryStringTooLong: Die Abfragezeichenfolge ist zu lang. |
| AADSTS90016 | MissingRequiredClaim: Das Zugriffstoken ist nicht gültig. Der erforderliche Anspruch fehlt. |
| AADSTS90019 | MissingTenantRealm: Azure AD konnte die Mandanten-ID nicht aus der Anforderung ermitteln. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat: Das Format des Benutzerprinzipalnamens ist ungültig oder entspricht nicht dem erwarteten `name[/host][@realm]`-Format. Der Prinzipalname ist erforderlich, Host und Bereich sind optional und können auf NULL festgelegt werden. |
| AADSTS90023 | InvalidRequest: Die Authentifizierungsdienstanforderung ist ungültig. |
| AADSTS9002313 | InvalidRequest: Anforderung ist falsch formatiert oder ungültig. - Der Grund für dieses Problem ist, dass für die Anforderung an einen bestimmten Endpunkt ein Fehler aufgetreten ist. Die empfohlene Vorgehensweise für dieses Problem besteht darin, eine Fiddler-Ablaufverfolgung zum Auftreten des Fehlers zu erhalten und zu ermitteln, ob die Anforderung richtig formatiert ist. |
| AADSTS90024 | RequestBudgetExceededError: Es wurde ein vorübergehender Fehler festgestellt. Versuchen Sie es erneut. |
| AADSTS90033 | MsodsServiceUnavailable: Der Microsoft Online Directory Service (MSODS) ist nicht verfügbar. |
| AADSTS90036 | MsodsServiceUnretryableFailure: Unerwarteter, nicht wiederholbarer Fehler vom WCF-Dienst, der vom MSODS gehostet wird. [Öffnen Sie ein Supportticket](../fundamentals/active-directory-troubleshooting-support-howto.md), um weitere Details zum Fehler zu erhalten. |
| AADSTS90038 | NationalCloudTenantRedirection: Der angegebene Mandant „Y“ gehört zur nationalen Cloud „X“. Die aktuelle Cloudinstanz „Z“ bildet keinen Verbund mit „X“. Ein Cloudumleitungsfehler wird zurückgegeben. |
| AADSTS90043 | NationalCloudAuthCodeRedirection: Das Feature ist deaktiviert. |
| AADSTS90051 | InvalidNationalCloudId: Der nationale Cloudbezeichner enthält einen ungültigen Cloudbezeichner. |
| AADSTS90055 | TenantThrottlingError: Zu viele eingehende Anforderungen. Diese Ausnahme wird für blockierte Mandanten ausgelöst. |
| AADSTS90056 | BadResourceRequest: Um den Code für ein Zugriffstoken einzulösen, sollte die App eine POST-Anforderung an den `/token`-Endpunkt senden. Außerdem sollten Sie zuvor einen Autorisierungscode bereitstellen und ihn in der POST-Anforderung an den `/token`-Endpunkt senden. In diesem Artikel finden Sie einen Überblick über den OAuth 2.0-Autorisierungscodeflow: [../azuread-dev/v1-protocols-oauth-code.md](../azuread-dev/v1-protocols-oauth-code.md). Sie müssen den Benutzer an den `/authorize`-Endpunkt weiterleiten, der einen authorization_code zurückgibt. Durch das Senden einer Anforderung an den `/token`-Endpunkt erhält der Benutzer das Zugriffstoken. Melden Sie sich am Azure-Portal an, und überprüfen Sie **App-Registrierungen > Endpunkte**, um sicherzustellen, dass die beiden Endpunkte ordnungsgemäß konfiguriert wurden. |
| AADSTS90072 | PassThroughUserMfaError: Das externe Konto, mit dem sich der Benutzer anmeldet, ist nicht im Mandanten für die Anmeldung vorhanden. Aus diesem Grund kann der Benutzer die MFA-Anforderungen für den Mandanten nicht erfüllen. Dieser Fehler kann auch auftreten, wenn die Benutzer synchronisiert werden, aber das Attribut „ImmutableID (sourceAnchor)“ zwischen Active Directory und Azure AD nicht übereinstimmt. Das Konto muss zunächst als externer Benutzer im Mandanten hinzugefügt werden. Melden Sie sich ab, und melden Sie sich mit einem anderen Azure AD-Benutzerkonto an. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid: Fehler beim Verarbeiten einer WS-Verbundnachricht durch den Dienst. Die Nachricht ist ungültig. |
| AADSTS90082 | OrgIdWsFederationNotSupported: Die ausgewählte Authentifizierungsrichtlinie für die Anforderung wird aktuell nicht unterstützt. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed: Gastkonten sind für diesen Standort nicht zugelassen. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed: Der Dienst kann kein Token ausstellen, weil das Unternehmensobjekt noch nicht bereitgestellt wurde. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired: Das DA-Benutzertoken ist abgelaufen. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed: Fehler beim Erstellen der WS-Verbundnachricht aus dem URI. |
| AADSTS90090 | GraphRetryableError: Der Dienst ist vorübergehend nicht verfügbar. |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized: Es wurde ein Graph mit einem unzulässigen Fehlercode für die Anforderung zurückgegeben. |
| AADSTS90094 | AdminConsentRequired: Die Zustimmung des Administrators ist erforderlich. |
| AADSTS900382 | Vertrauliche Clients werden in einer cloudübergreifenden Anforderung nicht unterstützt. |
| AADSTS90099 | Die Anwendung „{appId}“ ({appName}) wurde im Mandanten „{tenant}“ nicht autorisiert. Anwendungen müssen für den Zugriff auf den Kundenmandanten autorisiert werden, bevor die vom Partner delegierten Administratoren sie verwenden können. Erteilen Sie vorab die Zustimmung, oder führen Sie die entsprechende Partner Center-API zum Autorisieren der Anwendung aus. |
| AADSTS90100 | InvalidRequestParameter: Der Parameter ist leer oder ungültig. |
| AADSTS901002 | AADSTS901002: Der Anforderungsparameter „resource“ wird nicht unterstützt. |
| AADSTS90101 | InvalidEmailAddress: Es wurde keine gültige E-Mail-Adresse angegeben. Die E-Mail-Adresse muss dieses Format aufweisen: `someone@example.com`. |
| AADSTS90102 | InvalidUriParameter: Der Wert muss ein absoluter URI sein. |
| AADSTS90107 | InvalidXml: Die Anforderung ist nicht gültig. Stellen Sie sicher, dass Ihre Daten keine ungültigen Zeichen enthalten.|
| AADSTS90114 | InvalidExpiryDate: Der Ablaufzeitstempel für das Massentoken wird dazu führen, dass ein abgelaufenes Token ausgestellt wird. |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode: Der Benutzercode ist NULL oder leer.|
| AADSTS90120 | InvalidDeviceFlowRequest: Die Anforderung wurde bereits autorisiert oder abgelehnt. |
| AADSTS90121 | InvalidEmptyRequest: Ungültige, leere Anforderung.|
| AADSTS90123 | IdentityProviderAccessDenied: Das Token kann nicht ausgestellt werden, weil der Identitätsanbieter oder der Anbieter für die Anspruchsausstellung die Anforderung abgelehnt hat. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported: Die Ressource wird über die Endpunkte `/common` oder `/consumers` nicht unterstützt. Verwenden Sie stattdessen `/organizations` oder den mandantenspezifischen Endpunkt. |
| AADSTS90125 | DebugModeEnrollTenantNotFound: Der Benutzer ist nicht im System vorhanden. Stellen Sie sicher, dass Sie den Benutzernamen richtig eingegeben haben. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred: Der Benutzertyp wird auf diesem Endpunkt nicht unterstützt. Das System konnte den Benutzermandanten nicht aus dem Benutzernamen ableiten. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported: Die Anwendung wird über die Endpunkte `/common` oder `/consumers` nicht unterstützt. Verwenden Sie stattdessen `/organizations` oder den mandantenspezifischen Endpunkt. |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated: Es wurde ein nicht wiederholbarer Fehler festgestellt.|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound: Für den Benutzerprinzipal wurde der NGC-ID-Schlüssel nicht konfiguriert. |
| AADSTS130005 | NgcInvalidSignature: Fehler beim Überprüfen der NGC-Schlüsselsignatur.|
| AADSTS130006 | NgcTransportKeyNotFound: Der NGC-Transportschlüssel ist nicht auf dem Gerät konfiguriert. |
| AADSTS130007 | NgcDeviceIsDisabled: Das Gerät ist deaktiviert. |
| AADSTS130008 | NgcDeviceIsNotFound: Das durch den NGC-Schlüssel referenzierte Gerät wurde nicht gefunden. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce: Nonce für Anforderung nicht angegeben. |
| AADSTS140001 | InvalidSessionKey: Der Sitzungsschlüssel ist nicht gültig.|
| AADSTS165900 | InvalidApiRequest: Ungültige Anforderung. |
| AADSTS220450 | UnsupportedAndroidWebViewVersion: Die Chrome WebView-Version wird nicht unterstützt. |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource: Die Ressource ist nicht dafür konfiguriert, Nur-Gerätetoken zu akzeptieren. |
| AADSTS240001 | BulkAADJTokenUnauthorized: Der Benutzer ist nicht zum Registrieren von Geräten in Azure AD autorisiert. |
| AADSTS240002 | RequiredClaimIsMissing: Das id_token kann nicht zur Gewährung von `urn:ietf:params:oauth:grant-type:jwt-bearer` verwendet werden.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy: Der Mandantenadministrator hat eine Sicherheitsrichtlinie konfiguriert, die diese Anforderung blockiert. Überprüfen Sie die Sicherheitsrichtlinien, die auf Mandantenebene definiert sind, um festzustellen, ob Ihre Anforderung den Richtlinienanforderungen entspricht. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest: Die Anwendung wurde nicht im Verzeichnis/Mandanten gefunden. Dies kann auftreten, wenn die Anwendung nicht vom Administrator des Mandanten installiert wurde oder wenn sie von den Benutzern des Mandanten keine Zustimmung erhalten hat. Unter Umständen haben Sie den Bezeichnerwert für die Anwendung falsch konfiguriert oder die Authentifizierungsanforderung an den falschen Mandanten gesendet. |
| AADSTS700020 | InteractionRequired: Die Zugriffsgewährung erfordert eine Interaktion. |
| AADSTS700022 | InvalidMultipleResourcesScope: Der angegebene Wert für den Eingabeparameterbereich ist nicht gültig, weil er mehr als eine Ressource enthält. |
| AADSTS700023 | InvalidResourcelessScope: Der angegebene Wert für den Eingabeparameterbereich ist beim Anfordern eines Zugriffstokens nicht gültig. |
| AADSTS7000215 | Es wurde ein ungültiger geheimer Clientschlüssel bereitgestellt. Entwicklerfehler: Die App versucht, sich ohne die erforderlichen oder richtigen Authentifizierungsparameter anzumelden.|
| AADSTS7000222 | InvalidClientSecretExpiredKeysProvided: Die angegebenen geheimen Clientschlüssel sind abgelaufen. Erstellen Sie im Azure-Portal neue Schlüssel für Ihre App, oder verwenden Sie Zertifikatanmeldeinformationen, um die Sicherheit zu erhöhen: [https://aka.ms/certCreds](./active-directory-certificate-credentials.md) |
| AADSTS700005 | InvalidGrantRedeemAgainstWrongTenant: Der angegebene Autorisierungscode ist für einen anderen Mandanten bestimmt und wird daher abgelehnt. Der OAuth2-Autorisierungscode muss für denselben Mandanten verwendet werden, für den er bezogen wurde („/common“ oder „/{Mandanten-ID}“). |
| AADSTS1000000 | UserNotBoundError: Die Bindungs-API erfordert, dass sich der Azure AD-Benutzer auch bei einem externen Identitätsanbieter authentifiziert, was noch nicht geschehen ist. |
| AADSTS1000002 | BindCompleteInterruptError: Die Bindung wurde erfolgreich abgeschlossen, aber der Benutzer muss informiert werden. |
| AADSTS7000112 | UnauthorizedClientApplicationDisabled: Die Anwendung ist deaktiviert. |
| AADSTS7000114| Die Anwendung „appIdentifier“ ist nicht berechtigt, Im-Auftrag-von-Aufrufe auszuführen.|
| AADSTS7500529 | Der Wert „SAMLId-Guid“ ist keine gültige SAML-ID: Azure AD verwendet dieses Attribut, um das Attribut „InResponseTo“ der zurückgegebenen Antwort aufzufüllen. Die ID darf nicht mit einer Zahl beginnen, weshalb dem GUID-String häufig eine Zeichenfolge wie etwa „id“ vorangestellt wird. Beispielsweise ist id6c1c178c166d486687be4aaf5e482730 eine gültige ID. |

## <a name="next-steps"></a>Nächste Schritte

* Haben Sie eine Frage, oder können Sie nicht finden, was Sie suchen? Erstellen Sie ein GitHub-Problem, oder lesen Sie [Support- und Hilfeoptionen für Entwickler](./developer-support-help-options.md), um mehr über andere Möglichkeiten zu erfahren, wie Sie Hilfe und Unterstützung erhalten können.