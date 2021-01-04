---
title: Benutzerdefinierte E-Mail-Überprüfung mit Mailjet
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie Mailjet integrieren, um die Überprüfungs-E-Mail anzupassen, die an Ihre Kunden gesendet wird, wenn sie sich für die Verwendung Ihrer Azure AD B2C-fähigen Anwendungen registrieren.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b74de2bdf1f6239f1006c820579a336946939421
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949580"
---
# <a name="custom-email-verification-with-mailjet"></a>Benutzerdefinierte E-Mail-Überprüfung mit Mailjet

Verwenden Sie benutzerdefinierte E-Mails in Azure Active Directory B2C (Azure AD B2C), um angepasste E-Mails an Benutzer zu senden, die sich für die Verwendung Ihrer Anwendungen registrieren. Mithilfe von [Anzeigesteuerelementen](display-controls.md) (derzeit in der Vorschauphase) und dem E-Mail-Drittanbieter Mailjet können Sie eigene E-Mail-Vorlagen sowie *Absenderadressen* und Betreffzeilentexte verwenden. Darüber hinaus werden die Lokalisierung und benutzerdefinierte Einstellungen für Einmalkennwörter (One-Time Password, OTP) unterstützt.

Für die benutzerdefinierte E-Mail-Überprüfung ist die Verwendung eines E-Mail-Drittanbieters wie [Mailjet](https://Mailjet.com), [SendGrid](./custom-email-sendgrid.md) oder [SparkPost](https://sparkpost.com), einer benutzerdefinierten REST-API oder eines HTTP-basierten E-Mail-Anbieters (einschließlich Ihres eigenen Anbieters) erforderlich. In diesem Artikel wird das Einrichten einer Lösung beschrieben, bei der Mailjet verwendet wird.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-mailjet-account"></a>Erstellen eines Mailjet-Kontos

Wenn Sie noch nicht über ein Mailjet-Konto verfügen, müssen Sie zuerst ein solches Konto einrichten (Azure-Kunden können 6.000 E-Mails mit einer Beschränkung von 200 E-Mails pro Tag freischalten). 

1. Folgen Sie den Anweisungen zur Einrichtung unter [Erstellen eines Mailjet-Kontos](https://www.mailjet.com/guides/azure-mailjet-developer-resource-user-guide/enabling-mailjet/).
1. [Registrieren und validieren](https://www.mailjet.com/guides/azure-mailjet-developer-resource-user-guide/enabling-mailjet/#how-to-configure-mailjet-for-use) Sie Ihre Absender-E-Mail-Adresse oder Domäne, um E-Mails senden zu können.
2. Navigieren Sie zur [Seite für die API-Schlüsselverwaltung](https://app.mailjet.com/account/api_keys). Notieren Sie sich den **API-Schlüssel** und den **geheimen Schlüssel** zur Verwendung in einem späteren Schritt. Beide Schlüssel werden bei der Erstellung Ihres Kontos automatisch generiert.  

## <a name="create-azure-ad-b2c-policy-key"></a>Erstellen des Azure AD B2C-Richtlinienschlüssels

Speichern Sie als Nächstes den Mailjet-API-Schlüssel in einem Azure AD B2C-Richtlinienschlüssel, auf den Ihre Richtlinien verweisen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie im Hauptmenü den Filter **Verzeichnis und Abonnement** und dann Ihr Azure AD B2C-Verzeichnis aus.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite **Übersicht** die Option **Identity Experience Framework** aus.
1. Wählen Sie **Richtlinienschlüssel** aus, und wählen Sie dann **Hinzufügen** aus.
1. Wählen Sie unter **Optionen** die Option **Manuell** aus.
1. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `MailjetApiKey`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
1. Geben Sie im Feld **Geheimnis** den Mailjet-**API-Schlüssel** ein, den Sie zuvor notiert haben.
1. Wählen Sie unter **Schlüsselverwendung** **Signatur** aus.
1. Klicken Sie auf **Erstellen**.
1. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
1. Wählen Sie unter **Optionen** die Option **Manuell** aus.
1. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `MailjetSecretKey`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
1. Geben Sie im Feld **Geheimnis** den **geheimen Schlüssel** von Mailjet ein, den Sie zuvor notiert haben.
1. Wählen Sie unter **Schlüsselverwendung** **Signatur** aus.
1. Klicken Sie auf **Erstellen**.

## <a name="create-a-mailjet-template"></a>Erstellen einer Mailjet-Vorlage

Wenn Sie ein Mailjet-Konto erstellt und den Mailjet-API-Schlüssel in einem Azure AD B2C-Richtlinienschlüssel gespeichert haben, erstellen Sie eine [dynamische Mailjet-Transaktionsvorlage](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/).

1. Öffnen Sie auf der Mailjet-Website die Seite [Transaktionsvorlagen](https://app.mailjet.com/templates/transactional), und wählen Sie **Neue Vorlage erstellen** aus.
1. Wählen Sie **In HTML codieren** aus, und wählen Sie dann **Code von Grund auf neu erstellen** aus.
1. Geben Sie einen eindeutigen Vorlagennamen wie `Verification email` ein, und wählen Sie dann **Erstellen** aus.
1. Fügen Sie im HTML-Editor die folgende HTML-Vorlage ein, oder verwenden Sie eine eigene Vorlage. Die Parameter `{{var:otp:""}}` und `{{var:email:""}}` werden dynamisch durch das Einmalkennwort und die E-Mail-Adresse des Benutzers ersetzt.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{var:email:""}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{var:otp:""}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. Erweitern Sie im linken oberen Bereich die Option **Betreff bearbeiten**.
    1. Geben Sie für **Betreff** einen Standardwert ein. Mailjet verwendet diesen Wert, wenn die API keinen Betreffzeilenparameter enthält.
    1. Geben Sie im Feld **Name** den Namen Ihres Unternehmens ein.
    1. Wählen Sie für **Adresse** Ihre E-Mail-Adresse aus.
    1. Wählen Sie **Speichern** aus.
1. Wählen Sie rechts oben die Option **Speichern und veröffentlichen** aus, und wählen Sie dann **Ja, Änderungen veröffentlichen** aus.
1. Notieren Sie sich die **Vorlagen-ID** der von Ihnen erstellten Vorlage zur Verwendung in einem späteren Schritt. Diese ID geben Sie an, wenn Sie [die Anspruchstransformation hinzufügen](#add-the-claims-transformation).


## <a name="add-azure-ad-b2c-claim-types"></a>Hinzufügen von Azure AD B2C-Anspruchstypen

Fügen Sie in Ihrer Richtlinie im `<ClaimsSchema>`-Element in `<BuildingBlocks>` die folgenden Anspruchstypen hinzu.

Diese Anspruchstypen sind erforderlich, um die E-Mail-Adresse mit einem OTP-Code (One-Time Password, Einmalkennwort) zu generieren und zu überprüfen.

```XML
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="emailRequestBody">
  <DisplayName>Mailjet request body</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="VerificationCode">
  <DisplayName>Secondary Verification Code</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter your email verification code</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

## <a name="add-the-claims-transformation"></a>Hinzufügen der Anspruchstransformation

Als Nächstes benötigen Sie eine Anspruchstransformation zum Ausgeben eines JSON-Zeichenfolgenanspruchs, der als Text der an Mailjet gesendeten Anforderung verwendet wird.

Die Struktur des JSON-Objekts wird durch die IDs in der Punktnotation der InputParameters und der TransformationClaimTypes der InputClaims definiert. Zahlen in Punktnotation implizieren Arrays. Die Werte stammen aus den Werten der InputClaims und den Value-Eigenschaften der InputParameters. Weitere Informationen zu JSON-Anspruchstransformationen finden Sie unter [Transformationen von JSON-Ansprüchen](json-transformations.md).

Fügen Sie im `<ClaimsTransformations>`-Element in `<BuildingBlocks>` die folgenden Anspruchstransformation hinzu. Nehmen Sie die folgenden Aktualisierungen an der Anspruchstranformations-XML vor:

* Aktualisieren Sie den InputParameter-Wert `Messages.0.TemplateID` mit der ID der Mailjet-Transaktionsvorlage, die Sie zuvor unter [Erstellen einer Mailjet-Vorlage](#create-a-mailjet-template) erstellt haben.
* Aktualisieren Sie den Adresswert `Messages.0.From.Email`. Verwenden Sie eine gültige E-Mail-Adresse, um zu verhindern, dass die Überprüfungs-E-Mail als Spam markiert wird.
* Aktualisieren Sie den Wert des Betreffzeilen-Eingabeparameters `Messages.0.Subject` mit einer für Ihre Organisation geeigneten Betreffzeile.

```XML
<ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.To.0.Email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="Messages.0.Variables.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.Variables.email" />
  </InputClaims>
  <InputParameters>
    <!-- Update the template_id value with the ID of your Mailjet template. -->
    <InputParameter Id="Messages.0.TemplateID" DataType="int" Value="1234567"/>
    <InputParameter Id="Messages.0.TemplateLanguage" DataType="boolean" Value="true"/>

    <!-- Update with an email appropriate for your organization. -->
    <InputParameter Id="Messages.0.From.Email" DataType="string" Value="my_email@mydomain.com"/>

    <!-- Update with a subject line appropriate for your organization. -->
    <InputParameter Id="Messages.0.Subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>Hinzufügen der DataUri-Inhaltsdefinition

Fügen Sie unterhalb der Anspruchstransformationen in `<BuildingBlocks>` die folgende [ContentDefinition](contentdefinitions.md) hinzu, um auf den Daten-URI der Version 2.1.0 zu verweisen:

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Erstellen eines DisplayControl-Elements

Ein Anzeigesteuerelement zur Überprüfung wird verwendet, um die E-Mail-Adresse mit einem an den Benutzer gesendeten Überprüfungscode zu überprüfen.

Das folgende Beispielanzeigesteuerelement ist für Folgendes konfiguriert:

1. Erfassen des `email`-Adressanspruchstyps vom Benutzer
1. Warten, dass der Benutzer den `verificationCode`-Anspruchstyp mit dem an den Benutzer gesendeten Code angibt
1. Zurückgeben von `email` an das selbstbestätigte technische Profil, das einen Verweis auf dieses Anzeigesteuerelement enthält
1. Verwenden der Aktion `SendCode` zum Generieren eines Einmalkennwortcodes und Senden einer E-Mail mit dem Einmalkennwortcode an den Benutzer

   ![Senden des Überprüfungscodes in einer E-Mail](media/custom-email-mailjet/display-control-verification-email-action-01.png)

Fügen Sie der Richtlinie unter „content definitions“ in `<BuildingBlocks>` das folgende [DisplayControl](display-controls.md)-Element vom Typ [VerificationControl](display-control-verification.md) hinzu.

```XML
<DisplayControls>
  <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
    <DisplayClaims>
      <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
      <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
    </DisplayClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="email" />
    </OutputClaims>
    <Actions>
      <Action Id="SendCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendOtp" />
        </ValidationClaimsExchange>
      </Action>
      <Action Id="VerifyCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
        </ValidationClaimsExchange>
      </Action>
    </Actions>
  </DisplayControl>
</DisplayControls>
```

## <a name="add-otp-technical-profiles"></a>Hinzufügen technischer Profile für Einmalkennwörter

Mit dem technischen Profil `GenerateOtp` wird ein Code für die E-Mail-Adresse generiert. Mit dem technischen Profil `VerifyOtp` wird der der E-Mail-Adresse zugeordnete Code überprüft. Sie können die Konfiguration des Formats und den Ablauf des Einmalkennworts ändern. Weitere Informationen zu technischen Profilen für Einmalkennwörter finden Sie unter [Definieren eines technischen Einmalkennwortprofils](one-time-password-technical-profile.md).

Fügen Sie dem `<ClaimsProviders>`-Element die folgenden technischen Profile hinzu.

```XML
<ClaimsProvider>
  <DisplayName>One time password technical profiles</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="GenerateOtp">
      <DisplayName>Generate one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">1200</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="ReuseSameCode">true</Item>
        <Item Key="MaxNumAttempts">5</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
      </OutputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="VerifyOtp">
      <DisplayName>Verify one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">VerifyCode</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
      </InputClaims>
    </TechnicalProfile>
   </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="add-a-rest-api-technical-profile"></a>Hinzufügen eines technischen REST-API-Profils

Mit diesem technischen REST-API-Profil wird der E-Mail-Inhalt (im Mailjet-Format) generiert. Weitere Informationen zu technischen RESTful-Profilen finden Sie unter [Definieren eines technischen RESTful-Profils](restful-technical-profile.md).

Fügen Sie wie bei den technischen Profilen für Einmalkennwörter dem `<ClaimsProviders>`-Element die folgenden technischen Profile hinzu.

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="sendOtp">
      <DisplayName>Use email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.mailjet.com/v3.1/send</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">emailRequestBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_MailjetApiKey" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_MailjetSecretKey" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateEmailRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="emailRequestBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Erstellen eines Verweises auf das DisplayControl-Element

Fügen Sie im letzten Schritt einen Verweis auf das erstellte DisplayControl-Element hinzu. Ersetzen Sie Ihre selbstbestätigten technischen Profile `LocalAccountSignUpWithLogonEmail` und `LocalAccountDiscoveryUsingEmailAddress` durch Folgendes: Wenn Sie eine ältere Version der Azure AD B2C-Richtlinie verwendet haben. In diesen technischen Profilen wird `DisplayClaims` mit einem Verweis auf „DisplayControl“ verwendet.

Weitere Informationen finden Sie unter [Selbstbestätigtes technisches Profil](restful-technical-profile.md) und [DisplayControl](display-controls.md).

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="LocalAccountDiscoveryUsingEmailAddress">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
      </DisplayClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>[Optional] Lokalisieren Ihrer E-Mail

Zum Lokalisieren der E-Mail müssen Sie lokalisierte Zeichenfolgen an Mailjet oder Ihren E-Mail-Anbieter senden. Sie können beispielsweise den E-Mail-Betreff, den Nachrichtentext, Ihre Codemeldung oder die Signatur der E-Mail lokalisieren. Hierfür können Sie die Anspruchstransformation [GetLocalizedStringsTransformation](string-transformations.md) nutzen, um lokalisierte Zeichenfolgen in Anspruchstypen zu kopieren. Die Anspruchstransformation `GenerateEmailRequestBody`, mit der die JSON-Nutzlast generiert wird, verwendet Eingabeansprüche, die die lokalisierten Zeichenfolgen enthalten.

1. Definieren Sie in Ihrer Richtlinie die folgenden Zeichenfolgenansprüche: „subject“, „message“, „codeIntro“ und „signature“.
1. Definieren Sie die Anspruchstransformation [GetLocalizedStringsTransformation](string-transformations.md), um die lokalisierten Zeichenfolgenwerte in den Ansprüchen aus Schritt 1 zu ersetzen.
1. Ändern Sie die Anspruchstransformation `GenerateEmailRequestBody`, um Eingabeansprüche mit dem folgenden XML-Codeausschnitt zu verwenden.
1. Aktualisieren Sie Ihre Mailjet-Vorlage so, dass anstelle aller Zeichenfolgen, die von Azure AD B2C lokalisiert werden, dynamische Parameter verwendet werden.

    ```XML
    <ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
        <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
        <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
        <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.To.0.Email" />
        <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="Messages.0.Subject" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="Messages.0.Variables.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.Variables.email" />
        <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="Messages.0.Variables.otpmessage" />
        <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="Messages.0.Variables.otpcodeIntro" />
        <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="Messages.0.Variables.otpsignature" />
      </InputClaims>
      <InputParameters>
        <!-- Update the template_id value with the ID of your Mailjet template. -->
        <InputParameter Id="Messages.0.TemplateID" DataType="int" Value="1234567"/>
        <InputParameter Id="Messages.0.TemplateLanguage" DataType="boolean" Value="true"/>

        <!-- Update with an email appropriate for your organization. -->
        <InputParameter Id="Messages.0.From.Email" DataType="string" Value="my_email@mydomain.com"/>
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
      </OutputClaims>
    </ClaimsTransformation>
    ```

1. Fügen Sie das folgende [Localization](localization.md)-Element hinzu.

    ```xml
    <Localization Enabled="true">
      <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
        <SupportedLanguage>en</SupportedLanguage>
        <SupportedLanguage>es</SupportedLanguage>
      </SupportedLanguages>
      <LocalizedResources Id="api.custom-email.en">
        <LocalizedStrings>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for validating the account</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
        </LocalizedStrings>
        </LocalizedStrings>
      </LocalizedResources>
      <LocalizedResources Id="api.custom-email.es">
        <LocalizedStrings>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sinceramente</LocalizedString>
        </LocalizedStrings>
      </LocalizedResources>
    </Localization>
    ```

1. Fügen Sie Verweise auf die „LocalizedResources“-Elemente hinzu, indem Sie das [ContentDefinitions](contentdefinitions.md)-Element aktualisieren.

    ```xml
    <ContentDefinitions>
      <ContentDefinition Id="api.localaccountsignup">
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri>
        <LocalizedResourcesReferences MergeBehavior="Prepend">
          <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
          <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
        </LocalizedResourcesReferences>
      </ContentDefinition>
      <ContentDefinition Id="api.localaccountpasswordreset">
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri>
        <LocalizedResourcesReferences MergeBehavior="Prepend">
          <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
          <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
        </LocalizedResourcesReferences>
      </ContentDefinition>
    </ContentDefinitions>
    ```

1. Fügen Sie schließlich den technischen Profilen `LocalAccountSignUpWithLogonEmail` und `LocalAccountDiscoveryUsingEmailAddress` die folgende Eingabeanspruchstransformation hinzu.

    ```xml
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```
    
## <a name="optional-localize-the-ui"></a>[Optional] Lokalisieren der Benutzeroberfläche

Mithilfe des Localization-Elements können Sie mehrere Gebietsschemas oder Sprachen in der Richtlinie für die User Journeys unterstützen. Die Lokalisierungsunterstützung in Richtlinien ermöglicht es Ihnen, sprachspezifische Zeichenfolgen sowohl für [Anzeigesteuerelemente zur Überprüfung von Benutzeroberflächenelementen](localization-string-ids.md#verification-display-control-user-interface-elements) als auch für [Fehlermeldungen für Einmalkennwort](localization-string-ids.md#one-time-password-error-messages) bereitzustellen. Fügen Sie Ihren „LocalizedResources“ folgendes „LocalizedString“ hinzu. 

```XML
<LocalizedResources Id="api.custom-email.en">
  <LocalizedStrings>
    ...
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
    <!-- Claims-->
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="DisplayName">Verification Code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="UserHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="AdminHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Eamil</LocalizedString>
    <!-- Email validation error messages-->
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">The verification has failed, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

Nachdem Sie die lokalisierten Zeichenfolgen hinzugefügt haben, entfernen Sie die Metadaten für OTP-Validierungsfehlermeldungen aus den technischen Profilen „LocalAccountSignUpWithLogonEmail“ und „LocalAccountDiscoveryUsingEmailAddress“.

## <a name="next-steps"></a>Nächste Schritte

Ein Beispiel für eine Richtlinie für die benutzerdefinierte E-Mail-Überprüfung finden Sie auf GitHub:

- [Custom email verification - DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol) (Benutzerdefinierte E-Mail-Überprüfung – Display Controls)
- Informationen zur Verwendung einer benutzerdefinierten REST-API oder eines HTTP-basierten SMTP-E-Mail-Anbieters finden Sie unter [Definieren eines technischen RESTful-Profils in einer benutzerdefinierten Azure AD B2C-Richtlinie](restful-technical-profile.md).
