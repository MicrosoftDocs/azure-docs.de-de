---
title: Verwenden von TypingDNA mit Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie TypingDNA in die Azure AD B2C-Authentifizierung integrieren, um die Identitätsüberprüfung basierend auf Benutzereingabemustern zu unterstützen. TypingDNA bietet ID-Überprüfungslösungen, die eine mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) erzwingen und unterstützt Sie bei der Einhaltung von SCA-Anforderungen für die Zahlungsdiensterichtlinie PSD2 (Payment Services Directive 2).
author: gargi-sinha
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 68617d86fda940c5d3752f2389088a8c729aebec
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108347"
---
# <a name="tutorial-for-configuring-typingdna-with-azure-active-directory-b2c"></a>Tutorial zum Konfigurieren von TypingDNA mit Azure Active Directory B2C

In dieser exemplarischen Vorgehensweise erfahren Sie, wie Sie eine Beispiel-App für Onlinezahlungen in Azure Active Directory B2C mit der TypingDNA-App integrieren. Mithilfe der TypingDNA-App können Azure AD B2C-Kunden die Transaktionsanforderungen der [Zahlungsdiensterichtlinie PSD2](https://www.typingdna.com/use-cases/sca-strong-customer-authentication) (Payment Services Directive 2) durch Tastatureingabedynamik und starke Kundenauthentifizierung (Strong Customer Authentication, SCA) erfüllen. Weitere Informationen zu TypingDNA finden Sie [hier](https://www.typingdna.com/).

 Azure AD B2C verwendet die Technologien von TypingDNA, um die Eingabemerkmale/-muster der Benutzer zu erfassen und bei jeder Authentifizierung aufzuzeichnen und auf Bekanntheit zu analysieren. Dadurch wird eine Schutzebene hinzugefügt, die mit dem Risiko einer Authentifizierung verbunden ist, und die Risikostufen werden bewertet. Azure AD B2C kann weitere Mechanismen aufrufen, um mehr Sicherheit zu schaffen, dass der Benutzer auch die Person ist, für die er sich ausgibt, indem Azure AD MFA aufgerufen, die E-Mail-Überprüfung erzwungen oder eine andere benutzerdefinierte Logik für Ihr Szenario verwendet wird.

>[!NOTE]
> Diese Beispielrichtlinie basiert auf dem Starter Pack [SocialAndLocalAccountsWithMfa](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa).

## <a name="scenario-description"></a>Beschreibung des Szenarios

![Screenshot des TypingDNA-Architekturdiagramms](./media/partner-typingdna/typingdna-architecture-diagram.png)

### <a name="sign-up"></a>Registrierung

1. Auf Azure AD B2C-Seiten wird die JavaScript-Bibliothek von TypingDNA zum Aufzeichnen des Eingabemusters des Benutzers verwendet. Beispielsweise werden der Benutzername und das Kennwort bei der Erstregistrierung und dann bei jeder Anmeldung zur Überprüfung aufgezeichnet.

2. Wenn der Benutzer die Seite übermittelt, berechnet die TypingDNA-Bibliothek das Eingabemuster des Benutzers. Anschließend werden die Informationen in ein ausgeblendetes Textfeld eingefügt, das Azure AD B2C gerendert hat. Dieses Feld ist mit CSS ausgeblendet.  

    Das [Beispiel enthält HTML-Dateien](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignUp.cshtml) mit den JavaScript- und CSS-Änderungen. Darauf wird in den Inhaltsdefinitionen `api.selfasserted.tdnasignin` und `api.selfasserted.tdnasignup` verwiesen. Informationen zum Hosten Ihrer HTML-Dateien finden Sie unter [Hosten des Seiteninhalts](./customize-ui-with-html.md#hosting-the-page-content).

3. Azure AD B2C verfügt jetzt über das Eingabemuster im Anspruchsbehälter, wenn der Benutzer seine Anmeldeinformationen übermittelt. Es muss eine API (Ihre API) zum Übergeben dieser Daten an den TypingDNA-REST-API-Endpunkt aufgerufen werden. Diese API ist im [Beispiel (TypingDNA-API-Interface)](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) enthalten.
4. Die API der mittleren Ebene übergibt dann die Daten des Eingabemusters an die TypingDNA-REST-API. Bei der Registrierung wird der [Endpunkt zur Benutzerüberprüfung](https://api.typingdna.com/index.html#api-API_Services-GetUser) aufgerufen, um zu bestätigen, dass der Benutzer noch nicht vorhanden war, und dann wird der Endpunkt zum [Speichern des Eingabemusters](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) aufgerufen, um das erste Eingabemuster des Benutzers zu speichern.

> [!NOTE]
> Bei allen Aufrufen des TypingDNA-REST-API-Endpunkts wird eine Benutzer-ID gesendet. Dabei muss es sich um einen Hashwert handeln. Azure AD B2C verwendet die Anspruchstransformation `HashObjectIdWithEmail`, um einen Hashvorgang für die E-Mail mit einem zufälligen Saltwert und einem geheimen Schlüssel auszuführen.  

Die REST-API-Aufrufe werden mit `validationTechnicalProfiles` in `LocalAccountSignUpWithLogonEmail-TDNA` modelliert:

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail-TDNA" />
    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="true"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser"/>

</ValidationTechnicalProfiles>

```

### <a name="sign-in"></a>Anmeldung

Bei nachfolgenden Anmeldungen wird das Eingabemuster des Benutzers auf die gleiche Weise wie bei der Registrierung mit dem [benutzerdefinierten HTML-Code](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignIn.cshtml) berechnet. Sobald das Eingabemuster im Azure AD B2C-Anspruchsbehälter enthalten ist, ruft Azure AD B2C Ihre API auf, um den TypingDNA-REST-API-Endpunkt aufzurufen. Der Endpunkt zur [Benutzerüberprüfung](https://api.typingdna.com/index.html#api-API_Services-GetUser) wird aufgerufen, um zu bestätigen, dass der Benutzer vorhanden ist. Als Nächstes wird der Endpunkt zum [Überprüfen des Eingabemusters](https://api.typingdna.com/index.html#api-API_Services-verifyTypingPattern) aufgerufen, um den `net_score`-Wert zurückzugeben. Dieser `net_score`-Wert gibt Aufschluss darüber, wie genau das Eingabemuster mit dem ursprünglichen Muster bei der Registrierung übereinstimmt.

Dieses Eingabemuster wird mit `validationTechnicalProfiles` in `SelfAsserted-LocalAccountSignin-Email-TDNA` modelliert:

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="login-NonInteractive"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="false"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-VerifyUser"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser">

        <Preconditions>

            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>saveTypingPattern</Value>

            <Value>False</Value>

            <Action>SkipThisValidationTechnicalProfile</Action>

            </Precondition>

        </Preconditions>

    </ValidationTechnicalProfile>

</ValidationTechnicalProfiles>

```

 Wenn der Benutzer ein Eingabemuster mit einem hohen `net_score`-Wert erhält, können Sie dieses Muster mithilfe des TypingDNA-Endpunkts zum [Speichern des Eingabemusters](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) speichern.  

Ihre API muss einen `saveTypingPattern`-Anspruch zurückgeben, wenn der TypingDNA-Endpunkt zum Speichern des Eingabemusters von Azure AD B2C (über Ihre API) aufgerufen werden soll.

Das Beispiel im Repository enthält eine API (TypingDNA-API-Interface), die mit den folgenden Eigenschaften konfiguriert ist.

- Trainingsmodus: Wenn für einen Benutzer weniger als zwei Muster gespeichert sind, wird der Benutzer immer zur Verwendung von MFA aufgefordert.

- Wenn für den Benutzer 2 bis 5 Muster gespeichert sind und der `net_score`-Wert kleiner als 50 ist, wird der Benutzer zur Verwendung von MFA aufgefordert.

- Wenn für den Benutzer mehr als 5 Muster gespeichert sind und der `net_score`-Wert kleiner als 65 ist, wird der Benutzer zur Verwendung von MFA aufgefordert.

Diese Schwellenwerte sollten auf Ihren Anwendungsfall angepasst werden.

- Nachdem Ihre API den `net_score`-Wert ausgewertet hat, sollte sie einen booleschen Anspruch (`promptMFA`) an B2C zurückgeben.

- Der `promptMFA`-Anspruch wird in einer Vorbedingung verwendet, um Azure AD MFA bedingt auszuführen.

```xml

<OrchestrationStep Order="3" Type="ClaimsExchange">

    <Preconditions>

        <Precondition Type="ClaimsExist" ExecuteActionsIf="true">

            <Value>isActiveMFASession</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>promptMFA</Value>

            <Value>False</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

    </Preconditions>

    <ClaimsExchanges>

        <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" />

    </ClaimsExchanges>

</OrchestrationStep>

```

## <a name="onboard-with-typingdna"></a>Integrieren von TypingDNA

1. Registrieren Sie sich [hier](https://www.typingdna.com/) für TypingDNA.
2. Melden Sie sich beim TypingDNA-Dashboard an, und rufen Sie einen **API-Schlüssel** und ein **API-Geheimnis** ab. Diese Werte werden später beim Einrichten der API-Schnittstelle benötigt.

## <a name="integrate-typingdna-with-azure-ad-b2c"></a>Integrieren von TypingDNA in Azure AD B2C

1. Hosten Sie [TypingDNA-API-Interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) beim Hostinganbieter Ihrer Wahl.
2. Ersetzen Sie in der [TypingDNA-API-Interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface)-Lösung alle Instanzen von `apiKey` und `apiSecret` durch die Anmeldeinformationen aus Ihrem TypingDNA-Dashboard.
3. Hosten Sie die HTML-Dateien beim Anbieter Ihrer Wahl, und befolgen Sie dabei die [hier](./customize-ui-with-html.md#3-configure-cors) beschriebenen CORS-Anforderungen.
4. Ersetzen Sie in der Datei `TrustFrameworkExtensions.xml` die LoadURI-Elemente für die Inhaltsdefinitionen `api.selfasserted.tdnasignup` und `api.selfasserted.tdnasignin` jeweils durch den URI Ihrer gehosteten HTML-Dateien.
5. Erstellen Sie im **Azure-Portal** auf dem Blatt „Azure AD“ unter „Identity Experience Framework“ einen B2C-Richtlinienschlüssel. Verwenden Sie die Option `Generate`, und weisen Sie diesem Schlüssel den Namen `tdnaHashedId` zu.
6. Ersetzen Sie in den Richtliniendateien die Mandanten-ID (TenantId).
7. Ersetzen Sie die Dienst-URLs (ServiceURLs) in allen technischen TypingDNA-REST-API-Profilen (REST-TDNA-VerifyUser, REST-TDNA-SaveUser, REST-TDNA-CheckUser) durch den Endpunkt für Ihre [TypingDNA-API-Interface-API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface).
8. Laden Sie die [Richtliniendateien](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/policy) in Ihren Mandanten hoch.

## <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Öffnen Sie den B2C-Mandanten, und wählen Sie „Identity Experience Framework“ aus.
2. Wählen Sie den zuvor erstellten **Benutzerflow** aus.
3. Wählen Sie **Benutzerflow ausführen** aus.

    a. **Anwendung**: Wählen Sie die registrierte App aus. (Beispiel: JWT)

    b. **Antwort-URL**: Wählen Sie die Umleitungs-URL aus.

    c. Wählen Sie **Benutzerflow ausführen** aus.
  
4. Führen Sie den Registrierungsflow aus, und erstellen Sie ein Konto.
5. Abmelden
6. Führen Sie den Anmeldungsflow aus.
7. Im resultierenden JWT-Ergebnis werden die TypingDNA-Ergebnisse angezeigt.

## <a name="live-version"></a>Liveversion

• MFA wurde in dieser Testversion deaktiviert, aber Sie können das Ergebnis sehen, ob MFA durch den Anspruch `promptMFA` nach der Authentifizierung angefordert worden wäre.

• Registrieren Sie sich [hier](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SU_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login), und melden Sie sich [hier](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SI_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login) an.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Richtlinien in Azure Active Directory B2C](./custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure Active Directory B2C](./custom-policy-get-started.md?tabs=applications)