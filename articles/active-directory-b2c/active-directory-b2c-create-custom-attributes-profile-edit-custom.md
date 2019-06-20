---
title: Hinzufügen eigener Attribute zu benutzerdefinierten Richtlinien in Azure Active Directory B2C | Microsoft-Dokumentation
description: Eine exemplarische Vorgehensweise zur Verwendung von Erweiterungseigenschaften und benutzerdefinierten Attributen sowie zu ihrer Einbindung in die Benutzeroberfläche.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ab7231c214060d17927e2509bee1687e2c9c87a3
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507575"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: Verwenden von benutzerdefinierten Attributen in einer benutzerdefinierten Richtlinie für die Profilbearbeitung

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In diesem Artikel erstellen Sie ein benutzerdefiniertes Attribut in Ihrem Azure Active Directory- (Azure AD-)B2C-Verzeichnis. Sie verwenden dieses neue Attribut als benutzerdefinierten Anspruch in der User Journey der Profilbearbeitung.

## <a name="prerequisites"></a>Voraussetzungen

Folgen Sie den Schritten im Artikel [Azure Active Directory B2C: Erste Schritte mit benutzerdefinierten Richtlinien](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-ad-b2c-by-using-custom-policies"></a>Verwenden von benutzerdefinierten Attributen zum Erfassen von Informationen zu Ihren Kunden in Azure AD B2C mit benutzerdefinierten Richtlinien
Ihr Azure AD B2C-Verzeichnis verfügt über mehrere integrierte Attribute. Beispiele: **Given Name** (Vorname), **Surname** (Nachname), **City** (Stadt), **Postal Code** (PLZ) und **userPrincipalName** (Benutzerprinzipalname). Es kann häufiger vorkommen, dass Sie eigene Attribute erstellen müssen, z.B.:
* Für eine Kundenanwendung muss ein Attribut beibehalten werden, z.B. **LoyaltyNumber**
* Ein Identitätsanbieter verfügt über eine eindeutige Benutzer-ID, die gespeichert werden muss, z.B. **uniqueUserGUID**.
* Für eine benutzerdefinierte User Journey muss der Status eines Benutzers beibehalten werden, z.B. **migrationStatus**.

Azure AD B2C erweitert die für die einzelnen Benutzerkonten gespeicherten Attribute. Außerdem können Sie diese Attribute mit der [Azure AD Graph-API](active-directory-b2c-devquickstarts-graph-dotnet.md)lesen und schreiben.

Mit Erweiterungseigenschaften wird das Schema der Benutzerobjekte im Verzeichnis erweitert. Die Begriffe *Erweiterungseigenschaft*, *benutzerdefiniertes Attribut* und *benutzerdefinierter Anspruch* beziehen sich im Kontext dieses Artikels auf die gleiche Sache. Der Name variiert je nach Kontext (Anwendung, Objekt, Richtlinie).

Erweiterungseigenschaften können nur für ein Anwendungsobjekt registriert werden, obwohl sie Daten für einen Benutzer enthalten können. Die Eigenschaft ist an die Anwendung angefügt. Das Anwendungsobjekt muss Schreibzugriff zum Registrieren einer Erweiterungseigenschaft besitzen. 100 Erweiterungseigenschaften für alle Typen und alle Anwendungen können in jedes einzelne Objekt geschrieben werden. Erweiterungseigenschaften werden dem Zielverzeichnistyp hinzugefügt und sind im Azure AD B2C-Verzeichnismandanten dann sofort zugänglich.
Wenn die Anwendung gelöscht wird, werden auch diese Erweiterungseigenschaften zusammen mit darin enthaltenen Daten für alle Benutzer entfernt. Falls eine Erweiterungseigenschaft von der Anwendung gelöscht wird, wird sie aus den Zielverzeichnisobjekten entfernt, und die Werte werden gelöscht.

Erweiterungseigenschaften sind nur im Kontext einer registrierten Anwendung im Mandanten vorhanden. Die Objekt-ID dieser Anwendung muss in das **TechnicalProfile** einbezogen werden, von dem sie verwendet wird.

>[!NOTE]
>Normalerweise enthält das Azure AD B2C-Verzeichnis eine Web-App mit dem Namen `b2c-extensions-app`. Diese Anwendung wird hauptsächlich von den integrierten B2C-Richtlinien für die benutzerdefinierten Ansprüche verwendet, die mit dem Azure-Portal erstellt werden. Die Verwendung dieser Anwendung zum Registrieren von Erweiterungen für benutzerdefinierte B2C-Richtlinien wird nur für fortgeschrittene Benutzer empfohlen.  
Anleitungen finden Sie in diesem Artikel im Abschnitt **Nächste Schritte**.

## <a name="create-a-new-application-to-store-the-extension-properties"></a>Erstellen einer neuen Anwendung zum Speichern der Erweiterungseigenschaften

1. Öffnen Sie eine Browsersitzung, und navigieren Sie zum [Azure-Portal](https://portal.azure.com). Melden Sie sich mit den Administratoranmeldeinformationen des B2C-Verzeichnisses an, das Sie konfigurieren möchten.
2. Klicken Sie auf **Azure Active Directory** im linken Navigationsmenü. Unter Umständen müssen Sie die Option **Weitere Dienste** wählen, um das Verzeichnis zu suchen.
3. Wählen Sie **App-Registrierungen** aus. Wählen Sie **Registrierung einer neuen Anwendung** aus.
4. Stellen Sie die folgenden Einträge bereit:
    * Einen Namen für die Webanwendung: **WebApp-GraphAPI-DirectoryExtensions**.
    * Den Anwendungstyp: **Web-App/API**.
    * Anmelde-URL: **https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions**.
5. Klicken Sie auf **Erstellen**.
6. Wählen Sie die neu erstellte Webanwendung aus.
7. Wählen Sie **Einstellungen** > **Erforderliche Berechtigungen** aus.
8. Klicken Sie auf die API **Microsoft Azure Active Directory**.
9. Setzen Sie in den Anwendungsberechtigungen ein Häkchen für: **Verzeichnisdaten lesen und schreiben**. Klicken Sie dann auf **Speichern**.
10. Wählen Sie **Berechtigungen erteilen** aus, und bestätigen Sie den Vorgang mit **Ja**.
11. Kopieren Sie die folgenden Bezeichner in die Zwischenablage, und speichern Sie sie:
    * **Anwendungs-ID**. Beispiel: `103ee0e6-f92d-4183-b576-8c3739027780`.
    * **Objekt-ID** Beispiel: `80d8296a-da0a-49ee-b6ab-fd232aa45201`.

## <a name="modify-your-custom-policy-to-add-the-applicationobjectid"></a>Ändern der benutzerdefinierten Richtlinie zum Hinzufügen der **ApplicationObjectId**

Wenn Sie die Schritte unter [Azure Active Directory B2C: Erste Schritte mit benutzerdefinierten Richtlinien](active-directory-b2c-get-started-custom.md) ausgeführt haben, haben Sie die [Beispieldateien](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) namens **TrustFrameworkBase.xml**, **TrustFrameworkExtensions.xml**, **SignUpOrSignin.xml**, **ProfileEdit.xml** und **PasswordReset.xml** heruntergeladen und geändert. In diesem Schritt nehmen Sie weitere Änderungen an diesen Dateien vor.

* Öffnen Sie die Datei **TrustFrameworkBase.xml**, und fügen Sie den Abschnitt `Metadata` wie im folgenden Beispiel gezeigt ein. Fügen Sie die zuvor für den Wert `ApplicationObjectId` notierte Objekt-ID und die für den Wert `ClientId` notierte Anwendungs-ID ein: 

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Azure Active Directory</DisplayName>
        <TechnicalProfile Id="AAD-Common">
          <DisplayName>Azure Active Directory</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

          <!-- Provide objectId and appId before using extension properties. -->
          <Metadata>
            <Item Key="ApplicationObjectId">insert objectId here</Item>
            <Item Key="ClientId">insert appId here</Item>
          </Metadata>
          <!-- End of changes -->

          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
          </CryptographicKeys>
          <IncludeInSso>false</IncludeInSso>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

> [!NOTE]
> Wenn das **TechnicalProfile** zum ersten Mal einen Schreibvorgang in die neu erstellte Erweiterungseigenschaft durchführt, tritt möglicherweise ein einmaliger Fehler auf. Die Erweiterungseigenschaft wird bei erstmaliger Verwendung erstellt.

## <a name="use-the-new-extension-property-or-custom-attribute-in-a-user-journey"></a>Verwenden der neuen Erweiterungseigenschaft bzw. des benutzerdefinierten Attributs in einer User Journey

1. Öffnen Sie die Datei **ProfileEdit.xml**.
2. Fügen Sie den benutzerdefinierten Anspruch `loyaltyId` hinzu. Indem der benutzerdefinierte Anspruch in das Element `<RelyingParty>` eingefügt wird, wird er in das Token für die Anwendung eingefügt.
    
    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="ProfileEdit" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
          <OutputClaim ClaimTypeReferenceId="city" />

          <!-- Provide the custom claim identifier -->
          <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
          <!-- End of changes -->
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

3. Öffnen Sie die Datei **TrustFrameworkExtensions.xml**, und fügen Sie das `<ClaimsSchema>`-Element und seine untergeordneten Elemente in das `BuildingBlocks`-Element ein:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
          <DisplayName>Loyalty Identification Tag</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Your loyalty number from your membership card</UserHelpText>
          <UserInputType>TextBox</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

4. Fügen Sie die gleiche `ClaimType`-Definition in **TrustFrameworkBase.xml** ein. Es ist nicht erforderlich, eine `ClaimType`-Definition der Basisdatei und den Erweiterungsdateien hinzuzufügen. Allerdings wird in den nächsten Schritten eine `extension_loyaltyId` den **TechnicalProfiles** in der Basisdatei hinzugefügt. Daher lehnt die Richtlinienvalidierung den Upload der Basisdatei ohne diese Angabe ab. Es kann nützlich sein, die Ausführung der User Journey mit dem Namen **ProfileEdit** in der Datei **TrustFrameworkBase.xml** nachzuverfolgen. Suchen Sie die User Journey mit dem gleichen Namen in Ihrem Editor. Beachten Sie, dass im Orchestrierungsschritt 5 **TechnicalProfileReferenceID="SelfAsserted-ProfileUpdate** aufgerufen wird. Untersuchen Sie dieses **TechnicalProfile**, um sich mit dem Ablauf vertraut zu machen.

5. Öffnen Sie die Datei **TrustFrameworkBase.xml**, und fügen Sie `loyaltyId` als Eingabe- und Ausgabeanspruch in das TechnicalProfile **SelfAsserted-ProfileUpdate** hinzu:

    ```xml
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <DisplayName>User ID signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
        <InputClaim ClaimTypeReferenceId="userPrincipalName" />
        <InputClaim ClaimTypeReferenceId="givenName" />
        <InputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->

      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
    ```

6. In der Datei **Trustframeworkbase.xml** fügen Sie den Anspruch `loyaltyId` zu **TechnicalProfile AAD-UserWriteProfileUsingObjectId** hinzu. Durch diese Hinzufügung tritt der Wert des Anspruchs in der Erweiterungseigenschaft für den aktuellen Benutzer im Verzeichnis weiterhin auf:

    ```xml
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <Metadata>
        <Item Key="Operation">Write</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="givenName" />
        <PersistedClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </PersistedClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

7. Fügen Sie in der Datei **TrustFrameworkBase.xml** den Anspruch `loyaltyId` dem TechnicalProfile **AAD-UserReadUsingObjectId** hinzu, damit der Wert des Erweiterungsattributs bei jeder Anmeldung eines Benutzers gelesen wird. Bisher wurden die **TechnicalProfiles** nur im Ablauf der lokalen Konten geändert. Wenn Sie das neue Attribut im Ablauf eines Social Media- oder Verbundkontos wünschen, muss eine andere Gruppe von **TechnicalProfiles** geändert werden. Weitere Informationen finden Sie im Abschnitt **Nächste Schritte**.

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

## <a name="test-the-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Öffnen Sie das Blatt „Azure AD B2C“, und navigieren Sie zu **Identity Experience Framework** > **Benutzerdefinierte Richtlinien**.
1. Klicken Sie auf die benutzerdefinierte Richtlinie, die Sie hochgeladen haben. Wählen Sie **Jetzt ausführen** aus.
1. Registrieren mithilfe einer E-Mail-Adresse.

Das ID-Token, das an Ihre Anwendung zurückgesendet wird, enthält die neue Erweiterungseigenschaft als benutzerdefinierten Anspruch mit Voranstellung von **extension_loyaltyId**. Siehe folgendes Beispiel:

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Nächste Schritte

1. Fügen Sie den neuen Anspruch dem Ablauf für die Anmeldung bei Social Media-Konten hinzu, indem Sie die aufgeführten **TechnicalProfiles** ändern. Konten für soziale Netzwerke und Verbundkonten diese beiden **TechnicalProfiles** für die Anmeldung. Sie schreiben und lesen Benutzerdaten mithilfe von **AlternativeSecurityId** als Locator des Benutzerobjekts.

   ```xml
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
   ```

2. Verwenden Sie die gleichen Erweiterungsattribute zwischen integrierten und benutzerdefinierten Richtlinien. Beim Hinzufügen von Erweiterungs- oder benutzerdefinierten Attributen über die Portaloberfläche werden diese Attribute per **b2c-extensions-app** registriert, die unter jedem B2C-Mandanten vorhanden ist. Gehen Sie wie folgt vor, um diese Erweiterungsattribute in Ihrer benutzerdefinierten Richtlinie zu verwenden:

   a. Navigieren Sie in Ihrem B2C-Mandanten in „portal.azure.com“ zu **Azure Active Directory**, und wählen Sie die Option **App-Registrierungen**.  
   b. Suchen Sie **b2c-extensions-app**, und wählen Sie sie aus.  
   c. Geben Sie unter **Essentials** die **Anwendungs-ID** und die **Objekt-ID** ein.  
   d. Fügen Sie diese IDs wie folgt in die Metadaten Ihres technischen **AAD-Common**-Profils ein:  

   ```xml
      <ClaimsProviders>
        <ClaimsProvider>
          <DisplayName>Azure Active Directory</DisplayName>
          <TechnicalProfile Id="AAD-Common">
            <DisplayName>Azure Active Directory</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <!-- Provide objectId and appId before using extension properties. -->
            <Metadata>
              <Item Key="ApplicationObjectId">insert objectId here</Item> <!-- This is the "Object ID" from the "b2c-extensions-app"-->
              <Item Key="ClientId">insert appId here</Item> <!--This is the "Application ID" from the "b2c-extensions-app"-->
            </Metadata>
   ```

3. Achten Sie auf Konformität mit der Portalumgebung. Erstellen Sie diese Attribute mithilfe der Portalbenutzeroberfläche, bevor Sie sie in Ihren benutzerdefinierten Richtlinien verwenden. Wenn Sie im Portal das Attribut **ActivationStatus** erstellen, müssen Sie wie folgt darauf verweisen:

   ```
   extension_ActivationStatus in the custom policy.
   extension_<app-guid>_ActivationStatus via Graph API.
   ```

## <a name="reference"></a>Verweis

Weitere Informationen zur umfassenden Behandlung von Erweiterungseigenschaften finden Sie im Artikel [Verzeichnisschemaerweiterungen | Graph-API-Konzepte](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).

> [!NOTE]
> * Ein **TechnicalProfile** ist ein Elementtyp oder eine Funktion, die den Namen eines Endpunkts, Metadaten und das Protokoll definiert. Das **TechnicalProfile** beschreibt die Austausch von Ansprüchen, die das Identity Experience Framework ausführt. Wenn diese Funktion in einem Orchestrierungsschritt oder über ein anderes **TechnicalProfile** aufgerufen wird, werden die **InputClaims** und **OutputClaims** vom Aufrufer als Parameter bereitgestellt.  
> * Erweiterungsattribute in der Graph-API werden mithilfe der Konvention `extension_ApplicationObjectID_attributename` benannt.  
> * Benutzerdefinierte Richtlinien verweisen auf Erweiterungsattribute als **extension_attributename**. Dieser Verweis lässt die **ApplicationObjectId** im XML-Format aus.
