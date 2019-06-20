---
title: Azure-SAML-Protokoll für einmaliges Anmelden | Microsoft Docs
description: In diesem Artikel wird das SAML-Protokoll für einmaliges Anmelden in Azure Active Directory beschrieben.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ad8437f5-b887-41ff-bd77-779ddafc33fb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 593f07b27fec16c3df90a073479effb130bc5721
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545276"
---
# <a name="single-sign-on-saml-protocol"></a>SAML-Protokoll für einmaliges Anmelden

In diesem Artikel werden die SAML 2.0-Authentifizierungsanforderungen und -antworten erläutert, die Azure Active Directory (Azure AD) für das einmalige Anmelden unterstützt.

Das folgende Protokolldiagramm beschreibt den Ablauf für das einmalige Anmelden. Der Clouddienst (der Dienstanbieter) verwendet eine HTTP-Umleitungsbindung, um eine Authentifizierungsanforderung ( `AuthnRequest` ) an Azure AD (den Identitätsanbieter) zu übergeben. Azure AD verwendet daraufhin eine HTTP Post-Bindung, um ein `Response` -Element an den Clouddienst zu senden.

![Workflow für einmaliges Anmelden](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest

Clouddienste senden ein `AuthnRequest` -Element an Azure AD, um eine Benutzerauthentifizierung anzufordern. Eine `AuthnRequest` für SAML 2.0 könnte wie im folgenden Beispiel aussehen:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```

| Parameter |  | BESCHREIBUNG |
| --- | --- | --- |
| ID | Erforderlich | Azure AD verwendet dieses Attribut, um das `InResponseTo` -Attribut der zurückgegebenen Antwort aufzufüllen. Die ID darf nicht mit einer Zahl beginnen, weshalb dem GUID-String häufig eine Zeichenfolge wie etwa „id“ vorangestellt wird. `id6c1c178c166d486687be4aaf5e482730` ist beispielsweise eine gültige ID. |
| Version | Erforderlich | Dieser Parameter sollte auf **2.0** festgelegt werden. |
| IssueInstant | Erforderlich | Eine DateTime-Zeichenfolge mit einem UTC-Wert und im [Roundtrip-Format („o“)](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD erwartet einen DateTime-Wert dieses Typs, dieser Wert wird jedoch weder bewertet noch verwendet. |
| AssertionConsumerServiceUrl | Optional | Dieser Parameter muss (falls angegeben) dem `RedirectUri` des Clouddiensts in Azure AD entsprechen. |
| ForceAuthn | Optional | Dies ist ein Boolescher Wert. Bei „true“ wird der Benutzer gezwungen, sich erneut zu authentifizieren, selbst wenn für ihn bereits eine gültige Sitzung mit Azure AD besteht. |
| IsPassive | Optional | Dies ist ein Boolescher Wert, der festlegt, ob Azure AD den Benutzer im Hintergrund ohne Eingreifen des Benutzers, jedoch mithilfe des Sitzungscookies authentifiziert, sofern ein solches vorhanden ist. Bei „true“ versucht Azure AD den Benutzer mithilfe des Sitzungscookies zu authentifizieren. |

Alle anderen `AuthnRequest`-Attribute (wie „Consent“, „Destination“, „AssertionConsumerServiceIndex“, „AttributeConsumerServiceIndex“ und „ProviderName“) werden **ignoriert**.

Azure AD ignoriert auch das `Conditions`-Element in `AuthnRequest`.

### <a name="issuer"></a>Issuer (Aussteller)

Das `Issuer`-Element in einem `AuthnRequest`-Element muss mit einem der **ServicePrincipalNames**-Werte im Clouddienst von Azure AD exakt übereinstimmen. Normalerweise ist es auf den **App-ID-URI** festgelegt, der bei der Anwendungsregistrierung angegeben wird.

Im Anschluss finden Sie einen SAML-Ausschnitt, der das `Issuer`-Element enthält. Dies wird im folgenden Beispiel veranschaulicht:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Dieses Element erfordert in der Antwort ein bestimmtes Namens-ID-Format und ist bei an Azure AD gesendeten `AuthnRequest` -Elementen optional.

Ein `NameIdPolicy`-Element sieht wie im folgenden Beispiel aus:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Wenn `NameIDPolicy` angegeben ist, können Sie sein optionales `Format`-Attribut einbeziehen. Das `Format`-Attribut kann nur einen der folgenden Werte besitzen. Jeder andere Wert führt zu einem Fehler.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory stellt den NameID-Anspruch als paarweisen Bezeichner aus.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory stellt den NameID-Anspruch im E-Mail-Adressformat aus.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Azure Active Directory kann das Anspruchsformat selbst wählen. Azure Active Directory stellt die NameID als paarweisen Bezeichner aus.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Azure Active Directory stellt den NameID-Anspruch in Form eines zufällig generierten Werts aus, der für den aktuellen SSO-Vorgang eindeutig ist. Dieser Wert ist temporär und kann nicht zur Identifizierung des sich authentifizierenden Benutzers verwendet werden.

Das `AllowCreate` -Attribut wird von Azure AD ignoriert.

### <a name="requestauthncontext"></a>RequestAuthnContext
Das `RequestedAuthnContext` -Element gibt die gewünschten Authentifizierungsmethoden an. In `AuthnRequest` -Elementen, die an Azure AD gesendet werden, ist es optional. Azure AD unterstützt `AuthnContextClassRef`-Werte wie `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### <a name="scoping"></a>Bereichsdefinition
Das `Scoping`-Element enthält eine Liste mit Identitätsanbietern und ist bei `AuthnRequest`-Elementen, die an Azure AD gesendet werden, optional.

Wenn Sie sich für die Angabe entscheiden, schließen Sie nicht das `ProxyCount`-Attribut, `IDPListOption` oder das `RequesterID`-Element ein, da diese nicht unterstützt werden.

### <a name="signature"></a>Signatur
Schließen Sie in `AuthnRequest`-Elementen kein `Signature`-Element ein, da Azure AD nicht signierte Authentifizierungsanforderungen unterstützt.

### <a name="subject"></a>Subject
Azure AD ignoriert das `Subject`-Element von `AuthnRequest`-Elementen.

## <a name="response"></a>response
Wenn eine angeforderte Anmeldung erfolgreich abgeschlossen wird, sendet Azure AD eine Antwort an den Clouddienst. Eine Antwort auf einen erfolgreichen Anmeldeversuch sieht wie im folgenden Beispiel aus:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>response

Das `Response` -Element enthält das Ergebnis der Autorisierungsanforderung. Azure AD legt die Werte `ID`, `Version` und `IssueInstant` im `Response`-Element fest. Außerdem legt es die folgenden Attribute fest:

* `Destination`: Wird nach erfolgreichem Abschluss der Anmeldung auf den `RedirectUri` des Dienstanbieters (Clouddienst) festgelegt.
* `InResponseTo`: Wird auf das `ID`-Attribut des `AuthnRequest`-Elements festgelegt, das die Antwort initiiert hat.

### <a name="issuer"></a>Issuer (Aussteller)

Azure AD legt das `Issuer`-Element auf `https://login.microsoftonline.com/<TenantIDGUID>/` fest, wobei \<TenantIDGUID> die Mandanten-ID des Azure AD-Mandanten ist.

Eine Antwort mit einem Issuer-Element sieht beispielsweise wie im folgenden Beispiel aus:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Status

Das `Status` -Element gibt an, ob die Anmeldung erfolgreich war. Es enthält das `StatusCode`-Element, das einen Code oder mehrere geschachtelte Codes enthält, die den Status der Anforderung darstellen. Außerdem enthält es das `StatusMessage` -Element mit spezifischen, während der Anmeldung generierten Fehlermeldungen.

<!-- TODO: Add an authentication protocol error reference -->

Im folgenden Beispiel finden Sie eine SAML-Antwort auf einen fehlerhaften Anmeldeversuch.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>Assertion

Zusätzlich zu `ID`, `IssueInstant` und `Version` legt Azure AD im `Assertion`-Element der Antwort die folgenden Elemente fest.

#### <a name="issuer"></a>Issuer (Aussteller)

Dieses Element ist auf `https://sts.windows.net/<TenantIDGUID>/` festgelegt, wobei \<TenantIDGUID> die Mandanten-ID des Azure AD-Mandanten ist.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Signatur

Azure AD signiert die Assertion als Antwort auf eine erfolgreiche Anmeldung. Das `Signature` -Element enthält eine digitale Signatur, die vom Clouddienst zum Authentifizieren der Quelle und zum Überprüfen der Integrität der Assertion verwendet werden kann.

Azure AD verwendet den im `IDPSSODescriptor` -Element des Metadatendokuments festgelegten Signaturschlüssel, um diese digitale Signatur zu generieren.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Subject

Legt den Prinzipal fest, der Betreff der Anweisungen in der Assertion ist. Er enthält ein `NameID`-Element, das den authentifizierten Benutzer darstellt. Der `NameID`-Wert ist ein gezielter Bezeichner, der nur an den Dienstanbieter gerichtet ist, welcher die Zielgruppe für das Token darstellt. Er ist persistent – er kann widerrufen werden, eine erneute Zuweisung ist jedoch nicht mehr möglich. Er ist zudem nicht transparent, d.h. Informationen über den Benutzer werden nicht preisgegeben, und er kann nicht als Bezeichner für Attributabfragen verwendet werden.

Das `Method`-Attribut des `SubjectConfirmation`-Elements ist immer auf `urn:oasis:names:tc:SAML:2.0:cm:bearer` festgelegt.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Conditions (Bedingungen)

Dieses Element legt die Bedingungen fest, die die akzeptable Verwendung von SAML-Assertions definieren.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

Die Attribute `NotBefore` und `NotOnOrAfter` legen das Intervall fest, in dem die Assertion gültig ist.

* Der Wert des `NotBefore`-Attributs entspricht dem Wert des `IssueInstant`-Attributs des `Assertion`-Elements oder ist etwas (weniger als einer Sekunde) höher. Azure AD berücksichtigt keine Zeitunterschiede zwischen sich selbst und dem Clouddienst (Dienstanbieter) und fügt dieser Zeit keinen Puffer hinzu.
* Der Wert des `NotOnOrAfter`-Attributs liegt 70 Minuten nach dem Wert des `NotBefore`-Attributs.

#### <a name="audience"></a>Zielgruppe

Dieses Element enthält einen URI, der die beabsichtigte Zielgruppe identifiziert. Azure AD legt den Wert dieses Elements auf den Wert des `Issuer`-Elements der `AuthnRequest` fest, die die Anmeldung initiiert hat. Verwenden Sie zum Auswerten des Werts `Audience` den Wert von `App ID URI`, der bei der Anwendungsregistrierung angegeben wurde.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Genau wie der `Issuer`-Wert muss auch der `Audience`-Wert genau mit einem der Dienstprinzipalnamen des Clouddiensts in Azure AD übereinstimmen. Falls es sich beim Wert des `Issuer`-Elements allerdings nicht um einen URI-Wert handelt, entspricht der `Audience`-Wert in der Antwort dem `Issuer`-Wert mit dem Präfix `spn:`.

#### <a name="attributestatement"></a>AttributeStatement

Enthält Ansprüche bezüglich des Antragstellers oder des Benutzers. Der folgende Auszug enthält ein `AttributeStatement` -Beispielelement. Die Auslassungszeichen weisen darauf hin, dass das Element mehrere Attribute und Attributwerte enthalten kann.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```        

* **Name-Anspruch**: Der Wert des `Name`-Attributs (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) ist der Benutzerprinzipalname des authentifizierten Benutzers (z.B. `testuser@managedtenant.com`).
* **ObjectIdentifier-Anspruch**: Der Wert des `ObjectIdentifier`-Attributs (`http://schemas.microsoft.com/identity/claims/objectidentifier`) ist das `ObjectId`-Element des Verzeichnisobjekts, das den authentifizierten Benutzer in Azure AD darstellt. `ObjectId` ist ein unveränderlicher, global eindeutiger und sicher wiederverwendbarer Bezeichner des authentifizierten Benutzers.

#### <a name="authnstatement"></a>AuthnStatement

Dieses Element bestätigt, dass das Assertion-Subjekt mit einer bestimmten Methode zu einem bestimmten Zeitpunkt authentifiziert wurde.

* Das `AuthnInstant` -Attribut gibt die Uhrzeit an, zu der der Benutzer mit Azure AD authentifiziert wurde.
* Das `AuthnContext` -Element gibt den Authentifizierungskontext an, der zum Authentifizieren des Benutzers verwendet wurde.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
