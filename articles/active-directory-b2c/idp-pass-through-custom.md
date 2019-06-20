---
title: Übergeben eines Zugriffstokens über eine benutzerdefinierte Richtlinie an Ihre Anwendung in Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein Zugriffstoken für OAuth 2.0-Identitätsanbieter als Anspruch über eine benutzerdefinierte Richtlinie in Azure Active Directory B2C übergeben können.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d9420398c012b8da18e3b035cb845db6ce8c942d
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511067"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Übergeben eines Zugriffstokens über eine benutzerdefinierte Richtlinie an Ihre Anwendung in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Eine [benutzerdefinierte Richtlinie](active-directory-b2c-get-started-custom.md) in Azure Active Directory B2C (Azure AD) ermöglicht es Benutzern Ihrer Anwendung, sich mit einem Identitätsanbieter zu registrieren oder anzumelden. Bei diesem Vorgang empfängt Azure AD B2C zunächst ein [Zugriffstoken](active-directory-b2c-reference-tokens.md) vom Identitätsanbieter. Azure AD B2C verwendet dieses Token, um Informationen zum Benutzer abzurufen. Sie fügen einen Anspruchstyp und einen Ausgabeanspruch zu Ihrer benutzerdefinierten Richtlinie hinzu, um das Token an die Anwendungen zu übergeben, die Sie in Azure AD B2C registriert haben. 

Azure AD B2C unterstützt die Übergabe des Zugriffstokens von [OAuth 2.0](active-directory-b2c-reference-oauth-code.md)- und [OpenID Connect](active-directory-b2c-reference-oidc.md)-Identitätsanbietern. Für alle weiteren Identitätsanbieter wird ein leerer Anspruch zurückgegeben.

## <a name="prerequisites"></a>Voraussetzungen

- Ihre benutzerdefinierte Richtlinie ist mit einem OAuth 2.0- oder OpenID Connect-Identitätsanbieter konfiguriert.

## <a name="add-the-claim-elements"></a>Hinzufügen der Anspruchselemente 

1. Öffnen Sie Ihre Datei *TrustframeworkExtensions.xml*, und fügen Sie das folgende **ClaimType**-Element mit dem Bezeichner `identityProviderAccessToken` zum **ClaimsSchema**-Element hinzu:

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Fügen Sie für jeden OAuth 2.0-Anbieter, für den Sie ein Zugriffstoken benötigen, das **OutputClaim**-Element zum **TechnicalProfile**-Element hinzu. Das folgende Beispiel zeigt, wie das Element zum technischen Profil für Facebook hinzugefügt wurde:

    ```XML
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. Speichern Sie die Datei *TrustframeworkExtensions.xml*.
4. Öffnen Sie Ihre Richtliniendatei der vertrauenden Seite, z.B. *SignUpOrSignIn.xml*, und fügen Sie das **OutputClaim**-Element zum **TechnicalProfile**-Element hinzu:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. Speichern Sie die Richtliniendatei.

## <a name="test-your-policy"></a>Testen Ihrer Richtlinie

Wenn Sie Ihre Anwendungen in Azure AD B2C testen, kann es nützlich sein, das Azure AD B2C-Token an `https://jwt.ms` zurückzugeben, um die darin enthaltenen Ansprüche zu überprüfen.

### <a name="upload-the-files"></a>Hochladen der Dateien

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
4. Wählen Sie **Framework für die Identitätsfunktion** aus.
5. Klicken Sie auf der Seite „Benutzerdefinierte Richtlinien“ auf **Richtlinie hochladen**.
6. Aktivieren Sie **Richtlinie überschreiben, sofern vorhanden**, suchen Sie nach der Datei *TrustframeworkExtensions.xml*, und wählen Sie die Datei aus.
7. Klicken Sie auf **Hochladen**.
8. Wiederholen Sie die Schritte 5 bis 7 für die Datei der vertrauenden Seite, z.B. *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Ausführen der Richtlinie

1. Öffnen Sie die Richtlinie, die Sie geändert haben. Beispiel: *B2C_1A_signup_signin*.
2. Wählen Sie für **Anwendung** Ihre Anwendung aus, die Sie zuvor registriert haben. Um das Token im nachstehenden Beispiel anzuzeigen, muss als **Antwort-URL** der Wert `https://jwt.ms` angegeben werden.
3. Klicken Sie auf **Jetzt ausführen**.

    Sie sollten eine Ausgabe ähnlich wie im folgenden Beispiel sehen:

    ![Decodiertes Token](./media/idp-pass-through-custom/idp-pass-through-custom-token.png)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Token in der [Referenz zu Azure Active Directory-Token](active-directory-b2c-reference-tokens.md).





